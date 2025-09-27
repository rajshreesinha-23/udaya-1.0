<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Campus Carbon Credit Marketplace</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f7f7f7;
        }
        .container {
            max-width: 1200px;
        }
        .card {
            background-color: #fff;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
            transition: transform 0.2s;
        }
        .card:hover {
            transform: translateY(-5px);
        }
        .marketplace-item {
            cursor: pointer;
            transition: transform 0.2s, box-shadow 0.2s;
        }
        .marketplace-item:hover {
            transform: scale(1.03);
            box-shadow: 0 6px 12px rgba(0, 0, 0, 0.15);
        }
        .loading-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(255, 255, 255, 0.8);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }
        .loader {
            border: 8px solid #f3f3f3;
            border-top: 8px solid #3b82f6;
            border-radius: 50%;
            width: 60px;
            height: 60px;
            animation: spin 1s linear infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        .message-box {
            position: fixed;
            top: 20px;
            left: 50%;
            transform: translateX(-50%);
            padding: 1rem 2rem;
            border-radius: 9999px;
            color: white;
            z-index: 1000;
            font-weight: bold;
            display: none;
            box-shadow: 0 4px 12px rgba(0,0,0,0.2);
            transition: all 0.3s ease-in-out;
        }
        .message-box.success {
            background-color: #10B981;
        }
        .message-box.error {
            background-color: #EF4444;
        }
    </style>
</head>
<body class="bg-gray-100">

    <!-- Message Box -->
    <div id="messageBox" class="message-box"></div>
    
    <!-- Loading Overlay -->
    <div id="loadingOverlay" class="loading-overlay hidden">
        <div class="loader"></div>
    </div>

    <!-- Header -->
    <header class="bg-white shadow-sm sticky top-0 z-50">
        <div class="container mx-auto flex justify-between items-center p-4">
            <h1 class="text-3xl font-bold text-gray-800">Campus Carbon Credit Marketplace</h1>
            <div id="auth-section" class="flex items-center space-x-4">
                <p id="user-address" class="text-gray-600 font-medium hidden"></p>
                <button id="connect-wallet-btn" class="bg-blue-500 text-white font-semibold py-2 px-6 rounded-full shadow-lg hover:bg-blue-600 transition duration-300">
                    Connect Wallet
                </button>
                <p id="network-info" class="text-gray-500 text-sm hidden"></p>
            </div>
        </div>
    </header>

    <!-- Main Content -->
    <main class="container mx-auto p-6">

        <!-- User Dashboard Section -->
        <section id="dashboard" class="mb-8 p-6 card rounded-xl">
            <h2 class="text-2xl font-bold mb-4 text-gray-800">Your Dashboard</h2>
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
                <div class="bg-gray-50 p-4 rounded-lg flex items-center space-x-4">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-10 w-10 text-green-500" viewBox="0 0 24 24" fill="currentColor"><path d="M12 2C6.486 2 2 6.486 2 12s4.486 10 10 10 10-4.486 10-10S17.514 2 12 2zm-1 16h2v-7h-2v7zm0-9h2V6h-2v3z"/></svg>
                    <div>
                        <p class="text-gray-500">Carbon Credits</p>
                        <p id="credits-balance" class="text-3xl font-bold text-gray-800">0</p>
                    </div>
                </div>
                <div class="bg-gray-50 p-4 rounded-lg flex items-center space-x-4">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-10 w-10 text-yellow-500" viewBox="0 0 24 24" fill="currentColor"><path d="M12 2c-5.523 0-10 4.477-10 10s4.477 10 10 10 10-4.477 10-10-4.477-10-10-10zm0 18c-4.411 0-8-3.589-8-8s3.589-8 8-8 8 3.589 8 8-3.589 8-8 8zm-2-12h4v2h-4v-2zm0 4h4v4h-4v-4z"/></svg>
                    <div>
                        <p class="text-gray-500">GPA Contribution</p>
                        <p id="gpa-contribution" class="text-3xl font-bold text-gray-800">+0.00</p>
                    </div>
                </div>
                <div class="bg-gray-50 p-4 rounded-lg flex items-center space-x-4">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-10 w-10 text-red-500" viewBox="0 0 24 24" fill="currentColor"><path d="M12 2c-5.523 0-10 4.477-10 10s4.477 10 10 10 10-4.477 10-10-4.477-10-10-10zm0 18c-4.411 0-8-3.589-8-8s3.589-8 8-8 8 3.589 8 8-3.589 8-8 8zm-2-12h4v2h-4v-2zm0 4h4v4h-4v-4z"/></svg>
                    <div>
                        <p class="text-gray-500">CO2 Saved (kg)</p>
                        <p id="co2-saved" class="text-3xl font-bold text-gray-800">0</p>
                    </div>
                </div>
                <div class="bg-gray-50 p-4 rounded-lg flex items-center space-x-4">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-10 w-10 text-blue-500" viewBox="0 0 24 24" fill="currentColor"><path d="M12 2c-5.523 0-10 4.477-10 10s4.477 10 10 10 10-4.477 10-10-4.477-10-10-10zm0 18c-4.411 0-8-3.589-8-8s3.589-8 8-8 8 3.589 8 8-3.589 8-8 8zm-2-12h4v2h-4v-2zm0 4h4v4h-4v-4z"/></svg>
                    <div>
                        <p class="text-gray-500">Actions Logged</p>
                        <p id="actions-logged" class="text-3xl font-bold text-gray-800">0</p>
                    </div>
                </div>
            </div>
        </section>

        <!-- Log an Action Section -->
        <section id="log-action-section" class="mb-8 p-6 card rounded-xl">
            <h2 class="text-2xl font-bold mb-4 text-gray-800">Log a Sustainable Action</h2>
            <div class="flex flex-col space-y-4 md:flex-row md:space-y-0 md:space-x-4">
                <select id="action-type" class="flex-1 p-3 border border-gray-300 rounded-lg">
                    <option value="">Select an action...</option>
                    <option value="assignments">Submitting assignments(min.2)</option>
                    <option value="open source tool">Developed an open source tool for sustainability</option>
                    <option value="workshops">Attended eco-workshops(min.2)</option>
                    <option value="energy">Reducing power usage (1Kwh)</option>
                </select>
                <button id="log-action-btn" class="bg-green-500 text-white font-semibold py-3 px-6 rounded-full shadow-lg hover:bg-green-600 transition duration-300">
                    Log Action
                </button>
            </div>
        </section>

        <!-- Marketplace Section -->
        <section id="marketplace-section" class="p-6 card rounded-xl">
            <h2 class="text-2xl font-bold mb-4 text-gray-800">Redeem Credits for Academic Perks</h2>
            <div id="marketplace-grid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                <!-- Marketplace items will be populated here by JavaScript -->
            </div>
        </section>

    </main>

    <footer class="text-center p-4 text-gray-500 mt-8">
        <p>&copy; 2025 Campus Carbon Credit Marketplace. All rights reserved.</p>
    </footer>

    <script>
        // --- DATA AND STATE MANAGEMENT ---
        let userCredits = 0;
        let userCO2 = 0;
        let userGPA = 0.00;
        let actionsLogged = 0;
        let isWalletConnected = false;
        let userAddress = '';

        const marketplaceItems = [
            { id: 1, title: "0.1 GPA Boost", cost: 100, type: "gpa" },
            { id: 2, title: "Course Waiver", cost: 500, type: "waiver" },
            { id: 3, title: "Priority Class Registration", cost: 250, type: "priority" },
            { id: 4, title: "Sustainability Practicum Spot", cost: 1000, type: "practicum" },
        ];

        // --- DOM ELEMENTS ---
        const connectWalletBtn = document.getElementById('connect-wallet-btn');
        const userAddressElem = document.getElementById('user-address');
        const creditsBalanceElem = document.getElementById('credits-balance');
        const gpaContributionElem = document.getElementById('gpa-contribution');
        const co2SavedElem = document.getElementById('co2-saved');
        const actionsLoggedElem = document.getElementById('actions-logged');
        const logActionBtn = document.getElementById('log-action-btn');
        const actionTypeSelect = document.getElementById('action-type');
        const marketplaceGrid = document.getElementById('marketplace-grid');
        const loadingOverlay = document.getElementById('loadingOverlay');
        const messageBox = document.getElementById('messageBox');

        // --- FUNCTIONS ---

        function showMessage(message, type = 'success') {
            messageBox.textContent = message;
            messageBox.className = `message-box ${type}`;
            messageBox.style.display = 'block';
            setTimeout(() => {
                messageBox.style.display = 'none';
            }, 3000);
        }

        function updateDashboard() {
            creditsBalanceElem.textContent = userCredits;
            gpaContributionElem.textContent = `+${userGPA.toFixed(2)}`;
            co2SavedElem.textContent = userCO2;
            actionsLoggedElem.textContent = actionsLogged;
        }

        function populateMarketplace() {
            marketplaceGrid.innerHTML = '';
            marketplaceItems.forEach(item => {
                const itemDiv = document.createElement('div');
                itemDiv.className = 'marketplace-item card rounded-xl p-6 border border-gray-200 hover:border-blue-500';
                itemDiv.innerHTML = `
                    <h3 class="text-xl font-semibold mb-2">${item.title}</h3>
                    <p class="text-gray-600 mb-4">Cost: ${item.cost} Credits</p>
                    <button class="redeem-btn bg-blue-500 text-white font-semibold py-2 px-4 rounded-full hover:bg-blue-600 transition duration-300 w-full" data-cost="${item.cost}">
                        Redeem
                    </button>
                `;
                itemDiv.querySelector('.redeem-btn').addEventListener('click', () => redeemCredit(item));
                marketplaceGrid.appendChild(itemDiv);
            });
        }

        function redeemCredit(item) {
            if (!isWalletConnected) {
                showMessage("Please connect your wallet first.", "error");
                return;
            }

            if (userCredits >= item.cost) {
                // Simulate a blockchain transaction
                showLoading(true);
                setTimeout(() => {
                    userCredits -= item.cost;
                    if (item.type === 'gpa') {
                        userGPA += 0.1;
                    }
                    updateDashboard();
                    showLoading(false);
                    showMessage(`Successfully redeemed "${item.title}"!`);
                }, 2000);
            } else {
                showMessage("Not enough Carbon Credits!", "error");
            }
        }

        function logAction() {
            if (!isWalletConnected) {
                showMessage("Please connect your wallet first.", "error");
                return;
            }
            
            const selectedAction = actionTypeSelect.value;
            if (!selectedAction) {
                showMessage("Please select an action to log.", "error");
                return;
            }

            // Simulate AI verification and blockchain transaction
            showLoading(true);
            setTimeout(() => {
                actionsLogged++;
                let creditsEarned = 0;
                let co2Saved = 0;

                switch(selectedAction) {
                    case 'bike':
                        creditsEarned = 10;
                        co2Saved = 1;
                        break;
                    case 'recycle':
                        creditsEarned = 5;
                        co2Saved = 0.5;
                        break;
                    case 'compost':
                        creditsEarned = 3;
                        co2Saved = 0.3;
                        break;
                    case 'energy':
                        creditsEarned = 15;
                        co2Saved = 1.5;
                        break;
                }
                userCredits += creditsEarned;
                userCO2 += co2Saved;
                updateDashboard();
                showLoading(false);
                showMessage(`Logged action! You earned ${creditsEarned} credits.`);
                actionTypeSelect.value = '';
            }, 2000);
        }

        function showLoading(show) {
            if (show) {
                loadingOverlay.classList.remove('hidden');
            } else {
                loadingOverlay.classList.add('hidden');
            }
        }

        async function connectWallet() {
            if (typeof window.ethereum !== 'undefined') {
                try {
                    showLoading(true);
                    const accounts = await window.ethereum.request({ method: 'eth_requestAccounts' });
                    userAddress = accounts[0];
                    isWalletConnected = true;

                    // Update UI
                    connectWalletBtn.textContent = 'Wallet Connected';
                    connectWalletBtn.disabled = true;
                    userAddressElem.textContent = `Wallet: ${userAddress.slice(0, 6)}...${userAddress.slice(-4)}`;
                    userAddressElem.classList.remove('hidden');

                    showMessage("Wallet connected successfully!");
                } catch (error) {
                    console.error("User denied account access:", error);
                    showMessage("Connection failed. Please allow access to your wallet.", "error");
                } finally {
                    showLoading(false);
                }
            } else {
                showMessage("MetaMask or a similar wallet is not installed.", "error");
            }
        }

        // --- EVENT LISTENERS ---
        connectWalletBtn.addEventListener('click', connectWallet);
        logActionBtn.addEventListener('click', logAction);

        // --- INITIALIZATION ---
        document.addEventListener('DOMContentLoaded', () => {
            updateDashboard();
            populateMarketplace();
        });

    </script>
</body>
</html>

