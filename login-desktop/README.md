# Tesso — Login / Sign Up / Change Password (Desktop)

Framework B `System Pages / Login, Sign Up, Change Password` 섹션의 **데스크탑(≥768) 화면**을
플로우가 이어지도록 구현. Figma 섹션 node `2348:64115`.

의존성 없는 단일 HTML 파일입니다. `index.html`을 브라우저로 열면 바로 확인됩니다.

## 화면 & 플로우

```
login ──(로그인, 계정 일치)──────────────→ redirect (로그인 상태로 기존 화면 이동)
  │   └─(불일치)→ dialog "로그인 정보가 일치하지 않아요"
  ├─(회원가입)─→ signup-email ─(인증 요청 + snackbar)→ signup-verify ─→ signup-form ─→ complete ─→ redirect
  └─(비밀번호 찾기)→ pw-email ─(인증 요청 + snackbar)→ pw-verify ─→ pw-new ─(완료)→ login
```

| 화면 | Figma |
|---|---|
| login | `login/desktop` 2198:3402 · 페이지 셸 2202:4085 |
| signup-email / verify / form | `signUp/desktop` type=default / verifyCode / signup (2198:30316) |
| pw-email / verify / new | `changePassword/desktop` type=default / verifyCode / password (2198:30375) |
| complete | `complete/desktop` 2198:30595 |
| dialog | 2348:86478 |
| snackbar | `snackbar/default` 2519:93088 (login 셸의 snackbar variant) |
| redirect | 2348:86318 안내 프레임 텍스트 |

데모 로그인 계정: `example@gmail.com` / `12345678` — 그 외 조합은 dialog로 빠집니다.

우하단 패널(`width` 토글 + `flow` 목록)은 시안에 없는 **데모 컨트롤**입니다.
`.demo-nav` / `.demo-seg` / `.demo-readout` / `.demo-limit` CSS 와 `#demoNav` 마크업, 스크립트의
`가로 너비 모드 · min/max 한계` 블록을 지우면 됩니다.

## 단위 체계

디자인 기준 폭 **1920**.

### 1. 타이포그래피

`font-size = (size value − 1) × 0.0083 × screen width + 1rem` (size value = rem, **1rem = 14px**)
`0.0083 × screen width` = `0.83vw`. size value ≤ 1rem 이면 뷰포트와 무관하게 고정.

| 토큰 | @1920 | @768 | 처리 |
|---|---|---|---|
| `caption-2` / `button-label-small` | 12px | 12px | 고정 — 0.857rem |
| `caption-1` / `paragraph-4` / `button-label-large` | 13px | 13px | 고정 — 0.929rem |
| `paragraph-3` | 14px | 14px | 고정 — 1rem |
| `paragraph-2` | 16.27px | 14.91px | 가변 — 1.1429rem |
| `paragraph-1` | 18.55px | 15.82px | 가변 — 1.2857rem |
| 로고 | 20.83px | 16.73px | 가변 — 1.4286rem |

```css
--fs-p2:   calc((1.1429 - 1) * 0.83vw + 1rem);
--fs-p1:   calc((1.2857 - 1) * 0.83vw + 1rem);
--fs-logo: calc((1.4286 - 1) * 0.83vw + 1rem);
```

### 2. spacing

**`mid-*`** — `(값 − 8) × 100vw / 1920 + 8`, 값 < 8 이면 값 그대로 고정

```css
--us: calc(100vw / 1920);
--mid-4: 4px;  --mid-6: 6px;  --mid-8: 8px;        /* 고정 */
--mid-12:  calc( 4 * var(--us) + 8px);
--mid-16:  calc( 8 * var(--us) + 8px);
--mid-20:  calc(12 * var(--us) + 8px);
--mid-24:  calc(16 * var(--us) + 8px);
--mid-28:  calc(20 * var(--us) + 8px);
--mid-32:  calc(24 * var(--us) + 8px);
--mid-40:  calc(32 * var(--us) + 8px);
--mid-48:  calc(40 * var(--us) + 8px);
--mid-100: calc(92 * var(--us) + 8px);
```

**`px-*`** — 뷰포트와 무관하게 px 고정. 이 화면에서는 헤더 좌우 padding `px-12` = 12px.

### 3. width — 12컬럼 그리드

거터 16px / 좌우 마진 20px (둘 다 px 고정, 뷰포트와 무관).

```css
--grid-columns: 12;
--grid-gutter: 16px;
--grid-margin: 20px;
--grid-col: calc((100vw - 2*var(--grid-margin) - 11*var(--grid-gutter)) / 12);
--content-raw: calc(2.5 * var(--grid-col) + 3 * var(--grid-gutter));
--content-min: 360px;   /* min on 기본값 */
--content-max: none;    /* max off 기본값 */

.card { width: max(var(--content-min), var(--content-raw)); }
```

`--content-w` 조합은 `:root` 가 아니라 `.card` 에서 합니다. custom property 는 선언된
요소에서 `var()` 가 치환되므로, `:root` 에 두면 데모 패널이 `body` 에 심는
`--content-min` 변경이 반영되지 않습니다.

| viewport | col | 2.5col + 3거터 | 적용 |
|---|---|---|---|
| 768 | 46 | 163 | **360** (min) |
| 1280 | 88.67 | 269.67 | **360** (min) |
| 1440 | 102 | 303 | **360** (min) |
| 1713.6 | 124.8 | 360 | **360** (분기점) |
| 1920 | 142 | 403 | **403** |
| 2560 | 195.33 | 536.33 | **536.33** |

