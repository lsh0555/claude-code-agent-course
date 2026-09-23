# AX 채용정보 Agent Pipeline — 전체 사양 (SPEC)

> 이 문서는 "무엇을 만드는가"를 정의합니다.
> "어떤 순서로 만드는가"는 `GUIDE.md`, "지금 어디까지 했는가"는 `PROGRESS.md`를 봅니다.

---

## 1. 한 줄 목표

잡코리아의 AX / AI / 데이터 관련 채용공고를 **매주 1회 자동으로 수집 → 정리 → 분석 → AI 요약**하고,
주간 보고서를 **Slack과 Gmail**로 받아 보는 작은 Agent Pipeline을 만든다.

---

## 2. 작업 환경

| 항목 | 내용 |
|---|---|
| OS | Windows |
| 편집기 | VS Code (주로 Jupyter Notebook `.ipynb` 사용) |
| 언어 | Python (프로젝트 전용 가상환경 `.venv`) |
| 저장소 | `lsh0555/claude-code-agent-course` (GilbertMoon 원본의 Fork) |
| 브랜치 | `ax-job-agent` |
| 프로젝트 루트 | `C:\dev\claude-code-agent-course\chapter11\ax-job-agent` |
| 작업자 수준 | Python 데이터 분석 초보자 |

---

## 3. 역할 분담

| 역할 | 담당 | 하는 일 |
|---|---|---|
| Orchestrator (기획자) | Claude (웹/앱) | 전체 계획, STEP 분할, 완료 조건 정의, 에이전트용 지시문 작성, 결과 보고 다음 단계 결정 |
| Coding Agent (개발자) | **Claude Code** 또는 **Codex** (토큰 사용량에 따라 교대) | 파일 읽기/작성, 코드 작성, 명령 실행, 오류 수정 |
| 운영 AI | Gemini API | 완성된 파이프라인 안에서 공고 요약·기술 추출·추천 이유 작성 |
| Scheduler | GitHub Actions | 매주 1회 `python main.py` 실행 |
| Human (최종 승인자) | 나 | 셀 직접 실행, 결과 확인, 다음 단계 승인 |

### 에이전트 교대 규칙
- 두 에이전트 모두 같은 규칙을 따르도록 `CLAUDE.md`(Claude Code용)와 `AGENTS.md`(Codex용)를 **같은 내용**으로 둔다.
- 에이전트를 바꿀 때는 먼저 `docs/PROGRESS.md`를 읽게 한다.
- STEP이 끝날 때마다 commit 한다. (문제가 생기면 되돌리기 쉽게)

---

## 4. 전체 구조

### 4-1. 개발 단계 (지금 하는 것)
```
Claude (Orchestrator) → 지시문
        ↓
Claude Code / Codex → Notebook 셀 작성
        ↓
나: 셀 직접 실행 → 결과 확인 → Markdown으로 해석
        ↓
다음 STEP 결정
```

### 4-2. 운영 단계 (완성 후)
```
매주 월요일 09:00 (KST)
   ↓
GitHub Actions
   ↓
main.py
   ↓
Crawler → pandas(정제·중복제거·신규판별·필터·통계) → Gemini API(요약) → Report
   ↓
Slack / Gmail
```

---

## 5. 기능 요구사항

| 번호 | 기능 | 담당 | 완료 기준 |
|---|---|---|---|
| F1 | 채용공고 수집 | Crawler (requests + BeautifulSoup) | 수집 건수 > 0, 필수 컬럼 존재 |
| F2 | 정제 | pandas | 결측·날짜 변환 실패 확인 완료 |
| F3 | 중복 제거 | pandas | `job_url` 기준 중복 0건 |
| F4 | 신규 공고 판별 | pandas + `jobs_history.csv` | 지난번에 본 URL은 제외됨 |
| F5 | 기본 통계 | pandas | 신규 수, 회사별·지역별·경력별·검색어별 집계 |
| F6 | 관련 공고 필터 | pandas | AX 키워드 기준으로 추려짐 |
| F7 | AI 요약 | Gemini API | 원문과 비교 검증 통과 |
| F8 | 주간 보고서 | reporter | `reports/` 에 Markdown 파일 생성 |
| F9 | Slack 발송 | notifier | 실제 채널에 도착, 한글 정상 |
| F10 | Gmail 발송 | notifier | 실제 메일함에 도착 |
| F11 | 자동 실행 | GitHub Actions | 수동 실행 성공 후 주 1회 스케줄 |

### 검색 키워드 (초기값)
`AX`, `AI`, `인공지능`, `데이터 분석`, `생성형 AI`, `LLM`, `Machine Learning`, `Data Scientist`, `AI Engineer`
> 처음에는 **1개 키워드만** 사용하고, 검증 후 늘린다.

---

## 6. 데이터 명세

**DataFrame의 한 행 = 채용공고 한 건**

