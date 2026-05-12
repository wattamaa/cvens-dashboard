# CVENS 팀 대시보드 — 세션 인계 문서

이 문서 하나로 다음 세션에서 작업을 이어갈 수 있도록 모든 컨텍스트, 요구, 결정, 코드 구조를 정리.

---

## 0. 프로젝트 한 줄 요약

사용자(minuk.jeon@cvens.ai, 팀 CVENS)의 요청으로 만든 **단일 HTML 파일** 형태의 팀 대시보드.
글라스모피즘 디자인, localStorage 영속화, 풀 CRUD, 다중 view (대시보드 + 회의록), 채팅 스타일 메모 패널.

작업 파일: **`outputs/bookstore.html`** (이름은 초기 데모 잔재 — 사용자가 바꿔달라 하기 전엔 유지)

---

## 1. 대화 흐름 / 요청 시간순

각 단계는 사용자가 명시적으로 요청한 것을 그대로 인용 + 어떻게 처리했는지.

### 1단계: 첫 도서관 UI 복제
> "이걸 토대로 웹사이트 제작하고싶어, 레이아웃 및 구성 글라스모피즘 디테일까지 참고해서 사이트 만들어줘 다른점은 하나도 없게"
- 책 도서관 글라스모피즘 UI 스크린샷을 단일 HTML로 복제 (좌측 아이콘 레일, 사이드바, 책 4×2 그리드)
- 배경: Unsplash 거실 사진. 책 표지: CSS 그라데이션으로 mock.

### 2단계: 작업 방식 합의
> "이제부터 내가 너에게 명령하면 이 디자인과 레이아웃을 계속 유지한채로 수정해 가능해? 그리고 수시로 부분마다 수정하는 방법이 없나?"
- → 디자인 톤·레이아웃 유지 약속. 부분 수정 = `Edit` 도구로 핀포인트 변경.

### 3단계: 메모 패널
> "메인 페이지에 메모 기능을 오른쪽 사이드 탭에 생성, 메모는 스레드 형태로 채팅창처럼"
- 우측에 채팅 thread 스타일 메모 패널 추가. 좌·우 말풍선, 입력 + 전송.

### 4단계: 사용자 시스템
> "가로 너비를 조금 더 키워주고 세로는 지금 윈도우 창을 초과하고있어 이거 수정. 그리고 각 메모 하단에는 누가 작성했는지 이름 뜨게 해줘. 팀 멤버는 고동기 / 이유민 / 전민욱. 처음 접속하면 누구로 시작할건지 선택하게 해주고 한번 기억해놓으면 계속 유지되도록"
- viewport 맞춤(`body{height:100vh;overflow:hidden}` + 내부 영역 각각 scroll). 가로 1500px.
- 각 말풍선에 작성자 이름 메타.
- 첫 접속 시 picker 모달 (3명 중 선택), `localStorage cv_user`에 저장.
- 본인 메모 = 우측 푸른 풍선, 타인 = 좌측 회색.
- 메모 헤더에 사용자 chip 표시(녹색 점 + 이름) → 클릭 시 사용자 변경.

### 5단계: 책 → 퀵 링크
> "이제 책들은 모두 없애. 맨 윗 부분에는 퀵 링크로 아이콘들 일열로 나열되게 해줘, 동그란 아이콘에 디자인에 잘 맞게"
- 책 카드 전부 제거 → 동그란 글래스 아이콘 8개 (캘린더/메일/노트/음악/사진/파일/채팅/설정).

### 6단계: 팀 목표 섹션
> "그 아래에는 섹션 한개를 팀 목표로 만들어줘. 그 목표들은 모여있다가 누르면 짜라락 나오게 해주고, 카드: 목표명/진행률(숫자 좀 크게)/참여자(팀 멤버 기준)"
- collapsed: 4장 살짝 어긋나 stack. h3 클릭 → 2×2 grid로 펼쳐짐 (transform 트랜지션).
- 카드: 목표명 / 큰 % 숫자 / 진행 바 / 참여자 아바타(겹친 원, 색상별).

### 7단계: 타임라인 첫 버전
> "오케이 아래는 타임 라인 섹션 만들어줘, 가로로 볼거야"
- 가로 라인 + 위/아래 지그재그 카드 + 도트. 카테고리 6종 색상.

### 8단계: 타임라인 구간화 + 전체 리팩토링
> "타임라인이 언제부터 언제까지 이렇게 설정할 수 있는거 아니야? 추가로 지금 진행하는것에 대해서는 모두 커스텀 및 추가 삭제 등 가능하게 해줘. 맨 윗 부분 퀵 링크는 중제목이고. 맨 상단에 주 제목은 CVENS로 설정해줘, 그리고 그 옆에는 전역 검색 가능하게 검색창 추가해줘"
- **대규모 리팩토링** (file 거의 통째 재작성):
  - 카드 grid를 `topbar / sidebar+main+memo` 2-row로 재구성
  - 글로벌 헤더: CVENS 로고 + 검색창(⌘K) + 알림/설정 버튼
  - Quick Links를 중제목으로 강등
  - 타임라인을 시작/종료일 기반 % 좌표 배치, 월별 틱, TODAY 마커
  - Quick Links · 팀 목표 · 타임라인 모두 CRUD + localStorage

### 9단계: 편집 모드 분리
> "수정을 하는건 맞는데, 실시간으로 바로 수정이 아니라, 각 섹션 옆에 편집 또는 추가 버튼으로 누를 시에만 그 항목들 수정 활성화되도록"
- 각 섹션 헤더에 **편집(연필) 토글** 추가. 누르면 섹션에 `.editing` 클래스 부여.
- 편집 모드에서만 `+ 추가` 버튼과 항목별 편집/삭제 affordance 노출.
- 일반 모드: 카드 클릭 무반응. 편집 모드: 카드 클릭 → 편집 모달.

### 10단계: 진행 메모 시작
> "어디까지 일했지 기억이 안나네 md에는 수시로 저장하고 있었나?"
> "다음 세션에 넘기게 수시로 md 에 저장해주셈"
- → `PROGRESS.md` 생성, 변경마다 갱신 약속.

### 11단계: 타임라인 라벨 충돌 픽스
> "타임라인이 보기 좀 이상하게 되어있지 않나?"
- 위 카드의 날짜 라벨이 라인 아래로 떨어져 아래 카드 라벨과 충돌.
- → 날짜·태그를 카드 안 메타 row로 통합, 카드↔도트 사이에 짧은 stem.

### 12단계: 사이드바 트리 + 투명도 + 아이콘 레일 제거
> "맨 좌측 아이콘 있는 탭들은 삭제. 좌측 사이드탭은 내가 수정할수 있게. 글라스 모피즘 투명도 더 올려줘 더 투명해보이게. 좌측탭 상위폴더 및 하위탭들 구분지을수 있게"
- 좌측 `.icon-rail` 통째 제거 + `.card { margin-left }` 제거.
- `--glass-bg` alpha 0.35→0.18 (대폭 투명).
- 사이드바를 **폴더 → 항목 트리**로 재설계. 폴더 chevron 펼침/접힘. 사이드바 자체에도 편집 토글.

