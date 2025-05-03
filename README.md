# customize_RAG
# 📰 Finance‑News RAG with Groq Llama‑3

Semantic‑search + LLM generation over the latest articles from  
**Bloomberg · CNBC · MarketWatch** — served lightning‑fast on **Groq Cloud**.

<p align="center">
  <img src="docs/architecture.svg" width="650">
</p>

---

## ✨ Features
| Stage | What it does |
|-------|--------------|
| **Scraper** | Takes up to 3 URLs (default: homepage of each site) and stores raw HTML `<p>` text in Drive. |
| **Semantic Chunker** | Sentence‑splits with spaCy → MiniLM embeddings → groups sentences while cosine ≥ 0.72 → max 256 tokens each. |
| **Embedding & Index** | Creates dense vectors (MiniLM), builds a FAISS cosine index, and writes `news.index` + JSON metadata. |
| **Retriever + LLM** | For a user question: <br>1⃣ embedding → top‑k chunks <br>2⃣ context prompt → **Groq Cloud** (*Llama‑3‑70B* default). |
| **Gradio UI** | One‑click chat front‑end you can share publicly. |

---

## 🚀 Quick Start (Colab)

> Click **`open_in_colab`** ➜ follow the cells from top to bottom.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/your_notebook_link_here)

### Colab flow
1. **Mount Drive** → `/content/drive/…`.
2. **Add Secrets**  
   *Colab sidebar ▸ 🔑 Secrets* → `GROQ_API_KEY`.
3. **Run Scraper** – enter up to 3 URLs in the widgets.
4. **Run Semantic Chunker** – generates `semantic_chunks/*.txt`.
5. **Build Index** – writes `News_index/news.index` & `news_meta.json`.
6. **Ask Questions** – `rag_groq("…")` or launch Gradio.

---

## 🛠 Local Setup

```bash
git clone https://github.com/your-user/finance-news-rag.git
cd finance-news-rag
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
export GROQ_API_KEY=sk-...
python scrape.py https://www.bloomberg.com/ https://www.cnbc.com/
python semantic_chunk.py
python build_index.py
python query.py "What did the Fed say about rates?"
python app.py                   # runs Gradio at localhost:7860


🔑 Secrets

Name	Where it’s used	How to set
GROQ_API_KEY	rag_groq() & Gradio chat	Colab Secrets or export GROQ_API_KEY=
Groq Cloud uses an OpenAI‑compatible endpoint (https://api.groq.com/openai/v1)
so the official openai client works unchanged.




finance-news-rag/
├─ notebooks/                # Colab or Jupyter step-by-step
├─ scrape.py                 # URL → raw text
├─ semantic_chunk.py         # raw → clean semantic chunks
├─ build_index.py            # chunks → FAISS + metadata
├─ query.py                  # CLI prompt → RAG answer
├─ app.py                    # Gradio chat wrapper
├─ requirements.txt
├─ NEWS_text/                # raw HTML paragraphs  (Drive)
├─ NEWS_text/semantic_chunks # clean coherent chunks
└─ News_index/
     ├─ news.index           # FAISS file
     └─ news_meta.json       # list mapping vector ID → {path, preview}

Part | Latency | Notes
Vector search (FAISS) | ~2 ms | in‑RAM, cosine similarity
Groq Llama‑3 70B | 100–300 tok/s | typically < 1 s for 300 tokens
Total answer time | ~1 s | context assembly + generation


 Extending
Swap in Mixtral 8×7B by changing MODEL_NAME.

Point the scraper at any news site that allows HTML <p> extraction.

Deploy app.py on Cloud Run or Fly.io for a always‑on endpoint.

Schedule scrape.py daily via GitHub Actions → automatic index refresh.
---

**How to use**

1. Paste this entire block into `README.md` in your repo.  
2. Adjust:
   * the Colab link (`open_in_colab` badge) to your notebook URL.  
   * repo name, author, and paths if you changed them.  
3. Commit → push to GitHub. Your project page now shows a polished description, quick‑start, architecture, and usage docs.
