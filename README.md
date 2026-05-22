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
