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
.container { width:100%; padding:15px; box-sizing:border-box }
.card {
  background: rgba(255,255,255,0.08);
  border-radius:14px;
  padding:16px;
  margin-bottom:15px;
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
.lock{color:#ff5252;font-weight:bold;text-align:center}

/* TABLE */
table{width:100%;border-collapse:collapse;background:#1e2a38}
th,td{
  padding:6px;text-align:center;
  border-bottom:1px solid #ffffff33;
}
th{background:#0f3b57}
td{background:#203a43}
.win{color:#4caf50;font-weight:bold}
.loss{color:#ff5252;font-weight:bold}
</style>
</head>

<body>
<div class="container">

<h2>AI Dilwar</h2>

<!-- LOGIN -->
<div class="card" id="loginBox">
  <input id="uid" placeholder="User ID">
  <input id="upass" type="password" placeholder="Password">
  <button onclick="login()">Login</button>
  <p id="loginMsg"></p>
</div>

<!-- ADMIN -->
<div class="card hidden" id="adminBox">
<h3>Admin Panel</h3>

<label>Change USER Password</label>
<input id="newUserPass" placeholder="New User Password">
<button onclick="changeUserPass()">Update User Password</button>

<label style="margin-top:10px">Change ADMIN Password</label>
<input id="newAdminPass" placeholder="New Admin Password">
<button onclick="changeAdminPass()">Update Admin Password</button>

<button style="margin-top:10px" onclick="logout()">Logout</button>
</div>

<!-- SYSTEM -->
<div class="card hidden" id="systemBox">
<label>Initial Capital</label>
<input id="capitalInput" type="number" value="0">

<label>Risk %</label>
<select id="riskSelect">
<option value="1">1%</option>
<option value="2" selected>2%</option>
<option value="3">3%</option>
<option value="4">4%</option>
<option value="5">5%</option>
</select>

<button onclick="startSystem()">Start</button>

<p>Current Capital: <b id="currentCapital">0</b></p>
<p>Next Amount: <b id="tradeAmount">0</b></p>

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
// LOGIN DATA (dynamic)
let USER_UID = "DilwarAi";
let USER_PASS = "848654";

let ADMIN_UID = "admin";
let ADMIN_PASS = "admin123";

// DOM
const loginBox=document.getElementById("loginBox");
const adminBox=document.getElementById("adminBox");
const systemBox=document.getElementById("systemBox");
const historyBox=document.getElementById("historyBox");
const loginMsg=document.getElementById("loginMsg");

// SYSTEM
let capital=0,current=0,lossCarry=0,trades=0;
let historyData=[];

function login(){
  if(uid.value===USER_UID && upass.value===USER_PASS){
    loginBox.classList.add("hidden");
    systemBox.classList.remove("hidden");
    historyBox.classList.remove("hidden");
  }else if(uid.value===ADMIN_UID && upass.value===ADMIN_PASS){
    loginBox.classList.add("hidden");
    adminBox.classList.remove("hidden");
  }else{
    loginMsg.innerText="❌ Wrong Login";
  }
}

/* ADMIN FUNCTIONS */
function changeUserPass(){
  if(newUserPass.value.length<3){
    alert("Password too short");
    return;
  }
  USER_PASS=newUserPass.value;
  alert("✅ User password updated");
  newUserPass.value="";
}

function changeAdminPass(){
  if(newAdminPass.value.length<3){
    alert("Password too short");
    return;
  }
  ADMIN_PASS=newAdminPass.value;
  alert("✅ Admin password updated");
  newAdminPass.value="";
}

function startSystem(){
  capital=+capitalInput.value;
  current=capital;
  lossCarry=0;
  trades=0;
  historyData=[];
  updateTrade();
  render();
}

function updateTrade(){
  let base=current*riskSelect.value/100;
  if(lossCarry>0){
    base=(lossCarry+base)/(payoutInput.value/100)*1.35;
  }
  tradeAmount.innerText=base.toFixed(2);
}

function submitTrade(){
  let amt=parseFloat(tradeAmount.innerText);
  trades++;
  if(tradeResult.value==="win"){
    current+=amt*(payoutInput.value/100);
    lossCarry=0;
  }else{
    current-=amt;
    lossCarry+=amt;
  }
  historyData.push({
    n:trades,
    r:tradeResult.value,
    a:amt,
    c:current,
    t:new Date().toLocaleTimeString()
  });
  updateTrade();
  render();
}

function render(){
  currentCapital.innerText=current.toFixed(2);
  history.innerHTML="";
  historyData.forEach(h=>{
    history.innerHTML+=`
    <tr>
      <td>${h.n}</td>
      <td class="${h.r}">${h.r}</td>
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
