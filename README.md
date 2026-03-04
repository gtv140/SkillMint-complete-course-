<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>MintCrest Pro</title>

<script src="https://cdn.tailwindcss.com"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>

<style>
body{background:#0f172a;color:white;font-family:sans-serif}
.card{background:#1e293b;padding:20px;border-radius:16px}
input{background:#0f172a;border:1px solid #334155;padding:10px;border-radius:8px;width:100%;color:white}
button{padding:10px;border-radius:8px;font-weight:bold}
</style>
</head>
<body class="p-6">

<div id="loginBox" class="max-w-sm mx-auto mt-20">
<div class="card text-center">
<h1 class="text-2xl font-bold mb-6">MintCrest Pro</h1>
<input id="username" placeholder="Enter Username" class="mb-4">
<button onclick="login()" class="bg-blue-600 w-full">Login</button>
</div>
</div>

<div id="appBox" class="hidden max-w-md mx-auto space-y-6">

<div class="card text-center">
<h2 class="text-sm text-gray-400">Wallet Balance</h2>
<h1 id="balance" class="text-3xl font-bold mt-2">₨ 0</h1>
<p id="activePlan" class="text-xs text-yellow-400 mt-2"></p>
</div>

<div class="card">
<h3 class="font-bold mb-4">Buy Plan</h3>
<div id="plans"></div>
</div>

<div class="card">
<h3 class="mb-4 font-bold">Deposit</h3>
<input id="depAmount" type="number" placeholder="Amount" class="mb-3">
<button onclick="deposit()" class="bg-green-600 w-full">Send Request</button>
</div>

<div class="card">
<h3 class="mb-4 font-bold">Withdraw</h3>
<input id="wdAmount" type="number" placeholder="Amount" class="mb-3">
<button onclick="withdraw()" class="bg-red-600 w-full">Send Request</button>
</div>

<button onclick="openAdmin()" class="text-xs text-gray-400">Admin Panel</button>

</div>

<div id="adminBox" class="hidden fixed inset-0 bg-black p-6 overflow-auto">
<h2 class="text-xl font-bold mb-6">Admin Panel</h2>
<div id="requests"></div>
<button onclick="closeAdmin()" class="mt-6 bg-red-600 px-4 py-2">Close</button>
</div>

<script>
const firebaseConfig = {
apiKey: "YOUR_KEY",
authDomain: "YOUR_DOMAIN",
projectId: "YOUR_PROJECT"
};

firebase.initializeApp(firebaseConfig);
const db = firebase.firestore();

let currentUser=null;

// Generate 20+5 plans
const plans=[];
for(let i=1;i<=20;i++){
plans.push({name:"Standard "+i,price:i*200});
}
plans.push({name:"Premium 1",price:5000});
plans.push({name:"Premium 2",price:8000});
plans.push({name:"Premium 3",price:12000});
plans.push({name:"Premium 4",price:20000});
plans.push({name:"Premium 5",price:50000});

function renderPlans(){
let html="";
plans.forEach(p=>{
html+=`<div class="mb-2 flex justify-between">
<span>${p.name}</span>
<button onclick="buyPlan('${p.name}',${p.price})" class="bg-blue-600 text-xs px-3">₨ ${p.price}</button>
</div>`;
});
document.getElementById("plans").innerHTML=html;
}

function login(){
const name=document.getElementById("username").value.trim().toUpperCase();
if(!name)return alert("Enter username");

const ref=db.collection("users").doc(name);

ref.get().then(doc=>{
if(!doc.exists){
ref.set({balance:0,activePlan:null,planExpiry:null});
}
});

currentUser=name;

ref.onSnapshot(snap=>{
const d=snap.data();
document.getElementById("balance").innerText="₨ "+d.balance;

if(d.activePlan){
document.getElementById("activePlan").innerText=
"Active: "+d.activePlan+" | Expiry: "+new Date(d.planExpiry).toLocaleDateString();
}else{
document.getElementById("activePlan").innerText="No Active Plan";
}
});

document.getElementById("loginBox").classList.add("hidden");
document.getElementById("appBox").classList.remove("hidden");

renderPlans();
}

function deposit(){
const amt=parseInt(document.getElementById("depAmount").value);
if(!amt)return;

db.collection("requests").add({
user:currentUser,
type:"DEPOSIT",
amount:amt,
status:"pending",
time:Date.now()
});

alert("Deposit Request Sent");
}

function withdraw(){
const amt=parseInt(document.getElementById("wdAmount").value);
if(!amt)return;

db.collection("requests").add({
user:currentUser,
type:"WITHDRAW",
amount:amt,
status:"pending",
time:Date.now()
});

alert("Withdraw Request Sent");
}

function buyPlan(name,price){
db.collection("requests").add({
user:currentUser,
type:"PLAN",
planName:name,
amount:price,
status:"pending",
time:Date.now()
});

alert("Plan Request Sent to Admin");
}

function openAdmin(){
const key=prompt("Admin Key");
if(key!=="mint786")return;

document.getElementById("adminBox").classList.remove("hidden");

db.collection("requests")
.where("status","==","pending")
.onSnapshot(snap=>{
let html="";
snap.forEach(doc=>{
const r=doc.data();
html+=`
<div class="card mb-4">
<div>${r.user}</div>
<div>${r.type} - ₨ ${r.amount}</div>
<button onclick="approve('${doc.id}','${r.user}','${r.type}',${r.amount},'${r.planName||""}')" class="bg-green-600 mt-2">Approve</button>
</div>`;
});
document.getElementById("requests").innerHTML=html;
});
}

function approve(id,user,type,amount,planName){
const userRef=db.collection("users").doc(user);

userRef.get().then(doc=>{
const d=doc.data();

if(type==="DEPOSIT"){
userRef.update({balance:d.balance+amount});
}

if(type==="WITHDRAW"){
if(d.balance<amount)return alert("Low balance");
userRef.update({balance:d.balance-amount});
}

if(type==="PLAN"){
if(d.balance<amount)return alert("Low balance");
userRef.update({
balance:d.balance-amount,
activePlan:planName,
planExpiry:Date.now()+30*24*60*60*1000
});
}

db.collection("requests").doc(id).update({status:"approved"});
});
}

function closeAdmin(){
document.getElementById("adminBox").classList.add("hidden");
}
</script>

</body>
</html>
