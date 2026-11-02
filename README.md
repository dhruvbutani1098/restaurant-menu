<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Lush Bistro | Kitchen Command Center</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Plus Jakarta Sans', sans-serif; }
        @keyframes pulse-ring {
            0% { transform: scale(0.95); opacity: 0.8; }
            50% { transform: scale(1.05); opacity: 1; }
            100% { transform: scale(0.95); opacity: 0.8; }
        }
        .alert-pulse { animation: pulse-ring 1.5s infinite; }
    </style>
</head>
<body class="bg-slate-950 text-slate-100 antialiased min-h-screen pb-12">

    <!-- Top Navbar -->
    <header class="bg-slate-900 border-b border-slate-800 px-6 py-4 flex justify-between items-center sticky top-0 z-40">
        <div class="flex items-center gap-3">
            <div class="bg-emerald-500/10 text-emerald-400 p-2.5 rounded-xl border border-emerald-500/20">
                <i class="fa-solid fa-utensils text-base"></i>
            </div>
            <div>
                <h1 class="font-black text-base sm:text-lg text-white tracking-tight">Lush Bistro • Kitchen Command</h1>
                <p class="text-[11px] text-slate-400">Live payment tracking & queue management active</p>
            </div>
        </div>
        <div class="flex gap-2">
            <button onclick="switchTab('orders')" id="btnOrdersTab" class="bg-emerald-600 text-white px-4 py-2.5 rounded-xl text-xs font-bold transition shadow-md">Orders & Queue</button>
            <button onclick="switchTab('menu')" id="btnMenuTab" class="bg-slate-800 text-slate-300 px-4 py-2.5 rounded-xl text-xs font-bold transition hover:bg-slate-700">Menu Manager</button>
            <button onclick="switchTab('settings')" id="btnSettingsTab" class="bg-slate-800 text-slate-300 px-4 py-2.5 rounded-xl text-xs font-bold transition hover:bg-slate-700">UPI Settings</button>
        </div>
    </header>

    <!-- Main Workspace -->
    <main class="max-w-7xl mx-auto p-6 lg:p-8">
        
        <!-- ORDERS & QUEUE VIEW -->
        <section id="ordersView" class="space-y-6">
            <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4 bg-slate-900 p-5 rounded-3xl border border-slate-800">
                <div>
                    <h2 class="text-lg font-bold flex items-center gap-2">
                        <i class="fa-solid fa-bell text-emerald-400"></i> Live Kitchen Queue & Payment Records
                    </h2>
                    <p class="text-xs text-slate-400 mt-1">Monitor pending/paid statuses and clear completed orders.</p>
                </div>
                <div class="flex items-center gap-3">
                    <span class="bg-emerald-500/10 border border-emerald-500/20 text-emerald-400 text-xs px-3.5 py-2 rounded-xl font-bold">
                        Paid: <strong id="countPaid">0</strong>
                    </span>
                    <span class="bg-rose-500/10 border border-rose-500/20 text-rose-400 text-xs px-3.5 py-2 rounded-xl font-bold">
                        Pending: <strong id="countPending">0</strong>
                    </span>
                </div>
            </div>

            <!-- Cash Requests Alert Banner -->
            <div id="cashRequestsContainer" class="space-y-2"></div>

            <div id="ordersGrid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                <!-- Dynamically loaded orders -->
            </div>
        </section>

        <!-- MENU MANAGER VIEW -->
        <section id="menuView" class="hidden space-y-6">
            <div class="flex justify-between items-center">
                <h2 class="text-lg font-bold flex items-center gap-2">
                    <i class="fa-solid fa-book-open text-teal-400"></i> Live Menu Configuration
                </h2>
                <button onclick="openAddDishModal()" class="bg-emerald-600 hover:bg-emerald-700 text-white text-xs font-bold px-4.5 py-2.5 rounded-xl transition flex items-center gap-2 shadow-md">
                    <i class="fa-solid fa-plus"></i> Add New Dish
                </button>
            </div>

            <div id="adminMenuGrid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6"></div>
        </section>

        <!-- UPI SETTINGS VIEW -->
        <section id="settingsView" class="hidden space-y-6 max-w-lg">
            <h2 class="text-lg font-bold flex items-center gap-2">
                <i class="fa-solid fa-qrcode text-emerald-400"></i> Configure Restaurant UPI ID
            </h2>
            <div class="bg-slate-900 border border-slate-800 p-6 rounded-3xl space-y-4 shadow-xl">
                <div>
                    <label class="block text-xs font-semibold text-slate-400 mb-1">Restaurant UPI ID (for Customer Payments)</label>
                    <input type="text" id="adminUpiInput" placeholder="e.g. lushbistro@okhdfcbank" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-4 py-3 text-sm text-white focus:outline-none focus:border-emerald-500">
                </div>
                <button onclick="saveUpiId()" class="w-full bg-emerald-600 hover:bg-emerald-700 text-white font-bold py-3 rounded-xl text-xs shadow-md">Save UPI ID</button>
            </div>
        </section>

    </main>

    <!-- NEW ORDER POPUP MODAL -->
    <div id="newOrderModal" class="fixed inset-0 bg-black/80 z-50 hidden flex items-center justify-center p-4 backdrop-blur-md">
        <div class="bg-slate-900 border border-emerald-500/50 rounded-3xl p-8 max-w-md w-full text-center shadow-2xl alert-pulse">
            <div class="w-20 h-20 bg-emerald-500/20 text-emerald-400 rounded-full flex items-center justify-center mx-auto mb-4 text-3xl">
                <i class="fa-solid fa-bell-concierge"></i>
            </div>
            <span class="bg-emerald-500/20 text-emerald-300 text-xs font-bold px-3 py-1 rounded-full uppercase">NEW QR ORDER RECEIVED</span>
            <h3 id="popupTableTitle" class="text-2xl font-bold text-white mt-3">Table #1 Placed an Order!</h3>
            <p class="text-slate-400 text-xs mt-1 mb-6">Kitchen staff, please check the queue and begin food preparation.</p>
            <button onclick="closeNewOrderModal()" class="w-full bg-emerald-600 hover:bg-emerald-700 text-white font-bold py-3.5 rounded-2xl shadow-lg text-xs">Acknowledge & View</button>
        </div>
    </div>

    <!-- WAITER CALL POPUP MODAL -->
    <div id="waiterCallModal" class="fixed inset-0 bg-black/80 z-50 hidden flex items-center justify-center p-4 backdrop-blur-md">
        <div class="bg-slate-900 border border-amber-500/50 rounded-3xl p-8 max-w-md w-full text-center shadow-2xl alert-pulse">
            <div class="w-20 h-20 bg-amber-500/20 text-amber-400 rounded-full flex items-center justify-center mx-auto mb-4 text-3xl">
                <i class="fa-solid fa-bell"></i>
            </div>
            <span class="bg-amber-500/20 text-amber-300 text-xs font-bold px-3 py-1 rounded-full uppercase">WAITER REQUESTED</span>
            <h3 id="popupWaiterTitle" class="text-2xl font-bold text-white mt-3">Table #1 is Calling a Waiter!</h3>
            <p class="text-slate-400 text-xs mt-1 mb-6">Customer needs assistance or query resolution at their table.</p>
            <button onclick="closeWaiterModal()" class="w-full bg-amber-600 hover:bg-amber-700 text-white font-bold py-3.5 rounded-2xl shadow-lg text-xs">Attend Table</button>
        </div>
    </div>

    <!-- ADD DISH MODAL -->
    <div id="addDishModal" class="fixed inset-0 bg-black/70 z-50 hidden flex items-center justify-center p-4 backdrop-blur-md">
        <div class="bg-slate-900 border border-slate-800 rounded-3xl p-6 max-w-md w-full shadow-2xl">
            <h3 class="text-base font-bold text-white mb-4">Add New Menu Item (JPG/PNG Upload)</h3>
            <form id="addDishForm" onsubmit="saveNewDish(event)" class="space-y-4">
                <div>
                    <label class="block text-xs font-semibold text-slate-400 mb-1">Dish Name</label>
                    <input type="text" id="dishName" required class="w-full bg-slate-950 border border-slate-800 rounded-xl px-4 py-2.5 text-sm text-white focus:outline-none focus:border-emerald-500">
                </div>
                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-xs font-semibold text-slate-400 mb-1">Price (₹)</label>
                        <input type="number" id="dishPrice" required class="w-full bg-slate-950 border border-slate-800 rounded-xl px-4 py-2.5 text-sm text-white focus:outline-none focus:border-emerald-500">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-400 mb-1">Category</label>
                        <select id="dishCategory" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-4 py-2.5 text-sm text-white focus:outline-none focus:border-emerald-500">
                            <option value="Starter">Starter</option>
                            <option value="Main Course">Main Course</option>
                            <option value="Beverage">Beverage</option>
                        </select>
                    </div>
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-400 mb-1">Upload Dish Image (JPG/PNG)</label>
                    <input type="file" id="dishImageFile" accept="image/jpeg, image/png, image/jpg" required class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-xs text-slate-300 file:mr-3 file:py-1.5 file:px-3 file:rounded-lg file:border-0 file:text-xs file:font-semibold file:bg-emerald-600 file:text-white hover:file:bg-emerald-700">
                </div>
                <div class="flex gap-3 pt-2">
                    <button type="button" onclick="closeAddDishModal()" class="flex-1 bg-slate-800 hover:bg-slate-700 text-white font-bold py-3 rounded-xl text-xs">Cancel</button>
                    <button type="submit" class="flex-1 bg-emerald-600 hover:bg-emerald-700 text-white font-bold py-3 rounded-xl text-xs">Save Dish</button>
                </div>
            </form>
        </div>
    </div>

    <script>
        window.onload = function() {
            renderOrders();
            renderAdminMenu();
            renderCashRequests();

            const savedUpi = localStorage.getItem('restaurant_upi_id');
            if (savedUpi) document.getElementById('adminUpiInput').value = savedUpi;

            window.addEventListener('storage', function(e) {
                if (e.key === 'restaurant_orders') {
                    renderOrders();
                    const orders = JSON.parse(e.newValue) || [];
                    if (orders.length > 0) {
                        const latest = orders[0];
                        document.getElementById('popupTableTitle').innerText = `Table #${latest.table} Placed an Order!`;
                        document.getElementById('newOrderModal').classList.remove('hidden');
                        playBeep();
                    }
                }
                if (e.key === 'restaurant_waiter_calls') {
                    const calls = JSON.parse(e.newValue) || [];
                    if (calls.length > 0) {
                        const latest = calls[0];
                        document.getElementById('popupWaiterTitle').innerText = `Table #${latest.table} is Calling a Waiter! 🛎️`;
                        document.getElementById('waiterCallModal').classList.remove('hidden');
                        playBeep();
                    }
                }
                if (e.key === 'restaurant_cash_requests') {
                    renderCashRequests();
                    playBeep();
                }
            });
        };

        function saveUpiId() {
            const upi = document.getElementById('adminUpiInput').value.trim();
            if (!upi) return alert('Please enter a valid UPI ID');
            localStorage.setItem('restaurant_upi_id', upi);
            alert('UPI ID successfully updated!');
        }

        function renderCashRequests() {
            const container = document.getElementById('cashRequestsContainer');
            const requests = JSON.parse(localStorage.getItem('restaurant_cash_requests')) || [];
            container.innerHTML = '';

            if (requests.length > 0) {
                requests.forEach((req, idx) => {
                    container.innerHTML += `
                        <div class="bg-amber-500/10 border border-amber-500/30 p-4 rounded-2xl flex justify-between items-center">
                            <div>
                                <span class="bg-amber-500 text-slate-950 font-bold text-[10px] px-2.5 py-0.5 rounded-full uppercase">Cash Payment Requested</span>
                                <h4 class="font-bold text-sm text-white mt-1">Table #${req.table} wants to pay ₹${req.amount} in Cash.</h4>
                                <p class="text-[10px] text-slate-400">${req.time}</p>
                            </div>
                            <button onclick="resolveCashReq(${idx})" class="bg-amber-500 hover:bg-amber-600 text-slate-950 font-bold text-xs px-4 py-2 rounded-xl">Mark Collected</button>
                        </div>
                    `;
                });
            }
        }

        function resolveCashReq(idx) {
            let requests = JSON.parse(localStorage.getItem('restaurant_cash_requests')) || [];
            requests.splice(idx, 1);
            localStorage.setItem('restaurant_cash_requests', JSON.stringify(requests));
            renderCashRequests();
        }

        function playBeep() {
            const ctx = new (window.AudioContext || window.webkitAudioContext)();
            const osc = ctx.createOscillator();
            const gain = ctx.createGain();
            osc.connect(gain);
            gain.connect(ctx.destination);
            osc.type = 'sine';
            osc.frequency.value = 587.33;
            gain.gain.setValueAtTime(0.2, ctx.currentTime);
            osc.start();
            osc.stop(ctx.currentTime + 0.3);
        }

        function closeNewOrderModal() { document.getElementById('newOrderModal').classList.add('hidden'); }
        function closeWaiterModal() { document.getElementById('waiterCallModal').classList.add('hidden'); }

        function switchTab(tab) {
            const ordersView = document.getElementById('ordersView');
            const menuView = document.getElementById('menuView');
            const settingsView = document.getElementById('settingsView');
            
            const btnOrders = document.getElementById('btnOrdersTab');
            const btnMenu = document.getElementById('btnMenuTab');
            const btnSettings = document.getElementById('btnSettingsTab');

            ordersView.classList.add('hidden');
            menuView.classList.add('hidden');
            settingsView.classList.add('hidden');

            btnOrders.className = "bg-slate-800 text-slate-300 px-4 py-2.5 rounded-xl text-xs font-bold transition hover:bg-slate-700";
            btnMenu.className = "bg-slate-800 text-slate-300 px-4 py-2.5 rounded-xl text-xs font-bold transition hover:bg-slate-700";
            btnSettings.className = "bg-slate-800 text-slate-300 px-4 py-2.5 rounded-xl text-xs font-bold transition hover:bg-slate-700";

            if (tab === 'orders') {
                ordersView.classList.remove('hidden');
                btnOrders.className = "bg-emerald-600 text-white px-4 py-2.5 rounded-xl text-xs font-bold transition shadow-md";
            } else if (tab === 'menu') {
                menuView.classList.remove('hidden');
                btnMenu.className = "bg-emerald-600 text-white px-4 py-2.5 rounded-xl text-xs font-bold transition shadow-md";
            } else {
                settingsView.classList.remove('hidden');
                btnSettings.className = "bg-emerald-600 text-white px-4 py-2.5 rounded-xl text-xs font-bold transition shadow-md";
            }
        }

        function renderOrders() {
            const orders = JSON.parse(localStorage.getItem('restaurant_orders')) || [];
            const grid = document.getElementById('ordersGrid');
            grid.innerHTML = '';

            let paidCount = 0;
            let pendingCount = 0;

            if (orders.length === 0) {
                grid.innerHTML = `<div class="col-span-full text-center py-16 text-slate-500 text-xs">No active table orders right now.</div>`;
                document.getElementById('countPaid').innerText = '0';
                document.getElementById('countPending').innerText = '0';
                return;
            }

            orders.forEach((order, index) => {
                if (order.paymentStatus === 'PAID') paidCount++;
                else pendingCount++;

                let itemsHtml = order.items.map(i => `<li class="flex justify-between text-xs py-1.5 border-b border-slate-800"><span>${i.qty}x ${i.name}</span><span class="text-slate-400">₹${i.price * i.qty}</span></li>`).join('');

                let paymentBadge = order.paymentStatus === 'PAID' ? 
                    `<span class="bg-emerald-500/20 text-emerald-400 border border-emerald-500/30 text-[10px] font-bold px-2.5 py-1 rounded-md">PAID (UPI)</span>` :
                    `<span class="bg-rose-500/20 text-rose-400 border border-rose-500/30 text-[10px] font-bold px-2.5 py-1 rounded-md">PENDING PAYMENT</span>`;

                grid.innerHTML += `
                    <div class="bg-slate-900 border border-slate-800 rounded-3xl p-6 shadow-xl flex flex-col justify-between">
                        <div>
                            <div class="flex justify-between items-center mb-3">
                                <span class="bg-emerald-500 text-slate-950 font-black text-xs px-3.5 py-1.5 rounded-full">Table #${order.table}</span>
                                ${paymentBadge}
                            </div>
                            <div class="flex justify-between text-[11px] text-slate-500 mb-2 font-mono">
                                <span>ID: ${order.id}</span>
                                <span>${order.time}</span>
                            </div>
                            <ul class="my-3 space-y-1">${itemsHtml}</ul>
                        </div>
                        <div class="space-y-3 pt-3 border-t border-slate-800">
                            <div class="flex justify-between items-center">
                                <span class="text-xs text-slate-400 font-bold">Total Amount</span>
                                <span class="text-base font-extrabold text-emerald-400">₹${order.total}</span>
                            </div>
                            <div class="flex gap-2">
                                <select onchange="updateOrderStatus('${order.id}', this.value)" class="flex-1 bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-xs font-bold text-white focus:outline-none">
                                    <option value="Pending" ${order.status==='Pending'?'selected':''}>Pending</option>
                                    <option value="Preparing" ${order.status==='Preparing'?'selected':''}>👨‍🍳 Preparing</option>
                                    <option value="Ready" ${order.status==='Ready'?'selected':''}>✅ Ready</option>
                                </select>
                                <button onclick="removeOrder('${order.id}')" class="bg-rose-500/20 hover:bg-rose-500 text-rose-300 hover:text-white px-3.5 py-2 rounded-xl text-xs font-bold transition flex items-center gap-1" title="Remove from Queue">
                                    <i class="fa-solid fa-trash"></i> Clear
                                </button>
                            </div>
                        </div>
                    </div>
                `;
            });

            document.getElementById('countPaid').innerText = paidCount;
            document.getElementById('countPending').innerText = pendingCount;
        }

        function updateOrderStatus(id, newStatus) {
            let orders = JSON.parse(localStorage.getItem('restaurant_orders')) || [];
            const order = orders.find(o => o.id === id);
            if (order) {
                order.status = newStatus;
                localStorage.setItem('restaurant_orders', JSON.stringify(orders));
                renderOrders();
            }
        }

        function removeOrder(id) {
            if (confirm('Are you sure you want to remove this order from the kitchen queue?')) {
                let orders = JSON.parse(localStorage.getItem('restaurant_orders')) || [];
                orders = orders.filter(o => o.id !== id);
                localStorage.setItem('restaurant_orders', JSON.stringify(orders));
                renderOrders();
            }
        }

        function renderAdminMenu() {
            const menu = JSON.parse(localStorage.getItem('restaurant_menu')) || [];
            const grid = document.getElementById('adminMenuGrid');
            grid.innerHTML = '';

            menu.forEach(item => {
                grid.innerHTML += `
                    <div class="bg-slate-900 border border-slate-800 rounded-3xl p-4 sm:p-5 flex gap-4 items-center shadow-md">
                        <img src="${item.image}" class="w-20 h-20 sm:w-24 sm:h-24 rounded-2xl object-cover shrink-0 shadow-xs">
                        <div class="flex-1 min-w-0">
                            <span class="text-[10px] font-bold text-teal-400 bg-teal-500/10 px-2.5 py-0.5 rounded-md">${item.category}</span>
                            <h4 class="font-bold text-sm text-white mt-1.5 truncate">${item.name}</h4>
                            <p class="text-xs font-extrabold text-emerald-400 mt-1">₹${item.price}</p>
                        </div>
                        <div class="flex flex-col gap-2 shrink-0">
                            <button onclick="toggleAvailability(${item.id})" class="px-3 py-1.5 rounded-xl text-[11px] font-bold ${item.available ? 'bg-emerald-500/20 text-emerald-400 border border-emerald-500/30' : 'bg-rose-500/20 text-rose-400 border border-rose-500/30'}">
                                ${item.available ? 'Active' : 'Hidden'}
                            </button>
                            <button onclick="deleteDish(${item.id})" class="text-xs text-slate-500 hover:text-rose-400 py-1 transition text-center"><i class="fa-solid fa-trash"></i></button>
                        </div>
                    </div>
                `;
            });
        }

        function toggleAvailability(id) {
            let menu = JSON.parse(localStorage.getItem('restaurant_menu')) || [];
            const item = menu.find(i => i.id === id);
            if (item) {
                item.available = !item.available;
                localStorage.setItem('restaurant_menu', JSON.stringify(menu));
                renderAdminMenu();
            }
        }

        function deleteDish(id) {
            if (confirm('Are you sure you want to delete this dish?')) {
                let menu = JSON.parse(localStorage.getItem('restaurant_menu')) || [];
                menu = menu.filter(i => i.id !== id);
                localStorage.setItem('restaurant_menu', JSON.stringify(menu));
                renderAdminMenu();
            }
        }

        function openAddDishModal() { document.getElementById('addDishModal').classList.remove('hidden'); }
        function closeAddDishModal() { document.getElementById('addDishModal').classList.add('hidden'); }

        function saveNewDish(e) {
            e.preventDefault();
            const name = document.getElementById('dishName').value;
            const price = parseFloat(document.getElementById('dishPrice').value);
            const category = document.getElementById('dishCategory').value;
            const fileInput = document.getElementById('dishImageFile');
            const file = fileInput.files[0];

            if (!file) return alert('Please select a JPG/PNG image file.');

            const reader = new FileReader();
            reader.onload = function(uploadEvent) {
                const base64Image = uploadEvent.target.result;
                let menu = JSON.parse(localStorage.getItem('restaurant_menu')) || [];
                const newDish = { id: Date.now(), name, price, category, image: base64Image, available: true };
                menu.push(newDish);
                localStorage.setItem('restaurant_menu', JSON.stringify(menu));

                renderAdminMenu();
                closeAddDishModal();
                document.getElementById('addDishForm').reset();
            };
            reader.readAsDataURL(file);
        }
    </script>