### 13단계: 타임라인 시각 변경
> "타임라인이란 언제부터 언제까지 무슨일을 하겠다라고 바 형태로 되어있는게 제일 좋을듯"
- → 점 이벤트 → **Gantt 스타일 가로 바**.
- 이벤트 데이터: `{start, end, title, category}` (마이그레이션 자동: 옛 `date` → `start=end=date`).
- Lane 자동 할당 알고리즘: 시작일 정렬 → 가장 위 빈 레인 배치.

### 14단계: 메모 편집/삭제 + 회의록 + 동작 안 하던 버튼들
> "현재 작동하지 않고 있는 모양뿐인 버튼이나 이런것들 모두 동작하게 설정해줘. 메모도 삭제 및 편집 가능하게 해주고 (당사자가 쓴 메모에 대해서). 그리고 회의록 탭 생성해두었어 우리는 이렇게 쓰고있으니까 포맷 참고해서 톤앤매너에 맞게 회의록 탭 잘 구성해줘"
- 메모: 본인이 쓴 풍선만 hover 시 좌하단 펜·휴지통 노출. 편집은 textarea 모달.
- 회의록 view 신규 추가 (사용자가 첨부한 Notion 포맷 그대로):
  - 📌 데일리 미팅 준수 사항(회색) → 참석자/장소/특이사항 → 📢 대표 공지(파랑) → ⚠ 팀 내 이슈(빨강) → 📍 팀원별 공유(갈색 표) → 💡 결정/특이사항(초록).
- View 스위칭 시스템 도입: 사이드바 항목에 `view` 필드 → 클릭 시 `setView()` 호출.
- topbar 🔔 알림, ⚙ 설정 버튼 popover 활성화 (mock 알림 / 사용자 변경 + 데이터 초기화).

### 15단계: 미반영 버그 일괄 픽스
> "반영되지 않은게 많아 웹사이트전체적으로 확인해봐"
- 점검 결과:
  - 전역 검색이 옛 `.t-event` 셀렉터를 보고 있어서 timeline 검색 안 됨 → `.t-bar`로 픽스.
  - 기존 `cv_sidebar` localStorage가 새 메뉴(대시보드/회의록)를 모름 → 자동 마이그레이션 추가.

### 17단계: Firebase 실시간 동기화 (멀티유저 협업)
> "팀원들 실시간 편집도 가능해야하고 이 메일과 컴터가 없더라도 팀원들은 계속 편집 및 수정 가능해야함" + "파이어베이스 ㄱㄱ"
- 사용자가 직접 Firebase 프로젝트 `cvens-dfabb` 생성 (Spark 무료 요금제), Realtime Database 활성화 (asia-southeast1, 테스트 모드).
- HTML에 Firebase JS SDK CDN 2개 (`firebase-app-compat.js`, `firebase-database-compat.js`) 추가.
- `firebaseConfig` + `databaseURL` 박아두고 `window.__cvDb = firebase.database()` 글로벌.
- **공유 데이터** (Quick Links / 팀 목표 / 타임라인 range·events / 메모 / 사이드바 / 회의록) 만 Firebase RTDB `/cvens/<path>` 에 저장.
- **개인 prefs** (cv_user / cv_active_view / cv_current_meeting / cv_meeting_sort) 는 localStorage에만 유지 — 각자의 화면 상태.
- `save(key, value)` 패치: localStorage 저장 + `FIREBASE_PATHS[key]` 가 있으면 Firebase에도 push.
- `attachFirebaseListeners()`: 각 공유 키마다 `.ref(...).on('value', ...)` 리스너. 클라우드가 비어 있으면 로컬 state를 푸시. 데이터 들어오면 state 갱신 + 해당 render 함수 호출.
- 토픽바에 연결 상태 dot 추가 (`#connDot`): 녹색=연결됨, 핑크=오프라인. `.info/connected` 리스너 사용.
- 결과: 한 명이 편집 → 모든 팀원 화면에 즉시 반영. 본인 컴터 꺼도 됨. 오프라인이면 변경 큐잉 → 재연결 시 자동 sync (Firebase RTDB 내장).
- 보안: 현재 RTDB 룰 = `read/write: true` (URL 알면 누구나). 추후 인증 추가 시 룰 강화 필요.

### 16단계: 중복 + 회의록 리스트 + 정렬 + 다크 BG
> "회의록 대시보드 탭이 왜 2번 중복되었어. 미팅록 포맷은 저게 맞는데, 한번만 회의하고 끝낼게 아니잖아. 회의록 리스트를 만들어야지, 날짜들 오름차순 내림차순들 적용 가능하게. 내가 주문한거 이외에 한번더 생각해서 픽스해, 그리고 아직 안먹는 버튼들있엉"
> (이어서) "배경 어두운 그라데이션으로 변경해주셈 잘 안보임"
- 마이그레이션을 멱등(idempotent)하게: view별 dedupe + label만 같고 view 미바인딩인 legacy 항목 drop.
- 회의록 toolbar의 select dropdown → **회의 리스트 카드** + 정렬 토글(최신순↓/오래된순↑).
- CVENS 브랜드 클릭 → 대시보드.
- view 미바인딩 사이드바 항목도 클릭 시 dashboardView fallback navigate.
- 메모 헤더 ⋯ 버튼 동작화 (본인 메모 일괄 삭제).
- 더미 회의 3건 데이터.
- 배경: Unsplash 거실 → **다크 라디알 그라데이션** (보라/네이비 spot + 검은 핵).

### 18단계: 전반 점검 + 응급+잡결함 일괄 fix (commit d2ee457)
> "지금 웹사이트 전체적으로 살펴봐 이슈있는 부분 세세하게 하나하나씩 클릭해보고, 그리고 실무진 입장에서 통상적으로 이건 이렇게 보완해야한다 이런부분도 전체 체크해"
- 사이트 전체 코드/콘솔 점검 + 8개 패치 한 커밋:
  1. **CRITICAL**: `var FIREBASE_PATHS` 호이스팅 (1782 선언, 1755 사용) → 페이지 로드 즉시 `Cannot set properties of undefined` 예외 → 콘텐츠 미렌더 + picker 미표시. 빈 선언을 채움 코드 위로 이동.
  2. `.t-bar { overflow: hidden }` + `.t-actions { top:-10px; right:-10px }` 충돌로 편집 액션 버튼 잘림 → overflow: visible.
  3. 사이드바 view-less 항목 클릭 시 active 강조가 대시보드로 옮결 → 클릭 항목 자체 active.
  4. "모든 데이터 초기화" confirm 문구 누락 항목 추가 + Firebase 영향 안내.
  5. 모달 Enter 키 저장 지원 (textarea는 줄바꿈 유지).
  6. 전역 검색을 사이드바 트리/회의록 리스트까지 확장.
  7. 죽은 `.icon-rail` CSS 블록 + responsive 셀렉터 모두 제거.
  8. 알림 popover mock 3건 → "최근 활동 알림이 없습니다" 빈 상태.

