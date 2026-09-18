# MOVI Visual Assets Guide

이 문서는 MOVI Overview에서 어떤 원본 시각자료를 사용하고, 어떤 자료는 그대로 사용하지 않을지 정리한 가이드입니다.

목표는 **보기 좋은 그림을 많이 넣는 것**이 아니라,  
현재 GitHub와 문서로 검증된 범위를 오해 없이 보여주는 것입니다.

---

## 1. Recommended Visuals

### A. 사용자 체험 흐름 — 우선 사용

원본 제목: **실시간 의도 분석 체험 플로우**

이 그림은 사용자가 MOVI를 어떻게 경험하는지 한눈에 보여주기 때문에 Overview에 활용 가치가 높습니다.

핵심 흐름:

```text
마이크 켜기
→ 말로 요청
→ 처리 기다리기
→ 요청 이해 / 확인 질문
→ 대답 보내기
→ 결과 확인
```

### 사용 위치

README의 **Core User Flow** 아래.

### 사용할 때 주의

- “실시간 스트리밍이 최종 production 경로로 검증되었다”는 의미로 보이지 않게 합니다.
- 사용자 경험 흐름을 보여주는 **시연/기획 시각자료**로 사용합니다.
- 그림의 목적은 시스템 구현 완료 여부가 아니라 UX flow 설명입니다.

추천 캡션:

> **Original demo flow** — 음성 요청부터 확인 질문과 결과 확인까지의 사용자 경험을 정리한 시연 자료. 세부 연동 방식은 이후 구현 과정에서 변경될 수 있습니다.

---

### B. 심사위원 핵심 시연 플로우 — 선택 사용

원본 제목: **심사위원 핵심 시연 플로우**

```text
시작하기
→ PIN 로그인
→ 음성 요청
→ 요청 이해
→ 송금 확인
→ 안전 결과
→ 다음 행동
```

이 그림은 프로젝트 핵심 가치인

- 음성 요청
- 거래 내용 확인
- 실행 전 사용자 확인
- 위험 결과 확인

을 짧게 보여주기 좋습니다.

### 사용 위치

README 상단 Project Overview 뒤 또는 포트폴리오 PDF의 MOVI 첫 페이지.

### 사용할 때 주의

원본의 “실제 API 로그인” 같은 표현은 **현재 portfolio claim으로 그대로 반복하지 않습니다.**

추천 캡션:

> **Demo scenario** — 사용자가 요청 내용을 다시 확인한 뒤에만 금융 실행 단계로 넘어가도록 구성한 핵심 시연 흐름.

---

## 2. Use Only as Supporting Material

### 이상거래 탐지 플로우

원본에는 다음과 같은 팀 전체 설계가 포함되어 있습니다.

- Rule + Isolation Forest
- 모델 / 규칙 결합
- LOW / MEDIUM / HIGH
- OpenBanking
- 보호자 SMS

이 그림은 FDS 팀 설계를 이해하는 보조자료로는 사용할 수 있지만,  
문하늘 개인 구현 범위를 보여주는 대표 그림으로 사용하지 않습니다.

### 이유

- FDS AI 모델은 문하늘 담당이 아님
- 모델 성능은 문하늘 개인 성과가 아님
- 실제 OpenBanking / SMS 운영으로 오해될 수 있음

사용한다면 반드시:

> 팀 전체 FDS 설계 자료이며 AI 모델 구현은 다른 팀원이 담당했습니다.

라고 표시합니다.

---

## 3. Do Not Use as Current Architecture Without Revision

### 전체 시스템 아키텍처

디자인은 좋지만, 현재 Overview의 대표 Architecture로 그대로 쓰지 않습니다.

원본에는:

- OpenBanking API
- Solapi SMS
- WebSocket / streaming
- 외부 시스템 연동

이 실제 운영된 것처럼 보일 수 있습니다.

현재 포트폴리오에서는 GitHub로 검증된 범위에 맞춰:

```text
User
 ↓
Frontend
 ↓
Spring Backend
 ├─ Voice AI
 ├─ FDS AI
 └─ Mock Financial Adapter
```

구조를 기준으로 합니다.

현재 검증된 Architecture는 [architecture.md](./architecture.md)의 Mermaid diagram을 기준으로 사용합니다.

---

### 실시간 스트리밍 의도 분석

원본 자료에는 WebSocket 실시간 streaming, Google Cloud STT, OpenAI 의도 분석 등이 구체적인 실행 경로로 표현되어 있습니다.

이 자료는 당시 기술 설계 / 시연 자료로 보관하되,  
현재 Overview의 대표 그림으로 사용하지 않습니다.

이유:

- 최종 검증된 핵심 경로와 presentation 당시 구조를 혼동할 수 있음
- streaming 완료 여부가 과장될 수 있음
- AI 구현이 개인 기여처럼 보일 가능성이 있음

---

## 4. Final README Visual Set

MOVI Overview README에는 최종적으로 **2개 정도의 그림만** 사용하는 것을 권장합니다.

### Visual 1 — User Flow

**실시간 의도 분석 체험 플로우** 또는 **심사위원 핵심 시연 플로우**

목적:

> 사용자가 음성 요청을 하고, 거래 내용을 확인하고, 결과를 확인하는 UX를 보여준다.

### Visual 2 — Verified Architecture

현재 [architecture.md](./architecture.md)의 검증된 구조를 기준으로 사용.

목적:

> AI가 해석하고 Backend가 금융 상태를 검증·실행하는 책임 경계를 보여준다.

---

## 5. Visuals We Should Not Add Just for Portfolio

다음 그림은 취업용으로 새로 만들 필요가 없습니다.

- 기술 스택 로고 모음
- AI 모델 상세 내부 구조
- FDS 알고리즘 세부 수식
- 불필요하게 복잡한 ERD 전체
- 모든 API endpoint 목록
- 기능을 과장하기 위한 “enterprise architecture” 그림

MOVI에서 시각자료가 증명해야 할 것은 다음 두 가지면 충분합니다.

> **사용자가 어떤 흐름으로 안전하게 거래하는가**

> **AI와 금융 실행의 책임을 어떻게 분리했는가**

---

## 6. Evidence Rule for Images

원본 발표 자료에 있는 내용과 최종 구현이 다를 경우:

1. GitHub 최신 코드 / 문서를 우선
2. 발표 자료는 “당시 설계 / 시연 자료”로 표시
3. 실제로 구현·검증되지 않은 연동은 현재 상태처럼 표현하지 않음
4. 팀 전체 AI/FDS 설계를 개인 개발 성과로 표현하지 않음

이 기준은 README와 포트폴리오 PDF 모두 동일하게 적용합니다.
