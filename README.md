<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0">
<title>Math Magic - Latihan Perkalian</title>
<style>
*{box-sizing:border-box}
body{margin:0;font-family:Arial,sans-serif;background:#071b49;color:#10245a}
.app{min-height:100vh;background:linear-gradient(135deg,#061a49,#0d3f8f);padding:18px}
.shell{max-width:900px;margin:auto}
.header{display:flex;justify-content:space-between;align-items:center;color:white;margin-bottom:14px}
.brand{font-weight:800;font-size:20px}.brand span{color:#ffd43b}
.progress{font-weight:700}
.card{background:#fff;border-radius:24px;padding:24px;box-shadow:0 12px 35px #0005}
.badge{display:inline-block;background:#ffd43b;color:#071b49;font-weight:800;padding:8px 14px;border-radius:999px}
.timer-label{text-align:center;font-weight:700;color:#61708e;margin-top:14px;font-size:14px;letter-spacing:1px}
.timer{font-size:52px;font-weight:900;text-align:center;color:#0b3f91;margin:4px 0 12px;transition:color .2s}
.timer.warn{color:#f9a825}
.timer.danger{color:#d62828;animation:pulse .6s infinite}
@keyframes pulse{50%{transform:scale(1.08)}}
.timebar{height:10px;background:#e8eef8;border-radius:999px;overflow:hidden;margin-bottom:18px}
.timebar i{display:block;height:100%;width:100%;background:linear-gradient(90deg,#ffd43b,#1455a3);transition:width 1s linear}
.question{font-size:22px;line-height:1.5;font-weight:700;margin:18px 0}
.options{display:grid;gap:10px}
.option{border:2px solid #d8e0ef;border-radius:14px;background:white;padding:14px;text-align:left;font-size:18px;cursor:pointer;color:#10245a}
.option:hover{border-color:#1455a3;background:#f4f8ff}
.option.selected{border-color:#1455a3;background:#eaf2ff}
.option.correct{border-color:#2e7d32;background:#e8f5e9}
.option.wrong{border-color:#d62828;background:#fdecea}
.option:disabled{cursor:default;opacity:.9}
button{border:0;border-radius:14px;padding:13px 18px;font-weight:800;cursor:pointer;font-family:inherit}
button:disabled{opacity:.4;cursor:not-allowed}
.primary{background:#ffd43b;color:#071b49}
.secondary{background:#e8eef8;color:#10245a}
.nav-row{display:flex;gap:8px;flex-wrap:wrap;margin-top:14px;justify-content:space-between}
.nav-row .group{display:flex;gap:8px;flex-wrap:wrap}
.explain{position:relative;border:2px dashed #b9c7df;border-radius:18px;overflow:hidden;margin-top:15px;background:#fff}
.explain .questionText{padding:20px;min-height:220px;font-size:19px;line-height:1.55;font-weight:700;white-space:pre-line}
canvas{position:absolute;inset:0;width:100%;height:100%;touch-action:none}
.draw-area{height:330px}
.toolbar{display:flex;gap:8px;flex-wrap:wrap;align-items:center;margin-top:12px}
.toolbar input{width:90px}
.result{text-align:center;padding:30px}
.result h1{font-size:42px;margin:8px;color:#0b3f91}
.small{color:#61708e}
.hidden{display:none}
.footer{color:#d9e5ff;text-align:center;font-size:13px;margin-top:12px}
@media(max-width:600px){
 .app{padding:10px}.card{padding:16px;border-radius:18px}
 .question{font-size:19px}.timer{font-size:44px}
 .option{font-size:16px}.draw-area{height:300px}
 .nav-row{flex-direction:column}
 .nav-row .group{width:100%}
 .nav-row .group button{flex:1}
}
</style>
</head>
<body>
<div class="app">
<div class="shell">
<header class="header">
<div class="brand">✨ MATH <span>MAGIC</span> ACADEMY</div>
<div class="progress" id="progress"></div>
</header>

<section id="quiz" class="card">
<div class="badge">TIME FOR LEARNING</div>
<div class="timer-label">⏱️ WAKTU TERSISA</div>
<div class="timer" id="timer">30</div>
<div class="timebar"><i id="timebar"></i></div>
<div class="question" id="question"></div>
<div class="options" id="options"></div>
<div class="nav-row">
  <div class="group">
    <button class="secondary" id="prevBtn">⬅️ Soal Sebelumnya</button>
  </div>
  <div class="group">
    <button class="primary" id="answerBtn">Jawab</button>
  </div>
</div>
<p class="small">Waktu tiap soal: 30 detik. Setelah waktu habis, masuk ke pembahasan.</p>
</section>

<section id="explanation" class="card hidden">
<div class="badge">✏️ PEMBAHASAN</div>
<div class="explain draw-area">
<div class="questionText" id="explainQuestion"></div>
<canvas id="canvas"></canvas>
</div>
<div class="toolbar">
<button class="primary" id="penBtn">✏️ Pena</button>
<button class="secondary" id="eraserBtn">🧹 Penghapus</button>
<button class="secondary" id="undoBtn">↩️ Undo</button>
<button class="secondary" id="clearBtn">🗑️ Bersihkan</button>
<label>Ukuran <input id="size" type="range" min="2" max="12" value="4"></label>
</div>
<div class="nav-row">
  <div class="group">
    <button class="secondary" id="prevBtn2">⬅️ Soal Sebelumnya</button>
  </div>
  <div class="group">
    <button class="primary" id="nextBtn">Soal Berikutnya →</button>
  </div>
</div>
</section>

<section id="result" class="card hidden result">
<div class="badge">🏆 MATH MAGIC</div>
<h1>YOU ARE THE WINNER!</h1>
<p id="score"></p>
<button class="primary" onclick="location.reload()">Main Lagi</button>
</section>

<div class="footer">Pahami • Temukan • Taklukkan ✨</div>
</div>
</div>

<script>
const questions = [
["Manakah pasangan operasi perkalian yang menghasilkan nilai yang sama?",["130 × 230 = 160 × 200","110 × 270 = 160 × 200","120 × 250 = 150 × 200","130 × 270 = 160 × 200","110 × 270 = 140 × 200"],2],
["Manakah pasangan operasi perkalian yang menghasilkan nilai yang sama?",["140 × 300 = 150 × 250","140 × 300 = 170 × 250","130 × 300 = 170 × 250","125 × 320 = 160 × 250","135 × 300 = 170 × 250"],3],
["Manakah pasangan operasi perkalian yang menghasilkan nilai yang sama?",["105 × 410 = 150 × 275","110 × 400 = 160 × 275","115 × 390 = 170 × 275","100 × 390 = 170 × 275","125 × 380 = 150 × 275"],1],
["Manakah pasangan operasi perkalian yang menghasilkan nilai yang sama?",["110 × 410 = 130 × 300","115 × 380 = 130 × 300","95 × 410 = 150 × 300","105 × 400 = 140 × 300","95 × 390 = 150 × 300"],3],
["Manakah pasangan operasi perkalian yang menghasilkan nilai yang sama?",["135 × 240 = 180 × 180","145 × 230 = 170 × 180","125 × 250 = 170 × 180","130 × 230 = 170 × 180","140 × 230 = 190 × 180"],0],
["Manakah pasangan operasi perkalian yang menghasilkan nilai yang sama?",["130 × 380 = 140 × 300","110 × 380 = 160 × 300","115 × 340 = 140 × 300","125 × 360 = 150 × 300","140 × 370 = 160 × 300"],3],
["Manakah pasangan operasi perkalian yang menghasilkan nilai yang sama?",["145 × 235 = 190 × 200","160 × 225 = 180 × 200","175 × 235 = 190 × 200","150 × 215 = 170 × 200","165 × 205 = 190 × 200"],1],
["Manakah pasangan operasi perkalian yang menghasilkan nilai yang sama?",["145 × 280 = 165 × 240","140 × 300 = 175 × 240","135 × 290 = 165 × 240","130 × 280 = 185 × 240","135 × 290 = 185 × 240"],1],
["Manakah pasangan operasi perkalian yang menghasilkan nilai yang sama?",["115 × 260 = 165 × 200","125 × 280 = 175 × 200","115 × 300 = 165 × 200","115 × 260 = 185 × 200","140 × 290 = 165 × 200"],1],
["Manakah pasangan operasi perkalian yang menghasilkan nilai yang sama?",["155 × 250 = 190 × 200","150 × 240 = 180 × 200","135 × 230 = 170 × 200","140 × 220 = 190 × 200","135 × 260 = 170 × 200"],1],
["Manakah pasangan operasi perkalian yang menghasilkan nilai yang sama?",["140 × 280 = 160 × 245","125 × 300 = 170 × 245","135 × 270 = 150 × 245","155 × 260 = 170 × 245","150 × 300 = 150 × 245"],0],
["Manakah pasangan operasi perkalian yang menghasilkan nilai yang sama?",["130 × 370 = 170 × 270","120 × 360 = 160 × 270","135 × 350 = 170 × 270","130 × 380 = 170 × 270","115 × 380 = 170 × 270"],1],
["Manakah pasangan operasi perkalian yang menghasilkan nilai yang sama?",["120 × 300 = 185 × 200","125 × 280 = 175 × 200","140 × 300 = 185 × 200","135 × 270 = 185 × 200","115 × 270 = 165 × 200"],1],
["Manakah pasangan operasi perkalian yang menghasilkan nilai yang sama?",["110 × 310 = 140 × 240","105 × 310 = 160 × 240","120 × 300 = 150 × 240","110 × 310 = 160 × 240","115 × 290 = 160 × 240"],2],
["Manakah pasangan operasi perkalian yang menghasilkan nilai yang sama?",["150 × 240 = 180 × 200","165 × 230 = 190 × 200","140 × 220 = 170 × 200","145 × 250 = 170 × 200","165 × 220 = 190 × 200"],0],
["Manakah pasangan operasi perkalian yang menghasilkan nilai yang sama?",["135 × 270 = 165 × 200","130 × 240 = 185 × 200","135 × 240 = 165 × 200","135 × 260 = 185 × 200","140 × 250 = 175 × 200"],4],
["Manakah pasangan operasi perkalian yang menghasilkan nilai yang sama?",["155 × 245 = 170 × 200","150 × 205 = 190 × 200","160 × 225 = 180 × 200","145 × 215 = 170 × 200","145 × 205 = 170 × 200"],2],
["Manakah pasangan operasi perkalian yang menghasilkan nilai yang sama?",["135 × 360 = 180 × 270","125 × 350 = 170 × 270","125 × 350 = 190 × 270","120 × 350 = 170 × 270","150 × 380 = 170 × 270"],0],
["Manakah pasangan operasi perkalian yang menghasilkan nilai yang sama?",["150 × 240 = 165 × 200","155 × 260 = 165 × 200","140 × 250 = 175 × 200","130 × 230 = 165 × 200","135 × 260 = 185 × 200"],2],
["Manakah pasangan operasi perkalian yang menghasilkan nilai yang sama?",["100 × 310 = 150 × 230","105 × 300 = 150 × 230","125 × 310 = 170 × 230","115 × 320 = 160 × 230","130 × 310 = 170 × 230"],3]
];

const DURASI = 30;

let index = 0;
let score = 0;
let time = DURASI;
let timerId = null;
let selected = null;

const jawabanUser = new Array(questions.length).fill(null);
const sudahDihitung = new Array(questions.length).fill(false);

const q=document.getElementById("quiz"), e=document.getElementById("explanation"), r=document.getElementById("result");
const timer=document.getElementById("timer"), progress=document.getElementById("progress");
const timebar=document.getElementById("timebar");
const canvas=document.getElementById("canvas"), ctx=canvas.getContext("2d");
let drawing=false, erase=false, history=[];

function resetTimerUI(){
  time = DURASI;
  timer.textContent = time;
  timer.classList.remove("warn","danger");
  timebar.style.transition = "none";
  timebar.style.width = "100%";
  void timebar.offsetWidth;
  timebar.style.transition = "width 1s linear";
}
function stopTimer(){ if(timerId){ clearInterval(timerId); timerId = null; } }
function startTimer(){
  stopTimer();
  timebar.style.width = "0%";
  timerId = setInterval(()=>{
    time--;
    timer.textContent = time;
    if(time <= 10) timer.classList.add("warn");
    if(time <= 3)  timer.classList.add("danger");
    if(time <= 0){ stopTimer(); showExplanation(); }
  }, 1000);
}

function updatePrevButtons(){
  const noPrev = index === 0;
  document.getElementById("prevBtn").disabled  = noPrev;
  document.getElementById("prevBtn2").disabled = noPrev;
}

function showQuestion(){
  stopTimer();
  q.classList.remove("hidden"); e.classList.add("hidden"); r.classList.add("hidden");
  progress.textContent = ${index+1} / ${questions.length};
  document.getElementById("question").textContent = questions[index][0];

  const box = document.getElementById("options"); box.innerHTML="";
  selected = jawabanUser[index];

  questions[index][1].forEach((x,i)=>{
    const b = document.createElement("button");
    b.className = "option";
    b.textContent = String.fromCharCode(65+i)+". "+x;
    b.onclick = ()=>{
      if(jawabanUser[index] !== null) return;
      selected = i;
      document.querySelectorAll(".option").forEach(o=>o.classList.remove("selected"));
      b.classList.add("selected");
    };
    if(selected === i) b.classList.add("selected");
    if(jawabanUser[index] !== null){
      if(i === questions[index][2]) b.classList.add("correct");
      else if(i === jawabanUser[index]) b.classList.add("wrong");
      b.disabled = true;
    }
    box.appendChild(b);
  });

  updatePrevButtons();

  if(jawabanUser[index] !== null){
    showExplanation();
    return;
  }

  resetTimerUI();
  startTimer();
}

function showExplanation(){
  stopTimer();
  q.classList.add("hidden");
  e.classList.remove("hidden");

  const qText = questions[index][0];
  const opts = questions[index][1].map((x,i)=>{
    let mark = "";
    if(i === questions[index][2]) mark = " ✅";
    if(jawabanUser[index] === i && i !== questions[index][2]) mark = " ❌";
    return String.fromCharCode(65+i)+". "+x+mark;
  }).join("\n");

  let info = qText + "\n\n" + opts;
  if(jawabanUser[index] === null){
    info += "\n\n⏰ Waktu habis / belum dijawab. Jawaban benar: " + String.fromCharCode(65+questions[index][2]) + ".";
  } else if(jawabanUser[index] === questions[index][2]){
    info += "\n\n🎉 Jawabanmu BENAR!";
  } else {
    info += "\n\n❌ Jawabanmu salah. Jawaban benar: " + String.fromCharCode(65+questions[index][2]) + ".";
  }
  document.getElementById("explainQuestion").textContent = info;

  updatePrevButtons();
  document.getElementById("nextBtn").textContent = (index === questions.length-1) ? "🏁 Lihat Hasil" : "Soal Berikutnya →";

  setupCanvas();
}

function setupCanvas(){
  const rect = canvas.getBoundingClientRect(), dpr = devicePixelRatio||1;
  canvas.width = rect.width * dpr;
  canvas.height = rect.height * dpr;
  ctx.setTransform(dpr,0,0,dpr,0,0);
  history = [];
  ctx.clearRect(0,0,rect.width,rect.height);
}
function pos(ev){
  const r = canvas.getBoundingClientRect();
  return [ev.clientX - r.left, ev.clientY - r.top];
}
canvas.addEventListener("pointerdown", ev=>{
  drawing = true;
  canvas.setPointerCapture(ev.pointerId);
  const [x,y] = pos(ev);
  history.push(ctx.getImageData(0,0,canvas.width,canvas.height));
  ctx.beginPath();
  ctx.moveTo(x,y);
});
canvas.addEventListener("pointermove", ev=>{
  if(!drawing) return;
  const [x,y] = pos(ev);
  ctx.lineWidth = +document.getElementById("size").value;
  ctx.lineCap = "round";
  ctx.strokeStyle = erase ? "#ffffff" : "#0b3f91";
  ctx.lineTo(x,y);
  ctx.stroke();
});
canvas.addEventListener("pointerup", ()=> drawing=false);
canvas.addEventListener("pointercancel", ()=> drawing=false);

document.getElementById("eraserBtn").onclick = ()=> erase = true;
document.getElementById("penBtn").onclick    = ()=> erase = false;
document.getElementById("clearBtn").onclick  = ()=>{ ctx.clearRect(0,0,canvas.width,canvas.height); history=[]; };
document.getElementById("undoBtn").onclick   = ()=>{ if(history.length) ctx.putImageData(history.pop(),0,0); };

document.getElementById("answerBtn").onclick = ()=>{
  if(jawabanUser[index] !== null) return;
  if(selected === null){
    alert("Pilih salah satu jawaban dulu ya 😊");
    return;
  }
  stopTimer();
  jawabanUser[index] = selected;
  if(!sudahDihitung[index]){
    if(selected === questions[index][2]) score++;
    sudahDihitung[index] = true;
  }
  showExplanation();
};

document.getElementById("prevBtn").onclick = goPrev;
document.getElementById("prevBtn2").onclick = goPrev;
function goPrev(){
  if(index === 0) return;
  stopTimer();
  index--;
  showQuestion();
}

document.getElementById("nextBtn").onclick = ()=>{
  if(index === questions.length - 1){
    e.classList.add("hidden");
    r.classList.remove("hidden");
    document.getElementById("score").textContent = Skor kamu: ${score} / ${questions.length};
    return;
  }
  index++;
  showQuestion();
};

window.addEventListener("resize", ()=>{
  if(!e.classList.contains("hidden")) setupCanvas();
});

showQuestion();
</script>
</body>
</html>
