<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Global Assets</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: fadeIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; } }
        .badge-pending { @apply bg-yellow-500/20 text-yellow-500 px-2 py-0.5 rounded text-[8px] font-bold uppercase; }
        .badge-approved { @apply bg-green-500/20 text-green-400 px-2 py-0.5 rounded text-[8px] font-bold uppercase; }
        .badge-rejected { @apply bg-red-500/20 text-red-500 px-2 py-0.5 rounded text-[8px] font-bold uppercase; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="h-full flex items-center justify-center p-6 bg-[#010409] z-[1000] absolute inset-0">
        <div class="glass w-full max-w-sm p-10 rounded-[3rem] text-center border-t-8 border-blue-600 shadow-2xl">
            <h1 onclick="adminTap()" class="text-3xl font-black italic tracking-tighter mb-2 cursor-pointer">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[9px] uppercase tracking-[0.3em] mb-10 text-center">Premium Asset Management</p>
            <input type="text" id="user-name" placeholder="Full Name" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 outline-none text-center mb-4 font-bold">
            <button onclick="login()" class="w-full bg-blue-600 py-4 rounded-2xl font-bold shadow-xl active:scale-95 transition uppercase text-xs">Access Portfolio</button>
        </div>
    </section>

    <div id="app-ui" class="hidden flex-1 overflow-y-auto pb-24">
        
        <div id="p-home" class="page active-page p-6">
            <div class="glass p-8 rounded-[2.5rem] mb-6 border-l-4 border-blue-500">
                <p class="text-[10px] text-gray-500 font-bold mb-1 uppercase tracking-widest">Available Funds</p>
                <h2 class="text-5xl font-black tracking-tighter text-blue-500" id="v-bal">₨ 0</h2>
                <div class="flex gap-2 mt-4">
                    <span class="text-[9px] bg-green-500/20 text-green-400 px-3 py-1 rounded-full font-bold uppercase">Profit: <span id="v-profit">₨ 0</span></span>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-8">
                <button onclick="changePage('wallet')" class="glass p-6 rounded-3xl text-center border-b-2 border-blue-500">📥<br><span class="text-[10px] font-bold uppercase mt-2 block tracking-widest">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-6 rounded-3xl text-center border-b-2 border-red-500">📤<br><span class="text-[10px] font-bold uppercase mt-2 block tracking-widest">Withdraw</span></button>
            </div>

            <h3 class="text-[10px] font-black text-gray-500 mb-4 uppercase tracking-[0.2em] text-center">Activity History</h3>
            <div id="user-history" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500">Global Tiers</h2>
            <div id="plans-list" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6">
            <div class="glass p-6 rounded-3xl mb-4 text-center">
                <h4 class="text-[10px] font-bold text-blue-400 mb-2 uppercase">Your Referral Link</h4>
                <input type="text" id="ref-link" readonly class="w-full bg-white/5 p-3 rounded-xl text-[10px] text-center border border-white/5 mb-2">
                <button onclick="copyRef()" class="bg-blue-600 px-6 py-2 rounded-xl font-bold text-[10px] uppercase">Copy Link</button>
            </div>
            <div class="glass p-6 rounded-3xl border-l-4 border-green-500">
                <h3 class="font-bold text-xs mb-1 uppercase">Support</h3>
                <p class="text-[10px] opacity-50 mb-4">Email: webhub262@gmail.com</p>
                <button onclick="window.location.href='https://wa.me/03379827882'" class="w-full bg-green-600 py-3 rounded-xl font-bold text-[10px] uppercase">WhatsApp Support</button>
            </div>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-8 rounded-[3rem] text-center">
                <div class="p-4 glass rounded-2xl mb-3 border border-red-500/20">
                    <p class="text-[8px] font-bold text-red-500 uppercase">JazzCash</p>
                    <p class="text-xl font-black">03705519562</p>
                </div>
                <div class="p-4 glass rounded-2xl mb-6 border border-green-500/20">
                    <p class="text-[8px] font-bold text-green-500 uppercase">EasyPaisa</p>
                    <p class="text-xl font-black">03705519562</p>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-3 text-center font-bold">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-4 text-center font-bold">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-xs uppercase shadow-xl">Submit Deposit</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-8 rounded-[3rem] text-center">
                <input type="number" id="wd-amt" placeholder="Withdraw Amount" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-3 text-center font-bold">
                <input type="text" id="wd-acc" placeholder="Account Number & Name" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-6 text-center font-bold text-xs">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-5 rounded-2xl font-black text-xs uppercase shadow-xl">Submit Withdrawal</button>
            </div>
        </div>
    </div>

    <div id="admin-panel" class="fixed inset-0 bg-[#010409] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-6">
            <h2 class="text-xl font-black text-blue-500 italic uppercase">Control Centre</h2>
            <button onclick="closeAdmin()" class="bg-red-500/20 text-red-500 px-4 py-2 rounded-xl text-[10px] font-bold uppercase">Exit</button>
        </div>
        <div class="grid grid-cols-2 gap-4 mb-6">
            <div class="glass p-4 rounded-2xl"><p class="text-[8px] opacity-50 uppercase">Users</p><h4 id="adm-total-users" class="text-xl font-black">0</h4></div>
            <div class="glass p-4 rounded-2xl"><p class="text-[8px] opacity-50 uppercase">Funds</p><h4 id="adm-total-deposits" class="text-xl font-black text-green-400">₨ 0</h4></div>
        </div>
        <h3 class="text-[10px] font-black text-gray-500 mb-4 uppercase text-center">Pending Approval</h3>
        <div id="adm-req-list" class="space-y-4 mb-10"></div>
        <div class="glass p-6 rounded-[2rem] border border-white/5">
            <input type="text" id="adm-user" placeholder="Target User Name" class="w-full bg-white/5 p-4 rounded-xl mb-2 text-center text-sm font-bold">
            <input type="number" id="adm-val" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-bold">
            <div class="grid grid-cols-2 gap-2 mb-4">
                <button onclick="manualEdit('balance')" class="bg-blue-600 py-3 rounded-xl font-bold text-[9px] uppercase">Add Balance</button>
                <button onclick="manualEdit('profit')" class="bg-green-600 py-3 rounded-xl font-bold text-[9px] uppercase">Add Profit</button>
            </div>
            <button onclick="deleteUser()" class="w-full bg-red-600/20 text-red-500 py-3 rounded-xl font-bold text-[9px] uppercase">Delete Account</button>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-4 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-1 active-tab">🏠<span class="text-[8px] font-bold mt-1">Portfolio</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-1">📈<span class="text-[8px] font-bold mt-1">Tiers</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-1">🛠️<span class="text-[8px] font-bold mt-1">Support</span></button>
        <button onclick="logout()" class="flex flex-col items-center gap-1 text-red-500/50">🚪<span class="text-[8px] font-bold mt-1">Exit</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;
        const plans = Array.from({length: 25}, (_, i) => ({ n: `Tier ${i+1}`, p: (i+1)*500, r: 35 + (i*5) }));

        window.onload = () => { const saved = localStorage.getItem('mintcrest_user'); if(saved) { document.getElementById('user-name').value = saved; login(); } };
        function adminTap() { tapCount++; if(tapCount >= 3) { tapCount = 0; if(prompt("Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } } setTimeout(() => tapCount = 0, 2000); }

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mintcrest_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0 });
            startSync(name); document.getElementById('auth-ui').style.display = 'none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('ref-link').value = `https://mintcrest.com/?ref=${name}`; renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = snap.empty ? '<p class="text-center opacity-20 text-[8px] py-10">NO LOGS FOUND</p>' : '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center border-l-2 ${d.type==='deposit'?'border-blue-500':'border-red-500'} mb-2 text-[10px] uppercase font-bold"><div>${d.type}<br><span class="opacity-50 text-[8px]">${new Date(d.time).toLocaleDateString()}</span></div><div class="text-right">₨ ${d.amount.toLocaleString()}<br><span class="badge-${d.status}">${d.status}</span></div></div>`; });
            });
        }

        function renderPlans() { const list = document.getElementById('plans-list'); list.innerHTML = ''; plans.forEach(p => { list.innerHTML += `<div onclick="buy(${p.p})" class="glass p-5 rounded-2xl flex justify-between items-center border-b border-white/5 active:scale-95 mb-2 transition cursor-pointer"><div><h4 class="font-bold text-[10px] uppercase">${p.n}</h4><p class="text-[9px] text-green-400 font-bold uppercase">ROI: ${p.r}%</p></div><p class="font-black text-blue-500 text-sm">₨ ${p.p.toLocaleString()}</p></div>`; }); }
        function copyRef() { const c = document.getElementById('ref-link'); c.select(); document.execCommand('copy'); alert("Link Copied!"); }
        
        async function submitDeposit() { 
            const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value;
            if(!a||!t) return alert("Fill all!"); 
            await db.collection("requests").add({user:user.name, amount:parseInt(a), tid:t, type:"deposit", status:"pending", time:Date.now()}); 
            alert("Sent! Waiting for approval."); changePage('home'); 
        }

        async function submitWithdraw() { 
            const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value;
            if(!a||!acc||a>user.balance) return alert("Low balance or empty field!"); 
            await db.collection("requests").add({user:user.name, amount:parseInt(a), acc:acc, type:"withdraw", status:"pending", time:Date.now()}); 
            await db.collection("users").doc(user.name).update({balance:user.balance-parseInt(a)}); 
            alert("Withdraw Requested!"); changePage('home'); 
        }

        async function buy(p) { if(user.balance<p) { alert("Low balance!"); changePage('wallet'); } else { await db.collection("users").doc(user.name).update({balance:user.balance-p}); alert("Tier Active!"); } }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(p!=='wallet'&&p!=='withdraw') document.getElementById('n-'+p).classList.add('active-tab'); }
        function logout() { localStorage.removeItem('mintcrest_user'); location.reload(); }

        // ADMIN FUNCTIONS
        function syncAdmin() {
            db.collection("users").onSnapshot(snap => document.getElementById('adm-total-users').innerText = snap.size);
            db.collection("requests").where("status", "==", "approved").where("type", "==", "deposit").onSnapshot(snap => { let t = 0; snap.forEach(d => t += d.data().amount); document.getElementById('adm-total-deposits').innerText = "₨ " + t.toLocaleString(); });
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-req-list'); list.innerHTML = snap.empty ? '<p class="text-center opacity-30 py-4 text-[10px] uppercase">Queue Empty</p>' : '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center border-l-4 ${d.type==='deposit'?'border-blue-500':'border-red-500'} mb-2 text-[9px] uppercase"><div class="text-left font-bold">${d.user}<br>₨ ${d.amount}<br><span class="opacity-50">${d.tid || d.acc}</span></div><div class="flex gap-2"><button onclick="handleReq('${doc.id}', '${d.user}', ${d.amount}, 'approved', '${d.type}')" class="bg-green-600 px-3 py-2 rounded-lg font-black">YES</button><button onclick="handleReq('${doc.id}', '${d.user}', ${d.amount}, 'rejected', '${d.type}')" class="bg-red-600 px-3 py-2 rounded-lg font-black">NO</button></div></div>`; });
            });
        }

        async function manualEdit(f) { 
            const u = document.getElementById('adm-user').value.trim(); const v = document.getElementById('adm-val').value;
            if(!u||!v) return alert("Error"); 
            const ref = db.collection("users").doc(u); const d = await ref.get();
            if(d.exists) { await ref.update({ [f]: (d.data()[f] || 0) + parseInt(v) }); alert("Done!"); } else alert("No user"); 
        }

        async function handleReq(id, target, amt, action, type) {
            const ref = db.collection("users").doc(target);
            if(action === 'approved' && type === 'deposit') { const d = await ref.get(); await ref.update({ balance: (d.data().balance || 0) + amt }); }
            else if(action === 'rejected' && type === 'withdraw') { const d = await ref.get(); await ref.update({ balance: (d.data().balance || 0) + amt }); }
            await db.collection("requests").doc(id).update({ status: action });
        }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        async function deleteUser() { const u = document.getElementById('adm-user').value.trim(); if(!u||!confirm("Delete?")) return; await db.collection("users").doc(u).delete(); alert("Wiped!"); }
    </script>
</body>
</html>
