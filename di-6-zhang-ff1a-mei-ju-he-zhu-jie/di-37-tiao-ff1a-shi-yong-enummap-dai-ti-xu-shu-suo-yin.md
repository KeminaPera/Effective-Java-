Occasionally you may see code that uses the ordinal method \(Item 35\) to index into an array or list. For example, consider this simplistic class meant to represent a plant:

有时候你会发现，有些代码通过使用ordinal方法（条目35）来索引一个数组或列表。举个例子，我们来看看下面这个简化的类，它表示了一株植物：

```java
class Plant {
    enum LifeCycle { ANNUAL, PERENNIAL, BIENNIAL }
    final String name;
    final LifeCycle lifeCycle;
    Plant(String name, LifeCycle lifeCycle) { 
        this.name = name;
        this.lifeCycle = lifeCycle;
    }
    @Override 
    public String toString() { 
        return name;
    } 
}
```

Now suppose you have an array of plants representing a garden, and you want to list these plants organized by life cycle \(annual, perennial, or biennial\). To do this, you construct three sets, one for each life cycle, and iterate through the garden, placing each plant in the appropriate set. Some programmers would do this by putting the sets into an array indexed by the life cycle’s ordinal:

现在假设你有一系列的植物用于表示一个植物园，同时，你需要让这些植物按照生命周期（一年生植物，多年生植物，或者两年生植物）。为了达到这种效果，你构建了三个集合，分别对应三种生命周期，然后遍历整个植物园，将每株植物放入合适的集合里。有些程序员会将这三个集合放入一个数组里，然后这个数组通过生命周期的枚举的序数进行索引：

**// Using ordinal\(\) to index into an array - DON'T DO THIS!**

```java
Set<Plant>[] plantsByLifeCycle = (Set<Plant>[]) new Set[Plant.LifeCycle.values().length];
for (int i = 0; i < plantsByLifeCycle.length; i++)
    plantsByLifeCycle[i] = new HashSet<>(); 
for (Plant p : garden)
    plantsByLifeCycle[p.lifeCycle.ordinal()].add(p);
// Print the results
for (int i = 0; i < plantsByLifeCycle.length; i++) {
    System.out.printf("%s: %s%n", Plant.LifeCycle.values()[i], plantsByLifeCycle[i]);
}
```

This technique works, but it is fraught with problems. Because arrays are not compatible with generics \(Item 28\), the program requires an unchecked cast and will not compile cleanly. Because the array does not know what its index represents, you have to label the output manually. But the most serious problem with this technique is that when you access an array that is indexed by an enum’s ordinal, it is your responsibility to use the correct int value; ints do not provide the type safety of enums. If you use the wrong value, the program will silently do the wrong thing or—if you’re lucky—throw an ArrayIndexOutOfBoundsException.

这么做虽然也可以，但是里面隐藏着一些问题。由于数组无法与泛型兼容（条目28），所以在程序里需要进行未受检地强转，而且不能准确无误地进行编译。同时，由于数组无法知道它的索引代表的是什么，所以你还必须手动为输出加上标签。但这种做法最严重的问题是，当你访问一个由枚举的序数来进行索引的数组时，你必须保证你使用的是正确的int值；而这些int值不提供枚举类型安全。如果你用了错误的值，程序将在不知什么时候会做出错误的行为，你够幸运的话，也许还会抛出ArrayIndexOutOfBoundsException异常。

There is a much better way to achieve the same effect. The array is effectively serving as a map from the enum to a value, so you might as well use a Map. More specifically, there is a very fast Map implementation designed for use with enum keys, known as java.util.EnumMap. Here is how the program looks when it is rewritten to use EnumMap:

有个更好的办法来获得这种效果。这个数组的作用无非就是将枚举映射成一个值，所以你可能还是需要用到一个Map。java.util.EnumMap类就是专门设计用于这种场景，它与枚举键一起用，是一个性能速度很快的Map接口的实现。下面的是使用EnumMap重写后的代码：

**// Using an EnumMap to associate data with an enum**

```java
Map<Plant.LifeCycle, Set<Plant>> plantsByLifeCycle = new EnumMap<>(Plant.LifeCycle.class);
for (Plant.LifeCycle lc : Plant.LifeCycle.values())
    plantsByLifeCycle.put(lc, new HashSet<>()); 
for (Plant p : garden)
    plantsByLifeCycle.get(p.lifeCycle).add(p); 
System.out.println(plantsByLifeCycle);
```

