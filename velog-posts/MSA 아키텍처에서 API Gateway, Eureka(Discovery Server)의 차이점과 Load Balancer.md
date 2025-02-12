<p>마이크로서비스 아키텍처(MSA)를 설계할 때, <strong>확장성</strong>과 <strong>유지보수성</strong>을 위해 각 구성 요소의 역할을 명확히 하는 것이 중요합니다. 이번 글에서는 API Gateway, Eureka(Discovery Server), Load Balancer(LB), 그리고 클라이언트가 각각 어떤 목적을 가지고 있으며, 이들이 어떻게 상호 작용하여 확장성 있는 시스템을 구성하는지 정리해보겠습니다.</p>
<hr />
<h2 id="1-api-gateway와-eureka의-차이점-및-역할-구분">1. API Gateway와 Eureka의 차이점 및 역할 구분</h2>
<h3 id="11-api-gateway">1.1 API Gateway</h3>
<ul>
<li><p>주소 추상화 및 라우팅:</p>
<ul>
<li><p>클라이언트가 직접 각 서비스의 포트나 주소를 알 필요 없이, <a href="http://api-gateway/">http://api-gateway/</a> 등의 단일 엔드포인트로 접근할 수 있도록 합니다.</p>
</li>
<li><p>예를 들어, ProductService의 실제 주소가 <code>http://localhost:9091</code>이라 하더라도, Gateway에서 <code>http://product-service</code>로 매핑해주면, Front-end 개발자분들은 포트 변경이나 인스턴스 스케일아웃에 대해 신경 쓸 필요가 없습니다.</p>
</li>
</ul>
</li>
<li><p>부가 기능 제공:</p>
<ul>
<li><p>인증/인가: 잘못된 요청은 내부 서비스로 전달되지 않고 Gateway에서 바로 401/403 응답 처리</p>
</li>
<li><p>캐싱: 빈번하지만 변경이 적은 데이터를 Gateway에서 캐싱하여 응답 속도 개선</p>
</li>
<li><p>로깅, 모니터링 등 추가 기능을 제공하여 서비스의 안정성을 높입니다.</p>
</li>
</ul>
</li>
</ul>
<h3 id="12-eureka-discovery-server">1.2 Eureka (Discovery Server)</h3>
<ul>
<li><p>서비스 등록 및 상태 모니터링:</p>
<ul>
<li><p>각 서비스는 자신이 어느 포트에서 실행되고 있는지, 그리고 정상적인 상태(UP/DOWN)를 Eureka에 등록합니다.</p>
</li>
<li><p>Eureka 서버는 등록된 서비스들의 정보를 실시간으로 관리하며, 다른 서비스가 요청 시 Eureka를 통해 최신 정보를 받아올 수 있습니다.</p>
</li>
</ul>
</li>
<li><p>동적 서비스 검색:</p>
<ul>
<li>내부 서비스(예: order-service)가 product-service에 요청을 보내야 할 때, Eureka에서 현재 사용 가능한 인스턴스의 정보를 조회하고 적절한 인스턴스를 선택할 수 있습니다.</li>
</ul>
</li>
</ul>
<hr />
<h2 id="2-feignclient와-load-balancer-ribbon-→-spring-cloud-loadbalancer">2. FeignClient와 Load Balancer (Ribbon → Spring Cloud LoadBalancer)</h2>
<h3 id="21-feignclient">2.1 FeignClient</h3>
<p><strong>목적:</strong>  </p>
<ul>
<li><code>FeignClient</code>는 MSA에서 마이크로서비스 간의 HTTP 요청을 보다 쉽게 처리할 수 있도록 해주는 선언적(Declarative) REST 클라이언트입니다.</li>
<li>내부 서비스 간 통신을 단순화하며, Eureka와 연동하여 동적으로 서비스 인스턴스를 찾을 수 있습니다.</li>
<li><strong>Spring Cloud 2020 이전에는 Netflix Ribbon을 사용하여 로드밸런싱을 수행했지만, 이후 버전에서는 Spring Cloud LoadBalancer가 기본 로드밸런서로 사용됩니다.</strong></li>
</ul>
<p><strong>예제:</strong></p>
<pre><code class="language-java">@FeignClient(name = &quot;product-service&quot;)
public interface ProductClient {
    @GetMapping(&quot;/products/{id}&quot;)
    ProductResponse getProductById(@PathVariable(&quot;id&quot;) Long id);
}</code></pre>
<ul>
<li><code>name = &quot;product-service&quot;</code>를 설정하면, <code>product-service</code>의 인스턴스를 Eureka에서 자동으로 조회하여 로드밸런싱을 수행합니다.</li>
</ul>
<h3 id="22-load-balancer-ribbon-→-spring-cloud-loadbalancer">2.2 Load Balancer (Ribbon → Spring Cloud LoadBalancer)</h3>
<p><strong>목적:</strong>  </p>
<ul>
<li>여러 인스턴스로 구성된 동일 서비스(예: 여러 개의 <code>product-service</code> 인스턴스) 사이에 요청을 분산  </li>
<li>과부하를 방지하고, 장애가 발생한 인스턴스 대신 정상적인 인스턴스로 요청을 라우팅  </li>
</ul>
<h3 id="spring-cloud-2020-이전-ribbon-사용-시"><strong>Spring Cloud 2020 이전 (Ribbon 사용 시)</strong></h3>
<p>기존 Spring Cloud에서 <strong>FeignClient는 기본적으로 Ribbon을 사용하여 로드밸런싱</strong>을 수행했습니다.</p>
<pre><code class="language-java">@FeignClient(name = &quot;product-service&quot;)
public interface ProductClient {
    @GetMapping(&quot;/products/{id}&quot;)
    ProductResponse getProductById(@PathVariable(&quot;id&quot;) Long id);
}</code></pre>
<p>✔️ Ribbon이 Eureka와 연동되어 자동으로 서비스 인스턴스를 조회하고 로드밸런싱을 수행했습니다.</p>
<h3 id="spring-cloud-2020-이후-spring-cloud-loadbalancer-적용"><strong>Spring Cloud 2020 이후 (Spring Cloud LoadBalancer 적용)</strong></h3>
<p>Ribbon이 Spring Cloud에서 제거되었으며, 대신 <strong>Spring Cloud LoadBalancer가 기본 로드밸런서로 적용</strong>되었습니다.</p>
<pre><code class="language-yaml">spring:
  cloud:
    loadbalancer:
      ribbon:
        enabled: false  
                # true =&gt; Ribbon 활성화</code></pre>
