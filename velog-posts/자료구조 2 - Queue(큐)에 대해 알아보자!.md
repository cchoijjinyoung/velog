<h1 id="🔥-queue-큐">🔥 Queue (큐)</h1>
<h2 id="1-queue큐란">1. Queue(큐)란?</h2>
<p><strong>'Queue'</strong> 는 컴퓨터 과학에서 사용되는 데이터 구조 중 하나이다.
<strong>'FIFO'(First-In-First-Out : 선입선출) 원칙</strong>에 따라 동작한다.</p>
<blockquote>
<p>선입선출 : 첫 번째로 들어간 항목이 첫 번째로 나오게 되는 것을 의미한다.</p>
</blockquote>
<p>'매표소에 줄을 서있는 사람들'을 생각하면 쉽다.</p>
<hr />

<h2 id="2-큐의-특징">2. 큐의 특징</h2>
<p>데이터가 <code>추가(Enqueue)</code>되는 위치는 <code>'줄의 가장 뒤(Back)'</code>라고 하고,
데이터가 <code>제거(Dequeue)</code>되는 위치는 <code>'줄의 가장 앞(Front)'</code>이라고 한다.</p>
<p>큐는 작업이 불가능할 때 ⚡️블로킹(Blocking)이 일어날 수 있다.
예를 들어, <code>비어있는 큐에 제거 연산</code>을 시도하거나 <code>가득 찬 큐에 삽입</code>을 시도하는 경우이다.</p>
<p>또한 큐는 <strong>너비 우선 탐색 알고리즘(BFS)</strong>에서 사용된다.</p>
<blockquote>
<p>BFS :</p>
</blockquote>
<ul>
<li>그래프나 트리와 같은 자료구조를 탐색하는 알고리즘 중 하나.</li>
<li>시작 노드에서 출발하여 인접한 모든 노드를 먼저 방문하는 방식으로 작동한다.
(* 최단 경로를 찾는 문제에 효과적으로 사용된다.)</li>
</ul>
<hr />

<h2 id="3-메서드">3. 메서드</h2>
<h3 id="ⓜ️-삽입-메서드">Ⓜ️ 삽입 메서드</h3>
<p> <code>offer(E e)</code>
: 큐가 꽉차있으면 'false'를 반환하고 예외는 발생하지 않는다.</p>
<p> <code>add(E e)</code>
: 큐가 꽉차있으면 'IllegalStateException' 을 던진다.</p>
<h3 id="ⓜ️-제거-메서드">Ⓜ️ 제거 메서드</h3>
<p><code>poll()</code>
: 큐의 맨 앞에 요소를 제거하고 반환한다. 큐가 비어있을 경우 'null'을 반환한다.</p>
<p><code>remove()</code>
: 큐의 맨 앞에 요소를 제거하고 반환한다. 큐가 비어있을 경우 'NoSuchElementException'을 던진다.</p>
<h3 id="ⓜ️-조회-메서드">Ⓜ️ 조회 메서드</h3>
<p><code>peek()</code>
: 큐의 맨 앞에 있는 요소를 반환하지만 제거는 하지 않는다. 큐가 비었을 경우 'null'을 반환한다.</p>
<p><code>element()</code>
: 큐의 맨 앞에 있는 요소를 반환하지만 제거는 하지 않는다. 큐가 비었을 경우 'NoSuchElementException'을 반환한다.</p>
<hr />


