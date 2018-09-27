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



