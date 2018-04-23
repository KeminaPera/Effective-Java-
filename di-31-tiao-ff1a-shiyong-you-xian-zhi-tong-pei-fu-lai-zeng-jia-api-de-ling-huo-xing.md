### 第31条：使用有限制通配符来增加API的灵活性

As noted in Item 28, parameterized types are invariant. In other

words, for any two distinct types Type1 and Type2, List&lt;Type1&gt; is

neither a subtype nor a supertype of List&lt;Type2&gt;. Although it is

counterintuitive that List&lt;String&gt; is not a subtype of List&lt;Object&gt;, it

really does make sense. You can put any object into a List&lt;Object&gt;,

but you can put only strings into a List&lt;String&gt;. Since

a List&lt;String&gt; can’t do everything a List&lt;Object&gt; can, it isn’t a

subtype \(by the Liskov substitution principal, Item 10\).

Sometimes you need more flexibility than invariant typing can

provide. Consider the Stack class from Item 29. To refresh your

memory, here is its public API:

