<h1 id="목표"><strong>목표</strong></h1>
<p>클라이언트에서 OAuth2 인증코드를 받아서 넘겨주면, 해당 인증코드로 액세스 토큰을 발급받고 이 토큰으로 사용자 정보까지 가져온 뒤 후처리이다. 후 처리는 다음과 같다.</p>
<ul>
<li><ol>
<li>사용자 정보를 가져온 뒤 회원 DB에 저장</li>
</ol>
</li>
<li><ol start="2">
<li>로그인 처리</li>
</ol>
</li>
<li><ol start="3">
<li>액세스 토큰은 헤더에, 리프래시 토큰은 쿠키에 발급</li>
</ol>
</li>
</ul>
<hr />
<h2 id="처음-구현-방식--controller와-service에서의-처리"><strong>처음 구현 방식 : Controller와 Service에서의 처리</strong></h2>
<p><code>Controller</code> -&gt; <code>Service</code> 에서 <code>OpenFeign</code> 라이브러리를 이용해 인증코드로 액세스토큰을 발급하고 사용자 정보를 가져왔다.</p>
<ul>
<li>가져온 사용자 정보를 회원 DB에 저장할 수 있었고, 액세스토큰과 리프래시 토큰을 응답 값으로 보내주었다.</li>
</ul>
<hr />
<h2 id="첫번째-이슈구조-리팩토링"><strong>첫번째 이슈(구조 리팩토링)</strong></h2>
<p><code>Controller</code> -&gt; <code>Service</code> 에서 수행하는 로직을 리팩토링하고자 했다. 이유는 다음과 같다.</p>
<ul>
<li><ol>
<li>일반로그인 시에는 인증을 시큐리티 필터내에서 수행하고 인증에 성공하면 <code>LoginSuccessHandler</code>에서 토큰을 응답해주는 방식으로 구현했었다. 소셜로그인도 <code>LoginSuccessHandler</code> 를 사용함으로서 같은 방식으로 진행되어야 한다고 생각됐다.</li>
</ol>
</li>
<li><ol start="2">
<li>액세스토큰은 헤더에, 리프래시 토큰은 쿠키에 넣어서 응답하려다보니 <code>Controller</code>가 지저분해졌다.<ul>
<li>이 부분도 로그인 성공 시 <code>LoginSuccessHandler</code> 에서 토큰을 응답해주는 것으로 해결하고자 했다.</li>
</ul>
</li>
</ol>
</li>
</ul>
<p>시큐리티에서는 OAuth2 를 쉽게 사용할 수 있게끔 OAuth2 관련 기능을 지원한다.</p>
<p>그 중에서 OAuth2-Client 를 사용하면되는데, 인증코드를 요청하는 부분부터 시작하기때문에, </p>
<p>현재 내 상황인 클라이언트에서 인증코드를 받아서 액세스코드를 요청해야하는 것부터 시작해야할 때는 해당 기능을 어떻게 사용해야할 지 감이 잘 안왔다.</p>
<p>고민 끝에 스프링 시큐리티의 인증 기능을 커스텀해서 나만의 인증 기능을 만들기로 했다.</p>
<p>편한 이해를 위해 구현하고자 하는 요청 흐름을 그려보았다.
<img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/a1ab7937-60c1-4454-a9d5-2d14a0bfbdd0/image.png" /></p>
<hr />
<h2 id="abstractauthenticationprocessingfilter를-구현한-oauthloginfilter-작성"><strong>'AbstractAuthenticationProcessingFilter'를 구현한 'OAuthLoginFilter' 작성</strong></h2>
<pre><code class="language-bash">// 소셜 로그인 요청은 다음과 같다.

POST http://localhost:8080/auth/oauth2/login

