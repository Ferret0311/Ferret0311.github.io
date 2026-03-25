# Ferret0311.github.io
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Симулятор РОПа</title>
    <style>
        /* Основные стили страницы */
        body, html {
            margin: 0;
            padding: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
            background-color: #1a1a1a;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            color: white;
        }

        #game-container {
            position: relative;
            width: 100vw;
            height: 100vh;
            /* Исправлено: Относительный путь для фона */
            background-image: url('Изображения/BG.png'); 
            background-size: cover;
            background-position: center;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
        }

        .overlay-screen {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.85);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 10;
            padding: 20px;
            box-sizing: border-box;
            text-align: center;
        }

        #instructions-screen .content {
            max-width: 700px;
            text-align: left;
            line-height: 1.6;
            background: rgba(255, 255, 255, 0.05);
            padding: 30px;
            border-radius: 15px;
            border: 1px solid #2196F3;
        }

        #instructions-screen h2 {
            color: #2196F3;
            margin-top: 0;
            text-align: center;
        }

        /* Обновленный красивый стиль заголовка */
        h1 {
            font-size: 5rem;
            margin-bottom: 2rem;
            font-weight: 900;
            text-transform: uppercase;
            letter-spacing: 5px;
            background: linear-gradient(to bottom, #fff 20%, #2196F3 80%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            filter: drop-shadow(0 0 15px rgba(33, 150, 243, 0.5));
            margin-top: 0;
        }

        .btn {
            background-color: #2196F3;
            color: white;
            border: none;
            padding: 15px 40px;
            font-size: 1.2rem;
            cursor: pointer;
            border-radius: 5px;
            transition: background 0.3s;
            margin-top: 20px;
        }

        .btn:hover {
            background-color: #1976D2;
        }

        /* ПАНЕЛЬ СТАТОВ */
        #stats-container {
            position: absolute;
            top: 20px;
            left: 20px;
            display: flex;
            flex-direction: column;
            gap: 10px;
            background: rgba(0, 0, 0, 0.6);
            padding: 15px;
            border-radius: 10px;
            z-index: 5;
        }

        .stat-row {
            display: flex;
            align-items: center;
            gap: 15px;
        }

        .stat-name {
            width: 130px; 
            font-size: 14px;
            font-weight: bold;
        }

        .progress-bar-bg {
            width: 300px; 
            height: 14px;
            background: #444;
            border-radius: 7px;
            overflow: hidden;
        }

        .progress-bar-fill {
            height: 100%;
            width: 50%;
            transition: width 0.5s ease-in-out;
        }

        /* ПАНЕЛЬ ФИНАНСОВ */
        #finance-container {
            position: absolute;
            top: 20px;
            right: 20px;
            background: rgba(0, 0, 0, 0.7);
            padding: 15px;
            border-radius: 10px;
            border: 1px solid #FFD700;
            z-index: 5;
            text-align: center;
            min-width: 180px;
        }

        #budget-display {
            font-size: 20px;
            color: #FFD700;
            font-weight: bold;
            margin-bottom: 10px;
            text-align: center;
            width: 100%;
        }

        .finance-buttons {
            display: flex;
            flex-direction: column;
            gap: 8px;
        }

        .fin-btn {
            background: #333;
            color: #fff;
            border: 1px solid #555;
            padding: 8px 12px;
            font-size: 13px;
            cursor: pointer;
            position: relative;
            border-radius: 4px;
        }

        .fin-btn:hover {
            background: #444;
            border-color: #FFD700;
        }

        .fin-btn:disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }

        .tooltip {
            visibility: hidden;
            width: 200px;
            background-color: #f9f9f9;
            color: #333;
            text-align: center;
            border-radius: 6px;
            padding: 10px;
            position: absolute;
            z-index: 11;
            right: 110%;
            top: 50%;
            transform: translateY(-50%);
            opacity: 0;
            transition: opacity 0.3s;
            font-size: 12px;
            line-height: 1.4;
            box-shadow: 0 4px 8px rgba(0,0,0,0.3);
            pointer-events: none;
        }

        .fin-btn:hover .tooltip {
            visibility: visible;
            opacity: 1;
        }

        canvas {
            display: block;
            cursor: pointer;
        }
    </style>
