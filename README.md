<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>MintCrest Admin Wallet</title>

<script src="https://cdn.tailwindcss.com"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>

<style>
body{background:#0f172a;color:white;font-family:sans-serif;}
.card{background:#1e293b;padding:20px;border-radius:16px;}
input, select{background:#0f172a;border:1px solid #334155;padding:10px;border-radius:8px;width:100%;color:white;margin-bottom:10px;}
button{padding:10px;border-radius:8px;font-weight:bold;margin-top:5px;}
img{max-width:100%;border-radius:8px;margin-top:5px;}
</style>
</head>
<body class="p-6">

<!-- LOGIN -->
<div id="loginBox" class="max-w-sm mx-auto mt-20">
<div class="card text-center">
<h1 class="text-2xl font-bold mb-6">MintCrest Wallet</h1>
<input id="username" placeholder="Enter Username" class="mb-4">
<button onclick="login()" class="bg-blue-600 w-full">Login</button>
</div>
</div>

<!-- USER APP -->
<div id="appBox" class="hidden max-w-md mx-auto space-y-6">

<div class="card text-center">
<h2 class="text-sm text-gray-400">Wallet Balance</h2>
<h1 id="balance" class="text-3xl font-bold mt-2">₨ 0</h1>
<p id="activePlan" class="text-xs text-yellow-400 mt-2"></p>
</div>

<!-- BUY PLAN -->
<div class="card">
<h3 class="font-bold mb-4">Buy Plan</h3>
<div id="plans"></div>
</div>

<!-- DEPOSIT -->
<div class="card">
<h3 class="font-bold mb-4">Deposit Funds</h3>
<input id="depAmount" type="number" placeholder="Amount">
<select id="depMethod">
<option value="">Select Payment Method</option>
<option value="JazzCash 03705519562">JazzCash 03705519562</option>
<option value="Easypaisa 03379827882">Easypaisa 03379827882</option>
<option value="Sadapay 03705519562">SadaPay 03705519562</option>
</select>
<input id="depTid" type="text" placeholder="Transaction ID">
<input id="depProof" type="file" accept="image/*">
<button onclick="deposit()" class="bg-green-600 w-full">Send Deposit Request</button>
<div id="depPreview"></div>
</div>

<!-- WITHDRAW -->
<div class="card">
<h3 class="font-bold mb-4">Withdraw Funds</h3>
<input id="wdAmount" type="number" placeholder="Amount">
<input id="wdDetails" type="text" placeholder="Account Details">
<button onclick="withdraw()" class="bg-red-600 w-full">Send Withdraw Request</button>
</div>

<button onclick="openAdmin()" class="text-xs text-gray-400">Admin Panel</button>

</div>

<!-- ADMIN PANEL -->
<div id="adminBox" class="hidden fixed inset-0 bg-black p-6 overflow-auto">
<h2 class="text-xl font-bold mb-6">Admin Panel</h2>
<div id="requests"></div>
<button onclick="closeAdmin()" class="mt-6 bg-red-600 px-4 py-2">Close</button>
</div>

<script>
// Firebase config
const firebaseConfig = {
apiKey: "YOUR_API_KEY",
authDomain: "YOUR_AUTH_DOMAIN",
projectId: "YOUR_PROJECT_ID",
storageBucket: "YOUR_STORAGE_BUCKET"
};

firebase.initializeApp(firebaseConfig);
const db = firebase.firestore();
const storage = firebase.storage();

let currentUser=null;

// Plans
const plans=[];
for(let i=1;i<=20;i++) plans.push({name:"Standard "+i,price:i*200});
plans.push({name:"Premium 1",price:5000});
plans.push({name:"Premium 2",price:8000});
plans.push({name:"Premium 3",price:12000});
plans.push({name:"Premium 4",price:20000});
plans.push({name:"Premium 5",price:50000});

// Render plans
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

// LOGIN
function login(){
const name=document.getElementById("username").value.trim().toUpperCase();
if(!name)return alert("Enter username");

const ref=db.collection("users").doc(name);
ref.get().then(doc=>{if(!doc.exists) ref.set({balance:0,activePlan:null,planExpiry:null});});

currentUser=name;

// Live data
ref.onSnapshot(snap=>{
const d=snap.data();
document.getElementById("balance").innerText="₨ "+d.balance;
document.getElementById("activePlan").innerText=d.activePlan?`Active: ${d.activePlan} | Expiry: ${new Date(d.planExpiry).toLocaleDateString()}`:"No Active Plan";
});

document.getElementById("loginBox").classList.add("hidden");
document.getElementById("appBox").classList.remove("hidden");
renderPlans();
}

// DEPOSIT
document.getElementById("depProof").addEventListener("change",(e)=>{
const file=e.target.files[0];
if(file){
const reader=new FileReader();
reader.onload=function(){document.getElementById("depPreview").innerHTML=`<img src="${reader.result}">`;}
reader.readAsDataURL(file);
}
});

function deposit(){
const amt=parseInt(document.getElementById("depAmount").value);
const method=document.getElementById("depMethod").value;
const tid=document.getElementById("depTid").value;
const file=document.getElementById("depProof").files[0];
if(!amt||!method||!tid||!file) return alert("Fill all deposit fields");

const storageRef=storage.ref(`depositProofs/${currentUser}_${Date.now()}_${file.name}`);
storageRef.put(file).then(()=>storageRef.getDownloadURL().then(url=>{
db.collection("requests").add({
user:currentUser,
type:"DEPOSIT",
amount:amt,
method:method,
tid:tid,
proof:url,
status:"pending",
time:Date.now()
});
alert("Deposit Request Sent to Admin");
}));
}

// WITHDRAW
function withdraw(){
const amt=parseInt(document.getElementById("wdAmount").value);
const det=document.getElementById("wdDetails").value;
if(!amt||!det) return alert("Fill withdraw fields");
db.collection("requests").add({
user:currentUser,
type:"WITHDRAW",
amount:amt,
details:det,
status:"pending",
time:Date.now()
});
alert("Withdraw Request Sent");
}

// PLAN PURCHASE
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

// ADMIN PANEL
function openAdmin(){
const key=prompt("Admin Key");
if(key!=="mint786")return alert("Wrong key");

document.getElementById("adminBox").classList.remove("hidden");
db.collection("requests").where("status","==","pending").onSnapshot(snap=>{
let html="";
snap.forEach(doc=>{
const r=doc.data();
html+=`<div class="card mb-4">
<div>${r.user}</div>
<div>${r.type} - ₨ ${r.amount} ${r.planName? "("+r.planName+")":""}</div>
${r.proof?`<img src="${r.proof}">`:''}
<button onclick="approve('${doc.id}','${r.user}','${r.type}',${r.amount},'${r.planName||""}')" class="bg-green-600 mt-2">Approve</button>
<button onclick="reject('${doc.id}')" class="bg-red-600 mt-2 ml-2">Reject</button>
</div>`;
});
document.getElementById("requests").innerHTML=html;
});
}

// APPROVE
function approve(id,user,type,amount,planName){
const ref=db.collection("users").doc(user);
ref.get().then(doc=>{
const d=doc.data();
if(type==="DEPOSIT"){ref.update({balance:d.balance+amount});}
if(type==="WITHDRAW"){if(d.balance<amount)return alert("Low balance"); ref.update({balance:d.balance-amount});}
if(type==="PLAN"){if(d.balance<amount)return alert("Low balance"); ref.update({balance:d.balance-amount,activePlan:planName,planExpiry:Date.now()+30*24*60*60*1000});}
db.collection("requests").doc(id).update({status:"approved"});
});
}

// REJECT
function reject(id){db.collection("requests").doc(id).update({status:"rejected"});}

// CLOSE ADMIN
function closeAdmin(){document.getElementById("adminBox").classList.add("hidden");}

</script>
</body>
</html>
