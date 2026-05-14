# Desafio-de-Pit-goras}
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <title>Desafio de Pitágoras - Arena Avançada</title>
    <style>
        body { background: #050510; color: #fff; font-family: 'Segoe UI', sans-serif; text-align: center; }
        .neon-box { border: 2px solid #0ff; padding: 20px; border-radius: 15px; margin: 20px auto; max-width: 700px; box-shadow: 0 0 15px #0ff; }
        .card { display: inline-block; width: 60px; height: 90px; background: #1e1e3f; border: 2px solid #0ff; margin: 5px; border-radius: 8px; line-height: 90px; color: #0ff; font-weight: bold; font-size: 20px; cursor: pointer; }
        .used { opacity: 0.2; cursor: not-allowed; }
    </style>
</head>
<body>

    <h1>DESAFIO DE PITÁGORAS</h1>
    <div id="game" class="neon-box">
        <h2 id="target">Alvo: --</h2>
        <p>Rodada: <span id="roundDisplay">1</span></p>
        <div id="cardsArea"></div>
        <div id="scores"></div>
        <input type="text" id="expr" placeholder="Clique nas cartas para montar a expressão..." style="width:80%; padding:10px; margin:10px 0;">
        <br>
        <button onclick="check()">VALIDAR EXPRESSÃO</button>
        <button onclick="resetExpr()">LIMPAR EXPRESSÃO</button>
        <p>Tempo Restante: <span id="timer">180</span>s</p>
    </div>

    <script>
        let gameState = { 
            target: 0, cards: [], usedIndices: [], expression: "", 
            timeLeft: 180, players: [], timerInterval: null, round: 1 
        };

        function initGame() {
            let numP = prompt("Quantos jogadores?", "2");
            for(let i=0; i<numP; i++) gameState.players.push({name: 'P'+(i+1), pts: 0});
            renderScores();
            startRound();
        }

        function startRound() {
            let range = (gameState.round <= 5) ? {min:1, max:100} : (gameState.round <= 10) ? {min:50, max:200} : {min:100, max:400};
            gameState.target = Math.floor(Math.random() * (range.max - range.min + 1)) + range.min;
            gameState.cards = Array.from({length: 5}, () => Math.floor(Math.random() * 10) + 1);
            resetExpr();
            
            clearInterval(gameState.timerInterval);
            gameState.timerInterval = setInterval(() => {
                gameState.timeLeft--;
                document.getElementById('timer').innerText = gameState.timeLeft;
                if(gameState.timeLeft <= 0) { gameState.round++; startRound(); }
            }, 1000);
        }

        function renderCards() {
            document.getElementById('cardsArea').innerHTML = gameState.cards.map((c, i) => 
                `<div class="card ${gameState.usedIndices.includes(i) ? 'used' : ''}" 
                      onclick="useCard(${i}, ${c})">${c}</div>`).join('');
        }

        function useCard(idx, val) {
            if (!gameState.usedIndices.includes(idx)) {
                gameState.usedIndices.push(idx);
                gameState.expression += val;
                document.getElementById('expr').value = gameState.expression;
                renderCards();
            }
        }

        function resetExpr() {
            gameState.expression = "";
            gameState.usedIndices = [];
            document.getElementById('expr').value = "";
            renderCards();
        }

        function check() {
            let expr = document.getElementById('expr').value;
            try {
                if(eval(expr) == gameState.target) {
                    let player = prompt("Quem acertou? (ex: P1)");
                    let p = gameState.players.find(x => x.name == player);
                    if(p) { 
                        // Regra: 2 pontos por carta usada
                        p.pts += (gameState.usedIndices.length * 2); 
                        gameState.round++;
                        renderScores();
                        startRound();
                    }
                } else { alert("Incorreto!"); }
            } catch(e) { alert("Expressão inválida!"); }
        }

        function renderScores() {
            document.getElementById('scores').innerHTML = gameState.players.map(p => `<div>${p.name}: ${p.pts} pts</div>`).join('');
        }

        initGame();
    </script>
</body>
</html>
