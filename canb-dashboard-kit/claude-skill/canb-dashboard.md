# CANB Dashboard — 프로젝트 컨텍스트

CANB 캠퍼스 관리 대시보드입니다. 작업 전 이 컨텍스트와 `DESIGN SYSTEM.MD`를 기준으로 삼으세요.

---

## 프로젝트 개요

- **제품명**: CANB 캠퍼스 관리 대시보드
- **경로**: `d:\이진희\#WORK\#AI\Claude\canb_dashbord`
- **빌드**: 없음 — Vanilla JS + HTML, 브라우저에서 직접 열기
- **담당자**: 이진희 (관리자, jini.lee@canb.com)
- **디자인 Kit**: https://jinilee-hue.github.io/canb-dashboard/download.html

---

## 파일 구조

```
canb_dashbord/
├── styles/
│   ├── tokens.css        CSS 변수 (색상·간격·타이포그래피)
│   └── common.css        레이아웃·전체 컴포넌트 스타일
├── js/
│   ├── utils.js          포맷 함수, fpLocale, getChartColors(), setChartDefaults
│   └── layout.js         사이드바 렌더링, 테마 토글, 커스텀 셀렉트, Flatpickr 커스텀 드롭다운, 브레드크럼
├── index.html            대시보드 홈
├── students.html         학생 관리
├── courses.html          수강 관리
├── attendance.html       출결 관리
├── grades.html           성적 관리
├── payments.html         수납 관리
├── teachers.html         강사 관리
├── notices.html          공지사항
├── counseling.html       상담 관리
├── settings.html         설정
├── modal-demo.html       컴포넌트 데모 (날짜 피커·모달·커스텀 셀렉트)
└── DESIGN SYSTEM.MD      디자인 시스템 문서 (컬러·타이포·컴포넌트 스펙)
```

---

## 기술 스택 & CDN 로드 순서

```html
<!-- CSS — common.css가 라이브러리보다 뒤에 오도록 -->
<link rel="stylesheet" href="styles/tokens.css">
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/overlayscrollbars@2.16.0/styles/overlayscrollbars.min.css">
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/flatpickr/dist/flatpickr.min.css">
<link rel="stylesheet" href="styles/common.css">

<!-- JS — utils.js → layout.js 순서 필수 -->
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>  <!-- 차트 페이지만 -->
<script src="https://cdn.jsdelivr.net/npm/overlayscrollbars@2.16.0/browser/overlayscrollbars.browser.es5.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/flatpickr/dist/flatpickr.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/flatpickr/dist/l10n/ko.js"></script>
<script src="js/utils.js"></script>
<script src="js/layout.js"></script>
```

---

## 핵심 디자인 원칙

**모노톤 중립 시스템 위에 CANB Magenta `#BC216D`를 데이터 강조 포인트로만 사용.**

- 배경·테두리·텍스트·그림자는 모두 중립 회색 계열 (마젠타 틴트 사용 금지)
- `#BC216D`를 사용하는 곳: 차트 데이터, KPI 강조, 버튼(primary), 활성 네비, 배지(primary/success), 포커스 링
- 사이드바: 라이트 모드 `#FFFFFF`(흰색, 우측 보더로 구분) / 다크 모드 `#0F0F0F`(페이지 배경 `#1A1A1A`보다 진한 차콜)
- 파랑·주황·회색 계열 차트 색상 절대 사용 금지
- 상태 색상: success `#BC216D`(브랜드)·warning `#D97706`·danger `#DC2626` — 뱃지·텍스트 전용, 차트에 불가
- 차트 색상은 `--chart-1`~`--chart-6` CSS 변수로 관리 — 라이트/다크 모드 자동 분기
  - 라이트 모드 기저색: `rgb(212,46,120)` (hsl 330°,65%,51%) — 흰 배경 합성 시 채도 유지
  - 다크 모드 기저색: `rgb(188,33,109)` (CANB Magenta 원색) — `[data-theme="dark"]`에서 오버라이드
- `--chart-*` 변경은 차트·진행률 바에만 영향. 텍스트·배지·아이콘은 `--color-primary` 등 별도 토큰 사용 — 영향 없음
- 상세 스펙 → `DESIGN SYSTEM.MD`

---

## CSS 변수 색상 규칙 (필수)

**브랜드 색상을 하드코딩하면 브랜드 전환 불가. 반드시 CSS 변수 사용.**

| 금지 (하드코딩) | 대체 CSS 변수 |
|----------------|--------------|
| `rgba(188,33,109,0.08)` | `var(--color-primary-a08)` |
| `rgba(188,33,109,0.10)` | `var(--color-primary-a10)` |
| `rgba(188,33,109,0.15)` | `var(--color-primary-a15)` |
| `rgba(188,33,109,0.25)` | `var(--color-primary-a25)` |
| `rgba(188,33,109,0.30)` | `var(--color-primary-a30)` |
| `rgba(188,33,109,0.50)` | `var(--color-primary-a50)` |

