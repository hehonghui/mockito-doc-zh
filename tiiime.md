###27. Delegate calls to real instance (Since 1.9.5)

###27. [委托调用真实实例][delegating_call_to_real_instance] (Since 1.9.5)

Useful for spies or partial mocks of objects that are difficult to mock or spy using the usual spy API. Since Mockito 1.10.11, the delegate may or may not be of the same type as the mock. If the type is different, a matching method needs to be found on delegate type otherwise an exception is thrown. Possible use cases for this feature:

当**使用常规的 spy API 去 mock 或者 spy 一个对象很困难**时可以用 delegate 来 spy 或者 mock 对象的某一部分。
从 Mockito 的 1.10.11 版本开始， delegate 有可能和 mock 的类型相同也可能不同。如果不是同一类型，
delegate 类型需要提供一个匹配方法否则就会抛出一个异常。下面是关于这个特性的一些用例:

- Final classes but with an interface
- Already custom proxied object
- Special objects with a finalize method, i.e. to avoid executing it 2 times

- 带有 interface 的 final 类
- 已经自定义代理的对象
- 带有 finalize 方法的特殊对象，就是避免重复执行。

The difference with the regular spy:

和常规 spy 的不同:

- The regular spy (spy(Object)) contains all state from the spied instance and the methods are invoked on the spy. The spied instance is only used at mock creation to copy the state from. If you call a method on a regular spy and it internally calls other methods on this spy, those calls are remembered for verifications, and they can be effectively stubbed.

- 标准的 spy [(spy(Object))][spy] 包含被 spy 实例的所有状态信息，方法在 spy 对象上被调用。被 spy 的对象只在 mock
创建时被用来拷贝状态信息。如果你通过标准 spy 调用一个方法，这个 spy 会调用其内部的其他方法记录这次操作，
以便后面验证使用。等效于存根 (stubbed)操作。

- The mock that delegates simply delegates all methods to the delegate. The delegate is used all the time as methods are delegated onto it. If you call a method on a mock that delegates and it internally calls other methods on this mock, those calls are not remembered for verifications, stubbing does not have effect on them, too. Mock that delegates is less powerful than the regular spy but it is useful when the regular spy cannot be created.

- mock delegates 只是简单的把所有方法委托给 delegate。delegate 一直被当成它代理的方法使用。如果你
从一个 mock 调用它被委托的方法，它会调用其内部方法，这些调用不会被记录，stubbing 在这里也不会生效。
Mock 的 delegates 相对于标准的 spy 来说功能弱了很多，不过在标准 spy 不能被创建的时候很有用。

See more information in docs for AdditionalAnswers.delegatesTo(Object).

更多信息可以看这里 [AdditionalAnswers.delegatesTo(Object)][AdditionalAnswers].

[delegating_call_to_real_instance]:http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#delegating_call_to_real_instance
[spy]:http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#spy(T)
[AdditionalAnswers]:http://site.mockito.org/mockito/docs/current/org/mockito/AdditionalAnswers.html#delegatesTo(java.lang.Object)

---

###28. [MockMaker API ][mock_maker_plugin](Since 1.9.5)
###28. [MockMaker API ][mock_maker_plugin](Since 1.9.5)

Driven by requirements and patches from Google Android guys Mockito now offers an extension point that allows replacing the proxy generation engine. By default, Mockito uses cglib to create dynamic proxies.
为了满足用户的需求和 Android 平台使用。Mockito 现在提供一个扩展点，允许替换代理生成引擎。默认情况下，Mockito 使用 cglib 创建动态代理。

The extension point is for advanced users that want to extend Mockito. For example, it is now possible to use Mockito for Android testing with a help of dexmaker.
这个扩展点是为想要扩展 Mockito 功能的高级用户准备的。比如，我们现在就可以在 dexmaker 的帮助下使用 Mockito
测试 Android。

For more details, motivations and examples please refer to the docs for MockMaker.
更多的细节，原因和示例请看 [MockMaker][MockMaker] 的文档。


[mock_maker_plugin]:http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#mock_maker_plugin
[MockMaker]:http://site.mockito.org/mockito/docs/current/org/mockito/plugins/MockMaker.html

---

###29. [(new) BDD style verification][BDD_behavior_verification] (Since 1.10.0)
###29. [(new) BDD 风格的验证][BDD_behavior_verification] (Since 1.10.0)


Enables Behavior Driven Development (BDD) style verification by starting verification with the BDD then keyword.
开启 Behavior Driven Development (BDD) 风格的验证可以通过 BBD 的关键词 **then** 开始验证。

```java
 given(dog.bark()).willReturn(2);

 // when
 ...

 then(person).should(times(2)).ride(bike);

```

For more information and an example see BDDMockito.then(Object)}
更多信息请查阅 [ BDDMockito.then(Object)][then] .


[BDD_behavior_verification]:http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#BDD_behavior_verification
[then]:http://site.mockito.org/mockito/docs/current/org/mockito/BDDMockito.html#then(T)


 ---


###30. [(new) Spying or mocking abstract classes][spying_abstract_classes] (Since 1.10.12)
###30. [(new) Spying 或 mocking 抽象类][spying_abstract_classes] (Since 1.10.12)

It is now possible to conveniently spy on abstract classes. Note that overusing spies hints at code design smells (see [spy(Object)][spy]).
现在可以方便的 spy 一个抽象类。注意，过度使用 spy 或许意味着代码的设计上有问题。(see [spy(Object)][spy]).

