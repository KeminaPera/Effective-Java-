### 第34条：用枚举替换常量

An enumerated type is a type whose legal values consist of a fixed set of constants, such as the seasons of the year, the planets in the solar system, or the suits in a deck of playing cards. Before enum types were added to the language, a common pattern for representing enumerated types was to declare a group of named int constants, one for each member of the type:

```java
// The int enum pattern - severely deficient!
public static final int APPLE_FUJI = 0;
public static final int APPLE_PIPPIN = 1;
public static final int APPLE_GRANNY_SMITH = 2; 
public static final int ORANGE_NAVEL = 0;
public static final int ORANGE_TEMPLE = 1;
public static final int ORANGE_BLOOD = 2;
```

This technique, known as the int enum pattern, has many shortcomings. It provides nothing in the way of type safety and little in the way of expressive power. The compiler won’t complain if you pass an apple to a method that expects an orange, compare apples to oranges with the==operator, or worse:

Click here to view code image

// Tasty citrus flavored applesauce!

int i = \(APPLE\_FUJI - ORANGE\_TEMPLE\) / APPLE\_PIPPIN;

Note that the name of each apple constant is prefixed  
 withAPPLE\_and the name of each orange constant is prefixed  
 withORANGE\_. This is because Java doesn’t provide namespaces  
 forintenum groups. Prefixes prevent name clashes when  
 twointenum groups have identically named constants, for example betweenELEMENT\_MERCURYandPLANET\_MERCURY.  
 Programs that useintenums are brittle. Becauseintenums  
 areconstant variables\[JLS, 4.12.4\], theirintvalues are compiled into the clients that use them \[JLS, 13.1\]. If the value associated with anintenum is changed, its clients must be recompiled. If not, the clients will still run, but their behavior will be incorrect.  
 There is no easy way to translateintenum constants into printable strings. If you print such a constant or display it from a debugger, all you see is a number, which isn’t very helpful. There is no reliable way to iterate over all theintenum constants in a group, or even to obtain the size of anintenum group.  
 You may encounter a variant of this pattern in  
 whichStringconstants are used in place ofintconstants. This variant, known as theStringenum pattern, is even less desirable. While it does provide printable strings for its constants, it can lead naive users to hard-code string constants into client code instead of using field names. If such a hard-coded string constant contains a typographical error, it will escape detection at compile time and result in bugs at runtime. Also, it might lead to performance problems, because it relies on string comparisons.  
 Luckily, Java provides an alternative that avoids all the shortcomings of theintandstringenum patterns and provides many added benefits. It is theenum type\[JLS, 8.9\]. Here’s how it looks in its simplest form:

Click here to view code image

public enum Apple { FUJI, PIPPIN, GRANNY\_SMITH } public enum Orange { NAVEL, TEMPLE, BLOOD }

On the surface, these enum types may appear similar to those of other languages, such as C, C++, and C\#, but appearances are deceiving. Java’s enum types are full-fledged classes, far more powerful than their counterparts in these other languages, where enums are essentiallyintvalues.

The basic idea behind Java’s enum types is simple: they are classes that export one instance for each enumeration constant via a public static final field. Enum types are effectively final, by virtue of having no accessible constructors. Because clients can neither create instances of an enum type nor extend it, there can be no instances but the declared enum constants. In other words, enum types are instance-controlled \(page 6\). They are a generalization of singletons \(Item 3\), which are essentially single-element enums. Enums provide compile-time type safety. If you declare a parameter to be of typeApple, you are guaranteed that any non-null object reference passed to the parameter is one of the three

validApplevalues. Attempts to pass values of the wrong type will result in compile-time errors, as will attempts to assign an expression of one enum type to a variable of another, or to use the==operator to compare values of different enum types.  
 Enum types with identically named constants coexist peacefully because each type has its own namespace. You can add or reorder constants in an enum type without recompiling its clients because the fields that export the constants provide a layer of insulation between an enum type and its clients: constant values are not compiled into the clients as they are in theintenum patterns. Finally, you can translate enums into printable strings by calling theirtoStringmethod.

