<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Gold | Luxury Asset Authority</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        
        :root { --p: #00f2fe; --s: #7000ff; --bg: #010409; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: white; overflow-x: hidden; }
        
        /* Neon Glassmorphism UI */
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); }
        .neon-glow { border: 1px solid rgba(0, 242, 254, 0.3); box-shadow: 0 0 25px rgba(0, 242, 254, 0.1); }
        .grad-main { background: linear-gradient(135deg, #00f2fe 0%, #4facfe 100%); }
        .grad-neon { background: linear-gradient(135deg, #7000ff 0%, #00f2fe 100%); }
        .grad-gold { background: linear-gradient(135deg, #f6d365 0%, #fda085 100%); }
        
        /* Smooth Page Transitions */
        .page { display: none; animation: slideUp 0.5s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        
        .nav-btn { transition: 0.3s; opacity: 0.4; filter: grayscale(1); }
        .active-tab { opacity: 1; filter: grayscale(0); transform: translateY(-5px); color: var(--p); }

        input, select, textarea { background: rgba(255,255,255,0.05) !important; border: 1px solid rgba(255,255,255,0.1) !important; color: white !important; outline: none; transition: 0.3s; }
        input:focus { border-color: var(--p) !important; box-shadow: 0 0 10px rgba(0,242,254,0.2); }

        .file-box { border: 2px dashed rgba(0, 242, 254, 0.3); background: rgba(0, 242, 254, 0.03); padding: 30px; border-radius: 40px; text-align: center; cursor: pointer; }
        ::-webkit-scrollbar { display: none; }
        
        marquee { font-size: 10px; font-weight: 900; letter-spacing: 2px; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="ticker-wrap" class="hidden fixed top-0 w-full z-[1000] backdrop-blur-xl bg-cyan-500/10 py-3 border-b border-cyan-500/20">
        <marquee id="ticker-text" class="text-cyan-400 uppercase italic">✦ SYSTEM STATUS: SECURE ✦ NEW QUANTUM FLEETS ACTIVE ✦ WELCOME TO MINTCREST GOLD ✦</marquee>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-[#010409] flex flex-col items-center justify-center p-8">
        <h1 onclick="adminTap()" class="text-6xl font-black italic mb-2 bg-gradient-to-r from-white to-cyan-500 bg-clip-text text-transparent uppercase tracking-tighter">MINTCREST</h1>
        <p class="text-[8px] tracking-[0.6em] text-cyan-500/40 mb-12 font-bold uppercase italic">Global Authority Vault</p>
        <div class="glass p-10 rounded-[4rem] w-full max-w-sm neon-glow">
            <input type="text" id="user-name" placeholder="IDENTIFICATION NAME" class="w-full p-6 rounded-3xl mb-6 text-center font-bold uppercase tracking-widest text-[11px]">
            <button onclick="login()" class="w-full grad-main py-6 rounded-3xl font-black text-[11px] uppercase text-black active:scale-95 transition-all shadow-2xl">Unlock Vault</button>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pt-20 pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="grad-neon p-10 rounded-[4rem] mb-6 shadow-2xl relative overflow-hidden">
                <div class="absolute -right-10 -top-10 w-40 h-40 bg-white/10 blur-[60px]"></div>
                <p class="text-[9px] text-white/60 font-black uppercase mb-1">Vault Liquidity</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div class="mt-8 flex justify-between border-t border-white/10 pt-4">
                    <div><p class="text-[8px] opacity-60 font-bold uppercase">Cycle Profit</p><p class="text-lg font-black text-cyan-300" id="v-profit">₨ 0</p></div>
                    <span id="rank-badge" class="text-[8px] bg-black/30 px-4 py-2 rounded-full font-black uppercase italic self-center">Standard Node</span>
                </div>
            </div>

            <div class="glass p-6 rounded-[3rem] mb-6 border-l-8 border-yellow-500">
                <h4 class="text-[10px] font-black text-yellow-500 mb-4 uppercase italic">Redeem Promo Code</h4>
                <div class="flex gap-2">
                    <input type="text" id="promo-input" placeholder="ENTER CODE" class="flex-1 p-4 rounded-2xl text-[10px] font-bold text-center uppercase">
                    <button onclick="redeemPromo()" class="bg-yellow-500 text-black px-6 rounded-2xl font-black text-[9px] uppercase active:scale-95">Apply</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[3.5rem] text-center active:scale-95 transition-all border-b-4 border-cyan-500">
                    <span class="text-3xl block mb-2">📥</span>
                    <span class="text-[10px] font-black uppercase text-cyan-400">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[3.5rem] text-center active:scale-95 transition-all border-b-4 border-purple-500">
                    <span class="text-3xl block mb-2">📤</span>
                    <span class="text-[10px] font-black uppercase text-purple-400">Withdraw</span>
                </button>
            </div>

            <button onclick="requestBonus()" class="w-full grad-gold p-8 rounded-[3.5rem] flex justify-between items-center active:scale-95 shadow-xl">
                <div class="text-left"><p class="text-[12px] font-black uppercase text-black">Daily Asset Reward</p><p class="text-[8px] text-orange-900 uppercase font-bold italic">Claim your 24h bonus</p></div>
                <span class="text-2xl">🎁</span>
            </button>
        </div>

        <div id="p-invest" class="page p-6 pb-20">
            <h2 class="text-center font-black text-xl italic mb-8 text-cyan-400 uppercase tracking-widest">Quantum Fleets</h2>
            <div id="plans-list" class="space-y-4"></div>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-8 rounded-[3.5rem] border-t-8 border-cyan-400 shadow-2xl">
                <h3 class="text-center font-black text-cyan-400 uppercase italic mb-8 tracking-widest">Funding Gateway</h3>
                <div class="space-y-3 mb-8 text-[10px] font-black uppercase">
                    <div class="flex justify-between p-4 bg-white/5 rounded-2xl border border-white/5"><span>Jazz/Sada</span><span class="text-cyan-400">03705519562</span></div>
                    <div class="flex justify-between p-4 bg-white/5 rounded-2xl border border-white/5"><span>Easypaisa</span><span class="text-green-400">03379827882</span></div>
                </div>
                <input type="number" id="d-amt" placeholder="Amount (PKR)" class="w-full p-5 rounded-2xl mb-4 text-center font-bold">
                <input type="text" id="d-tid" placeholder="Transaction ID (TID)" class="w-full p-5 rounded-2xl mb-6 text-center font-bold uppercase">
                
                <div onclick="document.getElementById('d-file').click()" class="file-box mb-8">
                    <span class="text-3xl block mb-2">📸</span>
                    <p id="f-status" class="text-[9px] font-black text-cyan-500 uppercase">Upload Receipt Screenshot</p>
                    <input type="file" id="d-file" accept="image/*" class="hidden" onchange="upFileLab(this)">
                </div>
                
                <button id="d-btn" onclick="submitDeposit()" class="w-full grad-main py-6 rounded-3xl font-black text-[11px] uppercase text-black shadow-xl">Confirm Capital</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[4rem] border-t-8 border-red-500 text-center shadow-2xl">
                <h3 class="font-black text-red-500 mb-2 uppercase text-xl italic tracking-widest">Liquidation</h3>
                <p class="text-[8px] text-gray-500 mb-10 font-black uppercase italic tracking-[0.2em]">10% Regulation Tax Applied</p>
                <input type="number" id="w-amt" placeholder="Amount (PKR)" class="w-full p-5 rounded-2xl mb-4 text-center font-bold">
                <input type="text" id="w-acc" placeholder="Account Name & Number" class="w-full p-5 rounded-2xl mb-10 text-center font-bold">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-3xl font-black text-[11px] uppercase shadow-xl shadow-red-900/40">Authorize Payout</button>
            </div>
        </div>

        <div id="p-ledger" class="page p-6"><div id="user-history" class="space-y-4"></div></div>

    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000814] z-[50000] hidden overflow-y-auto pb-32">
        <header class="p-8 border-b border-white/10 flex justify-between items-center bg-[#000814]/90 backdrop-blur-xl sticky top-0">
            <h2 class="text-xl font-black text-cyan-400 uppercase italic">Command Node</h2>
            <button onclick="closeAdmin()" class="bg-red-600 px-6 py-2 rounded-xl text-[9px] font-black uppercase">Lock</button>
        </header>
        
        <div class="flex p-4 gap-2 bg-[#000814]/50 border-b border-white/5 sticky top-[93px] z-50">
            <button onclick="admTab('req')" class="flex-1 grad-main py-3 rounded-xl text-[8px] font-black text-black uppercase">Requests</button>
            <button onclick="admTab('tools')" class="flex-1 grad-neon py-3 rounded-xl text-[8px] font-black uppercase text-white">Tools</button>
        </div>

        <div class="p-4">
            <div id="as-req" class="as-sec space-y-4"></div>
            
            <div id="as-tools" class="as-sec hidden space-y-6">
                <div class="glass p-6 rounded-3xl mb-6 border border-yellow-500/20">
                    <h4 class="text-[10px] font-black text-yellow-500 mb-4 uppercase">Create Promo Code</h4>
                    <input type="text" id="new-p-code" placeholder="CODE NAME" class="w-full p-4 rounded-xl mb-2 text-center text-xs uppercase font-bold">
                    <input type="number" id="new-p-val" placeholder="REWARD AMOUNT" class="w-full p-4 rounded-xl mb-4 text-center text-xs">
                    <button onclick="createPromo()" class="w-full bg-yellow-500 text-black py-4 rounded-xl text-[9px] font-black uppercase">Activate Code</button>
                </div>

                <div class="glass p-6 rounded-3xl border border-cyan-500/20">
                    <h4 class="text-[10px] font-black text-cyan-400 mb-4 uppercase italic">Manual Asset Edit</h4>
                    <input type="text" id="am-u" placeholder="User Name" class="w-full p-4 rounded-xl mb-2 text-center text-xs uppercase">
                    <input type="number" id="am-v" placeholder="Value (PKR)" class="w-full p-4 rounded-xl mb-4 text-center text-xs">
                    <div class="flex gap-2">
                        <button onclick="manEd('balance')" class="flex-1 bg-white text-black py-4 rounded-xl text-[8px] font-black uppercase">Add Bal</button>
                        <button onclick="manEd('profit')" class="flex-1 bg-cyan-500 text-black py-4 rounded-xl text-[8px] font-black uppercase">Add Profit</button>
                    </div>
                </div>
                
                <div class="glass p-6 rounded-3xl border border-purple-500/20">
                    <h4 class="text-[10px] font-black text-purple-400 mb-4 uppercase">Broadcast News</h4>
                    <textarea id="am-news" class="w-full p-4 rounded-xl text-xs h-20 mb-4" placeholder="Enter message..."></textarea>
                    <button onclick="upNews()" class="w-full grad-neon py-4 rounded-xl text-[9px] font-black uppercase">Push Update</button>
                </div>
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass p-8 flex justify-around items-center fixed bottom-0 w-full z-[2000] rounded-t-[4rem] border-t border-white/10 shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="nav-btn active-tab text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-btn text-2xl">🚀</button>
        <button onclick="changePage('ledger')" id="n-ledger" class="nav-btn text-2xl">📜</button>
        <button onclick="changePage('wallet')" id="n-wallet" class="nav-btn text-2xl">💰</button>
    </nav>

    <script>
        // CONFIG
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore(); const storage = firebase.storage();
        let user = null; let tapCount = 0;

        const plans = [
            {n:"Micro-Node",p:500,r:5},{n:"Bronze-Fleet",p:2000,r:8},{n:"Gold-Master",p:10000,r:12},{n:"Diamond-Whale",p:50000,r:20}
        ];

        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase(); if(!n) return;
            localStorage.setItem('mc_user', n);
            const ref = db.collection("users").doc(n); const d = await ref.get();
            if(!d.exists) await ref.set({ name: n, balance: 0, profit: 0, time: Date.now(), redeemed: [] });
            document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            sync(n); renderPlans();
        }

        function sync(n) {
            db.collection("users").doc(n).onSnapshot(d => { 
                user = d.data(); document.getElementById('v-bal').innerText = "₨ "+user.balance.toLocaleString(); 
                document.getElementById('v-profit').innerText = "₨ "+user.profit.toLocaleString(); 
                document.getElementById('rank-badge').innerText = user.balance >= 10000 ? "VIP NODE" : "Standard Node";
            });
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").onSnapshot(s => {
                const h = document.getElementById('user-history'); h.innerHTML = '';
                s.forEach(doc => { const x = doc.data(); h.innerHTML += `<div class="glass p-6 rounded-3xl flex justify-between items-center border-l-4 ${x.status==='approved'?'border-cyan-400':'border-yellow-500'}"><div><p class="text-[10px] font-black uppercase text-white">${x.type}</p><p class="text-[7px] opacity-40">${new Date(x.time).toLocaleTimeString()}</p></div><div class="text-right"><p class="text-xs font-black">₨ ${x.amount}</p><span class="text-[7px] uppercase font-black text-cyan-400">${x.status}</span></div></div>`; });
            });
            db.collection("settings").doc("news").onSnapshot(d => { if(d.exists) { document.getElementById('ticker-wrap').classList.remove('hidden'); document.getElementById('ticker-text').innerText = d.data().msg; } });
        }

        function upFileLab(i) { if(i.files[0]) document.getElementById('f-status').innerText = "Selected: "+i.files[0].name; }

        async function submitDeposit() {
            const a = document.getElementById('d-amt').value; const t = document.getElementById('d-tid').value; const f = document.getElementById('d-file').files[0];
            if(!a || !t || !f) return alert("Photo proof required, Sweetie! 😘");
            const btn = document.getElementById('d-btn'); btn.innerText = "UPLOADING..."; btn.disabled = true;
            try {
                const sRef = storage.ref(`receipts/${Date.now()}_${user.name}`);
                await sRef.put(f); const url = await sRef.getDownloadURL();
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: url, type: "deposit", status: "pending", time: Date.now() });
                alert("Deposit Received! 😘"); changePage('home');
            } catch(e) { alert("Upload error!"); }
            btn.innerText = "Confirm Capital"; btn.disabled = false;
        }

        async function redeemPromo() {
            const c = document.getElementById('promo-input').value.trim().toUpperCase(); if(!c) return;
            const ref = db.collection("promos").doc(c); const d = await ref.get();
            if(!d.exists) return alert("Invalid Promo Code! 😘");
            if(user.redeemed && user.redeemed.includes(c)) return alert("Already Used! 😘");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(d.data().val), redeemed: firebase.firestore.FieldValue.arrayUnion(c) });
            alert("Redeemed! ₨ "+d.data().val+" Added! 😘");
        }

        async function submitWithdraw() {
            const a = parseInt(document.getElementById('w-amt').value); const acc = document.getElementById('w-acc').value;
            if(!a || a > user.balance || !acc) return alert("Check funds! 😘");
            await db.collection("users").doc(user.name).update({ balance: user.balance - a });
            await db.collection("requests").add({ user: user.name, amount: a, acc: acc, type: "withdraw (10% Tax)", status: "pending", time: Date.now() });
            alert("Authorized! 😘"); changePage('home');
        }

        async function requestBonus() { await db.collection("requests").add({ user: user.name, amount: 0, type: "Daily Bonus Request", status: "pending", time: Date.now() }); alert("Requested! 😘"); }

        // ADMIN LOGIC
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("System Pass:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function admTab(s) { document.querySelectorAll('.as-sec').forEach(x=>x.classList.add('hidden')); document.getElementById('as-'+s).classList.remove('hidden'); }

        async function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const rl = document.getElementById('as-req'); rl.innerHTML = '';
                s.forEach(doc => {
                    const x = doc.data(); rl.innerHTML += `<div class="glass p-6 rounded-[2.5rem] mb-4 border border-white/5">
                        <div class="flex justify-between font-black uppercase text-[10px] mb-2"><span class="text-cyan-400">${x.user}</span><span>₨ ${x.amount}</span></div>
                        <p class="text-[8px] opacity-40 mb-3">${x.type} | ${x.tid||x.acc||'N/A'}</p>
                        ${x.proof ? `<a href="${x.proof}" target="_blank" class="block grad-main text-black text-center py-4 rounded-xl text-[9px] font-black uppercase mb-4 shadow-lg shadow-cyan-900">View Photo Receipt</a>` : ''}
                        <div class="flex gap-2">
                            <button onclick="hndReq('${doc.id}','${x.user}',${x.amount},'approved','${x.type}')" class="flex-1 bg-white text-black py-4 rounded-xl text-[9px] font-black uppercase">Approve</button>
                            <button onclick="hndReq('${doc.id}','${x.user}',${x.amount},'rejected','${x.type}')" class="flex-1 bg-red-600 py-4 rounded-xl text-[9px] font-black uppercase">Reject</button>
                        </div>
                    </div>`;
                });
            });
        }

        async function hndReq(id, u, amt, act, type) {
            if(act==='approved') {
                if(type === "Daily Bonus Request") { const b = parseInt(prompt("Enter Bonus:")); await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(b) }); await db.collection("requests").doc(id).update({ status: 'approved', amount: b }); return; }
                if(type==='deposit') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(amt) });
            } else if(act==='rejected' && type.includes('withdraw')) { await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(amt) }); }
            await db.collection("requests").doc(id).update({ status: act });
        }

        async function createPromo() { const c = document.getElementById('new-p-code').value.toUpperCase(); const v = parseInt(document.getElementById('new-p-val').value); await db.collection("promos").doc(c).set({ val: v }); alert("Code Activated! 😘"); }
        async function manEd(f) { const u = document.getElementById('am-u').value.toLowerCase(); const v = parseInt(document.getElementById('am-v').value); await db.collection("users").doc(u).update({ [f]: firebase.firestore.FieldValue.increment(v) }); alert("Updated! 😘"); }
        async function upNews() { const m = document.getElementById('am-news').value; await db.collection("settings").doc("news").set({ msg: m }); alert("Broadcast Sent! 😘"); }

        function renderPlans() {
            const l = document.getElementById('plans-list'); l.innerHTML = '';
            plans.forEach(p => { l.innerHTML += `<div onclick="buyPlan(${p.p},'${p.n}')" class="glass p-8 rounded-[3.5rem] flex justify-between items-center active:scale-95 transition-all border-b-4 border-white/5"><div><h4 class="font-black text-[11px] uppercase">${p.n}</h4><p class="text-[8px] text-cyan-400 font-bold tracking-widest">${p.r}% Daily Yield</p></div><div class="text-xl font-black italic">₨ ${p.p}</div></div>`; });
        }

        async function buyPlan(p,n) { if(user.balance < p) { alert("Check Capital! 😘"); changePage('wallet'); } else { if(confirm("Activate "+n+"?")) { await db.collection("users").doc(user.name).update({ balance: user.balance - p }); await db.collection("requests").add({ user: user.name, amount: p, type: "Fleet: "+n, status: "approved", time: Date.now() }); alert("Fleet Online! 😘"); } } }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active-tab')); document.getElementById('n-'+p).classList.add('active-tab'); window.scrollTo(0,0); }
    </script>
</body>
</html>
