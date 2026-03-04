<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>MINTCRESTGOLD | Dashboard</title>
<script src="https://cdn.tailwindcss.com"></script>
<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/9.22.0/firebase-app.js";
import { getFirestore, doc, setDoc, getDoc, onSnapshot, collection, addDoc, query, orderBy, where, updateDoc } from "https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore.js";

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

// Variables
let currentUser=null, adminClicks=0, promoApplied=false;
const plans=[];
for(let i=1;i<=25;i++){
  plans.push({name:`Plan ${i}`,price:(i<=20?200*i:(i-20)*2000),daily:3+(i*0.2)});
}

// LOGIN
window.login=async function(){
  const name=document.getElementById("username").value.trim().toUpperCase();
  if(!name) return alert("Enter username");
  currentUser=name;
  const uRef=doc(db,"users",name);
  const uSnap=await getDoc(uRef);
  if(!uSnap.exists()){
    await setDoc(uRef,{balance:0,activePlan:null,lastProfit:0,redeemedCodes:[],pin:"0000"});
  }
  document.getElementById("loginBox").classList.add("hidden");
  document.getElementById("appBox").classList.remove("hidden");
  loadUserData(); renderPlans(); syncBroadcast(); loadActivity();
}

// LOAD USER DATA
async function loadUserData(){
  const uRef=doc(db,"users",currentUser);
  onSnapshot(uRef,docSnap=>{
    const d=docSnap.data();
    document.getElementById("balance").innerText="₨ "+d.balance;
    document.getElementById("activePlan").innerText=d.activePlan?`Active: ${d.activePlan}`:"No Active Plan";
    promoApplied=d.redeemedCodes && d.redeemedCodes.length>0;
  });
}

// RENDER PLANS
window.renderPlans=function(){
  const container=document.getElementById("plans");
  container.innerHTML="";
  plans.forEach(p=>{
    const div=document.createElement("div");
    div.className="bg-white/5 rounded-xl p-4 mb-2 flex justify-between items-center hover:bg-blue-600/10 transition";
    div.innerHTML=`<span>${p.name} (${p.daily}% daily)</span><button onclick="buyPlan('${p.name}',${p.price},${p.daily})" class="bg-blue-600 px-3 py-1 rounded-xl text-xs">₨ ${p.price}</button>`;
    container.appendChild(div);
  });
}

// BUY PLAN
window.buyPlan=async function(name,price,daily){
  await addDoc(collection(db,"requests"),{user:currentUser,type:"PLAN",planName:name,amount:price,daily:daily,status:"pending",time:Date.now()});
  alert("Plan request sent to admin!");
}

// DEPOSIT
window.deposit=async function(){
  const amt=parseInt(document.getElementById("depAmount").value);
  const method=document.getElementById("depMethod").value;
  const tid=document.getElementById("depTid").value;
  if(!amt||!method||!tid) return alert("Fill all fields");
  await addDoc(collection(db,"requests"),{user:currentUser,type:"DEPOSIT",amount:amt,method:method,tid:tid,status:"pending",time:Date.now()});
  alert("Deposit request sent!");
}

// WITHDRAW
window.withdraw=async function(){
  const amt=parseInt(document.getElementById("wdAmount").value);
  const det=document.getElementById("wdDetails").value;
  const pin=document.getElementById("wdPin").value;
  const uRef=doc(db,"users",currentUser);
  const uSnap=await getDoc(uRef);
  if(amt>uSnap.data().balance) return alert("Insufficient balance");
  if(pin!==uSnap.data().pin) return alert("Wrong PIN");
  if(!amt||!det) return alert("Fill all fields");
  await addDoc(collection(db,"requests"),{user:currentUser,type:"WITHDRAW",amount:amt,details:det,status:"pending",time:Date.now()});
  await updateDoc(uRef,{balance:uSnap.data().balance-amt});
  alert("Withdraw request sent!");
}

// PROMO CODE
window.applyPromo=async function(){
  if(promoApplied) return alert("Already applied code");
  const code=document.getElementById("promoCode").value.trim();
  if(!code) return alert("Enter code");
  await addDoc(collection(db,"requests"),{user:currentUser,type:"PROMO",code:code,status:"pending",time:Date.now()});
  alert("Promo code request sent!");
}

// SECRET ADMIN PANEL
document.getElementById("siteTitle").addEventListener("click",()=>{
  adminClicks++;
  if(adminClicks>=4){
    const key=prompt("Enter Admin Key");
    if(key==="mint786") document.getElementById("adminBox").classList.remove("hidden");
    else alert("Wrong key");
    adminClicks=0;
  }
});

// BROADCAST SYNC
async function syncBroadcast(){
  const ref=doc(db,"settings","broadcast");
  onSnapshot(ref,docSnap=>{
    if(docSnap.exists() && !promoApplied) alert("🎉 Promo/Message: "+docSnap.data().msg);
  });
}

