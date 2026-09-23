# AX 채용정보 Agent Pipeline — 진행 순서별 가이드 (GUIDE)

> 이 문서는 "어떤 순서로, 어떻게" 만드는지 설명합니다.
> **작업을 시작할 때는 항상 `PROGRESS.md`부터 열어** 현재 위치를 확인한 뒤, 이 문서의 해당 STEP으로 옵니다.

---

## 0. 매번 작업 시작할 때 하는 일 (5분 루틴)

VS Code 터미널에서 순서대로 실행합니다.

```powershell
# 1) 프로젝트 폴더로 이동
cd C:\dev\claude-code-agent-course\chapter11\ax-job-agent

# 2) 가상환경 켜기  → 맨 앞에 (.venv) 가 붙어야 함
.\.venv\Scripts\Activate.ps1

# 3) 브랜치 확인  → * ax-job-agent 이어야 함
git branch

# 4) 저장 안 한 변경이 있는지 확인
git status
```

그다음:

1. `docs/PROGRESS.md`를 열어 **"👉 다음에 할 일"** 칸을 읽는다.
2. 이 문서(GUIDE.md)에서 그 STEP을 찾는다.
3. Orchestrator(Claude)에게 "STEP XX 시작" 이라고 말하고 지시문을 받는다.
4. 사용할 에이전트(Claude Code 또는 Codex)를 켜고 **첫 문장**으로 아래를 붙여넣는다.

```
먼저 docs/PROGRESS.md 와 docs/SPEC.md 를 읽고, 현재 진행 상황을 3줄로 요약해 주세요.
아직 코드는 작성하지 마세요.
```

5. 요약이 PROGRESS.md 내용과 맞으면 → 이번 STEP 지시문을 전달한다.

## 0-1. 작업을 끝낼 때 하는 일

1. Notebook 상단 **… → Clear All Outputs** (비밀정보 노출 방지)
2. `docs/PROGRESS.md` 업데이트 (완료 체크, 다음 할 일, 작업 로그 한 줄)
3. commit

```powershell
git status              # .env 가 목록에 없는지 확인!
git add .
git commit -m "STEP XX: 한 일 요약"
git push                # 첫 push는 git push -u origin ax-job-agent
```

---

## 1. 모든 STEP의 공통 흐름

```
작업 계획 (Markdown 셀)
   ↓
코드 작성 (에이전트)
   ↓
셀 직접 실행 (나)
   ↓
결과 확인 (shape, head, 상태 코드 등)
   ↓
결과 해석 (Markdown 셀)
   ↓
이상 없으면 → 다음 STEP / 이상 있으면 → 멈추고 Orchestrator에 결과 공유
```

### Notebook 셀 패턴 (STEP마다 4개 셀)

```markdown
# STEP XX. 제목
## 작업 계획
이번 단계에서 할 일:
확인 항목:
이번에 하지 않는 것:
```
```python
# 실행 셀: 이번 STEP에 필요한 최소 코드
```
```python
# 확인 셀: 사람이 눈으로 볼 수 있는 출력 (print, display, shape 등)
```
```markdown
## 실행 결과 해석
- 성공 여부:
- 확인한 데이터:
- 예상과 다른 부분:
- 다음 단계 진행 가능 여부:
- 추가 확인 사항:
```

### 에이전트에게 지시할 때의 기본 틀

```
[현재 STEP] STEP XX. 제목
[읽을 파일] docs/PROGRESS.md, docs/SPEC.md
[할 일] (한두 가지만)
[하지 말 것] 다음 STEP 작업, 요청하지 않은 패키지 설치, 기존 셀 삭제
[완료 조건] (눈으로 확인 가능한 조건)
[마지막에] 변경한 파일 목록과 내가 실행해야 할 셀 번호를 알려 주세요. 셀은 직접 실행하지 마세요.
```

---

## 2. 전체 STEP 한눈에 보기