### 19단계: 타임라인 노션 스타일 재설계 (commit 02fa8d4)
> "타임라인 원래 저런 형식 아니쟖아, 노션의 타임라인을 인용하고"
- Gantt lane(자동 lane 할당) → Notion Timeline view식 1행 1항목 grid:
  - 새 `.t-header` (200px 라벨 컬럼 + 시간축), `.t-rows` 컨테이너, `.t-row` (200px 1fr grid)
  - 좌측 `.t-row-label`: `.t-cat-tag.cat-XX` 카테고리 태그 + `.t-row-name` 항목명
  - 우측 `.t-row-track`: `.t-bar` 절대좌표 배치 (vertical center)
  - 옷 `.t-lanes/.t-lane` CSS 제거
- HTML: `<div class="t-lanes" id="tLanes">` → `<div class="t-rows" id="tRows">`. t-header 신설.
- JS `renderTimeline()` 재작성: lane 알고리즘 제거, 정렬 후 1∶1 매핑. badge 'tasks' → '항목'.
- `t-today` 좌표 라벨 컬럼 200px 보정.

### 20단계: 회의록 리스트 accordion 인터랙션 (commit b43bc53)
> "회의록도 원래 리스트 쑌라락 있고 그걸 클릭하면 나오는 형태 회의록도 수정 가능하게 해주고"
- 회의 list + detail 항상 같이 보이던 구조 → accordion. 평소 리스트만, 행 클릭 시 그 아래 detail inline 펼침. 재클릭 시 닫힘.
- 새 `.ml-row` wrapper, `.ml-detail` (display:none → active 시 block).
- `.meeting-list` max-height 제거 (스크롤은 main이 처리).
- `.ml-arrow` 기본 보이게 + active `▼` / 비활성 `▶`.
- HTML: `<div class="meeting-page" id="meetingPage">` 제거.
- `renderMeetings()` 재작성 + `buildMeetingDetail()` 분리. share 표는 동적 TEAM 참조.
- 인라인 필드별 편집은 다음 단계로 미루었음 (toolbar 연필 버튼 모달은 그대로).

---

## 2. 디자인 원칙 (절대 깨면 안 됨)

- **글라스모피즘** 톤 유지: `backdrop-filter: blur(...) saturate(...)` + 반투명 배경 + 미세한 흰색 테두리.
- 모든 색은 `:root` 의 CSS 변수로 통일:
  - `--glass-bg: rgba(40,40,45,0.18)` / `--glass-bg-strong: rgba(60,60,65,0.26)`
  - `--glass-border: rgba(255,255,255,0.14)` / `--glass-border-strong: rgba(255,255,255,0.22)`
  - `--text: #fff` / `--text-dim: rgba(255,255,255,0.65)` / `--text-faint: rgba(255,255,255,0.45)`
  - `--accent: rgba(120,160,220,0.28)` / `--accent-strong: rgba(160,195,240,0.38)`
- 강조 푸른빛 = 본인/포커스. 강조 초록 = 편집 모드 시그널만 (다른 의미로 쓰지 말 것).
- 다크 그라데이션 배경 (보라 + 네이비 spot). Unsplash 사진 더 이상 안 씀.
- 폰트 weight: 400 / 500 / 600 만. 700 사용 자제.
- 카드 둥근 모서리: 기본 `12px`, 큰 카드 `18~22px`.

---

## 3. 현재 레이아웃

```
┌──────────────────────────────────────────────────────────────┐
│ topbar:  [CV] CVENS    [global search ⌘K]    [🔔][⚙]        │
├────────┬───────────────────────────────────────┬─────────────┤
│ side   │ main (scrolls)                        │ memo        │
│ - Home │  ┌ View: dashboardView ┐              │ (chat       │
│ tree:  │  │  • Quick Links 섹션 │              │  thread)    │
│  ▼ Wsp │  │  • 팀 목표 (deck)   │              │             │
│   대시보드│  │  • 타임라인 (Gantt)│              │ - 본인:    │
│   회의록 │  └─────────────────────┘              │   푸른     │
│   ...  │  ┌ View: meetingView ┐                │   풍선     │
│  ▶ Per │  │  toolbar + list +   │              │ - 타인:    │
│   ...  │  │  meeting detail     │              │   회색     │
│        │  └─────────────────────┘              │             │
└────────┴───────────────────────────────────────┴─────────────┘
```

너비: `min(1500px, 100%)`. 높이: `100vh - 56px` (body padding).

3-column grid: `260px 1fr 340px`. 모바일에서는 1col stack (media query).

---

## 4. 섹션별 기능 상세

### 4.1 글로벌 토픽바 (topbar)
- `[CV]` 로고 + `CVENS` 그라데이션 텍스트. 브랜드 영역 클릭 → `setView('dashboardView')`.
- 전역 검색 `#globalSearch`: 입력하면 `.q-link / .goal-card / .t-bar / .bubble` 중 매칭 안 되는 것의 opacity를 0.25로 낮춤.
- ⌘K (Mac) / Ctrl+K (Win) → 검색 포커스. Esc → 모달 닫기.
- 🔔 `#bellBtn` → `#bellPop` popover (mock 알림 3개).
- ⚙ `#settingsBtn` → `#settingsPop` popover (현재 사용자 / 사용자 변경 / 모든 데이터 초기화).
- popover 외부 클릭 시 자동 닫힘. popover 내부 클릭은 stopPropagation.

### 4.2 사이드바 (`#sidebarSection`)
- 헤더: "Home / 팀 대시보드" + 편집 토글 + 폴더 추가 버튼.
- **트리 구조**: 폴더 → 항목.
  - 폴더 (`.sb-folder-group` + `.sb-folder`): chevron 클릭으로 펼침/접힘 (`.expanded` 토글). 작은 대문자 + 두꺼운 글자 + 넓은 자간.
  - 항목 (`.sb-item`): 한 단계 들여쓰기 + 좌측 색띠. 작은 dot bullet + label + 선택적 count pill.
- 편집 모드(섹션 연필 클릭) 진입 시:
  - 헤더 + 폴더 추가 버튼 노출, `.section-head` 살짝 초록 톤
  - 폴더 hover: + 항목 추가 / 폴더 편집 / 폴더 삭제
  - 항목 hover: 편집 / 삭제
  - 항목 클릭 → 편집 모달
- 일반 모드: 항목 클릭 → `setView(item.view || 'dashboardView')` (view 없는 항목도 fallback).
- 데이터: `state.sidebar = [{ id, label, expanded, children: [{ id, label, count, view? }] }]`.
- 기본 항목: Workspace 폴더에 `대시보드(dashboardView) / 회의록(meetingView) / Popular / All items`, Personal 폴더에 `Your favorite`.

