<h1 id="🔥-span-stylecolororangelinkedlist-연결-리스트span">🔥 <span style="color: Orange;">LinkedList (연결 리스트)</span></h1>
<h2 id="연결-리스트linkedlist란">연결 리스트(LinkedList)란?</h2>
<p>노드라고 불리는 항목들이 연결로써 서로 연결되어 있는 구조이다.
<img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/bb8bf231-276a-4b4b-b77d-38c12a59d244/image.png" /></p>
<h3 id="span-stylecolorredspan-노드-node"><span style="color: red;">*</span> 노드 (Node)</h3>
<p>데이터 저장 단위로, 두 부분으로 나뉜다.</p>
<ul>
<li>값( data : 데이터를 저장하는 부분 )</li>
<li>포인터( next : 다음 노드의 래퍼런스를 저장한 부분 )로 구성</li>
</ul>
<h2 id="✔️-특징">✔️ 특징</h2>
<h3 id="1-동적크기">1. 동적크기</h3>
<p>연결 리스트는 미리 고정된 크기를 가지지 않는다. 항목을 추가하거나 제거함에 따라 크기가 동적으로 변화한다.</p>
<h3 id="2-시퀀셜-접근">2. 시퀀셜 접근</h3>
<p>배열과 달리 연결 리스트에서는 특정 인덱스에 직접 접근하는 것이 불가.
원하는 위치의 항목에 접근하기 위해서는 처음부터(또는 끝)에서 순차적으로 탐색해야한다.</p>
<h3 id="3-삽입-및-삭제의-효율성">3. 삽입 및 삭제의 효율성</h3>
<p>연결리스트는 배열과 달리 항목을 이동시킬 필요가 없다.</p>
<hr />

<h3 id="4-데이터-추가">4. 데이터 추가</h3>
<p>: 데이터 추가 위치 (head, 중간, tail) 에 따른 연결 작업 필요.</p>
<h4 id="➕-head에-추가-시">➕ head에 추가 시</h4>
<ol>
<li>추가할 데이터를 담을 노드 생성</li>
<li>링크 연결 작업</li>
<li>head 이전 작업</li>
</ol>
<h4 id="➕-tail에-추가-시">➕ tail에 추가 시</h4>
<ol>
<li>추가할 데이터를 담을 노드 생성</li>
<li>head로부터 시작해서 끝 노드까지 순회</li>
<li>링크 연결 작업</li>
</ol>
<h4 id="➕-중간에-추가-시">➕ 중간에 추가 시</h4>
<ol>
<li>추가할 데이터를 담을 노드 생성</li>
<li>head로부터 시작해서 데이터를 담을 위치 직전 노드까지 순회</li>
<li>링크 연결 작업
여기서 주의해야 할 점은 링크정보가 유실되지 않도록 해야한다.
추가 할 노드 : <code>newNode</code>
추가 할 위치에 있던 노드 : <code>oldNode</code>
<code>oldNode</code>를 가리키던 <code>oldNode - 1</code>의 포인터가 <code>newNode</code>를 가리키게 변경 후, <code>newNode</code>의 포인터는 oldNode를 가리키게 해야한다.</li>
</ol>
<p><img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/b15c26ba-ce99-4483-9467-1ad357614ff2/image.png" />
<span style="color: red;">⚠️</span> <strong>노드들의 래퍼런스가 바뀌는 것에 주목해야한다!</strong><img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/34b83fb4-f58d-49c3-bb98-0a3bb2dc5da3/image.png" /></p>
<hr />


<h3 id="5-데이터-삭제">5. 데이터 삭제</h3>
<p>: 데이터 추가와 마찬가지로 삭제 위치에 따른 연결 작업이 필요.</p>
<h4 id="➖-head-삭제-시">➖ head 삭제 시</h4>
<ol>
<li>삭제 대상 노드 지정 (delete_node)</li>
<li>head 이전 작업 : 맨 앞 노드(지워진 노드)의 next가 head가 되어야한다.</li>
<li>delete_node 삭제
자바(Java) 에서는 가비지컬렉터(Garbage Collector)가 해당 메모리를 래퍼런스하고 있는 애가 없으면 자동으로 지워준다.</li>
</ol>
<h4 id="➖-tail-삭제-시">➖ tail 삭제 시</h4>
<ol>
<li>head로부터 시작해서 끝 노드까지 순회</li>
<li>끝 노드 삭제</li>
<li>삭제노드의 previous (전)노드의 next를 null 처리</li>
</ol>
<h4 id="➖-중간-삭제-시">➖ 중간 삭제 시</h4>
<ol>
<li>head로부터 삭제 대상 노드까지 순회 및 해당 노드 지정(delete_node)</li>
<li>삭제 대상 이전/이후 노드의 링크 연결 작업</li>
<li>delete_node 삭제
<img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/b19f9f86-9c57-4ce0-b090-7f1f7ee2757c/image.png" /></li>
</ol>
<hr />

<h3 id="6-자바에서의-linkedlist">6. 자바에서의 LinkedList</h3>
<p>자바의 'LinkedList'는 java.util 패키지에 있는 클래스로, 이중 연결 리스트로 구현되어 있다. 그렇기 때문에 각 노드는 데이터와 두 개의 참조를 가지고 있다. (이전, 다음노드)
LinkedList는 List와 Deque 인터페이스를 모두 구현하기 때문에 리스트 기능과 덱 기능을 모두 제공한다.
<a href="https://velog.io/@cchoijjinyoung/%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0-2-Queue%ED%81%90%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90">[링크] 자료구조 2 - Queue(큐)에 대해 알아보자!</a></p>
<p>아래는 간단한 메서드들을 작성해보았다! 위에 링크도 도움이 되었으면 좋겠다.</p>
<pre><code class="language-java">LinkedList&lt;String&gt; list = new LinkedList&lt;&gt;();
list.add(&quot;A&quot;);      // 리스트의 끝에 추가
list.addFirst(&quot;B&quot;); // 리스트의 시작에 추가
list.addLast(&quot;C&quot;);  // 리스트의 끝에 추가

list.removeFirst(); // 첫 번째 항목 삭제
list.removeLast();  // 마지막 항목 삭제
list.remove(&quot;A&quot;);   // &quot;A&quot; 삭제

String first = list.getFirst(); // 첫 번째 항목 가져오기
String last = list.getLast();   // 마지막 항목 가져오기

int size = list.size();             // 크기 가져오기
boolean contains = list.contains(&quot;A&quot;); // 특정 요소가 있는지 확인
</code></pre>
<p>.</p>
<hr />


<h3 id="🤔-그렇다면-노드에-직접-접근해서-next나-previous를-수정할-수-있을까">🤔 그렇다면 노드에 직접 접근해서 next()나 previous()를 수정할 수 있을까?</h3>
<blockquote>
<p>'LinkedList'의 내부적인 노드 구조는 'private'으로 선언되어 있어서 외부에서 직접 접근은 불가능하다!</p>
</blockquote>