| 구간 | STEP | 제목 | 주요 결과물 |
|---|---|---|---|
| 준비 | 00 | 환경 준비 (Fork~가상환경) | `.venv`, `.gitignore` |
| 준비 | 00-B | 문서 파일 | `docs/` |
| 개발 | 01 | 개발환경 확인 | Notebook 뼈대 |
| 수집 | 02 | 수집 데이터 명세 | 컬럼 정의 셀 |
| 수집 | 03 | 페이지 접근 테스트 | 상태 코드 확인 |
| 수집 | 04 | 소량 데이터 수집 | 공고 5~10건 |
| 정제 | 05 | DataFrame 생성 | `df` |
| 정제 | 06 | 전처리 / 중복 제거 | `clean_df` |
| 정제 | 07 | 신규 공고 판별 | `jobs_history.csv` |
| 분석 | 08 | 기본 분석 / 관련 공고 필터 | 통계 표 |
| AI | 09 | Gemini API 연동 | 요약 결과 |
| AI | 10 | Gemini 결과 검증 | 검증 기록 |
| 보고 | 11 | Markdown 보고서 | `reports/*.md` |
| 보고 | 12 | Slack 발송 | Slack 메시지 |
| 보고 | 13 | Gmail 발송 | 메일 |
| 통합 | 14 | 함수화 | `src/*.py` |
| 통합 | 15 | main.py 통합 | `main.py` |
| 통합 | 16 | 로컬 전체 실행 검증 | 실행 로그 |
| 자동화 | 17 | GitHub Actions 수동 실행 | workflow 파일 |
| 자동화 | 18 | GitHub Actions 주간 실행 | schedule 추가 |

---

## 3. STEP별 상세 가이드

### STEP 00. 환경 준비 ✅ (완료)
- Fork → Clone → `origin`/`upstream` 설정 → `ax-job-agent` 브랜치 → 폴더 생성 → `.venv` → 패키지 설치 → `.gitignore`
- 확인 명령
  ```powershell
  git remote -v      # origin=lsh0555, upstream=GilbertMoon
  git branch         # * ax-job-agent
  python --version
  ```

### STEP 00-B. 문서 파일
- **목적**: 어느 에이전트를 쓰든, 언제 다시 시작하든 현재 위치를 알 수 있게 한다.
- **할 일**
  1. `docs/SPEC.md`, `docs/GUIDE.md`, `docs/PROGRESS.md` 확인
  2. VS Code에서 인터프리터를 `.venv`로 선택 (`Ctrl+Shift+P` → Python: Select Interpreter)
- **완료 조건**: 첫 commit & push 성공

### STEP 01. 개발환경 확인
- **목적**: Notebook이 올바른 Python(.venv)으로 실행되는지 확인.
- **할 일**: `notebooks/ax_job_pipeline.ipynb` 생성. 환경 확인 셀만 작성.
  ```python
  import sys, platform
  print("Python:", sys.version)
  print("실행 위치:", sys.executable)   # .venv 경로가 나와야 함
  print("Platform:", platform.platform())
  ```
  ```python
  import pandas as pd, requests
  from bs4 import BeautifulSoup
  print("pandas:", pd.__version__)
  print("requests:", requests.__version__)
  print("BeautifulSoup import: OK")
  ```
- **내가 확인**: Notebook 오른쪽 위 커널이 `.venv`인지 / `sys.executable`에 `.venv`가 들어있는지
- **하지 말 것**: 크롤링 코드 작성
- **완료 조건**: 모든 셀이 오류 없이 실행됨

### STEP 02. 수집 데이터 명세
- **목적**: 코드 전에 "한 행 = 무엇"인지 정한다.
- **할 일**: SPEC.md 6장의 9개 컬럼을 Markdown 셀과 Python 리스트(`COLUMNS = [...]`)로 기록.
- **초보자 포인트**: DataFrame은 엑셀 표와 같다. 행 = 공고 1건, 열 = 항목.
- **완료 조건**: `print(COLUMNS)`로 9개 컬럼 출력

### STEP 03. 채용공고 페이지 접근 테스트
- **목적**: 실제 수집 전에 요청이 가능한지만 본다.
- **할 일**
  1. 잡코리아 `robots.txt`와 이용약관 확인 → 결과를 Markdown 셀에 기록
  2. 검색어 **1개**, 페이지 **1개**만 요청
- **내가 확인**: 상태 코드(200인지), Content-Type, 응답 길이, HTML 안에 공고 제목이 보이는지
- **막힐 때**: 403/차단/빈 페이지 → 우회하지 말고 샘플 HTML/CSV 방식으로 전환 (Orchestrator에 보고)
- **완료 조건**: 요청 가능 여부가 명확히 기록됨

