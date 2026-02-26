<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <title>WealthWise Global | Premium Investment Platform</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;800&display=swap');
        body { font-family: 'Inter', sans-serif; background: #0f172a; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(12px); border: 1px solid rgba(255,255,255,0.1); }
        .neon-border { border-left: 4px solid #3b82f6; }
        .animate-slide { animation: slide 20s linear infinite; }
        @keyframes slide { from { transform: translateX(100%); } to { transform: translateX(-100%); } }
    </style>
</head>
<body class="text-white overflow-x-hidden">

    <div class="bg-blue-600 py-1 overflow-hidden whitespace-nowrap text-[10px] font-bold uppercase tracking-widest">
        <div class="animate-slide inline-block">
            🚀 Bitcoin hitting new highs! • PKR/USD: 278.40 • Gold Stable • UAE Real Estate Growth • Invest now for 25% Monthly Returns!
        </div>
    </div>

    <nav class="p-5 flex justify-between items-center glass sticky top-0 z-[100]">
        <h1 class="text-2xl font-black italic text-blue-500">WEALTH<span class="text-white">WISE</span></h1>
        <div class="flex items-center gap-4">
            <select id="currSelect" onchange="syncCurrency()" class="bg-gray-800 text-xs p-2 rounded border border-gray-600">
                <option value="PKR">PKR (₨)</option>
                <option value="USD">USD ($)</option>
                <option value="AED">AED (د.إ)</option>
                <option value="INR">INR (₹)</option>
            </select>
            <button onclick="toggleAdmin()" class="opacity-20 hover:opacity-100 text-[10px]">ADMIN</button>
        </div>
    </nav>

    <main class="max-w-6xl mx-auto p-6">
        
        <div class="grid lg:grid-cols-3 gap-6 mt-4">
            <div class="lg:col-span-2 glass p-8 rounded-[2.5rem] relative overflow-hidden">
                <div class="absolute top-0 right-0 p-4 opacity-10 text-6xl font-black">ACTIVE</div>
                <p class="text-blue-400 font-semibold mb-2 uppercase text-xs tracking-widest">Total Portfolio</p>
                <h2 class="text-5xl font-black mb-6" id="mainBal">₨ 150,000</h2>
                <div class="flex gap-4">
                    <button onclick="openModal('deposit')" class="bg-blue-600 hover:bg-blue-700 px-8 py-3 rounded-2xl font-bold shadow-lg transition-all">Add Cash</button>
                    <button onclick="openModal('withdraw')" class="bg-gray-800 hover:bg-gray-700 px-8 py-3 rounded-2xl font-bold border border-gray-600 transition-all">Withdraw</button>
                </div>
            </div>

            <div class="space-y-4">
                <div class="glass p-6 rounded-3xl neon-border">
                    <p class="text-gray-400 text-xs">Profit Earned</p>
                    <h3 class="text-2xl font-bold text-green-400">+ <span id="profitAmt">₨ 12,400</span></h3>
                </div>
                <div class="glass p-6 rounded-3xl border-l-4 border-purple-500">
                    <p class="text-gray-400 text-xs">Total Withdrawals</p>
                    <h3 class="text-2xl font-bold">₨ 5,000</h3>
                </div>
            </div>
        </div>

        <h3 class="text-2xl font-black mt-12 mb-8 uppercase italic">Premium Plans</h3>
        <div class="grid md:grid-cols-3 gap-8">
            <div class="glass p-8 rounded-[2rem] hover:scale-105 transition-transform border-b-4 border-blue-500">
                <h4 class="text-xl font-bold">Standard</h4>
                <div class="my-4">
                    <span class="text-4xl font-black">12%</span>
                    <span class="text-gray-400">/ 10 Days</span>
                </div>
                <ul class="text-sm text-gray-400 space-y-2 mb-8">
                    <li>✓ Min: <span class="minAmt">₨ 1,000</span></li>
                    <li>✓ 24/7 Support</li>
                    <li>✓ Instant Payout</li>
                </ul>
                <button onclick="invest('Standard')" class="w-full py-4 bg-blue-600/20 text-blue-400 rounded-2xl font-bold hover:bg-blue-600 hover:text-white transition-all">Select Plan</button>
            </div>

            <div class="glass p-8 rounded-[2rem] border-2 border-blue-500 relative">
                <div class="absolute -top-4 left-1/2 -translate-x-1/2 bg-blue-500 text-[10px] font-black px-4 py-1 rounded-full uppercase">Most Popular</div>
                <h4 class="text-xl font-bold">VIP Growth</h4>
                <div class="my-4">
                    <span class="text-4xl font-black">30%</span>
                    <span class="text-gray-400">/ 20 Days</span>
                </div>
                <ul class="text-sm text-gray-400 space-y-2 mb-8">
                    <li>✓ Min: <span class="minAmt">₨ 10,000</span></li>
                    <li>✓ Personal Manager</li>
                    <li>✓ Bonus Rewards</li>
                </ul>
                <button onclick="invest('VIP')" class="w-full py-4 bg-blue-600 rounded-2xl font-bold hover:bg-blue-700 shadow-xl shadow-blue-900/40">Select Plan</button>
            </div>

            <div class="glass p-8 rounded-[2rem] border-b-4 border-purple-500">
                <h4 class="text-xl font-bold">Elite Fortune</h4>
                <div class="my-4">
                    <span class="text-4xl font-black">60%</span>
                    <span class="text-gray-400">/ 45 Days</span>
                </div>
                <ul class="text-sm text-gray-400 space-y-2 mb-8">
                    <li>✓ Min: <span class="minAmt">₨ 50,000</span></li>
                    <li>✓ Compounding Interest</li>
                    <li>✓ Luxury Rewards</li>
                </ul>
                <button onclick="invest('Elite')" class="w-full py-4 bg-purple-600/20 text-purple-400 rounded-2xl font-bold hover:bg-purple-600 hover:text-white transition-all">Select Plan</button>
            </div>
        </div>
    </main>

    <div id="modal" class="hidden fixed inset-0 bg-black/90 z-[200] flex items-center justify-center p-4">
        <div class="glass max-w-md w-full p-8 rounded-[2.5rem] border border-blue-500/30">
            <h2 id="modalTitle" class="text-2xl font-bold mb-4">Deposit</h2>
            <div class="space-y-4">
                <input type="number" id="modalInput" placeholder="Enter Amount" class="w-full bg-gray-900 p-4 rounded-xl border border-gray-700 outline-none focus:border-blue-500">
                <p id="modalNote" class="text-xs text-gray-500 italic">Submit the request, our team will contact you for payment details.</p>
                <button onclick="submitAction()" class="w-full bg-blue-600 py-4 rounded-xl font-bold">Submit Request</button>
                <button onclick="closeModal()" class="w-full text-gray-400 text-sm">Cancel</button>
            </div>
        </div>
    </div>

    <div id="adminPanel" class="hidden fixed inset-0 bg-gray-950 z-[300] p-10 overflow-y-auto">
        <div class="flex justify-between mb-10">
            <h2 class="text-4xl font-black text-red-600">ADMIN CONTROL</h2>
            <button onclick="toggleAdmin()" class="bg-red-600 px-6 py-2 rounded-lg">Exit Admin</button>
        </div>
        <div class="grid md:grid-cols-2 gap-10">
            <div class="glass p-6 rounded-2xl">
                <h3 class="text-xl mb-4 font-bold border-b border-gray-800 pb-2">Pending Deposits</h3>
                <div class="space-y-3">
                    <div class="flex justify-between items-center bg-gray-900 p-4 rounded-lg">
                        <span>User_442 (₨ 25,000)</span>
                        <button class="bg-green-600 px-4 py-1 rounded text-xs">Approve</button>
                    </div>
                </div>
            </div>
            <div class="glass p-6 rounded-2xl">
                <h3 class="text-xl mb-4 font-bold border-b border-gray-800 pb-2">User Stats</h3>
                <p>Total Users: 1,240</p>
                <p>Total Assets: ₨ 4.2M</p>
            </div>
        </div>
    </div>

    <script>
        const data = {
            rates: { PKR: 1, USD: 0.0036, AED: 0.013, INR: 0.30 },
            symbols: { PKR: "₨", USD: "$", AED: "د.إ", INR: "₹" },
            bal: 150000,
            profit: 12400
        };

        function syncCurrency() {
            const c = document.getElementById('currSelect').value;
            const sym = data.symbols[c];
            const rate = data.rates[c];

            document.getElementById('mainBal').innerText = sym + " " + (data.bal * rate).toLocaleString();
            document.getElementById('profitAmt').innerText = sym + " " + (data.profit * rate).toLocaleString();
            
            // Update Plan Mins
            const mins = { Standard: 1000, VIP: 10000, Elite: 50000 };
            const minEls = document.querySelectorAll('.minAmt');
            minEls[0].innerText = sym + " " + (1000 * rate).toLocaleString();
            minEls[1].innerText = sym + " " + (10000 * rate).toLocaleString();
            minEls[2].innerText = sym + " " + (50000 * rate).toLocaleString();
        }

        function openModal(type) {
            document.getElementById('modal').classList.remove('hidden');
            document.getElementById('modalTitle').innerText = type.toUpperCase();
        }

        function closeModal() { document.getElementById('modal').classList.add('hidden'); }

        function submitAction() {
            alert("Request Registered! Admin will update your balance within 2 hours.");
            closeModal();
        }

        function invest(plan) {
            if(confirm("Join " + plan + " plan?")) {
                alert("Investment started successfully!");
            }
        }

        function toggleAdmin() {
            const p = document.getElementById('adminPanel');
            p.classList.toggle('hidden');
        }

        // Secret Admin Key: Ctrl+Shift+A
        window.addEventListener('keydown', (e) => {
            if(e.ctrlKey && e.shiftKey && e.key === 'A') {
                toggleAdmin();
            }
        });

        syncCurrency(); // Initialize
    </script>
</body>
</html>