In addition to rectifying the deficiencies ofintenums, enum types let you add arbitrary methods and fields and implement arbitrary interfaces. They provide high-quality implementations of all  
 theObjectmethods \(Chapter 3\), they implementComparable\(Item 14\)

andSerializable\(Chapter 12\), and their serialized form is designed to withstand most changes to the enum type.  
 So why would you want to add methods or fields to an enum type? For starters, you might want to associate data with its constants. OurAppleandOrangetypes, for example, might benefit from a method that returns the color of the fruit, or one that returns an image of it. You can augment an enum type with any method that seems appropriate. An enum type can start life as a simple collection of enum constants and evolve over time into a full-featured abstraction.

For a nice example of a rich enum type, consider the eight planets of our solar system. Each planet has a mass and a radius, and from these two attributes you can compute its surface gravity. This in turn lets you compute the weight of an object on the planet’s surface, given the mass of the object. Here’s how this enum looks. The numbers in parentheses after each enum constant are parameters that are passed to its constructor. In this case, they are the planet’s mass and radius:

Click here to view code image

// Enum type with data and behavior

public enum Planet { MERCURY\(3.302e+23, 2.439e6\), VENUS \(4.869e+24, 6.052e6\), EARTH \(5.975e+24, 6.378e6\), MARS \(6.419e+23, 3.393e6\), JUPITER\(1.899e+27, 7.149e7\), SATURN \(5.685e+26, 6.027e7\), URANUS \(8.683e+25, 2.556e7\), NEPTUNE\(1.024e+26, 2.477e7\);

private final double mass; // In kilograms private final double radius; // In meters

private final double surfaceGravity; // In m / s^2

// Universal gravitational constant in m^3 / kg s^2 private static final double G = 6.67300E-11;

// Constructor  
 Planet\(double mass, double radius\) {

this.mass = mass;  
 this.radius = radius;  
 surfaceGravity = G \* mass / \(radius \* radius\);

}

public double mass\(\) { return mass; }  
 public double radius\(\) { return radius; }  
 public double surfaceGravity\(\) { return surfaceGravity; }

public double surfaceWeight\(double mass\) { return mass \* surfaceGravity; // F = ma

} }

It is easy to write a rich enum type such asPlanet.To associate data with enum constants, declare instance fields and write a constructor that takes the data and stores it in the fields.Enums are by their nature immutable, so all fields should be final \(Item 17\). Fields can be public, but it is better to make them private and provide public accessors \(Item 16\). In the case

ofPlanet, the constructor also computes and stores the surface gravity, but this is just an optimization. The gravity could be recomputed from the mass and radius each time it was used by thesurfaceWeightmethod, which takes an object’s mass and returns its weight on the planet represented by the constant.

While thePlanetenum is simple, it is surprisingly powerful. Here is a short program that takes the earth weight of an object \(in any unit\) and prints a nice table of the object’s weight on all eight planets \(in the same unit\):

Click here to view code image

public class WeightTable {  
 public static void main\(String\[\] args\) {

double earthWeight = Double.parseDouble\(args\[0\]\);  
 double mass = earthWeight / Planet.EARTH.surfaceGravity\(\); for \(Planet p : Planet.values\(\)\)

System.out.printf\("Weight on %s is %f%n", p, p.surfaceWeight\(mass\)\);

} }

Note thatPlanet, like all enums, has a staticvaluesmethod that returns an array of its values in the order they were declared. Note also that thetoStringmethod returns the declared name of each enum value, enabling easy printing byprintlnandprintf. If you’re dissatisfied with this string representation, you can change it by overriding thetoStringmethod. Here is the result of running

ourWeightTableprogram \(which doesn’t overridetoString\) with the command line argument 185:

Click here to view code image

Weight on MERCURY is 69.912739 Weight on VENUS is 167.434436 Weight on EARTH is 185.000000 Weight on MARS is 70.226739 Weight on JUPITER is 467.990696 Weight on SATURN is 197.120111

Weight on URANUS is 167.398264 Weight on NEPTUNE is 210.208751

