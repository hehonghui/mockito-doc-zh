###Field Summary
<table>
	<tbody>
	<tr>
		<td><em>Modifier and Type</em></td>
		<td><em>Field and Description</em></td>
	</tr>
	<tr>
		<td>static Answer< Object ></td>
		<td>CALLS_REAL_METHODS<br>
		Optional Answer to be used with mock(Class, Answer)</td>
	</tr>
	</tbody>
</table>

###Field Detail
<table>
	<tbody> 
	<tr>
		<td>CALLS_REAL_METHODS</td>
	</tr>
	<tr>
		<td>
public static final Answer<Object> CALLS_REAL_METHODS<br><br>
Optional Answer to be used with mock(Class, Answer)<br><br>
Answer can be used to define the return values of unstubbed invocations.<br><br>

This implementation can be helpful when working with legacy code. When this implementation is used, unstubbed methods will delegate to the real implementation. This is a way to create a partial mock object that calls real methods by default.<br><br>

As usual you are going to read the partial mock warning: Object oriented programming is more less tackling complexity by dividing the complexity into separate, specific, SRPy objects. How does partial mock fit into this paradigm? Well, it just doesn't... Partial mock usually means that the complexity has been moved to a different method on the same object. In most cases, this is not the way you want to design your application.<br><br>

However, there are rare cases when partial mocks come handy: dealing with code you cannot change easily (3rd party interfaces, interim refactoring of legacy code etc.) However, I wouldn't use partial mocks for new, test-driven & well-designed code.<br><br>
Example:<br>
<table><tr><td bgcolor=#000000><font color=#ffffff>
	 Foo mock = mock(Foo.class, CALLS_REAL_METHODS);<br><br>
 	// this calls the real implementation of Foo.getSomething()<br>
	 value = mock.getSomething();<br><br>
	 when(mock.getSomething()).thenReturn(fakeValue);<br><br>
 	// now fakeValue is returned<br>
 	value = mock.getSomething();</font></td></tr></table></td>
	</tr>
	</tbody>
</table>

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
用于mock(Class, Answer)的Answer选项</td>
</tr>
</tbody>
</table>

###字段详情
<table>
<tbody>
<tr>
<td><b>CALLS_REAL_METHODS</b></td>
</tr>
<tr>
<td>public static final Answer<Object> CALLS_REAL_METHODS<br><br>
用于mock(Class, Answer)的Answer选项<br><br>
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

###Method Summary
<table>
	<tbody>
	<tr>
		<td><em>Modifier and Type</em></td>
		<td><em>Method and Description</em></td>
	</tr>
	<tr>
		<td>static VerificationAfterDelay</td>
		<td>after(long millis)<br>
		Allows verifying over a given period.</td>
	</tr>
	<tr>
		<td>static VerificationMode</td>
		<td>atLeast(int minNumberOfInvocations)<br>
		Allows at-least-x verification.</td>
	</tr>
	<tr>
		<td>static VerificationMode</td>
		<td>atLeastOnce()<br>
		Allows at-least-once verification.</td>
	</tr>
	<tr>
		<td>static VerificationMode</td>
		<td>atMost(int maxNumberOfInvocations)<br>
		Allows at-most-x verification.</td>
	</tr>
	<tr>
		<td>static VerificationMode</td>
		<td>calls(int wantedNumberOfInvocations)<br>
		Allows non-greedy verification in order.</td>
	</tr>
	</tbody>
</table>

###Method Detail
<table>
	<tr>
		<td><b>after</b></td>
	</tr>
	<tr>
		<td>
public static VerificationAfterDelay after(long millis)<br><br>

Allows verifying over a given period. It causes a verify to wait for a specified period of time for a desired interaction rather than failing immediately if has not already happened. May be useful for testing in concurrent conditions.<br><br>

This differs from timeout() in that after() will wait the full period, whereas timeout() will stop early as soon as verification passes, producing different behaviour when used with times(2), for example, which can pass and then later fail. In that case, timeout would pass as soon as times(2) passes, whereas after would run the full time, which point it will fail, as times(2) has failed.<br><br>

It feels this feature should be used rarely - figure out a better way of testing your multi-threaded system<br><br>

Not yet implemented to work with InOrder verification.<br>
<table><tr><td bgcolor=#000000><font color=#ffffff>
   //passes after 100ms, if someMethod() has only been called once at that time.<br>
   verify(mock, after(100)).someMethod();<br>
   //above is an alias to:<br>
   verify(mock, after(100).times(1)).someMethod();<br><br>

   //passes if someMethod() is called *exactly* 2 times after the given timespan<br>
   verify(mock, after(100).times(2)).someMethod();<br><br>

   //passes if someMethod() has not been called after the given timespan<br>
   verify(mock, after(100).never()).someMethod();<br><br>

   //verifies someMethod() after a given time span using given verification mode<br>
   //useful only if you have your own custom verification modes.<br>
   verify(mock, new After(100, yourOwnVerificationMode)).someMethod();<br><br>
