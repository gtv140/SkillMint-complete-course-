<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Gold | Ultimate Asset Hub</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        
        :root { --bg: #ffffff; --card: #f8fafc; --text: #0f172a; --accent: #2563eb; --border: #e2e8f0; }
        .dark { --bg: #0b0e14; --card: #161b22; --text: #f0f6fc; --accent: #58a6ff; --border: #30363d; }

        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); transition: 0.3s; overflow-x: hidden; -webkit-tap-highlight-color: transparent; }
        
        .glass { background: var(--card); border: 1px solid var(--border); backdrop-filter: blur(10px); }
        .page { display: none; animation: slideUp 0.4s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        
        .nav-btn { opacity: 0.4; transition: 0.3s; }
        .active-tab { opacity: 1; color: var(--accent); }
        
        .status-pending { color: #eab308; }
        .status-approved { color: #22c55e; }
        .status-rejected { color: #ef4444; }

        input, textarea { background: var(--card); border: 1px solid var(--border); color: var(--text); }
        ::-webkit-scrollbar { display: none; }
        
        .ticker-wrap { background: var(--accent); color: white; padding: 4px 0; font-size: 9px; font-weight: 800; overflow: hidden; }
        .ticker { display: inline-block; white-space: nowrap; animation: ticker 25s linear infinite; }
        @keyframes ticker { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-[var(--bg)] flex flex-col items-center justify-center p-8 text-center">
        <div class="w-20 h-20 bg-blue-600 rounded-[2rem] flex items-center justify-center text-white text-4xl font-black mb-6 shadow-2xl">M</div>
        <h1 onclick="adminTap()" class="text-3xl font-black tracking-tighter mb-1 uppercase">MintCrest Gold</h1>
        <p class="text-slate-500 text-[10px] uppercase tracking-[0.3em] mb-12 font-bold">Secure Wealth Management</p>
        <div class="w-full max-w-xs space-y-4">
            <input type="text" id="user-name" placeholder="Username" class="w-full p-4 rounded-2xl text-center font-bold outline-none focus:ring-2 ring-blue-500 transition-all">
            <button onclick="login()" class="w-full bg-blue-600 py-4 rounded-2xl font-black text-[11px] uppercase text-white shadow-xl active:scale-95 transition-all">Establish Connection</button>
        </div>
    </section>

    <header class="p-5 flex justify-between items-center fixed top-0 w-full z-50 bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)]">
        <div class="flex items-center gap-2">
            <div class="w-8 h-8 bg-blue-600 rounded-xl flex items-center justify-center text-white font-black text-xs">M</div>
            <span class="font-extrabold text-sm tracking-tighter uppercase">MintCrest</span>
        </div>
        <div class="flex items-center gap-4">
            <button onclick="toggleTheme()" class="text-xl">🌓</button>
            <button onclick="logout()" class="text-[10px] font-black uppercase text-red-500">Exit</button>
        </div>
    </header>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pt-24 pb-32 px-5">
        
        <div id="p-home" class="page active-page">
            <div class="bg-blue-600 p-8 rounded-[2.5rem] mb-6 shadow-xl text-white relative overflow-hidden">
                <div class="relative z-10">
                    <p class="text-[10px] opacity-70 font-bold uppercase mb-1 tracking-widest">Active Balance</p>
                    <h2 class="text-4xl font-black tracking-tight" id="v-bal">₨ 0</h2>
                    <div class="mt-6 flex justify-between items-center bg-black/20 p-4 rounded-2xl">
                        <div>
                            <p class="text-[8px] uppercase font-bold opacity-60">Next Cycle</p>
                            <p id="profit-timer" class="text-xs font-black">00:00:00</p>
                        </div>
                        <button onclick="requestBonus()" class="bg-white text-blue-600 px-5 py-2 rounded-xl text-[9px] font-black uppercase shadow-lg active:scale-90">Bonus 🎁</button>
                    </div>
                </div>
                <div class="absolute -right-10 -top-10 w-40 h-40 bg-white/10 rounded-full blur-3xl"></div>
            </div>

            <div class="ticker-wrap rounded-xl mb-6">
                <div class="ticker uppercase italic">
                    • User @Ali_786 just withdrew ₨ 10,000 via SadaPay • System Status: Online • 30-Day Portfolios Active • Welcome to MintCrest Gold •
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-8">
                <button onclick="changePage('wallet')" class="glass p-6 rounded-[2rem] text-center shadow-sm active:scale-95 transition-all">
                    <span class="text-2xl block mb-2">📥</span>
                    <span class="text-[10px] font-black uppercase tracking-widest">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-6 rounded-[2rem] text-center shadow-sm active:scale-95 transition-all">
                    <span class="text-2xl block mb-2">📤</span>
                    <span class="text-[10px] font-black uppercase tracking-widest">Payout</span>
                </button>
            </div>

            <div class="glass p-4 rounded-2xl mb-8 flex gap-2">
                <input type="text" id="promo-input" placeholder="Enter Promo Code" class="flex-1 bg-transparent text-[10px] font-bold px-2 uppercase outline-none">
                <button onclick="submitPromo()" class="bg-blue-600 text-white px-6 py-2 rounded-xl text-[9px] font-black uppercase shadow-md">Apply</button>
            </div>

            <h3 class="text-[11px] font-black uppercase mb-4 opacity-50 px-2 tracking-[0.2em]">Transaction History</h3>
            <div id="history-list" class="space-y-3 pb-4">
                <p class="text-[10px] text-center py-10 opacity-30 font-bold uppercase italic">Establishing Records...</p>
            </div>
        </div>

        <div id="p-invest" class="page">
            <h3 class="text-center text-[10px] font-black uppercase tracking-[0.3em] mb-8 opacity-40 italic">30-Day Asset Managed Tiers</h3>
            <div id="plans-list" class="space-y-4 pb-10"></div>
        </div>

        <div id="p-wallet" class="page">
            <div class="glass p-8 rounded-[2.5rem]">
                <h3 class="font-black text-center mb-6 uppercase text-blue-500 text-sm tracking-widest">Deposit Node</h3>
                <div class="space-y-3 mb-8 text-[10px] font-bold">
                    <div class="flex justify-between p-4 bg-blue-500/5 rounded-2xl border border-blue-500/10"><span>SadaPay/JazzCash</span><span class="text-blue-500">03705519562</span></div>
                    <div class="flex justify-between p-4 bg-green-500/5 rounded-2xl border border-green-500/10"><span>EasyPaisa</span><span class="text-green-500">03379827882</span></div>
                </div>
                <div class="space-y-4">
                    <input type="number" id="d-amt" placeholder="Amount (PKR)" class="w-full p-4 rounded-xl text-center font-bold text-xs outline-none">
                    <input type="text" id="d-tid" placeholder="Transaction ID (TID)" class="w-full p-4 rounded-xl text-center font-bold uppercase text-xs outline-none">
                    <div>
                        <label class="text-[9px] font-black uppercase opacity-40 ml-2 mb-2 block">Upload Proof (Image)</label>
                        <input type="file" id="d-file" class="w-full text-[10px] p-4 glass rounded-xl">
                    </div>
                    <button id="btn-dep" onclick="subDep()" class="w-full bg-blue-600 text-white py-5 rounded-2xl font-black text-[11px] uppercase shadow-xl shadow-blue-500/20 active:scale-95 transition-all">Submit Capital</button>
                </div>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <div class="glass p-8 rounded-[2.5rem]">
                <h3 class="font-black text-center mb-2 uppercase text-sm tracking-widest">Liquidation</h3>
                <p class="text-[8px] text-center opacity-40 mb-8 uppercase font-bold italic">24h Manual Verification Policy</p>
                <div class="space-y-4">
                    <input type="number" id="w-amt" placeholder="Amount" class="w-full p-4 rounded-xl text-center font-bold text-xs outline-none">
                    <input type="text" id="w-acc" placeholder="Account Details (Name/Number)" class="w-full p-4 rounded-xl text-center font-bold text-xs outline-none">
                    <button onclick="subWd()" class="w-full bg-slate-900 text-white py-5 rounded-2xl font-black text-[11px] uppercase shadow-xl active:scale-95 transition-all">Authorize Payout</button>
                </div>
            </div>
        </div>

        <div id="p-support" class="page">
            <div class="glass p-6 rounded-[2.5rem] flex flex-col h-[60vh]">
                <h3 class="font-black text-center mb-1 uppercase text-blue-500 text-sm">Direct Support</h3>
                <p class="text-[8px] text-center opacity-40 mb-6 uppercase font-bold">End-to-End Encrypted</p>
                <div id="sup-list" class="flex-1 overflow-y-auto space-y-3 mb-4 pr-2"></div>
                <div class="flex gap-2">
                    <input type="text" id="sup-msg" placeholder="Type message..." class="flex-1 p-4 rounded-xl text-[10px] font-bold outline-none">
                    <button onclick="sendMsg()" class="bg-blue-600 text-white px-6 rounded-xl font-black text-[10px] uppercase">Send</button>
                </div>
            </div>
        </div>

        <div id="p-info" class="page">
            <div class="space-y-6 pb-10">
                <div class="glass p-6 rounded-3xl">
                    <h4 class="text-blue-500 font-black text-[10px] uppercase mb-2 tracking-widest">About MintCrest Gold</h4>
                    <p class="text-[10px] leading-relaxed opacity-60 font-medium">Established in 2026, MintCrest Gold is a decentralized asset portal specializing in 30-day liquidity yields. We provide a safe haven for digital capital with manual auditing protocols to ensure 100% solvency.</p>
                </div>
                <div class="glass p-6 rounded-3xl">
                    <h4 class="text-blue-500 font-black text-[10px] uppercase mb-2 tracking-widest">Security Audit</h4>
                    <p class="text-[10px] leading-relaxed opacity-60 font-medium">Every transaction undergoes a three-layer verification process. Our servers use SSL encryption to protect user identity and financial records from third-party access.</p>
                </div>
            </div>
        </div>

    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[var(--bg)] z-[50000] hidden overflow-y-auto px-6 pt-12">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-2xl font-black text-blue-500 uppercase tracking-tighter">Command Hub</h2>
            <button onclick="closeAdmin()" class="bg-red-500 text-white px-6 py-2 rounded-xl text-[10px] font-black uppercase">Lock</button>
        </div>
        <div id="adm-req-list" class="space-y-4 pb-32"></div>
        <div class="fixed bottom-0 left-0 w-full p-6 bg-[var(--bg)] border-t border-[var(--border)] flex gap-2">
            <input type="text" id="adm-rep-u" placeholder="User" class="w-1/3 p-3 rounded-lg text-[10px] font-bold">
            <input type="text" id="adm-rep-m" placeholder="Reply Msg" class="flex-1 p-3 rounded-lg text-[10px] font-bold">
            <button onclick="adminReply()" class="bg-blue-600 text-white px-4 rounded-lg text-[10px] font-black uppercase">Send</button>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden bg-[var(--bg)]/95 backdrop-blur-md border-t border-[var(--border)] p-6 flex justify-around items-center fixed bottom-0 w-full z-[4000]">
        <button onclick="changePage('home')" id="n-home" class="nav-btn active-tab flex flex-col items-center">
            <span class="text-2xl">🏠</span>
            <span class="text-[8px] uppercase mt-2 font-black tracking-widest">Node</span>
        </button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-btn flex flex-col items-center">
            <span class="text-2xl">📈</span>
            <span class="text-[8px] uppercase mt-2 font-black tracking-widest">Tiers</span>
        </button>
        <button onclick="changePage('support')" id="n-support" class="nav-btn flex flex-col items-center">
            <span class="text-2xl">🎧</span>
            <span class="text-[8px] uppercase mt-2 font-black tracking-widest">Chat</span>
        </button>
        <button onclick="changePage('info')" id="n-info" class="nav-btn flex flex-col items-center">
            <span class="text-2xl">📜</span>
            <span class="text-[8px] uppercase mt-2 font-black tracking-widest">Terms</span>
        </button>
    </nav>

    <script>
        // FIREBASE SETUP
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.appspot.com", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const storage = firebase.storage();
        
        let user = null; let tapCount = 0;

        // DATA: PLANS
        const plans = []; for(let i=1; i<=25; i++) { plans.push({ name: `Vanguard Tier ${i}`, price: 200 * i, daily: 15 * i }); }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                list.innerHTML += `<div onclick="buyPlan(${p.price}, '${p.name}')" class="glass p-6 rounded-[2.5rem] flex justify-between items-center active:scale-95 transition-all">
                    <div><h4 class="font-black text-[11px] uppercase">${p.name}</h4><p class="text-[9px] text-blue-500 font-bold uppercase italic mt-1">₨ ${p.daily} Daily Profit (30 Days)</p></div>
                    <div class="text-sm font-black italic">₨ ${p.price.toLocaleString()}</div>
                </div>`;
            });
        }

        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase(); if(!n) return;
            localStorage.setItem('mc_user', n);
            const ref = db.collection("users").doc(n); const d = await ref.get();
            if(!d.exists) await ref.set({ name: n, balance: 0, time: Date.now() });
            document.getElementById('auth-ui').style.display='none'; 
            document.getElementById('app-ui').classList.remove('hidden'); 
            document.getElementById('bottom-nav').classList.remove('hidden');
            sync(n); renderPlans(); startTimer();
        }

        function sync(n) {
            db.collection("users").doc(n).onSnapshot(d => { user = d.data(); document.getElementById('v-bal').innerText = "₨ "+user.balance.toLocaleString(); });
            // Sync History
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").limit(15).onSnapshot(s => {
                const list = document.getElementById('history-list'); list.innerHTML = '';
                if(s.empty) { list.innerHTML = '<p class="text-[9px] text-center py-6 opacity-30 font-bold italic">Clear History</p>'; return; }
                s.forEach(doc => {
                    const x = doc.data();
                    list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center border-l-4 ${x.status==='pending'?'border-yellow-500':x.status==='approved'?'border-green-500':'border-red-500'}">
                        <div><p class="text-[10px] font-black uppercase">${x.type}</p><p class="text-[8px] opacity-40 font-bold">${new Date(x.time).toLocaleString([], {hour:'2-digit',minute:'2-digit'})}</p></div>
                        <div class="text-right"><p class="text-[10px] font-bold">₨ ${x.amount||0}</p><p class="text-[8px] font-black uppercase status-${x.status}">${x.status}</p></div>
                    </div>`;
                });
            });
            // Sync Support
            db.collection("support").where("user", "==", n).orderBy("time", "asc").onSnapshot(s => {
                const list = document.getElementById('sup-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const m = doc.data();
                    const isAdm = m.from === 'admin';
                    list.innerHTML += `<div class="flex ${isAdm?'justify-start':'justify-end'}">
                        <div class="${isAdm?'bg-slate-200 text-black':'bg-blue-600 text-white'} p-4 rounded-2xl max-w-[80%] text-[10px] font-bold shadow-sm">
                            <p class="opacity-40 text-[7px] uppercase mb-1">${isAdm?'Admin Support':'You'}</p>
                            <p>${m.message}</p>
                        </div>
                    </div>`;
                });
                list.scrollTo(0, list.scrollHeight);
            });
        }

        // DEPOSIT LOGIC
        async function subDep() {
            const a = document.getElementById('d-amt').value; const t = document.getElementById('d-tid').value; const f = document.getElementById('d-file').files[0];
            const btn = document.getElementById('btn-dep');
            if(!a || !t || !f) return alert("Fill all details! 😘");
            btn.innerText = "Processing Node..."; btn.disabled = true;
            try {
                const sRef = storage.ref().child(`receipts/${Date.now()}_${user.name}`);
                const snap = await sRef.put(f);
                const url = await snap.ref.getDownloadURL();
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: url, type: "deposit", status: "pending", time: Date.now() });
                alert("Deposit Logged! Verifying capital... 😘");
                changePage('home');
            } catch(e) { alert("Node Error! Try again. 😘"); } finally { btn.innerText = "Submit Capital"; btn.disabled = false; }
        }

        async function subWd() {
            const a = parseInt(document.getElementById('w-amt').value); const acc = document.getElementById('w-acc').value;
            if(!a || a > user.balance || a < 500) return alert("Min Payout: 500! 😘");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            await db.collection("requests").add({ user: user.name, amount: a, acc: acc, type: "withdraw", status: "pending", time: Date.now() });
            alert("Liquidation Authorized! 😘");
        }

        async function buyPlan(p, n) {
            if(user.balance < p) return alert("Low Liquidity! 😘");
            if(confirm(`Confirm ${n} activation?`)) {
                await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-p) });
                await db.collection("requests").add({ user: user.name, type: "Plan: "+n, amount: p, status: "approved", time: Date.now() });
                alert("Vanguard Node Activated! 😘");
            }
        }

        async function submitPromo() {
            const c = document.getElementById('promo-input').value.trim().toUpperCase(); if(!c) return;
            await db.collection("requests").add({ user: user.name, type: "Promo Request", code: c, status: "pending", time: Date.now() });
            document.getElementById('promo-input').value = ''; alert("Request Sent! 😘");
        }

        async function requestBonus() {
            await db.collection("requests").add({ user: user.name, type: "Bonus Request", status: "pending", time: Date.now() });
            alert("Manual Bonus Logged! 😘");
        }

        async function sendMsg() {
            const m = document.getElementById('sup-msg').value; if(!m) return;
            await db.collection("support").add({ user: user.name, from: "user", message: m, time: Date.now() });
            document.getElementById('sup-msg').value = '';
        }

        // ADMIN HUB
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Pass:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const rl = document.getElementById('adm-req-list'); rl.innerHTML = '';
                s.forEach(doc => {
                    const x = doc.data();
                    rl.innerHTML += `<div class="glass p-5 rounded-3xl border-l-4 border-blue-500">
                        <p class="text-[11px] font-black uppercase mb-1">${x.user} | ${x.type}</p>
                        <p class="text-[8px] opacity-40 mb-3">${x.tid||x.acc||x.code||''}</p>
                        ${x.proof ? `<a href="${x.proof}" target="_blank" class="text-[10px] text-blue-500 font-bold underline block mb-4">View Receipt</a>` : ''}
                        <div class="flex gap-2">
                            <button onclick="hndAdm('${doc.id}','${x.user}',${x.amount},'approved','${x.type}')" class="flex-1 bg-blue-600 text-white py-3 rounded-xl text-[9px] font-black uppercase">Approve</button>
                            <button onclick="hndAdm('${doc.id}','${x.user}',${x.amount},'rejected','${x.type}')" class="flex-1 bg-red-600 text-white py-3 rounded-xl text-[9px] font-black uppercase">Reject</button>
                        </div>
                    </div>`;
                });
            });
        }

        async function hndAdm(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u);
            if(act==='approved') {
                if(type === "deposit") await ref.update({ balance: firebase.firestore.FieldValue.increment(amt) });
                if(type.includes("Request")) { const b = parseInt(prompt("Enter Amount:")); await ref.update({ balance: firebase.firestore.FieldValue.increment(b) }); }
            } else if(act==='rejected' && type==='withdraw') { await ref.update({ balance: firebase.firestore.FieldValue.increment(amt) }); }
            await db.collection("requests").doc(id).update({ status: act });
        }

        async function adminReply() {
            const u = document.getElementById('adm-rep-u').value.toLowerCase();
            const m = document.getElementById('adm-rep-m').value;
            if(!u || !m) return;
            await db.collection("support").add({ user: u, from: "admin", message: m, time: Date.now() });
            alert("Reply Sent! 😘");
        }

        function toggleTheme() { document.documentElement.classList.toggle('dark'); }
        function startTimer() { setInterval(() => { const now = new Date(); const night = new Date(now.getFullYear(), now.getMonth(), now.getDate() + 1, 0, 0, 0); const diff = night - now; const h = Math.floor((diff / (1000 * 60 * 60)) % 24); const m = Math.floor((diff / (1000 * 60)) % 60); const s = Math.floor((diff / 1000) % 60); document.getElementById('profit-timer').innerText = `${h.toString().padStart(2, '0')}:${m.toString().padStart(2, '0')}:${s.toString().padStart(2, '0')}`; }, 1000); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active-tab')); if(document.getElementById('n-'+p)) document.getElementById('n-'+p).classList.add('active-tab'); }
    </script>
</body>
</html>
