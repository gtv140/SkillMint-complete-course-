<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <title>WealthWise | Premium Investment App</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #080a0f; color: white; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(15px); border: 1px solid rgba(255,255,255,0.08); }
        .active-nav { color: #3b82f6; border-top: 2px solid #3b82f6; }
        .hide-scroll::-webkit-scrollbar { display: none; }
    </style>
</head>
<body class="pb-24 lg:pb-0 lg:pl-64">

    <aside class="hidden lg:flex fixed left-0 top-0 h-screen w-64 bg-gray-950 border-r border-gray-800 flex-col p-6">
        <h1 class="text-2xl font-black text-blue-500 italic mb-10">WEALTHWISE</h1>
        <nav class="space-y-4 flex-1">
            <button onclick="showPage('home')" class="w-full text-left p-3 rounded-xl hover:bg-white/5 transition">🏠 Home</button>
            <button onclick="showPage('invest')" class="w-full text-left p-3 rounded-xl hover:bg-white/5 transition">📈 Invest</button>
            <button onclick="showPage('wallet')" class="w-full text-left p-3 rounded-xl hover:bg-white/5 transition">💰 Wallet</button>
            <button onclick="showPage('profile')" class="w-full text-left p-3 rounded-xl hover:bg-white/5 transition">👤 Profile</button>
        </nav>
        <button onclick="logout()" class="text-red-500 text-sm font-bold text-left p-3">Logout</button>
    </aside>

    <section id="page-auth" class="min-h-screen flex items-center justify-center p-6 bg-[#080a0f]">
        <div class="w-full max-w-md glass p-10 rounded-[2.5rem] text-center shadow-2xl">
            <h2 class="text-3xl font-black mb-8 italic">Welcome <span class="text-blue-500">Investor</span></h2>
            <input type="text" id="user-name" placeholder="Enter Full Name" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 outline-none focus:border-blue-500 mb-4">
            <button onclick="login()" class="w-full bg-blue-600 py-4 rounded-2xl font-black shadow-lg shadow-blue-900/40">GET STARTED</button>
        </div>
    </section>

    <div id="app-content" class="hidden">
        
        <section id="page-home" class="p-6">
            <header class="flex justify-between items-center mb-8">
                <div><p class="text-gray-500 text-xs">Good Day,</p><h2 class="text-xl font-bold" id="display-name">User</h2></div>
                <div class="h-12 w-12 bg-blue-600 rounded-full flex items-center justify-center font-black">W</div>
            </header>

            <div class="glass p-8 rounded-[2rem] bg-gradient-to-br from-blue-600/20 to-purple-600/10 border-l-4 border-blue-500 mb-8">
                <p class="text-xs font-bold text-gray-400 uppercase tracking-widest">Global Balance</p>
                <h3 class="text-5xl font-black mt-2 tracking-tighter" id="dash-bal">₨ 0</h3>
                <div class="flex gap-3 mt-6">
                    <button onclick="showPage('wallet')" class="bg-blue-600 px-6 py-2 rounded-xl text-xs font-bold">Deposit</button>
                    <button onclick="showPage('invest')" class="bg-white/10 px-6 py-2 rounded-xl text-xs font-bold">Invest</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <div class="glass p-6 rounded-3xl"><p class="text-gray-500 text-[10px] font-bold">PROFIT</p><p class="text-xl font-black text-green-400">+ 12.5%</p></div>
                <div class="glass p-6 rounded-3xl"><p class="text-gray-500 text-[10px] font-bold">REFERRALS</p><p class="text-xl font-black">5 Users</p></div>
            </div>
        </section>

        <section id="page-invest" class="hidden p-6">
            <h2 class="text-2xl font-black mb-6 italic">MARKET PLANS</h2>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4 hide-scroll overflow-y-auto max-h-[70vh]">
                <div id="plans-list" class="space-y-4 w-full lg:col-span-2"></div>
            </div>
        </section>

        <section id="page-wallet" class="hidden p-6">
            <h2 class="text-2xl font-black mb-6 italic">WALLET</h2>
            <div class="glass p-6 rounded-3xl mb-6">
                <p class="text-xs text-gray-400">Add funds via JazzCash or EasyPaisa</p>
                <div class="mt-4 space-y-3">
                    <div class="flex justify-between p-4 bg-white/5 rounded-xl"><span>JazzCash</span><span class="font-bold">03705519562</span></div>
                    <div class="flex justify-between p-4 bg-white/5 rounded-xl"><span>EasyPaisa</span><span class="font-bold">03379827882</span></div>
                </div>
                <a href="https://wa.me/923379827882" class="block w-full text-center bg-green-600 py-4 rounded-xl font-black mt-6 text-sm">SEND SCREENSHOT</a>
            </div>
        </section>

        <section id="page-profile" class="hidden p-6 text-center">
            <div class="h-24 w-24 bg-gray-800 rounded-full mx-auto mb-4 flex items-center justify-center text-4xl">👤</div>
            <h2 class="text-xl font-bold" id="profile-name">User Name</h2>
            <p class="text-gray-500 text-sm mb-10">Investor Account • ID: 88271</p>
            <div class="glass p-6 rounded-3xl text-left space-y-4">
                <div class="flex justify-between border-b border-white/5 pb-2"><span>Status</span><span class="text-green-500">Verified</span></div>
                <div class="flex justify-between border-b border-white/5 pb-2"><span>Joined</span><span>Feb 2026</span></div>
                <button onclick="logout()" class="w-full text-red-500 font-bold py-2">Sign Out</button>
            </div>
        </section>

    </div>

    <nav class="lg:hidden fixed bottom-0 left-0 w-full glass flex justify-around p-4 z-[200]">
        <button onclick="showPage('home')" id="nav-home" class="flex flex-col items-center gap-1"><span>🏠</span><span class="text-[10px]">Home</span></button>
        <button onclick="showPage('invest')" id="nav-invest" class="flex flex-col items-center gap-1"><span>📈</span><span class="text-[10px]">Invest</span></button>
        <button onclick="showPage('wallet')" id="nav-wallet" class="flex flex-col items-center gap-1"><span>💰</span><span class="text-[10px]">Wallet</span></button>
        <button onclick="showPage('profile')" id="nav-profile" class="flex flex-col items-center gap-1"><span>👤</span><span class="text-[10px]">Profile</span></button>
    </nav>

    <script>
        const plans = [
            { name: "Silver Starter", price: 1000, roi: "12%", days: 5 },
            { name: "Gold Growth", price: 5000, roi: "25%", days: 10 },
            { name: "VIP Executive", price: 20000, roi: "45%", days: 15 },
            { name: "Diamond Empire", price: 100000, roi: "80%", days: 30 }
        ];

        let user = JSON.parse(localStorage.getItem('ww_pro')) || null;

        function login() {
            const n = document.getElementById('user-name').value;
            if(!n) return alert("Enter your name!");
            user = { name: n, balance: 0, profit: 0 };
            save();
            initApp();
        }

        function initApp() {
            if(!user) return;
            document.getElementById('page-auth').classList.add('hidden');
            document.getElementById('app-content').classList.remove('hidden');
            document.getElementById('display-name').innerText = user.name;
            document.getElementById('profile-name').innerText = user.name;
            document.getElementById('dash-bal').innerText = "₨ " + user.balance.toLocaleString();
            
            // Generate Plans
            const plist = document.getElementById('plans-list');
            plist.innerHTML = '';
            plans.forEach((p, i) => {
                plist.innerHTML += `
                    <div class="glass p-6 rounded-3xl flex justify-between items-center">
                        <div>
                            <h4 class="font-bold">${p.name}</h4>
                            <p class="text-[10px] text-gray-500 uppercase">${p.roi} Return in ${p.days} Days</p>
                        </div>
                        <button onclick="buyPlan(${p.price})" class="bg-blue-600 px-4 py-2 rounded-xl font-bold text-xs">₨ ${p.price.toLocaleString()}</button>
                    </div>
                `;
            });
        }

        function showPage(pageId) {
            ['home', 'invest', 'wallet', 'profile'].forEach(p => {
                document.getElementById('page-' + p).classList.add('hidden');
                document.getElementById('nav-' + p)?.classList.remove('text-blue-500');
            });
            document.getElementById('page-' + pageId).classList.remove('hidden');
            document.getElementById('nav-' + pageId)?.classList.add('text-blue-500');
        }

        function buyPlan(price) {
            if(user.balance < price) {
                alert("Low Balance! Please deposit money.");
                showPage('wallet');
            } else {
                user.balance -= price;
                save();
                initApp();
                alert("Investment started!");
            }
        }

        function save() { localStorage.setItem('ww_pro', JSON.stringify(user)); }
        function logout() { localStorage.removeItem('ww_pro'); location.reload(); }

        // ADMIN: Ctrl + Shift + Enter to add 50k
        window.addEventListener('keydown', (e) => {
            if(e.ctrlKey && e.shiftKey && e.key === 'Enter') {
                user.balance += 50000;
                save();
                initApp();
                alert("50,000 PKR Added to your account!");
            }
        });

        if(user) initApp();
    </script>
</body>
</html>
