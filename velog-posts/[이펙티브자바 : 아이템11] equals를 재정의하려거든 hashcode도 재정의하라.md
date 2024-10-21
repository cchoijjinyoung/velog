<h2 id="들어가기-앞서">들어가기 앞서</h2>
<p>아이템11은 모든 객체의 공통 메서드 중 <code>hashCode()</code>의 재정의에 대해 설명하고 있다.
핵심 주제는 <strong>'equals를 재정의한 클래스 모두 hashCode도 재정의해야한다.'</strong> 이다.
아래는 <span style="color: #50B4F5;">hashCode 일반 규약</span> 세 가지이다.</p>
<h3 id="span-stylecolor50b4f5hashcode-규약span"><span style="color: #50B4F5;">hashCode 규약</span></h3>
<p><span style="color: #50B4F5;">1.</span> equals 비교에 사용되는 정보가 변경되지 않았다면, hashCode 메서드는 항상 같은 값을 반환해야 한다.
(애플리케이션을 다시 실행했다면 달라질 수 있음.)
<span style="color: #50B4F5;">2.</span> 두 객체에 대한 equals가 같다면, hashCode의 값도 같아야한다.
즉, <span style="color: pink;">논리적으로 같은 객체</span>는 같은 해시코드를 반환해야 한다.
<span style="color: #50B4F5;">3.</span> 두 객체에 대한 equals가 다르더라도, hashCode의 값은 같을 수 있지만 해시 테이블 성능을 고려해 다른 값을 리턴하는 것이 좋다.</p>
<p>그렇다면 왜 equals를 재정의한 클래스는 모두 hashCode를 재정의해야하는지 알아보자.</p>
<hr />
<p>아래는 <code>equals()</code>만 재정의한 <code>PhoneNumber.class</code>이다.</p>
<pre><code class="language-java">package item11;

public final class PhoneNumber {
    private final short first, middle, end;

    public PhoneNumber(int first, int middle, int end) {
        this.first = rangeCheck(first, 999, &quot;지역코드&quot;);
        this.middle = rangeCheck(middle, 9999, &quot;중간&quot;);
        this.end = rangeCheck(end, 9999, &quot;끝&quot;);
    }
    @Override
    public boolean equals(Object o) { // 객체의 속성 값이 모두 같으면 같은 객체라고 재정의.
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        PhoneNumber that = (PhoneNumber) o;

        if (first != that.first) return false;
        if (middle != that.middle) return false;
        return end == that.end;
    }
    // short 로 형변환 하는 코드는 생략하였다.
}</code></pre>
<p>예제를 보면,</p>
<pre><code class="language-java">public class Main {
    public static void main(String[] args) {
        Map&lt;PhoneNumber, String&gt; m = new HashMap&lt;&gt;();
        PhoneNumber p1 = new PhoneNumber(011, 1111, 1111);
        PhoneNumber p2 = new PhoneNumber(02, 222, 2222);
        System.out.println(p1.equals(p2)); // false
        System.out.println(&quot;p1의 해시코드 : &quot; + p1.hashCode()); // 1435804085
        System.out.println(&quot;p2의 해시코드 : &quot; + p2.hashCode()); // 1784662007

        m.put(p1, &quot;아이유&quot;);
        m.put(p2, &quot;제니&quot;);

        // 이 번호가 아이유 번호라고? 다시 확인해봐야지.
        String thisNumber = m.get(new PhoneNumber(011, 1111, 1111));

        System.out.println(thisNumber); // ?
    }
}
</code></pre>
<p>마지막 줄 <code>?</code> 는 어떻게 출력될까?</p>
<p>핸드폰 번호가 '아이유'와 동일하다. 다른 객체긴 하지만 우리가 <code>equals()</code>로 객체의 속성 값이 모두 같으면 동등하다고 작성했다. 
그렇다면 <code>thisNumber</code>는 <span style="color: #57E9E1;">&quot;아이유&quot;</span>를 출력할 것 같다!
.</p>
<pre><code class="language-java">null // 없는 번호입니다.</code></pre>
<p>🙄 ?
이게 바로 hashcode의 <span style="color: #50B4F5;">두 번째 규약</span>을 지키지 못한 코드이다.
(<span style="color: red;">*</span> <span style="color: #50B4F5;"> 두 번째 규약 </span>: 논리적으로 같은 객체는 같은 해시코드를 반환해야 한다.)</p>
<p><code>PhoneNumber.class</code>는 <code>hashCode()</code>를 재정의 하지 않았기 때문에 논리적으로 같은 두 객체가 서로 다른 해시코드를 반환했고, 그 결과 <code>get()</code>는 엉뚱한 해시 버킷에서 객체를 찾으려 한 것이다.
(<span style="color: red;">*</span> 설사 버킷이 같더라도, HashMap은 해시코드가 다른 엔트리끼리는 값 비교를 시도조차 않는다.)</p>
<p>결국 우리는 <code>PhoneNumber.class</code>에 <code>hashCode()</code>를 재정의 해야만 한다. 어떻게 해야할까?
미리 말하지만 아래와 같은 코드는 절대 해선 안되는 코드이다.</p>
<pre><code class="language-java">@Override
public int hashCode() { return 42; }

