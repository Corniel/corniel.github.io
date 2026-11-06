# [Techorama 2026](https://techorama.nl)
## 26-28 October - Utrecht (NL)

It has been a while since I visited an I(C)T conference merely as a guest. I actually responded to their *call for papers*, but given the talks they selected, I can safely say that I have not changed: none of my proposals were AI-oriented...

The keynote *Scale vs Soul* by Dado Van Peteghem had an interesting standpoint: that the use of AI in an organisation can be plotted along (at least) two axes: **Scale** and **Soul**. The question he raised is how an organisation can keep its soul, its personal touch while adopting AI. He did not come up with a concrete answer, but gave some food for thought.

Jon Galloway had a - not so structured - talk about *What's New in .NET 11*. I would not say that it was a complete waste of time, but I already knew almost everything he mentioned. Still looking forward to the introduction of `union` types, and the use of the `closed` modifier on `class`; the combination of the two allows for some structures that are new to .NET.

The only really bad talk I attended was *The Myth of Message Ordering: What Message Brokers Actually Guarantee*. It could have been a five-minute talk showing how different brokers implement their chosen strategy, and some suggestions on how you could deal with that. It took the speaker 55 minutes...

Steve Gordon's deep dive into *Unveiling .NET Secrets with the World's Smallest C# Program* was time well spent. His talk was a journey that touched on MSBuild, IL, and JIT. And for those wondering: `{}` is the smallest possible program (thanks to top-level statements).

The next morning, I played it safe by picking Dylan Beattie's talk: *OK, Code Is Worthless... Now What?* He made a really strong argument that writing the actual code was never the hardest part. When his talk is put online, I urge you to watch it yourself.

To my great joy, there was a talk that was not about AI at all: *Async Masterclass* by Stephen Cleary covered all the nitty-gritty details relevant to understanding `async` and `Task`. After his talk, we had a nice, short chat, and he was intrigued by the use of `AsyncLocal` to create a testable time provider.

My favourite talk of the event was *Creating great CLIs* by Kathleen Dollard. She covered the do's and don'ts (never create a breaking change; you never know which tool is silently relying on it) and explained how the use of `System.CommandLine` can ease a lot of your pain in parsing the actual command-line arguments. During this session, I got a great idea on how to improve my use of ANSI escape sequences (colour, style, and cursor). I'm working on it right now, so maybe I'll discuss it here soon too.

And to me, that is the most useful thing about attending conferences like these: even if there is not that much new to pick up from a session, they always spark new ideas. I wrote down five pages of ideas in the notebook I brought with me. It will be a challenge to build even half of them.
