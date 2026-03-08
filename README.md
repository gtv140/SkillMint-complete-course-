<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>MintCrest Gold | Global Node</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        .light { --bg: #f8fafc; --card: #ffffff; --text: #0f172a; --border: #e2e8f0; --accent: #2563eb; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 110px; overflow-x: hidden; transition: 0.3s; }
        .glass { background: var(--card); border: 1px solid var(--border); }
        .page { display: none; animation: slideUp 0.4s ease forwards; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; border-radius: 18px; font-weight: 800; transition: 0.2s; }
        input, textarea { background: var(--card); border: 1px solid var(--border); color: var(--text); padding: 14px; border-radius: 16px; width: 100%; outline: none; font-size: 13px; font-weight: 600; }
        .nav-active { color: var(--accent); opacity: 1 !important; transform: scale(1.1); }
        .animate-marquee { display: inline-block; animation: marquee 15s linear infinite; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        ::-webkit-scrollbar { display: none; }
    </style>
</head>
<body>

    <header class="p-4 flex justify-between items-center sticky top-0 bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)] z-[5000]">
        <div class="flex items-center gap-2">
            <div onclick="adminTap()" class="w-10 h-10 bg-blue-600 rounded-2xl flex items-center justify-center font-black text-white italic shadow-lg cursor-pointer">M</div>
            <div class="leading-none">
                <p class="font-black text-[10px] uppercase tracking-tighter">MintCrest</p>
                <p class="text-blue-500 text-xs font-black italic">GOLD NODE</p>
            </div>
        </div>
        <div class="flex items-center gap-3">
            <button onclick="toggleTheme()" class="w-9 h-9 glass rounded-xl flex items-center justify-center text-sm">🌓</button>
            <div id="top-bal" class="text-[11px] font-black bg-blue-600/10 text-blue-500 px-4 py-2 rounded-xl border border-blue-500/20 italic">₨ 0</div>
        </div>
    </header>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-8">
        <div class="w-24 h-24 bg-blue-600 rounded-[2.5rem] mb-10 flex items-center justify-center text-5xl font-black text-white italic shadow-2xl">M</div>
        <div class="w-full max-w-[320px] space-y-4">
            <input type="text" id="user-name" placeholder="Username" class="text-center uppercase tracking-widest">
            <input type="password" id="user-pass" placeholder="Password" class="text-center">
            <button onclick="login()" class="w-full p-5 btn-prime uppercase text-[11px] tracking-[0.2em] shadow-xl">Login to Node</button>
        </div>
    </section>

    <main id="app-ui" class="hidden p-4 space-y-6">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="bg-blue-600/5 border border-blue-500/10 p-2 rounded-xl overflow-hidden">
                <p class="animate-marquee text-[9px] font-black uppercase text-blue-400 italic">Welcome to MintCrest Global. Secure your nodes for maximum daily yields.</p>
            </div>

            <div class="p-8 rounded-[3.5rem] bg-gradient-to-br from-blue-600 to-blue-800 text-white shadow-2xl">
                <p class="text-[10px] font-black uppercase opacity-60 italic">Total Balance</p>
                <h2 class="text-4xl font-black tracking-tighter mt-1" id="v-bal">₨ 0.00</h2>
            </div>

            <div class="flex gap-2">
                <a href="https://chat.whatsapp.com/YOUR_LINK" class="flex-1 glass p-4 rounded-2xl flex items-center justify-center gap-2 border-l-4 border-green-500">
                    <span class="text-xl">💬</span><span class="text-[9px] font-black uppercase">Official Group</span>
                </a>
                <button onclick="changePage('support')" class="flex-1 glass p-4 rounded-2xl flex items-center justify-center gap-2 border-l-4 border-blue-500">
                    <span class="text-xl">🎧</span><span class="text-[9px] font-black uppercase">Help Desk</span>
                </button>
            </div>

            <div id="plans-grid" class="grid grid-cols-1 gap-5 pb-10"></div>
        </div>

        <div id="p-support" class="page space-y-6">
            <div class="text-center py-2 text-blue-500 text-2xl font-black uppercase italic">Live Support</div>
            <div id="chat-box" class="glass p-4 rounded-[2rem] h-[350px] overflow-y-auto space-y-3"></div>
            <div class="glass p-4 rounded-[2.5rem] flex gap-2">
                <input id="support-msg" placeholder="Describe your issue..." class="flex-1 !border-none">
                <button onclick="sendSupportMsg()" class="btn-prime px-6 py-3 text-[10px] uppercase">Send</button>
            </div>
        </div>

        <div id="p-wallet" class="page space-y-6">
            <div class="glass p-8 rounded-[3rem] space-y-5">
                <p class="text-center font-black text-blue-500 uppercase text-xs">Deposit Funds</p>
                <input type="number" id="dep-amt" placeholder="Amount (₨)">
                <input type="text" id="dep-tid" placeholder="Transaction ID (TID)">
                <input type="file" id="dep-img" accept="image/*" class="hidden" onchange="updateFileName(this)">
                <label for="dep-img" class="w-full flex items-center justify-center gap-3 p-5 border-2 border-dashed border-blue-500/30 rounded-2xl bg-blue-500/5 cursor-pointer">
                    <span id="file-name-label" class="text-[10px] font-black uppercase text-blue-400">📸 Upload Proof</span>
                </label>
                <button id="dep-btn" onclick="submitDepWithImage()" class="w-full p-5 btn-prime uppercase italic">Submit Proof</button>
            </div>
        </div>

        <div id="p-withdraw" class="page space-y-6">
            <div class="glass p-8 rounded-[3rem] space-y-5">
                <input type="number" id="w-amt" placeholder="Withdrawal Amount">
                <input type="text" id="w-acc" placeholder="Account Number & Bank">
                <button onclick="submitWith()" class="w-full p-5 btn-prime uppercase italic">Request Payout</button>
            </div>
        </div>

        <div id="p-about" class="page text-center p-10">
            <button onclick="logout()" class="w-full p-6 bg-red-600/10 text-red-500 border border-red-500/20 rounded-[2.5rem] font-black uppercase italic tracking-widest">Logout Session</button>
        </div>
    </main>

    <div id="admin-ui" class="fixed inset-0 bg-black z-[100000] hidden overflow-y-auto p-4">
        <div class="flex justify-between items-center mb-8 sticky top-0 bg-black py-4 z-50 border-b border-white/5">
            <h2 class="text-2xl font-black text-blue-500 italic uppercase">Master God Mode 👑</h2>
            <button onclick="closeAdmin()" class="bg-red-600 px-4 py-2 rounded-xl text-[10px] font-black uppercase">Close</button>
        </div>

        <div class="grid grid-cols-2 gap-4 mb-8">
            <div class="glass p-6 rounded-[2rem] text-center"><span>👥 Users</span><p id="adm-users-total" class="text-xl font-black">0</p></div>
            <div class="glass p-6 rounded-[2rem] text-center text-green-500"><span>💰 Wealth</span><p id="adm-cash-total" class="text-xl font-black">0</p></div>
            <div class="glass p-6 rounded-[2rem] text-center text-yellow-500"><span>⚡ Requests</span><p id="adm-req-count" class="text-xl font-black">0</p></div>
            <div class="glass p-6 rounded-[2rem] text-center text-purple-500"><span>✉️ Inbox</span><p id="adm-msg-count" class="text-xl font-black">0</p></div>
        </div>

        <div class="glass p-6 rounded-[2.5rem] mb-10 border-l-4 border-blue-500">
            <h4 class="text-[10px] font-black uppercase mb-4">Add New Node Plan</h4>
            <div class="space-y-2">
                <input id="new-plan-name" placeholder="Plan Name" class="!p-3">
                <input id="new-plan-price" type="number" placeholder="Price" class="!p-3">
                <input id="new-plan-profit" type="number" placeholder="Daily Profit %" class="!p-3">
                <button onclick="addNewPlan()" class="w-full bg-blue-600 p-3 rounded-xl text-[10px] font-black uppercase">Create Node</button>
            </div>
        </div>

        <div class="mb-10"><h4 class="text-[10px] font-black uppercase text-purple-400 mb-4">Customer Support Replies</h4><div id="adm-messages-list" class="space-y-4"></div></div>
        <div class="mb-10"><h4 class="text-[10px] font-black uppercase text-yellow-400 mb-4">Pending Transactions</h4><div id="adm-requests-list" class="space-y-4"></div></div>
        
        <div class="glass rounded-[3rem] overflow-hidden mb-20">
            <div class="p-6 bg-white/5 text-[10px] font-black uppercase">User Identity Database</div>
            <div class="overflow-x-auto"><table class="w-full text-left text-[11px] font-bold"><tbody id="god-users-list"></tbody></table></div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-white/5 p-5 flex justify-around items-center rounded-t-[3rem] z-[4000]">
        <button onclick="changePage('home')" id="n-home" class="nav-active flex flex-col items-center gap-1 opacity-40">🏠<span class="text-[8px] font-black">HOME</span></button>
        <button onclick="changePage('wallet')" id="n-wallet" class="flex flex-col items-center gap-1 opacity-40">📥<span class="text-[8px] font-black">DEPOSIT</span></button>
        <button onclick="changePage('withdraw')" id="n-withdraw" class="flex flex-col items-center gap-1 opacity-40">📤<span class="text-[8px] font-black">PAYOUT</span></button>
        <button onclick="changePage('support')" id="n-support" class="flex flex-col items-center gap-1 opacity-40">🎧<span class="text-[8px] font-black">SUPPORT</span></button>
    </nav>

    <script>
        // FIREBASE
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.appspot.com", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let user = null; let tapCount = 0;

        // AUTH
        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase();
            const p = document.getElementById('user-pass').value.trim();
            if(!n || !p) return alert("Credentials required.");
            const ref = db.collection("users").doc(n);
            const d = await ref.get();
            if(!d.exists) await ref.set({ name: n, password: p, balance: 0, time: Date.now() });
            else if(d.data().password !== p) return alert("Access denied.");
            localStorage.setItem('mc_user', n); enterApp(n);
        }

        function enterApp(n) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            sync(n);
        }

        function sync(n) {
            db.collection("users").doc(n).onSnapshot(d => {
                if(!d.exists) return;
                user = d.data();
                if(user.banned) { alert("Banned."); location.reload(); }
                document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
            });

            db.collection("support").where("user", "==", n).orderBy("time", "asc").onSnapshot(s => {
                const box = document.getElementById('chat-box'); box.innerHTML = '';
                s.forEach(d => { const m = d.data();
                    box.innerHTML += `<div class="flex ${m.sender==='user'?'justify-end':'justify-start'}">
                    <div class="${m.sender==='user'?'bg-blue-600 text-white':'bg-white/10 text-gray-200'} p-3 rounded-2xl max-w-[80%] text-[11px] font-bold shadow-sm">${m.message}</div></div>`;
                });
                box.scrollTop = box.scrollHeight;
            });
        }

        // HELP DESK
        async function sendSupportMsg() {
            const m = document.getElementById('support-msg').value.trim();
            if(!m) return;
            await db.collection("support").add({ user: user.name, message: m, time: Date.now(), sender: 'user', status: 'unread' });
            document.getElementById('support-msg').value = "";
        }

        // ADMINISTRATION
        function adminTap() { tapCount++; if(tapCount>=5) { if(prompt("God Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        function syncAdm() {
            db.collection("users").onSnapshot(s => {
                document.getElementById('adm-users-total').innerText = s.size;
                const l = document.getElementById('god-users-list'); l.innerHTML = ''; let tw = 0;
                s.forEach(d => { const u = d.data(); tw += (u.balance||0);
                    l.innerHTML += `<tr class="border-b border-white/5"><td class="p-5">@${u.name}</td><td class="p-5">${u.password}</td><td class="p-5 text-right"><button onclick="godAction('${u.name}')" class="text-blue-500">EDIT</button></td></tr>`;
                });
                document.getElementById('adm-cash-total').innerText = "₨ " + tw.toLocaleString();
            });

            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                document.getElementById('adm-req-count').innerText = s.size;
                const q = document.getElementById('adm-requests-list'); q.innerHTML = '';
                s.forEach(d => { const r = d.data();
                    q.innerHTML += `<div class="glass p-6 rounded-[2rem] border-l-8 border-blue-500 flex justify-between items-center">
                    <div><p class="text-[10px]">@${r.user}</p><p class="text-xl font-black italic">₨ ${r.amount||0}</p>
                    ${r.imgUrl?`<a href="${r.imgUrl}" target="_blank" class="text-blue-400 text-[8px] underline">VIEW PROOF</a>`:''}</div>
                    <button onclick="appvReq('${d.id}','${r.user}',${r.amount||0},'${r.type}')" class="bg-blue-600 px-6 py-3 rounded-2xl text-[10px] font-black uppercase">Approve</button></div>`;
                });
            });

            db.collection("support").where("status", "==", "unread").onSnapshot(s => {
                document.getElementById('adm-msg-count').innerText = s.size;
                const mList = document.getElementById('adm-messages-list'); mList.innerHTML = '';
                s.forEach(d => { const m = d.data();
                    mList.innerHTML += `<div class="glass p-6 rounded-[2rem] border-l-8 border-purple-500">
                    <p class="text-[9px] font-black uppercase opacity-40">@${m.user}</p><p class="text-[12px] font-bold mt-1">${m.message}</p>
                    <button onclick="replyMsg('${m.user}', '${d.id}')" class="mt-4 bg-purple-600 px-4 py-2 rounded-lg text-[9px] font-black uppercase">Reply</button></div>`;
                });
            });
        }

        async function addNewPlan() {
            const n = document.getElementById('new-plan-name').value;
            const p = document.getElementById('new-plan-price').value;
            const pr = document.getElementById('new-plan-profit').value;
            if(!n || !p || !pr) return;
            await db.collection("plans").add({ name: n, price: parseInt(p), profit: pr });
            alert("Plan Created!");
        }

        async function replyMsg(u, id) {
            const r = prompt("Reply Message:"); if(!r) return;
            await db.collection("support").add({ user: u, message: r, time: Date.now(), sender: 'admin' });
            await db.collection("support").doc(id).update({ status: 'read' });
        }

        function godAction(n) { let a = prompt("A: Add Amt, B: Ban"); let ref = db.collection("users").doc(n); if(a.toUpperCase()==='A') ref.update({balance: firebase.firestore.FieldValue.increment(parseInt(prompt("Amount:"))) }); if(a.toUpperCase()==='B') ref.update({banned: true}); }

        async function appvReq(id, u, a, t) {
            if(t !== 'withdrawal') await db.collection("users").doc(u).update({balance: firebase.firestore.FieldValue.increment(a)});
            await db.collection("requests").doc(id).update({status:'approved'}); alert("Confirmed.");
        }

        // UTILS
        function updateFileName(input) { document.getElementById('file-name-label').innerText = "Ready: " + input.files[0].name; }
        async function submitDepWithImage() {
            const amt = document.getElementById('dep-amt').value; const tid = document.getElementById('dep-tid').value; const file = document.getElementById('dep-img').files[0];
            if(!amt || !tid || !file) return alert("Please fill all details.");
            const btn = document.getElementById('dep-btn'); btn.innerText = "Processing..."; btn.disabled = true;
            const reader = new FileReader(); reader.readAsDataURL(file);
            reader.onload = async () => {
                await db.collection("requests").add({ user: user.name, type: 'deposit', amount: parseInt(amt), tid: tid, imgUrl: reader.result, status: 'pending', time: Date.now() });
                alert("Deposit request submitted."); location.reload();
            };
        }

        function syncPlans() {
            db.collection("plans").onSnapshot(s => {
                const grid = document.getElementById('plans-grid'); grid.innerHTML = '';
                if(s.empty) grid.innerHTML = `<p class="text-center opacity-30 uppercase font-black py-10">No plans available yet.</p>`;
                s.forEach(d => { const p = d.data();
                    grid.innerHTML += `<div class="glass p-8 rounded-[3rem] border-l-8 border-blue-500 animate-slideUp">
                    <div class="flex justify-between"><div><p class="text-[10px] opacity-40 uppercase font-black">${p.name}</p><h3 class="text-2xl font-black text-blue-500 mt-1">₨ ${p.price}</h3></div><div class="text-blue-500 font-black">${p.profit}% Daily</div></div>
                    <button onclick="buyNode(${p.price})" class="w-full mt-6 btn-prime p-4 text-[11px] uppercase italic">Invest in Node</button></div>`;
                });
            });
        }

        async function buyNode(p) { if(user.balance < p) return alert("Insufficient balance."); await db.collection("users").doc(user.name).update({balance: firebase.firestore.FieldValue.increment(-p)}); alert("Investment Active."); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>{b.classList.add('opacity-40'); b.classList.remove('nav-active');}); document.getElementById('n-'+p).classList.remove('opacity-40'); document.getElementById('n-'+p).classList.add('nav-active'); window.scrollTo(0,0); }
        function toggleTheme() { document.getElementById('main-html').classList.toggle('light'); document.getElementById('main-html').classList.toggle('dark'); }
        async function submitWith() { let a = document.getElementById('w-amt').value; if(user.balance < a) return alert("Low balance."); await db.collection("users").doc(user.name).update({balance:firebase.firestore.FieldValue.increment(-parseInt(a))}); await db.collection("requests").add({user:user.name, type:'withdrawal', amount:parseInt(a), status:'pending', time:Date.now()}); alert("Payout request sent."); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }

        window.onload = () => { if(localStorage.getItem('mc_user')) enterApp(localStorage.getItem('mc_user')); syncPlans(); }
    </script>
</body>
</html>
