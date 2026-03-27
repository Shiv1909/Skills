---
name: integrate-llm-provider
description: Integrates an LLM or embedding model provider into your codebase. Asks clarifying questions, auto-detects language and framework, fetches the official framework+provider integration docs (not just provider docs), and writes correct client code, env vars, and dependency updates.
version: 2.0.0
triggers:
  - integrate llm
  - add llm provider
  - integrate embedding
  - integrate openai
  - integrate anthropic
  - integrate ollama
  - integrate cohere
  - integrate mistral
  - integrate groq
  - integrate bedrock
  - integrate gemini
  - integrate together
  - integrate huggingface
  - integrate azure openai
  - integrate agno
  - add ai provider
usage: /integrate-llm-provider [provider-name]
---

# Skill: integrate-llm-provider

You are an expert AI/ML integration engineer. Your job is to integrate an LLM or embedding model provider into the user's codebase — correctly, cleanly, and with minimal friction.

**Critical principle:** Code generation must be driven by the **framework's own documentation for that specific provider** — not generic provider docs, not templates, not guesswork. For example, if the framework is LangChain and the provider is Azure OpenAI, you read LangChain's Azure OpenAI docs and write exactly what those docs show. If the framework is Agno and the provider is Groq, you read Agno's Groq docs.

Follow these steps **in order**. Do not skip steps. Do not write any code until Step 3 (documentation fetching) is complete.

---

## STEP 1 — Ask Clarifying Questions

Ask the user ALL of the following questions in a single message:

```
Before I start, I have a few quick questions:

1. Which provider do you want to integrate?
   Common options: OpenAI, Anthropic, Azure OpenAI, Cohere, HuggingFace,
   Mistral, Ollama, Together AI, Groq, AWS Bedrock, Google Gemini,
   Google Vertex AI — or name any other provider.

2. What do you need from this provider?
   a) Chat / text completions only
   b) Embeddings only
   c) Both chat and embeddings

3. Do you have a specific model in mind?
   (e.g., gpt-4o, claude-sonnet-4-6, mistral-large — or leave blank for the
   provider's recommended default)

4. Will this be used with a specific AI framework?
   a) Raw SDK / no framework
   b) LangChain
   c) LlamaIndex
   d) Vercel AI SDK
   e) LangGraph
   f) Agno (formerly Phidata)
   g) CrewAI
   h) Other (please specify)
   i) Not sure — please auto-detect from the codebase

5. Do you already have an API key / credentials for this provider?
   (yes / no / it's a local provider like Ollama)
```

**Wait for the user's answers before continuing.**

---

## STEP 2 — Detect Codebase

Once you have the user's answers, analyze the codebase using Glob and Grep.

### 2a. Detect language
- `package.json` → TypeScript/JavaScript
- `requirements.txt`, `pyproject.toml`, `setup.py`, `Pipfile` → Python
- `go.mod` → Go
- `Cargo.toml` → Rust
- `pom.xml`, `build.gradle`, `build.gradle.kts` → Java/Kotlin

### 2b. Detect existing AI framework (if user answered "auto-detect")
Grep the codebase for these import patterns:

**Python:**
- `from langchain` or `import langchain` or `from langchain_` → LangChain Python
- `from llama_index` or `import llama_index` → LlamaIndex Python
- `from langgraph` → LangGraph
- `from agno` or `import agno` → Agno
- `from phi` or `import phi` → Agno (older Phidata name)
- `from crewai` or `import crewai` → CrewAI
- `import openai` / `from openai` → raw OpenAI SDK
- `import anthropic` / `from anthropic` → raw Anthropic SDK

**TypeScript/JavaScript:**
- `from "langchain"` or `from '@langchain/` → LangChain JS
- `from "llamaindex"` or `from '@llamaindex/` → LlamaIndex TS
- `from "ai"` or `from '@ai-sdk/` → Vercel AI SDK
- `from "openai"` → raw OpenAI SDK
- `from "@anthropic-ai/sdk"` → raw Anthropic SDK

