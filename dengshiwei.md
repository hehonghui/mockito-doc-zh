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