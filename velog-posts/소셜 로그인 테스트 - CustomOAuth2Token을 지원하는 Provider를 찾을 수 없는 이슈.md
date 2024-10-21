<h2 id="issue">Issue</h2>
<hr />
<p>“ No AuthenticationProvider found for com.halfgallon.withcon.domain.auth.security.token.CustomOAuth2Token” 에러 발생</p>
<p><img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/ceaf9e38-fa01-4c5c-9d79-d519cf215f10/image.png" /></p>
<p>내가 커스텀한 <code>CustomOAuth2Token</code> 를 지원하는 <code>Provider</code> 를 찾을 수 없다는 에러였다.</p>
<p>현재 내가 구현한 소셜 로그인 기능의 실제 인증 기능은 내가 커스텀한 <code>OAuthLoginProvider</code>  에서 진행된다.</p>
<p><code>OAuthLoginProvider</code> 는 <code>AuthenticationProvider</code>를 구현해서 작성하게 되는데, 구현해야하는 메서드는 아래와 같다.</p>
<pre><code class="language-java">// 참고: @Override만 한 상태이고 내부 코드를 작성하지 않은 상태이다.

// 실제 인증을 수행하는 메서드
@Override
public Authentication authenticate(Authentication authentication) throws AuthenticationException {
  return null;
}

// 파라미터로 주어진 authentication이 현재 클래스(Provider)에 의해 인증될 수 있는 지 여부(boolean)를 반환하는 메서드
@Override
public boolean supports(Class&lt;?&gt; authentication) {
  return true;
}</code></pre>
<p>이번에 발생한 오류같은 경우 <code>supports(Class&lt;?&gt; authentication)</code> 에서 파라미터로 넘어온 authentication 객체를 이 <code>Provider</code> 가 지원하지 못해서 생긴 오류다.</p>
<p>아래 코드는 <code>ProviderManager</code> 인증 과정의 일부를 가져온 것이다.</p>
<pre><code class="language-java">// ProviderManager.class 의 authenticate() 일부 발췌

// OAuth2LoginFilter에서 인증 매니저에게 일임한 토큰의 클래스 타입을 의미한다. CustomOAuth2Token.class 일 것이다.
Class&lt;? extends Authentication&gt; toTest = authentication.getClass(); 

for (AuthenticationProvider provider : getProviders()) {
    if (!provider.supports(toTest)) { // 여기서 supports 메서드가 사용되는 것을 볼 수 있다.
        continue;
    }
    provider.authenticate(authentication);
}

// 코드를 보면 반복문을 돌려 찾은 Provider가 해당 토큰으로 인증 기능을 수행할 수 있으면, authenticate 를 수행한다.</code></pre>
<p>내가 처음 작성했던 코드는 아래와 같다.</p>
<pre><code class="language-java">@Override
public boolean supports(Class&lt;?&gt; authentication) {
    return authentication.isInstance(CustomOAuth2Token.class); 
}</code></pre>
<p>클래스 타입을 비교해야하는데, 파라미터로 넘어온 authentication 이 CustomOAuth2Token의 객체인 지를 판별하는 코드를 작성했다.</p>
<p>결과는 아래 사진과 같이 false 였다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/90993948-cfcc-48e8-8e37-b00b1ec4e239/image.png" /></p>
<hr />
<h2 id="solve">Solve</h2>
<hr />
<p>아래 사진과 같이 코드를 바꿔주었다. <code>isAssignableFrom(authentication)</code>  메서드는 다른 Provider 구현체의 <code>supports</code> 메서드를 보다가 발견했다.
해당 클래스 또는 인터페이스와 동일한지, 아니면 그 클래스 또는 인터페이스의 슈퍼클래스인지 여부를 확인해준다고 한다. 맞을 시 <code>true</code>  를 리턴한다.
<img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/3afa4210-4864-44d3-bac0-0c8754f748bd/image.png" /></p>