<p>✔️ Ribbon을 비활성화하고, Spring Cloud LoadBalancer를 기본적으로 사용하도록 변경되었습니다.</p>
<h3 id="spring-cloud-loadbalancer-사용-예제"><strong>Spring Cloud LoadBalancer 사용 예제</strong></h3>
<pre><code class="language-java">@Bean
@LoadBalanced
public RestTemplate restTemplate() {
    return new RestTemplate();
}</code></pre>
<p>✔️ <code>@LoadBalanced</code>를 붙이면 <code>RestTemplate</code>이 Eureka에서 서비스 인스턴스를 조회하여 로드밸런싱을 수행합니다.</p>
<blockquote>
<p><strong>참고:</strong> API Gateway도 간단한 로드밸런싱 기능을 제공할 수 있으나, Gateway의 본래 목적은 클라이언트 요청 라우팅과 인증/인가, 캐싱 등 부가 기능에 집중하는 것입니다. 따라서, 본격적인 부하 분산은 전용 LB가 맡는 것이 좋습니다.</p>
</blockquote>
<hr />
<h2 id="3-결론">3. 결론</h2>
<p>MSA 환경에서 API Gateway, Eureka(Discovery Server), Load Balancer는 각자의 목적에 맞게 역할을 수행하며, 이를 적절히 활용하면 확장성과 유지보수성이 뛰어난 시스템을 구축할 수 있습니다. </p>
<ul>
<li><strong>API Gateway</strong>는 클라이언트 요청을 최적화하고, </li>
<li><strong>Eureka</strong>는 내부 서비스들의 위치 및 상태를 관리하며, </li>
<li><strong>Load Balancer</strong>는 부하를 효율적으로 분산합니다. </li>
<li><strong>Spring Cloud 2020 이후 Ribbon이 제거되었으며, Spring Cloud LoadBalancer가 기본 로드밸런서 역할을 수행합니다.</strong></li>
</ul>
<p>이를 통해 안정적이고 확장 가능한 MSA 아키텍처를 설계할 수 있습니다.</p>