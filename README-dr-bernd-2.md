# Doctor Bernd, version 2: typed oral exam with Hold and Continuation

An adaptive oral exam for fourth-year medical students on the five-month-old febrile infant from "Claude generated kawasaki case with details". Doctor Bernd questions the student on history, differential diagnosis, recommended tests, a red herring, treatment and follow-up, then asks for the final diagnosis near the end. The diagnosis is never named before the student commits to one.

What's new compared with version 1 (`dr-bernd.html`):
- **No abbreviations.** Doctor Bernd is told to write every term in full ("C-reactive protein", "milligrams per kilogram", "Doctor"). The page also has a safety net that expands about 80 common abbreviations and units if the model slips.
- **Hold button.** Pauses the conversation: typing, Send and Stop are locked, the chat dims and a timer shows how long it has been paused. If Doctor Bernd was still thinking, his reply is kept hidden until the student comes back. The student can also type `hold`.
- **Continuation button.** Resumes the conversation and repeats Doctor Bernd's last question ("Welcome back. Where we left off: …").
- **Held sessions survive closing the page.** The conversation is saved in the student's browser. On return, the page offers "Continue my held session" (the access code is asked for again and is never saved).
- **Text only.** No voice, which keeps it simple and works in every browser.
- **The review** after "stop" is laid out as Question 1, Question 2, … with the student's answer, a verdict and the model answer, then topics not reached, a seven-area score, how to improve, and recommended reading. The downloadable transcript records how many times and for how long the session was on hold.

## Files

| File | Where it goes |
|---|---|
| `dr-bernd-2.html` | Your GitHub repo, next to `index.html` and `dr-bernd.html` |
| `worker.js` | Pasted into your existing "Infant Case Rounds" Cloudflare Worker (replaces its code) |
| `README-dr-bernd-2.md` | Your GitHub repo (optional, these instructions) |

## Setup (about 10 minutes)

### 1. Update the Worker code
1. Open your Worker in Cloudflare and click **Edit code**. Select all and save a copy to a text file on your computer as a backup.
2. Replace the code with everything in `worker.js` and click **Deploy**.
3. Your variables stay the same: `ANTHROPIC_API_KEY`, `ACCESS_CODE`, `ALLOWED_ORIGINS` and `MODEL_BALANCED = claude-sonnet-5-5`. There's nothing new to add.

This Worker contains both the version 1 and the version 2 case, so `dr-bernd.html` keeps working. Older pages that send their own instructions also still work. Open each one once after deploying to check. If something breaks, paste your backup back in and click **Deploy**.

### 2. Add the page to GitHub
1. Open `dr-bernd-2.html` in a text editor and find this line near the top of the script:
   ```js
   const WORKER_URL = "https://YOUR-WORKER-NAME.YOUR-SUBDOMAIN.workers.dev";
   ```
   Replace the address with your Worker's address. It's the same one in `dr-bernd.html`.
2. In your GitHub repo, click **Add file → Upload files**, drop in `dr-bernd-2.html`, then click **Commit changes**.
3. After a minute or two, the page is live at
   `https://<your-username>.github.io/<your-repo>/dr-bernd-2.html`

`ALLOWED_ORIGINS` doesn't need changing, because the page is on the same GitHub Pages address.

### 3. Test it
Open the link, enter the access code and click **Start the case**. Answer a question, press **Hold**, wait a few seconds, then press **Continuation**. Type `stop` to see the review.

## How students use it
- Type an answer and press Enter or **Send**.
- **Hold** pauses the conversation. **Continuation** picks up where they left off.
- Type `stop` on its own, or press **Stop and review**, to end. "I would stop the ibuprofen" does not end the session.
- **Download review** saves the transcript and evaluation as a text file they can submit to you.

## Instructor notes
- **Adaptive quizzing:** strong answers get harder follow-ups, such as mechanisms, thresholds, or fever that persists after treatment. Weak answers get hints and simpler sub-questions, for at most two attempts.
- **Red herring:** midway through, Doctor Bernd reports a respiratory panel that is positive for rhinovirus/enterovirus, mentions the daycare exposure, and invites the student to call it a viral rash and send the baby home. Strong students reject the premature closure. The review scores this separately and says whether it set the student apart.
- **Diagnosis concealment:** Doctor Bernd won't say "Kawasaki", "vasculitis", "intravenous immunoglobulin", "coronary", "aspirin" or "echocardiogram" until the student raises the topic. He deflects "is it X?" questions until the student commits.
- **Criteria count (unchanged from version 1):** as written, the patient has fever for at least 5 days plus 4 principal features, which technically meets complete criteria. Doctor Bernd accepts either "Kawasaki disease" or "incomplete (atypical) Kawasaki disease". To make the case truly incomplete, remove one feature (for example, the hand and foot swelling) from the `PROMPTS["dr-bernd-2"]` case text in `worker.js`.
- **Abbreviations in student answers:** students may use abbreviations. Doctor Bernd understands them and replies with the full terms, without penalizing them.

## Cost
A session is roughly 15 to 30 short turns plus one long review on the balanced model. Holding costs nothing, because nothing is sent while the conversation is on hold. Expect well under a dollar per student session; check current API pricing for exact figures. Set a monthly spend limit in the Anthropic Console to be safe.

## Editing the case later
All case content and examiner rules are in `PROMPTS["dr-bernd-2"]` in `worker.js`. Edit there and click **Deploy**; the page doesn't change.
