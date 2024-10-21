<h2 id="들어가기-앞서">들어가기 앞서</h2>
<p>아이템8은 객체의 소멸과 관련된 내용이다. 
객체가 가지고 있는 리소스를 제대로 정리하지 않고 소멸시키게 되면 리소스 고갈, 성능 저하, 데이터 손실 등의 문제가 생길 수 있기 때문에 객체의 소멸을 어떻게 하느냐는 매우 중요하다.</p>
<p>자바에서는 이러한 리소스들을 가비지 컬렉터(GC)가 자동으로 정리해주는데, 가비지컬렉터는 보통 JVM내의 <code>메모리 리소스</code>에 초점을 두어 관리한다. 그래서 메모리 외의 다른 리소스들도 관리해줘야하는데,
이 것을 해결하기 위해 기획된게 <strong><span style="color: pink;">finalizer</span> 와 <span style="color: pink;">cleaner</span></strong>이다.</p>
<p><span style="color: red;">*</span> <strong><span style="color: pink;">finalizer</span> 와 <span style="color: pink;">cleaner</span> 둘 다 객체가 <span style="color: orange;">가비지 컬렉터</span>에 의해 회수되기 전에 리소스 정리나 기타 정리 작업을 수행하기 위해 사용된다.</span></strong></p>
<p>그러나 단점이 너무 많아서 사실 상 쓸 수 없다. (cleaner는 그나마 사용할 수 있다.)</p>
<p>우리는 왜 사용하면 안되는 지 알아볼 것이고, 해결방법 또한 알아볼 것이다.</p>
<hr />
<h2 id="🤬-finalizer">🤬 finalizer</h2>
<p>권장하지 않지만, finalizer를 사용하려면,
우리가 갖고 있는 클래스에 finalize()를 오버라이딩 하면된다.
finalize()는 Object클래스에 정의되어 있고, 자바9부터 @Deprecated 됐다.</p>
<pre><code class="language-java">package item8

