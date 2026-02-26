<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <title>SkillMint | The Future of Wealth</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@300;500;700&display=swap');
        body { font-family: 'Space Grotesk', sans-serif; background: #030712; color: white; overflow: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.05); }
        .neon-blue { box-shadow: 0 0 15px rgba(59, 130, 246, 0.2); }
        .active-nav { color: #3b82f6; border-top: 4px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .scroller::-webkit-scrollbar { display: none; }
        @keyframes ticker { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .ticker-text { animation: ticker 25s linear infinite; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div class="w-full bg-blue-600/10 border-b border-blue-500/20 py-1 overflow-hidden">
        <div class="ticker-text whitespace-nowrap text-[10px] font-bold tracking-widest uppercase text-blue-400">
            ₿ BTC/USD: $94,203.12 (+2.4%) • Ξ ETH/USD: $3,124.50 (+1.8%) • ₨ PKR/USD: 278.40 • 🥇 GOLD: $2,045 • 📈 SkillMint Dividend: 12.5% Monthly
        </div>
    </div>

    <div id="main-app" class="flex-1 overflow-y-auto scroller pb-24 lg:pb-0">
        
        <section id="auth-ui" class="h-full flex items-center justify-center p-6">
            <div class="glass w-full max-w-sm p-10 rounded-[3rem] text-center neon-blue border-t-8 border-blue-600">
                <h1 class="text-4xl font-black mb-2 italic tracking-tighter">SKILL<span class="text-blue-500">MINT</span></h1>
                <p class="text-[10px] text-gray-500 uppercase tracking-widest mb-10">Premium Investment Hub</p>
                <input type="text" id="user-input" placeholder="Enter Full Name" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-4 outline-none focus:border-blue-500 text-center font-bold">
                <button onclick="loginUser()" class="w-full bg-blue-600 py-4 rounded-2xl font-black text-sm shadow-xl shadow-blue-900/40 hover:bg-blue-500 transition-all">ENTER PLATFORM</button>
            </div>
        </section>

        <div id="dashboard-ui" class="hidden">
            
            <section id="view-home" class="p-6 max-w-5xl mx-auto">
                <div class="flex justify-between items-center mb-8">
                    <div><p class="text-gray-500 text-xs">Welcome back,</p><h2 class="text-xl font-bold" id="name-tag">User</h2></div>
                    <button onclick="showPop('notify')" class="h-10 w-10 glass rounded-full flex items-center justify-center">🔔</button>
                </div>

                <div class="glass p-10 rounded-[2.5rem] bg-gradient-to-br from-blue-600/20 via-transparent to-purple-600/10 mb-8 border-l-4 border-blue-600">
                    <p class="text-xs font-bold text-blue-400 uppercase tracking-widest mb-2">Available Balance</p>
                    <h3 class="text-6xl font-black tracking-tighter" id="bal-val">₨ 0.00</h3>
                    <div class="flex gap-4 mt-8">
                        <button onclick="changePage('wallet')" class="bg-blue-600 px-8 py-3 rounded-2xl font-bold text-xs shadow-lg shadow-blue-900/30">DEPOSIT</button>
                        <button onclick="alert('Withdrawals are active on Sundays!')" class="bg-white/5 border border-white/10 px-8 py-3 rounded-2xl font-bold text-xs">WITHDRAW</button>
                    </div>
                </div>

                <div class="grid grid-cols-2 gap-4 mb-8">
                    <div class="glass p-6 rounded-3xl"><p class="text-gray-500 text-[10px] font-bold">TOTAL PROFIT</p><p class="text-xl font-black text-green-400" id="profit-val">₨ 0</p></div>
                    <div class="glass p-6 rounded-3xl"><p class="text-gray-500 text-[10px] font-bold">ACTIVE PLANS</p><p class="text-xl font-black" id="plans-val">0</p></div>
                </div>

                <h3 class="text-sm font-bold text-gray-400 mb-4 uppercase tracking-widest">Global Referrals</h3>
                <div class="glass p-6 rounded-3xl flex justify-between items-center">
                    <span class="text-xs font-bold text-blue-400" id="ref-code">SM-XXXX</span>
                    <button onclick="alert('Referral link copied!')" class="text-[10px] bg-white/5 px-4 py-2 rounded-xl font-bold">COPY LINK</button>
                </div>
            </section>

            <section id="view-invest" class="hidden p-6 max-w-5xl mx-auto">
                <h2 class="text-2xl font-black mb-8 italic uppercase tracking-tighter text-blue-500">Tier Selection</h2>
                <div class="space-y-4" id="plans-list">
                    </div>
            </section>

            <section id="view-wallet" class="hidden p-6 max-w-xl mx-auto text-center">
                <h2 class="text-2xl font-black mb-10 italic uppercase">Payment Hub</h2>
                <div class="glass p-8 rounded-[3rem] border-t-8 border-green-600 shadow-2xl">
                    <div class="space-y-4 mb-8">
                        <div class="p-6 bg-white/5 rounded-3xl flex justify-between items-center border border-white/5">
                            <div class="text-left"><p class="text-red-500 text-[10px] font-bold">JAZZCASH</p><p class="text-lg font-black">03705519562</p></div>
                            <button onclick="copy('03705519562')" class="bg-red-600/20 text-red-500 px-4 py-2 rounded-xl text-[10px] font-bold">COPY</button>
                        </div>
                        <div class="p-6 bg-white/5 rounded-3xl flex justify-between items-center border border-white/5">
                            <div class="text-left"><p class="text-green-500 text-[10px] font-bold">EASYPAISA</p><p class="text-lg font-black">03379827882</p></div>
                            <button onclick="copy('03379827882')" class="bg-green-600/20 text-green-500 px-4 py-2 rounded-xl text-[10px] font-bold">COPY</button>
                        </div>
                    </div>
                    <a href="https://wa.me/923379827882" class="block w-full bg-green-600 py-5 rounded-[2rem] font-black text-sm shadow-xl shadow-green-900/30">UPLOAD SCREENSHOT</a>
                    <p class="mt-4 text-[10px] text-gray-500 italic">Payments are verified within 2-24 hours.</p>
                </div>
            </section>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 left-0 w-full glass border-t border-white/10 flex justify-around p-4 z-[500]">
        <button onclick="changePage('home')" id="nav-home" class="flex flex-col items-center gap-1 active-nav pt-2">🏠<span class="text-[10px] font-bold">HOME</span></button>
        <button onclick="changePage('invest')" id="nav-invest" class="flex flex-col items-center gap-1 pt-2">📈<span class="text-[10px] font-bold">INVEST</span></button>
        <button onclick="changePage('wallet')" id="nav-wallet" class="flex flex-col items-center gap-1 pt-2">💰<span class="text-[10px] font-bold">WALLET</span></button>
        <button onclick="logoutUser()" class="flex flex-col items-center gap-1 pt-2">🚪<span class="text-[10px] font-bold text-red-500">EXIT</span></button>
    </nav>

    <script>
        const plans = [
            { n: "Bronze", p: 1000, r: "12%" }, { n: "Starter", p: 2500, r: "15%" },
            { n: "Silver", p: 5000, r: "20%" }, { n: "Pro", p: 10000, r: "25%" },
            { n: "Gold", p: 20000, r: "30%" }, { n: "Diamond", p: 40000, r: "40%" },
            { n: "Elite", p: 70000, r: "50%" }, { n: "VIP 1", p: 100000, r: "65%" },
            { n: "VIP 2", p: 150000, r: "80%" }, { n: "Master", p: 250000, r: "100%" },
            { n: "Legend", p: 400000, r: "130%" }, { n: "Empire", p: 600000, r: "160%" },
            { n: "Sovereign", p: 800000, r: "200%" }, { n: "Supreme", p: 1000000, r: "250%" },
            { n: "Global", p: 2000000, r: "500%" }
        ];

        let state = JSON.parse(localStorage.getItem('sm_unlimited')) || null;

        function loginUser() {
            const name = document.getElementById('user-input').value;
            if(!name) return alert("Please enter your name!");
            state = { name: name, bal: 0, profit: 0, activePlans: 0, ref: "SM-" + Math.floor(1000 + Math.random() * 9000) };
            save(); refresh();
        }

        function refresh() {
            if(!state) return;
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('dashboard-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            
            document.getElementById('name-tag').innerText = state.name;
            document.getElementById('bal-val').innerText = "₨ " + state.bal.toLocaleString();
            document.getElementById('profit-val').innerText = "₨ " + state.profit.toLocaleString();
            document.getElementById('plans-val').innerText = state.activePlans;
            document.getElementById('ref-code').innerText = state.ref;

            const list = document.getElementById('plans-list');
            list.innerHTML = '';
            plans.forEach((p, i) => {
                list.innerHTML += `
                    <div onclick="buyTier(${p.p})" class="glass p-6 rounded-[2rem] flex justify-between items-center hover:border-blue-500 transition-all cursor-pointer">
                        <div>
                            <h4 class="font-bold text-lg">${p.n} Tier</h4>
                            <p class="text-[10px] text-green-400 font-bold uppercase tracking-tighter">${p.r} Profit / 10 Days</p>
                        </div>
                        <div class="text-right">
                            <p class="font-black text-blue-500">₨ ${p.p.toLocaleString()}</p>
                            <p class="text-[8px] text-gray-500 font-bold uppercase tracking-widest">Secure Entry</p>
                        </div>
                    </div>
                `;
            });
        }

        function changePage(p) {
            ['home', 'invest', 'wallet'].forEach(v => {
                document.getElementById('view-' + v).classList.add('hidden');
                document.getElementById('nav-' + v)?.classList.remove('active-nav');
            });
            document.getElementById('view-' + p).classList.remove('hidden');
            document.getElementById('nav-' + p)?.classList.add('active-nav');
        }

        function buyTier(price) {
            if(state.bal < price) { alert("Insufficient balance! Go to Wallet to add funds."); changePage('wallet'); }
            else { state.bal -= price; state.activePlans++; save(); refresh(); alert("Tier Activated! Profit will be added soon."); }
        }

        function save() { localStorage.setItem('sm_unlimited', JSON.stringify(state)); }
        function logoutUser() { localStorage.removeItem('sm_unlimited'); location.reload(); }
        function copy(t) { navigator.clipboard.writeText(t); alert("Number Copied!"); }

        // ADMIN HOTKEY: Ctrl+Shift+S (Simulate Deposit 10k)
        window.addEventListener('keydown', (e) => {
            if(e.ctrlKey && e.shiftKey && e.key === 'S') {
                state.bal += 10000; save(); refresh(); alert("Demo Mode: 10,000 PKR Added!");
            }
        });

        if(state) refresh();
    </script>
</body>
</html>
