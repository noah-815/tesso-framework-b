# Tesso — Search (Mobile Responsive)

Framework B `search` 화면의 모바일(뷰포트 ≤ 767) 반응형 구현.
Figma: `0_Current / 02_Framework B` — node `2294:13739`

**Demo:** https://noah-815.github.io/tesso-search-mobile/

의존성 없는 단일 HTML 파일입니다. `index.html`을 브라우저로 열면 바로 확인됩니다.

```
index.html      # 마크업 + 전체 스타일
assets/         # Figma 익스포트 (search, chevron-left/right, thumbnail)
```

## 단위 체계

디자인 기준 폭 **390** (시안 프레임 393).

```css
--tw: min(100vw, 450px);      /* 타이포 기준 폭 — 450px 이후 고정 */
--us: calc(100vw / 390);      /* spacing 1px */
```

### 1. 타이포그래피

`font-size = size value × screen width / 390` (size value = rem, **1rem = 14px**)

- 450px 이후 폰트 사이즈 고정
- ~~size value ≤ 1rem 이면 고정~~ → **모바일에서는 이 예외를 적용하지 않음. 전부 가변**

| 토큰 | @390 | @320 | @450 이상 | 처리 |
|---|---|---|---|---|
| `caption-2` | 12px | 9.85px | 13.85px | 가변 |
| `caption-1` / `paragraph-4` | 13px | 10.67px | 15.00px | 가변 |
| `paragraph-3` | 14px | 11.49px | 16.15px | 가변 |
| BRAND 로고 | 20px | 20px | 20px | 고정 — 가변 로직 제외 |

```css
--ut: calc(var(--tw) / 390);    /* 타이포 1px */
--fs-c2: calc(12 * var(--ut));
--fs-c1: calc(13 * var(--ut));
--fs-p4: calc(13 * var(--ut));
--fs-p3: calc(14 * var(--ut));
--fs-logo: 20px;                /* 제외 */
```

### 2. spacing (`mid-*` 토큰)

`(값 − 8) × 100vw / 390 + 8`, 단 **값 < 8 인 토큰은 공식 제외 · 값 그대로 고정**

```css
--mid-2: 2px;  --mid-4: 4px;  --mid-6: 6px;  --mid-8: 8px;   /* 고정 */
--mid-12: calc( 4 * var(--us) + 8px);
--mid-16: calc( 8 * var(--us) + 8px);
--mid-24: calc(16 * var(--us) + 8px);
--mid-32: calc(24 * var(--us) + 8px);
--mid-40: calc(32 * var(--us) + 8px);
```

`mid-*` 는 명세대로 450px 캡 없이 100vw 로 계속 증가합니다. 타이포와 동일하게 450px 에서
멈추려면 `--us: calc(var(--tw) / 390);` 한 줄로 교체하면 됩니다.

### 3. em 적용

**search** — @base `paragraph-4` 13px

| 항목 | 값 | em |
|---|---|---|
| 상단 padding | 13px | `1em` |
| 하단 padding | 14px | `1.0769em` |
| text ↔ total gap | 13px | `1em` |
| 아이콘 크기 | 21px | `1.6154em` |

**badge** — @base `caption-2` 12px

| 항목 | 값 | em |
|---|---|---|
| 상하 padding | 8px | `0.6667em` |
| 좌우 padding | 10px | `0.8333em` |

`left` / `top` 12px 은 썸네일 모서리 기준 위치값이라 px 유지.

### 4. 폰트 — 영문 Work Sans / 국문 Pretendard

```css
--font: "Work Sans", "Pretendard Variable", Pretendard, sans-serif;
```

Work Sans 에 한글 글리프가 없어 한글만 Pretendard 로 폴백됩니다. 클래스나 언어 속성 없이
문자 단위로 갈리므로 `와일드 베리 핸드 로션 300ml` 같이 섞인 문자열도 의도대로 나옵니다
(한글 Pretendard / `300ml` Work Sans).

### 5. 그 외

토큰/정의가 없는 값은 px — header 60px, statusBar 54px, searchArea 좌우 16px,
badge left/top 12px, pagination 버튼 32px · 글리프 24px, hairline 1px.

## 검증

| | 320px | 390px | 767px |
|---|---|---|---|
| `paragraph-4` (본문·상품명) | 10.67px | 13px | 15px |
| `caption-2` | 9.85px | 12px | 13.85px |
| `paragraph-3` | 11.49px | 14px | 16.15px |
| BRAND 로고 | 20px | 20px | 20px |
| search 아이콘 (1.6154em) | 17.23px | 21px | 24.23px |
| badge padding (0.6667/0.8333em) | 6.56 / 8.20px | 8 / 10px | 9.23 / 11.54px |
| `mid-2` / `4` / `6` / `8` | 2 / 4 / 6 / 8px | 2 / 4 / 6 / 8px | 2 / 4 / 6 / 8px |
| `mid-12` / `16` / `24` / `40` | 11.3 / 14.6 / 21.1 / 34.3px | 12 / 16 / 24 / 40px | 15.9 / 23.7 / 39.5 / 70.9px |
| 가로 스크롤 | 없음 | 없음 | 없음 |

390px 기준 문서 높이 2158px (Figma 프레임 2177px — hairline 반올림 차).

## 샘플 데이터

카드 12개에 케이스를 섞어 넣었습니다.

- **상품명** — 영문 6 / 국문 6, 1줄 · 2줄 · 2줄 초과(말줄임) 각각 포함
  폰트는 `--font` 폴백으로 자동 분기
- **가격** — 정가만 6 / 세일가 + 원가(취소선) 6
- 첫 카드에 `SOLD OUT` 뱃지

상품명 길이가 달라도 행 내 가격 라인이 어긋나지 않도록 `.name` 에
`min-height: 3.2em` (2줄 슬롯)을, `.info` 에 `flex: 1 1 auto` 를 적용했습니다.

## 참고

디바이스 목업(상단 statusBar · 하단 브라우저 바)은 제외하고 서비스 화면만 구현했습니다.