### 4.3 Quick Links (대시보드 첫 섹션)
- 동그란 글래스 아이콘 8개 (기본): 캘린더 / 메일 / 노트 / 음악 / 사진 / 파일 / 채팅 / 설정.
- 색상 9종 (`COLORS`): red / blue / amber / purple / teal / orange / green / gray / pink. 그라데이션은 `COLOR_GRADIENTS` map.
- 아이콘 16종 (`ICONS`): calendar, mail, notes, music, photos, files, chat, settings, link, star, bookmark, clock, bolt, cloud, users, tasks.
- 편집 모드: hover 시 빨간 × 노출, 클릭 → 편집 모달 (이름/아이콘/색상).
- 데이터: `state.ql = [{ id, label, icon, color }]`.

### 4.4 팀 목표 (`#goals`)
- h3 ("팀 목표") 클릭 → deck 접기/펼치기. collapsed: 4장 살짝 어긋난 stack(transform). expanded: 2×2 grid.
- 카드: 목표명(2줄 clamp) / 큰 % 숫자 + 진행 바 / 참여자 아바타 (겹친 원, 색상별: a-go 파랑·a-yu 핑크·a-jun 초록).
- 편집 모드: 우상단 펜·휴지통, 클릭 → 편집 모달 (목표명/진행률/참여자 토글).
- 데이터: `state.goals = [{ id, name, progress, members[] }]`.

### 4.5 타임라인 (`#timelineSection`) — Gantt 스타일
- toolbar: 시작일·종료일 date input (항상 활성), 편집 토글, + 추가 버튼.
- `.timeline-range`: 컨테이너. 상단에 월별 틱 라벨 (`.t-months`), 옅은 세로 가이드 선이 9999px 아래로 뻗음.
- `.t-today`: 노란 점선 + TODAY 라벨 (구간 안일 때만).
- `.t-lanes` 안에 `.t-lane`들이 stack. 각 레인에 `.t-bar` (절대 좌표 `left% / width%`).
- **Lane 자동 할당**: 이벤트를 시작일 기준 정렬, 가장 위 빈 레인(`lastEnd < ev.start`) 배치. 겹치면 새 레인.
- 바: 카테고리 색 + 백드롭 블러. 안에 제목(ellipsis) + 날짜 범위 ("3.10 → 4.03").
- 상태: `done` (반투명 + line-through), `current` (흰 외곽선 + 글로우).
- 카테고리: plan(보라) / design(핑크) / dev(파랑) / qa(노랑) / ops(초록) / launch(청록).
- 편집 모드: hover 시 펜·휴지통, 클릭 → 편집 모달 (일정명/시작일/종료일/카테고리).
- 데이터: `state.events = [{ id, title, start, end, category }]`. `state.range = { start, end }`.
- 마이그레이션: 옛 `date` 필드만 있던 이벤트 자동으로 `start=end=date` 로 변환.

### 4.6 메모 (`.memo`, 우측 채팅 thread)
- 첫 접속 시 picker 모달 → 사용자 선택 (`state.user`).
- 본인 메모 = 우측 푸른 풍선(`.bubble.me`), 타인 = 좌측 회색.
- 각 풍선 하단 메타: `이름 · 시간`.
- 헤더 me-chip 클릭 → picker 다시 (사용자 변경).
- ⋯ 버튼 → 본인이 쓴 메모 일괄 삭제 (확인 후).
- 입력 폼 → 새 풍선 push. 사용자 미선택 상태로 제출 시 picker 강제 오픈.
- **본인이 쓴 메모만** hover 시 좌하단 `.b-actions` 노출 (편집 / 삭제). 타인 메모는 손댈 수 없음.
- 데이터: `state.memos = [{ id, author, text, time }]`.

### 4.7 회의록 view (`#meetingView`)
- 사이드바 "회의록" 항목 클릭으로 진입.
- toolbar: 큰 제목(yymmdd_ 크벤스 내부 미팅록) / 정렬 토글(최신순↓ ↔ 오래된순↑) / 현재 회의 편집(연필) / + 새 회의.
- 회의 **리스트** (`.meeting-list` + `.ml-item`): 각 행 = 날짜 · 제목 · 통계(참석 N · 결정 N · 이슈 N) · 활성 ► 화살표. 클릭 시 currentMeetingId 변경.
- 회의 **상세** (사용자 첨부 Notion 포맷 그대로):
  - 📌 데일리 미팅 준수 사항 (회색 callout) + 고정 bullet 3개 (아젠다/시간/소요)
  - 미팅 참석자 / 장소 / 특이사항 메타 블록
  - 📢 대표 공지 사항 (파랑 callout) + announcements 텍스트
  - 중요 공유 사항 h2
  - ⚠️ 팀 내 이슈 사항 (빨강 callout) + bullets
  - 📍 팀원 별 공유 사항 (갈색 callout) + 3행 표 (고동기/전민욱/이유민)
  - 💡 미팅 내 결정사항 및 특이사항 (초록 callout) + 결정 사항 / 특이사항 두 h3
- 편집 모달: 한 화면에 제목/날짜/장소/특이사항/참석자 토글/공지/이슈(텍스트라인=항목)/3인 공유/결정/특이.
- 데이터: `state.meetings = [{ id, date, title, attendees[], location, notes, announcements, issues[], shares{}, decisions[], specials[] }]`.
- 현재 선택: `state.currentMeetingId`. 정렬: `state.meetingSortDesc`.

---

## 5. 편집 모드 패턴

모든 CRUD 가능한 섹션이 같은 패턴을 따름:

```
.section-head 안에:
  h3 (제목) + badge + spacer + .edit-toggle (연필) + .add-btn (+)
                                       ↓
                          클릭 시 섹션 root에 .editing 클래스 토글
```

CSS 규칙:
```css
.section-head .add-btn { display: none; }              /* 기본 숨김 */
.editing .section-head .add-btn { display: grid; }     /* 편집 모드일 때만 */
.editing > .section-head { background: rgba(127,200,156,0.06); border-radius: 12px; }
.edit-toggle.active { background: rgba(127,200,156,0.32); ... color: #fff; }

/* 항목별 affordance (예: goal-card) */
.goal-actions { display: none; }
.editing .goal-actions { display: flex; }
.goal-card { cursor: default; }
.editing .goal-card { cursor: pointer; }
```

JS 패턴:
```js
function bindEditToggle(toggleId, sectionEl) {
  $(toggleId).addEventListener('click', function (e) {
    e.stopPropagation();
    sectionEl.classList.toggle('editing');
    $(toggleId).classList.toggle('active', sectionEl.classList.contains('editing'));
  });
}
bindEditToggle('editSidebar',    $('sidebarSection'));
bindEditToggle('editQuickLinks', $('qlSection'));
bindEditToggle('editGoals',      $('goals'));
bindEditToggle('editTimeline',   $('timelineSection'));
// 회의록 편집 버튼은 토글이 아니라 직접 모달 오픈 (회의는 통째로 한 번에 편집)
```

