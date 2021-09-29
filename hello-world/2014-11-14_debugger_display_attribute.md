# Debugger Display Attribute
Every developer has to debug the code he is working on. You IDE helps you by
showing your local variable watches and, allowing you to define watches. Those
watches are the `ToString()` representation of the objects/expressions you watch.

That’s nice, but you can improve this experience a lot by defining your own
debugger display! Of course, there some types in .NET that show you more than
the default implementation of object (`ToString(){ return GetType().FullName; }`),
but for you own classes, this is the behavior, by default.

So, what to do? Well, you can implement `ToString()` for your class, but there
are a lot of scenario’s (take `ToString()` for `XDocument`) where the result of
`ToString()` is not what you want for your debugger display. Fortunately, .NET
gives you the opportunity to add the `DebuggerDisplayAttribute`. This attribute
decorates your class with an instruction so that your debugger knows what to do,
when showing a watch on it.

Basically, you can write a full expression in the attribute constructor argument,
but referring to a (non public) property is more convenient. You can refer to a
method too, but this can lead to unintended behavior. The expression is evaluated
by the runtime and debugging a VB.NET project will give you a warning “Because
the evaluation could cause side effects, it will not be executed, until enabled
by the user”, even if the debugger display is implemented in C#. Using properties
will not lead to this kind of situations.

For most situations you want to return a string, but you can return other objects
as well. The IDE will call the DebuggerDisplay of that object. The cases where
this can be of value, is where you want to show 17 instead of "17". Most of the
time returning a string is what you want.

``` C#
using System.Diagnostics;
using System.Diagnostics.CodeAnalysis;
using System.Globalization;

namespace HelloWorld
{
	[DebuggerDisplay("{DebuggerDisplay}")]
	public class DebuggerDisplayClassStringProperty
	{
		public int Number { get; set; }
		[DebuggerBrowsable(DebuggerBrowsableState.Never)]
		private string DebuggerDisplay { get { return this.Number.ToString(CultureInfo.InvariantCulture); } }
	}

	[DebuggerDisplay("{DebuggerDisplay()}")]
	public class DebuggerDisplayClassStringMethod
	{
		public int Number { get; set; }
		[DebuggerBrowsable(DebuggerBrowsableState.Never)]
		private string DebuggerDisplay() { return this.Number.ToString(CultureInfo.InvariantCulture); }
	}

	[DebuggerDisplay("{DebuggerDisplay}")]
	public class DebuggerDisplayClassObjectProperty
	{
		public int Number { get; set; }
		[DebuggerBrowsable(DebuggerBrowsableState.Never)]
		private int DebuggerDisplay { get { return this.Number; } }
	}

	[DebuggerDisplay("{DebuggerDisplay()}")]
	public class DebuggerDisplayClassObjectMethod
	{
		public int Number { get; set; }

		[ExcludeFromCodeCoverage]
		[DebuggerBrowsable(DebuggerBrowsableState.Never)]
		private int DebuggerDisplay() { return this.Number; }
	}
}
```