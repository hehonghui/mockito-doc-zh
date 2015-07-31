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
 LinkedList mockedList = mock(LinkedList.class);

 //stubbing
 when(mockedList.get(0)).thenReturn("first");
 when(mockedList.get(1)).thenThrow(new RuntimeException());

 //following prints "first"
 System.out.println(mockedList.get(0));

 //following throws runtime exception
 System.out.println(mockedList.get(1));

 //following prints "null" because get(999) was not stubbed
 System.out.println(mockedList.get(999));

 //Although it is possible to verify a stubbed invocation, usually it's just redundant
 //If your code cares what get(0) returns then something else breaks (often before even verify() gets executed).
 //If your code doesn't care what get(0) returns then it should not be stubbed. Not convinced? See here.
 verify(mockedList).get(0);
```
 
* By default, for all methods that return value, mock returns null, an empty collection or appropriate primitive/primitive wrapper value (e.g: 0, false, ... for int/Integer, boolean/Boolean, ...).
* Stubbing can be overridden: for example common stubbing can go to fixture setup but the test methods can override it. Please note that overridding stubbing is a potential code smell that points out too much stubbing
* Once stubbed, the method will always return stubbed value regardless of how many times it is called.
* Last stubbing is more important - when you stubbed the same method with the same arguments many times. Other words: the order of stubbing matters but it is only meaningful rarely, e.g. when stubbing exactly the same method calls or sometimes when argument matchers are used, etc.

* 默认情况下，所有的函数都有返回值。

### 3. [参数匹配器 (matchers)]()




### 4. [验证函数的确切、最少、从未调用次数]()




### 5. [为返回值为void的函数通过Stub抛出异常]()




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