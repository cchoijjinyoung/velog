<p>오늘은 예전에 끝마친 프로젝트를 리팩토링해보았다!
최근에 진행 중인 프로젝트에서 스프링 빈 리스트 주입을 사용해서 OCP를 해결했었는데, 팀 프로젝트에서 진행했던 내 안티패턴이 생각나서 바로 고치고자 했다.</p>
<p>리팩토링 하기 전에 기존 구조를 그림으로 가져와봤다.
<img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/e7d330d0-437e-4496-b2f8-c6ee81aef63e/image.png" />
현재 내 <code>OAuth2Client</code>는 <code>request</code>로부터 넘어온 <code>registrationId(e.g &quot;naver&quot;)</code> 값을 받아서 리소스 서버에 맞는 <code>Service</code>를 호출한다.
그런데, 우리 애플리케이션에 새로운 리소스 서버가 추가될 때마다, <code>else if</code>문을 돌려가며 찾아야만 했다.</p>
<p>리팩토링 전 코드는 아래와 같다.</p>
<pre><code class="language-java">@Component
@RequiredArgsConstructor
public class OAuth2Client {

  private final NaverOAuth2Service naverOAuth2Service;
  private final KakaoOAuth2Service kakaoOAuth2Service;

  public String generateAccessToken(String registrationId, String authorizationCode) {
    String accessToken = null;

    if (NAVER.name().equalsIgnoreCase(registrationId)) {
      accessToken = naverOAuth2Service.generateAccessToken(authorizationCode);
    } else if (KAKAO.name().equalsIgnoreCase(registrationId)) {
      accessToken = kakaoOAuth2Service.generateAccessToken(authorizationCode);
    }
    return accessToken;
  }

  public OAuth2UserInfo getUserInfo(String registrationId, String accessToken) {
    OAuth2UserInfo userInfo = null;

    if (NAVER.name().equalsIgnoreCase(registrationId)) {
      userInfo = naverOAuth2Service.getUserInfo(accessToken);
    } else if (KAKAO.name().equalsIgnoreCase(registrationId)) {
      userInfo = kakaoOAuth2Service.getUserInfo(accessToken);
    }
    return userInfo;
  }
}</code></pre>
<p>우리 팀프로젝트의 소셜로그인은 당장은 네이버와 카카오만 지원하고 있다.</p>
<p>위에서 얘기했듯이, 앞으로 다른 리소스 서버도 지원하게 된다면, 위 <code>OAuth2Client</code> 클래스에 <code>else if</code>문을 매번 추가해야하는 상황이므로 <strong>OCP</strong>를 위반하게 된다.</p>
<p>이를 해결하기 위해 스프링 빈 리스트 주입을 사용했고, 그러기 위해서 <code>if/else-if</code>문에서 사용되는<code>NaverOAuth2Service</code>와 <code>KakaoOAuth2Service</code>를 아래 <code>OAuth2Service</code> 인터페이스의 구현체로써 묶어주었다.</p>
<pre><code class="language-java">public interface OAuth2Service {

  String generateAccessToken(String authorizationCode);

  OAuth2UserInfo getUserInfo(String accessToken);

  String supports();

}</code></pre>
<p>그리고 <code>OAuth2Service</code>에 <code>supports()</code> 메소드를 둠으로써, 각각의 구현체가 어떤 리소스 서버를 지원하는지 알 수 있도록 작성해주었다.</p>
<p>예를 들면, <code>NaverOAuth2Service</code> 같은 경우 다음과 같이 작성된다.</p>
<pre><code class="language-java">@Service
public class NaverOAuth2Service implements OAuth2Service {

  @Override
  public String supports() {
    return LoginType.NAVER.name(); // 해당 클래스가 네이버 로그인을 지원하는 서비스임을 알려줄 수 있다.
  }

  public String generateAccessToken(String authorizationCode) {
    // 생략
  }

  public OAuth2UserInfo getUserInfo(String accessToken) {
    // 생략
  }
}
</code></pre>
<p>따라서, 기존에 <code>if/else-if</code>문이 남발될 수 있었던 <code>OAuth2Client</code> 클래스는 아래와 같이 수정하면 된다.</p>
<pre><code class="language-java">@Component
@RequiredArgsConstructor
public class OAuth2Client {

  private final List&lt;OAuth2Service&gt; oAuth2Services;

  public String generateAccessToken(String registrationId, String authorizationCode) {
    String accessToken = null;

    for (OAuth2Service service : oAuth2Services) {
      if (registrationId.equals(service.supports())) {
        accessToken = service.generateAccessToken(authorizationCode);
      }
    }
    return accessToken;
  }

  public OAuth2UserInfo getUserInfo(String registrationId, String accessToken) {
    OAuth2UserInfo userInfo = null;

    for (OAuth2Service service : oAuth2Services) {
      if (registrationId.equals(service.supports())) {
        userInfo = service.getUserInfo(accessToken);
      }
    }
    return userInfo;
  }
}</code></pre>
<p>이렇게 작성하면 다른 소셜 로그인이 추가된다고 해도, <code>OAuth2Client</code>는 수정할 필요가 없게 된다.</p>
<p>그림으로 보면 아래처럼 된다!<img alt="" src="https://velog.velcdn.com/images/cchoijjinyoung/post/e5279baf-a71b-4902-a311-ca6afbf090bd/image.png" /></p>
<p>스프링 빈 리스트 주입은 정말 유용한 것 같다. 한 번 알게되니까 빈번히 사용하게 된다.
팀프로젝트를 진행하면서 정말 맘에 걸렸던 부분인데, 이렇게 간단하게 해결할 수 있었다는 것도 허무하긴 하지만, 뒤늦게라도 해결해서 뿌듯한 마음이 크다.</p>