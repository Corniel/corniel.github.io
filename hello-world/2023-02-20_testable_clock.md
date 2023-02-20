# A testable clock
Every developer that ever build an application that has any time dependency
knows that testing such dependencies is not straight forward. The most pressing
issue is arguably that most programming languages (including C#, PHP, Java, SQL)
do not allow you to change the behaviour of the (system) clock/date time provider.
So how to overcome this problem?

## Stubable Date Time provider
With the adoption of dependency injection/inverse of control, most programmers
have developed a habit of creating services for everything. DI frameworks do
most of the work, and a (unit) test implementation helps while testing.

``` C#
public interface IClock
{
    DateTime UtcNow();
}
```

This is a common way of solving the issue. By using an abstract class instead,
we can easily define the shared clock logic in the base class, as most of it
is the same for all implementations:

``` C#
public abstract class Clock
{
    public static readonly Clock System = new SystemClock();

    public abstract DateTime UtcNow();
    
    public DateOnly Today() => DateOnly.FromDateTime(UtcNow());
    
    public DateOnly Yesterday() => Today().AddDays(-1);
    
    public override ToString() => UtcNow().ToString("U");

    private sealed class SystemClock : Clock
    {
        public override DateTime UtcNow() => DateTime.UtcNow;
    }
}

public sealed class TestClock : Clock
{
    public TestClock(DateTime time) => Time = time;
    
    private readonly DateTime Time;
    
    public override DateTime UtcNow() => Time;
}
```

Now, in your startup, you simply can define `services.AddSingleton(Clock.System)`,
and in you test you use `new TestClock(new DateTime(...))`.

## Singleton delegate
The disadvantage of having an injectable date time provider is obviously that
you have to pass it through. There is, however, an other way, and I prefer this
one in almost all cases: A (testable) singleton delegate.

It is for obvious reasons that .NET contains `DateTime.Now`: is easy to use!
So how can we fix the (original) issue? Lets have a look at this snippet:

``` C#
public static Clock
{
    public static DateTime UtcNow() => (localUtcNow.Value ?? globalUtcNow).Invoke();

    public static void SetTime(Func<DateTime> time) => globalUtcNow = time;
    
    public static IDisposable SetTimeForCurrentContext(Func<DateTime> time) => new TimeScope(time);
    
    private readonly static AsyncLocal<Func<DateTime>?> localUtcNow = new();
    private static Func<DateTime> globalUtcNow = () => DateTime.UtcNow;
    
    private sealed class TimeScope : IDisposable
    {
        public TimeScope(Func<DateTime> time)
        {
            _func = localUtcNow.Value;
            localUtcNow.Value = time;
        }
        private readonly Func<DateTime>? _func;
        public void Dispose() => localUtcNow.Value = _func;
    }
}
```

Now, we can use `Clock.UtcNow()` in our production code when we need it. To
test it we just use:

``` C#
[Test]
public void SomeTest()
{
    using(Clock.SetTimeForCurrentContext(() => new DateTime(2017, 06, 11)))
    {
        // Within this scope, Clock.UtcNow() will return 2017-06-11
    }
}
```

Tests using this construct can run in parallel, and use `async/await` thanks to
Microsoft's [`AsyncLocal<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.threading.asynclocal-1).

The open source library [Qowaiv](https://github.com/Qowaiv/Qowaiv) contains a
Clock like this, including a way to have the same behaviour for time zones.
