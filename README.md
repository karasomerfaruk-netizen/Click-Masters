[index.html.txt](https://github.com/user-attachments/files/23731370/index.html.txt)
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Clicker Master</title>
    <style>
        /* GENEL STİLLER */
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            background-color: #2c3e50; 
            color: #ecf0f1; 
            padding-top: 20px;
        }
        #game-container {
            width: 95%;
            max-width: 1200px;
            background: #34495e;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.5);
            /* 3 Sütunlu düzen */
            display: grid;
            grid-template-columns: 1fr 1fr 1fr; 
            gap: 20px;
        }
        #stats {
            grid-column: 1 / 4; 
            text-align: center;
            background: #2c3e50;
            padding: 15px;
            border-radius: 8px;
        }
        #main-click-area {
            grid-column: 2 / 3; 
            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column; 
            margin-bottom: 20px;
        }
        #click-button {
            width: 100px;
            height: 100px;
            background-color: #e74c3c;
            border: none;
            border-radius: 50%;
            color: white;
            font-size: 1.2em;
            cursor: pointer;
            box-shadow: 0 4px #c0392b;
            transition: all 0.05s;
            margin-bottom: 10px; 
        }
        
        /* MAĞAZA MODAL STİLİ */
        #shop-modal {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 90%;
            max-width: 850px;
            background: #2c3e50;
            border-radius: 15px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.9);
            z-index: 1000;
            padding: 20px;
            display: none;
            border: 3px solid #f1c40f;
        }
        #shop-modal-content {
            display: grid;
            grid-template-columns: 1fr 1fr 1fr; 
            gap: 20px;
            margin-top: 15px;
        }
        #modal-close-btn {
            position: absolute;
            top: 10px;
            right: 15px;
            font-size: 1.8em;
            cursor: pointer;
            color: #e74c3c;
        }
        .shop-tab-button {
            background-color: #34495e;
            color: white;
            border: none;
            padding: 10px 15px;
            cursor: pointer;
            font-size: 1.1em;
            transition: background-color 0.2s;
            border-bottom: 3px solid transparent;
        }
        .shop-tab-button.active {
            background-color: #f1c40f;
            color: #2c3e50;
            border-bottom: 3px solid #e67e22;
        }
        .shop-tab-content {
            display: none;
            grid-column: 1 / 4; 
            padding: 15px;
            background: #34495e;
            border-radius: 8px;
        }
        .shop-tab-content.active {
            display: grid;
            grid-template-columns: 1fr 1fr 1fr;
            gap: 15px;
        }
        .shop-group {
            grid-column: span 3;
        }
        
        /* ANA SAYFA YÜKSELTME PANELLERİ (ÖZELLİKLER GELİŞTİR) */
        #upgrades-click, #upgrades-idle, #upgrades-advanced {
            padding: 10px; 
            border: 1px solid #7f8c8d;
            border-radius: 8px;
            grid-column: span 1; 
        }
        
        /* KOMPAKT YÜKSELTME ÖĞELERİ */
        .upgrade-item {
            display: flex;
            flex-direction: row; 
            justify-content: space-between;
            align-items: center;
            padding: 8px; 
            background: #46607a;
            border-radius: 5px;
            margin-bottom: 8px;
            text-align: left; 
            height: auto;
        }
        .upgrade-item p {
            margin: 0;
            font-size: 0.9em; 
        }
        .upgrade-item .buy-btn {
            padding: 5px 10px; 
            font-size: 0.8em;
            margin-top: 0;
            margin-left: 5px;
            white-space: nowrap;
        }

        /* YUMURTA/PET ARAYÜZÜ */
        #pets.shop-tab-content.active {
            grid-template-columns: 1fr 1fr 1fr; 
        }
        #pets .shop-item {
            grid-column: span 1; 
            flex-direction: column;
            padding: 10px;
        }
        #pets .shop-item p {
            font-size: 0.9em;
            margin-bottom: 10px;
        }
        #pets #modal-pet-list {
            grid-column: span 2; 
            padding: 0 10px;
            max-height: 250px;
            overflow-y: auto;
        }
        #pets #modal-pet-list li {
            font-size: 0.9em;
            margin-bottom: 5px;
            padding: 5px;
            border-bottom: 1px dashed #5d6d7e;
        }

        /* Diğerleri aynı kaldı */
        #leaderboard-panel { 
            grid-column: 1 / 4; 
            background: #2c3e50; 
            padding: 15px; 
            border-radius: 8px; 
            margin-top: 10px; 
        }
        .cost-gold { color: #f1c40f; font-weight: bold; }
        .buy-btn:hover:not(:disabled) { background-color: #27ae60; }
        .buy-btn:disabled { background-color: #7f8c8d; cursor: not-allowed; }
        .character-item h4 { color: #3498db; margin: 5px 0; }
        .character-item .req-rp { color: #9b59b6; font-weight: bold; }
        .unlocked-char { border: 3px solid #2ecc71; }
        .locked-char { border: 3px solid #e74c3c; opacity: 0.7; }
    </style>
</head>
<body>

    <h1>👑 Clicker Master</h1>

    <div id="shop-modal">
        <span id="modal-close-btn" onclick="toggleShopModal(false)">&times;</span>
        <h2>🛍️ Mağaza</h2>
        
        <div style="display:flex; justify-content: start; border-bottom: 2px solid #5d6d7e; margin-bottom: 10px;">
            <button class="shop-tab-button active" data-tab="characters">Özel Karakterler</button>
            <button class="shop-tab-button" data-tab="pets">Pet & Yumurta</button>
            <button class="shop-tab-button" data-tab="rewards">Günlük Ödül</button>
        </div>

        <div id="shop-modal-content">
            <div id="characters" class="shop-tab-content active">
                <div class="shop-group">
                    <h3>🎖️ Rebirth (RP) ile Açılan Karakterler</h3>
                    <p style="color:#bdc3c7;">Bu karakterler bir bonus sağlamaz, sadece **Başarı Puanınızı** ve oyun içi seviyenizi gösterir.</p>
                </div>
                </div>

            <div id="pets" class="shop-tab-content">
                <div class="shop-item">
                    <h3>🥚 Yumurta</h3>
                    <p>
                        **Gizemli Yumurta:** Rastgele Pet çıkarır.<br>
                        Şanslar: Kedi(%60), Köpek(%30), Ejderha(%10)<br>
                        Maliyet: <span id="egg-cost" class="cost-gold">15000</span>
                    </p>
                    <button class="buy-btn" id="buy-egg" onclick="buyEgg()">Yumurta Al</button>
                </div>

                <div style="grid-column: span 2;">
                    <h3 style="margin-top: 0;">🐾 Petleriniz</h3>
                    <ul id="modal-pet-list" style="list-style: none; padding: 0;">
                        <li>Henüz petiniz yok.</li>
                    </ul>
                </div>
            </div>

             <div id="rewards" class="shop-tab-content">
                <div id="daily-reward-item" style="grid-column: span 3; text-align: center;">
                    <h3>✨ Günlük Giriş Ödülü</h3>
                    <p>Her 24 saatte bir alabilirsiniz.</p>
                    <h4>Ödül: 💰 1000 Altın ve ⭐ 1 Rebirth Puanı (RP)</h4>
                    <button id="claim-daily-reward" onclick="claimDailyReward()">Ödülü Al</button>
                    <p id="daily-reward-timer" style="margin-top: 10px; font-weight: bold;"></p>
                </div>
            </div>
        </div>
    </div>
    <div id="game-container">
        
        <div id="stats">
            <p>Oyuncu: <span id="player-name">Misafir</span> | Kaynak: <span id="resource-count">0</span> Altın</p>
            <p>
                SBÜ (Saniye Başına Üretim): <span id="resource-per-sec">0</span> | 
                Tıklama Gücü: <span id="click-power">1</span> | 
                **Altın Çarpanı:** <span id="gold-multiplier">x1.00</span>
            </p>
            <p style="color:#9b59b6;">
                Rebirth Puanı (RP): <span id="rebirth-points">0</span> | 
                Toplam Üretim Bonusu: <span id="rebirth-bonus">0%</span>
            </p>
            <p>Son Kayıt: <span id="last-save">Hiç</span> (Otomatik Kayıt)</p>
        </div>

        <div id="leaderboard-panel">
            <h2>🥇 Liderlik Tablosu (Yerel Simülasyon)</h2>
            <table>
                <thead>
                    <tr>
                        <th>Sıra</th>
                        <th>Oyuncu</th>
                        <th>Rebirth Puanı (RP)</th>
                        <th>En Yüksek Kaynak</th>
                    </tr>
                </thead>
                <tbody id="leaderboard-table-body"></tbody>
            </table>
        </div>

        <div id="main-click-area">
            <button id="click-button">Tıkla!</button>
            <button id="rebirth-button" disabled onclick="doRebirth()">
                YENİDEN DOĞUŞ (REBIRTH)
            </button>
            <p>Gereksinim: <span id="rebirth-cost" class="cost-gold">100,000</span> Altın</p>
            <button id="open-shop-btn" onclick="toggleShopModal(true)">🛍️ MAĞAZA</button>
            <p id="pet-message" style="color: #f39c12; font-weight: bold; margin-top: 10px;"></p>
        </div>
        
        <div id="upgrades-click">
            <h2>⚡ Tıklama Gücü</h2>
            <div class="upgrade-item">
                <p>+1 Güç<br>Maliyet: <span id="powerCost" class="cost-gold">10</span></p>
                <button class="buy-btn" id="buy-power" onclick="buyUpgrade('power', 1, 'powerCost')">Al</button>
            </div>
            <div class="upgrade-item">
                <p>+5 Güç<br>Maliyet: <span id="power5Cost" class="cost-gold">100</span></p>
                <button class="buy-btn" id="buy-power5" onclick="buyUpgrade('power', 5, 'power5Cost')">Al</button>
            </div>
        </div>

        <div id="upgrades-idle">
            <h2>🤖 Otomatik Üretim</h2>
            <div class="upgrade-item">
                <p>+1 SBÜ<br>Maliyet: <span id="idleCost" class="cost-gold">50</span></p>
                <button class="buy-btn" id="buy-idle" onclick="buyUpgrade('idle', 1, 'idleCost')">Al</button>
            </div>
            <div class="upgrade-item">
                <p>+5 SBÜ<br>Maliyet: <span id="idle5Cost" class="cost-gold">500</span></p>
                <button class="buy-btn" id="buy-idle5" onclick="buyUpgrade('idle', 5, 'idle5Cost')">Al</button>
            </div>
        </div>
        
        <div id="upgrades-advanced">
            <h2>🚀 Gelişmiş</h2>
            <div class="upgrade-item">
                <p>+10 Güç<br>Maliyet: <span id="advancedPowerCost" class="cost-gold">5K</span></p>
                <button class="buy-btn" id="buy-advancedPower" onclick="buyUpgrade('power', 10, 'advancedPowerCost')">Al</button>
            </div>
            <div class="upgrade-item">
                <p>+10 SBÜ<br>Maliyet: <span id="advancedIdleCost" class="cost-gold">10K</span></p>
                <button class="buy-btn" id="buy-advancedIdle" onclick="buyUpgrade('idle', 10, 'advancedIdleCost')">Al</button>
            </div>
        </div>
    </div>

    <script>
        document.addEventListener('contextmenu', event => event.preventDefault());

        // --- OYUN DEĞİŞKENLERİ ---
        let playerName = 'Misafir';
        let resources = 0;
        let maxResources = 0; 
        let clickPower = 1;
        let resourcesPerSecond = 0; 
        let rebirthPoints = 0; 
        let lastDailyRewardDate = null; 
        let goldMultiplier = 1.0; 
        
        // Maliyetler ve Sabitler
        let rebirthCost = 100000; 
        const EGG_COST = 15000; 
        const DAILY_REWARD_RESOURCES = 1000;
        const DAILY_REWARD_RP = 1;
        const RP_MULTIPLIER_BASE = 2;

        // --- KARAKTER TANIMLARI (BONUSLAR KALDIRILDI) ---
        const characterDefinitions = {
            archer: { 
                name: 'Okçu', 
                icon: '🏹', 
                requiredRP: 1, 
                description: "Bu karakterin kilidini açarak Başarı Puanınızı yükseltin."
            },
            giant: { 
                name: 'Dev', 
                icon: '🛡️', 
                requiredRP: 2, 
                description: "Bu karakterin kilidini açarak Başarı Puanınızı yükseltin."
            },
            pekka: { 
                name: 'P.E.K.K.A.', 
                icon: '⚔️', 
                requiredRP: 5, 
                description: "Bu karakterin kilidini açarak Başarı Puanınızı yükseltin."
            },
            sparky: { 
                name: 'Sparky', 
                icon: '⚡', 
                requiredRP: 10, 
                description: "Bu karakterin kilidini açarak Başarı Puanınızı yükseltin."
            },
            prince: { 
                name: 'Prens', 
                icon: '🐴', 
                requiredRP: 20, 
                description: "Bu karakterin kilidini açarak Başarı Puanınızı yükseltin."
            }
        };

        // --- PET TANIMLARI ---
        const petDefinitions = {
            cat: { name: 'Kedi', rarity: 60, bonus: { click: 0.10 } }, 
            dog: { name: 'Köpek', rarity: 30, bonus: { idle: 0.15 } }, 
            dragon: { name: 'Ejderha', rarity: 10, bonus: { all: 0.25 } } 
        };
        
        let ownedPets = { cat: false, dog: false, dragon: false };
        let ownedCharacters = {}; 

        const costs = {
            powerCost: 10, power5Cost: 100, idleCost: 50, idle5Cost: 500, advancedPowerCost: 5000, advancedIdleCost: 10000,
            'egg-cost': EGG_COST
        };
        
        let leaderboardScores = [];
        const MAX_LEADERBOARD_SIZE = 200;

        // HTML Elementleri
        const lastSaveEl = document.getElementById('last-save');
        const leaderboardTableBody = document.getElementById('leaderboard-table-body');
        const playerNameEl = document.getElementById('player-name');
        const petMessageEl = document.getElementById('pet-message');
        const shopModal = document.getElementById('shop-modal');
        const dailyRewardButton = document.getElementById('claim-daily-reward');
        const dailyRewardTimerEl = document.getElementById('daily-reward-timer');
        const charactersTabContent = document.getElementById('characters');

        // Karakterlerin kalıcı bonuslarını hesaplar (ŞİMDİ DAİMA 0 DÖNDÜRÜYOR)
        function calculateCharacterBonus(type) {
            return 0;
            // Eski Kod (Kaldırıldı):
            /*
            let totalBonus = 0;
            for (const key in ownedCharacters) {
                if (ownedCharacters[key]) {
                    const char = characterDefinitions[key];
                    // Karakter tanımında bonus olmadığı için bu kısım artık çalışmayacak
                    // if (char.bonus && char.bonus.type === type) { totalBonus += char.bonus[type]; }
                }
            }
            return totalBonus;
            */
        }

        // --- HESAPLAMALAR VE ÇARPAN MANTIĞI ---

        function calculateGoldMultiplier() {
            return Math.pow(RP_MULTIPLIER_BASE, rebirthPoints);
        }

        function calculatePetBonus(type) {
            let totalBonus = 0;
            for (const key in petDefinitions) {
                if (ownedPets[key]) {
                    const pet = petDefinitions[key];
                    if (pet.bonus[type]) { totalBonus += pet.bonus[type]; }
                    if (pet.bonus.all) { totalBonus += pet.bonus.all; }
                }
            }
            return totalBonus;
        }
        
        // --- KARAKTER SATIN ALMA/AÇMA ---
        
        function unlockCharacter(key) {
             const char = characterDefinitions[key];

             if (rebirthPoints < char.requiredRP) {
                 petMessageEl.innerText = `${char.name} kilidini açmak için en az ${char.requiredRP} RP gerekiyor.`;
                 return;
             }
             
             if (ownedCharacters[key]) {
                 petMessageEl.innerText = `${char.name} karakterine zaten sahipsiniz.`;
                 return;
             }

             ownedCharacters[key] = true;
             
             // BONUS EKLEME KISMI KALDIRILDI
             /*
             if (char.bonus.type === 'power') {
                 clickPower += char.bonus.power;
             } else if (char.bonus.type === 'idle') {
                 resourcesPerSecond += char.bonus.idle;
             }
             */

             petMessageEl.innerText = `TEBRİKLER! ${char.name} karakterinin kilidi açıldı! Bu bir başarıdır!`;
             
             toggleShopModal(false); 
             updateDisplay();
             saveGame();
        }

        // --- MAĞAZA VE TAB MEKANİĞİ ---

        function toggleShopModal(show) {
            shopModal.style.display = show ? 'block' : 'none';
            if (show) {
                checkDailyRewardStatus();
                renderCharacters(); 
            }
        }
        
        function renderCharacters() {
            charactersTabContent.innerHTML = `
                <div class="shop-group">
                    <h3>🎖️ Rebirth (RP) ile Açılan Karakterler</h3>
                    <p style="color:#bdc3c7;">Bu karakterler bir bonus sağlamaz, sadece **Başarı Puanınızı** ve oyun içi seviyenizi gösterir.</p>
                </div>`;
                
            for (const key in characterDefinitions) {
                const char = characterDefinitions[key];
                const isUnlocked = ownedCharacters[key];
                const isAvailable = rebirthPoints >= char.requiredRP;
                
                let buttonText, buttonClass, itemClass, clickHandler;

                if (isUnlocked) {
                    buttonText = "Açıldı!";
                    buttonClass = "buy-btn";
                    itemClass = "unlocked-char";
                    clickHandler = "";
                } else if (isAvailable) {
                    buttonText = "Kilidi Aç";
                    buttonClass = "buy-btn";
                    itemClass = "";
                    clickHandler = `unlockCharacter('${key}')`;
                } else {
                    buttonText = "Kilitli";
                    buttonClass = "buy-btn disabled";
                    itemClass = "locked-char";
                    clickHandler = "";
                }

                // BONUS BİLGİSİ KALDIRILDI
                const bonusText = "Sadece Başarı"; 

                const charItem = document.createElement('div');
                charItem.className = `character-item ${itemClass}`;
                charItem.innerHTML = `
                    <h4>${char.icon} ${char.name}</h4>
                    <p>${char.description}</p>
                    <p>Bonus: <strong>${bonusText}</strong></p>
                    <p class="req-rp">Gereken RP: ${char.requiredRP}</p>
                    <button class="${buttonClass}" onclick="${clickHandler}" ${!isAvailable || isUnlocked ? 'disabled' : ''}>
                        ${buttonText}
                    </button>
                `;
                charactersTabContent.appendChild(charItem);
            }
        }


        document.querySelectorAll('.shop-tab-button').forEach(button => {
            button.addEventListener('click', () => {
                const targetTab = button.getAttribute('data-tab');
                
                document.querySelectorAll('.shop-tab-button').forEach(btn => btn.classList.remove('active'));
                document.querySelectorAll('.shop-tab-content').forEach(content => content.classList.remove('active'));
                
                button.classList.add('active');
                document.getElementById(targetTab).classList.add('active');

                if (targetTab === 'rewards') {
                    checkDailyRewardStatus();
                } else if (targetTab === 'characters') {
                    renderCharacters(); 
                }
            });
        });

        // --- GÜNLÜK ÖDÜL MEKANİĞİ ---
        
        function checkDailyRewardStatus() {
            const now = new Date().getTime();
            const oneDay = 24 * 60 * 60 * 1000;

            dailyRewardButton.disabled = true;

            if (!lastDailyRewardDate) {
                dailyRewardButton.disabled = false;
                dailyRewardTimerEl.innerText = "Ödül hazır! Hemen al.";
            } else {
                const lastClaimTime = new Date(lastDailyRewardDate).getTime();
                const timeSinceLastClaim = now - lastClaimTime;

                if (timeSinceLastClaim >= oneDay) {
                    dailyRewardButton.disabled = false;
                    dailyRewardTimerEl.innerText = "Ödül hazır! Hemen al.";
                } else {
                    const timeLeft = oneDay - timeSinceLastClaim;
                    const hours = Math.floor(timeLeft / (60 * 60 * 1000));
                    const minutes = Math.floor((timeLeft % (60 * 60 * 1000)) / (60 * 1000));
                    const seconds = Math.floor((timeLeft % (60 * 1000)) / 1000);
                    
                    dailyRewardTimerEl.innerText = `Sonraki ödül için kalan süre: ${hours}sa ${minutes}dk ${seconds}sn`;
                }
            }
        }
        
        function claimDailyReward() {
            if (!dailyRewardButton.disabled) {
                resources += DAILY_REWARD_RESOURCES;
                rebirthPoints += DAILY_REWARD_RP; 
                lastDailyRewardDate = new Date().toISOString(); 
                goldMultiplier = calculateGoldMultiplier(); 

                dailyRewardButton.disabled = true;
                dailyRewardTimerEl.innerText = `Ödül alındı! Altın: +${DAILY_REWARD_RESOURCES}, RP: +${DAILY_REWARD_RP}`;
                
                updateDisplay();
                saveGame();
                checkDailyRewardStatus();
            }
        }
        
        // --- PET MEKANİĞİ: YUMURTA ---
        
        function buyEgg() {
            const currentCost = costs['egg-cost'];

            if (resources >= currentCost) {
                resources -= currentCost;
                
                const roll = Math.random() * 100;
                let cumulativeChance = 0;
                let foundPetKey = null;

                for (const key in petDefinitions) {
                    cumulativeChance += petDefinitions[key].rarity;
                    if (roll < cumulativeChance) {
                        foundPetKey = key;
                        break;
                    }
                }
                
                if (foundPetKey) {
                    const petName = petDefinitions[foundPetKey].name;
                    if (!ownedPets[foundPetKey]) {
                        ownedPets[foundPetKey] = true;
                        petMessageEl.innerText = `TEBRİKLER! Yumurtadan yeni petiniz ${petName} çıktı!`;
                    } else {
                        petMessageEl.innerText = `Yumurtadan tekrar ${petName} çıktı. Zaten bu pete sahipsiniz.`;
                    }
                }
                
                updateDisplay(); 
                saveGame();
            } else {
                petMessageEl.innerText = `Yetersiz Altın! Yumurta almak için ${formatNumber(currentCost)} altın gerekiyor.`;
            }
        }

        // --- LİDERLİK TABLOSU MEKANİĞİ ---
        
        function updateLeaderboard() {
            const existingIndex = leaderboardScores.findIndex(s => s.name === playerName);
            const currentPlayerScore = { name: playerName, resources: maxResources, rp: rebirthPoints };
            if (existingIndex > -1) {
                if (currentPlayerScore.resources > leaderboardScores[existingIndex].resources || currentPlayerScore.rp > leaderboardScores[existingIndex].rp) {
                    leaderboardScores[existingIndex] = currentPlayerScore;
                }
            } else { leaderboardScores.push(currentPlayerScore); }
            leaderboardScores.sort((a, b) => {
                if (b.rp !== a.rp) return b.rp - a.rp; 
                return b.resources - a.resources; 
            });
            if (leaderboardScores.length > MAX_LEADERBOARD_SIZE) {
                leaderboardScores = leaderboardScores.slice(0, MAX_LEADERBOARD_SIZE);
            }
            leaderboardTableBody.innerHTML = '';
            leaderboardScores.forEach((score, index) => {
                const tr = document.createElement('tr');
                if (score.name === playerName) tr.className = 'highlight-player';
                tr.innerHTML = `<td>${index + 1}</td><td>${score.name}</td><td>${formatNumber(score.rp)}</td><td>${formatNumber(score.resources)}</td>`;
                leaderboardTableBody.appendChild(tr);
            });
            localStorage.setItem('leaderboardScores', JSON.stringify(leaderboardScores));
        }

        // --- KAYDETME / YÜKLEME MEKANİĞİ ---

        function saveGame() {
            const costsToSave = {};
            for (const key in costs) { costsToSave[key] = costs[key]; }
            
            const gameState = {
                playerName: playerName, maxResources: maxResources, 
                resources: resources, clickPower: clickPower, resourcesPerSecond: resourcesPerSecond, 
                rebirthPoints: rebirthPoints, rebirthCost: rebirthCost,     
                costs: costsToSave, 
                ownedPets: ownedPets,
                ownedCharacters: ownedCharacters, 
                lastDailyRewardDate: lastDailyRewardDate,
                goldMultiplier: goldMultiplier 
            };

            try {
                localStorage.setItem('clickerGameSave', JSON.stringify(gameState));
                updateLeaderboard(); 
                lastSaveEl.innerText = new Date().toLocaleTimeString();
            } catch (e) { console.error("Kaydetme hatası:", e); }
        }

        function loadGame() {
            try {
                const savedState = localStorage.getItem('clickerGameSave');
                const loadedLeaderboard = localStorage.getItem('leaderboardScores');

                if (loadedLeaderboard) leaderboardScores = JSON.parse(loadedLeaderboard);
                
                if (savedState) {
                    const gameState = JSON.parse(savedState);
                    
                    playerName = gameState.playerName || promptForName(); 
                    maxResources = gameState.maxResources || 0; 
                    resources = gameState.resources || 0; clickPower = gameState.clickPower || 1;
                    resourcesPerSecond = gameState.resourcesPerSecond || 0; rebirthPoints = gameState.rebirthPoints || 0; 
                    rebirthCost = gameState.rebirthCost || 100000; 
                    lastDailyRewardDate = gameState.lastDailyRewardDate || null; 
                    
                    if (gameState.costs) {
                        for (const key in gameState.costs) { costs[key] = gameState.costs[key]; }
                    }
                    
                    if (gameState.ownedPets) { ownedPets = gameState.ownedPets; }
                    
                    if (gameState.ownedCharacters) { ownedCharacters = gameState.ownedCharacters; }
                    
                    // Kalıcı karakter bonusları kaldırıldığı için sadece temel değerleri yükle
                    clickPower = 1; 
                    resourcesPerSecond = 0;

                    goldMultiplier = calculateGoldMultiplier(); 

                    updateDisplay();
                    lastSaveEl.innerText = "Yüklendi";
                } else {
                    playerName = promptForName(); 
                    goldMultiplier = calculateGoldMultiplier(); 
                }
            } catch (e) { console.error("Yükleme hatası:", e); }
        }
        
        // --- TEMEL MEKANİKLER ---

        function promptForName() {
            let name = localStorage.getItem('playerName') || 'Oyuncu' + Math.floor(Math.random() * 999);
            name = prompt("Lütfen bir kullanıcı adı girin:", name);
            if (name && name.length > 0 && name.length < 20) {
                localStorage.setItem('playerName', name);
                return name;
            }
            return 'Misafir';
        }
        
        // Tıklama Fonksiyonu
        document.getElementById('click-button').addEventListener('click', () => {
            const rebirthBonusMultiplier = 1 + (rebirthPoints * 0.10);
            const petClickBonus = calculatePetBonus('click'); 
            
            const totalMultiplier = rebirthBonusMultiplier * (1 + petClickBonus) * goldMultiplier; 
            
            resources += clickPower * totalMultiplier;
            maxResources = Math.max(maxResources, resources);
            updateDisplay();
        });

        // OYUN DÖNGÜSÜ
        function idleLoop() {
            const rebirthBonusMultiplier = 1 + (rebirthPoints * 0.10);
            const petIdleBonus = calculatePetBonus('idle'); 
            
            const totalMultiplier = rebirthBonusMultiplier * (1 + petIdleBonus) * goldMultiplier; 
            
            resources += resourcesPerSecond * totalMultiplier;
            maxResources = Math.max(maxResources, resources);
            updateDisplay();
        }

        // Rebirth Fonksiyonu
        function doRebirth() {
            if (resources >= rebirthCost) {
                const rpGained = Math.floor(Math.sqrt(resources / rebirthCost));
                if (rpGained < 1) return; 
                rebirthPoints += rpGained;
                
                resources = 0; 
                
                // Temel değerleri sıfırla (Karakter bonusu artık 0 döndürecek)
                clickPower = 1; 
                resourcesPerSecond = 0;
                
                // Maliyetleri sıfırla
                costs.powerCost = 10; costs.power5Cost = 100; costs.idleCost = 50; costs.idle5Cost = 500; costs.advancedPowerCost = 5000; costs.advancedIdleCost = 10000;
                
                // Pet sahipliğini sıfırla 
                ownedPets = { cat: false, dog: false, dragon: false };

                rebirthCost = Math.ceil(rebirthCost * 2);
                
                goldMultiplier = calculateGoldMultiplier();

                saveGame();
                updateDisplay();
            }
        }
        
        // Yükseltme satın alma (buyUpgrade)
        function buyUpgrade(type, increaseAmount, costKey) {
            const currentCost = costs[costKey];

            if (resources >= currentCost) {
                resources -= currentCost; 
                petMessageEl.innerText = "";

                if (type === 'power') {
                    clickPower += increaseAmount;
                    const multiplier = (increaseAmount <= 5) ? 1.15 : 1.35;
                    costs[costKey] = Math.ceil(currentCost * multiplier);
                } else if (type === 'idle') {
                    resourcesPerSecond += increaseAmount;
                    const multiplier = (increaseAmount <= 5) ? 1.2 : 1.4;
                    costs[costKey] = Math.ceil(currentCost * multiplier);
                }
                
                updateDisplay();
                saveGame();
            }
        }
        
        // Sayıları daha okunabilir hale getirir
        function formatNumber(num) {
            if (num >= 1e12) return (num / 1e12).toFixed(2) + "T";
            if (num >= 1e9) return (num / 1e9).toFixed(2) + "B";
            if (num >= 1e6) return (num / 1e6).toFixed(2) + "M";
            if (num >= 1e3) return (num / 1e3).toFixed(2) + "K";
            return Math.floor(num);
        }

        function updateDisplay() {
            const rebirthTotalBonusPercentage = rebirthPoints * 10; 
            const petClickBonus = calculatePetBonus('click');
            const petIdleBonus = calculatePetBonus('idle');
            const petAllBonus = calculatePetBonus('all');
            
            const baseMultiplier = (1 + rebirthTotalBonusPercentage / 100) * (1 + petAllBonus);
            
            // Karakter bonusları kaldırıldığı için basitleştirilmiş efektif hesaplama
            const effectiveClickPower = clickPower * baseMultiplier * (1 + petClickBonus) * goldMultiplier;
            const effectiveSBÜ = resourcesPerSecond * baseMultiplier * (1 + petIdleBonus) * goldMultiplier;

            // İstatistikleri güncelle
            playerNameEl.innerText = playerName; 
            document.getElementById('resource-count').innerText = formatNumber(resources);
            document.getElementById('resource-per-sec').innerText = formatNumber(effectiveSBÜ);
            document.getElementById('click-power').innerText = formatNumber(effectiveClickPower);
            document.getElementById('rebirth-points').innerText = rebirthPoints;
            document.getElementById('rebirth-bonus').innerText = rebirthTotalBonusPercentage + "%";
            document.getElementById('gold-multiplier').innerText = `x${goldMultiplier.toFixed(2)}`; 
            
            document.getElementById('rebirth-cost').innerText = formatNumber(rebirthCost);
            document.getElementById('rebirth-button').disabled = resources < rebirthCost;

            for (const key in costs) {
                const costValue = costs[key];
                const costEl = document.getElementById(key); 
                const buttonEl = document.getElementById('buy-' + key.replace('Cost', '').replace('-', ''));

                if (costEl) costEl.innerText = formatNumber(costValue);
                if (buttonEl) buttonEl.disabled = resources < costValue;
            }

            const petListEl = document.getElementById('modal-pet-list');
            petListEl.innerHTML = '';
            let hasPets = false;
            for (const key in ownedPets) {
                if (ownedPets[key]) {
                    const pet = petDefinitions[key];
                    const li = document.createElement('li');
                    let bonusText = '';
                    if (pet.bonus.click) bonusText += `Tıklama Gücü +${(pet.bonus.click * 100).toFixed(0)}% `;
                    if (pet.bonus.idle) bonusText += `SBÜ +${(pet.bonus.idle * 100).toFixed(0)}% `;
                    if (pet.bonus.all) bonusText += `Tüm Üretim +${(pet.bonus.all * 100).toFixed(0)}% `;
                    
                    li.innerHTML = `**${pet.name}:** <span style="color: #2ecc71;">${bonusText.trim()}</span>`;
                    petListEl.appendChild(li);
                    hasPets = true;
                }
            }
            if (!hasPets) { petListEl.innerHTML = '<li>Henüz petiniz yok.</li>'; }
            
            updateLeaderboard(); 
        }

        // --- BAŞLATMA ---

        loadGame();
        
        setInterval(idleLoop, 1000); 
        setInterval(saveGame, 5000); 
        setInterval(checkDailyRewardStatus, 1000); 

        updateDisplay();
    </script>
</body>
</html>