항목 클릭 핸들러는 `.editing` 안에 있을 때만 모달 오픈:
```js
onclick: function () { if (node.closest('.editing')) openXModal(item); }
```

---

## 6. View 스위칭 시스템

```html
<main class="main">
  <div class="view active" id="dashboardView">  <!-- Quick Links/Goals/Timeline -->
  <div class="view"        id="meetingView">    <!-- 회의록 toolbar/list/page -->
</main>
```

```css
.view { display: none; }
.view.active { display: block; }
```

```js
function setView(viewId) {
  if (!document.getElementById(viewId)) viewId = 'dashboardView';
  state.activeView = viewId;
  save(KEYS.view, viewId);
  document.querySelectorAll('.view').forEach(v => v.classList.toggle('active', v.id === viewId));
  // 사이드바 active 강조
  document.querySelectorAll('.sb-item.active').forEach(i => i.classList.remove('active'));
  state.sidebar.forEach(folder => (folder.children || []).forEach(item => {
    if (item.view === viewId) {
      var n = document.querySelector('.sb-item[data-id="' + item.id + '"]');
      if (n) n.classList.add('active');
    }
  }));
  if (viewId === 'meetingView') renderMeetings();
  if (viewId === 'dashboardView') renderTimeline();
}
```

새 view 추가 패턴:
1. HTML: `<div class="view" id="xxxView">...</div>` (main 안)
2. JS: setView 안에 `if (viewId === 'xxxView') renderXxx();`
3. 사이드바 항목에 `view: 'xxxView'` 추가 (또는 마이그레이션으로 자동 추가)

---

## 7. 데이터 모델 / localStorage 키

| 키 | 의미 | 기본값 |
|---|---|---|
| `cv_user` | 현재 사용자 ("고동기"/"이유민"/"전민욱") | null (picker 표시) |
| `cv_quicklinks` | Quick Links 배열 | DEFAULT_QUICKLINKS (8개) |
| `cv_goals` | 팀 목표 | DEFAULT_GOALS (4개) |
| `cv_timeline_range` | `{start, end}` | 2026-03-01 ~ 2026-06-30 |
| `cv_timeline_events` | 이벤트 (Gantt 바) | DEFAULT_EVENTS (8개) |
| `cv_memos` | 메모 thread | DEFAULT_MEMOS (3개) |
| `cv_sidebar` | 사이드바 트리 | DEFAULT_SIDEBAR |
| `cv_meetings` | 회의록 | DEFAULT_MEETINGS (3개) |
| `cv_active_view` | 활성 view id | "dashboardView" |
| `cv_current_meeting` | 회의록 선택 id | null (자동 최신) |
| `cv_meeting_sort` | 정렬 방향 (true=desc) | true |

### Firebase RTDB 경로 (공유 데이터, `/cvens/` 하위)
| RTDB 경로 | 매핑된 localStorage 키 |
|---|---|
| `/cvens/quicklinks` | `cv_quicklinks` |
| `/cvens/goals` | `cv_goals` |
| `/cvens/timeline_range` | `cv_timeline_range` |
| `/cvens/timeline_events` | `cv_timeline_events` |
| `/cvens/memos` | `cv_memos` |
| `/cvens/sidebar` | `cv_sidebar` |
| `/cvens/meetings` | `cv_meetings` |

`window.__cvDb` 가 firebase.database() 인스턴스. `FIREBASE_PATHS` 매핑 + `STATE_PROP` + `RENDER_FN` 으로 연결.

`load(key, fallback)` / `save(key, value)` 헬퍼로 일괄 처리. 모두 IIFE 시작부에서 한 번에 load.

### 고정 도메인 데이터
- `TEAM = ['고동기', '이유민', '전민욱']`
- `AVATAR_CLS = { '고동기':'a-go', '이유민':'a-yu', '전민욱':'a-jun' }`
- `INITIAL = { '고동기':'고', '이유민':'이', '전민욱':'전' }`
- `CATEGORIES = [{id:'plan',name:'기획'}, {id:'design',name:'디자인'}, {id:'dev',name:'개발'}, {id:'qa',name:'QA'}, {id:'ops',name:'운영'}, {id:'launch',name:'출시'}]`
- `COLORS = ['red','blue','amber','purple','teal','orange','green','gray','pink']`
- `ICON_KEYS` = 16종

---

## 8. 마이그레이션 (load 직후 실행)

코드는 IIFE 안 `var state = { ... }` 직후에 위치.

### A. 옛 이벤트 → start/end
```js
state.events = state.events.map(e => {
  if (e.date && !e.start) return { id:e.id, title:e.title, category:e.category, start:e.date, end:e.date };
  if (!e.end) e.end = e.start;
  return e;
});
```

### B. 사이드바 멱등 정규화 (가장 중요!)
- view 별 dedupe (seenViews set 사용)
- legacy "대시보드"/"회의록" 라벨인데 view 미바인딩이면 drop
- 첫 폴더 맨 앞에 `i_dash`, `i_meet` 가 정확히 1개씩 존재하도록 보장
- 무조건 save (화면 갱신 필요)

이게 없으면 기존 사용자가 회의록 탭에 못 들어가거나, 아니면 같은 항목이 중복 표시됨.

---

## 9. 코드 구조 (JS, IIFE 안)

```
1. ICONS (svg html), ICON_KEYS, COLORS, COLOR_GRADIENTS
2. TEAM, AVATAR_CLS, INITIAL, CATEGORIES
3. DEFAULT_QUICKLINKS / GOALS / RANGE / EVENTS / MEMOS / SIDEBAR / MEETINGS
4. KEYS, load(), save(), uid()
5. var state = { ... }
6. 마이그레이션 A (events) + B (sidebar)
7. DOM 헬퍼: $(), el(), iconSvg()
8. SIDEBAR: renderSidebar(), openFolderModal(), openSidebarItemModal()
9. VIEW: setView()
10. MEETINGS: fmtMeetingDateLabel, makeCallout, getCurrentMeeting,
    renderMeetings, makeTextarea, openMeetingModal
11. GOALS: renderGoals(), openGoalModal()
12. QUICK LINKS: renderQuickLinks(), openQuickLinkModal()
13. TIMELINE: renderTimeline(), openEventModal(), dateToMs, fmtMd, pctOf
14. MEMOS: renderMemos(), openMemoModal()
15. MODAL: showModal(), hideModal(), modalCtx
16. 사용자 picker 핸들러 (pickerList, meChip)
17. 팀 목표 deck open/close (h3 클릭)
18. 추가 버튼 핸들러: addQuickLinkBtn, addGoalBtn, addEventBtn,
    addFolderBtn, addMeetingBtn, editMeeting, sortMeetingBtn, brandHome,
    memoMoreBtn
19. EDIT-MODE bindings (4개 섹션)
20. TOPBAR popovers (bell, settings, changeUser, resetAll, document click)
21. 타임라인 range input change
22. 메모 form submit
23. 전역 검색 + ⌘K + Esc
24. 초기 렌더: renderSidebar / renderQuickLinks / renderGoals /
    renderTimeline / renderMemos / setView(state.activeView || 'dashboardView')
    + (state.user 있으면 whoami 갱신, 없으면 showPicker)
```

