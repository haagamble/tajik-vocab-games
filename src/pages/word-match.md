---
title: "Word Match"
description: "Match English words to their Tajik translations."
---

<div class="game-board" id="gameBoard"></div>

<style>
  /* Paste the CSS styles here from the previous example */
  body {
    font-family: Arial, sans-serif;
    background: #f0f4f8;
  }
  .game-board {
    display: grid;
    grid-template-columns: repeat(4, 120px);
    grid-gap: 15px;
    perspective: 1000px;
    max-width: 520px;
    margin: 2rem auto;
  }
  .card {
    width: 120px;
    height: 80px;
    cursor: pointer;
    position: relative;
    transform-style: preserve-3d;
    transition: transform 0.5s;
    box-shadow: 0 4px 6px rgba(0,0,0,0.1);
    border-radius: 8px;
    background: #2d89ef;
    color: white;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 1.2em;
    user-select: none;
  }
  .card.flipped {
    transform: rotateY(180deg);
    background: #fff;
    color: #333;
    cursor: default;
  }
  .card .front,
  .card .back {
    position: absolute;
    width: 100%;
    height: 100%;
    backface-visibility: hidden;
    border-radius: 8px;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 10px;
    box-sizing: border-box;
  }
  .card .front {
    background: #2d89ef;
    color: white;
  }
  .card .back {
    background: #fff;
    color: #333;
    transform: rotateY(180deg);
    border: 2px solid #2d89ef;
  }
</style>

<script>
  // Paste the JS code from the example here:

  const pairs = [
    {tajik: "салом", english: "hello"},
    {tajik: "об", english: "water"},
    {tajik: "китоб", english: "book"},
    {tajik: "дӯст", english: "friend"},
    {tajik: "маҳтоб", english: "moon"},
    {tajik: "рӯзи", english: "day"},
  ];

  let cards = [];
  pairs.forEach((pair, index) => {
    cards.push({id: index, text: pair.tajik, language: "tajik"});
    cards.push({id: index, text: pair.english, language: "english"});
  });

  function shuffle(array) {
    for (let i = array.length - 1; i > 0; i--) {
      let j = Math.floor(Math.random() * (i + 1));
      [array[i], array[j]] = [array[j], array[i]];
    }
    return array;
  }

  cards = shuffle(cards);

  const gameBoard = document.getElementById("gameBoard");
  let flippedCards = [];
  let matchedIds = new Set();

  function createCardElement(card, index) {
    const cardEl = document.createElement("div");
    cardEl.classList.add("card");
    cardEl.dataset.id = card.id;
    cardEl.dataset.index = index;

    const front = document.createElement("div");
    front.classList.add("front");
    front.textContent = "?";

    const back = document.createElement("div");
    back.classList.add("back");
    back.textContent = card.text;

    cardEl.appendChild(front);
    cardEl.appendChild(back);

    cardEl.addEventListener("click", () => {
      if (
        flippedCards.length < 2 &&
        !cardEl.classList.contains("flipped") &&
        !matchedIds.has(card.id)
      ) {
        flipCard(cardEl);
      }
    });

    return cardEl;
  }

  function flipCard(cardEl) {
    cardEl.classList.add("flipped");
    flippedCards.push(cardEl);

    if (flippedCards.length === 2) {
      checkMatch();
    }
  }

  function checkMatch() {
    const [cardA, cardB] = flippedCards;
    const idA = cardA.dataset.id;
    const idB = cardB.dataset.id;

    if (idA === idB && cardA !== cardB) {
      matchedIds.add(idA);
      flippedCards = [];

      if (matchedIds.size === pairs.length) {
        setTimeout(() => alert("Well done! You matched all pairs! 🎉"), 300);
      }
    } else {
      setTimeout(() => {
        cardA.classList.remove("flipped");
        cardB.classList.remove("flipped");
        flippedCards = [];
      }, 1000);
    }
  }

  function setupGame() {
    gameBoard.innerHTML = "";
    cards.forEach((card, i) => {
      gameBoard.appendChild(createCardElement(card, i));
    });
  }

  setupGame();
</script>
