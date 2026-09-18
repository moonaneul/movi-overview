# MOVI Validation

이 문서는 MOVI에서 **이미 검증된 것**과 **앞으로 직접 검증해야 할 것**을 구분해 기록합니다.

원칙은 단순합니다.

> 테스트를 했다는 사실보다, 어떤 조건에서 무엇을 확인했고 어떤 문제가 발견됐는지를 남긴다.

아직 수행하지 않은 검증은 PASS로 표시하지 않습니다.

---

## 1. Validation Status

| 영역 | 현재 상태 | 근거 |
|---|---|---|
| Frontend–Backend–AI 책임 경계 | 검증됨 | 통합 명세 / 계약 문서 |
| 음성 이체 상태 흐름 | 검증됨 | Backend 구현 및 테스트 |
| 누락 정보 재질문 | 검증됨 | Backend 구현 및 테스트 |
| 거래 확인 / 취소 | 검증됨 | Backend 구현 및 Frontend 통합 |
| FDS LOW / MEDIUM / HIGH 분기 | Mock 기준 검증 | Backend 테스트 / Demo scenario |
| idempotency | 검증됨 | Backend 구현 및 테스트 |
| Frontend–Backend contract | 검증됨 | Contract audit / 통합 PR |
| 직접 입력 송금 flow | 구현·검증됨 | Frontend PR #28 |
| 실제 금융기관 OpenBanking E2E | 미검증 | 실제 운영 근거 없음 |
| 실제 시각장애 사용자 usability test | 미수행 | 추가 검증 필요 |
| Keyboard-only 핵심 flow | 확인 필요 | 별도 실측 필요 |
| 200% zoom / reflow | 확인 필요 | 별도 실측 필요 |
| VoiceOver | 확인 필요 | 별도 실기기 검증 필요 |
| TalkBack | 확인 필요 | 별도 실기기 검증 필요 |

---

## 2. Already Verified

### 2.1 Integration contract

Frontend / Backend / AI 간 책임과 호출 구조를 문서화하고 실제 Controller / DTO / Frontend 구현과 대조했습니다.

주요 확인 항목:

- Frontend가 AI를 직접 호출하지 않는지
- AI Intent / Entity 결과를 Backend가 다시 검증하는지
- 세션 / 슬롯 / 확인 상태의 단일 소유자가 Backend인지
- Voice / FDS 오류와 timeout이 명시되어 있는지
- Frontend와 Backend 응답 contract가 실제 구현과 맞는지

근거:
- Backend PR #3
- Frontend PR #14

### 2.2 Voice transfer state flow

다음 상태 흐름을 Backend에서 구현·검증했습니다.

```text
음성 입력
→ AI 분석
→ Backend 검증
→ 누락 정보 재질문
→ 거래 내용 확인
→ 사용자 확인 / 취소
→ FDS
→ 실행 또는 차단
→ 결과 안내
```

근거:
- Backend PR #35

### 2.3 Duplicate execution protection

동일 요청이 재전송되더라도 같은 송금이 반복 실행되지 않도록 idempotency 기반 방어 구조를 사용했습니다.

검증 대상:

- 동일 key 재요청
- timeout 이후 상태 재확인
- 확인 상태와 실행 상태의 연결

근거:
- Backend PR #35
- Frontend PR #28

### 2.4 Frontend direct-transfer integration

Frontend에서 다음 흐름을 실제 Backend contract에 맞춰 연결했습니다.

```text
수취인 선택
→ 서버 review
→ 거래 내용 확인
→ explicit confirmation
→ idempotency key
→ 실행
→ 결과 표시
```

근거:
- Frontend PR #28

---

## 3. Accessibility Validation Plan

MOVI는 시각 중심 UI 이용이 어려운 사용자를 고려한 서비스이므로,  
새 기능 추가보다 **핵심 금융 flow의 실제 접근성 검증**을 우선합니다.

검증 순서는 다음과 같습니다.

1. Keyboard-only
2. 200% zoom / reflow
3. VoiceOver
4. TalkBack
5. 오류 발생 후 focus / recovery
6. 음성을 사용할 수 없는 경우의 대체 입력

---

## 4. Scenario A — Balance Inquiry

### Target flow

```text
로그인
→ 계좌 목록
→ 계좌 선택
→ 잔액 확인
```

### Checklist

| Test | Result | Issue | Fix / Note |
|---|---|---|---|
| Tab만으로 주요 조작 가능 | TODO |  |  |
| Focus 순서가 화면 흐름과 일치 | TODO |  |  |
| 계좌명이 screen reader에서 의미 있게 읽힘 | TODO |  |  |
| 잔액이 숫자만 나열되지 않고 의미 있게 전달 | TODO |  |  |
| 200% 확대 시 내용 잘림 없음 | TODO |  |  |
| 좁은 화면에서도 가로 스크롤 없이 주요 기능 사용 가능 | TODO |  |  |

