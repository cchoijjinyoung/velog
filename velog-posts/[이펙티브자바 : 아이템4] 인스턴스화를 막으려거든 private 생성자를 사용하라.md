<p>객체지향 언어에서 <strong>'인스턴스화를 막으려거든'</strong> 이라는 말은 상당히 의아하다.
간혹 그런 경우가 있는데, 보통 <code>유틸리티 클래스</code>가 그렇다.</p>
<blockquote>
<p>유틸리티 클래스 : 보통 static한 메서드만 갖고 있는 클래스를 칭한다.</p>
</blockquote>
<p>아래는 유틸리티 클래스를 임의로 작성해보았다.</p>
<pre><code class="language-java">package item04;

public class UtilityClass {

    // 이런 static한 메서드들은 클래스를 통해서 바로 호출이 가능하다.
    public static String hello() {
        return &quot;hello&quot;;
    }

    public static void main(String[] args) {
        UtilityClass.hello();

        // 물론, 문법적으로는 인스턴스를 통해서 호출할 수도 있지만 너무 불필요한 코드이다.
        // 오히려 지금 호출한 메서드가 static 메서드인지, 인스턴스 메서드인지 헷갈리게만 한다.
        UtilityClass utilityClass = new UtilityClass();
        utilityClass.hello();
    }
}
</code></pre>
<p>그래서 <strong>'애초에 인스턴스를 만드는 것을 방지하자'</strong> 라는 게<code>아이템4</code>의 핵심목표이다. 
그럼 어떻게 인스턴스를 만드는 것을 방지할 수 있을까?</p>
<h3 id="⚡️-추상클래스로-만들어보기">⚡️ 추상클래스로 만들어보기</h3>
<pre><code class="language-java">package item04;

public abstract class UtilityClass {

    // 자바에서는 생성자를 작성하지 않으면 생성자를 기본으로 제공해주지만,
    // 이 예제에서는 명시적으로 보이게끔 작성해주었다.
    public UtilityClass() {
        System.out.println(&quot;UtilityClass - Constructor&quot;);
    }

    public static String hello() {
        return &quot;hello&quot;;
    }

    public static void main(String[] args) {
        UtilityClass utilityClass = new UtilityClass(); // 컴파일 에러
                                      // ~~빨간 밑줄~~
    }
}</code></pre>
<p>이와 같은 방법으로 방지할 수 있지만, 사실 이 방법은 완벽히 인스턴스화를 방지하지는 못한다.</p>
<p>아래 코드는 <code>추상클래스</code>로 정의된 <code>UtilityClass</code>를 상속받은 <code>DefaultUtilityClass</code>이다.</p>
<pre><code class="language-java">package item04;

public class DefaultUtilityClass extends UtilityClass {
    public static void main(String[] args) {
        // 자식 클래스의 생성자를 호출하면 UtilityClass의 생성자도 호출되기 때문에,
        // 인스턴스가 방지되지 못한다.
        DefaultUtilityClass defaultUtilityClass = new DefaultUtilityClass();
        // 콘솔 출력 : UtilityClass - Constructor
    }
}</code></pre>
<p>위 에시처럼 <strong>자식클래스의 인스턴스를 만들게되면, 부모클래스의 생성자도 호출</strong>되기 때문에 인스턴스화를 방지하지 못한다.</p>
<p>그럼 어떻게 해야할까?</p>
<hr />

<h3 id="⚡️-생성자를-private으로-추가한다">⚡️ 생성자를 private으로 추가한다.</h3>
<p>생성자를 private으로 설정하게 되면 상속을 할 수 없게된다.
위 예시의 <code>DefaultUtilityClass</code>와 같은 자식클래스가 애초에 생길 수가 없다.</p>
<p>그러나 추상클래스가 아닌 채, 단순  private 생성자만 추가했다고 한다면,
아래 코드와 같이,</p>
<pre><code class="language-java">package item04;

public class UtilityClass {

    private UtilityClass() {
        System.out.println(&quot;UtilityClass - Constructor&quot;);
    }

    public static String hello() {
        return &quot;hello&quot;;
    }

    public static void main(String[] args) {
        UtilityClass utilityClass = new UtilityClass();
    }
}</code></pre>
<p>클래스 내부에서는 생성자를 호출할 수가 있다.
우리는 이것을 방지해야한다.
그래서 private 생성자가 호출되면 <code>에러</code>를 던지는 방식을 사용할 것이다.</p>
<pre><code class="language-java">private UtilityClass() {
        // System.out.println(&quot;UtilityClass - Constructor&quot;);
        throw new AssertionError(&quot;잘못됐어. 생성자를 호출하면 안돼&quot;);
    }</code></pre>
<p><span style="color: red;">*</span> 이 에러는 예외를 처리(try-catch)하기 위한 것이 아니다. 이 에시에서는 &quot;생성자를 만나선 안돼!&quot; 라고 알려주는 것이다.</p>
<p>아래 사진은 실행 결과다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/9b8fce30-f415-4cd2-be80-53de39122cb3/image.png" /></p>
<p>⚠️ 그러나, 생성자를 못쓰게하기 위해 직접 생성자를 작성하는 것은 상당히 아이러니하다.
또한 생성자가 너무 눈에 잘보이기 때문에 혼란을 야기할 수 있다.</p>
<p>그래서 저자는 아래 코드와 같이 주석을 통해 <code>문서화</code> 해주는 것을 추천하고 있다.</p>
<pre><code class="language-java">    package item04;

public class UtilityClass {

    /**
     * 이 클래스는 인스턴스를 만들 수 없습니다.
     */
    private UtilityClass() {
        throw new AssertionError();
    }

    public static String hello() {
        return &quot;hello&quot;;
    }
}
</code></pre>
<hr />

<h3 id="✔️-정리">✔️ 정리</h3>
<ul>
<li><p>정적 메서드만 담은 유틸리티 클래스는 애초에 인스턴스로 만들어 쓰려고 설계한 클래스가 아니다.</p>
</li>
<li><p>추상 클래스로 만드는 것으로는 인스턴스화를 막을 수 없다.</p>
</li>
<li><p>private 생성자를 추가하면 클래스의 인스턴스화를 막을 수 있다.</p>
</li>
<li><p>생성자에 주석으로 인스턴스화 불가한 이유를 설명하는 것이 좋다.</p>
</li>
<li><p>상속을 방지할 때도 같은 방법을 사용할 수 있다.(private)</p>
</li>
</ul>