# HakimBot-Medical-AI
Agentic Medical AI System with multi-source reasoning and intelligent memory

---

## ✨ Overview

HakimBot is an agentic AI system designed to simulate real-world decision-making in medical assistance.

Unlike traditional chatbots, it dynamically determines how to respond by combining:
- Structured data (SQL)
- Knowledge retrieval (RAG)
- Real-time web search
- Context-aware memory

The system doesn’t just generate responses — it reasons, validates, and adapts.

---

## 🧠 Architecture

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>HakimBot Architecture</title>
<link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Mono:wght@400;600&family=IBM+Plex+Sans+Arabic:wght@300;400;600;700&display=swap" rel="stylesheet">
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    background: #050d1a;
    font-family: 'IBM Plex Sans Arabic', 'IBM Plex Mono', monospace;
    display: flex;
    align-items: center;
    justify-content: center;
    min-height: 100vh;
    padding: 40px 20px;
  }

  .canvas {
    width: 1200px;
    min-height: 675px;
    background: #07111f;
    border: 1px solid #1a3a5c;
    border-radius: 20px;
    padding: 52px 60px;
    position: relative;
    overflow: hidden;
  }

  /* background grid */
  .canvas::before {
    content: '';
    position: absolute;
    inset: 0;
    background-image:
      linear-gradient(rgba(30,90,160,.06) 1px, transparent 1px),
      linear-gradient(90deg, rgba(30,90,160,.06) 1px, transparent 1px);
    background-size: 40px 40px;
    border-radius: 20px;
  }

  /* glow blobs */
  .blob {
    position: absolute;
    border-radius: 50%;
    filter: blur(80px);
    opacity: .18;
    pointer-events: none;
  }
  .blob1 { width: 420px; height: 420px; background: #1a6fff; top: -100px; left: -80px; }
  .blob2 { width: 300px; height: 300px; background: #00c8a0; bottom: -80px; right: 60px; }
  .blob3 { width: 200px; height: 200px; background: #7c4dff; top: 200px; right: 200px; opacity: .12; }

  .content { position: relative; z-index: 1; }

  /* header */
  .header {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    margin-bottom: 44px;
  }
  .title-block {}
  .eyebrow {
    font-size: 11px;
    letter-spacing: .18em;
    color: #3a8fff;
    text-transform: uppercase;
    font-family: 'IBM Plex Mono', monospace;
    font-weight: 600;
    margin-bottom: 8px;
  }
  .title {
    font-size: 34px;
    font-weight: 700;
    color: #e8f4ff;
    letter-spacing: -.01em;
    line-height: 1.15;
  }
  .title span { color: #3a8fff; }
  .subtitle {
    font-size: 13.5px;
    color: #5a8aaa;
    margin-top: 8px;
    font-family: 'IBM Plex Mono', monospace;
    letter-spacing: .02em;
  }

  .badges {
    display: flex;
    flex-direction: column;
    gap: 7px;
    align-items: flex-end;
  }
  .badge {
    font-size: 11px;
    font-family: 'IBM Plex Mono', monospace;
    font-weight: 600;
    padding: 4px 12px;
    border-radius: 6px;
    letter-spacing: .06em;
  }
  .b-groq  { background: rgba(58,143,255,.15); color: #5aadff; border: 1px solid rgba(58,143,255,.25); }
  .b-lg    { background: rgba(0,200,160,.12); color: #00c8a0; border: 1px solid rgba(0,200,160,.22); }
  .b-chroma{ background: rgba(124,77,255,.15); color: #a07aff; border: 1px solid rgba(124,77,255,.25); }

  /* pipeline */
  .pipeline {
    display: flex;
    align-items: center;
    gap: 0;
    margin-bottom: 36px;
  }

  .step {
    flex: 1;
    background: rgba(255,255,255,.03);
    border: 1px solid rgba(255,255,255,.08);
    border-radius: 12px;
    padding: 14px 12px 12px;
    text-align: center;
    position: relative;
    transition: border-color .2s;
  }
  .step-num {
    font-size: 10px;
    font-family: 'IBM Plex Mono', monospace;
    color: #2a5a80;
    font-weight: 600;
    letter-spacing: .1em;
    margin-bottom: 5px;
  }
  .step-name {
    font-size: 12.5px;
    font-weight: 600;
    color: #c8dff0;
    line-height: 1.3;
    margin-bottom: 3px;
  }
  .step-sub {
    font-size: 10px;
    color: #3a6888;
    font-family: 'IBM Plex Mono', monospace;
  }
  .step.hi { border-color: rgba(58,143,255,.4); background: rgba(58,143,255,.07); }
  .step.hi .step-name { color: #7ac4ff; }
  .step.hi2 { border-color: rgba(0,200,160,.35); background: rgba(0,200,160,.06); }
  .step.hi2 .step-name { color: #40d4b4; }
  .step.hi3 { border-color: rgba(124,77,255,.35); background: rgba(124,77,255,.06); }
  .step.hi3 .step-name { color: #b090ff; }

  .arrow {
    width: 28px;
    flex-shrink: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    color: #1e4a70;
    font-size: 18px;
  }

  /* layers grid */
  .layers {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr 1fr;
    gap: 14px;
    margin-bottom: 30px;
  }

  .layer {
    border-radius: 12px;
    padding: 16px;
    border: 1px solid transparent;
    position: relative;
    overflow: hidden;
  }
  .layer::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 2px;
    border-radius: 12px 12px 0 0;
  }

  .layer-sql { background: rgba(58,143,255,.08); border-color: rgba(58,143,255,.2); }
  .layer-sql::before { background: #3a8fff; }

  .layer-rag { background: rgba(0,200,160,.07); border-color: rgba(0,200,160,.2); }
  .layer-rag::before { background: #00c8a0; }

  .layer-web { background: rgba(124,77,255,.08); border-color: rgba(124,77,255,.2); }
  .layer-web::before { background: #7c4dff; }

  .layer-mem { background: rgba(255,160,40,.07); border-color: rgba(255,160,40,.18); }
  .layer-mem::before { background: #ffa028; }

  .layer-icon {
    font-size: 11px;
    font-family: 'IBM Plex Mono', monospace;
    font-weight: 600;
    letter-spacing: .1em;
    margin-bottom: 8px;
  }
  .sql-c { color: #5aadff; }
  .rag-c { color: #00c8a0; }
  .web-c { color: #a07aff; }
  .mem-c { color: #ffb850; }

  .layer-title {
    font-size: 13.5px;
    font-weight: 700;
    color: #d0e8f8;
    margin-bottom: 10px;
  }
  .layer-items {
    list-style: none;
    display: flex;
    flex-direction: column;
    gap: 5px;
  }
  .layer-items li {
    font-size: 10.5px;
    color: #4a7a98;
    font-family: 'IBM Plex Mono', monospace;
    padding-left: 10px;
    position: relative;
    line-height: 1.4;
  }
  .layer-items li::before {
    content: '─';
    position: absolute;
    left: 0;
    color: #1e4060;
  }

  /* footer */
  .footer {
    display: flex;
    justify-content: space-between;
    align-items: center;
    border-top: 1px solid rgba(255,255,255,.06);
    padding-top: 18px;
  }
  .footer-left {
    display: flex;
    gap: 20px;
  }
  .stat {
    text-align: center;
  }
  .stat-num {
    font-size: 20px;
    font-weight: 700;
    color: #e8f4ff;
    font-family: 'IBM Plex Mono', monospace;
    line-height: 1;
  }
  .stat-num span { color: #3a8fff; font-size: 14px; }
  .stat-label {
    font-size: 10px;
    color: #2a5070;
    letter-spacing: .08em;
    text-transform: uppercase;
    font-family: 'IBM Plex Mono', monospace;
    margin-top: 4px;
  }
  .sep { width: 1px; background: rgba(255,255,255,.06); align-self: stretch; }

  .footer-right {
    text-align: right;
  }
  .author {
    font-size: 12px;
    color: #3a8fff;
    font-family: 'IBM Plex Mono', monospace;
    font-weight: 600;
    letter-spacing: .04em;
  }
  .handle {
    font-size: 10px;
    color: #2a5070;
    font-family: 'IBM Plex Mono', monospace;
    margin-top: 2px;
  }

  /* retry note */
  .retry-bar {
    background: rgba(255,160,40,.07);
    border: 1px solid rgba(255,160,40,.15);
    border-radius: 8px;
    padding: 9px 16px;
    font-size: 11px;
    color: #7a6020;
    font-family: 'IBM Plex Mono', monospace;
    margin-bottom: 30px;
    display: flex;
    align-items: center;
    gap: 10px;
  }
  .retry-bar strong { color: #ffb850; }
  .dot { width: 5px; height: 5px; border-radius: 50%; background: #ffa028; flex-shrink: 0; }
</style>
</head>
<body>
<div class="canvas">
  <div class="blob blob1"></div>
  <div class="blob blob2"></div>
  <div class="blob blob3"></div>

  <div class="content">

    <!-- HEADER -->
    <div class="header">
      <div class="title-block">
        <div class="eyebrow">Medical AI · Agentic Pipeline</div>
        <div class="title">Hakim<span>Bot</span></div>
        <div class="subtitle">LangGraph · Groq · ChromaDB · SQLite · Gradio</div>
      </div>
      <div class="badges">
        <span class="badge b-groq">Llama 3.3 · 70B</span>
        <span class="badge b-lg">LangGraph</span>
        <span class="badge b-chroma">multilingual-e5</span>
      </div>
    </div>

    <!-- PIPELINE ROW -->
    <div class="pipeline">
      <div class="step hi">
        <div class="step-num">01</div>
        <div class="step-name">Understand</div>
        <div class="step-sub">intent · freshness</div>
      </div>
      <div class="arrow">›</div>
      <div class="step">
        <div class="step-num">02</div>
        <div class="step-name">Decision Gate</div>
        <div class="step-sub">chitchat / vague</div>
      </div>
      <div class="arrow">›</div>
      <div class="step hi">
        <div class="step-num">03</div>
        <div class="step-name">Planner</div>
        <div class="step-sub">picks sources</div>
      </div>
      <div class="arrow">›</div>
      <div class="step hi2">
        <div class="step-num">04</div>
        <div class="step-name">Retrieval</div>
        <div class="step-sub">parallel · seq</div>
      </div>
      <div class="arrow">›</div>
      <div class="step">
        <div class="step-num">05</div>
        <div class="step-name">Verification</div>
        <div class="step-sub">quality check</div>
      </div>
      <div class="arrow">›</div>
      <div class="step hi3">
        <div class="step-num">06</div>
        <div class="step-name">Smart Merge</div>
        <div class="step-sub">LLM synthesis</div>
      </div>
      <div class="arrow">›</div>
      <div class="step hi2">
        <div class="step-num">07</div>
        <div class="step-name">Answer</div>
        <div class="step-sub">same language</div>
      </div>
    </div>

    <!-- RETRY BAR -->
    <div class="retry-bar">
      <div class="dot"></div>
      <span><strong>Smart Retry</strong> — if verification fails, tries untried sources before fallback &nbsp;·&nbsp; max 2 retries &nbsp;·&nbsp; General LLM fallback as last resort</span>
    </div>

    <!-- 4 LAYERS -->
    <div class="layers">
      <div class="layer layer-sql">
        <div class="layer-icon sql-c">[ SQL ]</div>
        <div class="layer-title">Medical DB</div>
        <ul class="layer-items">
          <li>Clinics · Doctors · Staff</li>
          <li>Patients · Appointments</li>
          <li>Prescriptions · Lab results</li>
          <li>RBAC — 3 role levels</li>
          <li>Views + triggers</li>
        </ul>
      </div>

      <div class="layer layer-rag">
        <div class="layer-icon rag-c">[ RAG ]</div>
        <div class="layer-title">Knowledge Base</div>
        <ul class="layer-items">
          <li>PDF · DOCX · XLSX</li>
          <li>Images via OCR (Tesseract)</li>
          <li>Semantic chunking</li>
          <li>ChromaDB vector store</li>
          <li>AR + EN embeddings</li>
        </ul>
      </div>

      <div class="layer layer-web">
        <div class="layer-icon web-c">[ WEB ]</div>
        <div class="layer-title">Live Search</div>
        <ul class="layer-items">
          <li>DuckDuckGo scraping</li>
          <li>Playwright async</li>
          <li>Freshness detection</li>
          <li>Groq synthesis pass</li>
          <li>Language mirroring</li>
        </ul>
      </div>

      <div class="layer layer-mem">
        <div class="layer-icon mem-c">[ MEM ]</div>
        <div class="layer-title">Memory</div>
        <ul class="layer-items">
          <li>Rolling window K=6</li>
          <li>Auto-compression</li>
          <li>Conversation summary</li>
          <li>Route tracking</li>
          <li>Multi-turn context</li>
        </ul>
      </div>
    </div>

    <!-- FOOTER -->
    <div class="footer">
      <div class="footer-left">
        <div class="stat">
          <div class="stat-num">4<span>×</span></div>
          <div class="stat-label">Data Sources</div>
        </div>
        <div class="sep"></div>
        <div class="stat">
          <div class="stat-num">AR<span>+</span>EN</div>
          <div class="stat-label">Bilingual</div>
        </div>
        <div class="sep"></div>
        <div class="stat">
          <div class="stat-num">70<span>B</span></div>
          <div class="stat-label">Param Model</div>
        </div>
        <div class="sep"></div>
        <div class="stat">
          <div class="stat-num">7</div>
          <div class="stat-label">Pipeline Steps</div>
        </div>
      </div>
      <div class="footer-right">
        <div class="author">#MedicalAI · #LangGraph · #RAG</div>
        <div class="handle">Built with Groq · LangChain · Gradio</div>
      </div>
    </div>

  </div>
</div>
</body>
</html>


---

## 🔁 Agentic Pipeline

Understand → Decision → Plan → Retrieve → Verify → Synthesize → Answer

---

## 🧩 System Components

### 🗄️ SQL Layer — Medical Database
- Clinics · Doctors · Staff  
- Patients · Appointments  
- Prescriptions · Lab Results  
- Role-Based Access Control (RBAC)  

---

### 📚 RAG Layer — Knowledge Base
- PDF · DOCX · XLSX  
- OCR (Tesseract)  
- Semantic chunking  
- ChromaDB vector store  
- Multilingual embeddings (AR + EN)  

---

### 🌐 Web Layer — Live Intelligence
- Real-time web search  
- Freshness-aware retrieval  
- Async execution  
- LLM synthesis  

---

### 🧠 Memory Layer — Context Intelligence
- Rolling window (K=6)  
- LLM-based summarization  
- Context compression  
- Multi-turn reasoning  

---

## 🔥 Key Features

- 🧠 Agentic decision-making  
- 🔀 Dynamic multi-source routing  
- ♻️ Smart Retry mechanism  
- 🧩 Hybrid memory system  
- 🌍 Bilingual support (Arabic + English)  

## 🚀 Usage

Set your API key
