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
