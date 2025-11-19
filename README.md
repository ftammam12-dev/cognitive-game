<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Cognitive Game - 50 Questions</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
body { font-family: Arial; padding: 20px; max-width: 800px; margin: auto; }
button { padding: 10px; margin: 5px 0; width: 100%; font-size: 16px; cursor: pointer; }
input, select { padding: 8px; margin: 5px 0; width: 100%; font-size: 16px; }
.hidden { display: none; }
#question-box { margin-bottom: 20px; font-size: 20px; }
</style>
</head>
<body>

<h2>Cognitive Game - 50 Questions</h2>

<div id="registration">
  <label>Name:</label>
  <input type="text" id="playerName" placeholder="Enter your name">
  <label>Nap Condition:</label>
  <select id="napCondition">
    <option value="Took a nap">Took a nap</option>
    <option value="No nap">No nap</option>
  </select>
  <label>Mood (1-5):</label>
  <input type="number" id="moodScale" min="1" max="5" value="3">
  <button onclick="startGame()">Start Game</button>
</div>

<div id="game" class="hidden">
  <div id="question-box">
    <h3 id="questionTitle"></h3>
    <p id="questionText"></p>
    <div id="options"></div>
    <p>Reaction time: <span id="reaction">0</span> sec</p>
  </div>
</div>

<div id="results" class="hidden">
  <h3>Game Finished!</h3>
  <p id="summary"></p>
</div>

<script>
// -------------------- VARIABLES --------------------
let index = 0;
let correct = 0;
let totalTime = 0;
let startTime;

// -------------------- QUESTIONS (50 SAMPLE QUESTIONS) --------------------
const questions = [
  {"q":"Evaluate: If f(x) = 3x^2 - 2x + 1, what is f(4)?","options":["41","45","49","51"],"correct":2},
  {"q":"True or False: The Great Wall of China is visible from the Moon.","options":["True","False"],"correct":1},
  {"q":"Solve for x: 5x - 7 = 18","options":["4","5","6","7"],"correct":3},
  {"q":"Which scientist proposed the three laws of motion?","options":["Einstein","Newton","Galileo","Tesla"],"correct":1},
  {"q":"True or False: The Amazon River is the longest river in the world.","options":["True","False"],"correct":1},
  {"q":"Find the derivative of f(x) = x^3 + 5x","options":["3x^2 + 5","x^3","5x","3x^2"],"correct":0},
  {"q":"True or False: The chemical formula for ozone is O2.","options":["True","False"],"correct":1},
  {"q":"If sin(θ) = 1/2 and θ ∈ [0,360°], what is θ?","options":["30° & 150°","45° & 135°","60° & 120°","90° only"],"correct":0},
  {"q":"Which year did World War II end?","options":["1942","1945","1939","1948"],"correct":1},
  {"q":"True or False: Light behaves as both a particle and a wave.","options":["True","False"],"correct":0},
  {"q":"Solve: 2^x = 32","options":["4","5","6","8"],"correct":1},
  {"q":"Who wrote '1984'?","options":["George Orwell","Aldous Huxley","J.K. Rowling","Mark Twain"],"correct":0},
  {"q":"True or False: The human heart has four chambers.","options":["True","False"],"correct":0},
  {"q":"Find the area of a triangle with base 10 and height 6","options":["30","60","16","36"],"correct":0},
  {"q":"Which element has the atomic number 6?","options":["Carbon","Oxygen","Nitrogen","Helium"],"correct":0},
  {"q":"True or False: The speed of sound is faster than the speed of light.","options":["True","False"],"correct":1},
  {"q":"Simplify: (x^2y)(3xy^2)","options":["3x^3y^3","3x^2y^3","3x^3y^2","3x^2y^2"],"correct":0},
  {"q":"Which planet is known as the Morning Star?","options":["Venus","Mars","Mercury","Jupiter"],"correct":0},
  {"q":"True or False: The Renaissance began in France.","options":["True","False"],"correct":1},
  {"q":"Evaluate: 7! / (5! * 2!)","options":["21","42","35","28"],"correct":2},
  {"q":"Who is known as the father of modern physics?","options":["Newton","Einstein","Galileo","Faraday"],"correct":1},
  {"q":"True or False: A parallelogram always has right angles.","options":["True","False"],"correct":1},
  {"q":"Solve: 3x + 4 = 19","options":["5","6","7","8"],"correct":2},
  {"q":"Which country is called the Land of the Rising Sun?","options":["China","Japan","Thailand","India"],"correct":1},
  {"q":"True or False: DNA stands for Deoxyribonucleic Acid.","options":["True","False"],"correct":0},
  {"q":"Find x: log10(x) = 3","options":["10","100","1000","10000"],"correct":2},
  {"q":"Who painted the Mona Lisa?","options":["Van Gogh","Leonardo da Vinci","Michelangelo","Raphael"],"correct":1},
  {"q":"True or False: The circumference of a circle is 2πr.","options":["True","False"],"correct":0},
  {"q":"Solve: x^2 - 16 = 0","options":["x = ±4","x = 4 only","x = -4 only","x = ±8"],"correct":0},
  {"q":"Which organ detoxifies chemicals in the human body?","options":["Liver","Kidney","Lungs","Heart"],"correct":0},
  {"q":"True or False: Pi is a rational number.","options":["True","False"],"correct":1},
  {"q":"Evaluate: ∫0^1 x dx","options":["1","0.5","1.5","2"],"correct":1},
  {"q":"Who discovered penicillin?","options":["Marie Curie","Alexander Fleming","Louis Pasteur","Gregor Mendel"],"correct":1},
  {"q":"True or False: Sound requires a medium to travel.","options":["True","False"],"correct":0},
  {"q":"Solve: 2x^2 - 8x = 0","options":["x=0 or x=4","x=2 only","x=-2 only","x=0 or x=-4"],"correct":0},
  {"q":"Which gas is most abundant in Earth's atmosphere?","options":["Oxygen","Nitrogen","Carbon Dioxide","Hydrogen"],"correct":1},
  {"q":"True or False: The square root of 2 is an irrational number.","options":["True","False"],"correct":0},
  {"q":"If f(x) = 2x + 5, find f^-1(x)","options":["(x-5)/2","2x+5","x/2 +5","x-5"],"correct":0},
  {"q":"Who wrote 'Hamlet'?","options":["Shakespeare","Hemingway","Frost","Tolkien"],"correct":0},
  {"q":"True or False: Absolute zero is 0°C.","options":["True","False"],"correct":1},
  {"q":"Solve: 4(2x - 3) = 20","options":["x = 4","x = 5","x = 6","x = 3"],"correct":0},
  {"q":"Which planet has the shortest day in the solar system?","options":["Jupiter","Saturn","Earth","Mars"],"correct":0},
  {"q":"True or False: The Great Barrier Reef is located off the coast of Australia.","options":["True","False"],"correct":0},
  {"q":"Find the sum of the first 15 natural numbers","options":["105","100","110","120"],"correct":0},
  {"q":"Who developed the theory of general relativity?","options":["Einstein","Newton","Galileo","Maxwell"],"correct":0},
  {"q":"True or False: The boiling point of water decreases as altitude increases.","options":["True","False"],"correct":0},
  {"q":"Solve: x^3 = 27","options":["3","9","27","1"],"correct":0},
  {"q":"Which continent is the Sahara Desert located in?","options":["Asia","Africa","Australia","South America"],"correct":1},
  {"q":"True or False: Electrons are heavier than protons.","options":["True","False"],"correct":1},
  {"q":"Evaluate: 6C3","options":["20","15","18","10"],"correct":1},
  {"q":"Who wrote 'Pride and Prejudice'?","options":["Jane Austen","Charles Dickens","Leo Tolstoy","Virginia Woolf"],"correct":0},
  {"q":"True or False: Venus is closer to the Sun than Mercury.","options":["True","False"],"correct":1}
];

