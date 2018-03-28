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

Similarly, it’s not uncommon to see them implemented as

singletons \(Item 3\):



