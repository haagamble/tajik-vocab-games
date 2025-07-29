---
title: "Word Match"
description: "Match English words to their Tajik translations."
---

<div class="game-board" id="gameBoard"></div>

<script>
  // Load word pairs from the JSON file
fetch('/assets/data/first-100-words.json')
  .then(response => response.json())
  .then(data => {
    // Shuffle the full list
    const shuffled = data.sort(() => 0.5 - Math.random());
    // Take 6 random pairs
    const selectedPairs = shuffled.slice(0, 6);

    // Duplicate and mix English/Tajik for the game
    const gameCards = [];
    selectedPairs.forEach(pair => {
      gameCards.push({ text: pair.english, pairId: pair.english });
      gameCards.push({ text: pair.tajik, pairId: pair.english });
    });

    // Shuffle the game cards
    const shuffledCards = gameCards.sort(() => 0.5 - Math.random());

    // Render cards to the board
    const board = document.getElementById('gameBoard');
    shuffledCards.forEach((card, index) => {
      const cardDiv = document.createElement('div');
      cardDiv.className = 'card';
      cardDiv.dataset.pairId = card.pairId;
      cardDiv.innerText = ''; // Start face-down
      cardDiv.addEventListener('click', () => flipCard(cardDiv, card.text));
      board.appendChild(cardDiv);
    });
  });

let flippedCards = [];
let isBusy = false;
// track how many tries
let tries = 0

function flipCard(cardDiv, text) {
  if (isBusy) return; // Prevent clicks while busy
  if (cardDiv.classList.contains('matched') || flippedCards.includes(cardDiv)) return;

  cardDiv.innerText = text;
  flippedCards.push(cardDiv);

  if (flippedCards.length === 2) {
    tries++; // Increment tries count
    isBusy = true; // Prevent further clicks
    const [card1, card2] = flippedCards;
    const isMatch = card1.dataset.pairId === card2.dataset.pairId;

  if (isMatch) {
      // Instantly show matched color
      card1.classList.add('matched');
      card2.classList.add('matched');
    }

    setTimeout(() => {
      if (isMatch) {

        // Check if all cards are matched
        const allMatched = Array.from(document.querySelectorAll('.card'))
          .every(card => card.classList.contains('matched'));
        if (allMatched) {
          showCongratsMessage();
        }
      } else {
        card1.innerText = '';
        card2.innerText = '';
      }
      flippedCards = [];
      isBusy = false; // Allow further clicks
    }, 1000);
  }
}

  function shuffleArray(array) {
    for (let i = array.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [array[i], array[j]] = [array[j], array[i]];
    }
  }

  function renderCards(cards) {
    const gameBoard = document.getElementById('gameBoard');
    cards.forEach(card => {
      const div = document.createElement('div');
      div.className = 'card';
      div.textContent = card.text;
      div.dataset.pairId = card.pairId;
      gameBoard.appendChild(div);
    });

    let selected = [];

    gameBoard.addEventListener('click', (event) => {
      const clicked = event.target;
      if (!clicked.classList.contains('card') || selected.includes(clicked)) return;

      clicked.classList.add('flipped');
      selected.push(clicked);

      if (selected.length === 2) {
        const [first, second] = selected;
        if (first.dataset.pairId === second.dataset.pairId) {
          first.classList.add('matched');
          second.classList.add('matched');
        } else {
          setTimeout(() => {
            first.classList.remove('flipped');
            second.classList.remove('flipped');
          }, 1000);
        }
        selected = [];
      }
    });
  }

  function showCongratsMessage() {
  const msg = document.createElement('div');
  msg.innerText = `🎉 Congratulations! You matched all the words!\nTries: ${tries}`;
  msg.className = 'congrats-message';
  // Create Play Again button
  const btn = document.createElement('button');
  btn.innerText = 'Play again';
  btn.className = 'play-again-btn';
  btn.onclick = () => window.location.reload();

  msg.appendChild(document.createElement('br'));
  msg.appendChild(btn);
  document.body.appendChild(msg);
}
</script>