- 박스 그림자·포커스 링·배경 어디서도 `rgba(188,33,109,...)` 직접 사용 금지
- `#BC216D` 하드코딩도 금지 — `var(--color-primary)` 사용
- 배경·테두리·그림자는 반드시 중립 `rgba(0,0,0,...)` 또는 CSS 변수 사용

---

## 배지(Badge) 색상 규칙

상태 배지는 정보 우선순위에 따라 색상 부여. **완료/정상/중립 상태는 모노톤(`badge-neutral`)**, 주의가 필요한 상태만 컬러 사용.

| 배지 클래스 | 색상 | 사용 케이스 |
|-------------|------|-------------|
| `badge-success` | 브랜드 마젠타 | 재원, 운영중(활성 상태) |
| `badge-primary` | 브랜드 마젠타 | 정규직 |
| `badge-neutral` | 회색 모노톤 | 완납, 완료, 재직, 퇴원, 마감(강좌), 시간강사, 과목 태그, 일반 카테고리 |
| `badge-warning` | 황색 | 미납, 부분납, 지각, 조퇴, 휴원, 개설예정 |
| `badge-danger` | 적색 | 결석, 시험일정(공지 카테고리) |
| `badge-info` | 브랜드 마젠타 | 사용 지양 — badge-success/primary로 대체 |

---

## 버튼(Button) 규칙

| 클래스 | 용도 |
|--------|------|
| `btn btn-primary` | 주요 액션 (등록, 저장) — 브랜드 마젠타 배경 |
| `btn btn-secondary` | 보조 액션 (상세, 영수증, 연락하기) — 중립 |
| `btn btn-ghost` | 삭제 액션 — `#4B5563` 배경 + 흰색 텍스트 (수정 버튼과 시각적으로 구분) |
| `btn-sm` | 테이블 내 작은 버튼 |

---

## 카드(Card) 구조 규칙

```html
<!-- 카드 기본 구조 -->
<div class="card">
  <div class="card-header">
    <div class="card-title">제목</div>
    <!-- 우측에 period-tabs 등 선택적 추가 -->
  </div>
  <div class="card-body">
    <!-- 콘텐츠 -->
  </div>
</div>
```

- `.card` — `background: var(--color-bg)`, border 없음 (shadow만)
- `.card-header` — `padding: var(--space-5) var(--space-6) 0` (하단 padding·border 없음)
- `.card-body` — `padding: 12px var(--space-6) var(--space-6)`
- 테이블 `<thead>` — `style="border-top: var(--border-default);"` 직접 지정
- 테이블 마지막 행 — `tbody tr:last-child { border-bottom: var(--border-default); }` (전역 CSS)

---

## 필터바(Filter Bar) 규칙

**필터바는 항상 카드 외부에 독립 배치.** 카드 내부에 절대 넣지 않음.

```html
<!-- 올바른 패턴: 필터바 → 카드 순서로 분리 -->
<div class="filter-bar">
  <div class="search-box">...</div>
  <select class="filter-select">...</select>
</div>
<div class="card section">
  <div class="card-header">
    <div class="card-title">목록 제목</div>
  </div>
  <div class="card-body">
    <div class="table-wrapper">...
```

- 라이트 모드: `background: var(--color-bg-muted)` (`#EDEFF2`) — 카드보다 약간 진한 회색
- 다크 모드: `background: var(--color-sidebar-bg)` (`#0F0F0F`) — 카드(`#111111`)보다 어둡게 자동 적용 (CSS 전역 오버라이드)
- 레이아웃: `display: flex; align-items: center; justify-content: center; flex-wrap: wrap; width: 100%`

---

## 차트 설정 규칙

### setChartDefaults() — utils.js

```javascript
Chart.defaults.datasets.bar.barPercentage = 0.45;
Chart.defaults.datasets.bar.maxBarThickness = 32;  // 막대 최대 픽셀 너비 고정
```

### 데이터셋 개별 설정 (전역 기본값과 별도로 명시)

```javascript
datasets: [{
  barPercentage: 0.45,   // 항상 명시 (belt-and-suspenders)
  borderRadius: 3,
  backgroundColor: c[0],
}]
```

### 라인 차트 통일 스타일 (월별 추이 등)

```javascript
{
  type: 'line',
  tension: 0.4,
  fill: false,
  borderWidth: 2,
  pointRadius: 5,
  pointHoverRadius: 7,
  pointBackgroundColor: c[0],
  pointBorderColor: 'transparent',
  pointBorderWidth: 0,
}
```

