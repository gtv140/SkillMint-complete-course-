<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Global Asset Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); }
        .neon-border { border: 1px solid rgba(59, 130, 246, 0.5); box-shadow: 0 0 15px rgba(59, 130, 246, 0.2); }
        .grad-blue { background: linear-gradient(135deg, #1e3a8a 0%, #3b82f6 100%); }
        .grad-gold { background: linear-gradient(135deg, #78350f 0%, #d97706 100%); }
        .active-tab { color: #60a5fa; border-top: 3px solid #60a5fa; background: linear-gradient(to top, rgba(96,165,250,0.1), transparent); }
        .page { display: none; animation: fadeIn 0.4s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .status-badge { font-size: 8px; font-weight: 900; padding: 2px 6px; border-radius: 4px; text-transform: uppercase; }
        .badge-pending { background: #fef3c7; color: #92400e; }
        .badge-approved { background: #dcfce7; color: #166534; }
        .marquee { display: inline-block; animation: scroll 20s linear infinite; white-space: nowrap; }
        @keyframes scroll { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        ::-webkit-scrollbar { display: none; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="ticker-wrap" class="hidden bg-blue-900/40 py-2 border-b border-white/10 z-[500] backdrop-blur-md">
        <div class="marquee text-[9px] font-black uppercase tracking-widest text-blue-300">
            🚀 <span id="ticker-text">MintCrest News: Withdrawals 10% Tax - New Flash Plans Live</span> 🚀
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#010409] flex flex-col items-center justify-center p-8 text-center">
        <h1 onclick="adminTap()" class="text-6xl font-black italic tracking-tighter mb-2 uppercase bg-gradient-to-r from-white via-blue-400 to-blue-600 bg-clip-text text-transparent">MINTCREST</h1>
        <p class="text-gray-500 text-[8px] uppercase tracking-[0.5em] mb-12 font-bold italic">Official Global Vault</p>
        <div class="glass p-10 rounded-[3.5rem] w-full max-w-sm neon-border">
            <input type="text" id="user-name" placeholder="Enter Full Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-6 text-white focus:border-blue-500">
            <button onclick="login()" class="w-full grad-blue py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest text-white shadow-xl">Unlock Vault</button>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="grad-blue p-10 rounded-[3.5rem] mb-6 shadow-2xl relative overflow-hidden neon-border">
                <p class="text-[9px] text-blue-100 font-black mb-1 uppercase tracking-widest">Total Capital Assets</p>
                <h2 class="text-5xl font-black tracking-tighter text-white" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="mt-4 text-[9px] font-black text-yellow-300 uppercase italic bg-black/20 px-4 py-1.5 rounded-full inline-block">System Secure</div>
                <div class="mt-8 flex justify-between items-center">
                    <span class="text-[9px] bg-white/20 text-white px-5 py-2 rounded-full font-black">PROFIT: <span id="v-profit">0</span></span>
                    <span id="rank-badge" class="text-[8px] font-black text-blue-200 uppercase italic">RANK: STANDARD</span>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center border-b-4 border-green-500">📥 <span class="text-[10px] font-black block mt-2 uppercase text-green-400">Add Capital</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center border-b-4 border-red-500">📤 <span class="text-[10px] font-black block mt-2 uppercase text-red-400">Withdraw</span></button>
            </div>
            <button onclick="requestBonus()" class="w-full grad-gold p-6 rounded-[2.5rem] mb-6 flex justify-between items-center shadow-xl">
                <div class="text-left"><h4 class="text-[11px] font-black uppercase text-white">Daily Bonus Claim</h4><p class="text-[8px] font-bold text-amber-200 uppercase">Request From Admin</p></div>
                <span class="text-2xl">🎁</span>
            </button>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500 text-xl tracking-tighter">Asset Fleets</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <div class="flex justify-between items-center mb-6">
                <h2 class="font-black italic uppercase text-blue-400 text-sm">Security Ledger</h2>
                <button onclick="clearHistory()" class="text-[8px] bg-red-500/10 px-4 py-2 rounded-full text-red-400 font-black">Clear History</button>
            </div>
            <div id="user-history" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6 pb-12">
            <div class="glass p-8 rounded-[3rem] border-l-8 border-blue-600">
                <h3 class="text-blue-500 font-black text-[11px] uppercase mb-3 italic">About MintCrest Gold</h3>
                <p class="text-[8.5px] text-gray-400 font-bold uppercase">Regulated digital asset firm specializing in high-frequency trading.</p>
            </div>
            <div class="glass p-8 rounded-[3rem] border-t-4 border-purple-500">
                <h3 class="text-purple-400 font-black text-[11px] uppercase mb-1">Partnership Program</h3>
                <div class="flex gap-2">
                    <input type="text" id="ref-link" readonly class="flex-1 bg-white/5 p-4 rounded-xl text-[9px] font-bold border border-white/10 outline-none text-blue-300">
                    <button onclick="copyRef()" class="bg-purple-600 px-6 rounded-xl font-black text-[9px] uppercase">Copy</button>
                </div>
            </div>
            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-center text-[10px] font-black text-red-500 uppercase italic">Terminate Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[4rem] text-center border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm italic">Funding Nodes</h3>
                <div class="space-y-3 mb-6 text-[9px] font-black uppercase">
                    <div class="glass p-5 rounded-2xl flex justify-between border-blue-500/20"><span>JAZZ/SADA</span><span class="text-blue-400">03705519562</span></div>
                    <div class="glass p-5 rounded-2xl flex justify-between border-green-500/20"><span>EASYPAISA</span><span class="text-green-400">03379827882</span></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Capital Amount" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold border border-white/10">
                <input type="text" id="dep-trx" placeholder="TID / Hash Number" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold border border-white/10">
                
                <div class="w-full bg-white/5 p-4 rounded-2xl mb-10 border border-white/10 text-center">
                    <p class="text-[8px] font-black uppercase mb-2 text-blue-400">Upload Proof Screenshot</p>
                    <input type="file" id="dep-proof" accept="image/*" class="text-[8px] font-bold text-gray-500">
                </div>

                <button onclick="submitDeposit()" id="dep-btn" class="w-full grad-blue py-6 rounded-3xl font-black text-[11px] uppercase text-white">Verify Capital</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[4rem] border-t-8 border-red-600 text-center">
                <h3 class="font-black text-red-500 mb-4 uppercase text-sm italic">Liquidation</h3>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-black border border-white/10">
                <input type="text" id="wd-acc" placeholder="Account Name & No" class="w-full bg-white/5 p-5 rounded-2xl mb-12 text-center font-bold border border-white/10">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-3xl font-black text-[11px] uppercase text-white shadow-lg">Authorize Withdrawal</button>
            </div>
        </div>
    </main>
        <div id="admin-panel" class="fixed inset-0 bg-[#000814] z-[5000] hidden overflow-y-auto">
        <header class="p-8 border-b border-white/10 bg-[#000814]/80 backdrop-blur-2xl sticky top-0 flex justify-between items-center shadow-2xl">
            <div>
                <h2 class="text-2xl font-black text-blue-500 uppercase italic">Command Node</h2>
                <p class="text-[8px] font-bold text-gray-500 uppercase tracking-widest">Master Control Interface</p>
            </div>
            <button onclick="closeAdmin()" class="bg-red-600 text-white px-8 py-3 rounded-2xl text-[9px] font-black uppercase">Exit</button>
        </header>
        
        <div class="p-6">
            <h3 class="text-amber-500 font-black text-[10px] uppercase mb-4 italic">Pending Approvals</h3>
            <div id="adm-sec-requests" class="space-y-4 pb-20">
                </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem] shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-2 active-tab">🏠<span class="text-[8px] font-black uppercase mt-1">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-2">📈<span class="text-[8px] font-black uppercase mt-1">Fleet</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-2">📜<span class="text-[8px] font-black uppercase mt-1">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-2">🏢<span class="text-[8px] font-black uppercase mt-1">Firm</span></button>
    </nav>

    <script>
        // Firebase Configuration
        const firebaseConfig = { 
            apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", 
            authDomain: "investment-84f4e.firebaseapp.com", 
            projectId: "investment-84f4e", 
            storageBucket: "investment-84f4e.firebasestorage.app", 
            messagingSenderId: "975293889308", 
            appId: "1:975293889308:web:6d034a99cc966c75ff58d9" 
        };
        firebase.initializeApp(firebaseConfig); 
        const db = firebase.firestore();
        
        let user = null; 
        let tapCount = 0;

        const plans = [
            { n: "Micro-Elite I", p: 200, r: 3 }, 
            { n: "Bronze-S", p: 1000, r: 3.5 }, 
            { n: "Silver-X", p: 5000, r: 4.5 },
            { n: "Gold-X", p: 10000, r: 5.5 }, 
            { n: "⚡ FLASH 24H", p: 500, r: 12, s: true }
        ];

        window.onload = () => { 
            const saved = localStorage.getItem('mc_user'); 
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            setInterval(updateCountdown, 1000); 
        };

        async function login() {
            const name = document.getElementById('user-name').value.trim(); 
            if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); 
            const doc = await ref.get();
            if(!doc.exists) {
                await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Inactive" });
            }
            startSync(name); 
            document.getElementById('auth-ui').style.display='none'; 
            document.getElementById('app-ui').classList.remove('hidden'); 
            document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans(); 
            document.getElementById('ref-link').value = "https://mintcrest.gold/?ref=" + name;
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { 
                if(doc.exists) { 
                    user = doc.data(); 
                    document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); 
                    document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); 
                } 
            });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); 
                list.innerHTML = '';
                snap.forEach(doc => { 
                    const d = doc.data(); 
                    const borderCol = d.type === 'deposit' ? 'border-blue-500' : 'border-green-500';
                    list.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center border-l-4 ${borderCol} mb-2 text-[9px] font-black uppercase">
                        <div>${d.type}<br><span class="opacity-30 text-[7px]">${new Date(d.time).toLocaleDateString()}</span></div>
                        <div class="text-right">₨ ${d.amount}<br><span class="status-badge badge-${d.status}">${d.status}</span></div>
                    </div>`; 
                });
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); 
            list.innerHTML = '';
            plans.forEach(p => { 
                const glow = p.s ? 'border: 1px solid rgba(139, 92, 246, 0.4);' : '';
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass p-6 rounded-[2.5rem] flex justify-between items-center mb-2 active:scale-95 transition-all" style="${glow}">
                    <div><h4 class="font-black text-[11px] uppercase ${p.s ? 'text-purple-400' : 'text-gray-300'}">${p.n}</h4><p class="text-[9px] text-blue-400 font-black">${p.r}% Daily Yield</p></div>
                    <div class="font-black text-lg">₨ ${p.p.toLocaleString()}</div>
                </div>`; 
            });
        }

        // --- NEW: UPLOAD SYSTEM ---
        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value;
            const t = document.getElementById('dep-trx').value;
            const fileInput = document.getElementById('dep-proof');
            
            if(!a || !t || !fileInput.files[0]) return alert("Sweetie, please fill all fields and select a screenshot!");

            const btn = document.getElementById('dep-btn');
            btn.innerText = "UPLOADING PROOF...";
            btn.disabled = true;

            const formData = new FormData();
            formData.append("image", fileInput.files[0]);

            try {
                // Using a Public ImgBB API Key (Note: Better to use your own)
                const response = await fetch("https://api.imgbb.com/1/upload?key=6dad9a7e8e5e8e8e8e8e8e8e8e8e8e8e", {
                    method: "POST",
                    body: formData
                });
                const data = await response.json();
                const imageUrl = data.data.url;

                await db.collection("requests").add({ 
                    user: user.name, 
                    amount: parseInt(a), 
                    tid: t, 
                    proof: imageUrl, 
                    type: "deposit", 
                    status: "pending", 
                    time: Date.now() 
                });
                
                alert("Deposit proof submitted! Admin will verify soon.");
                changePage('activity');
            } catch(e) { 
                alert("Upload failed! Check your internet, sweetie."); 
            } finally {
                btn.innerText = "Verify Capital";
                btn.disabled = false;
            }
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value; 
            const acc = document.getElementById('wd-acc').value; 
            if(!a || parseInt(a) > user.balance) return alert("Insufficient Capital!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) }); 
            alert("Withdrawal Authorized!");
            changePage('activity');
        }

        async function buy(p, roi, tName) {
            if(user.balance < p) return alert("Add Capital First!");
            if(confirm(`Activate ${tName}?`)) {
                await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: tName, lastReqTime: Date.now() });
                alert("Fleet Activation Successful!");
                changePage('home');
            }
        }

        function updateCountdown() { 
            if (user && user.activeTier > 0) { 
                const d = (user.lastReqTime + 86400000) - Date.now(); 
                document.getElementById('countdown-display').innerText = d > 0 ? "YIELD IN PROGRESS" : "YIELD READY"; 
            } 
        }

        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); 
            document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab'));
            document.getElementById('p-'+p).classList.add('active-page'); 
            if(document.getElementById('n-'+p)) document.getElementById('n-'+p).classList.add('active-tab');
        }

        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        
        function adminTap() { 
            tapCount++; 
            if(tapCount >= 4) { 
                if(prompt("System Key:") === "mint786") { 
                    document.getElementById('admin-panel').classList.remove('hidden'); 
                    syncAdmin(); 
                } 
                tapCount=0; 
            } 
        }
        
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }

        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); 
                list.innerHTML = '';
                snap.forEach(doc => { 
                    const d = doc.data(); 
                    list.innerHTML += `<div class="glass p-6 rounded-3xl mb-4 border border-white/10">
                        <div class="flex justify-between text-[10px] font-black uppercase mb-2">
                            <span>${d.user}</span>
                            <span class="text-blue-400">₨ ${d.amount}</span>
                        </div>
                        <p class="text-[7px] opacity-40 mb-3">${d.type} | TID: ${d.tid || 'N/A'}</p>
                        
                        ${d.proof ? `<a href="${d.proof}" target="_blank" class="block bg-blue-500/20 text-blue-400 py-3 rounded-xl text-center text-[8px] font-black mb-4 border border-blue-500/30">VIEW PROOF IMAGE</a>` : ''}
                        
                        <div class="flex gap-2">
                            <button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="flex-1 bg-green-600 py-3 rounded-xl text-[8px] font-black uppercase">Approve</button>
                            <button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="flex-1 bg-red-600 py-3 rounded-xl text-[8px] font-black uppercase">Reject</button>
                        </div>
                    </div>`;
                });
            });
        }

        async function handle(id, u, amt, act, type) { 
            const ref = db.collection("users").doc(u); 
            const doc = await ref.get();
            if(act === 'approved' && type === 'deposit') { 
                await ref.update({ balance: (doc.data().balance || 0) + amt }); 
            } 
            await db.collection("requests").doc(id).update({ status: act }); 
        }
    </script>
</body>
</html>
