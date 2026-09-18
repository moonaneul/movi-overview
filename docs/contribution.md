# MOVI Contribution

이 문서는 MOVI 팀 프로젝트에서 **문하늘이 실제로 수행한 범위**와 팀 전체 작업을 구분하기 위한 기여도 기록입니다.

포트폴리오와 면접에서는 이 문서를 기준으로 개인 기여를 설명합니다.

---

## 1. Team Structure

MOVI는 **4인 팀 프로젝트**입니다.

- AI 담당 1명
- 나머지 3명은 기능별로 Frontend / Backend 작업을 나누어 진행

문하늘은 **AI 모델이나 AI 서버를 직접 개발하지 않았습니다.**

대신 서비스 문제와 요구사항을 정리하고, Frontend–Backend–AI 사이의 책임 경계를 문서화한 뒤, Backend와 Frontend의 일부 기능을 **AI 코딩 도구를 활용해 구현하고 직접 동작을 검증**했습니다.

---

## 2. My Main Contribution

제가 맡은 역할을 한 문장으로 정리하면 다음과 같습니다.

> **사용자 문제와 서비스 요구사항을 정의하고, Frontend–Backend–AI 간 책임 경계와 API/검증 구조를 설계한 뒤 AI 코딩 도구를 활용해 구현하고 실제 동작 여부를 반복 검증했습니다.**

핵심은 코드량이 아니라 다음 네 단계입니다.

1. 문제와 실패 상황 정의
2. 각 시스템의 책임과 계약 정의
3. AI-assisted implementation
4. 실제 동작 및 계약 검증

---

## 3. Directly Designed / Defined

다음 항목은 제가 직접 문제와 기준을 정리하거나 문서화한 내용입니다.

### Frontend–Backend–AI responsibility boundary

- 음성 입력은 Frontend → Backend → AI 순서로 전달
- AI는 STT와 Intent / Entity 해석 담당
- Backend는 세션, 슬롯, 실제 계좌/수취인 조회, 소유권, 한도, 거래 상태의 최종 책임 담당
- Frontend는 AI나 FDS를 직접 호출하지 않음
- AI가 추출한 값을 그대로 금융 실행값으로 사용하지 않음

