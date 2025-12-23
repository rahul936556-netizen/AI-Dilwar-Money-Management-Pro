<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>AI Dilwar – Money Management Pro</title>
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
<style>
html, body {
  height: 100%;
  margin: 0;
  padding: 0;
  font-family: Segoe UI, Arial;
  background: linear-gradient(135deg,#0f2027,#203a43,#2c5364);
  color: #fff;
}
.container {
  width: 100%;
  max-width: 100%;
  padding: 15px;
  box-sizing: border-box;
}
.card {
  width: 100%;
  max-width: 100%;
  background: rgba(255,255,255,0.08);
  backdrop-filter: blur(10px);
  border-radius: 14px;
  padding: 16px;
  margin-bottom: 15px;
  box-shadow: 0 10px 25px rgba(0,0,0,.35);
}
h2,h3{text-align:center}
input,select,button{
  width:100%;padding:10px;margin-top:6px;
  border-radius:8px;border:none;font-size:15px;
}
input,select{background:#1e2a38;color:#fff}
button{
  background:linear-gradient(90deg,#f7971e,#ffd200);
  color:#000;font-weight:600;
  cursor:pointer;
}
.hidden{display:none}
.small{text-align:center;font-size:13px;opacity:.85}
.lock{color:#ff5252;font-weight:bold;text-align:center}

/* Dark Table */
table {
  width: 100%;
  font-size: 14px;
  border-collapse: collapse;
  background-color: #1e2a38; /* solid dark background */
}
th {
  background-color: #0f3b57; /* darker header */
  color: #fff;
  padding: 6px;
  border-bottom: 1px solid #ffffff33;
}
td {
  background-color: #203a43; /* dark cell background */
  color: #fff;
  padding: 6px;
  border-bottom: 1px solid #ffffff33;
  text-align: center;
}
.win { color: #4caf50; font-weight: bold; }
.loss { color: #ff5252; font-weight: bold; }
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

<!-- ADMIN -->
<div class="card hidden" id="adminBox">
<h3>Admin Panel</h3>
<input id="newAdminPass" placeholder="New Admin Password">
<button onclick="changeAdminPass()">Change Password</button>
<button onclick="logout()">Logout</button>
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
<option value="win">Win</option>
<option value="loss">Loss</option>
</select>

<label>Payout %</label>
<input id="payoutInput" value="80">

<button id="submitBtn" onclick="submitTrade()">Submit Trade</button>
<button id="refreshBtn" class="hidden" onclick="resetSystem()">🔄 Refresh / New Day</button>
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
const adminBox = document.getElementById("adminBox");
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
const newAdminPass = document.getElementById("newAdminPass");
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
  } else if(uid.value==="admin" && upass.value==="admin123"){
    loginBox.classList.add("hidden");
    adminBox.classList.remove("hidden");
  } else {
    loginMsg.innerText="❌ Invalid User ID or Password";
  }
}

function changeAdminPass(){
  alert("Admin password changed to: "+newAdminPass.value);
  newAdminPass.value="";
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
      <td class="${h.r}">${h.r.toUpperCase()}</td>
      <td>${h.a.toFixed(2)}</td>
      <td>${h.c.toFixed(2)}</td>
      <td>${h.t}</td>
    </tr>`;
  });
}

function logout(){location.reload();}
</script>

</body>
</html>
