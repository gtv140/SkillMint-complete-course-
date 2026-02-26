<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest | Global Investment Group</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #020617; color: white; overflow: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(15px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .scroller::-webkit-scrollbar { display: none; }
        .page { display: none; animation: fadeIn 0.4s ease-in-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body class="h-screen flex flex-col">

    <header class="p-4 border-b border-white/5 flex justify-between items-center glass z-[100]">
        <h1 class="text-xl font-black italic tracking-tighter">MINT<span class="text-blue-500">CREST</span></h1>
        <div id="status-dot" class="flex items-center gap-2">
            <span class="text-[10px] font-bold text-green-400">SERVER LIVE</span>
            <div class="h-2 w-2 bg-green-500 rounded-full animate-pulse"></div>
        </div>
    </header>

    <section id="auth-ui" class="h-full flex items-center justify-center p-6 bg-[#020617] z-[1000] absolute inset-0">
        <div class="glass w-full max-w-sm p-12 rounded-[3.5rem] text-center border-t-8 border-blue-600 shadow-2xl">
            <h1 class="text-4xl font-black italic mb-2 tracking-tighter">MINTCREST</h1>
            <p class="text-gray-500 text-[9px] uppercase tracking-[0.4em] mb-12">Login to Secure Server</p>
            <input type="text" id="user-name" placeholder="Full Name" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 outline-none focus:border-blue-500 text-center mb-4 font-bold">
            <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-sm shadow-xl shadow-blue-900/40">ENTER DASHBOARD</button>
        </div>
    </section>

    <div id="app-ui" class="hidden flex-1 overflow-y-auto scroller pb-24">
        
        <div id="p-home" class="page active-page p-6">
            <div class="glass p-8 rounded-[2.5rem] mb-6 border-l-4 border-blue-500">
                <p class="text-xs text-gray-500 font-bold uppercase mb-2">Portfolio Value</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div class="mt-6 flex gap-3">
                    <button onclick="changePage('wallet')" class="bg-blue-600 px-6 py-2 rounded-xl text-[10px] font-bold">DEPOSIT</button>
                    <button onclick="alert('Withdrawals process on Sundays')" class="bg-white/5 px-6 py-2 rounded-xl text-[10px] font-bold border border-white/10">WITHDRAW</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <div class="glass p-5 rounded-3xl"><p class="text-[10px] text-gray-500 mb-1">PROFIT</p><p class="font-bold text-green-400" id="v-profit">₨ 0</p></div>
                <div class="glass p-5 rounded-3xl"><p class="text-[10px] text-gray-500 mb-1">STATUS</p><p class="font-bold text-blue-400">ACTIVE</p></div>
            </div>

            <h3 class="text-sm font-bold text-gray-500 mb-4 uppercase">Latest Market News</h3>
            <div class="glass p-4 rounded-2xl text-[10px] text-gray-400">
                • New Tier Diamond (300%) is now open for investors.<br>
                • Minimum deposit lowered to ₨ 1,000 for Silver Tier.<br>
                • System maintenance scheduled for Sunday 2 AM.
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-2xl font-black mb-6 italic text-blue-500 uppercase">Premium Plans</h2>
            <div id="plans-list" class="space-y-4"></div>
        </div>

        <div id="p-wallet" class="page p-6">
            <h2 class="text-2xl font-black mb-6 italic text-center uppercase">Fund Account</h2>
            <div class="glass p-8 rounded-[3rem] border-t-8 border-green-600">
                <div class="space-y-4">
                    <div class="p-4 bg-white/5 rounded-2xl flex justify-between items-center border border-white/5">
                        <div class="text-left"><p class="text-red-500 text-[8px] font-bold uppercase">JazzCash</p><p class="font-bold">03705519562</p></div>
                        <button onclick="copy('03705519562')" class="text-xs text-blue-500">COPY</button>
                    </div>
                    <div class="p-4 bg-white/5 rounded-2xl flex justify-between items-center border border-white/5">
                        <div class="text-left"><p class="text-green-500 text-[8px] font-bold uppercase">EasyPaisa</p><p class="font-bold">03379827882</p></div>
                        <button onclick="copy('03379827882')" class="text-xs text-blue-500">COPY</button>
                    </div>
                </div>
                <a href="https://wa.me/923379827882" class="block w-full bg-green-600 py-4 rounded-2xl font-bold mt-8 text-center text-xs">SUBMIT PROOF ON WHATSAPP</a>
            </div>
        </div>

        <div id="p-support" class="page p-6">
            <h2 class="text-2xl font-black mb-6 italic text-blue-500 uppercase">Contact & Help</h2>
            <div class="glass p-6 rounded-[2rem] mb-4">
                <h4 class="font-bold mb-2">Customer Support</h4>
                <p class="text-xs text-gray-400 mb-4">Available 24/7 for your queries.</p>
                <a href="mailto:support@mintcrest.com" class="block text-blue-400 text-xs mb-2 underline">support@mintcrest.com</a>
                <p class="text-xs">Office: F-10 Markaz, Islamabad, Pakistan</p>
            </div>
            
            <div class="glass p-6 rounded-[2rem]">
                <h4 class="font-bold mb-2 text-xs uppercase">Terms & Privacy</h4>
                <p class="text-[9px] text-gray-500 leading-relaxed mb-4">
                    By using MintCrest, you agree that investments carry market risk. 
                    MintCrest is not responsible for any financial loss. 
                    Withdrawals are subject to verification and process every Sunday. 
                    Data is secured using Google Cloud Firebase.
                </p>
                <p class="text-[9px] text-gray-500 italic">© 2026 MintCrest Global Assets Group.</p>
            </div>
        </div>
    </div>

    <nav class="glass border-t border-white/5 p-4 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-1 active-tab">🏠<span class="text-[8px] font-bold uppercase">Home</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-1">📈<span class="text-[8px] font-bold uppercase">Invest</span></button>
        <button onclick="changePage('wallet')" id="n-wallet" class="flex flex-col items-center gap-1">💰<span class="text-[8px] font-bold uppercase">Wallet</span></button>
        <button onclick="changePage('support')" id="n-support" class="flex flex-col items-center gap-1">🎧<span class="text-[8px] font-bold uppercase">Help</span></button>
    </nav>

    <div id="admin-panel" class="fixed inset-0 bg-black z-[5000] p-6 hidden overflow-y-auto">
        <h2 class="text-red-500 font-black mb-8">ADMIN CORE</h2>
        <input type="text" id="adm-user" placeholder="User Name" class="w-full bg-white/10 p-4 rounded-xl mb-4 border border-white/10">
        <input type="number" id="adm-val" placeholder="Amount" class="w-full bg-white/10 p-4 rounded-xl mb-4 border border-white/10">
        <div class="flex gap-2">
            <button onclick="updateUser('balance')" class="flex-1 bg-green-600 py-4 rounded-xl font-bold text-xs">ADD BAL</button>
            <button onclick="updateUser('profit')" class="flex-1 bg-blue-600 py-4 rounded-xl font-bold text-xs">ADD PROFIT</button>
        </div>
        <button onclick="closeAdmin()" class="w-full mt-4 bg-white/5 py-4 rounded-xl text-xs">EXIT</button>
        <div id="user-list-adm" class="mt-10 space-y-2 opacity-50"></div>
    </div>

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
        const db = firebase.firestore();

        let user = null;
        const tiers = [
            { n: "Silver Starter", p: 1000, r: "20%" }, { n: "Executive", p: 5000, r: "30%" },
            { n: "VIP Gold", p: 15000, r: "45%" }, { n: "Diamond Master", p: 50000, r: "70%" },
            { n: "Kingdom Tier", p: 1000000, r: "300%" }
        ];

        async function login() {
            const name = document.getElementById('user-name').value.trim();
            if(!name) return;
            const ref = db.collection("users").doc(name);
            const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0 });
            startSync(name);
            document.getElementById('auth-ui').style.display = 'none';
            document.getElementById('app-ui').classList.remove('hidden');
            renderTiers();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                if(doc.exists) {
                    user = doc.data();
                    document.getElementById('v-bal').innerText = "₨ " + user.balance.toLocaleString();
                    document.getElementById('v-profit').innerText = "₨ " + user.profit.toLocaleString();
                }
            });
        }

        function renderTiers() {
            const list = document.getElementById('plans-list');
            list.innerHTML = '';
            tiers.forEach(p => {
                list.innerHTML += `<div onclick="buy(${p.p})" class="glass p-6 rounded-[2rem] flex justify-between items-center border-b-2 border-blue-600">
                    <div><h4 class="font-bold">${p.n}</h4><p class="text-[10px] text-green-400">${p.r} ROI / 10d</p></div>
                    <p class="font-black text-blue-500">₨ ${p.p.toLocaleString()}</p>
                </div>`;
            });
        }

        async function buy(price) {
            if(user.balance < price) { alert("Low balance!"); changePage('wallet'); }
            else { await db.collection("users").doc(user.name).update({ balance: user.balance - price }); alert("Activated!"); }
        }

        function changePage(p) {
            document.querySelectorAll('.page').forEach(page => page.classList.remove('active-page'));
            document.querySelectorAll('nav button').forEach(btn => btn.classList.remove('active-tab'));
            document.getElementById('p-' + p).classList.add('active-page');
            document.getElementById('n-' + p).classList.add('active-tab');
        }

        function copy(t) { navigator.clipboard.writeText(t); alert("Copied!"); }

        // ADMIN CTRL+SHIFT+A
        window.addEventListener('keydown', e => {
            if(e.ctrlKey && e.shiftKey && e.key === 'A') {
                if(prompt("Key:") === "mint786") document.getElementById('admin-panel').classList.remove('hidden');
            }
        });

        async function updateUser(f) {
            const u = document.getElementById('adm-user').value;
            const v = document.getElementById('adm-val').value;
            const ref = db.collection("users").doc(u);
            const doc = await ref.get();
            if(doc.exists) await ref.update({ [f]: (doc.data()[f] || 0) + parseInt(v) });
            alert("Updated!");
        }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
    </script>
</body>
</html>
