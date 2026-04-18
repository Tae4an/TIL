# 도메인 이메일 인증 및 DNS 설정 (SPF, DKIM, DMARC, MX)
## 1. 개념 요약
도메인 이메일 인증은 **내 도메인에서 보내는 이메일이 진짜인지, 위·변조되지 않았는지를 수신 서버가 검증할 수 있도록 DNS에 설정하는 보안 레코드 묶음**이다.  
MX·SPF·DKIM·DMARC 4가지를 함께 설정해야 **이메일 수신, 발신 인증, 스푸핑·피싱 방지**가 완전히 동작한다.

***
## 2. 4가지 레코드 역할
- **MX (Mail Exchange)**
  - 이 도메인으로 오는 이메일을 어느 메일 서버가 받을지 지정
  - `example.com`으로 보낸 메일을 Google Workspace, Microsoft 365 등 지정 서버로 라우팅

- **SPF (Sender Policy Framework)**
  - 이 도메인 이름으로 이메일을 발송할 수 있는 **허가된 서버 IP 목록**을 DNS에 등록
  - 목록에 없는 서버가 보내면 → 스팸·차단 처리
  - **"허가된 서버에서 보냈는가?"** 를 검증

- **DKIM (DomainKeys Identified Mail)**
  - 발송 메일에 **비공개 키로 디지털 서명**을 삽입, 수신 서버는 DNS의 공개 키로 검증
  - 전송 중 메일 본문·헤더 변조 여부 확인
  - **"메일 내용이 위변조되지 않았는가?"** 를 검증

- **DMARC (Domain-based Message Authentication, Reporting & Conformance)**
  - SPF·DKIM 결과를 종합해 **실패 시 처리 정책(무시·격리·거부)을 정의**
  - 실패한 이메일 보고서(XML)를 지정 이메일로 수신 가능
  - **"인증 실패하면 어떻게 처리할 것인가?"** 를 정의

***
## 3. DNS 레코드 설정 예시
- **MX 레코드**
```
타입: MX
호스트: @
값: aspmx.l.google.com (Google Workspace 예시)
우선순위: 1
```

- **SPF 레코드 (TXT)**
```
타입: TXT
호스트: @
값: v=spf1 include:_spf.google.com -all
```
- `include:` → 허가할 외부 서비스 추가
- `-all` → 목록 외 모두 차단 / `~all` → Soft Fail(스팸 처리)

- **DKIM 레코드 (TXT)**
```
타입: TXT
호스트: selector._domainkey (selector는 메일 서비스 제공)
값: v=DKIM1; k=rsa; p=공개키값...
```
- 공개 키는 Google Workspace·Microsoft 365 등 메일 서비스에서 자동 생성 제공

- **DMARC 레코드 (TXT)**
```
타입: TXT
호스트: _dmarc
값: v=DMARC1; p=quarantine; rua=mailto:dmarc@example.com; pct=100
```
- `p=none` : 정책 없음(모니터링만)
- `p=quarantine` : 실패 시 스팸함 처리
- `p=reject` : 실패 시 완전 거부

***
## 4. 4가지 레코드 비교
| 항목 | MX | SPF | DKIM | DMARC |
|---|---|---|---|---|
| **DNS 레코드 타입** | MX | TXT | TXT | TXT |
| **역할** | 수신 서버 지정 | 발신 서버 허가 목록 | 메일 서명·무결성 | 정책 집행·보고 |
| **위치** | `@` | `@` | `selector._domainkey` | `_dmarc` |
| **단독 사용 한계** | 인증 없음 | 변조 감지 불가 | 실패 시 처리 정책 없음 | SPF·DKIM 없이 동작 불가 |

***
## 5. 설정 권장 순서
1. **MX 레코드** 설정 → 이메일 수신 가능하게
2. **SPF 레코드** 설정 → 발신 서버 인증
3. **DKIM 활성화** → 메일 서비스에서 키 생성 후 DNS에 등록
4. **DMARC 레코드** 설정 → 처음엔 `p=none`으로 모니터링, 이상 없으면 `quarantine` → `reject`로 강화

***
## 6. 한 줄 정리
MX는 수신 서버를, SPF는 발신 서버 허가를, DKIM은 메일 무결성을, DMARC는 인증 실패 시 처리 정책을 담당하며, **4가지를 모두 설정해야 도메인 이메일 스푸핑·피싱을 효과적으로 차단하는 완전한 이메일 인증 체계**가 완성된다.
