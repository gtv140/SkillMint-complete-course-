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
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); margin: 0; padding-bottom: 90px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); }
        .page { display: none; animation: fadeIn 0.4s ease forwards; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .btn-blue { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; font-weight: 800; border-radius: 14px; transition: 0.2s; }
        .btn-blue:active { transform: scale(0.95); }
        input { background: var(--card); border: 1px solid var(--border); color: var(--text); border-radius: 12px; padding: 14px; font-size: 13px; width: 100%; outline: none; }
        .nav-active { color: #3b82f6; border-top: 3px solid #3b82f6; background: rgba(59, 130, 246, 0.05); }
        #side-menu { transition: 0.3s ease; transform: translateX(-100%); z-index: 10000; }
        .menu-open { transform: translateX(0) !important; }
        .bank-card { transition: 0.3s; cursor: pointer; border: 2px solid transparent; }
        .bank-selected { border-color: #3b82f6 !important; background: rgba(59, 130, 246, 0.1) !important; }
        ::-webkit-scrollbar { display: none; }
    </style>
</head>
<body>

    <div id="maintenance-screen" class="fixed inset-0 z-[1000000] bg-[var(--bg)] hidden flex flex-col items-center justify-center p-8 text-center">
        <div class="w-20 h-20 bg-yellow-500/20 rounded-full flex items-center justify-center mb-6 animate-pulse text-4xl">⚙️</div>
        <h1 class="text-2xl font-black uppercase italic text-yellow-500 mb-2">System Upgrade</h1>
        <p class="text-[10px] font-bold opacity-60 uppercase tracking-widest leading-relaxed">We are currently optimizing the node for better performance. <br> Please check back in a few minutes, sweetie!</p>
    </div>

    <div id="side-menu" class="fixed inset-0 bg-black/60 backdrop-blur-md">
        <div class="w-72 h-full bg-[var(--bg)] border-r border-[var(--border)] p-6">
            <div class="flex justify-between items-center mb-10">
                <h2 class="font-black italic text-blue-500 uppercase">Menu</h2>
                <button onclick="toggleMenu()" class="text-2xl">✕</button>
            </div>
            <nav class="space-y-4">
                <button onclick="changePage('about'); toggleMenu()" class="w-full text-left p-4 glass rounded-xl text-[10px] font-black uppercase tracking-widest">About Company</button>
                <button onclick="changePage('privacy'); toggleMenu()" class="w-full text-left p-4 glass rounded-xl text-[10px] font-black uppercase tracking-widest">Privacy Policy</button>
                <a href="https://chat.whatsapp.com/YOUR_LINK" target="_blank" class="block w-full text-left p-4 glass rounded-xl text-[10px] font-black uppercase text-green-500 tracking-widest">WhatsApp Group 🟢</a>
                <button onclick="changePage('chat'); toggleMenu()" class="w-full text-left p-4 glass rounded-xl text-[10px] font-black uppercase text-blue-400 tracking-widest">Admin Support</button>
                <hr class="border-[var(--border)] my-6">
                <button onclick="logout()" class="w-full text-left p-4 bg-red-600/10 text-red-500 rounded-xl text-[10px] font-black uppercase tracking-widest">Logout</button>
            </nav>
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-8 text-center">
        <div class="w-16 h-16 bg-blue-600 rounded-2xl mb-4 flex items-center justify-center text-3xl font-black text-white shadow-2xl">M</div>
        <h1 onclick="adminTap()" class="text-2xl font-black italic tracking-tighter mb-2 uppercase">MintCrest Gold</h1>
        
        <div id="login-box" class="w-full max-w-[280px] space-y-3">
            <input type="text" id="user-name" placeholder="Username" class="text-center font-bold">
            <input type="password" id="user-pass" placeholder="Password" class="text-center font-bold">
            <button onclick="login()" class="w-full p-4 btn-blue uppercase text-[10px] tracking-widest">Authorize Access</button>
            <p onclick="showForgotMsg()" class="text-[9px] text-blue-400 font-bold uppercase mt-4 cursor-pointer italic">Forgot Password?</p>
        </div>

        <div id="forgot-msg" class="hidden w-full max-w-[280px] p-6 glass rounded-2xl">
            <p class="text-[9px] font-bold text-yellow-500 uppercase leading-relaxed mb-4">Please contact Administration to reset or recover your password.</p>
            <button onclick="hideForgotMsg()" class="text-[8px] opacity-40 font-bold uppercase">Back to Login</button>
        </div>
    </section>

    <header class="p-4 flex justify-between items-center sticky top-0 bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)] z-[5000]">
        <div class="flex items-center gap-3">
            <button onclick="toggleMenu()" class="text-xl">☰</button>
            <span class="font-black text-sm italic uppercase tracking-tighter">MintCrest</span>
        </div>
        <div id="v-bal-head" class="text-[10px] font-black bg-blue-600/20 text-blue-400 px-3 py-1 rounded-full uppercase italic">Rs 0</div>
    </header>

    <main id="app-ui" class="hidden p-4 space-y-6">
        
        <div id="p-home" class="page active-page">
            <div class="p-8 rounded-[2.5rem] bg-gradient-to-br from-blue-700 to-blue-900 text-white shadow-xl mb-6 relative overflow-hidden">
                <p class="text-[10px] font-black uppercase opacity-60">Global Balance</p>
                <h2 class="text-4xl font-black tracking-tighter mt-1" id="v-bal">₨ 0.00</h2>
                <div class="mt-6 flex justify-between items-center bg-black/20 p-4 rounded-2xl">
                    <span class="text-[8px] font-bold uppercase italic">Profit Node Active</span>
                    <button onclick="requestAction('Daily Profit Request')" class="bg-white text-blue-800 px-5 py-2 rounded-xl text-[9px] font-black uppercase shadow-md active:scale-90">Daily Yield 💰</button>
                </div>
            </div>

            <div class="glass p-5 rounded-[2rem] mb-6 border-l-4 border-purple-500">
                <p class="text-[9px] font-black uppercase text-purple-400 mb-2">Referral Program</p>
                <div class="flex gap-2">
                    <input type="text" id="ref-link" readonly class="flex-1 bg-black/40 text-[8px] p-3 border-dashed border-purple-500/30 font-bold">
                    <button onclick="copyRef()" class="bg-purple-600 px-4 py-2 rounded-xl text-[8px] font-black uppercase">Copy</button>
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

        <div id="p-wallet" class="page">
            <h3 class="text-[10px] font-black uppercase opacity-40 mb-6 text-center italic tracking-widest">Account Funding</h3>
            <div id="accounts-list" class="space-y-3 mb-6">
                </div>
            <div class="glass p-6 rounded-[2.5rem] space-y-4">
                <input type="number" id="dep-amt" placeholder="Amount (₨)">
                <input type="text" id="dep-tid" placeholder="Transaction ID (TID)">
                <div class="relative">
                    <input type="file" id="dep-file" accept="image/*" class="hidden" onchange="previewImg(this)">
                    <label for="dep-file" class="w-full p-4 border-2 border-dashed border-[var(--border)] rounded-xl flex flex-col items-center justify-center gap-1 cursor-pointer">
                        <span id="upload-icon" class="text-xl">📸</span>
                        <span id="file-label" class="text-[8px] font-black uppercase opacity-50">Upload Slip</span>
                    </label>
                    <img id="img-preview" class="hidden w-full h-32 object-cover rounded-xl mt-2 border border-blue-500/30">
                </div>
                <button id="sub-dep-btn" onclick="submitDeposit()" class="w-full p-4 btn-blue uppercase text-[10px]">Submit Deposit Proof</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <h3 class="text-[10px] font-black uppercase opacity-40 mb-6 text-center italic tracking-widest">Withdraw Funds</h3>
            <div class="grid grid-cols-2 gap-3 mb-6">
                <div onclick="selectBank('JazzCash')" id="b-JazzCash" class="bank-card glass p-4 rounded-2xl flex flex-col items-center gap-2">
                    <span class="text-2xl">🔴</span><span class="text-[8px] font-black uppercase">JazzCash</span>
                </div>
                <div onclick="selectBank('EasyPaisa')" id="b-EasyPaisa" class="bank-card glass p-4 rounded-2xl flex flex-col items-center gap-2">
                    <span class="text-2xl">🟢</span><span class="text-[8px] font-black uppercase">EasyPaisa</span>
                </div>
            </div>
            <div class="glass p-6 rounded-[2.5rem] space-y-4">
                <input type="number" id="w-amt" placeholder="Amount (₨)">
                <input type="text" id="w-acc" placeholder="Account Number">
                <input type="text" id="w-name" placeholder="Account Name">
                <button onclick="submitWithdraw()" class="w-full p-4 btn-blue uppercase text-[10px]">Confirm Withdrawal</button>
            </div>
        </div>

        <div id="p-history" class="page">
            <h3 class="text-[10px] font-black uppercase opacity-40 mb-4 italic tracking-widest">Transaction History</h3>
            <div id="history-list" class="space-y-3"></div>
        </div>

        <div id="p-chat" class="page">
            <div class="glass rounded-[2rem] flex flex-col h-[60vh] overflow-hidden">
                <div id="sup-list" class="flex-1 overflow-y-auto p-4 space-y-3"></div>
                <div class="p-4 border-t border-[var(--border)] flex gap-2">
                    <input type="text" id="sup-msg" placeholder="Message Admin..." class="flex-1">
                    <button onclick="sendMsg()" class="btn-blue px-6 font-black text-[10px]">SEND</button>
                </div>
            </div>
        </div>

    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[var(--bg)] z-[100000] hidden overflow-y-auto p-6">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Command Center</h2>
            <button onclick="closeAdmin()" class="bg-red-500 text-white px-5 py-2 rounded-xl text-[10px] font-black">CLOSE</button>
        </div>
        
        <div class="glass p-6 rounded-3xl mb-6 border-l-4 border-red-600 flex justify-between items-center">
            <span class="text-[10px] font-black uppercase">Maintenance Mode</span>
            <button id="m-btn" onclick="toggleMaintenance()" class="px-6 py-2 rounded-full text-[9px] font-black bg-gray-700">OFF</button>
        </div>

        <div class="glass p-6 rounded-3xl mb-6 border-l-4 border-green-600 space-y-3">
            <h4 class="text-[9px] font-black uppercase">Update Payment Numbers</h4>
            <input type="text" id="set-j-num" placeholder="JazzCash Number" class="mb-2">
            <input type="text" id="set-e-num" placeholder="EasyPaisa Number" class="mb-2">
            <button onclick="updateAccounts()" class="w-full bg-blue-600 p-3 rounded-xl text-[9px] font-black">UPDATE NUMBERS</button>
        </div>

        <h3 class="text-[10px] font-black uppercase mb-4 opacity-40 italic">Pending Actions</h3>
        <div id="adm-list" class="space-y-4 pb-20"></div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-4 flex justify-around items-center rounded-t-[2.5rem] z-[4000]">
        <button onclick="changePage('home')" id="n-home" class="nav-active flex flex-col items-center gap-1 px-4 py-2 rounded-2xl">🏠<span class="text-[7px] font-black">HOME</span></button>
        <button onclick="changePage('history')" id="n-history" class="opacity-40 flex flex-col items-center gap-1 px-4 py-2 rounded-2xl">📜<span class="text-[7px] font-black">HISTORY</span></button>
        <button onclick="changePage('chat')" id="n-chat" class="opacity-40 flex flex-col items-center gap-1 px-4 py-2 rounded-2xl">💬<span class="text-[7px] font-black">HELP</span></button>
    </nav>

    <script>
        // --- FIREBASE CONFIG ---
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.appspot.com", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let user = null; let tapCount = 0; let selectedBank = ""; let maintenanceActive = false;

        // --- AUTH & CORE ---
        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase();
            const p = document.getElementById('user-pass').value.trim();
            if(!n || !p) return alert("Fill all details! 😘");

            const urlParams = new URLSearchParams(window.location.search);
            const invitedBy = urlParams.get('ref') || "Direct";

            const ref = db.collection("users").doc(n);
            const d = await ref.get();
            if(!d.exists) {
                await ref.set({ name: n, password: p, balance: 0, invitedBy: invitedBy, time: Date.now() });
                alert("Account Created! 😘");
            } else if(d.data().password !== p) return alert("Incorrect Password! 😘");

            localStorage.setItem('mc_user', n);
            document.getElementById('auth-ui').style.display='none'; 
            document.getElementById('app-ui').classList.remove('hidden'); 
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('ref-link').value = window.location.origin + window.location.pathname + "?ref=" + n;
            sync(n);
        }

        function sync(n) {
            db.collection("users").doc(n).onSnapshot(d => { 
                user = d.data(); 
                document.getElementById('v-bal').innerText = "₨ "+(user.balance || 0).toLocaleString(); 
                document.getElementById('v-bal-head').innerText = "₨ "+(user.balance || 0).toLocaleString(); 
            });
            // History Sync
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").limit(15).onSnapshot(s => {
                const list = document.getElementById('history-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const x = doc.data();
                    list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center text-[10px] font-black border-l-4 ${x.status==='approved'?'border-green-500':'border-yellow-500'}">
                        <div><p class="uppercase">${x.type}</p><p class="text-[8px] opacity-40">${new Date(x.time).toLocaleDateString()}</p></div>
                        <div class="text-right"><p>₨ ${x.amount||0}</p><p class="text-[8px] uppercase">${x.status}</p></div>
                    </div>`;
                });
            });
            // Support Sync
            db.collection("support").where("user", "==", n).orderBy("time", "asc").onSnapshot(s => {
                const list = document.getElementById('sup-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const m = doc.data(); const isAdm = m.from === 'admin';
                    list.innerHTML += `<div class="flex ${isAdm?'justify-start':'justify-end'}"><div class="${isAdm?'bg-gray-800':'bg-blue-600'} p-3 rounded-2xl max-w-[85%] text-[10px] font-bold">${m.message}</div></div>`;
                });
                list.scrollTo(0, list.scrollHeight);
            });
        }

        // --- DEPOSIT & WITHDRAW ---
        function previewImg(i) {
            if (i.files && i.files[0]) {
                const r = new FileReader();
                r.onload = e => { document.getElementById('img-preview').src = e.target.result; document.getElementById('img-preview').classList.remove('hidden'); };
                r.readAsDataURL(i.files[0]);
            }
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amt').value;
            const t = document.getElementById('dep-tid').value;
            const f = document.getElementById('dep-file').files[0];
            if(!a || !t || !f) return alert("Fill all details sweetie! 😘");
            
            const r = new FileReader(); r.readAsDataURL(f);
            r.onload = async () => {
                await db.collection("requests").add({ user: user.name, invitedBy: user.invitedBy||"None", amount: parseInt(a), tid: t, proof: r.result, type: "deposit", status: "pending", time: Date.now() });
                alert("Deposit Request Sent! 😘"); location.reload();
            };
        }

        function selectBank(n) { selectedBank = n; document.querySelectorAll('.bank-card').forEach(c=>c.classList.remove('bank-selected')); document.getElementById('b-'+n).classList.add('bank-selected'); }
        async function submitWithdraw() {
            const a = parseInt(document.getElementById('w-amt').value);
            const ac = document.getElementById('w-acc').value;
            if(!selectedBank || !a || !ac) return alert("Fill all details! 😘");
            if(a > user.balance) return alert("Low balance! 😘");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            await db.collection("requests").add({ user: user.name, amount: a, method: selectedBank, account: ac, type: "withdrawal", status: "pending", time: Date.now() });
            alert("Withdrawal Sent! 😘"); location.reload();
        }

        // --- ADMIN & SYSTEM ---
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Key:")==="mint786") { localStorage.setItem('is_admin','true'); document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        
        async function toggleMaintenance() {
            const s = !maintenanceActive;
            await db.collection("settings").doc("system").set({ maintenance: s }, { merge: true });
        }

        function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const list = document.getElementById('adm-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const x = doc.data();
                    list.innerHTML += `<div class="glass p-5 rounded-3xl border-l-4 border-blue-500">
                        <p class="text-[10px] font-black uppercase italic">${x.user} | ${x.type}</p>
                        ${x.invitedBy ? `<p class="text-[7px] text-purple-400 font-bold uppercase mt-1">Invited By: ${x.invitedBy}</p>` : ''}
                        ${x.proof ? `<img src="${x.proof}" class="w-full h-32 object-cover rounded-xl mt-3">` : ''}
                        <div class="flex gap-2 mt-4">
                            <button onclick="approveReq('${doc.id}','${x.user}', ${x.amount})" class="flex-1 bg-blue-600 p-3 rounded-xl text-[8px] font-black uppercase">Approve</button>
                            <button onclick="rejectReq('${doc.id}')" class="flex-1 bg-red-600 p-3 rounded-xl text-[8px] font-black uppercase">Reject</button>
                        </div>
                    </div>`;
                });
            });
        }

        async function approveReq(id, u, a) { await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) }); await db.collection("requests").doc(id).update({ status: 'approved' }); }
        async function rejectReq(id) { await db.collection("requests").doc(id).update({ status: 'rejected' }); }

        // --- UTILS ---
        function toggleMenu() { document.getElementById('side-menu').classList.toggle('menu-open'); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.add('opacity-40')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('nav-active')); if(document.getElementById('n-'+p)){ document.getElementById('n-'+p).classList.remove('opacity-40'); document.getElementById('n-'+p).classList.add('nav-active'); } }
        async function requestAction(t) { await db.collection("requests").add({ user: user.name, type: t, status: "pending", time: Date.now() }); alert("Sent to Admin! 😘"); }
        function copyRef() { const c = document.getElementById('ref-link'); c.select(); navigator.clipboard.writeText(c.value); alert("Referral Link Copied! 😘"); }
        function logout() { localStorage.removeItem('mc_user'); localStorage.removeItem('is_admin'); location.reload(); }
        function showForgotMsg() { document.getElementById('login-box').classList.add('hidden'); document.getElementById('forgot-msg').classList.remove('hidden'); }
        function hideForgotMsg() { document.getElementById('login-box').classList.remove('hidden'); document.getElementById('forgot-msg').classList.add('hidden'); }

        // Settings Sync
        db.collection("settings").doc("system").onSnapshot(doc => {
            if(doc.exists) {
                maintenanceActive = doc.data().maintenance;
                if(maintenanceActive && localStorage.getItem('is_admin') !== 'true') document.getElementById('maintenance-screen').classList.remove('hidden');
                else document.getElementById('maintenance-screen').classList.add('hidden');
                const mBtn = document.getElementById('m-btn');
                if(mBtn) { mBtn.innerText = maintenanceActive ? "ON" : "OFF"; mBtn.className = maintenanceActive ? "px-6 py-2 rounded-full text-[9px] font-black bg-red-600" : "px-6 py-2 rounded-full text-[9px] font-black bg-gray-700"; }
            }
        });
        
        window.onload = () => { if(localStorage.getItem('mc_user')) { document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden'); sync(localStorage.getItem('mc_user')); } }
    </script>
</body>
</html>