<h2 id="4-단방향-큐queue--양방향-큐deque">4. 단방향 큐(Queue) / 양방향 큐(Deque)</h2>
<p>큐는 <code>단방향 큐(Queue) / 양방향 큐(Deque)</code> 로 구분해볼 수 있다.</p>
<h3 id="단방향-큐queue의-특징">단방향 큐(Queue)의 특징</h3>
<ul>
<li><p>FIFO 방식을 따르므로 로직이 간단하고 이해하기 쉽다.</p>
</li>
<li><p>데이터가 도착한 순서대로 처리되므로, 요소가 언제 처리될지 예측하기 쉽다.</p>
</li>
<li><p>단, 단방향이기 때문에 유연성이 부족할 수 있다. 
ex) 큐의 끝에서 요소를 제거하거나, 검색하는 것은 비효율적이다.</p>
</li>
</ul>
<h3 id="양방향-큐deque의-특징">양방향 큐(Deque)의 특징</h3>
<ul>
<li><p><strong>유연성</strong> : 양쪽 끝에서 요소를 추가하거나 제거할 수 있으므로 단방향 큐보다 더 다양한 상황에 대응할 수 있다. 이로 인해 양방향 큐는 <code>스택 or 큐</code> 로도 사용될 수 있다.</p>
</li>
<li><p><strong>복잡성</strong> : 데크는 양방향성이므로 로직이 더 복잡하다. 버그가 발생할 가능성이 높으며, 코드를 이해하거나 유지하기가 더 어려울 수 있다.</p>
</li>
<li><p>양방향 큐의 대표적인 클래스로는 <code>LinkedList</code>클래스가 있다.</p>
</li>
</ul>
<blockquote>
<p>단방향 큐는 순차적인 데이터 처리가 필요하고 단순성이 중요한 경우,
양방향 큐는 복잡한 데이터 조작이 필요하거나 양방향으로 작업을 수행해야 하는 경우에 적합하다.</p>
</blockquote>
<p>사실 우리가 말하는 표준 큐(Queue)는 FIFO 원칙을 따르며, Front, Back으로 나뉘어져 있다.
그럼 Deque는 뭘까?</p>
<h3 id="-deque-란">+ Deque 란?</h3>
<p>: 큐의 일반적인 FIFO 원칙을 완전히 따르지는 않지만, 그 기능을 확장한 자료구조이다.</p>
<p>이런 특징때문에 상황에 따라 <code>스택 or 큐</code> 로 동작할 수 있다.</p>
<p>자바의 <code>java.util.Queue</code> 인터페이스는 큐의 기본적인 <strong>FIFO 동작</strong>을 정의하고,
<code>java.util.Deque</code> 인터페이스는 이를 확장하여 <strong>양방향 동작</strong>을 추가한다. 
따라서 Deque는 Queue를 확장한 형태라고 생각하면 된다.</p>
<p><code>양방향 큐(Deque) 인터페이스</code>를 구현한 클래스들은 <strong>양방향에서 삽입하고 제거하고 조회하는 새로운 메서드</strong> 들을 사용할 수 있게되는데 최대한 간단히 적어보겠다.</p>
<blockquote>
<p>메서드마다 양방향이다 보니 처음과 마지막을 뜻하는 <strong>First</strong>와 <strong>Last</strong>가 똑같이 붙는다.</p>
</blockquote>
<h3 id="ⓜ️-삽입-메서드-1">Ⓜ️ 삽입 메서드</h3>
<p><code>addFirst(E e)</code>
: 앞쪽에 요소를 삽입한다. 꽉찼을 경우 IllegalStateException을 던진다.</p>
<p><code>addLast(E e)</code>
: 뒤쪽에 요소를 삽입한다. 꽉찼을 경우 IllegalStateException을 던진다.</p>
<p><code>offerFirst(E e)</code>
: 앞쪽에 요소를 삽입한다. 꽉찼을 경우 false를 반환하고 예외는 발생하지 않는다.</p>
<p><code>offerLast(E e)</code>
: 앞쪽에 요소를 삽입한다. 꽉찼을 경우 false를 반환하고 예외는 발생하지 않는다.</p>
<h3 id="ⓜ️-제거-메서드-1">Ⓜ️ 제거 메서드</h3>
<p><code>removeFirst()</code>
: 앞쪽의 첫 번째 요소를 제거하고 반환한다. 비어 있을 경우 'NoSuchElementException'을 던진다.</p>
<p><code>removeLast()</code>
: 뒤쪽의 첫 번째 요소를 제거하고 반환한다. 비어 있을 경우 'NoSuchElementException'을 던진다.</p>
<p><code>pollFirst()</code>
: 앞쪽의 첫 번째 요소를 제거하고 반환한다. 비어 있을 경우 'null'을 반환한다.</p>
<p><code>pollLast()</code>
: 뒤쪽의 첫 번째 요소를 제거하고 반환한다. 비어 있을 경우 'null'을 반환한다.</p>
<h3 id="ⓜ️-조회-메서드-1">Ⓜ️ 조회 메서드</h3>
<p><code>getFirst()</code>
: 앞쪽에 있는 요소를 반환하지만 제거하지 않는다. 데크가 비어 있을 경우 NoSuchElementException을 던진다.</p>
<p><code>getLast()</code>
: 뒤쪽에 있는 요소를 반환하지만 제거하지 않는다. 데크가 비어 있을 경우 NoSuchElementException을 던진다.</p>
<p><code>peekFirst()</code>
: 앞쪽에 있는 요소를 반환하지만 제거하지 않는다. 데크가 비어 있을 경우 null을 반환한다.</p>
<p><code>peekLast()</code>
: 뒤쪽에 있는 요소를 반환하지만 제거하지 않는다. 데크가 비어 있을 경우 null을 반환한다.</p>
<hr />

