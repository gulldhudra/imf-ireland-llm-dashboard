# IMF Country Report → Dashboard: LLM text-to-dashboard pipeline (Ireland, 2026 Article IV)

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/gulldhudra/imf-ireland-llm-dashboard/blob/main/Ireland_IMF_Pipeline.ipynb)

Module assignment (Data Science & AI). The pipeline reads **IMF Country Report No. 26/156 — Ireland: 2026 Article IV Consultation (Staff Report)** and produces summaries, thematic counts, a schema-valid indicator JSON with fallbacks, RAG question answering, a multi-query RAG extension, and a 4-panel dashboard. A second LLM role audits the outputs (LLM-as-a-Judge) and is checked against a human gold standard.

## Run it (Colab, no API key needed)
1. Open `Ireland_IMF_Pipeline.ipynb` in Google Colab (*File → Open notebook → GitHub*, paste this repo URL).
2. *Runtime → Run all*. The first cell clones this repo, so the PDF and the cached LLM responses are found automatically.
3. With the committed cache (`outputs/llm_cache`) the notebook replays every LLM call, so **no key and no quota are needed** and the numbers match the committed `outputs/`.

To regenerate with live calls, add a Gemini key in Colab *Secrets* as `GEMINI_API_KEY` (notebook access ON) and delete `outputs/llm_cache`. A full live run is about 85 calls; the free tier caps requests per model per day, so a paid key is faster. The key is never printed or stored.

## Pipeline
pypdf extraction → cleaning (headers, footers, chart axis noise, line merging) → section split → token-aware chunks (400 tokens, 50 overlap, table-header carry) → DistilBERT-based embeddings (`multi-qa-distilbert-cos-v1`) → FAISS `IndexFlatL2` → summaries, themes, strengths/vulnerabilities, indicator JSON → judge audit → RAG evaluation → multi-query RAG vs single-query → `pd.melt` → dashboard (dpi 300).

## Outputs (`outputs/`)
`indicators.json`, `indicator_audit_trail.json`, `extraction_comparison.json`, `thematic_counts.json`, `qualitative_insights.json`, `summaries.json`, `prompt_ab.json`, `rag_eval_single_query.json`, `extension_multiquery.json`, `indicators_long.csv`, `trend_series.json`, `run_manifest.json`, `figures/*.png`.

## Notes
- The policy rate is not stated in the report (the ECB sets euro-area rates), so it carries the `-1.0` fallback with a recorded reason.
- Source document: © International Monetary Fund, included for reproducibility of this coursework only.
- The files in `outputs/` and the saved notebook outputs come from one Colab run (89 LLM calls, 87 on `gemini-3.5-flash`, 2 on `gemini-3.5-flash-lite`); see `outputs/run_manifest.json`.
- Licence: MIT (code). See `LICENSE`.
