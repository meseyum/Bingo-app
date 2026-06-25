<!DOCTYPE html>
<html lang="am">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bingo Board Pro</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        body { background-color: #121032; color: white; font-family: sans-serif; text-align: center; margin: 0; padding: 15px; }
        .header-banner { background-color: #E74C3C; padding: 12px; border-radius: 8px; font-weight: bold; margin-bottom: 15px; font-size: 14px; cursor: pointer; }
        .wallet-container { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; margin-bottom: 20px; }
        .wallet-box { background-color: #1B184B; padding: 10px; border-radius: 8px; border: 1px solid #2A247A; }
        .wallet-title { font-size: 10px; color: #8B88B4; font-weight: bold; }
        .wallet-value { font-size: 15px; font-weight: bold; color: #FFD700; margin-top: 5px; }
        .grid-container { display: grid; grid-template-columns: repeat(10, 1fr); gap: 6px; background-color: #0A0824; padding: 10px; border-radius: 12px; }
        .tile { background-color: #D35400; border: none; color: white; padding: 12px 0; font-size: 12px; font-weight: bold; border-radius: 6px; cursor: pointer; transition: 0.2s; }
        .tile.selected { background-color: #FFD700; color: #121032; transform: scale(1.05); }
        .status-box { margin-top: 15px; background: #1B184B; padding: 10px; border-radius: 8px; font-size: 13px; color: #FFD700; }
    </style>
</head>
<body>

    <div class="header-banner" onclick="playAudio()">🔊 የጨዋታውን የአማርኛ ድምፅ ለመስማት ይህንን ይጫኑ!</div>

    <div class="wallet-container">
        <div class="wallet-box"><div class="wallet-title">MAIN WALLET</div><div class="wallet-value">0 ብር</div></div>
        <div class="wallet-box"><div class="wallet-title">PLAYERS</div><div class="wallet-value" id="player-count">1</div></div>
        <div class="wallet-box"><div class="wallet-title">PLAY WALLET</div><div class="wallet-value">0 ብር</div></div>
    </div>

    <h3 style="margin-bottom: 10px;">እባክዎ መጫወቻ ካርታ ይምረጡ (1-100)</h3>
    
    <div class="grid-container" id="bingo-grid"></div>

    <div class="status-box" id="status-display">የተመረጠ ካርታ የለም</div>

    <script>
        const tg = window.Telegram.WebApp;
        tg.expand();

        const grid = document.getElementById('bingo-grid');
        const statusDisplay = document.getElementById('status-display');
        let selectedNumbers = [];

        // Generate 1 to 100 tiles
        for (let i = 1; i <= 100; i++) {
            const btn = document.createElement('button');
            btn.className = 'tile';
            btn.innerText = i;
            
            btn.onclick = () => {
                if (btn.classList.contains('selected')) {
                    // Unselect if tapped again
                    btn.classList.remove('selected');
                    selectedNumbers = selectedNumbers.filter(num => num !== i);
                } else {
                    // Maximum 5 cards selection for safety balance rules
                    if (selectedNumbers.length >= 5) {
                        alert("በአንድ ጊዜ መምረጥ የሚችሉት 5 ካርታዎችን ብቻ ነው!");
                        return;
                    }
                    btn.classList.add('selected');
                    selectedNumbers.push(i);
                }
                
                // Update text display inside the app
                if (selectedNumbers.length > 0) {
                    statusDisplay.innerText = `የመረጧቸው ካርታዎች: ${selectedNumbers.sort((a,b)=>a-b).join(', ')}`;
                    // Send selections back to Python bot securely
                    tg.sendData(JSON.stringify({selected_cards: selectedNumbers}));
                } else {
                    statusDisplay.innerText = "የተመረጠ ካርታ የለም";
                }
            };
            
            grid.appendChild(btn);
        }

        function playAudio() {
            statusDisplay.innerText = "🔈 ድምፅ በመጫን ላይ...";
            // Placeholder sound notification hook
            setTimeout(() => { statusDisplay.innerText = "🔊 ድምፅ ዝግጁ ነው!"; }, 1000);
        }
    </script>
</body>
</html>
