# Smart Learning Punjab (prototype)

Built for government-school children first: large buttons, one action per screen, Urdu / English / both, read aloud, star ratings instead of scores.
Teachers prepare and approve lessons and questions with AI (grounded in the uploaded textbook). Children only use stored, published content, so the student side is fast, safe and works with no AI key.

## Run
    pip install -r requirements.txt
    streamlit run app.py
Secrets (see .streamlit/secrets.toml.example): API_KEY, BASE_URL, MODEL, TEACHER_PIN, ADMIN_PIN. The teacher PIN is 1234 unless TEACHER_PIN is set (the officer PIN falls back to the teacher PIN). Change it before real use. Deploy on Streamlit Community Cloud (Vercel cannot host Streamlit).

## Portal features
- Works on phones and laptops (responsive layout; phone shows a 2x2 menu and compact grids).
- Theme switch in the top bar: Auto (follows the device), Light, Dark.
- Readable font (Atkinson Hyperlegible for English, Noto Naskh Arabic for Urdu) with Normal / Large / Extra Large text size for students.
- Back button in the top bar returns to the portal home, where the teacher or officer enters the PIN. A student who goes back can "Continue as" the same learner or pick "A different student".

## Demo flow
1. Teacher > Curriculum: choose class and subject, upload a text-based PDF, Process, Find chapters and topics, Prepare all lessons and questions, preview, Publish.
2. Teacher > Learners: add a school and a class.
3. Student: name, class, language, then "Let's find your starting point", lesson, practice, stars, "another way" if needed, practise again to show improvement.
4. Teacher > Learners: add the learner to the class. Class Overview, Gaps and Interventions (plan, worksheet) now show real results.
5. Officer > Punjab Overview: load demonstration data (clearly labelled) to show district > school > class roll-up, heatmap and measured impact.

## Data model
documents, topics (Draft/Published), questions (with difficulty, objective, source page), schools (district, tehsil, code), classes (grade, section, academic year), students, attempts, answers, events, interventions, audit.

## Limitations
No real login (children identify by name and class; same name and class share a record), PIN only for teacher and officer, Urdu text should be reviewed by a native speaker, read aloud depends on the device having an Urdu voice, no OCR for scanned books, no teacher editing of AI text (only approve, regenerate or unpublish), SQLite resets on free hosting restarts, demonstration data is synthetic.