### 2c. Detect existing env file
- Look for `.env`, `.env.example`, `.env.local`
- If found, read it to understand current env var naming conventions

### 2d. Detect package manager
- `package-lock.json` → npm | `yarn.lock` → yarn | `pnpm-lock.yaml` → pnpm | `bun.lockb` → bun
- `uv.lock` → uv | `Pipfile.lock` → pipenv | `poetry.lock` → poetry | else → pip

### 2e. Report findings
Tell the user what you found before continuing:
```
Detected:
  Language:        [language]
  Framework:       [framework or "raw SDK"]
  Package manager: [manager]
  Env file:        [path or "none found"]

Fetching integration documentation...
```

---

## STEP 3 — Fetch Framework × Provider Documentation

**This is the most important step.** You must read the documentation that describes how the detected **framework** integrates with the chosen **provider** — not the provider's standalone docs.

### 3a. Look up the URL in the matrix below

Find the row matching (framework, provider) in the matrix. If the URL is listed, proceed to 3b.

### 3b. Verify the URL is live — always use WebSearch first

Even if the URL is in the matrix, doc sites restructure their pages. **Always use the `WebSearch` tool** to confirm the current URL before fetching:

```
WebSearch query: "[framework] [provider] integration [use-case] 2026 documentation"
```

Examples:
- `"LangChain Azure OpenAI chat integration 2026 documentation"`
- `"Agno Groq model integration 2026"`
- `"Vercel AI SDK Cohere embeddings 2026"`
- `"LlamaIndex Mistral LLM integration 2026"`

Pick the most relevant result from the search (prefer the official framework docs domain over third-party blogs).

### 3c. Fetch the documentation page

Use `WebFetch` on the confirmed URL. Extract **all** of the following before writing any code:
- The exact package(s) to install (name + version if specified)
- The exact import statements
- The exact class/function names and their constructor arguments
- Required vs optional configuration fields
- The exact method call pattern for chat completions
- The exact method call pattern for embeddings (if applicable)
- Any framework-specific patterns (e.g., how Agno wraps models in an Agent, how LangGraph wraps a model in a node)

### 3d. If the combination is missing from the matrix

Use `WebSearch` with these queries in order until you find official docs:
1. `"[framework] [provider] integration documentation site:[framework-docs-domain]"`
2. `"[framework] [provider] [language] quickstart 2026"`
3. `"[framework] [provider] how to use"`

Then `WebFetch` the best result.

If no official integration exists, tell the user clearly:
> "[Framework] does not have an official [Provider] integration. Options: [list alternatives such as raw SDK, LiteLLM, or a compatible provider]"
> Do not generate code for unsupported combinations — ask the user how to proceed.

**Do not generate code until steps 3a–3c are complete.**

---

### Framework × Provider Documentation Matrix

#### LangChain Python (`langchain-*` packages)

| Provider | Chat Doc URL | Embeddings Doc URL |
|---|---|---|
| OpenAI | https://python.langchain.com/docs/integrations/chat/openai/ | https://python.langchain.com/docs/integrations/text_embedding/openai/ |
| Anthropic | https://python.langchain.com/docs/integrations/chat/anthropic/ | — (no native embeddings) |
| Azure OpenAI | https://python.langchain.com/docs/integrations/chat/azure_chat_openai/ | https://python.langchain.com/docs/integrations/text_embedding/azureopenai/ |
| Cohere | https://python.langchain.com/docs/integrations/chat/cohere/ | https://python.langchain.com/docs/integrations/text_embedding/cohere/ |
| HuggingFace | https://python.langchain.com/docs/integrations/chat/huggingface/ | https://python.langchain.com/docs/integrations/text_embedding/huggingfacehub/ |
| Mistral | https://python.langchain.com/docs/integrations/chat/mistralai/ | https://python.langchain.com/docs/integrations/text_embedding/mistralai/ |
| Ollama | https://python.langchain.com/docs/integrations/chat/ollama/ | https://python.langchain.com/docs/integrations/text_embedding/ollama/ |
| Together AI | https://python.langchain.com/docs/integrations/chat/together/ | — |
| Groq | https://python.langchain.com/docs/integrations/chat/groq/ | — |
| AWS Bedrock | https://python.langchain.com/docs/integrations/chat/bedrock/ | https://python.langchain.com/docs/integrations/text_embedding/bedrock/ |
| Google Gemini | https://python.langchain.com/docs/integrations/chat/google_generative_ai/ | https://python.langchain.com/docs/integrations/text_embedding/google_generative_ai/ |
| Google Vertex | https://python.langchain.com/docs/integrations/chat/google_vertex_ai_palm/ | https://python.langchain.com/docs/integrations/text_embedding/google_vertex_ai_palm/ |

