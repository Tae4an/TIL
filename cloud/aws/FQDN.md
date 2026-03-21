# AWS FQDN이란?

## 개념 정의

FQDN(Full / Fully Qualified Domain Name)은 **호스트 이름 + 도메인 이름이 모두 포함된 “완전한” 도메인 이름**이다. 예를 들어 `ip-10-0-1-23.ap-northeast-2.compute.internal` 같은 형태가 FQDN이다.  
AWS에서 “FQDN”이라고 하면 보통 **EC2, RDS, ALB, API Gateway 등 리소스에 부여된 전체 도메인 이름**을 의미하며, 이 이름으로 리소스에 접속하거나 보안 정책(화이트리스트 등)을 설정한다.

***

## 일반적인 FQDN 개념

- 구성 요소  
  - 호스트 이름(hostname) + 도메인 + 상위 도메인(TLD)까지 점(.)으로 구분한 전체 문자열.  
  - 예: `www.example.com` (`www` = 호스트, `example.com` = 도메인).  
- 특징  
  - DNS 트리 상에서 위치가 모호하지 않은 “전역적으로 유일한” 이름.  
  - IP를 몰라도 이 이름만 알면 DNS로 IP를 조회해 접속할 수 있다.

***

## AWS에서의 FQDN 사용 예

- EC2 인스턴스  
  - 퍼블릭: `ec2-11-22-33-44.ap-northeast-2.compute.amazonaws.com`  
  - 프라이빗: `ip-10-0-1-23.ap-northeast-2.compute.internal`  
- ALB / NLB  
  - 예: `my-app-alb-1234567890.ap-northeast-2.elb.amazonaws.com`  
- RDS  
  - 예: `mydb.abcdefghijk.ap-northeast-2.rds.amazonaws.com`  
- API Gateway / VPC 엔드포인트 등  
  - 예: `abc123.execute-api.ap-northeast-2.amazonaws.com`  
이런 주소들이 전부 해당 리소스의 FQDN이다.

***

## AWS에서 FQDN을 왜 쓰는지

- 보안 설정  
  - 보안 솔루션, 프록시, 방화벽 등에서 AWS 리소스를 도메인 단위로 화이트리스트/접근 제어할 때 사용.  
- 접속·운영 편의성  
  - IP가 바뀌어도 FQDN은 동일하게 유지되므로, 애플리케이션이나 클라이언트 설정은 도메인만 pointing 하면 됨.  
- 인증서(SSL/TLS)  
  - HTTPS 사용할 때, ALB·CloudFront·API Gateway 등은 특정 FQDN에 대해 인증서를 발급·적용한다.

***

## 정리

- “AWS FQDN”이라는 별도의 서비스가 있는 것은 아니고,  
- **AWS 리소스에 부여된 “완전한 도메인 이름(호스트+도메인 전체 문자열)”을 지칭하는 일반 DNS 용어를 AWS 맥락에서 부르는 것**이라고 이해하면 된다.  
- 실무에서는 보통 “EC2 FQDN”, “RDS FQDN”, “VPC 엔드포인트 FQDN”처럼 리소스별 도메인 이름을 가리킬 때 쓰는 표현이다.
