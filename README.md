# cognitive-game
Game
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Cognitive Game - 50 Questions</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
body { font-family: Arial; padding: 20px; max-width: 700px; margin: auto; }
button { padding: 10px; margin: 5px 0; width: 100%; font-size: 16px; }
#question-box { margin-bottom: 20px; font-size: 20px; }
.hidden { display: none; }
</style>
</head>
<body>
<h2>Cognitive Game - 50 Questions</h2>
<div id="game">
  <div id="question-box"></div>
  <div id="answers"></div>
</div>
<div id="results" class="hidden"></div>

<script>
// Example 50 questions template (replace with actual questions)
const questions = [
  {q:"What is 12 + 17?", options:["29","28","27","25"], correct:0},
  {q:"Synonym of 'calm'?", options:["Angry","Peaceful","Loud","Bright"], correct:1},
  {q:"Which number is larger?", options:["3/4","2/3","Both equal","None"], correct:0},
  {q:"Which does NOT belong?", options:["Dog","Cat","Bird","Apple"], correct:3},
  {q:"What is 7 x 6?", options:["42","36","48","40"], correct:0},
  // Add remaining 45 questions here in same format
];

let index = 0;
let times = [];
let correctAnswers = 0;
let startTime;
const pointsPerCorrect = 320 / 44;

function loadQuestion(){
  const q = questions[index];
  document.getElementById("question-box").textContent = `Q${index+1}: ${q.q}`;
  
  const answersDiv = document.getElementById("answers");
  answersDiv.innerHTML = "";
  
  q.options.forEach((opt,i)=>{
    const btn = document.createElement("button");
    btn.textContent = opt;
    btn.onclick = ()=>recordAnswer(i);
    answersDiv.appendChild(btn);
  });
  
  startTime = Date.now();
}

function recordAnswer(choice){
  const endTime = Date.now();
  const reactionTime = ((endTime - startTime)/1000).toFixed(2);
  times.push(Number(reactionTime));
  
  if(choice === questions[index].correct){
    correctAnswers++;
  }
  
  index++;
  if(index < questions.length){
    loadQuestion();
  } else {
    showResults();
  }
}

function showResults(){
  document.getElementById("game").classList.add("hidden");
  const resultsDiv = document.getElementById("results");
  resultsDiv.classList.remove("hidden");
  
  const totalScore = (correctAnswers * pointsPerCorrect).toFixed(2);
  const averageTime = (times.reduce((a,b)=>a+b,0)/times.length).toFixed(2);
  
  resultsDiv.innerHTML = `<h3>Game Completed</h3>
    <p><strong>Total Correct:</strong> ${correctAnswers} / ${questions.length}</p>
    <p><strong>Total Score:</strong> ${totalScore}</p>
    <p><strong>Average Time per Question:</strong> ${averageTime} seconds</p>`;
}

loadQuestion();
</script>
</body>
</html>
