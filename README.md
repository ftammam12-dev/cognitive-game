<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title> Game - 50 Questions</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
body { font-family: Arial; padding: 20px; max-width: 800px; margin: auto; }
button { padding: 10px; margin: 5px 0; width: 100%; font-size: 16px; }
input, select { padding: 8px; margin: 5px 0; width: 100%; font-size: 16px; }
#question-box { margin-bottom: 20px; font-size: 20px; }
.hidden { display: none; }
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
  <div id="question-box"></div>
  <div id="answers"></div>
</div>

<div id="results" class="hidden"></div>

<script>
// EXACTLY 50 questions
const questions = [
{q:"What is 12 + 17?", options:["29","28","27","25"], correct:0},
{q:"Synonym of 'calm'?", options:["Angry","Peaceful","Loud","Bright"], correct:1},
{q:"Which number is larger?", options:["3/4","2/3","Both equal","None"], correct:0},
{q:"Which does NOT belong?", options:["Dog","Cat","Bird","Apple"], correct:3},
{q:"What is 7 x 6?", options:["42","36","48","40"], correct:0},
{q:"Antonym of 'bright'? ", options:["Dark","Light","Shine","Colorful"], correct:0},
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
{q:"Which is a reptile?", options:["Snake","Dog","Cat","Cow"], correct:0},
];

let playerResults = [];
let player = {};
let index = 0;
let times = [];

function startGame(){
  const name = document.getElementById("playerName").value.trim();
  const nap = document.getElementById("napCondition").value;
  const mood = document.getElementById("moodScale").value;
  if(!name){ alert("Enter name"); return; }
  player = {name, napCondition:nap, mood, correct:0, score:0, avgTime:0};
  index=0; times=[];
  document.getElementById("registration").classList.add("hidden");
  document.getElementById("game").classList.remove("hidden");
  loadQuestion();
}

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
  const reactionTime = (Date.now() - startTime)/1000;
  times.push(reactionTime);
  if(choice === questions[index].correct) player.correct++;
  index++;
  if(index<questions.length) loadQuestion();
  else endGame();
}

function endGame(){
  const pointsPerCorrect = 320/44;
  player.score = +(player.correct*pointsPerCorrect).toFixed(2);
  player.avgTime = +(times.reduce((a,b)=>a+b,0)/times.length).toFixed(2);
  playerResults.push(player);
  document.getElementById("game").classList.add("hidden");
  showResults();
  submitToGoogleForm(player);
}

function showResults(){
  const resultsDiv = document.getElementById("results");
  resultsDiv.classList.remove("hidden");
  let html = `<h3>Player Completed</h3>
  <p><strong>Name:</strong> ${player.name}</p>
  <p><strong>Nap Condition:</strong> ${player.napCondition}</p>
  <p><strong>Mood:</strong> ${player.mood}</p>
  <p><strong>Correct Answers:</strong> ${player.correct} / 50</p>
  <p><strong>Score:</strong> ${player.score}</p>
  <p><strong>Average Time per Question:</strong> ${player.avgTime.toFixed(2)} sec</p>
  <button onclick="restart()">Restart</button>`;
  resultsDiv.innerHTML = html;
}

function restart(){
  document.getElementById("results").classList.add("hidden");
  document.getElementById("registration").classList.remove("hidden");
}

// --------------------------
// Google Form submission
// --------------------------
function submitToGoogleForm(player){
  const url = "https://docs.google.com/forms/d/e/1FAIpQLScJMOM5ykAKDG-nlsJSRvpvfQYlYT3Bj-50vVpfLB6apSkhpw/formResponse"; // replace with your form URL
  const formData = new FormData();
  formData.append("entry.1402254112", player.name); // replace with actual entry IDs
  formData.append("entry.659099207", player.napCondition);
  formData.append("entry.92104427", player.mood);
  formData.append("entry.1990563217", player.score);
  formData.append("entry.1887608061", player.avgTime.toFixed(2));
  fetch(url, { method:"POST", body:formData, mode:"no-cors" });
}
</script>
</body>
</html>
