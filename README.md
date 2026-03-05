<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script type="text/javascript" src="https://s3.tradingview.com/tv.js"></script>
    <title>MintCrest Gold | Global Enterprise</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;700;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000205; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(30px); border: 1px solid rgba(255,255,255,0.06); border-radius: 2rem; }
        .page { display: none; animation: slideUp 0.3s ease; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .nav-active { color: #3b82f6; border-bottom: 2px solid #3b82f6; padding-bottom: 4px; }
        input, select { background: rgba(255,255,255,0.05); border: 1px solid rgba(255,255,255,0.1); border-radius: 1rem; padding: 1rem; outline: none; text-align: center; }
        .vip-badge { background: linear-gradient(90deg, #ffd700, #ff8c00); color: black; font-weight: 900; padding: 2px 8px; border-radius: 5px; font-size: 8px; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[9000] bg-[#000205] flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 onclick="adminTap()" class="text-5xl font-black italic mb-2 uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-[8px] tracking-[0.4em] text-gray-500 mb-10 font-bold uppercase">Institutional Wealth Management</p>
            <div class="glass p-10 border-t-2 border-blue-600">
                <input type="text" id="user-name" placeholder="Official Identity Name" class="w-full mb-4">
                <input type="password" id="user-pin" placeholder="Set 4-Digit Security PIN" class="w-full mb-6" maxlength="4">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest shadow-xl">Start Investing</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div class="p-8 flex justify-between items-end">
            <div>
                <span class="vip-badge uppercase">Diamond Tier</span>
                <h2 id="u-display" class="text-2xl font-black italic tracking-tighter">--</h2>
            </div>
            <button onclick="changePage('refer')" class="glass p-3 text-[10px] font-bold">🤝 REFER</button>
        </div>

        <div id="p-home" class="page active-page p-6">
            <div class="glass p-10 mb-6 bg-gradient-to-br from-blue-600/10 to-transparent border-l-4 border-blue-600">
                <p class="text-[9px] text-blue-400 font-black uppercase mb-1">Total Assets (PKR)</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div class="flex gap-4 mt-6">
                    <span class="text-[8px] bg-green-500/10 text-green-400 p-2 rounded-lg font-bold uppercase">Profit: <span id="v-profit">₨ 0</span></span>
                    <span class="text-[8px] bg-white/5 text-gray-400 p-2 rounded-lg font-bold uppercase" id="v-tier">Standard</span>
                </div>
            </div>

            <div id="timer-box" class="hidden glass p-5 mb-6 border border-yellow-500/20 text-center">
                <p class="text-[8px] font-black text-yellow-500 uppercase mb-2">Cycle Time Remaining</p>
                <h3 id="cycle-clock" class="text-xl font-black tracking-widest">00:00:00</h3>
            </div>

            <div class="glass p-4 h-32 mb-6 overflow-hidden">
                <p class="text-[8px] text-gray-500 font-bold uppercase mb-2">Live Gold/USD Index</p>
                <div id="tradingview_chart" class="h-full w-full opacity-50"></div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('deposit')" class="glass p-8 text-center bg-blue-600/5">📥<br><span class="text-[9px] font-black uppercase mt-2 block">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 text-center bg-red-600/5">📤<br><span class="text-[9px] font-black uppercase mt-2 block">Withdraw</span></button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black uppercase text-sm mb-8 italic text-blue-500">Global Mining Fleets</h2>
            <div id="plans-list" class="space-y-4"></div>
        </div>

        <div id="p-refer" class="page p-6">
            <div class="glass p-8 text-center border-t-4 border-purple-600">
                <h3 class="font-black uppercase text-sm mb-4">Refer & Earn</h3>
                <p class="text-[10px] text-gray-400 mb-6 uppercase">Invite partners and get 10% commission on every deposit they make!</p>
                <div class="bg-black/40 p-4 rounded-xl text-[10px] font-mono break-all mb-6" id="refer-link">https://mintcrest.gold/join?ref=user</div>
                <button onclick="copyRefer()" class="w-full bg-purple-600 py-4 rounded-xl font-black text-[10px] uppercase">Copy Link</button>
            </div>
        </div>

        <div id="p-deposit" class="page p-6">
            <div class="glass p-8 border-t-8 border-blue-600">
                <h3 class="text-center font-black uppercase text-sm mb-8">Funding</h3>
                <input type="number" id="dep-amt" placeholder="Amount (Min 200)" class="w-full mb-4">
                <input type="text" id="dep-tid" placeholder="Transaction ID (TID)" class="w-full mb-4 uppercase">
                <label class="block w-full bg-white/5 p-4 rounded-xl border-2 border-dashed border-white/10 text-center cursor-pointer mb-6">
                    <span id="dep-proof-text" class="text-[9px] font-bold uppercase opacity-50">📸 Upload Payment Proof</span>
                    <input type="file" id="dep-file" class="hidden" onchange="document.getElementById('dep-proof-text').innerText='Attached ✅'">
                </label>
                <button onclick="submitDep()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[10px] uppercase">Verify</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-8 border-t-8 border-red-600">
                <h3 class="text-center font-black uppercase text-sm mb-8">Withdraw</h3>
                <input type="number" id="wd-amt" placeholder="Amount" class="w-full mb-4">
                <input type="text" id="wd-acc" placeholder="Account Details" class="w-full mb-4">
                <input type="password" id="wd-pin" placeholder="Enter 4-Digit PIN" class="w-full mb-8" maxlength="4">
                <button onclick="submitWd()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[10px] uppercase">Extract</button>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden glass fixed bottom-0 left-0 w-full p-8 flex justify-around items-center z-[1000] rounded-t-[3.5rem] border-t border-white/5 bg-black/90">
        <button onclick="changePage('home')" id="n-home" class="nav-active text-2xl">🏛️</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-2xl">📈</button>
        <button onclick="changePage('deposit')" id="n-deposit" class="text-2xl">📥</button>
        <button onclick="changePage('more')" id="n-more" class="text-2xl">⚙️</button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            {n:"Trial Node", p:200, r:3}, {n:"Bronze Node", p:500, r:3.5}, {n:"Silver Node", p:1000, r:4},
            {n:"Gold Node", p:3000, r:4.5}, {n:"Platinum Node", p:5000, r:5}, {n:"Diamond Node", p:10000, r:6}
            // ... more plans
        ];

        async function login() {
            const name = document.getElementById('user-name').value.trim();
            const pin = document.getElementById('user-pin').value;
            if(!name || pin.length < 4) return alert("Fill Name & 4-Digit PIN!");
            
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(doc.exists && doc.data().pin !== pin) return alert("Incorrect PIN!");
            if(!doc.exists) await ref.set({ name: name, pin: pin, balance: 0, profit: 0, lastReqTime: 0, tierName: "Standard", banned: false });
            
            localStorage.setItem('mc_user', name);
            document.getElementById('u-display').innerText = name;
            document.getElementById('refer-link').innerText = `https://mintcrest.gold/join?ref=${name}`;
            startSync(name);
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
            new TradingView.widget({"container_id": "tradingview_chart", "symbols": [["OANDA:XAUUSD|1D"]], "interval": "D", "timezone": "Etc/UTC", "theme": "dark", "style": "3", "locale": "en", "enable_publishing": false, "hide_top_toolbar": true, "save_image": false});
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                if(doc.exists) { 
                    user = doc.data();
                    document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
                    document.getElementById('v-profit').innerText = "₨ " + (user.profit||0).toLocaleString();
                    document.getElementById('v-tier').innerText = user.tierName;
                    updateTimer();
                }
            });
        }

        function updateTimer() {
            if(user.lastReqTime > 0) {
                document.getElementById('timer-box').classList.remove('hidden');
                const run = () => {
                    const diff = (user.lastReqTime + 86400000) - Date.now();
                    if(diff > 0) {
                        document.getElementById('cycle-clock').innerText = new Date(diff).toISOString().substr(11, 8);
                        requestAnimationFrame(run);
                    } else { document.getElementById('cycle-clock').innerText = "PROFIT READY"; }
                }; run();
            }
        }

        function renderPlans() {
            const l = document.getElementById('plans-list'); l.innerHTML = '';
            plans.forEach(p => {
                l.innerHTML += `<div onclick="buyP(${p.p},${p.r},'${p.n}')" class="glass p-6 flex justify-between items-center active:scale-95 transition-all">
                    <div><h4 class="font-black text-[10px] uppercase">${p.n}</h4><p class="text-[8px] text-green-500 font-bold uppercase">${p.r}% Daily ROI</p></div>
                    <div class="font-black text-sm">₨ ${p.p}</div>
                </div>`;
            });
        }

        async function buyP(p, r, n) {
            if(user.balance < p) return alert("Low Liquidity!");
            await db.collection("users").doc(user.name).update({ balance: user.balance - p, tierName: n, lastReqTime: Date.now() });
            alert("Fleet Active! Profit in 24h. 😘"); changePage('home');
        }

        async function submitDep() {
            const a = document.getElementById('dep-amt').value; const f = document.getElementById('dep-file').files[0];
            if(!a || !f) return alert("Missing Data/Proof!");
            const reader = new FileReader(); reader.readAsDataURL(f);
            reader.onload = async () => {
                await db.collection("requests").add({ user: user.name, type: "deposit", amount: parseInt(a), proof: reader.result, status: "pending", time: Date.now() });
                alert("Deposit Received! Admin will verify proof sweetie. 😘");
            };
        }

        async function submitWd() {
            const a = document.getElementById('wd-amt').value; const pin = document.getElementById('wd-pin').value;
            if(pin !== user.pin) return alert("Wrong PIN!");
            if(a > user.balance) return alert("Insufficient Balance!");
            await db.collection("requests").add({ user: user.name, type: "withdraw", amount: parseInt(a), status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) });
            alert("Extraction Request Sent! ❤️");
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('nav-active')); document.getElementById('p-'+p).classList.add('active-page'); if(document.getElementById('n-'+p)) document.getElementById('n-'+p).classList.add('nav-active'); }
        function copyRefer() { navigator.clipboard.writeText(document.getElementById('refer-link').innerText); alert("Link Copied!"); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        window.onload = () => { if(localStorage.getItem('mc_user')) { login(); } };
    </script>
</body>
</html>
