<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Quiz Game</title>
  <link href="https://fonts.googleapis.com/css2?family=Syne:wght@700;800&family=DM+Sans:wght@400;500&display=swap" rel="stylesheet"/>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    :root {
      --bg: #0d0d0d;
      --card: #161616;
      --accent: #c8f542;
      --accent2: #f5a142;
      --text: #f0f0f0;
      --muted: #888;
      --correct: #4ade80;
      --wrong: #f87171;
      --radius: 16px;
    }

    body {
      background: var(--bg);
      color: var(--text);
      font-family: 'DM Sans', sans-serif;
      min-height: 100vh;
      display: flex;
      align-items: center;
      justify-content: center;
      padding: 24px;
    }

    .container {
      width: 100%;
      max-width: 620px;
    }

    /* SCREENS */
    .screen { display: none; animation: fadeUp .4s ease both; }
    .screen.active { display: block; }

    @keyframes fadeUp {
      from { opacity: 0; transform: translateY(24px); }
      to   { opacity: 1; transform: translateY(0); }
    }

    /* START SCREEN */
    .logo {
      font-family: 'Syne', sans-serif;
      font-size: clamp(2.4rem, 8vw, 4rem);
      font-weight: 800;
      line-height: 1;
      color: var(--accent);
      letter-spacing: -2px;
      margin-bottom: 8px;
    }

    .tagline {
      color: var(--muted);
      font-size: 1rem;
      margin-bottom: 48px;
    }

    .topic-grid {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 12px;
      margin-bottom: 32px;
    }

    .topic-btn {
      background: var(--card);
      border: 2px solid transparent;
      border-radius: var(--radius);
      padding: 20px 16px;
      color: var(--text);
      font-family: 'DM Sans', sans-serif;
      font-size: .95rem;
      font-weight: 500;
      cursor: pointer;
      transition: all .2s;
      text-align: left;
    }

    .topic-btn .icon { font-size: 1.6rem; display: block; margin-bottom: 8px; }

    .topic-btn:hover { border-color: var(--accent); }
    .topic-btn.selected { border-color: var(--accent); background: #1e2900; color: var(--accent); }

    .btn-primary {
      background: var(--accent);
      color: #0d0d0d;
      border: none;
      border-radius: 50px;
      padding: 16px 40px;
      font-family: 'Syne', sans-serif;
      font-size: 1rem;
      font-weight: 700;
      cursor: pointer;
      transition: transform .15s, opacity .15s;
      width: 100%;
    }

    .btn-primary:hover { transform: scale(1.02); }
    .btn-primary:disabled { opacity: .4; cursor: not-allowed; transform: none; }

    /* QUIZ SCREEN */
    .progress-bar-wrap {
      background: #222;
      border-radius: 100px;
      height: 6px;
      margin-bottom: 28px;
      overflow: hidden;
    }

    .progress-bar {
      background: var(--accent);
      height: 100%;
      border-radius: 100px;
      transition: width .4s ease;
    }

    .q-meta {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 20px;
      font-size: .85rem;
      color: var(--muted);
    }

    .q-num { font-family: 'Syne', sans-serif; font-weight: 700; color: var(--accent); font-size: 1rem; }

    .score-badge {
      background: var(--card);
      padding: 6px 14px;
      border-radius: 50px;
      font-weight: 500;
    }

    .question-text {
      font-family: 'Syne', sans-serif;
      font-size: clamp(1.15rem, 3.5vw, 1.4rem);
      font-weight: 700;
      line-height: 1.4;
      margin-bottom: 28px;
      min-height: 3em;
    }

    .options {
      display: flex;
      flex-direction: column;
      gap: 12px;
      margin-bottom: 28px;
    }

    .option-btn {
      background: var(--card);
      border: 2px solid transparent;
      border-radius: var(--radius);
      padding: 16px 20px;
      color: var(--text);
      font-family: 'DM Sans', sans-serif;
      font-size: .98rem;
      font-weight: 500;
      cursor: pointer;
      text-align: left;
      transition: all .2s;
      display: flex;
      align-items: center;
      gap: 14px;
    }

    .option-letter {
      width: 30px; height: 30px;
      border-radius: 50%;
      background: #222;
      display: flex; align-items: center; justify-content: center;
      font-family: 'Syne', sans-serif;
      font-size: .8rem;
      font-weight: 700;
      flex-shrink: 0;
      transition: all .2s;
    }

    .option-btn:hover:not(:disabled) { border-color: var(--accent); }
    .option-btn:hover:not(:disabled) .option-letter { background: var(--accent); color: #0d0d0d; }

    .option-btn.correct { border-color: var(--correct); background: #0a2318; }
    .option-btn.correct .option-letter { background: var(--correct); color: #0d0d0d; }

    .option-btn.wrong { border-color: var(--wrong); background: #2a0e0e; }
    .option-btn.wrong .option-letter { background: var(--wrong); color: #0d0d0d; }

    .option-btn:disabled { cursor: not-allowed; }

    .feedback-msg {
      min-height: 28px;
      font-size: .9rem;
      font-weight: 500;
      margin-bottom: 16px;
      color: var(--muted);
      transition: color .2s;
    }

    .feedback-msg.correct-fb { color: var(--correct); }
    .feedback-msg.wrong-fb   { color: var(--wrong); }

    .next-btn {
      background: var(--card);
      border: 2px solid var(--accent);
      border-radius: 50px;
      padding: 14px 36px;
      color: var(--accent);
      font-family: 'Syne', sans-serif;
      font-weight: 700;
      font-size: .95rem;
      cursor: pointer;
      transition: all .2s;
      display: none;
    }

    .next-btn.visible { display: inline-block; }
    .next-btn:hover { background: var(--accent); color: #0d0d0d; }

    /* RESULTS SCREEN */
    .result-header {
      font-family: 'Syne', sans-serif;
      font-size: clamp(2rem, 7vw, 3.2rem);
      font-weight: 800;
      line-height: 1.1;
      margin-bottom: 8px;
    }

    .result-score {
      font-size: clamp(3rem, 10vw, 5rem);
      font-family: 'Syne', sans-serif;
      font-weight: 800;
      color: var(--accent);
      margin: 24px 0 8px;
    }

    .result-label { color: var(--muted); margin-bottom: 40px; }

    .result-breakdown {
      display: grid;
      grid-template-columns: 1fr 1fr 1fr;
      gap: 12px;
      margin-bottom: 36px;
    }

    .rb-card {
      background: var(--card);
      border-radius: var(--radius);
      padding: 20px 12px;
      text-align: center;
    }

    .rb-val { font-family: 'Syne', sans-serif; font-size: 1.8rem; font-weight: 800; }
    .rb-lbl { font-size: .78rem; color: var(--muted); margin-top: 4px; }

    .btn-secondary {
      background: transparent;
      border: 2px solid #333;
      border-radius: 50px;
      padding: 14px 32px;
      color: var(--text);
      font-family: 'Syne', sans-serif;
      font-weight: 700;
      font-size: .95rem;
      cursor: pointer;
      transition: all .2s;
      width: 100%;
      margin-top: 12px;
    }

    .btn-secondary:hover { border-color: var(--text); }
  </style>
</head>
<body>
<div class="container">

  <!-- START SCREEN -->
  <div class="screen active" id="startScreen">
    <div class="logo">QUIZ<br>GAME.</div>
    <p class="tagline">Pick a topic. Test yourself. No pressure.</p>

    <div class="topic-grid">
      <button class="topic-btn" data-topic="science">
        <span class="icon">🔬</span> Science
      </button>
      <button class="topic-btn" data-topic="history">
        <span class="icon">📜</span> History
      </button>
      <button class="topic-btn" data-topic="tech">
        <span class="icon">💻</span> Technology
      </button>
      <button class="topic-btn" data-topic="geography">
        <span class="icon">🌍</span> Geography
      </button>
    </div>

    <button class="btn-primary" id="startBtn" disabled>Start Quiz →</button>
  </div>

  <!-- QUIZ SCREEN -->
  <div class="screen" id="quizScreen">
    <div class="progress-bar-wrap">
      <div class="progress-bar" id="progressBar"></div>
    </div>
    <div class="q-meta">
      <span class="q-num" id="qNum">Q1 / 5</span>
      <span class="score-badge">Score: <span id="scoreDisplay">0</span></span>
    </div>
    <div class="question-text" id="questionText"></div>
    <div class="options" id="optionsContainer"></div>
    <div class="feedback-msg" id="feedbackMsg"></div>
    <button class="next-btn" id="nextBtn">Next →</button>
  </div>

  <!-- RESULTS SCREEN -->
  <div class="screen" id="resultsScreen">
    <div class="result-header" id="resultHeader"></div>
    <div class="result-score" id="resultScore"></div>
    <div class="result-label" id="resultLabel"></div>
    <div class="result-breakdown">
      <div class="rb-card">
        <div class="rb-val" id="rbCorrect" style="color:var(--correct)"></div>
        <div class="rb-lbl">Correct</div>
      </div>
      <div class="rb-card">
        <div class="rb-val" id="rbWrong" style="color:var(--wrong)"></div>
        <div class="rb-lbl">Wrong</div>
      </div>
      <div class="rb-card">
        <div class="rb-val" id="rbPct" style="color:var(--accent)"></div>
        <div class="rb-lbl">Score</div>
      </div>
    </div>
    <button class="btn-primary" id="retryBtn">Play Again →</button>
    <button class="btn-secondary" id="changeTopicBtn">Change Topic</button>
  </div>

</div>

<script>
  // ── QUESTIONS ──────────────────────────────────────────────────
  const allQuestions = {
    science: [
      { q: "What gas do plants absorb from the atmosphere during photosynthesis?", options: ["Oxygen", "Carbon Dioxide", "Nitrogen", "Hydrogen"], answer: 1 },
      { q: "How many bones are in the adult human body?", options: ["196", "206", "216", "226"], answer: 1 },
      { q: "What is the chemical symbol for gold?", options: ["Go", "Gd", "Au", "Ag"], answer: 2 },
      { q: "Which planet is known as the Red Planet?", options: ["Venus", "Jupiter", "Saturn", "Mars"], answer: 3 },
      { q: "What is the powerhouse of the cell?", options: ["Nucleus", "Ribosome", "Mitochondria", "Vacuole"], answer: 2 },
    ],
    history: [
      { q: "In what year did World War II end?", options: ["1943", "1944", "1945", "1946"], answer: 2 },
      { q: "Who was the first President of the United States?", options: ["John Adams", "Thomas Jefferson", "Benjamin Franklin", "George Washington"], answer: 3 },
      { q: "The ancient city of Rome is located in which modern country?", options: ["Greece", "Spain", "France", "Italy"], answer: 3 },
      { q: "Who painted the Mona Lisa?", options: ["Michelangelo", "Raphael", "Leonardo da Vinci", "Donatello"], answer: 2 },
      { q: "In what year did the Berlin Wall fall?", options: ["1987", "1988", "1989", "1990"], answer: 2 },
    ],
    tech: [
      { q: "What does 'HTML' stand for?", options: ["HyperText Markup Language", "HighText Machine Language", "HyperText Machine Link", "HyperLink Markup Language"], answer: 0 },
      { q: "Which company created the JavaScript programming language?", options: ["Microsoft", "Google", "Netscape", "Apple"], answer: 2 },
      { q: "What does 'CPU' stand for?", options: ["Central Processing Unit", "Computer Personal Unit", "Central Program Utility", "Core Processing Unit"], answer: 0 },
      { q: "What is the most widely used operating system in the world?", options: ["macOS", "Linux", "Windows", "Android"], answer: 2 },
      { q: "Which data structure operates on a 'first in, first out' basis?", options: ["Stack", "Queue", "Tree", "Graph"], answer: 1 },
    ],
    geography: [
      { q: "What is the capital city of Australia?", options: ["Sydney", "Melbourne", "Brisbane", "Canberra"], answer: 3 },
      { q: "Which is the longest river in the world?", options: ["Amazon", "Nile", "Yangtze", "Mississippi"], answer: 1 },
      { q: "On which continent is the Sahara Desert located?", options: ["Asia", "South America", "Australia", "Africa"], answer: 3 },
      { q: "How many countries are in South America?", options: ["10", "11", "12", "13"], answer: 2 },
      { q: "Which country has the most natural lakes in the world?", options: ["Russia", "USA", "Canada", "Finland"], answer: 2 },
    ],
  };

  const letters = ["A", "B", "C", "D"];

  // ── STATE ──────────────────────────────────────────────────────
  let selectedTopic = null;
  let questions = [];
  let currentQ = 0;
  let score = 0;
  let answered = false;

  // ── DOM REFS ───────────────────────────────────────────────────
  const screens      = { start: document.getElementById('startScreen'), quiz: document.getElementById('quizScreen'), results: document.getElementById('resultsScreen') };
  const startBtn     = document.getElementById('startBtn');
  const topicBtns    = document.querySelectorAll('.topic-btn');
  const progressBar  = document.getElementById('progressBar');
  const qNum         = document.getElementById('qNum');
  const scoreDisplay = document.getElementById('scoreDisplay');
  const questionText = document.getElementById('questionText');
  const optionsEl    = document.getElementById('optionsContainer');
  const feedbackMsg  = document.getElementById('feedbackMsg');
  const nextBtn      = document.getElementById('nextBtn');

  // ── HELPERS ────────────────────────────────────────────────────
  function showScreen(name) {
    Object.values(screens).forEach(s => s.classList.remove('active'));
    screens[name].classList.add('active');
  }

  function shuffle(arr) {
    return [...arr].sort(() => Math.random() - .5);
  }

  // ── TOPIC SELECTION ────────────────────────────────────────────
  topicBtns.forEach(btn => {
    btn.addEventListener('click', () => {
      topicBtns.forEach(b => b.classList.remove('selected'));
      btn.classList.add('selected');
      selectedTopic = btn.dataset.topic;
      startBtn.disabled = false;
    });
  });

  // ── START ──────────────────────────────────────────────────────
  startBtn.addEventListener('click', () => {
    questions = shuffle(allQuestions[selectedTopic]);
    currentQ = 0;
    score = 0;
    showScreen('quiz');
    renderQuestion();
  });

  // ── RENDER QUESTION ────────────────────────────────────────────
  function renderQuestion() {
    answered = false;
    feedbackMsg.textContent = '';
    feedbackMsg.className = 'feedback-msg';
    nextBtn.classList.remove('visible');

    const total = questions.length;
    const q = questions[currentQ];

    progressBar.style.width = `${(currentQ / total) * 100}%`;
    qNum.textContent = `Q${currentQ + 1} / ${total}`;
    scoreDisplay.textContent = score;
    questionText.textContent = q.q;

    optionsEl.innerHTML = '';
    q.options.forEach((opt, i) => {
      const btn = document.createElement('button');
      btn.className = 'option-btn';
      btn.innerHTML = `<span class="option-letter">${letters[i]}</span> ${opt}`;
      btn.addEventListener('click', () => handleAnswer(i, btn, q.answer));
      optionsEl.appendChild(btn);
    });
  }

  // ── HANDLE ANSWER ──────────────────────────────────────────────
  function handleAnswer(chosen, btn, correct) {
    if (answered) return;
    answered = true;

    const allBtns = optionsEl.querySelectorAll('.option-btn');
    allBtns.forEach(b => b.disabled = true);

    if (chosen === correct) {
      btn.classList.add('correct');
      feedbackMsg.textContent = '✓ Correct! Nice one.';
      feedbackMsg.classList.add('correct-fb');
      score++;
    } else {
      btn.classList.add('wrong');
      allBtns[correct].classList.add('correct');
      feedbackMsg.textContent = `✗ Not quite — the answer was "${questions[currentQ].options[correct]}"`;
      feedbackMsg.classList.add('wrong-fb');
    }

    scoreDisplay.textContent = score;
    nextBtn.classList.add('visible');
    nextBtn.textContent = currentQ < questions.length - 1 ? 'Next →' : 'See Results →';
  }

  // ── NEXT ───────────────────────────────────────────────────────
  nextBtn.addEventListener('click', () => {
    currentQ++;
    if (currentQ < questions.length) {
      renderQuestion();
    } else {
      showResults();
    }
  });

  // ── RESULTS ────────────────────────────────────────────────────
  function showResults() {
    const total = questions.length;
    const pct = Math.round((score / total) * 100);

    const headers = ['Not bad for a first try!', 'Keep going — you got this!', 'Solid effort!', 'Great work!', 'Perfect score! 🔥'];
    const labels  = ['Every expert started somewhere.', 'Practice makes perfect.', 'You clearly know your stuff.', 'Impressive!', 'You\'re on fire!'];

    const tier = score === total ? 4 : score >= total * .8 ? 3 : score >= total * .6 ? 2 : score >= total * .4 ? 1 : 0;

    document.getElementById('resultHeader').textContent = headers[tier];
    document.getElementById('resultScore').textContent = `${score}/${total}`;
    document.getElementById('resultLabel').textContent = labels[tier];
    document.getElementById('rbCorrect').textContent = score;
    document.getElementById('rbWrong').textContent = total - score;
    document.getElementById('rbPct').textContent = `${pct}%`;

    showScreen('results');
  }

  document.getElementById('retryBtn').addEventListener('click', () => {
    questions = shuffle(allQuestions[selectedTopic]);
    currentQ = 0;
    score = 0;
    showScreen('quiz');
    renderQuestion();
  });

  document.getElementById('changeTopicBtn').addEventListener('click', () => {
    topicBtns.forEach(b => b.classList.remove('selected'));
    startBtn.disabled = true;
    selectedTopic = null;
    showScreen('start');
  });
</script>
</body>
</html>