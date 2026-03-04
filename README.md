<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Power Edition</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;600;700&display=swap');
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Space Grotesk', sans-serif; }
        body { background: #000; color: #fff; height: 100vh; overflow: hidden; display: flex; flex-direction: column; }
        .glass-card { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(15px); border: 1px solid rgba(255, 255, 255, 0.08); border-radius: 1.5rem; }
        .page-container { flex: 1; overflow-y: auto; padding-bottom: 120px; }
        .page { display: none; padding: 20px; animation: fadeIn 0.3s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        .active-nav { color: #3b82f6; transform: scale(1.1); }
        input { background: rgba(255,255,255,0.05) !important; border: 1px solid rgba(255,255,255,0.1) !important; color: white !important; border-radius: 12px !important; }
    </style>
</head>
<body>

    <div id="global-note" class="hidden bg-blue-600 p-2 text-[10px] font-bold uppercase text-center tracking-tighter z-[3000]">
        <span id="note-text">System Core Online</span>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[4000] bg-black flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-4xl font-bold mb-8 italic">MINT<span class="text-blue-500">CREST</span></h1>
            <div class="glass-card p-8">
                <input type="text" id="user-name" placeholder="AGENT USERNAME" class="w-full p-4 mb-4 text-center font-bold uppercase outline-none">
                <button onclick="login()" class="w-full bg-blue-600 p-4 rounded-xl font-bold uppercase text-xs tracking-widest active:scale-95">Verify & Connect</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden page-container">
        <div id="p-home" class="page active-page">
            <div class="glass-card p-8 mb-6 border-l-8 border-blue-500 shadow-2xl">
                <p class="text-[10px] text-blue-400 font-bold uppercase tracking-widest mb-1">Available Liquidity</p>
                <h2 class="text-5xl font-bold tracking-tighter" id="v-bal">₨ 0</h2>
                <div id="timer-box" class="mt-6 hidden bg-blue-500/10 p-4 rounded-2xl border border-blue-500/20">
                    <p class="text-[8px] text-gray-400 uppercase font-bold mb-1">Mining Cycle Ends In:</p>
                    <div id="countdown" class="text-xl font-bold text-yellow-500 font-mono">00:00:00</div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-4">
                <button onclick="changePage('wallet')" class="glass-card p-6 text-center">📥<br><span class="text-[10px] font-bold uppercase mt-2 block">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass-card p-6 text-center">📤<br><span class="text-[10px] font-bold uppercase mt-2 block text-red-500">Withdraw</span></button>
            </div>
        </div>

        <div id="p-invest" class="page">
            <h2 class="text-xl font-bold mb-6 text-center italic">FLEET NODES (25)</h2>
            <div id="plans-list" class="space-y-3"></div>
        </div>

        <div id="p-activity" class="page">
            <h2 class="text-xl font-bold mb-6 italic">TRANSACTION LEDGER</h2>
            <div id="user-history" class="space-y-2"></div>
        </div>

        <div id="p-support" class="page">
            <div class="bg-blue-600 p-8 rounded-[2rem] text-center mb-6">
                <h3 class="font-bold text-xl mb-4 text-white">Official Group</h3>
                <a href="https://chat.whatsapp.com/GTV140" class="bg-white text-blue-600 px-6 py-3 rounded-full font-bold uppercase text-[10px]">Join WhatsApp Community</a>
            </div>
            <div class="glass-card p-6 text-[11px] text-gray-500">
                <p class="mb-2">● Withdrawals: 10AM - 6PM</p>
                <p class="mb-2">● Min Withdraw: ₨ 500</p>
                <p>● Support: Contact Admin in Group</p>
            </div>
        </div>

        <div id="p-wallet" class="page">
            <div class="glass-card p-8">
                <h3 class="text-center font-bold text-blue-400 mb-6 uppercase">Deposit Funds</h3>
                <div class="bg-black/40 p-4 rounded-xl mb-6 text-[10px] text-center border border-white/5">
                    EASYPAISA: 03379827882<br>JAZZCASH: 03705519562
                </div>
                <input type="number" id="dep-amount" placeholder="AMOUNT" class="w-full p-4 mb-4 text-center">
                <input type="text" id="dep-trx" placeholder="TRANSACTION TID" class="w-full p-4 mb-6 text-center uppercase">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 p-4 rounded-xl font-bold text-xs">SUBMIT PROOF</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <div class="glass-card p-8">
                <h3 class="text-center font-bold text-red-500 mb-6 uppercase">Extract Funds</h3>
                <input type="number" id="wd-amount" placeholder="MIN 500" class="w-full p-4 mb-4 text-center">
                <input type="text" id="wd-method" placeholder="A/C DETAILS" class="w-full p-4 mb-6 text-center">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 p-4 rounded-xl font-bold text-xs">REQUEST PAYOUT</button>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden glass-card mx-6 my-4 p-4 flex justify-around items-center fixed bottom-0 left-0 right-0 z-[3000] bg-black/80 backdrop-blur-xl">
        <button onclick="changePage('home')" id="n-home" class="active-nav text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-2xl">📊</button>
        <button onclick="changePage('activity')" id="n-activity" class="text-2xl">📜</button>
        <button onclick="changePage('support')" id="n-support" class="text-2xl">🎧</button>
    </nav>

    <div id="admin-panel" class="fixed inset-0 bg-black z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8"><h2 class="text-xl font-bold text-blue-500 italic">SYSTEM TERMINAL</h2><button onclick="closeAdmin()" class="text-red-500">CLOSE</button></div>
        
        <div class="glass-card p-6 mb-6">
            <h3 class="text-[10px] font-bold text-gray-500 uppercase mb-4">Manual Adjustment</h3>
            <input type="text" id="adm-user" placeholder="USER ID" class="w-full p-3 mb-2 text-xs uppercase">
            <input type="number" id="adm-val" placeholder="AMOUNT" class="w-full p-3 mb-4 text-xs">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="manualEdit('add')" class="bg-green-600 p-3 rounded-xl text-[10px] font-bold">ADD BAL</button>
                <button onclick="manualEdit('rem')" class="bg-red-600 p-3 rounded-xl text-[10px] font-bold">REMOVE BAL</button>
            </div>
        </div>

        <h3 class="text-sm font-bold text-yellow-500 mb-4 uppercase italic">Pending Requests</h3>
        <div id="adm-requests" class="space-y-3 mb-10"></div>
        
        <h3 class="text-sm font-bold text-blue-500 mb-4 uppercase italic">Live Agent List</h3>
        <div id="adm-users-list" class="space-y-2 pb-20"></div>
    </div>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        
        let user = null, tapCount = 0, timerInt = null;

        // Generate 25 Plans: 20 Basic + 5 Premium
        const plans = []; 
        for(let i=1; i<=25; i++) { 
            let price = (i === 1) ? 200 : (i <= 20) ? (i * 500) : (i * 2000);
            plans.push({ id:`p${i}`, n: i > 20 ? "Premium Node" : "Standard Node", p: price, r: 4 + (i * 0.3) }); 
        }

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase();
            if(!name) return;
            const ref = db.collection("users").doc(name);
            const doc = await ref.get();
            if(!doc.exists) { await ref.set({ name: name, balance: 0, activePlanId: null, lastRequestTime: Date.now() }); }
            localStorage.setItem('mc_auth', name);
            ref.onSnapshot(d => { user = d.data(); if(user) { document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString(); renderPlans(); startCountdown(); checkAutoProfit(); } });
            document.getElementById('auth-ui').classList.add('hidden'); 
            document.getElementById('app-ui').classList.remove('hidden'); 
            document.getElementById('bottom-nav').classList.remove('hidden');
            syncHistory();
            syncGlobalNote();
        }

        function renderPlans() {
            const l = document.getElementById('plans-list'); if(!l) return; l.innerHTML = '';
            plans.forEach(p => {
                const isOwned = user.activePlanId === p.id;
                l.innerHTML += `<div onclick="${isOwned?'':`buy(${p.p},'${p.id}',${p.r})`}" class="glass-card p-6 flex justify-between items-center ${isOwned?'border-blue-500 border-2 bg-blue-500/10':''}">
                    <div><h4 class="font-bold text-[10px] uppercase text-blue-400">${p.n} ${p.id.substring(1)}</h4><p class="text-[8px] text-gray-500">${p.r.toFixed(1)}% Daily Profit</p></div>
                    <div class="font-bold text-lg italic">₨ ${p.p.toLocaleString()}</div>
                </div>`;
            });
        }

        async function buy(p, id, rate) {
            if(user.balance < p) return alert("Sweetie, balance is low!");
            if(user.activePlanId) return alert("Finish current node first!");
            await db.collection("users").doc(user.name).update({ balance: user.balance-p, activePlanId: id, dailyRate: rate, lastPrice: p, lastRequestTime: Date.now() });
        }

        function startCountdown() {
            if(!user.activePlanId) return;
            document.getElementById('timer-box').classList.remove('hidden');
            if(timerInt) clearInterval(timerInt);
            timerInt = setInterval(() => {
                const diff = (user.lastRequestTime + 86400000) - Date.now();
                const cd = document.getElementById('countdown');
                if(diff <= 0) { cd.innerText = "SIGNAL READY! ⚡"; return; }
                const h = Math.floor(diff/3600000), m = Math.floor((diff%3600000)/60000), s = Math.floor((diff%60000)/1000);
                cd.innerText = `${String(h).padStart(2,'0')}:${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
            }, 1000);
        }

        async function checkAutoProfit() {
            if(!user.activePlanId || Date.now() - user.lastRequestTime < 86400000) return;
            const amt = Math.floor((user.dailyRate/100)*user.lastPrice);
            await db.collection("requests").add({ user: user.name, amount: amt, type: "NODE PROFIT", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ lastRequestTime: Date.now() });
        }

        function syncHistory() {
            db.collection("requests").where("user", "==", user.name).orderBy("time", "desc").onSnapshot(snap => {
                const list = document.getElementById('user-history'); if(!list) return;
                list.innerHTML = '';
                snap.forEach(d => { const r = d.data(); list.innerHTML += `<div class="glass-card p-4 flex justify-between items-center text-[10px] font-bold uppercase mb-1"><div>${r.type}<br><span class="opacity-30 text-[7px]">${new Date(r.time).toLocaleString()}</span></div><div class="text-right">₨ ${r.amount}<br><span class="${r.status==='approved'?'text-green-500':'text-yellow-500'} text-[8px]">${r.status}</span></div></div>`; });
            });
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-nav')); document.getElementById('n-'+p)?.classList.add('active-nav'); }
        
        // ADMIN CORE
        function adminTap() { tapCount++; if(tapCount>=5) { if(prompt("Security Key:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        
        function syncAdmin() {
            db.collection("requests").where("status","==","pending").onSnapshot(snap => {
                const l = document.getElementById('adm-requests'); l.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); l.innerHTML += `<div class="glass-card p-4 text-[10px] mb-2 flex justify-between items-center border-l-4 border-yellow-500"><div>${d.user}<br>${d.type} - ₨ ${d.amount}</div><button onclick="hndl('${doc.id}','${d.user}',${d.amount})" class="bg-green-600 px-4 py-2 rounded-xl text-[8px] font-bold">APPROVE</button></div>`; });
            });
            db.collection("users").onSnapshot(snap => {
                const l = document.getElementById('adm-users-list'); l.innerHTML = '';
                snap.forEach(doc => { const u = doc.data(); l.innerHTML += `<div class="glass-card p-4 flex justify-between items-center text-[9px] font-bold"><div>${u.name} (₨ ${u.balance})</div><button onclick="db.collection('users').doc('${u.name}').delete()" class="text-red-500 text-xs">DEL</button></div>`; });
            });
        }

        async function hndl(id, u, amt) { const ref = db.collection("users").doc(u); const d = (await ref.get()).data(); await ref.update({ balance: d.balance + amt }); await db.collection("requests").doc(id).update({ status: 'approved' }); }
        async function manualEdit(act) { const u = document.getElementById('adm-user').value.toUpperCase(), v = parseInt(document.getElementById('adm-val').value); const ref = db.collection("users").doc(u); const d = (await ref.get()).data(); await ref.update({ balance: act==='add'?(d.balance+v):(d.balance-v) }); alert("Updated!"); }
        
        async function submitWithdraw() { const a = parseInt(document.getElementById('wd-amount').value), m = document.getElementById('wd-method').value; if(a < 500 || user.balance < a) return alert("Check Balance/Min Limit!"); await db.collection("requests").add({ user: user.name, amount: a, details: m, type: "WITHDRAW", status: "pending", time: Date.now() }); await db.collection("users").doc(user.name).update({ balance: user.balance - a }); alert("Withdrawal Requested!"); changePage('home'); }
        async function submitDeposit() { const a = document.getElementById('dep-amount').value, t = document.getElementById('dep-trx').value; await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, type: "DEPOSIT", status: "pending", time: Date.now() }); alert("Deposit Sent for Review!"); changePage('home'); }
        function syncGlobalNote() { db.collection("settings").doc("broadcast").onSnapshot(d => { const bar = document.getElementById('global-note'); if(d.exists && d.data().msg) { bar.classList.remove('hidden'); document.getElementById('note-text').innerText = d.data().msg; } }); }

        window.onload = () => { const s = localStorage.getItem('mc_auth'); if(s) { document.getElementById('user-name').value = s; login(); } };
    </script>
</body>
</html>
