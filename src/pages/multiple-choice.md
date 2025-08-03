---
title: "Multiple Choice Quiz"
description: "Test your Tajik vocabulary with a multiple choice quiz."
---

<div id="quiz-container" class="quiz-container"></div>

<script>
let words = [];
let quizWords = [];
let current = 0;
let score = 0;

fetch('{{ "/assets/data/first-100-words.json" | url }}')
  .then(res => res.json())
  .then(data => {
    words = data;
    quizWords = shuffleArray([...words]).slice(0, 20);
    showQuestion();
  });

function shuffleArray(array) {
  for (let i = array.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [array[i], array[j]] = [array[j], array[i]];
  }
  return array;
}

function showQuestion() {
  const container = document.getElementById('quiz-container');
  container.innerHTML = '';
  if (current >= quizWords.length) {
    container.innerHTML = `<div class="quiz-result">Quiz complete!<br>Score: ${score} / ${quizWords.length}
      <br><button onclick="window.location.reload()">Play Again</button></div>`;
    return;
  }
  const q = quizWords[current];
  // Get 3 random distractors (not the correct answer)
  const distractors = shuffleArray(words.filter(w => w.tajik !== q.tajik)).slice(0, 3);
  const choices = shuffleArray([q, ...distractors]);
  container.innerHTML = `
    <div class="quiz-q"> <b>${q.english}</b>?</div>
    <div class="quiz-choices">
      ${choices.map(c => `<button class="quiz-choice" onclick="checkAnswer('${c.tajik.replace(/'/g,"\\'")}')">${c.tajik}</button>`).join('')}
    </div>
    <div class="quiz-progress">Question ${current + 1} of ${quizWords.length}</div>
  `;
  window.checkAnswer = function(answer) {
    const correct = q.tajik;
    const btns = document.querySelectorAll('.quiz-choice');
    btns.forEach(btn => {
      if (btn.textContent === correct) {
        btn.classList.add('correct');
      } else if (btn.textContent === answer) {
        btn.classList.add('incorrect');
      }
      btn.disabled = true;
    });
    if (answer === correct) score++;
    setTimeout(() => {
      current++;
      showQuestion();
    }, 900);
  }
}
</script>

