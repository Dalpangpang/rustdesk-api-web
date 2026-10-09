# remote-ops 회사 변경 (rustdesk-api-web 포크)

원본: https://github.com/lejianwen/rustdesk-api-web (MIT). 원본 `LICENSE` 와 저작권 표시는 그대로 둔다.
원본 병합은 상위 저장소의 `/upstream-sync` 절차를 따르고, 병합 뒤 아래 변경을 다시 얹는다.

## 원칙

- 기능은 바꾸지 않는다. 보이는 모양과 문구만 바꾼다. 화면 주소·메뉴·서버 API 호출·권한·입력 항목·화면 동작 코드(script)는 그대로다.
- 원본 파일 수정을 줄이려고 공통 모양은 `src/styles/remote-ops/`(테마 변수·공통 클래스)에서 맞춘다.
  원본 파일은 속성·스타일 수준으로만 고치고, 스크립트에는 `// remote-ops:` 주석을 남긴다.
- 브랜딩은 에셋 교체(`src/assets/logo.png`, `public/favicon.ico`)와 서버 설정(`RUSTDESK_API_ADMIN_TITLE`,
  환영 문구 `conf/admin/hello.html`)으로 한다. 컴포넌트 구조는 바꾸지 않는다.
- 새 문구는 번역 키로 7개 언어 모두에 넣는다(회사 키는 `Ro` 로 시작). 컴포넌트에 한국어를 직접 쓰지 않는다.

## 바꾼 곳

| 구분 | 파일 | 내용 |
| --- | --- | --- |
| 새 파일 | `src/styles/remote-ops/index.scss` | 색·글꼴 토큰, Element Plus 변수(밝게·어둡게), 목록 화면 틀(필터·표·페이지 이동을 한 카드로, 필터 라벨은 입력란 위), 버튼 위계, 표, 휴대폰 |
| 새 파일 | `src/styles/remote-ops/fonts.js`, `public/fonts-OFL.txt` | IBM Plex Sans KR·Mono(SIL OFL 1.1)와 라이선스 고지 |
| 의존성 | `package.json`, `package-lock.json` | `@fontsource/ibm-plex-sans-kr`, `@fontsource/ibm-plex-mono` |
| 에셋 | `src/assets/logo.png`, `public/favicon.ico`, `index.html` | remote-ops 표시. 파비콘 링크를 `./favicon.ico` 로(원본은 `/favicon.ico` 라 404) |
| 시작 | `src/main.js` | 테마·글꼴 불러오기 2줄 |
| 레이아웃 | `src/layout/index.vue`, `components/header.vue`, `aside.vue`, `menu/index.vue`, `tags/index.vue`, `setting/index.vue` | 밝은 사이드바·상단바, 현재 위치(메뉴 묶음 / 화면), 화면 제목, 둥근 탭, 좁은 화면(768px 이하)에서 서랍 메뉴 |
| 화면 | `views/login/login.vue` | 스타일만 |
| 화면 | `views/my/info.vue` | 환영 문구를 위쪽 띠로(비어 있으면 숨김), 비밀번호 변경은 보조 버튼 |
| 화면 | `views/user/edit.vue` | 폼 폭 제한, 스위치 상태 글자(관리자·일반 사용자, 사용·중지) |
| 화면 | `views/rustdesk/*.vue`(6개) | 서버 명령 카드 제목을 번역 키 + 원래 설정 키로 |
| 화면 24개 | `views/**` | 버튼 `type` 속성만: 추가 = 주 버튼, 필터·내보내기·가져오기·일괄 추가 = 보조 |
| 번역 | `src/utils/i18n/*.json` | 한국어 누락 11개·어색한 번역 5개(내 → 내 항목, 링크 → 연결, 제출 → 저장, 생성 → 추가, 업데이트 → 수정), `Ro*` 키 |
| (2026-10-08) | `views/**`, 번역 | 고정 중국어 문구를 번역 키로 |

## 시험

상위 저장소 `tools/admin-ui-check/` 로 개편 전후를 비교한다. 운영과 분리된 임시 API(빈 DB)에서 화면 22개의
서버 API 호출·입력 항목·버튼·표 열·추가 대화상자, 다크 모드·7개 언어·로그아웃·휴대폰 메뉴를 기록해 비교한다.
2026-10-09 개편: 일치 320건, 다름 0건, 오류 0건, 서버 API 호출 수 전후 75건으로 같음.