#### LangChain JS/TS (`@langchain/*` packages)

| Provider | Chat Doc URL | Embeddings Doc URL |
|---|---|---|
| OpenAI | https://js.langchain.com/docs/integrations/chat/openai | https://js.langchain.com/docs/integrations/text_embedding/openai |
| Anthropic | https://js.langchain.com/docs/integrations/chat/anthropic | — |
| Azure OpenAI | https://js.langchain.com/docs/integrations/chat/azure | https://js.langchain.com/docs/integrations/text_embedding/azure_openai |
| Cohere | https://js.langchain.com/docs/integrations/chat/cohere | https://js.langchain.com/docs/integrations/text_embedding/cohere |
| Mistral | https://js.langchain.com/docs/integrations/chat/mistral | https://js.langchain.com/docs/integrations/text_embedding/mistral |
| Ollama | https://js.langchain.com/docs/integrations/chat/ollama | https://js.langchain.com/docs/integrations/text_embedding/ollama |
| Groq | https://js.langchain.com/docs/integrations/chat/groq | — |
| AWS Bedrock | https://js.langchain.com/docs/integrations/chat/bedrock | https://js.langchain.com/docs/integrations/text_embedding/bedrock |
| Google Gemini | https://js.langchain.com/docs/integrations/chat/google_generativeai | https://js.langchain.com/docs/integrations/text_embedding/google_generativeai |
| Google Vertex | https://js.langchain.com/docs/integrations/chat/google_vertex_ai | https://js.langchain.com/docs/integrations/text_embedding/google_vertex_ai |

#### LlamaIndex Python

| Provider | Doc URL |
|---|---|
| OpenAI | https://docs.llamaindex.ai/en/stable/examples/llm/openai/ |
| Anthropic | https://docs.llamaindex.ai/en/stable/examples/llm/anthropic/ |
| Azure OpenAI | https://docs.llamaindex.ai/en/stable/examples/llm/azure_openai/ |
| Cohere | https://docs.llamaindex.ai/en/stable/examples/llm/cohere/ |
| HuggingFace | https://docs.llamaindex.ai/en/stable/examples/llm/huggingface/ |
| Mistral | https://docs.llamaindex.ai/en/stable/examples/llm/mistralai/ |
| Ollama | https://docs.llamaindex.ai/en/stable/examples/llm/ollama/ |
| Groq | https://docs.llamaindex.ai/en/stable/examples/llm/groq/ |
| AWS Bedrock | https://docs.llamaindex.ai/en/stable/examples/llm/bedrock/ |
| Google Gemini | https://docs.llamaindex.ai/en/stable/examples/llm/gemini/ |
| Together AI | https://docs.llamaindex.ai/en/stable/examples/llm/together/ |

#### LlamaIndex TypeScript

| Provider | Doc URL |
|---|---|
| OpenAI | https://ts.llamaindex.ai/docs/llamaindex/examples/llm/openai |
| Anthropic | https://ts.llamaindex.ai/docs/llamaindex/examples/llm/anthropic |
| Azure OpenAI | https://ts.llamaindex.ai/docs/llamaindex/examples/llm/azure |
| Mistral | https://ts.llamaindex.ai/docs/llamaindex/examples/llm/mistral |
| Ollama | https://ts.llamaindex.ai/docs/llamaindex/examples/llm/ollama |
| Groq | https://ts.llamaindex.ai/docs/llamaindex/examples/llm/groq |
| Gemini | https://ts.llamaindex.ai/docs/llamaindex/examples/llm/gemini |

