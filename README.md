<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold Mobile</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; overflow: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(15px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .scroller::-webkit-scrollbar { display: none; }
        .page { display: none; animation: fadeIn 0.3s ease-in-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; } }
        .badge-pending { @apply bg-yellow-500/20 text-yellow-500 px-2 py-0.5 rounded text-[8px] font-bold; }
        .badge-approved { @apply bg-green-500/20 text-green-400 px-2 py-0.5 rounded text-[8px] font-bold; }
        .badge-rejected { @apply bg-red-500/20 text-red-500 px-2 py-0.5 rounded text-[8px] font-bold; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="h-full flex items-center justify-center p-6 bg-[#010409] z-[1000] absolute inset-0">
        <div class="glass w-full max-w-sm p-10 rounded-[3rem] text-center border-t-8 border-blue-600 shadow-2xl">
            <h1 id="mobile-admin-trigger" onclick="adminTap()" class="text-3xl font-black italic tracking-tighter mb-2">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[10px] uppercase tracking-widest mb-10">Institutional Access</p>
            <input type="text" id="user-name" placeholder="Enter Full Name" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 outline-none text-center mb-4 font-bold">
            <button onclick="login()" class="w-full bg-blue-600 py-4 rounded-2xl font-bold shadow-xl active:scale-95 transition">LOGIN</button>
        </div>
    </section>

    <div id="app-ui" class="hidden flex-1 overflow-y-auto scroller pb-24">
        <div id="p-home" class="page active-page p-6">
            <div class="glass p-8 rounded-[2.5rem] mb-6 border-l-4 border-blue-500">
                <p class="text-[10px] text-gray-500 font-bold mb-1">BALANCE</p>
                <h2 class="text-5xl font-black tracking-tighter text-blue-500" id="v-bal">₨ 0</h2>
                <p class="text-[10px] mt-4 text-green-400 font-bold uppercase">Profit: <span id="v-profit">₨ 0</span></p>
            </div>
            <div class="grid grid-cols-2 gap-4 mb-8">
                <button onclick="changePage('wallet')" class="glass p-6 rounded-3xl text-center border-b-2 border-blue-500 active:scale-95">📥<br><span class="text-[10px] font-bold uppercase mt-2 block">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-6 rounded-3xl text-center border-b-2 border-red-500 active:scale-95">📤<br><span class="text-[10px] font-bold uppercase mt-2 block">Withdraw</span></button>
            </div>
            <h3 class="text-[10px] font-bold text-gray-400 mb-4 uppercase tracking-[0.2em]">Activity Log</h3>
            <div id="user-history" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-invest" class="page p-6">
            <div id="plans-list" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-8 rounded-[3rem] border-t-8 border-blue-600">
                <p class="text-[10px] text-center opacity-50 mb-6 font-bold uppercase">JazzCash/EasyPaisa: 03705519562</p>
                <input type="number" id="dep-amount" placeholder="Amount (Min 500)" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-3 outline-none">
                <input type="text" id="dep-trx" placeholder="TID (Transaction ID)" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-6 outline-none">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-xs uppercase shadow-xl">Send Request</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-8 rounded-[3rem] border-t-8 border-red-600">
                <input type="number" id="wd-amt" placeholder="Withdraw Amount" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-3 outline-none">
                <input type="text" id="wd-acc" placeholder="Account No & Title" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-6 outline-none">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-5 rounded-2xl font-black text-xs uppercase shadow-xl">Request Payout</button>
            </div>
        </div>
    </div>

    <div id="admin-panel" class="fixed inset-0 bg-[#010409] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8"><h2 class="text-xl font-black text-blue-500 uppercase italic">Admin Terminal</h2><button onclick="closeAdmin()" class="text-red-500 text-xs font-bold px-4 py-2 bg-red-500/10 rounded-xl">CLOSE</button></div>
        <div id="adm-req-list" class="space-y-4 mb-10"></div>
        <div class="glass p-6 rounded-3xl border border-white/5">
            <input type="text" id="adm-user" placeholder="Exact Username" class="w-full bg-white/5 p-4 rounded-xl mb-2 border border-white/5 outline-none">
            <input type="number" id="adm-val" placeholder="Value" class="w-full bg-white/5 p-4 rounded-xl mb-4 border border-white/5 outline-none">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="manualEdit('balance')" class="bg-blue-600 py-4 rounded-xl font-bold text-[10px] uppercase">Add Balance</button>
                <button onclick="manualEdit('profit')" class="bg-green-600 py-4 rounded-xl font-bold text-[10px] uppercase">Add Profit</button>
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-4 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-1 active-tab">🏠<span class="text-[8px] font-bold uppercase">Home</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-1">📈<span class="text-[8px] font-bold uppercase">Plans</span></button>
        <button onclick="changePage('wallet')" id="n-wallet" class="flex flex-col items-center gap-1">💰<span class="text-[8px] font-bold uppercase">Funds</span></button>
        <button onclick="logout()" class="flex flex-col items-center gap-1 text-red-500/50">🚪<span class="text-[8px] font-bold uppercase">Exit</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;
        const plans = Array.from({length: 20}, (_, i) => ({ n: `Tier Level ${i+1}`, p: (i+1)*500, r: 25 + (i*5) }));

        window.onload = () => {
            const saved = localStorage.getItem('mintcrest_user');
            if(saved) { document.getElementById('user-name').value = saved; login(); }
        };

        function adminTap() {
            tapCount++;
            if(tapCount >= 3) {
                tapCount = 0;
                if(prompt("Admin Key:") === "mint786") {
                    document.getElementById('admin-panel').classList.remove('hidden');
                    syncAdmin();
                }
            }
            setTimeout(() => tapCount = 0, 2000);
        }

        async function login() {
            const name = document.getElementById('user-name').value.trim();
            if(!name) return; localStorage.setItem('mintcrest_user', name);
            const ref = db.collection("users").doc(name);
            const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0 });
            startSync(name);
            document.getElementById('auth-ui').style.display = 'none';
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                if(doc.exists) {
                    user = doc.data();
                    document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                    document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString();
                }
            });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history');
                list.innerHTML = snap.empty ? '<p class="text-center py-10 opacity-30 text-[10px]">No activity</p>' : '';
                snap.forEach(doc => {
                    const d = doc.data();
                    list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center border-l-2 ${d.type==='deposit'?'border-blue-500':'border-red-500'} mb-2">
                        <div class="text-[10px] font-bold uppercase">${d.type}</div>
                        <div class="text-right"><p class="font-bold text-xs">₨ ${d.amount}</p><span class="badge-${d.status}">${d.status.toUpperCase()}</span></div>
                    </div>`;
                });
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                list.innerHTML += `<div onclick="buy(${p.p})" class="glass p-5 rounded-2xl flex justify-between items-center border-b border-white/5 active:bg-blue-600/10 mb-2">
                    <div><h4 class="font-bold text-xs uppercase">${p.n}</h4><p class="text-[9px] text-green-400">ROI: ${p.r}%</p></div>
                    <p class="font-black text-blue-500 text-sm">₨ ${p.p.toLocaleString()}</p>
                </div>`;
            });
        }

        async function submitDeposit() {
            const amt = document.getElementById('dep-amount').value;
            const trx = document.getElementById('dep-trx').value;
            if(!amt || !trx) return alert("Fill all!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(amt), tid: trx, type: "deposit", status: "pending", time: Date.now() });
            alert("Request Sent!"); changePage('home');
        }

        async function submitWithdraw() {
            const amt = document.getElementById('wd-amt').value;
            const acc = document.getElementById('wd-acc').value;
            if(!amt || !acc || amt > user.balance) return alert("Error!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(amt), acc: acc, type: "withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(amt) });
            alert("Withdraw Sent!"); changePage('home');
        }

        async function buy(price) {
            if(user.balance < price) { alert("Low balance!"); changePage('wallet'); }
            else { await db.collection("users").doc(user.name).update({ balance: user.balance - price }); alert("Activated!"); }
        }

        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.querySelectorAll('nav button').forEach(btn => btn.classList.remove('active-tab'));
            document.getElementById('p-' + p).classList.add('active-page');
            document.getElementById('n-' + (p==='withdraw'?'home':p)).classList.add('active-tab');
        }

        function logout() { localStorage.removeItem('mintcrest_user'); location.reload(); }

        function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-req-list');
                list.innerHTML = snap.empty ? '<p class="text-center opacity-30 py-10 text-[10px]">Empty</p>' : '';
                snap.forEach(doc => {
                    const d = doc.data();
                    list.innerHTML += `<div class="glass p-5 rounded-2xl flex justify-between items-center border-l-4 ${d.type==='deposit'?'border-blue-500':'border-red-500'}">
                        <div class="text-[10px] text-left uppercase"><b>${d.user}</b><br>₨ ${d.amount}<br>${d.tid || d.acc}</div>
                        <div class="flex gap-2">
                            <button onclick="handleReq('${doc.id}', '${d.user}', ${d.amount}, 'approved', '${d.type}')" class="bg-green-600 px-4 py-2 rounded-xl text-[9px] font-black">YES</button>
                            <button onclick="handleReq('${doc.id}', '${d.user}', ${d.amount}, 'rejected', '${d.type}')" class="bg-red-600 px-4 py-2 rounded-xl text-[9px] font-black">NO</button>
                        </div>
                    </div>`;
                });
            });
        }

        async function handleReq(id, target, amt, action, type) {
            const ref = db.collection("users").doc(target);
            if(action === 'approved' && type === 'deposit') {
                const d = await ref.get(); await ref.update({ balance: (d.data().balance || 0) + amt });
            } else if(action === 'rejected' && type === 'withdraw') {
                const d = await ref.get(); await ref.update({ balance: (d.data().balance || 0) + amt });
            }
            await db.collection("requests").doc(id).update({ status: action });
        }

        async function manualEdit(f) {
            const u = document.getElementById('adm-user').value;
            const v = document.getElementById('adm-val').value;
            const ref = db.collection("users").doc(u);
            const d = await ref.get();
            if(d.exists) { await ref.update({ [f]: (d.data()[f] || 0) + parseInt(v) }); alert("Done!"); }
        }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
    </script>
</body>
</html>
