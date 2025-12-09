<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tap Game - Earn Diamonds</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
            color: white;
        }

        .container {
            max-width: 400px;
            margin: 0 auto;
            text-align: center;
        }

        .header {
            margin-bottom: 30px;
        }

        .header h1 {
            font-size: 28px;
            margin-bottom: 10px;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
        }

        .stats {
            display: flex;
            justify-content: space-around;
            background: rgba(255,255,255,0.1);
            padding: 15px;
            border-radius: 15px;
            margin-bottom: 20px;
            backdrop-filter: blur(10px);
        }

        .stat-item {
            text-align: center;
        }

        .stat-value {
            font-size: 24px;
            font-weight: bold;
            color: #FFD700;
        }

        .stat-label {
            font-size: 12px;
            opacity: 0.8;
            margin-top: 5px;
        }

        .tap-area {
            width: 200px;
            height: 200px;
            margin: 30px auto;
            background: radial-gradient(circle, #FFD700, #FF8C00);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
            transition: transform 0.1s;
            position: relative;
            overflow: hidden;
        }

        .tap-area:active {
            transform: scale(0.95);
        }

        .tap-text {
            font-size: 32px;
            font-weight: bold;
            color: white;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.5);
        }

        .multiplier {
            position: absolute;
            bottom: 20px;
            color: #4CAF50;
            font-weight: bold;
        }

        .controls {
            display: flex;
            gap: 10px;
            justify-content: center;
            margin-top: 20px;
            flex-wrap: wrap;
        }

        .btn {
            background: linear-gradient(135deg, #4CAF50, #2E7D32);
            border: none;
            color: white;
            padding: 12px 20px;
            border-radius: 25px;
            cursor: pointer;
            font-weight: bold;
            transition: all 0.3s;
        }

        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(0,0,0,0.2);
        }

        .btn:disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }

        .boost {
            background: linear-gradient(135deg, #FF9800, #F57C00);
        }

        .shop {
            background: linear-gradient(135deg, #9C27B0, #7B1FA2);
        }

        .click-effect {
            position: absolute;
            pointer-events: none;
            animation: floatUp 1s forwards;
            font-size: 24px;
            font-weight: bold;
            color: #FFD700;
        }

        @keyframes floatUp {
            0% { transform: translateY(0); opacity: 1; }
            100% { transform: translateY(-50px); opacity: 0; }
        }

        .notification {
            position: fixed;
            top: 20px;
            right: 20px;
            background: rgba(0,0,0,0.8);
            color: white;
            padding: 15px;
            border-radius: 10px;
            animation: slideIn 0.3s;
            z-index: 1000;
        }

        @keyframes slideIn {
            from { transform: translateX(100%); }
            to { transform: translateX(0); }
        }

        .leaderboard {
            background: rgba(255,255,255,0.1);
            padding: 15px;
            border-radius: 15px;
            margin-top: 20px;
        }

        .leaderboard h3 {
            margin-bottom: 10px;
        }

        .player {
            display: flex;
            justify-content: space-between;
            padding: 8px 0;
            border-bottom: 1px solid rgba(255,255,255,0.2);
        }

        .player:last-child {
            border-bottom: none;
        }

        .rank {
            width: 30px;
            text-align: center;
        }

        .name {
            flex: 1;
            text-align: left;
            padding-left: 10px;
        }

        .score {
            font-weight: bold;
            color: #FFD700;
        }

        .diamond {
            color: #FFD700;
            margin-right: 5px;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>💰 TAP GAME</h1>
            <p>Тапай и зарабатывай алмазы!</p>
        </div>

        <div class="stats">
            <div class="stat-item">
                <div class="stat-value" id="score">0</div>
                <div class="stat-label">ОЧКИ</div>
            </div>
            <div class="stat-item">
                <div class="stat-value" id="level">1</div>
                <div class="stat-label">УРОВЕНЬ</div>
            </div>
            <div class="stat-item">
                <div class="stat-value">
                    <span class="diamond">💎</span>
                    <span id="diamonds">100</span>
                </div>
                <div class="stat-label">АЛМАЗЫ</div>
            </div>
        </div>

        <div class="tap-area" id="tapArea">
            <div class="tap-text">TAP!</div>
            <div class="multiplier" id="multiplier">x1</div>
        </div>

        <div class="controls">
            <button class="btn boost" onclick="buyBoost('2x')">
                🚀 2x Множитель<br>10 💎
            </button>
            <button class="btn boost" onclick="buyBoost('auto')">
                🤖 Авто-кликер<br>25 💎
            </button>
            <button class="btn shop" onclick="showShop()">
                🛒 Магазин
            </button>
        </div>

        <div class="leaderboard">
            <h3>🏆 ТОП ИГРОКОВ</h3>
            <div id="leaderboard">
                <!-- Динамически заполнится -->
            </div>
        </div>
    </div>

    <script>
        // Данные игры
        let score = 0;
        let level = 1;
        let diamonds = 100;
        let multiplier = 1;
        let autoClicker = null;

        // Элементы DOM
        const scoreEl = document.getElementById('score');
        const levelEl = document.getElementById('level');
        const diamondsEl = document.getElementById('diamonds');
        const multiplierEl = document.getElementById('multiplier');
        const tapArea = document.getElementById('tapArea');
        const leaderboardEl = document.getElementById('leaderboard');

        // Загрузка данных из URL параметров
        function loadUserData() {
            const params = new URLSearchParams(window.location.search);
            const userId = params.get('user_id') || 'demo';
            
            // Загрузка данных из localStorage
            const savedData = localStorage.getItem(`tap_game_${userId}`);
            if (savedData) {
                const data = JSON.parse(savedData);
                score = data.score || 0;
                level = data.level || 1;
                diamonds = data.diamonds || 100;
                multiplier = data.multiplier || 1;
            }
            
            updateUI();
            loadLeaderboard();
        }

        // Обновление интерфейса
        function updateUI() {
            scoreEl.textContent = formatNumber(score);
            levelEl.textContent = level;
            diamondsEl.textContent = diamonds;
            multiplierEl.textContent = `x${multiplier}`;
        }

        // Форматирование чисел
        function formatNumber(num) {
            if (num >= 1000000) return (num / 1000000).toFixed(1) + 'M';
            if (num >= 1000) return (num / 1000).toFixed(1) + 'K';
            return num;
        }

        // Обработчик тапа
        tapArea.addEventListener('click', (e) => {
            const points = 1 * multiplier;
            score += points;
            
            // Анимация
            createClickAnimation(e, `+${points}`);
            
            // Проверка уровня
            checkLevel();
            
            // Начисление алмазов каждые 1000 очков
            if (score % 1000 === 0) {
                diamonds += 1;
                showNotification('🎉 +1 алмаз!');
            }
            
            updateUI();
            saveProgress();
        });

        // Анимация клика
        function createClickAnimation(event, text) {
            const anim = document.createElement('div');
            anim.className = 'click-effect';
            anim.textContent = text;
            
            const rect = tapArea.getBoundingClientRect();
            anim.style.left = (event.clientX - rect.left - 10) + 'px';
            anim.style.top = (event.clientY - rect.top - 10) + 'px';
            
            tapArea.appendChild(anim);
            setTimeout(() => anim.remove(), 1000);
        }

        // Проверка уровня
        function checkLevel() {
            const newLevel = Math.floor(Math.sqrt(score / 100)) + 1;
            if (newLevel > level) {
                level = newLevel;
                showNotification(`🎊 Уровень ${level}!`);
            }
        }

        // Уведомление
        function showNotification(message) {
            const notif = document.createElement('div');
            notif.className = 'notification';
            notif.textContent = message;
            
            document.body.appendChild(notif);
            setTimeout(() => notif.remove(), 3000);
        }

        // Покупка буста
        function buyBoost(type) {
            if (type === '2x') {
                if (diamonds >= 10) {
                    diamonds -= 10;
                    multiplier *= 2;
                    showNotification('🚀 Множитель x2 активирован!');
                    setTimeout(() => {
                        multiplier /= 2;
                        updateUI();
                        showNotification('⏰ Множитель закончился');
                    }, 30000); // 30 секунд
                } else {
                    showNotification('❌ Не хватает алмазов!');
                }
            } else if (type === 'auto') {
                if (diamonds >= 25) {
                    diamonds -= 25;
                    startAutoClicker();
                } else {
                    showNotification('❌ Не хватает алмазов!');
                }
            }
            
            updateUI();
            saveProgress();
        }

        // Авто-кликер
        function startAutoClicker() {
            if (autoClicker) clearInterval(autoClicker);
            
            showNotification('🤖 Авто-кликер активирован!');
            
            autoClicker = setInterval(() => {
                const points = 1 * multiplier;
                score += points;
                
                // Случайная позиция для анимации
                const x = Math.random() * 160 + 20;
                const y = Math.random() * 160 + 20;
                createClickAnimation({ clientX: x, clientY: y }, `+${points}`);
                
                checkLevel();
                updateUI();
                saveProgress();
            }, 1000);
            
            // Остановка через 60 секунд
            setTimeout(() => {
                clearInterval(autoClicker);
                autoClicker = null;
                showNotification('⏰ Авто-кликер закончился');
            }, 60000);
        }

        // Магазин
        function showShop() {
            showNotification('🛒 Магазин скоро откроется!');
        }

        // Загрузка лидерборда (демо данные)
        function loadLeaderboard() {
            const demoPlayers = [
                { name: 'Ты', score: score, rank: 1 },
                { name: 'ProPlayer', score: 15000, rank: 2 },
                { name: 'TapMaster', score: 12000, rank: 3 },
                { name: 'DiamondKing', score: 9500, rank: 4 },
                { name: 'Newbie', score: 3000, rank: 5 }
            ];
            
            leaderboardEl.innerHTML = demoPlayers.map(player => `
                <div class="player">
                    <div class="rank">${player.rank}</div>
                    <div class="name">${player.name}</div>
                    <div class="score">${formatNumber(player.score)}</div>
                </div>
            `).join('');
        }

        // Сохранение прогресса
        function saveProgress() {
            const params = new URLSearchParams(window.location.search);
            const userId = params.get('user_id') || 'demo';
            
            const data = {
                score,
                level,
                diamonds,
                multiplier,
                savedAt: Date.now()
            };
            
            localStorage.setItem(`tap_game_${userId}`, JSON.stringify(data));
            
            // Отправка данных в бот (если в iframe Telegram)
            if (window.Telegram && window.Telegram.WebApp) {
                window.Telegram.WebApp.sendData(JSON.stringify(data));
            }
        }

        // Инициализация
        window.addEventListener('load', () => {
            loadUserData();
            
            // Авто-сохранение каждые 10 секунд
            setInterval(saveProgress, 10000);
            
            // Сохранение при закрытии
            window.addEventListener('beforeunload', saveProgress);
        });
    </script>
</body>
</html>
[tapalka.index.html.html](https://github.com/user-attachments/files/24056932/tapalka.index.html.html)
