<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Global Master Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        .light { --bg: #f8fafc; --card: #ffffff; --text: #0f172a; --border: #e2e8f0; --accent: #2563eb; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); transition: 0.3s; padding-bottom: 110px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); }
        .page { display: none; animation: slideUp 0.4s ease forwards; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; border-radius: 18px; font-weight: 800; }
        input { background: var(--card); border: 1px solid var(--border); color: var(--text); padding: 14px; border-radius: 14px; width: 100%; outline: none; font-size: 13px; }
        .nav-active { color: var(--accent); opacity: 1 !important; transform: scale(1.1); }
        .animate-marquee { display: inline-block; animation: marquee 15s linear infinite; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        ::-webkit-scrollbar { display: none; }
    </style>
</head>
<body>

    <div id="lock-screen" class="fixed inset-0 z-[1000000] bg-[var(--bg)] hidden flex flex-col items-center justify-center p-8 text-center">
        <div class="w-20 h-20 bg-red-500/20 rounded-full flex items-center justify-center mb-6 animate-pulse text-4xl">⚠️</div>
        <h1 id="lock-title" class="text-2xl font-black uppercase text-red-500 mb-2 italic">Access Restricted</h1>
        <p id="lock-msg" class="text-[10px] font-bold opacity-60 uppercase tracking-widest leading-relaxed">System Optimization in Progress, sweetie! 😘</p>
    </div>

    <header class="p-4 flex justify-between items-center sticky top-0 bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)] z-[5000]">
        <div class="flex items-center gap-2">
            <div onclick="adminTap()" class="w-9 h-9 bg-blue-600 rounded-xl flex items-center justify-center font-black text-white italic shadow-lg shadow-blue-500/20">M</div>
            <span class="font-black text-[10px] uppercase tracking-tighter">MintCrest <span class="text-blue-500 text-xs">Gold</span></span>
        </div>
        <div class="flex items-center gap-3">
            <button onclick="toggleTheme()" class="w-8 h-8 glass rounded-full flex items-center justify-center text-sm">🌓</button>
            <div id="top-bal" class="text-[10px] font-black bg-blue-600/10 text-blue-500 px-4 py-1.5 rounded-full border border-blue-500/20 italic">₨ 0</div>
        </div>
    </header>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-8">
        <div class="w-16 h-16 bg-blue-600 rounded-[1.5rem] mb-6 flex items-center justify-center text-4xl font-black text-white italic shadow-2xl">M</div>
        <h1 class="text-2xl font-black italic tracking-tighter mb-8 uppercase text-center">Initialize Node</h1>
        <div class="w-full max-w-[300px] space-y-4">
            <input type="text" id="user-name" placeholder="Username" class="text-center font-bold">
            <input type="password" id="user-pass" placeholder="Password" class="text-center font-bold">
            <button onclick="login()" class="w-full p-4 btn-prime uppercase text-[10px] tracking-widest shadow-xl">Authorize Access</button>
        </div>
    </section>

    <main id="app-ui" class="hidden p-4 space-y-6">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="bg-blue-600/5 border border-blue-500/10 p-2 rounded-xl overflow-hidden whitespace-nowrap">
                <p id="v-news" class="animate-marquee inline-block text-[8px] font-black uppercase text-blue-400 italic">Welcome! Nodes Online... 😘</p>
            </div>

            <div class="p-8 rounded-[2.5rem] bg-gradient-to-br from-blue-700 to-blue-900 text-white shadow-2xl relative">
                <p class="text-[10px] font-black uppercase opacity-60 tracking-widest italic">Global Liquidity</p>
                <h2 class="text-4xl font-black tracking-tighter mt-1" id="v-bal">₨ 0.00</h2>
            </div>

            <div class="glass p-5 rounded-[2rem] flex gap-2">
                <input type="text" id="promo-input" placeholder="Enter Promo Code" class="flex-1 !p-3 font-bold uppercase">
                <button onclick="applyPromo()" class="btn-prime px-6 py-3 text-[9px] uppercase">Apply</button>
            </div>

            <div id="timer-box" class="hidden glass p-6 rounded-[2.5rem] border-l-4 border-green-500 bg-green-500/5">
                <p class="text-[8px] font-black uppercase opacity-60">Mining Node Active</p>
                <div class="flex justify-between items-end mt-2">
                    <h3 id="countdown" class="text-3xl font-black italic text-green-500 tracking-tighter">00:00:00</h3>
                    <p class="text-[7px] font-black opacity-30 uppercase">Yield Node</p>
                </div>
            </div>

            <div class="flex gap-2">
                <button onclick="renderPlans('normal')" id="btn-normal" class="flex-1 p-3 rounded-xl bg-blue-600 text-white text-[9px] font-black uppercase">Standard</button>
                <button onclick="renderPlans('special')" id="btn-special" class="flex-1 p-3 rounded-xl text-[9px] font-black uppercase glass opacity-40">Special</button>
            </div>
            <div id="plans-grid" class="grid grid-cols-1 gap-4 pb-10"></div>
        </div>

        <div id="p-team" class="page space-y-6">
            <div class="text-center"><h3 class="text-xl font-black uppercase italic text-purple-500">My Empire</h3></div>
            <div class="grid grid-cols-2 gap-4">
                <div class="glass p-6 rounded-[2.5rem] text-center"><p id="v-team-count" class="text-2xl font-black">0</p><p class="text-[7px] font-black opacity-40 uppercase">Referrals</p></div>
                <div class="glass p-6 rounded-[2.5rem] text-center"><p class="text-2xl font-black text-purple-500">Node</p><p class="text-[7px] font-black opacity-40 uppercase">Partner</p></div>
            </div>
            <div class="glass p-6 rounded-[2.5rem] bg-purple-500/5 space-y-3">
                <p class="text-[9px] font-black uppercase text-purple-400 italic">Invitation Link</p>
                <div class="flex gap-2"><input type="text" id="ref-link-team" readonly class="flex-1 !p-3 !text-[8px] font-mono bg-black/40 border-none rounded-xl text-purple-300"><button onclick="copyRef('ref-link-team')" class="bg-purple-600 px-4 py-2 rounded-xl text-[8px] font-black uppercase">Copy</button></div>
            </div>
            <div class="glass p-8 rounded-[3rem] border-t-2 border-yellow-500/30">
                <h4 class="text-[12px] font-black uppercase italic text-yellow-500 mb-6">🏆 Hall of Fame</h4>
                <div id="leaderboard-list" class="space-y-3"></div>
            </div>
            <div class="space-y-3">
                <h4 class="text-[10px] font-black uppercase opacity-50 italic">My Recruits</h4>
                <div id="team-list" class="space-y-2"></div>
            </div>
        </div>

        <div id="p-wallet" class="page space-y-6">
            <div class="glass p-5 rounded-[2rem] border-l-4 border-red-600 flex justify-between items-center"><p class="text-lg font-black tracking-widest">03705519562</p><button onclick="copyText('03705519562')" class="text-[8px] bg-white/5 px-4 py-2 rounded-lg font-bold">JAZZCASH</button></div>
            <div class="glass p-5 rounded-[2rem] border-l-4 border-green-500 flex justify-between items-center"><p class="text-lg font-black tracking-widest">03379827882</p><button onclick="copyText('03379827882')" class="text-[8px] bg-white/5 px-4 py-2 rounded-lg font-bold">EASYPAISA</button></div>
            <div class="glass p-6 rounded-[2.5rem] space-y-4">
                <input type="number" id="dep-amt" placeholder="Amount (₨)">
                <input type="text" id="dep-tid" placeholder="Transaction ID">
                <button onclick="submitDep()" class="w-full p-4 btn-prime uppercase text-[10px] tracking-widest">Deposit Proof ⚡</button>
            </div>
        </div>

        <div id="p-withdraw" class="page space-y-6">
            <div class="grid grid-cols-2 gap-3">
                <div onclick="selBank('JazzCash')" id="b-JazzCash" class="glass p-4 rounded-2xl text-center"><p class="text-[8px] font-black">JAZZCASH</p></div>
                <div onclick="selBank('EasyPaisa')" id="b-EasyPaisa" class="glass p-4 rounded-2xl text-center"><p class="text-[8px] font-black">EASYPAISA</p></div>
            </div>
            <input type="number" id="w-amt" placeholder="Amount (₨)">
            <input type="text" id="w-acc" placeholder="Account Number">
            <input type="text" id="w-name" placeholder="Holder Name">
            <button onclick="submitWith()" class="w-full p-4 btn-prime uppercase text-[10px] tracking-widest">Withdraw 📤</button>
        </div>

        <div id="p-history" class="page space-y-4"><div id="history-list" class="space-y-3"></div></div>

        <div id="p-about" class="page glass p-8 rounded-[3rem] text-[11px] leading-relaxed font-bold opacity-80">
            <h3 class="text-blue-500 uppercase mb-4 italic">MintCrest Gold Enterprise</h3>
            <p>Certified Node Mining Platform. Founders: Prime Solutions. Registered #UK-2026.</p>
            <p class="mt-4 opacity-50">Privacy: All transactions are AES-256 encrypted.</p>
        </div>
    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#000000] z-[100000] hidden overflow-y-auto p-4 md:p-10">
        <div class="flex justify-between items-center mb-10 sticky top-0 bg-black/90 py-4 z-10">
            <div><h2 class="text-3xl font-black text-blue-500 italic uppercase">Master Node</h2><p class="text-[8px] text-gray-500 uppercase tracking-widest">God Mode Online</p></div>
            <button onclick="closeAdmin()" class="bg-red-600 px-6 py-2 rounded-xl text-[10px] font-black uppercase">Exit</button>
        </div>
        <div class="grid grid-cols-2 lg:grid-cols-4 gap-6 mb-10">
            <div class="glass p-6 rounded-[2.5rem]"><p id="adm-users-total" class="text-3xl font-black italic">0</p><p class="text-[9px] opacity-40 uppercase">Total Souls</p></div>
            <div class="glass p-6 rounded-[2.5rem]"><p id="adm-cash-total" class="text-3xl font-black italic">₨ 0</p><p class="text-[9px] opacity-40 uppercase">Total Wealth</p></div>
        </div>
        <div class="glass rounded-[2rem] overflow-hidden mb-10">
            <div class="p-6 bg-white/5 font-black uppercase text-[10px]">User Directory & Passwords</div>
            <div class="overflow-x-auto"><table class="w-full text-left text-[11px] font-bold">
                <tr class="opacity-40 uppercase text-[8px] border-b border-white/5"><th class="p-6">User</th><th class="p-6">Security (Pass)</th><th class="p-6">Wealth</th><th class="p-6">Inviter</th><th class="p-6">Action</th></tr>
                <tbody id="god-users-list"></tbody>
            </table></div>
        </div>
        <div id="adm-requests-list" class="space-y-4 pb-32"></div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-4 flex justify-around items-center rounded-t-[2.5rem] z-[4000]">
        <button onclick="changePage('home')" id="n-home" class="nav-active opacity-40 flex flex-col items-center gap-1">🏠<span class="text-[7px] font-black">NODE</span></button>
        <button onclick="changePage('team')" id="n-team" class="opacity-40 flex flex-col items-center gap-1">👥<span class="text-[7px] font-black">TEAM</span></button>
        <button onclick="changePage('wallet')" id="n-wallet" class="opacity-40 flex flex-col items-center gap-1">📥<span class="text-[7px] font-black">FUND</span></button>
        <button onclick="changePage('withdraw')" id="n-withdraw" class="opacity-40 flex flex-col items-center gap-1">📤<span class="text-[7px] font-black">PAYOUT</span></button>
        <button onclick="changePage('history')" id="n-history" class="opacity-40 flex flex-col items-center gap-1">📜<span class="text-[7px] font-black">LOGS</span></button>
    </nav>

    <script>
        // --- FIREBASE & DATA ---
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.appspot.com", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let user = null; let tapCount = 0; let selectedBank = "";

        const NORMAL_PLANS = [];
        for(let i=1; i<=20; i++) {
            let prc = 200 + (i-1)*500;
            let prf = (3 + (i*0.2)).toFixed(1);
            NORMAL_PLANS.push({ name: `Node v${i}`, price: prc, profit: prf, days: 30 + (i-1) });
        }
        const SPECIAL_PLANS = [
            { name: "Gold Node", price: 5000, profit: "15", days: "No Limit" },
            { name: "Admin Node", price: 10000, profit: "25", days: "No Limit" },
            { name: "Whale Node", price: 50000, profit: "40", days: "No Limit" }
        ];

        // --- AUTH & SYNC ---
        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase();
            const p = document.getElementById('user-pass').value.trim();
            if(!n || !p) return alert("Fill data! 😘");
            const inviter = new URLSearchParams(window.location.search).get('ref') || "Direct";
            const ref = db.collection("users").doc(n);
            const d = await ref.get();
            if(!d.exists) await ref.set({ name: n, password: p, balance: 0, invitedBy: inviter, time: Date.now() });
            else if(d.data().password !== p) return alert("Wrong Key! 😘");
            localStorage.setItem('mc_user', n); enterApp(n);
        }

        function enterApp(n) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            sync(n);
        }

        function sync(n) {
            db.collection("users").doc(n).onSnapshot(d => {
                user = d.data();
                if(user.banned) location.reload();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
                if(user.activePlan) startTimer(user.activePlan.timer);
                updateRefUI();
            });
            db.collection("requests").where("user", "==", n).onSnapshot(s => {
                const l = document.getElementById('history-list'); l.innerHTML = '';
                let items = []; s.forEach(d=>items.push(d.data()));
                items.sort((a,b)=>b.time-a.time).forEach(x => {
                    l.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center text-[10px] font-black border-l-4 ${x.status==='approved'?'border-green-500':'border-yellow-500'}">
                    <div><p class="uppercase">${x.type}</p><p class="text-[7px] opacity-40">${new Date(x.time).toLocaleDateString()}</p></div>
                    <div class="text-right"><p>₨ ${x.amount||0}</p><p class="text-[7px] uppercase italic">${x.status}</p></div></div>`;
                });
            });
        }

        // --- NODES & TIMER ---
        function renderPlans(cat) {
            const grid = document.getElementById('plans-grid');
            const data = cat === 'normal' ? NORMAL_PLANS : SPECIAL_PLANS;
            grid.innerHTML = '';
            data.forEach(p => {
                grid.innerHTML += `<div class="glass p-6 rounded-[2rem] border-l-4 ${cat==='special'?'border-yellow-500':'border-blue-500'}">
                    <div class="flex justify-between"><div><p class="text-[10px] uppercase font-black">${p.name}</p><h3 class="text-xl font-black text-blue-500">₨ ${p.price}</h3></div>
                    <div class="text-right"><span class="bg-blue-600/10 text-blue-500 px-3 py-1 rounded-full text-[8px] font-black">${p.profit}%</span><p class="text-[7px] opacity-40 mt-1 uppercase">${p.days} DAYS</p></div></div>
                    <button onclick="buyNode('${p.name}', ${p.price})" class="w-full mt-4 btn-prime p-3 text-[9px] uppercase">Activate Node ⚡</button></div>`;
            });
        }

        async function buyNode(n, p) {
            if(user.balance < p) return alert("Low balance sweetie! 😘");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-p), activePlan: { name: n, timer: Date.now() + 86400000 } });
            alert("Activated! 😘");
        }

        function startTimer(end) {
            document.getElementById('timer-box').classList.remove('hidden');
            setInterval(() => {
                const diff = end - Date.now();
                if(diff <= 0) { document.getElementById('countdown').innerText = "PROFIT READY!"; return; }
                const h = Math.floor(diff/3600000); const m = Math.floor((diff%3600000)/60000); const s = Math.floor((diff%60000)/1000);
                document.getElementById('countdown').innerText = `${h.toString().padStart(2,'0')}:${m.toString().padStart(2,'0')}:${s.toString().padStart(2,'0')}`;
            }, 1000);
        }

        // --- TEAM & LEADERBOARD ---
        function updateRefUI() { 
            const link = window.location.origin + window.location.pathname + "?ref=" + user.name;
            if(document.getElementById('ref-link-team')) document.getElementById('ref-link-team').value = link; 
        }
        function copyRef(id) { const i = document.getElementById(id); i.select(); navigator.clipboard.writeText(i.value); alert("Copied! 😘"); }

        async function syncTeam(un) {
            db.collection("users").where("invitedBy", "==", un).onSnapshot(s => {
                document.getElementById('v-team-count').innerText = s.size;
                const l = document.getElementById('team-list'); l.innerHTML = '';
                s.forEach(d => { const m = d.data(); l.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between items-center text-[10px] font-black uppercase"><span>@${m.name}</span><span class="text-green-500 text-[8px]">Partner</span></div>`; });
            });
            // Leaderboard
            const all = await db.collection("users").get();
            let counts = {}; all.forEach(d => { const u = d.data(); if(u.invitedBy && u.invitedBy !== "Direct") counts[u.invitedBy] = (counts[u.invitedBy]||0)+1; });
            let sorted = Object.entries(counts).sort((a,b)=>b[1]-a[1]).slice(0,10);
            const lb = document.getElementById('leaderboard-list'); lb.innerHTML = '';
            sorted.forEach((x, i) => { lb.innerHTML += `<div class="flex justify-between p-3 bg-white/5 rounded-xl text-[10px] font-black uppercase"><span>#${i+1} ${x[0].substring(0,2)}***</span><span class="text-yellow-500">${x[1]} Nodes</span></div>`; });
        }

        // --- ADMIN GOD MODE ---
        function adminTap() { tapCount++; if(tapCount>=5) { if(prompt("God Key:")==="mint786") { localStorage.setItem('is_admin','true'); document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        function syncAdm() {
            db.collection("users").onSnapshot(s => {
                document.getElementById('adm-users-total').innerText = s.size;
                const l = document.getElementById('god-users-list'); l.innerHTML = ''; let tw = 0;
                s.forEach(d => { const u = d.data(); tw += (u.balance||0);
                    l.innerHTML += `<tr class="border-b border-white/5"><td class="p-6">@${u.name}</td><td class="p-6 text-yellow-500">${u.password}</td><td class="p-6 text-green-500">₨ ${u.balance||0}</td><td class="p-6 opacity-40">${u.invitedBy}</td><td class="p-6"><button onclick="godEdit('${u.name}', ${u.balance})" class="bg-blue-600 px-4 py-2 rounded-xl text-[8px]">EDIT</button></td></tr>`;
                });
                document.getElementById('adm-cash-total').innerText = "₨ " + tw.toLocaleString();
            });
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const q = document.getElementById('adm-requests-list'); q.innerHTML = '';
                s.forEach(d => { const r = d.data();
                    q.innerHTML += `<div class="glass p-6 rounded-[2rem] border-l-4 border-blue-500 flex justify-between items-center">
                    <div class="text-[10px] font-black uppercase"><p>${r.user} | ${r.type}</p><p class="text-green-500">₨ ${r.amount||r.code}</p></div>
                    <div class="flex gap-2"><button onclick="appvReq('${d.id}','${r.user}', ${r.amount||0}, '${r.type}')" class="bg-blue-600 px-4 py-2 rounded-xl text-[8px] font-black">APPROVE</button></div></div>`;
                });
            });
        }

        function godEdit(n, b) { const a = prompt("A: Add, S: Sub, P: Pass", "A"); if(a==="A"){ const m = prompt("Amt:"); db.collection("users").doc(n).update({balance:firebase.firestore.FieldValue.increment(parseInt(m))}); } }
        async function appvReq(id, u, a, t) { if(t!=="withdrawal") await db.collection("users").doc(u).update({balance:firebase.firestore.FieldValue.increment(a)}); await db.collection("requests").doc(id).update({status:'approved'}); alert("Done! 😘"); }

        // --- CORE UTILS ---
        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('nav button').forEach(b=>{ b.classList.add('opacity-40'); b.classList.remove('nav-active'); });
            document.getElementById('n-'+p).classList.remove('opacity-40'); document.getElementById('n-'+p).classList.add('nav-active');
            if(p==='team') syncTeam(user.name);
        }
        function toggleTheme() { document.getElementById('main-html').classList.toggle('light'); document.getElementById('main-html').classList.toggle('dark'); }
        function copyText(t) { navigator.clipboard.writeText(t); alert("Copied! 😘"); }
        async function applyPromo() { const c = document.getElementById('promo-input').value.toUpperCase(); await db.collection("requests").add({ user:user.name, type:'promo', code:c, status:'pending', time:Date.now() }); alert("Request Sent! 😘"); }
        async function submitDep() { const a = document.getElementById('dep-amt').value; const t = document.getElementById('dep-tid').value; await db.collection("requests").add({ user:user.name, type:'deposit', amount:parseInt(a), tid:t, status:'pending', time:Date.now() }); alert("Sent! 😘"); }
        async function submitWith() { const a = document.getElementById('w-amt').value; await db.collection("users").doc(user.name).update({balance:firebase.firestore.FieldValue.increment(-parseInt(a))}); await db.collection("requests").add({ user:user.name, type:'withdrawal', amount:parseInt(a), status:'pending', time:Date.now() }); alert("Requested! 😘"); }
        function selBank(b) { selectedBank = b; alert(b + " Selected! 😘"); }

        window.onload = () => { if(localStorage.getItem('mc_user')) enterApp(localStorage.getItem('mc_user')); renderPlans('normal'); }
    </script>
</body>
</html>
