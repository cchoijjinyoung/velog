<p><a href="https://velog.io/@cchoijjinyoung/%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0-2-Queue%ED%81%90%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90">(정리) 자료구조 - Queue에 대해 알아보자! + Deque</a></p>
<p><img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/8b006d24-88e6-4aef-915a-41052d1f4dbb/image.png" />
. . . 
문제를 풀면서 느낀점은</p>
<p>내가 문제를 잘 이해하지 못한다는 점이였다. 문제 읽느라 시간다썼다.</p>
<p>일단 문제에 <code>양방향 순환 큐</code>라고 언급되어 있어서 <code>LinkedList</code>클래스를 사용해야겠다고 생각할 수 있었다.</p>
<pre><code class="language-java">import java.util.ArrayDeque;
import java.util.LinkedList;
import java.util.Queue;
import java.util.Scanner;

class Main {
    public static void main(String[] args) {
        int result = 0;
        Scanner sc = new Scanner(System.in);
        LinkedList&lt;Integer&gt; queue = new LinkedList&lt;&gt;();
        int N = sc.nextInt();
        for (int i = 1; i &lt;= N; i++) {
            queue.add(i);
        } // {1, 2, 3, 4, 5, .., N}
        // 이 문제에서는 뽑아내고 싶은 숫자의 값은 중요하지 않았다.
        // 이 상태에서 '몇번째'숫자를 언제, 어떻게 빼내고 싶은 지가 중요했다.

        int M = sc.nextInt();

        int[] targets = new int[M];
        for (int i = 0; i &lt; M; i++) {
            targets[i] = sc.nextInt(); 
            // 뽑아내고 싶은 수의 위치(몇번째인지)를 담은 배열
            // 인덱스는 해당하는 수를 몇번째로 뽑을건지를 나타낸다.
            // [2, 9 ,5]
            // - 2번째 있는 수를 먼저 뽑아내겠다.
            // - 그 다음은 9번째 수 -&gt; 5번째 수
        }

        for (int i = 0; i &lt; targets.length; i++) {
            int target = queue.indexOf(targets[i]); // : 뽑고 싶은 수의 인덱스 ex) 2의 인덱스
            int move = 0;                             // : 이동한 칸의 수
            int leftRange = target - 0;             // : 타겟 기준 왼쪽으로 출구까지의 거리
            int rightRange = queue.size() - target; // : 타겟 기준 오른쪽으로 출구까지의 거리
            if (leftRange &lt; rightRange) {             // 왼쪽이 가까우면
                move = leftRange;                       // 왼쪽으로 이동, 움직한 만큼
                for (int j = 0; j &lt; move; j++) {    // 왼쪽으로 회전함
                    queue.addLast(queue.pollFirst()); 
                }                                    //
                queue.pollFirst();                     // 뽑기 !
            } else {
                move = rightRange;
                for (int j = 0; j &lt; move; j++) {    // 만약 오른쪽으로 움직이면
                    queue.addFirst(queue.pollLast()); // 오른쪽으로 회전함
                }
                queue.pollFirst();                    // 뽑기!
            }
            result += move;                            // 움직인만큼 결과로 출력
        }
        System.out.println(result);
    }
}

</code></pre>
<h3 id="나의-문제점--언어적인-능력">나의 문제점 : 언어적인 능력</h3>
<p>문제를 풀 때마다 느끼는 게. 나는 문제를 이해하는 과정부터 많이 느리고, 문제를 이해하고 푼다고 해도 상당히 복잡하게 생각하는 것 같다. 공책에 순차적으로 '여기서는 이렇게~ 저렇게~ 풀겠다.' 라는 생각을 단순화하여 적어가다보면 어느새 '처음 컨셉이 뭐였지?', '내가 앞에 어떻게 적었었지?' 하고 멈칫하게 되는 것 같다. 그러다보면 머리가 하얘지기 시작하고 다시 처음부터 새로히 과정을 적게된다. 지금와서 생각해보면 <strong>언어적인 능력</strong>이라고 해야될까. <code>'내가 생각한 것을 말로 풀어내는 능력'</code> 같은 점이 부족한 것 같다.</p>
<p>그래서 애초에 풀이과정을 노트에 적을 때 내 생각을 과하다 싶을 정도로 <strong>꼼꼼하게</strong> 작성하기로 했다. 그 후에는 적어둔 말을 실행시키려면 코드로는 어떻게 작성해야할까, 뭐가 필요할까를 시작으로 작성했다. 그러다보니 어떤 데이터타입을 사용할 것이고, 어떤 변수를 사용할 지도 전보다 명확하게 생각났다.</p>
<p>++ 아직 어떤 반복문(for문, 향상된 for문, while문)을 어떻게 사용할 것이며, 그 안에 조건문은 어떻게 작성할 지도 빠르게 판단이 안된다. 비슷한 조건의 반복문이 너무 많이 나오는 경우도 생기고, 조건이 겹치는 경우도 생긴다. 문제를 많이 풀어보며 고쳐나가야겠다.</p>