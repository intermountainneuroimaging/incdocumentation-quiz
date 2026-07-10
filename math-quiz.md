<!--
author:   Amy Hegarty
email:    amyhegarty10@gmail.com
version:  1.0.0
language: en
narrator: US English Female

comment:  A short interactive quiz covering three basic math questions, with automatic scoring and a final grade.

script: https://cdn.jsdelivr.net/npm/@emailjs/browser@4/dist/email.min.js

style:
  .quiz-options {
    display: flex;
    flex-direction: column;
    gap: 0.4em;
    margin: 0.75em 0;
    line-height: 1.2;
  }
  .quiz-option {
    display: inline-block;
    padding: 0.15em 0.3em;
  }
  .quiz-option input {
    vertical-align: middle;
    margin-right: 0.4em;
  }
  .quiz-btn {
    display: inline-block;
    padding: 0.5em 1em;
    border: 2px solid #888;
    border-radius: 8px;
    background: #f5f5f5;
    cursor: pointer;
    font-size: 1em;
  }
  .quiz-options .quiz-btn {
    margin-top: 0.3em;
  }
  .quiz-btn:disabled {
    cursor: default;
    opacity: 0.85;
  }
  .quiz-correct {
    background: #c8e6c9 !important;
    border-radius: 4px;
  }
  .quiz-wrong {
    background: #ffcdd2 !important;
    border-radius: 4px;
  }
  .quiz-field {
    margin: 0.4em 0;
  }
  .quiz-field label {
    display: inline-block;
    margin-right: 0.5em;
    vertical-align: middle;
  }
  .quiz-field input {
    vertical-align: middle;
    padding: 0.4em 0.6em;
    border: 2px solid #888;
    border-radius: 8px;
    font-size: 1em;
    width: 250px;
    max-width: 100%;
  }
-->

# Basic Math Quiz

--{{0}}--
Welcome! Answer three quick math questions. Pick an option and click "Check Answer" —
you can change your answer and check again as many times as you like. Your score
always reflects your latest attempt on each question, and accumulates for a final
grade at the end.

<script>
window.mathQuiz = { q1: null, q2: null, q3: null };

