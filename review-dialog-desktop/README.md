# Tesso — Review Dialog (Desktop)

Framework B `dialog/review` 의 데스크탑(뷰포트 ≥ 768) 구현.
Figma node `3584:128128` — 리뷰 작성 다이얼로그.

의존성 없는 단일 HTML 파일입니다. `index.html` 을 브라우저로 열면 바로 확인됩니다.

```
index.html      # 마크업 + 전체 스타일
assets/         # Figma 익스포트 (star-filled/empty, close, close-white, plus, thumbnail)
```

단위 규칙과 토글 구성은 [`../address-list-desktop/`](../address-list-desktop/) 와 동일합니다.

## 구성

헤더(리뷰 작성 + 닫기) → 스크롤 본문 → 하단 `리뷰 등록` 버튼.
본문은 상품 정보 → divider → 별점 → 리뷰 textarea → 사진 영역 순입니다.

동작하는 것: 별점 클릭(1~5), textarea 글자수 카운터(0/5,000), 사진 `×` 로 삭제.

## 단위 체계

디자인 기준 폭 **1920**.

### 1. 타이포그래피

`font-size = (size value − 1) × 0.0083 × screen width + 1rem` (size value = rem, **1rem = 14px**)
size value ≤ 1rem 이면 뷰포트와 무관하게 고정.

| 토큰 | 쓰임 | @768 | @1440 | @1920 | 처리 |
|---|---|---|---|---|---|
| `caption-2` 12px | 글자수 카운터 | 12px | 12px | 12px | 고정 |
| `button-label-large` 13px | 리뷰 등록 | 13px | 13px | 13px | 고정 |
| `paragraph-4` 13px | 상품명 · 옵션 · 리뷰 본문 | 13px | 13px | 13px | 고정 |
| `paragraph-2` 16px | 헤더 타이틀 | 14.91px | 15.71px | 16.27px | **가변** |

가변인 것은 헤더 타이틀 하나뿐입니다. 타이틀은 우하단 패널에서 P1~P4 로 바꿔 볼 수
있습니다 (기본 P2, P3/P4 는 ≤ 1rem 이라 고정).

### 2. spacing

```css
--us: calc(100vw / 1920);
--mid-4:  4px;                          /* 값 < 8 → 값 그대로 고정 */
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

| 영역 | 값 |
|---|---|
| header | padding `mid-16 / mid-16 / mid-8 / mid-24` (좌우 비대칭), gap `mid-8` |
| content | padding `mid-16 / mid-24 / mid-24`, 블록 간 gap `mid-24` |
| rating | 상하 padding `mid-12`, 별 사이 gap `mid-4` |
| multiLine | 입력 ↔ 카운터 gap `mid-12` |
| imageArea | 셀 gap `mid-8`, 추가 버튼 안쪽 padding `mid-8` |
| buttonArea | padding `mid-12 / mid-24 / mid-24` |

### 3. 다이얼로그 box — 세 가지 안

| 안 | 가로 너비 | 내부 여백 |
|---|---|---|
| **560 + mid** | 560px 고정 | `mid-*` 가변 |
| **560 + px** (기본 · 제안안) | 560px 고정 | @1920 값으로 px 고정 |
| **vw + mid** | 29.1667vw 가변 | `mid-*` 가변 |

```css
--dialog-w-fixed: 560px;
--dialog-w-fluid: 29.1667vw;         /* 560 / 1920 */
--dialog-w: var(--dialog-w-fixed);   /* 기본 */
```

세로는 800px 기본, **상한 85vh** (`0.85 × 뷰포트 높이`) 입니다.
상한에 걸리면 다이얼로그가 줄고 본문이 스크롤됩니다.

```css
--dialog-h:     800px;
--dialog-max-h: 85vh;
max-height: var(--dialog-max-h);
```

| viewport 높이 | 85vh | 실제 높이 |
|---|---|---|
| 1080 | 918px | 800px |
| 992 | 843.2px | 800px |
| 900 | 765px | 765px (상한) |
| 800 | 680px | 680px (상한) |

### 4. 버튼 padding = em

버튼 padding 은 해당 버튼 label 사이즈를 기준으로 한 em 입니다.

| 버튼 | 시안 px | em (@base) |
|---|---|---|
| 리뷰 등록 | 14 / 22px | `1.0769em 1.6923em` (@ button-label-large 13px) |

데스크탑에서는 button label 이 ≤ 1rem 이라 사이즈가 고정이므로, em 으로 써도 계산 결과는
14 / 22px 로 동일합니다. label 사이즈를 바꾸면 padding 이 함께 따라온다는 점이 달라집니다.

### 5. 그 외 px

닫기 버튼 32px · 글리프 24px, 상품 썸네일 56px · 좌우 gap 16px · 텍스트 gap 4px ·
옵션 gap 2px, 별 40px, textarea 영역 216px · 안쪽 padding 14/17px,
사진 삭제 버튼 20px · 글리프 16px, plus 48px, divider 1px.

### 6. 폰트

```css
--font: "Work Sans", "Pretendard Variable", Pretendard, sans-serif;
```

`135/5,000` 같은 숫자는 Work Sans, 국문은 Pretendard 로 문자 단위 분기됩니다.

## 검증

| | 768 | 1440 | 1920 |
|---|---|---|---|
| dialog · 560 + mid (@vh 992) | 560 × 800 | 560 × 800 | 560 × 800 |
| dialog · vw + mid | 224 | 420 | 560 |
| `mid-12` / `16` / `24` | 9.59 / 11.19 / 14.39px | 11 / 14 / 20px | 12 / 16 / 24px |
| 헤더 타이틀 | 14.91px | 15.71px | 16.27px |
| 리뷰 등록 버튼 padding | 14 / 22px | 14 / 22px | 14 / 22px |
| 본문 스크롤 · 별점 · 카운터 · 사진 삭제 | 정상 | 정상 | 정상 |

아이콘 실측 (@1920): 닫기 24px 박스 / leaf 9.67, 별 40px 박스 / leaf 33.29×31.66,
plus 48px 박스 / leaf 24×24, 사진 삭제 20px 박스 · 글리프 16px / leaf 6.45 —
모두 Figma 익스포트 원본 그대로입니다.

## 참고

- **사진 영역** — 시안은 4칸짜리 `row` 3개입니다. 결과가 동일하면서 삭제 시 자연스럽게
  흐르도록 4열 그리드(`repeat(4, 1fr)` + gap `mid-8`)로 구현했습니다. 1920 기준 셀 122px 로
  시안과 같습니다.
- **스크롤바** — 본문에 스크롤바가 붙는 환경(윈도우 등)에서는 그만큼 셀 폭이 줄어듭니다
  (예: 122 → 118.3px). macOS 오버레이 스크롤바에서는 시안값 그대로입니다.
- 우하단 데모 패널은 시안에 없는 요소입니다. `.demo-nav` / `.demo-seg` / `.demo-readout`
  CSS 와 `#demoNav` 마크업, 그 아래 스크립트를 지우면 됩니다.
