<!DOCTYPE html> 
<html lang="pt">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jogo da Memória</title>
    <style>
        body { font-family: Arial, sans-serif; display: flex; justify-content: space-around; align-items: flex-start; height: 100vh; margin: 0; }
        .game-container { text-align: center; }
        .game-board { display: grid; grid-template-columns: repeat(4, 150px); gap: 10px; justify-content: center; margin-top: 20px; }
        .card { width: 150px; height: 150px; background-color: #f0f0f0; display: flex; align-items: center; justify-content: center; font-size: 18px; cursor: pointer; border: 2px solid #ccc; }
        .matched { background-color: #8fce00; color: white; }
        .card-flipped { background-color: #d4e0e0; }
        #score, #timer { font-size: 20px; }
        #finalMessage { font-size: 40px; font-weight: bold; color: #8fce00; display: none; margin-top: 20px; text-align: center; position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); }
        .options { margin-top: 20px; text-align: left; }
        .option { padding: 5px; font-size: 18px; }
        ul { text-align: left; }
    </style>
</head>
<body>
    <div class="game-container">
        <h1>Jogo da Memória</h1>
        <div id="score">Pontuação: 0</div>
        <div id="timer">Tempo: 00:00</div>
        <div class="game-board" id="gameBoard"></div>
        <div id="finalMessage">PARABÉNS PROFESSORA ELISA! ARRASOU!</div>
    </div>
    
    <div class="options">
        <h2>Hipóteses:</h2>
        <ul id="optionsList"></ul>
    </div>

    <audio id="flipSound" src="https://www.soundjay.com/button/beep-07.wav" preload="auto"></audio>
    <audio id="matchSound" src="https://www.soundjay.com/button/sounds/button-3.mp3" preload="auto"></audio>

    <script>
        const cards = [
            { id: 1, name: "Bernardino Machado", pair: "Presidente da República" },
            { id: 2, name: "Presidente da República", pair: "Bernardino Machado" },
            { id: 3, name: "Brasileiros de Torna-Viagem", pair: "Arquitetura Brasileira em Portugal" },
            { id: 4, name: "Arquitetura Brasileira em Portugal", pair: "Brasileiros de Torna-Viagem" },
            { id: 5, name: "Museu Bernardino Machado", pair: "História e Cultura" },
            { id: 6, name: "História e Cultura", pair: "Museu Bernardino Machado" },
            { id: 7, name: "Solar dos Machados", pair: "História Local" },
            { id: 8, name: "História Local", pair: "Solar dos Machados" },
            { id: 9, name: "Confraria Nossa Senhora do Carmo", pair: "Patrimônio Cultural" },
            { id: 10, name: "Patrimônio Cultural", pair: "Confraria Nossa Senhora do Carmo" },
            { id: 11, name: "Escola Básica Conde S. Cosme", pair: "Educação em Famalicão" },
            { id: 12, name: "Educação em Famalicão", pair: "Escola Básica Conde S. Cosme" }
        ];

        let selectedCards = [];
        let matchedPairs = 0;
        let score = 0;
        let timer;
        let seconds = 0;

        const flipSound = document.getElementById("flipSound");
        const matchSound = document.getElementById("matchSound");

        function renderBoard() {
            const board = document.getElementById("gameBoard");
            board.innerHTML = "";
            cards.forEach((card, index) => {
                const cardElement = document.createElement("div");
                cardElement.classList.add("card");
                cardElement.dataset.index = index;
                cardElement.textContent = "?";
                cardElement.onclick = () => selectCard(cardElement, index);
                board.appendChild(cardElement);
            });
            renderOptions(); // Atualizar as opções ao lado
        }

        function renderOptions() {
            const optionsList = document.getElementById("optionsList");
            optionsList.innerHTML = "";
            cards.forEach((card, index) => {
                const optionDiv = document.createElement("li");
                optionDiv.classList.add("option");
                optionDiv.textContent = `${card.name}`;
                optionsList.appendChild(optionDiv);
            });
        }

        function selectCard(element, index) {
            if (selectedCards.length < 2 && !selectedCards.includes(index)) {
                element.textContent = cards[index].name;
                element.classList.add("card-flipped");
                flipSound.play();
                selectedCards.push(index);
            }
            
            if (selectedCards.length === 2) {
                setTimeout(checkMatch, 1000);
            }
        }

        function checkMatch() {
            const [firstIndex, secondIndex] = selectedCards;
            const firstCard = cards[firstIndex];
            const secondCard = cards[secondIndex];
            const board = document.getElementById("gameBoard").children;
            
            if (firstCard.pair === secondCard.name) {
                board[firstIndex].classList.add("matched");
                board[secondIndex].classList.add("matched");
                matchedPairs++;
                score += 10;  // Add points for correct match
                matchSound.play();
                document.getElementById("score").textContent = `Pontuação: ${score}`;
            } else {
                board[firstIndex].textContent = "?";
                board[secondIndex].textContent = "?";
                board[firstIndex].classList.remove("card-flipped");
                board[secondIndex].classList.remove("card-flipped");
            }
            
            selectedCards = [];
            
            if (matchedPairs === cards.length / 2) {
                clearInterval(timer); // Stop the timer when the game is finished
                setTimeout(() => {
                    document.getElementById("finalMessage").style.display = "block";
                }, 500);
            }
        }

        function startTimer() {
            timer = setInterval(() => {
                seconds++;
                document.getElementById("timer").textContent = `Tempo: ${formatTime(seconds)}`;
            }, 1000);
        }

        function formatTime(seconds) {
            const minutes = Math.floor(seconds / 60);
            const sec = seconds % 60;
            return `${minutes < 10 ? '0' + minutes : minutes}:${sec < 10 ? '0' + sec : sec}`;
        }

        // Initialize the game
        renderBoard();
        startTimer();
    </script>
</body>
</html>