### STEP 04. 소량 데이터 수집
- **목적**: HTML에서 필요한 항목을 뽑아낸다.
- **순서**: 공고 1건 → 5~10건 (한 번에 늘리지 않음)
- **할 일**: BeautifulSoup으로 9개 컬럼 추출 → 파이썬 리스트(dict 목록)로 저장
- **내가 확인**: 브라우저에서 같은 공고를 열어 회사명·제목·URL이 맞는지 **직접 비교**
- **완료 조건**: 5~10건이 정확히 추출되고 `data/raw/`에 원본 저장

### STEP 05. DataFrame 생성
- **할 일**: `df = pd.DataFrame(rows)` 후 기본 확인
  ```python
  print(df.shape)        # (행 수, 열 수)
  display(df.head())     # 앞 5행
  df.info()              # 컬럼별 자료형
  ```
- **초보자 포인트**: `shape`이 `(10, 9)`면 공고 10건, 항목 9개.
- **완료 조건**: 9개 컬럼 모두 존재, 행 수 = 수집 건수

### STEP 06. 전처리 / 중복 제거
- **할 일**
  ```python
  display(df.isna().sum())                              # 빈 값 개수
  print("URL 중복:", df.duplicated(subset=["job_url"]).sum())
  ```
  - 날짜 컬럼을 날짜형으로 변환 (`pd.to_datetime`, 실패는 `NaT`로 표시)
  - "상시채용" 같은 값 처리 규칙 정하기
  - `job_url` 기준 중복 제거 → `clean_df`
- **내가 확인**: 변환 전후 행 수, 날짜 변환 실패 건수
- **완료 조건**: 중복 0건, 변환 실패 원인 설명 가능

### STEP 07. 신규 공고 판별
- **목적**: 매주 같은 공고를 다시 추천하지 않기.
- **할 일**
  - `data/processed/jobs_history.csv`가 없으면 새로 만든다 (첫 실행 = 전부 신규)
  - 이번 URL 중 history에 없는 것만 `new_df`
  - history 업데이트
- **내가 확인**: 같은 셀을 **두 번** 실행 → 두 번째는 신규 0건이어야 정상
- **완료 조건**: 위 테스트 통과

### STEP 08. 기본 분석 / 관련 공고 필터링
- **할 일** (모두 pandas로 계산)
  - 신규 공고 수, 회사별·지역별·경력별·검색어별 개수 (`value_counts()`)
  - 제목에 AX 키워드가 들어간 공고만 `related_df`로 추리기
- **내가 확인**: 표의 숫자를 한두 개 손으로 세어 보고 맞는지
- **완료 조건**: 통계 표 출력, 필터 기준을 Markdown에 기록

### STEP 09. Gemini API 연동
- **사전 준비**: Google AI Studio에서 API Key 발급 → `.env`에 `GEMINI_API_KEY=...` 저장
- **패키지 설치**: 이 STEP에서 처음 설치 (Orchestrator가 정확한 패키지명 안내)
- **할 일**
  - `.env.example` 생성 (빈 값)
  - `related_df` 중 **3건만** Gemini에 보내 요약·기술 추출·직무 분류·추천 이유 받기
- **하지 말 것**: 숫자 계산을 Gemini에 묻기, 키를 코드에 직접 쓰기, 전체 공고 한꺼번에 보내기
- **완료 조건**: 3건 응답 수신, `git status`에 `.env` 안 보임

### STEP 10. Gemini 결과 검증
- **할 일**: 3건을 원문과 비교해 Markdown 셀에 기록
  ```markdown
  ### 공고 1
  - 원문에서 확인한 기술:
  - Gemini가 추출한 기술:
  - 누락:
  - 과도한 해석:
  - 사용 가능 여부:
  ```
- **핵심**: API 호출 성공 ≠ 분석 성공
- **완료 조건**: 3건 모두 "사용 가능" 또는 프롬프트 수정 후 재검증

### STEP 11. Markdown 보고서 생성
- **할 일**: `reports/weekly_YYYY-MM-DD.md` 생성
  - 1. 이번 주 요약 (pandas 숫자) / 2. 주요 동향 / 3. 추천 공고 (Gemini) / 4. 데이터 기준 / 5. 주의사항
- **내가 확인**: VS Code에서 파일 열고 `Ctrl+Shift+V`(미리보기)로 확인
- **완료 조건**: 숫자는 pandas에서, 설명은 Gemini에서 온 것이 코드상 구분됨

