第8条：避免使用终结方法和清理方法

**Finalizers are unpredictable, often dangerous, and generally unnecessary. **Their use can cause erratic behavior, poor performance, and portability problems. Finalizers have a few valid uses, which we’ll cover later in this item, but as a rule, you should avoid them. As of Java 9, finalizers have been deprecated, but they are still being used by the Java libraries. The Java 9 replacement for finalizers is cleaners. **Cleaners are less dangerous than finalizers, but still unpredictable, slow, and generally unnecessary.**

**终结方法（Finalizer）是不可预知的，很多时候是危险的，而且一般情况下是不必要的。**使用它们会导致程序行为不稳定，性能降低还有移植问题。终结方法只有少数几种用途，我们将会本条目后面谈到。但根据经验，我们应该避免使用终结方法。在Java 9中，终结方法已经被遗弃了，但它们仍被Java类库使用，相应用来替代终结方法的是清理方法（cleaner）。**比起终结方法，清理方法相对安全点，但仍是不可以预知的，运行慢的，而且一般情况下是不必要的。**

C++ programmers are cautioned not to think of finalizers or cleaners as Java’s analogue of C++ destructors. In C++, destructors are the normal way to reclaim the resources associated with an object, a necessary counterpart to constructors. In Java, the garbage collector reclaims the storage associated with an object when it becomes unreachable, requiring no special effort on the part of the programmer. C++ destructors are also used to reclaim other nonmemory resources. In Java, a try-with-resources or try-finally block is used for this purpose \(Item 9\).

C++程序员要注意的是，不要把Java的终结方法或清理方法看成是C++析构器（destructor）。在C++中，析构器是回收对象占用资源的一种常规方式。而在Java中，垃圾回收器在对象变得不可到达的时候才回收对象占用的资源，不用程序员做额外的工作。C++的析构器也可以用来回收其它非内存资源。相应地，Java中使用try-with-resources或者try-finally代码块来实现这个目的（条目9）。

One shortcoming of finalizers and cleaners is that there is no guarantee they’ll be executed promptly \[JLS, 12.6\]. It can take arbitrarily long between the time that an object becomes unreachable and the time its finalizer or cleaner runs. This means that you should **never do anything time-critical in a finalizer or cleaner.**For example, it is a grave error to depend on a finalizer or cleaner to close files because open file descriptors are a limited resource. If many files are left open as a result of the system’s tardiness in running finalizers or cleaners, a program may fail because it can no longer open files.

终结方法和清理方法的一个缺点是无法保证它们及时地被执行\[JLS, 12.6\]。一个对象从变得不可到达开始到它的终结方法和清理方法被执行，中间可能会经过任意长的时间。这意味着，我们**不应该在终结方法和清理方法中做对时间有严格要求的任务。**例如，依赖终结方法或者清理方法来关闭文件资源是个严重的错误，因为打开文件的描述符是个有限的资源。如果在一段程序中很多文件都因为系统延迟执行终结方法或清理方法而停留在打开状态，那么当这段程序再打开一个文件时就会失败。

The promptness with which finalizers and cleaners are executed is primarily a function of the garbage collection algorithm, which varies widely across implementations. The behavior of a program that depends on the promptness of finalizer or cleaner execution may likewise vary. It is entirely possible that such a program will run perfectly on the JVM on which you test it and then fail miserably on the one favored by your most important customer.

及时地执行终结方法和清理方法是垃圾回收算法的一个主要功能。而在不同的JVM实现中，垃圾回收算法会有很大的不同。如果一个程序的行为依赖于终结方法和清理方法的执行时间点，那么在不同的JVM中这些行为也将同样会不同。那样的话完全有可能这样的一段程序在你的测试用的JVM上运行得很好，而在你最重要的客户的JVM上却根本无法运行。

Tardy finalization is not just a theoretical problem. Providing a finalizer for a class can arbitrarily delay reclamation of its instances. A colleague debugged a long-running GUI application that was mysteriously dying with an OutOfMemoryError. Analysis revealed that at the time of its death, the application had thousands of graphics objects on its finalizer queue just waiting to be finalized and reclaimed. Unfortunately, the finalizer thread was running at a lower priority than another application thread, so objects weren’t getting finalized at the rate they became eligible for finalization. The language specification makes no guarantees as to which thread will execute finalizers, so there is no portable way to prevent this sort of problem other than to refrain from using finalizers. Cleaners are a bit better than finalizers in this regard because class authors have control over their own cleaner threads, but cleaners still run in the background, under the control of the garbage collector, so there can be no guarantee of prompt cleaning.

延迟终结过程不仅仅只是个理论问题。为一个类提供终结方法会随意延迟其实例的回收过程。一个同事最近在调试一个长时间运行的GUI应用。该应用因为OutOfMemoryError错误而莫名其妙地死掉了。分析表明，在这个应用在死掉时，终结方法队列里有几千个图像对象在等待被终结和回收。不幸的是，终结方法线程的执行优先级比其它应用线程低，所以这些图像对象被终结的速度赶不上它们进入队列的速度。Java语言规范并没有保证哪个线程执行终结方法，所以，除了不使用终结方法，没有什么轻便的办法来防止这种问题的出现。清理方法在这方面比终结方法要稍微好点，因为类的作者可以控制他们自己的清理方法的线程，但清理方法仍然是在垃圾回收器的控制下在后台运行，所以也并不能保证及时清理。

