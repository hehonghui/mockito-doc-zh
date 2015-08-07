# Mockito 中文文档 ( 2.0.26 beta )

![](http://img.blog.csdn.net/20150731162529393)

Mockito library enables mocks creation, verification and stubbing.

Mockito库能够Mock对象、验证结果以及存根(stubbing)。

This javadoc content is also available on the http://mockito.org web page. All documentation is kept in javadocs because it guarantees consistency between what's on the web and what's in the source code. It allows to access documentation straight from the IDE even if you work offline. It motivates Mockito developers to keep documentation up-to-date with the code that they write, every day, with every commit.

该文档您也可以通过[http://mockito.org](http://mockito.org)获取到。所有文档都保存在javadocs中，因为它能够保证文档与源代码的一致性。这样也能够让离线的用户从IDE直接访问到文档。这样一来也能够激励Mockito开发者在每次写代码、每次提交时更新对应的文档。


## Contents

0. Migrating to 2.0
1. Let's verify some behaviour! 
2. How about some stubbing? 
3. Argument matchers 
4. Verifying exact number of invocations / at least once / never 
5. Stubbing void methods with exceptions 
6. Verification in order 
7. Making sure interaction(s) never happened on mock 
8. Finding redundant invocations 
9. Shorthand for mocks creation - @Mock annotation 
10. Stubbing consecutive calls (iterator-style stubbing) 
11. Stubbing with callbacks 
12. doReturn()|doThrow()|doAnswer()|doNothing()|doCallRealMethod() family of methods
13. Spying on real objects 
14. Changing default return values of unstubbed invocations (Since 1.7) 
15. Capturing arguments for further assertions (Since 1.8.0) 
16. Real partial mocks (Since 1.8.0) 
17. Resetting mocks (Since 1.8.0) 
18. Troubleshooting & validating framework usage (Since 1.8.0) 
19. Aliases for behavior driven development (Since 1.8.0) 
20. Serializable mocks (Since 1.8.1) 
21. New annotations: @Captor, @Spy, @InjectMocks (Since 1.8.3) 
22. Verification with timeout (Since 1.8.5) 
23. Automatic instantiation of @Spies, @InjectMocks and constructor injection goodness (Since 1.9.0)
24. One-liner stubs (Since 1.9.0)
25. Verification ignoring stubs (Since 1.9.0)
26. Mocking details (Since 1.9.5)
27. Delegate calls to real instance (Since 1.9.5)
28. MockMaker API (Since 1.9.5)
29. (new) BDD style verification (Since 1.10.0)
30. (new) Spying or mocking abstract classes (Since 1.10.12)
31. (new) Mockito mocks can be serialized / deserialized across classloaders (Since 1.10.0) 32. (new) Better generic support with deep stubs (Since 1.10.0) 33. (new) Mockito JUnit rule (Since 1.10.17)
34. (new) Switch on or off plugins (Since 1.10.15)
35. (new) Custom verification failure message (Since 2.0.0)


## 目录

0. [迁移到Mockito 2.0]()
1. [验证某些行为]()
2. [如何做一些测试桩 (Stub)]()
3. [参数匹配器 (matchers)]()
4. [验证函数的确切、最少、从未调用次数]()
5. [为返回值为void的函数通过Stub抛出异常]()
6. [按照顺序验证执行结果]()
7. [确保交互(interaction)操作不会执行在mock对象上]()
8. [查找冗余的调用]()
9. [简化mock对象的创建]()
10. [为连续的调用做测试桩 (stub) ]()
11. [为回调做测试桩]()
12. [doReturn()、doThrow()、doAnswer()、doNothing()、doCallRealMethod()系列方法的运用]()
13. [追踪真实对象]()
14. [修改没有测试桩的调用的默认返回值 ( 1.7版本之后 ) ]()
15. [为下一步的断言捕获参数 (1.8版本之后)]()
16. [真实的局部mocks (1.8版本之后)]()
17. [重置mocks对象 (1.8版本之后)]()
18. [故障排查与验证框架的使用 (1.8版本之后)]()
19. [行为驱动开发的别名 (1.8版本之后)]()
20. [序列化mock对象]()
21. [新的注解 : @Captor,@Spy,@ InjectMocks (1.8.3版本之后) ]()
22. [验证超时 (1.8.5版本之后) ]()
23. [自动初始化被@Spies, @InjectMocks注解的字段以及构造函数注入 (1.9.0版本之后)]()
24. [单行测试桩 (1.9.0版本之后) ]()
25. [验证被忽略的测试桩 (1.9.0版本之后)]()
26. [mock详情 (1.9.5版本之后)]()
27. [delegate调用真实的实例 (1.9.5版本之后)]()
28. [MockMaker API (1.9.5版本之后)]()
29. [BDD风格的验证 (1.10.0版本之后)]()
30. [追踪或者Mock抽象类 (1.10.12版本之后)]()
31. [Mockito mock对象通过ClassLoader能被序列化/反序列化 (1.10.0版本之后)]()
32. [deep stubs更好的支持泛型 (1.10.0版本之后)]()
33. [Mockito JUnit 规则 (1.10.17版本之后)]()
34. [开/关插件 (1.10.15版本之后)]()
35. [自定义验证失败消息 (2.0.0版本之后)]()

### 0. 迁移到Mockito 2.0

In order to continue improving Mockito and further improve unit testing experience we want you to upgrade to 2.0. Mockito follows semantic versioning and contains breaking changes only on major version upgrades. In a lifecycle of a library breaking changes are necessary to roll out a set of brand new features that alter the existing behavior or even change the API. We hope that you enjoy Mockito 2.0!
List of breaking changes:

* Mockito is decoupled from Hamcrest and custom matchers API has changed, see ArgumentMatcher for rationale and migration guide

Following examples mock a List, because everyone knows its interface (methods like add(), get(), clear() will be used). 
Don't mock List class 'in real'. Use a real instance instead.

为了持续提升Mockito以及更进一步的提升单元测试体验,我们希望你升级到Mockito 2.0.Mockito遵循语意化的版本控制，除非有非常大的改变才会变化主版本号。在一个库的生命周期中,为了引入一系列有用的特性，修改已存在的行为或者API等重大变更是在所难免的。因此，我们希望你能够爱上 Mockito 2.0!

重要变更 : 

* Mockito从Hamcrest中解耦，自定义的matchers API也发生了改变,查看[ArgumentMatcher](http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html) 的基本原理以及迁移指南。

跟着我们的示例来mock 一个List,因为大家都知道它的接口（例如add(),get(), clear()）。不要mock一个真实的List类型,使用一个真实的实例来替代。

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
 
* By default, for all methods that return value, mock returns null, an empty collection or appropriate primitive/primitive wrapper value (e.g: 0, false, ... for int/Integer, boolean/Boolean, ...).
* Stubbing can be overridden: for example common stubbing can go to fixture setup but the test methods can override it. Please note that overridding stubbing is a potential code smell that points out too much stubbing
* Once stubbed, the method will always return stubbed value regardless of how many times it is called.
* Last stubbing is more important - when you stubbed the same method with the same arguments many times. Other words: the order of stubbing matters but it is only meaningful rarely, e.g. when stubbing exactly the same method calls or sometimes when argument matchers are used, etc.

* 默认情况下，所有的函数都有返回值。mock函数默认返回的是null，一个空的集合或者一个被对象类型包装的内置类型，例如0、false对应的对象类型为Integer、Boolean；
* 测试桩函数可以被覆写 : 例如常见的测试桩函数可以用于初始化夹具，但是测试函数能够覆写它。请注意，覆写测试桩函数是一种可能存在潜在问题的做法；
* 一旦测试桩函数被调用，该函数将会一致返回固定的值；
* 上一次调用测试桩函数有时候极为重要-当你调用一个函数很多次时，最后一次调用可能是你所感兴趣的。

### 3. [参数匹配器 (matchers)]()

Mockito verifies argument values in natural java style: by using an equals() method. Sometimes, when extra flexibility is required then you might use argument matchers:

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
 
Argument matchers allow flexible verification or stubbing. Click here to see more built-in matchers and examples of custom argument matchers / hamcrest matchers.

参数匹配器使验证和测试桩变得更灵活。[点击这里](http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html)查看更多内置的匹配器以及自定义参数匹配器或者hamcrest 匹配器的示例。

For information solely on custom argument matchers check out javadoc for ArgumentMatcher class.

如果仅仅是获取自定义参数匹配器的信息，查看[ArgumentMatcher类文档](http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html)即可。

Be reasonable with using complicated argument matching. The natural matching style using equals() with occasional anyX() matchers tend to give clean & simple tests. Sometimes it's just better to refactor the code to allow equals() matching or even implement equals() method to help out with testing.

为了合理的使用复杂的参数匹配，使用equals()与anyX() 的匹配器会使得测试代码更简洁、简单。有时，会迫使你重构代码以使用equals()匹配或者实现equals()函数来帮助你进行测试。

Also, read section 15 or javadoc for ArgumentCaptor class. ArgumentCaptor is a special implementation of an argument matcher that captures argument values for further assertions.

同时建议你阅读[第15章节](#sec_15)或者[ArgumentCaptor类文档](http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentCaptor.html)。ArgumentCaptor是一个能够捕获参数值的特俗参数匹配器。

Warning on argument matchers:

If you are using argument matchers, all arguments have to be provided by matchers.

参数匹配器的注意点 : 

如果你使用参数匹配器,所有参数都必须由匹配器提供。

E.g: (example shows verification but the same applies to stubbing):
示例 : ( 该示例展示了如何多次应用于测试桩函数的验证 ) 

```java
   verify(mock).someMethod(anyInt(), anyString(), eq("third argument"));
   //above is correct - eq() is also an argument matcher
   // 上述代码是正确的,因为eq()也是一个参数匹配器

   verify(mock).someMethod(anyInt(), anyString(), "third argument");
   //above is incorrect - exception will be thrown because third argument 
   // 上述代码是错误的,因为所有参数必须由匹配器提供，而参数"third argument"并非由参数匹配器提供，因此的缘故会抛出异常
```
 
Matcher methods like anyObject(), eq() do not return matchers. Internally, they record a matcher on a stack and return a dummy value (usually null). This implementation is due static type safety imposed by java compiler. The consequence is that you cannot use anyObject(), eq() methods outside of verified/stubbed method.

像anyObject(), eq()这样的匹配器函数不会返回匹配器。它们会在内部将匹配器记录到一个栈当中，并且返回一个假的值，通常为null。`这样的实现是由于被Java编译器强加的静态类型安全`。结果就是你不能在验证或者测试桩函数之外使用anyObject(), eq()函数。


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

times(1) is the default. Therefore using times(1) explicitly can be omitted.

verify函数默认验证的是执行了times(1)，也就是某个测试函数是否执行了1次.因此，times(1)通常被省略了。


### 5. [为返回值为void的函数通过Stub抛出异常]()

```java
  doThrow(new RuntimeException()).when(mockedList).clear();

   //following throws RuntimeException:
   // 调用这句代码会抛出异常
   mockedList.clear();
```
 
Read more about doThrow|doAnswer family of methods in paragraph 12.
Initially, stubVoid(Object) was used for stubbing voids. Currently stubVoid() is deprecated in favor of doThrow(Throwable). This is because of improved readability and consistency with the family of doAnswer(Answer) methods.

关于doThrow|doAnswer 等函数族的信息请阅读第十二章节。

最初，[stubVoid(Object)](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#stubVoid(T)) 函数用于为无返回值的函数打桩。现在stubVoid()函数已经过时,doThrow(Throwable)成为了它的继承者。这是为了提升与 doAnswer(Answer) 函数族的可读性与一致性。

### 6. [按照顺序验证执行结果]()




### 7. [确保交互(interaction)操作不会执行在mock对象上]()




### 8. [查找冗余的调用]()




### 9. [简化mock对象的创建]()




### 10. [为连续的调用做测试桩 (stub) ]()




### 11. [为回调做测试桩]()




### 12. [doReturn()、doThrow()、doAnswer()、doNothing()、doCallRealMethod()系列方法的运用]()




### 13. [追踪真实对象]()




### 14. [修改没有测试桩的调用的默认返回值 ( 1.7版本之后 ) ]()







### 15. 为下一步的断言捕获参数 (1.8版本之后)