### STEP 12. Slack 발송
- **사전 준비**: Slack Incoming Webhook 생성 → `.env`에 `SLACK_WEBHOOK_URL`
- **할 일**: 보고서 요약본을 Webhook으로 전송
- **내가 확인**: HTTP 상태, 실제 채널 도착, 한글 깨짐, 링크 표시, 메시지 길이
- **완료 조건**: 실제 Slack 채널에서 메시지 확인

### STEP 13. Gmail 발송
- **사전 준비**: Google 계정 2단계 인증 → 앱 비밀번호 발급 (현재 Google 정책 확인)
- **할 일**: `.env`에 `GMAIL_USER`, `GMAIL_APP_PASSWORD` → 보고서 메일 발송
- **완료 조건**: 받은편지함 도착, 한글 정상

### STEP 14. 함수화
- **목적**: Notebook 코드를 재사용 가능한 운영 코드로 옮긴다. (Notebook은 검증 기록으로 남김)
- **할 일**: `src/` 아래 파일로 이동
  | 함수 | 파일 |
  |---|---|
  | `collect_jobs()` | `crawler.py` |
  | `clean_jobs(df)` | `preprocess.py` |
  | `find_new_jobs(df, history_df)`, `analyze_jobs(df)` | `analyzer.py` |
  | `summarize_with_gemini(df)` | `gemini_client.py` |
  | `create_report(analysis, summaries)` | `reporter.py` |
  | `send_slack(report)`, `send_email(report)` | `notifier.py` |
- **내가 확인**: Notebook에서 `from src.crawler import collect_jobs`로 불러와 같은 결과인지
- **완료 조건**: 함수 버전 결과 = Notebook 결과

### STEP 15. main.py 통합
- **할 일**: `main.py`는 순서만 보여 준다. + `requirements.txt` 작성
  ```python
  def main():
      jobs = collect_jobs()
      clean = clean_jobs(jobs)
      new_jobs = find_new_jobs(clean)
      analysis = analyze_jobs(new_jobs)
      summaries = summarize_with_gemini(new_jobs)
      report = create_report(analysis, summaries)
      send_slack(report)
      send_email(report)

  if __name__ == "__main__":
      main()
  ```
- **완료 조건**: `python main.py` 실행 성공

### STEP 16. 로컬 전체 실행 검증
- SPEC.md 12장 체크리스트 전부 확인
- 실행 로그 남기기: 실행 시각 / 수집 건수 / 신규 수 / Gemini 처리 건수 / Slack·Email 성공 여부 / 오류 메시지
- 일부러 틀린 키를 넣어 **실패가 실패로 보이는지** 확인
- **완료 조건**: 체크리스트 전부 ✅ → commit & push

### STEP 17. GitHub Actions 수동 실행
- **할 일**
  1. GitHub 내 Fork → Settings → Secrets and variables → Actions 에 4개 키 등록
  2. `.github/workflows/ax-job-agent.yml` 작성 (`on: workflow_dispatch:` 만)
  3. push 후 Actions 탭 → **Run workflow**
- **실패 시 확인**: requirements.txt, 파일 경로, 환경변수, Secrets 이름, OS 차이, 쓰기 권한
- **완료 조건**: 수동 실행 성공 + Slack/Gmail 도착

### STEP 18. GitHub Actions 주간 실행
- **할 일**: workflow에 schedule 추가
  ```yaml
  on:
    workflow_dispatch:
    schedule:
      - cron: "0 0 * * 1"   # UTC 월 00:00 = 한국 월 09:00
  ```
- **참고**: 몇 분 늦게 실행될 수 있음 (정상)
- **완료 조건**: 다음 월요일 자동 실행 확인 🎉

---

## 4. 막혔을 때

1. **멈춘다.** 다음 STEP으로 넘어가지 않는다.
2. 오류 메시지 전체 + 실행한 셀/명령을 캡처한다.
3. Orchestrator(Claude)에게 공유한다.
4. 에이전트가 이상하게 많이 바꿨다면: `git diff`로 확인 → 필요하면 `git restore 파일명`으로 되돌린다.

## 5. 에이전트 교대할 때 (Claude Code ↔ Codex)

1. 현재 에이전트에게: "지금까지 한 작업을 docs/PROGRESS.md 작업 로그에 한 줄로 추가해 주세요."
2. commit
3. 새 에이전트 실행 → 0장의 "첫 문장" 붙여넣기 → 요약 확인 후 이어서 진행
