<h1 id="🔥-stack-스택">🔥 Stack (스택)</h1>
<h2 id="1-stack스택-이란">1. Stack(스택) 이란?</h2>
<p><strong>&quot;쌓다&quot;</strong> 라는 뜻을 가진다.<br />스택의 가장 중요한 특징은 <strong>LIFO(후입선출)</strong> 규칙을 따른다는 것이다.<br />상자에 물건을 차곡차곡 쌓아 올린 형태를 생각하면 쉽다.  </p>
<h2 id="2-스택의-특징">2. 스택의 특징</h2>
<p>스택은 <strong>'top'</strong> 이라는 것을 가진다.<br />상자에 물건을 넣을 때 바닥은 닫혀있고 열려있는 위로 물건을 넣고 빼듯이 Stack도 동일하다.<br />이 때,<br />top을 통해 데이터를 '삽입'하는 매서드를 <code>'push(data)'</code>,  
top을 통해 데이터를 '제거'하는 메서드를 <code>'pop()'</code>이라고 한다.  </p>
<h2 id="3-메서드">3. 메서드</h2>
<h3 id="ⓜ️-pushe-item">Ⓜ️ <code>push(E item)</code></h3>
<ul>
<li>스택의 맨 위에 요소를 추가한다. = 다음에 pop되는 요소가 된다.</li>
<li>매개변수가 필요하다</li>
<li>리턴 값으로 'push' 된 요소 자체를 반환한다.</li>
<li>시간복잡도 : O(1)</li>
</ul>
<pre><code class="language-java">// 제네릭을 사용할 수 있다.
Stack&lt;String&gt; stack = new Stack();

String data1 = &quot;데이터1&quot;;
stack.push(data1);

String data2 = &quot;데이터2&quot;;
stack.push(data2);

// push 된 요소 자체를 반환한다.
String data3 = &quot;데이터3&quot;;
System.out.println(stack.push(data3)); // &quot;데이터3&quot;</code></pre>
<h3 id="ⓜ️-pop">Ⓜ️ <code>pop()</code></h3>
<ul>
<li>스택의 맨 위에 요소를 제거하고 그 요소를 반환한다.</li>
<li>스택이 비어있는 상태에서 호출 시 Exception을 던진다. = 'EmptyStackException'</li>
<li>시간복잡도 : O(1)<blockquote>
<p>push(삽입), pop(제거) 메서드의 시간복잡도가 O(1)로 동일한 이유는 'top'(맨 위) 데이터를 삽입하고 제거하기 때문이다!  </p>
</blockquote>
</li>
</ul>
<pre><code class="language-java">// 위에 로직과 이어짐
// stack : [데이터1, 데이터2, 데이터3] (top)
stack.pop(); // 데이터3 - stack : [데이터1, 데이터2]
stack.pop(); // 데이터2

stack.peek(); // 데이터1 - 반환만할 뿐, 제거하지는 않는다.

stack.pop(); // 데이터1
// stack.size() == 0
stack.empty() // true
stack.pop(); // EmptyStackException
// or stack.peek(); // 동일하게 예외를 던진다.</code></pre>
<h3 id="ⓜ️-peek">Ⓜ️ <code>peek()</code></h3>
<ul>
<li>스택의 맨 위의 요소를 반환하지만 제거하지는 않는다.</li>
<li>스택이 비어있는 상황에서 호출 시 마찬가지로 'EmptyStackException'을 던진다.</li>
</ul>
<h3 id="ⓜ️-empty">Ⓜ️ <code>empty()</code></h3>
<ul>
<li>스택이 비어있으면 'true'를 반환, 그렇지 않으면 'false'를 반환한다.</li>
</ul>
<h3 id="ⓜ️-searchobject-o">Ⓜ️ <code>search(Object O)</code></h3>
<ul>
<li>스택에서 주어진 객체를 찾고, 객체까지의 거리를 반환한다.</li>
<li>top 에서부터 아래로 내려가며 찾는다.</li>
<li>찾지 못하면 -1 을 반환한다.</li>
</ul>
<pre><code class="language-java">Stack&lt;Integer&gt; stack = new Stack&lt;&gt;();
// 1 을 찾고 싶다.
stack.push(1); // 찾고자하는 Object
stack.push(2);
stack.push(3);
stack.push(4);
stack.push(1); // 값이 동일한 Object
System.out.println(stack.search(1)); // 1 (거리 반환)
// 가장 가까운 Object를 출력하는 것을 알 수 있다.

stack.pop(); // 맨 위 1 삭제
System.out.println(stack.search(1)); // 4</code></pre>