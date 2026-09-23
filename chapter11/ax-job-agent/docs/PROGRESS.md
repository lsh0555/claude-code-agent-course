# 진행 상황 (PROGRESS)

> **작업을 시작할 때 가장 먼저 여는 파일입니다.**
> 작업을 끝낼 때마다 이 파일을 업데이트하고 commit 합니다.
> (에이전트에게 "PROGRESS.md 업데이트해 줘"라고 시켜도 됩니다. 단, 내용은 내가 확인합니다.)

---

## 📍 현재 위치

| 항목 | 값 |
|---|---|
| 마지막 업데이트 | 2026-09-23 |
| 현재 STEP | **STEP 03. 채용공고 페이지 접근 테스트** (시작 전) |
| 마지막으로 완료한 STEP | STEP 02. 수집 데이터 명세 |
| 마지막 사용 에이전트 | Claude Code (STEP 02 셀 작성) |
| 브랜치 | `ax-job-agent` (origin에 push 완료, 추적 설정됨) |
| 마지막 commit | `STEP 02: define data columns` (push 완료) |

---

## 👉 다음에 할 일 (여기부터 시작)

**STEP 03. 채용공고 페이지 접근 테스트**: 순서대로 진행

1. [ ] 시작 루틴 (GUIDE.md 0장): `cd` → `.venv` 활성화 → `git branch` → `git status`
2. [ ] Notebook 커널이 `ax-job-agent (.venv)`인지 확인
3. [ ] Claude Code 또는 Codex 실행 → Orchestrator가 준 STEP 03 지시문 전달
4. [ ] 추가된 셀 직접 실행 → 결과 확인
5. [ ] "실행 결과 해석" 셀 채우기 → Clear All Outputs → 저장
6. [ ] commit & push

**그다음** → STEP 04. 소량 데이터 수집

---

## ✅ STEP 체크리스트

| STEP | 제목 | 상태 | 완료일 | 에이전트 | 메모 |
|---|---|---|---|---|---|
| 00 | 환경 준비 | ✅ 완료 | 2026-09-23 | - | Fork, Clone, remote, 브랜치, .venv, 패키지 |
| 00-B | 문서 파일 | ✅ 완료 | 2026-09-23 | - | docs/ 3개 문서 |
| 01 | 개발환경 확인 | ✅ 완료 | 2026-09-23 | 직접 작성 | 첫 commit & push 완료 |
| 02 | 수집 데이터 명세 | ✅ 완료 | 2026-09-23 | Claude Code | `COLUMNS` 9개 정의 (SPEC 6장 순서) |
| 03 | 페이지 접근 테스트 | ⬜ | | | |
| 04 | 소량 데이터 수집 | ⬜ | | | |
| 05 | DataFrame 생성 | ⬜ | | | |
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

## ⚠️ 막힌 점 / 결정 필요 사항

- (없음)

---

## 📝 작업 로그 (최신이 위)

| 날짜 | STEP | 한 일 | 에이전트 |
|---|---|---|---|
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
