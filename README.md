<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>AI Dilwar – Money Management Pro</title>
<meta name="viewport" content="width=device-width, initial-scale=1">
<style>
body {
  margin:0;
  font-family:Segoe UI, Arial;
  background: #f0f2f5;
  color: #000;
}
.container {
  max-width:430px;
  margin:auto;
  padding:15px;
}
.card {
  background:#fff;
  border-radius:10px;
  padding:16px;
  margin-bottom:15px;
  box-shadow:0 4px 15px rgba(0,0,0,0.2);
}
h2,h3{text-align:center;margin:5px 0;}
input,select,button{
  width:100%;padding:10px;margin-top:6px;
  border-radius:6px;border:1px solid #ccc;font-size:15px;
}
button{
  background:#4caf50;color:#fff;font-weight:600;cursor:pointer;border:none;
}
button.refresh{
  background:#ff9800;
}
.small{text-align:center;font-size:13px;color:#555;}
.lock{color:#f44336;font-weight:bold;text-align:center;}
table{width:100%;border-collapse:collapse;margin-top:10px;}
th,td{padding:8px;text-align:center;border:1px solid #ccc;}
.win{color:#4caf50;font-weight:bold;}
.loss{color:#f44336;font-weight:bold;}
</style>
</head>
<body>

<div class="container">

<h2>AI Dilwar</h2>
<p class="small">Money Management Pro</p>

<!-- LOGIN -->
<div class="card" id="loginBox">
  <input id="uid" placeholder="User ID">
  <input id="upass" type="password" placeholder="Password">
  <button onclick="login()">Login</button>
  <p id="loginMsg" class="small"></p>
</div>

<!-- SYSTEM -->
<div class="card hidden" id="systemBox">

<label>Initial Capital</label>
<input id="capitalInput" type="number" value="0">

<label>Risk %</label>
<select id="riskSelect">
<option value="1" selected>1%</option>
<option value="2">2%</option>
<option value="3">3%</option>
<option value="4">4%</option>
<option value="5">5%</option>
</select>

<button onclick="startSystem()">Start System</button>

<p>Current Capital: <b id="currentCapital">0</b></p>
<p>Next Trade Amount: <b id="tradeAmount">0</b></p>
<p>Trades Used: <b id="tradeCount">0</b> / 6</p>
<p id="status"></p>

<label>Result</label>
<select id="tradeResult">
<option value="win">Profit</option>
<option value="loss">Loss</option>
</select>

<label>Payout %</label>
<input id="payoutInput" value="80">

<button id="submitBtn" onclick="submitTrade()">Submit Trade</button>
<button id="refreshBtn" class="refresh hidden" onclick="resetSystem()">🔄 Refresh / New Day</button>
</div>

<!-- HISTORY -->
<div class="card hidden" id="historyBox">
<h3>Trade History</h3>
<table>
<thead>
<tr>
<th>#</th>
<th>Result</th>
<th>Amount</th>
<th>Capital</th>
<th>Time</th>
</tr>
</thead>
<tbody id="history"></tbody>
</table>
</div>

</div>

<script>
// DOM REFERENCES
const uid = document.getElementById("uid");
const upass = document.getElementById("upass");
const loginBox = document.getElementById("loginBox");
const systemBox = document.getElementById("systemBox");
const historyBox = document.getElementById("historyBox");

const capitalInput = document.getElementById("capitalInput");
const riskSelect = document.getElementById("riskSelect");
const tradeResult = document.getElementById("tradeResult");
const payoutInput = document.getElementById("payoutInput");
const submitBtn = document.getElementById("submitBtn");
const refreshBtn = document.getElementById("refreshBtn");
const currentCapital = document.getElementById("currentCapital");
const tradeAmount = document.getElementById("tradeAmount");
const tradeCount = document.getElementById("tradeCount");
const status = document.getElementById("status");
const loginMsg = document.getElementById("loginMsg");
const history = document.getElementById("history");

// FIXED LOGIN
const FIXED_UID = "DilwarAi";
const FIXED_PASS = "848654";

let capital=0,current=0,lossCarry=0,trades=0;
const MAX=6;
let historyData=[];

function login(){
  if(uid.value===FIXED_UID && upass.value===FIXED_PASS){
    loginBox.classList.add("hidden");
    systemBox.classList.remove("hidden");
    historyBox.classList.remove("hidden");
  } else {
    loginMsg.innerText="❌ Invalid User ID or Password";
  }
}

function startSystem(){
  capital=parseFloat(capitalInput.value);
  current=capital;
  lossCarry=0;
  trades=0;
  historyData=[];
  submitBtn.disabled=false;
  refreshBtn.classList.add("hidden");
  updateTrade();
  render();
  status.innerHTML="";
}

function updateTrade(){
  let base=current*riskSelect.value/100;
  if(lossCarry>0){
    base=(lossCarry+base)/(payoutInput.value/100);
  }
  tradeAmount.innerText=base.toFixed(2);
}

function submitTrade(){
  if(trades>=MAX){
    lockSystem();
    return;
  }
  let amt=parseFloat(tradeAmount.innerText);
  trades++;
  let now = new Date();
  let timeStr = now.getHours().toString().padStart(2,'0') + ":" +
                now.getMinutes().toString().padStart(2,'0') + ":" +
                now.getSeconds().toString().padStart(2,'0');

  if(tradeResult.value==="win"){
    current+=amt*(payoutInput.value/100);
    lossCarry=0;
  } else {
    current-=amt;
    lossCarry+=amt;
  }

  historyData.push({
    n:trades,
    r:tradeResult.value,
    a:amt,
    c:current,
    t:timeStr
  });
  updateTrade();
  render();
  if(trades>=MAX){
    lockSystem();
  }
}

function lockSystem(){
  status.innerHTML="<span class='lock'>🔒 6 Trades Completed – System Locked</span>";
  submitBtn.disabled=true;
  refreshBtn.classList.remove("hidden");
}

function resetSystem(){
  capitalInput.value=0;
  currentCapital.innerText="0";
  tradeAmount.innerText="0";
  tradeCount.innerText="0";
  history.innerHTML="";
  status.innerHTML="";
  historyData=[];
  refreshBtn.classList.add("hidden");
  submitBtn.disabled=false;
}

function render(){
  currentCapital.innerText=current.toFixed(2);
  tradeCount.innerText=trades;
  history.innerHTML="";
  historyData.forEach(h=>{
    history.innerHTML+=`<tr>
      <td>${h.n}</td>
      <td class="${h.r}">${h.r === "win" ? "Profit" : "Loss"}</td>
      <td>${h.a.toFixed(2)}</td>
      <td>${h.c.toFixed(2)}</td>
      <td>${h.t}</td>
    </tr>`;
  });
}
</script>

</body>
</html>
