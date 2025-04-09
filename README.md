<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>DFL 코인 채굴</title>
  <style>
    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background-color: #f0f4f8;
      color: #333;
      margin: 0;
      padding: 40px 20px;
    }

    h1 {
      text-align: center;
      color: #2c3e50;
      margin-bottom: 30px;
    }

    .card {
      background: white;
      max-width: 500px;
      margin: 20px auto;
      padding: 30px;
      border-radius: 20px;
      box-shadow: 0 8px 20px rgba(0,0,0,0.1);
    }

    label {
      font-weight: 600;
      display: block;
      margin-top: 15px;
      margin-bottom: 5px;
    }

    input[type="text"],
    input[type="number"] {
      width: 100%;
      padding: 10px 15px;
      border-radius: 10px;
      border: 1px solid #ccc;
      font-size: 16px;
      margin-bottom: 10px;
      box-sizing: border-box;
      transition: 0.2s;
    }

    input:focus {
      outline: none;
      border-color: #2980b9;
      box-shadow: 0 0 0 2px rgba(41, 128, 185, 0.2);
    }

    button {
      display: inline-block;
      width: 100%;
      padding: 12px;
      margin-top: 15px;
      background-color: #3498db;
      color: white;
      font-weight: bold;
      font-size: 16px;
      border: none;
      border-radius: 10px;
      cursor: pointer;
      transition: background-color 0.3s ease;
    }

    button:hover {
      background-color: #2980b9;
    }

    .section-title {
      font-size: 18px;
      font-weight: bold;
      margin-top: 30px;
      margin-bottom: 10px;
      color: #34495e;
      border-bottom: 2px solid #ecf0f1;
      padding-bottom: 5px;
    }

    .center-text {
      text-align: center;
      font-size: 16px;
      margin: 10px 0;
    }

    .coin-container {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      margin-top: 10px;
    }

    .coin {
      width: 20px;
      height: 20px;
      background: gold;
      border-radius: 50%;
      margin: 4px;
      box-shadow: 0 0 4px rgba(0,0,0,0.3);
    }

    @media (max-width: 600px) {
      .card {
        padding: 20px;
      }

      input, button {
        font-size: 14px;
      }
    }
  </style>
</head>

<body>

  <h1>DFL 코인 채굴</h1>

  <div class="card">
    <label>학번</label>
    <input id="studentId" type="text" onblur="getCoinFromSheet()">
    <label>비밀번호(별명)</label>
    <input id="name" type="text" onblur="getCoinFromSheet()">

    <div class="section-title">퀴즈 문제</div>
    <p class="center-text">문제: <span id="question"></span></p>
    <input type="number" id="answer">
    <button onclick="checkAnswer()">제출</button>

    <div class="section-title">현재 코인: <span id="coin">0</span>개</div>
    <div class="coin-container" id="coinVisual"></div>

    <button onclick="saveCoin()">코인 모두 저장하기</button>

    <div class="section-title">저장 코인: <span id="sentCoin">0</span>개</div>
  </div>

  <div class="card">
    <h3 class="center-text" style="margin-top: 0;">다른 학생에게 코인 보내기</h3>
    <label>받을 학번</label>
    <input id="receiverId" type="text">
    <label>받는 사람 기록(보낸 이유)</label>
    <input id="receiverName" type="text">
    <label>보낼 DFL 코인 수</label>
    <input id="sendAmount" type="number">
    <button onclick="sendToUser()">코인 보내기</button>
  </div>

  <script>
    let num1, num2, coin = 0, savedCoin = 0;

    function generateQuestion() {
      num1 = Math.floor(Math.random() * 9) + 1;
      num2 = Math.floor(Math.random() * 9) + 1;
      document.getElementById("question").textContent = num1 + " x " + num2;
    }

    function checkAnswer() {
      const userAnswer = parseInt(document.getElementById("answer").value);
      if (userAnswer === num1 * num2) {
        alert("정답입니다! 코인을 획득했습니다.");
        coin++;
        document.getElementById("coin").innerText = coin;
        updateCoinVisual();
      } else {
        alert("틀렸습니다. 다시 시도하세요.");
      }
      document.getElementById("answer").value = "";
      generateQuestion();
    }

    function updateCoinVisual() {
      const visual = document.getElementById("coinVisual");
      visual.innerHTML = "";
      for (let i = 0; i < coin; i++) {
        const c = document.createElement("div");
        c.className = "coin";
        visual.appendChild(c);
      }
    }

    function saveCoin() {
      const id = document.getElementById("studentId").value.trim();
      const name = document.getElementById("name").value.trim();
      if (!id || !name) {
        alert("학번과 비밀번호(별명)를 입력해주세요.");
        return;
      }

      const sentAmount = coin;
      const formUrl = "https://docs.google.com/forms/d/e/1FAIpQLSeAU98q_CdUUophR_eoceH38RuutBeWMuoFAkW9IC_jXN9VKg/formResponse";
      const params = new URLSearchParams({
        "entry.366340186": id,
        "entry.976901666": name,
        "entry.1556238593": "",
        "entry.2117446867": "",
        "entry.8599191": sentAmount
      });

      const fullUrl = formUrl + "?" + params.toString();
      window.open(fullUrl, "_blank");

      savedCoin += sentAmount;
      coin = 0;
      document.getElementById("coin").innerText = coin;
      document.getElementById("sentCoin").innerText = savedCoin;
      updateCoinVisual();
    }

    function sendToUser() {
      const senderId = document.getElementById("studentId").value.trim();
      const senderName = document.getElementById("name").value.trim();
      const receiverId = document.getElementById("receiverId").value.trim();
      const receiverName = document.getElementById("receiverName").value.trim();
      const amount = parseInt(document.getElementById("sendAmount").value);

      if (!senderId || !senderName || !receiverId || !receiverName || isNaN(amount)) {
        alert("모든 정보를 정확히 입력해주세요.");
        return;
      }

      if (amount > savedCoin) {
        alert("저장된 코인이 부족합니다. 먼저 코인을 저장하세요.");
        return;
      }

      const formUrl = "https://docs.google.com/forms/d/e/1FAIpQLSeAU98q_CdUUophR_eoceH38RuutBeWMuoFAkW9IC_jXN9VKg/formResponse";
      const params = new URLSearchParams({
        "entry.366340186": senderId,
        "entry.976901666": senderName,
        "entry.1556238593": receiverId,
        "entry.2117446867": receiverName,
        "entry.8599191": amount
      });

      const fullUrl = formUrl + "?" + params.toString();
      window.open(fullUrl, "_blank");

      savedCoin -= amount;
      document.getElementById("sentCoin").innerText = savedCoin;
      alert(receiverName + "에게 " + amount + " 코인을 보냈습니다.");
    }

    async function getCoinFromSheet() {
      const id = document.getElementById("studentId").value.trim();
      const name = document.getElementById("name").value.trim();
      if (!id || !name) return;

      const url = "https://opensheet.vercel.app/1KbhX-18UDYAFROkUSfH4H5f-N9_K2NJqfdt5DL_Ymg8/학생별 저장 코인";
      try {
        const res = await fetch(url);
        const data = await res.json();
        const row = data.find(r => r["학번"] === id && r["이름"] === name);
        if (row) {
          savedCoin = parseInt(row["현재 보유 코인"]) || 0;
          document.getElementById("sentCoin").innerText = savedCoin;
        }
      } catch (error) {
        console.error("코인 정보를 불러오는 중 오류 발생:", error);
      }
    }

    generateQuestion();
  </script>
</body>
</html>
