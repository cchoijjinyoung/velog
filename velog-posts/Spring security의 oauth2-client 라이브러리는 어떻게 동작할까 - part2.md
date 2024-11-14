<blockquote>
<p>저번 블로깅에 이어서 작성하는 내용입니다. 
oauth2-client 라이브러리의 동작을 알기 전에, OAuth2 프로토콜의 authorization code 자격 증명 방법의 흐름에 대해 알면 이해가 쉬울 것이라 생각합니다.</p>
</blockquote>
<h2 id="가볍게-보는-oauth2-client-동작-흐름">가볍게 보는 oauth2-client 동작 흐름</h2>
<ol>
<li>클라이언트의 '구글 로그인' 버튼 클릭<ul>
<li><code>localhost:8080/oauth2/authorize/google?redirect_uri=localhost:11078/oauth</code> 요청</li>
</ul>
</li>
<li>서버는 해당 요청을 받아서 사용자를 구글의 인증 URL로 redirection시킨다.<ul>
<li>위 동작은 <code>OAuth2AuthorizationRequestRedirectFilter</code>가 수행한다.</li>
<li><code>authorizationRequestRepository</code>는 구글로 redirection 되기 직전에 인증 요청을 저장한다.</li>
<li>redirection 할 때, OAuth2 인증 요청에 필요한 여러 정보를 URL에 쿼리 파라미터로 담아 구글로 전달한다.(<code>client_id, redirect_uri, scope, state 등</code>)</li>
<li>구글은 해당 요청에 포함된 <code>client_id, redirect_uri</code>등이 유효한지 검증하고, 사용자가 이미 로그인되어 있는지 확인한다. 로그인되어 있지 않으면, 로그인 폼을 보여준다.</li>
</ul>
</li>
<li>사용자가 구글 로그인 폼에서 로그인에 성공하면, 구글은 쿼리 파라미터에 포함했던 <code>redirect_uri</code>로 리디렉션 시키며, 쿼리 파라미터에 <code>authorization code</code>를 포함하여 redirection 시킨다.</li>
<li>서버는 <code>authorization code</code>를 사용하여  <code>access token</code>을 발급하기 위해 '구글 인증 서버'에 요청을 보낸다.<ul>
<li>이 때, 리디렉션 요청을 <code>OAuth2LoginAutenticationFilter</code>가 가로채고 해당 <code>authorization_code</code>를 구글의 '토큰 발급 uri'로 요청하여 <code>access token</code>을 받게 된다.</li>
<li>스프링 시큐리티에서 'google, github, facebook'의 토큰 발급 uri는 기본적으로 제공해준다. 하지만 'naver'나 'kakao'에 대해서는 제공해주지 않기 때문에 <code>application.yml</code>과 같은 설정파일에 작성해주어야한다.</li>
<li>작성된 정보는 <code>clientRegistration.providerDetails</code>필드에 담긴다.</li>
</ul>
</li>
<li>구글은 <code>authorization code</code>를 검증하고, <code>access token</code>을 <code>Authorization 헤더</code>에 담아 응답한다.</li>
<li>서버는 발급 받은 <code>access token</code>으로 '구글 리소스 서버' 로부터 회원 정보를 요청하고 응답받는다.</li>
</ol>
<hr />
<h2 id="1-oauth2authorizationrequestredirectfilter의-동작-authorization-code-발급">1. OAuth2AuthorizationRequestRedirectFilter의 동작(:= authorization code 발급)</h2>
<p><img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/ae8e9e7c-fbe2-47e9-9498-31161937b7af/image.png" />
대강 흐름을 보면, 들어온 요청을 통해 새로운 request 객체를 생성하고, <code>sendRedirectForAuthorization(~)</code> 을 호출하는 것으로 확인된다.
OAuth2 인증 흐름을 생각해보면, 리소스 서버의 '로그인 폼'으로 redirect 시키는 동작을 할 것으로 예상된다. 예상한 결과가 맞을지 자세히 들여다보자.</p>
<p><code>OAuth2AuthorizationRequest authorizationRequest = this.authorizationRequestResolver.resolve(request);</code>
들어온 요청으로 <code>resolve(request)</code>를 통해 <code>OAuth2AuthorizationRequest</code> 객체를 생성하는 것을 볼 수 있다.</p>
<p><code>resolve</code> 메서드는 무슨 동작을 할 지 살펴보자.</p>
<h3 id="11-defaultoauth2authorizationrequestresolverresolvehttpservletrequest-request">1.1 DefaultOAuth2AuthorizationRequestResolver.resolve(HttpServletRequest request)`</h3>
<p><img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/f68a49ac-b0b6-48e6-b86d-48e018e4efc5/image.png" />
<code>String registrationId = resolveRegistrationId(request);</code>는 요청 파라미터의 <code>registrationId</code>의 값을 가져온다.(e.g &quot;google&quot;)
<code>String redirectUriAction = getAction(request, &quot;login&quot;);</code> 는 내부적으로 action이라는 파라미터가 존재하는지 확인하고, 없으면 &quot;login&quot;을 반환하는 메서드다.</p>
<p>따라서 결국에 <code>resolve(request, &quot;google&quot;, &quot;login&quot;);</code> 을 호출 및 반환하게 된다.
<img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/678ba913-8d8a-4b08-ac2b-443af82e8b21/image.png" /></p>
<p>마지막 <code>resolve</code> 메서드를 살펴보면 <code>OAuth2AuthorizaionRequestBuilder</code> 를 통해 최종적으로 우리가 원하는 request를 만들어내는 것으로 보인다.
주요하게 볼 점은 <code>ClientRegistration</code> 클래스이다.</p>
<p><code>ClientRegistration</code> 객체가 갖는 정보는 <code>clientId, clientSecret, redirectUri</code> 등으로 리소스 서버별 인증 시 필요한 정보들을 담고 있다.(e.g 구글 소셜 로그인 시 필요한 정보들을 담고 있다.)
<code>ClientRegistration</code> 객체는 우리 애플리케이션을 실행할 때, 자동으로 생성되는데,
스프링이 내부적으로 <code>application.yml</code> 설정을 참고하여 생성한다.
만들어진 객체는 <code>ClientRegistrationRepository</code>의 Map에 저장해놓는다. <code>Map&lt;String, ClientRegistration&gt;</code>
Key는 &quot;google&quot; 와 같이 리소스 서버의 이름으로 되어 있다.</p>
<p>돌아와서, 
<img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/ade35ecd-b5fe-4ff9-aae2-62d3018b26df/image.png" /></p>
<p>방금까지 빨간 박스로 씌워진 Request 객체가 생성되는 과정을 보았으니,
<code>this.sendRedirectForAuthorization(request, response, authorizationRequest);</code> 부분을 보자.</p>
<p><img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/5bb29a7e-611a-4b09-820c-dd8b6a330822/image.png" /></p>
<p>우리는 OAuth2의 자격 증명 방식 중 'Authorization Code' 방식을 다루고 있으므로, <code>if</code> 문의 <code>authorizationRequestRepository.saveAuthorizationRequest(authorizationRequest, request, response);</code>라인을 실행할 것이다.
위에 동작 과정에서 잠깐 다뤘듯, <code>authorizationRequestRepository</code>가 요청 정보를 저장하는 것을 확인할 수 있다.</p>
<p>내부 동작을 더 자세하게 보면,</p>
<p><img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/8274ddbe-d00a-4cd7-9534-2a12b856950b/image.png" /></p>
<p><code>request.getSession().setAttribute(this.sessionAttributeName, authorizationRequest);</code> 로 요청 정보를 세션에 저장하게 된다.
추후에 세션에서 저장된 요청 정보를 가져와서 <code>state</code> 파라미터로 유효한 요청인지 검증하게 된다.</p>
<p>이렇게 요청 정보를 세션에 저장하고 난 뒤, 이전 메서드로 돌아가면 <code>authorizationRequest</code>의 <code>authorizationRequestUri</code>로 리디렉션시키는데,
해당 Uri에서 구글은 <code>clientId, redirectUri</code> 등을 검증하고 유효하면 사용자를 로그인 폼으로 리디렉션 시키게 된다.</p>
<p>사용자는 로그인 폼에서 아이디/패스워드를 입력해서 로그인을 하게되고, 구글은 사용자가 로그인에 성공하면, <code>authorization_code</code>를 쿼리 파라미터에 포함시켜 우리 서버의<code>redirect_uri</code>로 리디렉션시킨다.</p>
<h2 id="2-oauth2loginauthenticationfilter의-동작-access-token-발급">2. OAuth2LoginAuthenticationFilter의 동작(:= access token 발급)</h2>
<p><code>OAuth2LoginAuthenticationFilter</code>는 <code>redirect_url + authorziation_code</code> 로 들어오는 요청을 가로채는 필터이다.
이 필터에서는 <code>authorization_code</code>를 통해 리소스 서버로부터 <code>access_token</code>을 발급해오는 과정이 진행된다.</p>
<p>필터의 수행을 쭉 살펴보면, 
첫번째로는 <code>request</code>와 <code>response</code>를 검증하게되는데, <code>response</code>가 <code>null</code>일 땐,<code>IllegalArgumentException</code>을 던진다.
<code>request</code>에 대해서 검증을 할 때는 <code>code, state, error, state</code> 라는 파라미터가 포함되는지 확인한 후, 우리가 세션에 저장했던 요청 정보를 통해 안전한 요청인지 검증하게 된다.</p>
<blockquote>
<p><code>code</code>파라미터는 우리가 발급받은 <code>authorization_code</code>를 담고 있는 파라미터 이름이다.</p>
<p><code>request</code> 검증 시, <code>state</code>라는 파라미터가 존재해야하고, 존재한다면 이번 요청의 <code>state</code>와 세션의 <code>state</code>가 같아야만 한다.</p>
</blockquote>
<p>요청의 검증을 완료했으면, <code>authorization_code</code>를 발급받아올 때, 세션에 저장해뒀던 정보 안에 존재하는 <code>registrationId(e.g &quot;google&quot;)</code>를 통해 <code>ClientRegistration(소셜 로그인 시 필요한 정보를 담은 클래스)</code> 객체를 찾아온다.(존재하지 않을 시, <code>throw CLIENT_REGISTRATION_NOT_FOUND_ERROR</code>)</p>
<p>이제 <code>authorization_code</code>로 <code>access_token</code>을 발급받아오기 위해, 또 새로운 <code>request</code>를 만들어낼 차례이다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/950b0064-4b73-42cf-a8be-4d818733d450/image.png" /></p>
<p>코드를 보면, <code>redirectUri</code>와 <code>OAuth2AuthorizationResponse</code> 객체로 <code>OAuth2LoginAuthenticationToken</code>을 생성한다.</p>
<blockquote>
<p>스프링 시큐리티에서 <code>Token</code>은 인증을 위한 서류라고 생각하면 쉽다. 또한 이 서류는 '아직 인증되지 않은 서류'와 '인증된 서류'로 생성자가 나뉜다.
그래서 스프링 시큐리티의 인증 흐름은 '인증되지 않은 토큰'을 생성 후에 '인증 Provider'에게 해당 서류를 넘겨 검토를 받은 후 '인증된 서류'로 새롭게 생성되는 과정이 동반된다.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/f1dd0126-f3ad-4070-9dc6-6c5e243a6b4c/image.png" /></p>
<p>내부 로직을 파고들면 아래의 클래스들이 나온다. 주요 클래스 및 기능만 가져왔다.</p>
<h3 id="21-oauth2loginauthenticationprovider">2.1 OAuth2LoginAuthenticationProvider</h3>
<ul>
<li><code>access_token</code> 발급과 <code>userInfo</code>를 조회해오는 클래스이다.</li>
<li><code>access_token</code>의 발급은 내부에서 실질적으로 <code>OAuth2AuthorizationCodeAuthenticationProvider</code> 클래스가 수행하게된다.</li>
<li>access token <code>OAuth2AccessToken</code>이라는 클래스로 관리되는데, 이 클래스는 <code>Set&lt;String&gt; scopes</code>라는 필드를 갖고 있다. <code>scope</code>는 사용자의 <code>profile, email</code>등을 나타낸다. 즉, 해당 토큰이 <code>scopes</code>에 접근할 수 있는 토큰임을 의미한다.</li>
</ul>
<h3 id="22-defaultoauth2userserviceloaduseroauth2userrequest-userrequest">2.2 DefaultOAuth2UserService.loadUser(OAuth2UserRequest userRequest)</h3>
<ul>
<li><code>OAuth2LoginAuthenticationProvider</code> 내부에서 <code>access_token</code>으로 <code>userInfo</code>를 실질적으로 조회해오는 클래스 및 메서드</li>
<li>스프링 시큐리티는 외부 API를 호출할 때, RequestEntity와 RestTemplate를 사용한다. </li>
<li>우리가 지금껏 만들어낸 Request 객체를 RequestEntity로 convert한 후 RestTemplate를 사용하여 외부 API를 호출하게된다.(<code>restOperations.exchange(request);</code>)</li>
<li>(참고) 내부 코드를 파고든다면, <code>userNameAttributeName</code>이라는 변수명을 볼 수 있다. 이는 각 리소스 서버별로 사용자의 고유 식별자를 담고 있는 필드 이름을 제공하는데, 그 필드 이름을 의미한다.(e.g 구글 : &quot;sub&quot;, 카카오 : &quot;id&quot;)</li>
</ul>
<p>마지막으로 다시 필터로 돌아가보자.</p>
<p><img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/9c355af7-8f8b-44ec-af54-8fdbba434428/image.png" /></p>
<p>지금까지 <code>authorization_code</code>와 다른 요청정보로 <code>access_token</code>과 <code>userInfo</code>를 가져왔다.
마지막 코드는 토큰들을 포함한 인증 정보를 저장하는 코드인데, <code>access_token</code>이 유효한 동안은 추가 인증 없이 계속해서 리소스 서버에 <code>userInfo</code>를 요청할 수 있게 된다.</p>