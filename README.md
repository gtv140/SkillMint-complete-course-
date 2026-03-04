<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>MintCrest Wallet</title>

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
        <h1 class="text-2xl font-bold mb-6">MintCrest Wallet</h1>
        <input id="username" placeholder="Enter Username" class="mb-4">
        <button onclick="login()" class="bg-blue-600 w-full">Login</button>
    </div>
</div>

<div id="appBox" class="hidden max-w-md mx-auto mt-10 space-y-6">

    <div class="card text-center">
        <h2 class="text-sm text-gray-400">Wallet Balance</h2>
        <h1 id="balance" class="text-3xl font-bold mt-2">₨ 0</h1>
    </div>

    <div class="card">
        <h3 class="mb-4 font-bold">Deposit Request</h3>
        <input id="depAmount" type="number" placeholder="Amount" class="mb-3">
        <button onclick="deposit()" class="bg-green-600 w-full">Send Deposit Request</button>
    </div>

    <div class="card">
        <h3 class="mb-4 font-bold">Withdraw Request</h3>
        <input id="wdAmount" type="number" placeholder="Amount" class="mb-3">
        <button onclick="withdraw()" class="bg-red-600 w-full">Send Withdraw Request</button>
    </div>

    <div class="card">
        <h3 class="mb-4 font-bold">My Requests</h3>
        <div id="history"></div>
    </div>

    <button onclick="openAdmin()" class="text-xs text-gray-400 mt-4">Admin Panel</button>

</div>

<div id="adminBox" class="hidden fixed inset-0 bg-black p-6 overflow-auto">
    <h2 class="text-xl font-bold mb-6">Admin Panel</h2>
    <div id="requests"></div>
    <button onclick="closeAdmin()" class="mt-6 bg-red-600 px-4 py-2">Close</button>
</div>

<script>
const firebaseConfig = {
apiKey: "YOUR_API_KEY",
authDomain: "YOUR_DOMAIN",
projectId: "YOUR_PROJECT_ID"
};

firebase.initializeApp(firebaseConfig);
const db = firebase.firestore();

let currentUser = null;

function login(){
    const name = document.getElementById("username").value.trim().toUpperCase();
    if(!name) return alert("Enter username");

    const ref = db.collection("users").doc(name);

    ref.get().then(doc=>{
        if(!doc.exists){
            ref.set({balance:0});
        }
    });

    currentUser = name;

    ref.onSnapshot(snap=>{
        const data = snap.data();
        document.getElementById("balance").innerText = "₨ " + data.balance;
    });

    db.collection("requests")
    .where("user","==",name)
    .onSnapshot(snap=>{
        let html="";
        snap.forEach(d=>{
            const r=d.data();
            html+=`<div class="text-sm mb-2">
            ${r.type} - ₨ ${r.amount} 
            <span class="text-yellow-400">(${r.status})</span>
            </div>`;
        });
        document.getElementById("history").innerHTML=html;
    });

    document.getElementById("loginBox").classList.add("hidden");
    document.getElementById("appBox").classList.remove("hidden");
}

function deposit(){
    const amt=parseInt(document.getElementById("depAmount").value);
    if(!amt || amt<=0) return alert("Enter valid amount");

    db.collection("requests").add({
        user:currentUser,
        amount:amt,
        type:"DEPOSIT",
        status:"pending",
        time:Date.now()
    });

    alert("Deposit request sent");
}

function withdraw(){
    const amt=parseInt(document.getElementById("wdAmount").value);
    if(!amt || amt<=0) return alert("Enter valid amount");

    db.collection("requests").add({
        user:currentUser,
        amount:amt,
        type:"WITHDRAW",
        status:"pending",
        time:Date.now()
    });

    alert("Withdraw request sent");
}

function openAdmin(){
    const key=prompt("Enter Admin Key");
    if(key!=="mint786") return;

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
                <button onclick="approve('${doc.id}','${r.user}','${r.type}',${r.amount})" class="bg-green-600 mt-2">Approve</button>
            </div>`;
        });
        document.getElementById("requests").innerHTML=html;
    });
}

function approve(id,user,type,amount){
    const userRef=db.collection("users").doc(user);

    userRef.get().then(doc=>{
        const bal=doc.data().balance;

        if(type==="DEPOSIT"){
            userRef.update({balance:bal+amount});
        }

        if(type==="WITHDRAW"){
            if(bal<amount) return alert("Insufficient balance");
            userRef.update({balance:bal-amount});
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
