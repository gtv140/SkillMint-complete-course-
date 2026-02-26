<html lang="en">
<head>
    <script src="https://cdn.tailwindcss.com"></script>
    <title>My Investment Dashboard</title>
</head>
<body class="bg-gray-100 min-h-screen">

    <div class="flex">
        <div class="w-64 bg-indigo-900 h-screen text-white p-6 hidden md:block">
            <h2 class="text-2xl font-bold mb-10">InvestPro</h2>
            <nav class="space-y-4">
                <a href="#" class="block py-2 px-4 bg-indigo-700 rounded">Dashboard</a>
                <a href="#" class="block py-2 px-4 hover:bg-indigo-700">My Portfolio</a>
                <a href="#" class="block py-2 px-4 hover:bg-indigo-700">Withdraw</a>
            </nav>
        </div>

        <div class="flex-1 p-8">
            <header class="flex justify-between items-center mb-8">
                <h1 class="text-3xl font-bold">Welcome back, User! 👋</h1>
                <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-200">
                    <span class="text-gray-500">Wallet Balance:</span>
                    <span class="text-2xl font-bold text-green-600">₹25,000</span>
                </div>
            </header>

            <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-10">
                <div class="bg-white p-6 rounded-xl shadow-sm border-l-4 border-blue-500">
                    <p class="text-sm text-gray-500 uppercase">Total Invested</p>
                    <h3 class="text-2xl font-bold">₹15,000</h3>
                </div>
                <div class="bg-white p-6 rounded-xl shadow-sm border-l-4 border-green-500">
                    <p class="text-sm text-gray-500 uppercase">Profit Earned</p>
                    <h3 class="text-2xl font-bold text-green-600">+ ₹1,200</h3>
                </div>
                <div class="bg-white p-6 rounded-xl shadow-sm border-l-4 border-purple-500">
                    <p class="text-sm text-gray-500 uppercase">Active Plans</p>
                    <h3 class="text-2xl font-bold">2</h3>
                </div>
            </div>

            <div class="bg-white p-8 rounded-2xl shadow-lg max-w-lg">
                <h3 class="text-xl font-bold mb-4">New Investment</h3>
                <div class="space-y-4">
                    <label class="block text-sm font-medium">Select Amount</label>
                    <input type="number" placeholder="Enter Amount (min ₹500)" class="w-full p-3 border rounded-lg focus:ring-2 focus:ring-indigo-500 outline-none">
                    
                    <label class="block text-sm font-medium">Choose Plan</label>
                    <select class="w-full p-3 border rounded-lg">
                        <option>Starter (10% Profit - 15 Days)</option>
                        <option>Premium (25% Profit - 30 Days)</option>
                        <option>Gold (50% Profit - 90 Days)</option>
                    </select>

                    <button class="w-full bg-indigo-600 text-white py-3 rounded-lg font-bold hover:bg-indigo-700 transition">Invest Now</button>
                </div>
            </div>
        </div>
    </div>

</body>
</html>