근거:
- [Backend PR #3 — Front / AI / Backend 통합 명세 수립](https://github.com/movi-ai-challenge/movi_backend/pull/3)

### Voice transfer flow

- 필수 정보 누락 시 재질문
- 필요한 정보가 모두 확보된 뒤 거래 내용 재확인
- 사용자 명시적 확인 이후에만 FDS 및 거래 실행 단계 진행
- 확인 이후 금액·수취인·계좌가 바뀌면 이전 확인을 그대로 사용하지 않도록 설계
- 세션 만료 및 재질문 조건 정리

근거:
- [Backend PR #3](https://github.com/movi-ai-challenge/movi_backend/pull/3)
- [Backend PR #35 — 음성 이체 명령 및 실행 흐름 구현](https://github.com/movi-ai-challenge/movi_backend/pull/35)

### Failure / safety policy

- FDS timeout 또는 잘못된 응답을 정상 거래로 간주하지 않음
- 동일 요청이 반복될 수 있는 상황에서 idempotency 적용
- 거래 직전 잔액 및 한도 재검증
- AI confidence가 높더라도 실제 금융 데이터 검증을 생략하지 않음

근거:
- [Backend PR #3](https://github.com/movi-ai-challenge/movi_backend/pull/3)
- [Backend PR #35](https://github.com/movi-ai-challenge/movi_backend/pull/35)

---

## 4. AI-assisted Implementation

코드 구현에는 **Claude / Codex 등 AI 코딩 도구를 많이 활용했습니다.**

따라서 PR의 변경 라인 수나 파일 수를 곧바로 “직접 수작업으로 작성한 코드량”으로 표현하지 않습니다.

제가 직접 수행한 부분은 다음과 같습니다.

- 요구사항과 정상/실패 흐름 정의
- 구현해야 할 API와 상태 전이 결정
- AI 코딩 도구에 구현 요구 전달
- 생성된 코드 실행
- 테스트 실패와 실제 동작 확인
- Frontend / Backend 계약 불일치 확인
- 요구사항과 다르게 동작하는 부분 수정
- 문서와 실제 코드를 반복 대조

### Backend implementation evidence

[Backend PR #35](https://github.com/movi-ai-challenge/movi_backend/pull/35)에서 다음 흐름이 구현되었습니다.

- AI 분석 결과 Backend 검증
- 누락 슬롯 재질문
- 사용자 확인 / 취소
- LOW / MEDIUM / HIGH FDS 결과 분기
- idempotency 기반 중복 실행 방어
- 거래 직전 잔액 재검증
- Mock / 실제 adapter 선택 구조
- 민감정보 노출 제한

이 구현에는 AI 코딩 도구가 활용되었으므로, 포트폴리오에서는 **“설계·AI-assisted 구현·검증”**으로 표현합니다.

### Frontend integration evidence

[Frontend PR #28](https://github.com/movi-ai-challenge/movi_frontend/pull/28)에서는 다음을 통합했습니다.

- Backend 등록 수취인 API 연결
- 서버 review
- explicit confirmation
- UUID idempotency
- 실행 및 결과 상태 표시
- Backend 응답 계약 검증

[Frontend PR #29](https://github.com/movi-ai-challenge/movi_frontend/pull/29)에서는 FDS의 trusted-device 판단에 필요한 `deviceUuid`가 실제 요청 흐름에서 빠져 있던 문제를 확인하고 Frontend 요청에 연결했습니다.

---

## 5. Contract / Integration Validation

제가 반복적으로 수행한 작업 중 하나는 **문서상 계약과 실제 구현이 일치하는지 확인하는 것**이었습니다.

### Backend → Frontend contract audit

[Frontend PR #14](https://github.com/movi-ai-challenge/movi_frontend/pull/14)

- Backend 통합 명세와 실제 Controller / DTO / Security 설정 대조
- Voice / FDS / 보호자 정책 확인
- Frontend 구현과 Backend API 간 차이 기록
- 인증 callback, OpenBanking callback, CORS 등 확인이 필요한 항목 분리

### Direct transfer integration

[Frontend PR #28](https://github.com/movi-ai-challenge/movi_frontend/pull/28)

- 고정된 Mock 수취인 흐름을 Backend 등록 수취인 API 기반 흐름으로 교체
- 서버 검토 → 명시적 확인 → 실행 → 결과 표시까지 연결
- Frontend가 기대하는 계약과 Backend 실제 응답을 맞춤

이 부분은 MOVI에서 제가 강조할 수 있는 핵심 경험입니다.

> **기능을 따로 구현하는 것보다 파트 사이의 책임과 실제 계약이 일치하는지 검증하는 역할을 수행했습니다.**

---

## 6. Evidence Map

| 영역 | 내가 수행한 내용 | GitHub 근거 | 상태 |
|---|---|---|---|
| 시스템 책임 분리 | Front / Backend / AI 단일 책임 및 호출 구조 정의 | Backend PR #3 | Merged |
| Voice / FDS 계약 | 요청·응답·오류·timeout·Mock 정책 정리 | Backend PR #3 | Merged |
| 음성 이체 흐름 | 재질문 → 확인 → FDS → 실행/차단 | Backend PR #35 | Merged |
| 중복 거래 방어 | idempotency 및 상태 조회 흐름 | Backend PR #35 | Merged |
| Backend–Frontend 계약 검토 | Controller / DTO / 정책 대조 | Frontend PR #14 | Merged |
| 직접 입력 송금 통합 | review → confirmation → execution | Frontend PR #28 | Merged |
| FDS 기기정보 연결 | deviceUuid 누락 문제 확인 및 연결 | Frontend PR #29 | Merged |

> Open 상태의 PR은 완료된 성과로 계산하지 않습니다.

---

## 7. Team Work vs. My Work

### Team-level capabilities

다음은 MOVI 전체 시스템의 기능이며, 제 개인 단독 개발 결과로 표현하지 않습니다.

- Next.js 기반 전체 Frontend
- Spring 기반 전체 Backend
- 사용자 인증 전체 시스템
- 계좌 / 거래내역 전체 기능
- STT
- Intent / Entity 추출
- FDS AI
- 보호자 알림 전체 시스템
- OpenBanking adapter 전체 구조

### My verified scope

포트폴리오에서는 다음 범위를 제 기여로 설명합니다.

- 서비스 문제 및 실패 상황 구조화
- Frontend–Backend–AI 역할 경계와 통합 계약
- Voice / FDS 연동 조건과 오류 정책
- 음성 이체 Backend 흐름의 AI-assisted 구현 및 검증
- 직접 송금 Frontend 통합
- Backend–Frontend 계약 audit
- 테스트를 통해 발견한 통합 문제 수정

---

## 8. Not My Work

다음 항목은 제 개인 수행으로 표현하지 않습니다.

- AI 모델 연구 / 학습
- STT 모델 개발
- LLM Intent / Entity 모델 개발
- FDS 모델 개발
- AI 서버 구현
- 실제 금융기관 OpenBanking 운영
- 실제 SMS 서비스 운영

---

## 9. Current Evidence Gaps

아직 개인 성과로 확정해서 표현하지 않는 부분입니다.

### 실제 접근성 사용자 검증

현재 UI와 코드에서 접근성 대안을 고려한 흔적은 있지만,

- 실제 시각장애 사용자 usability test
- VoiceOver 실기기 전체 흐름
- TalkBack 실기기 전체 흐름
- 200% 확대 전체 흐름

은 별도 검증 결과가 아직 필요합니다.

### 실제 금융망 운영

프로젝트의 핵심 검증은 Mock 금융 환경을 중심으로 진행되었습니다.

따라서 “실제 OpenBanking을 운영했다”, “실제 은행 송금을 구축했다”는 표현은 사용하지 않습니다.

---

## 10. Interview-safe Description

면접에서는 다음 수준으로 설명하는 것이 현재 증거와 가장 잘 맞습니다.

> “MOVI에서 AI 모델 자체를 개발한 것은 아닙니다. 저는 사용자가 음성으로 금융 기능을 사용할 때 AI의 인식 결과를 어디까지 신뢰할지와 Frontend·Backend·AI의 책임을 정리했습니다. AI가 추출한 금액과 수취인을 Backend에서 다시 검증하고, 누락 정보는 재질문하며, 사용자가 거래 내용을 다시 확인한 뒤에만 송금하도록 흐름을 설계했습니다. 구현에는 Claude와 Codex를 많이 활용했고, 저는 생성된 코드가 정의한 계약과 실제 동작에 맞는지 테스트하고 통합 오류를 수정하는 역할을 했습니다.”

이 설명보다 개인 구현 범위를 크게 표현할 때는 추가 증거 확인이 필요합니다.
