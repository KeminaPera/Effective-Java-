If the elements of an enumerated type are used primarily in sets, it is traditional to use the int enum pattern \(Item 34\), assigning a different power of 2 to each constant:

// **Bit field enumeration constants - OBSOLETE!**

```java
public class Text {
    public static final int STYLE_BOLD = 1 << 0; // 1
    public static final int STYLE_ITALIC = 1 << 1; // 2
    public static final int STYLE_UNDERLINE = 1 << 2; // 4 
    public static final int STYLE_STRIKETHROUGH = 1 << 3; // 8
    // Parameter is bitwise OR of zero or more STYLE_constants
    public void applyStyles(int styles) { ... } 
}
```

This representation lets you use the bitwise OR operation to combine several constants into a set, known as a _bit field_:

