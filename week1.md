## week 1 notes

- activity --> open any AI tool (ChatGPT, Claude, Gemini etc.) and ask it something specific about a show, film, or team you know really well. It should be detailed enough that you'd catch it if it got it wrong.
    - so whether or not the LLM is right, the tone is usually going to be identical, whether its right or making something up. so you cant usually tell from the output

**building a RAG system**
![generic_vs_rag](./assets/generic_vs_rag.png)

---

**setup**
1. fork the [starter](https://github.com/codepath/ai201-project1-unofficial-guide-starter-v2026) repo
2. `brew install python@3.13`
3. `python3.13 -m venv newvenv`
4. `source newvenv/bin/activate`
5. `pip install -r requirements.txt`
6. [google aistudio](https://aistudio.google.com/) → sidebar → get api key (key icon)
7. add api key to `.env`
8. `python3 test.py`

    ```
    AI201 environment check
    ------------------------------------------------------------
    [PASS] Python version
            3.13.15 on Darwin
    [PASS] Virtual environment
            /Users/xxx/Downloads/ai201-project1-unofficial-guide-starter-v2026/newvenv
    [PASS] Pinned packages
            all 7 import cleanly
    [PASS] Free disk space
            345.6 GB
    [PASS] Memory
            16.0 GB
    [PASS] Key hygiene
            .env is ignored by git
    [PASS] API key
            loaded, 53 characters
    [ .. ] Embedding model — first run downloads ~80 MB, this is the slow part
    [PASS] Embedding model
            all-MiniLM-L6-v2 loaded, 384-dim vectors
    [PASS] Vector store
            Chroma round trip on a cosine collection
    [PASS] Model call
            gemini-3.5-flash-lite replied "Ready."
    ------------------------------------------------------------
    10 passed, 0 failed, 0 to look at, 0 skipped

    You're set. See you in class.
    ```

---

**milestone 1**
1. `python3 app.py index` (downloads 80 GB model)

    ```
    Corpus: campus_life

    loaded   88 documents, 27,908 characters, ~317 characters per document
    chunked  88 chunks, 317 characters on average (shortest 178, longest 549), produced by chunker.py::split_documents
    embedding 88 chunks (first run downloads the model)...
    stored   88 chunks in 8.1s

    Ready. Try: python app.py ask "your question here"
    ```

2. `python3 app.py ask "is the housing lottery random?"`

    ```
    (best distance 0.254, cutoff 0.6)

    The housing lottery is not entirely random in the way most people assume. 
    While rising sophomores get a number drawn at random, juniors and seniors are ordered first by accumulated credit hours, with random tie-breaking used only when necessary (*admin_housing_lottery.txt*).

    Sources retrieved: admin_housing_lottery.txt, admin_parking_permits.txt, advising_registration.txt, housing_morrow_house.txt, housing_tamsin_court.txt

    0 model calls this session, 1 served from cache
    ```

3. `python3 app.py --corpus advice_threads chunks -n 1`

    ```
    26 chunks total. Showing 1, spread across the corpus.

    Paste these into your README under Sample Chunks. The rubric asks
    for the source file and the function that produced them — both are
    printed for you below.

    ======================================================================
    Chunk 1  |  source: thread_bike_commute.txt#0  |  produced by: chunker.py::fallback_split
    ======================================================================
    THREAD: Is a bike worth it for a 20 minute walk commute?

    --- reply 1 (14 votes) ---
    Yeah. Cuts an 18 minute walk to about 6. The thing nobody mentions is storage — covered bike parking exists at three buildings and is full by 9am at all three.

    --- reply 2 (9 votes) ---
    Counterpoint, I sold mine. Between November and March the paths are either icy or salted and salt destroys a drivetrain in one season.

    --- reply 3 (22 votes) ---
    Both true. I keep a cheap bike for September to November and walk the rest of the year. Total cost was about $120 for the bike and I don't care what happens to it.

    --- reply 4 (5 votes) ---
    If you do get one, the campus does free registration and it's the only reason I got mine back after it was taken.

    For each one, ask: could someone answer a question using only this,
    without reading what came before or after?
    ```

4. `python3 app.py corpora`

    ```
    advice_threads
        Question-and-answer threads with several people replying and disagreeing. Uneven lengths; answers spread across replies. (23 documents)

    * campus_life
        Short posts about student life. ~88 documents of 1–3 paragraphs. Useful information usually sits in a single sentence. (88 documents)

    city_guides
        Long travel guides divided into labelled sections — nine towns and five guides that cut across them. Information is organised by heading and spread across paragraphs. (14 documents)

    practice
        Not for your project — the small corpus used for the in-class follow-along. (28 documents)

    * = current default, set in config.py (AI201_CORPUS in .env wins)
    ```

---

**milestone 2**
1. read `/corpora/campus_life texts` → form 5 in-scope questions with their "expects" in `question.py`
2. add criteria 4 & criteria 5 in `criteria.md`

---

**milestone 3**
1. `python3 chunker.py`

    ```
    88 chunks, 317 characters on average (shortest 178, longest 549), produced by chunker.py::split_documents
    ```

2. `grep -n "class Document" ingest.py`

    ```
    17:class Document:
    ```

3. `python3 -c "from ingest import load_documents; docs=load_documents('campus_life'); print([(d.source, len(d.text), d.text.count(chr(10))+1) for d in docs[:10]])"`

    ```
    [('admin_add_drop_deadline.txt', 300, 3), ('admin_campus_jobs_and_financial_aid.txt', 261, 3), ('admin_declaring_a_major.txt', 274, 3), ('admin_dining_dollars.txt', 211, 3), ('admin_grade_appeals.txt', 266, 3), ('admin_graduation_requirements.txt', 282, 3), ('admin_housing_lottery.txt', 397, 3), ('admin_library_holds.txt', 254, 3), ('admin_meal_plan_changes.txt', 222, 3), ('admin_parking_permits.txt', 309, 3)]
    ```

4. `python3 -c "from ingest import load_documents; d=load_documents('campus_life')[0]; print(repr(d.text))"`

    ```
    "On the add/drop deadline\n\nYou can add a course through the end of the second week. Dropping is a longer window — through the end of week six — but a drop after week two shows as a W on your transcript. Nothing anywhere on the registrar's site says this plainly, and students find out from each other."
    ```

5. `python3 -c "from ingest import load_documents; docs=load_documents('campus_life'); docs=sorted(docs,key=lambda d:len(d.text),reverse=True); print('\\n'.join(f'{len(d.text)} chars  {d.source}' for d in docs[:10]))"`

    ```
    549 chars  housing_old_brewhouse.txt
    516 chars  housing_innisfree_hall.txt
    461 chars  housing_morrow_house.txt
    430 chars  housing_calder_annexe.txt
    426 chars  course_cs_340.txt
    425 chars  housing_fenwick_court.txt
    422 chars  course_cs_210.txt
    421 chars  dining_the_atrium.txt
    419 chars  housing_tamsin_court.txt
    416 chars  course_engl_205.txt
    ```

5. modify `chunker.py` → instead of 800 character chunks, one document is one chunk → specific to `campus_life` corpus

6. `python3 app.py --corpus campus_life chunks -n 5`

    ```
    88 chunks total. Showing 5, spread across the corpus.

    Paste these into your README under Sample Chunks. The rubric asks
    for the source file and the function that produced them — both are
    printed for you below.

    ======================================================================
    Chunk 1  |  source: admin_add_drop_deadline.txt#0  |  produced by: chunker.py::split_documents
    ======================================================================
    On the add/drop deadline

    You can add a course through the end of the second week. Dropping is a longer window — through the end of week six — but a drop after week two shows as a Won your transcript. Nothing anywhere on the registrar's site says this plainly, and students find out from each other.

    ======================================================================
    Chunk 2  |  source: course_biol_160.txt#0  |  produced by: chunker.py::split_documents
    ======================================================================
    BIOL 160 Cell Biology

    I lived here my sophomore year. Format is lecture three times a week with a weekly lab. Assessment: four unit tests and a cumulative final. Not curved.

    Expect 9 to 11 hours a week, the heaviest first-year course by reputation.

    The one piece of advice: the unit tests come fast, roughly every three weeks; falling behind once is very hard to recover from.

    ======================================================================
    Chunk 3  |  source: course_hist_118_workload.txt#0  |  produced by: chunker.py::split_documents
    ======================================================================
    Workload for HIST 118 Modern World History

    People keep asking so: a lot of reading, about 120 pages a week, but no problem sets. That's real time, not optimistic time.

    It's front-loaded — the first month is heavier than the rest, partly because you're learning the format.

    ======================================================================
    Chunk 4  |  source: dining_pellew_dining_hall_followup.txt#0  |  produced by: chunker.py::split_documents
    ======================================================================
    Re: Pellew Dining Hall

    Adding to what people have said about Pellew Dining Hall. The wait figure of 12 to 18 minutes at peak matches what I've seen. If you're trying to eat between classes, go before 11:45 and it's a different building entirely.

    Also worth saying: the furthest hall from anywhere, next to the athletics centre. Nobody tells you this at orientation.

    ======================================================================
    Chunk 5  |  source: housing_innisfree_hall.txt#0  |  produced by: chunker.py::split_documents
    ======================================================================
    Innisfree Hall — what it's actually like

    Transferred in last year, so take this with a grain of salt. Built 1991, renovated 2022. Rooms are doubles arranged as pairs sharing one bathroom between two rooms.

    The good: the shared-bathroom-between-two-rooms arrangement is the best compromise on campus.

    The bad: no air conditioning, which matters for the first three weeks of September.

    Laundry costs $1.75 wash, $1.75 dry, app-based. On noise: moderate; the building is L-shaped and the short wing is much quieter.

    For each one, ask: could someone answer a question using only this,
    without reading what came before or after?
    ```

---