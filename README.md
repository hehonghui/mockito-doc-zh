# Mockito 中文文档 ( 2.0.26 beta )

![](http://img.blog.csdn.net/20150731162529393)

> 由于缺乏校对，难免有谬误之处，如果发现任何语句不通顺、翻译错误，都可以在[github中的项目](https://github.com/hehonghui/mockito-doc-zh)提出issue。谢谢~ 

Mockito框架官方地址[mockito](http://mockito.org/)，[文档地址](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html)。

Mockito库能够Mock对象、验证结果以及打桩(stubbing)。

该文档您也可以通过[http://mockito.org](http://mockito.org)获取到。所有文档都保存在javadocs中，因为它能够保证文档与源代码的一致性。这样也能够让离线的用户从IDE直接访问到文档。这样一来也能够激励Mockito开发者在每次写代码、每次提交时更新对应的文档。

## 参与人员

|  成员 |  任务  |
|-----------|------|
| [Mr.Simple](https://github.com/hehonghui/) | 1-15 |
| [chaosss](https://github.com/chaosss/) | 16-26 |
|[tiiime](https://github.com/tiiime/) | 27~35 |
|[dengshiwei](https://github.com/dengshiwei/) | a~c开头的方法,包含 |
|[objectlife](https://github.com/objectlife/) | d~m开头的方法 |
|[Conquer](https://github.com/Conquer/) | n-w开头的函数 |


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

16. 真实的局部mocks (1.8版本之后)

在内部通过邮件进行了无数争辩和讨论后，最终 Mockito 决定支持部分测试，早前我们不支持是因为我们认为部分测试会让代码变得糟糕。然而，我们发现了部分测试真正合理的用法。[详情点这](http://monkeyisland.pl/2009/01/13/subclass-and-override-vs-partial-mocking-vs-refactoring/)

在 Mockito 1.8 之前，spy() 方法并不会产生真正的部分测试，而这无疑会让一些开发者困惑。更详细的内容可以看：[这里](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#13) 或 [Java 文档](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#spy(T))

```java
    //you can create partial mock with spy() method:
    List list = spy(new LinkedList());

    //you can enable partial mock capabilities selectively on mocks:
    Foo mock = mock(Foo.class);
    //Be sure the real implementation is 'safe'.
    //If real implementation throws exceptions or depends on specific state of the object then you're in trouble.
    when(mock.someMethod()).thenCallRealMethod();
```

一如既往，你会去读部分测试的警告部分：面向对象编程通过将抽象的复杂度拆分为一个个独立，精确的 SRPy 对象中，降低了抽象处理的复杂度。那部分测试是怎么遵循这个规范的呢？事实上部分测试并没有遵循这个规范……部分测试通常意味着抽象的复杂度被移动到同一个对象的不同方法中，在大多数情况下，这不会是你想要的应用架构方式。

然而，在一些罕见的情况下部分测试才会是易用的：处理不能轻易修改的代码（第三方接口，临时重构的遗留代码等等）。然而，为了新的，测试驱动和架构优秀的代码，我是不会使用部分测试的。

17. 重置mocks对象 (1.8版本之后)

聪明的 Mockito 使用者很少会用到这个特性，因为他们知道这是出现糟糕测试单元的信号。通常情况下你不会需要重设你的测试单元，只需要为每一个测试方法重新创建一个测试单元就可以了。

如果你真的想通过 reset() 方法满足某些需求的话，请考虑实现简单，小而且专注于测试方法而不是冗长，精确的测试。首先可能出现的代码异味就是测试方法中间那的 reset() 方法。这可能意味着你已经过度测试了。请遵循测试方法的呢喃：请让我们小，而且专注于单一的行为上。在 Mockito 邮件列表中就有好几个讨论是和这个有关的。

添加 reset() 方法的唯一原因就是让它能与容器注入的测试单元协作。详情看 [issue 55](http://code.google.com/p/mockito/issues/detail?id=55) 或 [FAQ](http://code.google.com/p/mockito/wiki/FAQ)。

别自己给自己找麻烦，reset() 方法在测试方法的中间确实是代码异味。


```java
   List mock = mock(List.class);
   when(mock.size()).thenReturn(10);
   mock.add(1);

   reset(mock);
   //at this point the mock forgot any interactions & stubbing
```

18. 故障排查与验证框架的使用 (1.8版本之后)

首先，如果出现了任何问题，我建议你先看 [Mockito FAQ](http://code.google.com/p/mockito/wiki/FAQ)。

任何你提的问题都会被提交到 Mockito 的[邮件列表](http://groups.google.com/group/mockito)中。

然后你应该知道 Mockito 会验证你是否始终以正确的方式使用它，对此有疑惑的话不妨看看 [validateMockitoUsage()](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#validateMockitoUsage()) 的文档说明。

19.行为驱动开发的别名 (1.8版本之后)

行为驱动开发实现测试单元的模式将 //given //when //then comments 视作测试方法的基础，这也是我们实现单元测试时被建议做的！

[你可以在这开始学习有关 BDD 的知识](http://en.wikipedia.org/wiki/Behavior_Driven_Development)

问题是当信息没有很好地与 //given //when //then comments 交互时，扮演规范角色的测试桩 API 就会出现问题。这是因为测试桩属于给定测试单元的组件，而且不是任何测试的组件。因此 [BDDMockito](http://site.mockito.org/mockito/docs/current/org/mockito/BDDMockito.html) 类介绍了一个别名，使你的测试桩方法调用 [BDDMockito.given(Object)](http://site.mockito.org/mockito/docs/current/org/mockito/BDDMockito.html#given(T)) 方法。现在它可以很好地和给定的 BDD 模式的测试单元组件进行交互。

```java
 import static org.mockito.BDDMockito.*;

 Seller seller = mock(Seller.class);
 Shop shop = new Shop(seller);

 public void shouldBuyBread() throws Exception {
   //given
   given(seller.askForBread()).willReturn(new Bread());

   //when
   Goods goods = shop.buyBread();

   //then
   assertThat(goods, containBread());
 }
```

20. 序列化mock对象

模拟对象可以被序列化。有了这个特性你就可以在依赖被序列化的情况下使用模拟对象了。

警告：这个特性很少在单元测试中被使用。


To create serializable mock use [MockSettings.serializable()](http://site.mockito.org/mockito/docs/current/org/mockito/MockSettings.html#serializable()):

这个特性通过 BDD 拥有不可考外部依赖的特性的具体用例实现，来自外部依赖的 Web 环境和对象会被序列化，然后在不同层之间被传递。

```java
   List serializableMock = mock(List.class, withSettings().serializable());
```

The mock can be serialized assuming all the normal [serialization requirements](http://java.sun.com/j2se/1.5.0/docs/api/java/io/Serializable.html) are met by the class.

模拟对象能被序列化假设所有普通的序列化要求都被类满足了。

让一个真实的侦查对象可序列化需要多一些努力，因为 spy(...) 方法没有接收 MockSettings 的重载版本。不过不用担心，你几乎不可能用到这。

```java
 List<Object> list = new ArrayList<Object>();
 List<Object> spy = mock(ArrayList.class, withSettings()
                 .spiedInstance(list)
                 .defaultAnswer(CALLS_REAL_METHODS)
                 .serializable());
```

21. 新的注解 : @Captor,@Spy,@ InjectMocks (1.8.3版本之后)

V1.8.3 带来的新注解在某些场景下可能会很实用

@[Captor](http://site.mockito.org/mockito/docs/current/org/mockito/Captor.html) 简化 [ArgumentCaptor](http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentCaptor.html) 的创建 - 当需要捕获的参数是一个令人讨厌的通用类，而且你想避免编译时警告。

@[Spy](http://site.mockito.org/mockito/docs/current/org/mockito/Spy.html) - 你可以用它代替 [spy(Object) 方法](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#spy(T))

@[InjectMocks](http://site.mockito.org/mockito/docs/current/org/mockito/InjectMocks.html) - 自动将模拟对象或侦查域注入到被测试对象中。需要注意的是 @InjectMocks 也能与 @Spy 一起使用，这就意味着 Mockito 会注入模拟对象到测试的部分测试中。它的复杂度也是你应该使用部分测试原因。

所有新的注解仅仅在 [MockitoAnnotations.initMocks(Object)](http://site.mockito.org/mockito/docs/current/org/mockito/MockitoAnnotations.html#initMocks(java.lang.Object)) 方法中被处理，就像你在 built-in runner 中使用的 @[Mock](http://site.mockito.org/mockito/docs/current/org/mockito/Mock.html) 注解：[MockitoJUnitRunner](http://site.mockito.org/mockito/docs/current/org/mockito/runners/MockitoJUnitRunner.html) 或 规范: [MockitoRule](http://site.mockito.org/mockito/docs/current/org/mockito/junit/MockitoRule.html).

22. 验证超时 (1.8.5版本之后)

允许带有暂停的验证。这使得一个验证去等待一段特定的时间，以获得想要的交互而不是如果还没有发生事件就带来的立即失败。在并发条件下的测试这会很有用。

感觉起来这个特性应该很少被使用 - 指出更好的测试多线程系统的方法。

还没有实现去和 InOrder 验证协作。

例子：

```java
   //passes when someMethod() is called within given time span
   verify(mock, timeout(100)).someMethod();
   //above is an alias to:
   verify(mock, timeout(100).times(1)).someMethod();

   //passes when someMethod() is called *exactly* 2 times within given time span
   verify(mock, timeout(100).times(2)).someMethod();

   //passes when someMethod() is called *at least* 2 times within given time span
   verify(mock, timeout(100).atLeast(2)).someMethod();

   //verifies someMethod() within given time span using given verification mode
   //useful only if you have your own custom verification modes.
   verify(mock, new Timeout(100, yourOwnVerificationMode)).someMethod();
```

23. 自动初始化被@Spies, @InjectMocks注解的字段以及构造函数注入 (1.9.0版本之后)

Mockito 现在会通过注入构造方法、setter 或域注入尽可能初始化带有 @[Spy](http://site.mockito.org/mockito/docs/current/org/mockito/Spy.html) 和 @[InjectMocks](http://site.mockito.org/mockito/docs/current/org/mockito/InjectMocks.html) 注解的域或方法。

为了利用这一点特性，你需要使用 [MockitoAnnotations.initMocks(Object)](http://site.mockito.org/mockito/docs/current/org/mockito/MockitoAnnotations.html#initMocks(java.lang.Object)), [MockitoJUnitRunner](http://site.mockito.org/mockito/docs/current/org/mockito/runners/MockitoJUnitRunner.html) 或 [MockitoRule](http://site.mockito.org/mockito/docs/current/org/mockito/junit/MockitoRule.html)。

为了 InjectMocks 请在 Java 文档中了解更多可用的技巧和注入的规范

```java
 //instead:
 @Spy BeerDrinker drinker = new BeerDrinker();
 //you can write:
 @Spy BeerDrinker drinker;

 //same applies to @InjectMocks annotation:
 @InjectMocks LocalPub;
```

24. 单行测试桩 (1.9.0版本之后)

Mockito 现在允许你在使用测试桩时创建模拟对象。基本上，它允许在一行代码中创建一个测试桩，这对保持代码的整洁很有用。举例来说，有些乏味的测试桩会被创建，并在测试初始化域时被打入，例如：

```java
 public class CarTest {
   Car boringStubbedCar = when(mock(Car.class).shiftGear()).thenThrow(EngineNotStarted.class).getMock();

   @Test public void should... {}
 ```

25. 验证被忽略的测试桩 (1.9.0版本之后)

Mockito 现在允许为了验证无视测试桩。在与 verifyNoMoreInteractions() 方法或验证 inOrder() 方法耦合时，有些时候会很有用。帮助避免繁琐的打入测试桩调用验证 - 显然我们不会对验证测试桩感兴趣。

警告，ignoreStubs() 可能会导致 verifyNoMoreInteractions(ignoreStubs(...)) 的过度使用。谨记在心，Mockito 没有推荐用 [verifyNoMoreInteractions()](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#verifyNoMoreInteractions(java.lang.Object...)) 方法连续地施用于每一个测试中，原因在 Java 文档中有。

一些例子：

```java
 verify(mock).foo();
 verify(mockTwo).bar();

 //ignores all stubbed methods:
 verifyNoMoreInvocations(ignoreStubs(mock, mockTwo));

 //creates InOrder that will ignore stubbed
 InOrder inOrder = inOrder(ignoreStubs(mock, mockTwo));
 inOrder.verify(mock).foo();
 inOrder.verify(mockTwo).bar();
 inOrder.verifyNoMoreInteractions();
```

更好的例子和更多的细节都可以在 Java 文档的 [ignoreStubs(Object...)](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#ignoreStubs(java.lang.Object...)) 部分看到。

26. mock详情 (1.9.5版本之后)

为了区别一个对象是模拟对象还是侦查对象：

```java
     Mockito.mockingDetails(someObject).isMock();
     Mockito.mockingDetails(someObject).isSpy();
```

[MockingDetails.isMock()](http://site.mockito.org/mockito/docs/current/org/mockito/MockingDetails.html#isMock()) 和 [MockingDetails.isSpy()](http://site.mockito.org/mockito/docs/current/org/mockito/MockingDetails.html#isSpy()) 方法都会返回一个布尔值。因为一个侦查对象只是模拟对象的一种变种，所以 isMock() 方法在对象是侦查对象是会返回 true。在之后的 Mockito 版本中 MockingDetails 会变得更健壮，并提供其他与模拟对象相关的有用信息，例如：调用，测试桩信息，等等……

###27. [委托调用真实实例][delegating_call_to_real_instance] (Since 1.9.5)

当**使用常规的 spy API 去 mock 或者 spy 一个对象很困难**时可以用 delegate 来 spy 或者 mock 对象的某一部分。
从 Mockito 的 1.10.11 版本开始， delegate 有可能和 mock 的类型相同也可能不同。如果不是同一类型，
delegate 类型需要提供一个匹配方法否则就会抛出一个异常。下面是关于这个特性的一些用例:

- 带有 interface 的 final 类
- 已经自定义代理的对象
- 带有 finalize 方法的特殊对象，就是避免重复执行。

和常规 spy 的不同:

- 标准的 spy [(spy(Object))][spy] 包含被 spy 实例的所有状态信息，方法在 spy 对象上被调用。被 spy 的对象只在 mock
创建时被用来拷贝状态信息。如果你通过标准 spy 调用一个方法，这个 spy 会调用其内部的其他方法记录这次操作，
以便后面验证使用。等效于存根 (stubbed)操作。

- mock delegates 只是简单的把所有方法委托给 delegate。delegate 一直被当成它代理的方法使用。如果你
从一个 mock 调用它被委托的方法，它会调用其内部方法，这些调用不会被记录，stubbing 在这里也不会生效。
Mock 的 delegates 相对于标准的 spy 来说功能弱了很多，不过在标准 spy 不能被创建的时候很有用。

更多信息可以看这里 [AdditionalAnswers.delegatesTo(Object)][AdditionalAnswers].

[delegating_call_to_real_instance]:http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#delegating_call_to_real_instance
[spy]:http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#spy(T)
[AdditionalAnswers]:http://site.mockito.org/mockito/docs/current/org/mockito/AdditionalAnswers.html#delegatesTo(java.lang.Object)

---

###28. [MockMaker API ][mock_maker_plugin](Since 1.9.5)

为了满足用户的需求和 Android 平台使用。Mockito 现在提供一个扩展点，允许替换代理生成引擎。默认情况下，Mockito 使用 cglib 创建动态代理。

这个扩展点是为想要扩展 Mockito 功能的高级用户准备的。比如，我们现在就可以在 dexmaker 的帮助下使用 Mockito
测试 Android。

更多的细节，原因和示例请看 [MockMaker][MockMaker] 的文档。


[mock_maker_plugin]:http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#mock_maker_plugin
[MockMaker]:http://site.mockito.org/mockito/docs/current/org/mockito/plugins/MockMaker.html

---

###29. [(new) BDD 风格的验证][BDD_behavior_verification] (Since 1.10.0)

开启 Behavior Driven Development (BDD) 风格的验证可以通过 BBD 的关键词 **then** 开始验证。

```java
 given(dog.bark()).willReturn(2);

 // when
 ...

 then(person).should(times(2)).ride(bike);

```

更多信息请查阅 [ BDDMockito.then(Object)][then] .


[BDD_behavior_verification]:http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#BDD_behavior_verification
[then]:http://site.mockito.org/mockito/docs/current/org/mockito/BDDMockito.html#then(T)


 ---


###30. [(new) Spying 或 mocking 抽象类][spying_abstract_classes] (Since 1.10.12)

现在可以方便的 spy 一个抽象类。注意，过度使用 spy 或许意味着代码的设计上有问题。(see [spy(Object)][spy]).

之前，spying 只可以用在实例对象上。而现在新的 API 可以在创建一个 mock 实例时使用构造函数。这对 mock
一个抽象类来说是很重要的，这样使用者就不必再提供一个抽象类的实例了。目前的话只支持无参构造函数，
如果你认为这样还不够的话欢迎向我们反馈。

```java
//convenience API, new overloaded spy() method:
 SomeAbstract spy = spy(SomeAbstract.class);

 //Robust API, via settings builder:
 OtherAbstract spy = mock(OtherAbstract.class, withSettings()
    .useConstructor().defaultAnswer(CALLS_REAL_METHODS));

 //Mocking a non-static inner abstract class:
 InnerAbstract spy = mock(InnerAbstract.class, withSettings()
    .useConstructor().outerInstance(outerInstance).defaultAnswer(CALLS_REAL_METHODS));

```

更多信息请见 [MockSettings.useConstructor()][useConstructor] .

[spying_abstract_classes]:http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#spying_abstract_classes
[useConstructor]:http://site.mockito.org/mockito/docs/current/org/mockito/MockSettings.html#useConstructor()


 ---


###31. [(new) Mockito mocks 可以通过 classloaders 序列化/反序列化][serilization_across_classloader] (Since 1.10.0)

 Mockito 通过 classloader 引入序列化。和其他形式的序列化一样，所有 mock 层的对象都要被序列化，
 包括 answers。因为序列化模式需要大量的工作，所以这是一个可选择设置。

 ```java
 // 常规的 serialization
 mock(Book.class, withSettings().serializable());

 // 通过 classloaders 序列化
 mock(Book.class, withSettings().serializable(ACROSS_CLASSLOADERS));
 ```

更多信息请查看 [MockSettings.serializable(SerializableMode)][serializable].


[serilization_across_classloader]:http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#serilization_across_classloader
[serializable]:http://site.mockito.org/mockito/docs/current/org/mockito/MockSettings.html#serializable(org.mockito.mock.SerializableMode)

 ---

###32. [(new) Deep stubs 更好的泛型支持][better_generic_support_with_deep_stubs] (Since 1.10.0)

 Deep stubbing 现在可以更好的查找类的泛型信息。这就意味着像这样的类
 不必去 mock 它的行为就可以使用。

```java
class Lines extends List<Line> {
     // ...
 }

 lines = mock(Lines.class, RETURNS_DEEP_STUBS);

 // Now Mockito understand this is not an Object but a Line
 Line line = lines.iterator().next();

```

请注意，大多数情况下 mock 返回一个 mock 对象是错误的。

[better_generic_support_with_deep_stubs]:http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#better_generic_support_with_deep_stubs

 ---

###33.  [(new) Mockito JUnit rule][mockito_junit_rule] (Since 1.10.17)


Mockito 现在提供一个 JUnit rule。目前为止，有两种方法可以初始化 fields ，使用 Mockito 提供的注解比如
[@Mock][Mock_], [@Spy][Spy_], [@InjectMocks][InjectMocks_] 等等。

- 用 @RunWith([@MockitoJUnitRunner.class][MockitoJUnitRunner]) 标注 JUnit 测试类
- 在 @Before 之前调用 [MockitoAnnotations.initMocks(Object)][initMocks]

现在你可以选择使用一个 rule:

```java
 @RunWith(YetAnotherRunner.class)
 public class TheTest {
     @Rule public MockitoRule mockito = MockitoJUnit.rule();
     // ...
 }
```

更多信息到这里查看 [MockitoJUnit.rule()][rule].

[mockito_junit_rule]:http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#mockito_junit_rule
[Mock_]:http://site.mockito.org/mockito/docs/current/org/mockito/Mock.html
[Spy_]:http://site.mockito.org/mockito/docs/current/org/mockito/Spy.html
[InjectMocks_]:http://site.mockito.org/mockito/docs/current/org/mockito/InjectMocks.html
[MockitoJUnitRunner]:http://site.mockito.org/mockito/docs/current/org/mockito/runners/MockitoJUnitRunner.html
[initMocks]:http://site.mockito.org/mockito/docs/current/org/mockito/MockitoAnnotations.html#initMocks(java.lang.Object)

[rule]:http://site.mockito.org/mockito/docs/current/org/mockito/junit/MockitoJUnit.html#rule()

 ---

###34. [(new) 开启和关闭 plugins][PluginSwitch] (Since 1.10.15)

这是一个测试特性，可以控制一个 mockito-plugin 开启或者关闭。详情请查看 [PluginSwitch][PluginSwitch]

[plugin_switch]:http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#plugin_switch
[PluginSwitch]:http://site.mockito.org/mockito/docs/current/org/mockito/plugins/PluginSwitch.html

---

###35. 自定义验证失败信息 (Since 2.0.0)

允许声明一个在验证失败时输出的自定义消息
示例:

```java
 // will print a custom message on verification failure
 verify(mock, description("This will print on failure")).someMethod();

 // will work with any verification mode
 verify(mock, times(2).description("someMethod should be called twice")).someMethod();
```

###字段摘要

<table>
<tbody>
<tr>
<td>类型</td>
<td>字段以及描述</td>
</tr>
<tr>
<td>static Answer< Object ></td>
<td>CALLS_REAL_METHODS<br>
用于mock(Class, Answer)的可选参数Answer</td>
</tr>
</tbody>
</table>

###字段详情
<table>
<tbody>
<tr>
<td>**CALLS_REAL_METHODS**</td>
</tr>
<tr>
<td>public static final Answer<Object> CALLS_REAL_METHODS<br><br>
用于mock(Class, Answer)的可选参数Answer<br><br>
Answer可以用于定义unstubbed invocations的返回值.<br><br>

这个Answer接口对于legacy code非常有用. 当使用这个接口的时候, unstubbed methods会被实现. 
这是一种通过调用默认方法来创建partial mock对象的方式。<br><br>

通常，你将要阅读mock的部分警告：Object oriented programming is more less tackling complexity by dividing the complexity into separate, specific, , SRPy objects.
partial mock是如果适应这种模式的呢？好吧！它不仅仅是，partial mock通常意味着复杂性在同一个对象中移动到不同的方法.在大多数情况下，这不是你想要的设计你的应用的方式。<br><br>

然而，当partial mocks派上用场同样也有少许情况:处理你不易改变的代码（第三方接口，legacy code的临时重构）.我将不使用partial mocks用于新的、测试驱动以及设计不错的代码。<br><br>
例如：<br>
<table><tr><td bgcolor=#000000><font color=#ffffff>
	 Foo mock = mock(Foo.class, CALLS_REAL_METHODS);<br><br>
 	// this calls the real implementation of Foo.getSomething()<br>
	 value = mock.getSomething();<br><br>
	 when(mock.getSomething()).thenReturn(fakeValue);<br><br>
 	// now fakeValue is returned<br>
 	value = mock.getSomething();</font></td></tr></table>
</tbody>
</table>

###方法摘要
<table>
	<tbody>
	<tr>
		<td><em>Modifier and Type</em></td>
		<td><em>Method and Description</em></td>
	</tr>
	<tr>
		<td>static VerificationAfterDelay</td>
		<td>after(long millis)<br>
		给定的时间后进行验证</td>
	</tr>
	<tr>
		<td>static VerificationMode</td>
		<td>atLeast(int minNumberOfInvocations)<br>
		至少进行minNumberOfInvocations次验证</td>
	</tr>
	<tr>
		<td>static VerificationMode</td>
		<td>atLeastOnce()<br>
		至少进行一次验证</td>
	</tr>
	<tr>
		<td>static VerificationMode</td>
		<td>atMost(int maxNumberOfInvocations)<br>
		最多进行maxNumberOfInvocations次验证</td>
	</tr>
	<tr>
		<td>static VerificationMode</td>
		<td>calls(int wantedNumberOfInvocations)<br>
		允许顺序进行non-greedy验证</td>
	</tr>
	</tbody>
</table>

#方法详情
##after
```java
	public static VerificationAfterDelay after(long millis)
```

在给定的时间后进行验证。它会为了预期的效果进行等待一段时间后进行验证，而不是因为没发生而立即失败。这可能对于测试多并发条件非常有用。<br><br>

after()等待整个周期的特点不同于timeout()，而timeout()一旦验证通过就尽快停止，例如：当使用times(2)可以产生不同的行为方式，可能通过后过会又失败。这种情况下，timeout只要times(2)通过就会通过，然后after执行完整个周期时间，可能会失败，也意味着times(2)也失败。

感觉这个方法应该少使用——找到更好的方法测试你的多线程系统。

对尚未实现的工作进行验证。

```java

	//passes after 100ms, if someMethod() has only been called once at that time.<br>
	verify(mock, after(100)).someMethod();<br>
	//above is an alias to:<br>
	verify(mock, after(100).times(1)).someMethod();

	//passes if someMethod() is called *exactly* 2 times after the given timespan
	verify(mock, after(100).times(2)).someMethod();

	//passes if someMethod() has not been called after the given timespan<br>
	verify(mock, after(100).never()).someMethod();

	//verifies someMethod() after a given time span using given verification mode
	//useful only if you have your own custom verification modes.
	verify(mock, new After(100, yourOwnVerificationMode)).someMethod();
```

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html">Mockito</a>类的javadoc帮助文档中的例子

**Parameters:**

* millis - - time span in milliseconds

**Returns:**

* verification mode


##atLeast
```java
	public static VerificationMode atLeast(int minNumberOfInvocations)
```

允许至少进行x次验证。例如：

```java
	verify(mock, atLeast(3)).someMethod("some arg");
```

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html">Mockito</a>类的javadoc帮助文档中的例子

**Parameters:**

* minNumberOfInvocations - invocations的最小次数

**Returns:**

* verification mode<br><br>

##atLeastOnce
```java
	public static VerificationMode atLeastOnce()
```

至少进行一次一次验证。例如:

```java
	verify(mock, atLeastOnce()).someMethod("some arg");
```

atLeast(1)的别名.
参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html">Mockito</a>类的javadoc帮助文档中的例子

**Returns:**

* verification mode

##atMost
```java
	public static VerificationMode atMost(int maxNumberOfInvocations)
```

至多进行x次验证. 例如:

```java
	verify(mock, atMost(3)).someMethod("some arg");
```

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html">Mockito</a>类的javadoc帮助文档中的例子

**Parameters::**

* maxNumberOfInvocations - invocations的最大次数

**Returns:**

* verification mode

##calls
```java
	public static VerificationMode calls(int wantedNumberOfInvocations)
```

允许顺序进行non-greedy验证. 例如:<br>
```java
	inOrder.verify( mock, calls( 2 )).someMethod( "some arg" );
```
<ul>
<li>如果这个方法调用3次不会失败，不同于times(2)</li>
<li>不会标记第三次验证，不同于atLeast(2)</li>
</ul>
这个verification mode只能用于顺序验证.

**Parameters::**

* wantedNumberOfInvocations - 验证的次数

**Returns:**

* verification mode<br><br>

#继承org.mockito.Matchers的方法
##any
```java
	public static <T> T any()<br><br>
```

匹配任何值，包括null

anyObject()的别名

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

这是: anyObject() and any(java.lang.Class)的别名

**Returns:**

* null
	
<!--第二行-->
##any
```java
	public static <T> T any(Class<T> clazz)
```

匹配任何对象，包括null

这个方法不进行给定参数的类型检查，它就是为了避免代码中的强制转换(Casting)。然而这可能会改变（类型检查可以添加）将来的主要版本。

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

这是: anyObject() and any(java.lang.Class)的别名

**Returns:**

* null
<!--第三行-->
##anyBoolean
```java
	public static boolean anyBoolean()
```

任何boolean类型或非空(non-null)的Boolean.

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Returns:**

* false

<!--第四行-->
##anyByte
```java
	public static byte anyByte()
```

任何byte类型变量或非空(non-null)Byte.

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Returns:**

* 0

<!--第五行-->
##anyChar
```java
	public static char anyChar()
```

任何char类型变量或非空(non-null)的Character.

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Returns:**

* 0

<!--第六行-->
##anyCollection
```java
public static Collection anyCollection()
```

任何非空(non-null)的Collection.

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Returns:**

* 空Collection.

<!--第七行-->
##anyCollectionOf
```java
public static < T > Collection < T > anyCollectionOf(Class<T> clazz)
```

通用友好的别名anyCollection()。为了保持代码清洁，通过@SuppressWarnings("unchecked")来进行替代编译器警告。

任何非空(non-null)Collection.

这个方法不进行给定参数的类型检查，它就是为了避免代码中的强制转换(Casting)。然而这可能会改变（类型检查可以添加）将来的主要版本。

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Parameters**

* clazz - 类型属于Collection类型避免类型转换(Casting)

**Returns:**

* 空Collection.<br><br>

<!--第八行-->
##anyDouble
```java
	public static double anyDouble()
```

任何double类型或非空(non-null)的Double.

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Returns:**

* 0.

<!--第九行-->
##anyFloat
```java
	public static float anyFloat()
```

任何float类型或非空(non-null)Float.

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Returns:**

* 0.

<!--第十行-->
##anyInt
```java
	public static int anyInt()
```

任何int或非空(non-null)Integer.

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Returns:**

* 0.

<!--第十一行-->
##anyList
```java
public static List anyList()
```

任何非空(non-null)List.

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Returns:**

* 空List.

<!--第十二行-->
##anyListOf
```java
public static < T >  List < T > anyListOf(Class< T > clazz)
```

通用友好的别名anyList()。为了保持代码清洁，通过@SuppressWarnings("unchecked")来进行替代编译器警告。

任何非空(non-null)List.

这个方法不进行给定参数的类型检查，它就是为了避免代码中的强制转换(Casting)。然而这可能会改变（类型检查可以添加）将来的主要版本。

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Parameters:**

* clazz - 类型属于List类型避免类型转换(Casting)

**Returns:**

* 空List.

<!--第十三行-->
##anyLong
```java
	public static long anyLong()
```

任何long类型或非空(non-null)Long.<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Returns:**

* 0.

<!--第十四行-->
##anyMap
```java
public static Map anyMap()
```

任何非空(non-null)Map.

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Returns:**

* 空Map.

<!--第十五行-->
##anyMapOf
```java
public static < K,V> Map < K,V> anyMapOf(Class< K> keyClazz, Class< V> valueClazz)
```

通用友好的别名anyMap()。为了保持代码清洁，通过@SuppressWarnings("unchecked")来进行替代编译器警告。

任何非空(non-null)Map.

这个方法不进行给定参数的类型检查，它就是为了避免代码中的强制转换(Casting)。然而这可能会改变（类型检查可以添加）将来的主要版本。

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Parameters:**<br>

* keyClazz - map key类型避免类型强制转换(Casting)
* valueClazz - value类型避免类型强制转换(Casting)

**Returns:**

* 空Map.

<!--第十六行-->
##anyObject
```java
public static < T> T anyObject()
```

匹配任何事物, 包含null.

这是: any()和any(java.lang.Class)的别名

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Returns:**

* empty null.

<!--第十七行-->
##anySet**
```java
public static <a href="http://docs.oracle.com/javase/8/docs/api/java/util/Set.html?is-external=true">Set</a> anySet()
```

任何非空(non-null)Set.<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Returns:**

* 空Set.

<!--第十八行-->
##anySetOf
```java
public static < T> Set < T> anySetOf(Class< T> clazz)
```

通用友好的别名anySet()。为了保持代码清洁，通过@SuppressWarnings("unchecked")来进行替代编译器警告。

任何非空(non-null)Set.

这个方法不进行给定参数的类型检查，它就是为了避免代码中的强制转换(Casting)。然而这可能会改变（类型检查可以添加）将来的主要版本。

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Parameters:**

* clazz - 类型属于Set为了避免类型强制转换(Casting)

**Returns:**

* 空Set.

<!--第十九行-->
##anyShort
```java
	public static short anyShort()
```

任何short类型或非空(non-null)Short.

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Returns:**

* 0.

<!--第二十行-->
##anyString
```java
	public static String anyString()
```

任何非空(non-null)String

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Returns:**

* 空String ("").

<!--第二十一行-->
##anyVararg
```java
	public static < T> T anyVararg()
```

任何vararg类型, 即任何参数(arguments)的number和values

例如:

```java

	//verification:
	mock.foo(1, 2);
	mock.foo(1, 2, 3, 4);
	verify(mock, times(2)).foo(anyVararg());

	//stubbing:
	when(mock.foo(anyVararg()).thenReturn(100);

	//prints 100
	System.out.println(mock.foo(1, 2));
	//also prints 100<
	System.out.println(mock.foo(1, 2, 3, 4));

```

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Returns:**

* null.

<!--第二十二行-->
##argThat
```java
public static < T> T argThat(ArgumentMatcher < T> matcher)
```

允许创建自定义的参数匹配模式.这个API在2.0中已经改变,请阅读<a href="http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html">ArgumentMatcher</a>基础指南。

在实现自定义参数匹配模式前，理解使用的场景和non-trivial参数的可用选项是非常重要的。这种方式下，你可以在给定的情况下选择最好的方法来设计制造高质量的测试(清洁和维护).请阅读<a href="http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html">ArgumentMatcher</a>文档学习方法和例子。

在极少数情况下，当参数是基本数据类型(primitive)时，你必须使用相关的intThat()、floatThat()等方法。这些方法在进行自动拆箱(auto-unboxing)时可以避免NullPointerException异常。

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html">ArgumentMatcher</a>类的javadoc帮助文档中的例子

**Parameters:**

* matcher - 取决于选择的参数匹配模式(argument matches)

**Returns:**

* null.

<!--第二十三行-->
##booleanThat
```java
public static boolean booleanThat(ArgumentMatcher < Boolean> matcher)
```

允许创建自定义的Boolean类型参数匹配模式(Boolean argument matchers).

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Parameters:**

* matcher - 取决于选择的参数匹配模式(argument matches)
**Returns:**

* false.

<!--第二十四行-->
##byteThat
```java
public static byte byteThat(ArgumentMatcher < Byte> matcher)
```

允许创建自定义的Byte类型参数匹配模式(Byte argument matchers)

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Parameters:**

* matcher - 取决于选择的参数匹配模式(argument matches)

**Returns:**

* 0.

<!--第二十五行-->
##charThat
```java
public static char charThat(ArgumentMatcher < Character> matcher)
```

允许创建自定义的Character类型参数匹配模式(Character argument matchers)

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Parameters:**

* matcher - 取决于选择的参数匹配模式(argument matches)

**Returns:**

* 0.

<!--第二十六行-->

## contains
```java
	public static String contains(String substring)
```

String参数包含给定的substring字符串.

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子

**Parameters:**

* substring - substring字符串.

**Returns:**

* 空String ("").

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