---

## 공통 초기화 패턴

모든 페이지 `<script>` 블록 최상단:

```javascript
initLayout('현재파일명.html');   // 사이드바 + 테마 토글 버튼 + 브레드크럼 렌더링
```

**차트 페이지 초기화 패턴 (테마 대응):**

```javascript
let _charts = {};

function buildCharts() {
  setChartDefaults();
  Object.values(_charts).forEach(ch => ch && ch.destroy());
  const c = getChartColors();   // 현재 테마에 맞는 --chart-* 색상 배열

  _charts.myChart = new Chart(el, {
    data: { datasets: [{ backgroundColor: c[0], barPercentage: 0.45, ... }] }
  });
}

buildCharts();
window.addEventListener('themechange', buildCharts);
```

**Flatpickr 사용 시:**

```javascript
flatpickr('#inputId', {
  locale: fpLocale,
  dateFormat: 'Y.m.d',
  disableMobile: true,
  onReady:       function(_, __, fp) { initFlatpickrSelects(fp); },
  onMonthChange: function(_, __, fp) { syncFlatpickrMonthLabel(fp); },
});
```

**커스텀 셀렉트:** `<select class="filter-select">` 클래스만 붙이면 자동 변환

---

## 브레드크럼(Breadcrumb) 네비게이션

`layout.js`의 `initLayout()` 호출 시 자동 생성. `.topbar-title` 요소를 대체:
- index.html: 현재 페이지명만 표시
- 그 외 페이지: 🏠 홈 아이콘 `›` 현재 페이지명 형태로 렌더링

---

## 중요 구현 제약사항

| 항목 | 규칙 |
|------|------|
| `.flatpickr-calendar` CSS | `overflow: visible !important` 만 사용. `position: relative !important` 절대 금지 (팝업 위치 깨짐) |
| `.fp-cal-dropdown` | 기본 `top: -9999px` — JS가 open 시 `headerHeight px`로 설정 |
| `numInputWrapper` 숨김 | CSS `!important`로 숨기면 JS `display:none`이 무시됨 — JS로만 숨길 것 |
| 사이드바 OverlayScrollbars | 초기화 금지 — 내부 너비 축소로 콘텐츠 영역과 틈 발생 |
| 차트 색상 업데이트 | `getChartColors()` + `buildCharts()` 패턴 사용. `layout.js`가 `themechange` 이벤트 dispatch — 페이지에서 `window.addEventListener('themechange', buildCharts)` 등록 필수 |
| CSS 변수 차트 색상 | Progress bar 등 비Chart.js 요소는 `style="background:var(--chart-N)"` 사용 — 테마 전환 자동 반영 |
| 컬러 스코프 | `#BC216D` 사용처: 차트 데이터·버튼·활성 네비·배지(primary/success)·포커스 링만. 배경·텍스트·테두리에는 중립 회색 계열 사용 |
| 마젠타 틴트 금지 | 배경·테두리·그림자에 `rgba(188,33,109,...)` 또는 `#6E1040` 계열 사용 금지. 중립 rgba(0,0,0,...) 또는 CSS 변수 사용 |
| 필터바 위치 | 카드 내부 금지 — 반드시 카드 외부 독립 배치 |
| 막대 그래프 두께 | `barPercentage: 0.45` + `maxBarThickness: 32` 항상 적용 — 데이터 수 달라도 일관된 두께 |
| `.icon-btn` 크기 | `width: 36px; height: 36px` 고정 — 인라인 스타일로 변경 금지. `common.css` 정의 클래스 그대로 사용 |
| `.poly-nav-label` | LNB 섹션 헤더 사용 시 각 HTML `<style>` 블록에 직접 정의 필수 (common.css 미정의). 누락 시 섹션 헤더가 일반 텍스트 스타일로 렌더링됨 |

---

## 다크 모드

`<html data-theme="dark">` 로 제어. `localStorage('canbTheme')` 저장.
탑바 우측 달·해 아이콘 버튼이 자동 삽입됨 (`layout.js`).

**다크 모드 주요 색상:**

| 토큰 | 값 | 용도 |
|------|----|------|
| `--color-bg` | `#111111` | 카드 배경 |
| `--color-bg-subtle` | `#1A1A1A` | 페이지 배경 |
| `--color-bg-muted` | `#252525` | 라이트모드 필터바 (다크모드는 오버라이드됨) |
| `--color-sidebar-bg` | `#0F0F0F` | 사이드바 + 다크모드 필터바 배경 |

토큰 오버라이드 상세 → `DESIGN SYSTEM.MD` § 8.
