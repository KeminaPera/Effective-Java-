### 第16条：在公有类中使用访问方法，而不是公有域

Occasionally, you may be tempted to write degenerate classes that serve no purpose other than to group instance fields:

有时，你可能需要编写只是组合了实例域的类：

```
// Degenerate classes like this should not be public!
class Point { 
    public double x; 
    public double y;
}
```

Because the data fields of such classes are accessed directly, these classes do not offer the benefits of encapsulation\(Item 15\). You can’t change the representation without changing the API, you can’t enforce invariants, and you can’t take auxiliary action when a field is accessed. Hard-line object-oriented programmers feel that such classes are anathema and should always be replaced by classes with private fields and public accessor methods\(getters\) and, for mutable classes, mutators\(setters\):

由于这种类的数据域可以直接被访问，所以这些类不具有封装的优势（条目15）。你无法更改其表示形式，除非更改API。你也无法强加任何约束条件。当域被访问时，你也无法采取辅助的措施。坚持面向对象的程序员对这种类深恶痛绝，认为应该用包含私有域和公有访问方法（getter）的类来代替，对于可变类来说，还应包含设置方法（setter）。

```
// Encapsulation of data by accessor methods and mutators
class Point {
    private double x; 
    private double y;
    public Point(double x, double y) { 
        this.x = x;
        this.y = y;
    }
    public double getX() { 
        return x; 
    } 
    public double getY() { 
        return y; 
    }
    public void setX(double x) { 
        this.x = x; 
    }
    public void setY(double y) { 
        this.y = y; 
    }
}
```

Certainly, the hard-liners are correct when it comes to public classes: **if a class is accessible outside its package, provide accessor methods** to preserve the flexibility to change the class’s internal representation. If a public class exposes its data fields, all hope of changing its representation is lost because client code can be distributed far and wide.

毫无疑问，说到公有类时，坚持面向对象的程序员是对的：如果一个类在包外可以被访问，就应该提供访问方法，以此来保留改变类内部展示的灵活性。如果一个公有类暴露了它的数据域，就不用在想着在将来可以改变它的内部展示，因为已发布出去的客户端代码已经遍布各处了。

However, **if a class is package-private or is a private nested class, there is nothing inherently wrong with exposing its data fields**—assuming they do an adequate job of describing the abstraction provided by the class. This approach generates less visual clutter than the accessor-method approach, both in the class definition and in the client code that uses it. While the client code is tied to the class’s internal representation, this code is confined to the package containing the class. If a change in representation becomes desirable, you can make the change without touching any code outside the package. In the case of a private nested class, the scope of the change is further restricted to the enclosing class.

Several classes in the Java platform libraries violate the advice that public classes should not expose fields directly. Prominent examples include the Point and Dimension classes in the java.awt package. Rather than examples to be emulated, these classes should be regarded as cautionary tales. As described in Item 67, the decision to expose the internals of the Dimension class resulted in a serious performance problem that is still with us today.

While it’s never a good idea for a public class to expose fields directly, it is less harmful if the fields are immutable. You can’t change the representation of such a class without changing its API, and you can’t take auxiliary actions when a field is read, but you can enforce invariants. For example, this class guarantees that each instance represents a valid time:

```
// Public class with exposed immutable fields - questionable
public final class Time {
    private static final int HOURS_PER_DAY = 24; 
    private static final int MINUTES_PER_HOUR = 60;
    public final int hour; 
    public final int minute;
    public Time(int hour, int minute) {
        if (hour < 0 || hour >= HOURS_PER_DAY) throw new IllegalArgumentException("Hour: " + hour); 
        if (minute < 0 || minute >= MINUTES_PER_HOUR) throw new IllegalArgumentException("Min: " + minute); 
        this.hour = hour;
        this.minute = minute;
    }
... // Remainder omitted 
}
```

In summary, public classes should never expose mutable fields. It is less harmful, though still questionable, for public classes to expose immutable fields. It is, however, sometimes desirable for package-private or private nested classes to expose fields, whether mutable or immutable.