Until 2006, two years after enums were added to Java, Pluto was a planet. This raises the question “what happens when you remove an element from an enum type?” The answer is that any client program that doesn’t refer to the removed element will continue to work fine. So, for example, ourWeightTableprogram would simply print a table with one fewer row. And what of a client program that refers to the removed element \(in this case,Planet.Pluto\)? If you recompile the client program, the compilation will fail with a helpful error message at the line that refers to the erstwhile planet; if you fail to recompile the client, it will throw a helpful exception from this line at runtime. This is the best behavior you could hope for, far better than what you’d get with the int enum pattern.

Some behaviors associated with enum constants may need to be used only from within the class or package in which the enum is defined. Such behaviors are best implemented as private or package-private methods. Each constant then carries with it a hidden collection of behaviors that allows the class or package containing the enum to react appropriately when presented with the constant. Just as with other classes, unless you have a compelling reason to expose an enum method to its clients, declare it private or, if need be, package-private \(Item 15\).

If an enum is generally useful, it should be a top-level class; if its use is tied to a specific top-level class, it should be a member class of that top-level class \(Item 24\). For example,  
 thejava.math.RoundingModeenum represents a rounding mode for decimal fractions. These rounding modes are used by

theBigDecimalclass, but they provide a useful abstraction that is not fundamentally tied toBigDecimal. By makingRoundingModea top-level enum, the library designers encourage any programmer who needs

rounding modes to reuse this enum, leading to increased consistency across APIs.  
 The techniques demonstrated in thePlanetexample are sufficient for most enum types, but sometimes you need more. There is different data associated with eachPlanetconstant, but sometimes you need to associate fundamentally differentbehaviorwith each constant. For example, suppose you are writing an enum type to represent the operations on a basic four-function calculator and you want to provide a method to perform the arithmetic operation represented by each constant. One way to achieve this is to switch on the value of the enum:

Click here to view code image

// Enum type that switches on its own value - questionable  
public enum Operation {

PLUS, MINUS, TIMES, DIVIDE;

// Do the arithmetic operation represented by this constant public double apply\(double x, double y\) {

switch\(this\) {  
 case PLUS: return x + y; case MINUS: return x - y; case TIMES: return x \* y; case DIVIDE: return x / y;

}

throw new AssertionError\("Unknown op: " + this\); }

}

This code works, but it isn’t very pretty. It won’t compile without thethrowstatement because the end of the method is technically reachable, even though it will never be reached \[JLS, 14.21\]. Worse,

the code is fragile. If you add a new enum constant but forget to add a corresponding case to theswitch, the enum will still compile, but it will fail at runtime when you try to apply the new operation. Luckily, there is a better way to associate a different behavior with each enum constant: declare an abstractapplymethod in the enum type, and override it with a concrete method for each constant in  
 aconstant-specific class body. Such methods are known  
 asconstant-specific method implementations:

Click here to view code image

// Enum type with constant-specific method implementations  
public enum Operation {

PLUS {public double apply\(double x, double y\){return x + y;}}, MINUS {public double apply\(double x, double y\){return x - y;}}, TIMES {public double apply\(double x, double y\){return x \* y;}}, DIVIDE{public double apply\(double x, double y\){return x / y;}};

public abstract double apply\(double x, double y\); }

If you add a new constant to the second version ofOperation, it is unlikely that you’ll forget to provide anapplymethod, because the method immediately follows each constant declaration. In the unlikely event that you do forget, the compiler will remind you because abstract methods in an enum type must be overridden with concrete methods in all of its constants.

Constant-specific method implementations can be combined with constant-specific data. For example, here is a version  
 ofOperationthat overrides thetoStringmethod to return the symbol commonly associated with the operation:

Click here to view code image

// Enum type with constant-specific class bodies and data

public enum Operation { PLUS\("+"\) {

public double apply\(double x, double y\) { return x + y; } },

MINUS\("-"\) {  
 public double apply\(double x, double y\) { return x - y; }

}, TIMES\("\*"\) {

public double apply\(double x, double y\) { return x \* y; } },

DIVIDE\("/"\) {  
 public double apply\(double x, double y\) { return x / y; }

};

private final String symbol;

Operation\(String symbol\) { this.symbol = symbol; }

@Override public String toString\(\) { return symbol; }

public abstract double apply\(double x, double y\); }

