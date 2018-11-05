Historically, it was common to use naming patterns to indicate that some program elements demanded special treatment by a tool or framework. For example, prior to release 4, the JUnit testing framework required its users to designate test methods by beginning their names with the characters _test_\[Beck04\]. This technique works, but it has several big disadvantages. First, typographical errors result in silent failures. For example, suppose you accidentally named a test method _tsetSafetyOverride_ instead of _testSafetyOverride_. JUnit 3 wouldn’t complain, but it wouldn’t execute the test either, leading to a false sense of security.

在过去，经常会用命名模式来表明一些程序元素需要得到工具或者框架的特殊处理。例如，在第4个release版本之前，JUnit测试框架要求用户一定要以_test_作为测试方法的开头\[Beck04\]。这么做虽然可行，但是它有几个大的缺点。首先，文字拼写错误会导致失败。比如，假设你不小心将一个测试方法命名为_tsetSafetyOverride_，而不是_testSafetyOverride_。JUnit 3将不会报错，但它也将不会执行测试代码，从而导致让人误以为代码测试通过没问题。

A second disadvantage of naming patterns is that there is no way to ensure that they are used only on appropriate program elements. For example, suppose you called a class _TestSafetyMechanisms_ in hopes that JUnit 3 would automatically test all of its methods, regardless of their names. Again, JUnit 3 wouldn’t complain, but it wouldn’t execute the tests either.

命名模式的第二个缺点是，无法确保它们被用在恰当的程序元素里。例如，假设你调用了_TestSafetyMechanisms_类，以期望JUnit 3能自动测试这个类的所有方法，无论这些方法是如何命名的。同样，JUnit 3也不会报错，但它也同样不会执行测试代码。

A third disadvantage of naming patterns is that they provide no good way to associate parameter values with program elements. For example, suppose you want to support a category of test that succeeds only if it throws a particular exception. The exception type is essentially a parameter of the test. You could encode the exception type name into the test method name using some elaborate naming pattern, but this would be ugly and fragile \(Item 62\). The compiler would have no way of knowing to check that the string that was supposed to name an exception actually did. If the named class didn’t exist or wasn’t an exception, you wouldn’t find out until you tried to run the test.

命名模式的第三个缺点是，它没法提供将参数值与程序元素关联起来的好办法。例如，假设你想让一类测试只有在抛出特定异常时才算成功。这个异常类型本质上是这个测试的参数。你可以某些具体的命名模式将异常类型名字编码进测试方法名字里，但这么做会显得不优雅而且还会让代码变得不够健壮（条目62）。编译器将无法知道要去校验用来命名异常的字符串是否真的起作用。如果被命名的类不存在或者并不是一个异常，那么在运行测试前你都将发现不到这个问题。

