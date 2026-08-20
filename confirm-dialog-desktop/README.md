# Tesso — Confirm Dialog (Desktop)

Framework B `dialog` 의 데스크탑(뷰포트 ≥ 768) 구현.
Figma node `4029:184793` — 로그인 확인 다이얼로그 (로그인이 필요해요 / 취소 · 로그인).

의존성 없는 단일 HTML 파일입니다 (외부 에셋 없음). `index.html` 을 브라우저로 열면 바로
확인됩니다. 단위 규칙과 토글 구성은 [`../address-list-desktop/`](../address-list-desktop/) 와
동일하며, box 기준 너비만 400px 입니다.

## 단위 체계

디자인 기준 폭 **1920**.

### 1. 타이포그래피

`font-size = (size value − 1) × 0.0083 × screen width + 1rem` (size value = rem, **1rem = 14px**)
`0.0083 × screen width` = `0.83vw`. size value ≤ 1rem 이면 뷰포트와 무관하게 고정.

| 토큰 | 쓰임 | @768 | @1440 | @1920 | 처리 |
|---|---|---|---|---|---|
| `paragraph-4` 13px | 설명 | 13px | 13px | 13px | 고정 |
| `button-label-large` 13px | 취소 · 로그인 | 13px | 13px | 13px | 고정 |
| `paragraph-2` 16px | 타이틀 | 14.91px | 15.71px | 16.27px | **가변** |

이 화면도 가변인 것은 타이틀 하나뿐입니다.

#### 타이틀 사이즈 전환

타이틀을 P1~P4 로 바꿔 볼 수 있습니다 (우하단 데모 패널, 기본값 P2).

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
--mid-20: calc(12 * var(--us) + 8px);
--mid-24: calc(16 * var(--us) + 8px);
--mid-32: calc(24 * var(--us) + 8px);
```

| | @768 | @1440 | @1920 |
|---|---|---|---|
| `mid-20` | 12.80px | 17px | 20px |
| `mid-24` | 14.39px | 20px | 24px |
| `mid-32` | 17.59px | 26px | 32px |

| 영역 | 값 |
|---|---|
| dialog | padding `mid-32 / mid-20 / mid-24`, gap `mid-32` |
| title & description | gap `mid-8`, 좌우 padding `mid-4` |
| buttonGroup | gap `mid-8` |

### 3. 다이얼로그 box — 세 가지 안

| 안 | 가로 너비 | 내부 여백 |
|---|---|---|
| **400 + mid** (기본) | 400px 고정 | `mid-*` 가변 |
| **400 + px** | 400px 고정 | @1920 값으로 px 고정 |
| **vw + mid** | 20.8333vw 가변 | `mid-*` 가변 |

```css
--dialog-w-fixed: 400px;
--dialog-w-fluid: 20.8333vw;         /* 400 / 1920 */
--dialog-w: var(--dialog-w-fixed);   /* 기본 */

body[data-box="fixed-px"] {
  --mid-4: 4px; --mid-8: 8px; --mid-20: 20px; --mid-24: 24px; --mid-32: 32px;
}
body[data-box="fluid-mid"] { --dialog-w: var(--dialog-w-fluid); }
```

| viewport | 400 + mid | 400 + px | vw + mid |
|---|---|---|---|
| 768 | 400px · pad 12.8px | 400px · pad 20px | 160px · pad 12.8px |
| 1440 | 400px · pad 17px | 400px · pad 20px | 300px · pad 17px |
| **1920** | **400px · pad 20px** | **400px · pad 20px** | **400px · pad 20px** |
| 2560 | 400px · pad 24px | 400px · pad 20px | 533.33px · pad 24px |

pad 는 `mid-20` (다이얼로그 좌우) 기준입니다. 1920 에서는 세 안이 완전히 동일합니다.

### 4. 버튼 padding = em

버튼 padding 은 label 사이즈 기준 em 입니다 — 취소 · 로그인 모두
`1.0769em 1.6923em` (14 / 22px @ button-label-large 13px).
데스크탑에서는 label 이 ≤ 1rem 이라 고정이므로 계산 결과는 14 / 22px 로 동일합니다.

### 5. 그 외 px

hairline 1px, dim `rgba(0,0,0,0.5)`.

### 5. 폰트

```css
--font: "Work Sans", "Pretendard Variable", Pretendard, sans-serif;
```

## 검증

| | 768 × 900 | 1440 × 900 | 1920 × 992 |
|---|---|---|---|
| dialog · 400 + mid | 400 × 178 | 400 × 182 | 400 × 188.4 |
| dialog · vw + mid | 160 | 300 | 400 |
| 타이틀 P1 / P2 / P3 / P4 | 15.82 / 14.91 / 14 / 13px | 17.41 / 15.71 / 14 / 13px | 18.55 / 16.27 / 14 / 13px |
| `mid-20` / `24` / `32` | 12.80 / 14.39 / 17.59px | 17 / 20 / 26px | 20 / 24 / 32px |
| 가로 스크롤 | 없음 | 없음 | 없음 |

## 참고

- 우하단 데모 패널은 시안에 없는 요소입니다. `.demo-nav` / `.demo-seg` / `.demo-readout`
  CSS 와 `#demoNav` 마크업, 그 아래 스크립트를 지우면 됩니다.
- `vw + mid` 안에는 min/max 를 걸지 않았습니다. 768 에서 160px 까지 좁아져 두 버튼이
  상당히 좁아집니다. 하한이 필요하면 `--dialog-w-fluid` 를 `max(<하한>px, 20.8333vw)` 로
  바꾸면 됩니다.
- 뷰포트가 아주 좁을 때를 대비해 `max-width: calc(100% - 40px)` 만 걸어 두었습니다
  (고정 400px 안에서 400px 미만 창일 때 넘치지 않도록).