This program is shorter, clearer, safer, and comparable in speed to the original version. There is no unsafe cast; no need to label the output manually because the map keys are enums that know how to translate themselves to printable strings; and no possibility for error in computing array indices. The reason that EnumMap is comparable in speed to an ordinal-indexed array is that EnumMap uses such an array internally, but it hides this implementation detail from the programmer, combining the richness and type safety of a Map with the speed of an array. Note that the EnumMap constructor takes the Class object of the key type: this is a bounded type token, which provides runtime generic type information \(Item 33\).

相比原本的版本，这份代码更简短，安全，并且速度上也没有降低。重写后的代码没有不安全的强转，同时，由于map的键是枚举，所以知道如何将他们自身打印成合适的字符串；还有就是，在计算数组索引时不会出错。相较于原本基于索引的数组，EnumMap之所以在速度上具有可比性，是因为EnumMap在内部也使用了这样的一个数组，但对程序员隐藏了实现细节，不仅集合了Map的丰富功能和类型安全的优点，也集合了数组的速度优势。注意，EnumMap的构造器需要传入键类型的类对象，这样就限制了类型，提供了运行时的泛型类型信息（条目33）。

The previous program can be further shortened by using a stream \(Item 45\) to manage the map. Here is the simplest stream-based code that largely duplicates the behavior of the previous example:

前面的代码可以通过使用流（条目45）来管理这个map，从而进一步缩短代码。以下的代码就基于流，大幅减少了前面例子里的行为代码：

**// Naive stream-based approach - unlikely to produce an EnumMap!    **

```java
System.out.println(Arrays.stream(garden).collect(groupingBy(p -> p.lifeCycle)));
```

The problem with this code is that it chooses its own map implementation, and in practice it won’t be an EnumMap, so it won’t match the space and time performance of the version with the explicit EnumMap. To rectify this problem, use the  
three-parameter form of Collectors.groupingBy, which allows the caller to specify the map implementation using the mapFactory parameter:

但这份代码的问题就在于，它选择了它自己的map实现，在实际中这个实现不会是EnumMap，所以它的空间和时间性能与显式使用EnumMap版本的代码不匹配。为了解决这个问题，可以使用Collectors.groupBy的另一种形式，它接收三个参数，允许调用方通过用mapFactory参数来指明使用哪种map实现：

**// Using a stream and an EnumMap to associate data with an enum**

```java
System.out.println(Arrays.stream(garden).collect(groupingBy(p -> p.lifeCycle,
() -> new EnumMap<>(LifeCycle.class), toSet())));
```

This optimization would not be worth doing in a toy program like this one but could be critical in a program that made heavy use of the map.

The behavior of the stream-based versions differs slightly from that of the EmumMap version. The EnumMap version always makes a nested map for each plant lifecycle, while the stream-based versions only make a nested map if the garden contains one or more plants with that lifecycle. So, for example, if the garden contains annuals and perennials but no biennials, the size of plantsByLifeCycle will be three in the EnumMap version and two in both of the stream-based versions.

You may see an array of arrays indexed \(twice!\) by ordinals used to represent a mapping from two enum values. For example, this program uses such an array to map two phases to a phase transition \(liquid to solid is freezing, liquid to gas is boiling, and so forth\):

**// Using ordinal\(\) to index array of arrays - DON'T DO THIS!**

```java
public enum Phase {
    SOLID, LIQUID, GAS;
    public enum Transition {
        MELT, FREEZE, BOIL, CONDENSE, SUBLIME, DEPOSIT;
        // Rows indexed by from-ordinal, cols by to-ordinal 
        private static final Transition[][] TRANSITIONS = {
            { null, MELT, SUBLIME },
            { FREEZE, null, BOIL },
            { DEPOSIT, CONDENSE, null }
    };
        // Returns the phase transition from one phase to another     
        public static Transition from(Phase from, Phase to) {
            return TRANSITIONS[from.ordinal()][to.ordinal()]; 
        }
    } 
}
```

This program works and may even appear elegant, but appearances can be deceiving. Like the simpler garden example shown earlier, the compiler has no way of knowing the relationship between ordinals and array indices. If you make a mistake in the transition table or forget to update it when you modify the Phase or Phase.Transition enum type, your program will fail at runtime. The failure may be an ArrayIndexOutOfBoundsException, a NullPointerException, or \(worse\) silent erroneous behavior. And the size of the table is quadratic in the number of phases, even if the number of non-null entries is smaller.

