부록 B. OpenAI SDK for JavaScript, .NET, Node.js

이 부록에서는 OpenAI API를 다양한 언어 환경에서 사용할 수 있도록 도와주는 공식 및 커뮤니티 기반 SDK 중에서 JavaScript (Node.js 포함), .NET (C# 기반) 환경에 맞는 도구들을 소개하고, 각 환경에서의 설치, 인증, 호출 방식 및 대표 예제를 상세히 설명합니다. 이것은 Python 환경에 익숙하지 않은 개발자들이 OpenAI의 다양한 기능들을 손쉽게 활용할 수 있도록 돕기 위한 목적입니다.

📌 목차:

- B.1 JavaScript (Node.js)용 OpenAI SDK
  - B.1.1 설치
  - B.1.2 인증과 환경 설정
  - B.1.3 Chat Completions API 예제
  - B.1.4 Function Calling 예제
  - B.1.5 파일 업로드 및 Assistants API 간단 예제

- B.2 .NET (C#)용 OpenAI SDK
  - B.2.1 설치
  - B.2.2 인증 방식
  - B.2.3 텍스트 생성 (Completions/Chat API)
  - B.2.4 Embedding 예제
  - B.2.5 기타 기능 (파일 업로드, 이미지 생성)

- B.3 SDK 선택 팁과 주의사항



B.1 JavaScript (Node.js)용 OpenAI SDK

OpenAI는 공식 Node.js용 SDK (openai npm 패키지)를 제공합니다. 최신 기능인 Chat Completions API (gpt-4, gpt-4o 포함), Function Calling, Assistants API, Embeddings, Whisper, DALL·E 등 대부분의 기능을 지원합니다.

🧩 B.1.1 설치

Node.js 프로젝트에서 다음 명령으로 SDK를 설치합니다:

```bash
npm install openai
```

또는 yarn 사용 시:

```bash
yarn add openai
```

⚙️ B.1.2 인증과 환경 설정

API 키는 환경 변수로 설정하는 것이 가장 안전한 방법입니다. .env 파일을 활용하세요.

.env 파일 내용:

```dotenv
OPENAI_API_KEY=your-api-key-here
```

index.js에서 API 인스턴스 구성:

```javascript
import OpenAI from 'openai';
import dotenv from 'dotenv';

dotenv.config();

const openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });
```

🔧 B.1.3 Chat Completions API 예제

GPT-4 또는 GPT-3.5를 사용한 대화 생성:

```javascript
const response = await openai.chat.completions.create({
  model: "gpt-4",
  messages: [
    { role: "system", content: "너는 친절한 도우미야." },
    { role: "user", content: "서울의 날씨 알려줘." }
  ],
  temperature: 0.7,
});
console.log(response.choices[0].message.content);
```

📌 토큰, temperature 등은 Chapter 4–5에서 자세히 설명된 바 있습니다.

🔗 B.1.4 Function Calling 예제

Function Calling을 사용한 구조적 응답 예시:

```javascript
const response = await openai.chat.completions.create({
  model: "gpt-4-0613",
  messages: [
    { role: "user", content: "서울의 내일 날씨가 어때?" }
  ],
  functions: [
    {
      name: "get_weather",
      description: "도시의 날씨를 조회합니다.",
      parameters: {
        type: "object",
        properties: {
          city: { type: "string" }
        },
        required: ["city"]
      }
    }
  ],
  function_call: "auto"
});

const functionCallPayload = response.choices[0].message.function_call;
console.log(functionCallPayload.name, JSON.parse(functionCallPayload.arguments));
```

📁 B.1.5 파일 업로드 및 Assistants API 간단 예제

파일 업로드:

```javascript
import fs from 'fs';

const file = await openai.files.create({
  file: fs.createReadStream('./sample.txt'),
  purpose: 'assistants'
});
console.log(file.id);
```

Assistants API를 사용하는 기본 플로우 (더 자세한 내용은 Chapter 11 참고):

```javascript
// Assistant 생성
const assistant = await openai.beta.assistants.create({
  name: "코딩 도우미",
  instructions: "프로그래밍 질문에 답변해주세요.",
  model: "gpt-4",
});

// Thread 생성
const thread = await openai.beta.threads.create();

// 메시지 추가
await openai.beta.threads.messages.create(thread.id, {
  role: "user",
  content: "for문과 while문의 차이점이 뭐야?",
});

// 실행
const run = await openai.beta.threads.runs.create(thread.id, {
  assistant_id: assistant.id,
});

let status = "queued";
let runResult;
while (["queued", "in_progress", "cancelling"].includes(status)) {
  runResult = await openai.beta.threads.runs.retrieve(thread.id, run.id);
  status = runResult.status;
  await new Promise((r) => setTimeout(r, 1000));
}

// 응답 메시지 출력
const messages = await openai.beta.threads.messages.list(thread.id);
console.log(messages.data[0].content[0].text.value);
```



B.2 .NET (C#)용 OpenAI SDK

.NET 개발자들은 다양한 오픈소스 .NET용 OpenAI 클라이언트를 사용할 수 있습니다. 그중 가장 널리 쓰이는 패키지는 `OpenAI-DotNet`입니다.

패키지 이름: Azure/OpenAI-DotNet (공식 GitHub)  
NuGet 패키지: `Betalgo.OpenAI`

🧩 B.2.1 설치

NuGet 패키지 매니저 혹은 CLI를 사용하여 설치:

```bash
dotnet add package Betalgo.OpenAI
```

또는 Visual Studio NuGet UI를 활용해 설치 가능

⚙️ B.2.2 인증 방식

환경변수를 통해 인증하거나 직접 코드에서 API 키를 지정합니다.

예시:

```csharp
var openAiService = new OpenAIService(new OpenAiOptions()
{
    ApiKey = Environment.GetEnvironmentVariable("OPENAI_API_KEY")
});
```

또는:

```csharp
var openAiService = new OpenAIService(new OpenAiOptions()
{
    ApiKey = "your-api-key"
});
```

💬 B.2.3 텍스트 생성 (Completions/Chat API)

```csharp
var chatRequest = new ChatCompletionCreateRequest
{
    Messages = new List<ChatMessage>
    {
        ChatMessage.FromSystem("너는 유쾌한 대화 에이전트야."),
        ChatMessage.FromUser("오늘 저녁 뭐 먹을까?")
    },
    Model = Models.Gpt_4,
    Temperature = 0.7
};

var response = await openAiService.ChatCompletion.CreateCompletion(chatRequest);
Console.WriteLine(response.Choices.First().Message.Content);
```

📌 B.2.4 Embedding 예제

```csharp
var embeddingRequest = new EmbeddingCreateRequest
{
    Input = new List<string> { "OpenAI는 AI 연구에 집중하는 회사입니다." },
    Model = Models.TextEmbeddingAdaV2
};

var result = await openAiService.Embedding.CreateEmbedding(embeddingRequest);
var vector = result.Data[0].Embedding;
Console.WriteLine($"Vector length: {vector.Count}");
```

🎨 B.2.5 기타 기능 (파일 업로드, 이미지 생성 등)

이미지 생성 (DALL·E):

```csharp
var imageRequest = new ImageCreateRequest
{
    Prompt = "하늘을 나는 고양이 일러스트",
    N = 1,
    Size = "1024x1024"
};

var imageResult = await openAiService.Image.CreateImage(imageRequest);
Console.WriteLine(imageResult.Data[0].Url);
```

파일 업로드 및 Assistants API는 .NET용 SDK에서는 아직 제한적이거나 별도 HTTP 호출 방식이 필요합니다. 따라서 API 문서를 기반으로 직접 구현하거나 HTTP 클라이언트 연동이 필요할 수 있습니다.



B.3 SDK 선택 팁과 주의사항

| 항목 | Node.js | .NET (C#) |
|------|---------|------------|
| 공식 지원 | 공식 SDK (openai) 존재 | 비공식 SDK, 성숙한 오픈소스 |
| 최신 기능 제공 속도 | 매우 빠름 (Assistants 등 바로 지원) | 다소 느림, 직접 API 호출 권장 |
| 문서 및 커뮤니티 | 예제 풍부, OpenAI 공식 문서 연동 | 비교적 적은 예제, GitHub 중심 |
| 서버리스/웹 앱 연동 | Vercel, Netlify 등과 연동 쉬움 | ASP.NET Core 기반 웹 API와 통합 용이 |

- Node.js SDK는 최신 기능 업데이트 속도가 매우 빠르며, 예제도 풍부합니다. 특히 Assistants API, JSON Mode, Vision 활용 등에서 강력합니다.
- .NET SDK는 기본적인 텍스트 및 이미지 기능은 훌륭하게 지원하지만, 최신 베타 기능(예: Thread/Run 흐름 등)은 아직 미흡하므로 OpenAI 공식 REST API 직접 호출을 고려해야 할 수도 있습니다.
- 다양한 환경에서 테스트하고 API 호출 로그를 분석하기 위해 debug 플래그 또는 logging 옵션을 설정하는 것이 좋습니다.

예: Node.js에서는

```javascript
import openai from 'openai';
openai.logLevel = 'debug';
```



📌 요약

OpenAI는 다양한 개발 언어를 위한 SDK 또는 REST API 기반의 확장성을 제공합니다. JavaScript(Node.js)는 공식 SDK 중심으로 최신 기능까지 폭넓은 지원을 제공하며, .NET의 경우도 성숙한 커뮤니티 기반 SDK가 존재합니다.

자신의 기술 스택과 서비스 요구사항에 알맞은 SDK를 선택하고, 필요시 HTTP 레벨에서 직접 OpenAI API Docs (https://platform.openai.com/docs)를 참조하여 커스터마이징하는 것이 효과적인 전략입니다.