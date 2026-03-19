# AWS 네트워크 엣지란?
## 개념 정의
AWS에서 “네트워크 엣지”는 **사용자에게 최대한 가까운 위치에서 트래픽을 수용·처리·전달하는 AWS 인프라 지점과 그 주변 네트워크 계층**을 의미한다.  
대표적으로 CloudFront/Route 53이 사용하는 Edge Location, Global Accelerator 엔드포인트, Local Zones·Outposts 같은 엣지 인프라가 여기에 포함된다.  

***
## 주요 구성 요소
- Edge Location  
  - 전 세계 도시에 분산된 AWS의 CDN·DNS 캐시 서버 지점.  
  - CloudFront, Route 53, Global Accelerator 등이 여기서 트래픽을 받아 가장 가까운 곳에서 응답해 지연을 줄인다.  

- Regional Edge Cache  
  - 여러 Edge Location 앞단에 위치한 더 큰 캐시 계층으로, 자주 쓰이는 콘텐츠를 장기간 유지해 원본(오리진) 요청을 줄이고 성능을 높인다.  

- Local Zones  
  - 특정 도시/메트로 지역에 위치한 “리전에 붙어 있는 소형 리전” 같은 개념.  
  - EC2, EBS, VPC 등을 엣지 쪽에 배치해 수 ms 수준 저지연이 필요한 워크로드를 사용자 가까이에 두는 용도.  

- AWS Outposts  
  - 고객 데이터센터/엣지 위치에 AWS 인프라를 그대로 가져다 설치하는 하이브리드 엣지 인프라.  
  - 온프레미스에서 AWS와 동일한 API·콘솔·서비스 경험을 제공하면서, 지역 네트워크와 로컬 게이트웨이로 연동.  

***
## AWS 엣지 네트워킹 서비스
AWS는 위 인프라 위에서 다음과 같은 **엣지 네트워킹 서비스**를 제공한다.

- Amazon CloudFront  
  - 글로벌 CDN, Edge Location에서 콘텐츠 캐시·전달.  
- AWS Global Accelerator  
  - AWS 글로벌 백본을 이용해 클라이언트 → 가장 가까운 엣지 → 최적 리전으로 라우팅하는 가속 서비스.  
- Amazon Route 53  
  - DNS 질의에 가장 근접한 엣지에서 응답, 지연 최소화·헬스 체크 기반 라우팅.  
- Lambda@Edge / CloudFront Functions  
  - 엣지에서 HTTP 요청·응답을 가볍게 조작하는 서버리스 코드 실행.  

***
## 왜 쓰는지 (목적)
- 지연 시간(Latency) 감소  
  - 사용자의 요청이 리전까지 가지 않고, 가까운 엣지에서 캐싱·처리되어 응답 속도 향상.  
- 가용성·복원력 향상  
  - 글로벌 분산된 엣지를 통해 특정 리전 장애 시 다른 리전으로 빠르게 라우팅 전환.  
- 보안·트래픽 최적화  
  - AWS 백본망을 통해 인터넷 구간 품질·보안을 강화하고, DDoS 완화·TLS 종료 등을 엣지에서 수행.  

정리하면, “AWS 네트워크 엣지”는 **사용자와 리전 사이에서 AWS가 제공하는 전 세계 분산 접점(Edge Location, Local Zone, Outposts 등)과 그 위의 네트워킹·전달·가속·보안 계층 전체**를 가리키는 개념이라고 보면 된다.
