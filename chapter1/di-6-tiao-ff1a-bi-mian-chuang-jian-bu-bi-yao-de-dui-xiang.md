### 第6条：避免创建不必要的对象

It is often appropriate to reuse a single object instead of creating a

new functionally equivalent object each time it is needed. Reuse

can be both faster and more stylish. An object can always be reused

if it is immutable \(Item 17\).

As an extreme example of what not to do, consider this statement:



