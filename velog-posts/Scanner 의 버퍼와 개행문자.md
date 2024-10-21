<h1 id="🔥-scanner">🔥 Scanner</h1>
<p><strong>오늘은 간단한 문제를 통해 자바를 복습하였다.</strong></p>
<p><strong>자바의 <code>Scanner</code> 클래스를 사용하여 사용자가 입력한 값에 따라 입장권의 가격을 다르게 출력해야하는 문제였다.</strong></p>
<p>아래는 Scanner에 대해 쉽게 작성하기 위해 다른 예제로 가져왔다.</p>
<pre><code class="language-java">import java.util.Scanner; // Scanner 클래스를 import

public class Main {
    public static void main(String[] args) {

        Scanner scanner = new Scanner(System.in); // Scanner 인스턴스 생성

        System.out.println(&quot;이름을 입력하세요: &quot;); 
        String name = scanner.nextLine(); // 한 줄을 입력 받음

        System.out.println(&quot;나이를 입력하세요: &quot;);
        int age = scanner.nextInt(); // 정수를 입력 받음

        System.out.println(&quot;당신의 이름은 &quot; + name + &quot;이고, 나이는 &quot; + age + &quot; 세입니다.&quot;);
    }
}</code></pre>
<p><strong>위의 예제에서는 Scanner 클래스의 nextLine(), nextInt() 매소드만 사용하였다.</strong></p>
<p><strong>내가 원하는 대로 아무 문제 없이 잘 작동한다.</strong></p>
<p><span style="color: red;">*</span> <strong>하지만 문제는 nextInt() 혹은 next() 매소드 다음에 nextLine() 매소드가 오면 조금 주의가 필요했다.</strong></p>
<hr />
<p>주의할 점은 <code>nextInt()</code>나 <code>next()</code>와 같은 메서드를 이용해 입력받은 뒤 <code>nextLine()</code>을 이용해 문자열을 입력받으려 할 때 발생할 수 있는 문제인데,
<code>nextInt()</code>나 <code>next()</code> 메서드 같은 경우는 입력 값에서 줄바꿈 문자는 그대로 두기 때문에,</p>
<p><u><strong>Scanner의 버퍼(Buffer)에는 줄바꿈문자(개행문자)가 남아있게 된다.</strong></u></p>
<p>결국 이 후에 <code>nextLine()</code> 메서드를 사용하게 되면 버퍼에서 사라지지 않고 남아있던 줄바꿈문자가 입력을 종료해버리는 문제가 생긴다.</p>
<blockquote>
<p>nextInt(), next() 매소드와 nextLine() 매소드의 차이점은 개행문자(\r, \n, \r\n)의 무시여부이다!</p>
</blockquote>
<p><strong>예를 들면 이렇다.</strong></p>
<pre><code class="language-java">int age = sc.nextInt(); // 20 입력 후 (엔터) - 버퍼 : [20, 엔터,  ,  ,  ]
                        // age = 20         - 버퍼 : [엔터,  ,  ,  ,  ]   
String name = sc.next(); // &quot;김이박&quot; 입력 후 (엔터)
// 현재 버퍼 : (엔터)
// next() 매소드는 개행문자(공백, 엔터)를 무시한다.
// 이번에도 똑같이 엔터를 치므로 버퍼에는 (엔터)가 남이있게된다.

String birthDay = sc.nextLine();
// 하지만 다음으로 오는 nextLine() 매소드는 개행문자를 무시하지 않는다.
// 버퍼에 남아있던 엔터때문에 사용자는 입력을 못하게 된다.
</code></pre>
<h2 id="⚡️-해결방법">⚡️ 해결방법</h2>
<h3 id="1-nextline-매소드를-사용하지-않고-작성">1. nextLine() 매소드를 사용하지 않고 작성.</h3>
<h3 id="2-nextline-매소드를-사용한다면">2. nextLine() 매소드를 사용한다면</h3>
<h4 id="2---1-위에서-개행문자가-버퍼에-남아있다면-nextline로-버퍼에-있는-개행문자를-제거하고-nextline을-사용한다">2 - 1. 위에서 개행문자가 버퍼에 남아있다면 nextLine()로 버퍼에 있는 개행문자를 제거하고 nextLine()을 사용한다.</h4>
<h4 id="2---2-nextint-와-같은-매소드들은-integerparseintscnextline-으로-대체한다">2 - 2. nextInt() 와 같은 매소드들은 Integer.parseInt(sc.nextLine()) 으로 대체한다.</h4>
<p>아래 코드는 같은 문제에 2 - 2를 적용한 코드이다.</p>
<pre><code class="language-java">import java.util.Scanner;

public class Main {

    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);

        int age = Integer.parseInt(sc.nextLine());
        System.out.println(&quot;age = &quot; + age);

        String name = sc.next();
        sc.nextLine();
        System.out.println(&quot;name = &quot; + name);

        String birthDay = sc.nextLine();
        System.out.println(&quot;birthDay = &quot; + birthDay);
    }
}
</code></pre>