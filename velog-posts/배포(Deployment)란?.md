<h1 id="📌-배포deployment란">📌 배포(Deployment)란?</h1>
<p>배포(Deployment)란 <strong>내 개발 환경에서 만든 애플리케이션을 운영 환경에서 실행할 수 있도록 배포하는 과정</strong>을 의미한다.</p>
<hr />
<h2 id="1️⃣-배포란-무엇인가"><strong>1️⃣ 배포란 무엇인가?</strong></h2>
<p>배포는 단순히 &quot;파일을 서버로 이동시키는 것&quot;이 아니라, <strong>소프트웨어를 운영 환경에서 실행할 수 있도록 준비하는 과정 전체</strong>를 포함한다.</p>
<h3 id="✅-배포의-핵심-과정">✅ <strong>배포의 핵심 과정</strong></h3>
<ol>
<li><p><strong>코드 작성 &amp; 빌드(Build)</strong>  </p>
<ul>
<li>Java의 경우 <code>.java</code> 파일을 <code>.class</code>로 컴파일  </li>
<li><code>Maven</code> 또는 <code>Gradle</code>을 사용해 실행 가능한 <code>.jar</code> 또는 <code>.war</code> 파일 생성  </li>
</ul>
</li>
<li><p><strong>테스트 &amp; 코드 검증</strong>  </p>
<ul>
<li>단위 테스트, 통합 테스트 실행  </li>
<li>SonarQube, JaCoCo 등을 이용한 코드 품질 검사  </li>
</ul>
</li>
<li><p><strong>패키징(Packaging)</strong>  </p>
<ul>
<li>애플리케이션을 실행 가능한 단일 파일 (<code>.jar</code>, <code>.war</code>, Docker 이미지)로 묶음  </li>
</ul>
</li>
<li><p><strong>서버 업로드 &amp; 환경 설정</strong>  </p>
<ul>
<li><code>scp</code>, <code>rsync</code>를 이용한 파일 전송  </li>
<li>환경 변수 (<code>.env</code>, <code>application.yml</code>) 설정  </li>
</ul>
</li>
<li><p><strong>실행 &amp; 모니터링</strong>  </p>
<ul>
<li><code>java -jar</code> 혹은 <code>Docker</code>를 이용한 실행  </li>
<li><code>Prometheus</code>, <code>Grafana</code>를 이용한 모니터링  </li>
</ul>
</li>
</ol>
<hr />
<h2 id="2️⃣-배포를-쉽게-이해하는-비유"><strong>2️⃣ 배포를 쉽게 이해하는 비유</strong></h2>
<h3 id="🏭-1-공장에서-제품을-만들어-매장에-공급하는-과정">🏭 <strong>1. 공장에서 제품을 만들어 매장에 공급하는 과정</strong></h3>
<p>배포 과정을 공장과 매장에 비유하면 다음과 같다.</p>
<ul>
<li><strong>개발 환경 = 제품을 설계하고 제작하는 공장</strong><ul>
<li>개발자가 애플리케이션을 작성하는 단계</li>
</ul>
</li>
<li><strong>빌드 &amp; 패키징 = 제품을 조립하고 포장하는 과정</strong><ul>
<li><code>Gradle</code> 또는 <code>Maven</code>으로 <code>.jar</code> 혹은 <code>.war</code> 패키지를 생성</li>
</ul>
</li>
<li><strong>배포 = 제품을 매장에 공급하는 과정</strong><ul>
<li>서버에 애플리케이션을 업로드하고 실행</li>
</ul>
</li>
<li><strong>운영 환경 = 고객이 제품을 사용하는 매장</strong><ul>
<li>사용자가 실제로 애플리케이션을 이용하는 단계</li>
</ul>
</li>
</ul>
<h3 id="🎁-2-개발자가-만든-선물을-사용자에게-보내는-과정">🎁 <strong>2. 개발자가 만든 선물을 사용자에게 보내는 과정</strong></h3>
<p>배포는 마치 개발자가 만든 선물을 사용자에게 보내는 것과 같다.</p>
<ul>
<li><strong>개발자가 선물(코드)를 만든다.</strong></li>
<li><strong>포장(빌드 &amp; 패키징) 후, 배송(배포)한다.</strong></li>
<li><strong>사용자가 선물을 받아 실제로 사용한다.</strong></li>
</ul>
<hr />
<h2 id="3️⃣-배포-방법의-종류"><strong>3️⃣ 배포 방법의 종류</strong></h2>
<h3 id="🔹-1-수동-배포-manual-deployment">🔹 1. 수동 배포 (Manual Deployment)</h3>
<ul>
<li><code>.jar</code> 파일을 직접 서버에 복사 후 실행 (SCP, FTP 등 사용. *P는 프로토콜을 의미한다.)<blockquote>
<p>필자의 경우 Filezilla라는 프로그램을 사용하여 수동 배포한 경험이 있다. Filezilla의 기본 프로토콜은 SFTP(SSH File Transfer Protocal)을 사용한다.</p>
</blockquote>
</li>
<li><code>java -jar myapp.jar</code> 실행</li>
<li>✅ 장점: 간단하고 설정이 필요 없음</li>
<li>❌ 단점: 사람이 직접 해야 하므로 실수 가능성이 큼</li>
</ul>
<h3 id="🔹-2-스크립트-배포-shell-script">🔹 2. 스크립트 배포 (Shell Script)</h3>
<ul>
<li><code>deploy.sh</code> 같은 스크립트를 작성해 자동화  <pre><code class="language-sh">#!/bin/bash
scp myapp.jar user@server:/home/app/
ssh user@server &quot;java -jar /home/app/myapp.jar &amp;&quot;</code></pre>
</li>
<li>✅ 장점: 반복적인 작업 자동화 가능</li>
<li>❌ 단점: 여전히 관리가 필요함</li>
</ul>
<h3 id="🔹-3-cicd-자동-배포">🔹 3. CI/CD (자동 배포)</h3>
<ul>
<li>GitHub Actions, Jenkins, ArgoCD 등을 이용해 코드 변경 → 자동 빌드 &amp; 테스트 → 배포</li>
<li>✅ 장점: 배포 속도 증가, 실수 감소</li>
<li>❌ 단점: 초기 설정이 필요</li>
</ul>
<h3 id="🔹-4-컨테이너-기반-배포-docker--kubernetes">🔹 4. 컨테이너 기반 배포 (Docker &amp; Kubernetes)</h3>
<ul>
<li>Docker를 이용해 컨테이너 이미지를 빌드하고, Kubernetes로 배포<pre><code class="language-sh">docker build -t myapp:latest .
docker run -d -p 8080:8080 myapp:latest</code></pre>
</li>
<li>✅ 장점: 이식성이 뛰어나며, MSA 환경에서 유리</li>
<li>❌ 단점: Docker, Kubernetes 설정이 필요함</li>
</ul>