public class 내가_사용할_클래스 {
    @Override
    protected void finalize() throws Throwable {
        // TODO 리소스 정리
    }
}
</code></pre>
<p><strong>한가지 예시를 들어보자.</strong></p>
<p>예를 들어, 'File' 객체를 사용하여 파일을 열게 되면, 그 파일에 대한 핸들 또는 참조가 생긴다.
이렇게 열린 파일은 <code>시스템 리소스</code>를 점유하게 된다.
여러 파일을 계속 열게 되면 사용 가능한 리소스가 줄어들 것이고, 결국 파일을 열 수 없게 될 수도 있다.
따라서 파일을 사용한 후에는 반드시 <code>close()</code> 메서드를 호출하여 해당 파일 리소스를 시스템에 반환해야한다.</p>
<h4 id="지금까지의-정리">지금까지의 정리</h4>
<ol>
<li>객체가 가비지 컬렉션의 대상이 되기 전에 <code>finalize()</code>메서드가 호출된다.</li>
<li>만약 해당 객체가 <code>파일</code>과 같은 리소스를 점유하고 있고,</li>
<li><code>finalize()</code> 내에 <code>파일</code>을 닫는 등의 작업하는 코드가 있다면,</li>
<li><code>finalize()</code> 호출 시점에 그 리소스가 반환된다.</li>
</ol>
<p>이제부터는 문제점에 대해 알아보겠다.</p>
<h3 id="🤔-대체-문제점이-뭐길래">🤔 대체 문제점이 뭐길래?</h3>
<ul>
<li><code>finalize()</code>의 호출 시점은 정확히 예측할 수 없다. 이로 인해 파일과 같은 리소스가 필요 이상으로 오랜 시간 점유될 수 있다.</li>
<li><code>finalize()</code>가 오류로 인해 제대로 실행되지 않으면 리소스는 반환되지 않을 수 있다.</li>
<li><code>finalizer 공격</code>에 노출되어 심각한 보안 문제를 일으킬 수 있다.</li>
</ul>
<h4 id="span-stylecolorredspan-finalizer-공격이란-"><span style="color: red;">*</span> finalizer 공격이란 ?</h4>
<p>코드로 예시를 들어보겠다.</p>
<ul>
<li>User.class : name을 받는 생성자 존재, 단순 출력 hello() 메서드 존재.</li>
<li>CustomUser.class : User클래스를 상속받고, finalize()을 오버라이딩함.</li>
<li>AttackExam : 공격 예제</li>
</ul>
<p>User.class</p>
<pre><code class="language-java">public class User {

    private String name;

    public User(String name) {
        this.name = name;
        // 단순히 name이 홍길동인 유저는 생성자에 들어올 때 예외를 던지게 만들었다.
        if (name.equals(&quot;홍길동&quot;)) {
            throw new IllegalArgumentException(&quot;홍길동은 가입 금지입니다.&quot;);
        }
    }
    public void hello() {
        System.out.println(&quot;안녕하세요. &quot; + this.name + &quot;입니다.&quot;);
    }
}</code></pre>
<p>CustomUser.class</p>
<pre><code class="language-java">public class CustomUser extends User {

    public CustomUser(String name) {
        super(name);
    }

    @Override
    protected void finalize() throws Throwable {
    // finalize() 코드 내에서 hello()를 호출하고 있다는 것에 집중하자.
        hello();
    }
}</code></pre>
<p>AttackExam.class</p>
<pre><code class="language-java">public class AttackExam {
    public static void main(String[] args) throws InterruptedException {
        User user;
        try {
            user = new CustomUser(&quot;홍길동&quot;);
        } catch (IllegalArgumentException e) {
            System.out.println(&quot;이러면??&quot;);
        }
        System.gc();
        Thread.sleep(3000L);
    }
}</code></pre>
<p>위의 예제를 잠깐 살펴보면,
<code>User user = new CustomUser(&quot;홍길동&quot;);</code> 에서 자식클래스인 CustomUser의 생성자가 호출되고, 당연히 부모클래스의 생성자도 호출된다. 
하지만 우리는 name으로 홍길동을 받게되면 예외를 던지도록 했고, 이 예제에서도 물론 예외가 발생한다. 
문제는 여기서 발생한다. <span style="color: pink;">'만들어지다 만 객체'</span>가  <span style="color: orange;">GC</span>되면서 <code>finalize()</code>가 호출된다.
그럼 <code>finalize()</code> 메서드내에 <code>hello()</code>메서드가 호출될 것이고 우리는 실행결과로 아래와 같은 콘솔창을 볼 수 있을 것이다.</p>
<pre><code class="language-java">// 이러면??
// 안녕하세요. 홍길동입니다.
</code></pre>
<p>그럼 어떻게 해결해야하나?
⚡️ User 클래스를 final로 작성하여 상속을 못하게 하자.
= 좋은 방법이다. 그렇지만 내 클래스가 상속을 해야만 한다면?
⚡️ User 클래스에도 finalize()를 오버라이딩 한 후에 final로 선언하자.
= 그럼 자식클래스가 finalize()는 오버라이딩할 수 없기 때문에 finalizer 공격으로 부터 안전하다.</p>
<p>다음으로는 cleaner에 대해 알아보겠다.</p>
<h2 id="😡-cleaner">😡 cleaner</h2>
<pre><code class="language-java">public class 방 {

    private List&lt;Object&gt; 쓰레기들;

    public 방(List&lt;Object&gt; 쓰레기들) {
        this.쓰레기들 = 쓰레기들;
    }

    public static class 상태 implements Runnable {
        private List&lt;Object&gt; 청소할_쓰레기들;

        public 상태(List&lt;Object&gt; 청소할_쓰레기들) {
            this.청소할_쓰레기들 = 청소할_쓰레기들;
        }

        @Override
        public void run() {
            청소할_쓰레기들 = null;
            System.out.println(&quot;방 청소&quot;);
        }
    }
}</code></pre>
<p>간단하게 설명하면 <code>방.class</code> 에는 <code>쓰레기들</code>이란 필드가 있고, Inner클래스로 <code>상태.class</code>를 갖는다.
위 코드에서 봐야할 것은</p>
<ol>
<li><code>상태.class</code> 가 static 으로 선언됨으로써 <code>방.class</code> 를 참조하지 않는다는 것.</li>
<li><code>상태.class</code> 가 Runnable 을 구현했고 <code>run()</code>메서드를 오버라이딩한 것.</li>
</ol>
<p>아래 보이는 <code>나.class</code>는 <span style="color: pink;">Cleaner</span> 객체를 생성하고,
<code>register()</code> 메서드로 해당 객체가 GC가 될 때 <code>상태.class</code>의 <code>run()</code> 메서드를 호출하라고 등록하였다.</p>
<pre><code class="language-java">public class 나 {
    public static void main(String[] args) throws InterruptedException {
        Cleaner cleaner = Cleaner.create();

        List&lt;Object&gt; 쓰레기들 = new ArrayList&lt;&gt;();
        방 내방 = new 방(쓰레기들);

        // 클리너에 등록 : 내방이 GC가 될 때, 상태의 Runnable을 사용해서 정리해라.
        cleaner.register(내방, new 방.상태(쓰레기들));

        내방 = null; // 내방 객체가 null이 됨으로써
        System.gc(); // 가비지컬렉터가 해당 객체를 메모리에서 제거하려 할 것이고, 
                     // 그 직전에 run() 메서드가 호출된다.
        Thread.sleep(3000L);
    }
}</code></pre>
<p>Cleaner도 마찬가지로 즉시 수행된다는 보장이 없다.
그러면 이것들을 대신해줄 묘안은 무엇일까?</p>
<p>바로 <code>AutoCloseable</code>이다.</p>
<h2 id="😊-autocloseable">😊 AutoCloseable</h2>
<pre><code class="language-java">public class 방 implements AutoCloseable { 
// 그저 AutoCloseable을 구현하고, close() 메서드 오버라이딩

    private int 쓰레기들_갯수 = 100;

    public void autoCleanUp() {
        System.out.println(&quot;자동 청소&quot;);
        this.쓰레기들_갯수 = 0;
    }

    @Override
    public void close() {
        try {
            autoCleanUp();
        } catch (Exception e) {
            throw new RuntimeException(&quot;faild to cleanUp &quot;);
        }
    }
}</code></pre>
<pre><code class="language-java">public class Exam {

    public static void main(String[] args) {
        // close() 메서드를 호출하면 된다.    
        // try-with-resources 를 사용해서 예외가 발생해도 제대로 종료하도록 한다.
        try (방 내_방 = new 방()) {
            // TODO 자원 반납 처리
        }
    }
}</code></pre>
<p>간단한 예제이므로 주석으로만 설명해두었다.</p>
<p>그럼 마지막으로 <code>AutoCloseable + cleaner(안전망 역할)</code> 코드를 보겠다.</p>
<p>보기 전에 간략하게 설명하면, <code>방, 엄마, 나</code> 세 개의 클래스로 구성되어있다.
<code>방.class</code> 는 AutoCloseable을 구현했고, Runnable을 구현한 <code>상태.class</code> 내부클래스가 존재한다.
<code>엄마.class</code> 는 try-with-resources를 사용한것이고,
<code>나.class</code> 는 사용하지 않았다.</p>
<pre><code class="language-java">public class 방 implements AutoCloseable {
    private static final Cleaner cleaner = Cleaner.create();

    private static class 상태 implements Runnable {
        int 쓰레기_갯수;

        상태(int 쓰레기_갯수) {
            this.쓰레기_갯수 = 쓰레기_갯수;
        }

        @Override
        public void run() {
            System.out.println(&quot;방 청소&quot;);
            쓰레기_갯수 = 0;
        }
    }
    private final 상태 내_방_상태;

    private final Cleaner.Cleanable cleanable;

    public 방(int 쓰레기_갯수) {
        내_방_상태 = new 상태(쓰레기_갯수);
        // 어떤 오브젝트(this)가 GC가 될 때, 이 자원(내_방_상태)을 해제하라.
        cleanable = cleaner.register(this, 내_방_상태);
    }

    @Override
    public void close() {
        cleanable.clean();
    }
}</code></pre>
<pre><code class="language-java">public class 엄마 {
    public static void main(String[] args) {
        try (방 내_방 = new 방(10)) {
            System.out.println(&quot;방이 지저분하네.&quot;);
        }
    }
}</code></pre>
<pre><code class="language-java">public class 나 {
    public static void main(String[] args) {
        new 방(10);
        System.out.println(&quot;방이 지저분하네.&quot;);
    }
}</code></pre>
<pre><code class="language-java">// 엄마.class 
// 실행결과 :
// 방이 지저분하네.
// 방 청소

