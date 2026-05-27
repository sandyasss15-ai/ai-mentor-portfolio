# ai-mentor-portfolio
```markdown
# AI Mentor Bootcamp — <Sandhya>

Public portfolio of 12-day AI Trainer Workshop. By Day 12: 6 daily notebooks + capstone Streamlit URL.
```
<img width="1021" height="342" alt="image" src="https://github.com/user-attachments/assets/c355ca6b-a30a-421c-8a9d-4293a71049b0" />
## Common bugs + recovery

- **`Pydantic ValidationError: name Field required`** with a real résumé → the model decided the résumé was too sparse. Check the raw text — if the name is on line 1 and Gemini missed it, the prompt needs to be clearer (e.g., "the first line is the name").
- **Markdown fences in output** despite mime type → retry path handles. If still failing on retry, set temperature=0 in config: `'temperature': 0`.
- **`429 Resource exhausted`** mid-batch → backup Gemini key from 1Password OR switch to Groq via Day 11 fallback chain (preview).
- **Pydantic schema with required `phone: str`** → Optional. Walk back to step 2.
- **Notebook can't find `../data/sample_resumes.txt`** → mentor copied the notebook into a different folder. They drag-drop the kit's data folder into the same Colab session.

---

## Trainer notes

1. The teaching moment for the day: `response_schema` constrains the model at the decoding level. "Please return JSON" in the prompt is hope. `response_schema` is engineering. Show the difference live by removing `response_schema` from cell 3 and running on résumé 4 (Priya Nair, no email line) — model usually invents an email. Then put `response_schema` back; it now returns proper Optional / null.
2. When a mentor's call hits 429 mid-Step 4 and they panic — don't fix it. Walk through the rate-limit reality. "This is the 2026 free-tier reality. Tomorrow we add Groq fallback."
3. Pair-debug rule: if a mentor is stuck more than 10 minutes, pair them with someone whose code is working. Pair-debugging is faster than trainer-walks-over.
4. Acceptance verification at 15:25: ask each mentor to show the cell-4 output on the projector for 30 seconds. If 3 résumés processed, pass. If not, 5 min catch-up.

## Day 4 — Productivity sprint

**Company:** <COMPANY>
**Time:** 45 minutes (timeboxed)

### Edit notes (3 lines)

1. Gamma confabulated a "hiring 50,000 freshers in 2025" stat on slide 6. Source said 40,000. Edited.
2. Slide 4 listed "Kubernetes" as a required skill — actually nice-to-have per the JD. Edited.
3. Slide 1 (cover) — replaced Gamma's generic "Your Career Awaits" with a company-specific line.
   
```markdown
## Day 5 Lab 5B — Hugging Face Pulls

### Models tested
- `facebook/bart-large-mnli` — zero-shot classification
- `distilbert-base-uncased-finetuned-sst-2-english` — sentiment

### Timing comparison

| | min | avg | Notes |
|---|-----|-----|-------|
| HF Inference API | 0.8s | 1.2s | Cold-start: 20s |
| Local in Colab | 2.1s | 3.4s | Download: 60s on first run |

### When to use each (3-line reflection)

1. **API:** for low-volume, occasional calls. Avoids download. Cold-start risk on first call after idle.
2. **Local:** for batch processing 100+ items, where you want predictable latency and don't pay per call.
3. **Production rule of thumb:** if your usage exceeds the API free tier (~30K requests/month at HF), self-host. Otherwise API.
```

```markdown
## Day 6 Lab 6A — Errors handled

1. **Markdown fence wrapping** (`\`\`\`json ... \`\`\``)
   The retry prompt asks Gemini to output raw JSON without fences. Triggers on ~5-10% of calls.

2. **Hallucinated phone number when source has none**
   `Optional[str] = None` in Pydantic — model returns `null`, schema validates.

3. **Empty / whitespace-only input**
   Pydantic raises ValidationError with "Field required". Caller catches.

**Hallucination on garbage input:** Gemini sometimes invents a plausible résumé from non-résumé text. Defence: validate input before sending (e.g., minimum length, presence of email-like pattern).
```
```markdown
## Day 6 Lab 6A — Errors handled

1. **Markdown fence wrapping** (`\`\`\`json ... \`\`\``)
   The retry prompt asks Gemini to output raw JSON without fences. Triggers on ~5-10% of calls.

2. **Hallucinated phone number when source has none**
   `Optional[str] = None` in Pydantic — model returns `null`, schema validates.

3. **Empty / whitespace-only input**
   Pydantic raises ValidationError with "Field required". Caller catches.

**Hallucination on garbage input:** Gemini sometimes invents a plausible résumé from non-résumé text. Defence: validate input before sending (e.g., minimum length, presence of email-like pattern).
```
```markdown
## Day 9 Lab 9A — Hello-LangGraph

- 1-tool ReAct agent with DuckDuckGo web_search
- 4-message trace on a live-fact question (TCS 2026 hiring)
- Failure case: bad URL → agent reported "could not find" / agent hallucinated [pick one]

### Reflection (3 lines)

1. The trace IS the explanation. Print every step.
2. The doc-string IS the prompt. Bad doc-string = bad tool selection.
3. Real agents handle tool failures gracefully — define failure modes in the doc-string.
```
## Day 9 — Capstone Sprint 4: Career Agent

### 3 tools wired
1. **jd_fetcher** — wraps Day 6's fetch_jd. Returns clean text or ERROR string.
2. **skills_gap** — pure function set difference. Deterministic.
3. **answer_scorer** — Gemini-backed scoring 1-10 with rationale.

### 3 successful runs
| # | Student | Tools used | Outcome |
|---|---------|-----------|---------|
| 1 | Ravi Kumar (CSE) → TCS | skills_gap, answer_scorer | Skill-gap: Spring Boot, AWS |
| 2 | Sneha Reddy (ECE) → Cognizant | skills_gap | Strong match — focus on interview practice |
| 3 | Arun Pillai (IT) → Amazon | skills_gap, answer_scorer | Strong match — score 8/10 on sample |

### 1 failure-recovery analysis

Bad URL passed to jd_fetcher. Agent received `ERROR:` from tool. Agent correctly responded:
"I could not fetch the JD URL. Please provide a working URL." No hallucinated JD content.
This is the safe behaviour. If the agent had hallucinated, the fix would be to tighten the doc-string of jd_fetcher.

### Engineer Answer

1. **PROBLEM** — A static RAG cannot take actions. Students need an assistant that fetches JDs, computes skill gaps, and evaluates their answers — autonomously.

2. **ARCHITECTURE** — LangGraph ReAct agent with 3 specialised tools. Each tool is a plain Python function with a precise doc-string. Agent reasons about which tool to call.

3. **TRADE-OFFS** —
   - Cost: 5-15 LLM calls per task (each ~1-3K tokens). ~20K tokens per student session.
   - Latency: 5-10s per task (LLM calls dominant).
   - Reliability: tools must return predictable strings. ERROR returns are part of the contract.
   - Complexity: doc-strings are now part of the prompt. Bad doc-string = wrong tool.

4. **SCALE** —
   - 1 student / minute: free quota OK.
   - 50 students / day: hits free quota. Switch to paid.
   - 1K students / day: needs caching + parallel inference.

5. **INTERVIEW ANSWER** — "I built a 3-tool LangGraph agent that takes a student profile and produces tailored placement prep — JD analysis, skill gap, answer scoring. Each tool is a plain function; the agent picks which to call. Failure recovery is built into tool contracts."
```