<h2 id="🎁-사용-예시">🎁 사용 예시</h2>
<p>아래는 <code>LinkedList</code> 클래스를 사용하여 큐의 기본 메서드들과 양방향 큐에서 사용할 수 있는 메서드들을 사용해보았다.</p>
<blockquote>
<p><code>LinkedList</code> 클래스는 <strong>'List'</strong> 인터페이스와 <strong>'Deque'</strong> 인터페이스를 모두 구현했고, 
<strong>'Deque'</strong> 인터페이스는 <strong>'Queue'</strong> 인터페이스를 확장했기 때문에,
<code>LinKedList</code> 클래스는 간접적으로 <strong>'Queue'</strong> 를 구현하게 된다!)</p>
</blockquote>
<p>여기서는 잠깐 큐가 가득찬 상황을 만들기 위해 LinkedList대신 다른 클래스를 사용했다.</p>
<pre><code class="language-java">    // offer(), add()메서드의 가득찬 상황을 테스트하기 위해 해당 클래스를 사용했다.
    // LinkedList는 크기 제한이 없는 클래스이기 때문에 테스트에 적합하지 않았다.

        // offer() 메서드
        ArrayBlockingQueue&lt;Integer&gt; offerTest = new ArrayBlockingQueue&lt;&gt;(1);
        System.out.println(offerTest.offer(1)); // true
        System.out.println(offerTest.offer(1)); // false

        // add() 메서드
        ArrayBlockingQueue&lt;Integer&gt; addTest = new ArrayBlockingQueue&lt;&gt;(1);
        System.out.println(addTest.add(1)); // true

        try {
            System.out.println(addTest.add(1)); // Queue full!!
        } catch (IllegalStateException e) {
            e.printStackTrace();![]
        }
</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/8e71040a-39b6-45a9-a95d-45a4117c3082/image.png" /></p>
<p>다음은 기본 메서드들이다. <code>add(), poll(), peek()</code></p>
<pre><code class="language-java">LinkedList&lt;Integer&gt; deque = new LinkedList&lt;&gt;();
        deque.add(1);
        deque.add(2);
        deque.add(3);
        deque.add(4);
        deque.add(5); // 1, 2, 3, 4, 5

        System.out.println(&quot;poll(제거) : &quot; + deque.poll()); // 1을 출력하고 맨 앞 제거

        deque.peek();
        deque.peek(); // 몇 번을 해도 똑같다.
        System.out.println(&quot;peek(조회) : &quot; + deque.peek()); // 2를 출력하고 요소 제거 X

        for (int item : deque) {
            System.out.print(item + &quot; &quot;); 
        }
// deque 출력 : 2 3 4 5</code></pre>
<p>다음은 양방향 큐(Deque)의 메서드들이다. <code>offerFirst(), pollLast(), peekLast()</code></p>
<pre><code class="language-java">// 현재 deque : 2 3 4 5

        // 양방향 메서드를 사용해보겠다. addLast()는 사실 그냥 add와 같다.
        deque.addFirst((6)); // 6 2 3 4 5
        deque.offerFirst(7); // 7 6 2 3 4 5

        deque.removeLast(); // 맨 뒤 5를 제거 후 반환 -- 7 6 2 3 4
        deque.pollLast(); // 맨 뒤 4를 제거 후 반환 -- 7 6 2 3

        deque.getLast(); // 3
        deque.peekLast(); // 3

        // 양방향 큐(Deque)의 메서드를 같이 사용하면
        // 마치 새치기를 하는 듯한 모양을 볼 수 있다.
        // 7 6 2  '3'
        deque.offerFirst(deque.pollLast()); // 맨 뒤 3을 제거 후 맨 앞으로 추가
        for (int item : deque) {
            System.out.print(item + &quot; &quot;); // '3' 7 6 2 (웅성웅성)(수근수근)
        }
</code></pre>
<p>맨 아래 '새치기(?)' 처럼 Deque의 확장된 기능은 Queue로만은 구현하기 힘든 복잡한 로직을 쉽게 구현할 수 있다.</p>
<p>아래는 연결리스트에 대해 적어둔 내용이다. 도움이 되었으면 좋겠다.
<a href="https://velog.io/@cchoijjinyoung/%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0-5-%EC%97%B0%EA%B2%B0-%EB%A6%AC%EC%8A%A4%ED%8A%B8Linked-List%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90">[링크] 자료구조 4 - LinkedList에 대해 알아보자!</a></p>