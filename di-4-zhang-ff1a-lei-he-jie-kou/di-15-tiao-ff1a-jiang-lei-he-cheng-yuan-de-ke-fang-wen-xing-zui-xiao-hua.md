### 第15条：最小化类和成员的可访问性

The single most important factor that distinguishes a well-designed component from a poorly designed one is the degree to which the component hides its internal data and other implementation details from other components. A well-designed component hides all its implementation details, cleanly separating its API from its implementation. Components then communicate only through their APIs and are oblivious to each others’ inner workings. This concept, known as information hiding or encapsulation, is a fundamental tenet of software design \[Parnas72\].

要区分一个设计良好的组件和一个设计糟糕的组件，一个最重要的因素就是，这个组件将其内部的数据和其它实现细节向其它组件隐藏到了什么程度。一个设计良好的组件隐藏了它所有的实现细节，把它的API与它的实现清晰地隔离开来，然后组件之间通过它们各自的API进行交互，彼此之间不知道别的组件内部是如何工作的。这个概念被称为信息隐藏（information hiding）或者封装（encapsulation），是软件设计的一个基本宗旨\[Parnas72\]。

Information hiding is important for many reasons, most of which stem from the fact that it decouples the components that comprise a system, allowing them to be developed, tested, optimized, used, understood, and modified in isolation. This speeds up system development because components can be developed in parallel. It eases the burden of maintenance because components can be understood more quickly and debugged or replaced with little fear of harming other components. While information hiding does not, in and of itself, cause good performance, it enables effective performance tuning: once a system is complete and profiling has determined which components are causing performance problems \(Item 67\), those components can be optimized without affecting the correctness of others. Information hiding increases software reuse because components that aren’t tightly coupled often prove useful in other contexts besides the ones for which they were developed. Finally, information hiding decreases the risk in building large systems because individual components may prove successful even if the system does not.

  
  


