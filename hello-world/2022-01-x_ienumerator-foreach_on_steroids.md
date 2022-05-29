# The IEnumerator intefrace
## Foreach on steroids


``` C#
foreach(var elm in enumeration)
{
    // do something with elm
}
```

``` C#
var iterator = enumeration.GetEnumerator();
try
{
    while (iterator.MoveNext())
	{
	    var elm = iterator.Current;
		// do something with elm
	}
}
finally
{
    iterator.Dispose();
}
```

