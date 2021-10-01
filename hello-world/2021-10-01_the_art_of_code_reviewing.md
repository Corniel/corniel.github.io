# The Art of Code Reviewing
There are plenty good reasons to do [code reviews](https://en.wikipedia.org/wiki/Code_review).
All the reasons have one thing in common: afterwards, the code should have improved.

That leads to the fundamental question: what is good code?

> Indeed, the ratio of time spent reading versus writing is well over 10 to 1.
> We are constantly reading old code as part of the effort to write new code.
> Therefore, making it easy to read makes it easier to write (..)

This wisdom is not mine, but Robert C. Martin's, and even he probably borrowed
it somewhere. But the conclusion of this is, that good code not only should
work, but equally important, is **must** easy to reason about.

The smaller the context you need to understand a piece of code, the better;
it should be self descriptive. It should not be relevant if you were involved
in the designing process, nor that you know all the ins and outs of the calling
code and method calls made by the code at hand.

So, how to achieve this? Just look at the code and try to forget all the context
you know; does it still makes sense?
 
``` TypeScript
if (activeRows.length) {
  this.form = this.$refs.form as CustomFormComponent;
  this.form.touch();
}
else {
  this.removeKadasters();
}
```

Without scanning the rest of the code, this code is equivalent to:
If there is milk left in the fridge, play the piano, otherwise, burn down the garden.

So what went wrong?

> There are only two hard things in Computer Science: cache invalidation and naming things.
> -- Phil Karlton

Yes, the naming is off. It gives no clues how the statements relate. Another
thing that is violated here is the [Single-responsibility principle](https://en.wikipedia.org/wiki/Single-responsibility_principle).

By just renaming one variable, and move two lines of code to another method,
things suddenly become clear:

``` TypeScript
if (unsavedKadasters.length) {
  this.triggerValidation();
}
else {
  this.removeKadasters();
}
```

Long version short: While reviewing, try to be your dumbest self. Don't guess,
nor assume. Writing code may be solving a puzzle, reading code should not.
