<h2 id="들어가기-앞서">들어가기 앞서</h2>
<p>아이템12는 모든 객체의 공통 메서드 중 <code>toString()</code> 의 재정의에 대해 설명하고 있다.</p>
<h3 id="tostring-메서드에-대해-간단히-알아보자면"><code>toString</code> 메서드에 대해 간단히 알아보자면,</h3>
<ul>
<li>Java의 <code>Object</code>클래스에 정의된 메서드이다.</li>
<li>객체의 정보를 문자열로 반환할 때 사용한다.</li>
<li>모든 Java 객체는 <code>Object</code>를 상속받기 때문에 <code>toString</code> 메서드를 가지고 있다.</li>
</ul>
<p>하지만 <code>Object</code>의 기본 <code>toString</code> 메서드가 우리가 작성한 클래스에 적합한 문자열을 반환하는 경우는 거의 없다.</p>
<p>이 메서드는 <span style="color: #50B4F5;">PhoneNumber<span style="color: pink;">@</span>adbdb</span> 처럼 단순히 <span style="color: #50B4F5;">클래스_이름<span style="color: pink;">@</span>16진수로 표시한 해시코드</span>를 반환할 뿐이다.</p>
<p><code>toString</code>의 일반 규약에 따르면,
<span style="color: orange;"> '간결하면서 사람이 읽기 쉬운 형태의 유익한 정보'</span> 를 반환해야 한다.</p>
<p>예를 들면, <span style="color: #50B4F5;">PhoneNumber<span style="color: pink;">@</span>adbdb</span> 보다는 <span style="color: skyblue;">010-1234-5678</span> 처럼 전화번호로 직접 표현하는게 유익하다.</p>
<p>또 다른 규약으로는,
<span style="color: orange;"> '모든 하위 클래스에서 이 메서드를 재정의하라'</span> 이다.</p>
<blockquote>
<p>저자는 해당 규약을 &quot;정말 새겨들어야 할 조언이다!&quot; 라고 말한다.
. . .
<span style="color: red;">*</span> 개인적인 생각으로는 객체의 내부 상태를 외부로 노출시키고 싶지 않은 경우나 문자열 표현이 큰 의미를 갖지 않는 경우는 toString을 재정의할 필요가 없다고 생각한다.</p>
</blockquote>
<hr />
<h2 id="좋은-예와-나쁜-예">좋은 예와 나쁜 예</h2>
<p><code>toString</code> 메서드는 아래 상황에서 자동으로 호출한다.</p>
<ul>
<li>println, printf</li>
<li>문자열 연결 연산자 (+)</li>
<li>assert 구문에 넘길 때</li>
<li>디버거가 객체를 출력할 때</li>
</ul>
<p>좋은 <code>toString</code>은 이 인스턴스를 포함하는 객체에서 유용하게 쓰인다.
예를 들어 map 객체를 출력했을 때 {Jenny = 010-1234-5678} 처럼 말이다.</p>
<p>다음으로는 <code>toString</code>에 주요 정보가 담기지 않았을 때 문제가 되는 대표적인 예인 <span style="color: #FF6E6E;">테스트 실패 메시지</span>이다.</p>
<pre><code class="language-java">❌ Assertions failure: expected {abc, 123}, but was {abe, 123}
// 단언 실패: 예상 값 {abc, 123}, 실젯값 {abc, 123}</code></pre>
<p>아래와 같은 경우에 위와 같은 메시지가 나올 수 있다.
다른 예시로 보겠다.</p>
<pre><code class="language-java">public class Wizard {
    private String lastName;
    private String firstName;

    @Override
    public String toString() {
        return &quot;Wizard{&quot; +
                &quot;성 ='&quot; + lastName + '\'' +
                '}';
    } // toString 에 lastName 정보만 담겨져 있다.
}</code></pre>
<pre><code class="language-java">class WizardTest {
    @Test
    void toString_테스트() {
        Wizard w1 = new Wizard(&quot;포터&quot;, &quot;제임스&quot;);
        Wizard w2 = new Wizard(&quot;포터&quot;, &quot;해리&quot;);

        Assertions.assertEquals(w1, w2); // 둘의 equals 비교
    }
}</code></pre>
<pre><code class="language-java">❌ toString_테스트() // 서로 다르다!
Expected :Wizard{성 ='포터'}
Actual   :Wizard{성 ='포터'} // 그러나 보기에는 같아보임.</code></pre>
<hr />
<h2 id="포맷과-문서화">포맷과 문서화</h2>
<h3 id="tostring-을-구현할-때면-반환값의-포맷을-문서화할지-정해야-한다"><code>toString</code> 을 구현할 때면 반환값의 포맷을 문서화할지 정해야 한다.</h3>
<p>전화번호나 행렬 같은 값 클래스는 문서화하길 권장한다.
포맷을 명시하면('010-1234-5678' 와 같이) 사람이 읽기 쉽다.
따라서 이 값 그대로 입출력에 사용하거나, 사람이 읽을 수 있는 데이터 객체로 저장할 수도 있다.
포맷을 명시하기로 했다면, 명시한 포맷에 맞는 문자열과 객체를 서로 전환할 수 있는 정적 팩터리나 생성자를 함께 제공하면 좋다.</p>
<pre><code class="language-java">// BigInteger, BigDecimal과 대부분의 기본 타입 클래스가 여기 해당한다.
String s = &quot;10000000000000&quot;;
BigInteger b1 = new BigInteger(s);</code></pre>
<h4 id="포맷을-명시하면-단점도-있는데">포맷을 명시하면 단점도 있는데,</h4>
<p>포맷을 한 번 명시 후에, 프로그래머들이 해당 <span style="color: pink;">포맷에 맞춰 의존적인 코드</span>를 작성한다면
포맷을 바꿨을 때 그 코드들은 엉망이 돼버린다.
반대로 포맷을 명시하지 않는다면, 이 부분에서는 유연성을 얻게 된다.</p>
<h3 id="포맷을-명시하든-아니든-의도를-명확히-밝히자">포맷을 명시하든 아니든 의도를 명확히 밝히자.</h3>
<p>이펙티브 자바에서 가져온 코드다.</p>
<ul>
<li>명시한 경우
```java
/**</li>
<li>이 전화번호의 문자열 표현을 반환한다.</li>
<li>이 문자열은 &quot;XXX-YYY-ZZZZ&quot; 형태의 12글자로 구성된다.</li>
<li>XXX는 지역코드, YYY는 프리픽스, ZZZZ는 가입자 번호다.</li>
<li>각각의 대문자는 10진수 숫자 하나를 나타낸다.</li>
<li></li>
<li>전화번호의 각 부분의 값이 너무 작아서 자릿수를 채울 수 없다면,</li>
<li>앞에서부터 0으로 채워나간다. 예컨데 가입자 번호가 123이라면</li>
<li>전화번호의 마지막 네 문자는 &quot;0123&quot;이 된다.</li>
<li>/
@Override 
public String toString() {
   return String.format(&quot;%03d-%03d-%04d&quot;, areaCode, prefix, lineNum);
}
```</li>
<li>명시하지 않은 경우
```java
/**</li>
<li>이 약물에 관한 대략적인 설명을 반환한다.</li>
<li>다음은 이 설명의 일반적인 형태이나,</li>
<li>상세 형식은 정해지지 않았으며 향후 변경될 수 있다.</li>
<li></li>
<li>&quot;[약물 #9: 유형-사랑, 냄새=테러빈유, 겉모습=먹물]&quot;</li>
<li>/
@Override
public String toString() { ... }<pre><code>이러한 설명이 있어야 프로그래머들이 이 포맷에 맞춰 코드를 작성할 것이다.
아래 예시를 보면 `상세 형식은 정해지지 않았으며 향후 변경될 수 있다.` 라고 적혀있다.
그럼에도 불구하고 해당 포맷의 값을 가공한 코드를 작성하면 자신을 탓할 수 밖에 없다.
</code></pre></li>
</ul>
<h3 id="tostring이-반환한-값에-포함된-정보를-얻어올-수-있는-api를-제공하자">toString이 반환한 값에 포함된 정보를 얻어올 수 있는 API를 제공하자.</h3>
<p>이건 <code>getter</code> 와 같은 메서드를 제공하라는 의미이다.
어차피 <code>toString</code>으로 공개된 데이터라면, <code>toString</code>을 구성하는 각각의 데이터를 따로따로 받을 수 있는 메서드들을 제공하자는 것이다.
만약 <code>PhoneNumber.class</code> 의 각 필드에 대한 <code>getter</code>가 없다면, 
각 정보가 필요한 프로그래머는 <code>toString</code>의 반환값을 파싱할 수 밖에 없다. 상당히 비효율적이고 필요없는 작업이다.</p>
<hr />
<h2 id="자동-생성">자동 생성</h2>
<p><code>equals()</code>, <code>hashCode()</code> 메서드들과 마찬가지로 <code>toString()</code>도 구글의 AutoValue 프레임워크나 IDE에서 생성해주는 기능이 있다. 하지만 '클래스의 특성' 까지는 파악하지 못한다. 예컨대 앞서의 PhoneNumber 클래스가 이런 경우이다. 하지만, 이런 경우라도 <code>Object의 toString</code> 보다는 훨씬 유용하다.</p>