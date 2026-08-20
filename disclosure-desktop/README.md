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

#### 타이틀 사이즈 전환

헤더 타이틀을 P1~P4 로 바꿔 볼 수 있습니다. 우하단 데모 패널에서 선택하며, 값은
localStorage 에 저장됩니다. 기본값은 시안대로 P2 입니다.

```css
--fs-title: var(--fs-p2);                            /* 기본 = 시안값 */
body[data-title="p1"] { --fs-title: var(--fs-p1); }  /* p2 / p3 / p4 동일 */
```

| | @768 | @1440 | @1920 | 처리 |
|---|---|---|---|---|
| P1 (18px) | 15.82px | 17.06px | 18.55px | 가변 — 1.2857rem |
| **P2 (16px, 기본)** | 14.91px | 15.70px | 16.27px | 가변 — 1.1429rem |
| P3 (14px) | 14px | 14px | 14px | **고정** — 1rem |
| P4 (13px) | 13px | 13px | 13px | **고정** — 0.929rem |

모바일과 달리 데스크탑은 `size value ≤ 1rem 은 고정` 규칙이 살아 있어, P3·P4 를 고르면
타이포가 뷰포트와 무관하게 멈춥니다. 읽기값에 `(고정)` 으로 표시됩니다.

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

### 3. 다이얼로그 box — 세 가지 안

우하단 데모 패널의 `box` 세그먼트로 즉시 비교할 수 있습니다. 선택은 localStorage 에
저장되며 기본값은 `560 + mid` (시안 그대로) 입니다.

| 안 | 가로 너비 | 내부 여백 |
|---|---|---|
| **560 + mid** (기본) | 560px 고정 | `mid-*` 가변 |
| **560 + px** | 560px 고정 | @1920 값으로 px 고정 |
| **vw + mid** | 29.1667vw 가변 | `mid-*` 가변 |

```css
--dialog-w-fixed: 560px;
--dialog-w-fluid: 29.1667vw;         /* 560 / 1920 */
--dialog-w: var(--dialog-w-fixed);   /* 기본 */

body[data-box="fixed-px"] {          /* 내부 여백만 @1920 값으로 고정 */
  --mid-8: 8px; --mid-12: 12px; --mid-16: 16px; --mid-24: 24px;
}
body[data-box="fluid-mid"] { --dialog-w: var(--dialog-w-fluid); }
```

`--dialog-w` 와 `mid-*` 만 덮어쓰는 구조라, 어느 안으로 확정하든 해당 값 몇 줄만
남기고 `body[data-box=…]` 블록과 데모 패널을 지우면 됩니다.

#### 뷰포트별 비교

| viewport | 560 + mid | 560 + px | vw + mid |
|---|---|---|---|
| 768 | 560px · pad 14.4px | 560px · pad 24px | 224px · pad 14.4px |
| 1024 | 560px · pad 16.5px | 560px · pad 24px | 298.67px · pad 16.5px |
| 1440 | 560px · pad 20px | 560px · pad 24px | 420px · pad 20px |
| **1920** | **560px · pad 24px** | **560px · pad 24px** | **560px · pad 24px** |
| 2560 | 560px · pad 29.3px | 560px · pad 24px | 746.67px · pad 29.3px |

pad 는 `mid-24` (header/content/buttonArea 좌우) 기준입니다. 1920 에서는 세 안이
완전히 동일하고, 그 위아래로 갈수록 갈라집니다.

세로는 세 안 공통으로 746px 기본, **상한 85vh** (`0.85 × 뷰포트 높이`) 입니다.
상한에 걸리면 다이얼로그가 줄고 본문이 스크롤됩니다.

```css
--dialog-h:     746px;
--dialog-max-h: 85vh;
max-height: var(--dialog-max-h);
```

| viewport 높이 | 85vh | 실제 높이 |
|---|---|---|
| 992 | 843.2px | 746px |
| 900 | 765px | 746px |
| 800 | 680px | 680px (상한) |
| 600 | 510px | 510px (상한) |

### 4. 버튼 padding = em

확인 버튼 padding 은 `1.0769em 1.6923em` (14 / 22px @ button-label-large 13px).
데스크탑에서는 label 이 ≤ 1rem 이라 고정이므로 계산 결과는 14 / 22px 로 동일합니다.

### 5. 그 외 px

hairline 1px.

### 5. 폰트

```css
--font: "Work Sans", "Pretendard Variable", Pretendard, sans-serif;
```

Work Sans 에 한글 글리프가 없어 한글만 Pretendard 로 폴백됩니다.

## 검증

| | 768 × 600 | 1440 × 900 | 1920 × 992 |
|---|---|---|---|
| dialog · 560 + mid | 560 × 510 (85vh) | 560 × 746 | 560 × 746 |
| dialog · 560 + px | 560 × 510 (85vh) | 560 × 746 | 560 × 746 |
| dialog · vw + mid | 224 × 510 (85vh) | 420 × 746 | 560 × 746 |
| 내부 여백 (mid-24) | 14.39 / 24 / 14.39px | 20 / 24 / 20px | 24 / 24 / 24px |
| `paragraph-2` | 14.91px | 15.70px | 16.27px |
| `paragraph-3` / 버튼 | 14 / 13px | 14 / 13px | 14 / 13px |
| `mid-12` / `16` / `24` | 9.59 / 11.19 / 14.39px | 11 / 14 / 20px | 12 / 16 / 24px |
| 타이틀 P1 / P2 / P3 / P4 | 15.82 / 14.91 / 14 / 13px | 17.06 / 15.70 / 14 / 13px | 18.55 / 16.27 / 14 / 13px |
| 본문 스크롤 · 버튼 노출 | 정상 | 정상 | 정상 |
| 가로 스크롤 | 없음 | 없음 | 없음 |

## 참고

- 시안에 헤더·브라우저 크롬 없이 dim + 다이얼로그만 있는 화면이라 그대로 구현했습니다.
- 우하단 데모 패널은 시안에 없는 요소입니다. `.demo-nav` / `.demo-seg` / `.demo-readout`
  CSS 와 `#demoNav` 마크업, 그 아래 스크립트를 지우면 됩니다.
- `vw + mid` 안에는 min/max 를 걸지 않았습니다. 순수 vw 라 768 에서 224px 까지 좁아지고
  본문 줄바꿈이 잦아집니다. 하한이 필요하면 `--dialog-w-fluid` 를
  `max(<하한>px, 29.1667vw)` 로 바꾸면 됩니다.
- `paragraph-2` 가 1920 에서 16px 이 아닌 16.27px 인 것은 공식 상수 `0.0083`(≈1/120.5)이
  `1/120` 이 아니라서 생기는 약 1.7% 오차입니다. 공식 그대로 적용한 결과입니다.