</font></td></tr></table>

See examples in javadoc for Mockito class<br><br>
<b>Parameters:</b><br>
&ensp &ensp millis - - time span in milliseconds<br><br>
<b>Returns:</b><br>
&ensp &ensp verification mode<br><br>
		</td>
	</tr>
	<tr>
		<td>
<b>atLeast</b>
		</td>
	<tr>
		<td>
public static VerificationMode atLeast(int minNumberOfInvocations)<br><br>
Allows at-least-x verification. E.g:<br>

<table><tr><td bgcolor=#000000><font color=#ffffff>verify(mock, atLeast(3)).someMethod("some arg");</font></td></tr></table>

See examples in javadoc for Mockito class<br><br>
<b>Parameters:</b><br>
&ensp &ensp minNumberOfInvocations - minimum number of invocations<br><br>
<b>Returns:</b><br>
&ensp &ensp verification mode<br><br>
		</td>
	</tr>
	<tr>
		<td>
<b>atLeastOnce</b>
		</td>
	<tr>
		<td>
public static VerificationMode atLeastOnce()<br><br>
Allows at-least-once verification. E.g:<br>

<table><tr><td bgcolor=#000000><font color=#ffffff>verify(mock, atLeastOnce()).someMethod("some arg");</font></td></tr></table>

Alias to atLeast(1).<br><br>
See examples in javadoc for Mockito class<br><br>
<b>Returns:</b><br>
&ensp &ensp verification mode<br><br>
		</td>
	</tr>
	<tr>
		<td>
<b>atMost</b>
		</td>
	<tr>
		<td>
public static VerificationMode atMost(int maxNumberOfInvocations)<br><br>
Allows at-most-x verification. E.g:<br>

<table><tr><td bgcolor=#000000><font color=#ffffff>verify(mock, atMost(3)).someMethod("some arg");</font></td></tr></table>

See examples in javadoc for Mockito class<br><br>
<b>Parameters::</b><br>
&ensp &ensp maxNumberOfInvocations - max number of invocations<br><br>
<b>Returns:</b><br>
&ensp &ensp verification mode<br><br>
		</td>
	</tr>
	<tr>
		<td>
<b>calls</b>
		</td>
	<tr>
		<td>
public static VerificationMode calls(int wantedNumberOfInvocations)<br><br>
Allows non-greedy verification in order. For example:<br>

<table><tr><td bgcolor=#000000><font color=#ffffff>inOrder.verify( mock, calls( 2 )).someMethod( "some arg" );</font></td></tr></table>

<ul>
<li>will not fail if the method is called 3 times, unlike times( 2 )</li>
<li>will not mark the third invocation as verified, unlike atLeast( 2 )</li>
</ul>

This verification mode can only be used with in order verification.<br><br>

<b>Parameters::</b><br>
&ensp &ensp wantedNumberOfInvocations - number of invocations to verify<br><br>
<b>Returns:</b><br>
&ensp &ensp verification mode<br><br>
		</td>
	</tr>
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

###方法详情
<table>
	<tr>
		<td><b>after</b></td>
	</tr>
	<tr>
		<td>
public static VerificationAfterDelay after(long millis)<br><br>

在给定的时间后进行验证。它会为了预期的效果进行等待一段时间后进行验证，而不是因为没发生而立即失败。这可能对于测试多并发条件非常有用。<br><br>

after()等待整个周期的特点不同于timeout()，而timeout()一旦验证通过就尽快停止，例如：当使用times(2)可以产生不同的行为方式，可能通过后过会又失败。这种情况下，timeout只要times(2)通过就会通过，然后after执行完整个周期时间，可能会失败，也意味着times(2)也失败。<br><br>

感觉这个方法应该少使用——找到更好的方法测试你的多线程系统。<br><br>

对尚未实现的工作进行验证。<br>
<table><tr><td bgcolor=#000000><font color=#ffffff>
   //passes after 100ms, if someMethod() has only been called once at that time.<br>
   verify(mock, after(100)).someMethod();<br>
   //above is an alias to:<br>
   verify(mock, after(100).times(1)).someMethod();<br><br>

   //passes if someMethod() is called *exactly* 2 times after the given timespan<br>
   verify(mock, after(100).times(2)).someMethod();<br><br>

   //passes if someMethod() has not been called after the given timespan<br>
   verify(mock, after(100).never()).someMethod();<br><br>

   //verifies someMethod() after a given time span using given verification mode<br>
   //useful only if you have your own custom verification modes.<br>
   verify(mock, new After(100, yourOwnVerificationMode)).someMethod();<br><br>
</font></td></tr></table>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html">Mockito</a>类的javadoc帮助文档中的例子<br><br>
<b>Parameters:</b><br>
&ensp &ensp millis - - time span in milliseconds<br><br>
<b>Returns:</b><br>
&ensp &ensp verification mode<br><br>
		</td>
	</tr>
	<tr>
		<td>
