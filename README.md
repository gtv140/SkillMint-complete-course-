<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Infinity | Global Trusted Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #00040a; color: white; margin: 0; overflow: hidden; }
        .page-content { height: 100vh; overflow-y: scroll; padding-bottom: 180px; scroll-behavior: smooth; }
        ::-webkit-scrollbar { display: none; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); }
        .grad-main { background: linear-gradient(135deg, #0052d4 0%, #4364f7 50%, #6fb1fc 100%); }
        .grad-gold { background: linear-gradient(135deg, #bf953f, #fcf6ba, #b38728, #fbf5b7, #aa771c); }
        .page { display: none; animation: fadeIn 0.4s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: scale(0.95); } to { opacity: 1; transform: scale(1); } }
        .nav-btn { filter: grayscale(1); opacity: 0.4; transition: 0.4s; }
        .active-tab { filter: grayscale(0); opacity: 1; color: #00d4ff; transform: translateY(-5px); }
        .logout-btn { background: linear-gradient(45deg, #ff00cc, #3333ff); box-shadow: 0 0 15px #ff00cc; }
        .trust-badge { border: 1px solid rgba(0, 255, 157, 0.3); background: rgba(0, 255, 157, 0.05); }
    </style>
</head>
<body>

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-[#00040a] flex flex-col items-center justify-center p-8">
        <div class="w-24 h-24 grad-main rounded-[2.5rem] mb-6 flex items-center justify-center shadow-[0_0_50px_rgba(67,100,247,0.3)] animate-bounce">💎</div>
        <h1 onclick="adminTap()" class="text-4xl font-black italic tracking-tighter mb-2">MINTCREST</h1>
        <p class="text-[8px] uppercase tracking-[0.6em] text-cyan-400 mb-10 font-bold">The Infinity Investment Node</p>
        <div class="glass p-8 rounded-[3.5rem] w-full max-w-xs text-center border-t border-white/20">
            <input type="text" id="user-name" placeholder="Enter Full Name" class="w-full bg-white/5 p-4 rounded-2xl mb-4 text-center font-bold outline-none border border-white/10">
            <input type="text" id="ref-by" placeholder="Referral Code (Optional)" class="w-full bg-white/5 p-3 rounded-xl mb-6 text-[10px] text-center outline-none border border-white/5">
            <button onclick="login()" class="w-full grad-main py-5 rounded-2xl font-black uppercase text-xs shadow-xl active:scale-95 transition-all">Access My Vault</button>
        </div>
    </section>

    <main id="app-ui" class="hidden">
        
        <div id="p-home" class="page active-page page-content p-6">
            <div class="flex justify-between items-center mt-4 mb-8">
                <div>
                    <h2 class="text-2xl font-black" id="v-user">User</h2>
                    <div class="flex items-center gap-1"><span class="w-2 h-2 bg-green-500 rounded-full animate-pulse"></span><p class="text-[9px] text-green-400 font-bold uppercase">Node Verified</p></div>
                </div>
                <button onclick="logout()" class="logout-btn w-12 h-12 rounded-2xl flex items-center justify-center text-xl">🚀</button>
            </div>

            <div class="grad-main p-8 rounded-[3.5rem] mb-8 shadow-[0_20px_40px_rgba(0,82,212,0.3)] relative overflow-hidden">
                <div class="absolute -top-10 -right-10 w-32 h-32 bg-white/10 rounded-full blur-3xl"></div>
                <p class="text-[10px] font-black opacity-80 uppercase italic">Current Liquidity</p>
                <h2 class="text-5xl font-black my-2" id="v-bal">₨ 0</h2>
                <div class="mt-8 flex justify-between items-center border-t border-white/10 pt-4">
                    <div><p class="text-[8px] opacity-60 uppercase">Total Profit</p><p class="text-lg font-black" id="v-profit">₨ 0</p></div>
                    <button onclick="copyRef()" class="bg-black/30 px-5 py-2 rounded-xl text-[9px] font-black uppercase border border-white/10">🔗 Invite Link</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="claimDaily()" class="glass p-6 rounded-[2.5rem] border-b-4 border-yellow-500 text-center">
                    <span class="text-3xl block mb-2">🎁</span><span class="text-[10px] font-black uppercase">Daily Bonus</span>
                </button>
                <button onclick="changePage('spin')" class="glass p-6 rounded-[2.5rem] border-b-4 border-cyan-500 text-center">
                    <span class="text-3xl block mb-2">🎡</span><span class="text-[10px] font-black uppercase">Lucky Spin</span>
                </button>
            </div>

            <div class="trust-badge p-5 rounded-[2.5rem] mb-8 flex items-center gap-4">
                <div class="text-2xl">🛡️</div>
                <div>
                    <h4 class="text-[10px] font-black uppercase text-green-400">MintCrest Security</h4>
                    <p class="text-[8px] opacity-60 italic">Your funds are protected by 256-bit encryption & capital insurance.</p>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass py-8 rounded-[2.5rem] font-black text-xs uppercase text-cyan-400">Add Capital</button>
                <button onclick="changePage('withdraw')" class="glass py-8 rounded-[2.5rem] font-black text-xs uppercase text-rose-500">Outflow</button>
            </div>
        </div>

        <div id="p-invest" class="page page-content p-6">
            <h3 class="text-center font-black text-xl italic mb-8 uppercase text-blue-400">Investment Fleets</h3>
            <div id="plans-list" class="space-y-4"></div>
        </div>

        <div id="p-wallet" class="page page-content p-6">
            <h3 class="text-center font-black text-xl italic mb-8 uppercase">Capital Inflow</h3>
            <div class="space-y-3 mb-8">
                <div onclick="selM('JazzCash','03705519562')" class="glass p-5 rounded-2xl flex justify-between items-center border-l-4 border-yellow-500"><span>JazzCash</span><span class="text-xs font-bold">0370...562</span></div>
                <div onclick="selM('Easypaisa','03379827882')" class="glass p-5 rounded-2xl flex justify-between items-center border-l-4 border-green-500"><span>Easypaisa</span><span class="text-xs font-bold">0337...882</span></div>
                <div onclick="selM('Binance','TRC20-NODE-LINK')" class="glass p-5 rounded-2xl flex justify-between items-center border-l-4 border-orange-500"><span>Binance</span><span class="text-xs font-bold">USDT-TRC20</span></div>
            </div>
            <div id="dep-box" class="hidden glass p-8 rounded-[3rem] border-2 border-cyan-500/20">
                <p id="m-info" class="text-[10px] font-black text-cyan-400 mb-6 text-center uppercase tracking-tighter"></p>
                <input type="number" id="d-amt" placeholder="Deposit Amount" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-bold border border-white/10 outline-none">
                <input type="text" id="d-tid" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-black border border-white/10 outline-none">
                <p class="text-[8px] mb-2 opacity-50 text-center uppercase">Upload Screenshot Proof</p>
                <input type="file" id="d-file" class="w-full text-[10px] mb-6">
                <button onclick="subDep()" class="w-full grad-main py-5 rounded-2xl font-black text-xs uppercase">Request Verification</button>
            </div>
        </div>

        <div id="p-withdraw" class="page page-content p-6">
            <div class="glass p-8 rounded-[3.5rem] border-t-8 border-rose-600">
                <h3 class="text-2xl font-black text-rose-500 mb-6 uppercase text-center">Withdraw</h3>
                <input type="text" id="w-user-auto" readonly class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-black opacity-30 text-xs">
                <select id="w-method" class="w-full bg-white/10 p-4 rounded-xl mb-4 text-xs font-black outline-none border border-white/10 appearance-none text-center">
                    <option value="Easypaisa">Easypaisa</option>
                    <option value="JazzCash">JazzCash</option>
                    <option value="Binance">Binance (USDT)</option>
                    <option value="Bank">Local Bank</option>
                </select>
                <input type="number" id="w-amt" placeholder="Amount (Min 100)" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-bold border border-white/10 outline-none">
                <input type="text" id="w-acc" placeholder="Acc Number / Wallet Addr" class="w-full bg-white/5 p-4 rounded-xl mb-8 text-center text-xs font-bold border border-white/10 outline-none">
                <button onclick="subWd()" class="w-full bg-rose-600 py-5 rounded-2xl font-black text-xs uppercase shadow-xl">Confirm Payout</button>
            </div>
        </div>

        <div id="p-chat" class="page page-content flex flex-col h-screen">
            <div class="p-6 border-b border-white/10 flex justify-between items-center bg-black/50 backdrop-blur-md">
                <h3 class="font-black text-xl uppercase italic">Help Desk</h3>
                <span class="text-[9px] bg-green-500/20 text-green-500 px-3 py-1 rounded-full font-black">ONLINE</span>
            </div>
            <div id="chat-box" class="flex-1 overflow-y-auto p-6 flex flex-col gap-3"></div>
            <div class="p-4 glass rounded-t-[3rem] border-t border-white/10">
                <div class="flex gap-2">
                    <label class="bg-white/5 p-4 rounded-2xl cursor-pointer">🖼️<input type="file" id="chat-img" class="hidden" accept="image/*" onchange="sendChatImg()"></label>
                    <input type="text" id="chat-msg" placeholder="Message Admin..." class="flex-1 bg-white/5 p-4 rounded-2xl text-xs outline-none">
                    <button onclick="sendChat()" class="grad-main px-6 rounded-2xl font-black">▶</button>
                </div>
            </div>
        </div>

        <div id="p-activity" class="page page-content p-6">
            <div class="flex justify-between items-center mb-8">
                <h3 class="font-black text-xl uppercase">Node History</h3>
                <button onclick="clearHis()" class="text-[8px] font-black text-rose-500 bg-rose-500/10 px-4 py-2 rounded-lg">CLEAR LOGS</button>
            </div>
            <div id="user-history" class="space-y-3"></div>
        </div>

        <div id="p-support" class="page page-content p-6">
            <div class="glass p-8 rounded-[3rem] mb-6">
                <h3 class="font-black text-xl mb-4 text-cyan-400">About MintCrest</h3>
                <p class="text-[10px] opacity-70 leading-relaxed font-medium italic">MintCrest is a global decentralized investment node providing high-frequency trading yields to retail investors. We use AI-driven liquidity protocols to ensure 100% capital safety and daily dividends.</p>
            </div>
            <button onclick="window.open('https://chat.whatsapp.com/YOUR_GROUP')" class="w-full bg-green-600 py-6 rounded-[2.5rem] font-black text-xs uppercase mb-4 flex items-center justify-center gap-3 shadow-[0_10px_30px_rgba(22,163,74,0.3)]">
                <span>💬 WhatsApp Updates</span>
            </button>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#00040a] z-[50000] hidden overflow-y-auto p-8">
        <div class="flex justify-between items-center mb-10 border-b border-white/10 pb-6">
            <h2 class="text-xl font-black text-cyan-400">SUPREME ADMIN</h2>
            <button onclick="closeAdm()" class="bg-rose-600 px-6 py-2 rounded-xl text-[10px] font-black">EXIT</button>
        </div>

        <div class="grid grid-cols-2 gap-4 mb-8">
            <div class="glass p-6 rounded-3xl text-center"><p class="text-[8px] opacity-40">USERS</p><h4 id="adm-total-users" class="text-xl font-black">0</h4></div>
            <div class="glass p-6 rounded-3xl text-center"><p class="text-[8px] opacity-40">PENDING</p><h4 id="adm-live-reqs" class="text-xl font-black text-yellow-500">0</h4></div>
        </div>

        <div class="glass p-6 rounded-3xl mb-8 border border-white/10">
            <p class="text-[10px] font-black text-blue-400 mb-4 uppercase tracking-widest">Broadcast Message</p>
            <input type="text" id="adm-msg" placeholder="Announce something..." class="w-full bg-white/5 p-4 rounded-xl text-xs mb-3 border border-white/10 outline-none">
            <button onclick="sendBC()" class="w-full grad-main py-4 rounded-xl text-[9px] font-black">PUSH BROADCAST</button>
        </div>

        <div class="glass p-6 rounded-3xl mb-10 border-l-4 border-yellow-500">
            <p class="text-[10px] font-black text-yellow-500 mb-4 uppercase">Control Node</p>
            <input type="text" id="adm-u" placeholder="Target Username" class="w-full bg-white/5 p-4 rounded-xl text-xs mb-3 border border-white/10 outline-none">
            <div class="grid grid-cols-2 gap-2 mb-3">
                <button onclick="editU('balance')" class="bg-blue-600 py-3 rounded-xl text-[8px] font-black">SET BAL</button>
                <button onclick="editU('profit')" class="bg-cyan-600 py-3 rounded-xl text-[8px] font-black">SET PROF</button>
            </div>
            <button onclick="blockUser()" class="w-full bg-rose-700 py-3 rounded-xl text-[8px] font-black uppercase">Block User Account</button>
        </div>

        <h3 class="text-xs font-black uppercase mb-6 tracking-widest">Pending Inflow/Outflow ↓</h3>
        <div id="adm-reqs" class="space-y-4"></div>
    </div>

    <nav id="bottom-nav" class="hidden glass p-7 flex justify-around items-center fixed bottom-0 w-full z-[2000] rounded-t-[3.5rem] border-t border-white/10">
        <button onclick="changePage('home')" id="n-home" class="nav-btn text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-btn text-2xl">📈</button>
        <button onclick="changePage('chat')" id="n-chat" class="nav-btn text-2xl">💬</button>
        <button onclick="changePage('activity')" id="n-activity" class="nav-btn text-2xl">📜</button>
        <button onclick="changePage('support')" id="n-support" class="nav-btn text-2xl">ℹ️</button>
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
        let user = null; let tapCount = 0; let curM = '';

        // Auto Login System (Anti-Refresh)
        window.onload = () => {
            const saved = localStorage.getItem('mint_user_v22');
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            renderPlans();
        };

        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase();
            const r = document.getElementById('ref-by').value.trim().toLowerCase();
            if(!n) return;
            const ref = db.collection("users").doc(n); const d = await ref.get();
            if(!d.exists) { await ref.set({ name: n, balance: 0, profit: 0, refBy: r || 'Direct', time: Date.now(), status: 'active' }); }
            user = (await ref.get()).data();
            if(user.status === 'blocked') return alert("This Node is Blocked by Administration!");
            
            localStorage.setItem('mint_user_v22', n);
            document.getElementById('v-user').innerText = n.toUpperCase();
            document.getElementById('w-user-auto').value = "PAYEE: " + n.toUpperCase();
            document.getElementById('auth-ui').style.display='none';
            document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            startSync(n); syncChat(n);
        }

        function logout() { localStorage.removeItem('mint_user_v22'); location.reload(); }

        // 20+5 PLANS SYSTEM
        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            for(let i=1; i<=25; i++) {
                const isSpecial = i > 20;
                const price = i * 1000;
                const daily = (price * (isSpecial ? 0.15 : 0.08)).toFixed(0);
                list.innerHTML += `
                <div class="glass p-6 rounded-[2.5rem] border-l-4 ${isSpecial ? 'border-yellow-500' : 'border-blue-500'} relative overflow-hidden">
                    ${isSpecial ? '<span class="absolute top-2 right-2 text-[7px] font-black bg-yellow-500 text-black px-2 py-1 rounded-full">SPECIAL</span>' : ''}
                    <h4 class="font-black text-xs uppercase mb-1">${isSpecial ? 'Diamond Node P'+(i-20) : 'Fleet Node V'+i}</h4>
                    <p class="text-[8px] opacity-50 mb-4">${isSpecial ? 'Unlimited Cycle' : 'Duration: '+(i+10)+' Days'}</p>
                    <div class="flex justify-between items-center">
                        <div class="text-[10px] font-black">₨ ${price} <span class="text-green-400 block text-[8px]">₨ ${daily}/Day</span></div>
                        <button onclick="buyPlan(${price})" class="bg-white/10 px-6 py-3 rounded-xl text-[9px] font-black uppercase active:scale-90 transition-all">Buy Now</button>
                    </div>
                </div>`;
            }
        }

        async function buyPlan(p) {
            if(user.balance < p) return alert("Low Balance, Sweetie! 😘");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-p) });
            alert("Node Activated Successfully! 🚀");
        }

        // CHAT SYSTEM
        async function sendChat() {
            const m = document.getElementById('chat-msg').value; if(!m) return;
            await db.collection("chats").add({ user: user.name, msg: m, role: 'user', time: Date.now(), type: 'text' });
            document.getElementById('chat-msg').value = '';
        }

        async function sendChatImg() {
            const f = document.getElementById('chat-img').files[0]; if(!f) return;
            const sRef = storage.ref(`chats/${Date.now()}`); await sRef.put(f);
            const url = await sRef.getDownloadURL();
            await db.collection("chats").add({ user: user.name, msg: url, role: 'user', time: Date.now(), type: 'image' });
        }

        function syncChat(n) {
            db.collection("chats").where("user", "==", n).orderBy("time", "asc").onSnapshot(s => {
                const box = document.getElementById('chat-box'); box.innerHTML = '';
                s.forEach(d => {
                    const x = d.data();
                    box.innerHTML += `<div class="p-3 rounded-2xl text-[10px] max-w-[80%] ${x.role==='user'?'bg-blue-600 self-end':'bg-white/10 self-start'}">${x.type==='image'?`<img src="${x.msg}" class="rounded-lg w-full">`:x.msg}</div>`;
                });
                box.scrollTop = box.scrollHeight;
            });
        }

        // DEPOSIT / WITHDRAWAL
        function selM(m, a) { curM = m; document.getElementById('dep-box').classList.remove('hidden'); document.getElementById('m-info').innerText = `TRANSFER TO ${m}: ${a}`; }
        async function subDep() {
            const a = parseInt(document.getElementById('d-amt').value); const t = document.getElementById('d-tid').value; const f = document.getElementById('d-file').files[0];
            if(!a || !t || !f) return alert("Fill all details, Sweetie! 😘");
            const sRef = storage.ref(`proofs/${Date.now()}`); await sRef.put(f);
            const url = await sRef.getDownloadURL();
            await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: url, type: "Deposit", status: "pending", time: Date.now(), refBy: user.refBy });
            alert("Deposit Logged! Admin will verify soon. 😘");
        }

        async function subWd() {
            const a = parseInt(document.getElementById('w-amt').value); const acc = document.getElementById('w-acc').value;
            if(a < 100 || a > user.balance) return alert("Invalid Limit/Balance!");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            await db.collection("requests").add({ user: user.name, amount: a, acc: acc, type: "Withdrawal", status: "pending", time: Date.now() });
            alert("Withdrawal Requested! 😘");
        }

        // SYNC DATA
        function startSync(n) {
            db.collection("users").doc(n).onSnapshot(d => { 
                user = d.data(); 
                document.getElementById('v-bal').innerText = "₨ "+(user.balance||0).toLocaleString(); 
                document.getElementById('v-profit').innerText = "₨ "+(user.profit||0).toLocaleString();
            });
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").onSnapshot(s => {
                const l = document.getElementById('user-history'); l.innerHTML = '';
                s.forEach(d => { const x = d.data(); l.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center"><span class="text-[10px] font-black uppercase">${x.type}</span><span class="text-[8px] font-black ${x.status==='pending'?'text-yellow-500':'text-green-500'} uppercase">${x.status}</span></div>`; });
            });
        }

        // ADMIN TOOLS
        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("Security Key:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdm() { document.getElementById('admin-panel').classList.add('hidden'); }
        async function editU(f) { const u = document.getElementById('adm-u').value.toLowerCase(); const v = parseInt(prompt("Enter New Value:")); await db.collection("users").doc(u).update({ [f]: v }); alert("Success!"); }
        async function blockUser() { const u = document.getElementById('adm-u').value.toLowerCase(); await db.collection("users").doc(u).update({ status: 'blocked' }); alert("User Blocked!"); }

        function syncAdm() {
            db.collection("users").onSnapshot(s => document.getElementById('adm-total-users').innerText = s.size);
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                document.getElementById('adm-live-reqs').innerText = s.size;
                const l = document.getElementById('adm-reqs'); l.innerHTML = '';
                s.forEach(d => {
                    const x = d.data();
                    l.innerHTML += `<div class="glass p-6 rounded-3xl border-l-4 border-yellow-500">
                        <p class="text-[10px] font-black uppercase text-cyan-400 mb-1">${x.user} | ${x.type}</p>
                        <p class="text-[8px] opacity-60">Amt: ${x.amount} | ID/Acc: ${x.tid || x.acc} | Ref: ${x.refBy||'None'}</p>
                        ${x.proof ? `<a href="${x.proof}" target="_blank" class="block bg-cyan-600 text-center py-2 rounded-xl text-[8px] font-black mt-4 uppercase">View Proof</a>` : ''}
                        <div class="flex gap-2 mt-4"><button onclick="hnd('${d.id}','${x.user}',${x.amount},'approved','${x.type}')" class="flex-1 grad-main py-3 rounded-xl text-[8px] font-black">APPROVE</button><button onclick="hnd('${d.id}','${x.user}',${x.amount},'rejected','${x.type}')" class="flex-1 bg-rose-600 py-3 rounded-xl text-[8px] font-black">REJECT</button></div>
                    </div>`;
                });
            });
        }

        async function hnd(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u);
            if(act==='approved' && type === "Deposit") await ref.update({ balance: firebase.firestore.FieldValue.increment(amt) });
            if(act==='rejected' && type === "Withdrawal") await ref.update({ balance: firebase.firestore.FieldValue.increment(amt) });
            await db.collection("requests").doc(id).update({ status: act });
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(document.getElementById('n-'+p)) document.getElementById('n-'+p).classList.add('active-tab'); }
        function copyRef() { navigator.clipboard.writeText(user.name); alert("Referral ID Copied! Share with friends. 😘"); }
    </script>
</body>
</html>
