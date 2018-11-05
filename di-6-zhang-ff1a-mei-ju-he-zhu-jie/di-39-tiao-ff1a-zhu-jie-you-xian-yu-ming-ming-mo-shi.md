Historically, it was common to use naming patterns to indicate that some program elements demanded special treatment by a tool or framework. For example, prior to release 4, the JUnit testing framework required its users to designate test methods by beginning their names with the characters _test_\[Beck04\]. This technique works, but it has several big disadvantages. First, typographical errors result in silent failures. For example, suppose you accidentally named a test method _tsetSafetyOverride_ instead of _testSafetyOverride_. JUnit 3 wouldn’t complain, but it wouldn’t execute the test either, leading to a false sense of security.

在过去，经常会用命名模式来表明一些程序元素需要得到工具或者框架的特殊处理。例如，在第4个release版本之前，JUnit测试框架要求用户一定要以_test_作为测试方法的开头\[Beck04\]。这么做虽然可行，但是它有几个大的缺点。首先，文字拼写错误会导致失败。比如，假设你不小心将一个测试方法命名为_tsetSafetyOverride_，而不是_testSafetyOverride_。JUnit 3将不会报错，但它也将不会执行测试代码，从而导致让人误以为代码测试通过没问题。

A second disadvantage of naming patterns is that there is no way to ensure that they are used only on appropriate program elements. For example, suppose you called a class _TestSafetyMechanisms_ in hopes that JUnit 3 would automatically test all of its methods, regardless of their names. Again, JUnit 3 wouldn’t complain, but it wouldn’t execute the tests either.

命名模式的第二个缺点是，无法确保它们被用在恰当的程序元素里。例如，假设你调用了_TestSafetyMechanisms_类，以期望JUnit 3能自动测试这个类的所有方法，无论这些方法是如何命名的。同样，JUnit 3也不会报错，但它也同样不会执行测试代码。

A third disadvantage of naming patterns is that they provide no good way to associate parameter values with program elements. For example, suppose you want to support a category of test that succeeds only if it throws a particular exception. The exception type is essentially a parameter of the test. You could encode the exception type name into the test method name using some elaborate naming pattern, but this would be ugly and fragile \(Item 62\). The compiler would have no way of knowing to check that the string that was supposed to name an exception actually did. If the named class didn’t exist or wasn’t an exception, you wouldn’t find out until you tried to run the test.

命名模式的第三个缺点是，它没法提供将参数值与程序元素关联起来的好办法。例如，假设你想让一类测试只有在抛出特定异常时才算成功。这个异常类型本质上是这个测试的参数。你可以某些具体的命名模式将异常类型名字编码进测试方法名字里，但这么做会显得不优雅而且还会让代码变得不够健壮（条目62）。编译器将无法知道要去校验用来命名异常的字符串是否真的起作用。如果被命名的类不存在或者并不是一个异常，那么在运行测试前你都将发现不到这个问题。

Annotations \[JLS, 9.7\] solve all of these problems nicely, and JUnit adopted them starting with release 4. In this item, we’ll write our own toy testing framework to show how annotations work. Suppose you want to define an annotation type to designate simple tests that are run automatically and fail if they throw an exception. Here’s how such an annotation type, named _Test_, might look:

而注解（Annotation）\[JLS, 9.7\] 可以完美地解决这些问题，并且JUnit从第4个release版本开始也采用了这种方式。在本条目里，我们将编写一个简易的测试框架来展示注解是如何运作的。假设你想定义一个注解类型来指定一些简单的测试可以自动运行并且在抛出异常时失败。以下是这个注解类型的代码，这个注解命名为_Test_：

**// Marker annotation type declaration**

```java
import java.lang.annotation.*;
/**
* Indicates that the annotated method is a test method. 
* Use only on parameterless static methods.
*/
@Retention(RetentionPolicy.RUNTIME) 
@Target(ElementType.METHOD) 
public @interface Test {
}
```

The declaration for the _Test_ annotation type is itself annotated with _Retention_ and _Target_ annotations. Such annotations on annotation type declarations are known as meta-annotations. The _@Retention\(RetentionPolicy.RUNTIME\)_ meta-annotation indicates that _Test_ annotations should be retained at runtime. Without it, _Test_ annotations would be invisible to the test tool. The _@Target.get\(ElementType.METHOD\) \_meta-annotation indicates that the \_Test_ annotation is legal only on method declarations: it cannot be applied to class declarations, field declarations, or other program elements.

_Test_注解的声明就是它自身用_Retention_注解和_Target_注解进行了注解。这种注解类型声明上的注解被称为元注解（meta-annotation）。_@Retention\(RetentionPolicy.RUNTIME\)_元注解表明_Test_注解应该在运行时保留。如果没有加上这个注解，那么_Test_注解将对测试工具不可见。_@Target.get\(ElementType.METHOD\)_元注解表明_Test_注解只有用在方法声明上才是合法的，也就是它不能用于类声明，属性声明，或者其它的程序元素。

