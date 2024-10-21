---
layout: post
title: "The Importance of User Testing (Tea Timer Too)"
date: 2024-10-15 15:00:00
categories: hardware circuitpython
---

In [part one](/hardware/circuitpython/2024/10/05/tea-timer/) of this series, I
described refining my tea timer as sanding off burrs. I did smooth out my
surface-level experience, but my familiarity blinded me to fundamental cracks
lurking below. The most powerful method for unburying these faults is user
testing. In my day job, we rely on testing with our customers to perfect our
software, uncovering both bugs and new features. User testing has also been
invaluable for many of my side projects (especially the games). A second pair
of eyes is worth its weight in gold but is often just as difficult to acquire.

Luckily, I have a wonderful and patient mom with an unslakable thirst for tea.
In other words, I had a terrific tea timer tester. One brief explanation later,
my mom set a timer and turned off the timer when it finished. However, her
timing journey would run into two cracks that needed repair.

The first was easy to spot: after pressing a button to start the timer my mom
would wait to see if the timer had started, often pressing multiple times to
make sure. This was due to the lack of immediate visual feedback. Instead, the
first indication of the timer running would happen several seconds later when a
lighted button turned off on the other side of the board. I added a quick
animation across all the buttons, filling in this first crack.

It took a bit longer for the second flaw to surface. My mom accidentally
started the timer and didn’t know how to stop it. When she asked me and I
answered “unplug it” I realized that there had to be a better way. A couple of
lines of code and a brief test later, I had added the final improvement (for
now!) to the tea timer: long pressing to cancel.

The timer was now in a much better state thanks to my mom’s fresh perspective
and all it cost was 17 cups of [Upton’s Traditional Masala
Chai](https://www.uptontea.com/chai-tea/chai-loose-leaf-black-tea/p/V00171/)
(not sponsored but would jump at the opportunity). Behold! Its final form!

<iframe width="560" height="315" src="https://www.youtube.com/embed/IDi5ppbw2oY?si=TeJygsPI9pEV1LBC" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


Of course, even in this state of alleged perfection there is a fundamental
issue with the timer I feel like I should discuss. There are no affordances!
Affordances is jargon for "bits of a thing that make it obvious how you can do
stuff with it." For example, a tea pot’s lid suggests opening it up, its handle
suggests lifting it up, and its spout suggests pouring. Because I’m using a
generic 4x8 grid of buttons (generic but really quite wonderful, thank you very
much Adafruit) there are no affordances guiding how to use the timer. I can
instead give a brief explanation on how to use the timer every time someone
gets within range, but with my hourly rate this affordance workaround isn’t
affordable (sorry). Perhaps part three in this tea saga is a hardware project
with purpose-built design. For now, thank you for reading this journey
from zero to hero! May your tea always be steeped for the appropriate time.