---

## 5. Scenario B — Transfer

### Target flow

```text
수취인 선택 또는 입력
→ 금액 입력
→ 거래 내용 review
→ 명시적 확인
→ 실행
→ 결과
```

### Checklist

| Test | Result | Issue | Fix / Note |
|---|---|---|---|
| 수취인 선택을 키보드만으로 완료 가능 | TODO |  |  |
| 금액 입력 label이 명확함 | TODO |  |  |
| 확인 화면에서 수취인 / 금액 / 계좌 순서가 이해 가능 | TODO |  |  |
| 확인 버튼과 취소 버튼이 구분됨 | TODO |  |  |
| screen reader가 확인 내용을 의미 있는 순서로 읽음 | TODO |  |  |
| 200% 확대에서 확인 내용과 버튼이 잘리지 않음 | TODO |  |  |
| 오류 발생 후 focus가 안내문으로 이동 | TODO |  |  |

---

## 6. Scenario C — Voice Transfer

### Target flow

```text
마이크 시작
→ 음성 입력
→ AI 해석
→ 누락 정보 재질문 또는 거래 내용 확인
→ 사용자 확인
→ 결과
```

### Checklist

| Test | Result | Issue | Fix / Note |
|---|---|---|---|
| 마이크 권한 거부 시 오류 원인이 전달됨 | TODO |  |  |
| 음성 사용이 어려울 때 다른 입력 방법을 찾을 수 있음 | TODO |  |  |
| 재질문 내용이 현재 빠진 정보를 구체적으로 설명 | TODO |  |  |
| 확인 전 거래가 실행되지 않음 | TODO |  |  |
| 결과를 음성 없이도 화면에서 확인 가능 | TODO |  |  |
| TTS 중에도 핵심 UI를 조작할 수 있음 | TODO |  |  |

---

## 7. Scenario D — Error Recovery

다음 오류는 정상 흐름과 별도로 검증합니다.

### Cases

- Voice 분석 실패
- 낮은 confidence
- 필수 정보 누락
- FDS HIGH 차단
- FDS timeout / 장애
- Network timeout
- 중복 요청
- 만료된 세션
- 잘못된 수취인 / 금액

### Checklist

| Test | Result | Issue | Fix / Note |
|---|---|---|---|
| 오류 메시지가 사용자에게 노출됨 | TODO |  |  |
| 색상만으로 오류를 구분하지 않음 | TODO |  |  |
| 오류 후 다음 행동이 명확함 | TODO |  |  |
| focus가 적절한 오류 안내 또는 조작 요소로 이동 | TODO |  |  |
| 동일 거래가 중복 실행되지 않음 | 검증됨 | - | Backend idempotency |
| FDS 장애 시 거래가 실행되지 않음 | 검증됨 | - | fail-closed |

---

## 8. Test Environment

실제 접근성 검증을 수행할 때 아래 환경을 기록합니다.

| 항목 | 환경 |
|---|---|
| Test date | TODO |
| Frontend commit | TODO |
| Backend commit | TODO |
| Browser | TODO |
| Desktop OS | TODO |
| iOS / VoiceOver | TODO |
| Android / TalkBack | TODO |
| Zoom | 100% / 200% |
| Viewport | TODO |

환경을 기록하지 않은 접근성 테스트는 재현 가능한 검증 결과로 사용하지 않습니다.

---

## 9. Result Summary Template

검증을 마친 뒤 아래 형식으로 요약합니다.

```text
검증 시나리오:
발견된 문제:
수정한 문제:
남은 문제:
다시 검증한 결과:
검증하지 못한 범위:
```

예를 들어 PASS 개수만 보여주기보다,

> “송금 확인 화면에서 오류 발생 후 focus가 body로 이동하는 문제를 발견했고, 안내문으로 focus가 이동하도록 수정한 뒤 재검증했다.”

처럼 **발견 → 판단 → 수정 → 재검증**을 남기는 것을 우선합니다.

---

## 10. Evidence Links

- [My Contribution](./contribution.md)
- [System Architecture](./architecture.md)
- [Limitations & Evidence Boundaries](./limitations.md)
- [Frontend Repository](https://github.com/moonaneul/movi_frontend)
- [Backend Repository](https://github.com/moonaneul/movi_backend)

접근성 실측이 완료되면 실제 실행 환경과 결과를 이 문서에 업데이트합니다.
