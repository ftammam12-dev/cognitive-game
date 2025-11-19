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
  {q:"What is 12 + 17?", options:["29","28","27","25"], correct:0},
  {q:"Synonym of 'calm'?", options:["Angry","Peaceful","Loud","Bright"], correct:1},
  {q:"Which number is larger?", options:["3/4","2/3","Both equal","None"], correct:0},
  {q:"Which does NOT belong?", options:["Dog","Cat","Bird","Apple"], correct:3},
  {q:"What is 7 x 6?", options:["42","36","48","40"], correct:0},
  {q:"Antonym of 'bright'?", options:["Dark","Light","Shine","Colorful"], correct:0},
  {q:"Plural of 'mouse'?", options:["Mouses","Mices","Mice","Mousees"], correct:2},
  {q:"Complete the analogy: hot : cold :: tall : ___", options:["Warm","Short","Long","Big"], correct:1},
  {q:"Next number: 2, 4, 8, 16, ___", options:["24","30","32","34"], correct:2},
  {q:"Is 82 a prime number?", options:["Yes","No","Sometimes","Not sure"], correct:1},
  {q:"What is 15 ÷ 3?", options:["4","5","6","7"], correct:1},
  {q:"Opposite of 'empty'?", options:["Full","Blank","Vacant","Nothing"], correct:0},
  {q:"What comes next in sequence: A, C, E, ___", options:["F","G","H","I"], correct:1},
  {q:"Which one is a fruit?", options:["Carrot","Tomato","Potato","Onion"], correct:1},
  {q:"If you flip a coin, probability of heads?", options:["25%","50%","75%","100%"], correct:1},
  {q:"Which shape has 4 equal sides?", options:["Square","Rectangle","Triangle","Circle"], correct:0},
  {q:"5 + 3 x 2 = ?", options:["16","11","13","10"], correct:1},
  {q:"Which is an even number?", options:["3","7","10","13"], correct:2},
  {q:"Synonym of 'fast'?", options:["Quick","Slow","Late","Weak"], correct:0},
  {q:"Opposite of 'cold'?", options:["Hot","Cool","Warm","Freeze"], correct:0},
  {q:"Which planet is known as Red Planet?", options:["Earth","Mars","Jupiter","Venus"], correct:1},
  {q:"12 - 5 = ?", options:["5","7","6","8"], correct:1},
  {q:"What color is the sky on a clear day?", options:["Red","Blue","Green","Yellow"], correct:1},
  {q:"Which is a mammal?", options:["Shark","Dolphin","Frog","Crocodile"], correct:1},
  {q:"What is 9 x 9?", options:["81","72","79","90"], correct:0},
  {q:"Synonym of 'happy'?", options:["Sad","Joyful","Angry","Tired"], correct:1},
  {q:"Which number is prime?", options:["4","9","13","12"], correct:2},
  {q:"What is half of 60?", options:["20","25","30","35"], correct:2},
  {q:"Which is a vegetable?", options:["Apple","Carrot","Banana","Mango"], correct:1},
  {q:"Next in series: 1,2,4,8,___", options:["10","12","16","14"], correct:2},
  {q:"Which is largest?", options:["Elephant","Dog","Cat","Mouse"], correct:0},
  {q:"Opposite of 'hard'?", options:["Soft","Strong","Rough","Tough"], correct:0},
  {q:"What is 50 ÷ 5?", options:["5","10","15","12"], correct:1},
  {q:"Synonym of 'small'?", options:["Tiny","Big","Huge","Large"], correct:0},
  {q:"Which one is a bird?", options:["Dog","Cat","Eagle","Shark"], correct:2},
  {q:"5 + 7 = ?", options:["10","12","11","13"], correct:1},
  {q:"Next letter after D?", options:["E","F","G","H"], correct:0},
  {q:"What is 20 - 8?", options:["10","12","13","11"], correct:1},
  {q:"Which is an odd number?", options:["2","4","7","10"], correct:2},
  {q:"Which planet is closest to the Sun?", options:["Earth","Mercury","Venus","Mars"], correct:1},
  {q:"What is 6 x 7?", options:["42","36","48","40"], correct:0},
  {q:"Synonym of 'angry'?", options:["Mad","Calm","Happy","Tired"], correct:0},
  {q:"What color is a banana?", options:["Blue","Green","Yellow","Red"], correct:2},
  {q:"Next number: 5,10,15,___", options:["20","25","30","18"], correct:0},
  {q:"Which is a fish?", options:["Shark","Dog","Cat","Eagle"], correct:0},
  {q:"What is 14 + 6?", options:["18","20","21","19"], correct:1},
  {q:"Opposite of 'up'?", options:["Down","Left","Right","Back"], correct:0},
  {q:"Which is a prime number?", options:["8","11","12","14"], correct:1},
  {q:"What is 100 ÷ 10?", options:["5","10","15","20"], correct:1},
  {q:"Synonym of 'smart'?", options:["Intelligent","Dumb","Slow","Weak"], correct:0},
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
