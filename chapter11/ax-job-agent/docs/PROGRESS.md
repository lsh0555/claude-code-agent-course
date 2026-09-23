# 진행 상황 (PROGRESS)

> **작업을 시작할 때 가장 먼저 여는 파일입니다.**
> 작업을 끝낼 때마다 이 파일을 업데이트하고 commit 합니다.
> (에이전트에게 "PROGRESS.md 업데이트해 줘"라고 시켜도 됩니다. 단, 내용은 내가 확인합니다.)

---

## 📍 현재 위치

| 항목 | 값 |
|---|---|
| 마지막 업데이트 | 2026-09-23 |
| 현재 STEP | **STEP 06. 전처리 / 중복 제거** (시작 전) |
| 마지막으로 완료한 STEP | STEP 05. DataFrame 생성 (`df` 10행 × 9열) |
| 마지막 사용 에이전트 | Claude Code |
| 브랜치 | `ax-job-agent` |
| 마지막 commit | `STEP 05: create DataFrame` |

---

## 👉 다음에 할 일 (여기부터 시작)

**STEP 06. 전처리 / 중복 제거**: 순서대로 진행

1. [ ] 시작 루틴 (GUIDE.md 0장) + 커널 `ax-job-agent (.venv)` 확인
2. [ ] **커널을 재시작했다면**: STEP 02 `COLUMNS` 셀 → 04-1 경로 셀(저장 셀은 건너뛰기) → 04-3 추출 셀 → STEP 05 `df` 셀 순서로 다시 실행 (STEP 03 요청 셀은 실행하지 않기)
3. [ ] Orchestrator에게 STEP 06 지시문 받기 → Claude Code에 전달
4. [ ] 빈 값·중복 확인, 날짜를 날짜형으로 변환, 마감일 2070-01-01 → "상시채용" 표시
5. [ ] 결과 해석 → Clear All Outputs → commit & push

**그다음** → STEP 07. 신규 공고 판별

---

## ✅ STEP 체크리스트

| STEP | 제목 | 상태 | 완료일 | 에이전트 | 메모 |
|---|---|---|---|---|---|
| 00 | 환경 준비 | ✅ 완료 | 2026-09-23 | - | Fork, Clone, remote, 브랜치, .venv, 패키지 |
| 00-B | 문서 파일 | ✅ 완료 | 2026-09-23 | - | docs/ 3개 문서 |
| 01 | 개발환경 확인 | ✅ 완료 | 2026-09-23 | 직접 작성 | 첫 commit & push 완료 |
| 02 | 수집 데이터 명세 | ✅ 완료 | 2026-09-23 | Claude Code | `COLUMNS` 9개 정의 (SPEC 6장 순서) |
| 03 | 페이지 접근 테스트 | ✅ 완료 | 2026-09-23 | Claude Code | 200 OK, text/html, 340,027자, 'LLM' 191회 |
| 04 | 소량 데이터 수집 | ✅ 완료 | 2026-09-23 | Claude Code | 10건 추출(건너뜀 0), `extract_job()` 함수 |
| 05 | DataFrame 생성 | ✅ 완료 | 2026-09-23 | Claude Code | (10, 9), 빈 값 0, 모든 컬럼 str(글자) |
| 06 | 전처리 / 중복 제거 | ⬜ | | | |
| 07 | 신규 공고 판별 | ⬜ | | | |
| 08 | 기본 분석 / 관련 공고 필터 | ⬜ | | | |
| 09 | Gemini API 연동 | ⬜ | | | |
| 10 | Gemini 결과 검증 | ⬜ | | | |
| 11 | Markdown 보고서 | ⬜ | | | |
| 12 | Slack 발송 | ⬜ | | | |
| 13 | Gmail 발송 | ⬜ | | | |
| 14 | 함수화 | ⬜ | | | |
| 15 | main.py 통합 | ⬜ | | | |
| 16 | 로컬 전체 실행 검증 | ⬜ | | | |
| 17 | GitHub Actions 수동 실행 | ⬜ | | | |
| 18 | GitHub Actions 주간 실행 | ⬜ | | | |

상태 표시: ⬜ 시작 전 / 🔄 진행 중 / ✅ 완료 / ⚠️ 막힘

---

## 🧰 현재 환경 상태

| 항목 | 상태 |
|---|---|
| Fork | ✅ `github.com/lsh0555/claude-code-agent-course` |
| origin | ✅ lsh0555 (내 Fork, push 하는 곳) |
| upstream | ✅ GilbertMoon (강의 원본, 가져오기만) |
| 브랜치 | ✅ `ax-job-agent` |
| 가상환경 | ✅ `.venv` |
| 설치 패키지 | ✅ pandas 3.0.6, requests 2.34.2, beautifulsoup4, jupyter, python-dotenv 1.2.3 |
| `.gitignore` | ✅ `.venv/`, `.env`, `__pycache__/`, `.ipynb_checkpoints/` |
| Notebook 커널 | ✅ `ax-job-agent (.venv)` (Jupyter Kernel로 등록) |
| `.env` | ⬜ 아직 없음 (STEP 09에서 생성) |
| Gemini / Slack / Gmail 키 | ⬜ 아직 없음 |

---

## 🔎 잡코리아 HTML 구조 메모 (04-2 이후 참고)