</head>
<body>

    <div id="game-container">
        <!-- Экран 1: Старт -->
        <div id="start-screen" class="overlay-screen">
            <h1>Симулятор РОПа</h1>
            <button class="btn" onclick="showInstructions()">Начать игру</button>
        </div>

        <!-- Экран 2: Инструкция -->
        <div id="instructions-screen" class="overlay-screen" style="display: none;">
            <div class="content">
                <h2>Инструкция по игре</h2>
                <p>Вы — руководитель отдела продаж (РОП). За один рабочий день вам предстоит принять множество решений.</p>
                <p><strong>Ваши ключевые показатели:</strong></p>
                <ul>
                    <li><strong>Эффективность отдела</strong> — выполнение плана.</li>
                    <li><strong>Мораль команды</strong> — настроение сотрудников.</li>
                    <li><strong>Личный авторитет</strong> — уважение коллег.</li>
                    <li><strong>Бюджет</strong> — ваши финансовые ресурсы, берегите их.</li>
                </ul>
                <p><strong>Последовательность игры:</strong></p>
                <ol>
                    <li>Получайте диалоги с коллегами</li>
                    <li>Выбирайте один из трёх вариантов ответа</li>
                    <li>Следите за шкалами слева-вверху</li>
                </ol>
                <p>Совет: ищите баланс и держите показатели выше 70%.</p>
            </div>
            <button class="btn" onclick="startGame()">К игре!</button>
        </div>

        <!-- Панель характеристик -->
        <div id="stats-container" style="display: none;">
            <div class="stat-row">
                <span class="stat-name">Эффективность</span>
                <div class="progress-bar-bg"><div id="efficiencyBar" class="progress-bar-fill" style="background: #4CAF50;"></div></div>
            </div>
            <div class="stat-row">
                <span class="stat-name">Мораль</span>
                <div class="progress-bar-bg"><div id="moraleBar" class="progress-bar-fill" style="background: #FF9800;"></div></div>
            </div>
            <div class="stat-row">
                <span class="stat-name">Авторитет</span>
                <div class="progress-bar-bg"><div id="authorityBar" class="progress-bar-fill" style="background: #2196F3;"></div></div>
            </div>
        </div>

        <!-- Панель Финансов -->
        <div id="finance-container" style="display: none;">
            <div id="budget-display">Бюджет: 100</div>
            <div class="finance-buttons">
                <button class="fin-btn" onclick="spendMoney('bonuses')">
                    Бонусы сотрудникам
                    <span class="tooltip">Цена: 15<br>Мораль: +8%<br>Эффективность: -3%<br>(Мгновенная мотивация)</span>
                </button>
                <button class="fin-btn" onclick="spendMoney('training')">
                    Обучение персонала
                    <span class="tooltip">Цена: 25<br>Эффективность: +7%<br>Мораль: -2%<br>(Период адаптации)</span>
                </button>
                <button class="fin-btn" onclick="spendMoney('teambuilding')">
                    Командообразование
                    <span class="tooltip">Цена: 20<br>Мораль: +7%<br>Эффективность: -2%<br>(Сплочение команды)</span>
                </button>
            </div>
        </div>

        <canvas id="gameCanvas"></canvas>

        <div id="results-screen" class="overlay-screen" style="display: none;">
            <h2>Итоги дня</h2>
            <div id="finalStats" style="margin-bottom: 20px;"></div>
            <p id="resultText" style="font-style: italic;"></p>
            <button class="btn" onclick="location.reload()">Играть снова</button>
        </div>
    </div>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const startScreen = document.getElementById('start-screen');
        const instructionsScreen = document.getElementById('instructions-screen');
        const statsContainer = document.getElementById('stats-container');
        const financeContainer = document.getElementById('finance-container');
        const resultsScreen = document.getElementById('results-screen');

        // Исправлено: Относительные пути для всех персонажей
        const clientImage = new Image();
        clientImage.src = 'Изображения/Client.png';
        const dirImage = new Image();
        dirImage.src = 'Изображения/dir.png';
        const maryImage = new Image();
        maryImage.src = 'Изображения/mary.png';
        const findirImage = new Image();
        findirImage.src = 'Изображения/findir.png';
        const spec_andrewImage = new Image();
        spec_andrewImage.src = 'Изображения/spec_andrew.png';
        const men_elenaImage = new Image();
        men_elenaImage.src = 'Изображения/men_elena.png';

        let gameState = {
            efficiency: 50,
            morale: 50,
            authority: 50,
            budget: 100,
            currentStep: 0,
            activeButtons: []
        };

        const financeOptions = {
            bonuses: { cost: 15, efficiency: -3, morale: +8, authority: 0 },
            training: { cost: 25, efficiency: +7, morale: -2, authority: 0 },
            teambuilding: { cost: 20, efficiency: -2, morale: +7, authority: +2 }
        };

        const dialogs = [
            {
                speaker: "Утро",
                text: "Вы пришли в офис на 15 минут раньше. Планёрка через полчаса. Что сделаете?",
                options: [
                    { text: "Подготовлю детальные цифры по каждому менеджеру", effect: { efficiency: +15, morale: 0, authority: +5 } },
                    { text: "Пойду пить кофе с отделом, обсуждать выходные", effect: { efficiency: -5, morale: +15, authority: +5 } },
                    { text: "Запрусь в кабинете и буду разбирать почту", effect: { efficiency: +5, morale: -5, authority: 0 } }
                ]
            },
            {
                speaker: "Планёрка",
                text: "Менеджер Алексей снова не выполнил план по звонкам. Ваша реакция перед всеми?",
                options: [
                    { text: "Жестко отчитать при всех, чтобы другим неповадно было", effect: { efficiency: +10, morale: -15, authority: +10 } },
                    { text: "Предложить разобраться в причинах после планёрки", effect: { efficiency: +5, morale: +10, authority: +5 } },
                    { text: "Проигнорировать, чтобы не портить настроение утром", effect: { efficiency: -10, morale: 5, authority: -10 } }
                ]
            },
            {
                speaker: "Клиент",
                text: "Мы пересматриваем условия контракта, хотим скидку 20%.",
                options: [
                    { text: "Давайте найдем альтернативные опции вместо скидки.", effect: { efficiency: +10, morale: -5, authority: +10 } },
                    { text: "Я могу согласовать скидку, но только на этот раз.", effect: { efficiency: -10, morale: +10, authority: -5 } },
                    { text: "Наши цены не подлежат обсуждению", effect: { efficiency: +5, morale: -15, authority: +5 } }
                ]
            },
            {
                speaker: "Александр, директор",
                text: "Нам нужно увеличить продажи на 50% в следующем квартале. У нас есть возможность привлечь нового крупного клиента, но он требует особых условий.",
                options: [
                    { text: "Предлагаю разработать индивидуальное предложение с учетом их потребностей", effect: { efficiency: +15, morale: +5, authority: +10 } },
                    { text: "Мы не можем давать такие скидки, это убьет маржинальность.", effect: { efficiency: -5, morale: -10, authority: +5 } },
                    { text: "Давайте попробуем, я поговорю с командой.", effect: { efficiency: +5, morale: 15, authority: -5 } }
                ]
            },
            {
                speaker: "Елена, менеджер по продажам",
                text: "У меня конфликт с клиентом, он требует возврат средств, хотя все условия договора соблюдены.",
                options: [
                    { text: "Предлагаю найти компромиссное решение, например, дополнительные бонусы.", effect: { efficiency: +10, morale: +10, authority: +10 } },
                    { text: "Отстаивайте позицию компании, возврат невозможен.", effect: { efficiency: +5, morale: -15, authority: +15 } },
                    { text: "Давайте сделаем частичный возврат ради сохранения клиента.", effect: { efficiency: -5, morale: +5, authority: -5 } }
                ]
            },
            {
                speaker: "Мария, HR-менеджер",
                text: "В команде растет текучка кадров, сотрудники жалуются на высокие планы.",
                options: [
                    { text: "Предлагаю провести комплексный аудит рабочих процессов и внедрить систему грейдирования с пересмотром KPI.", effect: { efficiency: +8, morale: +18, authority: +7 } },
                    { text: "Временно заморозим планы, проведем серию встреч с каждым сотрудником для выявления проблем.", effect: { efficiency: +3, morale: +25, authority: -2 } },
                    { text: "Внедрим систему наставничества и пересмотрим систему мотивации, но планы оставим прежними.", effect: { efficiency: +12, morale: +12, authority: +12 } }
                ]
            },
            {
                speaker: "Андрей, ведущий менеджер",
                text: "Новый продукт плохо продается, клиенты не понимают его ценность.",
                options: [
                    { text: "Проведем анализ причин отказов и временно приостановим продажи для доработки продукта.", effect: { efficiency: -2, morale: +8, authority: -8 } },
                    { text: "Запустим программу лояльности для первых клиентов и предложим специальные условия.", effect: { efficiency: +7, morale: +15, authority: -3 } },
                    { text: "Создадим рабочую группу для разработки новой маркетинговой стратегии и проведем обучение с акцентом на уникальные преимущества продукта.", effect: { efficiency: +12, morale: +3, authority: +15 } }
                ]
            },
            {
                speaker: "Олег, финансовый директор",
                text: "Бюджет на маркетинг сокращен на 30%, нужно оптимизировать расходы.",
                options: [
                    { text: "Предложу временный мораторий на новые проекты и сосредоточимся на удержании текущих клиентов.", effect: { efficiency: +5, morale: +5, authority: +10 } },
                    { text: "Перераспределю бюджет на каналы с доказанной эффективностью и внедрю систему KPI для маркетологов.", effect: { efficiency: +18, morale: -8, authority: +18 } },
                    { text: "Оптимизируем расходы на внешние подрядчиков и переведем часть задач на внутренние ресурсы.", effect: { efficiency: +2, morale: -18, authority: +5 } }
                ]
            }
        ];

        function showInstructions() {
            startScreen.style.display = 'none';
            instructionsScreen.style.display = 'flex';
        }

        function startGame() {
            instructionsScreen.style.display = 'none';
            statsContainer.style.display = 'flex';
            financeContainer.style.display = 'block';
            resizeCanvas();
            updateStats();
            drawDialog();
        }

        function spendMoney(type) {
            const opt = financeOptions[type];
            if (gameState.budget >= opt.cost) {
                gameState.budget -= opt.cost;
                gameState.efficiency = Math.min(100, Math.max(0, gameState.efficiency + opt.efficiency));
                gameState.morale = Math.min(100, Math.max(0, gameState.morale + opt.morale));
                gameState.authority = Math.min(100, Math.max(0, gameState.authority + opt.authority));
                updateStats();
                drawDialog();
            } else {
                alert("Недостаточно средств в бюджете!");
            }
        }

        function updateStats() {
            document.getElementById('efficiencyBar').style.width = gameState.efficiency + '%';
            document.getElementById('moraleBar').style.width = gameState.morale + '%';
            document.getElementById('authorityBar').style.width = gameState.authority + '%';
            document.getElementById('budget-display').textContent = `Бюджет: ${gameState.budget.toLocaleString()}`;

            const buttons = document.querySelectorAll('.fin-btn');
            buttons.forEach(btn => {
                const type = btn.getAttribute('onclick').match(/'([^']+)'/)[1];
                btn.disabled = gameState.budget < financeOptions[type].cost;
            });
        }

        function resizeCanvas() {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
            if (statsContainer.style.display !== 'none') drawDialog();
        }

        window.addEventListener('resize', resizeCanvas);

        function getWrappedLines(text, maxWidth) {
            const words = text.split(' ');
            let lines = [];
            let currentLine = words[0];

            for (let i = 1; i < words.length; i++) {
                let word = words[i];
                let width = ctx.measureText(currentLine + " " + word).width;
                if (width < maxWidth) {
                    currentLine += " " + word;
                } else {
                    lines.push(currentLine);
                    currentLine = word;
                }
            }
            lines.push(currentLine);
            return lines;
        }

        function drawDialog() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            gameState.activeButtons = [];

            if (gameState.currentStep >= dialogs.length) {
                showFinalResults();
                return;
            }

            const current = dialogs[gameState.currentStep];

            let currentImg = null;
            if (current.speaker === "Клиент") currentImg = clientImage;
            else if (current.speaker === "Олег, финансовый директор") currentImg = findirImage;
            else if (current.speaker === "Андрей, ведущий менеджер") currentImg = spec_andrewImage;
            else if (current.speaker === "Мария, HR-менеджер") currentImg = maryImage;
            else if (current.speaker === "Елена, менеджер по продажам") currentImg = men_elenaImage;
            else if (current.speaker === "Александр, директор") currentImg = dirImage;

            if (currentImg && currentImg.complete) {
                const imgScale = (canvas.height * 1.4) / currentImg.height;
                const imgWidth = currentImg.width * imgScale;
                const imgHeight = currentImg.height * imgScale;
                const imgX = (canvas.width - imgWidth) / 2;
                const imgY = canvas.height - 1100; 
                ctx.drawImage(currentImg, imgX, imgY, imgWidth, imgHeight);
            }

            const boxWidth = Math.min(900, canvas.width - 40);
            const padding = 25;
            const spacingBetweenButtons = 10;

            ctx.font = "18px Arial";
            const questionLines = getWrappedLines(current.text, boxWidth - (padding * 2));
            const questionHeight = questionLines.length * 24;

            ctx.font = "16px Arial";
            let totalButtonsHeight = 0;
            const preparedOptions = current.options.map(opt => {
                const lines = getWrappedLines(opt.text, boxWidth - (padding * 2) - 30);
                const height = (lines.length * 20) + 20; 
                totalButtonsHeight += height + spacingBetweenButtons;
                return { lines, height };
            });

            const totalBoxHeight = 60 + questionHeight + totalButtonsHeight + padding;
            let boxY = canvas.height - totalBoxHeight - 30;
            if (boxY < 120) boxY = 120; 

            const boxX = (canvas.width - boxWidth) / 2;

            ctx.fillStyle = "rgba(0, 0, 0, 0.95)";
            ctx.strokeStyle = "#2196F3";
            ctx.lineWidth = 2;

            if (ctx.roundRect) {
                ctx.beginPath();
                ctx.roundRect(boxX, boxY, boxWidth, totalBoxHeight, 15);
                ctx.fill();
                ctx.stroke();
            } else {
                ctx.fillRect(boxX, boxY, boxWidth, totalBoxHeight);
                ctx.strokeRect(boxX, boxY, boxWidth, totalBoxHeight);
            }

            ctx.fillStyle = "#2196F3";
            ctx.font = "bold 20px Arial";
            ctx.fillText(current.speaker, boxX + padding, boxY + 35);

            ctx.fillStyle = "white";
            ctx.font = "18px Arial";
            questionLines.forEach((line, i) => {
                ctx.fillText(line, boxX + padding, boxY + 65 + (i * 24));
            });

            let currentButtonY = boxY + 75 + questionHeight;

            preparedOptions.forEach((optData, i) => {
                const btnX = boxX + padding;
                const btnW = boxWidth - (padding * 2);

                ctx.fillStyle = "rgba(255, 255, 255, 0.07)";
                ctx.strokeStyle = "rgba(33, 150, 243, 0.5)";

                if (ctx.roundRect) {
                    ctx.beginPath();
                    ctx.roundRect(btnX, currentButtonY, btnW, optData.height, 8);
                    ctx.fill();
                    ctx.stroke();
                } else {
                    ctx.fillRect(btnX, currentButtonY, btnW, optData.height);
                }

                ctx.fillStyle = "#e0e0e0";
                ctx.font = "16px Arial";
                optData.lines.forEach((line, lineIdx) => {
                    ctx.fillText(line, btnX + 15, currentButtonY + 22 + (lineIdx * 20));
                });

                gameState.activeButtons.push({
                    x: btnX, y: currentButtonY, w: btnW, h: optData.height, index: i
                });

                currentButtonY += optData.height + spacingBetweenButtons;
            });
        }

        canvas.addEventListener('mousedown', (e) => {
            if (statsContainer.style.display === 'none') return;
            const rect = canvas.getBoundingClientRect();
            const mouseX = e.clientX - rect.left;
            const mouseY = e.clientY - rect.top;

            gameState.activeButtons.forEach(btn => {
                if (mouseX >= btn.x && mouseX <= btn.x + btn.w &&
                    mouseY >= btn.y && mouseY <= btn.y + btn.h) {
                    const current = dialogs[gameState.currentStep];
                    const opt = current.options[btn.index];
                    gameState.efficiency = Math.min(100, Math.max(0, gameState.efficiency + opt.effect.efficiency));
                    gameState.morale = Math.min(100, Math.max(0, gameState.morale + opt.effect.morale));
                    gameState.authority = Math.min(100, Math.max(0, gameState.authority + opt.effect.authority));
                    gameState.currentStep++;
                    updateStats();
                    drawDialog();
                }
            });
        });

        function showFinalResults() {
            statsContainer.style.display = 'none';
            financeContainer.style.display = 'none';
            resultsScreen.style.display = 'flex';

            document.getElementById('finalStats').innerHTML = `
                <div style="
                    background: rgba(255, 255, 255, 0.07); 
                    backdrop-filter: blur(10px); 
                    -webkit-backdrop-filter: blur(10px);
                    border: 1px solid rgba(255, 255, 255, 0.1);
                    border-radius: 20px;
                    padding: 30px 40px;
                    display: inline-block;
                    text-align: left;
                    box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.37);
                ">
                    <div style="margin-bottom: 15px; font-size: 1.2rem;">
                        <span style="display: inline-block; width: 30px;">📊</span> 
                        <strong>Эффективность:</strong> ${gameState.efficiency}%
                    </div>
                    <div style="margin-bottom: 15px; font-size: 1.2rem;">
                        <span style="display: inline-block; width: 30px;">❤️</span> 
                        <strong>Мораль:</strong> ${gameState.morale}%
                    </div>
                    <div style="margin-bottom: 15px; font-size: 1.2rem;">
                        <span style="display: inline-block; width: 30px;">🎖️</span> 
                        <strong>Авторитет:</strong> ${gameState.authority}%
                    </div>
                    <div style="margin-top: 25px; padding-top: 15px; border-top: 1px solid rgba(255,255,255,0.1); font-size: 1.1rem; color: #FFD700;">
                        <span style="display: inline-block; width: 30px;">💰</span> 
                        <strong>Остаток бюджета:</strong> ${gameState.budget.toLocaleString()}
                    </div>
                </div>
            `;

            const resultMessage = document.getElementById('resultText');
            resultMessage.style.marginTop = "30px";
            resultMessage.style.fontSize = "1.5rem";
            resultMessage.textContent = gameState.efficiency > 60 ? 
                "✅ Вы успешно завершили день!" : "❌ Показатели слишком низки. Попробуйте еще раз.";
        }
    </script>
</body>
</html>
