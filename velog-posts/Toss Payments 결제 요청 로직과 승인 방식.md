<h2 id="1-toss-payments-결제-요청-개요">1. Toss Payments 결제 요청 개요</h2>
<p>Toss Payments는 간편한 결제 API를 제공하며, 클라이언트(Frontend)와 서버(Backend) 간의 역할이 구분되어 있다.
Toss Payments를 통해 결제를 진행하는 일반적인 흐름은 다음과 같다:</p>
<ol>
<li><strong>클라이언트에서 결제 요청 수행</strong></li>
<li><strong>결제 승인 후, 서버에서 최종 검증</strong></li>
</ol>
<p>Toss Payments의 결제 요청 방식은 클라이언트가 직접 결제 요청을 수행하지만, 이후 서버에서 최종적으로 결제 내역을 검증하는 구조를 가진다. 이를 통해 <strong>악의적인 결제 조작을 방지</strong>하고 <strong>데이터 무결성을 보장</strong>할 수 있다.</p>
<blockquote>
<p>결제를 카카오페이로 한다고 가정했을 때, QR코드 화면으로 리디렉션하는 과정을 포함해서, 마지막 검증을 제외한 처리들은 <strong>서버의 역할이 필요하지 않기 때문에</strong> Client → Toss Payments 방식으로 진행되는 것이 아닐까 추측해본다.</p>
</blockquote>
<hr />
<h2 id="2-결제-요청-로직-frontend-→-toss-payments">2. 결제 요청 로직 (Frontend → Toss Payments)</h2>
<h3 id="1-결제-요청-수행"><strong>1) 결제 요청 수행</strong></h3>
<p>사용자가 결제 버튼을 클릭하면 <code>widgets.requestPayment()</code>를 통해 Toss Payments에 직접 결제 요청을 보낸다.</p>
<pre><code class="language-javascript">paymentRequestButton.addEventListener('click', async () =&gt; {
    try {
        await widgets.requestPayment({
            orderId: generateRandomString(),
            orderName: &quot;토스 티셔츠 외 2건&quot;,
            successUrl: window.location.origin + &quot;/sandbox/success&quot;,
            failUrl: window.location.origin + &quot;/sandbox/fail&quot;,
            customerEmail: &quot;customer123@gmail.com&quot;,
            customerName: &quot;김토스&quot;,
            customerMobilePhone: &quot;01012341234&quot;,
        });
    } catch (err) {
        // TODO: 에러 처리
    }
});</code></pre>
<ul>
<li><code>orderId</code>: 주문 고유 식별값 (서버에서도 동일한 값을 저장해야 함)</li>
<li><code>orderName</code>: 주문명</li>
<li><code>successUrl</code>: 결제 성공 시 이동할 URL</li>
<li><code>failUrl</code>: 결제 실패 시 이동할 URL</li>
<li><code>customerEmail</code>, <code>customerName</code>, <code>customerMobilePhone</code>: 고객 정보</li>
</ul>
<p>이 과정에서 <strong>결제 요청이 Toss Payments 서버로 전송되며, 사용자 인증 및 결제가 진행된다.</strong></p>
<hr />
<h2 id="3-결제-승인-및-서버-검증-backend">3. 결제 승인 및 서버 검증 (Backend)</h2>
<h3 id="1-결제-완료-후-서버-검증-필요성"><strong>1) 결제 완료 후 서버 검증 필요성</strong></h3>
<p>클라이언트에서 직접 결제 요청을 수행하지만, <strong>결제 금액이 조작될 가능성이 있기 때문에 반드시 서버에서 검증을 수행해야 한다.</strong></p>
<h3 id="2-toss-payments의-승인-요청-서버-검증"><strong>2) Toss Payments의 승인 요청 (서버 검증)</strong></h3>
<p>클라이언트에서 결제 성공 후, Toss Payments는 <code>successUrl</code>로 리다이렉트하며 <code>paymentKey</code>, <code>orderId</code>, <code>amount</code> 값을 전달한다.</p>
<pre><code class="language-http">GET /sandbox/success?paymentKey={paymentKey}&amp;orderId={orderId}&amp;amount={amount}</code></pre>
<p>서버는 해당 데이터를 받아 Toss Payments API를 이용해 결제 승인을 요청한다.</p>
<pre><code class="language-java">@PostMapping(&quot;/confirm-payment&quot;)
public ResponseEntity&lt;?&gt; confirmPayment(@RequestBody PaymentConfirmRequest request) {
    // 서버 DB에서 orderId 및 amount 검증
    Order order = orderService.findByOrderId(request.getOrderId());
    if (order == null || !order.getAmount().equals(request.getAmount())) {
        return ResponseEntity.badRequest().body(&quot;결제 금액 불일치&quot;);
    }

    // Toss Payments 승인 API 호출
    tossPaymentService.confirmPayment(request.getPaymentKey(), request.getOrderId(), request.getAmount());

    // 결제 상태 업데이트
    orderService.updateOrderStatus(request.getOrderId(), &quot;COMPLETED&quot;);

    return ResponseEntity.ok(&quot;결제 승인 완료&quot;);
}</code></pre>
<h3 id="3-검증-과정"><strong>3) 검증 과정</strong></h3>
<ul>
<li>Toss Payments에서 전달받은 <code>orderId</code>, <code>amount</code> 값을 <strong>서버 DB에 저장된 값과 비교하여 일치하는지 확인</strong>한다.</li>
<li>만약 <code>amount</code> 값이 다르면 <strong>결제 취소 처리</strong>를 수행해야 한다.</li>
<li>검증이 완료되면 Toss Payments에 결제 승인을 요청한다.</li>
</ul>
<hr />
<h2 id="4-결제-성공-및-실패-처리">4. 결제 성공 및 실패 처리</h2>
<h3 id="1-결제-성공-시-처리"><strong>1) 결제 성공 시 처리</strong></h3>
<ul>
<li><code>successUrl</code>로 리다이렉트 후, 서버에서 <strong>결제 승인 API를 호출하여 최종 승인</strong>한다.</li>
<li>서버에서 주문 상태를 <code>COMPLETED</code>로 변경하고, 사용자에게 결제 완료 응답을 제공한다.</li>
</ul>
<pre><code class="language-json">{
    &quot;orderId&quot;: &quot;abc123&quot;,
    &quot;paymentKey&quot;: &quot;pay_xyz789&quot;,
    &quot;status&quot;: &quot;COMPLETED&quot;,
    &quot;amount&quot;: 100000
}</code></pre>
<h3 id="2-결제-실패-시-처리"><strong>2) 결제 실패 시 처리</strong></h3>
<ul>
<li><code>failUrl</code>로 리다이렉트되며, 결제 실패 이유를 서버에 기록한다.</li>
<li>사용자에게 결제 실패 메시지를 표시한다.</li>
</ul>
<pre><code class="language-json">{
    &quot;orderId&quot;: &quot;abc123&quot;,
    &quot;status&quot;: &quot;FAILED&quot;,
    &quot;message&quot;: &quot;결제 금액 불일치&quot;
}</code></pre>
<hr />
<h2 id="5-결론">5. 결론</h2>
<p>Toss Payments는 클라이언트에서 직접 결제를 요청하지만, <strong>결제 금액 조작 방지를 위해 서버에서 반드시 검증 후 승인하는 과정이 필요</strong>하다.</p>
<h3 id="💡-주요-정리"><strong>💡 주요 정리</strong></h3>
<ol>
<li><strong>클라이언트에서 결제 요청을 보냄.</strong></li>
<li><strong>결제 완료 후, Toss Payments가 <code>successUrl</code>로 리다이렉트하면서 결제 정보를 전달.</strong></li>
<li><strong>서버에서 해당 정보를 검증하고 Toss Payments에 최종 결제 승인 요청을 보냄.</strong></li>
<li><strong>검증이 완료되면 주문 상태를 <code>COMPLETED</code>로 업데이트.</strong></li>
<li><strong>결제 실패 시, 서버에 기록하고 사용자에게 안내.</strong></li>
</ol>
<p>이러한 과정을 통해 결제 요청과 검증을 안전하게 처리할 수 있다.</p>