---

## 10. 알려진 이슈 / 향후 작업 후보

### 잠재 개선
- 회의록 편집 모달이 폼 길이가 김 — 섹션 별 inline 편집(Notion-style) 가능.
- 팀 목표 deck collapsed 상태에서 5개 이상이 되면 stack offset 한계 (현재 4개 기준).
- 메모: 본인 풍선만 편집/삭제. 그룹 admin 권한 같은 건 없음.
- 타임라인 바가 매우 좁아질 때 텍스트 truncation. 가이드 line이 너무 옅어 잘 안 보일 수 있음.
- 알림 popover (`#bellPop`)는 mock — 실제 활동 추적 안 함.
- For you/Popular/All items/Your favorite 사이드바 항목은 모두 dashboardView로 fallback. 각각 의미 있는 view를 만들어야 한다면 작업 필요.
- 모바일 레이아웃 (`max-width:960px`)에서 한 번 더 검수 필요. 메모 thread `max-height:320px`.

### 사용자가 자주 요청하는 패턴
- 새 섹션 추가 → CSS 변수 사용, `.section-head` + edit-toggle + add-btn 패턴 답습.
- 새 데이터 → `state` + `KEYS` + `load/save` + `render*()` 4-tuple.
- 새 view → `<div class="view">` + setView() 분기 + 사이드바 항목 view 필드.

---

## 11. 다음 세션 작업 가이드

### 시작 전
1. `outputs/bookstore.html` 열어서 현재 상태 확인.
2. 브라우저에서 한 번 띄워보고 localStorage 확인.
3. 사용자가 변경 요청하면 작은 영역 `Edit` 도구로 핀포인트 수정 (전체 재작성 자제 — 위험).

### 톤·디자인 절대 금지
- 색을 인라인 hex로 박지 않기. 변수 사용.
- 글라스 효과 빼지 않기 (backdrop-filter + 반투명 배경 유지).
- 700 weight 글자 자제.
- 강조 초록은 편집 모드 시그널만, 다른 의미로 쓰지 말 것.

### 상태 변경 시 반드시
1. `state.xxx` 갱신
2. `save(KEYS.xxx, state.xxx)` 호출
3. 관련 `render*()` 함수 호출

### 새 멤버 추가 요청 시
- `TEAM`, `AVATAR_CLS`, `INITIAL` 세 곳 모두 업데이트
- 회의록 share 키 추가
- (필요시) 마이그레이션으로 기존 회의 share에 빈 키 채우기

### 사용자가 확장 명령
- "이 섹션 옆에 X 추가": `.section-head` 안에 적절한 버튼/배지 추가, CSS는 같은 패턴
- "이거 색 바꿔줘": CSS 변수 또는 카테고리 별 .cat-* 규칙 변경
- "회의록에 X 필드 추가": `state.meetings` 객체에 필드 추가 + renderMeetings render + openMeetingModal 폼 추가 + DEFAULT_MEETINGS 초기값 + 마이그레이션 (기존 데이터 호환)

### 매 변경 후
- 이 PROGRESS.md "1. 대화 흐름" 섹션에 한 줄 추가.
- 영향받은 다른 섹션도 갱신.

---

## 12. 파일 위치

- 작업 파일: `/Users/minuk/Library/Application Support/Claude/local-agent-mode-sessions/d1b75fc6-e813-40d6-aea7-3b9c864d19a6/477de543-d8bb-487b-94ee-079bb9fb00e8/local_679bc09e-64b0-4bd4-b782-04394a682ac0/outputs/bookstore.html`
- 이 핸드오프 문서: `outputs/PROGRESS.md`
- 사용자에게는 `computer://...outputs/bookstore.html` 형태 링크로 공유.

---

## 13. 사용자 컨텍스트

- 이름: 민욱 (전민욱) — minuk.jeon@cvens.ai
- 팀: CVENS (3인: 고동기 / 이유민 / 전민욱)
- 회의 패턴: 매일 10:30, 30~60분, 회의 전 아젠다 사전 기재
- 회의 장소: 내부 회의실
- Notion 스타일 회의록 포맷 사용 (5종 컬러 callout: 회색·파랑·빨강·갈색·초록)

### 21단계: 대시보드 재조정 (commit c7f6371)
> Quick Links 더미 1개 + 항상 +버튼 / 팀목표 작게+펼침+체크리스트 / 팀원구성(좌) | 팀목표(우) / 타임라인 줌 / 메모 대댓글 / 글라스 스크롤바 + 어두운 미니멀 배경 / 전역 검색 실시간+이동+3초 하이라이트
- **Quick Links**: DEFAULT 1개로 축소. 섹션 +버튼 항상 보이게. Firebase /cvens/quicklinks 도 1개 reset.
- **팀 목표**: HTML 기본 open 클래스 + 카드 142→96px, pct 38→26px. 카드 클릭 → openGoalChecklistModal. state.goals[i].checklist 마이그레이션. 체크 시 progress 자동 재계산. 연필 버튼 항상.
- **팀원 구성**: 신규 state.statuses + cv_statuses + Firebase /cvens/statuses. renderTeam, openStatusModal (본인만 편집).
- **배치**: <div class="dashboard-row"> 2-col grid — 좌 팀원/우 팀목표. <=1180px stack.
- **타임라인 줌**: toolbar -/+ 버튼. zoomTimeline(factor) today 기준. 라벨 적응형 (≤ 18일 일별 / ≤ 75일 주별 / else 월별).
- **메모 대댓글**: replies 스키마 마이그레이션. .reply-btn toggle, b-reply-input. renderMemos 재작성.
- **글라스 스크롤바**: webkit 10px + firefox thin, 반투명 thumb.
- **배경**: 보라 제거. 회색 spot + 0.012 white noise + #0b0b10 → #040406.
- **전역 검색**: #searchResults dropdown. buildSearchResults—ql/goals(+checklist)/events/memos(+replies)/sidebar/meetings(전필드)/statuses. gotoSearchTarget — setView + ml-item auto-expand + #goals auto-open + scrollIntoView center + 3초 .search-highlight outline+pulse. 외부클릭 닫힘.

