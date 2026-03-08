<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Gold | Professional Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --accent: #238636; --border: #30363d; }
        .light { --bg: #ffffff; --card: #f6f8fa; --text: #0f172a; --accent: #2ea043; --border: #d0d7de; }

        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); transition: 0.3s; -webkit-tap-highlight-color: transparent; min-height: 100vh; overflow-y: auto; }
        .glass { background: var(--card); border: 1px solid var(--border); }
        .page { display: none; animation: fadeIn 0.4s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }
        
        .nav-active { color: #3b82f6; border-top: 3px solid #3b82f6; background: rgba(59, 130, 246, 0.05); }
        input, select { background: var(--card); border: 1px solid var(--border); color: var(--text); outline: none; border-radius: 12px; font-size: 13px; }
        .btn-blue { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; font-weight: 800; border-radius: 14px; }
        ::-webkit-scrollbar { display: none; }
        .ticker-wrap { background: #1e40af; color: white; padding: 4px 0; font-size: 9px; font-weight: 800; overflow: hidden; }
        .ticker { display: inline-block; white-space: nowrap; animation: ticker 30s linear infinite; }
        @keyframes ticker { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
    </style>
</head>
<body class="pb-24">

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-[var(--bg)] flex flex-col items-center justify-center p-6 text-center">
        <div class="w-16 h-16 bg-blue-600 rounded-2xl mb-4 flex items-center justify-center text-2xl font-black text-white shadow-2xl">M</div>
        <h1 onclick="adminTap()" class="text-2xl font-black uppercase italic tracking-tighter">MintCrest Gold</h1>
        <p class="text-[9px] font-bold opacity-40 uppercase tracking-[0.3em] mb-10 italic">Certified Financial Node</p>
        <div class="w-full max-w-[280px] space-y-3">
            <input type="text" id="user-name" placeholder="Enter Username" class="w-full p-4 text-center font-bold">
            <button onclick="login()" class="w-full p-4 btn-blue uppercase text-[10px] tracking-widest shadow-lg">Initialize Session</button>
        </div>
    </section>

    <header class="p-4 flex justify-between items-center bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)] sticky top-0 z-[1000]">
        <div class="flex items-center gap-2">
            <div class="w-7 h-7 bg-blue-600 rounded flex items-center justify-center text-[10px] font-black text-white">M</div>
            <span class="font-extrabold text-[12px] uppercase tracking-tighter italic">MintCrest</span>
        </div>
        <div class="flex gap-4">
            <button onclick="toggleTheme()" class="text-lg">🌓</button>
            <button onclick="logout()" class="text-[9px] font-black text-red-500 border border-red-500/20 px-3 py-1 rounded-full uppercase">Exit</button>
        </div>
    </header>

    <div class="ticker-wrap"><div class="ticker italic">• MintCrest Gold is a Registered Asset Management Entity • 24/7 Manual Auditing Active • Global Payouts Guaranteed •</div></div>

    <main id="app-ui" class="hidden p-4 space-y-6">
        
        <div id="p-home" class="page active-page">
            <div class="p-8 rounded-[2.5rem] bg-gradient-to-br from-blue-700 to-blue-900 text-white shadow-xl relative overflow-hidden">
                <p class="text-[10px] font-black uppercase opacity-70 tracking-widest">Liquid Capital</p>
                <h2 class="text-4xl font-black mt-1 tracking-tighter" id="v-bal">₨ 0.00</h2>
                <div class="mt-6 flex justify-between items-center bg-black/20 p-4 rounded-2xl">
                    <span class="text-[9px] font-black uppercase italic">Node Secured ✓</span>
                    <button onclick="requestBonus()" class="bg-white text-blue-800 px-5 py-2 rounded-xl text-[9px] font-black uppercase shadow-md active:scale-90">Bonus 🎁</button>
                </div>
                <div class="absolute -right-5 -top-5 w-32 h-32 bg-white/5 rounded-full blur-3xl"></div>
            </div>

            <div class="grid grid-cols-2 gap-4 my-6">
                <button onclick="changePage('wallet')" class="glass p-5 rounded-3xl border-b-4 border-green-500 active:scale-95 transition-all">
                    <span class="text-2xl block mb-2">📥</span>
                    <p class="text-[10px] font-black uppercase tracking-widest">Deposit</p>
                </button>
                <button onclick="changePage('payout')" class="glass p-5 rounded-3xl border-b-4 border-red-500 active:scale-95 transition-all">
                    <span class="text-2xl block mb-2">📤</span>
                    <p class="text-[10px] font-black uppercase tracking-widest">Payout</p>
                </button>
            </div>

            <div class="glass p-4 rounded-2xl flex gap-2 mb-6">
                <input type="text" id="promo-input" placeholder="Promo Code" class="flex-1 p-3 text-[10px] font-bold uppercase">
                <button onclick="applyPromo()" class="btn-blue px-6 text-[9px] font-black uppercase">Apply</button>
            </div>

            <h3 class="text-[11px] font-black uppercase opacity-40 px-2 italic tracking-[0.2em] mb-4">Transaction History</h3>
            <div id="history-list" class="space-y-3"></div>
        </div>

        <div id="p-payout" class="page">
            <div class="glass p-8 rounded-[2.5rem] shadow-2xl border-t-8 border-red-500">
                <h3 class="text-center font-black uppercase text-[12px] mb-2 text-red-500 italic">Liquidation Center</h3>
                <p class="text-center text-[9px] opacity-40 mb-8 font-bold italic">24-Hour Security Audit Policy</p>
                <div class="space-y-4">
                    <input type="number" id="w-amt" placeholder="Payout Amount" class="w-full p-5 font-black text-xs">
                    <input type="text" id="w-acc" placeholder="Account Name & Details" class="w-full p-5 font-black text-xs">
                    <button onclick="subWd()" class="w-full p-5 bg-slate-900 text-white rounded-2xl font-black text-[11px] uppercase shadow-xl active:scale-95">Authorize Withdrawal</button>
                </div>
            </div>
        </div>

        <div id="p-about" class="page space-y-6 pb-10">
            <div class="glass p-6 rounded-3xl">
                <h4 class="text-blue-500 font-black text-[11px] uppercase mb-3 italic tracking-widest">Corporate Profile</h4>
                <p class="text-[10px] leading-relaxed opacity-60 font-medium italic">MintCrest Gold is a premier asset management firm incorporated in 2026. We specialize in decentralized liquidity yield and secure capital growth nodes for global investors. Our operations are audited daily to ensure 100% solvency and user protection.</p>
            </div>
            <div class="glass p-6 rounded-3xl">
                <h4 class="text-green-500 font-black text-[11px] uppercase mb-3 italic tracking-widest">Privacy & Security</h4>
                <p class="text-[10px] leading-relaxed opacity-60 font-medium italic">User data is encrypted using military-grade SSL protocols. We do not share financial records with third parties. Every payout is manually verified by our audit team to prevent fraudulent activity and protect the liquidity pool.</p>
            </div>
        </div>

    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[var(--bg)] z-[50000] hidden overflow-y-auto p-6">
        <div class="flex justify-between items-center mb-10 sticky top-0 bg-[var(--bg)] py-2">
            <h2 class="text-2xl font-black text-blue-500 uppercase italic">Command Hub</h2>
            <button onclick="closeAdmin()" class="bg-red-500 text-white px-5 py-2 rounded-xl text-[10px] font-black uppercase">Close</button>
        </div>

        <div class="space-y-8 pb-32">
            <section>
                <h4 class="text-[10px] font-black uppercase opacity-50 mb-4 tracking-widest italic">Pending Audits</h4>
                <div id="adm-req-list" class="space-y-4"></div>
            </section>

            <section class="glass p-6 rounded-3xl">
                <h4 class="text-[10px] font-black uppercase text-blue-500 mb-6 italic tracking-widest">User Intelligence</h4>
                <div class="space-y-4">
                    <input type="text" id="adm-target" placeholder="User Name" class="w-full p-4">
                    <input type="number" id="adm-val" placeholder="Value (Amount)" class="w-full p-4">
                    <div class="grid grid-cols-2 gap-2">
                        <button onclick="manualEdit('balance')" class="bg-green-600 p-3 rounded-xl text-[9px] font-black uppercase text-white">Update Balance</button>
                        <button onclick="manualDelete()" class="bg-red-600 p-3 rounded-xl text-[9px] font-black uppercase text-white">Delete User</button>
                    </div>
                </div>
            </section>

            <section class="glass p-6 rounded-3xl">
                <h4 class="text-[10px] font-black uppercase text-purple-500 mb-6 italic tracking-widest">Promo Control</h4>
                <div class="space-y-4">
                    <input type="text" id="adm-p-code" placeholder="New Promo Code" class="w-full p-4 uppercase">
                    <input type="number" id="adm-p-val" placeholder="Reward Amount" class="w-full p-4">
                    <button onclick="updatePromo()" class="w-full bg-purple-600 p-4 rounded-xl text-[10px] font-black uppercase text-white">Create/Update Promo</button>
                </div>
            </section>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-5 flex justify-around items-center rounded-t-[2.5rem] z-[4000]">
        <button onclick="changePage('home')" id="n-home" class="nav-active flex flex-col items-center gap-1 px-4 py-2 rounded-2xl">🏠<span class="text-[7px] font-black uppercase">Node</span></button>
        <button onclick="changePage('about')" id="n-about" class="opacity-40 flex flex-col items-center gap-1 px-4 py-2 rounded-2xl">📜<span class="text-[7px] font-black uppercase">Audit</span></button>
        <button onclick="changePage('payout')" id="n-payout" class="opacity-40 flex flex-col items-center gap-1 px-4 py-2 rounded-2xl">📤<span class="text-[7px] font-black uppercase">Payout</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.appspot.com", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore(); const storage = firebase.storage();
        let user = null; let tapCount = 0;

        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase(); if(!n) return;
            localStorage.setItem('mc_user', n);
            const ref = db.collection("users").doc(n); const d = await ref.get();
            if(!d.exists) await ref.set({ name: n, balance: 0, time: Date.now() });
            document.getElementById('auth-ui').style.display='none'; 
            document.getElementById('app-ui').classList.remove('hidden'); 
            document.getElementById('bottom-nav').classList.remove('hidden');
            sync(n);
        }

        function sync(n) {
            db.collection("users").doc(n).onSnapshot(d => { user = d.data(); document.getElementById('v-bal').innerText = "₨ "+(user.balance || 0).toLocaleString(); });
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").limit(10).onSnapshot(s => {
                const list = document.getElementById('history-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const x = doc.data();
                    list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center text-[10px] font-bold border-l-4 ${x.status==='approved'?'border-green-500':'border-yellow-500'}">
                        <div><p class="uppercase">${x.type}</p><p class="text-[8px] opacity-40">${new Date(x.time).toLocaleDateString()}</p></div>
                        <div class="text-right"><p>₨ ${x.amount||0}</p><p class="text-[8px] uppercase ${x.status==='approved'?'text-green-500':'text-yellow-500'}">${x.status}</p></div>
                    </div>`;
                });
            });
        }

        async function subWd() {
            const a = parseInt(document.getElementById('w-amt').value); const acc = document.getElementById('w-acc').value;
            if(!a || a > user.balance || a < 500) return alert("Invalid Payout Amount! 😘");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            await db.collection("requests").add({ user: user.name, amount: a, acc: acc, type: "Withdrawal", status: "pending", time: Date.now() });
            alert("Liquidation Authorized! 😘"); changePage('home');
        }

        async function applyPromo() {
            const c = document.getElementById('promo-input').value.trim().toUpperCase(); if(!c) return;
            await db.collection("requests").add({ user: user.name, type: "Promo: "+c, status: "pending", time: Date.now() });
            alert("Promo Request Logged! 😘");
        }

        async function requestBonus() { await db.collection("requests").add({ user: user.name, type: "Bonus Request", status: "pending", time: Date.now() }); alert("Daily Bonus Logged! 😘"); }

        // ADMIN HUB FUNCTIONS
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Pass:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const rl = document.getElementById('adm-req-list'); rl.innerHTML = '';
                s.forEach(doc => {
                    const x = doc.data();
                    rl.innerHTML += `<div class="glass p-5 rounded-3xl border-l-4 border-blue-500 mb-4">
                        <p class="text-[11px] font-black uppercase italic">${x.user} | ${x.type}</p>
                        <p class="text-[9px] opacity-50 my-2">${x.tid||x.acc||''}</p>
                        <div class="flex gap-2">
                            <button onclick="admAct('${doc.id}','${x.user}',${x.amount},'approved','${x.type}')" class="flex-1 bg-blue-600 text-white py-3 rounded-xl text-[9px] font-black uppercase">Approve</button>
                            <button onclick="admAct('${doc.id}','${x.user}',${x.amount},'rejected','${x.type}')" class="flex-1 bg-red-600 text-white py-3 rounded-xl text-[9px] font-black uppercase">Reject</button>
                        </div>
                    </div>`;
                });
            });
        }

        async function admAct(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u);
            if(act==='approved') {
                if(type === "deposit") await ref.update({ balance: firebase.firestore.FieldValue.increment(amt) });
                if(type.includes("Bonus") || type.includes("Promo")) { const val = parseInt(prompt("Enter Amount:")); await ref.update({ balance: firebase.firestore.FieldValue.increment(val) }); }
            } else if(act==='rejected' && type==='Withdrawal') { await ref.update({ balance: firebase.firestore.FieldValue.increment(amt) }); }
            await db.collection("requests").doc(id).update({ status: act });
        }

        async function manualEdit(f) {
            const u = document.getElementById('adm-target').value.toLowerCase();
            const v = parseInt(document.getElementById('adm-val').value);
            if(!u || isNaN(v)) return alert("Fill data! 😘");
            await db.collection("users").doc(u).update({ [f]: v });
            alert("Data Updated! 😘");
        }

        async function manualDelete() {
            const u = document.getElementById('adm-target').value.toLowerCase();
            if(confirm("Delete User "+u+"?")) { await db.collection("users").doc(u).delete(); alert("Terminated! 😘"); }
        }

        async function updatePromo() {
            const c = document.getElementById('adm-p-code').value.toUpperCase();
            const v = parseInt(document.getElementById('adm-p-val').value);
            if(!c || isNaN(v)) return;
            await db.collection("promos").doc(c).set({ code: c, reward: v });
            alert("Promo Hub Updated! 😘");
        }

        function toggleTheme() { document.documentElement.classList.toggle('light'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.add('opacity-40')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('nav-active')); document.getElementById('n-'+p).classList.remove('opacity-40'); document.getElementById('n-'+p).classList.add('nav-active'); }
    </script>
</body>
</html>
