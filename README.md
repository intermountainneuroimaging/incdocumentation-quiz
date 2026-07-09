# Basic Math Quiz (LiaScript)

`math-quiz.md` is a self-contained [LiaScript](https://liascript.github.io/) course: three multiple-choice math questions with automatic scoring, retries, a final grade, and a button that emails the results directly to a teacher via [EmailJS](https://www.emailjs.com/).

Preview any changes at the [LiaScript LiveEditor](https://liascript.github.io/LiveEditor/) by pasting the file's contents into the editor pane.

## Setting up EmailJS

The "Email My Results to My Teacher" button on the Results slide sends email straight from the browser — no backend server required. To connect it to your own email account:

1. **Sign up** at [emailjs.com](https://www.emailjs.com/) (free tier is enough for a classroom).
2. **Connect an email account**: go to **Email Services → Add New Service**, pick your provider (e.g. Gmail), and authorize it. Note the **Service ID** it generates (looks like `service_xxxxxxx`).
3. **Create a template**: go to **Email Templates → Create New Template** and set these fields exactly:
   - **To Email**: `{{to_email}}`
   - **Subject**: `{{subject}}`
   - **Content**: `{{message}}`

   Save it and note the **Template ID** (looks like `template_xxxxxxx`).
4. **Get your public key**: go to **Account → General** and copy the **Public Key**.
5. **Restrict it to your domain**: go to **Account → Security** and add the domain where you'll host/share this course to the allowed origins list. This is the actual protection on the key (see note below) — do this before publishing anywhere public.

## Wiring the values into `math-quiz.md`

Open `math-quiz.md` and update the three values near the top of the intro `<script>` block (around line 155):

```js
window.EMAILJS_SERVICE_ID = "service_lrwwp98";
window.EMAILJS_TEMPLATE_ID = "template_1ojukti";
window.EMAILJS_PUBLIC_KEY = "PwQXw3u1xmYiZixXg";
```

Replace each string with the values from your own EmailJS account (Service ID, Template ID, and Public Key from the steps above).

The SDK itself is loaded once in the course header (near line 10):

```
script: https://cdn.jsdelivr.net/npm/@emailjs/browser@4/dist/email.min.js
```

No other code changes are needed — `mathQuizEmailResults()` (further down in the same script block) already calls `emailjs.send()` using these three values.

## A note on the public key

EmailJS's public key is designed to be visible in client-side code — that's why it's called a "public" key rather than a secret. It can't be hidden or hashed away, since the browser needs the literal value to make the API call. The real safeguard is the domain allowlist from step 5 above, which rejects requests from any origin you haven't approved, regardless of who has copied the key out of the page source.
