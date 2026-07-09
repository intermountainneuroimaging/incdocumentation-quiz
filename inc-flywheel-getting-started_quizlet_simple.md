<!--
author:   Intermountain Neuroimaging Consortium
email:    amy.hegarty@colorado.edu
version:  1.0.0
language: en
narrator: US English Female

comment:  A shortened interactive training course covering INC Flywheel
          Getting Started — Overview, At the Scanner, Navigating the UI,
          and How to Cite Us. Questions use a check-answer/retry format
          with automatic scoring. Completion is confirmed by emailing
          results directly from the browser via EmailJS.

logo:     https://inc-documentation.readthedocs.io/en/dev/_static/INC_center.png

script: https://cdn.jsdelivr.net/npm/@emailjs/browser@4/dist/email.min.js

style:
  .quiz-options {
    display: flex;
    flex-direction: column;
    gap: 0.4em;
    margin: 0.75em 0;
  }
  .quiz-option {
    display: inline-flex;
    align-items: center;
    gap: 0.5em;
    padding: 0.4em 0.6em;
    border: 2px solid #888;
    border-radius: 8px;
    width: fit-content;
  }
  .quiz-btn {
    padding: 0.5em 1em;
    border: 2px solid #888;
    border-radius: 8px;
    background: #f5f5f5;
    cursor: pointer;
    font-size: 1em;
  }
  .quiz-options .quiz-btn {
    margin-top: 0.3em;
    align-self: flex-start;
  }
  .quiz-btn:disabled {
    cursor: default;
    opacity: 0.85;
  }
  .quiz-correct {
    border-color: #2e7d32 !important;
    background: #c8e6c9 !important;
  }
  .quiz-wrong {
    border-color: #c62828 !important;
    background: #ffcdd2 !important;
  }
  .quiz-field {
    display: flex;
    align-items: center;
    gap: 0.5em;
    margin: 0.4em 0;
  }
  .quiz-field input {
    flex: 1;
    padding: 0.4em 0.6em;
    border: 2px solid #888;
    border-radius: 8px;
    font-size: 1em;
    max-width: 320px;
  }
  #completion-gate textarea {
    display: block;
    width: 100%;
    max-width: 400px;
    padding: 0.4em 0.6em;
    border: 2px solid #888;
    border-radius: 8px;
    font-size: 1em;
    font-family: inherit;
    margin: 0.4em 0;
  }
-->

# INC Flywheel — Getting Started Training

Welcome to the **Intermountain Neuroimaging Consortium (INC) Flywheel** onboarding course.

This short training covers everything you need to know to get started using the Flywheel platform at INC — from setting up your study to scanning your first participant and finding your data.