</body>
</html>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Lush Bistro | Luxury Dining Experience</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- jsPDF for Exact Sample Bill PDF Generation -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Plus Jakarta Sans', sans-serif; }
        @keyframes toast-slide {
            0% { transform: translateY(100px); opacity: 0; }
            15% { transform: translateY(0); opacity: 1; }
            85% { transform: translateY(0); opacity: 1; }
            100% { transform: translateY(100px); opacity: 0; }
        }
        .toast-animate { animation: toast-slide 2.5s ease-in-out forwards; }
    </style>
</head>
<body class="bg-slate-50 text-slate-900 antialiased min-h-screen pb-28">

    <!-- Header -->
    <header class="bg-white/90 backdrop-blur-md sticky top-0 z-40 border-b border-slate-200 shadow-xs">
        <div class="max-w-7xl mx-auto px-4 sm:px-8 py-3.5 flex justify-between items-center">
            <div class="flex items-center gap-3">
                <div class="w-10 h-10 rounded-2xl bg-emerald-600 text-white flex items-center justify-center font-black text-lg shadow-md shadow-emerald-600/20">🌿</div>
                <div>
                    <h1 class="text-sm sm:text-lg font-black text-slate-900 tracking-tight">Lush Bistro</h1>
                    <div class="flex items-center gap-2">
                        <span id="tableBadge" class="text-[10px] font-bold text-emerald-700 bg-emerald-50 border border-emerald-200 px-2 py-0.5 rounded-full">Table #1</span>
                    </div>
                </div>
            </div>

            <div class="flex items-center gap-2">
                <!-- Call Waiter Button -->
                <button onclick="callWaiter()" class="bg-amber-50 hover:bg-amber-100 text-amber-900 px-3 py-2 rounded-xl text-xs font-bold border border-amber-200 transition flex items-center gap-1.5 shadow-xs">
                    <i class="fa-solid fa-bell text-amber-600"></i> Call Waiter
                </button>
                <!-- Bill Button -->
                <button onclick="openBillModal()" class="bg-emerald-50 hover:bg-emerald-100 text-emerald-900 px-3 py-2 rounded-xl text-xs font-bold border border-emerald-200 transition flex items-center gap-1.5 shadow-xs">
                    <i class="fa-solid fa-receipt text-emerald-600"></i> Bill
                </button>
                <!-- Cart Button -->
                <button onclick="openCart()" class="relative bg-slate-900 hover:bg-slate-800 text-white px-3.5 py-2 rounded-xl shadow-md transition flex items-center gap-1.5">
                    <i class="fa-solid fa-bag-shopping text-sm"></i>
                    <span id="cartCount" class="absolute -top-1.5 -right-1.5 bg-rose-500 text-white text-[10px] w-5 h-5 rounded-full flex items-center justify-center font-black shadow-xs">0</span>
                </button>
            </div>
        </div>
    </header>

    <!-- Main Content -->
    <main class="max-w-7xl mx-auto px-4 sm:px-8 py-6">
        <div class="bg-gradient-to-r from-emerald-800 via-emerald-700 to-teal-800 rounded-3xl p-6 sm:p-10 text-white mb-8 shadow-xl relative overflow-hidden flex flex-col justify-center min-h-[150px]">
            <span class="bg-white/20 backdrop-blur-md text-white text-[10px] font-bold px-3 py-1 rounded-full uppercase tracking-wider w-max mb-2">Digital QR Menu</span>
            <h2 class="text-2xl sm:text-4xl font-black tracking-tight">Savor Gourmet Excellence 🍽️</h2>
            <p class="text-emerald-100 text-xs sm:text-sm mt-1 max-w-xl">Order freshly prepared gourmet dishes straight to your table instantly.</p>
        </div>

        <div class="grid grid-cols-1 lg:grid-cols-4 gap-8">
            <div class="lg:col-span-1 space-y-4">
                <h3 class="text-xs font-extrabold text-slate-400 uppercase tracking-wider hidden lg:block">Categories</h3>
                <div id="categoryFilters" class="flex lg:flex-col gap-2 overflow-x-auto pb-2 lg:pb-0 no-scrollbar">
                    <button onclick="filterMenu('All')" class="category-btn lg:w-full text-left px-4 py-3 bg-slate-900 text-white rounded-2xl text-xs font-bold shadow-md transition flex items-center justify-between"><span>All Dishes</span></button>
                    <button onclick="filterMenu('Starter')" class="category-btn lg:w-full text-left px-4 py-3 bg-white hover:bg-slate-100 text-slate-600 border border-slate-200/80 rounded-2xl text-xs font-bold transition flex items-center justify-between"><span>Starters</span></button>
                    <button onclick="filterMenu('Main Course')" class="category-btn lg:w-full text-left px-4 py-3 bg-white hover:bg-slate-100 text-slate-600 border border-slate-200/80 rounded-2xl text-xs font-bold transition flex items-center justify-between"><span>Main Course</span></button>
                    <button onclick="filterMenu('Beverage')" class="category-btn lg:w-full text-left px-4 py-3 bg-white hover:bg-slate-100 text-slate-600 border border-slate-200/80 rounded-2xl text-xs font-bold transition flex items-center justify-between"><span>Beverages</span></button>
                </div>
            </div>

            <div class="lg:col-span-3">
                <div class="flex justify-between items-center mb-4">
                    <h3 id="categoryTitle" class="text-lg font-extrabold text-slate-900">All Dishes</h3>
                    <span id="dishCount" class="text-xs font-bold text-slate-400"></span>
                </div>
                <div id="menuGrid" class="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-5"></div>
            </div>
        </div>
    </main>

    <!-- TOAST NOTIFICATION -->
    <div id="toastNotification" class="fixed bottom-6 left-1/2 -translate-x-1/2 z-50 pointer-events-none hidden">
        <div class="bg-slate-900 text-white px-5 py-3 rounded-2xl shadow-2xl flex items-center gap-3 border border-slate-700">
            <div class="w-6 h-6 rounded-full bg-emerald-500 text-slate-900 flex items-center justify-center font-bold text-xs"><i class="fa-solid fa-check"></i></div>
            <span id="toastMessage" class="text-xs sm:text-sm font-bold">Item added to cart!</span>
        </div>
    </div>

    <!-- Cart Drawer Modal -->
    <div id="cartModal" class="fixed inset-0 bg-black/60 z-50 hidden backdrop-blur-sm flex justify-end">
        <div class="bg-white w-full max-w-md h-full flex flex-col shadow-2xl">
            <div class="p-5 border-b border-slate-100 flex justify-between items-center bg-slate-50">
                <h3 class="text-base font-bold text-slate-900 flex items-center gap-2"><i class="fa-solid fa-bag-shopping text-emerald-600"></i> Your Current Cart</h3>
                <button onclick="closeCart()" class="w-8 h-8 rounded-xl bg-slate-200/60 text-slate-600 flex items-center justify-center"><i class="fa-solid fa-xmark"></i></button>
            </div>
            <div id="cartItemsList" class="flex-1 overflow-y-auto p-5 space-y-3"></div>
            <div class="p-6 border-t border-slate-100 bg-slate-50 space-y-3">
                <div class="flex justify-between text-sm text-slate-500"><span>Subtotal</span><span id="cartSubtotal" class="font-bold text-slate-900">₹0</span></div>
                <div class="flex justify-between text-lg font-black text-slate-900 pt-2 border-t border-slate-200"><span>Total</span><span id="cartTotal" class="text-emerald-600">₹0</span></div>
                <button onclick="placeOrder()" class="w-full bg-emerald-600 hover:bg-emerald-700 text-white font-bold py-3.5 rounded-2xl shadow-lg transition flex items-center justify-center gap-2"><i class="fa-solid fa-paper-plane"></i> Send Order to Kitchen</button>
            </div>
        </div>
    </div>

    <!-- Table Bill & Payment Modal -->
    <div id="billModal" class="fixed inset-0 bg-black/60 z-50 hidden flex items-center justify-center p-4 backdrop-blur-sm">
        <div class="bg-white rounded-3xl max-w-md w-full shadow-2xl flex flex-col max-h-[90vh] overflow-hidden">
            <div class="p-5 border-b border-slate-100 flex justify-between items-center bg-slate-50">
                <div>
                    <h3 class="text-base font-bold text-slate-900">Table Session Bill</h3>
                    <p class="text-[11px] text-slate-500">All orders placed during your visit today</p>
                </div>
                <button onclick="closeBillModal()" class="w-8 h-8 rounded-xl bg-slate-200/60 text-slate-600 flex items-center justify-center"><i class="fa-solid fa-xmark"></i></button>
            </div>
            
            <div class="flex-1 overflow-y-auto p-6 space-y-4">
                <div class="text-center pb-4 border-b border-dashed border-slate-200">
                    <h4 class="font-black text-slate-900 text-lg">🌿 LUSH BISTRO</h4>
                    <p id="receiptTableTag" class="text-xs text-slate-500 mt-0.5 font-medium">Table #1 • Session Receipt</p>
                </div>
                <div id="sessionOrdersList" class="space-y-3"></div>
                <div class="pt-4 border-t border-dashed border-slate-200 space-y-1">
                    <div class="flex justify-between items-center text-sm font-bold">
                        <span class="text-slate-600">Total Bill Amount</span>
                        <span id="sessionGrandTotal" class="font-black text-emerald-600 text-xl">₹0</span>
                    </div>
                </div>
                <div id="paymentStatusBox" class="hidden p-3 rounded-xl text-xs font-bold text-center"></div>
            </div>

            <div class="p-5 border-t border-slate-100 bg-slate-50 rounded-b-3xl grid grid-cols-3 gap-2">
                <button onclick="downloadPDFBill()" class="bg-slate-900 hover:bg-slate-800 text-white text-[11px] font-bold py-3 rounded-2xl transition flex flex-col items-center justify-center gap-1">
                    <i class="fa-solid fa-file-pdf text-sm text-rose-400"></i> Download PDF
                </button>
                <button onclick="payViaUPIApp()" class="bg-emerald-600 hover:bg-emerald-700 text-white text-[11px] font-bold py-3 rounded-2xl transition flex flex-col items-center justify-center gap-1 shadow-md">
                    <i class="fa-solid fa-mobile-screen-button text-sm"></i> Pay via UPI App
                </button>
                <button onclick="payByCash()" class="bg-amber-600 hover:bg-amber-700 text-white text-[11px] font-bold py-3 rounded-2xl transition flex flex-col items-center justify-center gap-1 shadow-md">
                    <i class="fa-solid fa-money-bill-wave text-sm"></i> Pay by Cash
                </button>
            </div>
        </div>
    </div>

    <!-- Order Success Modal -->
    <div id="successModal" class="fixed inset-0 bg-black/60 z-50 hidden flex items-center justify-center p-4">
        <div class="bg-white rounded-3xl p-8 max-w-sm w-full text-center shadow-2xl">
            <div class="w-16 h-16 bg-emerald-100 text-emerald-600 rounded-full flex items-center justify-center mx-auto mb-4 text-2xl"><i class="fa-solid fa-check"></i></div>
            <h3 class="text-xl font-bold text-slate-900">Order Sent to Kitchen!</h3>
            <p class="text-slate-500 text-xs mt-1 mb-6">Your food is being prepared. Check your cumulative bill anytime via the top bar.</p>
            <button onclick="closeSuccessModal()" class="w-full bg-slate-900 text-white font-bold py-3.5 rounded-2xl text-xs">Back to Menu</button>
        </div>
    </div>

    <script>
        const defaultMenu = [
            { id: 1, name: "Truffle Mushroom Burger", price: 449, category: "Main Course", image: "https://images.unsplash.com/photo-1568901346375-23c9450c58cd?w=500&auto=format&fit=crop&q=60", available: true },
            { id: 2, name: "Crispy Calamari Rings", price: 320, category: "Starter", image: "https://images.unsplash.com/photo-1604908176997-125f25cc6f3d?w=500&auto=format&fit=crop&q=60", available: true },
            { id: 3, name: "Wood-Fired Margherita Pizza", price: 549, category: "Main Course", image: "https://images.unsplash.com/photo-1604382355076-af4b0eb60143?w=500&auto=format&fit=crop&q=60", available: true },
            { id: 4, name: "Artisan Iced Matcha Latte", price: 220, category: "Beverage", image: "https://images.unsplash.com/photo-1536256263959-770b48d82b0a?w=500&auto=format&fit=crop&q=60", available: true }
        ];

        let cart = [];
        let tableNumber = 1;

        window.onload = function() {
            const urlParams = new URLSearchParams(window.location.search);
            const t = urlParams.get('table');
            if (t) tableNumber = t;

            document.getElementById('tableBadge').innerText = `Table #${tableNumber}`;
            document.getElementById('receiptTableTag').innerText = `Table #${tableNumber} • Session Receipt`;

            if (!localStorage.getItem('restaurant_menu')) {
                localStorage.setItem('restaurant_menu', JSON.stringify(defaultMenu));
            }
            renderMenu();
        };

        function getMenu() {
            return JSON.parse(localStorage.getItem('restaurant_menu')) || defaultMenu;
        }

        function renderMenu(category = 'All') {
            const menu = getMenu();
            const grid = document.getElementById('menuGrid');
            grid.innerHTML = '';

            const filtered = category === 'All' ? menu : menu.filter(item => item.category === category);
            document.getElementById('categoryTitle').innerText = category === 'All' ? 'All Dishes' : category;
            document.getElementById('dishCount').innerText = `${filtered.length} items available`;

            filtered.forEach(item => {
                grid.innerHTML += `
                    <div class="bg-white rounded-3xl p-4 shadow-sm border border-slate-100 flex flex-col justify-between hover:shadow-md transition">
                        <div>
                            <div class="relative mb-3">
                                <img src="${item.image}" alt="${item.name}" class="w-full h-44 object-cover rounded-2xl shadow-xs">
                                <span class="absolute top-3 left-3 bg-black/60 backdrop-blur-md text-white text-[10px] font-bold px-2.5 py-1 rounded-lg">${item.category}</span>
                            </div>
                            <h4 class="font-bold text-slate-900 text-sm sm:text-base">${item.name}</h4>
                        </div>
                        <div class="flex justify-between items-center mt-4 pt-3 border-t border-slate-100">
                            <span class="font-extrabold text-slate-900 text-base">₹${item.price}</span>
                            ${item.available ? 
                                `<button onclick="addToCart(${item.id})" class="bg-slate-900 hover:bg-emerald-600 text-white text-xs px-4 py-2.5 rounded-xl transition font-bold shadow-xs">Add +</button>` :
                                `<span class="text-xs text-rose-500 font-bold bg-rose-50 px-3 py-1.5 rounded-xl">Sold Out</span>`
                            }
                        </div>
                    </div>
                `;
            });
        }

        function filterMenu(cat) {
            document.querySelectorAll('.category-btn').forEach(btn => {
                btn.className = "category-btn lg:w-full text-left px-4 py-3 bg-white hover:bg-slate-100 text-slate-600 border border-slate-200/80 rounded-2xl text-xs font-bold transition flex items-center justify-between";
            });
            event.currentTarget.className = "category-btn lg:w-full text-left px-4 py-3 bg-slate-900 text-white rounded-2xl text-xs font-bold shadow-md transition flex items-center justify-between";
            renderMenu(cat);
        }

        function addToCart(id) {
            const menu = getMenu();
            const item = menu.find(i => i.id === id);
            const existing = cart.find(i => i.id === id);
            if (existing) {
                existing.qty++;
            } else {
                cart.push({ ...item, qty: 1 });
            }
            updateCartUI();
            showToast(`"${item.name}" added to cart!`);
        }

        function showToast(message) {
            const toast = document.getElementById('toastNotification');
            const msgEl = document.getElementById('toastMessage');
            msgEl.innerText = message;
            toast.classList.remove('hidden');
            toast.classList.add('toast-animate');
            setTimeout(() => {
                toast.classList.remove('toast-animate');
                toast.classList.add('hidden');
            }, 2500);
        }

        function updateCartUI() {
            const count = cart.reduce((sum, i) => sum + i.qty, 0);
            document.getElementById('cartCount').innerText = count;

            const list = document.getElementById('cartItemsList');
            list.innerHTML = '';
            let total = 0;

            if (cart.length === 0) {
                list.innerHTML = `<div class="text-center py-16 text-slate-400 text-xs font-medium">Your cart is currently empty</div>`;
            }

            cart.forEach(item => {
                total += item.price * item.qty;
                list.innerHTML += `
                    <div class="flex justify-between items-center bg-slate-50 p-3 rounded-2xl border border-slate-100">
                        <div class="pr-2">
                            <h5 class="font-bold text-xs text-slate-900">${item.name}</h5>
                            <p class="text-slate-500 text-[11px] mt-0.5">₹${item.price} × ${item.qty}</p>
                        </div>
                        <div class="flex items-center gap-2 shrink-0">
                            <button onclick="changeQty(${item.id}, -1)" class="w-7 h-7 bg-white border border-slate-200 rounded-xl text-xs font-bold flex items-center justify-center text-slate-600 shadow-xs">-</button>
                            <span class="text-xs font-bold w-4 text-center">${item.qty}</span>
                            <button onclick="changeQty(${item.id}, 1)" class="w-7 h-7 bg-white border border-slate-200 rounded-xl text-xs font-bold flex items-center justify-center text-slate-600 shadow-xs">+</button>
                        </div>
                    </div>
                `;
            });

            document.getElementById('cartSubtotal').innerText = `₹${total}`;
            document.getElementById('cartTotal').innerText = `₹${total}`;
        }

        function changeQty(id, delta) {
            const item = cart.find(i => i.id === id);
            if (item) {
                item.qty += delta;
                if (item.qty <= 0) {
                    cart = cart.filter(i => i.id !== id);
                }
            }
            updateCartUI();
        }

        function openCart() { document.getElementById('cartModal').classList.remove('hidden'); }
        function closeCart() { document.getElementById('cartModal').classList.add('hidden'); }

        function callWaiter() {
            let calls = JSON.parse(localStorage.getItem('restaurant_waiter_calls')) || [];
            calls.unshift({
                table: tableNumber,
                time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
            });
            localStorage.setItem('restaurant_waiter_calls', JSON.stringify(calls));
            alert(`🛎️ Waiter notified for Table #${tableNumber}! Assistance is on the way.`);
        }

        function placeOrder() {
            if (cart.length === 0) return alert('Your cart is empty!');

            let orders = JSON.parse(localStorage.getItem('restaurant_orders')) || [];
            let sessionOrders = JSON.parse(localStorage.getItem(`table_session_${tableNumber}`)) || [];

            const newOrder = {
                id: 'ORD-' + Math.floor(1000 + Math.random() * 9000),
                table: tableNumber,
                items: [...cart],
                total: cart.reduce((sum, i) => sum + (i.price * i.qty), 0),
                status: 'Pending',
                time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
            };

            orders.unshift(newOrder);
            sessionOrders.push(newOrder);

            localStorage.setItem('restaurant_orders', JSON.stringify(orders));
            localStorage.setItem(`table_session_${tableNumber}`, JSON.stringify(sessionOrders));

            cart = [];
            updateCartUI();
            closeCart();
            document.getElementById('successModal').classList.remove('hidden');
        }

        function closeSuccessModal() { document.getElementById('successModal').classList.add('hidden'); }

        function openBillModal() {
            const sessionOrders = JSON.parse(localStorage.getItem(`table_session_${tableNumber}`)) || [];
            const list = document.getElementById('sessionOrdersList');
            list.innerHTML = '';
            let grandTop = 0;

            if (sessionOrders.length === 0) {
                list.innerHTML = `<div class="text-center py-10 text-slate-400 text-xs">No orders placed yet in this table session.</div>`;
            }

            sessionOrders.forEach((order, idx) => {
                grandTop += order.total;
                let itemsHtml = order.items.map(i => `<li class="flex justify-between text-xs py-0.5 text-slate-600"><span>${i.qty}x ${i.name}</span><span class="font-medium">₹${i.price * i.qty}</span></li>`).join('');
                
                list.innerHTML += `
                    <div class="bg-slate-50 p-3 rounded-2xl border border-slate-100">
                        <div class="flex justify-between text-[11px] font-bold text-slate-700 mb-1">
                            <span>Order #${idx + 1} (${order.time})</span>
                            <span class="text-emerald-600">₹${order.total}</span>
                        </div>
                        <ul class="space-y-0.5 pl-1">${itemsHtml}</ul>
                    </div>
                `;
            });

            document.getElementById('sessionGrandTotal').innerText = `₹${grandTop}`;
            document.getElementById('paymentStatusBox').classList.add('hidden');
            document.getElementById('billModal').classList.remove('hidden');
        }

        function closeBillModal() { document.getElementById('billModal').classList.add('hidden'); }

        function downloadPDFBill() {
            const { jsPDF } = window.jspdf;
            const doc = new jsPDF({ unit: 'mm', format: [80, 200] });
            const sessionOrders = JSON.parse(localStorage.getItem(`table_session_${tableNumber}`)) || [];

            if (sessionOrders.length === 0) return alert('No bill items to download.');

            let y = 10;
            doc.setFont("courier", "bold");
            doc.setFontSize(14);
            doc.text("LUSH BISTRO", 40, y, { align: "center" });
            
            y += 5;
            doc.setFontSize(8);
            doc.setFont("courier", "normal");
            doc.text("GOOD FOOD. GREAT MEMORIES.", 40, y, { align: "center" });
            
            y += 5;
            doc.text("Restaurant Address, City", 40, y, { align: "center" });
            y += 4;
            doc.text("Ph: 080-4567-8910", 40, y, { align: "center" });
            y += 5;
            doc.text("GSTIN: 29ABCDE1234F1Z5", 40, y, { align: "center" });

            y += 4;
            doc.text("----------------------------------------", 40, y, { align: "center" });
            
            y += 5;
            doc.text(`Date       : ${new Date().toLocaleDateString()}`, 6, y);
            y += 4;
            doc.text(`Time       : ${new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'})}`, 6, y);
            y += 4;
            doc.text(`Table No.  : T-0${tableNumber}`, 6, y);

            y += 4;
            doc.text("----------------------------------------", 40, y, { align: "center" });
            y += 5;
            doc.setFont("courier", "bold");
            doc.text("ITEM", 6, y);
            doc.text("QTY", 45, y);
            doc.text("UNIT", 55, y);
            doc.text("TOTAL", 74, y, { align: "right" });

            y += 3;
            doc.setFont("courier", "normal");
            doc.text("----------------------------------------", 40, y, { align: "center" });

            y += 5;
            let subtotal = 0;
            let itemCounter = 1;

            sessionOrders.forEach(order => {
                order.items.forEach(item => {
                    subtotal += item.price * item.qty;
                    doc.text(`${itemCounter}. ${item.name}`, 6, y);
                    y += 4;
                    doc.text(`${item.qty}`, 47, y);
                    doc.text(`Rs.${item.price}`, 55, y);
                    doc.text(`Rs.${item.price * item.qty}`, 74, y, { align: "right" });
                    y += 5;
                    itemCounter++;
                });
            });

            doc.text("----------------------------------------", 40, y, { align: "center" });
            y += 5;
            doc.text(`SUBTOTAL`, 6, y);
            doc.text(`Rs.${subtotal}`, 74, y, { align: "right" });

            let cgst = subtotal * 0.025;
            let sgst = subtotal * 0.025;
            let grandTotal = subtotal + cgst + sgst;

            y += 4;
            doc.text(`CGST (2.5%)`, 6, y);
            doc.text(`Rs.${cgst.toFixed(2)}`, 74, y, { align: "right" });
            y += 4;
            doc.text(`SGST (2.5%)`, 6, y);
            doc.text(`Rs.${sgst.toFixed(2)}`, 74, y, { align: "right" });

            y += 3;
            doc.text("----------------------------------------", 40, y, { align: "center" });
            y += 6;
            doc.setFont("courier", "bold");
            doc.setFontSize(10);
            doc.text(`GRAND TOTAL`, 6, y);
            doc.text(`Rs.${grandTotal.toFixed(2)}`, 74, y, { align: "right" });

            y += 6;
            doc.text("THANK YOU, VISIT AGAIN!", 40, y, { align: "center" });

            doc.save(`LushBistro_Bill_Table_${tableNumber}.pdf`);
        }

        function payViaUPIApp() {
            const sessionOrders = JSON.parse(localStorage.getItem(`table_session_${tableNumber}`)) || [];
            const subtotal = sessionOrders.reduce((sum, o) => sum + o.total, 0);
            const grandTotal = Math.round(subtotal * 1.05);

            if (grandTotal <= 0) return alert('No active bill to pay.');

            const upiId = localStorage.getItem('restaurant_upi_id') || 'lushbistro@okhdfcbank';
            const upiUrl = `upi://pay?pa=${encodeURIComponent(upiId)}&pn=${encodeURIComponent("Lush Bistro")}&am=${grandTotal}&cu=INR&tn=${encodeURIComponent("Table " + tableNumber + " Food Bill")}`;

            window.location.href = upiUrl;

            setTimeout(() => {
                const statusBox = document.getElementById('paymentStatusBox');
                statusBox.classList.remove('hidden');
                
                const isSuccess = confirm("Did you successfully complete the payment in your UPI app?");
                if (isSuccess) {
                    statusBox.className = "p-3 rounded-xl text-xs font-bold text-center bg-emerald-100 text-emerald-800";
                    statusBox.innerText = "Payment Successful! Thank you for dining with us.";
                    localStorage.removeItem(`table_session_${tableNumber}`);
                    setTimeout(() => {
                        closeBillModal();
                        location.reload();
                    }, 2000);
                } else {
                    statusBox.className = "p-3 rounded-xl text-xs font-bold text-center bg-rose-100 text-rose-700";
                    statusBox.innerText = "⚠️ Payment Declined or Cancelled by User.";
                }
            }, 1000);
        }

        function payByCash() {
            const sessionOrders = JSON.parse(localStorage.getItem(`table_session_${tableNumber}`)) || [];
            const subtotal = sessionOrders.reduce((sum, o) => sum + o.total, 0);
            const grandTotal = Math.round(subtotal * 1.05);
            if (grandTotal <= 0) return alert('No active bill.');

            let cashReqs = JSON.parse(localStorage.getItem('restaurant_cash_requests')) || [];
            cashReqs.unshift({
                table: tableNumber,
                amount: grandTotal,
                time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
            });
            localStorage.setItem('restaurant_cash_requests', JSON.stringify(cashReqs));

            const statusBox = document.getElementById('paymentStatusBox');
            statusBox.classList.remove('hidden');
            statusBox.className = "p-3 rounded-xl text-xs font-bold text-center bg-amber-100 text-amber-800";
            statusBox.innerText = "💵 Cash payment requested! A staff member is heading to your table.";
        }
    </script>
</body>
</html>
