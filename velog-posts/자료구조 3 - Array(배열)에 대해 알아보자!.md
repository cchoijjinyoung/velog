<h1 id="🔥-span-stylecolororangearray-배열span">🔥 <span style="color: orange;">Array (배열)</span></h1>
<h2 id="1-배열array이란">1. 배열(Array)이란?</h2>
<p><strong>'Array'</strong> 는 컴퓨터 과학에서 사용되는 데이터 구조 중 하나이다.
동일한 데이터 유형의 항목들이 연속적인 메모리 위치에 저장되는 구조.
배열의 각 항목을 고유한 인덱스(Index)로 참조한다.</p>
<hr />

<h2 id="2-특징">2. 특징</h2>
<ul>
<li><p>고정크기 : 생성 시 크기가 고정된다 &rarr; 동적으로 변경될 수 없다는 단점이 생긴다.</p>
</li>
<li><p>인덱스(Index)기반 : 각 항목을 고유한 인덱스로 참조한다. &rarr; 인덱스는 대게 0부터 시작한다.</p>
</li>
<li><p>직접 접근(Access) : 인덱스를 사용하여 배열의 요소에 빠르게 접근 가능</p>
</li>
<li><p>삽입 및 삭제 비효율성 : 배열의 중간에 요소 <code>삽입/삭제</code> 시 다른 요소들을 이동시켜야하므로 비효율적이다.</p>
</li>
<li><p>캐시 효율성 : <code>연속적인 메모리 할당</code>으로 인해, 배열은 종종 <code>캐시 효율적</code>이다.</p>
</li>
</ul>
<blockquote>
<p>연속적인 메모리 할당 : 컴퓨터의 메모리는 주소로 되어있다 
예를 들어 메모리의 한 위치에는 주소'<strong>0x1000</strong>' 가 있고 다음 위치에는  주소<strong>'0x1001'</strong> 가 있을 수 있다.</p>
</blockquote>
<p><span style="color: red;">*</span> 더 예를 들면 'int'타입의 배열에서 첫 번째 요소가 주소 <code>'0x1000'</code>에 저장된다면, 다음 요소는 <code>'0x1004'</code> 에 저장될 것이다. (왜냐하면 int는 대게 4바이트이기 때문이다.)</p>
<p>배열의 <code>이러한 연속성은 메모리 액세스를 최적화</code>하는데 도움을 준다. 그 이유는 <code>컴퓨터의 메모리 접근 방식</code>에 있는데, <code>메모리에서 데이터를 읽을 때 주변 데이터도 함께 읽혀질 가능성</code>이 높기 때문이다.</p>
<p>위에서 언급한 <code>연속적인 메모리 할당</code>의 특성 때문에 배열의 한 요소에 접근할 때 주변 요소들도 캐시에 로드될 가능성이 있다. 따라서 배열에서 연속적으로 데이터를 액세스하게되면 캐시 효율성이 크게 향상된다.</p>
<hr />

<h2 id="3-시간복잡도">3. 시간복잡도</h2>
<ul>
<li>접근 : O(1) &rarr; 인덱스로 접근을 하기 때문에 바로 접근 가능하다.</li>
<li>검색 : O(N) &rarr; 해당 요소의 인덱스를 모르면 배열의 인덱스를 모두 찾아봐야한다.</li>
<li>삽입 : O(N) (최악의 상황일 경우 : 배열의 중간에 삽입)</li>
<li>삭제 : O(N) (최악의 상황일 경우 : 배열의 중간 요소 삭제)</li>
</ul>
<hr />

<h2 id="4-자바에서의-배열">4. 자바에서의 배열</h2>
<p>기본 데이터 유형 (int, char, double, ...) 및 객체를 위한 배열을 선언하고 사용할 수 있다.</p>
<pre><code class="language-java">// ex) 
// 배열 선언하기
int[] arr; 
// int arr[]; 이와 같이 변수명에 대괄호를 붙여줌으로써 선언할 수도 있지만 별로 선호되진 않는다.
arr = {1, 2, 3};

// 배열 선언과 동시에 값을 할당하기
int[] arr2 = {1, 2, 3, 4, 5};

// 배열 초기화 및 선언하기
int[] arr3 = new int[5]; 

int[][] matrix; // 2차원 배열
matrix = new int[3][3]; // 3x3 크기의 2차원 배열

int[][] predefinedMatrix = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};
</code></pre>
<p>자바의 기본 배열(array)은 기본적으로 메서드를 많이 제공하지 않는다.
그러나, 길이를 알기 위해 사용할 수 있는 <code>length</code> 와 배열과 관련된 몇 가지 유틸리티 메서드가 <code>java.util.Arrays</code> 클래스에 있다.</p>
<pre><code class="language-java">// length : 배열의 길이를 리턴한다.
int[] arr = {1, 2, 3, 4, 5};
int arrLength = arr.length; // 5


