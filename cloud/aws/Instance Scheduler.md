# AWS Instance Scheduler란?

## 개념 정의

AWS Instance Scheduler는 EC2와 RDS 인스턴스를 정해진 시간에 자동으로 시작/중지하는 AWS 공식 솔루션이다. CloudFormation으로 배포되며, Lambda와 EventBridge로 태그 기반 스케줄링을 수행한다.

쉽게 말해 "업무 시간에만 인스턴스를 켜고 끄는 자동 타이머로, 개발/테스트 환경 비용을 최대 70% 절감하는 도구"다.

## 핵심 기능

- 태그 기반 자동 시작/중지
- 다중 계정/리전 중앙 관리
- 유연한 스케줄 정의 (요일, 시간대, 타임존)
- CloudFormation 원클릭 배포
- CLI로 스케줄 설정 및 비용 절감 예측

## 주요 구성 요소

**Lambda Function**
EventBridge가 5분마다 트리거하며, DynamoDB 스케줄을 읽어 태그된 인스턴스를 제어한다.

**DynamoDB**
ConfigTable에 Period/Schedule 저장하고, StateTable에 인스턴스 상태를 추적한다.

**EventBridge Rule**
설정한 간격마다 Lambda를 실행하며, 기본 5분 간격이다.

**IAM Role**
Lambda 실행 권한, Cross-Account 접근, EC2/RDS 제어 권한을 포함한다.

## 스케줄 개념

**Period**
시작/종료 시간과 요일 조합이다. 예: 평일 09:00~18:00

**Schedule**
하나 이상의 Period를 묶은 것이다. 타임존 설정이 가능하다.

**태그 적용**
인스턴스에 `Schedule=<스케줄명>` 태그를 붙인다.

## 작동 방식

1. CloudFormation으로 배포
2. DynamoDB에 Period/Schedule 등록
3. 대상 인스턴스에 태그 부여
4. EventBridge가 5분마다 Lambda 트리거
5. Lambda가 현재 시간과 스케줄 비교
6. 조건에 맞춰 Start/Stop API 호출
7. CloudWatch Logs에 기록

## 주요 활용 사례

- 개발/테스트 환경 (평일 업무 시간만 실행)
- QA/스테이징 (테스트 기간만 실행)
- 교육/데모 환경 (강의 시간만 실행)
- 배치 작업 (특정 시간대만 실행)
- 글로벌 팀 (타임존별 스케줄)

## 통합 서비스

Lambda, DynamoDB, EventBridge, CloudWatch, IAM, CloudFormation, Systems Manager

## 설정 예시

**평일 업무 시간**
- Period: 월~금 09:00~18:00
- 태그: Schedule=office-hours

**야간 배치 작업**
- Period: 매일 02:00~04:00
- 태그: Schedule=nightly-batch

**주말 제외 24시간**
- Period: 월~금 00:00~23:59
- 태그: Schedule=weekday-only

## 모범 사례

- 태그 명명 규칙 표준화 (dev-weekday, test-office-hours)
- 프로덕션 환경은 태그 제외
- 타임존 정확히 설정 (UTC 계산 오류 방지)
- Systems Manager Maintenance Window 연동
- CloudWatch 알림 설정
- DryRun 모드로 사전 검증
- Cost Explorer로 절감 효과 측정

## 제약 사항

- 5분 간격으로 1~2분 오차 발생 가능
- RDS Multi-AZ는 중지 시 Failover 없음
- Auto Scaling Group은 최소 용량 0일 때만 작동
- 리전별 별도 배포 필요

## 비용

Lambda 실행, DynamoDB 저장소, CloudWatch Logs 비용만 발생하며, 월 $5 이하다. 절감되는 인스턴스 비용이 훨씬 크다.

## 요약

AWS Instance Scheduler는 EC2/RDS를 태그 기반으로 자동 시작/중지하는 공식 솔루션으로, Lambda가 EventBridge 트리거로 DynamoDB 스케줄을 읽어 제어하며, 다중 계정/리전 중앙 관리를 지원하고, 개발/테스트 환경에서 최대 70% 비용 절감 효과를 제공한다.
