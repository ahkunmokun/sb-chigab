# 💶 금전출납부 · Kassenbuch

베를린 거주 한국인 프리랜서 / 예술감독을 위한 **이중 통화 금전출납부**입니다.
영수증을 사진으로 찍으면 브라우저 안에서 바로 글자를 읽어(OCR) 거래를 자동으로 채워줍니다.

- **단일 HTML 파일** (`ledger.html`) — 서버 불필요, 설치 불필요
- **완전 브라우저 내장** — 데이터는 내 폰·PC 안(localStorage)에만 저장, 외부로 안 나감
- **무료** — Tesseract.js(OCR) + Frankfurter(환율) 모두 무료, API 키 없음
- **모바일 최우선** — URL만 열면 폰에서 바로 사용, 다크모드 자동

---

## 주요 기능

### 📷 영수증 스캔 (OCR)
- 폰: 카메라로 바로 촬영 / PC: 파일 선택 또는 드래그앤드롭
- **한국어 + 독일어 + 영어** 동시 인식 (Tesseract.js v5)
- 날짜·금액·통화·카테고리를 자동 추출해 입력 폼에 채움
- 신뢰도 60% 미만이면 직접 확인 유도
- **영수증 이미지는 저장하지 않음** — 인식 직후 즉시 메모리 해제

### 💱 이중 통화 + 환율 자동 업데이트
- EUR(독일) · KRW(한국) 원본 저장, USD(클로드 구독 등)는 입력일 환율로 KRW 병기
- 앱 실행 시 **유럽중앙은행(ECB) 기준 환율** 자동 fetch (하루 1회, 캐시)
- 오프라인이면 마지막 저장 환율을 조용히 사용
- 설정에서 수동 갱신 / 자동 업데이트 중단 / 값 직접 입력 가능

### 📒 거래 기록
- 큰 스캔 버튼(폰 하단 엄지 위치) + 수동 입력
- 요약 카드: EUR/KRW 수입·지출, EUR 환산 순이익, 건수
- 월별 / 수입·지출 / 통화 / 국가(DE·KR) 필터

### 📤 세금 내보내기 (CSV, UTF-8 BOM)
연도 선택 후 미리보기 → CSV 3종 다운로드:
1. **독일 EÜR** (Einnahmen-Überschuss-Rechnung, EUR 기준)
2. **Kassenbuch** (GoBD 형식, 날짜순 잔액 누적)
3. **한국 종합소득세 내역** (KRW 수입/지출)

> 집세는 설정의 **사업용 비율(%)** 만큼만 경비로 반영됩니다 (현재 0%, 2026.04부터 조정 예정).
> 모든 CSV는 BOM 포함이라 Excel에서 한글이 깨지지 않습니다.

### ⚙️ 설정
환율 관리 · 집세 사업용 비율(독/한 각각) · 카테고리 추가/삭제 · 전체 백업(JSON)/복원/삭제

---

## 바로 쓰기

### 1. 가장 간단 — 파일만 열기
`ledger.html`을 더블클릭하거나 브라우저로 열면 끝입니다.
(단, OCR 엔진과 환율은 인터넷 연결이 있어야 처음 로드됩니다.)

### 2. 폰에서 쓰기 — GitHub Pages 배포 (권장)
URL로 어디서나 열 수 있게 무료로 올립니다.

```
1. GitHub에서 새 저장소 생성 (예: ledger)
2. ledger.html 을 업로드 → 파일명을 index.html 로 바꾸면 더 깔끔
   (그대로 ledger.html 둬도 됩니다)
3. 저장소 → Settings → Pages
   - Source: "Deploy from a branch"
   - Branch: main / (root) 선택 → Save
4. 1~2분 후 https://<아이디>.github.io/ledger/ 접속
   (index.html 이 아니면 .../ledger/ledger.html)
```

폰 브라우저에서 그 주소를 연 뒤 **"홈 화면에 추가"** 하면 앱처럼 쓸 수 있습니다.

> 💡 카메라 촬영(`capture`)과 OCR은 보안상 **https** 환경에서만 동작합니다.
> GitHub Pages는 기본 https라 문제없습니다. (로컬 `file://`에서는 카메라가 막힐 수 있어요.)

---

## 데이터 보관 & 백업

- 모든 데이터는 브라우저 **localStorage**(`raee_ledger_v1`, `raee_fx_cache`)에 저장됩니다.
- **같은 기기·같은 브라우저**에서만 보입니다. 캐시를 지우면 사라집니다.
- 정기적으로 **설정 → 백업(JSON)** 으로 내려받아 두세요. 새 기기에선 "불러오기"로 복원합니다.

---

## 기술 메모

| 항목 | 사용 |
|---|---|
| OCR | [Tesseract.js v5](https://cdn.jsdelivr.net/npm/tesseract.js@5/dist/tesseract.min.js) (kor+deu+eng) |
| 환율 | [Frankfurter API](https://frankfurter.dev) — `base=EUR&quotes=KRW,USD`, ECB 기준 |
| 크로스 환율 | `1 USD = ₩` = `rates.KRW / rates.USD` |
| 저장소 | 브라우저 localStorage (서버 없음) |
| 참고 | [drake7707/ocr-receipt](https://github.com/drake7707/ocr-receipt) |

기본 환율: `1 EUR = 1,480 KRW` / `1 USD = 1,370 KRW` (실행 시 자동 갱신)

---

## 카테고리 (기본값)

- **독일 지출(EUR)**: 집세(DE) · KSK · 전기세(DE) · 핸드폰(DE) · 기타지출(DE)
- **한국 지출(KRW)**: 집세(KR) · 건강보험 · 핸드폰(KR) · 전기세(KR) · 밀리의서재 · 클로드구독(USD) · 기타지출(KR)
- **수입(독일)**: BM e.V. 강의료 · MDC 수강료 · ANTHROPOLIS 연출료 · 기타수입(DE)
- **수입(한국)**: 우주머니액팅 코칭 · 국립극장/공연기관 · AF책/인세 · 기타수입(KR)

설정 탭에서 자유롭게 추가/삭제할 수 있습니다.