Again, you can do much better with EnumMap. Because each phase transition is indexed by a pair of phase enums, you are best off representing the relationship as a map from one enum \(the “from” phase\) to a map from the second enum \(the “to” phase\) to the result \(the phase transition\). The two phases associated with a phase transition are best captured by associating them with the phase transition enum, which can then be used to initialize the nestedEnumMap:

**// Using a nested EnumMap to associate data with enum pairs**

```java
public enum Phase {
    SOLID, LIQUID, GAS;
    public enum Transition {
        MELT(SOLID, LIQUID), 
        FREEZE(LIQUID, SOLID), 
        BOIL(LIQUID, GAS), 
        CONDENSE(GAS, LIQUID), 
        SUBLIME(SOLID, GAS), 
        DEPOSIT(GAS, SOLID);
        private final Phase from; 
        private final Phase to;
        Transition(Phase from, Phase to) { 
            this.from = from;
            this.to = to;
        }
        // Initialize the phase transition map
        private static final Map<Phase, Map<Phase, Transition>> 
            m = Stream.of(values()).collect(groupingBy(t -> t.from, 
            () -> new EnumMap<>(Phase.class),
            toMap(t -> t.to, t -> t,
            (x, y) -> y, () -> new EnumMap<>(Phase.class))));
        public static Transition from(Phase from, Phase to) { 
            return m.get(from).get(to);
        }
    } 
}
```

The code to initialize the phase transition map is a bit complicated. The type of the map is Map&lt;Phase, Map&lt;Phase, Transition&gt;&gt;, which means “map from \(source\) phase to map from \(destination\) phase to transition.” This map-of-maps is initialized using a cascaded sequence of two collectors. The first collector groups the transitions by source phase, and the second creates an EnumMap with mappings from destination phase to transition. The merge function in the second collector \(\(x, y\) -&gt; y\)\) is unused; it is required only because we need to specify a map factory in order to get an EnumMap, and Collectors provides telescoping factories. The previous edition of this book used explicit iteration to initialize the phase transition map. The code was more verbose but arguably easier to understand.

Now suppose you want to add a new phase to the system:plasma, or ionized gas. There are only two transitions associated with this phase: ionization, which takes a gas to a plasma; and deionization, which takes a plasma to a gas. To update the array-based program, you would have to add one new constant to Phase and two to Phase.Transition, and replace the original nine-element array of arrays with a new sixteen-element version. If you add too many or too few elements to the array or place an element out of order, you are out of luck: the program will compile, but it will fail at runtime. To update theEnumMap-based version, all you have to do is add PLASMA to the list of phases, and IONIZE\(GAS, PLASMA\) and DEIONIZE\(PLASMA, GAS\) to the list of phase transitions:

**// Adding a new phase using the nested EnumMap implementation**

```java
public enum Phase {
    SOLID, LIQUID, GAS, PLASMA;
    public enum Transition {
        MELT(SOLID, LIQUID), 
        FREEZE(LIQUID, SOLID), 
        BOIL(LIQUID, GAS), 
        CONDENSE(GAS, LIQUID), 
        SUBLIME(SOLID, GAS), 
        DEPOSIT(GAS, SOLID), 
        IONIZE(GAS, PLASMA), 
        DEIONIZE(PLASMA, GAS); ... // Remainder unchanged
    } 
}
```

The program takes care of everything else and leaves you virtually no opportunity for error. Internally, the map of maps is implemented with an array of arrays, so you pay little in space or time cost for the added clarity, safety, and ease of maintenance. In the interest of brevity, the above examples use null to indicate the absence of a state change \(where into and from are identical\).

This is not good practice and is likely to result in a NullPointerException at runtime. Designing a clean, elegant solution to this problem is surprisingly tricky, and the resulting programs are sufficiently long that they would detract from the primary material in this item.

In summary, it is rarely appropriate to use ordinals to index into arrays: use EnumMap instead. If the relationship you are representing is multidimensional, use EnumMap&lt;..., EnumMap&lt;...&gt;&gt;. This is a special case of the general principle that application programmers should rarely, if ever, useEnum.ordinal\(Item 35\).

