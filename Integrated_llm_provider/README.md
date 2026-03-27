# integrate-llm-provider

A skill that integrates any LLM or embedding model provider into your codebase — automatically.

Works across **Claude Code**, **OpenAI Codex**, **Gemini CLI**, **Cursor**, and any agent that supports the open Agent Skills standard.

## What it does

1. **Asks you clarifying questions** — provider name, use case (chat/embeddings/both), model preference, framework
2. **Detects your codebase** — language, AI framework, package manager, existing env files
3. **Fetches documentation** — looks up the official framework × provider docs live before writing any code
4. **Writes the integration** — client file, env vars, dependency updates, and a runnable usage example

## Supported Providers

| Provider | Chat | Embeddings |
|---|---|---|
| OpenAI | ✅ | ✅ |
| Anthropic | ✅ | — |
| Azure OpenAI | ✅ | ✅ |
| Cohere | ✅ | ✅ |
| HuggingFace Inference API | ✅ | ✅ |
| Mistral AI | ✅ | ✅ |
| Ollama (local) | ✅ | ✅ |
| Together AI | ✅ | ✅ |
| Groq | ✅ | — |
| AWS Bedrock | ✅ | ✅ |
| Google Gemini | ✅ | ✅ |
| Google Vertex AI | ✅ | ✅ |
| Any other provider | ✅ | ✅ (fetches docs live) |

## Supported Frameworks

- Raw SDK (no framework)
- LangChain (Python + JS/TS)
- LlamaIndex (Python + TS)
- Vercel AI SDK
- LangGraph
- Agno (formerly Phidata)
- CrewAI

For each framework, the skill fetches that **framework's own integration docs** for the chosen provider — not the provider's standalone docs. So LangChain + Azure uses LangChain's Azure page. Agno + Groq uses Agno's Groq page. The generated code matches exactly what the framework recommends.

## Supported Languages

Python, TypeScript, JavaScript, Go

---

## Installation

### One-liner (recommended) — works for Claude Code AND Codex

```bash
npx -y skills add your-username/integrate-llm-provider
```

This installs the skill globally so it's available in any project, on any supported agent.

---

### Manual install — Claude Code

**Project-level (one project only):**
```bash
mkdir -p .claude/skills
cp integrate-llm-provider.md .claude/skills/
```

**Global (all projects):**

Mac/Linux:
```bash
mkdir -p ~/.claude/skills
cp integrate-llm-provider.md ~/.claude/skills/
```

Windows (PowerShell):
```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills"
Copy-Item integrate-llm-provider.md "$env:USERPROFILE\.claude\skills\"
```

---

### Manual install — OpenAI Codex

**Project-level:**
```bash
mkdir -p .codex/skills/integrate-llm-provider
cp integrate-llm-provider.md .codex/skills/integrate-llm-provider/SKILL.md
```

**Global:**

Mac/Linux:
```bash
mkdir -p ~/.codex/skills/integrate-llm-provider
cp integrate-llm-provider.md ~/.codex/skills/integrate-llm-provider/SKILL.md
```

Windows (PowerShell):
```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.codex\skills\integrate-llm-provider"
Copy-Item integrate-llm-provider.md "$env:USERPROFILE\.codex\skills\integrate-llm-provider\SKILL.md"
```

---

## Usage

### In Claude Code
```
/integrate-llm-provider
```

Or with a provider name directly:
```
/integrate-llm-provider OpenAI
/integrate-llm-provider Ollama
/integrate-llm-provider Mistral
```

### In OpenAI Codex CLI / IDE

Type `/skills` or `$` to mention a skill, then select `integrate-llm-provider`.

Or just describe what you want — Codex auto-selects the skill when the task matches:
```
integrate OpenAI into this project using LangChain
```

---

## Example Session

```
User: /integrate-llm-provider

Claude/Codex: Before I start, I have a few quick questions:

  1. Which provider do you want to integrate?
  2. What do you need — chat, embeddings, or both?
  3. Specific model? (or leave blank for default)
  4. Framework? (raw SDK / LangChain / LlamaIndex / Vercel AI SDK / auto-detect)
  5. Do you have an API key?

User: Groq, chat only, llama-3.3-70b-versatile, raw SDK, yes

Agent: Detected:
  - Language: Python
  - Framework: raw SDK
  - Package manager: pip
  - Existing env file: .env.example

  Run this to install:
    pip install groq

  Created: src/llm_client.py
  Modified: .env.example
  Created: examples/llm_example.py

  Next steps:
  [ ] pip install groq
  [ ] Set GROQ_API_KEY in .env
  [ ] python examples/llm_example.py
```

---

## File Structure After Integration

```
your-project/
├── src/
│   └── llm_client.py        # Client init + chat()/embed() helpers
├── examples/
│   └── llm_example.py       # Runnable usage example
├── .env.example             # Updated with required env vars
└── requirements.txt         # Updated with SDK dependency
```

---

## Why This Skill Exists

Integrating an LLM provider sounds simple but has many sharp edges:
- Every framework has its own class names, constructor args, and import paths
- Provider docs and framework docs often diverge
- Env var naming conventions vary per project
- Package names differ from what you'd guess (`langchain-openai`, not `langchain`)

This skill solves that by always fetching the **framework's own docs for that provider** before writing a single line of code.

---

## Contributing / Customizing

The entire skill is defined in `integrate-llm-provider.md`. To add a new framework or provider:

### Add a new provider (to existing frameworks)
Find the table for each framework under **"Framework × Provider Documentation Matrix"** in Step 3 and add a row:
```markdown
| YourProvider | https://docs.yourframework.com/providers/yourprovider |
```

### Add a new framework
Add a new section under the matrix:
```markdown
#### YourFramework

| Provider | Doc URL |
|---|---|
| OpenAI | https://docs.yourframework.com/openai |
| Anthropic | https://docs.yourframework.com/anthropic |
```

Also add the framework's import detection pattern to Step 2b.

---

## Platform Compatibility

This skill follows the [open Agent Skills standard](https://github.com/VoltAgent/awesome-agent-skills), which means the same `SKILL.md` file works across:

| Platform | Supported |
|---|---|
| Claude Code | ✅ |
| OpenAI Codex (CLI + IDE + App) | ✅ |
| Gemini CLI | ✅ |
| Cursor | ✅ |
| OpenCode | ✅ |

---

## Requirements

- Any agent that supports the Agent Skills standard (Claude Code, Codex, Gemini CLI, etc.)
- An API key for the provider you want to integrate (or Ollama running locally)
