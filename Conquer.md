![](http://img.blog.csdn.net/20150731162529393)

# n-w开头的函数

##  never()函数

`public static VerificationMode never()`   

Alias to times(0), see times(int) 
Verifies that interaction did not happen. E.g:

相当于`times(0)`,可参见 `times(int)`    
验证交互没有发生. 例如:    

```java
verify(mock, never()).someMethod();
```

If you want to verify there were NO interactions with the mock check out verifyZeroInteractions(Object...) or verifyNoMoreInteractions(Object...)

See examples in javadoc for Mockito class

如果你想验证`mock`之间没有交互，可以使用`verifyZeroInteractions(Object...)` 或者 `verifyNoMoreInteractions(Object...)` 这两个方法

具体例子可以参考`Javadoc`中的`Mockito`类

**Returns:**

* verification mode 

* 验证模式

##  only()函数

 `public static VerificationMode only()`  
  
 Allows checking if given method was the only one invoked. E.g: 
 
 如果当前`mock`的方法只被调用一次，则允许被检验。例如：

```java

   verify(mock, only()).someMethod();
   //上面这行代码是下面这两行代码的简写
   
   verify(mock).someMethod();
   verifyNoMoreInvocations(mock);

```

See also verifyNoMoreInteractions(Object...)

See examples in javadoc for Mockito class

可以参考`verifyNoMoreInteractions(Object...)`方法

具体例子可以参考`Javadoc`中的`Mockito`类

**Returns:**

* verification mode 


##  reset(T... mocks)函数

`public static <T> void reset(T... mocks)`

Smart Mockito users hardly use this feature because they know it could be a sign of poor tests. Normally, you don't need to reset your mocks, just create new mocks for each test method.

Instead of #reset() please consider writing simple, small and focused test methods over lengthy, over-specified tests. First potential code smell is reset() in the middle of the test method. This probably means you're testing too much. Follow the whisper of your test methods: "Please keep us small & focused on single behavior". There are several threads about it on mockito mailing list.

The only reason we added reset() method is to make it possible to work with container-injected mocks. See issue 55 (here) or FAQ (here).

Don't harm yourself. reset() in the middle of the test method is a code smell (you're probably testing too much).

聪明的程序员很少会使用这个方法，因为他们知道使用这个方法意味着这个测试写的很low.通常情况下，你不需要重置你的`mocks`,你仅仅需要为你的测试方法创建新的`mocks`就可以了。

你可以考虑写一些简单的、精悍的、聚焦的测试方法来代替`reset()`这个方法。当你在在测试方法的中间部分用到reset()这个方法时，说明你的测试方法太庞大了。

请遵循以下关于测试方法的建议：`请保证你的测试方法在一个动作中短小、精悍、聚焦`。在`mockito`的邮件列表中有很多关于这方面的主题讨论。

