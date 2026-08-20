# Tesso — Address List Dialog (Desktop)

Framework B `dialog/addressList` 의 데스크탑(뷰포트 ≥ 768) 구현.
Figma node `2865:19307` (device=desktop) — 배송지 목록 팝업 창.

의존성 없는 단일 HTML 파일입니다. `index.html` 을 브라우저로 열면 바로 확인됩니다.

```
index.html      # 마크업 + 전체 스타일
assets/         # Figma 익스포트 (window-buttons, url-graphic, check, plus)
```

단위 규칙은 [`../disclosure-desktop/`](../disclosure-desktop/) 와 동일합니다.

## 구성

브라우저 팝업 창 형태입니다 — 창 크롬(신호등 + URL 바) → 헤더(배송지 목록) →
스크롤 목록(배송지 4건) → 하단 `배송지 추가` 버튼.

목록의 `선택` 버튼을 누르면 해당 항목이 `선택됨`(배경 #111 + 체크 아이콘)으로 바뀌고
나머지는 `선택`으로 돌아갑니다.

## 단위 체계

디자인 기준 폭 **1920**.

### 1. 타이포그래피

`font-size = (size value − 1) × 0.0083 × screen width + 1rem` (size value = rem, **1rem = 14px**)
`0.0083 × screen width` = `0.83vw`. size value ≤ 1rem 이면 뷰포트와 무관하게 고정.

| 토큰 | 쓰임 | @768 | @1440 | @1920 | 처리 |
|---|---|---|---|---|---|
| `caption-2` 12px | 기본 배송지 뱃지 | 12px | 12px | 12px | 고정 |
| `button-label-small` 12px | 수정 / 삭제 / 선택 | 12px | 12px | 12px | 고정 |
| `button-label-large` 13px | 배송지 추가 | 13px | 13px | 13px | 고정 |
| `paragraph-4` 13px | 이름 · 연락처 · 요청사항 | 13px | 13px | 13px | 고정 |
| `paragraph-3` 14px | 주소 | 14px | 14px | 14px | 고정 — 1rem |
| `paragraph-2` 16px | 헤더 타이틀 | 14.91px | 15.71px | 16.27px | **가변** |

본문 대부분이 1rem 이하라 이 화면도 가변인 것은 헤더 타이틀 하나뿐입니다.

#### 타이틀 사이즈 전환

헤더 타이틀을 P1~P4 로 바꿔 볼 수 있습니다 (우하단 데모 패널, 기본값 P2).

| | @768 | @1440 | @1920 | 처리 |
|---|---|---|---|---|
| P1 (18px) | 15.82px | 17.41px | 18.55px | 가변 |
| **P2 (16px, 기본)** | 14.91px | 15.71px | 16.27px | 가변 |
| P3 (14px) | 14px | 14px | 14px | **고정** |
| P4 (13px) | 13px | 13px | 13px | **고정** |

### 2. spacing

```css
--us: calc(100vw / 1920);
--mid-4:  4px;                          /* 값 < 8 → 값 그대로 고정 */
--mid-8:  8px;                          /* 값 = 8, 증감 없음 */
--mid-12: calc( 4 * var(--us) + 8px);
--mid-16: calc( 8 * var(--us) + 8px);
--mid-20: calc(12 * var(--us) + 8px);
--mid-24: calc(16 * var(--us) + 8px);
```

| | @768 | @1440 | @1920 |
|---|---|---|---|
| `mid-12` | 9.59px | 11px | 12px |
| `mid-16` | 11.19px | 14px | 16px |
| `mid-20` | 12.80px | 17px | 20px |
| `mid-24` | 14.39px | 20px | 24px |

| 영역 | 값 |
|---|---|
| header | padding `mid-20 / mid-24 / mid-8` |
| content | padding `mid-8 / mid-24 / mid-24` |
| addressListItem | padding `mid-20 / 0 / mid-16`, gap `mid-12` |
| textArea | gap `mid-16` → `mid-12` → `mid-4` |
| info · request | gap `mid-8` |
| 수정/삭제 buttonGroup | gap `mid-12` |
| buttonArea | padding `mid-12 / mid-24 / mid-24` |
| URL 바 | gap `mid-12` |

### 3. 다이얼로그 box — 세 가지 안

| 안 | 가로 너비 | 내부 여백 |
|---|---|---|
| **560 + mid** (기본) | 560px 고정 | `mid-*` 가변 |
| **560 + px** | 560px 고정 | @1920 값으로 px 고정 |
| **vw + mid** | 29.1667vw 가변 | `mid-*` 가변 |

```css
--dialog-w-fixed: 560px;
--dialog-w-fluid: 29.1667vw;         /* 560 / 1920 */
--dialog-w: var(--dialog-w-fixed);   /* 기본 */

body[data-box="fixed-px"] {
  --mid-4: 4px; --mid-8: 8px; --mid-12: 12px;
  --mid-16: 16px; --mid-20: 20px; --mid-24: 24px;
}
body[data-box="fluid-mid"] { --dialog-w: var(--dialog-w-fluid); }
```

세로는 목록 영역 600px + 상하 영역으로 최대 800px 이고, `max-height: min(800px, 100%)`
를 걸어 세로가 짧은 창에서는 창 높이에 맞춰 줄고 목록이 스크롤됩니다.

### 4. 그 외 px

창 크롬 36 / 32px · 신호등 52×12 · favicon 22px · 라운드 10px,
목록 영역 600px, 뱃지 padding 6/8px, 수정·삭제 padding 9px,
선택 버튼 padding 9/15px · 체크 14px, 추가 버튼 padding 14/22px · plus 16px,
divider 1×12px, hairline 1px.

### 5. 폰트

```css
--font: "Work Sans", "Pretendard Variable", Pretendard, sans-serif;
```

Work Sans 에 한글 글리프가 없어 한글만 Pretendard 로 폴백됩니다. `[12345]`, `010-1234-1234`
같은 숫자는 Work Sans, 국문은 Pretendard 로 문자 단위 분기됩니다. URL 바는 시안대로
Arial 입니다.

## 검증

| | 768 × 900 | 1440 × 900 | 1920 × 992 |
|---|---|---|---|
| dialog · 560 + mid | 560 × 782.6 | 560 × 795.1 | 560 × 800 |
| dialog · 560 + px | 560 × 800 | 560 × 800 | 560 × 800 |
| dialog · vw + mid | 224 × 782.6 | 420 × 795.1 | 560 × 800 |
| 타이틀 P1 / P2 / P3 / P4 | 15.82 / 14.91 / 14 / 13px | 17.41 / 15.71 / 14 / 13px | 18.55 / 16.27 / 14 / 13px |
| `mid-12` / `16` / `20` / `24` | 9.59 / 11.19 / 12.80 / 14.39px | 11 / 14 / 17 / 20px | 12 / 16 / 20 / 24px |
| 목록 스크롤 · 선택 전환 | 정상 | 정상 | 정상 |
| 가로 스크롤 | 없음 | 없음 | 없음 |

아이콘 실측: 신호등 52×12, favicon 22×22, 체크 박스 14×14 / leaf 6.31×4.18,
plus 박스 16×16 / leaf 8×8 — 모두 Figma 익스포트 원본 그대로입니다.

## 참고

- **창 크롬(신호등 + URL 바)** — 모바일에서 뺐던 디바이스 목업과 달리, 이 화면에서는
  시안상 dialog 컴포넌트 안에 포함된 팝업 창 표현이라 그대로 구현했습니다. 빼려면
  `.win-bar` / `.win-url` 블록과 마크업만 지우면 됩니다.
- 우하단 데모 패널은 시안에 없는 요소입니다. `.demo-nav` / `.demo-seg` / `.demo-readout`
  CSS 와 `#demoNav` 마크업, 그 아래 스크립트를 지우면 됩니다.
- `vw + mid` 안에는 min/max 를 걸지 않았습니다. 768 에서 224px 까지 좁아집니다.
