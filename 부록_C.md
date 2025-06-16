부록 C. 테스트용 샘플 데이터셋

OpenAI API를 이용한 개발과 실험에서 일관되고 재현 가능한 결과를 도출하기 위해서는 테스트에 사용할 수 있는 샘플 데이터셋이 필요합니다. 이 부록에서는 본 도서의 주요 예제 및 실습에 활용할 수 있는 다섯 가지 범주의 샘플 데이터셋을 설명하고 제공합니다. 각 데이터셋은 JSON, CSV 또는 텍스트 형식으로 제공되며, 실제 엔터프라이즈 환경 또는 서비스 모듈을 모델링할 수 있도록 구성되어 있습니다.

- 데이터는 책의 GitHub 저장소에서 다운로드하여 사용할 수 있습니다:  
  https://github.com/your-name/openai-api-book-resources

## C.1 문서 요약 및 임베딩 실습용 데이터셋

문서 요약, 임베딩(Embedding), 검색 시스템 구축 실습 등에서 사용할 수 있는 실제 기업 문서와 뉴스 기사 기반의 데이터셋입니다.

- 파일명: documents_corpus.jsonl
- 형식: JSON Lines
- 필드 구조:
  - id (문서 고유 식별자)
  - title (문서 제목)
  - content (전체 텍스트 내용)
  - category (분류: HR, Tech, Legal, Sales 등)

예시:

```json
{
  "id": "doc_001",
  "title": "사내 보안 정책 변경 안내",
  "content": "모든 임직원은 이번 달부터 2단계 인증을 필수로 설정해야 합니다...",
  "category": "IT 정책"
}
```

활용 예:
- Embedding API로 벡터화 후 FAISS 인덱싱
- ChatGPT를 이용한 문서 요약
- 사용자 질문과 관련된 문서 검색 (RAG)



## C.2 대화 프롬프트 테스트용 Chat 로그 샘플

Chat Completions API 및 Assistant API 실습에 사용할 수 있는 실제 채팅 패턴 기반의 데이터셋입니다. 고객지원, 상담, 안내 챗봇을 모방한 대화 시나리오로 구성되어 있습니다.

- 파일명: chat_prompts_sample.json
- 형식: JSON
- 구조:
  - id: 대화 식별자
  - messages: [ { role: "...", content: "..." }, ... ]
  - intent: 대화 의도(옵션, 레이블 학습용)

예시:

```json
{
  "id": "chat_001",
  "messages": [
    {"role": "user", "content": "안녕하세요, 어제 결제한 내역이 확인이 안돼요."},
    {"role": "assistant", "content": "결제 내역을 확인해드리겠습니다. 주문 번호를 알려주실 수 있을까요?"},
    {"role": "user", "content": "주문번호는 202405-12345입니다."}
  ],
  "intent": "결제확인"
}
```

활용 예:
- 다양한 role 기반 message 구성 학습
- 유사 대화 흐름 테스트 및 시스템 프롬프트 튜닝
- intent 분류기 학습 또는 분석



## C.3 다국어/한국어 프롬프트 실험용 병렬 텍스트

다국어 번역 성능 및 언어 특화 처리 실험을 위한 실제 뉴스 기사 및 문서의 병렬(한글-영어) 데이터셋입니다.

- 파일명: korean_parallel_text.csv
- 형식: CSV
- 필드:
  - ko_text: 한글 원문
  - en_text: 대응되는 영문 번역

예시:

```csv
ko_text,en_text
"오늘 서울은 맑겠지만 오후부터는 흐려지겠습니다.","Today, Seoul will be clear, but it will become cloudy in the afternoon."
"회사 내부 지침이 내일부터 변경됩니다.","The company's internal policy will be revised starting tomorrow."
```

활용 예:
- 번역 품질 비교 (ChatCompletion vs FunctionCalling)
- 시스템 언어 전략 설계 (한국어 사전 처리 여부)
- tokenizer 차이에 따른 비용/토큰 분석



## C.4 JSON 및 Function Calling 실험용 함수 매핑 표본

Function Calling 또는 JSON mode의 강제 구조 출력을 실습할 수 있는 샘플 데이터입니다. 사용자 질문에 대해 호출할 함수 이름과 예상 매개변수를 지정한 학습용 데이터입니다.

- 파일명: function_calls_sample.json
- 구조:
  - user_input: 사용자의 자연어 요청
  - function_name: 연결될 함수 이름
  - parameters_schema: 매개변수 예상 값

예시:

```json
{
  "user_input": "이번 주말 서울 날씨 알려줘",
  "function_name": "get_weather",
  "parameters_schema": {
    "location": "Seoul",
    "date": "2024-06-15"
  }
}
```

활용 예:
- Function Call 사전 매핑 기반 테스트
- Tool 호출 흐름 실습 (Assistants API 대응)
- JSON mode 구조화 응답 테스트



## C.5 음성/이미지 입력 실험용 멀티모달 샘플

Vision API, Whisper, TTS와 같은 멀티모달 API 실습을 위한 데이터셋입니다.

### C.5.1 이미지 입력용

- 파일 위치: ./multimodal/images/
- 예제 구성:
  - screenshots/, photos/, ocr_samples/ 디렉토리 내 예제 스크린샷 및 문서 사진
- 활용 예:
  - GPT-4o Vision 입력 테스트
  - 이미지 캡셔닝 및 UI 분석 과제

### C.5.2 음성 파일 샘플

- 파일 위치: ./multimodal/audio/
- 형식: .mp3, .wav
- 파일 구성:
  - customer_call_01.wav (고객센터 통화 예시)
  - announcement_kr.wav (한국어 안내 방송)
  - sample_prompt_en.mp3 (영문 질문 샘플)

- 활용 예:
  - Whisper에 의한 STT 처리
  - Audio API를 통한 자연스러운 TTS 테스트
  - 다국어 입력 실험



## C.6 데이터셋 사용시 주의사항

1. 모든 데이터셋은 교육 및 테스트용 공개 라이선스로 구성되어 있습니다. 실제 서비스 연동 시에는 사용자 프라이버시와 저작권을 반드시 검토해야 합니다.
2. 예제 데이터는 샘플 분량이며, 성능 테스트 시에는 수천 건 이상의 실제 상황에 맞는 데이터를 구축해 사용하는 것을 권장합니다.
3. Rate Limit을 초과하지 않도록 배치 호출 시 간격을 둘 것 (특히 Embedding API).



## C.7 샘플 데이터셋 다운로드

전체 예제 파일 묶음(zip 파일)과 GitHub 리포지토리는 아래 링크를 통해 제공됩니다:

- zip 전체 다운로드:  
  https://github.com/your-name/openai-api-book-resources/releases/download/v1.0/sample-datasets.zip

- 데이터셋 GitHub Repo:  
  https://github.com/your-name/openai-api-book-resources



부록 C는 독자가 OpenAI API의 다양한 기능을 실험하고 직접 프로젝트에 연결하기 위한 출발점을 제공합니다. 실제 개발 시에는 이 데이터를 기반으로 실 서비스 목적에 따른 고유한 데이터 수집 및 전처리 전략을 설계하는 것이 중요합니다.