- 한 페이지에 공고 **20건**, 전체 검색 결과 약 1,283건
- 공고 링크: `https://www.jobkorea.co.kr/Recruit/GI_Read/{공고번호}?...` (한 공고에 로고·제목·회사명 링크 3개)
- 카드 화면 글자: 제목 / 회사명 / 지역 / 직무 / 지원방식 / 경력 (등록일·마감일은 화면 글자에 없음)
- 주의: 복리후생 목록이 붙은 카드는 **경력이 맨 마지막 글자가 아님** / 회사명 옆에 그룹명(예: 현대자동차그룹)이 붙기도 함 / 지역이 `서울 송파구 외 3`처럼 나오기도 함
- 날짜는 HTML 안에 숨어 있는 JSON 데이터(`"content":[{"id": ...}]`)에 있음: `createdAt`(등록일), `applicationPeriod.end`(마감일)

## ⚠️ 막힌 점 / 결정 필요 사항

- **마감일 `2070-01-01`** = 잡코리아가 상시채용(채용 시 마감)을 표시하는 방식으로 보임 (04-3에서 2건: 솔트룩스 2건) → **STEP 06 전처리에서 "상시채용"으로 처리** 예정

## 💡 작업 중 주의사항 (지금까지 배운 것)

- **새 터미널은 바깥 폴더에서 열림** → 항상 `cd ...\ax-job-agent` → `.\.venv\Scripts\Activate.ps1` 먼저
- **Notebook 커널**은 `ax-job-agent (.venv)` 선택 (Select Kernel → Jupyter Kernel)
- **Claude Code 로그인**: 조직(Teamsparta) 계정은 막혀 있음 → 개인/Team 계정으로 `/login`
- **Claude Code 입력칸의 흐린 글씨는 자동 제안** → Enter 누르지 말고 지운 뒤 직접 입력
- **에이전트가 Notebook을 수정하면 VS Code 화면과 충돌 가능** → "저장할까요?" 창에서 **Don't Save**, 파일 다시 열기
- 에이전트에게 셀 실행·설치·commit은 시키지 않음 → 직접 함
- **요청 셀은 여러 번 실행하지 않음** (실행할 때마다 사이트에 요청 1번)
- VS Code에 `PROGRESS.md`가 열려 있으면 옛날 내용으로 덮어쓸 수 있음 → 저장 전 확인
- commit은 `git add 파일이름`으로 필요한 파일만 (`../../.vscode/`는 올리지 않음)

---

## 📝 작업 로그 (최신이 위)

| 날짜 | STEP | 한 일 | 에이전트 |
|---|---|---|---|
| 2026-09-23 | 05 | `df = pd.DataFrame(rows, columns=COLUMNS)` → shape (10, 9), 빈 값 없음, 날짜도 글자(str) 상태 | Claude Code / 직접 |
| 2026-09-23 | 04 | 결과 해석 작성, commit & push (`STEP 04: extract 10 jobs`) | 직접 |
| 2026-09-23 | 04-3 | `extract_job()` 함수로 공고 10건 추출, 건너뜀 0건. 마감일 2070-01-01 2건 발견 | Claude Code |
| 2026-09-23 | 04-2 | 공고 1건(NHN, 49941022) 9개 컬럼 추출 → 브라우저와 비교 일치 | Claude Code / 직접 |
| 2026-09-23 | 04-1 | `.gitignore`에 `data/raw/` 추가 (원본 HTML은 Git에 올리지 않음) | Orchestrator(Claude) |
| 2026-09-23 | 04-1 | 검색 결과 HTML을 `data/raw/jobkorea_LLM_page1.html`로 저장 (362.8KB) | Claude Code |
| 2026-09-23 | 03 | commit & push (`STEP 03: page access test`) | 직접 |
| 2026-09-23 | 03 | 검색 페이지 1회 요청 성공 (200, text/html, 340,027자, 'LLM' 191회), 결과 해석 작성 | 직접 |
| 2026-09-23 | 03 | robots.txt 확인: `User-agent: *`에서 /Search/ 허용, AI 학습 크롤러는 전체 차단 | 직접 |
| 2026-09-23 | 03 | STEP 03 셀 5개 추가 (계획, 설정, 요청 1회, 확인, 해석) | Claude Code |
| 2026-09-23 | 02 | 셀 실행 성공 (`COLUMNS` 9개 확인), 결과 해석 작성, commit & push | 직접 / Claude Code |
| 2026-09-23 | 02 | Notebook에 STEP 02 셀 4개 추가 (명세 표, `COLUMNS`, 확인 출력, 결과 해석) | Claude Code |
| 2026-09-23 | 01 | 첫 commit & `git push -u origin ax-job-agent` 성공 | - |
| 2026-09-23 | 01 | 환경 확인 셀 실행 성공 (Python 3.14.6, .venv, pandas 3.0.6, requests 2.34.2, bs4 OK) | 직접 |
| 2026-09-23 | 01 | pandas import 오류 → `pip install --force-reinstall --no-cache-dir pandas numpy` + 커널 Restart로 해결 | - |
| 2026-09-23 | 01 | `python -m ipykernel install --user --name ax-job-agent` 로 .venv 커널 등록 | - |
| 2026-09-23 | 01 | notebooks/ax_job_pipeline.ipynb 생성 | 직접 |
| 2026-09-23 | 00-B | 루트의 빈 .gitignore 삭제, git status로 .venv 제외 확인 | - |
| 2026-09-23 | 00-B | docs/ 폴더에 SPEC.md, GUIDE.md, PROGRESS.md 생성 | Orchestrator(Claude) |
| 2026-09-23 | 00 | 원본 Clone 후 Fork, remote를 origin(lsh0555)/upstream(GilbertMoon)으로 재설정 | - |
| 2026-09-23 | 00 | ax-job-agent 브랜치, chapter11/ax-job-agent 폴더, .venv, 패키지 설치, .gitignore | - |
