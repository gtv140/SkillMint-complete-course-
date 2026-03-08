<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Gold | Luxury Asset Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        
        :root { --p: #00f2fe; --s: #7000ff; --accent: #ff007a; --bg: #010409; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: white; overflow-x: hidden; }
        
        /* Glassmorphism Ultra */
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.1); }
        .neon-glow { box-shadow: 0 0 25px rgba(0, 242, 254, 0.15); border: 1px solid rgba(0, 242, 254, 0.3); }
        
        /* Dynamic Gradients */
        .grad-main { background: linear-gradient(135deg, #00f2fe 0%, #4facfe 100%); }
        .grad-neon { background: linear-gradient(135deg, #7000ff 0%, #00f2fe 100%); }
        .grad-gold { background: linear-gradient(135deg, #f6d365 0%, #fda085 100%); }
        
        /* Smooth Animations */
        .page { display: none; animation: slideIn 0.5s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideIn { from { opacity: 0; transform: translateY(40px); } to { opacity: 1; transform: translateY(0); } }
        
        .nav-btn { transition: 0.4s; opacity: 0.4; filter: grayscale(1); }
        .active-tab { opacity: 1; filter: grayscale(0); transform: translateY(-8px); color: var(--p); }

        /* Input Styling */
        input, select, textarea { background: rgba(255,255,255,0.05) !important; border: 1px solid rgba(255,255,255,0.1) !important; color: white !important; transition: 0.3s; }
        input:focus { border-color: var(--p) !important; box-shadow: 0 0 15px rgba(0,242,254,0.2); }

        .marquee-text { display: inline-block; animation: scroll 25s linear infinite; white-space: nowrap; font-size: 10px; font-weight: 900; }
        @keyframes scroll { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        ::-webkit-scrollbar { display: none; }
        
        .file-box { border: 2px dashed rgba(0, 242, 254, 0.3); background: rgba(0, 242, 254, 0.03); padding: 25px; border-radius: 30px; text-align: center; cursor: pointer; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="ticker-wrap" class="hidden fixed top-0 w-full z-[1000] backdrop-blur-xl bg-cyan-500/10 py-3 border-b border-cyan-500/20">
        <div class="marquee-text text-cyan-400 uppercase italic tracking-widest">
            ✦ SYSTEM STATUS: SECURE ✦ 10% REGULATION TAX APPLIED ✦ NEW QUANTUM FLEETS ACTIVE ✦ CONTACT SUPPORT FOR VIP ✦
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-[#010409] flex flex-col items-center justify-center p-8">
        <h1 onclick="adminTap()" class="text-6xl font-black italic tracking-tighter mb-2 uppercase bg-gradient-to-br from-white via-cyan-300 to-blue-600 bg-clip-text text-transparent">MINTCREST</h1>
        <p class="text-cyan-500/40 text-[8px] uppercase tracking-[0.6em] mb-12 font-extrabold italic">Global Authority Node</p>
        <div class="glass p-10 rounded-[4rem] w-full max-w-sm neon-glow">
            <input type="text" id="user-name" placeholder="IDENTIFICATION NAME" class="w-full p-6 rounded-3xl text-center font-black mb-6 uppercase tracking-widest text-[11px]">
            <button onclick="login()" class="w-full grad-main py-6 rounded-3xl font-black text-[11px] uppercase tracking-widest text-black shadow-2xl active:scale-95 transition-all">Unlock Node</button>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pt-20 pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="grad-neon p-10 rounded-[4rem] mb-8 relative overflow-hidden neon-glow shadow-2xl">
                <div class="absolute -right-10 -top-10 w-40 h-40 bg-white/20 blur-[80px]"></div>
                <p class="text-[10px] text-white/60 font-black mb-1 uppercase tracking-widest">Active Liquidity</p>
                <h2 class="text-6xl font-black tracking-tighter text-white mb-6" id="v-bal">₨ 0</h2>
                <div class="flex justify-between items-center border-t border-white/10 pt-6">
                    <div class="bg-black/20 px-6 py-2 rounded-2xl">
                        <p class="text-[8px] text-cyan-200 font-bold uppercase">Profit Yield</p>
                        <p class="text-xl font-black text-white" id="v-profit">0</p>
                    </div>
                    <span id="rank-badge" class="text-[9px] font-black text-white bg-black/40 px-5 py-2 rounded-full uppercase italic">Standard</span>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-8">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[3.5rem] flex flex-col items-center gap-2 border-b-4 border-cyan-500 active:scale-95 transition-all">
                    <span class="text-3xl">📥</span>
                    <span class="text-[10px] font-black uppercase text-white">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[3.5rem] flex flex-col items-center gap-2 border-b-4 border-pink-500 active:scale-95 transition-all">
                    <span class="text-3xl">📤</span>
                    <span class="text-[10px] font-black uppercase text-white">Withdraw</span>
                </button>
            </div>

            <button onclick="requestBonus()" class="w-full grad-gold p-8 rounded-[3.5rem] flex justify-between items-center shadow-2xl active:scale-95 group transition-all">
                <div class="text-left">
                    <h4 class="text-[13px] font-black uppercase text-black">Daily Asset Reward</h4>
                    <p class="text-[9px] font-bold text-orange-900 uppercase opacity-70">Claim your 24h cycle bonus</p>
                </div>
                <span class="text-3xl group-hover:rotate-12 transition-transform">🎁</span>
            </button>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-cyan-400 text-2xl tracking-tighter">High-Yield Fleets</h2>
            <div id="plans-list" class="space-y-4"></div>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[4rem] border-t-[12px] border-cyan-500 shadow-2xl">
                <h3 class="font-black text-cyan-400 mb-8 uppercase text-center italic tracking-widest">Funding Gateway</h3>
                <div class="space-y-3 mb-10 text-[10px] font-black uppercase">
                    <div class="bg-white/5 p-5 rounded-3xl flex justify-between border border-white/5"><span>JAZZ/SADA</span><span class="text-cyan-400">03705519562</span></div>
                    <div class="bg-white/5 p-5 rounded-3xl flex justify-between border border-white/5"><span>EASYPAISA</span><span class="text-green-400">03379827882</span></div>
                </div>
                
                <input type="number" id="dep-amount" placeholder="CAPITAL AMOUNT" class="w-full p-6 rounded-3xl mb-4 text-center font-black text-lg">
                <input type="text" id="dep-trx" placeholder="TRANSACTION ID (TID)" class="w-full p-6 rounded-3xl mb-6 text-center font-black uppercase text-[11px]">
                
                <div onclick="document.getElementById('d-file').click()" class="file-box mb-10">
                    <span class="text-3xl block mb-2">📸</span>
                    <p id="file-status" class="text-[9px] font-black text-cyan-500 uppercase">Click to Upload Proof</p>
                    <input type="file" id="d-file" accept="image/*" class="hidden" onchange="updateFileText(this)">
                </div>

                <button id="d-btn" onclick="submitDeposit()" class="w-full grad-main py-6 rounded-3xl font-black text-[12px] uppercase text-black shadow-2xl">Confirm Capital</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[4rem] border-t-[12px] border-pink-600 text-center shadow-2xl">
                <h3 class="font-black text-pink-500 mb-2 uppercase text-xl italic">Liquidation</h3>
                <p class="text-[9px] text-gray-500 mb-10 font-black uppercase italic opacity-60">10% Regulation Tax Deducted</p>
                <input type="number" id="wd-amt" placeholder="AMOUNT (PKR)" class="w-full p-6 rounded-3xl mb-4 text-center font-black text-xl">
                <input type="text" id="wd-acc" placeholder="ACCOUNT DETAILS" class="w-full p-6 rounded-3xl mb-10 text-center text-[10px] font-black">
                <button onclick="submitWithdraw()" class="w-full bg-pink-600 py-6 rounded-3xl font-black text-[12px] uppercase text-white shadow-xl shadow-pink-900">Authorize Release</button>
            </div>
        </div>

        <div id="p-activity" class="page p-6">
            <div class="flex justify-between items-center mb-8">
                <h2 class="font-black italic uppercase text-cyan-400 text-lg">Asset Ledger</h2>
                <button onclick="clearHistory()" class="text-[9px] bg-red-500/10 px-5 py-2 rounded-full text-red-500 font-black uppercase">Clear Cache</button>
            </div>
            <div id="user-history" class="space-y-4"></div>
        </div>

    </main>

    <nav id="bottom-nav" class="hidden glass p-8 flex justify-around items-center fixed bottom-0 w-full z-[2000] rounded-t-[4.5rem] border-t border-white/10 shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="nav-btn active-tab flex flex-col items-center"><span class="text-2xl">💎</span><span class="text-[8px] font-black uppercase mt-1">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-btn flex flex-col items-center"><span class="text-2xl">🚀</span><span class="text-[8px] font-black uppercase mt-1">Fleet</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="nav-btn flex flex-col items-center"><span class="text-2xl">⚡</span><span class="text-[8px] font-black uppercase mt-1">Ledger</span></button>
        <button onclick="changePage('wallet')" id="n-wallet" class="nav-btn flex flex-col items-center"><span class="text-2xl">💰</span><span class="text-[8px] font-black uppercase mt-1">Fund</span></button>
    </nav>

    <div id="admin-panel" class="fixed inset-0 bg-[#000814] z-[50000] hidden overflow-y-auto">
        <header class="p-8 border-b border-white/10 flex justify-between items-center sticky top-0 bg-[#000814]/90 backdrop-blur-xl">
            <h2 class="text-2xl font-black text-cyan-400 uppercase italic">Command Node</h2>
            <button onclick="closeAdmin()" class="bg-red-600 text-white px-8 py-3 rounded-2xl text-[9px] font-black uppercase">Lock</button>
        </header>
        <div class="p-6">
            <div id="adm-sec-requests" class="space-y-4"></div>
        </div>
    </div>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore(); const storage = firebase.storage();
        let user = null; let tapCount = 0;

        const plans = [
            { n: "Micro-Elite", p: 200, r: 3 }, { n: "Bronze-S", p: 1000, r: 4 }, { n: "Silver-X", p: 5000, r: 6 },
            { n: "Gold-X", p: 10000, r: 8 }, { n: "Diamond-Whale", p: 50000, r: 15 }
        ];

        window.onload = () => { 
            const saved = localStorage.getItem('mc_user'); if(saved) { document.getElementById('user-name').value = saved; login(); }
            listenForPromos();
        };

        function listenForPromos() { db.collection("app_data").doc("announcement").onSnapshot(doc => { if(doc.exists) { document.getElementById('ticker-wrap').classList.remove('hidden'); document.getElementById('ticker-text').innerText = doc.data().message; } }); }

        async function login() {
            const name = document.getElementById('user-name').value.trim().toLowerCase(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), activeTier: 0 });
            startSync(name); document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { 
                user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); 
                document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); 
                document.getElementById('rank-badge').innerText = user.activeTier >= 10000 ? "VIP NODE" : "STANDARD";
            } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); 
                    list.innerHTML += `<div class="glass p-6 rounded-[2.5rem] flex justify-between items-center border-l-4 ${d.status==='approved'?'border-cyan-500':'border-yellow-500'} mb-3"><div><p class="text-[10px] font-black uppercase text-white">${d.type}</p><p class="opacity-30 text-[8px] uppercase font-bold">${new Date(d.time).toLocaleTimeString()}</p></div><div class="text-right"><p class="font-black text-xs text-white">₨ ${d.amount}</p><span class="text-[8px] font-black uppercase text-cyan-400">${d.status}</span></div></div>`; 
                });
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass p-8 rounded-[3rem] flex justify-between items-center active:scale-95 transition-all border-b-2 border-white/5"><div><h4 class="font-black text-[12px] uppercase text-white">${p.n}</h4><p class="text-[9px] text-cyan-400 font-black uppercase">${p.r}% DAILY YIELD</p></div><div class="text-right font-black text-xl text-white">₨ ${p.p.toLocaleString()}</div></div>`;
            });
        }

        function updateFileText(input) { if(input.files && input.files[0]) document.getElementById('file-status').innerText = "Selected: " + input.files[0].name; }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; const f = document.getElementById('d-file').files[0];
            if(!a || !t || !f) return alert("Proof & Details Required! 😘");
            const btn = document.getElementById('d-btn'); btn.innerText = "UPLOADING..."; btn.disabled = true;

            try {
                const sRef = storage.ref(`proofs/${Date.now()}_${user.name}`);
                await sRef.put(f);
                const url = await sRef.getDownloadURL();
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: url, type: "deposit", status: "pending", time: Date.now() });
                alert("Deposit Received! 😘"); changePage('activity');
            } catch(e) { alert("Upload error!"); }
            btn.innerText = "Confirm Capital"; btn.disabled = false;
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value; if(!a || !acc || parseInt(a) > user.balance) return alert("Check Balance! 😘");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "withdraw (10% Tax)", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) }); 
            alert("Authorized! 😘"); changePage('activity');
        }

        async function requestBonus() {
            await db.collection("requests").add({ user: user.name, amount: 0, type: "Daily Bonus Request", status: "pending", time: Date.now() });
            alert("Bonus Requested! 😘");
        }

        async function buy(p, roi, tName) {
            if(user.balance < p) { alert("Insufficient Capital! 😘"); changePage('wallet'); }
            else { if(confirm("Activate "+tName+"?")) { await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p }); await db.collection("requests").add({ user: user.name, amount: p, type: "Fleet Activation", status: "approved", time: Date.now() }); alert("Fleet Online! 😘"); } }
        }

        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); 
            document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active-tab')); 
            document.getElementById('p-'+p).classList.add('active-page'); 
            if(document.getElementById('n-'+p)) document.getElementById('n-'+p).classList.add('active-tab'); 
            window.scrollTo(0,0);
        }

        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("System Override Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }

        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); 
                    list.innerHTML += `<div class="glass p-6 rounded-3xl mb-4 border border-white/10">
                        <div class="flex justify-between font-black uppercase text-[10px] mb-2"><span class="text-cyan-400">${d.user}</span><span>₨ ${d.amount}</span></div>
                        ${d.proof ? `<a href="${d.proof}" target="_blank" class="block bg-blue-600 text-center py-2 rounded-xl text-[8px] font-black uppercase mb-4">View Proof Image</a>` : ''}
                        <div class="flex gap-2">
                            <button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="flex-1 grad-main py-3 rounded-xl text-[9px] font-black text-black">Approve</button>
                            <button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="flex-1 bg-red-600 py-3 rounded-xl text-[9px] font-black uppercase">Reject</button>
                        </div>
                    </div>`;
                });
            });
        }

        async function handle(id, u, amt, act, type) { 
            if(act==='approved' && (type==='deposit' || type==='deposit' || type === "Daily Bonus Request")) { 
                const gain = type==='deposit' ? amt : parseInt(prompt("Enter Bonus:"));
                await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(gain) }); 
            }
            await db.collection("requests").doc(id).update({ status: act }); 
        }

        function clearHistory() { localStorage.clear(); location.reload(); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
    </script>
</body>
</html>
