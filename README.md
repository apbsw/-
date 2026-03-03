<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<title>اختبار إلكتروني</title>
<style>
body {
  margin: 0;
  font-family: Arial;
  background: #111;
  color: white;
  display: flex;
  height: 100vh;
  overflow: hidden;
}

/* Sidebar */
#sidebar {
  width: 30%;
  background: #1a1a1a;
  border-left: 3px solid #333;
  padding: 20px;
  overflow-y: auto;
}

#sidebar h3 {
  margin-top: 0;
  font-size: 32px;
  color: #fff;
}

.status-box {
  display: inline-flex;
  justify-content: center;
  align-items: center;
  width: 42px;
  height: 42px;
  margin: 7px;
  border-radius: 8px;
  background: #444;
  color: white;
  font-size: 20px;
  font-weight: bold;
  cursor: pointer;
}

.correct { background: #4CD964 !important; }
.wrong { background: #FF3B30 !important; }
.unanswered { background: #444 !important; }

/* Main content */
#main {
  width: 70%;
  padding: 30px;
  overflow-y: auto;
}

h2 {
  font-size: 36px;
  margin-bottom: 25px;
  color: white;
}

.option-btn {
  width: 100%;
  padding: 25px;
  margin: 18px 0;
  background: #222;
  border-radius: 18px;
  font-size: 28px;
  text-align: right;
  border: 3px solid #333;
  transition: 0.2s;
  display: flex;
  align-items: center;
  gap: 20px;
  color: white;
  cursor: pointer;
}

.option-number {
  background: #007AFF;
  color: white;
  padding: 10px 20px;
  border-radius: 12px;
  font-size: 24px;
}

.correct-opt {
  background: #4CD964 !important;
  color: white !important;
}

.wrong-opt {
  background: #FF3B30 !important;
  color: white !important;
}

/* Buttons */
#navButtons {
  margin-top: 25px;
  display: flex;
  justify-content: space-between;
}

.nav-btn {
  width: 48%;
  padding: 22px 0;
  font-size: 28px;
  border-radius: 16px;
  border: none;
  background: #007AFF;
  color: white;
  cursor: pointer;
}

/* Result circle */
#resultCircle {
  width: 300px;
  height: 300px;
  margin: 40px auto;
  display: block;
}

.result-buttons {
  display: flex;
  justify-content: space-between;
  margin-top: 20px;
}

.result-btn {
  width: 48%;
  padding: 20px 0;
  font-size: 26px;
  border-radius: 16px;
  border: none;
  background: #007AFF;
  color: white;
  cursor: pointer;
}
</style>
</head>

<body>

<div id="sidebar">
  <h3>الأسئلة</h3>
  <div id="statusList"></div>
</div>

<div id="main">
  <div id="content">
    <h1 style="font-size:42px; color:white;">اختبار إلكتروني</h1>
    <p style="font-size:30px;">اضغط ابدأ لبدء الاختبار</p>
    <button class="nav-btn" onclick="startQuiz()">ابدأ</button>
  </div>
</div>

<script>
let questions = [
  {q:"1- تدور كل الكواكب حول نفسها بعكس عقارب الساعة ما عدا كوكب:", c:["عطارد","الأرض","المريخ","الزهرة"], a:"D"},
  {q:"2- يقع حزام الكويكبات بين كوكبي المريخ والأرض:", c:["صواب","خطأ"], a:"B"},
  {q:"3- الصورة التي أمامك توضح مسار:", c:["الكويكبات","المذنبات","الشهب","الكواكب"], a:"B"},
  {q:"4- يعتقد أن المذنبات ذات المدارات طويلة الدورة تتشكل في:", c:["حزام كويبر","حزام الكويكبات","أطراف مجرة درب التبانة","سحابة أورت"], a:"D"},
  {q:"5- يرغب نواف في الحصول على صور فلكية أكثر دقة، لذا عليه شراء منظار قطر عدسته العينية كبير:", c:["صواب","خطأ"], a:"B"},
  {q:"6- تعتمد درجة تكبير التلسكوب على:", c:["حجم أنبوب التلسكوب","البعد البؤري للعدسة العينية","تكوّر المرآة","صفاء الغلاف الجوي"], a:"B"},
  {q:"7- نوع المنظار المستخدم في منظار هابل الفضائي هو:", c:["عاكس","كاسر","راديوي","أشعة سينية"], a:"A"},
  {q:"8- أول من استخدم المنظار العاكس هو العالم أرخميدس:", c:["صواب","خطأ"], a:"B"},
  {q:"9- يصحح الزيغ الكروي بالمنظار الكاسر باستخدام سطح مقعر للعدسات:", c:["صواب","خطأ"], a:"A"},
  {q:"10- تعد طبقة الإشعاع موقع عمليات الاندماج النووي داخل الشمس:", c:["صواب","خطأ"], a:"B"},
  {q:"11- سمك طبقة اللب من مركز الشمس يقدر بـ:", c:["1","0.3","2.5","4"], a:"B"},
  {q:"12- الطبقة الشمسية التي نراها دائماً هي الطبقة الملونة:", c:["صواب","خطأ"], a:"B"},
  {q:"13- يسمى العلم الذي يدرس الكائنات الحية التي عاشت في الماضي:", c:["علم الأحافير","التأريخ النسبي","العمر النسبي","علم الآثار"], a:"A"},
  {q:"14- من النادر وجود أحافير للكائنات التي ليس لها هياكل صلبة:", c:["صواب","خطأ"], a:"A"}
];

