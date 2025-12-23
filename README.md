<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>AI Dilwar – Money Management</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<style>
*{box-sizing:border-box}
body{
  margin:0;
  font-family:Arial, sans-serif;
  background:#f2f2f2;
  color:#000;
}
.container{
  max-width:480px;
  margin:auto;
  padding:12px;
}
.card{
  background:#ffffff;
  border-radius:8px;
  padding:12px;
  margin-bottom:12px;
  box-shadow:0 2px 6px rgba(0,0,0,.1);
}
h2,h3{text-align:center;margin:8px 0}
input,select,button{
  width:100%;
  padding:10px;
  margin-top:6px;
  font-size:16px;
}
button{
  background:#1976d2;
  color:#fff;
  border:none;
  border-radius:5px;
}
.hidden{display:none}

/* ===== NORMAL TABLE ===== */
#historyBox{overflow-x:auto}
table{
  width:100%;
  min-width:520px;
  border-collapse:collapse;
  background:#ffffff;
}
th,td{
  border:1px solid #ccc;
  padding:8px;
  font-size:14px;
  text-align:center;
}
th{
  background:#eeeeee;
  font-weight:bold;
}
.win{color:green;font-weight:bold}
.loss{color:red;font-weight:bold}
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
  <p>Next Trade Amount: <b id="tradeAmount">0</b></p>

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
  <th>Trade Amt</th>
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
const uid=document.getElementById("uid");
const upass=document.getElementById("upass");
const loginBox=document.getElementById("loginBox");
const systemBox=document.getElementById("systemBox");
const historyBox=document.getElementById("historyBox");
const history=document.getElementById("history");

const capitalInput=document.getElementById("capitalInput");
const riskSelect=document.getElementById("riskSelect");
const tradeResult=document.getElementById("tradeResult");
const payoutInput=document.getElementById("payoutInput");
const currentCapital=document.getElementById("currentCapital");
const tradeAmount=document.getElementById("tradeAmount");

const FIXED_UID="DilwarAi";
const FIXED_PASS="848654";

let capital=0,current=0,lossCarry=0,trades=0;
let historyData=[];

function login(){
  if(uid.value===FIXED_UID && upass.value===FIXED_PASS){
    loginBox.classList.add("hidden");
    systemBox.classList.remove("hidden");
    historyBox.classList.remove("hidden");
  }else{
    alert("Wrong ID or Password");
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
    base=(lossCarry+base)/(payoutInput.value/100);
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
      <td class="${h.r}">${h.r.toUpperCase()}</td>
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