1713.6px 이하에서는 min-width 360px 가 걸립니다. 이 그리드 정의는
`framework-e-login/login.html` 의 것과 동일합니다.

#### 고정폭 안과 토글

우하단 데모 패널의 `width — 가변 / 고정 400` 로 두 안을 즉시 비교할 수 있습니다.
선택은 localStorage 에 저장되어 새로고침해도 유지되고, 현재 폭과 뷰포트가 바로 아래
읽기값으로 표시됩니다.

```css
--content-w-fixed: 400px;
--content-max: none;                                  /* 가변 모드 상한 */
body[data-width="fixed"] { --content-w: var(--content-w-fixed); }
body[data-width="fluid"] .card { max-width: var(--content-max); }
```

토큰 하나만 덮어쓰는 구조라, 어느 안으로 확정하든 `--content-w` 한 줄만 남기면 됩니다.

#### min / max 한계

가변 모드에 하한·상한을 각각 켜고 끌 수 있습니다. 체크박스를 켜면 옆 입력칸이 활성화되고
px 값을 넣으면 즉시 반영됩니다 (허용 범위 200–4000px, 범위 밖 입력은 확정 시 보정).
on/off 와 값 모두 localStorage 에 저장됩니다.

기본값은 **가변 + min on 360px / max on 480px** (제안안) 입니다.
한계가 실제로 걸린 순간 읽기값에 `(min)` / `(max)` 가 붙습니다.

| viewport | 폭 | 걸린 한계 |
|---|---|---|
| 768 – 1713 | 360px | min |
| 1920 | 403px | 없음 (시안값 그대로 통과) |
| 2290 이상 | 480px | max |

실제로 값이 움직이는 구간은 **1713 ~ 2290px** 입니다. 그 아래는 360, 위는 480 고정입니다.

```css
body[data-width="fluid"] .card { max-width: var(--content-max); }
```

둘 다 적용되고 min > max 인 경우 CSS 상 `max-width` 가 `width` 를 이기므로 **max 가 우선**
합니다 (예: min 420 + max 390 → 390px). 읽기값도 `(max)` 로 표시됩니다.

min·max 모두 **가변 모드에서만** 적용되고 고정 400px 모드에서는 무시됩니다.

| viewport | 가변 (min 360 / max off) | 가변 (min off) | 가변 + max 403 | 고정 |
|---|---|---|---|---|
| 768 | 360px (min) | 163px | 360px (min) | 400px |
| 1440 | 360px (min) | 303px | 360px (min) | 400px |
| 1920 | 403px | 403px | 403px (max) | 400px |
| 2560 | 536.33px | 536.33px | 403px (max) | 400px |

| viewport | 가변 | 고정 |
|---|---|---|
| 768 – 1713 | 360px (min) | 400px |
| 1920 | 403px | 400px |
| 2560 | 536.33px | 400px |

### 4. 그 외 px

header 64px, textField padding 13/14px, button padding 14/22px,
dialog 400px, snackbar 320px, checkbox 16px, hairline 1px.

### 5. 폰트

```css
--font: "Work Sans", "Pretendard Variable", Pretendard, sans-serif;
```

Work Sans 에 한글 글리프가 없어 한글만 Pretendard 로 폴백됩니다. `example@gmail.com`, `0/20`,
`04:59` 같은 영문·숫자는 Work Sans, 나머지 국문은 Pretendard 로 문자 단위 분기됩니다.

## 검증

| | 768px | 1920px |
|---|---|---|
| `mid-12` / `24` / `32` / `100` | 9.59 / 14.40 / 17.59 / 44.80px | 12 / 24 / 32 / 100px |
| `paragraph-2` / `paragraph-1` / 로고 | 14.91 / 15.82 / 16.73px | 16.27 / 18.55 / 20.83px |
| `paragraph-4` (고정) | 13px | 13px |
| card 폭 (2.5col + 3거터) | 360px (min) | 403px |
| 가로 스크롤 | 없음 | 없음 |

플로우 전 구간 실행 확인: 로그인 실패 → dialog / 회원가입 4단계 → 완료 / 비밀번호 변경 3단계 →
로그인 / 로그인 성공 → redirect. 콘솔 에러 없음.

## 시안과 다르게 처리한 것

- **헤더 로고 정렬** — 시안(1920)은 로고가 절대배치 중앙이지만 768 부근에서 `THE STORY`와
  겹칩니다. `1fr auto 1fr` 그리드로 바꿔 1920 에서는 결과가 동일하고(중앙 오차 0px)
  좁은 폭에서는 겹치지 않게 했습니다. 768 에서는 nav 가 utilities 보다 넓어 로고가
  중앙에서 27px 우측으로 밀립니다.
- **로고 폰트** — 시안은 Inter Semi Bold. 영문 = Work Sans 규칙에 맞춰 Work Sans 600 사용.
- **가변 폰트 오차** — 공식의 상수 `0.0083`(≈1/120.5)이 `1/120`이 아니라, 1920 에서
  16 → 16.27px 처럼 약 1.7% 크게 계산됩니다. 공식 그대로 적용한 결과입니다.
- **체크박스 checked** — 시안에 off 상태만 있어 on 은 배경 #111 + 흰 체크로 구성했습니다.
- **버튼 enabled** — 시안의 disabled(#ebebeb/#c5c5c5)와 complete·dialog 의 enabled(#111/#fff)
  를 그대로 써서, 필수 입력이 채워지면 enabled 로 전환합니다.
