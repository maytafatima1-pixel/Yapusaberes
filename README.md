<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simulador de Costos Agrícolas</title>
    <style>
        :root {
            --primary: #2c5e3b;
            --primary-light: #eaf2ec;
            --accent: #f4e0a5;
            --text: #2d3748;
            --bg: #f7fafc;
            --card-bg: #ffffff;
            --success: #38a169;
            --error: #e53e3e;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
            user-select: none;
        }

        body {
            background-color: var(--bg);
            color: var(--text);
            padding-bottom: 20px;
        }

        /* Pantalla de Bienvenida / Carátula */
        .welcome-screen {
            position: fixed;
            top: 0; left: 0; right: 0; bottom: 0;
            background: white;
            z-index: 3000;
            padding: 25px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: space-between;
            text-align: center;
            overflow-y: auto;
        }

        .logo-container {
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 20px;
            margin-top: 10px;
            width: 100%;
        }

        .logo-img {
            height: 90px;
            object-fit: contain;
        }

        .project-title {
            font-size: 1.25rem;
            font-weight: bold;
            color: var(--primary);
            line-height: 1.4;
            margin: 20px 0;
            padding: 10px;
            border-bottom: 3px solid var(--accent);
        }

        .welcome-instruction {
            font-size: 0.95rem;
            color: #4a5568;
            margin-bottom: 25px;
            line-height: 1.5;
        }

        .btn-start-game {
            background: var(--primary);
            color: white;
            border: none;
            padding: 16px 32px;
            font-size: 1.2rem;
            font-weight: bold;
            border-radius: 50px;
            cursor: pointer;
            width: 100%;
            max-width: 300px;
            box-shadow: 0 4px 10px rgba(44, 94, 59, 0.3);
            transition: transform 0.2s;
            animation: pulse 1.5s infinite alternate;
        }

        /* Marcador Superior en Juego */
        .top-dashboard {
            position: sticky;
            top: 0;
            background: var(--primary);
            color: white;
            padding: 12px 15px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            z-index: 1000;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
        }

        .timer-box {
            font-size: 1.2rem;
            font-weight: bold;
            background: rgba(0, 0, 0, 0.2);
            padding: 5px 12px;
            border-radius: 20px;
        }

        .timer-box.urgent {
            background: var(--error);
        }

        .score-box {
            font-size: 0.9rem;
            font-weight: bold;
        }

        /* Contenedor Principal */
        .game-container {
            max-width: 500px;
            margin: 20px auto;
            padding: 0 15px;
            position: relative;
        }

        .stage-title {
            font-size: 1rem;
            font-weight: bold;
            color: var(--primary);
            margin-bottom: 15px;
            text-align: center;
            background: var(--primary-light);
            padding: 8px;
            border-radius: 8px;
        }

        .question-card {
            background: var(--card-bg);
            border-radius: 20px;
            padding: 20px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.05);
            margin-bottom: 15px;
            border: 1px solid #e2e8f0;
            text-align: center;
        }

        .activity-tag {
            display: inline-block;
            background: #edf2f7;
            padding: 4px 10px;
            border-radius: 12px;
            font-size: 0.8rem;
            font-weight: bold;
            color: #4a5568;
            margin-bottom: 10px;
        }

        .detail-text {
            font-size: 1.15rem;
            font-weight: bold;
            margin-bottom: 5px;
            color: var(--text);
        }

        .quantity-text {
            font-size: 0.9rem;
            color: #718096;
            margin-bottom: 20px;
        }

        .options-grid {
            display: flex;
            flex-direction: column;
            gap: 12px;
        }

        .option-btn {
            background: white;
            border: 2px solid #e2e8f0;
            border-radius: 12px;
            padding: 14px;
            font-size: 1.05rem;
            font-weight: bold;
            color: var(--text);
            cursor: pointer;
            transition: all 0.1s;
            outline: none;
            -webkit-tap-highlight-color: transparent;
        }

        /* Estados de validación instantánea */
        .option-btn.reveal-correct {
            background-color: #d1e7dd !important;
            border-color: var(--success) !important;
            color: #0f5132 !important;
        }

        .option-btn.reveal-incorrect {
            background-color: #f8d7da !important;
            border-color: var(--error) !important;
            color: #842029 !important;
        }

        /* Pantallas de Cierre Absolutas */
        .screen-overlay {
            display: none;
            position: fixed;
            top: 0; left: 0; right: 0; bottom: 0;
            background: white;
            z-index: 2000;
            padding: 25px;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            text-align: center;
            overflow-y: auto;
        }

        .screen-overlay h2 {
            font-size: 1.6rem;
            margin-bottom: 15px;
        }

        .screen-overlay p {
            font-size: 0.95rem;
            line-height: 1.5;
            color: #4a5568;
            margin-bottom: 20px;
        }

        .app-promo-box {
            background: #fdf6e2;
            border: 2px solid var(--accent);
            padding: 15px;
            border-radius: 15px;
            margin-bottom: 20px;
            width: 100%;
            max-width: 360px;
        }

        .app-logo-mini {
            height: 65px;
            margin-bottom: 5px;
        }

        .app-phrase {
            font-size: 0.85rem;
            font-style: italic;
            color: #744210;
            margin-bottom: 10px;
            font-weight: bold;
        }

        .btn-download {
            display: inline-block;
            background: #dd6b20;
            color: white;
            text-decoration: none;
            padding: 10px 20px;
            border-radius: 8px;
            font-weight: bold;
            font-size: 0.9rem;
            box-shadow: 0 3px 6px rgba(0,0,0,0.1);
        }

        .btn-action {
            background: var(--primary);
            color: white;
            border: none;
            padding: 14px 28px;
            font-size: 1rem;
            font-weight: bold;
            border-radius: 12px;
            cursor: pointer;
            width: 100%;
            max-width: 260px;
        }

        /* Firma de agua ultra-sutil y abstracta (Mínimo impacto visual) */
        .signature-watermark {
            font-size: 0.65rem;
            color: #e2e8f0;
            font-weight: 300;
            letter-spacing: 2px;
            position: absolute;
            bottom: -35px;
            right: 15px;
            opacity: 0.6;
        }

        .welcome-screen .signature-watermark {
            position: relative;
            bottom: 0;
            right: 0;
            margin-top: 15px;
        }

        .screen-overlay .signature-watermark {
            position: relative;
            bottom: 0;
            right: 0;
            margin-top: 20px;
        }

        @keyframes pulse {
            from { transform: scale(1); }
            to { transform: scale(1.03); }
        }

        .shake {
            animation: shake 0.2s ease-in-out 0s 2;
        }

        @keyframes shake {
            0% { margin-left: 0; }
            50% { margin-left: 6px; }
            100% { margin-left: -6px; }
        }
    </style>
