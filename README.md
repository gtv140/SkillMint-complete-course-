<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Pro Edition</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;600;700&display=swap');
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Space Grotesk', sans-serif; }
        body { background: #000; color: #fff; height: 100vh; overflow: hidden; display: flex; flex-direction: column; }
        .glass-card { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(15px); border: 1px solid rgba(255, 255, 255, 0.08); border-radius: 1.5rem; }
        .page-container { flex: 1; overflow-y: auto; padding-bottom: 120px; }
        .page { display: none; padding: 20px; animation: slideUp 0.4s ease; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .active-nav { color: #3b82f6; transform: translateY(-5px); text-shadow: 0 0 15px #3b82f6; }
        .ticker-bg { background: linear-gradient(90deg, #1e3a8a, #3b82f6); }
        input { background: rgba(255,255,255,0.05) !important; border: 1px solid rgba(255,255,255,0.1) !important; color: white !important; border-radius: 12px !important; }
    </style>
</head>
<body>

    <div class="ticker-bg py-1 overflow-hidden whitespace-nowrap z-[3000]">
        <div id="ticker-text" class="inline-block animate-marquee text-[9px] font-bold uppercase tracking-widest px-4">
            User A***D just withdrew ₨ 1,250 | New Node 14 Activated by Agent Z***N | Bonus ₨ 50 Credited to ID 992...
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[4000] bg-black flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-4xl font-bold mb-8 italic tracking-tighter">MINT<span class="text-blue-500">CREST</span></h1>
            <div class="glass-card p-8 border-b-4 border-blue-600">
                <input type="text" id="user-name" placeholder="USERNAME" class="w-full p-4 mb-4 text-center font-bold uppercase outline-none">
                <input type="text" id="ref-by" placeholder="REFERRAL ID (OPTIONAL)" class="w-full p-4 mb-6 text-center text-[10px] uppercase outline-none">
                <button onclick="login()" class="w-full bg-blue-600 p-4 rounded-xl font-bold uppercase text-xs tracking-widest active:scale-95 shadow-lg shadow-blue-600/30">Access Vault</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden page-container">
        
        <div id="p-home" class="page active-page">
            <div class="glass-card p-8 mb-6 border-l-8 border-blue-500 shadow-2xl relative">
                <p class="text-[10px] text-blue-400 font-bold uppercase mb-1">Current Capital</p>
                <h2 class="text-5xl font-bold tracking-tighter" id="v-bal">₨ 0</h2>
                
                <div class="mt-6 flex gap-2">
                    <button onclick="claimDaily()" id="daily-btn" class="flex-1 bg-yellow-500/10 border border-yellow-500/30 p-3 rounded-xl text-[9px] font-bold text-yellow-500 uppercase">🎁 Daily Check-in</button>
                    <div id="timer-box" class="flex-1 bg-white/5 p-3 rounded-xl text-center border border-white/10 hidden">
                        <div id="countdown" class="text-xs font-bold text-blue-400 font-mono">00:00:00</div>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('wallet')" class="glass-card p-6 text-center active:scale-95">📥<br><span class="text-[10px] font-bold uppercase mt-2 block">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass-card p-6 text-center active:scale-95 text-red-400">📤<br><span class="text-[10px] font-bold uppercase mt-2 block">Withdraw</span></button>
            </div>

            <div class="glass-card p-5 mb-4 flex justify-between items-center">
                <div><p class="text-[8px] text-gray-500 font-bold uppercase">Your Team ID</p><p id="share-link" class="text-xs font-bold text-blue-400 uppercase">---</p></div>
                <div class="text-right"><p class="text-[8px] text-gray-500 font-bold uppercase">Referral Bonus</p><p class="text-xs font-bold text-green-500">₨ 50 / User</p></div>
            </div>
        </div>

        <div id="p-invest" class="page">
            <h2 class="text-xl font-bold mb-6 text-center italic uppercase tracking-widest text-blue-500">Market Nodes</h2>
            <div id="plans-list" class="space-y-3"></div>
        </div>

        <div id="p-activity" class="page">
            <h2 class="text-xl font-bold mb-6 italic uppercase">Digital Ledger</h2>
            <div id="user-history" class="space-y-2"></div>
        </div>

        <div id="p-support" class="page">
            <div class="bg-blue-600 p-8 rounded-[2.5rem] mb-6 text-center shadow-xl">
                <h4 class="text-[10px] uppercase font-bold mb-2 opacity-70">Community Link</h4>
                <a href="https://chat.whatsapp.com/GTV140" class="bg-white text-blue-600 px-8 py-3 rounded-full font-bold uppercase text-[10px] inline-block">Join WhatsApp Group</a>
            </div>
            
            <div class="glass-card p-5 space-y-4">
                <button onclick="logout()" class="w-full text-left text-red-500 font-bold text-[10px] uppercase flex justify-between items-center">
                    Logout Session <span>⏻</span>
                </button>
            </div>
        </div>

        <div id="p-wallet" class="page">
            <div class="glass-card p-8">
                <h3 class="text-center font-bold text-blue-400 mb-6 uppercase">Top-up Account</h3>
                <div class="bg-blue-600/10 p-4 rounded-xl mb-6 text-[10px] text-center border border-blue-500/20">
                    EASYPAISA: 03379827882<br>JAZZCASH: 03705519562
                </div>
                <input type="number" id="dep-amount" placeholder="PKR AMOUNT" class="w-full p-4 mb-4 text-center">
                <input type="text" id="dep-trx" placeholder="TRANSACTION ID" class="w-full p-4 mb-6 text-center uppercase">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 p-4 rounded-xl font-bold uppercase text-[10px]">Verify Deposit</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <div class="glass-card p-8">
                <h3 class="text-center font-bold text-red-500 mb-6 uppercase">Withdrawal</h3>
                <input type="number" id="wd-amount" placeholder="MINIMUM 100" class="w-full p-4 mb-4 text-center">
                <input type="text" id="wd-method" placeholder="ACCOUNT DETAILS" class="w-full p-4 mb-6 text-center uppercase">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 p-4 rounded-xl font-bold uppercase text-[10px]">Send Request</button>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden glass-card mx-6 my-4 p-4 flex justify-around items-center fixed bottom-0 left-0 right-0 z-[3000] bg-black/80 backdrop-blur-xl border-white/10">
        <button onclick="changePage('home')" id="n-home" class="active-nav text-2xl transition-all">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-2xl transition-all">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="text-2xl transition-all">📜</button>
        <button onclick="changePage('support')" id="n-support" class="text-2xl transition-all">⚙️</button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        
        let user = null, timerInt = null;
        const plans = []; for(let i=1; i<=25; i++) { let prc = (i===1)?200:i*1000; plans.push({ id:`p${i}`, n: i>20?"Elite":"Standard", p: prc, r: 4+(i*0.4) }); }

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase();
            const refID = document.getElementById('ref-by').value.trim().toUpperCase();
            if(!name) return;
            const ref = db.collection("users").doc(name);
            const doc = await ref.get();
            if(!doc.exists) { 
                await ref.set({ name: name, balance: 0, activePlanId: null, lastRequestTime: Date.now(), referredBy: refID || null, lastCheckin: 0 }); 
                if(refID && refID !== name) {
                    const rDoc = await db.collection("users").doc(refID).get();
                    if(rDoc.exists) await db.collection("requests").add({ user: refID, amount: 50, type: "REFERRAL BONUS", status: "pending", time: Date.now() });
                }
            }
            localStorage.setItem('mc_auth', name);
            ref.onSnapshot(d => { user = d.data(); if(user) { 
                document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
                document.getElementById('share-link').innerText = user.name;
                renderPlans(); startCountdown(); checkAutoProfit(); 
            } });
            document.getElementById('auth-ui').classList.add('hidden'); 
            document.getElementById('app-ui').classList.remove('hidden'); 
            document.getElementById('bottom-nav').classList.remove('hidden');
            syncHistory(); startFakeTicker();
        }

        async function claimDaily() {
            const now = Date.now();
            if(now - (user.lastCheckin || 0) < 86400000) return alert("Sweetie, come back tomorrow!");
            await db.collection("users").doc(user.name).update({ balance: user.balance + 10, lastCheckin: now });
            await db.collection("requests").add({ user: user.name, amount: 10, type: "DAILY BONUS", status: "approved", time: now });
            alert("₨ 10 Bonus Added!");
        }

        function renderPlans() {
            const l = document.getElementById('plans-list'); if(!l) return; l.innerHTML = '';
            plans.forEach(p => {
                const isOwned = user.activePlanId === p.id;
                l.innerHTML += `<div onclick="${isOwned?'':`buy(${p.p},'${p.id}',${p.r})`}" class="glass-card p-6 flex justify-between items-center ${isOwned?'border-blue-500 border-2 bg-blue-500/10':''}">
                    <div><h4 class="font-bold text-[10px] uppercase text-blue-400">Node ${p.id.substring(1)}</h4><p class="text-[8px] text-gray-500 font-bold">${p.r.toFixed(1)}% PROFIT</p></div>
                    <div class="font-bold text-lg italic">₨ ${p.p.toLocaleString()}</div>
                </div>`;
            });
        }

        async function buy(p, id, rate) {
            if(user.balance < p) return alert("Sweetie, low balance!");
            if(user.activePlanId) return alert("Finish current mining cycle!");
            await db.collection("users").doc(user.name).update({ balance: user.balance-p, activePlanId: id, dailyRate: rate, lastPrice: p, lastRequestTime: Date.now() });
            alert("Mining Started!");
        }

        function syncHistory() {
            db.collection("requests").where("user", "==", user.name).orderBy("time", "desc").onSnapshot(snap => {
                const list = document.getElementById('user-history'); if(!list) return;
                list.innerHTML = '';
                snap.forEach(d => { const r = d.data(); list.innerHTML += `<div class="glass-card p-4 flex justify-between items-center text-[10px] font-bold uppercase mb-1"><div>${r.type}<br><span class="opacity-25 text-[7px]">${new Date(r.time).toLocaleString()}</span></div><div class="text-right">₨ ${r.amount}<br><span class="${r.status==='approved'?'text-green-500':'text-yellow-500'} text-[8px]">${r.status}</span></div></div>`; });
            });
        }

        function startFakeTicker() {
            const names = ["Ahmed", "Sajid", "Sara", "Ali", "Zain", "Hassan", "Bilal"];
            setInterval(() => {
                const name = names[Math.floor(Math.random()*names.length)];
                const amt = [500, 1000, 2500, 150, 200, 5000][Math.floor(Math.random()*6)];
                document.getElementById('ticker-text').innerText = `User ${name}*** just withdrew ₨ ${amt} | Node 12 Activated by ID 882... | Processing daily profits...`;
            }, 5000);
        }

        async function submitWithdraw() {
            const a = parseInt(document.getElementById('wd-amount').value), m = document.getElementById('wd-method').value;
            if(a < 100 || user.balance < a) return alert("Check Limit (100) / Balance!");
            await db.collection("requests").add({ user: user.name, amount: a, details: m, type: "WITHDRAW", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - a });
            alert("Requested!"); changePage('home');
        }

        function logout() { localStorage.removeItem('mc_auth'); location.reload(); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-nav')); document.getElementById('n-'+p)?.classList.add('active-nav'); }
        async function submitDeposit() { const a = document.getElementById('dep-amount').value, t = document.getElementById('dep-trx').value; await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, type: "DEPOSIT", status: "pending", time: Date.now() }); alert("Sent!"); changePage('home'); }
        async function checkAutoProfit() { if(!user.activePlanId || Date.now() - user.lastRequestTime < 86400000) return; const amt = Math.floor((user.dailyRate/100)*user.lastPrice); await db.collection("requests").add({ user: user.name, amount: amt, type: "NODE PROFIT", status: "pending", time: Date.now() }); await db.collection("users").doc(user.name).update({ lastRequestTime: Date.now() }); }
        function startCountdown() { if(!user.activePlanId) return; document.getElementById('timer-box').classList.remove('hidden'); if(timerInt) clearInterval(timerInt); timerInt = setInterval(() => { const diff = (user.lastRequestTime + 86400000) - Date.now(); const cd = document.getElementById('countdown'); if(diff <= 0) { cd.innerText = "SIGNAL READY!"; return; } const h = Math.floor(diff/3600000), m = Math.floor((diff%3600000)/60000), s = Math.floor((diff%60000)/1000); cd.innerText = `${String(h).padStart(2,'0')}:${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`; }, 1000); }
        window.onload = () => { const s = localStorage.getItem('mc_auth'); if(s) { document.getElementById('user-name').value = s; login(); } };
    </script>
</body>
</html>
