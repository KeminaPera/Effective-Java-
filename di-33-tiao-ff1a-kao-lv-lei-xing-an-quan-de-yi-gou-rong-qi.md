### 第33条：考虑类型安全的异构容器

Common uses of generics include collections, such as Set&lt;E&gt; and Map&lt;K,V&gt;, and single-element containers, such as ThreadLocal&lt;T&gt; and AtomicReference&lt;T&gt;. In all of these uses, it is the container that is parameterized. This limits you to a fixed number of type parameters per container. Normally that is exactly what you want. A Set has a single type parameter, representing its element type; a Map has two, representing its key and value types; and so forth.

泛型常见的应用场景里包括了集合，例如Set&lt;E&gt;，和Map&lt;K,V&gt;，还包括了单元素容器，例如ThreadLocal&lt;T&gt;和AtomicReference&lt;T&gt;。在这些应用场景里，它都充当被参数化了的容器。这样就限制了你对于每个容器都只能有固定个数的参数。一般情况下，这也正是你想要的。一个Set的元素类型只有一个；一个Map的键类型和值类型各有一个；以此类推。

Sometimes, however, you need more flexibility. For example, a database row can have arbitrarily many columns, and it would be nice to be able to access all of them in a typesafe manner. Luckily, there is an easy way to achieve this effect. The idea is to parameterize the key instead of the container. Then present the parameterized key to the container to insert or retrieve a value. The generic type system is used to guarantee that the type of the value agrees with its key.

然而，有时候你需要更灵活些。比如，数据库的某一行会有任意多列，如果能用一种类型安全的方式去访问这些列就再好不过了。幸运的是，有个简单的办法可以做到这点。这个办法就是，将键参数化，而不是容器。然后将参数化了的键提交给容器去插入或者获取一个值。用泛型类型系统来保证值的类型与值对应的键的类型相符。

As a simple example of this approach, consider a Favorites class that allows its clients to store and retrieve a favorite instance of arbitrarily many types. The Class object for the type will play the part of the parameterized key. The reason this works is that class Class is generic. The type of a class literal is not simply Class, but Class&lt;T&gt;. For example, String.class is of type Class&lt;String&gt;, and Integer.class is of type Class&lt;Integer&gt;. When a class literal is passed among methods to communicate both compile-time and runtime type information, it is called a type token \[Bracha04\].

下面针对上述的办法举一个简单的例子。考虑一个类，它叫Favorites，该类允许它的客户端存储和获取任意类型的favorite实例。某类型的Class对象将是参数化键的一部分。之所以可以这么做，是因为Class类是泛型的。从字面上来说，类的类型不是简单的Class，而是Class&lt;T&gt;。例如，String.class的类型为Class&lt;String&gt;，Integer.class的类型是Class&lt;Integer&gt;。

The API for the Favorites class is simple. It looks just like a simple map, except that the key is parameterized instead of the map. The client presents a Class object when setting and getting favorites. Here is the API:

```java
// Typesafe heterogeneous container pattern - API
public class Favorites {
    public <T> void putFavorite(Class<T> type, T instance);
    public <T> T getFavorite(Class<T> type);
}
```

Here is a sample program that exercises the Favorites class, storing, retrieving, and printing a favorite String, Integer, and Class instance:

```java
// Typesafe heterogeneous container pattern - client
public static void main(String[] args) {
    Favorites f = new Favorites();
    f.putFavorite(String.class, "Java");
    f.putFavorite(Integer.class, 0xcafebabe);
    f.putFavorite(Class.class, Favorites.class);
    String favoriteString = f.getFavorite(String.class);
    int favoriteInteger = f.getFavorite(Integer.class);
    Class<?> favoriteClass = f.getFavorite(Class.class);
    System.out.printf("%s %x %s%n", favoriteString,
    favoriteInteger, favoriteClass.getName());
}
```

As you would expect, this program prints Java cafebabe Favorites. Note, incidentally, that Java’s printf method differs from C’s in that you should use %n where you’d use \n in C. The %n generates the applicable platform-specific line separator, which is \n on many but not all platforms.

A Favorites instance is typesafe: it will never return an Integer when you ask it for a String. It is also heterogeneous: unlike an ordinary map, all the keys are of different types. Therefore, we call Favorites a typesafe heterogeneous container. The implementation of Favorites is surprisingly tiny. Here it is, in its entirety:

```java
// Typesafe heterogeneous container pattern -implementation
public class Favorites {
    private Map<Class<?>, Object> favorites = new HashMap<>();
    public <T> void putFavorite(Class<T> type, T instance) {
        favorites.put(Objects.requireNonNull(type), instance);
    } 
    public <T> T getFavorite(Class<T> type) {
        return type.cast(favorites.get(type));
    }
}
```

There are a few subtle things going on here. Each Favorites instance is backed by a private Map&lt;Class&lt;?&gt;, Object&gt; called favorites. You might think that you couldn’t put anything into this Map because of the unbounded wildcard type, but the truth is quite the opposite. The thing to notice is that the wildcard type is nested: it’s not the type of the map that’s a wildcard type but the type of its key. This means that every key can have a different parameterized type: one can be Class&lt;String&gt;, the next Class&lt;Integer&gt;, and so on. That’s where the heterogeneity comes from.

The next thing to notice is that the value type of the favorites Map is simply Object. In other words, the Map does not guarantee the type relationship between keys and values, which is that every value is of the type represented by its key. In fact, Java’s type system is not powerful enough to express this. But we know that it’s true, and we take advantage of it when the time comes to retrieve a favorite.

