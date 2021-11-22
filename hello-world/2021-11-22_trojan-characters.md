# (Potential) Trojan Horse (unicode) characters

Most readers are familiar (enough) with Greek mythology to know about the
origin of the Trojan Horse. 
![Trojan Horse](2021-11-22_trojan-characters.jpg)

Because of the similarities, a particular kind of [malware](https://en.wikipedia.org/wiki/Trojan_horse_(computing))
has be named after it, and recently, a second technique to breach security
measures, followed.

The idea of this technique is to use (unicode) characters that not visible/rendered
by your IDE, so that you **think** your code works in a certain way, but the
compiler/interpreter will behave differently (as the hacker intended).

So, what kind of characters are we talking about?
All of the following [unicode categories](https://en.wikipedia.org/wiki/Unicode_character_property) are off limits:
 * Format
 * Surrogate
 * Other/not assigned

But - yes there is a but - a lot of alphabets (including Arabic, and Chinese)
are marked as "other\not assigned". To keep things simple I suppose.

Anyhow, if your security concerns are - in this case - limited to .NET (both C#
and Visual Basic), I have a solution for you: use these Roslyn Analyzers:
 * [Qowaiv.Analyzers.CSharp](https://www.nuget.org/packages/Qowaiv.Analyzers.CSharp)
 * [Qowaiv.Analyzers.VisualBasic](https://www.nuget.org/packages/Qowaiv.Analyzers.VisualBasic)

They will report any violation of this kind as a compiler warning.