Previously, spying was only possible on instances of objects. New API makes it possible to use constructor when creating an instance of the mock. This is particularly useful for mocking abstract classes because the user is no longer required to provide an instance of the abstract class. At the moment, only parameter-less constructor is supported, let us know if it is not enough.
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
For more information please see [MockSettings.useConstructor()][useConstructor] .
更多信息请见 [MockSettings.useConstructor()][useConstructor] .

[spying_abstract_classes]:http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#spying_abstract_classes
[useConstructor]:http://site.mockito.org/mockito/docs/current/org/mockito/MockSettings.html#useConstructor()


 ---

 ###31. [(new) Mockito mocks can be serialized / deserialized across classloaders][serilization_across_classloader] (Since 1.10.0)
 ###31. [(new) Mockito mocks 可以通过 classloaders 序列化/反序列化][serilization_across_classloader] (Since 1.10.0)

 Mockito introduces serialization across classloader. Like with any other form of serialization, all types in the mock hierarchy have to serializable, inclusing answers. As this serialization mode require considerably more work, this is an opt-in setting.
 Mockito 通过 classloader 引入序列化。和其他形式的序列化一样，所有 mock 层的对象都要被序列化，
 包括 answers。因为序列化模式需要大量的工作，所以这是一个可选择设置。

 ```java
 // 常规的 serialization
 mock(Book.class, withSettings().serializable());

 // 通过 classloaders 序列化
 mock(Book.class, withSettings().serializable(ACROSS_CLASSLOADERS));
 ```

For more details see MockSettings.serializable(SerializableMode).
更多信息请查看 [MockSettings.serializable(SerializableMode)][serializable].


[serilization_across_classloader]:http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#serilization_across_classloader
[serializable]:http://site.mockito.org/mockito/docs/current/org/mockito/MockSettings.html#serializable(org.mockito.mock.SerializableMode)

 ---

 ###32. (new) Better generic support with deep stubs (Since 1.10.0)
 ###32. [(new) Deep stubs 更好的泛型支持][better_generic_support_with_deep_stubs] (Since 1.10.0)

 Deep stubbing has been improved to find generic information if available in the class. That means that classes like this can be used without having to mock the behavior.
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

Please note that in most scenarios a mock returning a mock is wrong.
请注意，大多数情况下 mock 返回一个 mock 对象是错误的。

[better_generic_support_with_deep_stubs]:http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#better_generic_support_with_deep_stubs

 ---

###33.  [(new) Mockito JUnit rule][mockito_junit_rule] (Since 1.10.17)
###33.  [(new) Mockito JUnit rule][mockito_junit_rule] (Since 1.10.17)

Mockito now offers a JUnit rule. Until now in JUnit there was two wasy to initialize fields annotated by Mockito annotations such as @Mock, @Spy, @InjectMocks, etc.
Mockito 现在提供一个 JUnit rule。目前为止，有两种方法可以初始化 fields ，使用 Mockito 提供的注解比如
[@Mock][Mock_], [@Spy][Spy_], [@InjectMocks][InjectMocks_] 等等。

- Annotating the JUnit test class with a @RunWith(@MockitoJUnitRunner.class)
- Invoking MockitoAnnotations.initMocks(Object) in the @Before method

- 用 @RunWith([@MockitoJUnitRunner.class][MockitoJUnitRunner]) 标注 JUnit 测试类
- 在 @Before 之前调用 [MockitoAnnotations.initMocks(Object)][initMocks]

Now you can choose to use a rule :
现在你可以选择使用一个 rule:

```java
 @RunWith(YetAnotherRunner.class)
 public class TheTest {
     @Rule public MockitoRule mockito = MockitoJUnit.rule();
     // ...
 }
```
For more information see [MockitoJUnit.rule()][rule].
更多信息到这里查看 [MockitoJUnit.rule()][rule].

[mockito_junit_rule]:http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#mockito_junit_rule
[Mock_]:http://site.mockito.org/mockito/docs/current/org/mockito/Mock.html
[Spy_]:http://site.mockito.org/mockito/docs/current/org/mockito/Spy.html
[InjectMocks_]:http://site.mockito.org/mockito/docs/current/org/mockito/InjectMocks.html
[MockitoJUnitRunner]:http://site.mockito.org/mockito/docs/current/org/mockito/runners/MockitoJUnitRunner.html
[initMocks]:http://site.mockito.org/mockito/docs/current/org/mockito/MockitoAnnotations.html#initMocks(java.lang.Object)

[rule]:http://site.mockito.org/mockito/docs/current/org/mockito/junit/MockitoJUnit.html#rule()

 ---

###34. (new) Switch on or off plugins (Since 1.10.15)
###34. [(new) 开启和关闭 plugins][PluginSwitch] (Since 1.10.15)

An incubating feature made it's way in mockito that will allow to toggle a mockito-plugin. More information here PluginSwitch.
这是一个测试特性，可以控制一个 mockito-plugin 开启或者关闭。详情请查看 [PluginSwitch][PluginSwitch]

[plugin_switch]:http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html#plugin_switch
[PluginSwitch]:http://site.mockito.org/mockito/docs/current/org/mockito/plugins/PluginSwitch.html

---

###35. Custom verification failure message (Since 2.0.0)
###35. 自定义验证失败信息 (Since 2.0.0)

Allows specifying a custom message to be printed if verification fails.
Examples:
允许声明一个在验证失败时输出的自定义消息
示例:

```java
 // will print a custom message on verification failure
 verify(mock, description("This will print on failure")).someMethod();

 // will work with any verification mode
 verify(mock, times(2).description("someMethod should be called twice")).someMethod();
```
