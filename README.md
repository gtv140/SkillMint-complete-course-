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
        .ticker { animation: ticker 30s linear infinite; display: inline-block; white-space: nowrap; font-size: 10px; font-weight: 800; color: #3b82f6; }
        @keyframes ticker { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .badge-pending { @apply bg-yellow-500/20 text-yellow-500 px-2 py-0.5 rounded text-[8px] font-bold; }
        .badge-approved { @apply bg-green-500/20 text-green-400 px-2 py-0.5 rounded text-[8px] font-bold; }
        .badge-rejected { @apply bg-red-500/20 text-red-500 px-2 py-0.5 rounded text-[8px] font-bold; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div class="bg-blue-600/10 py-2 border-b border-blue-600/20 overflow-hidden">
        <div class="ticker uppercase tracking-widest">🚀 Market Alert: Gold Assets Up 12% • User 'Sara' just withdrew ₨ 15,000 • New Tier 25 Plans Live • 24/7 Support Active </div>
    </div>

    <section id="auth-ui" class="h-full flex items-center justify-center p-6 bg-[#010409] z-[1000] absolute inset-0">
        <div class="glass w-full max-w-sm p-10 rounded-[3rem] text-center border-t-8 border-blue-600 shadow-2xl">
            <h1 onclick="adminTap()" class="text-3xl font-black italic tracking-tighter mb-2">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[9px] uppercase tracking-[0.3em] mb-10">Trusted Global Wealth Management</p>
            <input type="text" id="user-name" placeholder="Full Name" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 outline-none text-center mb-4 font-bold">
            <button onclick="login()" class="w-full bg-blue-600 py-4 rounded-2xl font-bold shadow-xl active:scale-95 transition uppercase text-xs tracking-widest">Access Portfolio</button>
        </div>
    </section>

    <div id="app-ui" class="hidden flex-1 overflow-y-auto pb-24">
        
        <div id="p-home" class="page active-page p-6">
            <div class="glass p-8 rounded-[2.5rem] mb-6 border-l-4 border-blue-500">
                <p class="text-[10px] text-gray-500 font-bold mb-1 uppercase tracking-widest">Current Balance</p>
                <h2 class="text-5xl font-black tracking-tighter text-blue-500" id="v-bal">₨ 0</h2>
                <div class="flex gap-2 mt-4">
                    <span class="text-[9px] bg-green-500/20 text-green-400 px-3 py-1 rounded-full font-bold uppercase">Profit: <span id="v-profit">₨ 0</span></span>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('wallet')" class="glass p-6 rounded-3xl text-center border-b-2 border-blue-500">📥<br><span class="text-[10px] font-bold uppercase mt-2 block tracking-widest">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-6 rounded-3xl text-center border-b-2 border-red-500">📤<br><span class="text-[10px] font-bold uppercase mt-2 block tracking-widest">Withdraw</span></button>
            </div>

            <div class="glass p-6 rounded-3xl mb-8 border border-blue-500/20">
                <h4 class="text-[9px] font-bold text-blue-400 mb-2 uppercase tracking-widest text-center">Referral Program (Earn 10%)</h4>
                <div class="flex gap-2">
                    <input type="text" id="ref-link" readonly class="flex-1 bg-white/5 p-2 rounded text-[10px] outline-none border border-white/5">
                    <button onclick="copyRef()" class="bg-blue-600 px-4 rounded font-bold text-[10px] uppercase">Copy</button>
                </div>
            </div>

            <div id="user-history" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500 tracking-tighter">Investment Tiers</h2>
            <div id="plans-list" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-4">
            <div class="glass p-6 rounded-3xl border-l-4 border-blue-500">
                <h3 class="font-bold text-sm mb-2 text-blue-400 uppercase">Support Center</h3>
                <p class="text-[10px] opacity-50 mb-4 font-bold">CONTACT: webhub262@gmail.com</p>
                <button onclick="window.location.href='mailto:webhub262@gmail.com'" class="w-full bg-blue-600 py-3 rounded-xl font-bold text-[10px] uppercase tracking-widest shadow-lg">Open Official Email</button>
            </div>
            <div class="glass p-6 rounded-3xl">
                <h3 class="font-bold text-sm mb-3 uppercase text-gray-400 tracking-widest">Legal Portal</h3>
                <button onclick="alert('Privacy Policy: All encrypted data is secured by Firebase SSL. No third-party sharing.')" class="w-full text-left p-4 glass rounded-xl mb-2 text-[10px] font-bold uppercase">Privacy Policy</button>
                <button onclick="alert('Withdrawals are processed manually by Admin within 1-24 hours. Daily Limit: ₨ 50,000.')" class="w-full text-left p-4 glass rounded-xl text-[10px] font-bold uppercase">Withdrawal Terms</button>
            </div>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-8 rounded-[3rem] border-t-8 border-blue-600 text-center">
                <h3 class="font-black italic text-blue-500 mb-6 uppercase tracking-tighter">Deposit Funds</h3>
                
                <div class="p-4 glass rounded-2xl mb-3 border border-red-500/20">
                    <p class="text-[8px] font-bold text-red-500 uppercase tracking-widest mb-1">JazzCash Wallet</p>
                    <p class="text-xl font-black text-white tracking-widest">03705519562</p>
                </div>

                <div class="p-4 glass rounded-2xl mb-6 border border-green-500/20">
                    <p class="text-[8px] font-bold text-green-500 uppercase tracking-widest mb-1">EasyPaisa Wallet</p>
                    <p class="text-xl font-black text-white tracking-widest">03705519562</p>
                </div>

                <div class="space-y-3">
                    <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 outline-none text-center font-bold">
                    <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-4 outline-none text-center font-bold">
                    <button onclick="submitDeposit()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-xs uppercase shadow-xl active:scale-95 transition tracking-widest">Confirm Transaction</button>
                </div>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-8 rounded-[3rem] border-t-8 border-red-600">
                <h3 class="font-black italic text-red-500 text-center mb-6 uppercase tracking-tighter">Withdraw Assets</h3>
                <input type="number" id="wd-amt" placeholder="Withdraw Amount" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-3 outline-none text-center font-bold">
                <input type="text" id="wd-acc" placeholder="Acc Number & Title" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-6 outline-none text-center font-bold text-xs">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-5 rounded-2xl font-black text-xs uppercase shadow-xl active:scale-95 transition tracking-widest">Request Payout</button>
            </div>
        </div>
    </div>

    <div id="admin-panel" class="fixed inset-0 bg-[#010409] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-6">
            <h2 class="text-xl font-black text-blue-500 italic uppercase">Admin Control</h2>
            <button onclick="closeAdmin()" class="bg-red-500/20 text-red-500 px-4 py-2 rounded-xl text-[10px] font-bold uppercase">Exit</button>
        </div>
        <div class="grid grid-cols-2 gap-4 mb-6">
            <div class="glass p-4 rounded-2xl border-l-2 border-blue-500"><p class="text-[8px] opacity-50 uppercase font-bold">Total Users</p><h4 id="adm-total-users" class="text-xl font-black">0</h4></div>
            <div class="glass p-4 rounded-2xl border-l-2 border-green-500"><p class="text-[8px] opacity-50 uppercase font-bold">Total Cash</p><h4 id="adm-total-deposits" class="text-xl font-black text-green-400">₨ 0</h4></div>
        </div>
        <h3 class="text-[10px] font-black text-gray-500 mb-4 uppercase text-center tracking-widest">Approval Queue</h3>
        <div id="adm-req-list" class="space-y-4 mb-10"></div>
        <div class="glass p-6 rounded-[2rem] border border-white/5">
            <h3 class="text-[10px] font-black text-blue-500 mb-4 uppercase text-center tracking-widest">Manual Adjustment</h3>
            <input type="text" id="adm-user" placeholder="User Name" class="w-full bg-white/5 p-4 rounded-xl mb-2 border border-white/10 outline-none text-sm text-center">
            <input type="number" id="adm-val" placeholder="Amount (PKR)" class="w-full bg-white/5 p-4 rounded-xl mb-4 border border-white/10 outline-none text-center font-bold">
            <div class="grid grid-cols-2 gap-2 mb-4">
                <button onclick="manualEdit('balance')" class="bg-blue-600 py-4 rounded-xl font-bold text-[9px] uppercase tracking-tighter">Add Balance</button>
                <button onclick="manualEdit('profit')" class="bg-green-600 py-4 rounded-xl font-bold text-[9px] uppercase tracking-tighter">Add Profit</button>
            </div>
            <button onclick="deleteUser()" class="w-full bg-red-600/20 text-red-500 py-3 rounded-xl font-bold text-[9px] uppercase border border-red-500/10">Delete User Account</button>
        </div>
    </div>

    <a href="https://wa.me/03379827882" target="_blank" class="fixed bottom-28 right-6 bg-green-500 p-4 rounded-full shadow-2xl z-[500] active:scale-90 transition">
        <svg class="w-6 h-6 fill-white" viewBox="0 0 24 24"><path d="M.057 24l1.687-6.163c-1.041-1.804-1.588-3.849-1.588-5.946 0-6.556 5.332-11.888 11.888-11.888 3.176 0 6.161 1.237 8.404 3.48s3.48 5.228 3.48 8.404c0 6.556-5.332 11.888-11.888 11.888-2.003 0-3.968-.505-5.717-1.467l-6.306 1.692zm6.75-1.765c1.551.92 3.109 1.403 4.75 1.403 5.452 0 9.888-4.435 9.888-9.888 0-2.639-1.028-5.119-2.895-6.987s-4.349-2.895-6.993-2.895c-5.452 0-9.887 4.435-9.887 9.888 0 1.742.467 3.443 1.354 4.927l-.888 3.253 3.333-.895z"/></svg>
    </a>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-4 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-1 active-tab">🏠<span class="text-[8px] font-bold uppercase mt-1">Portfolio</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-1">📈<span class="text-[8px] font-bold uppercase mt-1">Tiers</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-1">🛠️<span class="text-[8px] font-bold uppercase mt-1">Help</span></button>
        <button onclick="logout()" class="flex flex-col items-center gap-1 text-red-500/50">🚪<span class="text-[8px] font-bold uppercase mt-1">Exit</span></button>
    </nav>

    <script>
        // CONFIGURATION
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;
        const plans = Array.from({length: 25}, (_, i) => ({ n: `Asset Tier ${i+1}`, p: (i+1)*500, r: 35 + (i*5) }));

        window.onload = () => { const saved = localStorage.getItem('mintcrest_user'); if(saved) { document.getElementById('user-name').value = saved; login(); } };
        function adminTap() { tapCount++; if(tapCount >= 3) { tapCount = 0; if(prompt("Enter Admin Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } } setTimeout(() => tapCount = 0, 2000); }

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return; localStorage.setItem('mintcrest_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0 });
            startSync(name); document.getElementById('auth-ui').style.display = 'none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('ref-link').value = `https://mintcrest.com/auth?ref=${name}`; renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); } });
            db.collection("requests").where("user", "==", name).orderBy("time", "desc").onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = snap.empty ? '<p class="text-center py-10 opacity-20 text-[8px] uppercase tracking-widest">No Activity Records</p>' : '<h3 class="text-[9px] font-bold text-gray-500 mb-4 uppercase text-center tracking-widest">Transaction History</h3>';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center border-l-2 ${d.type==='deposit'?'border-blue-500':'border-red-500'} mb-2"><div class="text-[10px] font-bold uppercase">${d.type}</div><div class="text-right font-bold text-xs">₨ ${d.amount}<br><span class="badge-${d.status}">${d.status}</span></div></div>`; });
            });
        }

        function renderPlans() { const list = document.getElementById('plans-list'); list.innerHTML = ''; plans.forEach(p => { list.innerHTML += `<div onclick="buy(${p.p})" class="glass p-5 rounded-2xl flex justify-between items-center border-b border-white/5 active:scale-95 mb-2 transition cursor-pointer"><div><h4 class="font-bold text-[10px] uppercase">${p.n}</h4><p class="text-[9px] text-green-400 font-bold uppercase">ROI: ${p.r}%</p></div><p class="font-black text-blue-500 text-sm">₨ ${p.p.toLocaleString()}</p></div>`; }); }
        function copyRef() { const c = document.getElementById('ref-link'); c.select(); document.execCommand('copy'); alert("Link Copied Successfully!"); }
        async function submitDeposit() { const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; if(!a||!t) return alert("Please fill all details!"); await db.collection("requests").add({user:user.name, amount:parseInt(a), tid:t, type:"deposit", status:"pending", time:Date.now()}); alert("Submitted! Wait for verification."); changePage('home'); }
        async function submitWithdraw() { const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value; if(!a||!acc||a>user.balance) return alert("Check balance or details!"); await db.collection("requests").add({user:user.name, amount:parseInt(a), acc:acc, type:"withdraw", status:"pending", time:Date.now()}); await db.collection("users").doc(user.name).update({balance:user.balance-parseInt(a)}); alert("Payout Requested!"); changePage('home'); }
        async function buy(p) { if(user.balance<p) { alert("Insufficient Balance!"); changePage('wallet'); } else { await db.collection("users").doc(user.name).update({balance:user.balance-p}); alert("Tier Activated Successfully!"); } }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); document.getElementById('n-'+(p==='withdraw'||p==='wallet'?'home':p)).classList.add('active-tab'); }
        function logout() { localStorage.removeItem('mintcrest_user'); location.reload(); }

        // ADMIN ENGINE
        function syncAdmin() {
            db.collection("users").onSnapshot(snap => document.getElementById('adm-total-users').innerText = snap.size);
            db.collection("requests").where("status", "==", "approved").where("type", "==", "deposit").onSnapshot(snap => { let t = 0; snap.forEach(d => t += d.data().amount); document.getElementById('adm-total-deposits').innerText = "₨ " + t.toLocaleString(); });
            db.collection("requests").where("status", "==", "pending").orderBy("time", "asc").onSnapshot(snap => {
                const list = document.getElementById('adm-req-list'); list.innerHTML = snap.empty ? '<p class="text-center opacity-30 py-4 text-[10px] uppercase">All Clear</p>' : '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center border-l-4 ${d.type==='deposit'?'border-blue-500':'border-red-500'}"><div class="text-[10px] text-left uppercase"><b>${d.user}</b><br>₨ ${d.amount}<br><span class="opacity-50">${d.tid || d.acc}</span></div><div class="flex gap-2"><button onclick="handleReq('${doc.id}', '${d.user}', ${d.amount}, 'approved', '${d.type}')" class="bg-green-600 px-3 py-2 rounded-lg text-[8px] font-black uppercase">Yes</button><button onclick="handleReq('${doc.id}', '${d.user}', ${d.amount}, 'rejected', '${d.type}')" class="bg-red-600 px-3 py-2 rounded-lg text-[8px] font-black uppercase">No</button></div></div>`; });
            });
        }
        async function manualEdit(f) { const u = document.getElementById('adm-user').value.trim(); const v = document.getElementById('adm-val').value; if(!u||!v) return alert("Details missing"); const ref = db.collection("users").doc(u); const d = await ref.get(); if(d.exists) { await ref.update({ [f]: (d.data()[f] || 0) + parseInt(v) }); alert("Asset Adjusted!"); } else alert("User Error"); }
        async function deleteUser() { const u = document.getElementById('adm-user').value.trim(); if(!u||!confirm("Are you sure?")) return; await db.collection("users").doc(u).delete(); alert("Record Erased!"); }
        async function handleReq(id, target, amt, action, type) {
            const ref = db.collection("users").doc(target);
            if(action === 'approved' && type === 'deposit') { 
                const d = await ref.get(); 
                if(d.exists) await ref.update({ balance: (d.data().balance || 0) + amt }); 
            }
            else if(action === 'rejected' && type === 'withdraw') { 
                const d = await ref.get(); 
                if(d.exists) await ref.update({ balance: (d.data().balance || 0) + amt }); 
            }
            await db.collection("requests").doc(id).update({ status: action });
        }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
    </script>
</body>
</html>