> **📖 Full documentation:** This course is a quick summary. For the complete reference, visit [inc-documentation.readthedocs.io](https://inc-documentation.readthedocs.io/en/latest/).

> **⏱ Estimated time:** 10–15 minutes
>
> **📋 Modules:**
>
> 1. Overview — What is Flywheel? How is it deployed at INC?
> 2. Getting your MRI Data
> 3. Navigating the User Interface
> 4. How to Cite Us
>
> At the end, you'll be prompted to send a completion confirmation email. DO NOT FORGET this step - otherwise you will not be added to the platform.

---

## Before You Begin

Please enter your **CU Boulder IdentiKey** below. This is used to associate your quiz responses with your account in our training records.

> **ℹ️ What is an IdentiKey?** Your IdentiKey is your CU Boulder username... the part before `@colorado.edu` in your university email address (e.g. `jodo1234`). Don't have an identikey? Contact INC staff to request one.

<div class="quiz-options">
  <div class="quiz-field">
    <label for="identikey-input">IdentiKey:</label>
    <input type="text" id="identikey-input" placeholder="e.g. jodo1234">
  </div><br>
  <button class="quiz-btn" onclick="window.quizSaveIdentiKey()">Save IdentiKey</button>
</div>

<p id="identikey-status"></p>

> **✅ Once you've entered your IdentiKey above and pressed Save, scroll down to begin the training.**

<script>
window.quiz = {};

window.QUIZ_QUESTIONS = [
  { id: "q1",  text: "Where is INC's Flywheel imaging data stored?" },
  { id: "q2",  text: "Does Flywheel require pre-registering participants before a scan?" },
  { id: "q3",  text: "Can the PII-to-Subject-ID key be stored within Flywheel?" },
  { id: "q4",  text: "Which is NOT one of Flywheel's core value propositions?" },
  { id: "q5",  text: "Correct Accession Number format at the scanner console?" },
  { id: "q6",  text: "Where does data go if the accession number is entered incorrectly?" },
  { id: "q7",  text: "Recommended naming convention for subject/session labels?" },
  { id: "q8",  text: "Must the Scanner Requisition Form be submitted before each scan?" },
  { id: "q9",  text: "Correct order of the Flywheel data hierarchy?" },
  { id: "q10", text: "Where can you view all accessible projects in Flywheel?" },
  { id: "q11", text: "What is an \"Acquisition\" in Flywheel?" },
  { id: "q12", text: "Most likely cause if you cannot see data in your project?" },
  { id: "q13", text: "How do CU Boulder users log into Flywheel?" },
  { id: "q14", text: "What identifier should you use to cite INC?" },
  { id: "q15", text: "Which two collaborators must also be acknowledged?" },
  { id: "q16", text: "What is INC's RRID number?" }
];

function quizClearHighlight(container) {
  container.querySelectorAll("label, input").forEach(function (el) {
    el.classList.remove("quiz-correct", "quiz-wrong");
  });
}

window.quizCheckChoice = function (qid, btn) {
  var container = document.getElementById(qid + "-options");
  var feedback = document.getElementById("feedback-" + qid);
  quizClearHighlight(container);

  var selected = container.querySelector("input[type=radio]:checked");
  if (!selected) {
    if (feedback) {
      feedback.textContent = "Please select an answer first.";
      feedback.style.color = "#b26a00";
      feedback.style.fontWeight = "600";
    }
    return;
  }

  var correct = selected.getAttribute("data-correct") === "true";
  window.quiz[qid] = correct;

  container.querySelectorAll("input[type=radio]").forEach(function (r) {
    var label = r.closest("label");
    if (r.getAttribute("data-correct") === "true") {
      label.classList.add("quiz-correct");
    }
    if (r === selected && !correct) {
      label.classList.add("quiz-wrong");
    }
  });

  if (feedback) {
    feedback.textContent = correct
      ? "Correct!"
      : "Not quite — try a different option and check again.";
    feedback.style.color = correct ? "#2e7d32" : "#c62828";
    feedback.style.fontWeight = "600";
  }
};

window.quizCheckMulti = function (qid, btn) {
  var container = document.getElementById(qid + "-options");
  var feedback = document.getElementById("feedback-" + qid);
  quizClearHighlight(container);

  var boxes = container.querySelectorAll("input[type=checkbox]");
  var anyChecked = false;
  boxes.forEach(function (b) {
    if (b.checked) {
      anyChecked = true;
    }
  });

  if (!anyChecked) {
    if (feedback) {
      feedback.textContent = "Please select at least one answer first.";
      feedback.style.color = "#b26a00";
      feedback.style.fontWeight = "600";
    }
    return;
  }

  var correct = true;
  boxes.forEach(function (b) {
    var shouldBeChecked = b.getAttribute("data-correct") === "true";
    if (b.checked !== shouldBeChecked) {
      correct = false;
    }
  });
  window.quiz[qid] = correct;

  boxes.forEach(function (b) {
    var label = b.closest("label");
    if (b.getAttribute("data-correct") === "true") {
      label.classList.add("quiz-correct");
    } else if (b.checked) {
      label.classList.add("quiz-wrong");
    }
  });

  if (feedback) {
    feedback.textContent = correct
      ? "Correct!"
      : "Not quite — try again.";
    feedback.style.color = correct ? "#2e7d32" : "#c62828";
    feedback.style.fontWeight = "600";
  }
};

window.quizCheckText = function (qid, btn) {
  var input = document.getElementById(qid + "-input");
  var feedback = document.getElementById("feedback-" + qid);
  input.classList.remove("quiz-correct", "quiz-wrong");

  var answer = input.value.trim().toLowerCase();
  if (!answer) {
    if (feedback) {
      feedback.textContent = "Please enter an answer first.";
      feedback.style.color = "#b26a00";
      feedback.style.fontWeight = "600";
    }
    return;
  }

  var accepted = JSON.parse(input.getAttribute("data-accepted"));
  var correct = accepted.indexOf(answer) !== -1;
  window.quiz[qid] = correct;
  input.classList.add(correct ? "quiz-correct" : "quiz-wrong");

  if (feedback) {
    feedback.textContent = correct
      ? "Correct!"
      : "Not quite — try again.";
    feedback.style.color = correct ? "#2e7d32" : "#c62828";
    feedback.style.fontWeight = "600";
  }
};

window.quizSummary = function () {
  var total = window.QUIZ_QUESTIONS.length;
  var rows = window.QUIZ_QUESTIONS.map(function (q) {
    var v = window.quiz[q.id];
    var result = v === true ? "Correct" : v === false ? "Incorrect" : "Not answered yet";
    return { question: q.text, result: result };
  });
  var correctCount = rows.filter(function (r) { return r.result === "Correct"; }).length;
  var answeredCount = rows.filter(function (r) { return r.result !== "Not answered yet"; }).length;
  var pct = Math.round((correctCount / total) * 100);
  var grade =
    pct === 100 ? "A+" :
    pct >= 67   ? "B"  :
    pct >= 34   ? "C"  :
                  "F";

  return {
    total: total,
    correctCount: correctCount,
    answeredCount: answeredCount,
    pct: pct,
    grade: grade,
    rows: rows
  };
};

window.quizSaveIdentiKey = function () {
  var input = document.getElementById("identikey-input");
  var status = document.getElementById("identikey-status");
  var key = input.value.trim().toLowerCase();

  if (!key) {
    if (status) {
      status.textContent = "Please enter your IdentiKey first.";
      status.style.color = "#b26a00";
      status.style.fontWeight = "600";
    }
    return;
  }

  sessionStorage.setItem("lia_identitykey", key);
  window.identityKey = key;

  if (status) {
    status.textContent = "Saved! You can scroll down to begin the training.";
    status.style.color = "#2e7d32";
    status.style.fontWeight = "600";
  }
};

window.EMAILJS_SERVICE_ID = "service_lrwwp98";
window.EMAILJS_TEMPLATE_ID = "template_1ojukti";
window.EMAILJS_PUBLIC_KEY = "PwQXw3u1xmYiZixXg";
window.QUIZ_PASSING_PCT = 85;

window.renderCompletionGate = function () {
  var el = document.getElementById("completion-gate");
  if (!el) {
    return;
  }

  var summary = window.quizSummary();

  if (summary.answeredCount < summary.total) {
    el.innerHTML =
      "<p>You haven't answered all the questions yet. Please scroll back up, answer every question above, and return here.</p>";
    return;
  }

  if (summary.pct < window.QUIZ_PASSING_PCT) {
    el.innerHTML =
      "<p><strong>You scored " + summary.pct + "%.</strong> A score of at least " + window.QUIZ_PASSING_PCT +
      "% is required to confirm completion. Please scroll back up, review the questions you missed, and try again.</p>";
    return;
  }

  el.innerHTML =
    "<p>Great job — you scored " + summary.pct + "%! Enter your email address and sponsoring PI below (used for your own records) " +
    "and add any comments or questions, then click the button to send your completion confirmation.</p>" +
    "<div class=\"quiz-options\">" +
      "<div class=\"quiz-field\">" +
        "<label for=\"student-email\">Your email:</label>" +
        "<input type=\"email\" id=\"student-email\" placeholder=\"you@example.com\">" +
      "</div><br>" +
      "<div class=\"quiz-field\">" +
        "<label for=\"pi-input\">Sponsoring Principal Investigator:</label>" +
        "<input type=\"text\" id=\"pi-input\" placeholder=\"e.g. Dr. Jane Smith\">" +
      "</div><br>" +
      "<div class=\"quiz-field\">" +
        "<label for=\"comments-input\">Comments / questions (optional):</label>" +
      "</div>" +
      "<textarea id=\"comments-input\" rows=\"4\" placeholder=\"Anything you'd like INC staff to know...\"></textarea><br>" +
      "<button class=\"quiz-btn\" onclick=\"window.quizEmailResults()\">Send My Completion Confirmation</button>" +
    "</div>" +
    "<p id=\"email-status\"></p>";
};

window.quizEmailResults = function () {
  var status = document.getElementById("email-status");
  var identityKey = sessionStorage.getItem("lia_identitykey") || window.identityKey || "";

  if (!identityKey) {
    if (status) {
      status.textContent = "Please scroll back to \"Before You Begin\" and save your IdentiKey first.";
      status.style.color = "#b26a00";
      status.style.fontWeight = "600";
    }
    return;
  }

  var summary = window.quizSummary();
  if (summary.pct < window.QUIZ_PASSING_PCT) {
    if (status) {
      status.textContent = "A score of at least " + window.QUIZ_PASSING_PCT + "% is required to send your completion confirmation.";
      status.style.color = "#b26a00";
      status.style.fontWeight = "600";
    }
    return;
  }

  var emailInput = document.getElementById("student-email");
  var studentEmail = emailInput ? emailInput.value.trim() : "";
  if (!studentEmail || studentEmail.indexOf("@") === -1) {
    if (status) {
      status.textContent = "Please enter a valid email address for your records.";
      status.style.color = "#b26a00";
      status.style.fontWeight = "600";
    }
    return;
  }

  var piInput = document.getElementById("pi-input");
  var sponsoringPI = piInput ? piInput.value.trim() : "";
  if (!sponsoringPI) {
    if (status) {
      status.textContent = "Please enter your sponsoring Principal Investigator.";
      status.style.color = "#b26a00";
      status.style.fontWeight = "600";
    }
    return;
  }

  if (typeof emailjs === "undefined") {
    if (status) {
      status.textContent = "Email service is still loading — please wait a moment and try again.";
      status.style.color = "#b26a00";
      status.style.fontWeight = "600";
    }
    return;
  }

  var commentsInput = document.getElementById("comments-input");
  var comments = commentsInput ? commentsInput.value.trim() : "";
  var completedOn = new Date().toLocaleString();

  var bodyLines = [];
  bodyLines.push("INC Flywheel Getting Started training - completion confirmation");
  bodyLines.push("");
  bodyLines.push("IdentiKey: " + identityKey);
  bodyLines.push("Completed on: " + completedOn);
  bodyLines.push("Score: " + summary.correctCount + " / " + summary.total + " (" + summary.pct + "%)");
  bodyLines.push("Sponsoring PI: " + sponsoringPI);

  if (comments) {
    bodyLines.push("");
    bodyLines.push("Comments/Questions:");
    bodyLines.push(comments);
  }

  var subject = "INC Flywheel Training Completed - " + identityKey;

  if (status) {
    status.textContent = "Sending…";
    status.style.color = "#555";
    status.style.fontWeight = "600";
  }

  emailjs.init({ publicKey: window.EMAILJS_PUBLIC_KEY });

  emailjs.send(window.EMAILJS_SERVICE_ID, window.EMAILJS_TEMPLATE_ID, {
    to_email: studentEmail,
    subject: subject,
    message: bodyLines.join("\n")
  }).then(
    function () {
      if (status) {
        status.textContent = "Sent! A copy of your completion confirmation has been emailed to you.";
        status.style.color = "#2e7d32";
        status.style.fontWeight = "600";
      }
    },
    function (error) {
      if (status) {
        status.textContent = "Something went wrong sending the email. Please try again.";
        status.style.color = "#c62828";
        status.style.fontWeight = "600";
      }
    }
  );
};

""
</script>

---

## 1. Overview — What is Flywheel? How is Flywheel Deployed at INC?

**Flywheel.io** is an imaging data management platform used to receive, curate, manage, and analyze neuroimaging data. INC hosts a **cloud deployment** of Flywheel.io: your imaging data and metadata are stored on **AWS cloud infrastructure (S3)**, while data analysis and computation run on **CU Boulder Research Computing's (CURC)** on-premise HPC clusters, **Blanca** and **Alpine**, backed by **PetaLibrary** storage.

![Flywheel computing architecture at CU Boulder](https://inc-documentation.readthedocs.io/en/latest/_images/facilities_data_analysis.jpg)<!-- style="width: 70%" -->

> **Note:** Before starting a new or existing study in Flywheel, you must set up a meeting with INC Staff to discuss your specific needs and obtain a copy of INC's **Memorandum of Use (MOU)**.

### Why use Flywheel?

Flywheel is built to **organize**, **store**, and **share** research and medical imaging data:

- **Organize**: a consistent `Group → Project → Subject → Session → Acquisition` hierarchy, with structured metadata captured automatically at every level.
- **Store**: secure, versioned, automatically backed-up storage with full provenance: every action taken on your data is logged (who, what, when, how).
- **Share**: fine-grained user permissions, Collections, and Data Views make it easy to collaborate and to meet NIH/NSF data-sharing requirements, including submission to the NDA.

### Key things to know

- INC hosts a **3T Prisma Fit MR scanner** whose data flows directly into Flywheel.
- Flywheel does **not** require pre-registration of participants before scanning — INC recommends checking Flywheel *after* the scan session to catch any typos.
- **No personally identifiable information (PII)** may ever exist on the Flywheel platform. Subject IDs must be coded, and the key to that coding must be stored outside Flywheel (e.g. in REDCap or on paper).

---

### 🧠 Check Your Understanding — Overview

**Q1:** Where is INC's Flywheel imaging data stored?

<div class="quiz-options" id="q1-options">
  <label class="quiz-option"><input type="radio" name="q1"> On the researcher's local workstation</label><br>
  <label class="quiz-option"><input type="radio" name="q1" data-correct="true"> On AWS cloud infrastructure (S3)</label><br>
  <label class="quiz-option"><input type="radio" name="q1"> Directly on the MRI scanner console</label><br>
  <label class="quiz-option"><input type="radio" name="q1"> On a personal Google Drive or Dropbox account</label><br>
  <button class="quiz-btn" onclick="window.quizCheckChoice('q1', this)">Check Answer</button>
</div>

<p id="feedback-q1"></p>

---

**Q2:** Does Flywheel require you to pre-register participants before a scan session?

<div class="quiz-options" id="q2-options">
  <label class="quiz-option"><input type="radio" name="q2"> Yes — participants must be registered before scanning begins</label><br>
  <label class="quiz-option"><input type="radio" name="q2" data-correct="true"> No — INC recommends checking Flywheel after the scan to catch any typos</label><br>
  <label class="quiz-option"><input type="radio" name="q2"> Yes — but only for longitudinal studies</label><br>
  <label class="quiz-option"><input type="radio" name="q2"> Only for new participants, not returning ones</label><br>
  <button class="quiz-btn" onclick="window.quizCheckChoice('q2', this)">Check Answer</button>
</div>

<p id="feedback-q2"></p>

---

**Q3:** True or False: The key linking coded Subject IDs to personally identifiable information (PII) **may** be stored somewhere within Flywheel.

<div class="quiz-options" id="q3-options">
  <label class="quiz-option"><input type="radio" name="q3"> True</label><br>
  <label class="quiz-option"><input type="radio" name="q3" data-correct="true"> False</label><br>
  <button class="quiz-btn" onclick="window.quizCheckChoice('q3', this)">Check Answer</button>
</div>

<p id="feedback-q3"></p>

---

**Q4:** Which of the following is **NOT** one of Flywheel's core value propositions for managing imaging data?

<div class="quiz-options" id="q4-options">
  <label class="quiz-option"><input type="radio" name="q4"> Organize — a consistent, searchable data hierarchy</label><br>
  <label class="quiz-option"><input type="radio" name="q4"> Store — versioned, backed-up storage with full provenance</label><br>
  <label class="quiz-option"><input type="radio" name="q4"> Share — fine-grained permissions, Collections, and Data Views</label><br>
  <label class="quiz-option"><input type="radio" name="q4" data-correct="true"> Compute — Flywheel replaces the need for any HPC or compute cluster</label><br>
  <button class="quiz-btn" onclick="window.quizCheckChoice('q4', this)">Check Answer</button>
</div>

<p id="feedback-q4"></p>

---

## 2. At the Scanner

We use automated transfer protocols to send DICOM data collected on the MRI scanner to the Flywheel web instance. Here at INC, we encode the **landing spot** for the DICOM data within two of the DICOM's header fields: `refering physician` and `accession number`. The MRI technician running your scan can help answer questions about how to set these fields to ensure the scanning session's data ends up in the right spot.

Here are a few things to keep in mind:

- The `refering physician` refers to the principal investigator for the lab, and is typically set at the beginning of the study and not changed. 

- Getting the **Routing String** stored in the `accession number` field correct at the scanner is the **most critical step** in getting your data into Flywheel correctly. If this is entered incorrectly, your data will not land in the right project.

### The Routing String Naming Convention

When your participant is set up on the scanner console, you **must** enter the following **Routing Sring** into the field labelled `accession number`:

```
<project-label> / <subject-label> / <session-label>
```

For example, for a study called `MyStudy`, participant `sub-101`, session `ses-01`:

```
MyStudy / sub-101 / ses-01
```

> **💡 Tip:** INC strongly recommends using **BIDS-compliant** naming for subject and session labels (e.g. `sub-101`, `ses-01`).

### What if the naming goes wrong?

If the `refering physician` or `accession number` is entered incorrectly, all acquisitions from that session will be placed in an **"Unknown"** or **"Unsorted (PI specific)" project**. Study teams must:

1. Check Flywheel promptly after each scan session
2. Contact INC staff immediately if a session is missing or misrouted
3. INC staff will correct the labelling error

### Additional scanner information

Beyond the `accession number`, a small amount of additional participant/session metadata is collected via the **Scanner Requisition Form**, which must be submitted before each scan session.

---

### 🧠 Check Your Understanding — At the Scanner

**Q5:** What is the correct format for the Accession Number field at the scanner console?

<div class="quiz-options" id="q5-options">
  <label class="quiz-option"><input type="radio" name="q5"> &lt;subject-label&gt; / &lt;session-label&gt;</label><br>
  <label class="quiz-option"><input type="radio" name="q5"> &lt;group-label&gt; / &lt;project-label&gt;</label><br>
  <label class="quiz-option"><input type="radio" name="q5" data-correct="true">  &lt;project-label&gt; / &lt;subject-label&gt; / &lt;session-label&gt;</label><br>
  <label class="quiz-option"><input type="radio" name="q5"> &lt;session-label&gt; / &lt;subject-label&gt; / &lt;project-label&gt;</label><br>
  <button class="quiz-btn" onclick="window.quizCheckChoice('q5', this)">Check Answer</button>
</div>

<p id="feedback-q5"></p>

---

**Q6:** Where does data go if the `accession number` is entered incorrectly?

<div class="quiz-options" id="q6-options">
  <label class="quiz-option"><input type="radio" name="q6"> It is automatically deleted after 24 hours</label><br>
  <label class="quiz-option"><input type="radio" name="q6"> It is emailed to the PI</label><br>
  <label class="quiz-option"><input type="radio" name="q6" data-correct="true"> It lands in an "Unsorted" project in the PI's Flywheel Group</label><br>
  <label class="quiz-option"><input type="radio" name="q6"> It is renamed automatically to match Flywheel's naming convention</label><br>
  <button class="quiz-btn" onclick="window.quizCheckChoice('q6', this)">Check Answer</button>
</div>

<p id="feedback-q6"></p>

---

**Q7:** What naming convention does INC recommend for subject and session labels?

<div class="quiz-options" id="q7-options">
  <label class="quiz-option"><input type="radio" name="q7"> Participant's initials followed by date of birth</label><br>
  <label class="quiz-option"><input type="radio" name="q7"> Sequential numbers with no prefix (e.g. 001, 002)</label><br>
  <label class="quiz-option"><input type="radio" name="q7" data-correct="true"> BIDS-compliant labels (e.g. sub-101, ses-01)</label><br>
  <label class="quiz-option"><input type="radio" name="q7"> Free-form text with no restrictions</label><br>
  <button class="quiz-btn" onclick="window.quizCheckChoice('q7', this)">Check Answer</button>
</div>

<p id="feedback-q7"></p>

---

**Q8:** True or False: The Scanner Requisition Form must be submitted before each scan session.

<div class="quiz-options" id="q8-options">
  <label class="quiz-option"><input type="radio" name="q8" data-correct="true"> True</label><br>
  <label class="quiz-option"><input type="radio" name="q8"> False</label><br>
  <button class="quiz-btn" onclick="window.quizCheckChoice('q8', this)">Check Answer</button>
</div>

<p id="feedback-q8"></p>

---

## 3. Navigating the User Interface

Once logged into Flywheel, you will see the **Projects page** — your primary hub for navigating your data.

### Logging Into Flywheel

Flywheel uses **CILogon** to manage access — the same federated-login system used by most academic institutions. CU Boulder users log in with their **University of Colorado credentials** (IdentiKey); external collaborators can log in with an existing CILogon-connected account (e.g. from their home institution or ORCID), or request a CU Boulder Affiliate Account through their UCB collaborator.

![Flywheel landing page](https://inc-documentation.readthedocs.io/en/latest/_images/logging_in_1.png)<!-- style="width: 70%" -->

> **💡 Tip:** Having trouble logging in? Try changing browsers.

### The Flywheel Data Hierarchy

Flywheel organizes data into a strict hierarchy:

```
Group  →  Project  →  Subject  →  Session  →  Acquisition
```

| Level | Description |
|---|---|
| **Group** | The top-level container, typically corresponding to a PI or lab. Visible in the second column of the Projects list. |
| **Project** | A single study or dataset. Contains subjects, sessions, files, and metadata. |
| **Subject** | Bundles all sessions for one participant. Identified by a unique Subject ID. |
| **Session** | One visit/scan day for a participant. |
| **Acquisition** | A single scanner sequence within a session. Holds files and metadata. |

![Basic schematic describing Flywheel architecture](https://inc-documentation.readthedocs.io/en/latest/_images/flywheel_architecture.png)<!-- style="width: 70%" -->

### Navigating Projects

All accessible projects appear in the **left-hand ribbon** on the Projects page. Key features of a Project include:

- Description
- Project files
- Subjects and sessions list
- Custom data views
- Metadata

> **⚠️ Can't see your data?** If a session is missing from your project, the most likely cause is an incorrectly entered Accession Number at the scanner — causing data to land in the "Unsorted" project instead.

### Viewing Sessions and Subjects

From within a project, the **Sessions panel** lists all scan sessions sorted by date, with a summary of Subject ID and Session ID.

To switch to a subject-centric view, select the **Subjects icon** within the project.

### Collections and Data Views

**Collections** let you curate and share a subset of sessions across projects — for example, a "Radiologist Review" collection — without duplicating data or granting broader access than intended. **Data Views** let you extract and export metadata (age, sex, acquisition info, and more) across a project for statistical analysis or reporting.

![Flywheel collections panel](https://inc-documentation.readthedocs.io/en/latest/_images/collections_1.png)<!-- style="width: 70%" -->

---

### 🧠 Check Your Understanding — Navigating the UI

**Q9:** What is the correct order of the Flywheel data hierarchy?

<div class="quiz-options" id="q9-options">
  <label class="quiz-option"><input type="radio" name="q9"> Project → Group → Subject → Acquisition → Session</label><br>
  <label class="quiz-option"><input type="radio" name="q9"> Subject → Session → Project → Group → Acquisition</label><br>
  <label class="quiz-option"><input type="radio" name="q9" data-correct="true"> Group → Project → Subject → Session → Acquisition</label><br>
  <label class="quiz-option"><input type="radio" name="q9"> Group → Subject → Project → Acquisition → Session</label><br>
  <button class="quiz-btn" onclick="window.quizCheckChoice('q9', this)">Check Answer</button>
</div>

<p id="feedback-q9"></p>

---

**Q10:** Where can you view all of your accessible projects in Flywheel?

<div class="quiz-options" id="q10-options">
  <label class="quiz-option"><input type="radio" name="q10"> In the top navigation menu bar</label><br>
  <label class="quiz-option"><input type="radio" name="q10" data-correct="true"> In the left-hand ribbon on the Projects page</label><br>
  <label class="quiz-option"><input type="radio" name="q10"> In a dropdown under your user profile</label><br>
  <label class="quiz-option"><input type="radio" name="q10"> In the Admin settings panel</label><br>
  <button class="quiz-btn" onclick="window.quizCheckChoice('q10', this)">Check Answer</button>
</div>

<p id="feedback-q10"></p>

---

**Q11:** What is an "Acquisition" in Flywheel?

<div class="quiz-options" id="q11-options">
  <label class="quiz-option"><input type="radio" name="q11"> A group of projects belonging to one PI</label><br>
  <label class="quiz-option"><input type="radio" name="q11"> A participant's full set of visits across all sessions</label><br>
  <label class="quiz-option"><input type="radio" name="q11" data-correct="true"> A single scanner sequence within a session, holding files and metadata</label><br>
  <label class="quiz-option"><input type="radio" name="q11"> A metadata tag applied to a project</label><br>
  <button class="quiz-btn" onclick="window.quizCheckChoice('q11', this)">Check Answer</button>
</div>

<p id="feedback-q11"></p>

---

**Q12:** If you cannot see data in your project, what is the most likely cause?

<div class="quiz-options">
  <div class="quiz-field">
    <input type="text" id="q12-input" data-accepted='["accession number"]' placeholder="Type your answer">
  </div><br>
  <button class="quiz-btn" onclick="window.quizCheckText('q12', this)">Check Answer</button>
</div>

<p id="feedback-q12"></p>

---

**Q13:** How do CU Boulder users log into Flywheel?

<div class="quiz-options" id="q13-options">
  <label class="quiz-option"><input type="radio" name="q13"> With a separate Flywheel-specific username and password</label><br>
  <label class="quiz-option"><input type="radio" name="q13" data-correct="true"> With their University of Colorado credentials via CILogon</label><br>
  <label class="quiz-option"><input type="radio" name="q13"> By requesting a one-time access code from INC staff</label><br>
  <label class="quiz-option"><input type="radio" name="q13"> Flywheel does not require login for CU Boulder users</label><br>
  <button class="quiz-btn" onclick="window.quizCheckChoice('q13', this)">Check Answer</button>
</div>

<p id="feedback-q13"></p>

---

## 4. How to Cite Us

If INC's Flywheel platform has contributed to your publication, you are required to acknowledge the consortium and its collaborators.

### Citation Requirements

**Cite INC using its Research Resource Identifier (RRID):**

> Intermountain Neuroimaging Consortium, RRID: **SCR_025079**

**Also acknowledge the following collaborators:**

- **CU Boulder Research Computing (CURC)** — the infrastructure on which Flywheel is deployed
- **Flywheel.io** — without whose continued support this platform would not be possible

> **📬 Getting started?** Contact INC to request a copy of the Memorandum of Use and set up a one-on-one consultation.

---

### 🧠 Check Your Understanding — How to Cite Us

**Q14:** What identifier should you use to cite INC in a publication?

<div class="quiz-options" id="q14-options">
  <label class="quiz-option"><input type="radio" name="q14"> A DOI assigned per study</label><br>
  <label class="quiz-option"><input type="radio" name="q14"> The PI's ORCID number</label><br>
  <label class="quiz-option"><input type="radio" name="q14" data-correct="true"> INC's Research Resource Identifier (RRID): SCR_025079</label><br>
  <label class="quiz-option"><input type="radio" name="q14"> A URL to the INC website</label><br>
  <button class="quiz-btn" onclick="window.quizCheckChoice('q14', this)">Check Answer</button>
</div>

<p id="feedback-q14"></p>

---

**Q15:** Which TWO collaborators must also be acknowledged alongside INC? Select all that apply.

<div class="quiz-options" id="q15-options">
  <label class="quiz-option"><input type="checkbox" name="q15" data-correct="true"> CU Boulder Research Computing (CURC)</label><br>
  <label class="quiz-option"><input type="checkbox" name="q15" data-correct="true"> Flywheel.io</label><br>
  <label class="quiz-option"><input type="checkbox" name="q15"> Amazon Web Services</label><br>
  <label class="quiz-option"><input type="checkbox" name="q15"> CU Anschutz Medical Campus</label><br>
  <button class="quiz-btn" onclick="window.quizCheckMulti('q15', this)">Check Answer</button>
</div>

<p id="feedback-q15"></p>

---

**Q16:** What is INC's RRID number?

<div class="quiz-options">
  <div class="quiz-field">
    <input type="text" id="q16-input" data-accepted='["scr_025079"]' placeholder="Type your answer">
  </div><br>
  <button class="quiz-btn" onclick="window.quizCheckText('q16', this)">Check Answer</button>
</div>

<p id="feedback-q16"></p>

---

## 🧠 Bonus: Brain Souvenirs

As a thank-you for participating in research, INC generates **"brain souvenir"** images for study participants.

### Electronic Souvenirs

Electronic brain souvenirs are generated **automatically for all study participants** — no gear needs to be manually launched. Lab staff should access and download the souvenir from the **list of session analyses** for that participant's session.

![Example electronic brain souvenir — sagittal MRI slice](https://inc-documentation.readthedocs.io/en/latest/_images/brain_souvenir_example.jpg)<!-- style="width: 70%" -->

> **📤 Sharing with participants:** Flywheel does not email or share the souvenir on your behalf. Communicate with the participant **directly** about how you'll share their electronic souvenir with them — check with your IRB coordinator about appropriate sharing methods.

### 3D Printed Souvenirs

Looking for something more lasting? INC also offers **3D printed brain souvenirs**, which can be picked up within **2 weeks** of the scan.

> **📝 Requesting a 3D print:** Interest in a 3D printed souvenir should be noted on the **Scanner Requisition Form** at the time of scheduling.

---

## 🎓 Training Complete!

Congratulations — you've completed the **INC Flywheel Getting Started** training!

### Your Quiz Results

<script>
let summary = window.quizSummary();

let lines = [];
lines.push("| Question | Result |");
lines.push("|----------|--------|");
summary.rows.forEach(function (row) {
  lines.push("| " + row.question + " | " + row.result + " |");
});
lines.push("");
lines.push("**Score:** " + summary.correctCount + " / " + summary.total + " (" + summary.pct + "%)");
lines.push("");
lines.push("**Grade: " + summary.grade + "**");

if (summary.answeredCount < summary.total) {
  lines.push("");
  lines.push("_Go back and answer all questions above to see your final grade._");
}

"LIASCRIPT: " + lines.join("\n")
</script>

### What you've learned

- ✅ What INC Flywheel is, how it's deployed (AWS cloud storage + CURC on-premise compute), and why it's valuable for organizing, storing, and sharing imaging data
- ✅ How to correctly enter the Accession Number at the scanner
- ✅ How to log into Flywheel and the Flywheel data hierarchy: Group → Project → Subject → Session → Acquisition
- ✅ How to navigate the Flywheel user interface, including Collections and Data Views
- ✅ How to cite INC in your publications (RRID: SCR_025079)

---

### 📧 Confirm Your Completion

Course completion is confirmed by email, not an automatic submission. Make sure you've entered and saved your IdentiKey in the **Before You Begin** section above. A score of at least **85%** is required to confirm completion — if you haven't reached that yet, scroll back up and retry the questions you missed.

<div id="completion-gate"></div>

<script>
window.renderCompletionGate();
""
</script>

> **ℹ️ Missing your IdentiKey?** If you skipped the "Before You Begin" step, scroll back to the top and save it there first — it's read from the same session storage used to build this email.

---

### Next steps

- 📅 **Schedule a consultation** with INC staff before starting your study
- 📋 **Complete the Scanner Requisition Form** before each scan session
- 📖 **Explore the full documentation** at [inc-documentation.readthedocs.io](https://inc-documentation.readthedocs.io/en/latest/)
- ❓ **Questions?** Visit the [INC FAQs page](https://inc-documentation.readthedocs.io/en/latest/faqs.html)
