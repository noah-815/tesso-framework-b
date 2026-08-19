# Tesso — Search (Mobile Responsive)

Framework B `search` 화면의 모바일(뷰포트 ≤ 767) 반응형 구현.
Figma: `0_Current / 02_Framework B` — node `2294:13739`

**Demo:** https://noah-815.github.io/tesso-search-mobile/

의존성 없는 단일 HTML 파일입니다. `index.html`을 브라우저로 열면 바로 확인됩니다.

```
index.html      # 마크업 + 전체 스타일
assets/         # Figma 익스포트 (search, chevron-left/right, status-levels, thumbnail)
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
- size value ≤ 1rem 이면 뷰포트와 무관하게 고정

이 화면은 결과적으로 **전부 고정**입니다.

| 토큰 | 값 | 처리 |
|---|---|---|
| `caption-2` | 12px (0.857rem) | 고정 — ≤ 1rem |
| `caption-1` / `paragraph-4` | 13px (0.929rem) | 고정 — ≤ 1rem |
| `paragraph-3` | 14px (1rem) | 고정 — ≤ 1rem |
| BRAND 로고 | 20px | 고정 — 가변 로직 제외 |
| statusBar 시간 | 17px | 고정 — 가변 로직 제외 |

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

### 3. search 컴포넌트 — em (@base `p4` = 13px)

| 항목 | 값 | em |
|---|---|---|
| 상단 padding | 13px | `1em` |
| 하단 padding | 14px | `1.0769em` |
| text ↔ total gap | 13px | `1em` |
| 아이콘 크기 | 21px | `1.6154em` |

### 4. 그 외

토큰/정의가 없는 값은 px — header 60px, statusBar 54px, searchArea 좌우 16px,
badge(left/top 12px · padding 8/10px), pagination 버튼 32px · 글리프 24px, hairline 1px.

## 검증

| | 390px | 767px |
|---|---|---|
| BRAND / statusBar 시간 | 20 / 17px | 20 / 17px |
| `mid-2` / `4` / `6` / `8` | 2 / 4 / 6 / 8px | 2 / 4 / 6 / 8px |
| `mid-12` / `16` / `24` / `40` | 12 / 16 / 24 / 40px | 15.9 / 23.7 / 39.5 / 70.9px |
| 가로 스크롤 | 없음 | 없음 |

390px 기준 문서 높이 2158px (Figma 프레임 2177px — hairline 반올림 차).

## 참고

`.status-bar` 와 `.browser-bar` 는 디바이스 목업입니다. 실서비스 적용 시 두 블록과
`body { padding-bottom: 60px }` 를 함께 제거하세요.
