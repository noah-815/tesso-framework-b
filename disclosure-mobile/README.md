# Tesso — Disclosure Dialog (Mobile)

Framework B `dialog/disclosure` 의 모바일(뷰포트 ≤ 767) 반응형 구현.
Figma node `2828:20429` (device=mobile) — 개인정보 수집/이용 및 처리 동의 모달.

의존성 없는 단일 HTML 파일입니다. `index.html`을 브라우저로 열면 바로 확인됩니다.

```
index.html      # 마크업 + 전체 스타일 (외부 에셋 없음)
```

디바이스 목업(상단 statusBar · 하단 브라우저 바)은 제외하고 다이얼로그만 구현했습니다.

단위 규칙은 `framework-b-search-mobile` 과 동일합니다.

## 단위 체계

디자인 기준 폭 **390** (시안 프레임 393).

```css
--tw: min(100vw, 450px);      /* 타이포 기준 폭 — 450px 이후 고정 */
--ut: calc(var(--tw) / 390);  /* 타이포 1px */
--us: calc(100vw / 390);      /* spacing 1px */
```

### 1. 타이포그래피 — 전부 가변

`font-size = size value × screen width / 390` (size value = rem, **1rem = 14px**)
450px 이후 고정. `size value ≤ 1rem 은 고정` 예외는 적용하지 않습니다.

| 토큰 | @320 | @390 | @450 이상 |
|---|---|---|---|
| `button-label-large` 13px | 10.66px | 13px | 15px |
| `paragraph-3` 14px (본문) | 11.48px | 14px | 16.15px |
| `paragraph-2` 16px (타이틀 기본값) | 13.13px | 16px | 18.46px |
| `paragraph-1` 18px | 14.77px | 18px | 20.77px |

#### 타이틀 사이즈 전환

헤더 타이틀을 P1~P4 로 바꿔 볼 수 있습니다. 하단 데모 패널에서 선택하며, 값은
localStorage 에 저장됩니다. 기본값은 시안대로 P2 입니다.

```css
--fs-title: var(--fs-p2);                            /* 기본 = 시안값 */
body[data-title="p1"] { --fs-title: var(--fs-p1); }  /* p2 / p3 / p4 동일 */
```

| | @390 | @320 | @450 이상 |
|---|---|---|---|
| P1 | 18px | 14.77px | 20.77px |
| **P2 (기본)** | 16px | 13.13px | 18.46px |
| P3 | 14px | 11.48px | 16.15px |
| P4 | 13px | 10.66px | 15px |


### 2. spacing

```css
--mid-8:  8px;                          /* 값 < 8 아님, 공식 결과도 8px */
--mid-20: calc(12 * var(--us) + 8px);   /* (20 − 8) × 100vw / 390 + 8 */
```

| | @320 | @390 | @767 |
|---|---|---|---|
| `mid-20` | 17.84px | 20px | 31.59px |

시안에서는 다이얼로그 헤더의 상단 padding 만 `px-20`(고정)으로 걸려 있으나,
요청에 따라 `mid-20`(가변)으로 통일했습니다. 따라서 이 화면에 `px-*` 는 없습니다.

| 영역 | padding |
|---|---|
| header | `var(--mid-20) var(--mid-20) var(--mid-8)` |
| content | `var(--mid-8) var(--mid-20) var(--mid-20)` |
| buttonArea | `var(--mid-8) var(--mid-20) var(--mid-20)` |

`mid-*` 는 명세대로 450px 캡 없이 100vw 로 계속 증가합니다. 타이포와 동일하게 450px 에서
멈추려면 `--us: calc(var(--tw) / 390);` 한 줄로 교체하면 됩니다.

### 3. 버튼 padding = em

확인 버튼 padding 은 `1.0769em 1.6923em` (14 / 22px @ button-label-large 13px).
모바일은 label 사이즈가 가변이라 padding 도 함께 가변입니다 — 320 에서 11.5 / 18.0px,
390 에서 14 / 22px, 450 이상에서 16.2 / 25.4px.

### 4. 그 외 px

다이얼로그 높이 480px, 버튼 높이 44px, hairline 1px.

### 5. 폰트

```css
--font: "Work Sans", "Pretendard Variable", Pretendard, sans-serif;
```

Work Sans 에 한글 글리프가 없어 한글만 Pretendard 로 폴백됩니다. 본문의 숫자·영문은
Work Sans, 국문은 Pretendard 로 문자 단위 분기됩니다.

## 검증

| | 320px | 390px | 767px |
|---|---|---|---|
| `paragraph-2` / `paragraph-3` / `button-label-large` | 13.13 / 11.48 / 10.66px | 16 / 14 / 13px | 18.46 / 16.14 / 15px |
| `mid-20` | 17.84px | 20px | 31.59px |
| dialog 폭 | 280px | 350px | 353px (상한) |
| 가로 스크롤 | 없음 | 없음 | 없음 |

본문 스크롤 동작, 타이틀 P1~P4 전환(18 / 16 / 14 / 13px @390) 확인. 콘솔 에러 없음.

## 시안과 다르게 처리한 것

- **다이얼로그 폭** — 시안은 393 프레임에 353px 고정(좌우 20px)입니다. 그대로 px 로 두면
  353px 미만 뷰포트에서 넘치기 때문에 `min(353px, calc(100% - 40px))` 로 상한 처리했습니다.
  393 에서는 시안과 동일하고, 320 에서는 280px 이 됩니다.
- **다이얼로그 높이** — 480px 고정이되 `max-height: 100%` 를 함께 걸어 세로가 짧은 기기에서
  잘리지 않게 했습니다.
- **헤더 상단 padding** — 시안은 `px-20`(고정)이나 요청에 따라 `mid-20`(가변)으로 통일.
- **디바이스 목업 제외** — 상단 statusBar 와 하단 브라우저 바는 구현에서 제외했습니다.
- **하단 데모 패널** — 시안에 없는 요소입니다. `.demo-nav` / `.demo-seg` / `.demo-readout`
  CSS 와 `#demoNav` 마크업, 그 아래 스크립트를 지우면 됩니다.