<b>atLeast</b>
		</td>
	<tr>
		<td>
public static VerificationMode atLeast(int minNumberOfInvocations)<br><br>
允许至少进行x次验证。例如：<br>

<table><tr><td bgcolor=#000000><font color=#ffffff>verify(mock, atLeast(3)).someMethod("some arg");</font></td></tr></table>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html">Mockito</a>类的javadoc帮助文档中的例子<br><br>
<b>Parameters:</b><br>
&ensp &ensp minNumberOfInvocations - invocations的最小次数<br><br>
<b>Returns:</b><br>
&ensp &ensp verification mode<br><br>
		</td>
	</tr>
	<tr>
		<td>
<b>atLeastOnce</b>
		</td>
	<tr>
		<td>
public static VerificationMode atLeastOnce()<br><br>
至少进行一次一次验证。例如:<br>

<table><tr><td bgcolor=#000000><font color=#ffffff>verify(mock, atLeastOnce()).someMethod("some arg");</font></td></tr></table>

atLeast(1)的别名.<br><br>
参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html">Mockito</a>类的javadoc帮助文档中的例子<br><br>
<b>Returns:</b><br>
&ensp &ensp verification mode<br><br>
		</td>
	</tr>
	<tr>
		<td>
<b>atMost</b>
		</td>
	<tr>
		<td>
public static VerificationMode atMost(int maxNumberOfInvocations)<br><br>
至多进行x次验证. 例如:<br>

<table><tr><td bgcolor=#000000><font color=#ffffff>verify(mock, atMost(3)).someMethod("some arg");</font></td></tr></table>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Mockito.html">Mockito</a>类的javadoc帮助文档中的例子<br><br>
<b>Parameters::</b><br>
&ensp &ensp maxNumberOfInvocations - invocations的最大次数<br><br>
<b>Returns:</b><br>
&ensp &ensp verification mode<br><br>
		</td>
	</tr>
	<tr>
		<td>
<b>calls</b>
		</td>
	<tr>
		<td>
public static VerificationMode calls(int wantedNumberOfInvocations)<br><br>

允许顺序进行non-greedy验证. 例如:<br>

<table><tr><td bgcolor=#000000><font color=#ffffff>inOrder.verify( mock, calls( 2 )).someMethod( "some arg" );</font></td></tr></table>

<ul>
<li>如果这个方法调用3次不会失败，不同于times(2)</li>
<li>不会标记第三次验证，不同于atLeast(2)</li>
</ul>

这个verification mode只能用于顺序验证.<br><br>

<b>Parameters::</b><br>
&ensp &ensp wantedNumberOfInvocations - 验证的次数<br><br>
<b>Returns:</b><br>
&ensp &ensp verification mode<br><br>
		</td>
	</tr>
</table>

###Methods inherited from class org.mockito.Matchers

<table>
	<tr>
		<td><b>any</b></td>
	</tr>
	<tr>
		<td>
public static <T> T any()<br><br>

Matches anything, including nulls<br><br>

Shorter alias to anyObject()<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

This is an alias of: anyObject() and any(java.lang.Class)<br><br>

<b>Returns:</b><br>
&ensp &ensp null<br><br>
		</td>
	</tr>
<!--第二行-->
	<tr>
		<td><b>any</b></td>
	</tr>
	<tr>
		<td>
public static <T> T any(Class<T> clazz)<br><br>

Matches any object, including nulls<br><br>

This method doesn't do type checks with the given parameter, it is only there to avoid casting in your code. This might however change (type checks could be added) in a future major release.<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

This is an alias of: any() and anyObject()<br><br>

<b>Returns:</b><br>
&ensp &ensp null<br><br>
		</td>
	</tr>

<!--第三行-->
	<tr>
		<td><b>anyBoolean</b></td>
	</tr>
	<tr>
		<td>
public static boolean anyBoolean()<br><br>

Any boolean or non-null Boolean<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Returns:</b><br>
&ensp &ensp false<br><br>
		</td>
	</tr>

<!--第四行-->
	<tr>
		<td><b>anyByte</b></td>
	</tr>
	<tr>
		<td>
public static byte anyByte()<br><br>

Any byte or non-null Byte.<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Returns:</b><br>
&ensp &ensp 0<br><br>
		</td>
	</tr>

<!--第五行-->
	<tr>
		<td><b>anyChar</b></td>
	</tr>
	<tr>
		<td>
public static char anyChar()<br><br>

Any char or non-null Character.<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Returns:</b><br>
&ensp &ensp 0<br><br>
		</td>
	</tr>

<!--第六行-->
	<tr>
		<td><b>anyCollection</b></td>
	</tr>
	<tr>
		<td>
public static <a href="http://docs.oracle.com/javase/8/docs/api/java/util/Collection.html?is-external=true">Collection</a> anyCollection()<br><br>

