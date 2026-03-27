<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>HeartSync Matchmaker</title>
  <style>
    * {
      box-sizing: border-box;
    }

    body {
      margin: 0;
      font-family: Arial, Helvetica, sans-serif;
      background: linear-gradient(135deg, #ffe6ee, #fff5f8);
      color: #333;
    }

    .container {
      max-width: 800px;
      margin: 40px auto;
      background: white;
      border-radius: 18px;
      padding: 32px;
      box-shadow: 0 10px 30px rgba(0, 0, 0, 0.12);
    }

    h1 {
      text-align: center;
      margin-bottom: 8px;
      color: #c62868;
    }

    .subtitle {
      text-align: center;
      margin-bottom: 28px;
      color: #666;
    }

    .instructions {
      background: #fff0f5;
      border-left: 5px solid #c62868;
      padding: 16px;
      border-radius: 10px;
      margin-bottom: 24px;
    }

    .question-block {
      margin-bottom: 20px;
      padding: 18px;
      border: 1px solid #eee;
      border-radius: 12px;
      background: #fafafa;
    }

    .question-block label {
      display: block;
      font-weight: bold;
      margin-bottom: 10px;
    }

    .question-block input {
      width: 100%;
      padding: 10px;
      font-size: 16px;
      border: 1px solid #ccc;
      border-radius: 8px;
    }

    .error {
      color: #c62828;
      font-size: 14px;
      margin-top: 8px;
      min-height: 18px;
    }

    button {
      width: 100%;
      padding: 14px;
      font-size: 18px;
      font-weight: bold;
      border: none;
      border-radius: 10px;
      background: #c62868;
      color: white;
      cursor: pointer;
      transition: 0.2s ease;
    }

    button:hover {
      background: #ad1457;
    }

    .results {
      margin-top: 30px;
      padding: 22px;
      border-radius: 12px;
      background: #fff7fa;
      border: 1px solid #f3c2d4;
      display: none;
    }

    .results h2 {
      margin-top: 0;
      color: #c62868;
    }

    .results ul {
      padding-left: 20px;
    }

    .score {
      font-size: 28px;
      font-weight: bold;
      color: #ad1457;
    }

    .remark {
      margin-top: 18px;
      font-size: 18px;
      font-weight: bold;
    }

    footer {
      text-align: center;
      margin-top: 24px;
      color: #777;
      font-size: 14px;
    }

    @media (max-width: 600px) {
      .container {
        margin: 20px;
        padding: 20px;
      }
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>💖 HeartSync Matchmaker 💖</h1>
    <p class="subtitle">Answer five questions to see how compatible we are.</p>

    <div class="instructions">
      <strong>How it works:</strong>
      Rate each statement from <strong>1 to 5</strong>.<br />
      <strong>1 = Strongly Disagree</strong> and <strong>5 = Strongly Agree</strong>.<br />
      The closer your answers are to my ideal answers, the higher your compatibility score will be.
    </div>

    <form id="matchForm">
      <div class="question-block">
        <label for="q1">1. I enjoy trying new foods.</label>
        <input type="number" id="q1" min="1" max="5" />
        <div class="error" id="q1Error"></div>
      </div>

      <div class="question-block">
        <label for="q2">2. A perfect night includes music and relaxation.</label>
        <input type="number" id="q2" min="1" max="5" />
        <div class="error" id="q2Error"></div>
      </div>

      <div class="question-block">
        <label for="q3">3. I like being outdoors and active.</label>
        <input type="number" id="q3" min="1" max="5" />
        <div class="error" id="q3Error"></div>
      </div>

      <div class="question-block">
        <label for="q4">4. Communication is the most important part of a relationship.</label>
        <input type="number" id="q4" min="1" max="5" />
        <div class="error" id="q4Error"></div>
      </div>

      <div class="question-block">
        <label for="q5">5. I enjoy spontaneous adventures.</label>
        <input type="number" id="q5" min="1" max="5" />
        <div class="error" id="q5Error"></div>
      </div>

      <button type="submit">Calculate Compatibility</button>
    </form>

    <div class="results" id="results">
      <h2>Your Match Results</h2>
      <p class="score" id="finalScore"></p>
      <ul id="questionSummary"></ul>
      <p class="remark" id="closingRemark"></p>
    </div>

    <footer>
      Created for the Matchmaker for the Web project
    </footer>
  </div>

  <script>
    // =========================
    // Constants
    // =========================
    const QUESTIONS = [
      "I enjoy trying new foods.",
      "A perfect night includes music and relaxation.",
      "I like being outdoors and active.",
      "Communication is the most important part of a relationship.",
      "I enjoy spontaneous adventures."
    ];

    // My ideal "true love" answers
    const IDEAL_ANSWERS = [5, 5, 4, 5, 4];

    // Thresholds
    const TRUE_LOVE_THRESHOLD = 85;
    const FRIENDS_THRESHOLD = 65;

    // =========================
    // Validation Function
    // =========================
    function validate(value, errorElementId) {
      const errorElement = document.getElementById(errorElementId);
      errorElement.textContent = "";

      if (value === "") {
        errorElement.textContent = "Please enter a number from 1 to 5.";
        return false;
      }

      const numberValue = Number(value);

      if (!Number.isInteger(numberValue) || numberValue < 1 || numberValue > 5) {
        errorElement.textContent = "Invalid input. Enter a whole number between 1 and 5.";
        return false;
      }

      return true;
    }

    // =========================
    // Helper Functions
    // =========================
    function getClosingRemark(score) {
      if (score >= TRUE_LOVE_THRESHOLD) {
        return "💘 True Love! We are an amazing match!";
      } else if (score >= FRIENDS_THRESHOLD) {
        return "😊 Possible Friends! There is definitely some connection here.";
      } else {
        return "🏃 Run Away! We may be better off going separate directions.";
      }
    }

    function calculateQuestionScore(userAnswer, idealAnswer) {
      // Using a scaled value so the final total works cleanly as a percentage out of 100
      return Math.abs(userAnswer - idealAnswer) * 5;
    }

    // =========================
    // Main App Logic
    // =========================
    document.getElementById("matchForm").addEventListener("submit", function (event) {
      event.preventDefault();

      const userAnswers = [];
      let allValid = true;

      for (let i = 1; i <= QUESTIONS.length; i++) {
        const inputValue = document.getElementById(`q${i}`).value.trim();
        const isValid = validate(inputValue, `q${i}Error`);

        if (!isValid) {
          allValid = false;
        } else {
          userAnswers.push(Number(inputValue));
        }
      }

      if (!allValid) {
        document.getElementById("results").style.display = "none";
        return;
      }

      let totalPenalty = 0;
      let summaryHTML = "";

      for (let i = 0; i < QUESTIONS.length; i++) {
        const questionScore = calculateQuestionScore(userAnswers[i], IDEAL_ANSWERS[i]);
        totalPenalty += questionScore;

        summaryHTML += `
          <li>
            <strong>Question ${i + 1}:</strong>
            Your answer = ${userAnswers[i]}, ideal answer = ${IDEAL_ANSWERS[i]},
            compatibility penalty = ${questionScore}
          </li>
        `;
      }

      const finalCompatibilityScore = 100 - totalPenalty;
      const safeFinalScore = Math.max(0, finalCompatibilityScore);

      document.getElementById("finalScore").textContent =
        `Overall Compatibility Score: ${safeFinalScore}%`;

      document.getElementById("questionSummary").innerHTML = summaryHTML;
      document.getElementById("closingRemark").textContent =
        getClosingRemark(safeFinalScore);

      document.getElementById("results").style.display = "block";
    });
  </script>
</body>
</html>
