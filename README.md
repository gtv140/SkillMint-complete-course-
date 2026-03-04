<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Stable Live</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; margin: 0; padding: 0; }
        
        /* FIX: Body scroll enabled but managed */
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; height: 100vh; overflow: hidden; display: flex; flex-direction: column; }
        
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(15px); border: 1px solid rgba(255,255,255,0.08); }
        
        /* FIX: Scrollable area for pages */
        .page-container { flex: 1; overflow-y: auto; -webkit-overflow-scrolling: touch; padding-bottom: 120px; }
        
        .page { display: none; padding: 24px; animation: fadeIn 0.3s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }
        
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        
        ::-webkit-scrollbar { width: 0px; }
        .promo-banner { background: linear-gradient(90deg, #1e40af, #3b82f6); position: sticky; top: 0; z-index: 2000; }
        
        /* Prevent accidental selection */
        .no-select { user-select: none; }
    </style>
</head>
<body class="no-select">

    <div id="global-note" class="hidden promo-banner p-3 text-[10px] font-black uppercase text-center tracking-widest shadow-xl">
        <span id="note-text">System Online</span>
    </div>

    <header class="p-5 flex justify-between items-center bg-black/80 backdrop-blur-md border-b border-white/5 z-[1000]">
        <h1 onclick="adminTap()" class="text-xl font-black italic tracking-tighter uppercase cursor-pointer">MINT<span class="text-blue-500 text-2xl">CREST</span></h1>
        <div id="v-status" class="bg-blue-600/10 text-blue-400 border border-blue-500/20 px-3 py-1.5 rounded-xl text-[9px] font-black uppercase italic">Live Node</div>
    </header>

    <section id="auth-ui" class="fixed inset-0 z-[3000] bg-[#000103] flex items-center justify-center p-6">
        <div class="w-full max-w-sm text-center">
            <h1 class="text-6xl font-black italic mb-2 tracking-tighter text-blue-500">GOLD</h1>
            <p class="text-[9px] text-gray-500 uppercase tracking-[0.6em] mb-10">Vault Secure Connection</p>
            <div class="glass p-8 rounded-[3rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="ENTER USERNAME" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-4 uppercase focus:border-blue-500 transition-all">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[11px] uppercase tracking-widest shadow-xl shadow-blue-600/30 active:scale-95">Verify Identity</button>
            </div>
        </div>
    </section>

    <div id="app-ui" class="hidden page-container">
        
        <div id="p-home" class="page active-page">
            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600 shadow-2xl">
                <p class="text-[10px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Global Credit</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                
                <div id="timer-box" class="mt-8 hidden bg-blue-600/5 p-5 rounded-3xl border border-blue-500/10">
                    <p class="text-[8px] text-gray-500 uppercase font-black mb-1">Signal Cooldown:</p>
                    <div id="countdown" class="text-2xl font-black text-yellow-500 tracking-widest font-mono italic">00:00:00</div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95 border-b-4 border-blue-600">
                    <span class="text-2xl block mb-2">📥</span>
                    <span class="text-[10px] font-black uppercase">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95 border-b-4 border-red-600">
                    <span class="text-2xl block mb-2">📤</span>
                    <span class="text-[10px] font-black uppercase text-red-500">Withdraw</span>
                </button>
            </div>

            <button onclick="changePage('invest')" class="w-full mt-4 glass p-7 rounded-[2.5rem] flex items-center justify-between border-r-8 border-blue-500 active:scale-95">
                <div class="text-left">
                    <h4 class="text-[11px] font-black uppercase italic">Mining Nodes</h4>
                    <p class="text-[8px] text-gray-500 font-bold uppercase">25 Fleet Active</p>
                </div>
                <span class="text-2xl">📈</span>
            </button>
        </div>

        <div id="p-invest" class="page">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500 text-2xl tracking-tighter">Investment Fleets</h2>
            <div id="plans-list" class="space-y-3"></div>
        </div>

        <div id="p-activity" class="page">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-400 text-2xl tracking-tighter">Account History</h2>
            <div id="user-history" class="space-y-2"></div>
        </div>

        <div id="p-wallet" class="page">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black text-blue-500 mb-6 uppercase text-center tracking-widest">Inject Capital</h3>
                <div class="bg-black/50 p-5 rounded-2xl mb-8 text-[11px] font-mono text-center border border-white/5 text-blue-300">
                    EASYPAISA: 03379827882<br>JAZZCASH: 03705519562
                </div>
                <input type="number" id="dep-amount" placeholder="PKR AMOUNT" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold border border-white/10 outline-none">
                <input type="text" id="dep-trx" placeholder="TRANSACTION TID" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center font-bold border border-white/10 uppercase outline-none">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[11px] uppercase shadow-lg active:scale-95">Submit Data</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-red-600 shadow-2xl text-center">
                <h3 class="font-black text-red-500 mb-8 uppercase tracking-widest">Extraction</h3>
                <input type="number" id="wd-amount" placeholder="MINIMUM 500" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold border border-white/10 outline-none">
                <input type="text" id="wd-method" placeholder="A/C DETAILS & NAME" class="w-full bg-white/5 p-5 rounded-2xl mb-10 text-center font-bold border border-white/10 uppercase outline-none">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-5 rounded-2xl font-black text-[11px] uppercase active:scale-95 shadow-xl shadow-red-600/20">Withdraw Now</button>
            </div>
        </div>

        <div id="p-more" class="page text-center">
            <div class="glass p-12 rounded-[3.5rem] mb-10 shadow-2xl">
                <div id="wheel" class="text-8xl mb-10 transition-all duration-[4s] inline-block">🎡</div><br>
                <button onclick="spinNow()" class="bg-yellow-600 px-12 py-4 rounded-2xl font-black text-[11px] uppercase active:scale-95 shadow-lg">Spin Daily</button>
            </div>
            <button onclick="logout()" class="text-red-500 text-[10px] font-black uppercase opacity-40">Terminate Session</button>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass p-7 flex justify-around items-center fixed bottom-0 left-0 w-full z-[2500] rounded-t-[3.5rem] border-t border-white/10 bg-black/90 backdrop-blur-xl">
        <button onclick="changePage('home')" id="n-home" class="active-tab text-3xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-3xl">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="text-3xl">📜</button>
        <button onclick="changePage('more')" id="n-more" class="text-3xl">⚙️</button>
    </nav>

    <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8 border-b border-white/10 pb-4">
            <h2 class="text-xl font-black text-blue-500 italic uppercase">Control Terminal</h2>
            <button onclick="closeAdmin()" class="bg-red-500/20 text-red-500 px-5 py-2 rounded-xl text-[9px] font-black uppercase">Close</button>
        </div>
        <div class="glass p-5 rounded-3xl mb-6 border border-blue-500/20">
            <h3 class="text-[9px] font-black text-blue-400 mb-3 uppercase">Announcement</h3>
            <input type="text" id="adm-note" placeholder="Write msg..." class="w-full bg-white/5 p-4 rounded-xl mb-3 text-[10px] border border-white/5 outline-none">
            <button onclick="broadcastNote()" class="w-full bg-blue-600 py-3 rounded-xl text-[9px] font-black uppercase">Broadcast</button>
        </div>
        <div id="adm-requests" class="space-y-3 mb-10"></div>
    </div>

    <script>
        // CONFIG
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        
        let user = null, tapCount = 0, timerInt = null;
        const plansList = []; for(let i=1; i<=25; i++) { plansList.push({ id:`p${i}`, n: i<=5?"Starter":i<=15?"Pro":i<=20?"Gold":"Elite", p: i*1000, r: 5+(i*0.4) }); }

        window.onload = () => { 
            const saved = localStorage.getItem('mc_auth');
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            syncGlobalNote();
        };

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase();
            if(!name) return;
            try {
                const ref = db.collection("users").doc(name);
                const doc = await ref.get();
                if(!doc.exists) { await ref.set({ name: name, balance: 0, activePlanId: null, lastRequestTime: Date.now() }); }
                localStorage.setItem('mc_auth', name);
                ref.onSnapshot(d => { 
                    user = d.data(); 
                    if(user) { 
                        document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
                        startCountdown(); renderPlans(); checkAutoProfit(); 
                    }
                });
                document.getElementById('auth-ui').classList.add('hidden'); 
                document.getElementById('app-ui').classList.remove('hidden'); 
                document.getElementById('bottom-nav').classList.remove('hidden');
                syncHistory();
            } catch(e) { alert("Error connecting, check connection!"); }
        }

        function renderPlans() {
            const l = document.getElementById('plans-list'); if(!l) return; l.innerHTML = '';
            plansList.forEach(p => {
                const isOwned = user.activePlanId === p.id;
                l.innerHTML += `<div onclick="${isOwned?'':`buy(${p.p},'${p.n}',${p.r},'${p.id}')`}" class="glass p-6 rounded-[2rem] flex justify-between items-center ${isOwned?'border-blue-500 border-2 bg-blue-600/5':''}">
                    <div class="text-left"><h4 class="font-black text-[10px] text-blue-400 uppercase italic">${p.n} ${p.id.substring(1)}</h4><p class="text-[8px] text-gray-500 font-bold">${p.r}% Daily</p></div>
                    <div class="text-right font-black italic text-lg">₨ ${p.p.toLocaleString()}</div>
                </div>`;
            });
        }

        async function buy(p, n, rate, id) {
            if(user.activePlanId) return alert("Only 1 fleet at a time, sweetie!");
            if(user.balance < p) return alert("Insufficient balance!");
            if(confirm("Activate Fleet "+n+"?")) {
                await db.collection("users").doc(user.name).update({ balance: user.balance-p, activePlanId: id, tier: n, dailyRate: rate, lastPrice: p, lastRequestTime: Date.now() });
            }
        }

        function startCountdown() {
            if(!user.activePlanId) return;
            document.getElementById('timer-box').classList.remove('hidden');
            if(timerInt) clearInterval(timerInt);
            timerInt = setInterval(() => {
                const diff = (user.lastRequestTime + 86400000) - Date.now();
                const cd = document.getElementById('countdown');
                if(diff <= 0) { cd.innerText = "SIGNAL READY! ⚡"; return; }
                const h = Math.floor(diff/3600000), m = Math.floor((diff%3600000)/60000), s = Math.floor((diff%60000)/1000);
                cd.innerText = `${String(h).padStart(2,'0')}:${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
            }, 1000);
        }

        async function checkAutoProfit() {
            if(!user.activePlanId || Date.now() - user.lastRequestTime < 86400000) return;
            const amt = Math.floor((user.dailyRate/100)*user.lastPrice);
            await db.collection("requests").add({ user: user.name, amount: amt, type: "PROFIT SIGNAL", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ lastRequestTime: Date.now() });
        }

        function syncHistory() {
            db.collection("requests").where("user", "==", user.name).orderBy("time", "desc").limit(15).onSnapshot(snap => {
                const list = document.getElementById('user-history'); if(!list) return;
                list.innerHTML = '';
                snap.forEach(d => { const r = d.data(); list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center text-[9px] font-black uppercase mb-1"><div>${r.type}<br><span class="opacity-30 text-[6px]">${new Date(r.time).toLocaleString()}</span></div><div>₨ ${r.amount} <span class="${r.status==='approved'?'text-green-500':'text-yellow-500'}">${r.status}</span></div></div>`; });
            });
        }

        function syncGlobalNote() {
            db.collection("settings").doc("broadcast").onSnapshot(d => {
                const bar = document.getElementById('global-note');
                if(d.exists && d.data().msg) { bar.classList.remove('hidden'); document.getElementById('note-text').innerText = d.data().msg; }
                else { bar.classList.add('hidden'); }
            });
        }

        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); 
            document.getElementById('p-'+p).classList.add('active-page'); 
            document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); 
            document.getElementById('n-'+p).classList.add('active-tab'); 
            document.getElementById('app-ui').scrollTo(0,0);
        }

        async function submitWithdraw() { const a = parseInt(document.getElementById('wd-amount').value), m = document.getElementById('wd-method').value; if(a < 500 || user.balance < a) return alert("Check balance!"); await db.collection("requests").add({ user: user.name, amount: a, details: m, type: "WITHDRAW", status: "pending", time: Date.now() }); await db.collection("users").doc(user.name).update({ balance: user.balance - a }); alert("Success!"); changePage('home'); }
        async function submitDeposit() { const a = document.getElementById('dep-amount').value, t = document.getElementById('dep-trx').value; if(!a || !t) return alert("Fill all!"); await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, type: "DEPOSIT", status: "pending", time: Date.now() }); alert("Sent!"); changePage('home'); }
        function logout() { localStorage.removeItem('mc_auth'); location.reload(); }
        function spinNow() { document.getElementById('wheel').style.transform = `rotate(${Math.floor(2000+Math.random()*2000)}deg)`; }
        function adminTap() { tapCount++; if(tapCount>=5) { if(prompt("Key:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function broadcastNote() { db.collection("settings").doc("broadcast").set({ msg: document.getElementById('adm-note').value }); alert("OK"); }
        function syncAdmin() {
            db.collection("requests").where("status","==","pending").onSnapshot(snap => {
                const l = document.getElementById('adm-requests'); l.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); l.innerHTML += `<div class="glass p-4 text-[9px] font-black mb-2 flex justify-between items-center border-l-4 border-yellow-500"><div>${d.user}<br>${d.type} - Rs ${d.amount}</div><button onclick="hndl('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-4 py-2 rounded-xl text-[8px]">APPROVE</button></div>`; });
            });
        }
        async function hndl(id, u, amt, act, type) { const ref = db.collection("users").doc(u); const d = (await ref.get()).data(); if(type!=="WITHDRAW") await ref.update({ balance: (d.balance||0) + amt }); await db.collection("requests").doc(id).update({ status: act }); }
    </script>
</body>
</html>
