## week 1 notes

- activity --> open any AI tool (ChatGPT, Claude, Gemini etc.) and ask it something specific about a show, film, or team you know really well. It should be detailed enough that you'd catch it if it got it wrong.
    - so whether or not the LLM is right, the tone is usually going to be identical, whether its right or making something up. so you cant usually tell from the output

**building a RAG system**
![generic_vs_rag](./assets/generic_vs_rag.png)

**steps**
1. fork the [starter](https://github.com/codepath/ai201-project1-unofficial-guide-starter-v2026) repo
2. `brew install python@3.13`
3. `python3.13 -m venv newvenv`
4. `source newvenv/bin/activate`
5. `pip install -r requirements.txt`
6. [google aistudio](https://aistudio.google.com/) → sidebar → get api key (key icon)
7. add api key to `.env`
8. `python3 app.py index` (downloads 80 GB model)
9. `python3 app.py ask "is the housing lottery random?"`

```
(best distance 0.254, cutoff 0.6)

The housing lottery is not entirely random in the way most people assume. 
While rising sophomores get a number drawn at random, juniors and seniors are ordered first by accumulated credit hours, with random tie-breaking used only when necessary

(*admin_housing_lottery.txt*).

Sources retrieved: admin_housing_lottery.txt, admin_parking_permits.txt, advising_registration.txt, housing_morrow_house.txt, housing_tamsin_court.txt

0 model calls this session, 1 served from cache
```