# Erasyl-cloude
Стом игра 
<!DOCTYPE html>
<html lang="kk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Клиникалық Детектив: Дәлелді Стоматолог</title>
    <style>
        :root {
            --primary: #0284c7;
            --primary-hover: #0369a1;
            --bg: #f0f9ff;
            --card-bg: #ffffff;
            --text: #1e293b;
            --success: #16a34a;
            --danger: #dc2626;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg);
            color: var(--text);
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }

        .game-card {
            background: var(--card-bg);
            border-radius: 16px;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
            padding: 30px;
            max-width: 650px;
            width: 100%;
            text-align: center;
        }

        h1 {
            color: var(--primary);
            font-size: 24px;
            margin-bottom: 10px;
        }

        .description {
            font-size: 14px;
            color: #64748b;
            margin-bottom: 25px;
        }

        .score-board {
            font-size: 18px;
            font-weight: bold;
            margin-bottom: 20px;
            background: #e0f2fe;
            padding: 10px;
            border-radius: 8px;
            color: #0369a1;
        }

        .question-box {
            font-size: 18px;
            font-weight: 600;
            margin-bottom: 25px;
            min-height: 60px;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .options-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            margin-bottom: 20px;
        }

        .btn {
            background-color: var(--primary);
            color: white;
            border: none;
            padding: 15px 20px;
            border-radius: 8px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.2s ease;
        }

        .btn:hover {
            background-color: var(--primary-hover);
            transform: translateY(-2px);
        }

        .btn-myth {
            background-color: #ef4444;
        }
        .btn-myth:hover {
            background-color: #dc2626;
        }

        .btn-fact {
            background-color: #22c55e;
        }
        .btn-fact:hover {
            background-color: #16a34a;
        }

        .explanation {
            display: none;
            padding: 15px;
            border-radius: 8px;
            margin-top: 20px;
            font-size: 15px;
            text-align: left;
            line-height: 1.5;
        }

        .explanation.correct {
            background-color: #dcfce7;
            color: #15803d;
            border: 1px solid #86efac;
        }

        .explanation.wrong {
            background-color: #fee2e2;
            color: #b91c1c;
            border: 1px solid #fca5a5;
        }

        .next-btn {
            display: none;
            margin-top: 20px;
            width: 100%;
            background-color: #0f172a;
        }
        .next-btn:hover {
            background-color: #334155;
        }

        .result-screen {
            display: none;
        }

        .result-screen h2 {
            font-size: 28px;
            color: var(--primary);
        }
    </style>
</head>
<body>

<div class="game-card">
    <div id="game-screen">
        <h1>🩺 Клиникалық Детектив</h1>
        <p class="description">Дәлелді медицина (EBD) қағидаттарына сүйеніп, клиникалық мифтерді анықтаңыз!</p>
        
        <div class="score-board">
            Ұпай: <span id="score">0</span> / <span id="total-questions">0</span>
        </div>

        <div class="question-box" id="question-text">
            Сұрақ жүктелуде...
        </div>

        <div class="options-grid" id="options-container">
            <button class="btn btn-fact" onclick="checkAnswer(true)">ДӘЛЕЛДЕНГЕН (ФАКТ)</button>
            <button class="btn btn-myth" onclick="checkAnswer(false)">МИФ / ҚАТЕ</button>
        </div>

        <div id="explanation" class="explanation"></div>

        <button id="next-btn" class="btn next-btn" onclick="nextQuestion()">Келесі кейс ➔</button>
    </div>

    <div id="result-screen" class="result-screen">
        <h2>🎉 Ойын аяқталды!</h2>
        <p style="font-size: 20px; margin: 20px 0;">Сіздің нәтижеңіз: <strong><span id="final-score"></span></strong></p>
        <p id="feedback-text" style="font-size: 16px; color: #475569; margin-bottom: 30px;"></p>
        <button class="btn" onclick="restartGame()">Қайтадан бастау 🔄</button>
    </div>
</div>

