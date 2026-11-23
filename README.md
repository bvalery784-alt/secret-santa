<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Тайный Санта</title>
<style>
  body { font-family: Arial, sans-serif; background: #1d2333; color: #fff;
         display: flex; justify-content: center; padding: 20px; }
  .card { background: #2a3047; border-radius: 16px; padding: 24px;
          max-width: 480px; width: 100%; }
  h1 { margin-top: 0; font-size: 24px; }
  input, button { padding: 10px; border-radius: 8px; border: none;
                  font-size: 16px; box-sizing: border-box; }
  input { width: 70%; margin-right: 5px; }
  button { background: #e5484d; color: #fff; cursor: pointer; font-weight: bold; }
  button.secondary { background: #4a5568; width: 100%; margin-top: 12px; }
  ul { list-style: none; padding: 0; }
  li { display: flex; justify-content: space-between; align-items: center;
       background: #353c58; border-radius: 8px; padding: 8px 12px; margin: 6px 0; }
  .result { background: #fff; color: #1d2333; border-radius: 12px;
            padding: 16px; margin-top: 16px; display: none; }
  .error { color: #ff8a8a; font-size: 14px; }
</style>
</head>
<body>
<div class="card">
  <h1>🎄 Тайный Санта</h1>

  <div id="setup">
    <div>
      <input id="name" placeholder="Имя участника">
      <button onclick="addPlayer()">+</button>
    </div>
    <div id="players"></ul></div>
    <p class="error" id="error"></p>
    <button class="secondary" onclick="draw()">Провести жеребьёвку 🎅</button>
  </div>

  <div id="reveal" style="display:none">
    <p>Кто ты? Введи своё имя:</p>
    <div>
      <input id="who" placeholder="Твоё имя">
      <button onclick="showResult()">Узнать</button>
    </div>
    <div class="result" id="result"></div>
    <button class="secondary" onclick="location.reload()">Начать заново</button>
  </div>
</div>

<script>
let players = [];

function addPlayer() {
  const name = document.getElementById('name').value.trim();
  if (!name || players.includes(name)) return;
  players.push(name);
  document.getElementById('name').value = '';
  renderPlayers();
}

function removePlayer(name) {
  players = players.filter(p => p !== name);
  renderPlayers();
}

function renderPlayers() {
  document.getElementById('players').innerHTML =
    players.map(p => `<li>${p} <button onclick="removePlayer('${p}')">✕</button></li>`).join('');
}

function draw() {
  const err = document.getElementById('error');
  err.textContent = '';
  if (players.length < 3) { err.textContent = 'Нужно минимум 3 участника.'; return; }
  for (let attempt = 0; attempt < 1000; attempt++) {
    // Фишер–Йетс: случайная перестановка
    const targets = [...players];
    for (let i = targets.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [targets[i], targets[j]] = [targets[j], targets[i]];
    }
    // Проверяем, что никто не дарит сам себе
    if (players.every((p, i) => p !== targets[i])) {
      window.assignments = Object.fromEntries(players.map((p, i) => [p, targets[i]]));
      document.getElementById('setup').style.display = 'none';
      document.getElementById('reveal').style.display = 'block';
      return;
    }
  }
  err.textContent = 'Не удалось составить пару, попробуйте ещё раз.';
}

function showResult() {
  const who = document.getElementById('who').value.trim();
  const box = document.getElementById('result');
  if (window.assignments && window.assignments[who]) {
    box.textContent = `🎁 Ты даришь подарок: ${window.assignments[who]}!`;
    box.style.display = 'block';
  } else {
    box.textContent = 'Такого участника нет. Проверь имя!';
    box.style.display = 'block';
  }
}
</script>
</body>
</html>
