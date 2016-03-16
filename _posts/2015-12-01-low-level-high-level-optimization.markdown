---
layout: post
title:  "Low-level Optimization in a High-level Language"
date:   2015-12-01 04:00:00
categories: javascript optimization parallelism simd
---

A year ago I took Performance Engineering Optimization. The first lecture
launched into a study of matrix multiplication. Starting with a simple python
program, the professor ran through a series of optimization techniques that
sped up the program by a factor of 52,479. However, the first step in this
process was to switch to C. I began wondering what would happen if we had
stayed with Python or another high-level language. I have finally followed
through with JavaScript. Read on for a whirlwind tour of low-level
optimizations and a few JavaScript-specific tricks.


# Base Code
*12330 ms*

No optimization would be complete without a naive implementation to poke and
prod into a perfected program. In this case, it is a simple triply-nested for
loop performing the multiplication of two 1024 by 1024 matrices.

~~~ javascript
function matMul(a, b, c) {
  var n = 1024;
  for (var i = 0; i < n; i++) {
    for (var j = 0; j < n; j++) {
      for (var k = 0; k < n; k++) {
        c[i][j] += a[i][k] * b[k][j];
      }
    }
  }
}
~~~


# Testing Methods

The performance testing code initializes `a`, `b`, and `c` once and then runs
four warmup runs followed by sixteen measured runs. Note that only the first
run is a true matrix multiply because `c` is not reset to 0. This has no effect
on performance. The warmup runs were intended to limit the effect of JIT
warmup on the time. Even though this was clever at the time, this discarding
had no significant effect on test results.  This is especially true after my
switch from average time to minimum time. The minimum more accurately
represents the peak performance of the code and obsoletes the warmup period.

Firefox Nightly was used for all test runs on a 4 core 2.6 GHz i7 with 16 GB
1600 MHz RAM. 1024 by 1024 matrices were used to provide a balance between fast
execution and reliable results.


# Using Globals
*9160 ms: 1.35x*

The first concern I noticed was that the performance recording showed that
there were five incremental garbage collections during the run. I assumed that
this was due to either function parameter passing or closures involving some
amount of copying, even though arrays are passed by reference. The 3000 ms
saved confirms this hypothesis.


# Typed Arrays
*7830 ms: 1.57x*

Switching from a basic JavaScript Array to the specialized and pre-allocated
Float64Array yielded a significant speedup while requiring almost no actual
work. As any Haskell fanatic will tell you, the easiest way to improve a
program is to embed more type information. In this case, a Float64Array
improves the memory locality of the matrices.


# Aggregated Typed Arrays
*7315 ms: 1.69x*

The previous matrix representation was an Array of Float64Arrays. I expected
that this would have significant overhead. This led me to my next optimization,
storing each matrix as a single Float64Array with manually computed indexing.
While computing the index as `i * n +  j` yielded no improvement, calculating
the index as `(i << LOG_N) | j` yielded the slight speedup.

~~~ javascript
for (var i = 0; i < n; i++) {
  for (var j = 0; j < n; j++) {
    for (var k = 0; k < n; k++) {
        // Aggregated
        c[i * n + j] += a[i * n + k] * b[k * n + j];
        // Logical + Aggregated
        c[(i << LOG_N) | j] += a[(i << LOG_N) | k] * b[(j << LOG_N) | k];
    }
  }
}
~~~


# Transposition
*2900 ms: 4.25x*

I decided to move on from high-level quibbles and get into the cache-level
optimizations I had learned in the Performance Engineering lecture. The first
of these is transposition. A standard matrix multiplication works by going
across the rows of one matrix and down the columns of the other. Like a person raised
reading English, modern CPUs are good at reading left-to-right and bad at
reading top-to-bottom. This optimization transposes (rotates and flips) the
second matrix so that the computer can read both input matrices left-to-right.

Note that this uses standard aggregated indices for clarity:

~~~ javascript
for (var i = 0; i < n; i++) {
  for (var j = 0; j < n; j++) {
    for (var k = 0; k < n; k++) {
      c[i * n + j] += a[i * n + k] * b[j * n + k];
    }
  }
}
~~~


# Tiling
*2950 ms: 4.18x*

Another low-level optimization intended to improve cache usage, tiling splits
the matrix into submatrices that are intended to fit entirely within the
processor's cache. Unfortunately, this optimization had no actual effect,
serving as a cautionary tale for pushing low-level tricks into high-level
languages.

~~~ javascript
for (var ih = 0; ih < n; ih += tileSize) {
  for (var jh = 0; jh < n; jh += tileSize) {
    for (var kh = 0; kh < n; kh += tileSize) {
      for (var il = 0; il < tileSize; il++) {
        for (var jl = 0; jl < tileSize; jl++) {
          for (var kl = 0; kl < tileSize; kl++) {
            c[((ih | il) << LOG_N) | (jh | jl)] +=
                a[((ih | il) << LOG_N) | (kh | kl)] *
                b[((jh | jl) << LOG_N) | (kh | kl)];
          }
        }
      }
    }
  }
}
~~~


# SIMD
*665 ms: 18.54x*

The lecture next covered vectorization, the art of performing a Single
Instruction on Multiple Data (SIMD). Vectorization was recently implemented in
Firefox Nightly and does not yet exist in any other browsers. Always wanting to
explore new possibilities, I put together a simple use of the SIMD API to
vectorize the matrix multiplication, allowing it to work on two values at once.

With `SIMD.Float64x2` this was sadly the slowest optimization, clocking in at a
47x decrease in performance. However, this doesn't doom the SIMD API.
Float32x4, which works on four values at once instead of two, delivered an 18x
speedup. While the youthful SIMD API has some rough patches, it is still
impressively powerful.

~~~ javascript
for (var i = 0; i < n; i++) {
	for (var jh = 0; jh < n; jh += 4) {
		for (var k = 0; k < n; k += 4) {
			for (var jl = 0; jl < 4; jl++) {
				var simdC = SIMD.Float32x4.load(c, (i << LOG_N) | jh);
				var simdA = SIMD.Float32x4.load(a, (i << LOG_N) | k);
				var simdB = SIMD.Float32x4.load(b, ((jh + jl) << LOG_N) | k);
				var simdMul = SIMD.Float32x4.mul(simdA, simdB);
				var simdCPlusAB = SIMD.Float32x4.add(simdC, simdMul);
				SIMD.Float32x4.store(c, (i << LOG_N) | jh, simdCPlusAB);
			}
		}
	}
}
~~~


# Parallelization
*215 ms: 57.35x*

At this point the only remaining speedup covered in the lecture is
parallelization. In C this involves wrangling threading libraries and in
JavaScript this means dealing with WebWorkers and array copying. I opted for a
simple hard-coded split-into-four approach in which each worker produces one
quarter of the output. This exceeded my expectations, showing the extensive
optimizations in the browser for passing Arrays to WebWorkers.


# Wrapping Up
While a 57x speedup sounds impressive on paper, what is more important is that
all of these techniques work. Using "port it to C" as the holy grail of
optimization is simplistic and limiting when low-level rewrites and JavaScript
implementation tricks are available.

*JavaScript doesn't have to be slow.*

[View the code](https://github.com/hobinjk/matrix-optimization/commits/master)