{
  &quot;registrationId&quot;: &quot;kakao&quot;,
  &quot;authorizationCode&quot;: &quot;hW22qEzPzV9Dm5NknFjhH0pDKUg58VitM5LdeJ5vJICPnAgJYN9ZvsFMwFwKKwynAAABjX6BLHkFVMIyByjmyg&quot;
}</code></pre>
<p>스프링 시큐리티에서 제공하는 <code>AbstractAuthenticationProcessingFilter</code>를 구현하여 <code>AntRequestMatcher</code>를 사용하기로 했다.</p>
<p>@Override 해야하는 <code>attemptAuthentication</code> 메서드는 <code>Authentication</code> 객체를 리턴해야했다.
스프링 시큐리티에서 <code>AbstractAuthenticationProcessingFilter</code>를 구현한 클래스들을 참고해봤다.</p>
<p>⇒ Token이란 개념을 사용했다. 간단히 훑어봤을 때, 스프링 시큐리티에서 Token의 사용법은 아래와 같았다.</p>
<ol>
<li><p>Token은 <code>Authentication</code> 인터페이스를 구현한 것이다.(정확하게는 <code>Authentication</code>을 구현한 <code>AbstractAuthenticationToken</code> 상속한다.)</p>
</li>
<li><p>생성자는 '인증되지 않은 토큰' 과 '인증된 토큰'을 생성할 수 있도록 제공했다.</p>
</li>
</ol>
<p>인증 여부는 <code>AbstractAuthenticationToken</code> 의 <code>boolean authenticated</code> 로 알 수 있다.</p>
<p>보통 생성자에 <code>setAuthenticate(false or true);</code> 라인을 추가해줌으로서 세팅된다.</p>
<p>보통 '인증되지 않은 토큰'을 <code>AuthenticationProvider</code>로 넘겨주면 인증 과정을 거치고 나서, '인증된 토큰'을 새로 발급하는 식으로 진행되는 것으로 확인했다.</p>
<p>인증된 토큰은 회원의 정보를 담은 채 <code>AuthenticationManager</code>에게 전달되어 <code>SecurityContext</code>에 저장된다.</p>
<p>위의 내용들을 수행하는 <code>CustomOAuth2ToKen</code> 을 구현해서 '인증되지 않은 토큰'을 생성하고 <code>AuthenticationManager</code>에게 일임하기로 했다.</p>
<p><code>CustomOAuth2ToKen</code> 코드는 다음과 같다.</p>
<pre><code class="language-java">// CustomOAuth2Token.class

public class CustomOAuth2Token extends AbstractAuthenticationToken {

  private CustomUserDetails principal;

  private String registrationId;

  private String authorizationCode;

  private String accessToken;

  /**
   * 인증되지 않은 토큰 생성자
   *
   * @param registrationId    : OAuth2 클라이언트 (e.g &quot;kakao&quot;)
   * @param authorizationCode : 인증 코드
   */
  public CustomOAuth2Token(String registrationId, String authorizationCode) {
    super(Collections.emptyList());
    this.registrationId = registrationId;
    this.authorizationCode = authorizationCode;
    this.setAuthenticated(false); // 인증되지 않은 토큰!
  }

  /**
   * 인증된 토큰 생성자
   *
   * @param principal         : 액세스토큰으로 kakao 에서 가져온 userInfo
   * @param accessToken       : 인증 코드로 OAuth2 클라이언트에 인증되면 발급해주는 액세스토큰.
   */
  public CustomOAuth2Token(CustomUserDetails principal, String accessToken) {
    super(Collections.emptyList());
    this.principal = principal;
    this.accessToken = accessToken;
    this.setAuthenticated(true); // 인증된 토큰!
  }

  @Override
  public Object getCredentials() {
    return &quot;&quot;;
  }

  @Override
  public Object getPrincipal() {
    return this.principal;
  }

  public String getRegistrationId() {
    return this.registrationId;
  }

  public String getAuthorizationCode() {
    return this.authorizationCode;
  }

