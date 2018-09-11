Many enums are naturally associated with a single int value. All enums have an ordinal method, which returns the numerical position of each enum constant in its type. You may be tempted to derive an associated int value from the ordinal:

许多枚举都与一个单独的int值进行了关联。所有的枚举都有ordinal方法，这个方法返回了每个枚举常量的位置。你也许会试着从ordinal方法里获取得到一个关联的int值：

```java
public enum Ensemble {
    SOLO, DUET, TRIO, QUARTET, QUINTET, SEXTET, SEPTET, OCTET, NONET, DECTET;
    public int numberOfMusicians() { 
        return ordinal() + 1; 
    } 
}
```

While this enum works, it is a maintenance nightmare. If the constants are reordered, the numberOfMusicians method will break. If you want to add a second enum constant associated with an int value that you’ve already used, you’re out of luck. For example, it might be nice to add a constant for double quartet, which, like an octet, consists of eight musicians, but there is no way to do it.

虽然这个枚举可以正常使用，但是它的维护却将是一场噩梦。如果对常量进行重排序，那么numberOfMusicians方法将被破坏。如果你想再增加一个常量，并且让这个新的常量与一个已经被使用了的int值进行绑定，那也是不行的。例如，你想为双四重奏增加一个常量，而它却像八重奏一样，有8个演奏者，那么此时你也是无法做到让numberOfMusicians返回8的。

Also, you can’t add a constant for an int value without adding constants for all intervening int values. For example, suppose you want to add a constant representing a triple quartet, which consists of twelve musicians. There is no standard term for an ensemble consisting of eleven musicians, so you are forced to add a dummy constant for the unused int value \(11\). At best, this is ugly. If many int values are unused, it’s impractical.

同样，若是没有为所有涉及到的int值添加常量，你也是无法为某个int值添加常量的。例如，假设你想增加一个常量用来表示一个由12个演奏者组成的三重奏。由于没有由11个演奏者组成合奏组的标准，那么此时你不得不为一个用不到的int值\(11\)添加一个虚拟的常量。这么做顶多就只是显得不优雅。但如果有大量的int值都是用不到的，那么这么做就不切实际了。

