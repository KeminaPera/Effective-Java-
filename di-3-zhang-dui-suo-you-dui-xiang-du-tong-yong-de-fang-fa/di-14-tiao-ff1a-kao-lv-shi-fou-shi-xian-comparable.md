### 第14条：考虑是否实现comparable

Unlike the other methods discussed in this chapter, the compareTo method is not declared in Object. Rather, it is the sole method in the Comparable interface. It is similar in character to Object’s equals method, except that it permits order comparisons in addition to simple equality comparisons, and it is generic. By implementing Comparable, a class indicates that its instances have a natural ordering. Sorting an array of objects that implement Comparable is as simple as this:

不像本章里讨论的其它方法，compareTo方法并没有在Object里被声明。想法，它是Comparable接口声明的唯一方法。compareTo方法与Object的equals方法在特征上有点类似，只是前者不仅能做是否相等的比较，还能做谁大谁小的比较，而且还是范型的。假如一个类实现了Comparable接口，那就表明了这个类的实例之间具有自然顺序（natural ordering）。对一组实现了Comparable接口的对象数组进行排序是件很简单的事：

```
Arrays.sort(a);
```

It is similarly easy to search, compute extreme values, and maintain automatically sorted collections of Comparable objects. For example, the following program, which relies on the fact that String implements Comparable, prints an alphabetized list of its command-line arguments with duplicates eliminated:

```
public class WordList {
    public static void main(String[] args) {
        Set<String> s = new TreeSet<>(); 
        Collections.addAll(s, args); 
        System.out.println(s);
    } 
}
```



