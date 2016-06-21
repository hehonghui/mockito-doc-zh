# Mockito 中文文档 ( 2.0.26 beta )

![](http://img.blog.csdn.net/20150731162529393)

Mockito库能够Mock对象、验证结果以及打桩(stubbing)。

该文档您也可以通过[http://mockito.org](http://mockito.org)获取到。所有文档都保存在javadocs中，因为它能够保证文档与源代码的一致性。这样也能够让离线的用户从IDE直接访问到文档。这样一来也能够激励Mockito开发者在每次写代码、每次提交时更新对应的文档。

## 目录

0. [迁移到Mockito 2.0](#0)
1. [验证某些行为](#1)
2. [如何做一些测试桩 (Stub)](#2)
3. [参数匹配器 (matchers)](#3)
4. [验证函数的确切、最少、从未调用次数](#4)
5. [为返回值为void的函数通过Stub抛出异常](#5)
6. [按照顺序验证执行结果](#6)
7. [确保交互(interaction)操作不会执行在mock对象上](#7)
8. [查找冗余的调用](#8)
9. [简化mock对象的创建](#9)
10. [为连续的调用做测试桩 (stub) ](#10)
11. [为回调做测试桩](#11)
12. [doReturn()、doThrow()、doAnswer()、doNothing()、doCallRealMethod()系列方法的运用](#12)
13. [监控真实对象](#13)
14. [修改没有测试桩的调用的默认返回值 ( 1.7版本之后 ) ](#14)
15. [为下一步的断言捕获参数 (1.8版本之后)](#15)
16. [真实的局部mocks (1.8版本之后)](#16)
17. [重置mocks对象 (1.8版本之后)](#17)
18. [故障排查与验证框架的使用 (1.8版本之后)](#18)
19. [行为驱动开发的别名 (1.8版本之后)](#19)
20. [序列化mock对象](#20)
21. [新的注解 : @Captor,@Spy,@ InjectMocks (1.8.3版本之后) ](#21)
22. [验证超时 (1.8.5版本之后) ](#22)
23. [自动初始化被@Spies, @InjectMocks注解的字段以及构造函数注入 (1.9.0版本之后)](#23)
24. [单行测试桩 (1.9.0版本之后) ](#24)
25. [验证被忽略的测试桩 (1.9.0版本之后)](#25)
26. [mock详情 (1.9.5版本之后)](#26)
27. [delegate调用真实的实例 (1.9.5版本之后)](#27)
28. [MockMaker API (1.9.5版本之后)](#28)
29. [BDD风格的验证 (1.10.0版本之后)](#29)
30. [追踪或者Mock抽象类 (1.10.12版本之后)](#30)
31. [Mockito mock对象通过ClassLoader能被序列化/反序列化 (1.10.0版本之后)](#31)
32. [deep stubs更好的支持泛型 (1.10.0版本之后)](#32)
33. [Mockito JUnit 规则 (1.10.17版本之后)](#33)
34. [开/关插件 (1.10.15版本之后)](#34)
35. [自定义验证失败消息 (2.0.0版本之后)](#35)

<b id="0"></b>
### 0. 迁移到Mockito 2.0


为了持续提升Mockito以及更进一步的提升单元测试体验,我们希望你升级到Mockito 2.0.Mockito遵循语意化的版本控制，除非有非常大的改变才会变化主版本号。在一个库的生命周期中,为了引入一系列有用的特性，修改已存在的行为或者API等重大变更是在所难免的。因此，我们希望你能够爱上 Mockito 2.0!

重要变更 : 

* Mockito从Hamcrest中解耦，自定义的matchers API也发生了改变,查看[ArgumentMatcher](http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html) 的基本原理以及迁移指南。

跟着我们的示例来mock 一个List,因为大家都知道它的接口（例如add(),get(), clear()）。不要mock一个真实的List类型,使用一个真实的实例来替代。

<b id="1"></b>
### 1. 验证某些行为

```java
 // 静态导入会使代码更简洁
 import static org.mockito.Mockito.*;

 // mock creation 创建mock对象
 List mockedList = mock(List.class);

 //using mock object 使用mock对象
 mockedList.add("one");
 mockedList.clear();

 //verification 验证
 verify(mockedList).add("one");
 verify(mockedList).clear();
```
一旦mock对象被创建了，mock对象会记住所有的交互。然后你就可能选择性的验证你感兴趣的交互。


<b id="2"></b>
### 2. [如何做一些测试桩 (Stub)]()

```java
 //You can mock concrete classes, not only interfaces
 // 你可以mock具体的类型,不仅只是接口
 LinkedList mockedList = mock(LinkedList.class);

 //stubbing
 // 测试桩
 when(mockedList.get(0)).thenReturn("first");
 when(mockedList.get(1)).thenThrow(new RuntimeException());

 //following prints "first"
 // 输出“first”
 System.out.println(mockedList.get(0));

 //following throws runtime exception
 // 抛出异常
 System.out.println(mockedList.get(1));

 //following prints "null" because get(999) was not stubbed
 // 因为get(999) 没有打桩，因此输出null
 System.out.println(mockedList.get(999));

 //Although it is possible to verify a stubbed invocation, usually it's just redundant
 //If your code cares what get(0) returns then something else breaks (often before even verify() gets executed).
 //If your code doesn't care what get(0) returns then it should not be stubbed. Not convinced? See here.
 // 验证get(0)被调用的次数
 verify(mockedList).get(0);
```

* 默认情况下，所有的函数都有返回值。mock函数默认返回的是null，一个空的集合或者一个被对象类型包装的内置类型，例如0、false对应的对象类型为Integer、Boolean；
* 测试桩函数可以被覆写 : 例如常见的测试桩函数可以用于初始化夹具，但是测试函数能够覆写它。请注意，覆写测试桩函数是一种可能存在潜在问题的做法；
* 一旦测试桩函数被调用，该函数将会一致返回固定的值；
* 上一次调用测试桩函数有时候极为重要-当你调用一个函数很多次时，最后一次调用可能是你所感兴趣的。


<b id="3"></b>
### 3. [参数匹配器 (matchers)]()

Mockito以自然的java风格来验证参数值: 使用equals()函数。有时，当需要额外的灵活性时你可能需要使用参数匹配器，也就是argument matchers :

```java
 //stubbing using built-in anyInt() argument matcher
 // 使用内置的anyInt()参数匹配器
 when(mockedList.get(anyInt())).thenReturn("element");

 //stubbing using custom matcher (let's say isValid() returns your own matcher implementation):
 // 使用自定义的参数匹配器( 在isValid()函数中返回你自己的匹配器实现 )
 when(mockedList.contains(argThat(isValid()))).thenReturn("element");

 //following prints "element"
 // 输出element
 System.out.println(mockedList.get(999));

 //you can also verify using an argument matcher
 // 你也可以验证参数匹配器
 verify(mockedList).get(anyInt());
```

参数匹配器使验证和测试桩变得更灵活。[点击这里](http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html)查看更多内置的匹配器以及自定义参数匹配器或者hamcrest 匹配器的示例。


如果仅仅是获取自定义参数匹配器的信息，查看[ArgumentMatcher类文档](http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html)即可。

为了合理的使用复杂的参数匹配，使用equals()与anyX() 的匹配器会使得测试代码更简洁、简单。有时，会迫使你重构代码以使用equals()匹配或者实现equals()函数来帮助你进行测试。

同时建议你阅读[第15章节](#sec_15)或者[ArgumentCaptor类文档](http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentCaptor.html)。ArgumentCaptor是一个能够捕获参数值的特俗参数匹配器。


参数匹配器的注意点 : 

如果你使用参数匹配器,所有参数都必须由匹配器提供。

示例 : ( 该示例展示了如何多次应用于测试桩函数的验证 ) 

```java
verify(mock).someMethod(anyInt(), anyString(), eq("third argument"));
//above is correct - eq() is also an argument matcher
// 上述代码是正确的,因为eq()也是一个参数匹配器

verify(mock).someMethod(anyInt(), anyString(), "third argument");
//above is incorrect - exception will be thrown because third argument 
// 上述代码是错误的,因为所有参数必须由匹配器提供，而参数"third argument"并非由参数匹配器提供，因此的缘故会抛出异常
```

像anyObject(), eq()这样的匹配器函数不会返回匹配器。它们会在内部将匹配器记录到一个栈当中，并且返回一个假的值，通常为null。`这样的实现是由于被Java编译器强加的静态类型安全`。结果就是你不能在验证或者测试桩函数之外使用anyObject(), eq()函数。

<b id="4"></b>
### 4. [验证函数的确切、最少、从未调用次数]()

```java
 //using mock
 mockedList.add("once");

 mockedList.add("twice");
 mockedList.add("twice");

 mockedList.add("three times");
 mockedList.add("three times");
 mockedList.add("three times");

 //following two verifications work exactly the same - times(1) is used by default
 // 下面的两个验证函数效果一样,因为verify默认验证的就是times(1)
 verify(mockedList).add("once");
 verify(mockedList, times(1)).add("once");

 //exact number of invocations verification
 // 验证具体的执行次数
 verify(mockedList, times(2)).add("twice");
 verify(mockedList, times(3)).add("three times");

 //verification using never(). never() is an alias to times(0)
 // 使用never()进行验证,never相当于times(0)
 verify(mockedList, never()).add("never happened");

 //verification using atLeast()/atMost()
 // 使用atLeast()/atMost()
 verify(mockedList, atLeastOnce()).add("three times");
 verify(mockedList, atLeast(2)).add("five times");
 verify(mockedList, atMost(5)).add("three times");

```

verify函数默认验证的是执行了times(1)，也就是某个测试函数是否执行了1次.因此，times(1)通常被省略了。

<b id="5"></b>
### 5. [为返回值为void的函数通过Stub抛出异常]()

```java
doThrow(new RuntimeException()).when(mockedList).clear();

//following throws RuntimeException:
// 调用这句代码会抛出异常
mockedList.clear();
```

关于doThrow|doAnswer 等函数族的信息请阅读第十二章节。

最初，[stubVoid(Object)](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#stubVoid(T)) 函数用于为无返回值的函数打桩。现在stubVoid()函数已经过时,doThrow(Throwable)成为了它的继承者。这是为了提升与 doAnswer(Answer) 函数族的可读性与一致性。


<b id="6"></b>
### 6. [验证执行执行顺序]()

```java
 // A. Single mock whose methods must be invoked in a particular order
 // A. 验证mock一个对象的函数执行顺序
 List singleMock = mock(List.class);

 //using a single mock
 singleMock.add("was added first");
 singleMock.add("was added second");

 //create an inOrder verifier for a single mock
 // 为该mock对象创建一个inOrder对象
 InOrder inOrder = inOrder(singleMock);

 //following will make sure that add is first called with "was added first, then with "was added second"
 // 确保add函数首先执行的是add("was added first"),然后才是add("was added second")
 inOrder.verify(singleMock).add("was added first");
 inOrder.verify(singleMock).add("was added second");

 // B. Multiple mocks that must be used in a particular order
 // B .验证多个mock对象的函数执行顺序
 List firstMock = mock(List.class);
 List secondMock = mock(List.class);

 //using mocks
 firstMock.add("was called first");
 secondMock.add("was called second");

 //create inOrder object passing any mocks that need to be verified in order
 // 为这两个Mock对象创建inOrder对象
 InOrder inOrder = inOrder(firstMock, secondMock);

 //following will make sure that firstMock was called before secondMock
 // 验证它们的执行顺序
 inOrder.verify(firstMock).add("was called first");
 inOrder.verify(secondMock).add("was called second");

 // Oh, and A + B can be mixed together at will
```

验证执行顺序是非常灵活的-你不需要一个一个的验证所有交互,只需要验证你感兴趣的对象即可。
另外，你可以仅通过那些需要验证顺序的mock对象来创建InOrder对象。


<b id="7"></b>
### 7. [确保交互(interaction)操作不会执行在mock对象上]()

```java
 //using mocks - only mockOne is interacted
 // 使用Mock对象
 mockOne.add("one");

 //ordinary verification
 // 普通验证
 verify(mockOne).add("one");

 //verify that method was never called on a mock
 // 验证某个交互是否从未被执行
 verify(mockOne, never()).add("two");

 //verify that other mocks were not interacted
 // 验证mock对象没有交互过
 verifyZeroInteractions(mockTwo, mockThree);
```

<b id="8"></b>
### 8. [查找冗余的调用]()

```java
//using mocks
mockedList.add("one");
mockedList.add("two");

verify(mockedList).add("one");

//following verification will fail
// 下面的验证将会失败
verifyNoMoreInteractions(mockedList);
```

一些用户可能会在频繁地使用`verifyNoMoreInteractions()`，甚至在每个测试函数中都用。但是`verifyNoMoreInteractions()`并不建议在每个测试函数中都使用。`verifyNoMoreInteractions()`在交互测试套件中只是一个便利的验证，它的作用是当你需要验证是否存在冗余调用时。滥用它将导致测试代码的可维护性降低。你可以阅读[这篇文档](http://monkeyisland.pl/2008/07/12/should-i-worry-about-the-unexpected/)来了解更多相关信息。

`never()`是一种更为明显且易于理解的形式。


<b id="9"></b>
### 9. [简化mock对象的创建]()

* 最小化重复的创建代码
* 使测试类的代码可读性更高
* 使验证错误更易于阅读，因为字段名可用于标识mock对象

```java
public class ArticleManagerTest {

   @Mock private ArticleCalculator calculator;
   @Mock private ArticleDatabase database;
   @Mock private UserProvider userProvider;

   private ArticleManager manager;
```

注意！下面这句代码需要在运行测试函数之前被调用,一般放到测试类的基类或者test runner中:

```java
 MockitoAnnotations.initMocks(testClass);
```

你可以使用内置的runner: [MockitoJUnitRunner] [runner] 或者一个rule : [MockitoRule][rule]。
关于mock注解的更多信息可以阅读[MockitoAnnotations文档](http://site.mockito.org/mockito/docs/current/org/mockito/MockitoAnnotations.html)。

[runner]: http://site.mockito.org/mockito/docs/current/org/mockito/runners/MockitoJUnitRunner.html
[rule]: http://site.mockito.org/mockito/docs/current/org/mockito/junit/MockitoRule.html


<b id="10"></b>
### 10. [为连续的调用做测试桩 (stub) ]()

有时我们需要为同一个函数调用的不同的返回值或异常做测试桩。典型的运用就是使用mock迭代器。
原始版本的Mockito并没有这个特性，例如，可以使用Iterable或者简单的集合来替换迭代器。这些方法提供了更自然的方式，在一些场景中为连续的调用做测试桩会很有用。示例如下 ： 

```java
 when(mock.someMethod("some arg"))
   .thenThrow(new RuntimeException())
   .thenReturn("foo");

 //First call: throws runtime exception:
 // 第一次调用 : 抛出运行时异常
 mock.someMethod("some arg");

 //Second call: prints "foo"
 // 第二次调用 : 输出"foo"
 System.out.println(mock.someMethod("some arg"));

 //Any consecutive call: prints "foo" as well (last stubbing wins).
 // 后续调用 : 也是输出"foo"
 System.out.println(mock.someMethod("some arg"));
```
 
另外，连续调用的另一种更简短的版本 : 

```java
// 第一次调用时返回"one",第二次返回"two",第三次返回"three"
 when(mock.someMethod("some arg"))
   .thenReturn("one", "two", "three");
```

<b id="11"></b>
### 11. [为回调做测试桩]()

Allows stubbing with generic Answer interface.
运行为泛型接口Answer打桩。

在最初的Mockito里也没有这个具有争议性的特性。我们建议使用thenReturn() 或thenThrow()来打桩。这两种方法足够用于测试或者测试驱动开发。

```java
 when(mock.someMethod(anyString())).thenAnswer(new Answer() {
     Object answer(InvocationOnMock invocation) {
         Object[] args = invocation.getArguments();
         Object mock = invocation.getMock();
         return "called with arguments: " + args;
     }
 });

 //Following prints "called with arguments: foo"
 // 输出 : "called with arguments: foo"
 System.out.println(mock.someMethod("foo"));
```

<b id="12"></b>
### 12. [doReturn()、doThrow()、doAnswer()、doNothing()、doCallRealMethod()系列方法的运用]()

通过`when(Object)`为无返回值的函数打桩有不同的方法,因为编译器不喜欢void函数在括号内...

使用`doThrow(Throwable)` 替换`stubVoid(Object)`来为void函数打桩是为了与`doAnswer()`等函数族保持一致性。

当你想为void函数打桩时使用含有一个exception 参数的`doAnswer()` : 

```java
doThrow(new RuntimeException()).when(mockedList).clear();

//following throws RuntimeException:
// 下面的代码会抛出异常
mockedList.clear();
```

当你调用`doThrow()`, `doAnswer()`, `doNothing()`, `doReturn()` and `doCallRealMethod()` 这些函数时可以在适当的位置调用`when()`函数. 当你需要下面这些功能时这是必须的: 

* 测试void函数
* 在受监控的对象上测试函数
* 不知一次的测试为同一个函数，在测试过程中改变mock对象的行为。

但是在调用`when()`函数时你可以选择是否调用这些上述这些函数。

阅读更多关于这些方法的信息:

* [doReturn(Object)](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#doReturn(java.lang.Object)) 
* [doThrow(Throwable)](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#doThrow(java.lang.Throwable))
* [doThrow(Class)](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#doThrow(java.lang.Class))
* [doAnswer(Answer)](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#doAnswer(org.mockito.stubbing.Answer))
* [doNothing()](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#doNothing())
* [doCallRealMethod()](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#doCallRealMethod())


<b id="13"></b>
### 13. [监控真实对象]()

你可以为真实对象创建一个监控(spy)对象。当你使用这个spy对象时真实的对象也会也调用，除非它的函数被stub了。尽量少使用spy对象，使用时也需要小心形式，例如spy对象可以用来处理遗留代码。

监控一个真实的对象可以与“局部mock对象”概念结合起来。在1.8之前，mockito的监控功能并不是真正的局部mock对象。原因是我们认为局部mock对象的实现方式并不好，在某些时候我发现一些使用局部mock对象的合法用例。（第三方接口、临时重构遗留代码，完整的文章在[这里](http://monkeyisland.pl/2009/01/13/subclass-and-override-vs-partial-mocking-vs-refactoring/) ）

```java
List list = new LinkedList();
List spy = spy(list);

//optionally, you can stub out some methods:
// 你可以为某些函数打桩
when(spy.size()).thenReturn(100);

//using the spy calls *real* methods
// 通过spy对象调用真实对象的函数
spy.add("one");
spy.add("two");

//prints "one" - the first element of a list
// 输出第一个元素
System.out.println(spy.get(0));

//size() method was stubbed - 100 is printed
// 因为size()函数被打桩了,因此这里返回的是100
System.out.println(spy.size());

//optionally, you can verify
// 交互验证
verify(spy).add("one");
verify(spy).add("two");
```

理解监控真实对象非常重要！

有时，在监控对象上使用`when(Object)`来进行打桩是不可能或者不切实际的。因此，当使用监控对象时请考虑`doReturn|Answer|Throw()`函数族来进行打桩。例如 : 

```java
List list = new LinkedList();
List spy = spy(list);

//Impossible: real method is called so spy.get(0) throws IndexOutOfBoundsException (the list is yet empty)
// 不可能 : 因为当调用spy.get(0)时会调用真实对象的get(0)函数,此时会发生IndexOutOfBoundsException异常，因为真实List对象是空的
   when(spy.get(0)).thenReturn("foo");

//You have to use doReturn() for stubbing
// 你需要使用doReturn()来打桩
doReturn("foo").when(spy).get(0);
```

Mockito并不会为真实对象代理函数调用，实际上它会拷贝真实对象。因此如果你保留了真实对象并且与之交互，不要期望从监控对象得到正确的结果。当你在监控对象上调用一个没有被stub的函数时并不会调用真实对象的对应函数，你不会在真实对象上看到任何效果。

因此结论就是 : 当你在监控一个真实对象时，你想在stub这个真实对象的函数，那么就是在自找麻烦。或者你根本不应该验证这些函数。

<b id="14"></b>
### 14. [修改没有测试桩的调用的默认返回值 ( 1.7版本之后 ) ]()

你可以指定策略来创建mock对象的返回值。这是一个高级特性，通常来说，你不需要写这样的测试。然后，它对于遗留系统来说是很有用处的。当你不需要为函数调用打桩时你可以指定一个默认的answer。

```java
Foo mock = mock(Foo.class, Mockito.RETURNS_SMART_NULLS);
Foo mockTwo = mock(Foo.class, new YourOwnAnswer());
```

关于RETURNS_SMART_NULLS更多的信息请查看 : 
[RETURNS_SMART_NULLS文档](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#RETURNS_SMART_NULLS) 。

<b id="15"></b>
### 15. 为下一步的断言捕获参数 (1.8版本之后)

Mockito以java代码风格的形式来验证参数值 : 即通过使用`equals()`函数。这也是我们推荐用于参数匹配的方式，因为这样会使得测试代码更简单、简洁。在某些情况下，当验证交互之后要检测真实的参数值时这将变得有用。例如 ： 

```java
ArgumentCaptor<Person> argument = ArgumentCaptor.forClass(Person.class);
// 参数捕获
verify(mock).doSomething(argument.capture());
// 使用equal断言
assertEquals("John", argument.getValue().getName());
```

警告 : 我们建议使用没有测试桩的ArgumentCaptor来验证，因为使用含有测试桩的ArgumentCaptor会降低测试代码的可读性，因为captor是在断言代码块之外创建的。另一个好处是它可以降低本地化的缺点，因为如果测试桩函数没有被调用，那么参数就不会被捕获。总之，ArgumentCaptor与自定义的参数匹配器相关(可以查看[ArgumentMatcher类的文档](ArgumentMatcher) )。这两种技术都能用于检测外部传递到Mock对象的参数。然而，使用ArgumentCaptor在以下的情况下更合适 : 

* 自定义不能被重用的参数匹配器
* 你仅需要断言参数值

自定义参数匹配器相关的资料你可以参考[ArgumentMatcher](ArgumentMatcher)文档。

[ArgumentMatcher]: http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html