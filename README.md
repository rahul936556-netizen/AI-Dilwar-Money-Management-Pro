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
.container { padding:15px; }
.card {
  background: rgba(255,255,255,0.08);
  backdrop-filter: blur(10px);
  border-radius: 14px;
  padding: 16px;
  margin-bottom: 15px;
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
}
.hidden{display:none}
.lock{color:#ff5252;text-align:center;font-weight:bold}
table{width:100%;border-collapse:collapse;background:#1e2a38}
th,td{padding:6px;border-bottom:1px solid #ffffff33;text-align:center}
th{background:#0f3b57}
.win{color:#4caf50;font-weight:bold}
.loss{color:#ff5252;font-weight:bold}
</style>
</head>

<body>
<div class="container">

<h2>AI Dilwar</h2>
<p style="text-align:center;font-size:13px">Money Management Pro</p>

<!-- LOGIN -->
<div class="card" id="loginBox">
  <input id="uid" placeholder="User ID">
  <input id="upass" type="password" placeholder="Password">
  <button onclick="login()">Login</button>
  <p id="loginMsg"></p>
</div>

<!-- ADMIN PANEL -->
<div class="card hidden" id="adminBox">
<h3>Admin Panel</h3>

<label>Change Admin Password</label>
<input id="newAdminPass" type="password" placeholder="New Admin Password">
<button onclick="changeAdminPass()">Update Admin Password</button>

<hr>

<label>Change User Password</label>
<input id="newUserPass" type="password" placeholder="New User Password">
<button onclick="changeUserPass()">Update User Password</button>

<button onclick="logout()">Logout</button>
</div>

<!-- SYSTEM -->
<div class="card hidden" id="systemBox">

<label>Initial Capital</label>
<input id="capitalInput" type="number" value="0">

<label>Risk %</label>
<select id="riskSelect">
<option value="1">1%</option>
<option value="2">2%</option>
<option value="3">3%</option>
<option value="4">4%</option>
<option value="5">5%</option>
</select>

<button onclick="startSystem()">Start</button>

<p>Current Capital: <b id="currentCapital">0</b></p>
<p>Next Trade Amount: <b id="tradeAmount">0</b></p>
<p>Trades: <b id="tradeCount">0</b> / 6</p>

<label>Result</label>
<select id="tradeResult">
<option value="win">Win</option>
<option value="loss">Loss</option>
</select>

<label>Payout %</label>
<input id="payoutInput" value="80">

<button onclick="submitTrade()">Submit Trade</button>
<p id="status"></p>
</div>

<!-- HISTORY -->
<div class="card hidden" id="historyBox">
<h3>History</h3>
<table>
<thead>
<tr><th>#</th><th>Result</th><th>Amount</th><th>Capital</th></tr>
</thead>
<tbody id="history"></tbody>
</table>
</div>

</div>

<script>
// ===== PASSWORD STORAGE =====
let USER_ID = "DilwarAi";
let userPass = localStorage.getItem("USER_PASS") || "848654";
let adminPass = localStorage.getItem("ADMIN_PASS") || "admin123";

// ===== LOGIN =====
function login(){
  if(uid.value === USER_ID && upass.value === userPass){
    loginBox.classList.add("hidden");
    systemBox.classList.remove("hidden");
    historyBox.classList.remove("hidden");
  }
  else if(uid.value === "admin" && upass.value === adminPass){
    loginBox.classList.add("hidden");
    adminBox.classList.remove("hidden");
  }
  else{
    loginMsg.innerText="❌ Invalid ID or Password";
  }
}

// ===== ADMIN FUNCTIONS =====
function changeAdminPass(){
  if(newAdminPass.value.length < 4){
    alert("Minimum 4 characters");
    return;
  }
  adminPass = newAdminPass.value;
  localStorage.setItem("ADMIN_PASS", adminPass);
  alert("✅ Admin password updated");
  newAdminPass.value="";
}

function changeUserPass(){
  if(newUserPass.value.length < 4){
    alert("Minimum 4 characters");
    return;
  }
  userPass = newUserPass.value;
  localStorage.setItem("USER_PASS", userPass);
  alert("✅ User password updated");
  newUserPass.value="";
}

// ===== MONEY SYSTEM =====
let capital=0,current=0,lossCarry=0,trades=0;
const MAX=6;

function startSystem(){
  capital=+capitalInput.value;
  current=capital;
  trades=0; lossCarry=0;
  updateTrade(); render();
}

function updateTrade(){
  let base=current*riskSelect.value/100;
  if(lossCarry>0){
    base=(lossCarry+base)/(payoutInput.value/100)*1.35;
  }
  tradeAmount.innerText=base.toFixed(2);
}

function submitTrade(){
  if(trades>=MAX){status.innerHTML="🔒 Locked";return;}
  let amt=+tradeAmount.innerText;
  trades++;
  if(tradeResult.value==="win"){
    current+=amt*(payoutInput.value/100);
    lossCarry=0;
  }else{
    current-=amt;
    lossCarry+=amt;
  }
  history.innerHTML+=`<tr>
    <td>${trades}</td>
    <td class="${tradeResult.value}">${tradeResult.value}</td>
    <td>${amt.toFixed(2)}</td>
    <td>${current.toFixed(2)}</td>
  </tr>`;
  render();
}

function render(){
  currentCapital.innerText=current.toFixed(2);
  tradeCount.innerText=trades;
  updateTrade();
}

function logout(){location.reload();}
</script>

</body>
</html>
