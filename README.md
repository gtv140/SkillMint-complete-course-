<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Ultimate Control</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000205; color: white; overflow-x: hidden; -webkit-tap-highlight-color: transparent; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: fadeIn 0.3s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }
        ::-webkit-scrollbar { width: 0px; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div class="p-5 flex justify-between items-center border-b border-white/5 bg-black/80 backdrop-blur-lg sticky top-0 z-[500]">
        <h1 onclick="adminTap()" class="text-xl font-black italic tracking-tighter uppercase">MINT<span class="text-blue-500 text-2xl">CREST</span></h1>
        <div class="flex gap-2">
            <button onclick="toggleLang()" class="bg-white/5 border border-white/10 px-3 py-1.5 rounded-xl text-[9px] font-black uppercase">Language</button>
            <div id="v-status" class="bg-blue-600/20 text-blue-400 border border-blue-500/20 px-3 py-1.5 rounded-xl text-[9px] font-black uppercase italic">Standard</div>
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000205] flex items-center justify-center p-8">
        <div class="w-full max-w-sm">
            <div class="text-center mb-10">
                <h1 class="text-5xl font-black italic tracking-tighter uppercase text-blue-500">GOLD</h1>
                <p class="text-[8px] text-gray-500 uppercase tracking-[0.5em]">The Asset Vault</p>
            </div>
            <div class="glass p-8 rounded-[3rem] border-t-2 border-blue-600">
                <input type="text" id="user-name" placeholder="USERNAME" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-4 uppercase focus:border-blue-500">
                <input type="text" id="ref-code" placeholder="REFERRAL (OPTIONAL)" class="w-full bg-white/5 p-4 rounded-xl border border-white/5 outline-none text-center text-[9px] mb-8 uppercase text-gray-400">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[11px] uppercase tracking-widest active:scale-95 shadow-xl shadow-blue-600/20">Authorized Login</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600 shadow-2xl relative">
                <p class="text-[10px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Global Balance</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div id="timer-box" class="mt-8 hidden bg-blue-600/5 p-4 rounded-3xl border border-blue-500/10">
                    <p class="text-[8px] text-gray-500 uppercase font-black">Next Signal Pulse:</p>
                    <div id="countdown" class="text-2xl font-black text-yellow-500 italic tracking-widest font-mono">00:00:00</div>
                </div>
            </div>

            <div class="glass p-5 rounded-[2rem] mb-6 flex gap-2">
                <input type="text" id="promo-input" placeholder="PROMO CODE" class="flex-1 bg-white/5 p-3 rounded-xl border border-white/5 text-center text-[10px] uppercase outline-none font-bold">
                <button onclick="applyPromo()" class="bg-blue-600 px-6 py-3 rounded-xl text-[9px] font-black uppercase">Apply</button>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2rem] text-center active:scale-95"><span class="text-2xl block mb-2">📥</span><span class="text-[10px] font-black uppercase tracking-widest">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2rem] text-center active:scale-95"><span class="text-2xl block mb-2">📤</span><span class="text-[10px] font-black uppercase tracking-widest text-red-500">Withdraw</span></button>
                <button onclick="changePage('invest')" class="glass p-8 rounded-[2rem] text-center active:scale-95"><span class="text-2xl block mb-2">📈</span><span class="text-[10px] font-black uppercase tracking-widest text-blue-400">Fleets</span></button>
                <button onclick="changePage('more')" class="glass p-8 rounded-[2rem] text-center active:scale-95"><span class="text-2xl block mb-2">⚙️</span><span class="text-[10px] font-black uppercase tracking-widest text-gray-400">Utility</span></button>
            </div>
        </div>

        <div id="p-invest" class="page p-6 pb-20">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-500 text-2xl tracking-widest">Investment Fleets</h2>
            <div id="plans-list" class="space-y-3"></div>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3rem] border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black text-blue-500 mb-8 uppercase italic text-center tracking-widest">Inject Capital</h3>
                <div class="bg-black/50 p-4 rounded-xl border border-white/5 mb-6 text-[10px] font-mono text-blue-300">
                    EASYPAISA: 03379827882<br>JAZZCASH: 03705519562
                </div>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold border border-white/5">
                <input type="text" id="dep-trx" placeholder="Transaction ID" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center font-bold border border-white/5 uppercase">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase shadow-xl">Verify Injection</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3rem] border-t-8 border-red-600 shadow-2xl text-center">
                <h3 class="font-black text-red-500 mb-8 uppercase italic tracking-widest">Extraction Request</h3>
                <input type="number" id="wd-amount" placeholder="Min Amount: 500" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold border border-white/5 outline-none">
                <input type="text" id="wd-method" placeholder="Account Details" class="w-full bg-white/5 p-5 rounded-2xl mb-10 text-center font-bold border border-white/5 uppercase outline-none">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-5 rounded-2xl font-black text-[10px] uppercase shadow-xl shadow-red-600/20">Extract Funds</button>
            </div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400 tracking-widest">Account Ledger</h2>
            <div id="user-history" class="space-y-2 pb-24"></div>
        </div>

        <div id="p-more" class="page p-6 text-center">
            <div class="glass p-10 rounded-[3rem] mb-10">
                <div id="wheel" class="text-7xl mb-8 transition-all duration-[4s] inline-block">🎡</div><br>
                <button onclick="spinNow()" class="bg-yellow-600 px-10 py-4 rounded-2xl font-black text-[10px] uppercase shadow-lg shadow-yellow-600/20">Luck Extract</button>
            </div>
            <button onclick="logout()" class="text-red-500 text-[10px] font-black uppercase tracking-[0.4em] opacity-30 hover:opacity-100 transition-all">Terminate Agent Session</button>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden glass p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[400] rounded-t-[4rem] border-t border-white/10">
        <button onclick="changePage('home')" id="n-home" class="active-tab text-3xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-3xl">📉</button>
        <button onclick="changePage('activity')" id="n-activity" class="text-3xl">📜</button>
        <button onclick="changePage('more')" id="n-more" class="text-3xl">⚙️</button>
    </nav>

    <div id="admin-panel" class="fixed inset-0 bg-[#000205] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8 border-b border-white/10 pb-4">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Super Control</h2>
            <button onclick="closeAdmin()" class="bg-red-500/10 text-red-500 px-5 py-2 rounded-xl text-[10px] font-black uppercase border border-red-500/20">Exit Console</button>
        </div>

        <div class="glass p-6 rounded-3xl mb-8 border border-blue-500/20">
            <h3 class="text-[10px] font-black uppercase text-blue-400 mb-4 tracking-tighter italic">Manual Balance Override</h3>
            <input type="text" id="adm-user" placeholder="USERNAME" class="w-full bg-white/5 p-3 rounded-xl mb-2 text-[10px] font-bold uppercase border border-white/5 outline-none">
            <input type="number" id="adm-val" placeholder="AMOUNT" class="w-full bg-white/5 p-3 rounded-xl mb-4 text-[10px] font-bold border border-white/5 outline-none">
            <div class="flex gap-2">
                <button onclick="manualEdit('add')" class="flex-1 bg-green-600 py-3 rounded-xl text-[9px] font-black">ADD CASH</button>
                <button onclick="manualEdit('rem')" class="flex-1 bg-red-600 py-3 rounded-xl text-[9px] font-black">SUBTRACT</button>
            </div>
        </div>

        <h3 class="text-[10px] font-black uppercase text-yellow-500 mb-4 px-2 italic">Awaiting Approval</h3>
        <div id="adm-requests" class="space-y-3 mb-10"></div>

        <h3 class="text-[10px] font-black uppercase text-blue-500 mb-4 px-2 italic">Active Agents</h3>
        <div id="adm-users-list" class="space-y-2"></div>
    </div>

    <script>
        // CONFIG
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        
        let user = null, tapCount = 0, timerInt = null, lang = "en";

        // 25 PLANS GENERATED
        const plans = [];
        for(let i=1; i<=25; i++) {
            plans.push({ id:`p${i}`, n: i<=5?"Bronze":i<=15?"Silver":i<=20?"Gold":"VIP", p: i*500, r: 5+(i*0.5) });
        }

        window.onload = () => { if(localStorage.getItem('mc_auth')) { document.getElementById('user-name').value = localStorage.getItem('mc_auth'); login(); } };

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase();
            if(!name) return;
            try {
                const ref = db.collection("users").doc(name); const doc = await ref.get();
                if(!doc.exists) {
                    await ref.set({ name: name, balance: 0, profit: 0, lastSpin: 0, activePlanId: null, lastRequestTime: Date.now(), referredBy: document.getElementById('ref-code').value || "NONE" });
                }
                localStorage.setItem('mc_auth', name);
                ref.onSnapshot(d => { user = d.data(); updateUI(); startCountdown(); checkAndSendProfit(); });
                document.getElementById('auth-ui').classList.add('hidden'); document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
                renderPlans(); syncHistory();
            } catch(e) { alert("Auth Error, sweetie!"); }
        }

        function updateUI() {
            document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
            const badge = document.getElementById('v-status');
            if(user.balance > 20000) badge.innerText = "DIAMOND"; else if(user.balance > 5000) badge.innerText = "GOLD";
        }

        function renderPlans() {
            const l = document.getElementById('plans-list'); l.innerHTML = '';
            plans.forEach(p => {
                const isOwned = user.activePlanId === p.id;
                l.innerHTML += `<div onclick="${isOwned?'':`buy(${p.p},'${p.n}',${p.r},'${p.id}')`}" class="glass p-6 rounded-[2rem] flex justify-between items-center ${isOwned?'border-blue-500 border-2':''}">
                    <div><h4 class="font-black text-[9px] text-blue-400 uppercase tracking-tighter">${p.n} ${p.id.substring(1)} ${isOwned?'(ACTIVE)':''}</h4><p class="text-[7px] text-gray-500 font-bold uppercase">${p.r}% Daily Profit</p></div>
                    <div class="text-right font-black italic">₨ ${p.p.toLocaleString()}</div>
                </div>`;
            });
        }

        async function buy(p, n, rate, id) {
            if(user.activePlanId) return alert("One plan at a time, sweetie!");
            if(user.balance < p) return alert("Low balance!");
            if(confirm("Authorize "+n+" Activation?")) {
                await db.collection("users").doc(user.name).update({ balance: user.balance-p, activePlanId: id, tier: n, dailyRate: rate, lastPrice: p, lastRequestTime: Date.now() });
                alert("Fleet Engaged!");
            }
        }

        function startCountdown() {
            if(!user.activePlanId) return;
            document.getElementById('timer-box').classList.remove('hidden');
            if(timerInt) clearInterval(timerInt);
            timerInt = setInterval(() => {
                const diff = (user.lastRequestTime + 86400000) - Date.now();
                if(diff <= 0) { document.getElementById('countdown').innerText = "SIGNAL READY!"; return; }
                const h = Math.floor(diff/3600000), m = Math.floor((diff%3600000)/60000), s = Math.floor((diff%60000)/1000);
                document.getElementById('countdown').innerText = `${String(h).padStart(2,'0')}:${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
            }, 1000);
        }

        async function checkAndSendProfit() {
            if(!user.activePlanId || Date.now() - user.lastRequestTime < 86400000) return;
            const amt = Math.floor((user.dailyRate/100)*user.lastPrice);
            await db.collection("requests").add({ user: user.name, amount: amt, type: "PROFIT SIGNAL", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ lastRequestTime: Date.now() });
            alert("Daily profit signal sent, sweetie!");
        }

        async function applyPromo() {
            const code = document.getElementById('promo-input').value.trim();
            const snap = await db.collection("promos").doc(code).get();
            if(snap.exists && !snap.data().used) {
                await db.collection("users").doc(user.name).update({ balance: user.balance + snap.data().val });
                await db.collection("promos").doc(code).update({ used: true, by: user.name });
                alert("Promo Applied! Cash Added.");
            } else alert("Invalid/Used Code, sweetie!");
        }

        async function submitWithdraw() { const a = parseInt(document.getElementById('wd-amount').value), m = document.getElementById('wd-method').value; if(a < 500 || user.balance < a) return alert("Invalid amount!"); await db.collection("requests").add({ user: user.name, amount: a, details: m, type: "WITHDRAW", status: "pending", time: Date.now() }); await db.collection("users").doc(user.name).update({ balance: user.balance - a }); alert("Pending!"); changePage('home'); }
        async function submitDeposit() { const a = document.getElementById('dep-amount').value, t = document.getElementById('dep-trx').value; await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, type: "DEPOSIT", status: "pending", time: Date.now() }); alert("Sent!"); changePage('home'); }
        function spinNow() { if(Date.now() - (user.lastSpin || 0) < 86400000) return alert("Try tomorrow"); document.getElementById('wheel').style.transform = `rotate(${Math.floor(4000+Math.random()*4000)}deg)`; setTimeout(async () => { const win = Math.floor(Math.random()*200)+50; await db.collection("requests").add({ user: user.name, amount: win, type: "SPIN WIN", status: "pending", time: Date.now() }); await db.collection("users").doc(user.name).update({ lastSpin: Date.now() }); alert("Won Rs "+win); }, 4000); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('n-'+p).classList.add('active-tab'); }
        function syncHistory() { db.collection("requests").where("user", "==", user.name).orderBy("time", "desc").onSnapshot(snap => { const list = document.getElementById('user-history'); list.innerHTML = ''; snap.forEach(d => { const r = d.data(); list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center text-[8px] font-black uppercase mb-1"><div>${r.type}<br><span class="opacity-40 text-[6px]">${new Date(r.time).toLocaleString()}</span></div><div>₨ ${r.amount} <span class="${r.status==='approved'?'text-green-500':'text-yellow-500'}">${r.status}</span></div></div>`; }); }); }
        
        // ADMIN LOGIC
        function adminTap() { tapCount++; if(tapCount>=5) { if(prompt("Master Key:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function syncAdmin() { 
            db.collection("requests").where("status","==","pending").onSnapshot(snap => { const list = document.getElementById('adm-requests'); list.innerHTML = ''; snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-4 text-[9px] font-black uppercase mb-2 border-l-4 border-yellow-500 flex justify-between items-center"><div>${d.user}<br>${d.type} - Rs ${d.amount}<br>ID: ${d.tid||d.details||'NA'}</div><div class="flex gap-1"><button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-3 py-2 rounded-lg">YES</button><button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="bg-red-600 px-3 py-2 rounded-lg">NO</button></div></div>`; }); });
            db.collection("users").onSnapshot(snap => { const list = document.getElementById('adm-users-list'); list.innerHTML = ''; snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div class="glass p-3 rounded-xl flex justify-between items-center text-[8px] font-black uppercase"><div>${u.name} (₨ ${u.balance})</div><button onclick="deleteUser('${u.name}')" class="text-red-500 text-[10px]">❌</button></div>`; }); });
        }
        async function handle(id, u, amt, act, type) { const ref = db.collection("users").doc(u); const doc = await ref.get(); if(act==='approved') { if(type !== "WITHDRAW") await ref.update({ balance: (doc.data().balance||0) + amt }); } else { if(type === "WITHDRAW") await ref.update({ balance: (doc.data().balance||0) + amt }); } await db.collection("requests").doc(id).update({ status: act }); }
        async function manualEdit(act) { const u = document.getElementById('adm-user').value.toUpperCase(), val = parseInt(document.getElementById('adm-val').value); if(!u || !val) return; const ref = db.collection("users").doc(u); const doc = await ref.get(); if(!doc.exists) return alert("User not found!"); await ref.update({ balance: act==='add' ? (doc.data().balance+val) : (doc.data().balance-val) }); alert("Updated!"); }
        async function deleteUser(n) { if(confirm("Delete Agent "+n+"?")) await db.collection("users").doc(n).delete(); }
        function logout() { localStorage.removeItem('mc_auth'); location.reload(); }
    </script>
</body>
</html>