// `hashCode()`를 재정의해서 모두 같은 해시코드를 갖게하기</code></pre>
<p>위의 코드는,</p>
<ol>
<li>같은 해시코드를 반환.</li>
<li>모든 객체가 같은 버킷에 담김.</li>
<li>해당 객체들은 버킷 내에서 연결리스트처럼 동작.</li>
</ol>
<p>결국 평균 수행 시간을 O(1) &rarr; O(n) 으로 느려지게 만든다.</p>
<p>좋은 해시 함수라면 서로 다른 객체에 다른 해시코드를 반환해야한다.
이것이 hashCode의 <span style="color: #50B4F5;">세 번째 규약</span>이다.
(<span style="color: red;">*</span> <span style="color: #50B4F5;"> 세 번째 규약 </span>: 해시코드는 같을 수 있지만, 성능을 고려해 다른 값을 리턴하는 것이 좋다.)</p>
<p>다음은 이펙티브 자바에서 말하는 '좋은 hashCode를 작성하는 요령'을 예시 코드로 작성해보았다.</p>
<pre><code class="language-java">public class Wizard { // 마법사
    private final int no; // 식별 번호
    private final Feature feature; // 특징
    private final String[] friends; // 친구들


    @Override
    public int hashCode() {
        // int 타입 변수 result에 필드마다 해시함수를 거쳐서 해시코드를 담아줄 것
        // 기본 타입 필드 : Type.hashCode(필드)
        int result = Integer.hashCode(no); 

        // 참조 타입 필드 : 해당 클래스에서 재정의한 hashCode()를 사용 
        result = 31 * result + feature.hashCode(); // 필드.hashCode()

        // 배열 타입 필드 : Arrays.hashCode(필드)    
        result = 31 * result + Arrays.hashCode(friends);
        return result;
    }

       // 생성자 및 equals() 는 생략했다.
    // ...
}</code></pre>
<p>int 타입 result 객체에 필드마다 해시코드를 구해서 계속 더해주는데, 더해줄때마다 <code>31</code>을 곱해주면된다.</p>
<blockquote>
<p>31인 이유? :
31은 소수이면서 홀수이기 때문에 선택된 값이다. 만일 그 값이 짝수였고 곱셈 결과가 오버플로되었다면 정보는 사라졌을 것이다. 2로 곱하는 것은 비트를 왼쪽으로 shift하는 것과 같기 때문이다. 소수를 사용하는 이점은 그다지 분명하지 않지만 전통적으로 널리 사용된다. 31의 좋은 점은 곱셈을 시프트와 뺄셈의 조합으로 바꾸면 더 좋은 성능을 낼 수 있다는 것이다(31 * i는 (i &lt;&lt; 5) - i 와 같다). 최신 VM은 이런 최적화를 자동으로 실행한다. </p>
</blockquote>
<ul>
<li>현대 컴파일러와 JVM의 JIT 컴파일러는 곱셈과 관련된 일반적인 패턴을 감지하고, 필요한 경우 이를 비트 시프트 연산으로 최적화할 수 있다고한다. </li>
</ul>
<p>아래 코드는 우리가 오버라이딩한 <code>hashCode()</code> 를 사용하는 예제이다.</p>
<pre><code class="language-java">public class Main {
    public static void main(String[] args) {
        Map&lt;Wizard, String&gt; map = new HashMap&lt;&gt;();
        // 학번, 특징, 친구들
        Wizard w1 = new Wizard(1, &quot;번개 흉터&quot;, new String[]{&quot;론, 헤르미온느&quot;});
        Wizard w2 = new Wizard(2, &quot;금색 머리&quot;, new String[]{&quot;크레브, 고일&quot;});

        map.put(w1, &quot;해리포터&quot;);
        map.put(w2, &quot;말포이&quot;);

        String s = map.get(new Wizard(1, &quot;번개 흉터&quot;, new String[]{&quot;론, 헤르미온느&quot;}));
        System.out.println(s); // 해리포터
    }
}</code></pre>
<p>구현하는 방법은 어렵지 않지만, 직접 구현할 필요 없이, 
이러한 기능은 IDE에서 제공해주기도 하고, lombok, AutoValue, guava도 이런 기능을 제공한다.</p>
<p>아래는 롬복을 사용한 코드다.</p>
<pre><code class="language-java">
@EqualsAndHashCode // lombok
public class Wizard {
    private final int no;
    private final Feature feature;
    private final String[] friends;