### 22단계: 팀목표 리스트화 + 자동 진행률 + 인라인 편집 + 타임라인 보드 인터랙션 (commit 400cb41)
> "팀목표 자를리면 리스트 형태로 / 바그래프는 체크리스트 진행 자동 / 클릭하면 상세보기 + 수정버튼으로 제목/참여자/체크리스트 섭션별 수정 / 메모는 pip 창 안 뜨고 그 자리에서 바로 수정 / 담당자는 우상단 알림 옆 + 타임라인 보드를 클릭해서 늘리고 줄이고 추가
- **팀 목표 리스트화**: deck/2-col 카드 구조 → 수직 list (.goals-list/.goal-row). 이름 잘림 없음. 행 구성: 이름+진행바 / % / 아바타 / 연필
- **자동 진행률**: state.goals[i].checklist 있으면 progress=done/total*100 (수동값 덮어씬). 비어있으면 기존 g.progress. % 텍스트에 .auto 클래스 (섬세한 색 구분)
- **상세보기 모달 (openGoalDetailModal)**: 제목 + 진행도 + 참여자 + 체크리스트. 우상단 연필 토글 → 제목 input + 참여자 toggle chip + 체크리스트 항목 inline input (일반 모드에서는 read-only). 체크 시 자동 재계산.
- **메모 인라인 편집 (startMemoInlineEdit)**: 편집 버튼 → bubble 내부 텍스트 노드를 textarea로 교체. Enter 저장 / Shift+Enter 줄바꿈 / Esc 취소 / blur commit. 그 사이 b-actions/reply-btn/b-replies 숨김.
- **Topbar user chip**: connDot 다음 자리에 #userChip 추가 (아바타+이름). 클릭 → showPicker. memo-head의 me-chip 제거. picker click → renderUserChip() + renderMemos + renderTeam.
- **타임라인 드래그 핸들**: 각 .t-bar에 .t-handle.t-handle-l/-r 6px (hover시 표시). mousedown→document mousemove/up 사용. left/width % 조정 후 mouseup으로 date 환산 + state.events save + renderTimeline. .dragging 클래스 노란 outline
- **타임라인 새 일정 버튼**: 마지막 줄 .t-row.t-row-add — 클릭시 today↝+7일 새 ev push + openEventModal.

### 23단계: 팀원/의제/모달/회의록/메모 세부 리파인 (commit 19aa8ae)
> 사용자 요청: 팀원구성 카드형 해제·아이콘+명대 / 팀목표 빈 체크리스트 → 0% / 체크리스트 추가 점선 + 버튼 / 메모… 메모 주 제목 + 설명 제거 / 삭제 버튼 정렬 / 회의록 첫 진입 점→ 닫힌 / + 버튼 새 큰 창 / 행 복사·삭제 / 메모는 대시보드 테이블에서만
- **팀원 구성**: 카드 구조 제거. .team-cards row flex(space-around). .team-card 세로 정렬 (아바타 + 이름), absolute 위치의 .tc-bubble (위에 떨어진 말풍선 + ::after 꿐리).
- **팀목표 진행률 0%**: checklist 빈 배열이면 progress 강제 0 (수동 g.progress 무시).
- **체크리스트 추가 점선 버튼**: input 자리를 .cl-add-btn (dashed border)로. 클릭 → .cl-add-input 교체 + blur/Enter에 자동 저장, 빈값이면 버튼로 돌아감 / Esc 취소.
- **목표 상세 모달 h3·sub 제거**: showModal에 title='' sub='' 전달 + .modal-card h3:empty/.sub:empty { display: none } CSS.
- **삭제 버튼 정렬**: .modal-foot align-items: center + .btn-danger margin-right:auto/align-self:center 명시.
- **회의록 첫 진입 닫힌**: renderMeetings 자동 currentMeetingId 설정 제거. 무효 id만 clear.
- **회의록 새 큰 창**: showModal 옥션 wide:true → .modal-card.large (640px, 85vh, scroll). openMeetingModal에 적용.
- **회의록 행 복사/삭제**: .ml-actions hover 표시, .ml-act (copy+휴지통). 복사는 deepClone + uid + 오늘 날짜 + "(복사)" suffix.
- **메모 대시보드 전용**: setView 안에서 .memo display 토글 + .card gridTemplateColumns 조정 (260px 1fr [340px]).

### 24단계: 회의록 인라인 편집 + 빈 캐버스 + 폴더 + Quick Links 간소화 + badge 숨김 (commits 8d331fc / 5f73e64)
> "회의록 이름·내용 바로 수정 / 새 회의는 빈 대지 / 폴더로 묶음 카드 / 퀸링크 알아서 지정 + 제목·링크 / Quick Links → 퀸 링크 / 소제목 옆 숫자 제거"
- **회의록 전체 인라인 편집 (P8)**: makeEditable 헬퍼 — contentEditable + blur/Enter/Esc. 제목·날짜·참석자 토글·장소·특이·공지·이슈 list·공유 3명·결정·특이사항 모두 그 자리에서 수정. list 항목은 + 점선 버튼 + 자동 저장 (빈값이면 제거). buildMeetingDetail 재작성.
- **새 회의 = 빈 캐버스 (P8)**: + 버튼 → createBlankMeeting() 즉시 빈 mt push + currentMeetingId 설정 + 제목 focus. 모달 안 쓴
- **회의록 폴더 (P9)**: state.meetingFolders 신규 + cv_meeting_folders KEY + Firebase /cvens/meetingFolders + meeting.folderId 마이그레이션. toolbar 폴더 추가 버튼. renderMeetings 재작성 — 폴더 그룹 카드 + 안에 회의 list. 폴더 이름 변경·삭제 (내용 미분류 이동). 회의 행 hover 시 folder select dropdown
- **Quick Links 간소화 (P9)**: openQuickLinkModal을 제목 + URL 2개 필드만。 아이콘/색 자동 랜덤 (ICON_KEYS/COLORS modulo). q.url 필드 마이그레이션. 일반 클릭 → window.open(url, _blank). 편집 모드만 모달.
- **소제목 badge 숨김 (P9)**: .section-head .badge { display: none; } — 퀸링크/팀원구성/팀목표/타임라인 숫자 모두 제거.
- **명칭 한글화 (P9)**: <h3>Quick Links</h3> → <h3>퀸 링크</h3>.

### 25단계: 회의록 폴더 카드 그리드 + 드래그/드롭 이동 + 회의 제목 인라인 편집 (commit 8393f45)
> "폴더는 라운드진 사각형 카드 / 드래그로 회의록 이동 / 회의 다른 필드도 수정"
- **폴더 카드 그리드**: 기존 bar 형 .mt-folder-group/.mt-folder-head 제거 → .meeting-folders-grid (auto-fill, min 200px) + .mt-folder-card (둘근 사각 16px radius). 카드 클릭 → .expanded (grid-column: 1 / -1) 안 회의 list 표시. 한 번에 하나만 펼침. 이름/삭제 버튼 hover
- **드래그 & 드롭**: ml-item draggable=true + dragstart/dragend. 폴더 카드 / 미분류 wrap에 dragover/leave/drop. drop 시 mt.folderId 갱신 + persist + re-render. 별도 dropdown(.ml-folder-select) 제거
- **미분류 드롭 영역**: .mt-misc-wrap (dashed border → dragover 시 solid+아이콘 틴트). 폴더 있을 때만 등장
- **회의 제목 인라인 편집**: .ml-title contentEditable=true + blur/Enter/Esc. 클릭/mousedown stopPropagation 하여 접기/펼침 트리거 방지. 수정 값이 원본과 다르면 mt.title 갱신 + save + renderMeetings