  public String getAccessToken() {
    return this.accessToken;
  }
}</code></pre>
<p>해당 토큰은 <code>OAuth2LoginFilter</code> 에서 아래와 같이 사용된다.</p>
<pre><code class="language-java">// OAuth2LoginFilter.class

@Slf4j
public class OAuth2LoginFilter extends AbstractAuthenticationProcessingFilter {

  private final ObjectMapper objectMapper;

  public OAuth2LoginFilter(
      RequestMatcher requiresAuthenticationRequestMatcher,
      AuthenticationManager authenticationManager, ObjectMapper objectMapper) {
    super(requiresAuthenticationRequestMatcher, authenticationManager);
    this.objectMapper = objectMapper;
  }

  /**
   * POST &quot;/auth/oauth2/login&quot;, { registrationId(e.g &quot;naver&quot;, &quot;kakao&quot;), authorizationCode(인가 코드) }
   */
  @Override
  public Authentication attemptAuthentication(HttpServletRequest request,
      HttpServletResponse response) throws AuthenticationException, IOException, ServletException {

    // 단순 요청 체크(요청메서드, 요청 컨텐츠 타입)
    if (!POST.name().equals(request.getMethod())) {
      throw new CustomException(ErrorCode.METHOD_NOT_SUPPORTED);
    }

    if (request.getContentType() == null || !request.getContentType()
        .equals(MediaType.APPLICATION_JSON_VALUE)) {
      throw new CustomException(ErrorCode.CONTENT_TYPE_NOT_SUPPORTED);
    }

    // 요청 데이터 매핑
    OAuth2LoginRequest loginRequest = objectMapper.readValue(request.getInputStream(),
        OAuth2LoginRequest.class);

    String registrationId = loginRequest.registrationId();
    String authorizationCode = loginRequest.authorizationCode();

    // '인증되지 않은 토큰'을 생성한다.
    CustomOAuth2Token token = new CustomOAuth2Token(registrationId, authorizationCode);

    // 인증 매니저에게 '인증되지 않은 토큰'을 일임한다.
    // 이 때는 내부적으로 인증 과정이 수행되고 '인증된 토큰'이 생성되어 return 될 것이다.
    return getAuthenticationManager().authenticate(token);
  }

  public record OAuth2LoginRequest(String registrationId, String authorizationCode) {

  }
}</code></pre>
<hr />
<h2 id="authenticationmanager를-어떻게-작성해야할-지에-대한-고민"><strong>'AuthenticationManager'를 어떻게 작성해야할 지에 대한 고민</strong></h2>
<p>스프링 시큐리티에서 <code>ProviderManager</code> 클래스를 제공한다.</p>
<ul>
<li><code>ProviderManager</code> 에는 여러 <code>AuthenticationProvider</code> 를 List로 갖고 있다.</li>
<li><code>Provider</code> 리스트에서 <code>Provider</code>를 하나씩 꺼내서 해당 인증 프로세스에 필요한 인증을 수행하도록 한다.</li>
</ul>
<p>⇒ 결론 : 인증 기능을 수행하는 <code>AuthenticationProvider</code> 를 커스텀해서 <code>ProviderManager</code>에 추가(add)하는 것으로 진행한다.</p>
<hr />
<h2 id="authenticationprovider를-구현한-커스텀-oauth2loginprovider-클래스-생성"><strong>'AuthenticationProvider'를 구현한 커스텀 'OAuth2LoginProvider' 클래스 생성</strong></h2>
<p><code>AuthenticationProvider</code> 를 구현하면 아래의 메서드를 @Override 해야한다.</p>
<pre><code class="language-java">// 참고: @Override만 한 상태이고 내부 코드를 작성하지 않은 상태이다.
//
// 실제 인증을 수행하는 메서드
@Override
public Authentication authenticate(Authentication authentication) throws AuthenticationException {
  return null;
}

