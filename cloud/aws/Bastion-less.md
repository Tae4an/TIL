# **전통적인 Bastion Host 방식 vs Bastion-less 아키텍처 방식 비교**

---

## **전통적인 Bastion Host 방식**

### **보안 특성**
Bastion Host는 Public Subnet에 배치되어 SSH 포트(22번)를 인터넷에 개방한다. 이는 지속적인 스캔 및 무차별 대입 공격에 노출되는 공격 표면을 생성한다. SSH 키 관리의 복잡성으로 인해 키 유출 시 심각한 보안 사고 위험이 존재한다.

### **운영 특성**  
별도의 Bastion Host EC2 인스턴스 생성과 관리가 필요하다. SSH 키의 주기적 교체, 안전한 보관, 다수 인원 간 배포 등의 관리 부담이 발생한다. Bastion Host 자체에 대한 패치, 모니터링, 취약점 관리 등의 추가적인 운영 오버헤드가 발생한다.

### **감사 및 컴플라이언스**
SSH 세션 내에서 사용자가 실행한 명령어를 중앙에서 추적하고 기록하는 것이 제한적이다. 컴플라이언스 요구사항을 충족하기 어려운 구조이다.

### **비용 구조**
Bastion Host 인스턴스 운영 비용이 지속적으로 발생한다. 사용하지 않을 때도 인스턴스 비용을 지불해야 한다.

***

## **Bastion-less 아키텍처 방식 (Session Manager)**

### **보안 특성**
EC2 인스턴스의 모든 인바운드 규칙을 차단하여 Zero-Port Security를 구현한다. 외부 인터넷에 노출된 포트가 없어 공격 시도 자체가 불가능하다. IAM 사용자/역할과 정책을 통한 세분화된 접근 권한 제어가 가능하다. MFA(다단계 인증) 적용도 지원한다.

### **운영 특성**
별도의 Bastion Host 인스턴스 관리 부담과 비용이 완전히 제거된다. SSH 키 파일 관리 없이 IAM 기반으로 접근을 제어한다. AWS 관리형 서비스로 인프라 관리 복잡성이 대폭 감소한다.

### **감사 및 컴플라이언스**
모든 접속 시도와 터미널에서 실행된 모든 명령어가 CloudTrail과 CloudWatch에 자동으로 기록된다. 완벽한 감사 추적을 제공하여 컴플라이언스 요구사항을 충족한다.

### **네트워크 구성**
Private Subnet에 있는 EC2가 Session Manager와 통신하기 위해 VPC 엔드포인트(ssm, ssmmessages, ec2messages)가 필수적으로 구성된다. 인터넷을 거치지 않고 AWS 내부망을 통해 안전하게 통신한다.

### **비용 구조**
Bastion Host 인스턴스 비용이 완전히 제거되어 비용 최적화가 실현된다. Session Manager 사용료와 VPC 엔드포인트 사용료만 발생한다.

---

## **AWS 공식 근거 출처**

### **전통적인 Bastion Host 방식의 한계점**
- [AWS Prescriptive Guidance: Access a bastion host by using Session Manager](https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/access-a-bastion-host-by-using-session-manager-and-amazon-ec2-instance-connect.html)
- [AWS Blog: Replacing a Bastion Host with Amazon EC2 Systems Manager](https://aws.amazon.com/blogs/mt/replacing-a-bastion-host-with-amazon-ec2-systems-manager/)
- [AWS Blog: Toward a bastion-less world](https://aws.amazon.com/blogs/infrastructure-and-automation/toward-a-bastion-less-world/)

### **Bastion-less 아키텍처의 공식 권장사항**
- [AWS Prescriptive Guidance: WKLD.06 Use Systems Manager instead of SSH or RDP](https://docs.aws.amazon.com/prescriptive-guidance/latest/aws-startup-security-baseline/wkld-06.html)
- [AWS Prescriptive Guidance: Connect to an Amazon EC2 instance by using Session Manager](https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/connect-to-an-amazon-ec2-instance-by-using-session-manager.html)
- [AWS Systems Manager: VPC endpoints for Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-getting-started-privatelink.html)
- [AWS Blog: Securely administer servers with Session Manager](https://aws.amazon.com/blogs/mt/securely-administer-servers-migrated-with-aws-application-migration-service-using-aws-systems-manager-session-manager/)
