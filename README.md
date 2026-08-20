# Tesso — Framework B 반응형 구현

Figma `-0_Current / 02_Framework B` 시안을 가변 규칙에 따라 구현한 화면 모음입니다.
전부 의존성 없는 단일 HTML 파일이라 각 `index.html` 을 브라우저로 열면 바로 확인됩니다.

**Demo:** https://noah-815.github.io/tesso-framework-b/

| 화면 | 뷰포트 | Figma | 데모 |
|---|---|---|---|
| [Search](search-mobile/) | Mobile ≤ 767 | `2294:13739` | [열기](https://noah-815.github.io/tesso-framework-b/search-mobile/) |
| [Login · Sign Up · Change Password](login-desktop/) | Desktop ≥ 768 | `2348:64115` | [열기](https://noah-815.github.io/tesso-framework-b/login-desktop/) |
| [Disclosure Dialog](disclosure-desktop/) | Desktop ≥ 768 | `2828:20428` | [열기](https://noah-815.github.io/tesso-framework-b/disclosure-desktop/) |
| [Address List Dialog](address-list-desktop/) | Desktop ≥ 768 | `2865:19307` | [열기](https://noah-815.github.io/tesso-framework-b/address-list-desktop/) |
| [Review Dialog](review-dialog-desktop/) | Desktop ≥ 768 | `3584:128128` | [열기](https://noah-815.github.io/tesso-framework-b/review-dialog-desktop/) |
| [Confirm Dialog](confirm-dialog-desktop/) | Desktop ≥ 768 | `4029:184793` | [열기](https://noah-815.github.io/tesso-framework-b/confirm-dialog-desktop/) |
| [Disclosure Dialog](disclosure-mobile/) | Mobile ≤ 767 | `2828:20429` | [열기](https://noah-815.github.io/tesso-framework-b/disclosure-mobile/) |

각 폴더의 README 에 단위 체계, 뷰포트별 검증값, 시안과 다르게 처리한 부분이 정리되어 있습니다.

> **데모 기본값 = 제안안** — 다이얼로그 4종은 `너비 px 고정 + 내부 여백 px 고정`,
> 로그인은 `너비 가변 + min 360 / max 480` 으로 열립니다. 우하단 패널로 다른 안과
> 비교해 볼 수 있습니다.

## 가변 규칙 요약

### Mobile (≤ 767) — 기준 폭 390

```css
--tw: min(100vw, 450px);      /* 타이포 기준 폭 — 450px 이후 고정 */
--ut: calc(var(--tw) / 390);  /* 타이포 1px */
--us: calc(100vw / 390);      /* spacing 1px */
```

- **타이포** `font-size = size value × screen width / 390` (size value = rem, 1rem = 14px).
  450px 이후 고정. `size value ≤ 1rem 은 고정` 예외는 적용하지 않습니다.
- **spacing** `mid-*` = `(값 − 8) × 100vw / 390 + 8`, 값 < 8 이면 값 그대로 고정.
  `px-*` 는 뷰포트와 무관하게 px 고정.
- **em** search 컴포넌트(상하 padding · text↔total gap · 아이콘 크기, @base p4 13px),
  badge padding (@base c2 12px).

### Desktop (≥ 768) — 기준 폭 1920

```css
--us: calc(100vw / 1920);                         /* spacing 1px */
--fs-p2: calc((1.1429 - 1) * 0.83vw + 1rem);      /* 예: 16px @1920 */
```

- **타이포** `font-size = (size value − 1) × 0.0083 × screen width + 1rem`.
  `0.0083 × screen width` = `0.83vw`. size value ≤ 1rem 은 고정.
- **spacing** `mid-*` = `(값 − 8) × 100vw / 1920 + 8`, 값 < 8 이면 값 그대로 고정.
  `px-*` 는 px 고정.
- **width** 로그인·회원가입 콘텐츠 = `21vw`, min 360px / max 480px.
  1920 에서 403.2px. (이전 `(2.5 × 컬럼) + (3 × 거터)` 공식과 최대 1.3px 차이)

### 공통 — 버튼 padding

버튼 padding 은 해당 버튼 label 사이즈를 기준으로 한 em 입니다.

| label | 시안 px | em |
|---|---|---|
| `button-label-large` 13px | 14 / 22px | `1.0769em 1.6923em` |
| `button-label-small` 12px | 9 / 15px | `0.75em 1.25em` |
| `button-label-small` 12px | 9 / 0px | `0.75em 0` |

데스크탑은 button label 이 ≤ 1rem 이라 고정이므로 계산 결과가 시안 px 와 같고,
모바일은 label 이 가변이라 padding 도 함께 가변이 됩니다.

### 공통 — 폰트

```css
--font: "Work Sans", "Pretendard Variable", Pretendard, sans-serif;
```

Work Sans 에 한글 글리프가 없어 한글만 Pretendard 로 폴백됩니다. 클래스나 언어 속성 없이
문자 단위로 갈리므로 `와일드 베리 핸드 로션 300ml` 같이 섞인 문자열도 의도대로 나옵니다.