Any non-null Collection.<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Returns:</b><br>
&ensp &ensp empty Collection.<br><br>
		</td>
	</tr>

<!--第七行-->
	<tr>
		<td><b>anyCollectionOf</b></td>
	</tr>
	<tr>
		<td>
public static < T > <a href="http://docs.oracle.com/javase/8/docs/api/java/util/Collection.html?is-external=true">Collection</a> < T > anyCollectionOf(Class<T> clazz)<br><br>

Generic friendly alias to anyCollection(). It's an alternative to @SuppressWarnings("unchecked") to keep code clean of compiler warnings.<br><br>

Any non-null Collection.<br><br>

This method doesn't do type checks with the given parameter, it is only there to avoid casting in your code. This might however change (type checks could be added) in a future major release.<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Parameters</b><br>
clazz - Type owned by the collection to avoid casting<br><br>
<b>Returns:</b><br>
&ensp &ensp empty Collection.<br><br>
		</td>
	</tr>

<!--第八行-->
	<tr>
		<td><b>anyDouble</b></td>
	</tr>
	<tr>
		<td>
public static double anyDouble()<br><br>

Any double or non-null Double.<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Returns:</b><br>
&ensp &ensp 0.<br><br>
		</td>
	</tr>

<!--第九行-->
	<tr>
		<td><b>anyFloat</b></td>
	</tr>
	<tr>
		<td>
public static float anyFloat()<br><br>

Any float or non-null Float.<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Returns:</b><br>
&ensp &ensp 0.<br><br>
		</td>
	</tr>

<!--第十行-->
	<tr>
		<td><b>anyInt</b></td>
	</tr>
	<tr>
		<td>
public static int anyInt()<br><br>

Any int or non-null Integer.<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Returns:</b><br>
&ensp &ensp 0.<br><br>
		</td>
	</tr>

<!--第十一行-->
	<tr>
		<td><b>anyList</b></td>
	</tr>
	<tr>
		<td>
public static <a href="http://docs.oracle.com/javase/8/docs/api/java/util/List.html?is-external=true">List</a> anyList()<br><br>

Any non-null List.<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Returns:</b><br>
&ensp &ensp empty List.<br><br>
		</td>
	</tr>

<!--第十二行-->
	<tr>
		<td><b>anyListOf</b></td>
	</tr>
	<tr>
		<td>
public static < T >  <a href="http://docs.oracle.com/javase/8/docs/api/java/util/List.html?is-external=true">List</a> < T > anyListOf(Class< T > clazz)<br><br>

Generic friendly alias to anyList(). It's an alternative to @SuppressWarnings("unchecked") to keep code clean of compiler warnings.<br><br>

Any non-null List.<br><br>

This method doesn't do type checks with the given parameter, it is only there to avoid casting in your code. This might however change (type checks could be added) in a future major release.<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Parameters:</b><br>
&ensp &ensp clazz - Type owned by the list to avoid casting<br><br>
<b>Returns:</b><br>
&ensp &ensp empty List.<br><br>
		</td>
	</tr>

<!--第十三行-->
	<tr>
		<td><b>anyLong</b></td>
	</tr>
	<tr>
		<td>
public static long anyLong()<br><br>

Any long or non-null Long.<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Returns:</b><br>
&ensp &ensp 0.<br><br>
		</td>
	</tr>

<!--第十四行-->
	<tr>
		<td><b>anyMap</b></td>
	</tr>
	<tr>
		<td>
public static <a href="http://docs.oracle.com/javase/8/docs/api/java/util/Map.html?is-external=true">Map</a> anyMap()<br><br>

Any non-null Map.<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Returns:</b><br>
&ensp &ensp empty Map.<br><br>
		</td>
	</tr>

<!--第十五行-->
	<tr>
		<td><b>anyMapOf</b></td>
	</tr>
	<tr>
		<td>
public static < K,V> <a href="http://docs.oracle.com/javase/8/docs/api/java/util/Map.html?is-external=true">Map</a> < K,V> anyMapOf(Class< K> keyClazz, Class< V> valueClazz)<br><br>

Generic friendly alias to anyMap(). It's an alternative to @SuppressWarnings("unchecked") to keep code clean of compiler warnings.<br><br>

Any non-null Map.<br><br>

This method doesn't do type checks with the given parameter, it is only there to avoid casting in your code. This might however change (type checks could be added) in a future major release.<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Parameters:</b><br>
&ensp &ensp keyClazz - Type of the map key to avoid casting<br>
&ensp &ensp valueClazz - Type of the value to avoid casting<br><br>
<b>Returns:</b><br>
&ensp &ensp empty Map.<br><br>
		</td>
	</tr>

<!--第十六行-->
	<tr>
		<td><b>anyObject</b></td>
	</tr>
	<tr>
		<td>
public static < T> T anyObject()<br><br>

Matches anything, including null.<br><br>

