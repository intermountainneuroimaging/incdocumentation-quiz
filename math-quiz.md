<!--
author:   Amy Hegarty
email:    amyhegarty10@gmail.com
version:  1.0.0
language: en
narrator: US English Female

comment:  A short interactive quiz covering three basic math questions, with automatic scoring and a final grade.

style:
  .quiz-options {
    display: flex;
    flex-wrap: wrap;
    gap: 0.5em;
    margin: 0.75em 0;
  }
  .quiz-btn {
    padding: 0.5em 1em;
    border: 2px solid #888;
    border-radius: 8px;
    background: #f5f5f5;
    cursor: pointer;
    font-size: 1em;
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
-->

# Basic Math Quiz

--{{0}}--
Welcome! Answer three quick math questions. Your answers are tracked automatically,
and you'll see your final grade at the end.

<script>
window.mathQuiz = { q1: null, q2: null, q3: null };

window.mathQuizAnswer = function (q, correct, btn) {
  var key = "q" + q;
  if (window.mathQuiz[key] !== null) {
    return;
  }
  window.mathQuiz[key] = correct;

  var container = btn.parentElement;
  var buttons = container.querySelectorAll("button");
  buttons.forEach(function (b) {
    b.disabled = true;
    if (b.getAttribute("data-correct") === "true") {
      b.classList.add("quiz-correct");
    }
  });
  if (!correct) {
    btn.classList.add("quiz-wrong");
  }

  var feedback = document.getElementById("feedback-" + key);
  if (feedback) {
    feedback.textContent = correct
      ? "Correct!"
      : "Not quite — the correct answer is highlighted above.";
    feedback.style.color = correct ? "#2e7d32" : "#c62828";
    feedback.style.fontWeight = "600";
  }
};

""
</script>

## Question 1

What is 7 + 8?

<div class="quiz-options">
  <button class="quiz-btn" onclick="window.mathQuizAnswer(1, false, this)">14</button>
  <button class="quiz-btn" data-correct="true" onclick="window.mathQuizAnswer(1, true, this)">15</button>
  <button class="quiz-btn" onclick="window.mathQuizAnswer(1, false, this)">16</button>
  <button class="quiz-btn" onclick="window.mathQuizAnswer(1, false, this)">22</button>
</div>

<p id="feedback-q1"></p>

## Question 2

What is 9 x 6?

<div class="quiz-options">
  <button class="quiz-btn" onclick="window.mathQuizAnswer(2, false, this)">45</button>
  <button class="quiz-btn" onclick="window.mathQuizAnswer(2, false, this)">51</button>
  <button class="quiz-btn" data-correct="true" onclick="window.mathQuizAnswer(2, true, this)">54</button>
  <button class="quiz-btn" onclick="window.mathQuizAnswer(2, false, this)">63</button>
</div>

<p id="feedback-q2"></p>

## Question 3

What is 100 divided by 4?

<div class="quiz-options">
  <button class="quiz-btn" onclick="window.mathQuizAnswer(3, false, this)">20</button>
  <button class="quiz-btn" onclick="window.mathQuizAnswer(3, false, this)">24</button>
  <button class="quiz-btn" onclick="window.mathQuizAnswer(3, false, this)">30</button>
  <button class="quiz-btn" data-correct="true" onclick="window.mathQuizAnswer(3, true, this)">25</button>
</div>

<p id="feedback-q3"></p>

## Results

--{{0}}--
Let's see how you did.

<script>
let state = window.mathQuiz || { q1: null, q2: null, q3: null };
let total = 3;

let results = [state.q1, state.q2, state.q3];
let answeredCount = results.filter(function (v) { return v !== null; }).length;
let correctCount = results.filter(function (v) { return v === true; }).length;

function rowLabel(v) {
  if (v === true) return "Correct";
  if (v === false) return "Incorrect";
  return "Not answered yet";
}

let pct = Math.round((correctCount / total) * 100);
let grade =
  pct === 100 ? "A+" :
  pct >= 67   ? "B"  :
  pct >= 34   ? "C"  :
                "F";

let lines = [];
lines.push("| Question | Result |");
lines.push("|----------|--------|");
lines.push("| 1. What is 7 + 8? | " + rowLabel(state.q1) + " |");
lines.push("| 2. What is 9 x 6? | " + rowLabel(state.q2) + " |");
lines.push("| 3. What is 100 / 4? | " + rowLabel(state.q3) + " |");
lines.push("");
lines.push("**Score:** " + correctCount + " / " + total + " (" + pct + "%)");
lines.push("");
lines.push("**Grade: " + grade + "**");

if (answeredCount < total) {
  lines.push("");
  lines.push("_Go back and answer all three questions above to see your final grade._");
}

"LIASCRIPT: " + lines.join("\n")
</script>