ThetoStringimplementation shown makes it easy to print arithmetic expressions, as demonstrated by this little program:

Click here to view code image

public static void main\(String\[\] args\) { double x = Double.parseDouble\(args\[0\]\); double y = Double.parseDouble\(args\[1\]\); for \(Operation op : Operation.values\(\)\)

System.out.printf\("%f %s %f = %f%n",

}

x, op, y, op.apply\(x, y\)\);

Running this program with 2 and 4 as command line arguments produces the following output:

Click here to view code image

2.000000 + 4.000000 = 6.000000 2.000000 - 4.000000 = -2.000000 2.000000 \* 4.000000 = 8.000000 2.000000 / 4.000000 = 0.500000

Enum types have an automatically  
 generatedvalueOf\(String\)method that translates a constant’s name into the constant itself. If you override thetoStringmethod in an enum type, consider writing afromStringmethod to translate the custom string representation back to the corresponding enum. The following code \(with the type name changed appropriately\) will do the trick for any enum, so long as each constant has a unique string representation:

Click here to view code image

// Implementing a fromString method on an enum type

private static final Map&lt;String, Operation&gt; stringToEnum = Stream.of\(values\(\)\).collect\(

toMap\(Object::toString, e -&gt; e\)\);

// Returns Operation for string, if any  
 public static Optional&lt;Operation&gt; fromString\(String symbol\) {

return Optional.ofNullable\(stringToEnum.get\(symbol\)\); }

Note that theOperationconstants are put into thestringToEnummap from a static field initialization that runs after the enum constants

have been created. The previous code uses a stream \(Chapter 7\) over the array returned by thevalues\(\)method; prior to Java 8, we would have created an empty hash map and iterated over the values array inserting the string-to-enum mappings into the map, and you can still do it that way if you prefer. But note that attempting to have each constant put itself into a map from its own constructor doesnotwork. It would cause a compilation error, which is good thing because if it were legal, it would cause  
 aNullPointerExceptionat runtime. Enum constructors aren’t permitted to access the enum’s static fields, with the exception of constant variables \(Item 34\). This restriction is necessary because static fields have not yet been initialized when enum constructors run. A special case of this restriction is that enum constants cannot access one another from their constructors.  
 Also note that thefromStringmethod returns anOptional&lt;String&gt;. This allows the method to indicate that the string that was passed in does not represent a valid operation, and it forces the client to confront this possibility \(Item 55\).  
 A disadvantage of constant-specific method implementations is that they make it harder to share code among enum constants. For example, consider an enum representing the days of the week in a payroll package. This enum has a method that calculates a worker’s pay for that day given the worker’s base salary \(per hour\) and the number of minutes worked on that day. On the five weekdays, any time worked in excess of a normal shift generates overtime pay; on the two weekend days, all work generates overtime pay. With  
 aswitchstatement, it’s easy to do this calculation by applying multiple case labels to each of two code fragments:

Click here to view code image

// Enum that switches on its value to share code - questionable  
enum PayrollDay {

MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY;

private static final int MINS\_PER\_SHIFT = 8 \* 60;

int pay\(int minutesWorked, int payRate\) { int basePay = minutesWorked \* payRate;

int overtimePay;

switch\(this\) {  
 case SATURDAY: case SUNDAY: // Weekend

overtimePay = basePay / 2;

break;  
 default: // Weekday

overtimePay = minutesWorked &lt;= MINS\_PER\_SHIFT ?

0 : \(minutesWorked - MINS\_PER\_SHIFT\) \* payRate / 2;

}

return basePay + overtimePay; }

}

This code is undeniably concise, but it is dangerous from a maintenance perspective. Suppose you add an element to the enum, perhaps a special value to represent a vacation day, but forget to add a corresponding case to theswitchstatement. The program will still compile, but thepaymethod will silently pay the worker the same amount for a vacation day as for an ordinary weekday.

To perform the pay calculation safely with constant-specific method implementations, you would have to duplicate the

