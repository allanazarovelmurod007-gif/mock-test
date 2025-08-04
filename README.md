<!DOCTYPE html>
<html lang="uz">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Mock Test MSTEST2</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: linear-gradient(to right, #00b894, #0984e3);
      color: #fff;
      padding: 20px;
    }
    .container {
      max-width: 900px;
      margin: 0 auto;
      background: #fff;
      color: #000;
      border-radius: 10px;
      padding: 20px;
    }
    .question {
      margin-bottom: 15px;
    }
    .options button, .options textarea {
      display: block;
      margin: 5px 0;
      padding: 10px;
      width: 100%;
      font-size: 16px;
      border-radius: 8px;
      border: 1px solid #ccc;
      background-color: #fff;
      color: #000;
    }
    .options button.selected {
      background-color: #9b59b6;
      color: #fff;
    }
    .admin-panel {
      position: fixed;
      top: 10px;
      right: 10px;
      background: #2d3436;
      padding: 10px;
      border-radius: 10px;
      color: #fff;
      z-index: 1000;
    }
    #idPrompt {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0,0,0,0.85);
      color: white;
      display: flex;
      align-items: center;
      justify-content: center;
      flex-direction: column;
      z-index: 2000;
    }
    #idPrompt input, #idPrompt button {
      font-size: 18px;
      padding: 10px;
      border-radius: 8px;
      border: none;
      margin-top: 10px;
    }
    #idPrompt button {
      background: #00b894;
      color: white;
    }
  </style>
</head>
<body>
<div id="idPrompt">
  <h2>Test ID ni kiriting:</h2>
  <input type="text" id="testIdInput" placeholder="Masalan: mstest2" />
  <button onclick="checkID()">Kirish</button>
</div>

<div class="admin-panel">
  <button onclick="toggleAdmin()">Admin Panel</button>
  <div id="adminArea" style="display:none">
    <input type="password" id="adminPass" placeholder="Parol" />
    <button onclick="adminLogin()">Kirish</button>
    <div id="adminContent" style="display:none">
      <textarea id="answerKey" placeholder="Masalan: A,B,D,C,... (45 ta)" rows="5" style="width:100%"></textarea>
      <button onclick="stopTest()">Testni to‘xtatish</button>
    </div>
  </div>
</div>

<div class="container" id="testContainer" style="display:none">
  <h1>Elmurod Allanazarov Mock Test</h1>
  <form id="mockTestForm" onsubmit="return showResult(event)">
    <div id="questions"></div>
    <button type="submit">Natijani ko‘rish</button>
  </form>
  <div id="resultBox" style="margin-top: 20px; font-size: 18px;"></div>
</div>

<script>
  const testID = "mstest2";
  let selectedAnswers = {};
  let answerKey = [];

  function checkID() {
    const input = document.getElementById("testIdInput").value;
    if (input === testID) {
      document.getElementById("idPrompt").style.display = "none";
      document.getElementById("testContainer").style.display = "block";
      generateQuestions();
    } else {
      alert("ID noto‘g‘ri");
    }
  }

  function toggleAdmin() {
    const panel = document.getElementById("adminArea");
    panel.style.display = panel.style.display === "none" ? "block" : "none";
  }

  function adminLogin() {
    const pass = document.getElementById("adminPass").value;
    if (pass === "admin2025") {
      document.getElementById("adminContent").style.display = "block";
    } else {
      alert("Noto‘g‘ri parol");
    }
  }

  function stopTest() {
    alert("Test to‘xtatildi!");
    document.getElementById("testContainer").innerHTML = "<h2>Test tugatildi.</h2>";
  }

  function generateQuestions() {
    const qDiv = document.getElementById("questions");
    for (let i = 1; i <= 45; i++) {
      const q = document.createElement("div");
      q.className = "question";
      q.innerHTML = `<strong>${i}-savol</strong>`;
      const opts = document.createElement("div");
      opts.className = "options";

      if (i <= 32) {
        ["A","B","C","D"].forEach(opt => {
          const btn = document.createElement("button");
          btn.type = "button";
          btn.textContent = opt;
          btn.onclick = () => selectAnswer(i, opt, btn);
          opts.appendChild(btn);
        });
      } else if (i <= 35) {
        ["A","B","C","D","E","F"].forEach(opt => {
          const btn = document.createElement("button");
          btn.type = "button";
          btn.textContent = opt;
          btn.onclick = () => selectAnswer(i, opt, btn);
          opts.appendChild(btn);
        });
      } else {
        const a = document.createElement("input");
        a.type = "text";
        a.placeholder = `${i}-a)`;
        a.style.marginBottom = "5px";
        a.oninput = () => selectedAnswers[`${i}a`] = a.value.trim();

        const b = document.createElement("input");
        b.type = "text";
        b.placeholder = `${i}-b)`;
        b.oninput = () => selectedAnswers[`${i}b`] = b.value.trim();

        opts.appendChild(a);
        opts.appendChild(b);
      }
      q.appendChild(opts);
      qDiv.appendChild(q);
    }
  }

  function selectAnswer(qNum, answer, btn) {
    selectedAnswers[qNum] = answer;
    const allButtons = btn.parentElement.querySelectorAll("button");
    allButtons.forEach(b => b.classList.remove("selected"));
    btn.classList.add("selected");
  }

  function showResult(event) {
    event.preventDefault();
    const adminKey = document.getElementById("answerKey").value.trim().split(',');
    if (adminKey.length < 45) {
      alert("Admin 45 ta to‘g‘ri javobni kiritsin.");
      return false;
    }
    let correct = 0;
    for (let i = 1; i <= 45; i++) {
      const correctAns = adminKey[i-1].trim().toUpperCase();
      if (i >= 36) {
        const a = selectedAnswers[`${i}a`] || "";
        const b = selectedAnswers[`${i}b`] || "";
        if (a.toUpperCase() === correctAns.split(" ")[0] && b.toUpperCase() === correctAns.split(" ")[1]) {
          correct++;
        }
      } else {
        if ((selectedAnswers[i] || "").toUpperCase() === correctAns) {
          correct++;
        }
      }
    }
    document.getElementById("resultBox").innerHTML = `<strong>Sizning natijangiz:</strong> ${correct} / 45`;
    return false;
  }
</script>
</body>
</html>