The putFavorite implementation is trivial: it simply puts into favorites a mapping from the given Class object to the given favorite instance. As noted, this discards the “type linkage” between the key and the value; it loses the knowledge that the value is an instance of the key. But that’s OK, because the getFavorites method can and does reestablish this linkage.

The implementation of getFavorite is trickier than that of putFavorite. First, it gets from the favorites map the value corresponding to the given Class object. This is the correct object reference to return, but it has the wrong compile-time type: it is Object \(the value type of the favoritesmap\) and we need to return a T. So, the getFavorite implementation dynamically casts theobject reference to the type represented by the Class object, using Class’s cast method.

The cast method is the dynamic analogue of Java’s cast operator. It simply checks that its argument is an instance of the type represented by the Class object. If so, it returns the argument; otherwise it throws a ClassCastException. We know that the cast invocation in getFavorite won’t throw ClassCastException, assuming the client code compiled cleanly. That is to say, we know that the values in the favorites map always match the types of their keys.

So what does the cast method do for us, given that it simply returns its argument? The signature of the cast method takes full advantage of the fact that class Class is generic. Its return type is the type parameter of the Class object:

```java
public class Class<T> {
    T cast(Object obj);
}
```

This is precisely what’s needed by the getFavorite method. It is what allows us to make Favorites typesafe without resorting to an unchecked cast to T.

There are two limitations to the Favorites class that are worth noting. First, a malicious client could easily corrupt the type safety of a Favorites instance, by using a Class object in its raw form. But the resulting client code would generate an unchecked warning when it was compiled. This is no different from a normal collection implementations such as HashSet and HashMap. You can easily put a String into a HashSet&lt;Integer&gt; by using the raw type HashSet \(Item 26\). That said, you can have runtime type safety if you’re willing to pay for it. The way to ensure that Favorites never violates its type invariant is to have the putFavorite method check that instance isactually an instance of the type represented by type, and we already know how to do this. Just use a dynamic cast:

```java
// Achieving runtime type safety with a dynamic cast
public <T> void putFavorite(Class<T> type, T instance) {
    favorites.put(type, type.cast(instance));
}
```

There are collection wrappers in java.util.Collections that play the same trick. They are called checkedSet, checkedList, checkedMap, and so forth. Their static factories take a Class object \(or two\) in addition to a collection \(or map\). The static factories are generic methods, ensuring that the compile-time types of the Class object and the collection match. The wrappers add reification to the collections they wrap. For example, the wrapper throws a ClassCastException at runtime if someone tries to put a Coin into your Collection&lt;Stamp&gt;. These wrappers are useful for tracking down client code that adds an incorrectly typed element to a collection, in an application that mixes generic and raw types.

The second limitation of the Favorites class is that it cannot be used on a non-reifiable type \(Item 28\). In other words, you can store your favorite String or String\[\], but not your favorite List&lt;String&gt;. If you try to store your favorite List&lt;String&gt;, your program won’t compile. The reason is that you can’t get a Class object for List&lt;String&gt;. The class literal List&lt;String&gt;.class is a syntax error, and it’s a good thing, too. List&lt;String&gt; and List&lt;Integer&gt; share a single Class object, which is List.class. It would wreak havoc with the internals of a Favorites object if the “type literals” List&lt;String&gt;.class and List&lt;Integer&gt;.classwere legal and returned the same object reference. There is no entirely satisfactory workaround for this limitation.The type tokens used by Favorites are unbounded: getFavorite and put-Favorite accept any Class object.

Sometimes you may need to limit the types that can be passed to a method. This can be achieved with a bounded type token, which is simply a type token that places a bound on what type can be represented, using a bounded type parameter \(Item 30\) or a bounded wildcard \(Item 31\).

The annotations API \(Item 39\) makes extensive use of bounded type tokens. For example, here is the method to read an annotation at runtime. This method comes from the AnnotatedElement interface, which is implemented by the reflective types that represent classes, methods, fields, and other program elements:

```java
public <T extends Annotation> T getAnnotation(Class<T> annotationType);
```

The argument, annotationType, is a bounded type token representing an annotation type. The method returns the element’s annotation of that type, if it has one, or null, if it doesn’t. In essence, an annotated element is a typesafe heterogeneous container whose keys are annotation types.

Suppose you have an object of type Class&lt;?&gt; and you want to pass it to a method that requires a bounded type token, such as getAnnotation. You could cast the object to Class&lt;? extends Annotation&gt;, but this cast is unchecked, so it would generate a compile-time warning \(Item 27\). Luckily, class Class provides an instance method that performs this sort of cast safely \(and dynamically\). The method is called asSubclass, and it casts the Class object on which it is called to represent a subclass of the class represented by its argument. If the cast succeeds, the method returns its argument; if it fails, it throws a ClassCastException.Here’s how you use the asSubclass method to read an annotation whose type is unknown at compile time. This method compiles without error or warning:

```java
// Use of asSubclass to safely cast to a bounded type token
static Annotation getAnnotation(AnnotatedElement element, String annotationTypeName) {
    Class<?> annotationType = null; // Unbounded type token
    try {
        annotationType = Class.forName(annotationTypeName);
    } catch (Exception ex) {
        throw new IllegalArgumentException(ex);
    } 
    return element.getAnnotation(annotationType.asSubclass(Annotation.class));
}
```

In summary, the normal use of generics, exemplified by the collections APIs, restricts you to a fixed number of type parameters per container. You can get around this restriction by placing the type parameter on the key rather than the container. You can use Class objects as keys for such typesafe heterogeneous containers. A Class object used in this fashion is called a type token. You can also use a custom key type. For example, you could have a DatabaseRow type representing a database row \(the container\), and a generic type Column&lt;T&gt; as its key.

