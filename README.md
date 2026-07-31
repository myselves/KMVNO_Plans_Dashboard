# KMVNO Plans Dashboard

핀다이렉트 vs 알뜰폰(MVNO) 경쟁사 요금제 비교 대시보드.

- **대시보드 원본**: `MVNO_요금제_비교_대시보드.html`
- **배포용 사본**: `index.html` (원본과 항상 동일 내용, Vercel 루트 `/` 서빙용)
- **갱신 주기**: 매주 금요일 13:00 KST — 로컬 PC의 Claude Desktop 예약 작업
- **데이터 출처**: 각사 공식 홈페이지 (⑥·⑦ 페이지는 모요·이벤트 게시판 정보)

## 갱신 파이프라인

수집 소스 17곳 중 14곳이 JS 렌더링·클릭을 요구해 실제 브라우저가 필수다.
따라서 갱신은 Claude in Chrome이 붙는 **로컬 PC 세션에서만** 수행한다.

1. 매주 금 13:00 KST에 Claude Desktop 예약 작업이 로컬 세션을 시작
2. `mvno-dashboard-update` 스킬 절차로 15개 브랜드 요금제 데이터 재수집
3. `MVNO_요금제_비교_대시보드.html` 갱신 → 검증 → `index.html` 동기화
4. 기본 브랜치에 커밋·푸시 → Vercel이 감지해 자동 배포

예약 작업에 넣을 프롬프트는 [`docs/scheduled-task-prompt.md`](docs/scheduled-task-prompt.md) 참조.

## 최초 1회 설정

### 1. 로컬 클론

```bash
git clone https://github.com/myselves/KMVNO_Plans_Dashboard.git
cd KMVNO_Plans_Dashboard
```

`mvno-dashboard-update` 스킬이 로컬 Claude Code에 설치돼 있어야 한다
(`~/.claude/skills/mvno-dashboard-update/`).

### 2. Vercel 연결

1. <https://vercel.com/new> 에서 `myselves/KMVNO_Plans_Dashboard` 저장소를 Import
2. Framework Preset **Other**, Build/Output 설정은 기본값 그대로 두고 Deploy
3. 이후 기본 브랜치에 푸시될 때마다 자동으로 Production 배포됨

### 3. Claude Desktop 예약 작업 등록

Claude Desktop 앱에서 예약 작업(Scheduled task)을 만들고:

- 실행 위치: **로컬(Local)** — 클라우드로 만들면 Chrome에 접근할 수 없다
- 주기: 매주 금요일 13:00
- 프롬프트: [`docs/scheduled-task-prompt.md`](docs/scheduled-task-prompt.md)의 내용을
  붙여넣고 저장소 경로만 실제 클론 위치로 바꾼다

## 실행 조건

예약 작업이 성공하려면 금요일 13:00에 아래가 모두 만족돼야 한다.

- 컴퓨터가 켜져 있고 절전 상태가 아닐 것
- Claude Desktop 앱이 실행 중일 것 (앱이 닫혀 있으면 작업이 발화하지 않음)
- Chrome이 실행 중이고 Claude in Chrome 확장이 연결돼 있을 것
- Claude Code가 claude.ai 계정으로 로그인돼 있을 것
  (API 키·장기 토큰으로 인증하면 Chrome 통합이 비활성화된다)
- Windows는 WSL이 아닌 네이티브 환경일 것 (WSL은 Chrome 통합 미지원)

조건이 맞지 않아 한 주를 건너뛰어도 대시보드는 직전 데이터로 유지되고,
다음 실행에서 자동으로 따라잡는다.

수집 중 CAPTCHA·로그인 페이지를 만나면 Claude가 멈추고 수동 처리를 요청한다.
자리에 없으면 해당 브랜드는 기존 값을 유지한 채 실패로 기록된다.

## 클라우드(웹) 세션에서 열었을 때

Claude Code on the web 세션은 수집 사이트가 네트워크 정책으로 차단되고
Claude in Chrome도 사용할 수 없다. 웹 세션에서는 데이터를 갱신하지 않는다.
