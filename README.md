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
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #00040a; color: white; margin: 0; overflow: hidden; }
        
        /* Smooth Scrolling & Modern Feel */
        .app-shell { height: 100vh; overflow-y: auto; scroll-behavior: smooth; -webkit-overflow-scrolling: touch; padding-bottom: 150px; }
        ::-webkit-scrollbar { display: none; }
        
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); }
        .grad-main { background: linear-gradient(135deg, #0052d4 0%, #4364f7 50%, #6fb1fc 100%); }
        .grad-gold { background: linear-gradient(135deg, #f59e0b 0%, #d97706 100%); }
        
        .logout-btn { background: linear-gradient(45deg, #ff0055, #ffdd00, #00ffcc, #3333ff); background-size: 400%; animation: rainbow 5s infinite linear; border-radius: 1.2rem; }
        @keyframes rainbow { 0%{background-position:0%} 100%{background-position:100%} }

        .page { display: none; }
        .active-page { display: block; animation: slideIn 0.4s cubic-bezier(0.18, 0.89, 0.32, 1.28); }
        @keyframes slideIn { from { opacity: 0; transform: scale(0.95) translateY(10px); } to { opacity: 1; transform: scale(1) translateY(0); } }

        .nav-btn { transition: 0.3s; opacity: 0.4; }
        .nav-active { opacity: 1; color: #6fb1fc; transform: translateY(-5px) scale(1.1); }
        
        .ticker-wrap { background: rgba(0,0,0,0.5); overflow: hidden; white-space: nowrap; padding: 5px 0; border-bottom: 1px solid rgba(255,255,255,0.05); }
        .ticker { display: inline-block; animation: ticker 20s linear infinite; font-size: 9px; font-weight: bold; color: #4ade80; }
        @keyframes ticker { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
    </style>
</head>
<body>

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-[#00040a] flex flex-col items-center justify-center p-8 text-center">
        <div class="w-24 h-24 grad-main rounded-[2.5rem] mb-6 shadow-[0_0_50px_rgba(67,100,247,0.4)] flex items-center justify-center text-4xl animate-pulse">💎</div>
        <h1 onclick="adminTap()" class="text-4xl font-black italic tracking-tighter mb-2">MINTCREST</h1>
        <p class="text-[10px] uppercase tracking-[0.5em] text-cyan-400 font-bold mb-10">Infinity Node Protocol</p>
        <div class="glass p-8 rounded-[3.5rem] w-full max-w-xs border-t border-white/20">
            <input type="text" id="user-name" placeholder="Full Name" class="w-full bg-white/5 p-4 rounded-2xl mb-4 text-center font-bold outline-none border border-white/10 text-white">
            <input type="text" id="ref-by" placeholder="Invite ID (Optional)" class="w-full bg-white/5 p-3 rounded-xl mb-6 text-[10px] text-center outline-none border border-white/5">
            <button onclick="login()" class="w-full grad-main py-5 rounded-2xl font-black uppercase text-xs shadow-xl hover:brightness-110 transition-all">Establish Link</button>
        </div>
    </section>

    <div id="ticker-bar" class="hidden ticker-wrap fixed top-0 w-full z-[1000]">
        <div class="ticker">
            ● User_772 just earned ₨ 5,200 from Node V10 — ● User_019 withdrew ₨ 15,000 via Easypaisa — ● New VIP Fleet P1 activated by User_X — ● System Status: 100% Secure
        </div>
    </div>

    <main id="app-ui" class="hidden app-shell pt-10">
        
        <div id="p-home" class="page active-page p-6">
            <div class="flex justify-between items-center mt-4 mb-8">
                <div><h2 class="text-2xl font-black" id="v-user">...</h2><p class="text-[9px] text-green-400 font-bold uppercase tracking-widest">● Verified Quantum Node</p></div>
                <button onclick="logout()" class="logout-btn w-12 h-12 flex items-center justify-center text-xl shadow-lg">🚀</button>
            </div>

            <div class="grad-main p-8 rounded-[3.5rem] mb-8 shadow-[0_20px_50px_rgba(0,82,212,0.3)] relative overflow-hidden">
                <div class="absolute -right-10 -top-10 w-40 h-40 bg-white/10 rounded-full blur-3xl"></div>
                <p class="text-[10px] font-black opacity-80 uppercase italic">Main Node Wallet</p>
                <h2 class="text-5xl font-black my-2" id="v-bal">₨ 0</h2>
                <div class="mt-8 flex justify-between items-center border-t border-white/10 pt-4">
                    <div><p class="text-[8px] opacity-60 uppercase font-bold">Node Yield</p><p class="text-lg font-black" id="v-profit">₨ 0</p></div>
                    <button onclick="copyRef()" class="bg-black/30 px-5 py-2 rounded-2xl text-[8px] font-black uppercase border border-white/10 backdrop-blur-md">🔗 Share Node</button>
                </div>
            </div>

            <div class="glass p-5 rounded-[2.5rem] mb-8">
                <div class="flex justify-between items-center mb-4">
                    <h3 class="text-[10px] font-black uppercase text-yellow-500">🏆 Top Node Masters</h3>
                    <span class="text-[8px] opacity-40">Live Rankings</span>
                </div>
                <div id="leaderboard" class="space-y-3">
                    </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-8">
                <button onclick="reqAction('Daily Bonus', 50)" class="glass p-6 rounded-[2.5rem] text-center border-b-4 border-blue-500 shadow-xl">
                    <span class="text-3xl block mb-2">🎁</span><span class="text-[10px] font-black uppercase">Daily Reward</span>
                </button>
                <button onclick="changePage('spin')" class="glass p-6 rounded-[2.5rem] text-center border-b-4 border-purple-500 shadow-xl">
                    <span class="text-3xl block mb-2">🎡</span><span class="text-[10px] font-black uppercase">Lucky Spin</span>
                </button>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="grad-main py-8 rounded-[2.5rem] font-black text-xs uppercase shadow-2xl">Deposit</button>
                <button onclick="changePage('withdraw')" class="glass py-8 rounded-[2.5rem] font-black text-xs uppercase text-rose-500 border border-rose-500/20">Withdraw</button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h3 class="text-center font-black text-xl mb-8 uppercase text-blue-400 italic">Available Fleets</h3>
            <div id="plans-list" class="space-y-4"></div>
        </div>

        <div id="p-wallet" class="page p-6">
            <h3 class="text-center font-black text-xl mb-8 uppercase italic">Capital Inflow</h3>
            <div class="space-y-3 mb-8">
                <div class="glass p-5 rounded-3xl flex justify-between items-center border-l-4 border-yellow-500"><span>JazzCash</span><b>03705519562</b></div>
                <div class="glass p-5 rounded-3xl flex justify-between items-center border-l-4 border-green-500"><span>Easypaisa</span><b>03379827882</b></div>
            </div>
            <div class="glass p-8 rounded-[3.5rem]">
                <input type="number" id="d-amt" placeholder="Min 200 PKR" class="w-full bg-white/5 p-4 rounded-2xl mb-4 text-center font-bold outline-none">
                <input type="text" id="d-tid" placeholder="TID Number" class="w-full bg-white/5 p-4 rounded-2xl mb-4 text-center font-bold outline-none">
                <input type="file" id="d-file" accept="image/*" class="w-full text-[10px] mb-6">
                <button id="dep-btn" onclick="subDep()" class="w-full grad-main py-5 rounded-2xl font-black uppercase text-xs">Confirm Payment</button>
            </div>
        </div>

        <div id="p-history" class="page p-6"><h3 class="font-black text-xl mb-8 uppercase text-center">Node Logs</h3><div id="user-history" class="space-y-4"></div></div>

    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#00040a] z-[50000] hidden overflow-y-auto p-6">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-xl font-black text-cyan-400 italic">CORE COMMAND</h2>
            <button onclick="closeAdm()" class="bg-rose-600 px-6 py-2 rounded-xl text-[10px] font-black uppercase">Exit</button>
        </div>

        <div class="grid grid-cols-2 gap-4 mb-8">
            <div class="glass p-6 rounded-3xl text-center"><p class="text-[8px] opacity-40 uppercase mb-1">Live Nodes</p><h4 id="adm-u-count" class="text-2xl font-black">0</h4></div>
            <div class="glass p-6 rounded-3xl text-center"><p class="text-[8px] opacity-40 uppercase mb-1">Queue</p><h4 id="adm-p-count" class="text-2xl font-black text-yellow-500">0</h4></div>
        </div>

        <div class="glass p-5 rounded-3xl mb-8 overflow-x-auto">
            <h4 class="text-[10px] font-black uppercase text-blue-400 mb-4">Node Directory</h4>
            <table class="w-full text-[9px] text-left">
                <thead class="opacity-40 uppercase border-b border-white/10">
                    <tr><th class="p-2">User</th><th class="p-2">Bal</th><th class="p-2">Ref</th><th class="p-2">Control</th></tr>
                </thead>
                <tbody id="adm-user-list"></tbody>
            </table>
        </div>

        <div id="adm-reqs-list" class="space-y-4"></div>
    </div>

    <nav id="bottom-nav" class="hidden glass p-7 flex justify-around items-center fixed bottom-0 w-full z-[2000] rounded-t-[3.5rem] border-t border-white/10">
        <button onclick="changePage('home')" id="n-home" class="nav-btn nav-active text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-btn text-2xl">📈</button>
        <button onclick="changePage('history')" id="n-history" class="nav-btn text-2xl">📜</button>
    </nav>

    <script>
        const firebaseConfig = {
            apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg",
            authDomain: "investment-84f4e.firebaseapp.com",
            projectId: "investment-84f4e",
            storageBucket: "investment-84f4e.firebasestorage.app",
            messagingSenderId: "975293889308",
            appId: "1:975293889308:web:6d034a99cc966c75ff58d9"
        };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore(); const storage = firebase.storage();
        let user = null; let tapCount = 0;

        window.onload = () => {
            const saved = localStorage.getItem('mint_v35_session');
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            renderPlans();
            renderLeaderboard();
        };

        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase();
            const r = document.getElementById('ref-by').value.trim().toLowerCase();
            if(!n) return;
            const ref = db.collection("users").doc(n);
            const d = await ref.get();
            if(!d.exists) await ref.set({ name: n, balance: 0, profit: 0, refBy: r || 'Direct', time: Date.now(), plans: [], status: 'active' });
            
            localStorage.setItem('mint_v35_session', n);
            document.getElementById('auth-ui').style.display='none';
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('ticker-bar').classList.remove('hidden');
            syncData(n);
        }

        function syncData(n) {
            db.collection("users").doc(n).onSnapshot(d => {
                user = d.data();
                if(user.status === 'banned') { alert("Node Access Denied! 😘"); logout(); }
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString();
                document.getElementById('v-user').innerText = n.toUpperCase();
                updateTimers();
            });

            db.collection("requests").where("user", "==", n).orderBy("time", "desc").onSnapshot(s => {
                const l = document.getElementById('user-history'); l.innerHTML = '';
                s.forEach(doc => {
                    const x = doc.data();
                    l.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center border-l-4 ${x.status==='approved'?'border-green-500':'border-yellow-500'}">
                        <div><p class="text-[10px] font-black uppercase text-cyan-400">${x.type}</p><p class="text-[8px] opacity-40">${new Date(x.time).toLocaleTimeString()}</p></div>
                        <div class="text-right"><p class="text-[12px] font-black">₨ ${x.amount}</p><p class="text-[8px] font-black uppercase ${x.status==='approved'?'text-green-400':'text-yellow-400'}">${x.status}</p></div>
                    </div>`;
                });
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            for(let i=1; i<=25; i++) {
                const vip = i > 20;
                let price = i === 1 ? 200 : (i * 500) - 300; if(vip) price = (i-20) * 10000;
                let days = i <= 5 ? 15 + (i * 2) : 30 + i;
                const daily = (price * (vip ? 0.15 : 0.08)).toFixed(0);
                list.innerHTML += `<div class="glass p-6 rounded-[3rem] border border-white/5 relative overflow-hidden">
                    ${vip?'<div class="absolute top-0 right-0 bg-yellow-500 text-black text-[7px] font-black px-4 py-1 rounded-bl-xl uppercase">Special</div>':''}
                    <div class="flex justify-between items-center mb-4">
                        <h4 class="font-black text-[11px] uppercase">${vip?'Elite Fleet P'+(i-20):'Quantum Node V'+i}</h4>
                        <span class="text-xs font-black text-cyan-400">₨ ${price}</span>
                    </div>
                    <div class="flex justify-between items-end">
                        <div><p class="text-green-400 font-bold text-xs">₨ ${daily}/Day</p><p class="text-[7px] opacity-40 font-bold">${vip?'No Expiry Limit':days+' Days Lifecycle'}</p></div>
                        <button onclick="buyPlan(${price}, ${days}, ${i})" class="${vip?'grad-gold':'bg-white/10'} px-6 py-2 rounded-xl text-[8px] font-black uppercase">Deploy</button>
                    </div>
                    <div id="timer-${i}" class="text-[8px] mt-3 font-bold text-cyan-500 italic"></div>
                </div>`;
            }
        }

        async function renderLeaderboard() {
            db.collection("users").orderBy("balance", "desc").limit(5).onSnapshot(s => {
                const l = document.getElementById('leaderboard'); l.innerHTML = '';
                let rank = 1;
                s.forEach(doc => {
                    const u = doc.data();
                    l.innerHTML += `<div class="flex justify-between items-center text-[10px] font-bold opacity-80">
                        <span>${rank}. ${u.name.toUpperCase()}</span>
                        <span class="text-green-400">₨ ${u.balance.toLocaleString()}</span>
                    </div>`;
                    rank++;
                });
            });
        }

        async function buyPlan(p, d, id) {
            if(user.balance < p) return alert("Sweetie, Balance insufficient! 😘");
            const endTime = Date.now() + (d * 24 * 60 * 60 * 1000);
            await db.collection("users").doc(user.name).update({ 
                balance: firebase.firestore.FieldValue.increment(-p),
                plans: firebase.firestore.FieldValue.arrayUnion({ id, endTime })
            });
            alert("Node Deployed! 😘");
        }

        function updateTimers() {
            if(!user.plans) return;
            user.plans.forEach(p => {
                const el = document.getElementById('timer-'+p.id);
                if(el) {
                    const rem = p.endTime - Date.now();
                    if(rem > 0) {
                        const days = Math.floor(rem / (1000 * 60 * 60 * 24));
                        const hrs = Math.floor((rem % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
                        el.innerText = `⏳ Cycle: ${days}d ${hrs}h Left`;
                    } else { el.innerText = "✅ Lifecycle Complete"; }
                }
            });
        }

        async function subDep() {
            const a = parseInt(document.getElementById('d-amt').value);
            const t = document.getElementById('d-tid').value;
            const f = document.getElementById('d-file').files[0];
            if(a < 200 || !t || !f) return alert("Sweetie, Fill all details! 😘");
            const btn = document.getElementById('dep-btn'); btn.innerText = "LOGGING NODE..."; btn.disabled = true;
            try {
                const sRef = storage.ref(`proofs/${Date.now()}_${user.name}`);
                await sRef.put(f);
                const url = await sRef.getDownloadURL();
                await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: url, type: "Deposit", status: "pending", time: Date.now(), refBy: user.refBy });
                alert("Request Sent! 😘"); changePage('home');
            } catch(e) { alert("Rules Lock! Allow Firebase Storage."); }
            btn.innerText = "Confirm Payment"; btn.disabled = false;
        }

        async function reqAction(type, amt) {
            alert(type + " Logged for Approval! 😘");
            await db.collection("requests").add({ user: user.name, amount: amt, type: type, status: "pending", time: Date.now() });
        }

        // ADMIN CORE
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Access Code:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdm() { document.getElementById('admin-panel').classList.add('hidden'); }

        function syncAdm() {
            db.collection("users").onSnapshot(s => {
                document.getElementById('adm-u-count').innerText = s.size;
                const ul = document.getElementById('adm-user-list'); ul.innerHTML = '';
                s.forEach(doc => {
                    const u = doc.data();
                    ul.innerHTML += `<tr class="border-b border-white/5">
                        <td class="p-2 font-black">${u.name}</td>
                        <td class="p-2 text-green-400">${u.balance}</td>
                        <td class="p-2 opacity-50">${u.refBy}</td>
                        <td class="p-2"><button onclick="banUser('${doc.id}')" class="text-rose-500 font-black">BAN</button></td>
                    </tr>`;
                });
            });
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                document.getElementById('adm-p-count').innerText = s.size;
                const rl = document.getElementById('adm-reqs-list'); rl.innerHTML = '';
                s.forEach(doc => {
                    const x = doc.data();
                    rl.innerHTML += `<div class="glass p-5 rounded-3xl text-[10px]">
                        <p class="font-black text-cyan-400 uppercase">${x.user} - ₨ ${x.amount} (${x.type})</p>
                        <p class="opacity-60 mt-1">RefBy: ${x.refBy || 'Direct'} | TID: ${x.tid || 'N/A'}</p>
                        ${x.proof ? `<a href="${x.proof}" target="_blank" class="bg-blue-600 block text-center py-2 rounded-xl my-2 font-black">VIEW PROOF</a>` : ''}
                        <div class="flex gap-2"><button onclick="hnd('${doc.id}','${x.user}',${x.amount},'approved','${x.type}')" class="flex-1 bg-green-600 py-3 rounded-xl font-bold">APPROVE</button>
                        <button onclick="hnd('${doc.id}','${x.user}',${x.amount},'rejected','${x.type}')" class="flex-1 bg-rose-600 py-3 rounded-xl font-bold">REJECT</button></div>
                    </div>`;
                });
            });
        }

        async function hnd(id, u, amt, act, type) {
            if(act==='approved' && type !== "Withdrawal") await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(amt) });
            await db.collection("requests").doc(id).update({ status: act });
        }
        async function banUser(id) { if(confirm("Ban this user?")) await db.collection("users").doc(id).update({ status: 'banned' }); }

        function logout() { localStorage.removeItem('mint_v35_session'); location.reload(); }
        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('nav-active'));
            document.getElementById('n-'+p).classList.add('nav-active');
        }
        function copyRef() { navigator.clipboard.writeText(user.name); alert("Node Link Copied! 😘"); }
    </script>
</body>
</html>
