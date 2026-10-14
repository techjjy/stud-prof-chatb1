# Dr. Bernd: voice oral-exam chatbot (Kawasaki virtual patient)

A spoken, adaptive oral exam for 4th-year medical students. Dr. Bernd presents the 5-month-old febrile infant, questions the student on history, differential diagnosis, workup, a red herring, treatment and follow-up, then asks for the final diagnosis. When the student says or types **stop**, he gives a question-by-question review, a scored evaluation, ways to improve, and referenced reading.

It uses the same GitHub Pages repo and the same Cloudflare Worker ("Infant Case Rounds") as your existing chatbot.

## Files

| File | Where it goes |
|---|---|
| `dr-bernd.html` | Your GitHub repo, next to your existing `index.html` |
| `worker.js` | Pasted into your existing Cloudflare Worker (replaces its code) |
| `README-dr-bernd.md` | Your GitHub repo (optional, these instructions) |

## Setup (about 10 minutes)

### 1. Update the Worker code
1. Before changing anything, open your Worker in Cloudflare → **Edit code**, select all, and save a copy of the current code to a text file on your computer (your backup).
2. Replace it with everything in `worker.js` and click **Deploy**.
3. Your variables stay the same: `ANTHROPIC_API_KEY`, `ACCESS_CODE`, `ALLOWED_ORIGINS`, and `MODEL_BALANCED = claude-sonnet-5-5`. Nothing new to add.

Why the Worker changes: the case details and diagnosis now live inside the Worker, not in the web page. Students can't find the answer by viewing the page source.

The new Worker still accepts the older style of request (a page sending its own system prompt), so your existing chatbot should keep working. Open it once after deploying to check. If it breaks, paste your backup back in, click Deploy, and send me your old Worker code so I can merge the two.

### 2. Add the page to GitHub
1. Open `dr-bernd.html` in a text editor and find this line near the top of the script:
   ```js
   const WORKER_URL = "https://YOUR-WORKER-NAME.YOUR-SUBDOMAIN.workers.dev";
   ```
   Replace it with your Worker's address (the same one your existing page uses; it's shown on the Worker's overview page in Cloudflare).
2. In your GitHub repo click **Add file → Upload files**, drop in `dr-bernd.html` (and this README if you like), then **Commit changes**.
3. After a minute or two the page is live at
   `https://<your-username>.github.io/<your-repo>/dr-bernd.html`

Because it's on the same GitHub Pages domain, `ALLOWED_ORIGINS` doesn't need changing.

### 3. Test it
Open the link, enter the access code, click **Start the case**, allow the microphone, and answer a question or two. Say "stop" to see the review.

## How students use it
- Tap the **microphone**, speak, and tap again (or just pause) to send. Typing also works.
- Dr. Bernd's replies are read aloud. **Voice on/off** and **Stop speaking** control that. A voice can be picked on the start screen.
- Say or type **stop** (on its own) or press **Stop & review** to end. Saying "stop" inside a sentence, like "I'd stop the aspirin," does not end the session.
- **Download review** saves the full transcript and evaluation as a text file they can submit to you.

## Browser notes
- Voice input: Chrome, Edge and Safari (desktop and mobile). Firefox has no speech recognition, so students type there; replies are still spoken.
- Speech recognition in Chrome sends audio to Google's speech service. Tell students that if it matters for your course.

## Design notes (instructor)
- **Adaptive questioning:** strong answers get harder follow-ups (mechanisms, thresholds, IVIG resistance); weak answers get hints and simpler sub-questions.
- **Red herring:** mid-session, Dr. Bernd reports a positive rhinovirus/enterovirus panel plus daycare exposure and invites the student to call it a viral exanthem and send the baby home. Strong students reject premature closure; average students tend to accept it. The review scores this explicitly.
- **Diagnosis concealment:** Dr. Bernd never names the disease or its specific treatment terms until the student raises them, and deflects "is it X?" questions until the student commits.
- **Test results:** the lab, urine, CSF, viral and echo results are released only when ordered, and are consistent with the case (high CRP/ESR, anemia, hypoalbuminemia, raised ALT, sterile pyuria, coronary Z-scores +2.6 / +2.1).
- **Criteria count:** as written, the case shows fever ≥5 days plus 4 principal features (conjunctivitis, lip changes, rash, hand/foot edema; the 1 cm node doesn't count). So it technically meets complete criteria. Dr. Bernd accepts either "Kawasaki disease" or "incomplete/atypical Kawasaki disease" and gives extra credit for recognizing why infants under 6 months are high risk. To make the case truly incomplete, remove one feature (e.g., the hand/foot edema) from the patient file and from the case text in `worker.js`.

## Cost
Each session is roughly 15–30 short turns plus one long review on the balanced model. Because the case prompt is resent with every turn, expect somewhere under a dollar per student session; check current API pricing for exact figures. Set a monthly spend limit in the Anthropic Console to be safe.

## Editing the case later
All case content and examiner rules are in the `PROMPTS["dr-bernd"]` section of `worker.js`. Edit there and click **Deploy**; the page itself doesn't change.
