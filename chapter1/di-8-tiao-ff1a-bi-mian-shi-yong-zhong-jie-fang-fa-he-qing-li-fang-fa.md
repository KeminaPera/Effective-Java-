### 第8条：避免使用终结方法和清理方法

**Finalizers are unpredictable, often dangerous, and generally unnecessary. **Their use can cause erratic behavior, poor performance, and portability problems. Finalizers have a few valid uses, which we’ll cover later in this item, but as a rule, you should avoid them. As of Java 9, finalizers have been deprecated, but they are still being used by the Java libraries. The Java 9 replacement for finalizers is cleaners. **Cleaners are less dangerous than finalizers, but still unpredictable, slow, and generally unnecessary.**

**终结方法（Finalizer）是不可预知的，很多时候是危险的，而且一般情况下是不必要的。**使用它们会导致程序行为不稳定，性能降低还有移植问题。终结方法只有少数几种用途，我们将会本条目后面谈到。但根据经验，我们应该避免使用终结方法。在Java 9中，终结方法已经被遗弃了，但它们仍被Java类库使用，相应用来替代终结方法的是清理方法（cleaner）。比起终结方法，清理方法相对安全点，但仍是不可以预知的，运行慢的，而且一般情况下是不必要的。

