# MOVI

시각 중심 금융 UI 이용에 어려움이 있는 사용자를 고려한 **음성 보조 금융 거래 서비스 프로토타입**입니다.

MOVI는 음성으로 계좌 조회와 송금 과정을 보조하되, AI가 해석한 결과를 그대로 금융 실행에 사용하지 않고 Backend가 실제 금융 상태와 권한을 다시 검증하도록 설계했습니다.

<p align="center">
  <img src="./assets/demo-flow.png" alt="MOVI 핵심 시연 사용자 흐름" width="100%">
</p>

---

## 프로젝트 개요

- 형태: 4인 팀 프로젝트
- 주요 흐름: 로그인 → 계좌 조회 → 음성/직접 송금 → 거래 확인 → FDS → 결과 안내
- 구조: Frontend / Backend / AI 서버 분리
- 금융 연동: Mock 기반 환경 중심 검증

---

## 해결하려던 문제

시각 중심 금융 서비스에서는 다음과 같은 문제가 생길 수 있습니다.

- 계좌나 금액을 화면에서 확인하기 어려움
- 음성 인식 오류가 잘못된 거래로 이어질 가능성
- AI가 해석한 값을 금융 사실처럼 그대로 사용할 위험
- 음성을 사용할 수 없는 상황에서 대체 입력 경로가 필요
- 네트워크 재시도로 중복 거래가 발생할 가능성

MOVI는 단순히 “음성으로 송금한다”보다 **음성 입력을 금융 거래에 안전하게 연결하는 구조**에 초점을 맞췄습니다.

---

## 핵심 설계 원칙

### AI는 해석하고, Backend는 검증하고 실행

Voice AI는 사용자의 발화를 Intent / Entity 형태로 해석합니다.

하지만 다음 값은 Backend가 다시 확인합니다.

- 사용자 계좌
- 수취인
- 금액
- 잔액 / 한도
- 세션 상태
- 사용자가 확인한 값과 실제 실행 값의 일치 여부

### 명시적 확인

음성 인식 결과만으로 거래를 완료하지 않습니다.

사용자가 수취인·금액·출금 계좌를 다시 확인한 뒤 거래를 진행합니다.

### Idempotency

같은 요청이 네트워크 재시도로 반복되더라도 중복 송금이 발생하지 않도록 idempotency key를 사용합니다.

### Fail-closed

FDS 오류, timeout, 잘못된 응답, 검증 실패를 정상 거래로 처리하지 않습니다.

---

## Core User Flow

```text
사용자 음성 입력
      ↓
Frontend 녹음 / 전달
      ↓
Spring Backend
      ↓
Voice AI: STT + Intent / Entity
      ↓
Backend 재검증
 ├─ 정보 누락 → 재질문
 ├─ 검증 실패 → 오류 안내
 └─ 검증 완료 → 거래 내용 확인
      ↓
사용자 명시적 확인
      ↓
FDS 평가
 ├─ LOW    → 송금 완료
 ├─ MEDIUM → 송금 완료 + 보호자 알림 요청
 └─ HIGH   → 송금 차단 + 보호자 알림 요청
      ↓
결과 안내
```

<p align="center">
  <img src="./assets/user-flow.png" alt="MOVI 음성 요청 사용자 체험 흐름" width="100%">
</p>

---

## 시스템 구조

```text
[ Frontend ]
     │
     ▼
[ Spring Backend ]
     │
     ├────► Voice AI
     │       STT / Intent / Entity
     │
     ├────► FDS AI
     │       Risk Assessment
     │
     └────► Financial Adapter
             Mock 중심 거래 흐름
```

상세 구조는 [docs/architecture.md](./docs/architecture.md)에 정리되어 있습니다.

---

## 접근성 고려

MOVI에서는 음성을 유일한 입력 방식으로 두지 않았습니다.

- 키보드 / 터치 기반 대체 조작
- 거래 내용 화면 재확인
- 오류 시 focus 이동
- 상태를 색상만으로 구분하지 않음
- 결과 텍스트 제공

### 현재 확인한 범위

- Keyboard-only
  - 로그인 일부 흐름
  - 잔액조회 핵심 흐름
  - 송금 수취인 선택
- 200% 확대
  - Login
  - Accounts
  - Balance
  - Transfer
  - Review
  - Result

VoiceOver / TalkBack 및 실제 시각장애 사용자 대상 검증은 아직 수행하지 않았습니다.

자세한 기록:
- [Frontend Accessibility Validation](https://github.com/moonaneul/movi_frontend/blob/main/docs/ACCESSIBILITY_VALIDATION.md)

---

## 팀 역할

4인 팀으로 진행했습니다.

문하늘은 주로 다음 범위를 담당했습니다.

- 사용자 문제와 서비스 요구사항 정의
- Frontend / Backend / AI 책임 경계 정리
- API / 검증 흐름 설계
- Frontend / Backend 구현 및 통합 검증
- 접근성 수동 점검

AI 모델 및 AI 서버 구현은 별도 팀원이 담당했습니다.

자세한 내용:
- [Contribution](./docs/contribution.md)

---

## 관련 저장소

- [MOVI Backend](https://github.com/moonaneul/movi_backend)
- [MOVI Frontend](https://github.com/moonaneul/movi_frontend)
- [MOVI AI](https://github.com/moonaneul/movi_ai)

---

## 문서

- [Architecture](./docs/architecture.md)
- [Contribution](./docs/contribution.md)
- [Validation](./docs/validation.md)
- [Limitations](./docs/limitations.md)