let index = 0;
let score = 0;
let locked = false;
let wrongList = [];
let chosenAnswers = {};
let status = Array(questions.length).fill("unanswered");

let lastPercent = 0;
let lastColor = "#4CD964";

function updateSidebar() {
  let html = "";
  status.forEach((s,i)=>{
    html += `<div class="status-box ${s}" onclick="jump(${i})">${i+1}</div>`;
  });
  document.getElementById("statusList").innerHTML = html;
}

function startQuiz() {
  index = 0;
  score = 0;
  locked = false;
  wrongList = [];
  chosenAnswers = {};
  status = Array(questions.length).fill("unanswered");
  updateSidebar();
  load();
}

function load() {
  locked = false;

  let q = questions[index];
  let html = `<h2>${q.q}</h2>`;

  q.c.forEach((c,i)=>{
    let letter = String.fromCharCode(65+i);
    let num = i + 1;
    html += `
      <div class="option-btn" id="opt${letter}" onclick="choose('${letter}')">
        <div class="option-number">${num}</div>
        <div>${c}</div>
      </div>`;
  });

  html += `
    <div id="navButtons">
      <button class="nav-btn" onclick="prev()">السابق</button>
      <button class="nav-btn" onclick="next()">التالي</button>
    </div>`;

  document.getElementById("content").innerHTML = html;
}

function choose(letter) {
  if (locked) return;

  locked = true;
  chosenAnswers[index] = letter;

  let correct = questions[index].a;

  if (letter === correct) {
    document.getElementById("opt" + letter).classList.add("correct-opt");
    status[index] = "correct";
    score++;
  } else {
    document.getElementById("opt" + letter).classList.add("wrong-opt");
    document.getElementById("opt" + correct).classList.add("correct-opt");
    status[index] = "wrong";
    if (!wrongList.includes(index)) wrongList.push(index);
  }

  updateSidebar();
}

function next() {
  if (index < questions.length-1) {
    index++;
    load();
  } else {
    endQuiz();
  }
}

function prev() {
  if (index > 0) {
    index--;
    load();
  }
}

function jump(i) {
  index = i;
  load();
}

function endQuiz() {
  let percent = Math.round((score / questions.length) * 100);
  let color = percent >= 80 ? "#4CD964" : percent >= 50 ? "#FFA500" : "#FF3B30";

  lastPercent = percent;
  lastColor = color;

  renderResult();
}

function renderResult() {
  let msg = `
    <canvas id="resultCircle" width="300" height="300"></canvas>
    <div class="result-buttons">
      <button class="result-btn" onclick="review()">مراجعة الأسئلة الخاطئة</button>
      <button class="result-btn" onclick="startQuiz()">إعادة الاختبار</button>
    </div>`;

  document.getElementById("content").innerHTML = msg;

  animateCircle(lastPercent, lastColor);
}

function animateCircle(percent, color) {
  let canvas = document.getElementById("resultCircle");
  let ctx = canvas.getContext("2d");

  let current = 0;

  function draw() {
    ctx.clearRect(0,0,300,300);

    ctx.beginPath();
    ctx.arc(150,150,120,0,Math.PI*2);
    ctx.strokeStyle = "#333";
    ctx.lineWidth = 20;
    ctx.stroke();

    ctx.beginPath();
    ctx.arc(150,150,120,-Math.PI/2, (2 * Math.PI * (current / 100)) - Math.PI/2);
    ctx.strokeStyle = color;
    ctx.lineWidth = 20;
    ctx.stroke();

    ctx.fillStyle = "white";
    ctx.font = "bold 48px Arial";
    ctx.textAlign = "center";
    ctx.textBaseline = "middle";
    ctx.fillText(current + "%",150,150);

    if (current < percent) {
      current++;
      requestAnimationFrame(draw);
    }
  }

  draw();
}

function review() {
  if (wrongList.length === 0) {
    document.getElementById("content").innerHTML = `
      <h2>لا توجد أسئلة خاطئة</h2>
      <div class="result-buttons">
        <button class="result-btn" onclick="renderResult()">رجوع</button>
        <button class="result-btn" onclick="startQuiz()">إعادة الاختبار</button>
      </div>`;
    return;
  }

  index = 0;
  showWrong();
}

function showWrong() {
  let qIndex = wrongList[index];
  let q = questions[qIndex];

  let html = `<h2>${q.q}</h2>`;

  q.c.forEach((c,i)=>{
    let letter = String.fromCharCode(65+i);
    let num = i + 1;

    let cls = "";

    if (letter === q.a) cls = "correct-opt";
    if (chosenAnswers[qIndex] === letter && letter !== q.a) cls = "wrong-opt";

    html += `
      <div class="option-btn ${cls}">
        <div class="option-number">${num}</div>
        <div>${c}</div>
      </div>`;
  });

  html += `
    <div id="navButtons">
      <button class="nav-btn" onclick="prevWrong()">السابق</button>
      <button class="nav-btn" onclick="nextWrong()">التالي</button>
    </div>
    <div class="result-buttons">
      <button class="result-btn" onclick="renderResult()">رجوع للنتيجة</button>
      <button class="result-btn" onclick="startQuiz()">إعادة الاختبار</button>
    </div>`;

  document.getElementById("content").innerHTML = html;
}

function nextWrong() {
  if (index < wrongList.length-1) {
    index++;
    showWrong();
  }
}

function prevWrong() {
  if (index > 0) {
    index--;
    showWrong();
  }
}
</script>

</body>
</html>
