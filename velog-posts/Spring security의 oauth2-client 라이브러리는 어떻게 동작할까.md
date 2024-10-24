<p>oauth2-client에 대해 알아보기전에, OAuth란 무엇일지부터 간단하게 알아보자.</p>
<h2 id="1-oauth란-무엇일까">1. OAuth란 무엇일까?</h2>
<p>웹사이트를 이용하다보면, 소셜 로그인(e.g 구글 로그인)을 통해 해당 사이트에서 별도의 회원가입을 하지 않고도 로그인 할 수 있다.</p>
<p>위처럼 웹사이트를 이용할 때 구글과 같은 제 3의 서비스에 저장된 사용자의 정보를 가져와서 로그인할 수 있는 기능은 OAuth라는 프로토콜(약속)을 통해 구현된다.</p>
<h3 id="11-oauth-동작에-관여하는-참여자들">1.1. OAuth 동작에 관여하는 참여자들</h3>
<ul>
<li><strong>Resource Server</strong> : Client가 제어하고자 하는 자원을 보유하고 있는 서버(e.g 구글)</li>
<li><strong>Resource Owner</strong> : 자원의 소유자(Client에 로그인하는 실제 사용자)</li>
<li><strong>Client</strong> : Resource Server에 접속해서 사용자 정보를 가져오고자 하는 웹 애플리케이션</li>
</ul>
<h3 id="12-client가-resource-server를-이용하기-위해서-어떻게-해야하는가">1.2. Client가 Resource Server를 이용하기 위해서 어떻게 해야하는가?</h3>
<p>자신의 서비스를 Resource Server에 등록함으로써, 사전 승인을 받아야한다. 등록을 하게 되면 Resource Server로부터 아래의 세 가지 정보를 받을 수 있다.</p>
<ul>
<li><strong>Client ID</strong> : Resource Server에서 Client를 식별할 수 있는 식별자</li>
<li><strong>Client Secret</strong> : Client ID에 대한 비밀키로, 절대 노출되어서는 안된다.</li>
<li><strong>Authorized redirect URL</strong> : Authorization Code를 전달받을 리다이렉트 주소</li>
</ul>
<h3 id="13-authorization-code란-무엇이고-위-세-가지-정보는-어떻게-활용되는걸까">1.3. Authorization Code란 무엇이고, 위 세 가지 정보는 어떻게 활용되는걸까?</h3>
<p>구글과 같은 Resource Server를 통해 인증을 마치면, Resource Server는 Client를 명시된 주소로(Authorization redirect URL)로 redirect 시킨다.</p>
<ul>
<li>이 때 등록되지 않은 Authorization redirect URL을 사용하는 경우, Resource Server가 인증을 거부한다.</li>
<li>등록되어있는 redirect URL이라면 해당 주소의 Query String으로 <strong>'Authorization Code'</strong>를 함께 전달한다.</li>
<li>클라이언트는 Authorization Code와 Client ID 및 Client Secret으로 Resource Server에게 다시 요청을 보내는데, 이 때는 <strong>'Access Token'</strong>을 발급 받게 된다.</li>
<li>마지막으로, Client는 발급받은 Access Token을 통해 Resource Server로부터 사용자의 정보에 접근할 수 있게 된다.</li>
</ul>
<blockquote>
<p>결국 Client는 Resource Server로부터 Access Token을 발급받아야 사용자 정보에 접근할 수 있게된다.
이 때, Access Token을 발급 받는 방식(권한 부여 방식 : Grant type)은 여러가지가 있는데, 이 포스팅에서는 <strong>'Authorization Code Grant type'</strong> 만 다루려고 한다.</p>
</blockquote>
<h3 id="14-authorization-code-grant-type">1.4. Authorization Code Grant type</h3>
<p>사용자의 승인 하에, Authorization Code를 발급받고, 해당 Code를 이용해서 Access Token을 발급하는 방식이다.</p>
<h2 id="2-다시-보는-flow">2. 다시 보는 Flow</h2>
<ol>
<li><p>Client(소셜 로그인을 제공할 웹애플리케이션)가 Resource Server(e.g 구글)에 자신의 서비스를 등록함으로써 사전 승인을 받는다.</p>
</li>
<li><p>Resource Server는 Client에게 Client ID, Client Secret, redirect URL 정보를 알려준다.</p>
</li>
<li><p>사용자가 Client에서 소셜 로그인을 버튼을 클릭하면, 사용자는 Resource Server에 접속하여 로그인을 수행하게 된다.</p>
<img alt="이미지 설명" src="https://velog.velcdn.com/images/cchoijjinyoung/post/82cfebbe-75dd-48c7-9a5a-f2610b304f93/image.png" width="400" />
</li>
<li><p>로그인을 완료하면 Resource Server는 쿼리스트링으로 넘어온 파라미터들을 통해 Client를 검증한다.</p>
<ul>
<li>파라미터의 Client ID와 동일한 ID가 서버에 존재하는지 확인</li>
<li>해당 Client ID에 해당하는 Redirect URL이 파라미터로 전달된 Redirect URL과 일치하는지 확인</li>
</ul>
</li>
<li><p>Client 검증을 완료했으면, Resource Server는 사용자에게 해당 Client에게 권한을 부여해도 될 지 물어본다.</p>
<img alt="이미지 설명" src="https://velog.velcdn.com/images/cchoijjinyoung/post/41f05a24-7e9d-487f-bdf9-d3b0b954cb52/image.png" width="400" />
</li>
<li><p>사용자가 권한 접근에 대해 허용하면 Resource Server는 Client의 접근을 승인하게 된다.</p>
</li>
<li><p>승인이 마무리되면, 명시된 Redirect URL로 Client를 리다이렉트 시킨다.</p>
</li>
<li><p>이 때, Resource Server는 <strong>'Authorization Code'</strong> 를 같이 발급한다.</p>
</li>
<li><p>Client는 Resource Server에게 Client ID, Client Secret, Authorization Code를 통해 <strong>'Access Token'</strong> 을 발급받게 된다.</p>
</li>
<li><p>Client는 Token을 서버에 저장해두고, Resource Server의 자원을 사용하기 위한 API 호출 시 해당 토큰을 헤더에 담아 보낸다.</p>
</li>
<li><p>Client는 Resource Server의 자원들을 사용할 수 있게된다.</p>
</li>
</ol>