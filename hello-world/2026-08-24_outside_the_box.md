# Outside The Box
## A story about fundamental limitations on [LLMs](https://en.wikipedia.org/wiki/Large_language_model)

As a non-native speaker, expressing yourself is even harder than it is in your
mother tongue. One of the (many) moments I was reminded of this was when
someone pointed out a mistake in my [LinkedIn bio](https://www.linkedin.com/in/corniel-nobel/).
I had called myself an *"Out-of-the-box thinker,"* which was an attempt to hint
at my [ADHD](https://en.wikipedia.org/wiki/Attention_deficit_hyperactivity_disorder)
using [LinkedIn speak](https://www.polytranslator.com/linkedin-speak/). Most
likely, it should have been **outside-the-box** thinker. (I've since updated my
bio, in case you were wondering.)

But this post is about AI, not my LinkedIn bio, so why is this relevant, you
may ask? Well, recently I asked [Google's Gemini](https://gemini.google.com/)
to help write a proposal for my company to be more inclusive to neurodiverse
people, developers in particular, to help them reach their true potential. I
was surprised to see it use *out-of-the-box* instead of *outside-the-box* too.

So I thought, let’s check if it could explain the difference:

> **Question**: Could you explain the difference to me between outside-the-box
> and out-of-the-box?
>
> **Answer**: While both idioms sound almost identical, they describe completely
> opposite concepts: one is about creative thinking, and the other is about
> practical readiness.
> 
> ## Thinking Outside the Box
> + **Meaning**: Thinking creatively, unconventionally, or from a new perspective.
> + **Origin**: Inspired by the famous [9-dot puzzle](https://en.wikipedia.org/wiki/Nine_dots_puzzle),
>   which requires drawing lines beyond the invisible boundary of a square to connect all the dots.
> + **Usage**: "To solve this budget deficit, we need to think outside the box."
> 
> ## Out of the Box
> + **Meaning**: Ready to use immediately upon arrival or installation,
>   requiring no additional assembly, configuration, or modification.
> + **Origin**: Derived from retail products (like software or electronics)
>   that work straight out of their original packaging.
> + **Usage**: "This software works right out of the box with zero setup required."

So, when asked explicitly, it can perfectly explain the differences between the
two, but it fails to apply this insight in practice when generating text. What
is going on here?

Sometimes we seem to forget that LLMs are nothing more (nor less) than text
predictors: given a chain of text tokens, what is the most likely continuation?
Due to their enormous models and training sets, they can do magnificent things.
When it performed the first task, it reused tons of corporate lingo to come up
with an answer. Because I am part of a majority of non-native speakers who mix
up the expression, the LLM mirrored that exact mistake. In the second case,
however, it apparently reused insights derived from an English dictionary. So,
among the long list of limitations LLMs have, we have to add a new one: they
cannot think (or answer) outside the box.

*Note: Written by me, spellchecked by an LLM*