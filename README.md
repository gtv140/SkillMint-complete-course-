<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Gold</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap');
        
        :root { --bg: #0b0e14; --card: #161b22; --text: #f0f6fc; --accent: #238636; --border: #30363d; }
        .light { --bg: #ffffff; --card: #f6f8fa; --text: #0f172a; --accent: #2ea043; --border: #d0d7de; }

        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); transition: 0.3s; -webkit-tap-highlight-color: transparent; margin: 0; padding: 0; }
        
        .glass { background: var(--card); border: 1px solid var(--border); }
        .page { display: none; animation: fadeIn 0.3s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        
        .nav-active { color: #3b82f6; border-top: 2px solid #3b82f6; }
        
        input, button { outline: none; border-radius: 12px; }
        .btn-grad { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; font-weight: 800; transition: 0.2s; }
        .btn-grad:active { transform: scale(0.96); }

        /* Smooth Scrolling */
        ::-webkit-scrollbar { display: none; }
        
        .status-pill { padding: 2px 8px; border-radius: 6px; font-size: 8px; font-weight: 800; text-transform: uppercase; }
    </style>
</head>
<body class="flex flex-col h-screen overflow-hidden">

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-[var(--bg)] flex flex-col items-center justify-center p-6">
        <div class="w-16 h-16 bg-blue-600 rounded-2xl mb-4 flex items-center justify-center text-2xl font-black text-white shadow-xl">M</div>
        <h2 onclick="adminTap()" class="text-xl font-extrabold uppercase tracking-tighter italic">MintCrest Gold</h2>
        <div class="w-full max-w-[280px] mt-8 space-y-3">
            <input type="text" id="user-name" placeholder="Username" class="w-full p-4 glass text-center font-bold">
            <button onclick="login()" class="w-full p-4 btn-grad uppercase text-[10px] tracking-widest">Connect Account</button>
        </div>
    </section>

    <header class="p-4 flex justify-between items-center bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)] sticky top-0 z-50">
        <div class="flex items-center gap-2">
            <div class="w-7 h-7 bg-blue-600 rounded-lg flex items-center justify-center text-[10px] font-black text-white">M</div>
            <span class="font-extrabold text-[12px] uppercase tracking-tighter italic">MintCrest</span>
        </div>
        <div class="flex gap-4 items-center">
            <button onclick="toggleTheme()" class="text-lg">🌓</button>
            <button onclick="logout()" class="text-[8px] font-black text-red-500 uppercase">Exit</button>
        </div>
    </header>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto px-4 pt-4 pb-24">
        
        <div id="p-home" class="page active-page">
            <div class="p-6 rounded-[2rem] bg-gradient-to-br from-blue-700 to-blue-500 text-white shadow-lg mb-5 relative overflow-hidden">
                <p class="text-[9px] font-bold uppercase opacity-80 tracking-widest">Your Balance</p>
                <h2 class="text-3xl font-black mt-1" id="v-bal">₨ 0.00</h2>
                <div class="flex justify-between mt-6 bg-black/10 p-3 rounded-xl backdrop-blur-md">
                    <span class="text-[8px] font-bold uppercase self-center">System Secure ✓</span>
                    <button onclick="requestBonus()" class="bg-white text-blue-700 px-4 py-1.5 rounded-lg text-[8px] font-black uppercase">Gift 🎁</button>
                </div>
                <div class="absolute -right-5 -top-5 w-24 h-24 bg-white/10 rounded-full blur-2xl"></div>
            </div>

            <div class="grid grid-cols-2 gap-3 mb-6">
                <button onclick="changePage('wallet')" class="glass p-4 rounded-2xl flex items-center gap-3">
                    <span class="text-xl">📥</span>
                    <div class="text-left"><p class="text-[9px] font-black uppercase">Deposit</p></div>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-4 rounded-2xl flex items-center gap-3">
                    <span class="text-xl">📤</span>
                    <div class="text-left"><p class="text-[9px] font-black uppercase">Payout</p></div>
                </button>
            </div>

            <h3 class="text-[10px] font-black uppercase opacity-40 mb-3 px-1">Recent Activity</h3>
            <div id="history-list" class="space-y-2"></div>
        </div>

        <div id="p-tiers" class="page">
            <h3 class="text-center text-[9px] font-black uppercase tracking-widest mb-6 opacity-40">Profit Portfolios</h3>
            <div id="plans-list" class="space-y-3"></div>
        </div>

        <div id="p-wallet" class="page">
            <div class="glass p-5 rounded-[2rem]">
                <h3 class="text-center font-black uppercase text-[11px] mb-4 text-blue-500">Fund Account</h3>
                <div class="space-y-2 mb-6 text-[9px] font-bold">
                    <div class="flex justify-between p-3 border border-blue-500/20 rounded-xl"><span>Jazz/Sada</span><span class="text-blue-500">03705519562</span></div>
                    <div class="flex justify-between p-3 border border-green-500/20 rounded-xl"><span>EasyPaisa</span><span class="text-green-500">03379827882</span></div>
                </div>
                <div class="space-y-3">
                    <input type="number" id="d-amt" placeholder="Amount (PKR)" class="w-full p-4 glass">
                    <input type="text" id="d-tid" placeholder="TID Number" class="w-full p-4 glass">
                    <div class="p-4 glass rounded-xl border-dashed border-2 border-[var(--border)] text-center">
                        <p class="text-[8px] font-bold opacity-50 uppercase mb-2">Upload Proof Image</p>
                        <input type="file" id="d-file" class="text-[9px] w-full">
                    </div>
                    <button id="btn-dep" onclick="subDep()" class="w-full p-4 btn-grad uppercase text-[10px]">Submit Deposit</button>
                </div>
            </div>
        </div>

        <div id="p-chat" class="page">
            <div class="glass rounded-[2rem] flex flex-col h-[60vh] overflow-hidden">
                <div class="bg-blue-600/10 p-3 text-center border-b border-[var(--border)]">
                    <span class="text-[9px] font-black uppercase text-blue-500">Live Assistant</span>
                </div>
                <div id="sup-list" class="flex-1 overflow-y-auto p-4 space-y-3 text-[10px]"></div>
                <div class="p-3 border-t border-[var(--border)] flex gap-2">
                    <input type="text" id="sup-msg" placeholder="Type here..." class="flex-1 p-3 glass">
                    <button onclick="sendMsg()" class="btn-grad px-4 rounded-xl font-bold text-[9px] uppercase">Send</button>
                </div>
            </div>
        </div>

    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] px-4 py-3 flex justify-around items-center rounded-t-3xl z-[4000]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-1 nav-active">
            <span class="text-xl">🏠</span>
            <span class="text-[7px] font-extrabold uppercase">Node</span>
        </button>
        <button onclick="changePage('tiers')" id="n-tiers" class="flex flex-col items-center gap-1 opacity-40">
            <span class="text-xl">📊</span>
            <span class="text-[7px] font-extrabold uppercase">Tiers</span>
        </button>
        <button onclick="changePage('chat')" id="n-chat" class="flex flex-col items-center gap-1 opacity-40">
            <span class="text-xl">💬</span>
            <span class="text-[7px] font-extrabold uppercase">Chat</span>
        </button>
        <button onclick="changePage('wallet')" id="n-wallet" class="flex flex-col items-center gap-1 opacity-40">
            <span class="text-xl">💰</span>
            <span class="text-[7px] font-extrabold uppercase">Wallet</span>
        </button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.appspot.com", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore(); const storage = firebase.storage();
        let user = null; let tapCount = 0;

        const plans = []; for(let i=1; i<=20; i++) { plans.push({ name: `Elite Tier ${i}`, price: 250 * i, daily: 18 * i }); }

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
            db.collection("users").doc(n).onSnapshot(d => { 
                user = d.data(); 
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); 
            });
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").limit(8).onSnapshot(s => {
                const list = document.getElementById('history-list'); list.innerHTML = '';
                if(s.empty) list.innerHTML = `<p class='text-center py-4 opacity-20 text-[9px]'>No records yet</p>`;
                s.forEach(doc => {
                    const x = doc.data();
                    list.innerHTML += `<div class="glass p-3 rounded-xl flex justify-between items-center text-[9px] font-bold">
                        <span>${x.type.toUpperCase()}</span>
                        <span class="${x.status==='approved'?'text-green-500':'text-yellow-500'} uppercase">${x.status}</span>
                    </div>`;
                });
            });
            db.collection("support").where("user", "==", n).orderBy("time", "asc").onSnapshot(s => {
                const list = document.getElementById('sup-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const m = doc.data(); const isAdm = m.from === 'admin';
                    list.innerHTML += `<div class="flex ${isAdm?'justify-start':'justify-end'}"><div class="${isAdm?'bg-gray-700':'bg-blue-600'} p-3 rounded-2xl max-w-[85%] text-white">${m.message}</div></div>`;
                });
                list.scrollTo(0, list.scrollHeight);
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                list.innerHTML += `<div onclick="buyPlan(${p.price}, '${p.name}')" class="glass p-4 rounded-2xl flex justify-between items-center active:scale-95 shadow-sm border-l-4 border-blue-500">
                    <div><h4 class="font-bold text-[10px] uppercase">${p.name}</h4><p class="text-[8px] text-blue-500 font-bold">₨ ${p.daily} Daily Yield</p></div>
                    <div class="text-[11px] font-black italic">₨ ${p.price}</div>
                </div>`;
            });
        }

        async function subDep() {
            const a = document.getElementById('d-amt').value; const t = document.getElementById('d-tid').value; const f = document.getElementById('d-file').files[0];
            const btn = document.getElementById('btn-dep'); if(!a || !t || !f) return alert("Fill all fields! 😘");
            btn.innerText = "Processing..."; btn.disabled = true;
            try {
                const sRef = storage.ref().child(`proofs/${Date.now()}_${user.name}`);
                const snap = await sRef.put(f);
                const url = await snap.ref.getDownloadURL();
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: url, type: "deposit", status: "pending", time: Date.now() });
                alert("Deposit Logged! 😘"); changePage('home');
            } catch(e) { alert("Upload Failed! Check connection. 😘"); } finally { btn.innerText = "Submit Deposit"; btn.disabled = false; }
        }

        async function sendMsg() {
            const m = document.getElementById('sup-msg').value; if(!m) return;
            await db.collection("support").add({ user: user.name, from: "user", message: m, time: Date.now() });
            document.getElementById('sup-msg').value = '';
        }

        async function buyPlan(p, n) {
            if(user.balance < p) return alert("Low Balance! 😘");
            if(confirm(`Confirm ${n}?`)) {
                await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-p) });
                await db.collection("requests").add({ user: user.name, type: "Plan: "+n, amount: p, status: "approved", time: Date.now() });
                alert("Plan Activated! 😘"); changePage('home');
            }
        }

        async function requestBonus() { await db.collection("requests").add({ user: user.name, type: "Bonus Request", status: "pending", time: Date.now() }); alert("Bonus requested! 😘"); }
        function toggleTheme() { document.documentElement.classList.toggle('light'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.add('opacity-40')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('nav-active')); document.getElementById('n-'+p).classList.remove('opacity-40'); document.getElementById('n-'+p).classList.add('nav-active'); }
        function adminTap() { tapCount++; if(tapCount >= 5) { alert("Admin Node Ready!"); tapCount=0; } }
    </script>
</body>
</html>
