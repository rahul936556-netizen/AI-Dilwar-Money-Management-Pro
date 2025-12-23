💵💵Ai Dilwar mastermind website 🫡😊

<!D<!DO<!DOC<!DOC
<head>
<meta charset="UTF-8">
<title>AI Dilwar – Money Management Pro</title>
<meta name="viewport" content="width=device-width, initial-scale=1">

<style>
html,body{
margin:0;padding:0;
font-family:Segoe UI,Arial;
background:linear-gradient(135deg,#0f2027,#203a43,#2c5364);
color:#fff}
.container{padding:15px}
.card{
background:rgba(255,255,255,.08);
backdrop-filter:blur(10px);
border-radius:14px;
padding:16px;
margin-bottom:15px}
input,select,button{
width:100%;padding:10px;
margin-top:6px;
border-radius:8px;border:none;
font-size:15px}
input,select{background:#1e2a38;color:#fff}
button{
background:linear-gradient(90deg,#f7971e,#ffd200);
font-weight:600}
.hidden{display:none}
.lock{color:#ff5252;font-weight:bold;text-align:center}

/* TABLE FIX */
table{width:100%;border-collapse:collapse;margin-top:10px}
th{background:#0f3b57;padding:8px}
td{background:#203a43;padding:8px;text-align:center}
tr:nth-child(even) td{background:#1b2f3a}
.win{color:#4caf50;font-weight:bold}
.loss{color:#ff5252;font-weight:bold}
</style>
</head>

<body>
<div class="container">

<h2 style="text-align:center">AI Dilwar</h2>
<p style="text-align:center;font-size:13px">Money Management Pro</p>

<!-- LOGIN -->
<div class="card" id="loginBox">
<input id="uid" placeholder="User ID">
<input id="upass" type="password" placeholder="Password">
<button onclick="login()">Login</button>
<p id="loginMsg" style="text-align:center"></p>
</div>

<!-- ADMIN -->
<div class="card hidden" id="adminBox">
<h3 style="text-align:center">Admin Panel</h3>
<input id="newUserPass" placeholder="New USER Password">
<button onclick="changeUserPass()">Change User Password</button>
<button onclick="logout()">Logout</button>
</div>

<!-- SYSTEM -->
<div class="card hidden" id="systemBox">
<label>Initial Capital</label>
<input id="capitalInput" type="number">

<label>Risk %</label>
<select id="riskSelect">
<option value="1">1%</option>
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
<h3 style="text-align:center">Trade History</h3>
<table>
<thead>
<tr>
<th>#</th><th>Result</th><th>Amount</th><th>Capital</th><th>Time</th>
</tr>
</thead>
<tbody id="history"></tbody>
</table>
</div>

</div>

<script>
const USER_ID="DilwarAi";
let USER_PASS="848654";
const ADMIN_ID="admin",ADMIN_PASS="admin123";

let capital=0,current=0,trades=0,lossCarry=0;
const MAX=6;
let historyData=[];

const history=document.getElementById("history");

function login(){
if(uid.value===USER_ID && upass.value===USER_PASS){
loginBox.classList.add("hidden");
systemBox.classList.remove("hidden");
historyBox.classList.remove("hidden");
}
else if(uid.value===ADMIN_ID && upass.value===ADMIN_PASS){
loginBox.classList.add("hidden");
adminBox.classList.remove("hidden");
}
else loginMsg.innerText="Invalid login";
}

function changeUserPass(){
USER_PASS=newUserPass.value;
alert("User password changed");
newUserPass.value="";
}

function startSystem(){
capital=parseFloat(capitalInput.value);
current=capital;
trades=0;
lossCarry=0;
historyData=[];
submitBtn.disabled=false;
refreshBtn.classList.add("hidden");
status.innerHTML="";
updateTrade();
render();
}

function updateTrade(){
let base=current*riskSelect.value/100;
if(lossCarry>0){
base=(lossCarry+base)/(payoutInput.value/100)*1.37;
}
tradeAmount.innerText=base.toFixed(2);
}

function submitTrade(){
if(trades>=MAX)return;
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
n:trades,r:tradeResult.value,
a:amt,c:current,
t:new Date().toLocaleTimeString()
});
updateTrade();
render();
if(trades>=MAX)lockSystem();
}

function lockSystem(){
submitBtn.disabled=true;
refreshBtn.classList.remove("hidden");
status.innerHTML="<span class='lock'>🔒 6 Trades Completed – System Locked</span>";
}

function resetSystem(){
capitalInput.value="";
currentCapital.innerText="0";
tradeAmount.innerText="0";
tradeCount.innerText="0";
history.innerHTML="";
historyData=[];
lossCarry=0;
trades=0;
refreshBtn.classList.add("hidden");
submitBtn.disabled=false;
status.innerHTML="";
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

function logout(){location.reload();}
</script>
</body>
</html>
