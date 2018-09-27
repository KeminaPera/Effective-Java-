Occasionally you may see code that uses the ordinal method \(Item 35\) to index into an array or list. For example, consider this simplistic class meant to represent a plant:

```java
class Plant {
    enum LifeCycle { ANNUAL, PERENNIAL, BIENNIAL }
    final String name;
    final LifeCycle lifeCycle;
    Plant(String name, LifeCycle lifeCycle) { 
        this.name = name;
        this.lifeCycle = lifeCycle;
    }
    @Override public String toString() { 
        return name;
    } 
}
```

Now suppose you have an array of plants representing a garden, and you want to list these plants organized by life cycle \(annual, perennial, or biennial\). To do this, you construct three sets, one for each life cycle, and iterate through the garden, placing each plant in the appropriate set. Some programmers would do this by putting the sets into an array indexed by the life cycle’s ordinal:

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

This technique works, but it is fraught with problems. Because arrays are not compatible with generics \(Item 28\), the program requires an unchecked cast and will not compile cleanly. Because the array does not know what its index represents, you have to label the output manually. But the most serious problem with this technique is that when you access an array that is indexed by an enum’s ordinal, it is your responsibility to use the correct int value; ints do not provide the type safety of enums. If you use the wrong value, the program will silently do the wrong thing or—if you’re lucky—throw an ArrayIndexOutOfBoundsException. There is a much better way to achieve the same effect. The array is effectively serving as a map from the enum to a value, so you might as well use aMap. More specifically, there is a very fast Map implementation designed for use with enum keys, known as java.util.EnumMap. Here is how the program looks when it is rewritten to use EnumMap:

**// Using an EnumMap to associate data with an enum**

```java
Map<Plant.LifeCycle, Set<Plant>> plantsByLifeCycle = new EnumMap<>(Plant.LifeCycle.class);
for (Plant.LifeCycle lc : Plant.LifeCycle.values())
    plantsByLifeCycle.put(lc, new HashSet<>()); 
for (Plant p : garden)
    plantsByLifeCycle.get(p.lifeCycle).add(p); 
System.out.println(plantsByLifeCycle);
```

This program is shorter, clearer, safer, and comparable in speed to the original version. There is no unsafe cast; no need to label the output manually because the map keys are enums that know how to translate themselves to printable strings; and no possibility for error in computing array indices. The reason that EnumMap is comparable in speed to an ordinal-indexed array is that EnumMap uses such an array internally, but it hides this implementation detail from the programmer, combining the richness and type safety of a Map with the speed of an array. Note that the EnumMap constructor takes the Class object of the key type: this is abounded type token, which provides runtime generic type information \(Item 33\).

The previous program can be further shortened by using a stream \(Item 45\) to manage the map. Here is the simplest stream-based code that largely duplicates the behavior of the previous example:

**// Naive stream-based approach - unlikely to produce an EnumMap!    
**

