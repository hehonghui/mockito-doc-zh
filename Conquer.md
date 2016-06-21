![](http://img.blog.csdn.net/20150731162529393)

# n-w开头的函数

##  never()函数

`public static VerificationMode never()`   

相当于`times(0)`,可参见 `times(int)`    
验证交互没有发生. 例如:    

```java
verify(mock, never()).someMethod();
```

如果你想验证`mock`之间没有交互，可以使用`verifyZeroInteractions(Object...)` 或者 `verifyNoMoreInteractions(Object...)` 这两个方法

具体例子可以参考`Javadoc`中的`Mockito`类

**Returns:**

* 验证模式

##  only()函数

 `public static VerificationMode only()`  

 
 如果当前`mock`的方法只被调用一次，则允许被检验。例如：

```java

   verify(mock, only()).someMethod();
   //上面这行代码是下面这两行代码的简写
   
   verify(mock).someMethod();
   verifyNoMoreInvocations(mock);

```

可以参考`verifyNoMoreInteractions(Object...)`方法

具体例子可以参考`Javadoc`中的`Mockito`类

**Returns:**

* verification mode 


##  reset(T... mocks)函数

`public static <T> void reset(T... mocks)`

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

* T - mocks的类型

**Parameters:**

* 被重置的mocks


##  spy(Class<T> classToSpy)函数

`@Incubating 
public static <T> T spy(Class<T> classToSpy)`

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

* T - spy的类型

**Parameters:**

* spy的类

**Returns:**

* a spy of the provided class

**Since:**

* 1.10.12


##  stub(T methodCall)函数

`public static <T> DeprecatedOngoingStubbing<T> stub(T methodCall)`

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
 
 一些用户有点混乱、混淆，是因为相比于'stub()'，'when(Object)'更加被推荐

```
   //Instead of:
   //替代为：
   stub(mock.count()).toReturn(10);

	//你可以这样做：
   //You can do:
   when(mock.count()).thenReturn(10);
```

当对一个返回值为空且抛出异常的方法打测试桩：`doThrow(Throwable)`测试桩会被重写：例如通常测试桩会设置为常用固定设置，但测试方法可以重写它。切记重写测试桩是一种非常不推荐的写法，因为这样做会导致非常多的测试桩。

一旦这个方法打过桩，无论这个方法被调用多少次，这个方法会一直返回这个测试桩的值。

当你对相同的方法调用相同的参数打测试桩很多次，最后面的测试桩则非常重要

尽管我们可以去验证对测试桩的调用，但通常它都是多余的。比如说你对`foo.bar()`打测试桩。如果你比较关心的是当某些情况`foo.bar()`中断了（经常在`verify()`方法执行之前）,此时会返回什么。如果你的代码不关心是`get(0)`会返回什么那么它就不应该被添加测试桩。如果你还不确定？看[这里](http://monkeyisland.pl/2008/04/26/asking-and-telling)


**Parameters:**

* methodCall - 调用的方法

**Returns:**

* DeprecatedOngoingStubbing 对象是用来设置测试桩的值或者异常的


##  stubVoid(T mock)函数

 `public static <T> VoidMethodStubbable<T> stubVoid(T mock)` 

已废弃.使用`doThrow(Throwable)`方法代替去打空测试桩

```
	//Instead of:
	//替代为：
   stubVoid(mock).toThrow(e).on().someVoidMethod();

   //Please do:
   //请这样做：
   doThrow(e).when(mock).someVoidMethod();
 
```

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

具体例子可以参考`Javadoc`中的`Mockito`类

**Parameters:**

* mock - to stub

**Returns:**

* stubbable object that allows stubbing with throwable


##  timesout(long millis)函数

`public static VerificationWithTimeout timeout(long millis)`

允许验证时使用`timeout`。它会在指定的时间后触发你所期望的动作，而不是立即失败，也许这个对并发条件下的测试非常有用。它和`after()`是有所有不同的，因为`after()`会等候一个完整的时期，除非最终的测试结果很快就出来了(例如：当`never()`失败了), 然而当验证通过时，`timeout()`会快速地停止，当你使用`times(2)`时会产生不同的行为。例如，当先通过然后失败，在这种情况下，`timeout`将会当`time(2)`通过时迅速通过，然而`after()`将会一直运行直到`times(2)`失败，然后它也一同失败。

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

* millis - - 时间长度（单位：毫秒）

**Returns:**

* 验证模式 


##  time(int wantedNumberOfInvocations)函数

`public static VerificationMode times(int wantedNumberOfInvocations)`

允许验证调用方法的精确次数，例如：

```
verify(mock, times(2)).someMethod("some arg");
//连续调用该方法两次
```

具体例子可以参考`Javadoc`中的`Mockito`类

**Parameters:**

* wantedNumberOfInvocations - 希望调用的次数

**Returns:**

* 验证模式 

## validateMockitoUsage()函数

`public static void validateMockitoUsage()`

首页，无论遇到任何问题，我都鼓励你去阅读`the Mockito`问答集：[http://groups.google.com/group/mockito](http://groups.google.com/group/mockito),你也可以在`mockito `邮件列表提问[http://groups.google.com/group/mockito](http://groups.google.com/group/mockito).

`validateMockitoUsage()`会明确地检验`framework`的状态以用来检查`Mockito`是否有效使用。但是，这个功能是可选的，因为**'Mockito`会使这个用法一直有效**，不过有一个问题请继续读下去。

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
 
如果你错误的使用了`Mockito`，这样将会抛出异常，这样你就会知道你的测试是否写的正确。你要清楚当你使用这个框架时，`Mockito`会接下来的所有时刻开始验证（例如：下一次你验证、打测试桩、调用`mock`等）。尽管在下一次测试中可能会抛出异常，但这个异常消息包含了一个完整栈踪迹路径以及这个错误的位置。此时你可以点击并找到这个`Mockito`使用错误的地方。

有时，你可能想知道这个框架的使用方法。比如，一个用户想将`validateMockitoUsage()`放在它的`@after`方法中，为了能快速地知道它使用`Mockito`时哪里错了。如果没有这个，它使用这个框架时将不能那么迅速地知道哪里使用错了。另外在`@after`中使用`validateMockitoUsage()`比较好的一点是`jUnit runner`以及`Junit rule`中的测试方法在有错误时也会失败，然而普通的`next-time`验证可能会在下一次测试方法中才失败。但是尽管`Junit`可能对下一次测试报告显示红色，但不要担心，这个异常消息包含了一个完整栈踪迹路径以及这个错误的位置。此时你可以点击并找到这个`Mockito`使用错误的地方。

同样在runner中：`MockitoJUnitRunner and rule`：`MockitoRule`在每次测试方法之后运行`validateMockitoUsage()`


一定要牢记通常你不需要'validateMockitoUsage()'和框架验证，因为基于`next-time`触发的应该已经足够，主要是因为可以点击出错位置查看强大的错误异常消息。但是，如果你已经有足够的测试基础（比如你为所有的测试写有自己的`runner`或者基类），我将会推荐你使用`validateMockitoUsage()`，因为对`@After`添加一个特别的功能时将是零成本。

 
具体例子可以参考`Javadoc`中的`Mockito`类 



## verify(T mock)函数

`public static <T> T verify(T mock)`

验证发生的某些行为
等同于`verify(mock, times(1))` 例如：

```java
   verify(mock).someMethod("some arg");
```

上面的写法等同于：

```
   verify(mock, times(1)).someMethod("some arg");
```

参数比较是通过`equals()`方法。可参考`ArgumentCaptor`或者`ArgumentMatcher`中关于匹配以及断言参数的方法。

尽管我们可以去验证对测试桩的调用，但通常它都是多余的。比如说你对`foo.bar()`打测试桩。如果你比较关心的是当某些情况`foo.bar()`中断了（经常在`verify()`方法执行之前）,此时会返回什么。如果你的代码不关心是`get(0)`会返回什么那么它就不应该被添加测试桩。如果你还不确定？看[这里](http://monkeyisland.pl/2008/04/26/asking-and-telling)

具体例子可以参考`Javadoc`中的`Mockito`类

**Parameters:**

* mock - 要被验证的
**Returns:**

* mock本身


##  verifyNoMoreInteractions(Object... mocks)函数
 
`public static void verifyNoMoreInteractions(Object... mocks)`

检查入参的`mocks`是否有任何未经验证的交互,你可以在验证你的`mocks`之后使用这个方法，用以确保你的`mocks`没有其它地方会被调用.

测试柱的调用也被看成是交互。


**警告：**一些使用者，倾向于经常使用`verifyNoMoreInteractions()`方法做大量经典的、期望-运行-验证的模拟，甚至是在所有的测试方法中使用。`verifyNoMoreInteractions()`并不被推荐于使用在所有的测试方法中。在交互测试工具中，`verifyNoMoreInteractions()`是一个很方便的断言。你只能在当它是明确的、相关的时候使用它。滥用它将导致多余的指定、不可维护的测试。你可以在[这里](http://monkeyisland.pl/2008/07/12/should-i-worry-about-the-unexpected/)查找更多的文章。

这个方法会在测试方法运行之前检查未经验证的调用，例如：在`setUp()`,`@Before`方法或者构造函数中。考虑到要写出良好优秀的代码，交互只能够在测试方法中。

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

具体例子可以参考`Javadoc`中的`Mockito`类

**Parameters:**

* mocks - 被验证的


##  verifyZeroInteractions(Object... mocks)函数
`public static void verifyZeroInteractions(Object... mocks)`
 
传进来的mocks之间没有任何交互。

```
   verifyZeroInteractions(mockOne, mockTwo);
```

这个方法会在测试方法运行之前检查调用，例如：在`setUp()`,`@Before`方法或者构造函数中。考虑到要写出良好的代码，交互只能够在测试方法中。

你也可以参考never()方法 - 这个方法很明确的表达了当前方法的用途.
具体例子可以参考`Javadoc`中的`Mockito`类

**Parameters:**

* mocks - 被验证的


## when(T methodCall)函数

`public static <T> OngoingStubbing<T> when(T methodCall)`

使测试桩方法生效。当你想让这个`mock`能调用特定的方法返回特定的值，那么你就可以使用它。
简而言之：当你调用`x`方法时会返回`y`。

`when()`是继承自已经废弃的方法`stub(Object)`

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

当你打空方法的测试桩，相关异常可参见：`doThrow(Throwable)`,`Stubbing`可以被重写：比如：普通的测试桩可以使用固定的设置，但是测试方法能够重写它。切记重写测试桩是一种非常不推荐的写法，因为这样做会导致非常多的测试桩。

一旦这个方法打过桩，无论这个方法被调用多少次，这个方法会一直返回这个测试桩的值。

当你对相同的方法调用相同的参数打测试桩很多次，最后面的测试桩则非常重要

尽管我们可以去验证对测试桩的调用，但通常它都是多余的。比如说你对`foo.bar()`打测试桩。如果你比较关心的是当某些情况`foo.bar()`中断了（经常在`verify()`方法执行之前）,此时会返回什么。如果你的代码不关心是`get(0)`会返回什么那么它就不应该被添加测试桩。如果你还不确定？看[这里](http://monkeyisland.pl/2008/04/26/asking-and-telling)

具体例子可以参考`Javadoc`中的`Mockito`类


**Parameters:**

* methodCall - 调用的方法

**Returns:**

* 通常是`OngoingStubbing`对象。不要为被返回的对象创建一个引用。


##  withSettings()函数

`public static MockSettings withSettings()`

可以在创建`mock`时添加设置。
不要经常去设置它。应该在使用简单的`mocks`时写简单的设置。跟着我重复：简单的测试会使整体的代码更简单，更可读、更可维护。如果你不能把测试写的很简单-那么请在测试时重构你的代码。

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

有两种原因推荐使用`MockSettings`.第一种，有需求要增加另外一种`mock`设置，这样用起来更方便。第二种，能够结合不同的`moke`设置以减少大量重载`moke()`方法。

具体可参考`MockSettings`文档来学习`mock settins`

**Returns:**

* `mock settings`默认实例
