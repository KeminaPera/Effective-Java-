### 第5条：优先使用依赖注入而不是硬连接资源

Many classes depend on one or more underlying resources. For example, a spell checker depends on a dictionary. It is not uncommon to see such classes implemented as static utility classes\(Item 4\):

很多类都依赖一个或多个底层资源。例如，拼写检查器依赖于字典。我们常常会见到这种类被做成了静态工具类（条目4）：

```
// Inappropriate use of static utility - inflexible & untestable!
public class SpellChecker {
    private static final Lexicon dictionary = ...;
    private SpellChecker() {} // Noninstantiable
    public static boolean isValid(String word) { ... }
    public static List<String> suggestions(String typo) { ... }
}
```

Similarly, it’s not uncommon to see them implemented as singletons \(Item 3\):

类似的，我们也会常常见到这种类被做成了Singleton（条目3）：

```
// Inappropriate use of singleton - inflexible & untestable!
public class SpellChecker {
    private final Lexicon dictionary = ...;
    private SpellChecker(...) {}
    public static INSTANCE = new SpellChecker(...);
    public boolean isValid(String word) { ... }
    public List<String> suggestions(String typo) { ... }
}
```

Neither of these approaches is satisfactory, because they assume that there is only one dictionary worth using. In practice, each language has its own dictionary, and special dictionaries are used for special vocabularies. Also, it may be desirable to use a special dictionary for testing. It is wishful thinking to assume that a single dictionary will suffice for all time.

上述两种方法都不不是很令人满意，因为它们都假定了只有一部字典可用。但在实际当中，每一种语言都有它自己的字典，尤其是有些用于特殊词汇的字典。而且，我们也可能需要使用特殊字典来做测试工作。我们不能一厢情愿地想当然以为单单一部字典就能满足所有情景。

You could try to have _SpellChecker_ support multiple dictionaries by making the _dictionary_ field nonfinal and adding a method to change the dictionary in an existing spell checker, but this would be awkward, error-prone, and unworkable in a concurrent setting. **Static utility classes and singletons are inappropriate for classes whose behavior is parameterized by an underlying resource.**

你也可以尝试着将例子中的_dictionary_属性的final修饰符去掉，然后添加一个可以更改字典的方法，但这又显得笨拙，容易出错，而且在并发场景中变得不可用。**静态工具类和Singleton对于类行为需要被底层资源参数化的场景是不适用的。**

What is required is the ability to support multiple instances of the class \(in our example, _SpellChecker_\), each of which uses the resource desired by the client \(in our example, the _dictionary_\). A simple pattern that satisfies this requirement is to pass the resource into the constructor when creating a new instance. This is one form of dependency injection: the dictionary is a dependency of the spell checker and is injected into the spell checker when it is created.

我们需要的是能支持多个实例的类（例子中对应的是_SpellChecker_），每个实例使用对应客户端需要用到的资源（例子中对应的是_dictionary_）。有个简单的模式能满足这种需求，那就是在创建一个新的实例时将资源参数传入构造器。这是依赖注入（dependency injection）的一种形式：词典作为拼写检查器的依赖在检查器被创建时就被注入了。

```
// Dependency injection provides flexibility and testability
public class SpellChecker {
    private final Lexicon dictionary;
    public SpellChecker(Lexicon dictionary) {
        this.dictionary = Objects.requireNonNull(dictionary);
    } 
    public boolean isValid(String word) { ... }
    public List<String> suggestions(String typo) { ... }
}
```



The dependency injection pattern is so simple that many

programmers use it for years without knowing it has a name. While

our spell checker example had only a single resource \(the

dictionary\), dependency injection works with an arbitrary number

of resources and arbitrary dependency graphs. It preserves

immutability \(Item 17\), so multiple clients can share dependent

objects \(assuming the clients desire the same underlying

resources\). Dependency injection is equally applicable to

constructors, static factories \(Item 1\), and builders \(Item 2\).

