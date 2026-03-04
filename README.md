<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | VIP Edition</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(30px); border: 1px solid rgba(255,255,255,0.07); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: slideUp 0.4s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .spin-anim { animation: rotate 2s cubic-bezier(0.1, 0.7, 0.1, 1) infinite; }
        @keyframes rotate { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-5xl font-black italic tracking-tighter mb-2 cursor-pointer uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[8px] uppercase tracking-[0.4em] mb-12 font-bold italic">USA STRATEGIC ASSET VAULT</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="LEGAL NAME" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 text-white uppercase">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest shadow-2xl active:scale-95 transition-all">ESTABLISH SESSION</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600 shadow-2xl relative overflow-hidden">
                <div class="absolute top-0 right-0 p-4 opacity-10 text-4xl italic font-black">GOLD</div>
                <p class="text-[9px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Available Capital</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="mt-4 text-[9px] font-black text-yellow-500 uppercase italic">System Active</div>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95">📥 <span class="text-[9px] font-black block mt-2 uppercase">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95">📤 <span class="text-[9px] font-black block mt-2 uppercase">Withdraw</span></button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500 text-xl tracking-widest underline decoration-4">Trading Fleet</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400">Security Ledger</h2>
            <div id="user-history" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6 pb-12">
            <div class="glass p-8 rounded-[3rem] text-center border-t-4 border-yellow-500 shadow-2xl">
                <h3 class="text-yellow-500 font-black text-[14px] uppercase mb-4 italic tracking-widest">Lucky Node Spin</h3>
                <div id="wheel-container" class="relative w-40 h-40 mx-auto mb-6 flex items-center justify-center">
                    <div id="spin-visual" class="w-full h-full border-8 border-dashed border-yellow-500/20 rounded-full flex items-center justify-center text-4xl bg-white/5 transition-all duration-[3s]">🎰</div>
                </div>
                <button id="spin-btn" onclick="startSpin()" class="bg-yellow-600 px-12 py-4 rounded-2xl font-black text-[11px] uppercase shadow-xl disabled:opacity-20 active:scale-90">Spin Wheel</button>
                <p id="spin-timer" class="mt-4 text-[9px] font-black text-gray-500 uppercase italic tracking-widest"></p>
            </div>

            <div class="glass p-8 rounded-[3rem] text-center border-b-4 border-green-500">
                <h3 class="text-green-500 font-black text-[12px] uppercase mb-4">Official Community</h3>
                <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3" class="inline-block bg-green-600 px-10 py-4 rounded-2xl font-black text-[10px] uppercase shadow-2xl active:scale-95">💬 Join Group</a>
            </div>

            <div class="glass p-8 rounded-[3rem] border-l-4 border-blue-500">
                <h3 class="text-blue-500 font-black text-[12px] uppercase italic mb-4 underline">Firm Privacy Policy</h3>
                <div class="text-[9px] text-gray-400 space-y-4 font-bold uppercase italic leading-loose">
                    <p><span class="text-white">● CAPITAL PROTECTION:</span> MintCrest Gold utilizes USA-based server encryption to secure user funds against volatility.</p>
                    <p><span class="text-white">● PAYOUT SYSTEM:</span> All withdrawals take 2-24 hours. A standard 10% server tax is mandatory on all profit claims.</p>
                    <p><span class="text-white">● SECURITY:</span> Only one session per IP is permitted. Multi-accounting results in immediate asset freezing.</p>
                </div>
            </div>

            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-center text-[11px] font-black text-red-500 uppercase tracking-[0.2em]">Logout</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] text-center border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm italic tracking-widest">Asset Funding</h3>
                <div class="space-y-3 mb-10 text-[10px] font-black">
                    <div class="glass p-5 rounded-2xl flex justify-between bg-white/5 border border-white/10"><span>JAZZCASH/SADAPAY</span><span class="text-blue-400">03705519562</span></div>
                    <div class="glass p-5 rounded-2xl flex justify-between bg-white/5 border border-white/10"><span>EASYPAISA</span><span class="text-green-400">03379827882</span></div>
                </div>
                
                <input type="number" id="dep-amount" placeholder="AMOUNT (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold outline-none border border-white/10 text-white">
                <input type="text" id="dep-trx" placeholder="TID / TRX ID" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold uppercase outline-none border border-white/10 text-white">
                
                <input type="text" id="dep-promo" placeholder="PROMO CODE" class="w-full bg-blue-500/10 p-5 rounded-2xl mb-8 text-center text-[10px] font-black uppercase outline-none border border-blue-500/30 text-blue-400">

                <button id="dep-btn" onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[11px] uppercase shadow-lg active:scale-95">Verify Deposit</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-red-600 text-center shadow-2xl">
                <h3 class="font-black text-red-500 mb-8 uppercase text-sm italic">Request Payout</h3>
                <input type="number" id="wd-amt" placeholder="AMOUNT (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold outline-none border border-white/10 text-white">
                <input type="text" id="wd-acc" placeholder="ACCOUNT DETAILS" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center text-[9px] font-bold outline-none border border-white/10 text-white">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[11px] uppercase shadow-lg active:scale-95">Request Assets</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Master Console</h2>
            <button onclick="closeAdmin()" class="bg-red-600 text-white px-6 py-2 rounded-xl text-[10px] font-black">EXIT</button>
        </div>
        <div id="adm-sec-requests" class="space-y-4 pb-24"></div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/10 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem] shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="active-tab px-4 py-2 rounded-full">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="px-4 py-2 rounded-full">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="px-4 py-2 rounded-full">📜</button>
        <button onclick="changePage('more')" id="n-more" class="px-4 py-2 rounded-full">🏢</button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            { n: "Bronze Fleet", p: 200 }, { n: "Silver Fleet", p: 1000 }, { n: "Gold Fleet", p: 5000 }, { n: "Diamond Fleet", p: 15000 }
        ];

        window.onload = () => { 
            const saved = localStorage.getItem('mc_user'); 
            if(saved) { document.getElementById('user-name').value = saved; login(); }
        };

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, time: Date.now(), lastSpin: 0 });
            startSync(name);
            document.getElementById('auth-ui').classList.add('hidden'); 
            document.getElementById('app-ui').classList.remove('hidden'); 
            document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); updateUI(); } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center mb-2 text-[10px] font-black uppercase border-l-4 border-blue-500 shadow-lg"><div>${d.type}</div><div class="text-right">₨ ${d.amount}<br><span class="text-blue-400 text-[8px]">${d.status}</span></div></div>`; });
            });
        }

        function updateUI() {
            document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
            const last = user.lastSpin || 0;
            const diff = Date.now() - last;
            const btn = document.getElementById('spin-btn');
            if(diff < 86400000) {
                btn.disabled = true;
                document.getElementById('spin-timer').innerText = "NODE LOCKED: " + Math.ceil((86400000-diff)/3600000) + "H";
            } else { btn.disabled = false; document.getElementById('spin-timer').innerText = "NODE READY"; }
        }

        async function startSpin() {
            const win = [5, 10, 20, 50, 0, 100, 200][Math.floor(Math.random()*7)];
            const wheel = document.getElementById('spin-visual');
            wheel.classList.add('spin-anim');
            setTimeout(async () => {
                wheel.classList.remove('spin-anim');
                wheel.innerText = "₨ " + win;
                if(win > 0) await db.collection("requests").add({ user: user.name, amount: win, type: "Spin Bonus", status: "pending", time: Date.now() });
                await db.collection("users").doc(user.name).update({ lastSpin: Date.now() });
                alert(win > 0 ? "Bonus Recorded!" : "Zero Balance Node!");
            }, 3000);
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => { list.innerHTML += `<div onclick="buy(${p.p})" class="glass p-6 rounded-[2.5rem] flex justify-between items-center active:scale-95 mb-2 cursor-pointer border border-white/5"><div><h4 class="font-black text-[12px] uppercase text-blue-400">${p.n}</h4></div><div class="font-black text-lg tracking-tighter">₨ ${p.p}</div></div>`; });
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value, t = document.getElementById('dep-trx').value, p = document.getElementById('dep-promo').value;
            if(!a || !t) return alert("Enter Details!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, promo: p||"NONE", type: "Deposit", status: "pending", time: Date.now() });
            alert("Sent for Verification!"); changePage('activity');
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value, acc = document.getElementById('wd-acc').value; 
            if(!a || !acc || a > user.balance) return alert("Invalid Balance!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "Withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - a }); 
            alert("Withdrawal Logged!"); changePage('activity');
        }

        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); 
            document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); 
            document.getElementById('p-'+p).classList.add('active-page');
            document.getElementById('n-'+p)?.classList.add('active-tab');
            window.scrollTo(0,0);
        }

        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }

        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); 
                    list.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center text-[10px] font-black uppercase mb-3 border-l-4 border-blue-600">
                        <div>${d.user}<br>Rs ${d.amount}<br>P: ${d.promo||'NO'}</div>
                        <button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-blue-600 px-6 py-3 rounded-2xl">APPROVE</button>
                    </div>`; 
                });
            });
        }

        async function handle(id, u, amt, act, type) { 
            const ref = db.collection("users").doc(u); const doc = await ref.get(); 
            if(act==='approved' && (type==='Deposit' || type==='Spin Bonus')) await ref.update({ balance: (doc.data().balance||0)+amt });
            await db.collection("requests").doc(id).update({ status: act }); alert("Action Finalized!");
        }
    </script>
</body>
</html>
