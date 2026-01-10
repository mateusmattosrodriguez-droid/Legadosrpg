<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<title>Oráculo das Moiras – Legados RPG</title>

<style>
body {
  margin: 0;
  background: radial-gradient(circle, #0b0b14, #000);
  color: #e6e0d4;
  font-family: Georgia, serif;
  text-align: center;
  padding: 20px;
}

h1 { font-size: 2.2em; margin-bottom: 5px; }
.subtitle { font-style: italic; opacity: .8; }

.panel {
  background: rgba(0,0,0,.65);
  border: 1px solid rgba(255,255,255,.2);
  border-radius: 14px;
  padding: 15px;
  margin: 15px auto;
  max-width: 600px;
}

input {
  background: #111;
  color: #e6e0d4;
  border: 1px solid #555;
  padding: 8px;
  border-radius: 6px;
  margin: 5px;
  width: 150px;
  text-align: center;
}

button {
  background: #8a6f1e;
  color: #000;
  border: none;
  padding: 10px 18px;
  border-radius: 10px;
  cursor: pointer;
  font-weight: bold;
}

.dice {
  width: 120px;
  height: 120px;
  margin: 25px auto;
  background: linear-gradient(145deg, #d4af37, #8a6f1e);
  color: #000;
  font-size: 3em;
  font-weight: bold;
  line-height: 120px;
  border-radius: 20px;
  cursor: pointer;
  box-shadow: 0 15px 30px rgba(0,0,0,.7);
  transition: transform 1s ease;
}

.dice.rolling {
  transform: rotateX(720deg) rotateY(720deg) translateY(-40px);
}

.good { color: #7CFC98; }
.neutral { color: #f5deb3; }
.bad { color: #ff6b6b; }

.history {
  text-align: left;
  max-height: 220px;
  overflow-y: auto;
  font-size: .9em;
}
</style>
</head>

<body>

<h1>🎲 Oráculo das Senhoras do Destino</h1>
<div class="subtitle">Cloto fia • Láquesis mede • Átropos corta</div>

<div class="panel" id="loginPanel">
  <h3>Entrar no Destino</h3>
  <input id="playerName" placeholder="Nome do Personagem">
  <br>
  <button onclick="enterGame()">Entrar</button>
</div>

<div class="panel" id="gamePanel" style="display:none">
  <h3 id="welcome"></h3>

  Modificador do Destino:<br>
  <input id="modifier" type="number" value="0"><br><br>

  <div id="dice" class="dice" onclick="rollDice()">?</div>

  <div id="result" class="panel">
    Clique no dado para desafiar as Moiras.
  </div>

  <div class="panel">
    <strong>📜 Histórico do Jogador</strong>
    <div id="history" class="history"></div>
    <button onclick="clearHistory()">Apagar Histórico</button>
  </div>
</div>

<script>
const outcomes = {
1:{t:"Átropos corta o fio. Perda severa.",c:"bad",m:"Átropos"},
2:{t:"O destino se fecha em dor.",c:"bad",m:"Átropos"},
3:{t:"Complicações inevitáveis surgem.",c:"bad",m:"Átropos"},
4:{t:"O fio enfraquece perigosamente.",c:"bad",m:"Láquesis"},
5:{t:"Sacrifício exigido pelo destino.",c:"bad",m:"Láquesis"},
6:{t:"O destino observa em silêncio.",c:"neutral",m:"Moiras"},
7:{t:"Um presságio sutil se revela.",c:"neutral",m:"Cloto"},
8:{t:"O fio permanece estável.",c:"neutral",m:"Láquesis"},
9:{t:"Uma escolha difícil se apresenta.",c:"neutral",m:"Moiras"},
10:{t:"Equilíbrio absoluto.",c:"neutral",m:"Moiras"},
11:{t:"Vislumbre do futuro.",c:"neutral",m:"Láquesis"},
12:{t:"Uma oportunidade velada surge.",c:"neutral",m:"Cloto"},
13:{t:"Cloto fortalece o fio.",c:"good",m:"Cloto"},
14:{t:"O destino se inclina a favor.",c:"good",m:"Láquesis"},
15:{t:"Sucesso com benefício extra.",c:"good",m:"Cloto"},
16:{t:"Reviravolta positiva ocorre.",c:"good",m:"Moiras"},
17:{t:"Grande vantagem concedida.",c:"good",m:"Láquesis"},
18:{t:"O fio brilha intensamente.",c:"good",m:"Cloto"},
19:{t:"Sucesso absoluto.",c:"good",m:"Moiras"},
20:{t:"O destino é reescrito. Milagre.",c:"good",m:"Todas"}
};

let player = localStorage.getItem("player");

function enterGame(){
  const name = document.getElementById("playerName").value;
  if(!name) return;
  localStorage.setItem("player", name);
  player = name;
  init();
}

function init(){
  if(player){
    document.getElementById("loginPanel").style.display="none";
    document.getElementById("gamePanel").style.display="block";
    document.getElementById("welcome").innerText = "Bem-vindo, " + player;
    loadHistory();
  }
}

function rollDice(){
  const dice = document.getElementById("dice");
  const result = document.getElementById("result");
  dice.classList.add("rolling");
  dice.innerText="?";
  result.innerHTML="As Moiras estão tecendo...";

  setTimeout(()=>{
    const roll = Math.floor(Math.random()*20)+1;
    const mod = parseInt(document.getElementById("modifier").value)||0;
    const total = roll+mod;
    const o = outcomes[roll];
    dice.classList.remove("rolling");
    dice.innerText=roll;

    const entry = `<div class="${o.c}">
    <strong>${player}</strong> rolou ${roll} + ${mod} = ${total}<br>
    ${o.t}<br><em>${o.m}</em></div><hr>`;

    result.innerHTML=entry;
    saveHistory(entry);
    loadHistory();
  },1000);
}

function saveHistory(e){
  let h = JSON.parse(localStorage.getItem("history_"+player))||[];
  h.unshift(e);
  localStorage.setItem("history_"+player,JSON.stringify(h.slice(0,20)));
}

function loadHistory(){
  const box=document.getElementById("history");
  let h=JSON.parse(localStorage.getItem("history_"+player))||[];
  box.innerHTML=h.join("");
}

function clearHistory(){
  localStorage.removeItem("history_"+player);
  loadHistory();
}

init();
</script>

</body>
</html>
