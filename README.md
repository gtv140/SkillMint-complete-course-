<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>MINTCRESTGOLD | Live Dashboard</title>
<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/9.22.0/firebase-app.js";
import { getFirestore, doc, setDoc, getDoc, onSnapshot, collection, addDoc, query, where } from "https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore.js";
import { getStorage, ref as sRef, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/9.22.0/firebase-storage.js";

// Firebase config
const firebaseConfig = {
  apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg",
  authDomain: "investment-84f4e.firebaseapp.com",
  projectId: "investment-84f4e",
  storageBucket: "investment-84f4e.firebasestorage.app",
  messagingSenderId: "975293889308",
  appId: "1:975293889308:web:6d034a99cc966c75ff58d9"
};
const app = initializeApp(firebaseConfig);
const db = getFirestore(app);
const storage = getStorage(app);

let currentUser = null;
let adminClicks = 0;

// Plans
const plans = [];
for(let i=1;i<=20;i++) plans.push({name:"Starter "+i,price:i*200,dailyProfit:3+(i*0.2)});
plans.push({name:"Elite 1",price:5000,dailyProfit:10});
plans.push({name:"Elite 2",price:8000,dailyProfit:12});
plans.push({name:"Elite 3",price:12000,dailyProfit:15});

// Login
window.login = async function(){
  const name = document.getElementById("username").value.trim().toUpperCase();
  if(!name) return alert("Enter username");
  currentUser = name;
  const userRef = doc(db,"users",name);
  const docSnap = await getDoc(userRef);
  if(!docSnap.exists()){
    await setDoc(userRef,{balance:0,activePlan:null,planExpiry:null,lastProfit:0,referrals:0});
  }
  document.getElementById("loginBox").style.display="none";
  document.getElementById("appBox").style.display="block";
  loadUserData();
  renderPlans();
};

// Load user data
async function loadUserData(){
  const userRef = doc(db,"users",currentUser);
  onSnapshot(userRef,docSnap=>{
    const data=docSnap.data();
    document.getElementById("balance").innerText="₨ "+data.balance;
    document.getElementById("activePlan").innerText=data.activePlan?`Active: ${data.activePlan} | Expiry: ${new Date(data.planExpiry).toLocaleDateString()}`:"No Active Plan";
    startProfitCountdown(data.planExpiry,data.lastProfit);
  });
}

// Render plans
window.renderPlans = function(){
  const container = document.getElementById("plans");
  container.innerHTML = "";
  plans.forEach(p=>{
    const div = document.createElement("div");
    div.className="card mb-2 flex justify-between items-center";
    div.innerHTML=`<span>${p.name} (${p.dailyProfit}% daily)</span><button onclick="buyPlan('${p.name}',${p.price},${p.dailyProfit})" class="bg-blue-600 px-2 text-xs">₨ ${p.price}</button>`;
    container.appendChild(div);
  });
}

// Buy plan request
window.buyPlan=async function(name,price,dailyProfit){
  await addDoc(collection(db,"requests"),{
    user:currentUser,
    type:"PLAN",
    planName:name,
    amount:price,
    dailyProfit:dailyProfit,
    status:"pending",
    time:Date.now()
  });
  alert("Plan Request Sent to Admin");
}

// Deposit
window.deposit = async function(){
  const amt = parseInt(document.getElementById("depAmount").value);
  const method = document.getElementById("depMethod").value;
  const tid = document.getElementById("depTid").value;
  const file = document.getElementById("depProof").files[0];
  if(!amt||!method||!tid||!file) return alert("Fill all fields");
  const storageRef = sRef(storage,`depositProofs/${currentUser}_${Date.now()}_${file.name}`);
  await uploadBytes(storageRef,file);
  const url = await getDownloadURL(storageRef);
  await addDoc(collection(db,"requests"),{
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
}

// Withdraw
window.withdraw=async function(){
  const amt=parseInt(document.getElementById("wdAmount").value);
  const det=document.getElementById("wdDetails").value;
  if(!amt||!det) return alert("Fill withdraw fields");
  await addDoc(collection(db,"requests"),{
    user:currentUser,
    type:"WITHDRAW",
    amount:amt,
    details:det,
    status:"pending",
    time:Date.now()
  });
  alert("Withdraw Request Sent");
}

// Secret Admin Trigger
document.getElementById("siteTitle").addEventListener("click",()=>{
  adminClicks++;
  if(adminClicks>=4){
    const key = prompt("Enter Admin Key");
    if(key==="mint786"){
      document.getElementById("adminBox").style.display="block";
    } else alert("Wrong Key");
    adminClicks=0;
  }
});

// Admin approve/reject
window.approve = async function(id,user,type,amount,planName,dailyProfit){
  const userRef=doc(db,"users",user);
  const docSnap=await getDoc(userRef);
  const d=docSnap.data();
  if(type==="DEPOSIT"){await setDoc(userRef,{...d,balance:d.balance+amount});}
  if(type==="WITHDRAW"){if(d.balance<amount)return alert("Low balance"); await setDoc(userRef,{...d,balance:d.balance-amount});}
  if(type==="PLAN"){if(d.balance<amount)return alert("Low balance"); await setDoc(userRef,{...d,balance:d.balance-amount,activePlan:planName,planExpiry:Date.now()+24*60*60*1000,lastProfit:dailyProfit});}
  await setDoc(doc(db,"requests",id),{status:"approved"},{merge:true});
}
window.reject=async function(id){await setDoc(doc(db,"requests",id),{status:"rejected"},{merge:true});}

// Profit countdown
function startProfitCountdown(expiry,lastProfit){
  clearInterval(window.profitInterval);
  if(!expiry) return;
  const bar = document.getElementById("profitBar");
  window.profitInterval=setInterval(()=>{
    const diff = expiry - Date.now();
    if(diff<=0){document.getElementById("profitCountdown").innerText="Next Profit Ready!"; bar.style.width="100%"; clearInterval(window.profitInterval); return;}
    let h=Math.floor(diff/3600000), m=Math.floor((diff%3600000)/60000), s=Math.floor((diff%60000)/1000);
    document.getElementById("profitCountdown").innerText=`Next Profit in ${String(h).padStart(2,'0')}:${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
    bar.style.width=`${100-(diff/86400000)*100}%`;
  },1000);
}

// Navigation
window.showPage=function(page,btn){
  document.querySelectorAll(".page").forEach(p=>p.style.display="none");
  document.getElementById("page-"+page).style.display="block";
  document.querySelectorAll(".nav-btn").forEach(b=>b.classList.remove("active-nav"));
  btn.classList.add("active-nav");
}
</script>
<style>
body{font-family:sans-serif;background:#0f172a;color:white;margin:0;padding-bottom:80px;}
.card{background:rgba(255,255,255,0.05);padding:16px;border-radius:12px;margin-bottom:12px;transition:0.3s;}
.card:hover{transform:scale(1.02);box-shadow:0 4px 12px rgba(0,0,0,0.5);}
button{padding:8px 12px;border-radius:8px;font-weight:bold;cursor:pointer;}
button:hover{opacity:0.9;}
.progress-bar{height:6px;background:#3b82f6;border-radius:3px;margin-top:4px;transition:width 0.5s;}
.nav-btn{flex:1;text-align:center;padding:10px;font-size:18px;color:#9ca3af;}
.active-nav{color:#3b82f6;transform:scale(1.1);}
input,select{width:100%;padding:8px;margin-top:4px;margin-bottom:8px;border-radius:8px;background:rgba(255,255,255,0.1);border:none;color:white;}
img{max-width:100%;border-radius:8px;margin-top:4px;}
</style>
</head>
<body>

<!-- Login -->
<div id="loginBox" class="max-w-sm mx-auto mt-20">
  <div class="card text-center">
    <h1 id="siteTitle" class="text-3xl font-bold mb-4 cursor-pointer">MINTCRESTGOLD</h1>
    <input id="username" placeholder="Username">
    <button onclick="login()" class="bg-blue-600 w-full mt-2">Login</button>
  </div>
</div>

<!-- App Dashboard -->
<div id="appBox" class="max-w-md mx-auto space-y-4 hidden">

<div class="page" id="page-home">
  <div class="card text-center">
    <h2 class="text-sm text-gray-400">Wallet Balance</h2>
    <h1 id="balance" class="text-2xl font-bold mt-1">₨ 0</h1>
    <p id="activePlan" class="text-xs text-yellow-400 mt-1"></p>
    <p id="profitCountdown" class="text-xs text-green-400 mt-1"></p>
    <div class="w-full bg-gray-600 rounded-full h-2 mt-1"><div class="progress-bar" id="profitBar"></div></div>
    <button onclick="claimGift()" class="bg-green-600 mt-2 w-full">🎁 Claim Gift</button>
  </div>

  <div class="card">
    <h3 class="font-bold mb-2">💳 Deposit</h3>
    <input id="depAmount" type="number" placeholder="Amount">
    <select id="depMethod">
      <option value="">Select Payment Method</option>
      <option value="JazzCash 03705519562">JazzCash 03705519562</option>
      <option value="Easypaisa 03379827882">Easypaisa 03379827882</option>
      <option value="SadaPay 03705519562">SadaPay 03705519562</option>
    </select>
    <input id="depTid" type="text" placeholder="Transaction ID">
    <input id="depProof" type="file" accept="image/*">
    <div id="depPreview"></div>
    <button onclick="deposit()" class="bg-green-600 mt-2 w-full">Send Deposit Request</button>
  </div>

  <div class="card">
    <h3 class="font-bold mb-2">🏦 Withdraw</h3>
    <input id="wdAmount" type="number" placeholder="Amount">
    <input id="wdDetails" type="text" placeholder="Account Details">
    <button onclick="withdraw()" class="bg-red-600 mt-2 w-full">Send Withdraw Request</button>
  </div>

  <div class="card">
    <h3 class="font-bold mb-2">⚡ Plans / Nodes</h3>
    <div id="plans"></div>
  </div>
</div>

<!-- Bottom Nav -->
<div class="fixed bottom-0 left-0 right-0 flex bg-black/90">
<button class="nav-btn active-nav" onclick="showPage('home',this)">🏠</button>
<button class="nav-btn" onclick="showPage('plans',this)">⚡</button>
<button class="nav-btn" onclick="showPage('activity',this)">📜</button>
<button class="nav-btn" onclick="showPage('help',this)">⚙️</button>
</div>

<!-- Admin Panel -->
<div id="adminBox" class="hidden fixed inset-0 bg-black p-4 overflow-auto"></div>

</body>
</html>
