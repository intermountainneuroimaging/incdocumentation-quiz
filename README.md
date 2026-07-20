# Quiz Courses (LiaScript)

This project contains two self-contained [LiaScript](https://liascript.github.io/) courses:

- `math-quiz.md` — three multiple-choice math questions with automatic scoring, retries, a final grade, and a button that emails the results directly to a teacher via [EmailJS](https://www.emailjs.com/).
- `inc-flywheel-getting-started_quizlet_simple.md` — a scored INC Flywheel onboarding quiz plus an unscored drag-and-drop practice exercise. Completion is gated behind an 85% score (regardless of whether every question was answered), and confirmation is emailed to the student's own address via EmailJS (with INC staff CC'd via the template — see below).

Preview any changes at the [LiaScript LiveEditor](https://liascript.github.io/LiveEditor/) by pasting the file's contents into the editor pane.

## Setting up EmailJS

Both courses send email straight from the browser — no backend server required. Both currently share the same EmailJS account/template; to connect it to your own:

1. **Sign up** at [emailjs.com](https://www.emailjs.com/) (free tier is enough for a classroom).
2. **Connect an email account**: go to **Email Services → Add New Service**, pick your provider (e.g. Gmail), and authorize it. Note the **Service ID** it generates (looks like `service_xxxxxxx`).
3. **Create a template**: go to **Email Templates → Create New Template** and set these fields exactly:
   - **To Email**: `{{to_email}}`
   - **Subject**: `{{subject}}`
   - **Content**: `{{message}}`

   Save it and note the **Template ID** (looks like `template_xxxxxxx`).

   **For the INC Flywheel course specifically:** the student's own email goes in `to_email` (so completion results go to them for their own records), not to INC staff directly. To make sure the INC staff member who approves Flywheel access still sees every submission, hard-code their address in the template's **CC** field (in the EmailJS template editor, not in the course's JavaScript) — e.g. set CC to `staffmember@colorado.edu`. Every send will silently include them regardless of what the student enters as their own email.
4. **Get your public key**: go to **Account → General** and copy the **Public Key**.
5. **Restrict it to your domain**: go to **Account → Security** and add the domain where you'll host/share this course to the allowed origins list. This is the actual protection on the key (see note below) — do this before publishing anywhere public.

## Wiring the values into the course files

Each course has its own copy of the same three values, near the top of its intro `<script>` block — in `math-quiz.md` around line 155, and in `inc-flywheel-getting-started_quizlet_simple.md` around line 306:

```js
window.EMAILJS_SERVICE_ID = "service_lrwwp98";
window.EMAILJS_TEMPLATE_ID = "template_1ojukti";
window.EMAILJS_PUBLIC_KEY = "PwQXw3u1xmYiZixXg";
```

Replace each string with the values from your own EmailJS account (Service ID, Template ID, and Public Key from the steps above) in both files.

The SDK itself is loaded once in each course's header:

```
script: https://cdn.jsdelivr.net/npm/@emailjs/browser@4/dist/email.min.js
```

No other code changes are needed — `mathQuizEmailResults()` / `quizEmailResults()` (further down in each script block) already call `emailjs.send()` using these three values.

## A note on the public key

EmailJS's public key is designed to be visible in client-side code — that's why it's called a "public" key rather than a secret. It can't be hidden or hashed away, since the browser needs the literal value to make the API call. The real safeguard is the domain allowlist from step 5 above, which rejects requests from any origin you haven't approved, regardless of who has copied the key out of the page source. Note that the allowlist is a paid-plan feature on some EmailJS tiers — if it's unavailable on your plan, that's fine; the worst-case risk without it is quota abuse from another site reusing the key, not data exposure.

## Troubleshooting

### How to check the browser console and network requests

Both courses run entirely in the browser, so most problems can be diagnosed with dev tools:

1. Open dev tools: **Cmd+Option+I** on Mac (or right-click the page → **Inspect**).
2. For JavaScript errors, use the **Console** tab.
3. For a failed EmailJS send specifically, use the **Network** tab:
   - Click the **Send** / **Email My Results** button again with the Network tab open so the request gets captured.
   - Look for a row named `send` (not `email.min.js`) with method `POST` to `api.emailjs.com`. Filter the list by typing `send` if it's hard to spot.
   - Click that row, then open its **Response** (or **Preview**) tab — this has the actual short error text. Our own code only ever shows a generic "Something went wrong sending the email" message on screen, so this is the only place to see the real reason.

### "Check Answer" buttons do nothing / console shows `window.quizCheck... is not a function`

This is not a content bug — it's a rendering-order quirk in some LiaScript preview tools (observed in the VS Code LiaScript extension). The setup `<script>` block near the top of the course (right after "Before You Begin") defines all the `window.quizCheck*` functions, but if the preview jumps straight to a specific line/section (e.g. via a "sync scroll to cursor" feature) instead of rendering top-to-bottom, that script never mounts and the functions stay undefined.

**Fix:** put your cursor at line 1, close and reopen the preview, and scroll down manually through the course from the top rather than jumping/searching to a question. This doesn't affect the real published site (GitHub Pages), since visitors naturally load from the top.

### Email fails with "Something went wrong sending the email. Please try again."

Find the real error using the Network tab steps above. The one we've hit in practice:

- **Response body says `Gmail_API: Invalid grant. Please reconnect your Gmail account` (HTTP 412):** the OAuth connection between EmailJS and the connected Gmail account has expired or been revoked. Fix:
  1. EmailJS dashboard → **Email Services**.
  2. Click the Gmail service → **Edit** → **Disconnect**.
  3. **Connect Account** again, and click **Allow** on every permission in the Google popup (don't close it early).
  4. Save, then retry the send.

  This is usually infrequent, but if it starts happening every week or so, that points to EmailJS's own Gmail OAuth app being in Google's unverified "Testing" publishing status, which causes Google to force-expire tokens after 7 days — that's on EmailJS's side, not something fixable from this repo. If it becomes a recurring nuisance, the practical options are periodic manual reconnects, or switching the connected service to a non-OAuth provider (e.g. an SMTP-based service) that doesn't have this failure mode.

### Course doesn't render at all / stuck on "Loading" when testing via a local server + hosted LiaScript URL

If you preview by running a local static server and loading `https://liascript.github.io/course/?http://localhost:PORT/file.md`, be aware that a browser will not let an `https://` page fetch `http://localhost` directly (mixed content). LiaScript may silently retry through a public third-party CORS proxy (`api.allorigins.win`) instead — which means your file's contents (including any real credentials) would be sent to that third party. **Avoid this preview method for files with real EmailJS keys.** Instead:
- Test structural/syntax changes with a sanitized copy pasted into the [LiveEditor](https://liascript.github.io/LiveEditor/), or
- Test the real file using a local editor preview (e.g. the VS Code LiaScript extension) that renders from disk, or
- Test against the actual deployed GitHub Pages URL once published.

### Verifying the GitHub Pages deployment

`.github/workflows/publish.yml` runs on every push to `main`: it exports both courses to static HTML and pushes the result to a `gh-pages` branch. That workflow succeeding does **not** by itself mean the site is live — GitHub Pages has to be separately enabled:

1. Check the workflow ran: repo → **Actions** tab → confirm the latest "Publish LiaScript Courses" run succeeded.
2. Check Pages is turned on: repo → **Settings → Pages** → **Source** should be "Deploy from a branch" with branch `gh-pages`, folder `/ (root)`.
3. If Pages is off, an org admin needs to enable Pages for the organization first if it's greyed out.
4. Once live, the site is at `https://<org>.github.io/<repo>/`, with each course under its own subfolder (e.g. `/inc-flywheel/`, `/math-quiz/`).