<script>
    const questions = [
        {
            text: "Жүктіліктің II триместрінде жергілікті анестетикті (артикаин эпинефринмен 1:200000) қолдану бала мен ана үшін қауіпті.",
            isFact: false,
            explanation: "❌ МИФ! Халықаралық EBD клиникалық хаттамалары бойынша жүктіліктің 2-триместрінде стоматологиялық емдеу және арнайы анестетиктерді қолдану қауіпсіз болып табылады."
        },
        {
            text: "Фторланған тіс пасталарын күнделікті қолдану кариестің алдын алудағы тиімділігі жүйелі шолулармен (Cochrane) дәлелденген.",
            isFact: true,
            explanation: "✅ ДӘЛЕЛДЕНГЕН! Жоғары дәрежелі ғылыми зерттеулер (1500+ ppm фторы бар пасталар) кариесті азайтатынын толық растайды."
        },
        {
            text: "Барлық кариес қуыстарын өңдеу кезінде міндетті түрде антибиотиктерді жергілікті немесе жүйелі түрде тағайындау керек.",
            isFact: false,
            explanation: "❌ МИФ! Асқынбаған кариесті емдеуде антибиотик қолдану ғылыми негізделмеген және резистенттілікті арттырады."
        },
        {
            text: "Тіс имплантациясы алдында 3D КТ (КЛКТ) сканерлеу жасау — анатомиялық құрылымдарды зақымдап алмау үшін заманауи стандарт.",
            isFact: true,
            explanation: "✅ ДӘЛЕЛДЕНГЕН! КЛКТ (CBCT) диагностикасы имплантация кезінде қателіктерді азайтып, емнің сәттілігін арттырады."
        },
        {
            text: "Тіс тастарын ультрадыбыспен тазалау тістің эмалін толықтай құртады.",
            isFact: false,
            explanation: "❌ МИФ! Дұрыс режимде және баптауда орындалған УД-скалинг эмальға зиян келтірмейді, керісінше қызыл иек қабынуының (гингивит/пародонтит) алдын алады."
        }
    ];

    let currentQuestionIndex = 0;
    let score = 0;

    const questionTextEl = document.getElementById('question-text');
    const scoreEl = document.getElementById('score');
    const totalQuestionsEl = document.getElementById('total-questions');
    const explanationEl = document.getElementById('explanation');
    const nextBtn = document.getElementById('next-btn');
    const optionsContainer = document.getElementById('options-container');
    const gameScreen = document.getElementById('game-screen');
    const resultScreen = document.getElementById('result-screen');

    function startGame() {
        currentQuestionIndex = 0;
        score = 0;
        totalQuestionsEl.textContent = questions.length;
        gameScreen.style.display = 'block';
        resultScreen.style.display = 'none';
        showQuestion();
    }

    function showQuestion() {
        resetState();
        let q = questions[currentQuestionIndex];
        questionTextEl.textContent = `${currentQuestionIndex + 1}. ${q.text}`;
        scoreEl.textContent = score;
    }

    function resetState() {
        explanationEl.style.display = 'none';
        explanationEl.className = 'explanation';
        nextBtn.style.display = 'none';
        optionsContainer.style.pointerEvents = 'auto';
    }

    function checkAnswer(userChoice) {
        optionsContainer.style.pointerEvents = 'none'; // Түймелерді бұғаттау
        let q = questions[currentQuestionIndex];

        if (userChoice === q.isFact) {
            score++;
            scoreEl.textContent = score;
            explanationEl.className = 'explanation correct';
            explanationEl.innerHTML = `<strong>Дұрыс!</strong><br>${q.explanation}`;
        } else {
            explanationEl.className = 'explanation wrong';
            explanationEl.innerHTML = `<strong>Қате!</strong><br>${q.explanation}`;
        }

        explanationEl.style.display = 'block';
        nextBtn.style.display = 'block';
    }

    function nextQuestion() {
        currentQuestionIndex++;
        if (currentQuestionIndex < questions.length) {
            showQuestion();
        } else {
            showResult();
        }
    }

    function showResult() {
        gameScreen.style.display = 'none';
        resultScreen.style.display = 'block';
        document.getElementById('final-score').textContent = `${score} / ${questions.length}`;

        const feedback = document.getElementById('feedback-text');
        if (score === questions.length) {
            feedback.textContent = "Өте тамаша! Сіз — дәлелді медицинаның нағыз сарапшысысыз! 🏆";
        } else if (score >= questions.length / 2) {
            feedback.textContent = "Жақсы нәтиже! Бірақ ғылыми зерттеулерді (EBD) әлі де қарап шығу артық етпейді. 📚";
        } else {
            feedback.textContent = "Ескірген мифтерге сенбеңіз! Дәлелді стоматология негіздерін қайта қарап шығуды ұсынамыз. 🩺";
        }
    }

    function restartGame() {
        startGame();
    }

    // Ойынды бастау
    startGame();
</script>

</body>
</html>
