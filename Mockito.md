16. Real partial mocks (Since 1.8.0)

16. 真实的局部mocks (1.8版本之后)

Finally, after many internal debates & discussions on the mailing list, partial mock support was added to Mockito. Previously we considered partial mocks as code smells. However, we found a legitimate use case for partial mocks - more reading: here

在内部通过邮件进行了无数争辩和讨论后，最终 Mockito 决定支持部分测试，早前我们不支持是因为我们认为部分测试会让代码变得糟糕。然而，我们发现了部分测试真正合理的用法。[详情点这](http://monkeyisland.pl/2009/01/13/subclass-and-override-vs-partial-mocking-vs-refactoring/)

Before release 1.8 spy() was not producing real partial mocks and it was confusing for some users. Read more about spying: here or in javadoc for spy(Object) method.

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

As usual you are going to read the partial mock warning: Object oriented programming is more less tackling complexity by dividing the complexity into separate, specific, SRPy objects. How does partial mock fit into this paradigm? Well, it just doesn't... Partial mock usually means that the complexity has been moved to a different method on the same object. In most cases, this is not the way you want to design your application.

However, there are rare cases when partial mocks come handy: dealing with code you cannot change easily (3rd party interfaces, interim refactoring of legacy code etc.) However, I wouldn't use partial mocks for new, test-driven & well-designed code.

一如既往，你会去读部分测试的警告部分：面向对象编程通过将抽象的复杂度拆分为一个个独立，精确的 SRPy 对象中，降低了抽象处理的复杂度。那部分测试是怎么遵循这个规范的呢？事实上部分测试并没有遵循这个规范……部分测试通常意味着抽象的复杂度被移动到同一个对象的不同方法中，在大多数情况下，这不会是你想要的应用架构方式。

然而，在一些罕见的情况下部分测试才会是易用的：处理不能轻易修改的代码（第三方接口，临时重构的遗留代码等等）。然而，为了新的，测试驱动和架构优秀的代码，我是不会使用部分测试的。

17. Resetting mocks (Since 1.8.0)

17. 重置mocks对象 (1.8版本之后)

Smart Mockito users hardly use this feature because they know it could be a sign of poor tests. Normally, you don't need to reset your mocks, just create new mocks for each test method.

聪明的 Mockito 使用者很少会用到这个特性，因为他们知道这是出现糟糕测试单元的信号。通常情况下你不会需要重设你的测试单元，只需要为每一个测试方法重新创建一个测试单元就可以了。

Instead of reset() please consider writing simple, small and focused test methods over lengthy, over-specified tests. First potential code smell is reset() in the middle of the test method. This probably means you're testing too much. Follow the whisper of your test methods: "Please keep us small & focused on single behavior". There are several threads about it on mockito mailing list.

如果你真的想通过 reset() 方法满足某些需求的话，请考虑实现简单，小而且专注于测试方法而不是冗长，精确的测试。首先可能出现的代码异味就是测试方法中间那的 reset() 方法。这可能意味着你已经过度测试了。请遵循测试方法的呢喃：请让我们小，而且专注于单一的行为上。在 Mockito 邮件列表中就有好几个讨论是和这个有关的。

The only reason we added reset() method is to make it possible to work with container-injected mocks. See issue 55 (here) or FAQ (here).

添加 reset() 方法的唯一原因就是让它能与容器注入的测试单元协作。详情看 [issue 55](http://code.google.com/p/mockito/issues/detail?id=55) 或 [FAQ](http://code.google.com/p/mockito/wiki/FAQ)。

Don't harm yourself. reset() in the middle of the test method is a code smell (you're probably testing too much).

别自己给自己找麻烦，reset() 方法在测试方法的中间确实是代码异味。


```java
   List mock = mock(List.class);
   when(mock.size()).thenReturn(10);
   mock.add(1);

   reset(mock);
   //at this point the mock forgot any interactions & stubbing
```

18. Troubleshooting & validating framework usage (Since 1.8.0)

18. 故障排查与验证框架的使用 (1.8版本之后)

First of all, in case of any trouble, I encourage you to read the Mockito FAQ: http://code.google.com/p/mockito/wiki/FAQ

In case of questions you may also post to mockito mailing list: http://groups.google.com/group/mockito

Next, you should know that Mockito validates if you use it correctly all the time. However, there's a gotcha so please read the javadoc for validateMockitoUsage()

首先，如果出现了任何问题，我建议你先看 [Mockito FAQ](http://code.google.com/p/mockito/wiki/FAQ)。

任何你提的问题都会被提交到 Mockito 的[邮件列表](http://groups.google.com/group/mockito)中。

然后你应该知道 Mockito 会验证你是否始终以正确的方式使用它，对此有疑惑的话不妨看看 [validateMockitoUsage()](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#validateMockitoUsage()) 的文档说明。

19. Aliases for behavior driven development (Since 1.8.0)

19.行为驱动开发的别名 (1.8版本之后)

Behavior Driven Development style of writing tests uses //given //when //then comments as fundamental parts of your test methods. This is exactly how we write our tests and we warmly encourage you to do so!

Start learning about BDD here: http://en.wikipedia.org/wiki/Behavior_Driven_Development

行为驱动开发实现测试单元的模式将 //given //when //then comments 视作测试方法的基础，这也是我们实现单元测试时被建议做的！

[你可以在这开始学习有关 BDD 的知识](http://en.wikipedia.org/wiki/Behavior_Driven_Development)

The problem is that current stubbing api with canonical role of when word does not integrate nicely with //given //when //then comments. It's because stubbing belongs to given component of the test and not to the when component of the test. Hence BDDMockito class introduces an alias so that you stub method calls with BDDMockito.given(Object) method. Now it really nicely integrates with the given component of a BDD style test!

Here is how the test might look like:

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
 
20. Serializable mocks (Since 1.8.1)

20. 序列化mock对象

Mocks can be made serializable. With this feature you can use a mock in a place that requires dependencies to be serializable.

WARNING: This should be rarely used in unit testing.

模拟对象可以被序列化。有了这个特性你就可以在依赖被序列化的情况下使用模拟对象了。

警告：这个特性很少在单元测试中被使用。

The behaviour was implemented for a specific use case of a BDD spec that had an unreliable external dependency. This was in a web environment and the objects from the external dependency were being serialized to pass between layers.

To create serializable mock use [MockSettings.serializable()](http://site.mockito.org/mockito/docs/current/org/mockito/MockSettings.html#serializable()):

这个特性通过 BDD 拥有不可考外部依赖的特性的具体用例实现，来自外部依赖的 Web 环境和对象会被序列化，然后在不同层之间被传递。

```java
   List serializableMock = mock(List.class, withSettings().serializable());
```

The mock can be serialized assuming all the normal [serialization requirements](http://java.sun.com/j2se/1.5.0/docs/api/java/io/Serializable.html) are met by the class.

模拟对象能被序列化假设所有普通的序列化要求都被类满足了。

Making a real object spy serializable is a bit more effort as the spy(...) method does not have an overloaded version which accepts MockSettings. No worries, you will hardly ever use it.

让一个真实的侦查对象可序列化需要多一些努力，因为 spy(...) 方法没有接收 MockSettings 的重载版本。不过不用担心，你几乎不可能用到这。

```java
 List<Object> list = new ArrayList<Object>();
 List<Object> spy = mock(ArrayList.class, withSettings()
                 .spiedInstance(list)
                 .defaultAnswer(CALLS_REAL_METHODS)
                 .serializable());
```

21. New annotations: @Captor, @Spy, @InjectMocks (Since 1.8.3)

21. 新的注解 : @Captor,@Spy,@ InjectMocks (1.8.3版本之后)

Release 1.8.3 brings new annotations that may be helpful on occasion:

V1.8.3 带来的新注解在某些场景下可能会很实用

@Captor simplifies creation of ArgumentCaptor - useful when the argument to capture is a nasty generic class and you want to avoid compiler warnings

@Spy - you can use it instead spy(Object).

@[Captor](http://site.mockito.org/mockito/docs/current/org/mockito/Captor.html) 简化 [ArgumentCaptor](http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentCaptor.html) 的创建 - 当需要捕获的参数是一个令人讨厌的通用类，而且你想避免编译时警告。

@[Spy](http://site.mockito.org/mockito/docs/current/org/mockito/Spy.html) - 你可以用它代替 [spy(Object) 方法](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#spy(T))

@InjectMocks - injects mock or spy fields into tested object automatically.
Note that @InjectMocks can also be used in combination with the @Spy annotation, it means that Mockito will inject mocks into the partial mock under test. This complexity is another good reason why you should only use partial mocks as a last resort. See point 16 about partial mocks.

@[InjectMocks](http://site.mockito.org/mockito/docs/current/org/mockito/InjectMocks.html) - 自动将模拟对象或侦查域注入到被测试对象中。需要注意的是 @InjectMocks 也能与 @Spy 一起使用，这就意味着 Mockito 会注入模拟对象到测试的部分测试中。它的复杂度也是你应该使用部分测试原因。

All new annotations are *only* processed on MockitoAnnotations.initMocks(Object). Just like for @Mock annotation you can use the built-in runner: MockitoJUnitRunner or rule: MockitoRule.

所有新的注解仅仅在 [MockitoAnnotations.initMocks(Object)](http://site.mockito.org/mockito/docs/current/org/mockito/MockitoAnnotations.html#initMocks(java.lang.Object)) 方法中被处理，就像你在 built-in runner 中使用的 @[Mock](http://site.mockito.org/mockito/docs/current/org/mockito/Mock.html) 注解：[MockitoJUnitRunner](http://site.mockito.org/mockito/docs/current/org/mockito/runners/MockitoJUnitRunner.html) 或 规范: [MockitoRule](http://site.mockito.org/mockito/docs/current/org/mockito/junit/MockitoRule.html).

22. Verification with timeout (Since 1.8.5)

22. 验证超时 (1.8.5版本之后)

Allows verifying with timeout. It causes a verify to wait for a specified period of time for a desired interaction rather than fails immediately if had not already happened. May be useful for testing in concurrent conditions.

允许带有暂停的验证。这使得一个验证去等待一段特定的时间，以获得想要的交互而不是如果还没有发生事件就带来的立即失败。在并发条件下的测试这会很有用。

It feels this feature should be used rarely - figure out a better way of testing your multi-threaded system.

感觉起来这个特性应该很少被使用 - 指出更好的测试多线程系统的方法。

Not yet implemented to work with InOrder verification.

还没有实现去和 InOrder 验证协作。

Examples:

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

23. Automatic instantiation of @Spies, @InjectMocks and constructor injection goodness (Since 1.9.0)

23. 自动初始化被@Spies, @InjectMocks注解的字段以及构造函数注入 (1.9.0版本之后)

Mockito will now try to instantiate @Spy and will instantiate @InjectMocks fields using constructor injection, setter injection, or field injection.

Mockito 现在会通过注入构造方法、setter 或域注入尽可能初始化带有 @[Spy](http://site.mockito.org/mockito/docs/current/org/mockito/Spy.html) 和 @[InjectMocks](http://site.mockito.org/mockito/docs/current/org/mockito/InjectMocks.html) 注解的域或方法。

To take advantage of this feature you need to use MockitoAnnotations.initMocks(Object), MockitoJUnitRunner or MockitoRule.

为了利用这一点特性，你需要使用 [MockitoAnnotations.initMocks(Object)](http://site.mockito.org/mockito/docs/current/org/mockito/MockitoAnnotations.html#initMocks(java.lang.Object)), [MockitoJUnitRunner](http://site.mockito.org/mockito/docs/current/org/mockito/runners/MockitoJUnitRunner.html) 或 [MockitoRule](http://site.mockito.org/mockito/docs/current/org/mockito/junit/MockitoRule.html)。

Read more about available tricks and the rules of injection in the javadoc for InjectMocks

为了 InjectMocks 请在 Java 文档中了解更多可用的技巧和注入的规范

```java
 //instead:
 @Spy BeerDrinker drinker = new BeerDrinker();
 //you can write:
 @Spy BeerDrinker drinker;

 //same applies to @InjectMocks annotation:
 @InjectMocks LocalPub;
```

24. One-liner stubs (Since 1.9.0)

24. 单行测试桩 (1.9.0版本之后)

Mockito will now allow you to create mocks when stubbing. Basically, it allows to create a stub in one line of code. This can be helpful to keep test code clean. For example, some boring stub can be created & stubbed at field initialization in a test:

Mockito 现在允许你在使用测试桩时创建模拟对象。基本上，它允许在一行代码中创建一个测试桩，这对保持代码的整洁很有用。举例来说，有些乏味的测试桩会被创建，并在测试初始化域时被打入，例如：

```java
 public class CarTest {
   Car boringStubbedCar = when(mock(Car.class).shiftGear()).thenThrow(EngineNotStarted.class).getMock();

   @Test public void should... {}
 ```

25. Verification ignoring stubs (Since 1.9.0)

25. 验证被忽略的测试桩 (1.9.0版本之后)

Mockito will now allow to ignore stubbing for the sake of verification. Sometimes useful when coupled with verifyNoMoreInteractions() or verification inOrder(). Helps avoiding redundant verification of stubbed calls - typically we're not interested in verifying stubs.

Mockito 现在允许为了验证无视测试桩。在与 verifyNoMoreInteractions() 方法或验证 inOrder() 方法耦合时，有些时候会很有用。帮助避免繁琐的打入测试桩调用验证 - 显然我们不会对验证测试桩感兴趣。

Warning, ignoreStubs() might lead to overuse of verifyNoMoreInteractions(ignoreStubs(...)); Bear in mind that Mockito does not recommend bombarding every test with verifyNoMoreInteractions() for the reasons outlined in javadoc for verifyNoMoreInteractions(Object...)

Some examples:

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

Advanced examples and more details can be found in javadoc for ignoreStubs(Object...)

更好的例子和更多的细节都可以在 Java 文档的 [ignoreStubs(Object...)](http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#ignoreStubs(java.lang.Object...)) 部分看到。

26. Mocking details (Since 1.9.5)

26. mock详情 (1.9.5版本之后)

To identify whether a particular object is a mock or a spy:

为了区别一个对象是模拟对象还是侦查对象：

```java
     Mockito.mockingDetails(someObject).isMock();
     Mockito.mockingDetails(someObject).isSpy();
```

Both the MockingDetails.isMock() and MockingDetails.isSpy() methods return boolean. As a spy is just a different kind of mock, isMock() returns true if the object is a spy. In future Mockito versions MockingDetails may grow and provide other useful information about the mock, e.g. invocations, stubbing info, etc.

[MockingDetails.isMock()](http://site.mockito.org/mockito/docs/current/org/mockito/MockingDetails.html#isMock()) 和 [MockingDetails.isSpy()](http://site.mockito.org/mockito/docs/current/org/mockito/MockingDetails.html#isSpy()) 方法都会返回一个布尔值。因为一个侦查对象只是模拟对象的一种变种，所以 isMock() 方法在对象是侦查对象是会返回 true。在之后的 Mockito 版本中 MockingDetails 会变得更健壮，并提供其他与模拟对象相关的有用信息，例如：调用，测试桩信息，等等……