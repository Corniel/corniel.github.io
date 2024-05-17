# .NET Zuid
## Single Value Objects

Earlier this year, I was contacted by someone from [.NET Zuid](https://www.dotnetzuid.nl/);
If I could give [my talk about Single Value Objects](2024-05-16_dot_net_zuid.md).
Sure, no problem. I have given this talk multiple times, but not that frequently
that it is becomming dull.

So I updated my slides, drove to Eindhoven, faced some traffic yams and thanks
to Google Maps, also found the venue of the event. It turned out they arranged
a food truck too, with decent - but not that healthy - food, and I nice
opportunity to have some small talk with the audience.

The talk went fine, as I guess that almost always the case, although I spotted
a mistake on a slide I just added. Note to self: check new content. The Q&A was
brief, but relevant.

So far so good, but it turned out that the organization was under the impression
that I would also give a talk after the break. Fortunatly, [Fons Sonnemans](https://www.linkedin.com/in/fonssonnemans)
suggested to do a shared ad-hoc live coding session together after the break.

We touchted the surface of how to deal with `struct`'s in .NET. We discussed
the behavior of default intialization, which is limited by design:

``` C#
MyCustomStruct ctor = new();
MyCustomStruct init = default;
_ = someDictionary.TryGetValue("unknownKey", out MyCustomStruct value);

Console.WriteLine(ctor);
Console.WriteLine(init);
Console.WriteLine(value);
```

How to overcome those limitations if for instance, you want to design an SVO
like a postive integer:

``` C#
[DebuggerDisplay("{Value}")]
public readonly struct PositiveInteger
{
    [DebuggerBrowsable(DebuggerBrowsableState.Never)]
    private readonly int value;

    [DebuggerBrowsable(DebuggerBrowsableState.Never)]
    private int Value=> value+1;

    public PositiveInteger(int val)
    {
        ArgumentOutOfRangeException.ThrowIfNegativeOrZero(val);
        value = val - 1;
    }
}
```

The last topic we covered was the `[StructLayout]` attribute. It allows you to
automatically or even explicitly ask the compiler to define how to arrange
memory usages on the stack. Powerful in some cases, but rarley practical in cases
where you build buisness software. (The kind of software that pays the bills
for most of us mortals)

My slides: [2024-05-16_dot_net_zuid.odp](2024-05-16_dot_net_zuid.odp)
