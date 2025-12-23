<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>AI Dilwar – Money Management Pro</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<style>
*{box-sizing:border-box}
body{
margin:0;
font-family:Arial;
background:#0f2027;
color:#fff;
}
.container{
max-width:480px;
margin:auto;
padding:12px;
}
.card{
background:#1e2a38;
border-radius:12px;
padding:14px;
margin-bottom:14px;
}
input,select,button{
width:100%;
padding:12px;
margin-top:8px;
border:none;
border-radius:8px;
font-size:16px;
}
input,select{background:#263545;color:#fff}
button{
background:linear-gradient(90deg,#f7971e,#ffd200);
color:#000;
font-weight:bold;
}
.hidden{display:none}
h2,h3{text-align:center}

/* TABLE FIX */
#historyBox{overflow-x:auto}
table{
width:100%;
min-width:520px;
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

<!-- LOGIN -->
<div class="card" id="loginBox">
<input id="uid" placeholder="User ID">
<input id="upass" type="password" placeholder="Password">
<button onclick="login()">Login</button>
<p id="loginMsg"></p>
</div>

<!-- SYSTEM -->
<div class="card hidden" id="systemBox">
<label>Capital</label>
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
</div>

<!-- HISTORY -->
<div class="card hidden" id="historyBox">
<h3>Trade History</h3>
<table>
<thead>
<tr>
<th>#</th>
<th>Result</th>
<th>Trade</th>
<th>Profit</th>
<th>Loss</th>
<th>Capital</th>
<th>Time</th>
</tr>
</thead>
<tbody id="history"></tbody>
</table>
</div>

</div>

<script>
// DOM FIX (VERY IMPORTANT)
const uid = document.getElementById("uid");
const upass = document.getElementById("upass");
const loginBox = document.getElementById("loginBox");
const systemBox = document.getElementById("systemBox");
const historyBox = document.getElementById("historyBox");
const history = document.getElementById("history");

const capitalInput = document.getElementById("capitalInput");
const riskSelect = document.getElementById("riskSelect");
const tradeResult = document.getElementById("tradeResult");
const payoutInput = document.getElementById("payoutInput");
const currentCapital = document.getElementById("currentCapital");
const tradeAmount = document.getElementById("tradeAmount");

const FIXED_UID="DilwarAi";
const FIXED_PASS="848654";

let capital=0,current=0,trades=0,lossCarry=0;
let historyData=[];

function login(){
if(uid.value===FIXED_UID && upass.value===FIXED_PASS){
loginBox.classList.add("hidden");
systemBox.classList.remove("hidden");
historyBox.classList.remove("hidden");
}else{
alert("Wrong Login");
}
}

function startSystem(){
capital=+capitalInput.value;
current=capital;
trades=0;
lossCarry=0;
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

let profit=0,loss=0;
if(tradeResult.value==="win"){
profit=amt*(payoutInput.value/100);
current+=profit;
lossCarry=0;
}else{
loss=amt;
current-=amt;
lossCarry+=amt;
}

historyData.push({
n:trades,
r:tradeResult.value,
a:amt,
p:profit,
l:loss,
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
<td class="win">${h.p?"+ "+h.p.toFixed(2):"-"}</td>
<td class="loss">${h.l?"- "+h.l.toFixed(2):"-"}</td>
<td>${h.c.toFixed(2)}</td>
<td>${h.t}</td>
</tr>`;
});
}
</script>

</body>
</html>
