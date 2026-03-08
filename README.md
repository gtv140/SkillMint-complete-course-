<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <title>MintCrest Infinity | Global Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #00040a; color: white; margin: 0; overflow: hidden; }
        .app-shell { height: 100vh; overflow-y: auto; scroll-behavior: smooth; -webkit-overflow-scrolling: touch; padding-bottom: 150px; }
        ::-webkit-scrollbar { display: none; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); }
        .grad-main { background: linear-gradient(135deg, #0052d4 0%, #4364f7 50%, #6fb1fc 100%); }
        .page { display: none; } .active-page { display: block; animation: fadeInUp 0.4s ease; }
        @keyframes fadeInUp { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        /* Colorful Logout Button */
        .logout-btn { background: linear-gradient(45deg, #ff0055, #ffdd00, #00ffcc, #3333ff); background-size: 400%; animation: rainbow 5s infinite linear; border-radius: 1.2rem; }
        @keyframes rainbow { 0%{background-position:0%} 100%{background-position:100%} }
        .spin-wheel { transition: transform 4s cubic-bezier(0.1, 0, 0, 1); border: 8px solid rgba(255,255,255,0.1); }
    </style>
</head>
<body>

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-[#00040a] flex flex-col items-center justify-center p-8 text-center">
        <div class="w-20 h-20 grad-main rounded-[2.5rem] mb-6 flex items-center justify-center text-3xl shadow-2xl animate-pulse">💎</div>
        <h1 onclick="adminTap()" class="text-4xl font-black italic mb-2 tracking-tighter">MINTCREST</h1>
        <p class="text-[8px] uppercase tracking-[0.4em] text-cyan-400 mb-10 font-bold">Trusted Investment Node</p>
        <div class="glass p-8 rounded-[3.5rem] w-full max-w-xs border-t border-white/20">
            <input type="text" id="user-name" placeholder="Full Name" class="w-full bg-white/5 p-4 rounded-2xl mb-4 text-center font-bold outline-none border border-white/10 text-white">
            <input type="text" id="ref-by" placeholder="Referral ID (Optional)" class="w-full bg-white/5 p-3 rounded-xl mb-6 text-[10px] text-center outline-none border border-white/5 text-white">
            <button onclick="login()" class="w-full grad-main py-5 rounded-2xl font-black uppercase text-xs">Establish Link</button>
        </div>
    </section>

    <main id="app-ui" class="hidden app-shell">
        
        <div id="p-home" class="page active-page p-6">
            <div class="flex justify-between items-center mt-4 mb-8">
                <div><h2 class="text-2xl font-black" id="v-user">USER</h2><p class="text-[9px] text-green-400 font-bold uppercase tracking-widest">● System Verified</p></div>
                <button onclick="logout()" class="logout-btn w-12 h-12 flex items-center justify-center text-xl shadow-lg">🚀</button>
            </div>

            <div id="broadcast" class="hidden glass border-blue-500/50 p-4 rounded-2xl mb-6 text-[10px] font-bold text-center text-blue-300"></div>

            <div class="grad-main p-8 rounded-[3.5rem] mb-8 shadow-2xl relative overflow-hidden">
                <p class="text-[10px] font-black opacity-80 uppercase italic">Main Node Wallet</p>
                <h2 class="text-5xl font-black my-2" id="v-bal">₨ 0</h2>
                <div class="mt-8 flex justify-between items-center border-t border-white/10 pt-4">
                    <div><p class="text-[8px] opacity-60 uppercase font-bold">Total Profit</p><p class="text-lg font-black" id="v-profit">₨ 0</p></div>
                    <button onclick="copyRef()" class="bg-black/30 px-4 py-2 rounded-xl text-[8px] font-black uppercase border border-white/10">🔗 Share ID</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-8">
                <button onclick="reqBonus('Daily Bonus', 50)" class="glass p-5 rounded-3xl text-center"><span class="text-2xl block mb-1">🎁</span><span class="text-[10px] font-black uppercase">Daily Bonus</span></button>
                <button onclick="changePage('spin')" class="glass p-5 rounded-3xl text-center"><span class="text-2xl block mb-1">🎡</span><span class="text-[10px] font-black uppercase">Lucky Spin</span></button>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass py-8 rounded-[2.5rem] font-black text-xs uppercase text-cyan-400 border border-cyan-400/20">Deposit</button>
                <button onclick="changePage('withdraw')" class="glass py-8 rounded-[2.5rem] font-black text-xs uppercase text-rose-500 border border-rose-500/20">Withdraw</button>
            </div>
        </div>

        <div id="p-invest" class="page p-6"><h3 class="text-center font-black text-xl mb-8 uppercase text-blue-400 italic">Node Fleets</h3><div id="plans-list" class="space-y-4"></div></div>

        <div id="p-spin" class="page p-6 text-center">
            <h3 class="font-black text-xl mb-10 uppercase italic">Infinity Spin</h3>
            <div id="wheel" class="w-56 h-56 rounded-full spin-wheel grad-main mx-auto mb-10 flex items-center justify-center text-3xl font-black">?</div>
            <button onclick="reqBonus('Spin Win', 100)" class="grad-main px-12 py-4 rounded-2xl font-black uppercase text-xs">Spin (Request ₨ 100)</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <h3 class="text-center font-black text-xl mb-8 uppercase italic">Capital Inflow</h3>
            <div class="space-y-3 mb-8">
                <div class="glass p-5 rounded-2xl flex justify-between items-center border-l-4 border-yellow-500"><span>JazzCash</span><b>03705519562</b></div>
                <div class="glass p-5 rounded-2xl flex justify-between items-center border-l-4 border-green-500"><span>Easypaisa</span><b>03379827882</b></div>
                <div class="glass p-5 rounded-2xl flex justify-between items-center border-l-4 border-orange-500"><span>Binance</span><b>USDT-TRC20 (Edit in Code)</b></div>
            </div>
            <div class="glass p-6 rounded-[3rem]">
                <input type="number" id="d-amt" placeholder="Min 200" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-bold outline-none border border-white/10">
                <input type="text" id="d-tid" placeholder="TID Number" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-bold outline-none border border-white/10">
                <input type="file" id="d-file" accept="image/*" class="w-full text-[10px] mb-6">
                <button id="dep-btn" onclick="subDep()" class="w-full grad-main py-5 rounded-2xl font-black text-xs uppercase">Confirm Proof</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-8 rounded-[3.5rem] border-t-8 border-rose-600">
                <h3 class="text-2xl font-black text-rose-500 mb-6 uppercase text-center italic">Capital Out</h3>
                <input type="number" id="w-amt" placeholder="Min 100" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-bold">
                <input type="text" id="w-acc" placeholder="Account Number" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-bold">
                <select id="w-method" class="w-full bg-black/50 p-4 rounded-xl mb-8 text-center text-xs">
                    <option value="Easypaisa">Easypaisa</option>
                    <option value="JazzCash">JazzCash</option>
                    <option value="Binance">Binance</option>
                    <option value="Bank">Local Bank</option>
                </select>
                <button onclick="subWd()" class="w-full bg-rose-600 py-5 rounded-2xl font-black text-xs uppercase shadow-xl">Confirm Payout</button>
            </div>
        </div>

        <div id="p-about" class="page p-6">
            <div class="glass p-6 rounded-3xl mb-6">
                <h4 class="font-black text-cyan-400 mb-2 uppercase">Company Details</h4>
                <p class="text-[9px] opacity-60 leading-relaxed font-bold">MintCrest is a 100% Trusted Global Investment Node. Our system is protected by 256-bit SSL encryption. Privacy and security are our top priorities.</p>
            </div>
            <button onclick="window.open('https://chat.whatsapp.com/YOUR_GROUP')" class="w-full bg-green-600 py-6 rounded-3xl font-black text-xs uppercase mb-4">WhatsApp Community</button>
            <button onclick="alert('Admin Help Desk: Please message via WhatsApp!')" class="w-full grad-main py-6 rounded-3xl font-black text-xs uppercase">Help Desk</button>
        </div>

        <div id="p-history" class="page p-6"><h3 class="font-black text-xl mb-8 uppercase italic text-center">Node Logs</h3><div id="user-history" class="space-y-3"></div></div>

    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#00040a] z-[50000] hidden overflow-y-auto p-8">
        <div class="flex justify-between items-center mb-10 border-b border-white/10 pb-6">
            <h2 class="text-xl font-black text-cyan-400 uppercase italic">Supreme Admin</h2>
            <button onclick="closeAdm()" class="bg-rose-600 px-6 py-2 rounded-xl text-[10px] font-black">EXIT</button>
        </div>
        <div class="grid grid-cols-2 gap-4 mb-8 text-center text-[10px] font-bold">
            <div class="glass p-6 rounded-3xl"><p class="opacity-40 uppercase">Total Nodes</p><h4 id="adm-u-count" class="text-xl font-black">0</h4></div>
            <div class="glass p-6 rounded-3xl"><p class="opacity-40 uppercase">Pending</p><h4 id="adm-p-count" class="text-xl font-black text-yellow-500">0</h4></div>
        </div>
        <div class="glass p-6 rounded-3xl mb-8">
            <input type="text" id="adm-bc" placeholder="Global Broadcast..." class="w-full bg-white/5 p-3 rounded-xl mb-3 outline-none text-[10px]">
            <button onclick="setBC()" class="w-full grad-main py-3 rounded-xl text-[9px] font-black uppercase">Push Announcement</button>
        </div>
        <div id="adm-reqs-list" class="space-y-4"></div>
    </div>

    <nav id="bottom-nav" class="hidden glass p-7 flex justify-around items-center fixed bottom-0 w-full z-[2000] rounded-t-[3.5rem] border-t border-white/10">
        <button onclick="changePage('home')" class="text-2xl">🏠</button>
        <button onclick="changePage('invest')" class="text-2xl">📈</button>
        <button onclick="changePage('history')" class="text-2xl">📜</button>
        <button onclick="changePage('about')" class="text-2xl">ℹ️</button>
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

        // SESSION FIX - NO LOGOUT ON REFRESH
        window.onload = () => {
            const saved = localStorage.getItem('mint_global_session');
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            renderPlans();
        };

        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase();
            const r = document.getElementById('ref-by').value.trim().toLowerCase();
            if(!n) return;
            const ref = db.collection("users").doc(n);
            const d = await ref.get();
            if(!d.exists) await ref.set({ name: n, balance: 0, profit: 0, refBy: r || 'Direct', time: Date.now(), plans: [] });
            localStorage.setItem('mint_global_session', n);
            document.getElementById('auth-ui').style.display='none';
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            sync(n);
        }

        function sync(n) {
            db.collection("users").doc(n).onSnapshot(d => {
                user = d.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString();
                document.getElementById('v-user').innerText = n.toUpperCase();
                updateTimers();
            });
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").limit(10).onSnapshot(s => {
                const l = document.getElementById('user-history'); l.innerHTML = '';
                s.forEach(doc => {
                    const x = doc.data();
                    l.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between border-l-4 ${x.status==='approved'?'border-green-500':'border-yellow-500'}">
                        <span class="text-[10px] font-black uppercase">${x.type} - ₨ ${x.amount}</span>
                        <span class="text-[8px] font-bold uppercase">${x.status}</span>
                    </div>`;
                });
            });
            db.collection("settings").doc("broadcast").onSnapshot(d => {
                const b = document.getElementById('broadcast');
                if(d.exists && d.data().msg) { b.innerText = d.data().msg; b.classList.remove('hidden'); }
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            for(let i=1; i<=25; i++) {
                const vip = i > 20;
                let price = i === 1 ? 200 : (i * 500) - 300; if(vip) price = (i-20) * 10000;
                let days = i <= 5 ? 15 + (i * 2) : 30 + i;
                const daily = (price * (vip ? 0.15 : 0.08)).toFixed(0);
                list.innerHTML += `<div class="glass p-6 rounded-[2.5rem] border-l-4 ${vip?'border-yellow-500':'border-blue-500'} mb-4">
                    <div class="flex justify-between items-start mb-4">
                        <div><h4 class="font-black text-[10px] uppercase">${vip?'VIP Diamond P'+(i-20):'Node Fleet V'+i}</h4>
                        <p class="text-[8px] opacity-60 font-bold">${vip?'Special Offer':days+' Days Cycle'}</p></div>
                        <div class="text-right text-xs font-black">₨ ${price}</div>
                    </div>
                    <div class="flex justify-between items-center"><span class="text-green-400 font-bold text-xs">₨ ${daily}/Day</span>
                    <button onclick="buyPlan(${price}, ${days}, ${i})" class="bg-white/10 px-6 py-2 rounded-xl text-[8px] font-black uppercase">Activate</button></div>
                    <div id="timer-${i}" class="text-[8px] mt-3 font-bold text-cyan-400 italic"></div>
                </div>`;
            }
        }

        async function buyPlan(p, d, id) {
            if(user.balance < p) return alert("Low Balance, Sweetie! 😘");
            const endTime = Date.now() + (d * 24 * 60 * 60 * 1000);
            await db.collection("users").doc(user.name).update({ 
                balance: firebase.firestore.FieldValue.increment(-p),
                plans: firebase.firestore.FieldValue.arrayUnion({ id, endTime })
            });
            alert("Plan Activated! Timer Started. 😘");
        }

        function updateTimers() {
            if(!user.plans) return;
            user.plans.forEach(p => {
                const el = document.getElementById('timer-'+p.id);
                if(el) {
                    const rem = p.endTime - Date.now();
                    if(rem > 0) {
                        const days = Math.floor(rem / (1000 * 60 * 60 * 24));
                        const hours = Math.floor((rem % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
                        el.innerText = `⏳ Node active: ${days}d ${hours}h left`;
                    } else { el.innerText = "❌ Cycle Ended"; }
                }
            });
        }

        async function subDep() {
            const a = parseInt(document.getElementById('d-amt').value);
            const t = document.getElementById('d-tid').value;
            const f = document.getElementById('d-file').files[0];
            if(a < 200 || !t || !f) return alert("Min 200 & Proof needed, Sweetie! 😘");
            const btn = document.getElementById('dep-btn'); btn.innerText = "UPLOADING..."; btn.disabled = true;
            try {
                const sRef = storage.ref(`proofs/${Date.now()}_${user.name}`);
                const snap = await sRef.put(f);
                const url = await snap.ref.getDownloadURL();
                await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: url, type: "Deposit", status: "pending", time: Date.now(), refBy: user.refBy });
                alert("Request Sent! 😘"); changePage('home');
            } catch(e) { alert("Error! Check Firebase Storage Rules."); }
            btn.innerText = "Confirm Proof"; btn.disabled = false;
        }

        async function subWd() {
            const a = parseInt(document.getElementById('w-amt').value); const acc = document.getElementById('w-acc').value; const m = document.getElementById('w-method').value;
            if(a < 100 || a > user.balance) return alert("Invalid Amount, Sweetie! 😘");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            await db.collection("requests").add({ user: user.name, amount: a, acc: acc, method: m, type: "Withdrawal", status: "pending", time: Date.now() });
            alert("Withdrawal Requested! 😘"); changePage('home');
        }

        async function reqBonus(type, amt) {
            alert(type + " Request Logged! 😘");
            await db.collection("requests").add({ user: user.name, amount: amt, type: type, status: "pending", time: Date.now() });
        }

        // ADMIN CORE
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Pass:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdm() { document.getElementById('admin-panel').classList.add('hidden'); }
        function syncAdm() {
            db.collection("users").onSnapshot(s => document.getElementById('adm-u-count').innerText = s.size);
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                document.getElementById('adm-p-count').innerText = s.size;
                const l = document.getElementById('adm-reqs-list'); l.innerHTML = '';
                s.forEach(doc => {
                    const x = doc.data();
                    l.innerHTML += `<div class="glass p-5 rounded-3xl text-[10px]">
                        <div class="flex justify-between font-black text-cyan-400 mb-2 uppercase"><span>${x.user}</span><span>₨ ${x.amount}</span></div>
                        <p class="opacity-60 mb-1">Type: ${x.type} | Ref By: ${x.refBy || 'Direct'}</p>
                        <p class="opacity-60 mb-3">Details: ${x.tid || x.acc} (${x.method || 'DEP'})</p>
                        ${x.proof ? `<a href="${x.proof}" target="_blank" class="bg-blue-600 block text-center py-2 rounded-xl mb-3 font-bold uppercase">View Proof</a>` : ''}
                        <div class="flex gap-2"><button onclick="hnd('${doc.id}','${x.user}',${x.amount},'approved','${x.type}')" class="flex-1 bg-green-600 py-3 rounded-xl font-bold">APPROVE</button>
                        <button onclick="hnd('${doc.id}','${x.user}',${x.amount},'rejected','${x.type}')" class="flex-1 bg-rose-600 py-3 rounded-xl font-bold">REJECT</button></div>
                    </div>`;
                });
            });
        }
        async function hnd(id, u, amt, act, type) {
            if(act==='approved' && type !== "Withdrawal") await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(amt) });
            if(act==='rejected' && type === "Withdrawal") await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(amt) });
            await db.collection("requests").doc(id).update({ status: act });
        }
        function setBC() { const m = document.getElementById('adm-bc').value; db.collection("settings").doc("broadcast").set({ msg: m }); alert("Broadcast Sent!"); }

        function logout() { localStorage.removeItem('mint_global_session'); location.reload(); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function copyRef() { navigator.clipboard.writeText(user.name); alert("ID Copied! Sweetie 😘"); }
    </script>
</body>
</html>