This is an alias of: any() and any(java.lang.Class)<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Returns:</b><br>
&ensp &ensp empty null.<br><br>
		</td>
	</tr>

<!--第十七行-->
	<tr>
		<td><b>anySet</b></td>
	</tr>
	<tr>
		<td>
public static <a href="http://docs.oracle.com/javase/8/docs/api/java/util/Set.html?is-external=true">Set</a> anySet()<br><br>

Any non-null Set.<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Returns:</b><br>
&ensp &ensp empty Set.<br><br>
		</td>
	</tr>

<!--第十八行-->
	<tr>
		<td><b>anySetOf</b></td>
	</tr>
	<tr>
		<td>
public static < T> <a href="http://docs.oracle.com/javase/8/docs/api/java/util/Set.html?is-external=true">Set</a> < T> anySetOf(Class< T> clazz)<br><br>

Generic friendly alias to anySet(). It's an alternative to @SuppressWarnings("unchecked") to keep code clean of compiler warnings.<br><br>

Any non-null Set.<br><br>

This method doesn't do type checks with the given parameter, it is only there to avoid casting in your code. This might however change (type checks could be added) in a future major release.<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Parameters:</b><br>
&ensp &ensp clazz - Type owned by the Set to avoid casting<br><br>
<b>Returns:</b><br>
&ensp &ensp empty Set.<br><br>
		</td>
	</tr>

<!--第十九行-->
	<tr>
		<td><b>anyShort</b></td>
	</tr>
	<tr>
		<td>
public static short anyShort()<br><br>

Any short or non-null Short.<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Returns:</b><br>
&ensp &ensp 0.<br><br>
		</td>
	</tr>

<!--第二十行-->
	<tr>
		<td><b>anyString</b></td>
	</tr>
	<tr>
		<td>
public static String anyString()<br><br>

Any non-null String<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Returns:</b><br>
&ensp &ensp empty String ("").<br><br>
		</td>
	</tr>

<!--第二十一行-->
	<tr>
		<td><b>anyVararg</b></td>
	</tr>
	<tr>
		<td>
public static < T> T anyVararg()<br><br>

Any vararg, meaning any number and values of arguments.<br><br>

