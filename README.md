# Punjab AI Education Intelligence Platform (Rapid Prototype)

## Purpose
Turns an uploaded, approved textbook into a curriculum-grounded learning and assessment loop: structure, learn, assess, diagnose gaps, targeted practice, reassess, analytics. This is a prototype, not a production system.

## Architecture
Upload (PDF/DOCX/TXT) -> PyMuPDF / python-docx text extraction with page numbers -> chunks in SQLite -> AI curriculum structure (Grade > Subject > Book > Chapter > Topic > Sub-topic > SLO > Concept) -> TF-IDF retrieval -> grounded LLM generation (JSON) -> validation -> attempts and answers in SQLite -> mastery, gap rules, dashboards.
Tables: documents (with curriculum_version), chunks, qsets, questions, attempts, answers. Grades, subjects and books are data, not code; adding Grade 8 means uploading Grade 8 content.

## Configuration and deployment
End users see no key or URL fields. The administrator sets these values as secrets (Streamlit Cloud: App settings, Secrets; locally: `.streamlit/secrets.toml`, see `secrets.toml.example`):
    API_KEY = "..."            # any OpenAI-compatible key
    BASE_URL = "https://api.groq.com/openai/v1"
    MODEL = "openai/gpt-oss-120b"
    TEACHER_PIN = "..."        # optional; protects teacher tools
Environment variables with the same names also work. Never commit secrets.toml.

Run locally: `pip install -r requirements.txt` then `streamlit run app.py`.
Deploy: push the folder to GitHub, create an app on Streamlit Community Cloud pointing at app.py, paste the secrets. Vercel cannot host Streamlit; Render, Railway or Hugging Face Spaces also work.
Note: SQLite data (uploaded books, attempts) is lost when a free host restarts or redeploys. Use a hosted database for persistence.

## How RAG works
For each request the topic, sub-topics, concepts and SLOs form a query. The top 6 TF-IDF matches (with page numbers) are sent inside <curriculum> tags. The system prompt treats them as data only (prompt-injection guard), forbids outside knowledge, and requires the sentence "The uploaded curriculum does not provide sufficient information for this request." when context is insufficient. Each question stores a source page; "View Source" shows the stored text of that page. If the page is not among retrieved pages, it is labelled "No verified curriculum source found."

## How assessment works
Teacher picks chapter, topic, type (MCQ, True/False, Short), difficulty, Bloom level, count. Output is validated (4 distinct options, correct answer matches an option, valid difficulty and Bloom, source page) with one repair round. Sets stay Draft until Approved. MCQ and True/False are auto-scored; short answers are graded by the LLM against the model answer.

## How mastery works
Mastery = marks earned / marks available x 100, per SLO, topic, chapter, difficulty and Bloom level, across all stored attempts. Not a validated psychometric model. Rules: below 50 concept revision plus Easy practice; 50-70 guided examples plus Medium; 70-85 application practice; above 85 extension. Targeted practice is generated for the weakest SLO and excludes earlier question text. Improvement is shown only from actually recorded attempts.

## Demo script
1. Curriculum: upload a textbook PDF, Process document, Build curriculum structure with AI, expand chapters.
2. Learn: pick chapter and topic, Generate learning material, then the Simpler style, open View Source.
3. Assessments: generate 5 MCQs, inspect View Source, Approve and publish.
4. Student: enter a name, answer (deliberately miss some), Submit, read results.
5. Same page: read gap analysis, Generate targeted practice, select it in the dropdown, attempt it, see improvement chart.
6. Teacher Copilot: "Show which SLOs students are struggling with" and "Generate remedial exercises".
7. Analytics: real data plus the labelled demo roll-up.

## Limitations
No OCR (scanned PDFs are rejected), no authentication, roles, audit logs or encryption, English only (strings centralised in T for Urdu later), targeted practice auto-published and flagged as AI Generated, management roll-up is labelled demo data, TF-IDF retrieval (swap for embeddings at scale), structure extraction samples pages when a book exceeds about 28,000 characters so very large books should be processed per chapter.

## Scaling
Add school_id and district_id to students; use Postgres and a vector store; add roles, audit trail, approval workflow, curriculum re-indexing per version, and Urdu/bilingual prompts.