The comment before the _Test_ annotation declaration says, “Use only on parameterless static methods.” It would be nice if the compiler could enforce this, but it can’t, unless you write an annotation processor to do so. For more on this topic, see the documentation for _javax.annotation.processing_. In the absence of such an annotation processor, if you put a _Test_ annotation on the declaration of an instance method or on a method with one or more parameters, the test program will still compile, leaving it to the testing tool to deal with the problem at runtime.

_Test_注解声明上面的注释说道，“只用于无参的静态方法。”如果编译器能强制这一点那自然是最好，但它不能，除非你写一个注解处理器来实现这一点。关于这个话题，请阅读_javax.annotation.processing_的文档。在没有提供相应的注解处理器的情况下，如果你将_Test_注解置于实例方法或者带有一个或多个参数的方法之上，测试程序还是可以编译，然后让测试工具在运行时处理这个问题。

**// Program containing marker annotations**

```java
public class Sample {
    @Test 
    public static void m1() { } // Test should pass 
    public static void m2() { }
    @Test 
    public static void m3() { // Test should fail
        throw new RuntimeException("Boom"); 
    }
    public static void m4() { }
    @Test 
    public void m5() { } // INVALID USE: nonstatic method
    public static void m6() { }
    @Test 
    public static void m7() { // Test should fail
        throw new RuntimeException("Crash"); 
    }
    public static void m8() { } 
}
```

The _Sample_ class has seven static methods, four of which are annotated as tests. Two of these, _m3_ and _m7_, throw exceptions, and two, _m1_ and _m5_, do not. But one of the annotated methods that does not throw an exception, _m5_, is an instance method, so it is not a valid use of the annotation. In sum, _Sample_ contains four tests: one will pass, two will fail, and one is invalid. The four methods that are not annotated with the _Test_ annotation will be ignored by the testing tool.

_Sample_类有7个静态方法，其中4个被注解为测试代码。在这4个方法里，方法_m3_和方法_m7_都抛出了异常，方法_m1_和方法m5则没有。在没有抛出异常的注解方法里，由于方法_m5_是个实例方法，所以此处的注解应用是无效的。总的来说，_Sample_包含了4个测试，一个会通过，两个会失败，一个是无效的。另外4个没有用_Test_注解的方法则将被测试工具忽略。

The _Test_ annotations have no direct effect on the semantics of the _Sample_ class. They serve only to provide information for use by interested programs. More generally, annotations don’t change the semantics of the annotated code but enable it for special treatment by tools such as this simple test runner:

_Test_注解对_Sample_类的语义没有直接影响。它们仅提供信息给相关程序使用。一般来说，注解不会改变被注解代码的语义，但其能让某些工具对被注解的代码进行特殊处理，例如下面这个简单的测试运行类：

**// Program to process marker annotations**

```java
import java.lang.reflect.*;

public class RunTests {
    public static void main(String[] args) throws Exception {
        int tests = 0;
        int passed = 0;
        Class<?> testClass = Class.forName(args[0]);
        for (Method m : testClass.getDeclaredMethods()) { 
            if (m.isAnnotationPresent(Test.class)) {
                tests++; 
                try {
                    m.invoke(null);
                    passed++;
                } catch (InvocationTargetException wrappedExc) {
                    Throwable exc = wrappedExc.getCause();
                    System.out.println(m + " failed: " + exc); 
                } catch (Exception exc) {
                    System.out.println("Invalid @Test: " + m); 
                }
            } 
        }
        System.out.printf("Passed: %d, Failed: %d%n", passed, tests - passed);
    } 
}
```

The test runner tool takes a fully qualified class name on the command line and runs all of the class’s _Test_-annotated methods reflectively, by calling _Method.invoke_. The _isAnnotationPresent_ method tells the tool which methods to run. If a test method throws an exception, the reflection facility wraps it in an _InvocationTargetException_. The tool catches this exception and prints a failure report containing the original exception thrown by the test method, which is extracted from the _InvocationTargetException_ with the _getCause_ method.

上面的测试运行工具在使用时需要往命令行里传入完全匹配的类名，并通过调用Method.invoke以反射的方式来运行相应类里加了_Test_注解的方法。_isAnnotationPresent_方法告诉工具要运行哪个方法。如果某个测试方法抛出了异常，那么反射机制将会把它包装成_InvocationTargetException_异常。工具会抓获这个异常并将测试方法抛出的原始异常打印出来，而这个原始异常则是通过_getCause_方法从_InvocationTargetException_抽离出来。

If an attempt to invoke a test method by reflection throws any exception other than _InvocationTargetException_, it indicates an invalid use of the _Test_ annotation that was not caught at compile time. Such uses include annotation of an instance method, of a method with one or more parameters, or of an inaccessible method. The second catch block in the test runner catches these Test usage errors and prints an appropriate error message. Here is the output that is printed if _RunTests_ is run onSample:

```bash
public static void Sample.m3() failed: RuntimeException: Boom Invalid @Test: public void Sample.m5()
public static void Sample.m7() failed: RuntimeException: Crash Passed: 1, Failed: 3

```



