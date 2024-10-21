<p>문제 링크
 : <a href="https://www.acmicpc.net/problem/26008">백준 - 26008 : 해시 해킹</a></p>
<pre><code class="language-java"> public class Main {

    public static void main(String[] args) throws IOException {

        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));
        StringTokenizer st = new StringTokenizer(br.readLine());

        long N = Long.parseLong(st.nextToken()); // 비밀번호 길이
        long M = Long.parseLong(st.nextToken()); // 사용 가능한 문자 갯수
        long A = Long.parseLong(st.nextToken()); // A
        int H = Integer.parseInt(br.readLine()); // 해시값 H
        long answer = 1;

        for(int i = 0; i &lt; n - 1; i++) {
            answer = (answer * m) % 1000000007;
        }
        bw.write(String.valueOf(answer));

        bw.flush();
        bw.close();
        br.close();
    }
}</code></pre>
<h3 id="span-stylecolororangespan-pa0--pa1--pa2--pa3--pa4----pan-1"><span style="color: orange;">#</span> PA^0 + PA^1 + PA^2 + PA^3 + PA^4 + ... + PA^(N-1)</h3>
<p>2진법, 10진법을 떠올릴수 있다. &rarr; 값이 중복될 수 없다.</p>
<p>다만, 이 문제에서는 P의 범위가 A보다 크거나 같을 수 있기 때문에 중복이 일어날 수 있다.</p>
<p>이런 중복이 몇 개나 있는 지를 물어보는게 이 문제다.</p>
<h3 id="span-stylecolororangespan-p_0--a0"><span style="color: orange;">#</span> P_0 * A^0</h3>
<p>P_0 * 1 과 같으므로 P_0이 나올 것. ( 0 &lt;= P_0 &lt;= M - 1 )</p>
<h3 id="span-stylecolororangespan-mod-m----m-"><span style="color: orange;">#</span> mod M ( = % M )</h3>
<p>mod M 을 함으로써 h(P)의 값인 정수 H의 범위는 ( 0 &lt;= H &lt;= M - 1 ) 이다.</p>
<p><strong>P_0을 제외한,
<span style="color: pink;">(PA^1 + PA^2 + PA^3 + PA^4 + ... + PA^(N-1)) </span> 를 <span style="color: skyblue;">F</span> 라고 한다면,</strong></p>
<p>&rarr; <strong>( P_0 + <span style="color: skyblue;">F</span> ) mod M  = H</strong></p>
<p>&rarr; 앞서 얘기했던 것처럼 P_0와 H의 범위를 생각해보면 <span style="color: skyblue;">F</span>의 값이 무엇이 오든,
P_0 과 H는 일대일 매칭이 된다.</p>
<h3 id="span-stylecolororangespan-결론"><span style="color: orange;">#</span> 결론</h3>
<p>*<em>우리는 <span style="color: skyblue;">F</span>의 경우의 수를 구하면 되기 때문에 M가지의 문자를 N-1번 골라야하기에 
M^(n-1)이 정답이다.
다만, 문제를 보면 답이 커질것을 우려하여 % 100000007을 하라고한다.  *</em></p>
<h2 id="⚡️-회고">⚡️ 회고</h2>
<p>나는 스스로는 문제를 풀지 못했다. 하루종일 생각하다가 다음 날 결국 구글에 도움을 청했다. 다른 사람들의 풀이를 보고나니 조금 허무하기도 했다. 하루를 날린 기분이었다. 그리고 나의 수학 능력에 실망했다. 문제 풀이를 보면서도 바로 이해하지 못했기때문이다. 공책에 예시를 여러 번 적어가며 겨우 이해하게 되었다. 이해한 끝에 내가 도출해낸 것은
<span style="color: orange;">P_0</span> = 해시의 key
<span style="color: skyblue;">F</span> = 해시함수
<span style="color: pink;">H</span> = 해시코드이고,
비밀번호가 중복되는 상황은 '해시충돌'이겠구나 라는 것이었다.
너무나 어려웠던 문제임은 틀림없지만, 실체를 알고나니 아쉬운 마음을 감출 수가 없다.</p>