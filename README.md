<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <title>WealthWise Unlimited | Professional Tiers</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@400;700;900&display=swap');
        body { font-family: 'Outfit', sans-serif; background: #020617; color: white; scroll-behavior: smooth; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.05); }
        .plan-card:hover { border-color: #3b82f6; transform: translateY(-5px); transition: all 0.3s ease; }
        ::-webkit-scrollbar { width: 5px; }
        ::-webkit-scrollbar-thumb { background: #1e293b; border-radius: 10px; }
    </style>
</head>
<body>

    <nav class="p-4 flex justify-between items-center glass sticky top-0 z-[100]">
        <h1 class="text-xl font-black text-blue-500 italic">WEALTHWISE <span class="text-white opacity-50 text-[10px]">ULTIMATE</span></h1>
        <div id="nav-user" class="hidden flex items-center gap-4">
            <span id="user-name-tag" class="text-xs font-bold text-blue-400"></span>
            <button onclick="logout()" class="text-[10px] bg-red-600/20 text-red-500 px-3 py-1 rounded-full">LOGOUT</button>
        </div>
    </nav>

    <section id="auth-screen" class="min-h-[90vh] flex items-center justify-center p-6">
        <div class="glass w-full max-w-md p-10 rounded-[3rem] text-center">
            <h2 class="text-4xl font-black mb-8 text-white uppercase italic">Welcome</h2>
            <input type="text" id="auth-name" placeholder="Username" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-4 outline-none focus:border-blue-500">
            <button onclick="login()" class="w-full bg-blue-600 py-4 rounded-2xl font-black hover:bg-blue-500 shadow-xl shadow-blue-900/40">START INVESTING</button>
        </div>
    </section>

    <section id="dash-screen" class="hidden">
        <main class="max-w-6xl mx-auto p-6">
            
            <div class="grid lg:grid-cols-3 gap-6 mb-10">
                <div class="lg:col-span-2 glass p-10 rounded-[3rem] border-l-8 border-blue-600 shadow-2xl">
                    <p class="text-gray-500 text-xs font-bold tracking-widest uppercase">Total Balance</p>
                    <h2 class="text-6xl font-black mt-2 tracking-tighter" id="bal">₨ 0</h2>
                    <div class="mt-8 flex gap-4">
                        <button onclick="showPop('dep')" class="bg-blue-600 px-10 py-4 rounded-2xl font-black text-sm hover:scale-105 transition">DEPOSIT</button>
                        <button onclick="showPop('calc')" class="bg-white/5 border border-white/10 px-10 py-4 rounded-2xl font-black text-sm">CALCULATOR</button>
                    </div>
                </div>
                <div class="space-y-4">
                    <div class="glass p-6 rounded-3xl border-l-4 border-green-500">
                        <p class="text-gray-500 text-[10px] font-bold">TOTAL PROFIT</p>
                        <p class="text-2xl font-black text-green-400" id="profit-text">₨ 0</p>
                    </div>
                    <div class="glass p-6 rounded-3xl border-l-4 border-purple-500">
                        <p class="text-gray-500 text-[10px] font-bold">REFERRAL BONUS</p>
                        <p class="text-2xl font-black">₨ 500</p>
                    </div>
                </div>
            </div>

            <h3 class="text-2xl font-black mb-8 italic uppercase tracking-tighter flex items-center gap-2">
                <span class="w-8 h-1 bg-blue-500"></span> Investment Tiers
            </h3>
            
            <div id="plans-container" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6 mb-20">
                </div>
        </main>
    </section>

    <div id="dep-pop" class="hidden fixed inset-0 bg-black/95 z-[200] flex items-center justify-center p-6">
        <div class="glass w-full max-w-sm p-8 rounded-[2rem] text-center border border-blue-500/30">
            <h2 class="text-xl font-black mb-6">FUNDING DETAILS</h2>
            <div class="space-y-4 text-left mb-6">
                <div class="p-4 bg-white/5 rounded-2xl border border-white/5">
                    <p class="text-[10px] text-red-500 font-bold">JAZZCASH</p>
                    <p class="text-lg font-black tracking-widest text-white">03705519562</p>
                </div>
                <div class="p-4 bg-white/5 rounded-2xl border border-white/5">
                    <p class="text-[10px] text-green-500 font-bold">EASYPAISA</p>
                    <p class="text-lg font-black tracking-widest text-white">03379827882</p>
                </div>
            </div>
            <a href="https://wa.me/923379827882" class="block w-full bg-green-600 py-4 rounded-2xl font-black text-sm mb-4">WHATSAPP SCREENSHOT</a>
            <button onclick="showPop('hide')" class="text-gray-500 text-xs">GO BACK</button>
        </div>
    </div>

    <script>
        // 15 PLANS DATA
        const plans = [
            { name: "Starter", price: 500, profit: "10% / 3 Days", color: "blue" },
            { name: "Basic", price: 1500, profit: "15% / 5 Days", color: "blue" },
            { name: "Silver", price: 3000, profit: "20% / 7 Days", color: "blue" },
            { name: "Pro", price: 5000, profit: "25% / 10 Days", color: "indigo" },
            { name: "Expert", price: 8000, profit: "30% / 12 Days", color: "indigo" },
            { name: "VIP 1", price: 15000, profit: "35% / 15 Days", color: "yellow" },
            { name: "VIP 2", price: 25000, profit: "40% / 20 Days", color: "yellow" },
            { name: "Gold", price: 40000, profit: "45% / 25 Days", color: "yellow" },
            { name: "Premium", price: 60000, profit: "50% / 30 Days", color: "purple" },
            { name: "Wealth", price: 100000, profit: "60% / 40 Days", color: "purple" },
            { name: "Diamond", price: 150000, profit: "75% / 45 Days", color: "purple" },
            { name: "Master", price: 250000, profit: "90% / 50 Days", color: "red" },
            { name: "Ultra", price: 400000, profit: "110% / 60 Days", color: "red" },
            { name: "Legendary", price: 700000, profit: "150% / 80 Days", color: "red" },
            { name: "Empire", price: 1000000, profit: "200% / 100 Days", color: "red" }
        ];

        let user = JSON.parse(localStorage.getItem('ww_ultimate')) || null;

        function login() {
            const n = document.getElementById('auth-name').value;
            if(!n) return alert("Enter Name!");
            user = { name: n, balance: 0, profit: 0, plans_active: [] };
            save();
            render();
        }

        function render() {
            if(!user) return;
            document.getElementById('auth-screen').classList.add('hidden');
            document.getElementById('dash-screen').classList.remove('hidden');
            document.getElementById('nav-user').classList.remove('hidden');
            document.getElementById('user-name-tag').innerText = "HELLO, " + user.name.toUpperCase();
            document.getElementById('bal').innerText = "₨ " + user.balance.toLocaleString();
            document.getElementById('profit-text').innerText = "₨ " + user.profit.toLocaleString();

            // Inject Plans
            const container = document.getElementById('plans-container');
            container.innerHTML = '';
            plans.forEach((p, i) => {
                container.innerHTML += `
                    <div onclick="buyPlan(${i})" class="glass p-8 rounded-[2.5rem] plan-card border-l-4 border-${p.color}-500 cursor-pointer">
                        <h4 class="font-black text-xl mb-1">${p.name}</h4>
                        <p class="text-green-400 font-bold mb-4">${p.profit}</p>
                        <div class="flex justify-between items-center">
                            <span class="text-2xl font-black">₨ ${p.price.toLocaleString()}</span>
                            <span class="bg-white/5 px-3 py-1 rounded-full text-[10px] text-gray-500 uppercase font-bold tracking-tighter italic">Join Tier</span>
                        </div>
                    </div>
                `;
            });
        }

        function buyPlan(index) {
            const p = plans[index];
            if(user.balance < p.price) {
                alert("Insufficient funds! Please deposit first.");
                showPop('dep');
            } else {
                user.balance -= p.price;
                user.plans_active.push(p.name);
                alert(p.name + " Plan activated successfully!");
                save();
                render();
            }
        }

        function save() { localStorage.setItem('ww_ultimate', JSON.stringify(user)); }
        function logout() { localStorage.removeItem('ww_ultimate'); location.reload(); }
        function showPop(t) { 
            if(t === 'dep') document.getElementById('dep-pop').classList.remove('hidden'); 
            else if(t === 'hide') document.getElementById('dep-pop').classList.add('hidden');
            else if(t === 'calc') alert("Calculator: (Amount x Profit Rate) / Days. Comming soon!");
        }

        // ADMIN HOTKEY: Ctrl+Shift+Q to add 1 Lakh PKR
        window.addEventListener('keydown', (e) => {
            if(e.ctrlKey && e.shiftKey && e.key === 'Q') {
                user.balance += 100000;
                save();
                render();
                alert("Admin Added 100,000 PKR!");
            }
        });

        if(user) render();
    </script>
</body>
</html>
