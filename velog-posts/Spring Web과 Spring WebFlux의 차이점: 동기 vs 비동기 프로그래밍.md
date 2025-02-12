<h2 id="1-spring-web과-spring-webflux-개요">1. Spring Web과 Spring WebFlux 개요</h2>
<p>Spring 기반의 웹 애플리케이션을 개발할 때 <code>Spring Web (Spring MVC)</code>과 <code>Spring WebFlux</code> 중 어떤 것을 선택해야 할지 고민한 적이 있나요? 두 기술은 웹 애플리케이션을 만들 수 있는 강력한 도구이지만, 동작 방식이 다릅니다. 이 글에서는 둘의 동작 방식의 차이를 다뤄보고자 합니다.</p>
<h2 id="2-spring-web-spring-mvc-vs-spring-webflux-차이점">2. Spring Web (Spring MVC) vs Spring WebFlux 차이점</h2>
<h3 id="21-블로킹-vs-논블로킹">2.1 블로킹 vs 논블로킹</h3>
<table>
<thead>
<tr>
<th>특징</th>
<th>Spring Web (MVC)</th>
<th>Spring WebFlux</th>
</tr>
</thead>
<tbody><tr>
<td>기본 실행 방식</td>
<td>동기(Blocking)</td>
<td>비동기(Non-Blocking)</td>
</tr>
<tr>
<td>기반 기술</td>
<td>Servlet API (Thread-per-request)</td>
<td>Reactive Streams (Event-driven)</td>
</tr>
<tr>
<td>기본 컨테이너</td>
<td>Tomcat, Jetty 등</td>
<td>Netty, Undertow 등</td>
</tr>
<tr>
<td>데이터 처리</td>
<td>동기 방식 (Blocking I/O)</td>
<td>리액티브 스트림 기반 (Non-Blocking I/O)</td>
</tr>
</tbody></table>
<p>Spring Web은 전통적인 MVC 패턴을 사용하며 요청이 들어오면 스레드가 하나 할당되어 작업을 수행합니다. 반면, WebFlux는 논블로킹 방식으로 이벤트 기반 처리 모델을 따릅니다.</p>
<h3 id="22-스레드-모델-차이">2.2 스레드 모델 차이</h3>
<h4 id="동기-방식의-스레드-동작">동기 방식의 스레드 동작</h4>
<p>동기 방식(Spring Web)에서는 요청이 들어오면 <strong>Controller - Service - Repository - DB</strong> 과정을 하나의 스레드가 담당합니다.
이 과정에서 데이터베이스나 외부 API를 호출하는 동안 해당 스레드는 결과가 반환될 때까지 <strong>대기(Blocking)</strong> 상태에 놓이게 됩니다. 이로 인해 동시 요청이 많아질 경우 서버의 스레드 풀(Thread Pool)이 빠르게 소진되면서 성능이 저하될 수 있습니다.</p>
<h4 id="비동기-방식의-스레드-동작">비동기 방식의 스레드 동작</h4>
<p>비동기 방식(Spring WebFlux)에서는 요청이 들어오면 <strong>이벤트 기반(Event-driven) 모델</strong>이 작동합니다. 즉,</p>
<ul>
<li>요청을 처리하는 동안 별도의 <strong>작업 스레드가 할당되지 않고, 요청이 처리될 때까지 기다리는 과정이 없다.</strong></li>
<li>요청을 보내고 즉시 반환되며, 응답이 준비되면 콜백(Callback) 방식으로 데이터를 반환한다.</li>
<li>따라서 하나의 스레드가 여러 개의 요청을 처리할 수 있어 <strong>높은 동시성(Concurrency)</strong>을 유지할 수 있다.</li>
</ul>
<h3 id="23-mono와-flux란">2.3 Mono와 Flux란?</h3>
<p>Spring WebFlux에서 <code>Mono</code>와 <code>Flux</code>는 비동기 데이터를 다루기 위한 핵심 클래스입니다. 이들은 <strong>리액티브 스트림(Reactive Streams)</strong>을 기반으로 하며, 기존의 <code>Future</code> 또는 <code>CompletableFuture</code>보다 유연하게 데이터를 다룰 수 있습니다.</p>
<p>Spring WebFlux에서는 비동기 데이터 처리를 위해 <code>Mono</code>와 <code>Flux</code>를 사용합니다.</p>
<ul>
<li><p><strong>Mono</strong>: 0개 또는 1개의 값을 반환하는 비동기 객체입니다. 단일 데이터를 처리할 때 사용됩니다.</p>
<pre><code class="language-java">Mono&lt;String&gt; monoExample = Mono.just(&quot;Hello, Mono!&quot;);</code></pre>
<p>위 코드에서 <code>Mono.just(&quot;Hello, Mono!&quot;)</code>는 &quot;Hello, Mono!&quot; 값을 감싸 비동기적으로 반환합니다.</p>
</li>
<li><p><strong>Flux</strong>: 0개 이상의 여러 값을 반환하는 비동기 객체입니다. 스트리밍 데이터나 컬렉션을 처리할 때 사용됩니다.</p>
<pre><code class="language-java">Flux&lt;String&gt; fluxExample = Flux.just(&quot;Hello&quot;, &quot;Flux&quot;, &quot;World!&quot;);</code></pre>
<p>위 코드에서 <code>Flux.just(&quot;Hello&quot;, &quot;Flux&quot;, &quot;World!&quot;)</code>는 3개의 값을 순차적으로 반환할 수 있습니다.</p>
</li>
</ul>
<p>Mono와 Flux가 0개 혹은 그 이상의 데이터를 반환할 수 있는 이유는, 비동기 환경에서 데이터의 가용성을 보장하기 위해 설계되었기 때문입니다.</p>
<ul>
<li><strong>Mono</strong>: 0개 또는 1개의 데이터를 반환하는 비동기 객체입니다. 이는 단일 결과가 보장되지만, 응답이 없을 수도 있는 경우(예: 데이터베이스에서 특정 ID를 조회했는데 존재하지 않는 경우)에 유용합니다.</li>
<li><strong>Flux</strong>: 0개 이상(즉, 다수)의 데이터를 반환하는 비동기 객체입니다. 스트리밍 방식의 데이터 처리나 컬렉션을 비동기적으로 다룰 때 적합합니다.</li>
</ul>
<p>이러한 구조는 WebFlux가 <strong>데이터의 흐름을 제어</strong>하고, <strong>백프레셔(Backpressure)</strong>를 활용하여 소비자(Consumer)의 처리 속도에 맞춰 데이터를 제공할 수 있도록 도와줍니다.</p>
<h3 id="24-msa와-api-gateway에서의-비동기-방식-사용">2.4 MSA와 API Gateway에서의 비동기 방식 사용</h3>
<p>Microservices Architecture(MSA)에서는 API Gateway가 많은 트래픽을 처리해야 합니다. 이때 비동기 방식이 효과적인 이유는 다음과 같습니다.</p>
<ul>
<li>API Gateway는 여러 마이크로서비스로부터 데이터를 수집하고 응답을 조합해야 하므로, <strong>비동기 요청이 많아지는 구조</strong>가 됩니다.</li>
<li>동기 방식의 API Gateway는 많은 요청이 몰리면 스레드가 부족해지고 응답 지연이 발생할 수 있습니다.</li>
<li>WebFlux 기반의 API Gateway는 논블로킹 방식으로 동작하여 <strong>적은 리소스로 더 많은 요청을 처리할 수 있습니다</strong>.</li>
</ul>