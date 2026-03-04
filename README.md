<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Pro | Modern Investment</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;700&display=swap');
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Outfit', sans-serif; }
        body { background: #050505; color: #fff; height: 100vh; overflow: hidden; display: flex; flex-direction: column; }
        .glass-card { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(20px); border: 1px solid rgba(255, 255, 255, 0.05); border-radius: 2rem; }
        .page-container { flex: 1; overflow-y: auto; padding-bottom: 100px; }
        .page { display: none; padding: 20px; animation: slideIn 0.4s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideIn { from { opacity: 0; transform: scale(0.95); } to { opacity: 1; transform: scale(1); } }
        .active-nav { color: #3b82f6; position: relative; }
        .active-nav::after { content: ''; position: absolute; bottom: -8px; width: 4px; height: 4px; background: #3b82f6; border-radius: 50%; left: 50%; transform: translateX(-50%); }
        .ticker-bar { background: linear-gradient(90deg, #1e40af, #3b82f6); height: 24px; font-size: 9px; }
        input { background: rgba(255,255,255,0.03) !important; border: 1px solid rgba(255,255,255,0.08) !important; color: white !important; border-radius: 15px !important; }
        .btn-gradient { background: linear-gradient(135deg, #3b82f6 0%, #1e40af 100%); transition: 0.3s; }
        .btn-gradient:active { transform: scale(0.96); }
    </style>
</head>
<body>

    <div class="ticker-bar flex items-center overflow-hidden z-[3000]">
        <div id="ticker" class="whitespace-nowrap px-4 font-bold uppercase tracking-widest animate-pulse">
            Loading Live Network Activity...
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[4000] bg-[#050505] flex items-center justify-center p-8">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-4xl font-bold mb-10 text-center tracking-tighter italic">MINT<span class="text-blue-500">CREST</span></h1>
            <div class="glass-card p-8 border-t border-blue-500/30">
                <input type="text" id="user-name" placeholder="AGENT USERNAME" class="w-full p-4 mb-4 text-center font-bold uppercase outline-none">
                <input type="text" id="ref-by" placeholder="REFERRAL ID (IF ANY)" class="w-full p-4 mb-8 text-center text-[10px] uppercase outline-none">
                <button onclick="login()" class="w-full btn-gradient p-4 rounded-2xl font-bold uppercase text-[11px] tracking-widest">Connect Device</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden page-container">
        
        <div id="p-home" class="page active-page">
            <div class="glass-card p-8 mb-6 relative overflow-hidden">
                <div class="absolute -top-10 -right-10 w-32 h-32 bg-blue-600/10 blur-3xl rounded-full"></div>
                <p class="text-[10px] text-gray-400 font-bold uppercase mb-2">Network Valuation</p>
                <h2 class="text-5xl font-bold tracking-tighter mb-4" id="v-bal">₨ 0</h2>
                <div class="flex gap-2">
                    <button onclick="claimDaily()" class="flex-1 bg-white/5 p-3 rounded-xl text-[9px] font-bold uppercase border border-white/5">🎁 Claim Bonus</button>
                    <button onclick="openRedeem()" class="flex-1 bg-blue-500/10 p-3 rounded-xl text-[9px] font-bold text-blue-400 uppercase border border-blue-500/20">🎫 Gift Code</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('wallet')" class="glass-card p-6 flex flex-col items-center">
                    <span class="text-2xl mb-2">📥</span>
                    <span class="text-[10px] font-bold uppercase">Inject Funds</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass-card p-6 flex flex-col items-center">
                    <span class="text-2xl mb-2">📤</span>
                    <span class="text-[10px] font-bold uppercase text-red-500">Payout</span>
                </button>
            </div>

            <div class="glass-card p-5 mb-4 flex justify-between items-center border-l-4 border-green-500">
                <div><p class="text-[8px] text-gray-500 uppercase font-bold">Account Integrity</p><p class="text-xs font-bold text-green-500 uppercase">Secure / Verified</p></div>
                <div class="text-right text-[10px] font-bold"><p id="share-link" class="text-blue-500 uppercase">---</p></div>
            </div>
        </div>

        <div id="p-invest" class="page">
            <h3 class="text-sm font-bold mb-6 uppercase tracking-widest opacity-50">Operational Nodes</h3>
            <div id="plans-list" class="space-y-4 pb-10"></div>
        </div>

        <div id="p-activity" class="page">
            <h3 class="text-sm font-bold mb-6 uppercase tracking-widest opacity-50">Transaction Logs</h3>
            <div id="user-history" class="space-y-3"></div>
        </div>

        <div id="p-support" class="page">
            <div class="glass-card p-6 mb-4">
                <h4 class="text-xs font-bold mb-4 uppercase text-blue-400 italic">Network Config</h4>
                <div class="space-y-3">
                    <a href="https://chat.whatsapp.com/GTV140" class="flex items-center justify-between p-4 bg-white/5 rounded-2xl text-[10px] font-bold">
                        JOIN WHATSAPP GROUP <span>→</span>
                    </a>
                    <button onclick="logout()" class="w-full flex items-center justify-between p-4 bg-red-500/5 text-red-500 rounded-2xl text-[10px] font-bold uppercase">
                        Terminate Session <span>⏻</span>
                    </button>
                </div>
            </div>
            <div class="p-6 text-center text-[8px] text-gray-600 uppercase font-bold">Build Version 4.0.2 Pro</div>
        </div>

        <div id="p-withdraw" class="page">
            <div class="glass-card p-8">
                <h3 class="text-center font-bold text-red-500 mb-8 uppercase italic">Request Extraction</h3>
                <input type="number" id="wd-amount" placeholder="MIN ₨ 100" class="w-full p-4 mb-4 text-center">
                <input type="text" id="wd-method" placeholder="A/C DETAILS" class="w-full p-4 mb-4 text-center">
                <input type="password" id="wd-pin" placeholder="SECURITY PIN" class="w-full p-4 mb-8 text-center">
                <button onclick="submitWithdraw()" class="w-full btn-gradient p-4 rounded-2xl font-bold uppercase text-[10px]">Verify & Send</button>
            </div>
        </div>

        <div id="p-wallet" class="page">
            <div class="glass-card p-8 text-center">
                <h3 class="font-bold text-blue-400 mb-6 uppercase">Injection Protocol</h3>
                <div class="bg-black/40 p-5 rounded-3xl mb-6 text-[11px] font-bold border border-white/5">
                    EASYPAISA: 03379827882<br>JAZZCASH: 03705519562
                </div>
                <input type="number" id="dep-amount" placeholder="PKR AMOUNT" class="w-full p-4 mb-4 text-center">
                <input type="text" id="dep-trx" placeholder="TRANSACTION TID" class="w-full p-4 mb-8 text-center uppercase">
                <button onclick="submitDeposit()" class="w-full btn-gradient p-4 rounded-2xl font-bold uppercase text-[10px]">Authorize</button>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden glass-card mx-8 my-6 p-4 flex justify-around items-center fixed bottom-0 left-0 right-0 z-[3000] bg-black/60 backdrop-blur-2xl">
        <button onclick="changePage('home')" id="n-home" class="active-nav text-2xl transition-all">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-2xl transition-all">⚙️</button>
        <button onclick="changePage('activity')" id="n-activity" class="text-2xl transition-all">📜</button>
        <button onclick="changePage('support')" id="n-support" class="text-2xl transition-all">👤</button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        
        let user = null;
        
        // RECONFIGURED PLANS (10 Small + 15 Large)
        const plans = [];
        for(let i=1; i<=25; i++) {
            let price = (i <= 10) ? i * 100 : (i - 10) * 2000;
            plans.push({ id:`p${i}`, n: i<=10?"Starter":"Alpha", p: price, r: 5+(i*0.4) });
        }

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase();
            const refID = document.getElementById('ref-by').value.trim().toUpperCase();
            if(!name) return;
            const ref = db.collection("users").doc(name);
            const doc = await ref.get();
            if(!doc.exists) { 
                await ref.set({ name: name, balance: 0, activePlanId: null, lastRequestTime: Date.now(), referredBy: refID || null, lastCheck: 0, pin: "0000" }); 
                if(refID && refID !== name) {
                    const rDoc = await db.collection("users").doc(refID).get();
                    if(rDoc.exists) await db.collection("requests").add({ user: refID, amount: 50, type: "REFERRAL BONUS", status: "pending", time: Date.now() });
                }
            }
            localStorage.setItem('mc_auth', name);
            ref.onSnapshot(d => { user = d.data(); if(user) { 
                document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
                document.getElementById('share-link').innerText = "UID: " + user.name;
                renderPlans(); startTicker();
            } });
            document.getElementById('auth-ui').classList.add('hidden'); 
            document.getElementById('app-ui').classList.remove('hidden'); 
            document.getElementById('bottom-nav').classList.remove('hidden');
            syncHistory();
        }

        function renderPlans() {
            const l = document.getElementById('plans-list'); if(!l) return; l.innerHTML = '';
            plans.forEach(p => {
                const isOwned = user.activePlanId === p.id;
                l.innerHTML += `<div onclick="${isOwned?'':`buy(${p.p},'${p.id}',${p.r})`}" class="glass-card p-6 flex justify-between items-center transition-all ${isOwned?'border-blue-500 bg-blue-500/5 border-2':''}">
                    <div><h4 class="font-bold text-[11px] uppercase text-blue-400 tracking-widest">${p.n} Node ${p.id.substring(1)}</h4><p class="text-[8px] text-gray-500 font-bold">${p.r.toFixed(1)}% DAILY PROFIT</p></div>
                    <div class="text-right"><p class="text-lg font-bold">₨ ${p.p.toLocaleString()}</p></div>
                </div>`;
            });
        }

        function startTicker() {
            const msgs = ["New Node 5 Activated by User J***D", "Withdrawal of ₨ 4,500 Approved for Agent A***", "Bonus Credit: ₨ 50 to User S***", "Liquidity Pool Stability: 99.8%"];
            setInterval(() => {
                document.getElementById('ticker').innerText = msgs[Math.floor(Math.random()*msgs.length)];
            }, 4000);
        }

        async function submitWithdraw() {
            const a = parseInt(document.getElementById('wd-amount').value), m = document.getElementById('wd-method').value, p = document.getElementById('wd-pin').value;
            if(a < 100 || user.balance < a) return alert("Sweetie, check balance or limit!");
            if(p !== user.pin) return alert("Incorrect Security PIN!");
            await db.collection("requests").add({ user: user.name, amount: a, details: m, type: "WITHDRAWAL", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - a });
            alert("Payout Authorized!"); changePage('home');
        }

        async function claimDaily() {
            const now = Date.now();
            if(now - (user.lastCheck || 0) < 86400000) return alert("Daily limit reached, sweetie!");
            await db.collection("users").doc(user.name).update({ balance: user.balance + 10, lastCheck: now });
            await db.collection("requests").add({ user: user.name, amount: 10, type: "DAILY REWARD", status: "approved", time: now });
            alert("₨ 10 Reward Collected!");
        }

        function openRedeem() { const code = prompt("Enter Gift Code:"); if(code === "MINT100") alert("Code Validated! (Processing)"); else alert("Invalid Code"); }
        function logout() { localStorage.removeItem('mc_auth'); location.reload(); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-nav')); document.getElementById('n-'+p)?.classList.add('active-nav'); }
        async function submitDeposit() { const a = document.getElementById('dep-amount').value, t = document.getElementById('dep-trx').value; await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, type: "DEPOSIT", status: "pending", time: Date.now() }); alert("Sent for Verification!"); changePage('home'); }
        async function buy(p, id, r) { if(user.balance < p) return alert("Low funds!"); await db.collection("users").doc(user.name).update({ balance: user.balance-p, activePlanId: id, dailyRate: r, lastPrice: p, lastRequestTime: Date.now() }); alert("Node Activated!"); }
        function syncHistory() { db.collection("requests").where("user", "==", user.name).orderBy("time", "desc").onSnapshot(snap => { const l = document.getElementById('user-history'); l.innerHTML = ''; snap.forEach(d => { const r = d.data(); l.innerHTML += `<div class="glass-card p-4 flex justify-between items-center text-[10px] font-bold uppercase mb-1"><div>${r.type}<br><span class="opacity-30 text-[7px]">${new Date(r.time).toLocaleTimeString()}</span></div><div class="text-right">₨ ${r.amount}<br><span class="${r.status==='approved'?'text-green-500':'text-yellow-500'} text-[8px]">${r.status}</span></div></div>`; }); }); }
        window.onload = () => { const s = localStorage.getItem('mc_auth'); if(s) { document.getElementById('user-name').value = s; login(); } };
    </script>
</body>
</html>
