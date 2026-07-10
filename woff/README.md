# WOFF 미니앱 — PoC #1 (F5-01) 실행 runbook

> **목적**: 무료 테넌트에서 WOFF 앱을 등록하고 톡룸에 **실제 렌더**되는지 self-test.
> flagship(WOFF 무설치 미니앱)의 유일한 load-bearing 미검증 가정 해소 → PLAUSIBLE → CONFIRMED 격상 관문.
> SoT: `cheesestock-android/docs/decision/works_b2b_deep_dive_roadmap_2026-07-10.md` §5.
> **경계**: read-only · info-only · 유료화/★등급 확산/자문성 코멘트 도입 = **변호사 게이트 전 금지**.

`index.html` = WOFF endpoint. `cheesestock.github.io` 동일 오리진 manifest(`/manifest/v1/*.json.gz`) 소비 → CORS 0.
자율 검증 완료: gzip 해제·필드 정합(KeyError 0)·JS 문법 OK·경계 OFF 호출 0.

---

## Step 0 — 로컬 미리보기 (WORKS 밖, 렌더 절반 self-검증)

WORKS 등록 전에 일반 브라우저로 **데이터 렌더가 되는지** 먼저 확인(가장 싸게 리스크 절반 해소).

```powershell
# ⚠️ repo 루트에서 서빙해야 /manifest/v1/... 절대경로가 해석됨 (woff/ 아님)
cd C:\cheesestock-data
python -m http.server 8080
# 브라우저에서 http://localhost:8080/woff/ 열기
```

**확인**: ① KOSPI/KOSDAQ Top10 렌더(삼성전자 1625조·▲/▼ 등락색) ② 닮은꼴 자기-과거 유사도 분포(3,000건) ③ PoC 패널 = "SDK 미로드(일반 브라우저)"·init "skip" (정상 — WOFF 밖이라 SDK 없음, 데이터는 렌더됨).

---

## Step 1 — Developer Console WOFF 앱 등록

`developers.worksmobile.com` → 해당 앱 → **API app › WOFF app › Register**:
- **WOFF name**: `CheeseStock 시세` (max 20자)
- **Endpoint URL**: `https://cheesestock.github.io/woff/`  (HTTPS·max 500자·fragment `#` 금지)
- **App size**: `Full`

→ **woffId 발급** (예: `1654426689BR7LN87b`).

> ✅ **Self-test Q1**: 무료 테넌트에서 이 등록이 **admin consent 없이 self-approve** 되는가?
> (P4 실측: board/calendar scope는 Dev Console self-approve였음 — WOFF도 동일한지 확인. 여기서 막히면 flagship 유통 경로 재설계 필요.)

---

## Step 2 — woffId 주입

`woff/index.html`의 상단 상수 교체:
```js
var WOFF_ID = "REPLACE_WITH_WOFF_ID";   // → 발급받은 woffId
```

---

## Step 3 — 배포 (GitHub Pages)

```powershell
# git -C 절대경로 (android repo git 과 분리 — feedback_bash_cwd_revert_worktree_git_c)
git -C C:\cheesestock-data add woff/
git -C C:\cheesestock-data commit -m "feat(woff): PoC #1 WOFF 미니앱 (시세 랭킹·닮은꼴 분포)"
git -C C:\cheesestock-data push
```
몇 분 후 `https://cheesestock.github.io/woff/` HTTP 200 확인(브라우저).

---

## Step 4 — 톡룸 렌더 검증 (핵심)

WORKS 모바일 앱에서 WOFF 진입 — 두 경로:
- **(A) WOFF URL 직접**: Console 발급 WOFF URL을 자신에게 전송 → 탭.
- **(B) 봇 딥링크(private 경로)**: 기존 봇 `Cheddar`가 WOFF URL을 링크 메시지로 발송 → 탭. (App Directory 공개 없이 개인 테넌트 내부 private 실행 여부 확인)

톡룸 인앱에서 페이지 하단 **PoC 검증 패널** 펼쳐 값 기록:

| 항목 | 기대값 | 실측 |
|---|---|---|
| SDK 로드 | OK (3.7.1) | |
| isInClient | true | |
| viewType | full | |
| clientType | MOBILE_APP | |
| init 결과 | 성공 | |
| manifest | OK basDt … | |

> ✅ **Self-test Q2**: 톡룸 인앱에서 랭킹·분포가 **실제 렌더**되는가?
> ✅ **Self-test Q3**: **봇 딥링크(B)** 로 App Directory 공개 없이 private 실행되는가?
> ✅ **Self-test Q4**: `getAccessToken` 미사용으로도 렌더 정상인가? → PII 표면 0 확인.

---

## 판정

4개 self-test(Q1~Q4) 결과를 `user_upcoming_naver_works_employment` 잔여에 기록:
- **전부 성공** → F5-01 **PLAUSIBLE → CONFIRMED** 격상. flagship 딜리버리 실증.
- **Q1/Q3 실패**(등록 막힘·private 불가) → 유통 경로 재설계(App Directory 심사·유료 gating 여부) 별 트랙.

**이후 확장(전부 변호사 게이트 BLOCKING)**: 차트 캔들 렌더(candles_embedded)·유료 gating(getProfile 티어식별)·★등급 확산 표면 → §101 대가+조언 교차 재판정.
