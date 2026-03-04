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
        .badge-pending { background: rgba(234, 179, 8, 0.1); color: #eab308; padding: 2px 4px; border-radius: 4px; font-size: 7px; }
        .badge-approved { background: rgba(34, 197, 94, 0.1); color: #22c55e; padding: 2px 4px; border-radius: 4px; font-size: 7px; }
        .badge-rejected { background: rgba(239, 68, 68, 0.1); color: #ef4444; padding: 2px 4px; border-radius: 4px; font-size: 7px; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="promo-ticker" class="bg-blue-600/20 border-b border-blue-500/30 py-2 overflow-hidden z-[500]">
        <div class="animate-marquee text-[10px] font-black uppercase tracking-widest text-blue-400" id="broadcast-msg">
            Official Announcement: Upgrade your fleet for 2x profit speed!
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-5xl font-black italic mb-2 cursor-pointer uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[8px] uppercase tracking-[0.4em] mb-12 font-bold italic">Digital Asset Vault</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="Enter Full Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 text-white uppercase">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95">Access Account</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="flex justify-between items-center mb-6 px-2">
                <div>
                    <p class="text-[8px] text-gray-500 uppercase font-black">Welcome back,</p>
                    <h3 class="text-sm font-black text-blue-400" id="u-display-name">USER</h3>
                </div>
                <div class="text-right">
                    <p class="text-[8px] text-gray-500 uppercase font-black">Status</p>
                    <span id="u-status" class="text-[9px] font-black text-green-500">SECURE</span>
                </div>
            </div>

            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600 shadow-xl relative overflow-hidden">
                <p class="text-[9px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Total Capital</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                
                <div class="mt-6 grid grid-cols-2 gap-2">
                    <div class="bg-white/5 p-3 rounded-2xl">
                        <p class="text-[7px] text-gray-500 uppercase font-bold">Total Profit</p>
                        <p class="text-xs font-black text-green-400" id="v-profit">₨ 0</p>
                    </div>
                    <div class="bg-white/5 p-3 rounded-2xl">
                        <p class="text-[7px] text-gray-500 uppercase font-bold">Days Remaining</p>
                        <p class="text-xs font-black text-yellow-500" id="v-days">0 Days</p>
                    </div>
                </div>

                <div class="mt-6 pt-4 border-t border-white/5 text-center">
                    <p class="text-[7px] text-gray-500 uppercase font-black mb-1">Next Profit Distribution</p>
                    <div id="countdown-display" class="text-[12px] font-black text-blue-500 tracking-[0.2em]">00:00:00</div>
                </div>
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
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400">Transaction History</h2>
            <div id="user-history" class="space-y-2 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6 pb-12">
            <div class="glass p-8 rounded-[3rem] text-center border-t-4 border-green-500 shadow-lg">
                <h3 class="text-green-500 font-black text-[12px] uppercase mb-4 italic">Official Community</h3>
                <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3?mode=hqctcla" target="_blank" class="w-full bg-green-600 py-4 rounded-2xl font-black text-[10px] uppercase flex items-center justify-center gap-2">
                    <span>Join WhatsApp Group</span>
                </a>
            </div>

            <div class="glass p-8 rounded-[3rem] border-t-4 border-blue-500">
                <h3 class="text-blue-500 font-black text-[12px] uppercase mb-4 italic">VIP Promo Code</h3>
                <input type="text" id="special-promo" placeholder="ENTER CODE" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-black uppercase text-blue-400 outline-none border border-blue-500/20">
                <button onclick="claimPromo()" class="w-full bg-blue-600 py-3 rounded-2xl font-black text-[9px] uppercase">Redeem Now</button>
            </div>

            <div class="glass p-8 rounded-[3rem] border-t-4 border-red-500">
                <h3 class="text-red-500 font-black text-[12px] uppercase mb-4 italic">SOS Help Desk</h3>
                <textarea id="help-msg" placeholder="Tell admin your issue..." class="w-full bg-white/5 p-4 rounded-xl mb-4 text-[10px] font-bold outline-none border border-white/5 h-20 text-white"></textarea>
                <button onclick="sendHelp()" class="w-full bg-red-600 py-3 rounded-2xl font-black text-[9px] uppercase">Send Message</button>
            </div>

            <div class="glass p-8 rounded-[3rem]">
                <h3 class="text-gray-400 font-black text-[10px] uppercase mb-2">About MintCrest Gold</h3>
                <p class="text-[8px] text-gray-500 leading-relaxed font-bold uppercase">Leading Digital Asset Management Firm Established In 2026. We Use High-Frequency AI Nodes To Generate Secure Profits For Our Global Investors. Your Assets Are Protected By SHA-256 Vault Encryption.</p>
            </div>

            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-center text-[10px] font-black text-red-500 uppercase">Exit Vault</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-8 rounded-[3rem] border-t-8 border-blue-600">
                <h3 class="text-center font-black text-blue-500 mb-6 uppercase text-sm">Deposit Funds</h3>
                <div class="text-[9px] font-black mb-6 space-y-2 uppercase">
                    <p class="flex justify-between"><span>Easypaisa:</span> <span class="text-blue-400">03379827882</span></p>
                    <p class="flex justify-between"><span>JazzCash:</span> <span class="text-blue-400">03705519562</span></p>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center font-bold border border-white/5 text-white">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-4 rounded-xl mb-6 text-center font-bold border border-white/5 text-white uppercase">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase">Submit Deposit</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-8 rounded-[3rem] border-t-8 border-red-600">
                <h3 class="text-center font-black text-red-500 mb-6 uppercase text-sm">Withdraw Capital</h3>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center font-bold border border-white/5 text-white">
                <input type="text" id="wd-acc" placeholder="Account Name & Number" class="w-full bg-white/5 p-4 rounded-xl mb-6 text-center text-[10px] font-bold border border-white/5 text-white uppercase">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-5 rounded-2xl font-black text-[10px] uppercase">Confirm Payout</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
        <h2 class="text-xl font-black text-blue-500 uppercase mb-8 italic">Master Control Panel</h2>
        
        <div class="glass p-5 rounded-2xl mb-6">
            <h4 class="text-[10px] font-black uppercase mb-3">Broadcast Message</h4>
            <input type="text" id="adm-bc-msg" placeholder="Update ticker text..." class="w-full bg-white/5 p-3 rounded-xl text-xs mb-3 border border-white/10 text-white">
            <button onclick="updateBroadcast()" class="w-full bg-blue-600 py-2 rounded-xl text-[9px] font-black uppercase">Update Ticker</button>
        </div>

        <div id="adm-sec-requests" class="space-y-2"></div>
        <button onclick="closeAdmin()" class="w-full mt-10 bg-red-600 py-4 rounded-2xl text-[10px] font-black uppercase">Close Admin</button>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem]">
        <button onclick="changePage('home')" id="n-home" class="active-tab text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-2xl">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="text-2xl">📜</button>
        <button onclick="changePage('more')" id="n-more" class="text-2xl">🏢</button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            { n: "Micro-Elite I", p: 200, r: 3, d: 30 }, { n: "Bronze-S", p: 1000, r: 3.5, d: 30 },
            { n: "Silver-X", p: 5000, r: 4.5, d: 45 }, { n: "Gold-Master", p: 15000, r: 6, d: 60 },
            { n: "Diamond-X", p: 50000, r: 8, d: 90 }, { n: "👑 CROWN KING", p: 25000, r: 22, d: 15 }
        ];

        window.onload = () => { 
            const saved = localStorage.getItem('mc_user'); if(saved) { document.getElementById('user-name').value = saved; login(); } 
            setInterval(syncCountdown, 1000);
            syncBroadcast();
        };

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, activeTier: 0, tierROI: 0, tierExpiry: 0, lastReqTime: Date.now() });
            startSync(name);
            document.getElementById('u-display-name').innerText = name;
            document.getElementById('auth-ui').classList.add('hidden'); document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); updateUI(); checkProfitAuto(); } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                let items = []; snap.forEach(doc => items.push(doc.data())); items.sort((a,b) => b.time - a.time);
                items.forEach(d => { list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center mb-2 text-[8px] font-black uppercase"><div>${d.type}<br><span class="opacity-40 text-[6px]">${d.promo || d.msg || ''}</span></div><div>₨ ${d.amount} (<span class="badge-${d.status}">${d.status}</span>)</div></div>`; });
            });
        }

        function updateUI() {
            document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
            document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString();
            const left = user.tierExpiry ? Math.max(0, Math.ceil((user.tierExpiry - Date.now()) / 86400000)) : 0;
            document.getElementById('v-days').innerText = left + " Days";
        }

        function syncCountdown() {
            if(user && user.activeTier > 0) {
                const next = (user.lastReqTime || Date.now()) + 86400000;
                const diff = next - Date.now();
                if(diff > 0) {
                    const h = Math.floor(diff/3600000), m = Math.floor((diff%3600000)/60000), s = Math.floor((diff%60000)/1000);
                    document.getElementById('countdown-display').innerText = `${h.toString().padStart(2,'0')}:${m.toString().padStart(2,'0')}:${s.toString().padStart(2,'0')}`;
                } else document.getElementById('countdown-display').innerText = "CALCULATING...";
            }
        }

        function syncBroadcast() {
            db.collection("settings").doc("broadcast").onSnapshot(doc => { if(doc.exists) document.getElementById('broadcast-msg').innerText = doc.data().text; });
        }

        async function updateBroadcast() {
            const t = document.getElementById('adm-bc-msg').value; if(!t) return;
            await db.collection("settings").doc("broadcast").set({ text: t }); alert("Ticker Updated!");
        }

        async function checkProfitAuto() {
            if (user.activeTier > 0 && (Date.now() - user.lastReqTime) >= 86400000 && Date.now() < user.tierExpiry) {
                const amt = (user.activeTier * user.tierROI) / 100;
                await db.collection("requests").add({ user: user.name, amount: amt, type: "Daily Profit", status: "pending", time: Date.now() });
                await db.collection("users").doc(user.name).update({ lastReqTime: Date.now() });
            }
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, ${p.d}, '${p.n}')" class="glass p-5 rounded-[2.5rem] flex justify-between items-center active:scale-95 mb-2"><div><h4 class="font-black text-[10px] uppercase text-gray-400">${p.n}</h4><p class="text-[8px] text-green-400 font-bold uppercase">${p.r}% Daily • ${p.d} Days</p></div><p class="font-black text-[12px]">₨ ${p.p.toLocaleString()}</p></div>`;
            });
        }

        async function buy(p, roi, d, n) {
            if(user.balance < p) return alert("Insufficient Balance!");
            if(confirm(`Activate ${n} for ${d} Days?`)) {
                const exp = Date.now() + (d * 86400000);
                await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierExpiry: exp, lastReqTime: Date.now() });
                alert("Fleet Activated!");
            }
        }

        async function claimPromo() {
            const c = document.getElementById('special-promo').value.toUpperCase(); if(!c) return;
            await db.collection("requests").add({ user: user.name, amount: 0, type: "Promo Claim", promo: c, status: "pending", time: Date.now() });
            alert("Code Submitted!"); document.getElementById('special-promo').value = "";
        }

        async function sendHelp() {
            const m = document.getElementById('help-msg').value; if(!m) return;
            await db.collection("requests").add({ user: user.name, amount: 0, type: "Support", msg: m, status: "pending", time: Date.now() });
            alert("Sent to Admin!"); document.getElementById('help-msg').value = "";
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value, t = document.getElementById('dep-trx').value; if(!a || !t) return;
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, type: "Deposit", status: "pending", time: Date.now() });
            alert("Deposit Submitted!"); changePage('activity');
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value, acc = document.getElementById('wd-acc').value;
            if(!a || !acc || a > user.balance) return alert("Balance Error!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "Withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) });
            alert("Withdrawal Sent!");
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Master Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }

        function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); 
                    list.innerHTML += `<div class="glass p-4 flex justify-between items-center text-[7px] font-black mb-2 uppercase"><div>User: ${d.user}<br>Type: ${d.type}<br>Detail: ${d.amount || d.promo || d.msg}</div><div class="flex gap-2"><button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-3 py-2 rounded">YES</button><button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="bg-red-600 px-3 py-2 rounded">NO</button></div></div>`;
                });
            });
        }
        async function handle(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u); const doc = await ref.get();
            if(act === 'approved') {
                if(type === 'Deposit' || type === 'Daily Profit' || type === 'Promo Claim') {
                    let val = amt; if(amt === 0) val = parseInt(prompt("Enter amount to add:"));
                    await ref.update({ balance: (doc.data().balance||0) + val });
                }
            } else {
                if(type === 'Withdraw') await ref.update({ balance: (doc.data().balance||0) + amt }); // Refund if rejected
            }
            await db.collection("requests").doc(id).update({ status: act });
            alert("Handled!");
        }
    </script>
</body>
</html>