window.mathQuizCheck = function (q, btn) {
  var key = "q" + q;
  var container = document.getElementById(key + "-options");
  var feedback = document.getElementById("feedback-" + key);
  var selected = container.querySelector("input[type=radio]:checked");

  var radios = container.querySelectorAll("input[type=radio]");
  radios.forEach(function (r) {
    var label = r.closest("label");
    label.classList.remove("quiz-correct", "quiz-wrong");
  });

  if (!selected) {
    if (feedback) {
      feedback.textContent = "Please select an answer first.";
      feedback.style.color = "#b26a00";
      feedback.style.fontWeight = "600";
    }
    return;
  }

  var correct = selected.getAttribute("data-correct") === "true";
  window.mathQuiz[key] = correct;

  radios.forEach(function (r) {
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

window.mathQuizSummary = function () {
  var state = window.mathQuiz;
  var total = 3;
  var questions = ["What is 7 + 8?", "What is 9 x 6?", "What is 100 / 4?"];
  var results = [state.q1, state.q2, state.q3];

  function rowLabel(v) {
    if (v === true) return "Correct";
    if (v === false) return "Incorrect";
    return "Not answered yet";
  }

  var correctCount = results.filter(function (v) { return v === true; }).length;
  var answeredCount = results.filter(function (v) { return v !== null; }).length;
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
    rows: questions.map(function (q, i) {
      return { question: q, result: rowLabel(results[i]) };
    })
  };
};

window.EMAILJS_SERVICE_ID = "service_lrwwp98";
window.EMAILJS_TEMPLATE_ID = "template_1ojukti";
window.EMAILJS_PUBLIC_KEY = "PwQXw3u1xmYiZixXg";

window.mathQuizEmailResults = function () {
  var nameInput = document.getElementById("student-name");
  var emailInput = document.getElementById("teacher-email");
  var status = document.getElementById("email-status");
  var name = nameInput ? nameInput.value.trim() : "";
  var teacherEmail = emailInput ? emailInput.value.trim() : "";

  if (!name || !teacherEmail || teacherEmail.indexOf("@") === -1) {
    if (status) {
      status.textContent = "Please enter your name and a valid teacher email address.";
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

  var summary = window.mathQuizSummary();
  var bodyLines = [];
  bodyLines.push("Student: " + name);
  bodyLines.push("");
  summary.rows.forEach(function (row) {
    bodyLines.push(row.question + " " + row.result);
  });
  bodyLines.push("");
  bodyLines.push("Score: " + summary.correctCount + " / " + summary.total + " (" + summary.pct + "%)");
  bodyLines.push("Grade: " + summary.grade);

  var subject = "Math Quiz Results for " + name;

  if (status) {
    status.textContent = "Sending…";
    status.style.color = "#555";
    status.style.fontWeight = "600";
  }

  emailjs.init({ publicKey: window.EMAILJS_PUBLIC_KEY });

  emailjs.send(window.EMAILJS_SERVICE_ID, window.EMAILJS_TEMPLATE_ID, {
    to_email: teacherEmail,
    subject: subject,
    message: bodyLines.join("\n")
  }).then(
    function () {
      if (status) {
        status.textContent = "Sent! Your teacher should receive your results shortly.";
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

## Question 1

What is 7 + 8?

<div class="quiz-options" id="q1-options">
  <label class="quiz-option"><input type="radio" name="q1"> 14</label><br>
  <label class="quiz-option"><input type="radio" name="q1" data-correct="true"> 15</label><br>
  <label class="quiz-option"><input type="radio" name="q1"> 16</label><br>
  <label class="quiz-option"><input type="radio" name="q1"> 22</label><br>
  <button class="quiz-btn" onclick="window.mathQuizCheck(1, this)">Check Answer</button>
</div>

<p id="feedback-q1"></p>

## Question 2

What is 9 x 6?

<div class="quiz-options" id="q2-options">
  <label class="quiz-option"><input type="radio" name="q2"> 45</label><br>
  <label class="quiz-option"><input type="radio" name="q2"> 51</label><br>
  <label class="quiz-option"><input type="radio" name="q2" data-correct="true"> 54</label><br>
  <label class="quiz-option"><input type="radio" name="q2"> 63</label><br>
  <button class="quiz-btn" onclick="window.mathQuizCheck(2, this)">Check Answer</button>
</div>

<p id="feedback-q2"></p>

## Question 3

What is 100 divided by 4?

<div class="quiz-options" id="q3-options">
  <label class="quiz-option"><input type="radio" name="q3"> 20</label><br>
  <label class="quiz-option"><input type="radio" name="q3"> 24</label><br>
  <label class="quiz-option"><input type="radio" name="q3"> 30</label><br>
  <label class="quiz-option"><input type="radio" name="q3" data-correct="true"> 25</label><br>
  <button class="quiz-btn" onclick="window.mathQuizCheck(3, this)">Check Answer</button>
</div>

<p id="feedback-q3"></p>

## Results

--{{0}}--
Let's see how you did.

<script>
let summary = window.mathQuizSummary();

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
  lines.push("_Go back and answer all three questions above to see your final grade._");
}

"LIASCRIPT: " + lines.join("\n")
</script>

### Send Your Results to Your Teacher

<div class="quiz-options">
  <div class="quiz-field">
    <label for="student-name">Your name:</label>
    <input type="text" id="student-name" placeholder="e.g. Jane Doe">
  </div><br>
  <div class="quiz-field">
    <label for="teacher-email">Teacher's email:</label>
    <input type="email" id="teacher-email" placeholder="teacher@example.com">
  </div><br>
  <button class="quiz-btn" onclick="window.mathQuizEmailResults()">Email My Results to My Teacher</button>
</div>

<p id="email-status"></p>
