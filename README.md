<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Colorful Edition</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #020617; color: white; overflow-x: hidden; }
        
        /* COLORFUL GLASS EFFECTS */
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.1); }
        .glass-blue { background: linear-gradient(135deg, rgba(59,130,246,0.1), rgba(37,99,235,0.05)); border: 1px solid rgba(59,130,246,0.2); }
        .glass-purple { background: linear-gradient(135deg, rgba(168,85,247,0.1), rgba(139,92,246,0.05)); border: 1px solid rgba(168,85,247,0.2); }
        .glass-green { background: linear-gradient(135deg, rgba(34,197,94,0.1), rgba(22,163,74,0.05)); border: 1px solid rgba(34,197,94,0.2); }
        .glass-red { background: linear-gradient(135deg, rgba(239,68,68,0.1), rgba(220,38,38,0.05)); border: 1px solid rgba(239,68,68,0.2); }
        
        .active-tab { color: #60a5fa; border-top: 3px solid #60a5fa; background: linear-gradient(to top, rgba(96,165,250,0.1), transparent); }
        .page { display: none; animation: fadeIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; scale: 0.98; } to { opacity: 1; scale: 1; } }
        
        .badge-pending { background: #fef3c7; color: #92400e; padding: 3px 8px; border-radius: 6px; font-size: 8px; font-weight: 800; }
        .badge-approved { background: #dcfce7; color: #166534; padding: 3px 8px; border-radius: 6px; font-size: 8px; font-weight: 800; }
        .badge-rejected { background: #fee2e2; color: #991b1b; padding: 3px 8px; border-radius: 6px; font-size: 8px; font-weight: 800; }
        
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .animate-marquee { display: inline-block; animation: marquee 12s linear infinite; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="promo-ticker" class="hidden bg-gradient-to-r from-blue-600 to-purple-600 py-2 overflow-hidden z-[500] shadow-lg">
        <div class="animate-marquee text-[10px] font-black uppercase tracking-widest text-white">
            🚀 <span id="ticker-text">MintCrest Gold: The Most Colorful & Secure Investment Platform!</span> 💎
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-slate-950 flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 onclick="adminTap()" class="text-6xl font-black tracking-tighter mb-4 italic bg-gradient-to-r from-blue-400 via-purple-500 to-pink-500 bg-clip-text text-transparent uppercase">MINTCREST</h1>
            <div class="glass p-10 rounded-[3rem] shadow-2xl border-t-4 border-blue-500">
                <input type="text" id="user-name" placeholder="Enter Full Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-6 text-blue-300 focus:border-blue-400">
                <button onclick="login()" class="w-full bg-gradient-to-r from-blue-600 to-blue-400 py-5 rounded-2xl font-black text-xs uppercase tracking-widest shadow-lg shadow-blue-500/20 active:scale-95 transition-all">Launch Vault</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="glass-blue p-8 rounded-[3rem] mb-6 shadow-2xl relative overflow-hidden">
                <div class="absolute -top-10 -right-10 w-32 h-32 bg-blue-500/20 rounded-full blur-3xl"></div>
                <p class="text-[10px] text-blue-400 font-black uppercase mb-1">Live Capital</p>
                <h2 class="text-5xl font-black tracking-tighter text-white" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="mt-4 text-[9px] font-black text-cyan-400 uppercase italic">Syncing Assets...</div>
                <div class="mt-6 flex gap-3">
                    <span class="text-[9px] bg-green-500/20 text-green-400 px-4 py-2 rounded-xl font-black border border-green-500/30">PROFIT: <span id="v-profit">0</span></span>
                    <span id="tier-tag" class="text-[9px] bg-white/5 text-slate-400 px-4 py-2 rounded-xl font-black uppercase italic border border-white/10">No Fleet</span>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass-green p-8 rounded-[2.5rem] text-center border-b-4 border-green-500 shadow-xl">📥 <span class="text-[10px] font-black block mt-2 uppercase text-green-400">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass-red p-8 rounded-[2.5rem] text-center border-b-4 border-red-500 shadow-xl">📤 <span class="text-[10px] font-black block mt-2 uppercase text-red-400">Withdraw</span></button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-purple-400 text-xl tracking-tighter">Premium Fleet Marketplace</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-4 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-300">Transaction Ledger</h2>
            <div id="user-history" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6 pb-12">
            <div class="glass-purple p-8 rounded-[3rem] border-t-4 border-yellow-500 shadow-lg">
                <h3 class="text-yellow-500 font-black text-xs uppercase mb-4 text-center italic">Reward Center 🎁</h3>
                <input type="text" id="promo-input" placeholder="Promo Code" class="w-full bg-white/5 p-4 rounded-xl text-xs border border-white/10 outline-none mb-4 text-center font-black uppercase text-yellow-200">
                <button onclick="applyPromoRequest()" class="w-full bg-gradient-to-r from-yellow-600 to-yellow-400 py-4 rounded-xl font-black text-xs uppercase shadow-lg shadow-yellow-500/20">Claim Reward</button>
            </div>
            
            <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3" class="block glass p-8 rounded-[3rem] text-center border-b-4 border-green-500 shadow-lg">
                <span class="text-green-400 font-black text-xs uppercase">🟢 Official Community Group</span>
            </a>

            <div class="glass p-8 rounded-[3.5rem] border-l-4 border-blue-500">
                <h3 class="text-blue-400 font-black text-xs uppercase mb-4 text-center italic">Admin Support 🛠️</h3>
                <textarea id="support-msg" placeholder="Tell us your issue..." class="w-full bg-white/5 p-5 rounded-xl text-xs border border-white/10 outline-none mb-4 h-24 font-bold"></textarea>
                <button onclick="sendSupport()" class="w-full bg-blue-600 py-4 rounded-xl font-black text-[10px] uppercase">Send Message</button>
            </div>

            <button onclick="logout()" class="w-full bg-red-600/10 border border-red-500/20 p-6 rounded-[2.5rem] text-center text-xs font-black text-red-500 uppercase">Terminate Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-8 rounded-[3.5rem] text-center border-t-8 border-green-500 shadow-2xl">
                <h3 class="font-black text-green-500 mb-6 uppercase text-sm italic">Capital Funding</h3>
                <div class="space-y-2 mb-8 text-[10px] text-left font-black">
                    <div class="bg-blue-500/10 p-4 rounded-xl flex justify-between border border-blue-500/20"><span>SADAPAY/JAZZ</span><span class="text-blue-400">03705519562</span></div>
                    <div class="bg-green-500/10 p-4 rounded-xl flex justify-between border border-green-500/20"><span>EASYPAISA</span><span class="text-green-400">03379827882</span></div>
                    <div class="bg-yellow-500/10 p-4 rounded-xl border border-yellow-500/20 flex flex-col"><span class="text-yellow-500">BINANCE (USDT-TRC20)</span><span class="text-[7px] text-white break-all mt-1">TTSxm4pBK26RB4vXaa3Uo3hqGa5HdhxBDR</span></div>
                </div>
                <select id="dep-method" class="w-full bg-slate-900 p-4 rounded-xl mb-3 font-bold border border-white/10 text-xs">
                    <option value="EasyPaisa">EasyPaisa</option><option value="JazzCash/SadaPay">JazzCash/SadaPay</option><option value="Binance">Binance (USDT)</option>
                </select>
                <input type="number" id="dep-amount" placeholder="Amount" class="w-full bg-slate-900 p-4 rounded-xl mb-3 text-center font-bold border border-white/10 outline-none">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-slate-900 p-4 rounded-xl mb-6 text-center font-bold uppercase border border-white/10">
                <input type="file" id="dep-proof" accept="image/*" class="mb-6 text-[10px] text-slate-400">
                <button onclick="submitDeposit()" class="w-full bg-green-600 py-5 rounded-2xl font-black text-xs uppercase shadow-xl">Verify Deposit</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-8 rounded-[3.5rem] border-t-8 border-red-600 text-center shadow-2xl">
                <h3 class="font-black text-red-500 mb-8 uppercase text-sm italic">Authorize Payout</h3>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-slate-900 p-5 rounded-2xl mb-4 text-center font-black border border-white/10">
                <input type="text" id="wd-acc" placeholder="Account Name & Number" class="w-full bg-slate-900 p-5 rounded-2xl mb-8 text-center text-xs font-black border border-white/10">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-5 rounded-2xl font-black text-xs uppercase shadow-lg">Request Withdrawal</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#020617] z-[5000] hidden overflow-y-auto">
        <header class="p-6 border-b border-white/10 bg-slate-950/80 sticky top-0 backdrop-blur-xl flex justify-between items-center shadow-xl">
            <div>
                <h2 class="text-xl font-black text-cyan-400 uppercase italic tracking-tighter">MASTER CONSOLE ⚡</h2>
            </div>
            <button onclick="closeAdmin()" class="bg-red-600 px-6 py-2 rounded-xl text-[10px] font-black uppercase shadow-lg shadow-red-500/20">Kill Switch</button>
        </header>

        <div class="p-6">
            <div class="grid grid-cols-2 gap-4 mb-8 text-center">
                <div class="glass-purple p-6 rounded-3xl border-b-4 border-purple-500">
                    <p class="text-[8px] text-purple-300 font-black uppercase mb-1">Pending Tasks</p>
                    <h4 id="stat-pending" class="text-3xl font-black text-white">0</h4>
                </div>
                <div class="glass-blue p-6 rounded-3xl border-b-4 border-blue-500">
                    <p class="text-[8px] text-blue-300 font-black uppercase mb-1">Total Users</p>
                    <h4 id="stat-users" class="text-3xl font-black text-white">0</h4>
                </div>
            </div>

            <div class="flex gap-2 mb-10 overflow-x-auto pb-4 no-scrollbar">
                <button onclick="showAdmTab('requests')" class="bg-cyan-600 px-6 py-3 rounded-2xl text-[10px] font-black uppercase shrink-0">📦 Orders</button>
                <button onclick="showAdmTab('users')" class="bg-indigo-600 px-6 py-3 rounded-2xl text-[10px] font-black uppercase shrink-0">👥 Users</button>
                <button onclick="showAdmTab('tools')" class="bg-pink-600 px-6 py-3 rounded-2xl text-[10px] font-black uppercase shrink-0">🛠️ Tools</button>
                <button onclick="showAdmTab('broadcast')" class="bg-yellow-600 px-6 py-3 rounded-2xl text-[10px] font-black uppercase shrink-0">📢 News</button>
            </div>

            <div id="adm-sec-requests" class="adm-tab space-y-4"></div>
            <div id="adm-sec-users" class="adm-tab hidden space-y-3"></div>
            
            <div id="adm-sec-tools" class="adm-tab hidden space-y-6">
                <div class="glass p-8 rounded-[3rem] border-t-4 border-cyan-500 shadow-xl">
                    <h3 class="text-[10px] font-black uppercase mb-6 text-center text-cyan-400 italic tracking-widest">Inject Capital (Manual)</h3>
                    <input type="text" id="adm-user" placeholder="Target User Name" class="w-full bg-slate-900 p-4 rounded-xl mb-2 text-center font-black text-xs border border-white/10 outline-none">
                    <input type="number" id="adm-val" placeholder="Amount (+ or -)" class="w-full bg-slate-900 p-4 rounded-xl mb-6 text-center font-black text-xs border border-white/10 outline-none">
                    <div class="grid grid-cols-2 gap-3">
                        <button onclick="manualEdit('balance')" class="bg-cyan-600 py-4 rounded-xl text-[9px] font-black uppercase shadow-lg">To Balance</button>
                        <button onclick="manualEdit('profit')" class="bg-green-600 py-4 rounded-xl text-[9px] font-black uppercase shadow-lg">To Profit</button>
                    </div>
                </div>
            </div>

            <div id="adm-sec-broadcast" class="adm-tab hidden glass p-8 rounded-[3rem] border-t-4 border-yellow-500 shadow-xl">
                <h3 class="text-[10px] font-black uppercase mb-4 text-center text-yellow-500 italic">Broadcast News Ticker</h3>
                <textarea id="bc-msg" placeholder="Write global update..." class="w-full bg-slate-900 p-5 rounded-2xl text-xs border border-white/10 outline-none mb-4 h-24 font-bold"></textarea>
                <button onclick="sendBroadcast()" class="w-full bg-yellow-600 py-4 rounded-xl font-black text-[10px] uppercase shadow-lg">🚀 Blast Update</button>
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass-blue border-t border-white/10 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem] shadow-[0_-10px_40px_rgba(0,0,0,0.5)]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-1 active-tab transition-all">🏠<span class="text-[8px] font-black uppercase">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-1 transition-all">📈<span class="text-[8px] font-black uppercase">Fleet</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-1 transition-all">📜<span class="text-[8px] font-black uppercase">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-1 transition-all">⚙️<span class="text-[8px] font-black uppercase">Firm</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            { n: "Micro-Elite I", p: 200, r: 3, d: 30, c: "blue" }, { n: "Micro-Elite II", p: 500, r: 3.2, d: 30, c: "cyan" },
            { n: "Bronze-S", p: 1000, r: 3.5, d: 30, c: "indigo" }, { n: "Bronze-X", p: 2000, r: 3.8, d: 30, c: "purple" },
            { n: "Gold-Master", p: 15000, r: 6, d: 30, c: "yellow" }, { n: "Royal-Grand", p: 100000, r: 10, d: 30, c: "green" },
            { n: "⚡ FLASH 24H", p: 500, r: 12, s: true, c: "pink" }, { n: "👑 CROWN KING", p: 25000, r: 22, s: true, c: "orange" }
        ];

        window.onload = () => { const saved = localStorage.getItem('mc_user'); if(saved) { document.getElementById('user-name').value = saved; login(); } setInterval(updateCountdown, 1000); listenForPromos(); };

        function listenForPromos() { db.collection("app_data").doc("announcement").onSnapshot(doc => { if(doc.exists) { document.getElementById('promo-ticker').classList.remove('hidden'); document.getElementById('ticker-text').innerText = doc.data().message; } }); }

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return; localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierExpiry: 0, tierName: "Inactive" });
            startSync(name); document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans(); 
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); document.getElementById('tier-tag').innerText = user.tierName; checkProfitReq(user); } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = snap.empty ? '<p class="text-center opacity-30 py-10 text-[10px] font-black uppercase italic tracking-widest">No Logs Found</p>' : '';
                let items = []; snap.forEach(doc => items.push(doc.data())); items.sort((a,b) => b.time - a.time);
                items.forEach(d => { 
                    const clr = d.type.includes('dep') ? 'border-green-500' : 'border-blue-500'; 
                    list.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center border-l-4 ${clr} mb-2 text-[9px] font-black uppercase"><div>${d.type}<br><span class="opacity-30 text-[7px]">${new Date(d.time).toLocaleString()}</span></div><div class="text-right text-white">₨ ${d.amount}<br><span class="badge-${d.status}">${d.status}</span></div></div>`; 
                });
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                const border = p.s ? 'border-2 border-pink-500 shadow-[0_0_15px_rgba(236,72,153,0.3)]' : 'border border-white/10';
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass p-6 rounded-[2.5rem] flex justify-between items-center active:scale-95 cursor-pointer ${border} shadow-lg"><div><h4 class="font-black text-xs uppercase tracking-tight text-white">${p.n}</h4><p class="text-[10px] text-${p.c}-400 font-black uppercase">${p.r}% Yield</p></div><div class="text-right"><p class="font-black text-lg text-white">₨ ${p.p.toLocaleString()}</p></div></div>`;
            });
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; const m = document.getElementById('dep-method').value; const f = document.getElementById('dep-proof');
            if(!a || !t || !f.files[0]) return alert("Please fill all details!");
            const r = new FileReader(); r.readAsDataURL(f.files[0]); r.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, method: m, proof: r.result, type: "deposit", status: "pending", time: Date.now() });
                alert("Deposit Pending Verification!"); changePage('activity');
            };
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value; if(!a || !acc || parseInt(a) > user.balance) return alert("Check amount and balance!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) }); alert("Withdrawal Requested!");
        }

        async function applyPromoRequest() {
            const c = document.getElementById('promo-input').value.trim(); if(!c) return;
            await db.collection("requests").add({ user: user.name, amount: 0, type: "Promo Request ("+c+")", status: "pending", time: Date.now() });
            alert("Promo Code Submitted!");
        }

        async function buy(p, roi, tName) { if(user.balance < p) { alert("Low Wallet Balance!"); changePage('wallet'); } else { if(confirm("Activate Asset "+tName+"?")) { const exp = Date.now() + (30 * 86400000); await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: tName, tierExpiry: exp, lastReqTime: Date.now() }); await db.collection("requests").add({ user: user.name, amount: p, type: "Fleet Activation", status: "approved", time: Date.now() }); alert("Fleet Active!"); } } }
        async function checkProfitReq(u) { if (u.activeTier > 0 && (Date.now() - u.lastReqTime) >= 86400000) { const amt = (u.activeTier * u.tierROI) / 100; await db.collection("requests").add({ user: u.name, amount: amt, type: "Daily Profit Yield", status: "pending", time: Date.now() }); await db.collection("users").doc(u.name).update({ lastReqTime: Date.now() }); } }
        function updateCountdown() { if (user && user.activeTier > 0) { const d = (user.lastReqTime + 86400000) - Date.now(); if(d>0) { const h = Math.floor(d/3600000); const m = Math.floor((d%3600000)/60000); document.getElementById('countdown-display').innerText = `YIELD SYNC IN: ${h}H ${m}M`; } else { document.getElementById('countdown-display').innerText = "YIELD READY"; } } else { document.getElementById('countdown-display').innerText = "VAULT IDLE"; } }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('n-'+p).classList.add('active-tab'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("Security Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function showAdmTab(t) { document.querySelectorAll('.adm-tab').forEach(s=>s.classList.add('hidden')); document.getElementById('adm-sec-'+t).classList.remove('hidden'); }

        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = ''; document.getElementById('stat-pending').innerText = snap.size;
                snap.forEach(doc => { const d = doc.data(); 
                    const pBtn = d.proof ? `<button onclick="window.open().document.write('<img src=\\'${d.proof}\\' style=\\'width:100%\\' />')" class="bg-indigo-600 px-4 py-2 rounded-xl text-[10px] font-black">VIEW PROOF</button>` : '';
                    list.innerHTML += `<div class="glass p-6 rounded-[2rem] flex flex-col text-[10px] font-black uppercase mb-4 shadow-xl border border-white/10">
                        <div class="flex justify-between items-center mb-2"><span class="text-cyan-400 font-black">${d.user}</span><span class="text-white">Rs ${d.amount}</span></div>
                        <div class="grid grid-cols-1 gap-1 opacity-60 text-[8px] mb-4">
                            <div>TYPE: ${d.type}</div>
                            ${d.tid ? `<div class="text-green-400">TID: ${d.tid}</div>` : ''}
                            ${d.method ? `<div class="text-yellow-400">VIA: ${d.method}</div>` : ''}
                            ${d.acc ? `<div class="text-red-400 bg-black/40 p-2 rounded mt-1 border border-white/5 lowercase">${d.acc}</div>` : ''}
                        </div>
                        <div class="flex gap-2">${pBtn}<button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="flex-1 bg-green-600 py-3 rounded-xl">APPROVE</button><button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="flex-1 bg-red-600 py-3 rounded-xl">REJECT</button></div>
                    </div>`;
                });
            });
            db.collection("users").onSnapshot(snap => { 
                const list = document.getElementById('adm-sec-users'); list.innerHTML = ''; document.getElementById('stat-users').innerText = snap.size;
                snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center text-[10px] mb-2 uppercase border border-white/10 shadow-lg"><div><span class="text-cyan-400 font-black">${u.name}</span><br><span class="opacity-30 text-[8px]">Bal: ${u.balance}</span></div><button onclick="deleteUser('${u.name}')" class="text-red-500 font-black text-lg">×</button></div>`; }); 
            });
        }

        async function handle(id, u, amt, act, type) { 
            const ref = db.collection("users").doc(u); const doc = await ref.get(); let finalAmt = amt;
            if(act==='approved') { 
                if(type.includes('Promo Request')) { const bonus = prompt("Bonus Amount:"); if(!bonus) return; finalAmt = parseInt(bonus); }
                if(type==='deposit' || type.includes('Profit') || type.includes('Promo')) { await ref.update({ balance: (doc.data().balance||0) + finalAmt }); }
            } else if(act==='rejected' && type==='withdraw') { await ref.update({ balance: (doc.data().balance||0) + amt }); }
            await db.collection("requests").doc(id).update({ status: act, amount: finalAmt }); 
        }

        async function manualEdit(f) { const u = document.getElementById('adm-user').value; const v = parseInt(document.getElementById('adm-val').value); const ref = db.collection("users").doc(u); const d = await ref.get(); if(d.exists) await ref.update({ [f]: (d.data()[f]||0)+v }); alert("Update Finalized!"); }
        async function deleteUser(n) { if(confirm("Permanently delete vault for "+n+"?")) await db.collection("users").doc(n).delete(); }
        async function sendBroadcast() { const m = document.getElementById('bc-msg').value; await db.collection("app_data").doc("announcement").set({ message: m }); alert("Global News Live!"); }
        function sendSupport() { const m = document.getElementById('support-msg').value; if(!m) return; db.collection("requests").add({ user: user.name, amount: 0, type: "Support Req: "+m, status: "pending", time: Date.now() }); alert("Message Dispatched!"); document.getElementById('support-msg').value = ''; }
    </script>
</body>
</html>
       
