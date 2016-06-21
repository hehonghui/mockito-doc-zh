##description函数

`public static VerificationMod description(String description)`

添加验证失败时要输出的文字内容

```java

verify(mock, description("This will print on failure")).someMethod("some arg");

```
 
**Parameters:**

输出的文字内容

**Returns:**

验证模式

**Since:**

* 2.0.0

---

##doAnswer函数

`public static Stubber doAnswer(Answer answer)`

当你想要测试一个无返回值的函数时，可以使用一个含有泛型类[Answer](http://site.mockito.org/mockito/docs/current/org/mockito/stubbing/Answer.html)参数的doAnswer()函数。为无返回值的函数做测试桩与`when(Objecy)`方法不同，因为编译器不喜欢在大括号内使用void函数。

```java

doAnswer(new Answer() {
      public Object answer(InvocationOnMock invocation) {
          Object[] args = invocation.getArguments();
          Mock mock = invocation.getMock();
          return null;
      }})
  .when(mock).someMethod();

```

参照[Mockito](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html)类的javadoc帮助文档中的例子

**Parameters:**

测试函数的应答内容


**Returns:**

测试方法的测试桩

---

##doCallRealMethod函数

`public static Stubber doCallRealMethod()`

如果你想调用某一个方法的真实实现请使用`doCallRealMethod()`。

像往常一样你需要阅读**局部的mock对象警告**:面向对象编程通过将复杂的事物分解成单独的、具体的、SRPY对象来减少对复杂事件的处理。
局部模拟是如何符合这种范式的呢。？局部模拟通常情况下是指在对象相同的情况下那些复杂的事物被移动另一个不同的方法中。在大多数情况下，并没有按照你所希望的方式来设计你的应用。

然而，使用局部mock对象也会有个别情况:有些代码你并不能非常容易的改变(3rd接口,临时遗留代码的重构等)，但是我对于新的、测试驱动及良好设计的代码不会使用局部mock对象。

同样在javadoc中`spy(Object)`阅读更多关于partial mocks的说明.**推荐使用`Mockito.spy()`来创建局部mock对象**原因是由于你负责构建对象并传值到`spy()`中，它只管保证被调用。

```java

Foo mock = mock(Foo.class);
   doCallRealMethod().when(mock).someVoidMethod();

   // this will call the real implementation of Foo.someVoidMethod()
   // 调用Foo.someVoidMethod()的真实现
   mock.someVoidMethod();

```

参照[Mockito](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html)类的javadoc帮助文档中的例子

**Returns:**

测试方法的测试桩

**Since:**

* 1.9.5

---

##doNothing函数

`public static Stubber doNothing()`

使用`doNothing()`函数是为了设置void函数什么也不做。**需要注意的是默认情况下返回值为void的函数在mocks中是什么也不做的**但是，也会有一些特殊情况。

1.测试桩连续调用一个void函数

```java

doNothing().
   doThrow(new RuntimeException())
   .when(mock).someVoidMethod();

   //does nothing the first time:
   //第一次才能都没做
   mock.someVoidMethod();

   //throws RuntimeException the next time:
   //一下次抛出RuntimeException
   mock.someVoidMethod();

```

2.当你监控真实的对象并且你想让void函数什么也不做：

```java

List list = new LinkedList();
   List spy = spy(list);

   //let's make clear() do nothing
   doNothing().when(spy).clear();

   spy.add("one");

   //clear() does nothing, so the list still contains "one"
   spy.clear();

```

参照[Mockito](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html)类的javadoc帮助文档中的例子

**Returns:**

stubber - 测试方法的测试桩

---



##doReturn函数

`public static Stubber doReturn(Object toBeReturned)`

在某些特殊情况下如果你无法使用`when(Object)`可以使用`doReturn()`函数

**注意：对于测试桩推荐使用`when(Object)`函数，因为它是类型安全的并且可读性更强**(特别是在测试桩连续调用的情况下)

都有哪些特殊情况下需要使用`doReturn()`

1.当监控真实的对象并且调用真实的函数带来的影响时：

```java
List list = new LinkedList();
   List spy = spy(list);

   //Impossible: real method is called so spy.get(0) throws IndexOutOfBoundsException (the list is yet empty)
   
   // 不能完成的：真实方法被调用所以spy.get(0)抛出IndexOutOfBoundsException(list仍是空的)
   when(spy.get(0)).thenReturn("foo");

   //You have to use doReturn() for stubbing:
   //你应用使用doReturn()函数
   doReturn("foo").when(spy).get(0);

```

2. 重写一个前exception-stubbing：

```java

when(mock.foo()).thenThrow(new RuntimeException());

   //Impossible: the exception-stubbed foo() method is called so RuntimeException is thrown.
    // 不能完成的：exception-stubbed foo()被调用抛出RuntimeException异常
   when(mock.foo()).thenReturn("bar");

   //You have to use doReturn() for stubbing:
   //你应用使用doReturn()函数
   doReturn("bar").when(mock).foo();

```

上面的情况展示了Mockito's的优雅语法。注意这些情况并不常见。监控应该是分散的并且重写exception-stubbing也不常见。更何况对于指出测试桩并复写测试桩是一种潜在的代码嗅觉

参照[Mockito](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html)类的javadoc帮助文档中的例子

**Parameters:**

toBeReturned - 当测试桩函数被调用时要被返回的对象

**Returns:**

stubber - 测试方法的测试桩

---

##doThrow函数

`public static Stubber doThrow(Class<? extends Throwable> toBeThrown)`

当你想测试void函数中指定类的抛出异常时使用`doThrow()`

当每一个函数被调用时一个新的异常实例将会被创建

为无返回值的函数做测试桩与`when(Objecy)`方法不同，因为编译器不喜欢在大括号内使用void函数。

```java
doThrow(RuntimeException.class).when(mock).someVoidMethod();
```

**Parameters:**

测试方法被调用时返回的对象

**Returns:**

测试方法的测试桩

**Since:**

* 1.9.0

---

##doThrow函数

`public static Stubber doThrow(Throwable toBeThrown)`

当测试一个void函数的异常时使用`doThrow()`

测试void函数需要与使用`when(Object)`不同的方式，因为编译器不喜欢大括号内有void函数

Example:

```java
doThrow(RuntimeException.class).when(mock).someVoidMethod();
```

**Parameters:**

测试方法被调用时返回的对象

**Returns:**

测试方法的测试桩

**Since:**

* 1.9.0

---

##ignoreStubs函数

`public static Object[] ignoreStubs(Object... mocks)`
 
 忽略对验证函数的测试，当与`verifyNoMoreInteractions()`成对出现或是验证inOrder()时是很有用的。避免了在测试时的多余验证，实际上我们对验证测试一点也不感兴趣。

**警告**,`ignoreStubs()`可能会导致`verifyNoMoreInteractions(ignoreStubs(...))`的过度使用。考虑到Mockito并不推荐使用`verifyNoMoreInteractions()`函数轰炸每一个测试，这其中的原由在文档`verifyNoMoreInteractions(Object…)`部分已经说明：换句话说在mocks中所有* **stubbed** * 的函数都被标记为  * **verified** * 所以不需要使用这种方式。

该方法改变了input mocks！该方法只是为了方便返回 imput mocks 。

忽略测试也会被忽略掉验证inOrder,包括`InOrder.verifyNoMoreInteractions()`，看下面第二个示例：


Example:

```java
//mocking lists for the sake of the example (if you mock List in real you will burn in hell)
  List mock1 = mock(List.class), mock2 = mock(List.class);

  //stubbing mocks:
  when(mock1.get(0)).thenReturn(10);
  when(mock2.get(0)).thenReturn(20);

  //using mocks by calling stubbed get(0) methods:
  
  // 调用stubbed get(0)使用mocks
  System.out.println(mock1.get(0)); //prints 10
  System.out.println(mock2.get(0)); //prints 20

  //using mocks by calling clear() methods:
  // 调用clear()使用mocks
  mock1.clear();
  mock2.clear();

  //verification:
  
  // 验证
  verify(mock1).clear();
  verify(mock2).clear();

  //verifyNoMoreInteractions() fails because get() methods were not accounted for.
  
  // verifyNoMoreInteractions()会失败，因为get()未关联账号
  
  try { verifyNoMoreInteractions(mock1, mock2); } catch (NoInteractionsWanted e);

  //However, if we ignore stubbed methods then we can verifyNoMoreInteractions()
  //如要我们忽略测试函数我可以这样verifyNoMoreInteractions()
  
  verifyNoMoreInteractions(ignoreStubs(mock1, mock2));

  //Remember that ignoreStubs() *changes* the input mocks and returns them for convenience.

```

忽略测试可以用于**verification in order**:

```java
List list = mock(List.class);
  when(mock.get(0)).thenReturn("foo");

  list.add(0);
  System.out.println(list.get(0)); //we don't want to verify this
  list.clear();

  InOrder inOrder = inOrder(ignoreStubs(list));
  inOrder.verify(list).add(0);
  inOrder.verify(list).clear();
  inOrder.verifyNoMoreInteractions();

```

**Parameters:**

将被改变的input mocks

**Returns:**
 
和传入参数一样的mocks

**Since:**

1.9.0


---


##inOrder函数

`public static InOrder inOrder(Object... mocks)`

创建[InOrder](http://site.mockito.org/mockito/docs/current/org/mockito/InOrder.html)对象验证 mocks in order

```java
InOrder inOrder = inOrder(firstMock, secondMock);

inOrder.verify(firstMock).add("was called first");
inOrder.verify(secondMock).add("was called second");

```

验证in order是很灵活的。你可以只验证你感兴趣的，**并不需要一个一个验证所有的交互**。同样你也可以创建InOrder对象只在相关in-order的验证中进行传值。

`InOrder` 验证是'greedy'.你很难每一个都注意到。你可以在Mockito [wiki pages](https://code.google.com/p/mockito/w/list)页搜索'greedy'获取更多信息。

Mockito 1.8.4版本你能以order-sensitive方式调用`verifyNoMoreInvocations()`，阅读更多[InOrder.verifyNoMoreInteractions()](http://site.mockito.org/mockito/docs/current/org/mockito/InOrder.html#verifyNoMoreInteractions())

参照[Mockito](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html)类的javadoc帮助文档中的例子

**Parameters:**

in order中被修改的mocks

**Returns:**

in order中被用于验证的InOrder对象

---

##mock函数

`public static <T> T mock(Class <T> classToMock)`

对给定的类或接口创建mock对象


**Parameters:**

需要mock的类或接口

**Returns:**

mock对象

---

##mock函数

`public static <T> T mock(Class <T> classToMock, Answer defaultAnswer)`


根据它对交互的回应指明策略创建mock对象。这个是比较高级特性并且你不需要它写多少测试代码。但是对于legacy系统这是非常有用的。

这个是默认answer，所以当**你不想测试**函数时你可以使用它。

```java

Foo mock = mock(Foo.class, RETURNS_SMART_NULLS);
Foo mockTwo = mock(Foo.class, new YourOwnAnswer());

```

参照[Mockito](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html)类的javadoc帮助文档中的例子

**Parameters:**

* 需要mock的类或接口

* 未测试函数的默认answer

**Returns:**

mock对象

---

##mock函数

`public static <T> T mock(Class <T> classToMock, MockSettings mockSettings)`
 
 没有标准的设置来创建mock对象


配置点的数目对mock的扩大有影响，所以我们在没有越来越多重载Mockito.mock()的情况下需要一种更流利的方式来介绍一种新的配置方式。即[MockSettings](http://site.mockito.org/mockito/docs/current/org/mockito/MockSettings.html).

```java

Listener mock = mock(Listener.class, withSettings()
     .name("firstListner").defaultBehavior(RETURNS_SMART_NULLS));
   );

```        

使用它时需要小心一些并且不要常用。在什么情况下你的测试会不需要标准配置的mocks?在测试代码下太复杂以至于不需要标准配置的mocks?你有没有重构测试代码来让它更加容易测试？

也可以参考[withSettings()](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#withSettings())

参照[Mockito](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html)类的javadoc帮助文档中的例子

**Parameters:**

* 需要mock的类或接口
* mock配置

**Returns:**

* mock对象

---

##mock

@Deprecated

@已过期

`public static <T> T mock(Class <T> classToMock, ReturnValues returnValues)`

已过期，*请使用`mock(Foo.class, defaultAnswer)`*;

已过期，**请使用`mock(Foo.class, defaultAnswer)`**;

见[mock(Class, Answer)](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#mock(java.lang.Class,%20org.mockito.stubbing.Answer))

为什么会过期？为了框架更好的一致性与交互性用Answer替换了ReturnValues。Answer接口很早就存在框架中了并且它有和ReturnValues一样的责任。没有必要维护两个一样的接口。

针对它的返回值需要指明策略来创建mock对象。这个是比较高级特性并且你不需要它写多少测试代码。但是对于legacy系统这是非常有用的。

明显地，当你不需要测试方法时可以使用这个返回值。

```java
Foo mock = mock(Foo.class, Mockito.RETURNS_SMART_NULLS);
Foo mockTwo = mock(Foo.class, new YourOwnReturnValues());

```

参照[Mockito](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html)类的javadoc帮助文档中的例子

**Parameters:**

* 需要mock的类或接口
* 未测试方法默认返回值

**Returns:**

* mock对象

##mock

`public static <T> T mock(Class <T> classToMock, String name)`

指明mock的名字。命名mock在debug的时候非常有用。名字会在所有验证错误中使用。需要注意的是对于使用太多mock或者collaborators的复杂代码命名mock并不能解决问题。**如果你使用了太多的mock**，为了更加容易测试/调试 你需要对其进行重构而不是对mock命名。

**如果你使用了@Mock注解，意味着你的mock已经有名字了！**

@Mock使用字段名称作为mock名字[Read more](http://site.mockito.org/mockito/docs/current/org/mockito/Mock.html)

参照[Mockito](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html)类的javadoc帮助文档中的例子

**Parameters:**

* 需要mock的类或接口
* mock的名字

**Returns:**

* mock对象


## mockingDetails函数

`public static MockingDetails mockingDetails(Object toInspect)`

对于Mockito的关联信息返回MockingDetails实例可以用于检查某一特定的对象，无论给定的对象是mock还是监视的都可以被找出来。

在Mockito以后的版本中MockingDetails可能会扩大并且提供其它有用的有关mock的信息。e.g. invocations, stubbing info, etc.

**Parameters:**

* 要检查的对象。允许为空

**Returns:**

[MockingDetails](http://site.mockito.org/mockito/docs/current/org/mockito/MockingDetails.html)实例

**Since:**

* 1.9.5