// 파라미터로 주어진 authentication(token)이 현재 클래스(Provider)에 의해 인증될 수 있는 지 여부(boolean)를 반환하는 메서드
@Override
public boolean supports(Class&lt;?&gt; authentication) {
  return true;
}</code></pre>
<p>아까 위에서 <code>ProviderManager</code>는 &quot;Provider 리스트를 반복문 돌면서 현재 인증 프로세스에 필요한 <code>Provider</code> 를 찾아 인증을 수행하도록 한다.&quot; 라고 했는데, 이 때 <code>supports(Class&lt;?&gt; authentication)</code> 메서드가 사용된다.</p>
<p>아래에 스프링 시큐리티의 <code>ProviderManager</code> 인증 동작 과정의 일부를 간단하게 가져왔다.</p>
<pre><code class="language-java">// ProviderManager.class 의 authenticate() 일부 발췌

// OAuth2LoginFilter에서 인증 매니저에게 일임한 토큰의 클래스 타입을 의미한다. 
// 나의 경우는 CustomOAuth2Token.class 가 된다.
Class&lt;? extends Authentication&gt; toTest = authentication.getClass(); 

for (AuthenticationProvider provider : getProviders()) {
    if (!provider.supports(toTest)) { // 여기서 supports 메서드가 사용되는 것을 볼 수 있다.
        continue;
    }
    provider.authenticate(authentication);
}

