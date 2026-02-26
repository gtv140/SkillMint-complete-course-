<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <title>WealthWise Pro | Secure Investment</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;700;900&display=swap');
        body { font-family: 'Outfit', sans-serif; background: #05070a; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); }
        .auth-screen { display: flex; }
        .dashboard-screen { display: none; }
        .tab-active { border-bottom: 2px solid #3b82f6; color: #3b82f6; }
    </style>
</head>
<body>

    <section id="auth-page" class="min-h-screen flex items-center justify-center p-6 auth-screen">
        <div class="glass w-full max-w-md p-10 rounded-[2.5rem] shadow-2xl">
            <h1 class="text-3xl font-black text-center text-blue-500 mb-2 uppercase italic">WealthWise</h1>
            <p class="text-center text-gray-500 text-sm mb-8">Invest Smart, Live Better.</p>
            
            <div class="flex gap-4 mb-6 border-b border-gray-800">
                <button onclick="switchAuth('login')" id="login-tab" class="pb-2 flex-1 font-bold tab-active">Login</button>
                <button onclick="switchAuth('signup')" id="signup-tab" class="pb-2 flex-1 font-bold text-gray-600">Signup</button>
            </div>

            <div id="auth-form" class="space-y-4">
                <input type="email" id="email" placeholder="Email Address" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 outline-none focus:border-blue-500">
                <input type="password" id="pass" placeholder="Password" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 outline-none focus:border-blue-500">
                <button onclick="handleAuth()" id="auth-btn" class="w-full bg-blue-600 py-4 rounded-2xl font-black shadow-lg shadow-blue-900/40 hover:bg-blue-500 transition-all">LOGIN NOW</button>
            </div>
        </div>
    </section>

    <section id="dash-page" class="min-h-screen dashboard-screen flex-col">
        <nav class="p-5 flex justify-between items-center glass sticky top-0 z-50">
            <h1 class="text-xl font-black text-blue-500">WEALTHWISE <span class="text-white text-xs block opacity-50">PORTFOLIO</span></h1>
            <div class="flex items-center gap-4">
                <span id="user-display" class="text-xs font-bold text-gray-400"></span>
                <button onclick="logout()" class="text-[10px] bg-red-600/20 text-red-500 px-3 py-1 rounded-full border border-red-500/20 font-bold">LOGOUT</button>
            </div>
        </nav>

        <main class="max-w-4xl mx-auto p-6 w-full">
            <div class="glass p-10 rounded-[3rem] border-t-8 border-blue-600 shadow-2xl mb-8 relative overflow-hidden">
                <div class="absolute -top-10 -right-10 w-40 h-40 bg-blue-600/10 rounded-full blur-3xl"></div>
                <p class="text-gray-500 text-xs font-bold uppercase tracking-widest">Global Balance (PKR)</p>
                <h2 class="text-6xl font-black mt-2 tracking-tighter" id="balanceText">₨ 0.00</h2>
                <div class="mt-8 flex gap-3">
                    <button onclick="showDep()" class="bg-blue-600 px-8 py-3 rounded-2xl font-black text-sm hover:scale-105 transition">DEPOSIT</button>
                    <button onclick="alert('Withdrawals open on weekends!')" class="bg-white/5 border border-white/10 px-8 py-3 rounded-2xl font-black text-sm">WITHDRAW</button>
                </div>
            </div>

            <div class="grid md:grid-cols-2 gap-4 mb-20">
                <div class="glass p-6 rounded-3xl border-l-4 border-green-500">
                    <h4 class="font-bold text-gray-400 text-xs uppercase">Daily Profit (Avg)</h4>
                    <p class="text-2xl font-black text-green-400">+ 12.5%</p>
                </div>
                <div class="glass p-6 rounded-3xl border-l-4 border-yellow-500">
                    <h4 class="font-bold text-gray-400 text-xs uppercase">Active Plan</h4>
                    <p id="active-plan" class="text-2xl font-black">None</p>
                </div>
            </div>

            <h3 class="text-xl font-black mb-6 italic opacity-70">AVAILABLE PLANS</h3>
            <div class="space-y-4">
                <div onclick="buyPlan('Starter', 1500)" class="glass p-6 rounded-[2rem] flex justify-between items-center cursor-pointer hover:border-blue-500 transition">
                    <div>
                        <h4 class="font-black">Starter Growth</h4>
                        <p class="text-xs text-gray-500">Profit: 15% in 7 Days</p>
                    </div>
                    <div class="text-right">
                        <p class="font-black text-blue-500">₨ 1,500</p>
                        <p class="text-[10px] text-green-400">Low Risk</p>
                    </div>
                </div>
                <div onclick="buyPlan('Executive', 10000)" class="glass p-6 rounded-[2rem] flex justify-between items-center cursor-pointer hover:border-yellow-500 transition border-l-4 border-yellow-500">
                    <div>
                        <h4 class="font-black">Executive VIP</h4>
                        <p class="text-xs text-gray-500">Profit: 40% in 15 Days</p>
                    </div>
                    <div class="text-right">
                        <p class="font-black text-yellow-500">₨ 10,000</p>
                        <p class="text-[10px] text-green-400">High Return</p>
                    </div>
                </div>
            </div>
        </main>
    </section>

    <div id="dep-sheet" class="hidden fixed inset-0 bg-black/95 z-[100] flex items-end">
        <div class="w-full glass rounded-t-[3rem] p-10 animate-bounce-short">
            <h2 class="text-2xl font-black mb-6">FUND YOUR ACCOUNT</h2>
            <div class="space-y-4 mb-8">
                <div class="flex justify-between p-4 bg-white/5 rounded-2xl items-center">
                    <span>JazzCash (03705519562)</span>
                    <button onclick="alert('Copied!')" class="text-blue-500 text-xs font-bold">COPY</button>
                </div>
                <div class="flex justify-between p-4 bg-white/5 rounded-2xl items-center">
                    <span>EasyPaisa (03379827882)</span>
                    <button onclick="alert('Copied!')" class="text-green-500 text-xs font-bold">COPY</button>
                </div>
            </div>
            <a href="https://wa.me/923379827882" class="block w-full text-center bg-green-600 py-4 rounded-2xl font-black">SEND PROOF ON WHATSAPP</a>
            <button onclick="closeDep()" class="w-full py-4 text-gray-500 text-sm">Go Back</button>
        </div>
    </div>

    <script>
        let mode = 'login';
        let currentUser = JSON.parse(localStorage.getItem('userSession')) || null;

        // AUTH LOGIC
        function switchAuth(m) {
            mode = m;
            document.getElementById('login-tab').className = m === 'login' ? 'pb-2 flex-1 font-bold tab-active' : 'pb-2 flex-1 font-bold text-gray-600';
            document.getElementById('signup-tab').className = m === 'signup' ? 'pb-2 flex-1 font-bold tab-active' : 'pb-2 flex-1 font-bold text-gray-600';
            document.getElementById('auth-btn').innerText = m === 'login' ? 'LOGIN NOW' : 'CREATE ACCOUNT';
        }

        function handleAuth() {
            const email = document.getElementById('email').value;
            if(!email) return alert("Enter email!");
            
            currentUser = { email: email, balance: 0, plan: 'None' };
            localStorage.setItem('userSession', JSON.stringify(currentUser));
            showDashboard();
        }

        function showDashboard() {
            document.getElementById('auth-page').style.display = 'none';
            document.getElementById('dash-page').style.display = 'flex';
            document.getElementById('user-display').innerText = currentUser.email;
            document.getElementById('balanceText').innerText = "₨ " + currentUser.balance.toLocaleString();
            document.getElementById('active-plan').innerText = currentUser.plan;
        }

        function logout() {
            localStorage.removeItem('userSession');
            location.reload();
        }

        // DEPOSIT LOGIC
        function showDep() { document.getElementById('dep-sheet').classList.remove('hidden'); }
        function closeDep() { document.getElementById('dep-sheet').classList.add('hidden'); }

        function buyPlan(name, price) {
            if(currentUser.balance < price) {
                alert("Insufficient Balance! Please Deposit first.");
                showDep();
            } else {
                currentUser.balance -= price;
                currentUser.plan = name;
                localStorage.setItem('userSession', JSON.stringify(currentUser));
                showDashboard();
                alert(name + " Plan activated!");
            }
        }

        // AUTO-PROFIT SIMULATOR (Every 10 seconds)
        setInterval(() => {
            if(currentUser && currentUser.plan !== 'None') {
                currentUser.balance += Math.floor(Math.random() * 50);
                localStorage.setItem('userSession', JSON.stringify(currentUser));
                document.getElementById('balanceText').innerText = "₨ " + currentUser.balance.toLocaleString();
            }
        }, 10000);

        // Check if already logged in
        if(currentUser) showDashboard();

        // Admin Access: Ctrl + Shift + P to add 5000 PKR
        window.addEventListener('keydown', (e) => {
            if(e.ctrlKey && e.shiftKey && e.key === 'P') {
                currentUser.balance += 5000;
                localStorage.setItem('userSession', JSON.stringify(currentUser));
                showDashboard();
                alert("Admin Added 5000 PKR!");
            }
        });
    </script>
</body>
</html>
