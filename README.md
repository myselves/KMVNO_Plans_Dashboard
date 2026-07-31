# KMVNO Plans Dashboard

핀다이렉트 vs 알뜰폰(MVNO) 경쟁사 요금제 비교 대시보드.

- **대시보드 원본**: `MVNO_요금제_비교_대시보드.html`
- **배포용 사본**: `index.html` (원본과 항상 동일 내용, Vercel 루트 `/` 서빙용)
- **갱신 주기**: 매주 금요일 13:00 KST — Claude Code 루틴(Routine)이 자동 실행
- **데이터 출처**: 각사 공식 홈페이지 (⑥·⑦ 페이지는 모요·이벤트 게시판 정보)

## 자동 갱신 파이프라인

1. 매주 금 13:00 KST에 Claude Code 루틴이 이 환경에서 새 세션을 생성
2. `mvno-dashboard-update` 스킬 절차로 15개 브랜드 요금제 데이터 재수집
3. `MVNO_요금제_비교_대시보드.html` 갱신 → `index.html` 동기화 → 기본 브랜치에 커밋·푸시
4. Vercel이 GitHub 푸시를 감지해 자동 배포

## Vercel 연결 (최초 1회 수동 설정)

1. <https://vercel.com/new> 에서 `myselves/KMVNO_Plans_Dashboard` 저장소를 Import
2. Framework Preset **Other**, Build/Output 설정은 기본값 그대로 두고 Deploy
3. 이후 기본 브랜치에 푸시될 때마다 자동으로 Production 배포됨

## 실행 환경 요구사항 (Claude Code 환경 설정)

예약 실행 세션이 요금제 데이터를 수집하려면 이 저장소가 연결된 Claude Code
환경의 **네트워크 정책이 아래 도메인에 대한 아웃바운드 HTTPS를 허용**해야 한다
(Full network access 권장 — 공홈들이 JS 렌더링 중 별도 CDN/API 서브도메인을
호출하므로 도메인 단위 허용 목록으로는 누락이 생기기 쉽다):

api.moyoplan.com · www.moyoplan.com · z-api.pindirectshop.com ·
www.pindirectshop.com · www.mobing.co.kr · www.tplusmobile.com ·
www.eyes.co.kr · www.freet.co.kr · www.eyagi.co.kr · tossmobile.co.kr ·
m.liivm.com · www.sk7mobile.com · www.ktmmobile.com · www.uplusumobile.com ·
shakemobile.co.kr · www.siwolmobile.com · amobile.co.kr · www.s1mobile.co.kr ·
www.smartel.kr

네트워크가 차단된 상태에서 루틴이 실행되면 데이터는 갱신하지 않고 실패 보고만
남긴다 (기존 대시보드는 보존됨).
