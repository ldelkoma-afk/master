# 마케팅 전략 관리 홈페이지

## 프로젝트 개요
상품별 마케팅 전략을 관리하는 단일 페이지 웹앱.
- 파일: `index.html` 하나로 구성 (CSS, JS 모두 포함)
- 데이터: 브라우저 `localStorage` 키 `mktDash2` 에 저장
- 배포: GitHub Pages (`main` 브랜치 → `ldelkoma-afk.github.io/master/`)
- 개발 브랜치: `claude/marketing-strategy-dashboard-DM0Yw`

## 레이아웃 구조
```
┌─────────────────────────────────────────────────────┐
│ [전략 ＼ 상품]  [⠿ CCTV ✕] [⠿ 복합기 렌탈 ✕] [＋] │  ← 상단 상품 탭
├──────────────┬──────────────────────────────────────┤
│ ⠿ 시장분석 ✕│                                      │
│  ＋ 하위추가 │     메인 콘텐츠 영역                  │
│ ⠿ 영업전략 ✕│     (iframe URL 또는 srcdoc HTML)    │
│ ⠿ 마케팅1  ✕│                                      │
│ ＋ 전략 추가 │                                      │
└──────────────┴──────────────────────────────────────┘
```

## 데이터 구조 (state)
```javascript
{
  products: [{ id, name }],

  // 상품별 독립 전략 목록 (핵심: 상품마다 다른 전략)
  productStrategies: {
    [productId]: [{ id, name, collapsed }]
  },

  // 상품별 하위 카테고리
  productSubs: {
    [productId]: {
      [strategyId]: [{ id, name }]
    }
  },

  // 콘텐츠: URL 또는 HTML
  cells: {
    "pId_sId":       { type: 'url'|'html', value: '...' },
    "pId_sub_subId": { type: 'url'|'html', value: '...' }
  }
}
```

## 셀 키 규칙
- 전략 셀: `"${productId}_${strategyId}"`
- 하위 카테고리 셀: `"${productId}_sub_${subId}"`
- stratKey 형식: `"s_${id}"` (전략) / `"sub_${id}"` (하위)

## 콘텐츠 표시 방식
- `type: 'url'` → `<iframe src="...">` (sandbox 속성 포함)
- `type: 'html'` → `<iframe srcdoc="...">` (HTML 직접 렌더링)
- 빈 셀 → 빈 상태 UI (URL 등록 / HTML 작성 버튼 표시)

## 주요 기능
- 상품 탭: 추가 / 삭제 / 이름 수정 / 드래그 순서 변경
- 전략 목록: 추가 / 삭제 / 이름 수정 / 드래그 순서 변경
- 하위 카테고리: 추가 / 삭제 / 이름 수정 / 접기·펼치기
- 콘텐츠: URL 임베드 또는 HTML 붙여넣기
- 툴바: 새 탭 열기 / 콘텐츠 삭제 (🗑)

## 디자인 시스템
```css
/* 주요 색상 */
--primary:       #4f6ef7   /* 버튼, 액티브 상태 */
--primary-dark:  #3b55e6
--header-bg:     #1e293b   /* 상단 탭 바 배경 */
--sidebar-bg:    #ffffff   /* 사이드바 배경 */
--border:        #e2e8f0
--bg:            #f8fafc
--text:          #1e293b
--muted:         #94a3b8
--danger:        #ef4444

/* 레이아웃 */
/* body: flex column, height 100vh, overflow hidden */
/* .top-bar: height 52px, flex row */
/* .corner: width 260px (사이드바 너비와 동일) */
/* .sidebar: width 260px, background white */
/* .content-area: flex 1 */
```

## 반복된 버그 패턴 주의
### stopPropagation 버그
`<input onclick="event.stopPropagation()">` 처럼 stopPropagation만 하면
부모의 selectProduct/selectStrategy가 호출되지 않음.
**올바른 패턴:**
```javascript
// 이미 선택된 항목이면 재렌더링 없이 수정 가능
onclick="if(activeProductId !== id) selectProduct(id); event.stopPropagation()"
onclick="if(activeStrategyKey !== key) selectStrategy(key); event.stopPropagation()"
```

### 인라인 이름 수정
클릭 시 render()가 호출되면 input이 파괴되어 수정 불가.
**해결법:** 이미 선택된 항목 클릭 시 render() 생략.

## 드래그 앤 드롭 구현 패턴
```javascript
// 드래그 중 표시
element.classList.add('is-dragging')  // opacity: 0.35

// 드롭 위치 표시
element.classList.add('drag-before')  // 앞에 삽입 (파란 선)
element.classList.add('drag-after')   // 뒤에 삽입 (파란 선)

// 순서 변경
const [item] = arr.splice(fromIdx, 1);
arr.splice(after ? toIdx + 1 : toIdx, 0, item);
save(); render();
```

## 모달 엔진
```javascript
openModal(title, subtitle, fields, callback, okLabel, opts)
// fields: [{ id, label, placeholder, value, type: 'input'|'textarea' }]
// opts: { wide: true }  → 넓은 모달 (HTML 편집용)
```

## Git 워크플로우
```bash
# 작업은 feature 브랜치에서
git checkout claude/marketing-strategy-dashboard-DM0Yw

# 커밋 후 main에 머지해서 GitHub Pages 배포
git checkout main
git merge claude/marketing-strategy-dashboard-DM0Yw --ff-only
git push origin main

# feature 브랜치도 푸시
git checkout claude/marketing-strategy-dashboard-DM0Yw
git push origin claude/marketing-strategy-dashboard-DM0Yw
```

## localStorage 마이그레이션
이전 포맷(`state.strategies` 전역 배열)에서 현재 포맷으로 자동 마이그레이션
load() 함수에서 처리. 키: `mktDash2`