// ACTIVITY
async function loadActivity(){
  const q=query(collection(db,"requests"),where("user","==",currentUser),orderBy("time","desc"));
  onSnapshot(q,snap=>{
    const container=document.getElementById("activityList");
    container.innerHTML="";
    snap.forEach(d=>{
      const r=d.data();
      const div=document.createElement("div");
      div.className="bg-white/5 rounded-xl p-2 mb-1 flex justify-between text-xs";
      div.innerHTML=`<span>${r.type}${r.planName?": "+r.planName:""}<br><span class="opacity-50">${new Date(r.time).toLocaleString()}</span></span><span class="${r.status==='approved'?'text-green-400':'text-yellow-400'}">${r.status}</span>`;
      container.appendChild(div);
    });
  });
}

// NAVIGATION
window.showPage=function(page,btn){
  document.querySelectorAll(".page").forEach(p=>p.classList.add("hidden"));
  document.getElementById("page-"+page).classList.remove("hidden");
  document.querySelectorAll(".nav-btn").forEach(b=>b.classList.remove("text-blue-500"));
  if(btn) btn.classList.add("text-blue-500");
}
</script>
</head>
<body class="bg-gray-900 text-white font-sans">

<!-- LOGIN -->
<div id="loginBox" class="max-w-sm mx-auto mt-16 p-4">
  <h1 id="siteTitle" class="text-3xl font-bold text-center mb-4 cursor-pointer">MINTCRESTGOLD</h1>
  <input id="username" placeholder="Username" class="w-full p-3 rounded-xl mb-2 bg-white/10 text-white">
  <button onclick="login()" class="w-full bg-blue-600 p-3 rounded-xl font-bold">Login</button>
</div>

<!-- DASHBOARD -->
<div id="appBox" class="hidden max-w-md mx-auto space-y-4 p-4">

<div class="page" id="page-home">
  <div class="bg-white/5 rounded-2xl p-4 text-center mb-3">
    <h2 class="text-gray-400 text-sm">Wallet Balance</h2>
    <h1 id="balance" class="text-2xl font-bold mt-1">₨ 0</h1>
    <p id="activePlan" class="text-yellow-400 text-xs mt-1"></p>
  </div>

  <!-- Deposit -->
  <div class="bg-white/5 rounded-2xl p-4 mb-3">
    <h3 class="font-bold mb-2">💳 Deposit</h3>
    <input id="depAmount" placeholder="Amount" class="w-full p-2 rounded-xl mb-2 bg-white/10">
    <select id="depMethod" class="w-full p-2 rounded-xl mb-2 bg-white/10">
      <option value="">Select Method</option>
      <option>JazzCash 03705519562</option>
      <option>Easypaisa 03379827882</option>
      <option>SadaPay 03705519562</option>
    </select>
    <input id="depTid" placeholder="Transaction ID" class="w-full p-2 rounded-xl mb-2 bg-white/10">
    <button onclick="deposit()" class="w-full bg-green-600 p-2 rounded-xl">Send Deposit</button>
  </div>

  <!-- Withdraw -->
  <div class="bg-white/5 rounded-2xl p-4 mb-3">
    <h3 class="font-bold mb-2">🏦 Withdraw</h3>
    <input id="wdAmount" placeholder="Amount" class="w-full p-2 rounded-xl mb-2 bg-white/10">
    <input id="wdDetails" placeholder="Account Details" class="w-full p-2 rounded-xl mb-2 bg-white/10">
    <input id="wdPin" placeholder="PIN" type="password" class="w-full p-2 rounded-xl mb-2 bg-white/10">
    <button onclick="withdraw()" class="w-full bg-red-600 p-2 rounded-xl">Send Withdraw</button>
  </div>

  <!-- Plans -->
  <div class="bg-white/5 rounded-2xl p-4 mb-3">
    <h3 class="font-bold mb-2">⚡ Plans</h3>
    <div id="plans" class="space-y-2"></div>
  </div>

  <!-- Promo -->
  <div class="bg-white/5 rounded-2xl p-4 mb-3">
    <h3 class="font-bold mb-2">🎁 Promo Code</h3>
    <input id="promoCode" placeholder="Enter Code" class="w-full p-2 rounded-xl mb-2 bg-white/10">
    <button onclick="applyPromo()" class="w-full bg-purple-600 p-2 rounded-xl">Apply</button>
  </div>

  <!-- Activity -->
  <div class="bg-white/5 rounded-2xl p-4 mb-3">
    <h3 class="font-bold mb-2">📜 Activity</h3>
    <div id="activityList" class="space-y-1"></div>
  </div>
</div>

<!-- Admin Panel -->
<div id="adminBox" class="hidden fixed inset-0 bg-black/95 p-4 overflow-auto z-50 text-white">
  <h2 class="text-xl font-bold mb-4">Admin Panel</h2>
  <p>Approve/Reject Requests, Broadcast Messages, Set Promo Codes</p>
</div>

<!-- Bottom Nav -->
<div class="fixed bottom-0 left-0 right-0 flex justify-around bg-black/90 p-3">
  <button class="nav-btn text-blue-500" onclick="showPage('home',this)">🏠 Home</button>
  <button class="nav-btn" onclick="showPage('plans',this)">⚡ Plans</button>
  <button class="nav-btn" onclick="showPage('activity',this)">📜 Activity</button>
  <button class="nav-btn" onclick="showPage('help',this)">⚙️ Support</button>
</div>

</body>
</html>
