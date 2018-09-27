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