// 코드를 보면 반복문을 돌려 찾은 Provider가 해당 토큰으로 인증 기능을 수행할 수 있으면, 
// provider.authenticate(authentication)을 수행한다.</code></pre>
<p>그래서 이번에 커스텀할 <code>OAuth2LoginProvider</code>의 <code>supports(Class&lt;?&gt; authentication)</code> 메서드는 아래와 같이 작성했다.</p>
<pre><code class="language-java">@Override
public boolean supports(Class&lt;?&gt; authentication) {
  return authentication.isInstance(CustomOAuth2Token.class);
} // 추후에 이 부분 때문에 에러가 발생하였다. 해당 트러블 슈팅은 아래에 작성하였다.</code></pre>
<ul>
<li><p><code>supports</code> 메소드 트러블 슈팅</p>
<p>  <a href="https://www.notion.so/CustomOAuth2Token-Provider-e7524a0c49ed4ec3a72d51471d78d9e9?pvs=21">소셜 로그인 테스트 - CustomOAuth2Token을 지원하는 Provider를 찾을 수 없는 이슈</a> </p>
</li>
</ul>
<p>이제 실제 인증 기능을 수행할 <code>authenticate()</code> 만 작성하면된다.</p>
<p>아래와 같이 작성할 계획을 세웠다.</p>
<pre><code class="language-java">@Override
public Authentication authenticate(Authentication authentication) throws AuthenticationException {

    // 실제 인증 기능 수행
    // 1. '인증되지 않은 토큰'으로 부터 데이터 꺼내기
    CustomOAuth2Token unauthenticatedToken = (CustomOAuth2Token) authentication;
    String registrationId = unauthenticatedToken.getRegistrationId();
    String authorizationCode = unauthenticatedToken.getAuthorizationCode();

    // 데이터(registrationId, 인증코드)를 기반으로 액세스 토큰 요청
    String accessToken = oAuth2Client.generateAccessToken(registrationId, authorizationCode);

    // 2. 받아온 액세스 토큰으로 사용자 정보 요청
    UserInfo userInfo = oAuth2Client.getUserInfo(accessToken);

    // 3. 사용자 정보로 DB에 저장되어 있는 회원이 있는 지 조회
    // - 없으면 DB에 새로 저장 및 조회
    // - 있으면 해당 회원 조회
    Member member = saveOrFindMember(userInfo);

    // 3. 조회한 회원 엔티티로 '인증된 토큰' 생성 및 리턴
    CustomUserDetails principal = CustomUserDetails.fromEntity(member);
    CustomOAuth2Token authenticatedToken = new CustomOAuth2Token(principal, accessToken);
    return authenticatedToken;
}</code></pre>
<hr />
<h2 id="네이버--카카오-소셜-로그인---객체지향-설계에-대한-고민"><strong>네이버 / 카카오 소셜 로그인 -&gt; 객체지향 설계에 대한 고민</strong></h2>
<pre><code class="language-java">/**
 * 아래와 같은 구조로 설계하였다.
 * OAuth2Client -&gt; NaverOAuth2Service -&gt; (OpenFeignClient) NaverClient
 *              -&gt; KakaoOAuth2Service -&gt; (OpenFeignClient) KakaoClient
 * /</code></pre>
<p>이렇게 했을 때, 아래와 같이 <code>if/else-if</code>  가 반복되는 코드가 나왔다.</p>
<pre><code class="language-java">@Component
@RequiredArgsConstructor
public class OAuth2Client {

  private final NaverOAuth2Service naverOAuth2Service;
  private final KakaoOAuth2Service kakaoOAuth2Service;

  public String generateAccessToken(String registrationId, String authorizationCode) {
    String accessToken = null;

        // if , else-if 가 반복된다.
    if (&quot;naver&quot;.equalsIgnoreCase(registrationId)) {
      accessToken = naverOAuth2Service.generateAccessToken(authorizationCode);
    } else if (&quot;kakao&quot;.equalsIgnoreCase(registrationId)) {
      accessToken = kakaoOAuth2Service.generateAccessToken(authorizationCode);
    }
    // 다른 소셜 로그인이 추가된다면, 계속 추가될 것이다.
    // } else if (&quot;google&quot;.equals..)
    return accessToken;
  }

  // 코드 생략...
}</code></pre>
<p>스프링 시큐리티에서는 ClientRegistration 객체를 registrationId를 키 값으로 저장해서 관리하는 것 같았다.</p>
<p><code>Map&lt;registrationId, ClientRegistration&gt;</code>  과 같이 말이다.</p>
<p>(e.g registrationId가 &quot;naver&quot; 이면, yml 파일에 작성된 naver를 기준으로 OAuth2 로그인에 필요한 모든 정보를 ClientRegistration에 저장함으로서 관리하는 것으로 이해했다.)</p>
<p>추후에 참고해서 리팩토링 해볼 생각이다.</p>
<hr />
<h2 id="인증-성공-시-handler-작성"><strong>인증 성공 시 Handler 작성</strong></h2>
<p>일반 로그인 시에 작성해둔 <code>LoginSuccessHandler</code> 를 그대로 사용했다.</p>
<p><code>LoginSuccessHandler</code> 스프링 시큐리티에서 제공하는 <code>AuthenticationSuccessHandler</code>를 구현한 클래스다.</p>
<p>커스텀한 <code>LoginSuccessHandler</code> 에는 ‘<em>액세스토큰과 리프래시토큰을 생성하고, redis에 저장 및 응답 기능’</em>이 구현되어있다.</p>
<p>아래 처럼 세팅 해주면 인증필터에서 수행한 인증이 성공하면 호출된다.</p>
<pre><code class="language-java">// 아래처럼 AbstractAuthenticationProcessingFilter 필터에 set 해줄 수 있다.
@Bean
public OAuth2LoginFilter oAuth2LoginFilter() {
    OAuth2LoginFilter filter = new OAuth2LoginFilter(
    OAUTH2_LOGIN_ANT_PATH_REQUEST_MATCHER, authenticationManager(), objectMapper
);

        // 인증 성공 시 호출될 Handler 세팅
    filter.setAuthenticationSuccessHandler(
        new LoginSuccessHandler(jwtManager, accessTokenRepository, refreshTokenRepository));

    filter.setAuthenticationFailureHandler(new LoginFailureHandler(objectMapper));

    return filter;
}</code></pre>