// -------------------- START GAME --------------------
    function startGame() {
      if (document.getElementById("playerName").value.trim() === "") {
        alert("Please enter your name!");
        return;
      }
      document.getElementById("registration").classList.add("hidden");
      document.getElementById("game").classList.remove("hidden");
      showQuestion();
    }

    function showQuestion() {
      let q = questions[index];
      document.getElementById("questionTitle").innerText = "Question " + (index + 1) + " of " + questions.length;
      document.getElementById("questionText").innerText = q.q;
      let html = "";
      q.options.forEach(opt => {
        html += `<button onclick="answer('${opt}')">${opt}</button><br>`;
      });
      document.getElementById("options").innerHTML = html;
      startTime = Date.now();
      document.getElementById("reaction").innerText = "0";
    }

    function answer(selected) {
      let q = questions[index];
      let reactionTime = (Date.now() - startTime) / 1000;
      totalTime += reactionTime;
      document.getElementById("reaction").innerText = reactionTime.toFixed(2);
      if (q.options[q.correct] === selected) correct++;
      index++;
      if (index < questions.length) showQuestion();
      else endGame();
    }

    function endGame() {
      document.getElementById("game").classList.add("hidden");
      document.getElementById("results").classList.remove("hidden");

      let accuracy = (correct / questions.length) * 100;
      let score = correct * 8;
      let avgTime = totalTime / questions.length;

      document.getElementById("summary").innerHTML =
        `Correct: ${correct}<br>` +
        `Accuracy: ${accuracy.toFixed(2)}%<br>` +
        `Score: ${score}<br>` +
        `Total Time: ${totalTime.toFixed(2)} sec<br>` +
        `Average Reaction Time: ${avgTime.toFixed(2)} sec`;

      const player = {
        name: document.getElementById("playerName").value,
        napCondition: document.getElementById("napCondition").value,
        mood: document.getElementById("moodScale").value,
        score: score,
        avgTime: avgTime
      };

      submitToSheet(player);
    }

    function submitToSheet(player) {
      const url = "https://script.google.com/macros/s/AKfycbwotonaTo-9HLQfc27M934-k-VJYrjND3uNFCTdbH4I7rrG4QU3-pV-VjQNd88x-A5L/exec";

      fetch(url, {
        method: "POST",
        redirect: "follow",  // important for GAS redirect
        headers: {
          "Content-Type": "text/plain;charset=utf-8"
        },
        body: JSON.stringify(player)
      })
      .then(response => response.json())
      .then(data => {
        console.log("تم إرسال البيانات:", data);
      })
      .catch(err => {
        console.error("خطأ في الإرسال:", err);
      });
    }
  </script>

</body>
</html>