我们添加`reset()`方法的唯一原因是使得注入容器的`mocks`得以有效的运行，具体可以参看issue 55 [here](http://code.google.com/p/mockito/issues/detail?id=55) or FAQ [here](http://code.google.com/p/mockito/wiki/FAQ)

不要败坏了你在程序员界的名声，测试方法中间的`reset()`方法是代码中的害群之马（这意味着你的这个测试方法太多）


```java
   List mock = mock(List.class);
   when(mock.size()).thenReturn(10);
   mock.add(1);

   reset(mock);
   //此时会清除你之前所有的交互以及测试桩
     
```

 
**Type Parameters:**

* T - The Type of the mocks

* T - mocks的类型

**Parameters:**

* mocks - to be reset 

* 被重置的mocks


##  spy(Class<T> classToSpy)函数

`@Incubating 
public static <T> T spy(Class<T> classToSpy)`

Please refer to the documentation of spy(Object). Overusing spies hints at code design smells.

This method, in contrast to the original spy(Object), creates a spy based on class instead of an object. Sometimes it is more convenient to create spy based on the class and avoid providing an instance of a spied object.

This is particularly useful for spying on abstract classes because they cannot be instantiated. See also MockSettings.useConstructor().

Examples:

请参考关于类`spy`的文档，过渡使用`spy`会导致代码变的非常糟糕。

相比与原来的`spy`（对象），这种方法可以在类的基础上创建一个`spy`，而不是一个对象。有时你可以很方便基于类创建spy而避免提供一个`spy`对象的实例。

因为他们不能被实例化，所以这个对于抽象类的监控非常有用。参见`mocksettings.useconstructor()`。

例如：


```java

   SomeAbstract spy = spy(SomeAbstract.class);

   //Robust API, via settings builder:
   //稳定的API，充过builder方式来设置
   
   OtherAbstract spy = mock(OtherAbstract.class, withSettings()
      .useConstructor().defaultAnswer(CALLS_REAL_METHODS));

   //Mocking a non-static inner abstract class:
   //模拟一个非静态抽象内部类
   
   InnerAbstract spy = mock(InnerAbstract.class, withSettings()
      .useConstructor().outerInstance(outerInstance).defaultAnswer(CALLS_REAL_METHODS));
      
```

**Type Parameters:**

* T - type of the spy

* T - spy的类型

**Parameters:**

* classToSpy 

* spy的类

**Returns:**

* a spy of the provided class

**Since:**

* 1.10.12


##  stub(T methodCall)函数

`public static <T> DeprecatedOngoingStubbing<T> stub(T methodCall)`
 
Stubs a method call with return value or an exception. E.g:

对一个方法打桩会返回结果值或者错误异常，例如：

```java

 stub(mock.someMethod()).toReturn(10);

 //you can use flexible argument matchers, e.g:
 //你可以使用灵活的参数匹配，例如：
 
 stub(mock.someMethod(anyString())).toReturn(10);

 //setting exception to be thrown:
 //设置抛出的异常
 
 stub(mock.someMethod("some arg")).toThrow(new RuntimeException());

 //you can stub with different behavior for consecutive method calls.
 // 你可以对不同作用的连续回调的方法打测试桩：
 //Last stubbing (e.g: toReturn("foo")) determines the behavior for further consecutive calls.
 // 最后面的测试桩（例如：返回一个对象："foo"）决定了接下来的回调方法以及它的行为。
 
 stub(mock.someMethod("some arg"))
  .toThrow(new RuntimeException())
  .toReturn("foo");
  
```
 
 
Some users find `stub()` confusing therefore `when(Object)` is recommended over `stub()`
 
 一些用户有点混乱、混淆，是因为相比于'stub()'，'when(Object)'更加被推荐

```
   //Instead of:
   //替代为：
   stub(mock.count()).toReturn(10);

	//你可以这样做：
   //You can do:
   when(mock.count()).thenReturn(10);
```

For stubbing void methods with throwables see: doThrow(Throwable)
Stubbing can be overridden: for example common stubbing can go to fixture setup but the test methods can override it. Please note that overridding stubbing is a potential code smell that points out too much stubbing.

当对一个返回值为空且抛出异常的方法打测试桩：`doThrow(Throwable)`测试桩会被重写：例如通常测试桩会设置为常用固定设置，但测试方法可以重写它。切记重写测试桩是一种非常不推荐的写法，因为这样做会导致非常多的测试桩。

Once stubbed, the method will always return stubbed value regardless of how many times it is called.

一旦这个方法打过桩，无论这个方法被调用多少次，这个方法会一直返回这个测试桩的值。

Last stubbing is more important - when you stubbed the same method with the same arguments many times.

当你对相同的方法调用相同的参数打测试桩很多次，最后面的测试桩则非常重要

Although it is possible to verify a stubbed invocation, usually it's just redundant. Let's say you've stubbed foo.bar(). If your code cares what foo.bar() returns then something else breaks(often before even verify() gets executed). If your code doesn't care what get(0) returns then it should not be stubbed. Not convinced? See here.

尽管我们可以去验证对测试桩的调用，但通常它都是多余的。比如说你对`foo.bar()`打测试桩。如果你比较关心的是当某些情况`foo.bar()`中断了（经常在`verify()`方法执行之前）,此时会返回什么。如果你的代码不关心是`get(0)`会返回什么那么它就不应该被添加测试桩。如果你还不确定？看[这里](http://monkeyisland.pl/2008/04/26/asking-and-telling)


**Parameters:**

* methodCall - method call

* methodCall - 调用的方法

**Returns:**

* DeprecatedOngoingStubbing object to set stubbed value/exception

* DeprecatedOngoingStubbing 对象是用来设置测试桩的值或者异常的


##  stubVoid(T mock)函数

 `public static <T> VoidMethodStubbable<T> stubVoid(T mock)`

Deprecated. Use doThrow(Throwable) method for stubbing voids  

已废弃.使用`doThrow(Throwable)`方法代替去打空测试桩

```
	//Instead of:
	//替代为：
   stubVoid(mock).toThrow(e).on().someVoidMethod();

   //Please do:
   //请这样做：
   doThrow(e).when(mock).someVoidMethod();
 
```
doThrow() replaces stubVoid() because of improved readability and consistency with the family of doAnswer() methods.
Originally, stubVoid() was used for stubbing void methods with exceptions. E.g:

`doThrow()`之所以取代了`stubVoid()`方法，是因为它增加了和它的兄弟方法`doAnswer()`的可读性以及一致性

```

 stubVoid(mock).toThrow(new RuntimeException()).on().someMethod();

 //you can stub with different behavior for consecutive calls.
 //你可以对不同作用的连续回调的方法打测试桩：
 //Last stubbing (e.g. toReturn()) determines the behavior for further consecutive calls.
 //最后面的测试桩（例如：`toReturn()`）决定了接下来的回调方法以及它的行为。
 
 stubVoid(mock)
   .toThrow(new RuntimeException())
   .toReturn()
   .on().someMethod();
```

See examples in javadoc for Mockito class

具体例子可以参考`Javadoc`中的`Mockito`类

**Parameters:**

* mock - to stub

**Returns:**

* stubbable object that allows stubbing with throwable



##  timesout(long millis)函数

`public static VerificationWithTimeout timeout(long millis)`

Allows verifying with timeout.It causes a verify to wait for a specified period of time for a desired interaction rather than fails immediately if has not already happened.This differs from after() in that after() will wait the full period, unless the final test result is known early (e.g. if a never() fails), whereas timeout() will stop early as soon as verification passes, producing different behaviour when used with times(2), for example, which can pass and then later fail. In that case, timeout would pass as soon as times(2) passes, whereas after would run until times(2) failed, and then fail.

允许验证时使用`timeout`。它会在指定的时间后触发你所期望的动作，而不是立即失败，也许这个对并发条件下的测试非常有用。它和`after()`是有所有不同的，因为`after()`会等候一个完整的时期，除非最终的测试结果很快就出来了(例如：当`never()`失败了), 然而当验证通过时，`timeout()`会快速地停止，当你使用`times(2)`时会产生不同的行为。例如，当先通过然后失败，在这种情况下，`timeout`将会当`time(2)`通过时迅速通过，然而`after()`将会一直运行直到`times(2)`失败，然后它也一同失败。

It feels this feature should be used rarely - figure out a better way of testing your multi-threaded system

Not yet implemented to work with InOrder verification.

这个功能看起来应该极少使用，但在多线程的系统的测试中，这是一个很好的方式

目前尚未实现按照顺序去验证

```

   //passes when someMethod() is called within given time span
   //当`someMethod()`被以时间段的形式调用时通过
    
   verify(mock, timeout(100)).someMethod();
   //above is an alias to:
   // 上面的是一个别名
   
   verify(mock, timeout(100).times(1)).someMethod();

   //passes as soon as someMethod() has been called 2 times before the given timeout
   // 在超时之前，`someMethod()`通过了2次调用
   verify(mock, timeout(100).times(2)).someMethod();

   //equivalent: this also passes as soon as someMethod() has been called 2 times before the given timeout
   //这个和上面的写法是等价的，也是在超时之前，`someMethod()`通过了2次调用
   verify(mock, timeout(100).atLeast(2)).someMethod();

   //verifies someMethod() within given time span using given verification mode
   //在一个超时时间段内，用自定义的验证模式去验证`someMethod()`方法
   //useful only if you have your own custom verification modes.
   //只有在你有自己定制的验证模式时才有用
   
   verify(mock, new Timeout(100, yourOwnVerificationMode)).someMethod();
```

具体例子可以参考`Javadoc`中的`Mockito`类

**Parameters:**

* millis - - time span in milliseconds

* millis - - 时间长度（单位：毫秒）

**Returns:**

* verification mode

* 验证模式 


##  time(int wantedNumberOfInvocations)函数

`public static VerificationMode times(int wantedNumberOfInvocations)`

Allows verifying exact number of invocations. E.g:
允许验证调用方法的精确次数，例如：

```
verify(mock, times(2)).someMethod("some arg");
//连续调用该方法两次
```

See examples in javadoc for Mockito class

具体例子可以参考`Javadoc`中的`Mockito`类

**Parameters:**

* wantedNumberOfInvocations - wanted number of invocations

* wantedNumberOfInvocations - 希望调用的次数

**Returns:**

* verification mode

* 验证模式 

## validateMockitoUsage()函数

`public static void validateMockitoUsage()`

First of all, in case of any trouble, I encourage you to read the Mockito FAQ: http://code.google.com/p/mockito/wiki/FAQ
In case of questions you may also post to mockito mailing list: http://groups.google.com/group/mockito

首页，无论遇到任何问题，我都鼓励你去阅读`the Mockito`问答集：[http://groups.google.com/group/mockito](http://groups.google.com/group/mockito),你也可以在`mockito `邮件列表提问[http://groups.google.com/group/mockito](http://groups.google.com/group/mockito).

validateMockitoUsage() explicitly validates the framework state to detect invalid use of Mockito. However, this feature is optional because Mockito validates the usage all the time... but there is a gotcha so read on.

`validateMockitoUsage()`会明确地检验`framework`的状态以用来检查`Mockito`是否有效使用。但是，这个功能是可选的，因为**'Mockito`会使这个用法一直有效**，不过有一个问题请继续读下去。

Examples of incorrect use:
错误示例:

```
 //Oops, thenReturn() part is missing:
 //当心，`thenReturn()`部分是缺失的
 
 when(mock.get());

 //Oops, verified method call is inside verify() where it should be on the outside:
 //当心，下面验证方法的调用在`verify()`里面，其实应该在外面
 
 verify(mock.execute());

 //Oops, missing method to verify:
 //当心，验证缺失方法
 
 verify(mock);
```
 
Mockito throws exceptions if you misuse it so that you know if your tests are written correctly. The gotcha is that Mockito does the validation next time you use the framework (e.g. next time you verify, stub, call mock etc.). But even though the exception might be thrown in the next test, the exception message contains a navigable stack trace element with location of the defect. Hence you can click and find the place where Mockito was misused.
 
如果你错误的使用了`Mockito`，这样将会抛出异常，这样你就会知道你的测试是否写的正确。你要清楚当你使用这个框架时，`Mockito`会接下来的所有时刻开始验证（例如：下一次你验证、打测试桩、调用`mock`等）。尽管在下一次测试中可能会抛出异常，但这个异常消息包含了一个完整栈踪迹路径以及这个错误的位置。此时你可以点击并找到这个`Mockito`使用错误的地方。

Sometimes though, you might want to validate the framework usage explicitly. For example, one of the users wanted to put validateMockitoUsage() in his @After method so that he knows immediately when he misused Mockito. Without it, he would have known about it not sooner than next time he used the framework. One more benefit of having validateMockitoUsage() in @After is that jUnit runner and rule will always fail in the test method with defect whereas ordinary 'next-time' validation might fail the next test method. But even though JUnit might report next test as red, don't worry about it and just click at navigable stack trace element in the exception message to instantly locate the place where you misused mockito.

有时，你可能想知道这个框架的使用方法。比如，一个用户想将`validateMockitoUsage()`放在它的`@after`方法中，为了能快速地知道它使用`Mockito`时哪里错了。如果没有这个，它使用这个框架时将不能那么迅速地知道哪里使用错了。另外在`@after`中使用`validateMockitoUsage()`比较好的一点是`jUnit runner`以及`Junit rule`中的测试方法在有错误时也会失败，然而普通的`next-time`验证可能会在下一次测试方法中才失败。但是尽管`Junit`可能对下一次测试报告显示红色，但不要担心，这个异常消息包含了一个完整栈踪迹路径以及这个错误的位置。此时你可以点击并找到这个`Mockito`使用错误的地方。

Both built-in runner: MockitoJUnitRunner and rule: MockitoRule do validateMockitoUsage() after each test method.

同样在runner中：`MockitoJUnitRunner and rule`：`MockitoRule`在每次测试方法之后运行`validateMockitoUsage()`

Bear in mind that usually you don't have to validateMockitoUsage() and framework validation triggered on next-time basis should be just enough, mainly because of enhanced exception message with clickable location of defect. However, I would recommend validateMockitoUsage() if you already have sufficient test infrastructure (like your own runner or base class for all tests) because adding a special action to @After has zero cost.

一定要牢记通常你不需要'validateMockitoUsage()'和框架验证，因为基于`next-time`触发的应该已经足够，主要是因为可以点击出错位置查看强大的错误异常消息。但是，如果你已经有足够的测试基础（比如你为所有的测试写有自己的`runner`或者基类），我将会推荐你使用`validateMockitoUsage()`，因为对`@After`添加一个特别的功能时将是零成本。

See examples in javadoc for Mockito class   
具体例子可以参考`Javadoc`中的`Mockito`类 



## verify(T mock)函数

`public static <T> T verify(T mock)`

Verifies certain behavior happened once.
Alias to verify(mock, times(1)) E.g:

验证发生的某些行为
等同于`verify(mock, times(1))` 例如：

```java
   verify(mock).someMethod("some arg");
```

Above is equivalent to:
上面的写法等同于：

```
   verify(mock, times(1)).someMethod("some arg");
```
Arguments passed are compared using equals() method. Read about ArgumentCaptor or ArgumentMatcher to find out other ways of matching / asserting arguments passed.

参数比较是通过`equals()`方法。可参考`ArgumentCaptor`或者`ArgumentMatcher`中关于匹配以及断言参数的方法。


Although it is possible to verify a stubbed invocation, usually it's just redundant. Let's say you've stubbed foo.bar(). If your code cares what foo.bar() returns then something else breaks(often before even verify() gets executed). If your code doesn't care what get(0) returns then it should not be stubbed. Not convinced? See here.

尽管我们可以去验证对测试桩的调用，但通常它都是多余的。比如说你对`foo.bar()`打测试桩。如果你比较关心的是当某些情况`foo.bar()`中断了（经常在`verify()`方法执行之前）,此时会返回什么。如果你的代码不关心是`get(0)`会返回什么那么它就不应该被添加测试桩。如果你还不确定？看[这里](http://monkeyisland.pl/2008/04/26/asking-and-telling)


See examples in javadoc for Mockito class
具体例子可以参考`Javadoc`中的`Mockito`类

**Parameters:**

* mock - to be verified

* mock - 要被验证的
**Returns:**

* mock object itself

* mock本身


##  verifyNoMoreInteractions(Object... mocks)函数
 
`public static void verifyNoMoreInteractions(Object... mocks)`
 
Checks if any of given mocks has any unverified interaction.
You can use this method after you verified your mocks - to make sure that nothing else was invoked on your mocks.

检查入参的`mocks`是否有任何未经验证的交互,你可以在验证你的`mocks`之后使用这个方法，用以确保你的`mocks`没有其它地方会被调用.

Stubbed invocations (if called) are also treated as interactions.
测试柱的调用也被看成是交互。

A word of warning: Some users who did a lot of classic, expect-run-verify mocking tend to use verifyNoMoreInteractions() very often, even in every test method. verifyNoMoreInteractions() is not recommended to use in every test method. verifyNoMoreInteractions() is a handy assertion from the interaction testing toolkit. Use it only when it's relevant. Abusing it leads to overspecified, less maintainable tests. You can find further reading here.

警告：一些使用者，倾向于经常使用`verifyNoMoreInteractions()`方法做大量经典的、期望-运行-验证的模拟，甚至是在所有的测试方法中使用。`verifyNoMoreInteractions()`并不被推荐于使用在所有的测试方法中。在交互测试工具中，`verifyNoMoreInteractions()`是一个很方便的断言。你只能在当它是明确的、相关的时候使用它。滥用它将导致多余的指定、不可维护的测试。你可以在[这里](http://monkeyisland.pl/2008/07/12/should-i-worry-about-the-unexpected/)查找更多的文章。

This method will also detect unverified invocations that occurred before the test method, for example: in setUp(), @Before method or in constructor. Consider writing nice code that makes interactions only in test methods.

这个方法会在测试方法运行之前检查未经验证的调用，例如：在`setUp()`,`@Before`方法或者构造函数中。考虑到要写出良好优秀的代码，交互只能够在测试方法中。


Example:
示例：

```java
 //interactions
 //交互
 mock.doSomething();
 mock.doSomethingUnexpected();

 //verification
 //验证
 verify(mock).doSomething();

 //following will fail because 'doSomethingUnexpected()' is unexpected
 //因为'doSomethingUnexpected()'是未被期望的，所以下面将会失败
 verifyNoMoreInteractions(mock);
 ```
 
See examples in javadoc for Mockito class
具体例子可以参考`Javadoc`中的`Mockito`类

**Parameters:**

* mocks - to be verified

* mocks - 被验证的


##  verifyZeroInteractions(Object... mocks)函数
`public static void verifyZeroInteractions(Object... mocks)`
 
Verifies that no interactions happened on given mocks.
传进来的mocks之间没有任何交互。

```
   verifyZeroInteractions(mockOne, mockTwo);
```

This method will also detect invocations that occurred before the test method, for example: in setUp(), @Before method or in constructor. Consider writing nice code that makes interactions only in test methods.

这个方法会在测试方法运行之前检查调用，例如：在`setUp()`,`@Before`方法或者构造函数中。考虑到要写出良好的代码，交互只能够在测试方法中。

See also never() - it is more explicit and communicates the intent well.
See examples in javadoc for Mockito class

你也可以参考never()方法 - 这个方法很明确的表达了当前方法的用途.
具体例子可以参考`Javadoc`中的`Mockito`类

**Parameters:**

* mocks - to be verified

* mocks - 被验证的


## when(T methodCall)函数

`public static <T> OngoingStubbing<T> when(T methodCall)`

Enables stubbing methods. Use it when you want the mock to return particular value when particular method is called.
Simply put: "When the x method is called then return y".

使测试桩方法生效。当你想让这个`mock`能调用特定的方法返回特定的值，那么你就可以使用它。
简而言之：当你调用`x`方法时会返回`y`。

when() is a successor of deprecated stub(Object)
`when()`是继承自已经废弃的方法`stub(Object)`

Examples:
例如：

```java
 when(mock.someMethod()).thenReturn(10);

 //you can use flexible argument matchers, e.g:
 //你可以使用灵活的参数匹配，例如 
 when(mock.someMethod(anyString())).thenReturn(10);

 //setting exception to be thrown:
 //设置抛出的异常
 when(mock.someMethod("some arg")).thenThrow(new RuntimeException());

 //you can set different behavior for consecutive method calls.
 //你可以对不同作用的连续回调的方法打测试桩：
 //Last stubbing (e.g: thenReturn("foo")) determines the behavior of further consecutive calls.
 //最后面的测试桩（例如：返回一个对象："foo"）决定了接下来的回调方法以及它的行为。
 
 when(mock.someMethod("some arg"))
  .thenThrow(new RuntimeException())
  .thenReturn("foo");

 //Alternative, shorter version for consecutive stubbing:
 //可以用以下方式替代，比较小版本的连贯测试桩：
 when(mock.someMethod("some arg"))
  .thenReturn("one", "two");
 //is the same as:
 //和下面的方式效果是一样的
 when(mock.someMethod("some arg"))
  .thenReturn("one")
  .thenReturn("two");

 //shorter version for consecutive method calls throwing exceptions:
 //比较小版本的连贯测试桩并且抛出异常：
 when(mock.someMethod("some arg"))
  .thenThrow(new RuntimeException(), new NullPointerException();
```
 
For stubbing void methods with throwables see: doThrow(Throwable)
Stubbing can be overridden: for example common stubbing can go to fixture setup but the test methods can override it. Please note that overridding stubbing is a potential code smell that points out too much stubbing.

当你打空方法的测试桩，相关异常可参见：`doThrow(Throwable)`,`Stubbing`可以被重写：比如：普通的测试桩可以使用固定的设置，但是测试方法能够重写它。切记重写测试桩是一种非常不推荐的写法，因为这样做会导致非常多的测试桩。

Once stubbed, the method will always return stubbed value regardless of how many times it is called.

一旦这个方法打过桩，无论这个方法被调用多少次，这个方法会一直返回这个测试桩的值。

Last stubbing is more important - when you stubbed the same method with the same arguments many times.

当你对相同的方法调用相同的参数打测试桩很多次，最后面的测试桩则非常重要

Although it is possible to verify a stubbed invocation, usually it's just redundant. Let's say you've stubbed foo.bar(). If your code cares what foo.bar() returns then something else breaks(often before even verify() gets executed). If your code doesn't care what get(0) returns then it should not be stubbed. Not convinced? See here.

尽管我们可以去验证对测试桩的调用，但通常它都是多余的。比如说你对`foo.bar()`打测试桩。如果你比较关心的是当某些情况`foo.bar()`中断了（经常在`verify()`方法执行之前）,此时会返回什么。如果你的代码不关心是`get(0)`会返回什么那么它就不应该被添加测试桩。如果你还不确定？看[这里](http://monkeyisland.pl/2008/04/26/asking-and-telling)

See examples in javadoc for Mockito class
具体例子可以参考`Javadoc`中的`Mockito`类


**Parameters:**

* methodCall - method call

* methodCall - 调用的方法

**Returns:**

* OngoingStubbing object used to stub fluently. Do not create a reference to this returned object.

* 通常是`OngoingStubbing`对象。不要为被返回的对象创建一个引用。


##  withSettings()函数

`public static MockSettings withSettings()`

Allows mock creation with additional mock settings.
Don't use it too often. Consider writing simple tests that use simple mocks. Repeat after me: simple tests push simple, KISSy, readable & maintainable code. If you cannot write a test in a simple way - refactor the code under test.

可以在创建`mock`时添加设置。
不要经常去设置它。应该在使用简单的`mocks`时写简单的设置。跟着我重复：简单的测试会使整体的代码更简单，更可读、更可维护。如果你不能把测试写的很简单-那么请在测试时重构你的代码。

Examples of mock settings:

`mock`设置的例子


```java
   //Creates mock with different default answer & name
   //用不同的默认结果和名字去创建`mock`
   
   Foo mock = mock(Foo.class, withSettings()
       .defaultAnswer(RETURNS_SMART_NULLS)
       .name("cool mockie"));

   //Creates mock with different default answer, descriptive name and extra interfaces
   ////用不同的默认结果和描述的名称以及额外的接口去创建`mock`
   Foo mock = mock(Foo.class, withSettings()
       .defaultAnswer(RETURNS_SMART_NULLS)
       .name("cool mockie")
       .extraInterfaces(Bar.class));
```
  
MockSettings has been introduced for two reasons. Firstly, to make it easy to add another mock settings when the demand comes. Secondly, to enable combining different mock settings without introducing zillions of overloaded mock() methods.
See javadoc for MockSettings to learn about possible mock settings.

有两种原因推荐使用`MockSettings`.第一种，有需求要增加另外一种`mock`设置，这样用起来更方便。第二种，能够结合不同的`moke`设置以减少大量重载`moke()`方法。

具体可参考`MockSettings`文档来学习`mock settins`

**Returns:**

* mock settings instance with defaults.

* `mock settings`默认实例