Not only does the specification provide no guarantee that finalizers or cleaners will run promptly; it provides no guarantee that they’ll run at all. It is entirely possible, even likely, that a program terminates without running them on some objects that are no longer reachable. As a consequence, you should never depend on a finalizer or cleaner to update persistent state.For example, depending on a finalizer or cleaner to release a persistent lock on a shared resource such as a database is a good way to bring your entire distributed system to a grinding halt.

Java语言规范不仅不保证终结方法或清理方法会被及时运行，而且不保证它们最终会运行。这样的话完全有可能一个程序在终止的时候，某些已经无法访问的对象却还没被终结方法或清理方法处理。所以，我们应该永远也不依赖于终结方法或清理方法来更新持久化状态。例如，依赖于终结方法或清理方法来释放共享资源（比如数据库）的永久锁，将很容易使得整个分布式系统停止运行。

Don’t be seduced by the methods _System.gc_ and _System.runFinalization_. They may increase the odds of finalizers or cleaners getting executed, but they don’t guarantee it. Two methods once claimed to make this guarantee: _System.runFinalizersOnExit_ and its evil twin, _Runtime.runFinalizersOnExit_. These methods are fatally flawed and have been deprecated for decades \[ThreadStop\].

不要被_System.gc_和_System.runFinalization_两个方法给诱惑了。这两个方法也许会增加终结方法和清理方法被执行的概率，但也并不能保证一定会执行。有两个方法可以保证一旦被调用就执行终结方法和清理方法：_System.runFinalizersOnExit_和它臭名昭著的孪生兄弟，_Runtime.runFinalizersOnExit_。这两个方法都有致命的缺陷而且很久前就已经废弃了\[ThreadStop\]。

Another problem with finalizers is that an uncaught exception thrown during finalization is ignored, and finalization of that object terminates\[JLS, 12.6\]. Uncaught exceptions can leave other objects in a corrupt state. If another thread attempts to use such a corrupted object, arbitrary nondeterministic behavior may result.

终结方法的另一个问题是在终结过程中若有未被捕获的异常抛出，则抛出的异常会被忽略，而且该对象的终结过程也会终止\[JLS, 12.6\]。未捕获的异常会使别的对象处于破坏的状态。如果别的线程尝试着使用被破坏了的对象，那么有可能将出现任意不确定的行为。

Normally, an uncaught exception will terminate the thread and print a stack trace, but not if it occurs in a finalizer—it won’t even print a warning. Cleaners do not have this problem because a library using a cleaner has control over its thread.  
正常情况下，未捕获的异常会终止线程并打印错误栈，但假如这个异常出现在终结方法中就不会这么做了，甚至连警告都不会打印。清理方法不会有这个问题，因为使用清理方法的类库可以控制它自己的线程。

**There is a severe performance penalty for using finalizers and cleaners. **On my machine, the time to create a simple AutoCloseable object, to close it using try-with-resources, and to have the garbage collector reclaim it is about 12 ns. Using a finalizer instead increases the time to 550 ns. In other words, it is about 50 times slower to create and destroy objects with finalizers. This is primarily because finalizers inhibit efficient garbage collection. Cleaners are comparable in speed to finalizers if you use them to clean all instances of the class \(about 500 ns per instance on my machine\), but cleaners are much faster if you use them only as a safety net, as discussed below. Under these circumstances, creating, cleaning, and destroying an object takes about 66 ns on my machine, which means you pay a factor of five \(not fifty\) for the insurance of a safety net if you don’t use it.

**使用终结方法和清理方法还会导致严重的性能损失。**在我的机器上，创建一个简单的AutoCloseable对象，并通过try-with-resources来关闭它，然后让垃圾回收器回收它，整个过程花费了12纳秒。而使用清理方法后这个时间就增加到550纳秒了。换句话说，用终结方法来创建和销毁对象慢了大约50倍。这主要是因为终结方法会阻碍有效的垃圾回收。如果我们使用清理方法来清理类的所有对象，则其于终结方法速度相当（在我机器上是平局每个实例500纳秒），但如果我们将清理方法当作下面讨论到的安全网（safety net）来使用，则其比终结方法快很多。在这种情况下，创建，清除和销毁对象一个对象在我机器上整个过程花费了约66纳秒，这意味着如果我们不使用它，将要为其支付5倍（不是50倍）的保险。

