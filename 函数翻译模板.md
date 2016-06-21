## after(long millis)函数

`public static VerificationAfterDelay after(long millis)`

在给定的时间后进行验证。它会为了预期的效果进行等待一段时间后进行验证，而不是因为没发生而立即失败。这可能对于测试多并发条件非常有用。

`after()`等待整个周期的特点不同于`timeout()`，而`timeout()`一旦验证通过就尽快停止，例如：当使用`times(2)`可以产生不同的行为方式，可能通过后过会又失败。这种情况下，`timeout`只要`times(2)`通过就会通过，然后after执行完整个周期时间，可能会失败，也意味着`times(2)`也失败。

感觉这个方法应该少使用——找到更好的方法测试你的多线程系统。对尚未实现的工作进行验证。

```java
//passes after 100ms, if someMethod() has only been called once at that time.
// 传递参数为100毫秒,100毫秒之后someMethod()会被调用一次
verify(mock, after(100)).someMethod();
//above is an alias to:
verify(mock, after(100).times(1)).someMethod();

//passes if someMethod() is called *exactly* 2 times after the given timespan
verify(mock, after(100).times(2)).someMethod();

//passes if someMethod() has not been called after the given timespan
verify(mock, after(100).never()).someMethod();

//verifies someMethod() after a given time span using given verification mode
//useful only if you have your own custom verification modes.
verify(mock, new After(100, yourOwnVerificationMode)).someMethod();
```

参照Mockito类的javadoc帮助文档中的例子

**Parameters:**

 * millis - - time span in milliseconds 等待的时间(单位为毫秒)

**Returns:**       

* verification mode 验证模型