| 컬럼 | 의미 | 예시 |
|---|---|---|
| `company_name` | 회사명 | ㈜에이아이랩 |
| `job_title` | 공고 제목 | 생성형 AI 엔지니어 채용 |
| `career` | 경력 조건 | 신입·경력 3년↑ |
| `location` | 근무 지역 | 서울 강남구 |
| `posted_date` | 등록일 | 2026-09-20 |
| `closing_date` | 마감일 | 2026-10-10 / 상시채용 |
| `job_url` | 공고 URL (**고유 키**) | https://www.jobkorea.co.kr/... |
| `search_keyword` | 어떤 검색어로 찾았는지 | LLM |
| `collected_at` | 수집 시각 | 2026-09-23 10:00 |

- 신규 판별 기본 키: `job_url`
- 보조 키(필요 시): `company_name + job_title + closing_date`
- 이력 저장: `data/processed/jobs_history.csv` (DB 사용 안 함)

---

## 7. pandas와 Gemini의 경계 (중요)

| pandas가 계산 (사실) | Gemini가 작성 (해석) |
|---|---|
| 신규 공고 수 | 공고 핵심 요약 |
| 회사별 / 지역별 공고 수 | 요구 기술 추출 |
| 경력 조건 분포 | 직무 유형 분류 |
| 검색어별 발견 건수 | AX 관련성 설명 |
| 주요 키워드 빈도 | 추천 이유 |

> 규칙: **계산할 수 있는 숫자는 절대 Gemini에게 묻지 않는다.**
> 보고서에서도 "pandas가 만든 부분"과 "Gemini가 만든 부분"을 코드상 분리한다.

---

## 8. 최종 폴더 구조

```
chapter11/ax-job-agent/
├── docs/
│   ├── SPEC.md          ← 이 문서 (무엇을)
│   ├── GUIDE.md         ← 진행 순서 (어떻게)
│   └── PROGRESS.md      ← 진행 상황 (어디까지)
├── notebooks/
│   └── ax_job_pipeline.ipynb
├── src/
│   ├── crawler.py
│   ├── preprocess.py
│   ├── analyzer.py
│   ├── gemini_client.py
│   ├── reporter.py
│   └── notifier.py
├── data/
│   ├── raw/
│   └── processed/
│       └── jobs_history.csv
├── reports/
├── .env                 ← 실제 키 (Git에 올리지 않음)
├── .env.example         ← 빈 템플릿 (Git에 올림)
├── .gitignore
├── CLAUDE.md            ← Claude Code 규칙
├── AGENTS.md            ← Codex 규칙 (CLAUDE.md와 같은 내용)
├── main.py
├── requirements.txt
└── README.md
```
> 모든 파일을 처음부터 만들지 않는다. 필요한 STEP에서 하나씩 추가한다.

---

## 9. 보안 규칙

- API Key·비밀번호·Webhook URL은 **코드에 직접 쓰지 않는다.**
- 로컬: `.env` / GitHub: **Repository Secrets**
- `.gitignore` 필수 항목: `.venv/`, `.env`, `__pycache__/`, `.ipynb_checkpoints/`
- commit 전: Notebook **Clear All Outputs** → `git status`로 `.env`가 없는지 확인

`.env.example`
```
GEMINI_API_KEY=
SLACK_WEBHOOK_URL=
GMAIL_USER=
GMAIL_APP_PASSWORD=
```

---

## 10. 크롤링 윤리 / 대체 경로

- 요청 전 확인: 이용약관, `robots.txt`, 요청 간격(과도한 요청 금지), 상태 코드, 페이지 구조
- 자동 수집이 막히거나 불안정하면 **우회하지 않는다.**
  → 저장된 샘플 HTML/CSV로 이후 파이프라인을 계속 진행한다.
- 이 프로젝트의 목표는 크롤링 기술이 아니라 **파이프라인 완성**이다.

---

## 11. 하지 않는 것 (범위 밖)

- 복잡한 Agent Framework, 별도 서버, 데이터베이스(SQLite 등)
- 한 번에 전체 프로그램 생성
- 로컬 검증 전 GitHub Actions 추가
- 사이트 차단 우회

---

## 12. 최종 완료 조건 (Definition of Done)

- [ ] 로컬에서 `python main.py`가 끝까지 성공한다
- [ ] 수집 건수 > 0, 필수 컬럼 모두 존재
- [ ] 신규 공고 판별이 정상 (두 번 실행하면 두 번째 신규 수가 줄어든다)
- [ ] Gemini 결과가 원문과 크게 다르지 않다 (최소 3건 비교)
- [ ] Markdown 보고서가 `reports/`에 생성된다
- [ ] Slack·Gmail에 실제로 도착한다
- [ ] 오류가 나면 성공처럼 보이지 않는다 (로그에 실패 기록)
- [ ] GitHub Actions 수동 실행 성공 → 주 1회 스케줄 동작
- [ ] Git 저장소에 비밀정보가 없다
