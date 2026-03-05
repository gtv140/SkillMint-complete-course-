<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Private Equity & Asset Management</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;800&display=swap');
        body { font-family: 'Outfit', sans-serif; background: #010409; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(50px); border: 1px solid rgba(255,255,255,0.07); border-radius: 2.5rem; }
        .neon-glow { box-shadow: 0 0 20px rgba(59, 130, 246, 0.2); border: 1px solid rgba(59, 130, 246, 0.4); }
        .page { display: none; animation: slideIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes slideIn { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .active-tab { color: #3b82f6; position: relative; }
        .active-tab::after { content: ''; position: absolute; bottom: -10px; left: 25%; width: 50%; height: 3px; background: #3b82f6; border-radius: 10px; box-shadow: 0 0 10px #3b82f6; }
        .fake-popup { position: fixed; bottom: 100px; left: 20px; right: 20px; z-index: 5000; transform: translateY(200%); transition: 0.5s; }
        .show-popup { transform: translateY(0); }
        .special-card { background: linear-gradient(135deg, #1e1b4b 0%, #312e81 100%); border: 1px solid #6366f1; }
        .admin-card { background: #0d1117; border: 1px solid #30363d; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="popup" class="fake-popup glass p-4 flex items-center gap-4 border-l-4 border-green-500">
        <div class="bg-green-500/20 p-2 rounded-full text-xl">💰</div>
        <div>
            <p id="pop-text" class="text-[10px] font-bold uppercase"></p>
            <p class="text-[8px] opacity-50">Just now • Secured by MintCrest</p>
        </div>
    </div>

    <div id="broadcast-bar" class="hidden bg-blue-600 text-white text-[9px] font-black uppercase py-2 text-center tracking-widest">
        📢 BROADCAST: <span id="broadcast-msg">---</span>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[6000] bg-[#010409] flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 onclick="adminTap()" class="text-5xl font-black italic tracking-tighter mb-2 uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-[8px] uppercase tracking-[0.5em] text-gray-500 font-bold mb-12">Institutional Grade Asset Management</p>
            <div class="glass p-10 neon-glow">
                <input type="text" id="user-name" placeholder="Protocol Identity" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-4 focus:border-blue-500 text-sm">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[11px] uppercase tracking-widest shadow-2xl active:scale-95 transition-all">Secure Login</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div class="p-8 pb-2 flex justify-between items-end">
            <div>
                <p class="text-[8px] font-black text-blue-500 uppercase mb-1 tracking-widest">Authorized Node</p>
                <h1 class="text-2xl font-black tracking-tighter" id="u-name">--</h1>
            </div>
            <div class="flex gap-2">
                <button onclick="changePage('chat')" class="glass p-3 relative">💬 <span id="chat-dot" class="hidden absolute top-2 right-2 w-2 h-2 bg-red-500 rounded-full"></span></button>
                <button onclick="changePage('about')" class="glass p-3">🏛️</button>
            </div>
        </div>

        <div id="p-home" class="page active-page p-6">
            <div class="glass p-10 neon-glow mb-6 relative overflow-hidden bg-gradient-to-br from-blue-900/20 to-transparent">
                <p class="text-[10px] font-black text-blue-400 uppercase tracking-widest mb-2">Vault Balance</p>
                <h2 class="text-5xl font-extrabold tracking-tighter mb-8" id="v-bal">₨ 0</h2>
                
                <div id="timer-box" class="hidden bg-black/40 p-4 rounded-3xl border border-white/5">
                    <div class="flex justify-between text-[8px] font-black uppercase mb-2">
                        <span>Cycle Progression</span>
                        <span id="timer-txt" class="text-blue-400">00:00:00</span>
                    </div>
                    <div class="w-full h-1.5 bg-white/10 rounded-full overflow-hidden">
                        <div id="progress-bar" class="h-full bg-blue-500 transition-all duration-1000" style="width: 0%"></div>
                    </div>
                </div>

                <div class="grid grid-cols-2 gap-4 mt-8 pt-6 border-t border-white/10">
                    <div><p class="text-[8px] font-black text-gray-500 uppercase">Yield Status</p><p id="v-profit" class="text-xl font-bold text-green-400">₨ 0</p></div>
                    <div class="text-right"><p class="text-[8px] font-black text-gray-500 uppercase">Fleet</p><p id="v-tier" class="text-[10px] font-bold text-blue-400 uppercase italic">None</p></div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('deposit')" class="glass p-8 flex flex-col items-center border-b-4 border-blue-600 shadow-xl"><span class="text-3xl mb-2">💎</span><span class="text-[10px] font-black uppercase">Fund</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 flex flex-col items-center border-b-4 border-red-600 shadow-xl"><span class="text-3xl mb-2">🏧</span><span class="text-[10px] font-black uppercase">Payout</span></button>
            </div>

            <button onclick="changePage('promo')" class="w-full glass p-6 border-2 border-dashed border-blue-500/50 flex justify-between items-center px-10">
                <span class="text-[10px] font-black uppercase tracking-widest">Apply Promo Code</span>
                <span class="text-blue-400">🎟️</span>
            </button>
        </div>

        <div id="p-invest" class="page p-6 pb-20">
            <h2 class="text-center font-black uppercase text-sm tracking-widest mb-8">Investment Fleets</h2>
            <div id="plans-grid" class="space-y-3"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black uppercase text-sm tracking-widest mb-8">Asset Ledger</h2>
            <div id="ledger-list" class="space-y-3"></div>
        </div>

        <div id="p-chat" class="page p-6">
            <div class="glass flex flex-col h-[70vh]">
                <div class="p-6 border-b border-white/10 text-center font-black uppercase text-[10px]">Help Desk Support</div>
                <div id="chat-box" class="flex-1 overflow-y-auto p-6 space-y-4 text-[11px]"></div>
                <div class="p-4 flex gap-2">
                    <input type="text" id="chat-msg" placeholder="Type your issue..." class="flex-1 bg-white/5 p-4 rounded-xl outline-none text-xs">
                    <button onclick="sendMsg()" class="bg-blue-600 px-6 rounded-xl font-bold">Send</button>
                </div>
            </div>
        </div>

        <div id="p-promo" class="page p-6">
            <div class="glass p-10 text-center border-t-8 border-yellow-500">
                <h3 class="font-black uppercase mb-6">Redeem Voucher</h3>
                <input type="text" id="promo-inp" placeholder="Enter Code" class="w-full bg-white/5 p-5 rounded-2xl mb-6 text-center font-black uppercase outline-none border border-white/10">
                <button onclick="applyPromo()" class="w-full bg-yellow-600 py-5 rounded-2xl font-black text-[10px] uppercase">Verify Code</button>
            </div>
        </div>

        <div id="p-about" class="page p-6 space-y-4">
            <div class="glass p-8">
                <h3 class="font-black text-blue-500 mb-4 uppercase">About MintCrest</h3>
                <p class="text-[10px] leading-relaxed opacity-70">MintCrest Gold is a leading private equity firm specialized in gold trading and institutional asset management. Registered under Global Security Protocols, we provide 100% insured returns through diversified portfolios. Our mission is to democratize elite-level finance for secure future growth.</p>
            </div>
            <div class="glass p-8">
                <h3 class="font-black text-red-500 mb-4 uppercase">Privacy Policy</h3>
                <p class="text-[10px] leading-relaxed opacity-70">We use AES-256 bit encryption to secure your data. Your identity is private and never shared with 3rd parties. All transactions are logged on our private ledger for complete audit transparency.</p>
            </div>
            <div class="glass p-6 flex justify-between items-center" onclick="window.open('https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3')">
                <span class="text-[10px] font-black uppercase">Official Community Group</span>
                <span class="text-green-500">↗️</span>
            </div>
            <div class="glass p-6 text-center">
                <p class="text-[8px] font-bold opacity-30 mb-2 uppercase">Your Referral Link</p>
                <div id="ref-link" class="text-[9px] font-black text-blue-400 truncate mb-4">--</div>
                <button onclick="copyRef()" class="bg-blue-600 px-10 py-2 rounded-lg text-[9px] font-black uppercase">Copy Link</button>
            </div>
            <button onclick="logout()" class="w-full glass p-6 text-red-500 text-[10px] font-black uppercase">Logout Session</button>
        </div>

        <div id="p-deposit" class="page p-6">
            <div class="glass p-10 border-t-8 border-blue-600">
                <h2 class="text-center font-black uppercase mb-8">Inject Capital</h2>
                <div class="space-y-3 mb-8">
                    <div class="glass p-4 rounded-xl flex justify-between text-[10px]"><span>JazzCash/SadaPay</span><span class="font-black text-blue-400">03705519562</span></div>
                    <div class="glass p-4 rounded-xl flex justify-between text-[10px]"><span>EasyPaisa</span><span class="font-black text-green-400">03379827882</span></div>
                </div>
                <input type="number" id="d-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 outline-none text-center font-bold">
                <input type="text" id="d-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-5 rounded-2xl mb-8 outline-none text-center font-bold uppercase">
                <button onclick="submitReq('deposit')" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[10px] uppercase shadow-xl">Confirm Funding</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 border-t-8 border-red-600">
                <h2 class="text-center font-black uppercase mb-8">Request Payout</h2>
                <input type="number" id="w-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 outline-none text-center font-bold">
                <input type="text" id="w-acc" placeholder="Account Details (Bank/Wallet)" class="w-full bg-white/5 p-5 rounded-2xl mb-8 outline-none text-center font-bold">
                <button onclick="submitReq('withdraw')" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[10px] uppercase shadow-xl">Secure Withdrawal</button>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-0 left-0 w-full glass rounded-t-[3.5rem] p-10 flex justify-around items-center z-[2000] border-t border-white/5">
        <button onclick="changePage('home')" id="n-home" class="active-tab">🏛️</button>
        <button onclick="changePage('invest')" id="n-invest">💎</button>
        <button onclick="changePage('activity')" id="n-activity">📑</button>
        <button onclick="changePage('about')" id="n-about">⚙️</button>
    </nav>

    <div id="admin-panel" class="fixed inset-0 bg-black z-[9000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-xl font-black italic">ULTRA <span class="text-blue-500">GOD MODE</span></h2>
            <button onclick="closeAdmin()" class="bg-red-500 px-4 py-2 rounded-xl text-[8px] font-bold">CLOSE</button>
        </div>

        <div class="grid grid-cols-2 gap-3 mb-6">
            <div class="glass p-5 text-center"><p class="text-[7px] font-black opacity-40 uppercase">Global Nodes</p><h4 id="adm-total" class="text-xl font-black">0</h4></div>
            <div class="glass p-5 text-center border-yellow-500/30"><p class="text-[7px] font-black opacity-40 uppercase">Pending Task</p><h4 id="adm-pending" class="text-xl font-black">0</h4></div>
        </div>

        <div class="glass p-6 mb-6">
            <h3 class="text-[8px] font-black uppercase mb-4 text-center opacity-40 italic">Asset & User Control</h3>
            <input type="text" id="adm-u" placeholder="Target Username" class="w-full bg-white/5 p-4 rounded-xl mb-2 text-[10px] outline-none">
            <input type="number" id="adm-v" placeholder="Amount / Value" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-[10px] outline-none">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="admAction('balance')" class="bg-blue-600 py-4 rounded-xl font-black text-[8px]">ADD BAL</button>
                <button onclick="admAction('profit')" class="bg-green-600 py-4 rounded-xl font-black text-[8px]">ADD PROF</button>
                <button onclick="admAction('ban')" class="bg-red-600 py-4 rounded-xl font-black text-[8px]">BAN USER</button>
                <button onclick="admAction('unban')" class="bg-white/20 py-4 rounded-xl font-black text-[8px]">UNBAN</button>
            </div>
            <button onclick="admAction('broadcast')" class="w-full bg-purple-600 py-4 rounded-xl font-black text-[8px] mt-2 uppercase">Set Global Broadcast</button>
        </div>

        <div id="adm-list" class="space-y-3"></div>
    </div>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            {n:"Protocol X", p:100, r:2.5, d:30}, {n:"Bronze Node", p:500, r:3, d:30}, {n:"Silver Fleet", p:1000, r:3.5, d:30},
            {n:"Gold Prime", p:2500, r:4, d:30}, {n:"Platinum Elite", p:5000, r:4.5, d:30}, {n:"Diamond Vault", p:7500, r:5, d:30},
            {n:"Titanium", p:10000, r:5.5, d:30}, {n:"Master", p:15000, r:6, d:30}, {n:"Royal", p:20000, r:7, d:30},
            {n:"Emperor", p:30000, r:8, d:30}, {n:"Prime X", p:40000, r:9, d:30}, {n:"Zenith", p:50000, r:10, d:30},
            {n:"⚡ FLASH 24H", p:500, r:20, s:true}, {n:"🚀 ROCKET", p:5000, r:25, s:true}, {n:"👑 CROWN", p:15000, r:30, s:true},
            {n:"🔥 NOVA", p:2000, r:18, s:true}, {n:"🪐 COSMOS", p:50000, r:40, s:true}, {n:"🧿 ORACLE", p:10000, r:22, s:true},
            {n:"🛸 NEBULA", p:25000, r:28, s:true}, {n:"🔱 ZEUS", p:100000, r:50, s:true}, {n:"🦁 LION", p:3500, r:15, s:true},
            {n:"🦅 HAWK", p:8000, r:16, d:30}, {n:"🐢 TURTLE", p:200, r:5, d:30}, {n:"🐺 WOLF", p:12000, r:18, d:30}, {n:"🐉 DRAGON", p:150000, r:60, s:true}
        ];

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(doc.exists && doc.data().banned) return alert("Your node has been suspended by Admin!");
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Unlinked", banned: false, unread: false });
            
            localStorage.setItem('mc_user', name);
            document.getElementById('u-name').innerText = name;
            document.getElementById('ref-link').innerText = window.location.origin + window.location.pathname + "?ref=" + name;
            startSync(name);
            document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans(); startPopups();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { 
                if(doc.exists) { 
                    user = doc.data(); 
                    document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); 
                    document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); 
                    document.getElementById('v-tier').innerText = user.tierName;
                    if(user.unread) document.getElementById('chat-dot').classList.remove('hidden');
                    if(user.activeTier > 0) document.getElementById('timer-box').classList.remove('hidden');
                } 
            });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('ledger-list'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-5 flex justify-between items-center text-[10px] font-black uppercase"><div>${d.type}<br><span class="opacity-30 text-[7px]">${new Date(d.time).toLocaleString()}</span></div><div class="text-right">₨ ${d.amount}<br><span class="text-blue-500">${d.status}</span></div></div>`; });
            });
            db.collection("messages").where("user", "==", name).orderBy("time").onSnapshot(snap => {
                const box = document.getElementById('chat-box'); box.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); box.innerHTML += `<div class="${d.side==='admin' ? 'bg-blue-600/20 mr-10' : 'bg-white/5 ml-10 text-right'} p-4 rounded-2xl">${d.text}</div>`; });
                box.scrollTop = box.scrollHeight;
            });
            db.collection("config").doc("broadcast").onSnapshot(doc => { if(doc.exists && doc.data().msg) { document.getElementById('broadcast-bar').classList.remove('hidden'); document.getElementById('broadcast-msg').innerText = doc.data().msg; } else { document.getElementById('broadcast-bar').classList.add('hidden'); } });
            setInterval(updateTimer, 1000);
        }

        function updateTimer() {
            if (user && user.activeTier > 0) {
                const diff = ((user.lastReqTime || Date.now()) + 86400000) - Date.now();
                if(diff > 0) {
                    const h = Math.floor(diff/3600000), m = Math.floor((diff%3600000)/60000), s = Math.floor((diff%60000)/1000);
                    document.getElementById('timer-txt').innerText = `${String(h).padStart(2,'0')}:${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
                    document.getElementById('progress-bar').style.width = (100 - (diff/86400000*100)) + "%";
                } else { document.getElementById('timer-txt').innerText = "READY"; document.getElementById('progress-bar').style.width = "100%"; }
            }
        }

        function renderPlans() {
            const list = document.getElementById('plans-grid'); list.innerHTML = '';
            plans.forEach(p => {
                const isS = p.s === true;
                const info = isS ? `<p class="text-[9px] text-blue-300 font-bold mt-1 uppercase">Special Yield: ${p.r}%</p>` : `<p class="text-[9px] text-green-500 font-bold mt-1 uppercase">${p.r}% Daily • ⏳ ${p.d} Days</p>`;
                list.innerHTML += `<div onclick="buyFleet(${p.p}, ${p.r}, '${p.n}')" class="${isS ? 'special-card' : 'glass'} p-7 flex justify-between items-center active:scale-95 transition-all mb-2 cursor-pointer shadow-xl">
                    <div><h4 class="font-black text-[11px] uppercase">${p.n}</h4>${info}</div>
                    <div class="text-right"><p class="font-black text-sm">₨ ${p.p.toLocaleString()}</p></div>
                </div>`;
            });
        }

        async function buyFleet(p, r, n) {
            if(user.balance < p) return alert("Capital Required!");
            if(confirm(`Invest in ${n}?`)) {
                await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: r, tierName: n, lastReqTime: Date.now() });
                alert("Fleet Activated!"); changePage('activity');
            }
        }

        async function submitReq(type) {
            const a = document.getElementById(type==='deposit'?'d-amt':'w-amt').value;
            const e = document.getElementById(type==='deposit'?'d-trx':'w-acc').value;
            if(!a || !e) return alert("Fill all details!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), extra: e, type: type, status: "pending", time: Date.now() });
            if(type==='withdraw') await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) });
            alert("Request Registered, sweetie! 😘"); changePage('activity');
        }

        async function applyPromo() {
            const c = document.getElementById('promo-inp').value.toUpperCase(); if(!c) return;
            await db.collection("requests").add({ user: user.name, amount: 0, extra: c, type: "promocode", status: "pending", time: Date.now() });
            alert("Voucher submitted for audit!"); changePage('activity');
        }

        async function sendMsg() {
            const t = document.getElementById('chat-msg').value; if(!t) return;
            await db.collection("messages").add({ user: user.name, text: t, side: "user", time: Date.now() });
            document.getElementById('chat-msg').value = '';
        }

        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page'));
            document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab'));
            document.getElementById('p-'+p).classList.add('active-page');
            if(document.getElementById('n-'+p)) document.getElementById('n-'+p).classList.add('active-tab');
            if(p==='chat') { document.getElementById('chat-dot').classList.add('hidden'); db.collection("users").doc(user.name).update({ unread: false }); }
        }

        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function copyRef() { navigator.clipboard.writeText(document.getElementById('ref-link').innerText); alert("Referral Link Copied!"); }

        // --- POPUPS ---
        function startPopups() {
            const names = ["Ahmed", "Sajid", "Maria", "Zia", "Fatima", "Usman", "Hamza", "Irfan", "Zoya", "Kashif"];
            const amounts = [1200, 5000, 8500, 15000, 2500, 4500, 12000, 20000, 500, 9500];
            setInterval(() => {
                const n = names[Math.floor(Math.random()*names.length)];
                const a = amounts[Math.floor(Math.random()*amounts.length)];
                document.getElementById('pop-text').innerText = `${n} just withdrew ₨ ${a.toLocaleString()}`;
                document.getElementById('popup').classList.add('show-popup');
                setTimeout(() => document.getElementById('popup').classList.remove('show-popup'), 4000);
            }, 12000);
        }

        // --- GOD MODE ---
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("ACCESS CODE:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function syncAdm() {
            db.collection("users").onSnapshot(s => document.getElementById('adm-total').innerText = s.size);
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                document.getElementById('adm-pending').innerText = snap.size;
                const l = document.getElementById('adm-list'); l.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); l.innerHTML += `<div class="glass p-5 flex justify-between items-center text-[9px] font-black uppercase mb-3"><div>${d.user}<br>₨ ${d.amount} (${d.type})<br><span class="text-blue-400">${d.extra}</span></div><div class="flex gap-2"><button onclick="handleAdm('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-3 py-1 rounded">OK</button><button onclick="handleAdm('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="bg-red-500 px-3 py-1 rounded">NO</button></div></div>`; });
            });
        }
        async function admAction(act) {
            const u = document.getElementById('adm-u').value; const v = document.getElementById('adm-v').value;
            const ref = db.collection("users").doc(u);
            if(act==='broadcast') await db.collection("config").doc("broadcast").set({ msg: v });
            else if(act==='ban') await ref.update({ banned: true });
            else if(act==='unban') await ref.update({ banned: false });
            else { const d = await ref.get(); if(d.exists) await ref.update({ [act]: (d.data()[act]||0) + parseInt(v) }); }
            alert("Protocol Executed!");
        }
        async function handleAdm(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u); const doc = await ref.get();
            if(act==='approved') {
                if(type==='deposit' || type==='promocode') await ref.update({ balance: (doc.data().balance||0) + (type==='promocode' ? parseInt(prompt("Enter Bonus Amount:")) : amt) });
            } else if(act==='rejected' && type==='withdraw') await ref.update({ balance: (doc.data().balance||0) + amt });
            await db.collection("requests").doc(id).update({ status: act });
        }

        window.onload = () => { if(localStorage.getItem('mc_user')) { document.getElementById('user-name').value = localStorage.getItem('mc_user'); login(); } };
    </script>
</body>
</html>