#### Vercel AI SDK (`ai` + `@ai-sdk/*` packages)

| Provider | Doc URL |
|---|---|
| OpenAI | https://sdk.vercel.ai/providers/ai-sdk-providers/openai |
| Anthropic | https://sdk.vercel.ai/providers/ai-sdk-providers/anthropic |
| Azure OpenAI | https://sdk.vercel.ai/providers/ai-sdk-providers/azure |
| Cohere | https://sdk.vercel.ai/providers/ai-sdk-providers/cohere |
| Mistral | https://sdk.vercel.ai/providers/ai-sdk-providers/mistral |
| Ollama | https://sdk.vercel.ai/providers/ai-sdk-providers/ollama |
| Together AI | https://sdk.vercel.ai/providers/ai-sdk-providers/togetherai |
| Groq | https://sdk.vercel.ai/providers/ai-sdk-providers/groq |
| AWS Bedrock | https://sdk.vercel.ai/providers/ai-sdk-providers/amazon-bedrock |
| Google Gemini | https://sdk.vercel.ai/providers/ai-sdk-providers/google-generative-ai |
| Google Vertex | https://sdk.vercel.ai/providers/ai-sdk-providers/google-vertex |
| HuggingFace | https://sdk.vercel.ai/providers/ai-sdk-providers/hugging-face |

#### Agno (formerly Phidata) — `agno` Python package

| Provider | Doc URL |
|---|---|
| OpenAI | https://docs.agno.com/models/openai |
| Anthropic | https://docs.agno.com/models/anthropic |
| Azure OpenAI | https://docs.agno.com/models/azure |
| Cohere | https://docs.agno.com/models/cohere |
| HuggingFace | https://docs.agno.com/models/huggingface |
| Mistral | https://docs.agno.com/models/mistral |
| Ollama | https://docs.agno.com/models/ollama |
| Together AI | https://docs.agno.com/models/together |
| Groq | https://docs.agno.com/models/groq |
| AWS Bedrock | https://docs.agno.com/models/aws-bedrock |
| Google Gemini | https://docs.agno.com/models/google |
| Google Vertex | https://docs.agno.com/models/vertexai |
| xAI (Grok) | https://docs.agno.com/models/xai |
| DeepSeek | https://docs.agno.com/models/deepseek |

#### LangGraph Python
LangGraph uses LangChain's model integrations. Use the LangChain Python URLs above, then wrap the model in a LangGraph node:
- LangGraph concepts: https://langchain-ai.github.io/langgraph/concepts/

#### CrewAI Python
| Provider | Doc URL |
|---|---|
| OpenAI | https://docs.crewai.com/concepts/llms#openai |
| Anthropic | https://docs.crewai.com/concepts/llms#anthropic |
| Azure OpenAI | https://docs.crewai.com/concepts/llms#azure |
| Ollama | https://docs.crewai.com/concepts/llms#ollama-local-llms |
| Groq | https://docs.crewai.com/concepts/llms#groq |
| Bedrock | https://docs.crewai.com/concepts/llms#aws-bedrock |
| Gemini | https://docs.crewai.com/concepts/llms#gemini |
| Mistral | https://docs.crewai.com/concepts/llms#mistral |
| HuggingFace | https://docs.crewai.com/concepts/llms#huggingface |
| Together AI | https://docs.crewai.com/concepts/llms#together-ai |

#### Raw SDK (no framework)

For raw SDK integrations, fetch the **provider's own quickstart docs**:

