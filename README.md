<!DOCTYPE html>
<html>
<head>
    <title>Juego de Sesgos y Efectos</title>
    <style>
        .container {
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
            font-family: Arial, sans-serif;
        }
        
        .card {
            border: 2px solid #ddd;
            padding: 10px;
            margin: 5px;
            border-radius: 5px;
            cursor: pointer;
            background-color: white;
            transition: all 0.3s ease-in-out;
            opacity: 1;
        }
        
        .card.selected {
            background-color: #e0f0ff;
        }
        
        .card.matched {
            background-color: #90EE90;
            cursor: default;
            opacity: 0;
            transform: scale(0.9);
            transition: all 0.5s ease-in-out;
        }
        
        .concepts, .definitions {
            float: left;
            width: 45%;
            margin: 10px;
        }
        
        .score {
            clear: both;
            padding: 20px;
            text-align: center;
        }

        button {
            display: block;
            margin: 10px auto;
            padding: 10px 20px;
            font-size: 16px;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }

        button:hover {
            background-color: #45a049;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Juego de Sesgos y Efectos</h1>
        <button onclick="resetGame()">Reiniciar Juego</button>
        <div class="concepts"></div>
        <div class="definitions"></div>
        <div class="score">
            Parejas encontradas: <span id="matches">0</span>/6
            <br>
            Tiempo: <span id="timer">0</span> segundos
        </div>
    </div>

    <script>
        const pairs = [
            {
                concept: "Sesgo de Afinidad",
                definition: "Favorecer a personas con características similares a las nuestras, como gustos, apariencia o experiencias."
            },
            {
                concept: "Sesgo de Confianza",
                definition: "Tendencia a confiar más en personas que ya conocemos, incluso sin evidencia que respalde esta confianza."
            },
            {
                concept: "Sesgo Racial",
                definition: "Creencias o estereotipos que afectan cómo percibimos a las personas en función de su origen étnico o color de piel."
            },
            {
                concept: "Sesgo de Género",
                definition: "Prejuicios o expectativas basadas en el género de una persona que influyen en el trato u oportunidades."
            },
            {
                concept: "Efecto Halo",
                definition: "Juzgar a una persona de manera favorable en múltiples aspectos por una característica positiva."
            },
            {
                concept: "Efecto Cuerno",
                definition: "Juzgar a una persona negativamente en múltiples aspectos debido a una característica negativa percibida."
            }
        ];

        let selectedCards = [];
        let matchedPairs = 0;
        let startTime;
        let timerInterval;
        const matchSound = new Audio('https://www.soundjay.com/button/sounds/button-09.mp3');

        function shuffle(array) {
            return array.sort(() => Math.random() - 0.5);
        }

        function startTimer() {
            startTime = Date.now();
            timerInterval = setInterval(() => {
                const elapsedTime = Math.floor((Date.now() - startTime) / 1000);
                document.getElementById('timer').textContent = elapsedTime;
            }, 1000);
        }

        function createGame() {
            const conceptsDiv = document.querySelector('.concepts');
            const definitionsDiv = document.querySelector('.definitions');
            
            const shuffledPairs = shuffle([...pairs]);
            const shuffledDefinitions = shuffle([...pairs]);

            shuffledPairs.forEach(pair => {
                const card = document.createElement('div');
                card.className = 'card';
                card.textContent = pair.concept;
                card.dataset.concept = pair.concept;
                conceptsDiv.appendChild(card);
            });

            shuffledDefinitions.forEach(pair => {
                const card = document.createElement('div');
                card.className = 'card';
                card.textContent = pair.definition;
                card.dataset.definition = pair.concept;
                definitionsDiv.appendChild(card);
            });

            document.querySelectorAll('.card').forEach(card => {
                card.addEventListener('click', handleCardClick);
            });

            startTimer();
        }

        function handleCardClick(e) {
            const card = e.target;
            
            if (card.classList.contains('matched') || selectedCards.includes(card)) {
                return;
            }

            card.classList.add('selected');
            selectedCards.push(card);

            if (selectedCards.length === 2) {
                checkMatch();
            }
        }

        function checkMatch() {
            const [card1, card2] = selectedCards;
            
            // Verificar si una carta es concepto y la otra definición
            const isCard1Concept = card1.dataset.concept !== undefined;
            const isCard2Concept = card2.dataset.concept !== undefined;
            
            // Solo proceder si una es concepto y la otra definición
            if (isCard1Concept !== isCard2Concept) {
                const conceptCard = isCard1Concept ? card1 : card2;
                const definitionCard = isCard1Concept ? card2 : card1;
                
                if (conceptCard.dataset.concept === definitionCard.dataset.definition) {
                    // Coincidencia correcta
                    matchSound.play();
                    conceptCard.classList.add('matched');
                    definitionCard.classList.add('matched');
                    
                    setTimeout(() => {
                        conceptCard.style.display = 'none';
                        definitionCard.style.display = 'none';
                    }, 500);

                    matchedPairs++;
                    document.getElementById('matches').textContent = matchedPairs;
                    
                    if (matchedPairs === pairs.length) {
                        clearInterval(timerInterval);
                        setTimeout(() => alert('¡Felicitaciones! Has completado el juego en ' + 
                            document.getElementById('timer').textContent + ' segundos.'), 500);
                    }
                }
            }
            
            // Si no hay coincidencia o son del mismo tipo, solo quitar la selección
            setTimeout(() => {
                card1.classList.remove('selected');
                card2.classList.remove('selected');
                selectedCards = [];
            }, 1000);
        }

        function resetGame() {
            clearInterval(timerInterval);
            matchedPairs = 0;
            selectedCards = [];
            document.getElementById('matches').textContent = '0';
            document.getElementById('timer').textContent = '0';
            
            const conceptsDiv = document.querySelector('.concepts');
            const definitionsDiv = document.querySelector('.definitions');
            
            conceptsDiv.innerHTML = '';
            definitionsDiv.innerHTML = '';
            
            createGame();
        }

        createGame();
    </script>
</body>
</html>