// toString(array) : 배열의 문자열 표현을 리턴한다.
int[] arr2 = {1, 2, 3};
System.out.println(Arrays.toString(arr2); // [1, 2, 3]


// sort(array) : 배열의 요소를 오름차순으로 정렬한다.
int[] arr3 = {2, 1, 4 ,5 ,3};
Arrays.sort(arr3);
System.out.println(Arrays.toString(arr3); // [1, 2, 3, 4, 5]</code></pre>
<hr />

<h2 id="요약하자면">요약하자면,</h2>
<p><span style="color: red;">*</span>배열은 간단한 자료구조이며, 인덱스를 통해 빠른 데이터 접근을 제공한다.
그러나, 크기 변경의 제한이 존재하고, 배열 중간에 요소를 삽입한다거나, 중간 요소를 제거하는 것은 비효율적이다.</p>
<h2 id="🏀-practice--배열-arr-의-요소를-오름차순으로-출력하기">🏀 Practice : 배열 arr 의 요소를 오름차순으로 출력하기</h2>
<h3 id="방법-1-하나씩-비교-후-교체">방법 1) 하나씩 비교 후 교체</h3>
<pre><code class="language-java">// 입출력 예시)
// arr: 5, 3, 1, 4, 6, 1
// 결과: 1, 1, 3, 4, 5, 6

import java.util.Arrays;

public class Practice5 {
    public static void main(String[] args) {
        int[] arr = {5, 3, 1, 4, 6, 1};
        for (int i = 0; i &lt; arr.length - 1; i++) {
            for (int j = i + 1; j &lt; arr.length; j++) {
                if (arr[i] &gt; arr[j]) {
                    int tmp = arr[i];
                    arr[i] = arr[j];
                    arr[j] = tmp;
                }
            }
        }
        System.out.println(Arrays.toString(arr));

    }
}</code></pre>
<p>찾아보니까 내가 작성한 코드가 <strong>'선택 정렬(Selection Sort)알고리즘'</strong>에 해당한다고 한다.</p>
<blockquote>
<p>선택정렬(Selection Sort) : </p>
</blockquote>
<ul>
<li>시간복잡도 : O(n^2) : 배열의 길이가 n일 때, 외부 루프는 n번, 내부 루프는 최대 n번 실행될 수 있기 때문이다.</li>
<li>공간복잡도 : O(1) : 주어진 배열 내에서 정렬을 수행하기 때문에 추가적인 메모리를 거의 사용하지 않는다.</li>
</ul>
<p>선택정렬은 작은 데이터셋에 적합하다. 큰 데이터셋에는 <code>'퀵 정렬'</code>, <code>'병합 정렬'</code> 등이 있다.
.
.
.</p>
<h3 id="방법-2-arrlength만큼-반복해서-그때마다-최솟값을-찾아낸다">방법 2) arr.length만큼 반복해서 그때마다 최솟값을 찾아낸다.</h3>
<pre><code class="language-java">// 입출력 예시)
// arr: 5, 3, 1, 4, 6, 1
// 결과: 1, 1, 3, 4, 5, 6

import java.util.Arrays;

public class Practice5 {
    public static void main(String[] args) {

    // 방법 2) 반복문을 arr.length만큼 돌려서 그때마다 최솟값을 출력
        // arr : 5, 3, 1, 4, 6, 1
        // 첫째 : 반복문으로 6개의 요소중 가장 작은 값을 가진 인덱스를 찾는다.
        // 둘째 : 최솟값의 인덱스를 찾았으면 해당 인덱스는 다음부터 조건에 걸리지 않도록한다. 
        // ---&gt; : 여기서는 visited 배열을 이용해서 arr각 요소들의 인덱스의 방문여부를 확인.
        // ---&gt; : 이미 최솟값으로 지정됐던 인덱스면 0 -&gt; 1로 바꿔주는 방식을 사용했다.
        // 셋째 : 결국 처음 최솟값으로는 arr[2]의 값인 1이 될 것이고, visited[2]는 1이 될 것.
        // 넷째 : visited[i]가 0인 인덱스에서만 최솟값을 찾기 때문에 5개의 인덱스만 비교
        // ---&gt; : 4개 비교 -&gt; 3개 -&gt; 2개 ... 
        // 마지막으로, visitCnt가 arr.length 와 같게되면(visited[]이 모두 1이면,) 프로그램은 종료된다.
        int[] visited = new int[arr.length];
        int visitCnt = 0;
        int minVal = Integer.MAX_VALUE;
        int minIdx = -1;

        while (visitCnt &lt; arr.length) {
            for (int i = 0; i &lt; arr.length; i++) {
                if (arr[i] &lt; minVal &amp;&amp; visited[i] == 0) {
                    minVal = arr[i];
                    minIdx = i;
                }
            }
            if (minIdx != -1) {
                System.out.print(minVal + &quot; &quot;);
                visited[minIdx] = 1;
                visitCnt++;
            }
            minVal = Integer.MAX_VALUE;
            minIdx = -1;
        }
        System.out.println();

    }
}
</code></pre>
<hr />

<h2 id="회고">회고</h2>
<p>하나를 배우면 자꾸 무언가 고개를 내민다. 오늘도 Array에 대해 공부했더니 컴퓨터의 메모리 접근 방식에 대해 간단히 알게되고, 정렬에는 여러 종류가 있다는 것, 또한 시간복잡도말고도 공간복잡도도 있다라는 것을 알았다.</p>
<p>++ 그리고 마크다운도 점점 익숙해져가는 것 같다. 글씨에 색 넣는 법이랑 화살표 적는 법을 처음 알았다. 제목을 검정배경에 노란색으로 하니까 잘보이고 이쁜 것 같다.
요즘에 이런 고민도 한다. 나에게는 블로깅이 그 날 노트에 공부한 것을 다시 적는 과정이기때문에 아무래도 나는 내 글이 이해가 잘 갈 수 밖에 없는데, 다른 사람이 보기에도 이해가 잘 되는 글일까 싶다. 최대한 깔끔하고 이해하기 쉽게 정리하려고 하는데 좋게 보일 지 모르겠다. 하다보면 좋아지겠지.</p>