    public Wizard(int no, String ft, String[] friends) {
        this.no = no;
        feature = new Feature(ft);
        this.friends = friends;
    }
}</code></pre>
<h3 id="마지막으로-주의할-점">마지막으로 주의할 점</h3>
<ol>
<li><p><code>equals()</code>에 쓰이지 않는 필드는 반드시 제거해야 한다. 
해시코드를 계산할 때 <code>equals()</code> 에서 사용하지 않는 필드까지 포함하게 되면 다른 해시코드를 반환하게 된다. 이는 성능 저하 뿐만아니라 <code>hashCode</code>의 규약을 깨뜨린다.</p>
</li>
<li><p>성능을 높인답시고 해시코드를 계산할 때 핵심필드를 생략해서는 안된다. 속도는 빨라지겠지만, 해시 품질이 나빠져 해시테이블의 성능을 심각하게 떨어뜨릴 수도 있다.</p>
</li>
<li><p>hashCode가 반환하는 값의 생성 규칙을 API 사용자에게 자세히 공표하지 말자. 그래야 클라이언트가 이 값에 의지하지 않게 되고, 추후에 계산 방식을 바꿀 수도 있다.
요약하면, 구체적인 생성 규칙을 공개하지 않음으로써, 개발자는 내부 구현을 자유롭게 변경할 수 있고, 사용자는 해당 메서드의 반환 값에 너무 의존하지 않게 되어, 전반적으로 소프트웨어의 품질과 유지 보수성이 향상된다.</p>
</li>
</ol>
<hr />
<h3 id="🤔-책에서-잘-이해되지-않는-부분">🤔 책에서 잘 이해되지 않는 부분</h3>
<ol>
<li><p><code>요즘 VM들은 최적화를 자동으로 해준다.</code> : JVM 말고도 다른 VM들도 뜻한다. 해당 부분은 <code>31 * i</code> 가 <code>i &lt;&lt; 5) - 1</code> 로 최적화 된다는 내용에 포함된 내용인데, 이것은 <span style="color: #50B4F5;">JIT 컴파일러</span>가 이런 패턴을 인식하고, 코드가 런타임에 실행될 때 해당 연산을 더 효율적인 비트 연산으로 <span style="color: #50B4F5;">자동 변환</span>할 수 있다고 한다.</p>
</li>
<li><p><code>비결정적(undeterminisitc) 요소</code> : 다른 것들과 비교할 때 결정적인 요소가 아님을 뜻한다. 현재 아이템 내용에서는 <code>equals</code> 비교할 때 <span style="color: #50B4F5;">'핵심 필드가 아닌 요소'</span> 로 해석할 수 있고, <span style="color: #50B4F5;">'해시코드 생성에서 고려되지 않아야 하는 요소'</span>로 해석할 수 있다.</p>
</li>
<li><p><code>URL처럼 계층적인 이름을 대량으로 사용하면 심각한 오류 유발 가능성</code> : 자바2 전의 String은 문자열이 길면 균일하게 나눠 최대 16문자만 뽑아내 해시코드를 계산했는데, URL처럼 계층적인 이름은 나머지 부분의 문자열이 크게 다르지 않기 때문에 해시충돌이 일어날 확률이 높다.</p>
<pre><code class="language-java">https://example.com  /user/  data
https://example.com  /admin/  data</code></pre>
<p><span style="color: red;">*</span> 균일하게 나누다가 /user/ 와 /admin/ 부분이 누락되면 해싱충돌이 일어날 수 있다. </p>
</li>
</ol>