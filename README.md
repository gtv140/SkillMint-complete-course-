<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <title>WealthWise Global | Invest & Grow</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&display=swap');
        body { font-family: 'Inter', sans-serif; background: #0b0f1a; color: white; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(15px); border: 1px solid rgba(255,255,255,0.1); }
        .btn-grad { background: linear-gradient(90deg, #3b82f6, #2563eb); }
    </style>
</head>
<body>

    <nav class="p-5 flex justify-between items-center glass sticky top-0 z-50">
        <h1 class="text-2xl font-black text-blue-500 italic uppercase">WealthWise</h1>
        <select id="curr" onchange="updateUI()" class="bg-gray-800 text-xs p-2 rounded border border-gray-600 outline-none">
            <option value="PKR">PKR (₨)</option>
            <option value="USD">USD ($)</option>
            <option value="AED">AED (د.إ)</option>
        </select>
    </nav>

    <main class="max-w-4xl mx-auto p-6 mt-4">
        <div class="glass p-10 rounded-[2.5rem] border-l-8 border-blue-600 shadow-2xl mb-10">
            <p class="text-gray-400 text-sm font-bold uppercase tracking-widest">Active Balance</p>
            <h2 class="text-6xl font-black mt-2" id="mainBal">₨ 0.00</h2>
            <div class="mt-8 flex gap-4">
                <button onclick="openDeposit()" class="btn-grad px-10 py-4 rounded-2xl font-black shadow-lg hover:scale-105 transition">DEPOSIT</button>
                <button onclick="alert('Withdrawals are processed every Sunday.')" class="bg-white/5 border border-white/10 px-10 py-4 rounded-2xl font-black hover:bg-white/10 transition">WITHDRAW</button>
            </div>
        </div>

        <h3 class="text-2xl font-black mb-6 italic">INVESTMENT PLANS</h3>
        <div class="grid md:grid-cols-2 gap-6">
            <div class="glass p-8 rounded-3xl border-b-4 border-blue-500">
                <h4 class="text-xl font-bold">Silver Plan</h4>
                <p class="text-3xl font-black text-green-400 my-2">15% Profit</p>
                <p class="text-gray-400 text-sm">Duration: 7 Days</p>
                <button onclick="openDeposit()" class="w-full mt-6 py-3 bg-gray-800 rounded-xl font-bold hover:bg-blue-600 transition">Start Now</button>
            </div>
            <div class="glass p-8 rounded-3xl border-b-4 border-yellow-500">
                <h4 class="text-xl font-bold">Gold VIP</h4>
                <p class="text-3xl font-black text-green-400 my-2">40% Profit</p>
                <p class="text-gray-400 text-sm">Duration: 15 Days</p>
                <button onclick="openDeposit()" class="w-full mt-6 py-3 bg-gray-800 rounded-xl font-bold hover:bg-yellow-600 transition">Start Now</button>
            </div>
        </div>
    </main>

    <div id="depositModal" class="hidden fixed inset-0 bg-black/95 flex items-center justify-center p-4 z-[100]">
        <div class="glass max-w-md w-full p-8 rounded-[2rem] border-blue-500/50 border shadow-2xl">
            <h2 class="text-2xl font-black mb-6">SELECT PAYMENT METHOD</h2>
            
            <div class="space-y-4">
                <div class="bg-red-600/10 border border-red-600/30 p-4 rounded-2xl flex justify-between items-center">
                    <div>
                        <p class="text-red-500 font-bold text-sm uppercase">JazzCash</p>
                        <p class="text-lg font-black tracking-widest">03705519562</p>
                    </div>
                    <button onclick="copy('03705519562')" class="text-xs bg-red-600 px-3 py-1 rounded-lg">Copy</button>
                </div>

                <div class="bg-green-600/10 border border-green-600/30 p-4 rounded-2xl flex justify-between items-center">
                    <div>
                        <p class="text-green-500 font-bold text-sm uppercase">EasyPaisa</p>
                        <p class="text-lg font-black tracking-widest">03379827882</p>
                    </div>
                    <button onclick="copy('03379827882')" class="text-xs bg-green-600 px-3 py-1 rounded-lg">Copy</button>
                </div>

                <div class="mt-8">
                    <p class="text-xs text-gray-400 mb-4 italic">Payment bhenjne ke baad screenshot niche button par click karke WhatsApp karein.</p>
                    <a href="https://wa.me/923379827882?text=I%20have%20sent%20payment.%20Please%20approve%20my%20deposit." target="_blank" class="block text-center w-full bg-green-600 py-4 rounded-xl font-black text-white hover:bg-green-700 transition">SEND SCREENSHOT (WHATSAPP)</a>
                    <button onclick="closeDeposit()" class="w-full mt-4 text-gray-500 text-sm">Close</button>
                </div>
            </div>
        </div>
    </div>

    <script>
        const rates = { PKR: 1, USD: 0.0036, AED: 0.013 };
        const syms = { PKR: "₨", USD: "$", AED: "د.إ" };
        let bal = 0;

        function updateUI() {
            const c = document.getElementById('curr').value;
            document.getElementById('mainBal').innerText = syms[c] + " " + (bal * rates[c]).toLocaleString();
        }

        function openDeposit() { document.getElementById('depositModal').classList.remove('hidden'); }
        function closeDeposit() { document.getElementById('depositModal').classList.add('hidden'); }
        
        function copy(text) {
            navigator.clipboard.writeText(text);
            alert("Number copied: " + text);
        }

        // Admin Secret: Ctrl + Shift + Enter to add fake balance for demo
        window.addEventListener('keydown', (e) => {
            if(e.ctrlKey && e.shiftKey && e.key === 'Enter') {
                bal = prompt("Enter new balance amount:");
                updateUI();
            }
        });
    </script>
</body>
</html>
