Static factories and constructors share a limitation: they do not scale well to large numbers of optional parameters. Consider the case of a class representing the Nutrition Facts label that appears on packaged foods. These labels have a few required fields—serving size, servings per container, and calories per serving—and more than twenty optional fields—total fat, saturated fat, trans fat, cholesterol, sodium, and so on. Most products have nonzero values for only a few of these optional fields.

What sort of constructors or static factories should you write for such a class? Traditionally, programmers have used the telescoping constructor pattern, in which you provide a constructor with only the required parameters, another with a single optional parameter, a third with two optional parameters, and so on, culminating in a constructor with all the optional parameters. Here’s how it looks in practice. For brevity’s sake, only four optional fields are shown:

静态工厂和构造器都有个共同的不足的地方：它们都不能很好地扩展到大量的可选参数。考虑这么一种情况，用一个类来表示包装食品外面的营养成分标签。这些标签里有几个属性是必须有的：每份的含量，每罐的含量以及每份的卡路里，同时还有20个可选的属性：总脂肪含量、饱和脂肪含量、胆固醇含量、钠含量等等。大多数只在某几个可选属性里会有非零值。

对于这样的类，我们应该如何编写构造器或者静态工厂？一般情况下，程序员会习惯于用可伸缩构造器（telescoping constructor pattern）模式。在这种模式下，程序员会先提供一个只有必要参数的构造器，然后在这个构造器的基础上，提供一个还要需要有一个可选参数的构造器，接着提供一个需要有两个可选参数的构造器，以此类推，终于在最后一个构造器的参数列表里，不仅包含了那几个必要的参数，还包含了所有的可选参数。下面有个例子。为了简单起见，它只显示4个可选属性：

```
// Telescoping constructor pattern - does not scale well!
public class NutritionFacts {
    private final int servingSize; // (mL) required 
    private final int servings;    // (per container) required
    private final int calories;    // (per serving) optional    
    private final int fat;         // (g/serving) optional
    private final int sodium;      // (mg/serving) optional
    private final int carbohydrate; // (g/serving) optional
    public NutritionFacts(int servingSize, int servings) { 
        this(servingSize, servings, 0);
    }
    public NutritionFacts(int servingSize, int servings, int calories) {
        this(servingSize, servings, calories, 0); 
    }
    public NutritionFacts(int servingSize, int servings, int calories, int fat) {
        this(servingSize, servings, calories, fat, 0); 
    }
    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium) {
        this(servingSize, servings, calories, fat, sodium, 0); 
    }
    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, 
        int carbohydrate) {
        this.servingSize = servingSize; this.servings = servings;
        this.calories = calories
        this.fat = fat
        this.sodium = sodium
        this.carbohydrate = carbohydrate;
    } 
}
```

When you want to create an instance, you use the constructor with the shortest parameter list containing all the parameters you want to set:

当你想创建一个实例时，就用那个包含了所有你想设置的参数而且是参数列表最短的那个构造器：

```
NutritionFacts cocaCola = new NutritionFacts(240, 8, 100, 0, 35, 27);
```

Typically this constructor invocation will require many parameters that you don’t want to set, but you’re forced to pass a value for them anyway. In this case, we passed a value of 0 for fat. With “only” six parameters this may not seem so bad, but it quickly gets out of hand as the number of parameters increases.

这个构造器的调用通常需要很多你原本不想设置的参数，但你还是不得不给这些参数传一个值进去。在上述的例子中，我们为参数fat传了0值进去。若仅仅是这6个参数，情况还好点，但随着参数的增多，很快就开始失控。

In short, the telescoping constructor pattern works, but it is hard to write client code when there are many parameters, and harder still to read it. The reader is left wondering what all those values mean and must carefully count parameters to find out. Long sequences of identically typed parameters can cause subtle bugs. If the client accidentally reverses two such parameters, the compiler won’t complain, but the program will misbehave at runtime \(Item 51\).

简而言之，可伸缩构造器是可行，只是当有很多参数时，会让客户端代码很难编写，而且代码也很难阅读。读者若想知道传入的那些值代表什么，就必须得仔细地数着这些参数来一探究竟。一长串相同类型的参数还会导致一些难以察觉的错误。假如客户端不小心将两个相同类型的参数调换来位置，编译器不会报错，但程序在运行时就不会按照我们所想的去做了（条目51）。

A second alternative when you’re faced with many optional parameters in a constructor is the Java Beans pattern, in which you call a parameterless constructor to create the object and then call setter methods to set each required parameter and each optional parameter of interest:

对于这种一个构造器里有很多可选参数的情况，另一种可选的方案就是采用Java Beans模式。若采用这种模式，则先调用无参构造器来创建一个对象，然后分别调用不同的setter方法来设置必要参数和可选参数：

```

// JavaBeans Pattern - allows inconsistency, mandates mutability
public class NutritionFacts {
    // Parameters initialized to default values (if any)
    private int servingSize = -1; // Required; no default value 
    private int servings = -1; // Required; no default value
    private int calories = 0;
    private int fat = 0;
    private int sodium = 0;
    private int carbohydrate = 0;
    public NutritionFacts() { }
    // Setters
    public void setServingSize(int val) { 
        servingSize = val; 
    } 
    public void setServings(int val) { 
        servings = val; 
    }
    public void setCalories(int val) {
        calories = val;
    }
    public void setFat(int val) {
        fat = val;
    }
    public void setSodium(int val) {
        sodium = val;
    }
    public void setCarbohydrate(int val) { 
        carbohydrate = val; 
    }
}
```



