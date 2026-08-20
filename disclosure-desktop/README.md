# Tesso — Disclosure Dialog (Desktop)

Framework B `dialog/disclosure` 의 데스크탑(뷰포트 ≥ 768) 구현.
Figma node `2828:20428` (device=desktop) — 개인정보 수집/이용 및 처리 동의 모달.

의존성 없는 단일 HTML 파일입니다 (외부 에셋 없음). `index.html` 을 브라우저로 열면 바로
확인됩니다. 모바일 버전은 [`../disclosure-mobile/`](../disclosure-mobile/) 에 있습니다.

## 단위 체계

디자인 기준 폭 **1920**.

### 1. 타이포그래피

`font-size = (size value − 1) × 0.0083 × screen width + 1rem` (size value = rem, **1rem = 14px**)
`0.0083 × screen width` = `0.83vw`. size value ≤ 1rem 이면 뷰포트와 무관하게 고정.

| 토큰 | @768 | @1440 | @1920 | 처리 |
|---|---|---|---|---|
| `button-label-large` 13px | 13px | 13px | 13px | 고정 — 0.929rem |
| `paragraph-3` 14px (본문) | 14px | 14px | 14px | 고정 — 1rem |
| `paragraph-2` 16px (타이틀) | 14.91px | 15.70px | 16.27px | 가변 — 1.1429rem |

```css
--fs-p3:    14px;
--fs-btn-l: 13px;
--fs-p2:    calc((1.1429 - 1) * 0.83vw + 1rem);
```

본문(`paragraph-3`)이 정확히 1rem 이라, 이 화면에서 가변인 것은 헤더 타이틀 하나뿐입니다.

### 2. spacing

```css
--us: calc(100vw / 1920);
--mid-8:  8px;                          /* 값 = 8, 증감 없음 */
--mid-12: calc( 4 * var(--us) + 8px);
--mid-16: calc( 8 * var(--us) + 8px);
--mid-24: calc(16 * var(--us) + 8px);
```

| | @768 | @1440 | @1920 |
|---|---|---|---|
| `mid-12` | 9.59px | 11px | 12px |
| `mid-16` | 11.19px | 14px | 16px |
| `mid-24` | 14.39px | 20px | 24px |

| 영역 | padding |
|---|---|
| header | `var(--mid-24) var(--mid-24) var(--mid-8)` |
| content | `var(--mid-16) var(--mid-24) var(--mid-24)` |
| buttonArea | `var(--mid-12) var(--mid-24) var(--mid-24)` |

`px-*` 토큰은 이 화면에 없습니다.

### 3. 다이얼로그 box — 가로 고정

```css
--dialog-w:     560px;   /* 고정 */
--dialog-h:     746px;
--dialog-max-h: 800px;
max-height: min(var(--dialog-max-h), 100%);
```

가로 560px 는 뷰포트와 무관하게 고정입니다. 세로는 746px 기본에 상한 800px 이고,
`100%` 를 함께 걸어 세로가 짧은 창에서는 창 높이에 맞춰 줄고 본문이 스크롤됩니다.

### 4. 그 외 px

버튼 padding 14/22px, hairline 1px.

### 5. 폰트

```css
--font: "Work Sans", "Pretendard Variable", Pretendard, sans-serif;
```

Work Sans 에 한글 글리프가 없어 한글만 Pretendard 로 폴백됩니다.

## 검증

| | 768 × 600 | 1440 × 900 | 1920 × 992 |
|---|---|---|---|
| dialog | 560 × 600 (창 높이에 맞춤) | 560 × 746 | 560 × 746 |
| `paragraph-2` | 14.91px | 15.70px | 16.27px |
| `paragraph-3` / 버튼 | 14 / 13px | 14 / 13px | 14 / 13px |
| `mid-12` / `16` / `24` | 9.59 / 11.19 / 14.39px | 11 / 14 / 20px | 12 / 16 / 24px |
| 본문 스크롤 · 버튼 노출 | 정상 | 정상 | 정상 |
| 가로 스크롤 | 없음 | 없음 | 없음 |

## 참고

- 시안에 헤더·브라우저 크롬 없이 dim + 다이얼로그만 있는 화면이라 그대로 구현했습니다.
- `paragraph-2` 가 1920 에서 16px 이 아닌 16.27px 인 것은 공식 상수 `0.0083`(≈1/120.5)이
  `1/120` 이 아니라서 생기는 약 1.7% 오차입니다. 공식 그대로 적용한 결과입니다.
