<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Elite Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --accent: #238636; --border: #30363d; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); margin: 0; padding-bottom: 90px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); }
        .page { display: none; animation: fadeIn 0.3s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .btn-blue { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; font-weight: 800; border-radius: 14px; }
        input { background: var(--card); border: 1px solid var(--border); color: var(--text); border-radius: 12px; padding: 14px; font-size: 13px; width: 100%; outline: none; }
        .nav-active { color: #3b82f6; border-top: 2px solid #3b82f6; background: rgba(59, 130, 246, 0.05); }
        
        /* Sidebar Menu */
        #side-menu { transition: 0.3s ease; transform: translateX(-100%); z-index: 10000; }
        .menu-open { transform: translateX(0) !important; }
        ::-webkit-scrollbar { display: none; }
    </style>
</head>
<body>

    <div id="side-menu" class="fixed inset-0 bg-black/60 backdrop-blur-md">
        <div class="w-72 h-full bg-[var(--bg)] border-r border-[var(--border)] p-6">
            <div class="flex justify-between items-center mb-10">
                <h2 class="font-black italic text-blue-500 uppercase">Menu</h2>
                <button onclick="toggleMenu()" class="text-2xl">✕</button>
            </div>
            <nav class="space-y-4">
                <button onclick="changePage('about'); toggleMenu()" class="w-full text-left p-4 glass rounded-xl text-[10px] font-black uppercase">About Company</button>
                <button onclick="changePage('privacy'); toggleMenu()" class="w-full text-left p-4 glass rounded-xl text-[10px] font-black uppercase">Privacy Policy</button>
                <a href="https://chat.whatsapp.com/YOUR_GROUP_LINK" target="_blank" class="block w-full text-left p-4 glass rounded-xl text-[10px] font-black uppercase text-green-500">WhatsApp Group 🟢</a>
                <button onclick="changePage('chat'); toggleMenu()" class="w-full text-left p-4 glass rounded-xl text-[10px] font-black uppercase text-blue-400">Help Desk / Admin</button>
                <hr class="border-[var(--border)] my-6">
                <button onclick="logout()" class="w-full text-left p-4 bg-red-600/10 text-red-500 rounded-xl text-[10px] font-black uppercase">Logout</button>
            </nav>
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-8 text-center">
        <div class="w-16 h-16 bg-blue-600 rounded-2xl mb-4 flex items-center justify-center text-3xl font-black text-white shadow-xl">M</div>
        <h1 onclick="adminTap()" class="text-2xl font-black italic tracking-tighter mb-10 uppercase">MintCrest Gold</h1>
        <div class="w-full max-w-[280px] space-y-3">
            <input type="text" id="user-name" placeholder="Enter Username" class="text-center font-bold">
            <button onclick="login()" class="w-full p-4 btn-blue uppercase text-[10px] tracking-widest">Connect Node</button>
        </div>
    </section>

    <header class="p-4 flex justify-between items-center sticky top-0 bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)] z-[5000]">
        <div class="flex items-center gap-3">
            <button onclick="toggleMenu()" class="text-xl">☰</button>
            <span class="font-black text-sm italic uppercase tracking-tighter">MintCrest</span>
        </div>
        <div class="w-8 h-8 bg-blue-600 rounded-lg flex items-center justify-center text-[10px] font-black text-white shadow-lg">M</div>
    </header>

    <main id="app-ui" class="hidden p-4 space-y-6">
        
        <div id="p-home" class="page active-page">
            <div class="p-8 rounded-[2.5rem] bg-gradient-to-br from-blue-700 to-blue-900 text-white shadow-xl mb-6 relative overflow-hidden">
                <p class="text-[10px] font-black uppercase opacity-60">Global Balance</p>
                <h2 class="text-4xl font-black tracking-tighter mt-1" id="v-bal">₨ 0.00</h2>
                <div class="mt-6 flex justify-between items-center bg-black/20 p-4 rounded-2xl">
                    <div class="text-[8px] font-bold uppercase">Manual Profit System Active</div>
                    <button onclick="requestProfit()" class="bg-white text-blue-800 px-5 py-2 rounded-xl text-[9px] font-black uppercase shadow-md active:scale-90">Daily Profit 💰</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-5 rounded-3xl border-b-4 border-green-500 active:scale-95 transition-all">
                    <span class="text-2xl block mb-2">📥</span>
                    <p class="text-[10px] font-black uppercase">Deposit</p>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-5 rounded-3xl border-b-4 border-red-500 active:scale-95 transition-all">
                    <span class="text-2xl block mb-2">📤</span>
                    <p class="text-[10px] font-black uppercase">Withdraw</p>
                </button>
            </div>
        </div>

        <div id="p-plans" class="page">
            <h3 class="text-[10px] font-black uppercase opacity-40 mb-5 text-center italic tracking-widest">Active Yield Portfolios</h3>
            <div id="plans-list" class="space-y-3"></div>
        </div>

        <div id="p-history" class="page">
            <h3 class="text-[11px] font-black uppercase opacity-40 mb-4 italic tracking-widest">Ledger History</h3>
            <div id="history-list" class="space-y-3"></div>
        </div>

        <div id="p-about" class="page space-y-6">
            <div class="glass p-6 rounded-[2rem]">
                <h3 class="text-blue-500 font-black text-[11px] uppercase mb-4 italic">The MintCrest Legacy</h3>
                <p class="text-[10px] leading-relaxed opacity-60 font-bold italic">MintCrest Gold is a legally recognized financial node providing global users with secure asset growth. We operate 24/7 with manual auditing to ensure total transparency and liquidity safety.</p>
            </div>
        </div>

        <div id="p-privacy" class="page">
            <div class="glass p-6 rounded-[2rem]">
                <h3 class="text-green-500 font-black text-[11px] uppercase mb-4 italic">Security Protocols</h3>
                <p class="text-[10px] leading-relaxed opacity-60 font-bold italic">Every transaction is manually verified by an administrator. User data is encrypted. We do not process automatic payouts to maintain the highest level of security for your capital.</p>
            </div>
        </div>

        <div id="p-chat" class="page">
            <div class="glass rounded-[2rem] flex flex-col h-[65vh] overflow-hidden">
                <div class="bg-blue-600/10 p-4 border-b border-[var(--border)] text-center">
                    <span class="text-[10px] font-black uppercase text-blue-500">Official Admin Support</span>
                </div>
                <div id="sup-list" class="flex-1 overflow-y-auto p-4 space-y-3 text-[10px]"></div>
                <div class="p-4 border-t border-[var(--border)] flex gap-2">
                    <input type="text" id="sup-msg" placeholder="Tell us your issue..." class="flex-1 p-3">
                    <button onclick="sendMsg()" class="btn-blue px-6 font-black text-[10px] uppercase">Send</button>
                </div>
            </div>
        </div>

    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[var(--bg)] z-[100000] hidden overflow-y-auto p-6">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-2xl font-black text-blue-500 italic uppercase">Admin Command</h2>
            <button onclick="closeAdmin()" class="bg-red-500 text-white px-5 py-2 rounded-xl text-[10px] font-black uppercase">Close</button>
        </div>
        <div id="adm-list" class="space-y-4 pb-20"></div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-4 flex justify-around items-center rounded-t-[2rem] z-[4000]">
        <button onclick="changePage('home')" id="n-home" class="nav-active flex flex-col items-center gap-1 px-4 py-2 rounded-2xl">🏠<span class="text-[7px] font-black uppercase">Home</span></button>
        <button onclick="changePage('plans')" id="n-plans" class="opacity-40 flex flex-col items-center gap-1 px-4 py-2 rounded-2xl">📊<span class="text-[7px] font-black uppercase">Plans</span></button>
        <button onclick="changePage('history')" id="n-history" class="opacity-40 flex flex-col items-center gap-1 px-4 py-2 rounded-2xl">📜<span class="text-[7px] font-black uppercase">History</span></button>
        <button onclick="changePage('chat')" id="n-chat" class="opacity-40 flex flex-col items-center gap-1 px-4 py-2 rounded-2xl">💬<span class="text-[7px] font-black uppercase">Help</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.appspot.com", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = []; for(let i=1; i<=15; i++) { plans.push({ name: `Elite Tier ${i}`, price: 300 * i, daily: 22 * i }); }

        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase(); if(!n) return;
            localStorage.setItem('mc_user', n);
            const ref = db.collection("users").doc(n); const d = await ref.get();
            if(!d.exists) await ref.set({ name: n, balance: 0, time: Date.now() });
            document.getElementById('auth-ui').style.display='none'; 
            document.getElementById('app-ui').classList.remove('hidden'); 
            document.getElementById('bottom-nav').classList.remove('hidden');
            sync(n); renderPlans();
        }

        function sync(n) {
            db.collection("users").doc(n).onSnapshot(d => { user = d.data(); document.getElementById('v-bal').innerText = "₨ "+(user.balance || 0).toLocaleString(); });
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").limit(15).onSnapshot(s => {
                const list = document.getElementById('history-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const x = doc.data();
                    list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center text-[10px] font-black border-l-4 ${x.status==='approved'?'border-green-500':'border-yellow-500'}">
                        <div><p class="uppercase">${x.type}</p><p class="text-[8px] opacity-40 italic">${new Date(x.time).toLocaleDateString()}</p></div>
                        <div class="text-right"><p>₨ ${x.amount||0}</p><p class="uppercase text-[8px] ${x.status==='approved'?'text-green-500':'text-yellow-500'}">${x.status}</p></div>
                    </div>`;
                });
            });
            db.collection("support").where("user", "==", n).orderBy("time", "asc").onSnapshot(s => {
                const list = document.getElementById('sup-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const m = doc.data(); const isAdm = m.from === 'admin';
                    list.innerHTML += `<div class="flex ${isAdm?'justify-start':'justify-end'}"><div class="${isAdm?'bg-gray-800':'bg-blue-600'} p-3 rounded-2xl max-w-[85%] text-white font-bold">${m.message}</div></div>`;
                });
                list.scrollTo(0, list.scrollHeight);
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                list.innerHTML += `<div onclick="buyPlan(${p.price}, '${p.name}')" class="glass p-5 rounded-3xl flex justify-between items-center active:scale-95 border-l-4 border-blue-500 shadow-lg">
                    <div><h4 class="font-black text-[11px] uppercase italic">${p.name}</h4><p class="text-[8px] text-blue-400 font-bold italic">₨ ${p.daily} Daily Yield</p></div>
                    <div class="text-[12px] font-black italic">₨ ${p.price}</div>
                </div>`;
            });
        }

        async function requestProfit() { await db.collection("requests").add({ user: user.name, type: "Daily Profit Request", status: "pending", time: Date.now() }); alert("Request sent to Admin! 😘"); }
        async function sendMsg() { const m = document.getElementById('sup-msg').value; if(!m) return; await db.collection("support").add({ user: user.name, from: "user", message: m, time: Date.now() }); document.getElementById('sup-msg').value = ''; }
        
        // ADMIN HUB
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const list = document.getElementById('adm-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const x = doc.data();
                    list.innerHTML += `<div class="glass p-5 rounded-3xl border-l-4 border-blue-500">
                        <p class="text-[11px] font-black uppercase italic">${x.user} | ${x.type}</p>
                        <div class="flex gap-2 mt-4">
                            <button onclick="approve('${doc.id}','${x.user}','${x.type}', ${x.amount||0})" class="flex-1 bg-blue-600 p-3 rounded-xl text-[9px] font-black uppercase">Approve</button>
                            <button onclick="reject('${doc.id}','${x.user}')" class="flex-1 bg-red-600 p-3 rounded-xl text-[9px] font-black uppercase">Reject</button>
                        </div>
                    </div>`;
                });
            });
        }

        async function approve(id, u, type, amt) {
            let finalAmt = amt;
            if(type.includes("Profit") || type.includes("Bonus")) finalAmt = parseInt(prompt("Enter Amount for " + u + ":"));
            if(isNaN(finalAmt)) return;
            await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(finalAmt) });
            await db.collection("requests").doc(id).update({ status: 'approved', amount: finalAmt });
        }

        async function reject(id, u) { await db.collection("requests").doc(id).update({ status: 'rejected' }); }

        function toggleMenu() { document.getElementById('side-menu').classList.toggle('menu-open'); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.add('opacity-40')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('nav-active')); if(document.getElementById('n-'+p)) { document.getElementById('n-'+p).classList.remove('opacity-40'); document.getElementById('n-'+p).classList.add('nav-active'); } }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
    </script>
</body>
</html>
