# Quiz Courses (LiaScript)

This project contains two self-contained [LiaScript](https://liascript.github.io/) courses:

- `math-quiz.md` — three multiple-choice math questions with automatic scoring, retries, a final grade, and a button that emails the results directly to a teacher via [EmailJS](https://www.emailjs.com/).
- `inc-flywheel-getting-started_quizlet_simple.md` — a 16-question INC Flywheel onboarding quiz. Completion is gated behind an 85% score, and confirmation is emailed to the student's own address via EmailJS (with INC staff CC'd via the template — see below).

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

EmailJS's public key is designed to be visible in client-side code — that's why it's called a "public" key rather than a secret. It can't be hidden or hashed away, since the browser needs the literal value to make the API call. The real safeguard is the domain allowlist from step 5 above, which rejects requests from any origin you haven't approved, regardless of who has copied the key out of the page source.
