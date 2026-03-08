<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Gold | Elite Mobile</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;800&display=swap');
        
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --accent: #2563eb; --border: #30363d; }
        .light { --bg: #ffffff; --card: #f6f8fa; --text: #0f172a; --accent: #2563eb; --border: #d0d7de; }

        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); transition: 0.3s; overflow-x: hidden; -webkit-tap-highlight-color: transparent; }
        
        /* Mobile-First Adjustments */
        .glass { background: var(--card); border: 1px solid var(--border); backdrop-filter: blur(10px); }
        .page { display: none; animation: slideUp 0.3s ease-out; }
        .active-page { display: block; }
        
        @keyframes slideUp { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        
        .nav-btn { opacity: 0.4; transition: 0.2s; font-size: 20px; }
        .active-tab { opacity: 1; color: var(--accent); transform: scale(1.1); }
        
        .grad-blue { background: linear-gradient(135deg, #1e40af, #3b82f6); }
        .grad-green { background: linear-gradient(135deg, #065f46, #10b981); }
        .grad-gold { background: linear-gradient(135deg, #92400e, #f59e0b); }

        input { background: var(--card); border: 1px solid var(--border); color: var(--text); border-radius: 12px; padding: 12px; font-size: 13px; font-weight: 600; width: 100%; outline: none; }
        ::-webkit-scrollbar { display: none; }
        
        .ticker-wrap { background: #2563eb; color: white; padding: 5px 0; font-size: 9px; font-weight: 800; overflow: hidden; border-radius: 8px; margin-bottom: 15px; }
        .ticker { display: inline-block; white-space: nowrap; animation: ticker 20s linear infinite; }
        @keyframes ticker { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }

        /* Compact Layout */
        .btn-main { padding: 14px; border-radius: 16px; font-weight: 800; font-size: 11px; text-transform: uppercase; transition: 0.2s; width: 100%; color: white; }
        .btn-main:active { scale: 0.96; opacity: 0.9; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-[var(--bg)] flex flex-col items-center justify-center p-8 text-center">
        <div class="w-16 h-16 grad-blue rounded-2xl flex items-center justify-center text-white text-3xl font-black mb-4 shadow-xl">M</div>
        <h1 onclick="adminTap()" class="text-2xl font-extrabold tracking-tighter mb-1 uppercase italic">MintCrest Gold</h1>
        <p class="text-gray-500 text-[8px] uppercase tracking-[0.4em] mb-10 font-bold">Secure Mobile Node</p>
        <div class="w-full max-w-[280px] space-y-3">
            <input type="text" id="user-name" placeholder="Username" class="text-center">
            <button onclick="login()" class="btn-main grad-blue">Connect Portal</button>
        </div>
    </section>

    <header class="p-4 flex justify-between items-center fixed top-0 w-full z-50 bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)]">
        <div class="flex items-center gap-2">
            <div class="w-8 h-8 grad-blue rounded-lg flex items-center justify-center text-white font-black text-xs">M</div>
            <span class="font-black text-xs tracking-tight uppercase italic">MintCrest</span>
        </div>
        <div class="flex items-center gap-3">
            <button onclick="toggleTheme()" class="text-lg">🌓</button>
            <button onclick="logout()" class="text-[8px] font-black uppercase text-red-500 border border-red-500/20 px-3 py-1 rounded-full">Exit</button>
        </div>
    </header>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pt-20 pb-28 px-4">
        
        <div id="p-home" class="page active-page">
            <div class="grad-blue p-8 rounded-[2rem] mb-5 shadow-lg text-white relative overflow-hidden">
                <p class="text-[9px] opacity-80 font-bold uppercase mb-1 tracking-widest">Available Balance</p>
                <h2 class="text-3xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div class="mt-6 flex justify-between items-center bg-black/20 p-3 rounded-2xl">
                    <div class="text-[8px] font-black">
                        <p class="opacity-60 uppercase">System Status</p>
                        <p id="profit-timer" class="tracking-widest">SECURE</p>
                    </div>
                    <button onclick="requestBonus()" class="bg-white text-blue-600 px-4 py-2 rounded-xl text-[8px] font-black uppercase shadow-md">Bonus 🎁</button>
                </div>
            </div>

            <div class="ticker-wrap"><div class="ticker uppercase italic font-bold">• System Secure • Fast Withdrawals • Join Community • MintCrest Elite •</div></div>

            <div class="grid grid-cols-2 gap-3 mb-5">
                <button onclick="changePage('wallet')" class="glass p-5 rounded-3xl text-center active:scale-95 border-b-4 border-green-500">
                    <span class="text-xl block mb-1">📥</span>
                    <span class="text-[9px] font-black uppercase tracking-widest">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-5 rounded-3xl text-center active:scale-95 border-b-4 border-red-500">
                    <span class="text-xl block mb-1">📤</span>
                    <span class="text-[9px] font-black uppercase tracking-widest">Payout</span>
                </button>
            </div>

            <h3 class="text-[10px] font-black uppercase mb-3 opacity-40 px-2 italic tracking-widest">History Ledger</h3>
            <div id="history-list" class="space-y-2"></div>
        </div>

        <div id="p-invest" class="page">
            <div id="plans-list" class="space-y-3"></div>
        </div>

        <div id="p-wallet" class="page">
            <div class="glass p-6 rounded-[2rem] shadow-xl border-t-4 border-green-500">
                <h3 class="font-black text-center mb-5 uppercase text-[11px] text-green-500">Deposit Capital</h3>
                <div class="space-y-2 mb-6 text-[9px] font-bold uppercase">
                    <div class="flex justify-between p-3 bg-blue-500/5 rounded-xl border border-blue-500/10"><span>Jazz/Sada</span><span class="text-blue-500">03705519562</span></div>
                    <div class="flex justify-between p-3 bg-green-500/5 rounded-xl border border-green-500/10"><span>EasyPaisa</span><span class="text-green-500">03379827882</span></div>
                </div>
                <div class="space-y-3">
                    <input type="number" id="d-amt" placeholder="Amount (PKR)">
                    <input type="text" id="d-tid" placeholder="TID Number">
                    <div class="p-3 glass rounded-xl">
                        <p class="text-[8px] font-black opacity-40 uppercase mb-2 italic">Proof Screenshot</p>
                        <input type="file" id="d-file" class="text-[9px] border-none p-0 bg-transparent">
                    </div>
                    <button id="btn-dep" onclick="subDep()" class="btn-main grad-green">Submit Node</button>
                </div>
            </div>
        </div>

        <div id="p-support" class="page">
            <div class="glass p-4 rounded-[2rem] flex flex-col h-[60vh] border-l-4 border-blue-500">
                <h3 class="font-black text-center mb-4 uppercase text-[10px] text-blue-500">Support Chat</h3>
                <div id="sup-list" class="flex-1 overflow-y-auto space-y-3 mb-4 text-[10px]"></div>
                <div class="flex gap-2">
                    <input type="text" id="sup-msg" placeholder="Write..." class="flex-1">
                    <button onclick="sendMsg()" class="grad-blue px-5 rounded-xl font-black text-[9px] uppercase">Send</button>
                </div>
            </div>
        </div>

    </main>

    <nav id="bottom-nav" class="hidden glass border-t border-[var(--border)] p-4 flex justify-around items-center fixed bottom-0 w-full z-[4000] rounded-t-[1.5rem]">
        <button onclick="changePage('home')" id="n-home" class="nav-btn active-tab flex flex-col items-center">🏠<span class="text-[7px] font-black uppercase mt-1">Home</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-btn flex flex-col items-center">📊<span class="text-[7px] font-black uppercase mt-1">Tiers</span></button>
        <button onclick="changePage('support')" id="n-support" class="nav-btn flex flex-col items-center">💬<span class="text-[7px] font-black uppercase mt-1">Help</span></button>
        <button onclick="changePage('wallet')" id="n-wallet" class="nav-btn flex flex-col items-center">💰<span class="text-[7px] font-black uppercase mt-1">Wallet</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.appspot.com", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore(); const storage = firebase.storage();
        let user = null; let tapCount = 0;

        const plans = []; for(let i=1; i<=20; i++) { plans.push({ name: `Node Tier ${i}`, price: 250 * i, daily: 18 * i }); }

        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase(); if(!n) return;
            localStorage.setItem('mc_user', n);
            const ref = db.collection("users").doc(n); const d = await ref.get();
            if(!d.exists) await ref.set({ name: n, balance: 0, time: Date.now() });
            document.getElementById('auth-ui').style.display='none'; 
            document.getElementById('app-ui').classList.remove('hidden'); 
            document.getElementById('bottom-nav').classList.remove('hidden');
            sync(n); renderPlans();
        }

        function sync(n) {
            db.collection("users").doc(n).onSnapshot(d => { user = d.data(); document.getElementById('v-bal').innerText = "₨ "+(user.balance || 0).toLocaleString(); });
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").limit(10).onSnapshot(s => {
                const list = document.getElementById('history-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const x = doc.data();
                    list.innerHTML += `<div class="glass p-3 rounded-xl flex justify-between items-center text-[9px] font-bold border-l-2 ${x.status==='approved'?'border-green-500':'border-yellow-500'}">
                        <span>${x.type.toUpperCase()}</span><span class="${x.status==='approved'?'text-green-500':'text-yellow-500'}">${x.status}</span>
                    </div>`;
                });
            });
            db.collection("support").where("user", "==", n).orderBy("time", "asc").onSnapshot(s => {
                const list = document.getElementById('sup-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const m = doc.data(); const isAdm = m.from === 'admin';
                    list.innerHTML += `<div class="flex ${isAdm?'justify-start':'justify-end'}"><div class="${isAdm?'bg-gray-700':'grad-blue'} p-3 rounded-xl max-w-[80%]">${m.message}</div></div>`;
                });
                list.scrollTo(0, list.scrollHeight);
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                list.innerHTML += `<div onclick="buyPlan(${p.price}, '${p.name}')" class="glass p-4 rounded-2xl flex justify-between items-center active:scale-95 border-l-4 border-blue-500 shadow-md">
                    <div><h4 class="font-black text-[10px] uppercase italic">${p.name}</h4><p class="text-[8px] text-blue-500 font-bold italic">₨ ${p.daily} Daily Profit</p></div>
                    <div class="text-[12px] font-black italic">₨ ${p.price}</div>
                </div>`;
            });
        }

        async function subDep() {
            const a = document.getElementById('d-amt').value; const t = document.getElementById('d-tid').value; const f = document.getElementById('d-file').files[0];
            const btn = document.getElementById('btn-dep'); if(!a || !t || !f) return alert("Fill all sweetie! 😘");
            btn.innerText = "Uploading..."; btn.disabled = true;
            try {
                // IMPORTANT: Correct storage bucket path
                const sRef = storage.ref().child(`proofs/${Date.now()}_${user.name}`);
                const snap = await sRef.put(f);
                const url = await snap.ref.getDownloadURL();
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: url, type: "deposit", status: "pending", time: Date.now() });
                alert("Proof Uploaded! 😘"); changePage('home');
            } catch(e) { console.error(e); alert("Upload Error! 😘"); } finally { btn.innerText = "Submit Node"; btn.disabled = false; }
        }

        async function sendMsg() {
            const m = document.getElementById('sup-msg').value; if(!m) return;
            await db.collection("support").add({ user: user.name, from: "user", message: m, time: Date.now() });
            document.getElementById('sup-msg').value = '';
        }

        async function requestBonus() { await db.collection("requests").add({ user: user.name, type: "Bonus Request", status: "pending", time: Date.now() }); alert("Sent! 😘"); }
        function toggleTheme() { document.documentElement.classList.toggle('light'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active-tab')); if(document.getElementById('n-'+p)) document.getElementById('n-'+p).classList.add('active-tab'); }
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Key:")==="mint786") { alert("Admin Open!"); } tapCount=0; } }
    </script>
</body>
</html>