**Finalizers have a serious security problem: they open your class up to finalizer attacks.**The idea behind a finalizer attack is simple: If an exception is thrown from a constructor or its serialization equivalents—the readObject and readResolve methods \(Chapter 12\)—the finalizer of a malicious subclass can run on the partially constructed object that should have “died on the vine.” This finalizer can record a reference to the object in a static field, preventing it from being garbage collected. Once the malformed object has been recorded, it is a simple matter to invoke arbitrary methods on this object that should never have been allowed to exist in the first place.**Throwing an exception from a constructor should be sufficient to prevent an object from coming into existence; in the presence of finalizers, it is not.**Such attacks can have dire consequences. Final classes are immune to finalizer attacks because no one can write a malicious subclass of a final class.**To protect nonfinal classes from finalizer attacks, write a final finalize method that does nothing.**

**终结方法还有一个严重的安全问题：它将你的类暴露于终结方法攻击（finalizer attack）。**终结方法攻击的背后机制很简单：如果一个异常从构造器或者序列化中抛出（对于序列化，主要是readObject和readResolve方法，见12章），恶意子类的终结方法可以运行在本应夭折的只构造了部分的对象上。终结方法可以在一个静态属性上记录对象的应用，从而阻止这个对象被垃圾回收。一旦记录了有缺陷的对象，就可以简单地调用该对象上的任意方法，而这些方法本来就不应该允许存在。**从构造方法里抛出异常应该足以防止对象被创建，但假如终结方法也存在，就不是这样了。**这种攻击会带来可怕的后果。final类能免疫于此类攻击，因为没有人能对final类进行恶意继承。**为了防止非final类遭受终结方法攻击，我们可以写一个什么都不做而且是final的终结方法。**

So what should you do instead of writing a finalizer or cleaner for a class whose objects encapsulate resources that require termination, such as files or threads? Just **have your class implement **_**AutoCloseable**_, and require its clients to invoke the _close_ method on each instance when it is no longer needed, typically using _try-with-resources_ to ensure termination even in the face of exceptions \(Item 9\). One detail worth mentioning is that the instance must keep track of whether it has been closed: the _close_ method must record in a field that the object is no longer valid, and other methods must check this field and throw an _IllegalStateException_ if they are called after the object has been closed.

所以，对于封装了需要终止使用的资源（比如文件或者线程），我们应该怎么做才能不用编写终止方法或者清理方法呢？我们只需**让类继承**_**AutoCloseable**_**接口**即可，并要求使用这个类的客户端在每个类实例都不再需要时就调用_close_方法，一般都是运用_try-with-resources_来保证资源的终止使用，即使抛出了异常，也能正确终止（条目9）。这里有个细节值得提到，实例必须能对其是否被关闭保持追踪：_close_方法必须在一个属性里声明此对象不再有效，其它方法必须校验这个属性，如果对象被关闭后它们还被调用，就要抛出一个_IllegalStateException_异常。

So what, if anything, are cleaners and finalizers good for? They have perhaps two legitimate uses. One is to act as a safety net in case the owner of a resource neglects to call its _close_ method. While there’s no guarantee that the cleaner or finalizer will run promptly \(or at all\), it is better to free the resource late than never if the client fails to do so. If you’re considering writing such a safety-net finalizer, think long and hard about whether the protection is worth the cost. Some Java library classes, such as _FileInputStream_, _FileOutputStream_, _ThreadPoolExecutor_, and _java.sql.Connection_, have finalizers that serve as safety nets.

那么清理方法或者终结方法有什么好处呢？它们可能有两种合法用途。一种用途是作为安全网，以防资源拥有者忘了调用资源的_close_方法。虽然清理方法或者终结方法并不保证会被及时执行（或根本就不运行），但晚释放总比客户端忘了释放好。如果你正考虑写一个这样的作为安全网的终结方法，应该想长远点，想想这层保护是否值得。一些Java类库，如_FileInputStream_，_FileOutputStream_，_ThreadPoolExecutor_，还有_java.sql.Connection_，都有作为安全网的终结方法。

A second legitimate use of cleaners concerns objects with native peers. A native peer is a native \(non-Java\) object to which a normal object delegates via native methods. Because a native peer is not a normal object, the garbage collector doesn’t know about it and can’t reclaim it when its Java peer is reclaimed. A cleaner or finalizer may be an appropriate vehicle for this task, assuming the performance is acceptable and the native peer holds no critical resources. If the performance is unacceptable or the native peer holds resources that must be reclaimed promptly, the class should have a _close_ method, as described earlier.

清理方法的第二种合法用途与对象的本地对等体（native peer）有关。本地对等体是指非Java实现的本地对象，普通对象通过本地方法代理给本地对象。由于本地对等体不是普通的对象，垃圾回收器并不知道它的存在进而当Java对等体被回收时也不会去回收它。而清理方法或终结方法正是适合完成这件事的工具，但前提条件是接受其性能并且本地对等体不持有关键资源。假如性能问题无法接受或者本地对等体持有的资源必须被及时回收，那么我们的类还是应该实现一个_close_方法，就如我们一开始提到。

Cleaners are a bit tricky to use. Below is a simple Room class

demonstrating the facility. Let’s assume that rooms must be

cleaned before they are reclaimed. The Room class

implements AutoCloseable; the fact that its automatic cleaning

safety net uses a cleaner is merely an implementation detail.

Unlike finalizers, cleaners do not pollute a class’s public API:

