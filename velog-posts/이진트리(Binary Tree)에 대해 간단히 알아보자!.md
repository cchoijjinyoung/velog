<h1 id="🔥span-stylecolororange이진트리binary-treespan">🔥<span style="color: Orange;">이진트리(Binary Tree)</span></h1>
<p>각 노드가 최대 두 개의 자식 노드를 가질 수 있는 트리 구조이다.</p>
<blockquote>
<p>두 개의 자식 노드를 각각 왼쪽 자식(노드), 오른쪽 자식(노드)이라고 한다.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/2a54dc2b-b42d-47ae-a17c-16f8d3b5f3b2/image.png" /></p>
<h3 id="1-이진-트리의-종류">1) 이진 트리의 종류</h3>
<h4 id="포화perfect--완전complete-이진-트리">포화(Perfect) / 완전(Complete) 이진 트리</h4>
<ul>
<li>포화 이진 트리 : 마지막 레벨포함 모든 노드가 꽉 차 있는 이진 트리</li>
<li>완전 이진 트리 : 마지막 레벨을 제외한 모든 노드가 꽉 차 있는 이진 트리</li>
</ul>
<h4 id="균형-이진-트리">균형 이진 트리</h4>
<ul>
<li>모든 노드의 좌/우 서브 트리의 높이가 1 이상 차이나지 않는 트리.
<img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/b91208ca-b1e4-48be-b82d-71b6e8705fd0/image.png" /></li>
</ul>
<hr />


<h4 id="이진-탐색-트리">이진 탐색 트리</h4>
<ul>
<li>왼쪽 서브트리에 있는 모든 노드의 값이 현재 노드의 값보다 작고,</li>
<li>오른쪽 서브 트리에 있는 모든 노드의 값이 현재 노드의 값보다 큰 특성을 가진 이진트리.</li>
</ul>
<h3 id="2-이진-트리의-특징">2) 이진 트리의 특징</h3>
<ul>
<li>포화 이진 트리의 높이가 'h'라면 노드의 수는 2^(h + 1) - 1개이다.</li>
<li>포화 or 완전 이진 트리의 노드가 'N'개 일 때, 높이는 log_2 N 이다.</li>
<li>이진 트리의 노드가 'N'개 일때, 최대 가능 높이는 N - 1이다.<blockquote>
<p>왼쪽으로만 뻗어있는 형태</p>
</blockquote>
</li>
</ul>
<h3 id="3-이진-트리의-순회traversal">3) 이진 트리의 순회(Traversal)</h3>
<ul>
<li><p>전위 순회(현재 → 왼쪽 → 오른쪽)
<img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/82c2c9c9-a139-4cdb-833e-309bdd09a2fd/image.png" /></p>
</li>
<li><p>후위 순회(왼쪽 → 오른쪽 → 현재)
<img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/0ead551a-f7a2-4b9a-87d7-e6394ba0ff58/image.png" /></p>
</li>
</ul>
<hr />

<p>최근 자바로 자료구조를 구현하기도 하고 매주있을 코딩테스트를 대비하느라 블로깅이 많이 밀렸다. 블로깅 할 때는 너무 재밌는데, 내가 필기한 내용을 노드에 끄적인 내용은 남들이 보기엔 무리가 있어서 이미지를 하나하나 수작업하려고 하면 너무 힘들다. 필기할 수 있는 태블릿이 너무나 간절해진다.😭</p>