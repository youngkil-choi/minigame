# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 프로젝트 개요

브라우저에서 바로 즐기는 간단한 게임 5종 모음("미니 아케이드"). **빌드 시스템·의존성·서버가 전혀 없는** 순수 정적 사이트로, 각 게임은 단일 HTML 파일 하나에 CSS·JS가 전부 인라인되어 독립 실행됩니다. GitHub Pages로 배포됩니다.

## 실행 및 검증

- **실행**: HTML 파일을 브라우저로 직접 엽니다. `index.html`이 홈(게임 선택 허브). 서버 불필요 — `file://` 더블클릭만으로 완전히 동작해야 합니다(외부 CDN/네트워크 의존 금지).
- **배포본**: https://youngkil-choi.github.io/minigame/
- **문법 검증**: 테스트 프레임워크는 없습니다. 이 환경의 Git Bash에는 `jq`가 없으므로 **`node`로 검증**하세요. 인라인 `<script>`를 추출해 파싱 검사하는 방식(대화 이력의 `vm.Script` 스니펫 참고)과, `node -e "require('./.claude/settings.json')"` 식의 JSON 검증을 사용합니다.

## 아키텍처 (큰 그림)

- **허브 + 개별 파일 구조**: `index.html`(홈 갤러리)에서 각 게임 파일(`minesweeper.html`, `2048.html`, `snake.html`, `memory.html`, `tetris.html`)로 링크. SPA가 아니라 페이지 이동 방식.
- **공유 디자인 시스템, 의도적 중복**: 모든 파일이 동일한 `:root` CSS 변수(다크 배경 + 앰버 액센트 `#ffcc4d`, `--mono`/`--sans` 폰트, 숫자색 `--c1`~`--c8`)와 좌상단 `.home-nav`(`← 게임 모음`, `href="index.html"`)를 씁니다. 각 게임이 독립 실행 가능해야 하므로 이 CSS 블록은 **파일마다 복제**되어 있습니다 — 공용 파일로 추출하지 마세요. 한 게임의 색/톤을 바꿀 때 사이트 전체 일관성을 유지하려면 다른 파일도 함께 맞춰야 합니다.
- **디자인 원본**: `minesweeper-site.html`(지뢰찾기 소개 랜딩 페이지)이 디자인 시스템의 기준점. 히어로 캔버스 애니메이션·스탯 스트립·카드 그리드 패턴이 여기서 왔고 `index.html`이 이를 재사용합니다.
- **게임 코드 관례**: 각 게임 스크립트는 `초기화(init/reset) → 상태 변수 → 이벤트 바인딩 → 렌더(draw/render)` 로 분리됩니다(원형은 `minesweeper.html`). 캔버스 게임(snake, tetris)은 `requestAnimationFrame` 루프 + 누적 시간(accumulator)으로 틱을 돌립니다. 점수/기록은 `localStorage`(키 접두사 `mini*`)에 저장합니다. 모든 게임은 홈 복귀 내비·리셋·결과 오버레이·조작 안내(hint)·모바일 터치(스와이프/D-패드)·`prefers-reduced-motion` 대응을 갖춥니다.

## 새 게임 추가하기

1. `<game>.html`을 **자체 완결형 단일 파일**로 작성 — 공유 `:root` 변수 블록과 `.home-nav`를 복사해 톤을 맞춥니다.
2. `index.html`의 `.gallery`에 게임 카드(`<a class="game-card" href="<game>.html">`) 한 장 추가. 기존 "더 많은 게임" 자리 카드를 실제 게임으로 대체하는 자리로 활용하세요.
3. `README.md`의 게임 목록 표에 한 줄 추가.

## 배포 및 자동 커밋 (중요)

- 이 폴더는 상위 `CLAUDE` 폴더의 비공개 저장소(`youngkil-choi/workgroup`)에서 관리되고, 상위의 **Stop 훅**이 턴 종료 시 자동 커밋·푸시합니다. **별도 커밋을 수동으로 만들 필요는 보통 없습니다.**
- 같은 훅이 `main`에서 이 폴더가 바뀐 것을 감지하면 이 폴더만 떼어 **공개 저장소 `youngkil-choi/minigame`의 `main`으로 배포**합니다(GitHub Pages, 약 1분 내 반영). 공개 저장소는 배포 전용이니 직접 수정하지 마세요 — 매번 덮어씌워집니다.
- 이 폴더의 모든 파일(이 CLAUDE.md 포함)은 **공개**됩니다. 민감정보를 넣지 마세요.
