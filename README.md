<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest | Professional Asset Management</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #020617; color: white; overflow: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(15px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .scroller::-webkit-scrollbar { display: none; }
        @keyframes slide { from { transform: translateX(100%); } to { transform: translateX(-100%); } }
        .ticker { animation: slide 20s linear infinite; }
        .stat-card { transition: all 0.3s ease; }
        .stat-card:hover { transform: translateY(-5px); border-color: #3b82f6; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div class="w-full bg-blue-900/30 border-b border-white/5 py-1 overflow-hidden z-[100]">
        <div class="ticker whitespace-nowrap text-[10px] font-bold uppercase tracking-widest text-blue-400">
            🔥 FLASH: New Diamond Tier launched with 300% ROI • BTC at All-Time High • MintCrest cross 10,000 active users • Deposit via JazzCash now active • 
        </div>
    </div>

    <section id="auth-ui" class="h-full flex items-center justify-center p-6 bg-[#020617] z-[1000]">
        <div class="glass w-full max-w-sm p-12 rounded-[3.5rem] text-center border-t-8 border-blue-600 shadow-2xl">
            <h1 class="text-4xl font-black italic tracking-tighter mb-2">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[9px] uppercase tracking-[0.4em] mb-12">Institutional Grade Investing</p>
            <input type="text" id="user-name" placeholder="Full Name" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 outline-none focus:border-blue-500 text-center mb-4 font-bold text-lg">
            <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-sm shadow-xl shadow-blue-900/40 hover:scale-105 transition-all">ENTER PLATFORM</button>
            <div class="mt-8 flex justify-center gap-2 opacity-30">
                <div class="h-4 w-4 bg-white rounded-full"></div>
                <div class="h-4 w-4 bg-blue-600 rounded-full"></div>
            </div>
        </div>
    </section>

    <div id="app-ui" class="hidden flex-1 flex flex-col overflow-hidden">
        <main class="flex-1 overflow-y-auto scroller p-6">
            
            <section id="view-home" class="max-w-4xl mx-auto space-y-6">
                <div class="flex justify-between items-center">
                    <div>
                        <p class="text-gray-500 text-[10px] font-bold uppercase">Welcome Back,</p>
                        <h2 class="text-2xl font-black flex items-center gap-2" id="hello-user">... <span class="text-[10px] bg-green-500/20 text-green-400 px-2 py-1 rounded-md font-bold">VERIFIED</span></h2>
                    </div>
                    <div class="h-12 w-12 glass rounded-2xl flex items-center justify-center shadow-lg border-blue-500/50">👤</div>
                </div>

                <div class="glass p-10 rounded-[3rem] bg-gradient-to-br from-blue-600/20 via-transparent to-purple-600/5 border-l-8 border-blue-600">
                    <div class="flex justify-between items-start mb-2">
                        <p class="text-[10px] font-bold text-gray-400 uppercase tracking-widest">Global Assets Portfolio</p>
                        <span class="text-[10px] text-blue-400 font-bold">LIVE UPDATING</span>
                    </div>
                    <h3 class="text-6xl font-black tracking-tighter" id="v-bal">₨ 0</h3>
                    <div class="mt-10 flex flex-wrap gap-4">
                        <button onclick="changePage('wallet')" class="bg-blue-600 px-10 py-4 rounded-2xl font-black text-[10px] shadow-lg shadow-blue-900/30 active:scale-95 transition">DEPOSIT FUNDS</button>
                        <button onclick="alert('Withdrawals process on Sundays. Minimum: ₨ 500')" class="bg-white/5 border border-white/10 px-10 py-4 rounded-2xl font-black text-[10px] active:scale-95 transition">WITHDRAW</button>
                    </div>
                </div>

                <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
                    <div class="glass p-5 rounded-[2rem] stat-card">
                        <p class="text-gray-500 text-[9px] font-bold mb-1 uppercase">Total Profit</p>
                        <p class="text-lg font-black text-green-400" id="v-profit">₨ 0</p>
                    </div>
                    <div class="glass p-5 rounded-[2rem] stat-card">
                        <p class="text-gray-500 text-[9px] font-bold mb-1 uppercase">Active Plans</p>
                        <p class="text-lg font-black" id="v-plans">0</p>
                    </div>
                    <div class="glass p-5 rounded-[2rem] stat-card">
                        <p class="text-gray-500 text-[9px] font-bold mb-1 uppercase">Account Level</p>
                        <p class="text-lg font-black text-blue-400">BRONZE</p>
                    </div>
                    <div class="glass p-5 rounded-[2rem] stat-card">
                        <p class="text-gray-500 text-[9px] font-bold mb-1 uppercase">Referrals</p>
                        <p class="text-lg font-black">0</p>
                    </div>
                </div>

                <div class="glass p-6 rounded-[2rem] bg-blue-600/5 border-dashed border-2 border-blue-500/20">
                    <h4 class="text-xs font-bold mb-4 flex items-center gap-2">🎁 Share & Earn 10% Bonus</h4>
                    <div class="flex justify-between items-center bg-black/20 p-4 rounded-xl">
                        <span class="text-xs font-mono text-blue-400" id="ref-id">MINT-XXXX</span>
                        <button onclick="copy('MINT-REF-LINK')" class="text-[10px] font-bold text-gray-400 hover:text-white">COPY LINK</button>
                    </div>
                </div>
            </section>

            <section id="view-invest" class="hidden max-w-4xl mx-auto pb-24">
                <div class="mb-10">
                    <h2 class="text-3xl font-black italic text-blue-500">INVESTMENT TIERS</h2>
                    <p class="text-gray-500 text-xs">High-yield capital growth plans.</p>
                </div>
                <div id="plans-list" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6"></div>
            </section>

            <section id="view-wallet" class="hidden max-w-xl mx-auto text-center">
                <h2 class="text-3xl font-black mb-10 italic uppercase">Payment Gateway</h2>
                <div class="glass p-10 rounded-[4rem] border-t-8 border-green-600 shadow-2xl relative overflow-hidden">
                    <div class="absolute top-0 right-0 p-4 opacity-10 text-4xl font-black uppercase">Secure</div>
                    <p class="text-gray-400 text-xs mb-10 leading-relaxed">Send your payment to the official merchant accounts below. After sending, share the Transaction ID and Screenshot on WhatsApp.</p>
                    <div class="space-y-4 mb-10 text-left">
                        <div class="p-6 bg-white/5 rounded-3xl flex justify-between items-center border border-white/5">
                            <div><p class="text-red-500 text-[9px] font-bold uppercase tracking-widest">Merchant JazzCash</p><p class="text-xl font-black">03705519562</p></div>
                            <button onclick="copy('03705519562')" class="bg-red-600/20 text-red-500 px-4 py-2 rounded-xl text-[10px] font-bold">COPY</button>
                        </div>
                        <div class="p-6 bg-white/5 rounded-3xl flex justify-between items-center border border-white/5">
                            <div><p class="text-green-500 text-[9px] font-bold uppercase tracking-widest">Merchant EasyPaisa</p><p class="text-xl font-black">03379827882</p></div>
                            <button onclick="copy('03379827882')" class="bg-green-600/20 text-green-500 px-4 py-2 rounded-xl text-[10px] font-bold">COPY</button>
                        </div>
                    </div>
                    <a href="https://wa.me/923379827882" class="block w-full bg-green-600 py-6 rounded-[2.5rem] font-black text-sm shadow-xl shadow-green-900/40 hover:scale-105 transition-all">CONNECT WITH MERCHANT</a>
                </div>
            </section>
        </main>

        <nav class="glass border-t border-white/5 p-6 flex justify-around items-center z-[500] backdrop-blur-3xl">
            <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-1 active-tab">🏠<span class="text-[8px] font-bold uppercase tracking-widest">Home</span></button>
            <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-1">📈<span class="text-[8px] font-bold uppercase tracking-widest">Invest</span></button>
            <button onclick="changePage('wallet')" id="n-wallet" class="flex flex-col items-center gap-1">💰<span class="text-[8px] font-bold uppercase tracking-widest">Wallet</span></button>
            <button onclick="logout()" class="flex flex-col items-center gap-1 text-red-500/50">🚪<span class="text-[8px] font-bold uppercase tracking-widest">Exit</span></button>
        </nav>
    </div>

    <div id="admin-panel" class="fixed inset-0 bg-black z-[5000] p-6 hidden overflow-y-auto">
        <div class="max-w-xl mx-auto">
            <div class="flex justify-between items-center mb-10"><h2 class="text-2xl font-black text-red-500">MINTCREST CORE</h2><button onclick="closeAdmin()" class="bg-white/10 px-4 py-2 rounded-xl text-xs">EXIT ADMIN</button></div>
            <div class="glass p-8 rounded-[2.5rem] mb-6 border-red-500/30">
                <input type="text" id="adm-user" placeholder="User Name" class="w-full bg-white/5 p-4 rounded-xl mb-4 border border-white/10">
                <div class="grid grid-cols-2 gap-4">
                    <input type="number" id="adm-val" placeholder="Add Bal" class="bg-white/5 p-4 rounded-xl border border-white/10">
                    <button onclick="updateUser('balance')" class="bg-green-600 font-bold rounded-xl">DEPOSIT</button>
                    <input type="number" id="adm-prof" placeholder="Add Prof" class="bg-white/5 p-4 rounded-xl border border-white/10">
                    <button onclick="updateUser('profit')" class="bg-blue-600 font-bold rounded-xl">PROFITS</button>
                </div>
            </div>
            <div id="user-list-adm" class="space-y-2"></div>
        </div>
    </div>

    <script>
        // CONFIG
        const firebaseConfig = {
            apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg",
            authDomain: "investment-84f4e.firebaseapp.com",
            projectId: "investment-84f4e",
            storageBucket: "investment-84f4e.firebasestorage.app",
            messagingSenderId: "975293889308",
            appId: "1:975293889308:web:6d034a99cc966c75ff58d9"
        };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();

        let user = null;
        const tiers = [
            { n: "Silver Starter", p: 1000, r: "20%" }, { n: "Growth Pro", p: 3000, r: "25%" },
            { n: "Executive", p: 7000, r: "30%" }, { n: "VIP Prime", p: 15000, r: "40%" },
            { n: "Diamond", p: 50000, r: "60%" }, { n: "Elite Master", p: 100000, r: "80%" },
            { n: "Grand Fortune", p: 250000, r: "110%" }, { n: "Mint Legend", p: 500000, r: "150%" },
            { n: "Kingdom", p: 1000000, r: "250%" }, { n: "Global Apex", p: 2500000, r: "500%" }
        ];

        async function login() {
            const name = document.getElementById('user-name').value.trim();
            if(!name) return alert("Enter Name!");
            const ref = db.collection("users").doc(name);
            const doc = await ref.get();
            if(!doc.exists) {
                user = { name: name, balance: 0, profit: 0, plans: 0, refCode: "MINT-" + Math.floor(1000+Math.random()*9000) };
                await ref.set(user);
            }
            startSync(name);
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            renderTiers();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                if(doc.exists) {
                    user = doc.data();
                    document.getElementById('hello-user').innerHTML = `${user.name} <span class="text-[10px] bg-green-500/20 text-green-400 px-2 py-1 rounded-md font-bold">VERIFIED</span>`;
                    document.getElementById('v-bal').innerText = "₨ " + user.balance.toLocaleString();
                    document.getElementById('v-profit').innerText = "₨ " + user.profit.toLocaleString();
                    document.getElementById('v-plans').innerText = user.plans || 0;
                    document.getElementById('ref-id').innerText = user.refCode || "MINT-XXXX";
                }
            });
        }

        function renderTiers() {
            const list = document.getElementById('plans-list');
            list.innerHTML = '';
            tiers.forEach(p => {
                list.innerHTML += `
                    <div onclick="buy(${p.p})" class="glass p-8 rounded-[3rem] border-b-4 border-blue-600 hover:border-blue-400 transition cursor-pointer">
                        <div class="flex justify-between items-start mb-6">
                            <h4 class="font-black text-xl uppercase italic">${p.n}</h4>
                            <span class="text-[10px] bg-blue-600/20 text-blue-400 px-2 py-1 rounded-md">LIVE</span>
                        </div>
                        <p class="text-green-400 font-bold text-sm mb-8">${p.r} Return in 10 Days</p>
                        <div class="flex justify-between items-center bg-white/5 p-4 rounded-2xl">
                            <span class="font-black text-blue-500 text-lg">₨ ${p.p.toLocaleString()}</span>
                            <span class="text-[9px] font-black opacity-40 uppercase tracking-widest">Subscribe</span>
                        </div>
                    </div>
                `;
            });
        }

        async function buy(price) {
            if(user.balance < price) { alert("Low balance! Redirecting to Wallet."); changePage('wallet'); }
            else {
                const newPlans = (user.plans || 0) + 1;
                await db.collection("users").doc(user.name).update({ balance: user.balance - price, plans: newPlans });
                alert("Subscription Success! Profit starts now.");
            }
        }

        function changePage(p) {
            ['home', 'invest', 'wallet'].forEach(v => {
                document.getElementById('view-' + v).classList.add('hidden');
                document.getElementById('n-' + v).classList.remove('active-tab');
            });
            document.getElementById('view-' + p).classList.remove('hidden');
            document.getElementById('n-' + p).classList.add('active-tab');
        }

        function logout() { location.reload(); }
        function copy(t) { navigator.clipboard.writeText(t); alert("Copied!"); }

        // ADMIN ACCESS
        window.addEventListener('keydown', e => {
            if(e.ctrlKey && e.shiftKey && e.key === 'A') {
                if(prompt("System Key:") === "mint786") {
                    document.getElementById('admin-panel').classList.remove('hidden');
                    loadUsers();
                }
            }
        });

        async function loadUsers() {
            const snap = await db.collection("users").get();
            const list = document.getElementById('user-list-adm');
            list.innerHTML = '';
            snap.forEach(doc => {
                const d = doc.data();
                list.innerHTML += `<div class="p-4 bg-white/5 rounded-2xl text-[10px] flex justify-between"><span>${d.name}</span><b>₨ ${d.balance}</b></div>`;
            });
        }

        async function updateUser(f) {
            const u = document.getElementById('adm-user').value;
            const v = f === 'balance' ? document.getElementById('adm-val').value : document.getElementById('adm-prof').value;
            if(!u || !v) return;
            const ref = db.collection("users").doc(u);
            const doc = await ref.get();
            if(doc.exists) {
                await ref.update({ [f]: (doc.data()[f] || 0) + parseInt(v) });
                alert("System Updated!"); loadUsers();
            }
        }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
    </script>
</body>
</html>