// ----------------------

// 나.class
// 실행결과 : 
// 방이 지저분하네.</code></pre>
<p><code>나.class</code>에서도 <code>방 청소</code>가 출력될 것이라고 예상했다면, 이게 바로 앞서 말한 <strong>'예측할 수 없는 상황'</strong>이다.</p>
<pre><code class="language-java">public class 나 {
    public static void main(String[] args) {
        new 방(10);
        System.out.println(&quot;방이 지저분하네.&quot;);

        System.gc(); // 이 코드를 추가함으로써 &quot;방 청소&quot;를 출력할 수도 있고 안 할수도 있다.
    }
}</code></pre>
<h3 id="✔️-정리">✔️ 정리</h3>
<ul>
<li>finalizer와 cleaner는 즉시 수행된다는 보장이 없다.</li>
<li>finalizer와 cleaner는 실행되지 않을 수도 있다.</li>
<li>finalizer 동작 중에 예외가 발생하면 정리 작업이 처리되지 않을 수도 있다.</li>
<li>finalizer와 cleaner는 심각한 성능 문제가 있다.</li>
<li>finalizer는 보안 문제가 있다.</li>
<li><span style="color: red;">*</span> <span style="color: orange;">반납할 자원이 있는 클래스는 AutoCloseable을 구현하고 클라이언트에서 close()를 호출하거나 try-with-resource를 사용해야 한다.</span></li>
</ul>