overtime pay computation for each constant, or move the computation into two helper methods, one for weekdays and one for weekend days, and invoke the appropriate helper method from each constant. Either approach would result in a fair amount of boilerplate code, substantially reducing readability and increasing the opportunity for error.  
 The boilerplate could be reduced by replacing the  
 abstractovertimePaymethod onPayrollDaywith a concrete method that performs the overtime calculation for weekdays. Then only the weekend days would have to override the method. But this would have the same disadvantage as theswitchstatement: if you added another day without overriding theovertimePaymethod, you would silently inherit the weekday calculation.  
 What you really want is to beforcedto choose an overtime pay strategy each time you add an enum constant. Luckily, there is a nice way to achieve this. The idea is to move the overtime pay computation into a private nested enum, and to pass an instance of thisstrategy enumto the constructor for thePayrollDayenum.  
 ThePayrollDayenum then delegates the overtime pay calculation to the strategy enum, eliminating the need for aswitchstatement or constant-specific method implementation inPayrollDay. While this pattern is less concise than theswitchstatement, it is safer and more flexible:

Click here to view code image

// The strategy enum pattern

enum PayrollDay {  
 MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY\(PayType.WEEKEND\),

SUNDAY\(PayType.WEEKEND\); private final PayType payType;

PayrollDay\(PayType payType\) { this.payType = payType; } PayrollDay\(\) { this\(PayType.WEEKDAY\); } // Default

int pay\(int minutesWorked, int payRate\) {  
 return payType.pay\(minutesWorked, payRate\);

}

// The strategy enum type

private enum PayType { WEEKDAY {

int overtimePay\(int minsWorked, int payRate\) { return minsWorked &lt;= MINS\_PER\_SHIFT ? 0 :

\(minsWorked - MINS\_PER\_SHIFT\) \* payRate / 2; }

}, WEEKEND {

int overtimePay\(int minsWorked, int payRate\) { return minsWorked \* payRate / 2;

} };

abstract int overtimePay\(int mins, int payRate\); private static final int MINS\_PER\_SHIFT = 8 \* 60;

int pay\(int minsWorked, int payRate\) {  
 int basePay = minsWorked \* payRate;  
 return basePay + overtimePay\(minsWorked, payRate\);

} }

}

Ifswitchstatements on enums are not a good choice for implementing constant-specific behavior on enums, whatarethey

good for?Switches on enums are good for augmenting enum types with constant-specific behavior.For example, suppose theOperationenum is not under your control and you wish it had an instance method to return the inverse of each operation. You could simulate the effect with the following static method:

Click here to view code image

// Switch on an enum to simulate a missing method

public static Operation inverse\(Operation op\) { switch\(op\) {

case PLUS: return Operation.MINUS; case MINUS: return Operation.PLUS; case TIMES: return Operation.DIVIDE; case DIVIDE: return Operation.TIMES;

default: throw new AssertionError\("Unknown op: " + op\); }

}

You should also use this technique on enum types thatareunder your control if a method simply doesn’t belong in the enum type. The method may be required for some use but is not generally useful enough to merit inclusion in the enum type.

Enums are, generally speaking, comparable in performance  
 tointconstants. A minor performance disadvantage of enums is that there is a space and time cost to load and initialize enum types, but it is unlikely to be noticeable in practice.  
 So when should you use enums?Use enums any time you  
 need a set of constants whose members are known at compile time.Of course, this includes “natural enumerated types,” such as the planets, the days of the week, and the chess pieces. But it also includes other sets for which you know all the possible values at compile time, such as choices on a menu,

operation codes, and command line flags.It is not necessary that the set of constants in an enum type stay fixed for all time.The enum feature was specifically designed to allow for binary compatible evolution of enum types.  
 In summary, the advantages of enum types overintconstants are compelling. Enums are more readable, safer, and more powerful. Many enums require no explicit constructors or members, but others benefit from associating data with each constant and providing methods whose behavior is affected by this data. Fewer enums benefit from associating multiple behaviors with a single method. In this relatively rare case, prefer constant-specific methods to enums that switch on their own values. Consider the strategy enum pattern if some, but not all, enum constants share common behaviors.



