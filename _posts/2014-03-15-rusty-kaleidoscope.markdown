---
layout: post
title:  "Why Rust is better than C++, a case study"
date:   2014-03-15 19:00
categories: rust kaleidoscope llvm
---

For the past week I have been working on transferring LLVM's wonderful
Kaleidoscope tutorial to Rust as a way to gauge the possibility of finally
introducing a proper REPL into the language. I am still working on getting the
JIT compiler functioning, but I thought I may as well document some thoughts
about where Rust is infinitely superior to C++ and where it still needs some
improvement.

Globals are evil
----------------
C++ can be a horribly difficult language to read. Just look at the following example:
{% highlight c++ %}
static PrototypeAST *ParsePrototype() {
  if (CurTok != tok_identifier)
    return ErrorP("Expected function name in prototype");

  std::string FnName = IdentifierStr;
  getNextToken();

  if (CurTok != '(')
    return ErrorP("Expected '(' in prototype");
{% endhighlight %}

This is a function which returns an optionally null pointer. The variables
`CurTok`, `tok_identifier`, and `IdentifierStr` are all from other unknown
location. It's not even clear what type `CurTok` and `tok_identifier` could
be. The only reason we know the type of `IdentifierStr` is that it was copied
into the string FnName. The potential side effects of `getNextToken` are
unknown and the results of ErrorP are unclear.

In constrast, this same example when translated to Rust becomes:
{% highlight rust %}
fn parsePrototype(&mut self) -> ParseResult<~PrototypeAst> {
  let fnName: ~str = match self.currentToken {
    Identifier(ref name) => name.clone(),
    _ => return Err(~"Expected function name in prototype")
  };

  self.getNextToken();
  if self.currentToken != Char('(') {
    return Err(~"Expected '(' in prototype");
  }
{% endhighlight %}
Pattern matches reduce the ambiguity of `CurTok` and `tok_identifier`,
allowing the possible input tokens to be clearly demarcated without requiring
global mutable state. In addition, the error handling now occurs through a
Result class which will force callers of this method to employ error checking.
This makes it impossible for a missing `if(!result)` to cause a program crash.

Tracking a moving target
========================
If everything I had to say about Rust were positive, I would be lying. An
occasional downside to Rust is its current "move fast break things" attitude.
At one point in development I went from a perfect clean build to around 30
warnings because of the deprecation of owned vectors. While refactoring to the
new Vec struct was not horrific, it was two minutes I could have spent
documenting or refactoring.

Overall, working with Rust for this project was both fun and refreshing, and I
highly recommend trying out the language. Programming with Rust is kind of like
programming with Haskell: the semantics are tricky and the learning curve can
be steep, but becoming proficient in it teaches a way of thought that will stay
with you across all languages.

If you want to check out current work on Rusty Kaleidoscope, you can find it [here](https://github.com/hobinjk/rusty-kaleidoscope).