Examples:<br>
<table><tr><td bgcolor=#000000><font color=#ffffff>
   //verification:<br>
   mock.foo(1, 2);<br>
   mock.foo(1, 2, 3, 4);<br><br>

   verify(mock, times(2)).foo(anyVararg());<br><br>

   //stubbing:<br>
   when(mock.foo(anyVararg()).thenReturn(100);<br><br>

   //prints 100<br>
   System.out.println(mock.foo(1, 2));<br>
   //also prints 100<br>
   System.out.println(mock.foo(1, 2, 3, 4));<br><br>
</font></td></tr></table>
See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Returns:</b><br>
&ensp &ensp null.<br><br>
		</td>
	</tr>

<!--第二十二行-->
	<tr>
		<td><b>argThat</b></td>
	</tr>
	<tr>
		<td>
public static < T> T argThat(<a href="http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html">ArgumentMatcher</a> < T> matcher)<br><br>

Allows creating custom argument matchers. This API has changed in 2.0, please read <a href="http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html">ArgumentMatcher</a> for rationale and migration guide.<br><br>

It is important to understand the use cases and available options for dealing with non-trivial arguments before implementing custom argument matchers. This way, you can select the best possible approach for given scenario and produce highest quality test (clean and maintainable). Please read the documentation for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html">ArgumentMatcher</a> to learn about approaches and see the examples.<br><br>

In rare cases when the parameter is a primitive then you *must* use relevant intThat(), floatThat(), etc. method. This way you will avoid NullPointerException during auto-unboxing.<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html">ArgumentMatcher</a> class<br><br>

<b>Parameters:</b><br>
&ensp &ensp matcher - decides whether argument matches<br><br>
<b>Returns:</b><br>
&ensp &ensp null.<br><br>
		</td>
	</tr>

<!--第二十三行-->
	<tr>
		<td><b>booleanThat</b></td>
	</tr>
	<tr>
		<td>
public static boolean booleanThat(<a href="http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html">ArgumentMatcher</a> < Boolean> matcher)<br><br>

Allows creating custom Boolean argument matchers.<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Parameters:</b><br>
&ensp &ensp matcher - decides whether argument matches<br><br>
<b>Returns:</b><br>
&ensp &ensp false.<br><br>
		</td>
	</tr>

<!--第二十四行-->
	<tr>
		<td><b>byteThat</b></td>
	</tr>
	<tr>
		<td>
public static byte byteThat(<a href="http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html">ArgumentMatcher</a> < Byte> matcher)<br><br>

Allows creating custom Byte argument matchers.<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Parameters:</b><br>
&ensp &ensp matcher - decides whether argument matches<br><br>
<b>Returns:</b><br>
&ensp &ensp 0.<br><br>
		</td>
	</tr>

<!--第二十五行-->
	<tr>
		<td><b>charThat</b></td>
	</tr>
	<tr>
		<td>
public static char charThat(<a href="http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html">ArgumentMatcher</a> < Character> matcher)<br><br>

Allows creating custom Character argument matchers.<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Parameters:</b><br>
&ensp &ensp matcher - decides whether argument matches<br><br>
<b>Returns:</b><br>
&ensp &ensp 0.<br><br>
		</td>
	</tr>

<!--第二十六行-->
	<tr>
		<td><b>contains</b></td>
	</tr>
	<tr>
		<td>
public static String contains(String substring)<br><br>

String argument that contains the given substring.<br><br>

See examples in javadoc for <a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a> class<br><br>

<b>Parameters:</b><br>
&ensp &ensp substring - the substring.<br><br>
<b>Returns:</b><br>
&ensp &ensp empty String ("").<br><br>
		</td>
	</tr>
</table>

###继承org.mockito.Matchers的方法

<table>
	<tr>
		<td><b>any</b></td>
	</tr>
	<tr>
		<td>
public static <T> T any()<br><br>

匹配任何值，包括null<br><br>

anyObject()的别名<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

这是: anyObject() and any(java.lang.Class)的别名<br><br>

<b>Returns:</b><br>
&ensp &ensp null<br><br>
		</td>
	</tr>
<!--第二行-->
	<tr>
		<td><b>any</b></td>
	</tr>
	<tr>
		<td>
public static <T> T any(Class<T> clazz)<br><br>

匹配任何对象，包括null<br><br>

这个方法不进行给定参数的类型检查，它就是为了避免代码中的强制转换(Casting)。然而这可能会改变（类型检查可以添加）将来的主要版本。<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

这是: anyObject() and any(java.lang.Class)的别名<br><br>

<b>Returns:</b><br>
&ensp &ensp null<br><br>
		</td>
	</tr>

<!--第三行-->
	<tr>
		<td><b>anyBoolean</b></td>
	</tr>
	<tr>
		<td>
public static boolean anyBoolean()<br><br>

任何boolean类型或非空(non-null)的Boolean.<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Returns:</b><br>
&ensp &ensp false<br><br>
		</td>
	</tr>

<!--第四行-->
	<tr>
		<td><b>anyByte</b></td>
	</tr>
	<tr>
		<td>
public static byte anyByte()<br><br>

任何byte类型变量或非空(non-null)Byte.<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Returns:</b><br>
&ensp &ensp 0<br><br>
		</td>
	</tr>

<!--第五行-->
	<tr>
		<td><b>anyChar</b></td>
	</tr>
	<tr>
		<td>
public static char anyChar()<br><br>

任何char类型变量或非空(non-null)的Character.<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Returns:</b><br>
&ensp &ensp 0<br><br>
		</td>
	</tr>

<!--第六行-->
	<tr>
		<td><b>anyCollection</b></td>
	</tr>
	<tr>
		<td>
public static <a href="http://docs.oracle.com/javase/8/docs/api/java/util/Collection.html?is-external=true">Collection</a> anyCollection()<br><br>

任何非空(non-null)的Collection.<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Returns:</b><br>
&ensp &ensp 空Collection.<br><br>
		</td>
	</tr>

<!--第七行-->
	<tr>
		<td><b>anyCollectionOf</b></td>
	</tr>
	<tr>
		<td>
public static < T > <a href="http://docs.oracle.com/javase/8/docs/api/java/util/Collection.html?is-external=true">Collection</a> < T > anyCollectionOf(Class<T> clazz)<br><br>

通用友好的别名anyCollection()。为了保持代码清洁，通过@SuppressWarnings("unchecked")来进行替代编译器警告。<br><br>

任何非空(non-null)Collection.<br><br>

这个方法不进行给定参数的类型检查，它就是为了避免代码中的强制转换(Casting)。然而这可能会改变（类型检查可以添加）将来的主要版本。<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Parameters</b><br>
clazz - 类型属于Collection类型避免类型转换(Casting)<br><br>
<b>Returns:</b><br>
&ensp &ensp 空Collection.<br><br>
		</td>
	</tr>

<!--第八行-->
	<tr>
		<td><b>anyDouble</b></td>
	</tr>
	<tr>
		<td>
public static double anyDouble()<br><br>

任何double类型或非空(non-null)的Double.<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Returns:</b><br>
&ensp &ensp 0.<br><br>
		</td>
	</tr>

<!--第九行-->
	<tr>
		<td><b>anyFloat</b></td>
	</tr>
	<tr>
		<td>
public static float anyFloat()<br><br>

任何float类型或非空(non-null)Float.<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Returns:</b><br>
&ensp &ensp 0.<br><br>
		</td>
	</tr>

<!--第十行-->
	<tr>
		<td><b>anyInt</b></td>
	</tr>
	<tr>
		<td>
public static int anyInt()<br><br>

任何int或非空(non-null)Integer.<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Returns:</b><br>
&ensp &ensp 0.<br><br>
		</td>
	</tr>

<!--第十一行-->
	<tr>
		<td><b>anyList</b></td>
	</tr>
	<tr>
		<td>
public static <a href="http://docs.oracle.com/javase/8/docs/api/java/util/List.html?is-external=true">List</a> anyList()<br><br>

任何非空(non-null)List.<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Returns:</b><br>
&ensp &ensp 空List.<br><br>
		</td>
	</tr>

<!--第十二行-->
	<tr>
		<td><b>anyListOf</b></td>
	</tr>
	<tr>
		<td>
public static < T >  <a href="http://docs.oracle.com/javase/8/docs/api/java/util/List.html?is-external=true">List</a> < T > anyListOf(Class< T > clazz)<br><br>

通用友好的别名anyList()。为了保持代码清洁，通过@SuppressWarnings("unchecked")来进行替代编译器警告。<br><br>

任何非空(non-null)List.<br><br>

这个方法不进行给定参数的类型检查，它就是为了避免代码中的强制转换(Casting)。然而这可能会改变（类型检查可以添加）将来的主要版本。<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Parameters:</b><br>
&ensp &ensp clazz - 类型属于List类型避免类型转换(Casting)<br><br>
<b>Returns:</b><br>
&ensp &ensp 空List.<br><br>
		</td>
	</tr>

<!--第十三行-->
	<tr>
		<td><b>anyLong</b></td>
	</tr>
	<tr>
		<td>
public static long anyLong()<br><br>

任何long类型或非空(non-null)Long.<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Returns:</b><br>
&ensp &ensp 0.<br><br>
		</td>
	</tr>

<!--第十四行-->
	<tr>
		<td><b>anyMap</b></td>
	</tr>
	<tr>
		<td>
public static <a href="http://docs.oracle.com/javase/8/docs/api/java/util/Map.html?is-external=true">Map</a> anyMap()<br><br>

任何非空(non-null)Map.<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Returns:</b><br>
&ensp &ensp 空Map.<br><br>
		</td>
	</tr>

<!--第十五行-->
	<tr>
		<td><b>anyMapOf</b></td>
	</tr>
	<tr>
		<td>
public static < K,V> <a href="http://docs.oracle.com/javase/8/docs/api/java/util/Map.html?is-external=true">Map</a> < K,V> anyMapOf(Class< K> keyClazz, Class< V> valueClazz)<br><br>

通用友好的别名anyMap()。为了保持代码清洁，通过@SuppressWarnings("unchecked")来进行替代编译器警告。<br><br>

任何非空(non-null)Map.<br><br>

这个方法不进行给定参数的类型检查，它就是为了避免代码中的强制转换(Casting)。然而这可能会改变（类型检查可以添加）将来的主要版本。<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Parameters:</b><br>
&ensp &ensp keyClazz - map key类型避免类型强制转换(Casting)<br>
&ensp &ensp valueClazz - value类型避免类型强制转换(Casting)<br><br>
<b>Returns:</b><br>
&ensp &ensp 空Map.<br><br>
		</td>
	</tr>

<!--第十六行-->
	<tr>
		<td><b>anyObject</b></td>
	</tr>
	<tr>
		<td>
public static < T> T anyObject()<br><br>

匹配任何事物, 包含null.<br><br>

这是: any()和any(java.lang.Class)的别名<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Returns:</b><br>
&ensp &ensp empty null.<br><br>
		</td>
	</tr>

<!--第十七行-->
	<tr>
		<td><b>anySet</b></td>
	</tr>
	<tr>
		<td>
public static <a href="http://docs.oracle.com/javase/8/docs/api/java/util/Set.html?is-external=true">Set</a> anySet()<br><br>

任何非空(non-null)Set.<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Returns:</b><br>
&ensp &ensp 空Set.<br><br>
		</td>
	</tr>

<!--第十八行-->
	<tr>
		<td><b>anySetOf</b></td>
	</tr>
	<tr>
		<td>
public static < T> <a href="http://docs.oracle.com/javase/8/docs/api/java/util/Set.html?is-external=true">Set</a> < T> anySetOf(Class< T> clazz)<br><br>

通用友好的别名anySet()。为了保持代码清洁，通过@SuppressWarnings("unchecked")来进行替代编译器警告。<br><br>

任何非空(non-null)Set.<br><br>

这个方法不进行给定参数的类型检查，它就是为了避免代码中的强制转换(Casting)。然而这可能会改变（类型检查可以添加）将来的主要版本。<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Parameters:</b><br>
&ensp &ensp clazz - 类型属于Set为了避免类型强制转换(Casting)<br><br>
<b>Returns:</b><br>
&ensp &ensp 空Set.<br><br>
		</td>
	</tr>

<!--第十九行-->
	<tr>
		<td><b>anyShort</b></td>
	</tr>
	<tr>
		<td>
public static short anyShort()<br><br>

任何short类型或非空(non-null)Short.<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Returns:</b><br>
&ensp &ensp 0.<br><br>
		</td>
	</tr>

<!--第二十行-->
	<tr>
		<td><b>anyString</b></td>
	</tr>
	<tr>
		<td>
public static String anyString()<br><br>

任何非空(non-null)String<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Returns:</b><br>
&ensp &ensp 空String ("").<br><br>
		</td>
	</tr>

<!--第二十一行-->
	<tr>
		<td><b>anyVararg</b></td>
	</tr>
	<tr>
		<td>
public static < T> T anyVararg()<br><br>

任何vararg类型, 即任何参数(arguments)的number和values<br><br>

例如:<br>
<table><tr><td bgcolor=#000000><font color=#ffffff>
   //verification:<br>
   mock.foo(1, 2);<br>
   mock.foo(1, 2, 3, 4);<br><br>

   verify(mock, times(2)).foo(anyVararg());<br><br>

   //stubbing:<br>
   when(mock.foo(anyVararg()).thenReturn(100);<br><br>

   //prints 100<br>
   System.out.println(mock.foo(1, 2));<br>
   //also prints 100<br>
   System.out.println(mock.foo(1, 2, 3, 4));<br><br>
</font></td></tr></table>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Returns:</b><br>
&ensp &ensp null.<br><br>
		</td>
	</tr>

<!--第二十二行-->
	<tr>
		<td><b>argThat</b></td>
	</tr>
	<tr>
		<td>
public static < T> T argThat(<a href="http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html">ArgumentMatcher</a> < T> matcher)<br><br>

允许创建自定义的参数匹配模式.这个API在2.0中已经改变,请阅读<a href="http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html">ArgumentMatcher</a>基础指南。<br><br>

在实现自定义参数匹配模式前，理解使用的场景和non-trivial参数的可用选项是非常重要的。这种方式下，你可以在给定的情况下选择最好的方法来设计制造高质量的测试(清洁和维护).请阅读<a href="http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html">ArgumentMatcher</a>文档学习方法和例子。<br><br>

在极少数情况下，当参数是基本数据类型(primitive)时，你必须使用相关的intThat()、floatThat()等方法。这些方法在进行自动拆箱(auto-unboxing)时可以避免NullPointerException异常。<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html">ArgumentMatcher</a>类的javadoc帮助文档中的例子<br><br>

<b>Parameters:</b><br>
&ensp &ensp matcher - 取决于选择的参数匹配模式(argument matches)<br><br>
<b>Returns:</b><br>
&ensp &ensp null.<br><br>
		</td>
	</tr>

<!--第二十三行-->
	<tr>
		<td><b>booleanThat</b></td>
	</tr>
	<tr>
		<td>
public static boolean booleanThat(<a href="http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html">ArgumentMatcher</a> < Boolean> matcher)<br><br>

允许创建自定义的Boolean类型参数匹配模式(Boolean argument matchers).<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Parameters:</b><br>
&ensp &ensp matcher - 取决于选择的参数匹配模式(argument matches)<br><br>
<b>Returns:</b><br>
&ensp &ensp false.<br><br>
		</td>
	</tr>

<!--第二十四行-->
	<tr>
		<td><b>byteThat</b></td>
	</tr>
	<tr>
		<td>
public static byte byteThat(<a href="http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html">ArgumentMatcher</a> < Byte> matcher)<br><br>

允许创建自定义的Byte类型参数匹配模式(Byte argument matchers)<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Parameters:</b><br>
&ensp &ensp matcher - 取决于选择的参数匹配模式(argument matches)<br><br>
<b>Returns:</b><br>
&ensp &ensp 0.<br><br>
		</td>
	</tr>

<!--第二十五行-->
	<tr>
		<td><b>charThat</b></td>
	</tr>
	<tr>
		<td>
public static char charThat(<a href="http://site.mockito.org/mockito/docs/current/org/mockito/ArgumentMatcher.html">ArgumentMatcher</a> < Character> matcher)<br><br>

允许创建自定义的Character类型参数匹配模式(Character argument matchers)<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Parameters:</b><br>
&ensp &ensp matcher - 取决于选择的参数匹配模式(argument matches)<br><br>
<b>Returns:</b><br>
&ensp &ensp 0.<br><br>
		</td>
	</tr>

<!--第二十六行-->
	<tr>
		<td><b>contains</b></td>
	</tr>
	<tr>
		<td>
public static String contains(String substring)<br><br>

String参数包含给定的substring字符串.<br><br>

参照<a href="http://site.mockito.org/mockito/docs/current/org/mockito/Matchers.html">Matchers</a>类的javadoc帮助文档中的例子<br><br>

<b>Parameters:</b><br>
&ensp &ensp substring - substring字符串.<br><br>
<b>Returns:</b><br>
&ensp &ensp 空String ("").<br><br>
		</td>
	</tr>
</table>