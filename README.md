<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>AI Dilwar – Money Management Pro</title>

<meta name="viewport"
content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">

<style>
*{box-sizing:border-box}

body{
margin:0;
font-family:Arial,Segoe UI;
background:#0f2027;
color:#fff;
}

/* MOBILE WIDTH FIX */
.container{
max-width:480px;
margin:auto;
padding:12px;
}

/* CARD */
.card{
background:#1e2a38;
border-radius:12px;
padding:14px;
margin-bottom:14px;
}

/* INPUTS */
input,select,button{
width:100%;
padding:12px;
margin-top:8px;
border:none;
border-radius:8px;
font-size:16px;
}

input,select{
background:#263545;
color:#fff;
}

button{
background:linear-gradient(90deg,#f7971e,#ffd200);
color:#000;
font-weight:bold;
}

/* TEXT */
h2,h3{text-align:center;margin:6px 0}
.small{text-align:center;font-size:13px;opacity:.85}
.hidden{display:none}
.lock{color:#ff5252;font-weight:bold;text-align:center}

/* HISTORY FIX */
#historyBox{
overflow-x:auto;
}

table{
width:100%;
min-width:420px;
border-collapse:collapse;
background:#203a43;
}

th,td{
padding:8px;
font-size:14px;
border-bottom:1px solid #ffffff22;
text-align:center;
}

th{background:#0f3b57}

.win{color:#4caf50;font-weight:bold}
.loss{color:#ff5252;font-weight:bold}
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
<option value="1">1%</option>
<option value="2" selected>2%</option>
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

<button onclick="submitTrade()">Submit Trade</button>
<button id="refreshBtn" class="hidden" onclick="resetSystem()">🔄 Refresh</button>
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
const FIXED_UID="DilwarAi";
const FIXED_PASS="848654";

let capital=0,current=0,trades=0,lossCarry=0;
const MAX=6;
const historyData=[];

function login(){
if(uid.value===FIXED_UID && upass.value===FIXED_PASS){
loginBox.classList.add("hidden");
systemBox.classList.remove("hidden");
historyBox.classList.remove("hidden");
}else{
loginMsg.innerText="Invalid login";
}
}

function startSystem(){
capital=+capitalInput.value;
current=capital;
trades=0;
lossCarry=0;
historyData.length=0;
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
if(trades>=MAX)return lockSystem();
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
if(trades>=MAX)lockSystem();
}

function render(){
currentCapital.innerText=current.toFixed(2);
tradeCount.innerText=trades;
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

function lockSystem(){
status.innerHTML="<div class='lock'>🔒 6 Trades Completed</div>";
refreshBtn.classList.remove("hidden");
}

function resetSystem(){
location.reload();
}
</script>
</body>
</html>
