### 第8条：避免使用终结方法和清理方法

**Finalizers are unpredictable, often dangerous, and generally unnecessary. **Their use can cause erratic behavior, poor performance, and portability problems. Finalizers have a few valid uses, which we’ll cover later in this item, but as a rule, you should avoid them. As of Java 9, finalizers have been deprecated, but they are still being used by the Java libraries. The Java 9 replacement for finalizers is cleaners. **Cleaners are less dangerous than finalizers, but still unpredictable, slow, and generally unnecessary.**

  