</head>
<body>

    <!-- CARÁTULA INICIAL -->
    <div id="welcome-screen" class="welcome-screen">
        <div class="logo-container">
            <img class="logo-img" src="WhatsApp Image 2026-05-29 at 11.52.29 AM.jpeg" alt="Logo UMSA">
            <img class="logo-img" src="WhatsApp Image 2026-05-29 at 11.51.36 AM.jpeg" alt="Logo Yapu Aroma">
        </div>
        
        <div class="project-title">
            Desarrollo de un sistema de cooperación agropecuaria a familias productoras de los municipios de Sica Sica y Patacamaya
        </div>

        <div class="welcome-instruction">
            <strong>¿Cuánto cuesta producir una hectárea de quinua?</strong><br>
            Pon a prueba tu conocimiento calculando los costos reales de campo. ¡Cuentas con 60 segundos iniciales!
        </div>

        <button class="btn-start-game" onclick="startGame()">¡Juega Productor!</button>
        
        <div class="signature-watermark">⟨ f.m ⟩</div>
    </div>

    <!-- CONTROL TOTAL EN JUEGO -->
    <div class="top-dashboard">
        <div class="score-box">DESAFÍO: <span id="current-index">1</span>/14</div>
        <div id="timer-display" class="timer-box">⏱️ <span id="seconds">60</span>s</div>
    </div>

    <div class="game-container">
        <div id="stage-indicator" class="stage-title">Fase</div>

        <div id="question-box" class="question-card">
            <div id="act-tag" class="activity-tag">-</div>
            <div id="det-text" class="detail-text">-</div>
            <div id="cant-text" class="quantity-text">-</div>

            <div class="options-grid">
                <button class="option-btn" onclick="processSelection(0)">-</button>
                <button class="option-btn" onclick="processSelection(1)">-</button>
                <button class="option-btn" onclick="processSelection(2)">-</button>
            </div>
        </div>
        
        <div class="signature-watermark">⟨ f.m ⟩</div>
    </div>

    <!-- PANTALLA EXCELENTE (GANADOR) -->
    <div id="win-screen" class="screen-overlay">
        <h2 style="color: var(--success);">🏆 ¡Desafío Superado!</h2>
        <p><strong>¡Felicidades! Ahora entiendes el valor real de registrar tus costos.</strong><br><br>Para los productores, conocer este presupuesto es la clave para fijar precios justos y evitar pérdidas. Para quienes consumen, ver estos números nos ayuda a valorar el enorme esfuerzo y capital que las familias del campo invierten en cada plato de comida.</p>
        
        <div class="app-promo-box">
            <img class="app-logo-mini" src="WhatsApp Image 2026-05-29 at 11.51.36 AM.jpeg" alt="Yapu Aroma">
            <div class="app-phrase">"Organiza tus cultivos y lleva el control exacto de tus gastos."</div>
            <a class="btn-download" href="https://n9.cl/oczs0" target="_blank">📱 Instalar Yapu Aroma</a>
        </div>

        <button class="btn-action" onclick="restartToMenu()">Volver al Inicio</button>
        <div class="signature-watermark">⟨ f.m ⟩</div>
    </div>

    <!-- PANTALLA FIN DE TIEMPO (PERDEDOR) -->
    <div id="lose-screen" class="screen-overlay">
        <h2 style="color: var(--error);">⏱️ El tiempo terminó</h2>
        <p>Calcular costos de memoria en el campo es difícil y puede hacernos perder dinero sin que nos demos cuenta. ¡No te preocupes ni te rindas, el aprendizaje es un proceso!</p>
        
        <div class="app-promo-box">
            <img class="app-logo-mini" src="WhatsApp Image 2026-05-29 at 11.51.36 AM.jpeg" alt="Yapu Aroma">
            <div class="app-phrase">"Organiza tus cultivos y lleva el control exacto de tus gastos."</div>
            <p style="font-size:0.8rem; margin-bottom:10px; color:#555;">Usa esta aplicación gratuita para ordenar todas las cuentas de tu producción de forma automática.</p>
            <a class="btn-download" href="https://n9.cl/oczs0" target="_blank">📱 Descargar Aplicación</a>
        </div>

        <button class="btn-action" style="background:#4a5568;" onclick="restartToMenu()">Reintentar Juego</button>
        <div class="signature-watermark">⟨ f.m ⟩</div>
    </div>

    <script>
        const rawDataset = [
            { act: "Preparación del suelo", cant: "1 unidad", det: "Alquiler tractor (arado y rastreo)", cost: 1300, group: "Cuadro 1: Preparación y Siembra", alt: [1500, 1100] },
            { act: "Preparación del suelo", cant: "2 jornal", det: "Tractorista (arado y rastreo)", cost: 800, group: "Cuadro 1: Preparación y Siembra", alt: [600, 950] },
            { act: "Siembra", cant: "2 jornal", det: "Mano de obra", cost: 300, group: "Cuadro 1: Preparación y Siembra", alt: [450, 200] },
            { act: "Siembra", cant: "6 kilos", det: "Semilla", cost: 360, group: "Cuadro 1: Preparación y Siembra", alt: [300, 420] },
            { act: "Manejo del cultivo", cant: "2 unidades", det: "Herbicida", cost: 320, group: "Cuadro 2: Manejo del Cultivo", alt: [220, 400] },
            { act: "Manejo del cultivo", cant: "1 jornal", det: "Fumigación (mano de obra)", cost: 150, group: "Cuadro 2: Manejo del Cultivo", alt: [250, 100] },
            { act: "Manejo del cultivo", cant: "1 unidad", det: "Fertilizante", cost: 120, group: "Cuadro 2: Manejo del Cultivo", alt: [180, 90] },
            { act: "Manejo del cultivo", cant: "1 jornal", det: "Aplicación (mano de obra)", cost: 150, group: "Cuadro 2: Manejo del Cultivo", alt: [130, 200] },
            { act: "Cosecha", cant: "4 jornal", det: "Mano de obra", cost: 600, group: "Cuadro 3: Cosecha y Venta", alt: [500, 700] },
            { act: "Cosecha", cant: "1 unidad", det: "Trillado (tractor)", cost: 150, group: "Cuadro 3: Cosecha y Venta", alt: [250, 100] },
            { act: "Cosecha", cant: "2 jornal", det: "Trillado (mano de obra)", cost: 300, group: "Cuadro 3: Cosecha y Venta", alt: [400, 250] },
            { act: "Venta", cant: "3 jornal", det: "Venteado", cost: 450, group: "Cuadro 3: Cosecha y Venta", alt: [350, 500] },
            { act: "Venta", cant: "1 unidad", det: "Transporte", cost: 150, group: "Cuadro 3: Cosecha y Venta", alt: [120, 200] },
            { act: "Venta", cant: "1 unidad", det: "Refrigerio", cost: 100, group: "Cuadro 3: Cosecha y Venta", alt: [80, 150] }
        ];

        let activeDataset = [];
        let currentIndex = 0;
        let timeLeft = 60;
        let gameTimer = null;
        let currentOptions = [];
        let isInputBlocked = false;

        function startGame() {
            document.getElementById('welcome-screen').style.display = 'none';
            document.getElementById('win-screen').style.display = 'none';
            document.getElementById('lose-screen').style.display = 'none';
            
            activeDataset = [...rawDataset].sort(() => Math.random() - 0.5);
            
            currentIndex = 0;
            timeLeft = 60;
            isInputBlocked = false;
            document.getElementById('seconds').innerText = timeLeft;

            clearInterval(gameTimer);
            gameTimer = setInterval(() => {
                timeLeft--;
                document.getElementById('seconds').innerText = timeLeft;
                const timerDisplay = document.getElementById('timer-display');
                if(timeLeft <= 15) timerDisplay.classList.add('urgent');
                else timerDisplay.classList.remove('urgent');

                if (timeLeft <= 0) handleEndGame(false);
            }, 1000);

            renderQuestion();
        }

        function renderQuestion() {
            if (currentIndex >= activeDataset.length) {
                handleEndGame(true);
                return;
            }

            isInputBlocked = false;
            const currentItem = activeDataset[currentIndex];
            
            document.getElementById('current-index').innerText = currentIndex + 1;
            document.getElementById('stage-indicator').innerText = currentItem.group;
            document.getElementById('act-tag').innerText = currentItem.act;
            document.getElementById('det-text').innerText = currentItem.det;
            document.getElementById('cant-text').innerText = `Cantidad total: ${currentItem.cant}`;

            const choices = [currentItem.cost, ...currentItem.alt];
            currentOptions = choices.sort(() => Math.random() - 0.5);

            const optionButtons = document.querySelectorAll('.option-btn');
            optionButtons.forEach((btn, index) => {
                btn.className = 'option-btn';
                btn.innerText = `Bs. ${currentOptions[index].toLocaleString()}`;
            });
        }

        function processSelection(selectedIdx) {
            if (isInputBlocked) return;
            isInputBlocked = true;

            const currentItem = activeDataset[currentIndex];
            const selectedValue = currentOptions[selectedIdx];
            const optionButtons = document.querySelectorAll('.option-btn');

            let correctBtnIdx = currentOptions.indexOf(currentItem.cost);

            if (selectedValue === currentItem.cost) {
                optionButtons[selectedIdx].classList.add('reveal-correct');
                timeLeft += 5;
                document.getElementById('seconds').innerText = timeLeft;
            } else {
                optionButtons[selectedIdx].classList.add('reveal-incorrect');
                optionButtons[correctBtnIdx].classList.add('reveal-correct');
                
                const cardBox = document.getElementById('question-box');
                cardBox.classList.add('shake');
                setTimeout(() => cardBox.classList.remove('shake'), 300);

                timeLeft = Math.max(0, timeLeft - 5);
                document.getElementById('seconds').innerText = timeLeft;
            }

            setTimeout(() => {
                currentIndex++;
                renderQuestion();
            }, 1000);
        }

        function handleEndGame(isWinner) {
            clearInterval(gameTimer);
            if (isWinner) {
                document.getElementById('win-screen').style.display = 'flex';
            } else {
                document.getElementById('lose-screen').style.display = 'flex';
            }
        }

        function restartToMenu() {
            document.getElementById('win-screen').style.display = 'none';
            document.getElementById('lose-screen').style.display = 'none';
            document.getElementById('welcome-screen').style.display = 'flex';
        }
    </script>
</body>
</html>