| Provider | Python Docs | JS/TS Docs |
|---|---|---|
| OpenAI | https://platform.openai.com/docs/quickstart | https://platform.openai.com/docs/quickstart |
| Anthropic | https://docs.anthropic.com/en/api/getting-started | https://docs.anthropic.com/en/api/getting-started |
| Azure OpenAI | https://learn.microsoft.com/en-us/azure/ai-services/openai/quickstart?pivots=programming-language-python | https://learn.microsoft.com/en-us/azure/ai-services/openai/quickstart?pivots=programming-language-javascript |
| Cohere | https://docs.cohere.com/docs/the-cohere-platform | https://docs.cohere.com/docs/the-cohere-platform |
| HuggingFace | https://huggingface.co/docs/huggingface_hub/guides/inference | https://huggingface.co/docs/huggingface.js/inference/README |
| Mistral | https://docs.mistral.ai/getting-started/quickstart/ | https://docs.mistral.ai/getting-started/quickstart/ |
| Ollama | https://github.com/ollama/ollama/blob/main/docs/api.md | https://github.com/ollama/ollama-js |
| Together AI | https://docs.together.ai/docs/quickstart | https://docs.together.ai/docs/quickstart |
| Groq | https://console.groq.com/docs/quickstart | https://console.groq.com/docs/quickstart |
| AWS Bedrock | https://docs.aws.amazon.com/bedrock/latest/userguide/getting-started.html | https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/client/bedrock-runtime/ |
| Google Gemini | https://ai.google.dev/gemini-api/docs/quickstart?lang=python | https://ai.google.dev/gemini-api/docs/quickstart?lang=node |
| Google Vertex | https://cloud.google.com/vertex-ai/generative-ai/docs/start/quickstarts/quickstart-multimodal | https://cloud.google.com/vertex-ai/generative-ai/docs/start/quickstarts/quickstart-multimodal |

---

### If the combination is missing from the matrix

1. Use WebSearch: `"[framework name]" "[provider name]" integration documentation`
2. Also try: `site:[framework-docs-domain] [provider name]`
3. WebFetch the most relevant result
4. If the framework has no official integration for this provider, tell the user and suggest the closest alternative (e.g., "Agno doesn't have a native HuggingFace integration — would you like to use the raw HuggingFace SDK, or use it via LiteLLM which Agno supports?")

---

## STEP 4 — Generate Integration Code

**Code must be derived from the documentation you fetched in Step 3**, not from the templates below. The templates are a structural reference only — use the exact class names, method names, arguments, and patterns from the fetched docs.

### 4a. Install command
Print the exact install command from the docs. Do not run it:
```
Run this to install:
  [exact install command from docs]
```

### 4b. Environment variables
Update `.env.example` (create if missing). Append to `.env` if the vars are absent.
Never overwrite existing values. Format:
```env
# [Framework] × [Provider]
API_KEY_VAR=your_key_here
# OPTIONAL_VAR=default_value  # remove this line if not using
```

### 4c. Client / agent initialization file

**File naming — match existing project structure:**
- Python: `src/llm_client.py` or `llm_client.py`
- TypeScript: `src/lib/llm.ts` or `lib/llm.ts`
- Go: `internal/llm/client.go`

If the file already exists, read it first and ADD to it rather than overwriting.

The file must include, in this order:
1. Imports (exactly as shown in the docs)
2. Client / model initialization (env vars, not hardcoded keys)
3. `chat(prompt)` wrapper function — if chat use case
4. `embed(text)` wrapper function — if embedding use case
5. Short inline comments only where the config is non-obvious

### 4d. Example usage file
Create `examples/llm_example.[ext]` — a runnable file that:
- Calls `chat()` with a test prompt
- Calls `embed()` with a test string (if embedding use case)
- Prints the result

### Structural reference templates (adapt from docs — do not copy blindly)

#### Raw SDK — Python — Chat
```python
import os
# [exact import from docs]

client = [ExactClientClass](api_key=os.environ["[API_KEY_VAR]"])

def chat(prompt: str, model: str = "[default_model]") -> str:
    response = client.[exact_method]([exact_args])
    return response.[exact_content_accessor]
```

#### Raw SDK — TypeScript — Chat
```typescript
import [ExactClient] from "[exact-package]";

const client = new [ExactClient]({ apiKey: process.env.[API_KEY_VAR]! });

export async function chat(prompt: string, model = "[default_model]"): Promise<string> {
  const res = await client.[exactMethod]([exactArgs]);
  return res.[exactContentAccessor];
}
```

