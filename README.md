```html
<!DOCTYPE html>
<html lang="en" class="h-full">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Telegram Store Mini App</title>

    <!-- Telegram WebApp SDK -->
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>

    <!-- Inter Font -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">

    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        tg: {
                            bg: 'var(--tg-theme-bg-color, #ffffff)',
                            secBg: 'var(--tg-theme-secondary-bg-color, #f4f4f5)',
                            text: 'var(--tg-theme-text-color, #09090b)',
                            hint: 'var(--tg-theme-hint-color, #71717a)',
                            link: 'var(--tg-theme-link-color, #2563eb)',
                            button: 'var(--tg-theme-button-color, #2563eb)',
                            buttonText: 'var(--tg-theme-button-text-color, #ffffff)',
                        }
                    },
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                    }
                }
            }
        }
    </script>

    <style>
        /* Custom scrollbar hiding & Telegram Theme CSS variables mapping */
        .no-scrollbar::-webkit-scrollbar {
            display: none;
        }
        .no-scrollbar {
            -ms-overflow-style: none;
            scrollbar-width: none;
        }

        body {
            background-color: var(--tg-theme-bg-color, #f8fafc);
            color: var(--tg-theme-text-color, #0f172a);
            font-family: 'Inter', sans-serif;
            -webkit-tap-highlight-color: transparent;
        }

        /* Glassmorphism utility */
        .glass {
            background: rgba(255, 255, 255, 0.75);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
        }
        .dark .glass {
            background: rgba(15, 23, 42, 0.75);
        }

        /* Active navigation indicator */
        .nav-item.active {
            color: var(--tg-theme-button-color, #2563eb);
        }

        /* Pulsing animation for cart badge */
        @keyframes badgePulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.2); }
            100% { transform: scale(1); }
        }
        .badge-pop {
            animation: badgePulse 0.3s ease-out;
        }
    </style>
</head>
<body class="h-full flex flex-col justify-between antialiased selection:bg-blue-500 selection:text-white">

    <!-- App Container -->
    <div id="app" class="flex flex-col min-h-full max-w-md mx-auto w-full relative pb-20 shadow-xl min-h-screen bg-tg-bg">
        
        <!-- Top Header -->
        <header class="sticky top-0 z-30 glass border-b border-slate-200/80 dark:border-slate-800/80 px-4 py-3 flex items-center justify-between transition-colors">
            <div class="flex items-center gap-2">
                <div class="w-8 h-8 rounded-full bg-blue-600 flex items-center justify-center text-white font-bold text-sm shadow-md shadow-blue-500/20">
                    <i data-lucide="shopping-bag" class="w-4 h-4"></i>
                </div>
                <div>
                    <h1 class="text-base font-bold leading-none tracking-tight">NovaStore</h1>
                    <p class="text-[10px] text-tg-hint font-medium">Fast Telegram Delivery</p>
                </div>
            </div>
            
            <!-- User Profile Quick View -->
            <div id="user-pill" class="flex items-center gap-2 bg-tg-secBg px-3 py-1.5 rounded-full border border-slate-200 dark:border-slate-800">
                <div class="w-5 h-5 rounded-full bg-slate-300 dark:bg-slate-700 flex items-center justify-center text-xs font-semibold overflow-hidden">
                    <span id="user-avatar-initial">U</span>
                </div>
                <span id="user-name" class="text-xs font-medium max-w-[80px] truncate">Guest</span>
            </div>
        </header>

        <!-- Dynamic Content Body -->
        <main class="flex-1 p-4 overflow-y-auto">
            
            <!-- TAB 1: CATALOG -->
            <section id="tab-catalog" class="space-y-4">
                <!-- Search & Filters -->
                <div class="space-y-3">
                    <div class="relative">
                        <i data-lucide="search" class="w-4 h-4 absolute left-3 top-1/2 -translate-y-1/2 text-tg-hint"></i>
                        <input type="text" id="search-input" placeholder="Search products..." 
                            class="w-full bg-tg-secBg border border-slate-200 dark:border-slate-800 rounded-xl pl-9 pr-4 py-2.5 text-sm focus:outline-none focus:ring-2 focus:ring-blue-500/50 transition-all">
                        <button id="clear-search" class="hidden absolute right-3 top-1/2 -translate-y-1/2 text-tg-hint hover:text-tg-text">
                            <i data-lucide="x" class="w-4 h-4"></i>
                        </button>
                    </div>

                    <!-- Category Pills -->
                    <div class="flex gap-2 overflow-x-auto no-scrollbar py-1 -mx-4 px-4" id="categories-container">
                        <!-- Populated by JS -->
                    </div>
                </div>

                <!-- Products Grid -->
                <div class="grid grid-cols-2 gap-3" id="products-grid">
                    <!-- Populated by JS -->
                </div>

                <!-- Empty Search Result -->
                <div id="empty-catalog" class="hidden text-center py-12">
                    <div class="w-16 h-16 mx-auto mb-3 text-tg-hint bg-tg-secBg rounded-full flex items-center justify-center">
                        <i data-lucide="package-search" class="w-8 h-8"></i>
                    </div>
                    <h3 class="font-semibold text-base mb-1">No products found</h3>
                    <p class="text-xs text-tg-hint">Try adjusting your search or filters</p>
                </div>
            </section>

            <!-- TAB 2: CART & CHECKOUT -->
            <section id="tab-cart" class="hidden space-y-5">
                <div class="flex items-center justify-between">
                    <h2 class="text-lg font-bold">Shopping Cart</h2>
                    <button id="clear-cart-btn" class="text-xs text-red-500 font-medium hover:underline hidden">Clear All</button>
                </div>

                <!-- Cart Items List -->
                <div id="cart-items-container" class="space-y-3">
                    <!-- Populated by JS -->
                </div>

                <!-- Empty Cart View -->
                <div id="empty-cart-view" class="text-center py-12 space-y-3">
                    <div class="w-20 h-20 mx-auto text-tg-hint bg-tg-secBg rounded-full flex items-center justify-center">
                        <i data-lucide="shopping-cart" class="w-10 h-10"></i>
                    </div>
                    <h3 class="font-semibold text-base">Your cart is empty</h3>
                    <p class="text-xs text-tg-hint max-w-[220px] mx-auto">Explore our catalog and add items to your cart to get started.</p>
                    <button onclick="switchTab('catalog')" class="mt-2 px-5 py-2.5 bg-blue-600 text-white text-xs font-semibold rounded-xl shadow-md shadow-blue-500/20 active:scale-95 transition-all">
                        Browse Products
                    </button>
                </div>

                <!-- Order Details & Checkout Form (Hidden when cart is empty) -->
                <div id="checkout-section" class="hidden space-y-5 pt-3 border-t border-slate-200 dark:border-slate-800">
                    
                    <!-- Promo Code Input -->
                    <div class="space-y-1.5">
                        <label class="text-xs font-medium text-tg-hint">Promo Code</label>
                        <div class="flex gap-2">
                            <input type="text" id="promo-input" placeholder="e.g. NOVA10" class="uppercase flex-1 bg-tg-secBg border border-slate-200 dark:border-slate-800 rounded-xl px-3 py-2 text-xs uppercase focus:outline-none focus:ring-2 focus:ring-blue-500/50">
                            <button id="apply-promo-btn" class="px-4 py-2 bg-slate-800 dark:bg-slate-200 text-white dark:text-slate-900 text-xs font-semibold rounded-xl active:scale-95 transition-all">
                                Apply
                            </button>
                        </div>
                        <p id="promo-message" class="text-[11px] hidden font-medium"></p>
                    </div>

                    <!-- Delivery Options -->
                    <div class="space-y-2">
                        <label class="text-xs font-semibold text-tg-hint">Delivery Method</label>
                        <div class="grid grid-cols-2 gap-2">
                            <button id="btn-delivery" onclick="setDeliveryMethod('delivery')" class="flex flex-col items-center justify-center p-3 rounded-xl border-2 border-blue-600 bg-blue-50 dark:bg-blue-950/40 text-blue-600 font-medium transition-all">
                                <i data-lucide="truck" class="w-5 h-5 mb-1"></i>
                                <span class="text-xs font-bold">Courier ($5.00)</span>
                            </button>
                            <button id="btn-pickup" onclick="setDeliveryMethod('pickup')" class="flex flex-col items-center justify-center p-3 rounded-xl border-2 border-slate-200 dark:border-slate-800 bg-tg-secBg text-tg-hint font-medium transition-all">
                                <i data-lucide="store" class="w-5 h-5 mb-1"></i>
                                <span class="text-xs font-bold">Store Pickup (Free)</span>
                            </button>
                        </div>
                    </div>

                    <!-- Contact & Shipping Details Form -->
                    <form id="checkout-form" class="space-y-3">
                        <div class="space-y-1">
                            <label class="text-xs font-medium text-tg-hint">Full Name *</label>
                            <input type="text" id="cust-name" required placeholder="John Doe" class="w-full bg-tg-secBg border border-slate-200 dark:border-slate-800 rounded-xl px-3 py-2 text-xs focus:outline-none focus:ring-2 focus:ring-blue-500/50">
                        </div>

                        <div class="space-y-1">
                            <label class="text-xs font-medium text-tg-hint">Phone Number *</label>
                            <input type="tel" id="cust-phone" required placeholder="+1 234 567 890" class="w-full bg-tg-secBg border border-slate-200 dark:border-slate-800 rounded-xl px-3 py-2 text-xs focus:outline-none focus:ring-2 focus:ring-blue-500/50">
                        </div>

                        <div class="space-y-1" id="address-field-group">
                            <label class="text-xs font-medium text-tg-hint">Delivery Address *</label>
                            <textarea id="cust-address" required rows="2" placeholder="Street address, Apartment, City" class="w-full bg-tg-secBg border border-slate-200 dark:border-slate-800 rounded-xl px-3 py-2 text-xs focus:outline-none focus:ring-2 focus:ring-blue-500/50 resize-none"></textarea>
                        </div>

                        <div class="space-y-1">
                            <label class="text-xs font-medium text-tg-hint">Order Comment (Optional)</label>
                            <input type="text" id="cust-comment" placeholder="e.g. Leave at door" class="w-full bg-tg-secBg border border-slate-200 dark:border-slate-800 rounded-xl px-3 py-2 text-xs focus:outline-none focus:ring-2 focus:ring-blue-500/50">
                        </div>
                    </form>

                    <!-- Order Financial Breakdown -->
                    <div class="bg-tg-secBg p-3.5 rounded-2xl space-y-2 text-xs border border-slate-200 dark:border-slate-800/80">
                        <div class="flex justify-between text-tg-hint">
                            <span>Subtotal</span>
                            <span id="summary-subtotal">$0.00</span>
                        </div>
                        <div class="flex justify-between text-tg-hint">
                            <span>Delivery Fee</span>
                            <span id="summary-delivery">$5.00</span>
                        </div>
                        <div id="summary-discount-row" class="flex justify-between text-emerald-600 dark:text-emerald-400 font-medium hidden">
                            <span>Discount</span>
                            <span id="summary-discount">-$0.00</span>
                        </div>
                        <div class="pt-2 border-t border-slate-200 dark:border-slate-700/60 flex justify-between font-bold text-sm text-tg-text">
                            <span>Total</span>
                            <span id="summary-total" class="text-blue-600 dark:text-blue-400">$0.00</span>
                        </div>
                    </div>

                    <!-- Web Fallback Submit Button (Used if WebApp MainButton is not active) -->
                    <button id="web-checkout-btn" onclick="processCheckout()" class="w-full py-3.5 bg-blue-600 text-white font-semibold text-sm rounded-xl shadow-lg shadow-blue-500/25 active:scale-[0.98] transition-all flex items-center justify-center gap-2">
                        <span>Place Order</span>
                        <i data-lucide="arrow-right" class="w-4 h-4"></i>
                    </button>
                </div>
            </section>

            <!-- TAB 3: ORDERS HISTORY -->
            <section id="tab-orders" class="hidden space-y-4">
                <h2 class="text-lg font-bold">Order History</h2>
                
                <div id="orders-list" class="space-y-3">
                    <!-- Populated dynamically -->
                </div>

                <div id="empty-orders" class="text-center py-12 space-y-2">
                    <div class="w-16 h-16 mx-auto text-tg-hint bg-tg-secBg rounded-full flex items-center justify-center">
                        <i data-lucide="clock" class="w-8 h-8"></i>
                    </div>
                    <h3 class="font-semibold text-base">No previous orders</h3>
                    <p class="text-xs text-tg-hint">Your placed orders will appear here for tracking.</p>
                </div>
            </section>
        </main>

        <!-- Product Details Modal -->
        <div id="product-modal" class="fixed inset-0 z-50 glass bg-black/50 hidden items-end sm:items-center justify-center p-0 sm:p-4 transition-opacity">
            <div class="bg-tg-bg border border-slate-200 dark:border-slate-800 rounded-t-3xl sm:rounded-2xl w-full max-w-md max-h-[90vh] overflow-y-auto p-5 space-y-4 animate-in slide-in-from-bottom duration-200">
                <div class="relative">
                    <button onclick="closeProductModal()" class="absolute right-0 top-0 w-8 h-8 rounded-full bg-tg-secBg flex items-center justify-center text-tg-hint hover:text-tg-text z-10">
                        <i data-lucide="x" class="w-4 h-4"></i>
                    </button>
                    <img id="modal-img" src="" alt="" class="w-full h-52 object-cover rounded-2xl bg-tg-secBg">
                </div>

                <div class="space-y-1">
                    <div class="flex items-center justify-between">
                        <span id="modal-badge" class="text-[10px] font-bold uppercase tracking-wider px-2 py-0.5 rounded-md bg-blue-100 text-blue-700 dark:bg-blue-900/50 dark:text-blue-300">Category</span>
                        <span id="modal-stock" class="text-xs text-emerald-600 font-medium">In Stock</span>
                    </div>
                    <h2 id="modal-title" class="text-lg font-bold">Product Title</h2>
                    <p id="modal-price" class="text-xl font-extrabold text-blue-600 dark:text-blue-400">$0.00</p>
                </div>

                <p id="modal-desc" class="text-xs text-tg-hint leading-relaxed">Product description goes here...</p>

                <div class="pt-2">
                    <button id="modal-add-btn" class="w-full py-3 bg-blue-600 text-white font-semibold text-xs rounded-xl shadow-md shadow-blue-500/20 active:scale-95 transition-all flex items-center justify-center gap-2">
                        <i data-lucide="shopping-cart" class="w-4 h-4"></i>
                        <span>Add to Cart</span>
                    </button>
                </div>
            </div>
        </div>

        <!-- Success Modal -->
        <div id="success-modal" class="fixed inset-0 z-50 glass bg-black/60 hidden items-center justify-center p-4">
            <div class="bg-tg-bg border border-slate-200 dark:border-slate-800 rounded-3xl w-full max-w-sm p-6 text-center space-y-4 shadow-2xl">
                <div class="w-16 h-16 bg-emerald-100 dark:bg-emerald-900/50 text-emerald-600 dark:text-emerald-400 rounded-full flex items-center justify-center mx-auto">
                    <i data-lucide="check-circle-2" class="w-10 h-10"></i>
                </div>
                <div class="space-y-1">
                    <h3 class="text-lg font-bold">Order Received!</h3>
                    <p class="text-xs text-tg-hint">Thank you for your order. We have sent the confirmation details to your Telegram account.</p>
                </div>
                <div class="bg-tg-secBg p-3 rounded-xl text-left space-y-1 text-xs">
                    <div class="flex justify-between">
                        <span class="text-tg-hint">Order ID:</span>
                        <span id="success-order-id" class="font-bold">#NOVA-0000</span>
                    </div>
                    <div class="flex justify-between">
                        <span class="text-tg-hint">Amount Paid:</span>
                        <span id="success-order-total" class="font-bold">$0.00</span>
                    </div>
                </div>
                <button onclick="closeSuccessModal()" class="w-full py-3 bg-blue-600 text-white font-semibold text-xs rounded-xl shadow-md active:scale-95 transition-all">
                    Back to Shopping
                </button>
            </div>
        </div>

        <!-- Bottom Navigation Bar -->
        <nav class="fixed bottom-0 left-0 right-0 max-w-md mx-auto z-40 glass border-t border-slate-200/80 dark:border-slate-800/80 flex justify-around py-2.5 px-2">
            <button onclick="switchTab('catalog')" id="nav-catalog" class="nav-item active flex flex-col items-center gap-1 text-tg-hint transition-colors">
                <i data-lucide="grid" class="w-5 h-5"></i>
                <span class="text-[10px] font-medium">Catalog</span>
            </button>
            <button onclick="switchTab('cart')" id="nav-cart" class="nav-item relative flex flex-col items-center gap-1 text-tg-hint transition-colors">
                <i data-lucide="shopping-bag" class="w-5 h-5"></i>
                <span class="text-[10px] font-medium">Cart</span>
                <span id="cart-badge" class="hidden absolute -top-1 right-3 bg-blue-600 text-white text-[10px] font-bold w-4 h-4 rounded-full flex items-center justify-center">0</span>
            </button>
            <button onclick="switchTab('orders')" id="nav-orders" class="nav-item flex flex-col items-center gap-1 text-tg-hint transition-colors">
                <i data-lucide="clock" class="w-5 h-5"></i>
                <span class="text-[10px] font-medium">Orders</span>
            </button>
        </nav>

    </div>

    <!-- Application Script -->
    <script>
        // Sample Product Data
        const PRODUCTS = [
            {
                id: 'p1',
                title: 'Wireless Noise-Canceling Headphones',
                category: 'Electronics',
                price: 129.99,
                stock: 12,
                image: 'https://images.unsplash.com/photo-1505740420928-5e560c06d30e?w=500&q=80',
                description: 'Premium wireless headphones with active noise cancellation, 30-hour battery life, and crystal-clear sound signature.'
            },
            {
                id: 'p2',
                title: 'Smart Fitness Watch Series 5',
                category: 'Electronics',
                price: 89.50,
                stock: 8,
                image: 'https://images.unsplash.com/photo-1523275335684-37898b6baf30?w=500&q=80',
                description: 'Track your workouts, heart rate, sleep, and notifications with a sleek AMOLED touch display and 7-day battery.'
            },
            {
                id: 'p3',
                title: 'Minimalist Cotton Hoodie',
                category: 'Clothing',
                price: 49.99,
                stock: 20,
                image: 'https://images.unsplash.com/photo-1556905055-8f358a7a47b2?w=500&q=80',
                description: 'Ultra-soft 100% organic cotton hoodie. Relaxed fit designed for everyday comfort and modern street style.'
            },
            {
                id: 'p4',
                title: 'Ergonomic Mechanical Keyboard',
                category: 'Electronics',
                price: 110.00,
                stock: 5,
                image: 'https://images.unsplash.com/photo-1587829741301-dc798b83add3?w=500&q=80',
                description: 'RGB customizable mechanical keyboard with tactile switches, hot-swappable keycaps, and aluminum body frame.'
            },
            {
                id: 'p5',
                title: 'Ceramic Pour-Over Coffee Maker',
                category: 'Home',
                price: 34.00,
                stock: 15,
                image: 'https://images.unsplash.com/photo-1514432324607-a09d9b4aefdd?w=500&q=80',
                description: 'Artisanal ceramic dripper for brewing rich, aromatic artisanal coffee at home. Easy to clean and durable.'
            },
            {
                id: 'p6',
                title: 'Leather Daily Backpack',
                category: 'Accessories',
                price: 79.99,
                stock: 9,
                image: 'https://images.unsplash.com/photo-1553062407-98eeb64c6a62?w=500&q=80',
                description: 'Water-resistant genuine leather backpack with dedicated 15-inch laptop sleeve and hidden anti-theft pocket.'
            },
            {
                id: 'p7',
                title: 'Aromatic Soy Wax Candle',
                category: 'Home',
                price: 19.50,
                stock: 25,
                image: 'https://images.unsplash.com/photo-1603006905003-be475563bc59?w=500&q=80',
                description: 'Hand-poured natural soy wax candle infused with essential oils of cedarwood, vanilla, and amber.'
            },
            {
                id: 'p8',
                title: 'Polarized Sunglasses UV400',
                category: 'Accessories',
                price: 29.00,
                stock: 14,
                image: 'https://images.unsplash.com/photo-1511499767150-a48a237f0083?w=500&q=80',
                description: 'Classic timeless frame design with HD polarized lenses providing complete UV400 protection.'
            }
        ];

        // Valid Promo Codes
        const PROMO_CODES = {
            'NOVA10': 0.10, // 10% off
            'TELEGRAM20': 0.20 // 20% off
        };

        // Application State
        let state = {
            cart: {}, // { productId: quantity }
            selectedCategory: 'All',
            searchQuery: '',
            deliveryMethod: 'delivery', // 'delivery' | 'pickup'
            deliveryFee: 5.00,
            appliedPromo: null,
            discountRate: 0,
            ordersHistory: [],
            tgUser: null
        };

        // Initialize Telegram WebApp
        const tg = window.Telegram?.WebApp;

        document.addEventListener('DOMContentLoaded', () => {
            initTelegramApp();
            initData();
            renderCategories();
            renderProducts();
            updateCartUI();
            setupEventListeners();
            lucide.createIcons();
        });

        function initTelegramApp() {
            if (tg) {
                tg.ready();
                tg.expand();

                // Apply Telegram Theme colors if available
                if (tg.colorScheme === 'dark') {
                    document.documentElement.classList.add('dark');
                }

                // Extract user details from Telegram WebApp SDK
                if (tg.initDataUnsafe?.user) {
                    state.tgUser = tg.initDataUnsafe.user;
                    const name = state.tgUser.first_name || 'User';
                    document.getElementById('user-name').textContent = name;
                    document.getElementById('user-avatar-initial').textContent = name.charAt(0).toUpperCase();

                    // Pre-fill user form
                    document.getElementById('cust-name').value = `${state.tgUser.first_name || ''} ${state.tgUser.last_name || ''}`.trim();
                }

                // Listen to MainButton click from Telegram
                tg.MainButton.onClick(() => {
                    processCheckout();
                });
            }
        }

        function initData() {
            // Load persistent orders mock from localStorage
            try {
                const savedOrders = localStorage.getItem('nova_orders');
                if (savedOrders) {
                    state.ordersHistory = JSON.parse(savedOrders);
                    renderOrdersHistory();
                }
            } catch (e) {
                console.error("Could not load stored orders", e);
            }
        }

        function setupEventListeners() {
            // Search Input
            const searchInput = document.getElementById('search-input');
            const clearSearchBtn = document.getElementById('clear-search');

            searchInput.addEventListener('input', (e) => {
                state.searchQuery = e.target.value.trim().toLowerCase();
                clearSearchBtn.classList.toggle('hidden', state.searchQuery === '');
                renderProducts();
            });

            clearSearchBtn.addEventListener('click', () => {
                searchInput.value = '';
                state.searchQuery = '';
                clearSearchBtn.classList.add('hidden');
                renderProducts();
            });

            // Promo code button
            document.getElementById('apply-promo-btn').addEventListener('click', handlePromoApply);

            // Clear Cart Button
            document.getElementById('clear-cart-btn').addEventListener('click', () => {
                state.cart = {};
                updateCartUI();
            });
        }

        // Navigation Switcher
        function switchTab(tabName) {
            ['catalog', 'cart', 'orders'].forEach(t => {
                document.getElementById(`tab-${t}`).classList.add('hidden');
                document.getElementById(`nav-${t}`).classList.remove('active');
            });

            document.getElementById(`tab-${tabName}`).classList.remove('hidden');
            document.getElementById(`nav-${tabName}`).classList.add('active');

            // Sync Telegram MainButton state
            updateTelegramMainButton();
        }

        // Render Categories Bar
        function renderCategories() {
            const categories = ['All', ...new Set(PRODUCTS.map(p => p.category))];
            const container = document.getElementById('categories-container');

            container.innerHTML = categories.map(cat => `
                <button onclick="selectCategory('${cat}')" 
                    class="px-3.5 py-1.5 rounded-xl text-xs font-medium whitespace-nowrap transition-all ${
                        state.selectedCategory === cat 
                        ? 'bg-blue-600 text-white shadow-md shadow-blue-500/20' 
                        : 'bg-tg-secBg text-tg-hint hover:text-tg-text'
                    }">
                    ${cat}
                </button>
            `).join('');
        }

        function selectCategory(cat) {
            state.selectedCategory = cat;
            renderCategories();
            renderProducts();
        }

        // Render Product Catalog
        function renderProducts() {
            const grid = document.getElementById('products-grid');
            const emptyState = document.getElementById('empty-catalog');

            const filtered = PRODUCTS.filter(p => {
                const matchesCategory = state.selectedCategory === 'All' || p.category === state.selectedCategory;
                const matchesSearch = p.title.toLowerCase().includes(state.searchQuery) || p.description.toLowerCase().includes(state.searchQuery);
                return matchesCategory && matchesSearch;
            });

            if (filtered.length === 0) {
                grid.classList.add('hidden');
                emptyState.classList.remove('hidden');
                return;
            }

            grid.classList.remove('hidden');
            emptyState.classList.add('hidden');

            grid.innerHTML = filtered.map(product => {
                const qty = state.cart[product.id] || 0;
                return `
                <div class="bg-tg-secBg rounded-2xl p-2.5 flex flex-col justify-between border border-slate-200/60 dark:border-slate-800/60 shadow-sm hover:shadow-md transition-all">
                    <div class="cursor-pointer" onclick="openProductModal('${product.id}')">
                        <div class="relative w-full aspect-square rounded-xl overflow-hidden mb-2 bg-slate-200 dark:bg-slate-800">
                            <img src="${product.image}" alt="${product.title}" loading="lazy" class="w-full h-full object-cover">
                            <span class="absolute top-1.5 left-1.5 bg-black/60 backdrop-blur-md text-white text-[9px] font-semibold px-2 py-0.5 rounded-full">
                                ${product.category}
                            </span>
                        </div>
                        <h3 class="font-semibold text-xs leading-snug line-clamp-2 mb-1">${product.title}</h3>
                        <p class="text-xs font-extrabold text-blue-600 dark:text-blue-400 mb-2">$${product.price.toFixed(2)}</p>
                    </div>

                    ${qty > 0 ? `
                        <div class="flex items-center justify-between bg-blue-600 text-white rounded-xl p-1 shadow-sm">
                            <button onclick="updateQty('${product.id}', -1)" class="w-6 h-6 flex items-center justify-center rounded-lg hover:bg-blue-700 active:scale-90 transition-all">
                                <i data-lucide="minus" class="w-3.5 h-3.5"></i>
                            </button>
                            <span class="text-xs font-bold px-1">${qty}</span>
                            <button onclick="updateQty('${product.id}', 1)" class="w-6 h-6 flex items-center justify-center rounded-lg hover:bg-blue-700 active:scale-90 transition-all">
                                <i data-lucide="plus" class="w-3.5 h-3.5"></i>
                            </button>
                        </div>
                    ` : `
                        <button onclick="updateQty('${product.id}', 1)" class="w-full py-1.5 bg-slate-200 dark:bg-slate-800 hover:bg-blue-600 hover:text-white dark:hover:bg-blue-600 text-tg-text text-xs font-semibold rounded-xl transition-all flex items-center justify-center gap-1 active:scale-95">
                            <i data-lucide="plus" class="w-3.5 h-3.5"></i>
                            <span>Add</span>
                        </button>
                    `}
                </div>
            `}).join('');

            lucide.createIcons();
        }

        // Quantity Modify Handler
        function updateQty(productId, delta) {
            const currentQty = state.cart[productId] || 0;
            const newQty = currentQty + delta;

            if (newQty <= 0) {
                delete state.cart[productId];
            } else {
                state.cart[productId] = newQty;
            }

            updateCartUI();
            renderProducts();

            // Animate badge
            const badge = document.getElementById('cart-badge');
            badge.classList.remove('badge-pop');
            void badge.offsetWidth; // trigger reflow
            badge.classList.add('badge-pop');
        }

        // Cart Sync & UI Updates
        function updateCartUI() {
            const cartContainer = document.getElementById('cart-items-container');
            const emptyCartView = document.getElementById('empty-cart-view');
            const checkoutSection = document.getElementById('checkout-section');
            const clearCartBtn = document.getElementById('clear-cart-btn');
            const badge = document.getElementById('cart-badge');

            const cartItemIds = Object.keys(state.cart);
            const totalItemsCount = Object.values(state.cart).reduce((a, b) => a + b, 0);

            // Update Badge
            if (totalItemsCount > 0) {
                badge.textContent = totalItemsCount;
                badge.classList.remove('hidden');
                clearCartBtn.classList.remove('hidden');
            } else {
                badge.classList.add('hidden');
                clearCartBtn.classList.add('hidden');
            }

            if (cartItemIds.length === 0) {
                cartContainer.innerHTML = '';
                emptyCartView.classList.remove('hidden');
                checkoutSection.classList.add('hidden');
                updateTelegramMainButton();
                return;
            }

            emptyCartView.classList.add('hidden');
            checkoutSection.classList.remove('hidden');

            // Build Cart List Items
            let subtotal = 0;
            cartContainer.innerHTML = cartItemIds.map(id => {
                const product = PRODUCTS.find(p => p.id === id);
                const qty = state.cart[id];
                const itemTotal = product.price * qty;
                subtotal += itemTotal;

                return `
                    <div class="flex items-center justify-between p-2.5 bg-tg-secBg rounded-2xl border border-slate-200/60 dark:border-slate-800/60">
                        <div class="flex items-center gap-3">
                            <img src="${product.image}" class="w-12 h-12 rounded-xl object-cover bg-slate-200 dark:bg-slate-800" alt="">
                            <div>
                                <h4 class="text-xs font-bold line-clamp-1">${product.title}</h4>
                                <p class="text-[11px] text-tg-hint">$${product.price.toFixed(2)} × ${qty}</p>
                                <p class="text-xs font-extrabold text-blue-600 dark:text-blue-400">$${itemTotal.toFixed(2)}</p>
                            </div>
                        </div>

                        <div class="flex items-center gap-2 bg-tg-bg border border-slate-200 dark:border-slate-800 rounded-xl p-1">
                            <button onclick="updateQty('${product.id}', -1)" class="w-5 h-5 flex items-center justify-center text-tg-hint hover:text-tg-text">
                                <i data-lucide="minus" class="w-3 h-3"></i>
                            </button>
                            <span class="text-xs font-bold min-w-[14px] text-center">${qty}</span>
                            <button onclick="updateQty('${product.id}', 1)" class="w-5 h-5 flex items-center justify-center text-tg-hint hover:text-tg-text">
                                <i data-lucide="plus" class="w-3 h-3"></i>
                            </button>
                        </div>
                    </div>
                `;
            }).join('');

            // Calculate Order Financials
            const discountAmount = subtotal * state.discountRate;
            const finalTotal = Math.max(0, subtotal - discountAmount + state.deliveryFee);

            document.getElementById('summary-subtotal').textContent = `$${subtotal.toFixed(2)}`;
            document.getElementById('summary-delivery').textContent = state.deliveryMethod === 'pickup' ? 'Free' : `$${state.deliveryFee.toFixed(2)}`;
            
            if (state.discountRate > 0) {
                document.getElementById('summary-discount-row').classList.remove('hidden');
                document.getElementById('summary-discount').textContent = `-$${discountAmount.toFixed(2)}`;
            } else {
                document.getElementById('summary-discount-row').classList.add('hidden');
            }

            document.getElementById('summary-total').textContent = `$${finalTotal.toFixed(2)}`;

            lucide.createIcons();
            updateTelegramMainButton();
        }

        function setDeliveryMethod(method) {
            state.deliveryMethod = method;
            const delBtn = document.getElementById('btn-delivery');
            const picBtn = document.getElementById('btn-pickup');
            const addrGroup = document.getElementById('address-field-group');
            const addrInput = document.getElementById('cust-address');

            if (method === 'delivery') {
                state.deliveryFee = 5.00;
                delBtn.className = "flex flex-col items-center justify-center p-3 rounded-xl border-2 border-blue-600 bg-blue-50 dark:bg-blue-950/40 text-blue-600 font-medium transition-all";
                picBtn.className = "flex flex-col items-center justify-center p-3 rounded-xl border-2 border-slate-200 dark:border-slate-800 bg-tg-secBg text-tg-hint font-medium transition-all";
                addrGroup.classList.remove('hidden');
                addrInput.setAttribute('required', 'true');
            } else {
                state.deliveryFee = 0.00;
                picBtn.className = "flex flex-col items-center justify-center p-3 rounded-xl border-2 border-blue-600 bg-blue-50 dark:bg-blue-950/40 text-blue-600 font-medium transition-all";
                delBtn.className = "flex flex-col items-center justify-center p-3 rounded-xl border-2 border-slate-200 dark:border-slate-800 bg-tg-secBg text-tg-hint font-medium transition-all";
                addrGroup.classList.add('hidden');
                addrInput.removeAttribute('required');
            }

            updateCartUI();
        }

        function handlePromoApply() {
            const promoInput = document.getElementById('promo-input');
            const msg = document.getElementById('promo-message');
            const code = promoInput.value.trim().toUpperCase();

            if (PROMO_CODES[code]) {
                state.appliedPromo = code;
                state.discountRate = PROMO_CODES[code];
                msg.textContent = `Promo code '${code}' applied (${state.discountRate * 100}% OFF)!`;
                msg.className = "text-[11px] font-medium text-emerald-600 dark:text-emerald-400";
                msg.classList.remove('hidden');
            } else {
                state.appliedPromo = null;
                state.discountRate = 0;
                msg.textContent = "Invalid or expired promo code.";
                msg.className = "text-[11px] font-medium text-red-500";
                msg.classList.remove('hidden');
            }

            updateCartUI();
        }

        // Telegram Native MainButton integration
        function updateTelegramMainButton() {
            if (!tg) return;

            const isCartTab = !document.getElementById('tab-cart').classList.contains('hidden');
            const hasItems = Object.keys(state.cart).length > 0;

            if (isCartTab && hasItems) {
                const subtotal = Object.entries(state.cart).reduce((sum, [id, qty]) => {
                    const product = PRODUCTS.find(p => p.id === id);
                    return sum + (product.price * qty);
                }, 0);

                const discount = subtotal * state.discountRate;
                const total = Math.max(0, subtotal - discount + state.deliveryFee);

                tg.MainButton.setText(`CHECKOUT ($${total.toFixed(2)})`);
                tg.MainButton.show();
                // Hide web fallback button when TG MainButton active
                document.getElementById('web-checkout-btn').classList.add('hidden');
            } else {
                tg.MainButton.hide();
                document.getElementById('web-checkout-btn').classList.remove('hidden');
            }
        }

        // Checkout Process Handler
        function processCheckout() {
            const form = document.getElementById('checkout-form');
            if (!form.checkValidity()) {
                form.reportValidity();
                return;
            }

            const name = document.getElementById('cust-name').value;
            const phone = document.getElementById('cust-phone').value;
            const address = state.deliveryMethod === 'delivery' ? document.getElementById('cust-address').value : 'Store Pickup';
            const comment = document.getElementById('cust-comment').value;

            // Calculate Final Pricing
            const subtotal = Object.entries(state.cart).reduce((sum, [id, qty]) => {
                const product = PRODUCTS.find(p => p.id === id);
                return sum + (product.price * qty);
            }, 0);

            const discount = subtotal * state.discountRate;
            const total = Math.max(0, subtotal - discount + state.deliveryFee);

            // Construct Order Object
            const order = {
                id: 'NOVA-' + Math.floor(100000 + Math.random() * 900000),
                date: new Date().toLocaleDateString('en-US', { month: 'short', day: 'numeric', hour: '2-digit', minute: '2-digit' }),
                items: Object.entries(state.cart).map(([id, qty]) => {
                    const prod = PRODUCTS.find(p => p.id === id);
                    return { id, title: prod.title, price: prod.price, qty };
                }),
                customer: { name, phone, address, comment },
                deliveryMethod: state.deliveryMethod,
                total: total,
                status: 'Processing'
            };

            // Save Order to Local History
            state.ordersHistory.unshift(order);
            try {
                localStorage.setItem('nova_orders', JSON.stringify(state.ordersHistory));
            } catch (e) {
                console.error("Storage save failed", e);
            }

            // Send Data back to Telegram Bot if available
            if (tg && tg.sendData) {
                try {
                    tg.sendData(JSON.stringify(order));
                } catch (e) {
                    console.log("tg.sendData error", e);
                }
            }

            // Reset Cart
            state.cart = {};
            state.appliedPromo = null;
            state.discountRate = 0;
            document.getElementById('promo-input').value = '';
            document.getElementById('promo-message').classList.add('hidden');

            updateCartUI();
            renderProducts();
            renderOrdersHistory();

            // Show Confirmation Modal
            document.getElementById('success-order-id').textContent = `#${order.id}`;
            document.getElementById('success-order-total').textContent = `$${total.toFixed(2)}`;
            document.getElementById('success-modal').classList.remove('hidden');
            document.getElementById('success-modal').classList.add('flex');
        }

        function closeSuccessModal() {
            document.getElementById('success-modal').classList.add('hidden');
            document.getElementById('success-modal').classList.remove('flex');
            switchTab('orders');
        }

        // Render Orders History
        function renderOrdersHistory() {
            const container = document.getElementById('orders-list');
            const emptyState = document.getElementById('empty-orders');

            if (state.ordersHistory.length === 0) {
                container.innerHTML = '';
                emptyState.classList.remove('hidden');
                return;
            }

            emptyState.classList.add('hidden');

            container.innerHTML = state.ordersHistory.map(order => `
                <div class="bg-tg-secBg border border-slate-200/60 dark:border-slate-800/60 rounded-2xl p-3.5 space-y-2.5">
                    <div class="flex items-center justify-between">
                        <div>
                            <span class="text-xs font-bold">${order.id}</span>
                            <p class="text-[10px] text-tg-hint">${order.date}</p>
                        </div>
                        <span class="text-[10px] font-bold px-2.5 py-0.5 rounded-full bg-amber-100 text-amber-700 dark:bg-amber-900/40 dark:text-amber-300">
                            ${order.status}
                        </span>
                    </div>

                    <div class="border-t border-b border-slate-200/60 dark:border-slate-700/50 py-2 space-y-1">
                        ${order.items.map(item => `
                            <div class="flex justify-between text-xs">
                                <span class="text-tg-hint line-clamp-1">${item.title} x${item.qty}</span>
                                <span class="font-semibold">$${(item.price * item.qty).toFixed(2)}</span>
                            </div>
                        `).join('')}
                    </div>

                    <div class="flex items-center justify-between text-xs pt-0.5">
                        <span class="text-tg-hint">${order.deliveryMethod === 'delivery' ? 'Courier Delivery' : 'Store Pickup'}</span>
                        <span class="font-extrabold text-blue-600 dark:text-blue-400">$${order.total.toFixed(2)}</span>
                    </div>
                </div>
            `).join('');
        }

        // Product Modal Functions
        function openProductModal(productId) {
            const product = PRODUCTS.find(p => p.id === productId);
            if (!product) return;

            document.getElementById('modal-img').src = product.image;
            document.getElementById('modal-title').textContent = product.title;
            document.getElementById('modal-badge').textContent = product.category;
            document.getElementById('modal-price').textContent = `$${product.price.toFixed(2)}`;
            document.getElementById('modal-desc').textContent = product.description;

            const modalAddBtn = document.getElementById('modal-add-btn');
            modalAddBtn.onclick = () => {
                updateQty(product.id, 1);
                closeProductModal();
            };

            const modal = document.getElementById('product-modal');
            modal.classList.remove('hidden');
            modal.classList.add('flex');
        }

        function closeProductModal() {
            const modal = document.getElementById('product-modal');
            modal.classList.add('hidden');
            modal.classList.remove('flex');
        }
    </script>
</body>
</html>
```