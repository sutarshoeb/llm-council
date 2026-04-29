# LLM Council — Research Paper Assistant

## **Name :** Shoeb Shakil Sutar
## **PRN :** 25070149022
## **Subject :** Large Language Models

A Multi-LLM Deliberation Framework for Research Paper Analysis and Academic Question Answering.

Built from scratch, inspired by Andrej Karpathy's LLM Council project.

---

## Overview

The system routes a research query through four specialized LLMs simultaneously, anonymizes their responses, and uses a meta-model as an impartial judge to evaluate and synthesize a final answer.

### Pipeline

```
User Query → Council Models (Parallel) → Anonymization → Meta-Model → Final Answer
  Stage 0         Stage 1                   Stage 2         Stage 3      Stage 4
```

---

## Repository Structure

```
llm-council/
│
├── README.md
├── Text_Model_Judge_Response.txt        ← Judge prompt used by the meta-model
│
├── ├── LLM_Council_OpenRouter.ipynb     ← Multi-provider version
│   └── LLM_Council_OpenAI.ipynb         ← OpenAI-only version with PDF support
│
└── ── LLM_Council_Report.pdf           ← Full project report
```

---

## Versions

### Version 1 — OpenRouter (Multi-Provider Council)

Uses the OpenRouter API to access models from multiple providers via the Chat Completions API.

| Model | Provider | Research Strength |
|---|---|---|
| `deepseek/deepseek-v3.2` | DeepSeek | Mathematical and logical reasoning |
| `qwen/qwen3-235b-a22b` | Alibaba | Literature breadth, long context |
| `anthropic/claude-sonnet-4-6` | Anthropic | Academic writing and analysis |
| `google/gemini-3-flash-preview` | Google | Factual grounding, speed |

**Meta-Model (Chairman)** : `openai/gpt-5.4`

---

### Version 2 — OpenAI Only (Responses API + PDF Support)

Uses the native OpenAI Responses API with support for PDF ingestion via file upload or URL.

| Model | Family | Research Strength |
|---|---|---|
| `gpt-5.4-mini` | Language Model | Fast, efficient general QA |
| `o3` | Reasoning Model | Deep multi-step academic reasoning |
| `gpt-5.1` | Language Model | Balanced research analysis |
| `gpt-5.2` | Language Model | Strong academic writing and synthesis |

**Meta-Model (Chairman)** : `gpt-5.4`

#### Input Modes (Version 2 only)

| Mode | Description |
|---|---|
| `text` | Plain research query or pasted abstract |
| `url` | Direct link to a PDF (e.g. arXiv URL) |
| `upload` | Upload a local PDF file via OpenAI Files API |

---

## How It Works

### Stage 1 — Parallel Inference
All four council models receive the user query simultaneously via Python threading. Total latency ≈ slowest model, not sum of all models.

### Stage 2 — Anonymization
Model identities are stripped and replaced with neutral labels (Response A, B, C, D) to eliminate self-preference bias — the tendency of a model to favour its own output.

### Stage 3 — Meta-Model Deliberation
The meta-model (Chairman) receives the original query and all anonymized responses. It evaluates each response and synthesizes a final consolidated answer.

---

## Evaluation Criteria

Each council response is scored by the meta-model on four academic criteria:

| Metric | Description | Score |
|---|---|---|
| Correctness | Factual accuracy of the content | /10 |
| Clarity | Structure and readability | /10 |
| Completeness | Coverage of methodology, findings, limitations | /10 |
| Academic Depth | Goes beyond surface-level explanation | /10 |
| **Total** | | **/40** |

---

## Setup and Usage

### Requirements

- Python 3.10+
- `openai` library (`pip install openai`)
- Google Colab (recommended)

---

### Version 1 — OpenRouter Setup

1. Get your API key from [openrouter.ai](https://openrouter.ai)
2. In Google Colab go to `Tools → Secrets → Add new secret`
3. Name : `OpenRouter`, Value : your API key
4. Upload `Text_Model_Judge_Response.txt` to `/content/`
5. Open and run `LLM_Council_OpenRouter.ipynb`

---

### Version 2 — OpenAI Setup

1. Get your API key from [platform.openai.com](https://platform.openai.com)
2. In Google Colab go to `Tools → Secrets → Add new secret`
3. Name : `OpenAI`, Value : your API key
4. Upload `Text_Model_Judge_Response.txt` to `/content/`
5. Open and run `LLM_Council_OpenAI.ipynb`
6. When prompted, select a mode :
   - `text` — type your research query directly
   - `url` — paste a PDF link (e.g. `https://arxiv.org/pdf/1706.03762`)
   - `upload` — upload your PDF to `/content/` and enter the file path

---

## Key Differences Between Versions

| Feature | OpenRouter Version | OpenAI Version |
|---|---|---|
| API | Chat Completions | Responses API |
| SDK call | `client.chat.completions.create()` | `client.responses.create()` |
| Response field | `response.choices[0].message.content` | `response.output_text` |
| Model diversity | Cross-provider | Architectural (reasoning vs language) |
| PDF support | Not supported | Native via `file_id` / `file_url` |
| File upload | Not supported | OpenAI Files API |

---

## Sample Output

```
=== Research Paper Assistant (LLM Council) ===
Options :
  1. Ask a question about a research topic
  2. Paste a paper abstract for analysis
  3. Ask for a literature summary on a topic

Enter your research query : On Layer Normalization in Transformers

--- Querying Council Models ---
  llm_4 (google/gemini-3-flash-preview) → Success | Latency : 6.81s
  llm_3 (anthropic/claude-sonnet-4-6)   → Success | Latency : 17.02s
  llm_2 (qwen/qwen3-235b-a22b)          → Success | Latency : 23.61s
  llm_1 (deepseek/deepseek-v3.2)        → Success | Latency : 39.47s

--- Council Deliberation Complete ---

--- Final Research Analysis (Meta Model) ---
Response A :
  - Correctness     : 8/10
  - Clarity         : 9/10
  - Completeness    : 9/10
  - Academic Depth  : 8/10
  - Total           : 34/40

Rankings (Best to Worst) : A > D > B > C

Final Synthesized Answer :
...
```

---

## Inspired By

Andrej Karpathy's LLM Council — https://github.com/karpathy/llm-council

---

## References

1. Vaswani, A. et al. (2017). *Attention is All You Need*. NeurIPS.
2. Karpathy, A. (2025). *LLM Council*. GitHub. https://github.com/karpathy/llm-council
3. Zheng, L. et al. (2023). *Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena*. arXiv:2306.05685.
4. OpenAI. (2025). *Responses API Reference*. https://platform.openai.com/docs/api-reference/responses
5. OpenAI. (2025). *Files API Reference*. https://platform.openai.com/docs/api-reference/files
6. Dietterich, T. G. (2000). *Ensemble Methods in Machine Learning*. Springer.
7. Wang, P. et al. (2023). *Large language models are not yet human-level evaluators*. arXiv:2305.13091.

---