#### LangChain Python — Chat
```python
import os
from [exact_langchain_package] import [ExactChatClass]  # from docs

llm = [ExactChatClass](
    # use exact constructor args from docs
    api_key=os.environ["[API_KEY_VAR]"],
)

def chat(prompt: str) -> str:
    return llm.invoke(prompt).content
```

#### LangChain Python — Embeddings
```python
import os
from [exact_langchain_package] import [ExactEmbeddingsClass]  # from docs

embeddings = [ExactEmbeddingsClass](
    api_key=os.environ["[API_KEY_VAR]"],
)

def embed(text: str) -> list[float]:
    return embeddings.embed_query(text)

def embed_documents(texts: list[str]) -> list[list[float]]:
    return embeddings.embed_documents(texts)
```

#### Vercel AI SDK — TypeScript
```typescript
import { generateText, embed } from "ai";
import { [exactProviderFn] } from "@ai-sdk/[exact-provider-pkg]";  // from docs

const model = [exactProviderFn]("[model-id]");

export async function chat(prompt: string): Promise<string> {
  const { text } = await generateText({ model, prompt });
  return text;
}

export async function embedText(text: string): Promise<number[]> {
  const { embedding } = await embed({ model: [exactEmbedModel], value: text });
  return embedding;
}
```

#### Agno Python — Chat
```python
import os
from agno.agent import Agent
from agno.models.[exact_provider_module] import [ExactModelClass]  # from docs

agent = Agent(
    model=[ExactModelClass](
        id="[model-id]",
        # exact args from docs
    ),
)

def chat(prompt: str) -> str:
    response = agent.run(prompt)
    return response.content
```

#### CrewAI Python — LLM config
```python
import os
from crewai import LLM

llm = LLM(
    model="[provider]/[model-id]",  # exact format from docs
    api_key=os.environ["[API_KEY_VAR]"],
    # exact optional args from docs
)
```

---

## STEP 5 — Dependency File Updates

**Python — requirements.txt:** Append:
```
# [Framework] × [Provider Name]
[exact-package-name]  # version from docs if specified
```

**Python — pyproject.toml:** Add to `[project.dependencies]` or `[tool.poetry.dependencies]`

**TypeScript/JS — package.json:** Tell the user to run the install command (do not edit manually)

**Go — go.mod:** Tell the user: `go get [module-path]`

---

## STEP 6 — Post-Integration Checklist

Print this after all files are written:

```
Integration complete!

FRAMEWORK:  [framework]
PROVIDER:   [provider]
DOCS USED:  [URL you fetched in Step 3]

FILES CREATED / MODIFIED:
  [list each file with brief description]

NEXT STEPS:
  [ ] Install: [exact install command]
  [ ] Add to .env:
        [API_KEY_VAR]=your_actual_key_here
        [any other required vars]
  [if Ollama]  [ ] Install Ollama: https://ollama.com/download
  [if Ollama]  [ ] Pull model: ollama pull [model]
  [if Bedrock] [ ] Configure AWS: aws configure
  [if Vertex]  [ ] Auth: gcloud auth application-default login
  [ ] Run example: [command to run examples/llm_example.*]
```

---

## Rules

- **Always use `WebSearch` before `WebFetch`** — search first to confirm the current URL, then fetch. Doc sites restructure pages; never assume the matrix URL is still valid.
- **Use precise search queries** — include the framework name, provider name, use case, and year (e.g., `"Agno Groq chat 2026 documentation"`) to get current results.
- **Code comes from the fetched docs** — not from memory, not from the templates in Step 4.
- **Never hardcode API keys** — always use `os.environ` / `process.env`.
- **Never overwrite existing files** — always read first, then append or merge.
- **Match the project's existing code style** — indentation, quotes, naming conventions.
- **If no official integration exists**, say so and offer alternatives — do not generate speculative code.
- **If a page 404s or is unhelpful**, run another `WebSearch` with a different query — do not skip documentation and fall back to guessing.
- **Keep generated code minimal** — no abstractions beyond what the docs show, no extra error handling, no unnecessary wrappers.
