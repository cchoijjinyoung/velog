<h2 id="1-api-버전-명시의-단점">1. API 버전 명시의 단점</h2>
<h3 id="11-개요">1.1 개요</h3>
<p>구글링을 하다보면 <code>/api/v1/members/</code> 와 같이 앤드포인트를 작성하는 것을 볼 수 있다.
<code>v1 or v2</code> 처럼 버전을 명시하는 것이 과연 좋은 방법일까?를 주제로 고민을 해보았다.</p>
<h3 id="12-버전-운영-시-고려해야-할-점">1.2 버전 운영 시 고려해야 할 점</h3>
<p>API 버전이 <code>v1</code>, <code>v2</code> 처럼 여러개 존재하는 경우, 두 버전을 동시에 운영해야 한다. 이때, 다음과 같은 문제들이 발생할 수 있다.</p>
<ol>
<li>두 버전이 동일한 리소스(e.g repository)를 공유할 경우 서로에게 영향을 미칠 가능성</li>
</ol>
<ul>
<li><code>v1</code>과 <code>v2</code>가 같은 데이터베이스를 바라보거나, 동일한 내부 서비스를 사용한다면 한쪽의 변경이 다른 버전에 의도치 않은 영향을 줄 수 있다. 따라서, 운영적으로 복잡해질 우려가 있다.</li>
</ul>
<ol start="2">
<li>API 버전의 라이프사이클 관리</li>
</ol>
<ul>
<li>운영 중인 API의 이전 버전(v1)을 언젠가는 만료시켜야한다. 하지만, 이를 즉시 중단할 경우 여전히 <code>v1</code>을 사용하는 클라이언트에게 문제가 발생할 수 있다.</li>
<li>기존 버전을 종료하기 위해서는 해당 버전을 어떤 클라이언트가 사용하고 있는지 지속적으로 <strong>추적</strong>해야한다. 이는 마찬가지로 운영 측면에서 부담이 된다.</li>
</ul>
<p>버전이 만약 <code>v1, v2, v3, v4 ...</code> 와 같이 된다면 부담은 더 커질 것이다. 백엔드 개발자에게 역할을 분담할 때도 버전별로 역할을 맡게 될 수도 있고, 이를 일일이 지정해야한다.</p>
<hr />
<h2 id="2-주문-상세-조회-api-고찰">2. 주문 상세 조회 API 고찰</h2>
<p>주문 상세 조회 API를 개발해야했다. 테이블은 간략하게 <code>Order</code>와 <code>Menu</code>가 있고, 서로는 다대다 관계이기 때문에 <code>Order_menu</code>라는 중간 테이블을 두고자 했다.</p>
<p><code>Order_menu</code> 테이블은 현재 주문에 어떤 메뉴들이 몇개 주문됐는지를 나타낼 수 있어야했다. 그래서 <code>order_id</code>, <code>menu_id</code>, <code>quantity</code> 컬럼을 갖도록 했다.</p>
<p>그리고 주문 상세에서 menu의 가격을 <code>Order_menu</code>를 조인해서 menuId로 <code>price</code>를 조회해오면 될 것이라고 생각했다.</p>
<p>하지만 조금 더 고민해보니, 오늘 시킨 짜장면은 7000원이였는데, 내일 8000원으로 가격이 바뀐다면?</p>
<p>주문의 메뉴별 가격을 실시간으로 <code>menuId</code>로 조회해오기 때문에, 실제 주문했을 때의 가격이 아니라 최신 가격으로 매번 영수증이 바뀔 수 있다.</p>
<p>그래서 <code>Order_menu</code> 테이블에 <code>menu_price</code> 컬럼도 추가해서 '주문 시 메뉴 가격'을 나타내도록 했다.</p>
<p>이에 대해서는 <strong>'정규화, 반정규화, 역정규화'</strong> 에 대해서 더 공부해보고 추후에 포스팅해보고자 한다.</p>
<hr />
<h2 id="3-로컬-db-세팅-by-docket-compose">3. 로컬 DB 세팅 (by docket-compose)</h2>
<h3 id="31-개요">3.1 개요</h3>
<p>docker compose를 사용해서 로컬 DB 환경을 postgresql으로 세팅하고자 했다.</p>
<p>DB배포를 AWS로 할 계획이었기 때문에, AWS에서 기본적으로 지원하는 버전인 16.3으로 선택했다. 작성한 <code>docker-compose.yml</code>은 아래와 같다.</p>
<pre><code class="language-yml">version: '3.8'

services:
  db:
    image: postgres:16.3
    container_name: postgresql
    restart: always
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
      POSTGRES_DB: mydatabase
    ports:
      - &quot;5432:5432&quot;
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:</code></pre>
<p><code>docker compose up</code>을 한 후 인텔리제이에서 DB Test Connection을 했더니 당연히도 잘 돼었고, <code>SpringApplication</code>도 정상적으로 잘 실행됐다.</p>
<h3 id="32-트러블-슈팅">3.2 트러블 슈팅</h3>
<p>그 후에 <code>POSTGRES_USER</code>, <code>POSTGRES_PASSWORD</code>, <code>POSTGRES_DB</code> 값이 마음에 들지 않아서 각각 <code>admin</code>, <code>1234</code>, <code>testdb</code>로 변경하고 다시 <code>docker compose up</code>을 했다.</p>
<p>문제는 여기서 발생했다.</p>
<p>기존에 잘 연결되던 Test Connection이 Faild Error가 났다.
<code>docker compose up</code>을 해봤자, 이전에 새로운 <code>up</code>하는게아니었기 때문이다.</p>
<p>따라서 해결하기 위해서는 <code>docker compose down -v</code>를 해줌으로써 docker-compose를 down 시킴과 동시에 volumn을 삭제해주어야했다.</p>
<p>그 이후 <code>docker compose up</code>을 해준 뒤 다시 Test Connection하면 성공하게 된다.</p>
<p>참고로 <code>docker compose up -d</code> 커멘드를 사용하면, 백그라운드에서 <code>up</code> 시킬 수 있다.</p>
<h3 id="33-번외">3.3 번외</h3>
<p><code>brew install postgresql</code>을 통해 로컬에 postgresql을 직접 설치했다면, docker-compose의 postgresql보다 로컬의 postgresql에 먼저 접근될 것이다.</p>
<p>만약 Test Connection에서 db를 찾을 수 없다거나 하는 오류가 발생한다면, 5432 포트에서 실행되고 있는 프로세스를 확인 후에 <code>kill -9</code> 명령어를 통해 다운시키고 다시 해보자.</p>
<p>docker-compose로 db를 구성할거라면 brew로 설치한 postgresql을 uninsall하는 것도 방법이다.</p>
<p>다만, 이미 로컬의 postgresql이 실행중인 상태라면 종료시킨 후에 uninstall해야 정상적으로 삭제된다.</p>
<p>brew의 내부 동작에 대해서는 정확하게 알진 못하지만, postgresql이 실행중인데 <code>brew uninstall postgresql</code>을 하면 brew에서 postgresql이 종료될 때까지 삭제를 안하는 것 같다.</p>
<p align="center">
<img alt="썸네일 이미지" src="https://velog.velcdn.com/images/cchoijjinyoung/post/34c7254c-442b-4dfd-b965-96d80928d669/image.png" width="400" />
  </p>