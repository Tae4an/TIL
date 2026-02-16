# AWS Deny by Default란?

## 개념 정의

AWS Deny by Default는 **모든 요청은 기본적으로 거부(Implicit Deny)** 되고, **명시적으로 Allow 된 경우에만 허용**되며, 그 중 **명시적 Deny가 하나라도 있으면 무조건 거부**되는 보안 모델을 말한다. IAM, SCP(서비스 제어 정책), 리소스 기반 정책 모두 이 규칙을 따른다.

즉, "기본은 전부 막혀 있고, 정책에서 허용해준 것만 쓸 수 있으며, 어딘가에서 Deny라고 박아두면 다른 데서 Allow해도 결국 거부된다"는 구조다.

## IAM 정책 평가 핵심 규칙

AWS가 요청을 평가할 때 적용하는 기본 규칙은 아래 세 가지다.

### 1. 기본 거부(Default / Implicit Deny)
- 루트 계정(계정 root 유저)을 제외한 모든 엔티티는 기본적으로 요청이 거부된다
- 어떤 정책에도 해당 작업을 허용하는 Allow 문이 없으면, 자동으로 **암시적 거부(Implicit Deny)**다

### 2. 명시적 허용(Explicit Allow)
- 하나 이상의 적용 가능한 정책(Identity-based, Resource-based, SCP, Permissions Boundary, Session Policy 등)에 Allow 문이 있고, 그 조건이 모두 맞으면 기본 거부가 해제된다

### 3. 명시적 거부(Explicit Deny) 우선
- 어떤 정책에든 Deny 문이 요청에 적용되면, 다른 모든 Allow를 무시하고 **무조건 최종 Deny**가 된다

이 세 가지를 한 줄로 요약하면:

> 기본은 모두 거부 → 허용하려면 명시적 Allow 필요 → 그래도 Deny가 하나라도 있으면 최종 Deny

## SCP(서비스 제어 정책)에서의 Deny by Default

Organizations의 SCP도 **deny-by-default 모델**을 따른다.

- SCP가 붙은 계정에서는, SCP에 **Allow**로 명시된 서비스/액션만 잠재적으로 허용 가능하다
- SCP에 허용되지 않은 모든 권한은 **암시적으로 거부**된다
- 그 위에서 IAM 정책이 Allow를 주더라도, SCP에서 허용하지 않은 영역은 쓸 수 없다

그래서 SCP 설계 시:

- "조직 전체에서 허용할 서비스/액션만 SCP에서 Allow"
- 나머지는 Deny by Default에 의해 자연스럽게 차단

이 패턴이 권장된다.

## Explicit Deny vs Implicit Deny

### Implicit Deny (암시적 거부)
- 해당 요청을 허용하는 Allow 문이 **아무 정책에도 없을 때** 발생
- 별도의 Deny 문이 없어도, 그냥 "허용이 없으니 기본 거부" 상태

### Explicit Deny (명시적 거부)
- 정책에 `Effect: "Deny"`가 있고, 그 조건이 요청에 적용될 때 발생
- 어떤 곳에서 Allow를 주더라도 **명시적 Deny가 항상 우선**한다

실무적으로는:

- 가능한 한 **"기본 거부 + 최소한의 Allow"** 구조로 정책을 설계한다
- 특별히 막고 싶은 케이스(예: 특정 리전, 특정 태그가 없는 리소스)는 **명시적 Deny**로 추가한다

## 정리

AWS는 IAM·SCP 전반에 걸쳐 **Deny by Default(기본 거부)** 모델을 사용하며, 어떤 액션이든 **명시적 Allow가 없으면** 실행할 수 없고(Implicit Deny), 명시적 Allow가 있더라도 그 요청에 적용되는 **Explicit Deny가 하나라도 있으면 최종 Deny**되며, SCP도 같은 모델을 따라 **SCP에 허용되지 않은 권한은 모두 암시적으로 거부**된다.