### 26단계: 회의록 자유편집 본문 + 폴더카드 리파인 + 검수 fix (commits 948dd59 / 9b08578 / 4403f05)
> "회의록 빈 대지에서 클릭/줄바꿈/자유 편집 / 영역이 아니라 모든 곳 수정 / 보완할 부분 전체 검수"
- **P11**: 미분류 wrap 제거 → 미분류도 폴더 grid 안 카드(📂)과 동일한 치. 폴더 카드 전체 영역 click → 펼침/접기 (ml-item / .ml-actions / .mt-folder-actions 클릭 제외). rules/h2 인라인 편집 추가.
- **P12**: 회의록 세션별 구조 제거 → .mt-body 단일 contentEditable. 기존 mt(rules/announcements/issues/shares/decisions/specials/h2/location/notes) 데이터는 마이그레이션으로 HTML callout/ul/p로 합쳐 mt.body에 옮김. 빈 회의는 완전 빈 캠버스 + placeholder. 자동 저장 (input → 600ms debounce, blur 즉시).
- **P13 검수 fix**: 전역 검색 buildSearchResults가 mt.announcements/issues/decisions 등만 색인하고 있었음 → mt.body의 HTML 텍스트 추출 후 검색 추가 (`<tag>` / `&entity;` 변가 제거 후 substring match + snippet 50자).

### 27단계: 회의록 노션식 좌측 트리 + 우측 본문 + 슬래시 명령 (commits 3585b67 / 438ab99)
> 사용자: 옵션 A로 설정, 수정 및 커스텀 항목 피드백 / "노션보다 더 상회하게"
- **layout 재설계 (P14a)**: 폴더 카드 grid 제거 → .mt-shell (260px 트리 + 1fr 본문). meetingView 내부 구조 교체. editMeeting 버튼 제거 (자유 편집이 인라인이라 불필요).
- **좌측 트리 (.mt-tree)**: 폴더(chevron 펼침/접기) → 회의 항목. 폴더 타쟄 대문자 + 이름 인라인 contentEditable (Enter·Esc·blur). 폴더 헤더 dragover/drop → 회의 움. 합치면 폴더 자동 펼침. 폴더 없으면 회의는 트리 root에 직접 나열. 미분류는 폴더 있을 때만 등장.
- **우측 본문 (.mt-content)**: 선택된 회의를 buildMeetingDetail로 읽어 표시. 서아 아무것도 선택되었다면 큰 이모지 + "새 회의 만들기" CTA. hover 시 우상단에 floating 복사/삭제 버튼.
- **슬래시 명령 popup (P14b)**: 본문에서 "/" 입력 → 포테 뜨움. H1·H2·H3 / 글머리표 · 번호 리스트 / 체크박스 할 일 / 4종 콜아웃 (회·파·빨·초) / 구분선. mousedown으로 선택 → 이전 "/" 제거 + html 삽입 + 커서 아이템 끝. Esc 또는 외부 클릭으로 닫힘.
- **체크박스 블록**: .task .done 클래스. input checkbox 클릭 시 text-decoration line-through.
- **트리 드래그 상태**: .mt-tree-item.dragging opacity 0.4. 폴더 head .dragover 시 백색 틴트.

### 28단계: 자동펼침 제거 + 슬래시 위치 fix + 콜아웃 색·아이콘 편집 (commits 2f482af / 4e8025e)
> 사용자: "클릭 안 하면 상세 안 뜨게 / 슬래시 자꾸 상단 이스슈 / 콜아웃 색·아이콘 수정"
- **자동 펼침 제거**: state.currentMeetingId 이기 값 load(KEYS.currentMeeting, null) → null 고정. 페이지 reload 시 이전 선택 제거 → 트리에서 클릭해야 대자시한림 펼침. save는 그대로 (세션 안에서 유지)
- **슬래시 popup 위치 fix**: range.getClientRects()[0]를 먼저. 빈 collapsed range면 startContainer→parentElement의 rect. 그것도 없으면 body 좌상단 +16/24. 화면 하단 넘으면 아이템 위로 뒤집기
- **콜아웃 색·아이콘 편집**: body mousedown 이벤트에서 .callout .ico 감지 → cursor 이동 차단 + showCalloutEditor() popup. 5색 동그라미 swatch (회·파·빨·갈·초) + 16개 아이콘 그리드 + 직접 입력

### 29단계: 모바일 반응형 + 트리 검색/호버 리뷰 + 미분류 이름 편집 + 사용자 수정 보호 (commit 37f8720)
> 사용자: "모바일 완박 / 회의록 보완 / 미분류 이름 변경 / 수정한 게 자꾸 변함"
- **모바일 반응형**: @media (max-width: 900px) — .mt-shell 1col stack (트리 위/본문 아래), .dashboard-row 1col. @media (≤720px) — .card grid 1col, topbar wrap, user-chip-name 숨김, .quick-links 4col, .t-row 110px+1fr, team-card avatar 44px, modal 92% 너비. @media (≤480px) — .quick-links 3col, range input 소형, 제목 글자 18px.
- **트리 검색 (.mt-tree-search)**: sticky top, 30px input + 지우기 × 버튼. window.__mtSearchQ에 저장. filterTree() — label/preview 텍스트 substring match, 매칭 있는 폴더 자동 expand.
- **호버 미리보기 (.preview-tt)**: 다 트리 항목 우측에 absolute popup. mt.body HTML 제거 후 140자 스니펫 + 제목 굵게. 호버 시만 표시.
- **미분류 이름 변경**: state.miscName (KEYS.miscName / FIREBASE_PATHS / cv_misc_name) 신규. 마이그레이션 default '미분류'. 트리에서 미분류 너레 폴더명도 inline contentEditable.
- **편집 중 데이터 보호 (핵심)**: Firebase listener 아 설택된 회의를 편집 중 (.mt-body / .mt-page-title focus) 이면 cloud에서 받은 그 회의 데이터는 로컬 데이터로 교체 (사용자 입력 보존). renderFn도 편집 중에는 skip → DOM 그대로 유지 → 사용자 blur 시 save 호출로 클라우드 푸시.

---

마지막 업데이트: 29단계 모바일 + 트리 검색/호버 + 미분류 이름 + 편집 데이터 보호 완료.
