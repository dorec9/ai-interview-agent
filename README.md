#  AI 면접관 Agent

> LangGraph + GPT-4o-mini 기반 개인화 AI 면접 시뮬레이션 시스템

---

##  프로젝트 소개

이력서(PDF/DOCX)를 업로드하면 AI가 자동으로 이력서를 분석하고, 지원자 맞춤형 면접 질문을 생성하여 실시간으로 면접을 진행합니다. 면접이 끝나면 항목별 피드백 보고서를 제공합니다.

KT AI/DX 직무 면접을 타깃으로 설계되었으며, 사내 핵심가치 및 예상 질문 문서를 벡터 DB에 적재해 RAG 방식으로 심화 질문을 생성합니다.

---

##  주요 기능

| 기능 | 설명 |
|---|---|
| 이력서 분석 | PDF/DOCX에서 텍스트 추출 후 GPT로 핵심 요약 및 키워드 도출 |
| 질문 전략 수립 | 잠재력/조직적합도/직무역량 3개 관점의 면접관이 경험/동기/논리 3개 영역 전략 생성 |
| 실시간 면접 | 지원자 답변 입력 → 평가 → 심화질문 생성의 LangGraph 기반 에이전트 루프 |
| 답변 평가 | 구체성/일관성/적합성/논리성 4개 항목 이진 채점 (GPT Few-shot) |
| RAG 심화질문 | KT 핵심가치/예상질문 문서를 Chroma DB에 적재해 답변 맥락 반영 |
| 피드백 보고서 | 분야별 평균 점수 + GPT 종합 코멘트 |
| Gradio UI | 웹 기반 채팅 인터페이스로 실제 면접처럼 진행 |

---

##  시스템 아키텍처

```
이력서 업로드 (PDF/DOCX)
        ↓
  [사전 준비 파이프라인]
  ① 텍스트 추출 (PyMuPDF / python-docx)
  ② 이력서 분석 - GPT (요약 + 키워드)
  ③ 질문 전략 수립 - GPT (3명 면접관 × 3개 영역)
  ④ 첫 질문 선택
        ↓
  [면접 Agent - LangGraph]
  답변 입력
    → evaluate_answer (GPT 4항목 채점)
    → decide_next_step (규칙 기반 분기)
        ├─ 기준 미달 → generate_question (심화질문 + RAG)
        ├─ 분야 통과 → change_strategy (다음 영역 질문)
        └─ 마지막 분야 통과 → summarize_interview (보고서)
        ↓
  [Gradio 웹 UI]
```

---

## 🛠️ 기술 스택

- **LLM**: OpenAI GPT-4o-mini
- **오케스트레이션**: LangGraph, LangChain
- **벡터 DB**: Chroma + OpenAI Embeddings (RAG)
- **문서 처리**: PyMuPDF, python-docx, unstructured
- **UI**: Gradio
- **언어**: Python 3.10+

---

## 📂 파일 구조

```
ai-interview-agent/
├── app.py                  # Gradio 앱 (메인 실행 파일)
├── notebooks/
│   └── AI_Interview_Agent.ipynb   # 개발 노트북 (단계별 설명 포함)
├── requirements.txt        # 의존 패키지
├── .env.example            # 환경 변수 템플릿
├── .gitignore
└── README.md
```

---

## ⚙️ 실행 방법

### 1. 환경 설정

```bash
git clone https://github.com/dorec9/ai-interview-agent.git
cd ai-interview-agent
pip install -r requirements.txt
```

### 2. API 키 설정

`.env.example` 을 복사해 `.env` 파일을 만들고 키를 입력하세요.

```bash
cp .env.example .env
```

```
OPENAI_API_KEY=여기에_본인_키_입력
```

### 3. 참고 문서 준비 (선택사항)

RAG 기능을 쓰려면 아래 두 파일을 프로젝트 루트에 놓으세요.

- `KT.docx` : KT 핵심가치 문서
- `KT_interview.docx` : KT 예상 질문 문서

없으면 RAG 없이 GPT만으로 심화질문을 생성합니다.

### 4. 실행

```bash
python app.py
```

브라우저에서 출력된 로컬 주소로 접속하면 됩니다.

---

## 🖥️ 화면 구성

| 단계 | 화면 |
|---|---|
| 시작 | 이력서 업로드 → "인터뷰 시작" 버튼 클릭 |
| 면접 진행 | AI 면접관 질문 표시 → 채팅창에 답변 입력 → Enter |
| 종료 | 피드백 보고서 자동 출력 (분야별 점수 + 종합 코멘트) |

---

## 📊 평가 기준

| 항목 | 설명 |
|---|---|
| 구체성 | 수치·역할·과정·결과가 명확한가 |
| 일관성 | 주장-근거-사례 흐름이 자연스러운가 |
| 적합성 | 직무/질문 의도에 직접 부합하는가 |
| 논리성 | 원인→행동→결과의 인과가 명확한가 |

각 항목 0 또는 1점, 분야별 평균으로 통과 기준(75%) 판단 후 다음 분야로 넘어갑니다.

---

## 🚀 향후 개선 방향

- [ ] 음성 입출력 추가 (TTS/STT)
- [ ] 다양한 기업/직군 템플릿 확장
- [ ] 인터뷰 이력 저장 및 히스토리 조회
- [ ] 실시간 표정/태도 분석 (카메라 연동)

---

## 👤 개발자

**dorec9**  
AI/ML 엔지니어링 & 백엔드 개발
