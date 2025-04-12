# 📚 RAG 強化系統使用說明（README）

本專案基於 LangChain 打造 Retrieval-Augmented Generation（RAG）系統，並充分利用 `.parquet` 結構化資料進行擴展、檢索、排序與回答生成。

---

## 📦 資料結構與模組對應

| 檔名 | 類型 | 對 RAG 的幫助 | 適用模組 |
|------|------|----------------|----------|
| `text_units.parquet` | 段落/句子級 chunk | ✅ 檢索內容來源，是 retriever 要搜尋的文本主體 | `Retriever` |
| `embeddings.text_unit.text.parquet` | chunk 向量 | ✅ 提供每段 chunk 的語意表示，用來建立向量索引 | `Retriever` |
| `documents.parquet` | 文件 metadata | ✅ 補充 chunk 的標題、主題、出處，讓回答更可信 | `PromptBuilder` |
| `embeddings.entity.description.parquet` | 實體嵌入 | ✅ 查詢擴展（找 query 相關的實體詞） | `QueryExpander` |
| `entities.parquet` | chunk 提及實體 | ✅ 可進行 symbolic rerank 與實體提示 | `Reranker`, `PromptBuilder` |
| `relationships.parquet` | 實體關聯圖 | ✅ 多跳推理與實體關聯排序 | `Reranker` |
| `communities.parquet` | 主題分群 | ✅ 可過濾主題、標示內容主題 | `Retriever`, `PromptBuilder` |
| `community_reports.parquet` | 主題摘要 | ✅ 回答補充背景，提高信任度 | `PromptBuilder` |

---

## 🔧 模組功能與資料對應

| 模組 | 需要的資料 | 功能說明 |
|------|-----------------------------|--------------|
| 🔍 `Retriever` | `text_units`, `embeddings.text_unit.text`, `communities` | 根據向量檢索 chunk，可加上主題過濾 |
| 🧠 `QueryExpander` | `embeddings.entity.description` | 擴展查詢語意，提升召回率 |
| ⚖️ `Reranker` | `entities`, `relationships` | 利用 symbolic 關係 rerank，提高查準率 |
| 🧾 `PromptBuilder` | `documents`, `entities`, `community_reports` | 組合回應提示，加入文件出處、摘要背景等 |

---

## ✅ 小結：資料分類用途

| 類別 | 檔案 | 主要作用 |
|------|------|----------|
| 🔑 核心檢索資料 | `text_units.parquet`, `embeddings.text_unit.text.parquet` | 建立向量資料庫、Retriever |
| 🧠 語意強化擴展 | `embeddings.entity.description.parquet` | 查詢擴展（語意補強） |
| 🔁 精準排序強化 | `entities.parquet`, `relationships.parquet` | 檢索結果 rerank（symbolic） |
| 🧾 回答增強信任 | `documents.parquet`, `community_reports.parquet` | 附加 metadata、摘要背景 |
| 🎯 主題導向過濾 | `communities.parquet` | 檢索前過濾主題、提示標註主題 |

---

## 🚀 模組建議開發順序

1. ✅ `query_expander.py`：擴展語意（已完成）
2. ⏳ `retriever.py`：向量資料庫與檢索邏輯
3. ⏳ `reranker.py`：加入實體相似度與關聯度排序
4. ⏳ `prompt_builder.py`：組合 prompt 並加入 metadata
5. ✅ `rag_pipeline.py`：整合流程，已支援 LangChain

---