<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>BD Income Pro</title>
    <!-- Icons -->
    <link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <!-- Telegram Web App SDK -->
    <script src="https://telegram.org/js/telegram-web-app.js"></script>

    <!-- Ad Network SDK -->
    <script src='//libtl.com/sdk.js' data-zone='10345622' data-sdk='show_10345622'></script>

    <style>
        :root {
            --primary-color: #6200ea;
            --secondary-color: #00c853;
            --accent-color: #2979ff;
            --background-color: #f3f4f6;
            --card-background: #ffffff;
            --text-color: #1f2937;
            --text-muted: #6b7280;
        }

        /* Prevent Scrolling & Bouncing Completely */
        html, body {
            font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--background-color);
            margin: 0;
            padding: 0;
            color: var(--text-color);
            user-select: none;
            -webkit-tap-highlight-color: transparent;
            overflow: hidden;
            height: 100%;
            width: 100%;
            position: fixed; /* Completely Fixed Screen */
            touch-action: none; /* Disable touch scrolling/moving */
        }

        /* ==============================
           BEAUTIFUL LOADING SCREEN
           ============================== */
        #loading-screen {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            z-index: 9999;
            transition: opacity 0.6s ease-in-out, visibility 0.6s;
        }

        .loader-content {
            text-align: center;
            color: white;
            animation: float 3s ease-in-out infinite;
        }

        .logo-circle {
            width: 100px;
            height: 100px;
            background: rgba(255, 255, 255, 0.2);
            border-radius: 50%;
            margin: 0 auto 20px;
            display: flex;
            align-items: center;
            justify-content: center;
            box-shadow: 0 0 20px rgba(255, 255, 255, 0.3), inset 0 0 20px rgba(255, 255, 255, 0.2);
            position: relative;
        }

        .logo-circle::before {
            content: '';
            position: absolute;
            width: 100%;
            height: 100%;
            border-radius: 50%;
            border: 3px solid transparent;
            border-top-color: #00e5ff;
            border-bottom-color: #00e5ff;
            animation: spin 2s linear infinite;
        }

        .logo-icon {
            font-size: 50px;
            color: #fff;
        }

        .app-title {
            font-size: 28px;
            font-weight: 800;
            margin: 0;
            text-transform: uppercase;
            letter-spacing: 2px;
            text-shadow: 0 4px 10px rgba(0,0,0,0.2);
        }

        .app-version {
            font-size: 14px;
            color: rgba(255, 255, 255, 0.8);
            margin-top: 5px;
            letter-spacing: 4px;
            font-weight: 500;
        }

        .loading-bar-container {
            width: 200px;
            height: 4px;
            background: rgba(255, 255, 255, 0.3);
            border-radius: 2px;
            margin-top: 30px;
            overflow: hidden;
            position: relative;
        }

        .loading-bar {
            width: 0%;
            height: 100%;
            background: #00e5ff;
            border-radius: 2px;
            animation: progress 2.5s ease-in-out forwards;
            box-shadow: 0 0 10px #00e5ff;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        @keyframes float {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-10px); }
        }

        @keyframes progress {
            0% { width: 0%; }
            100% { width: 100%; }
        }

        /* ==============================
           MAIN APP CONTAINER
           ============================== */
        .container {
            max-width: 480px;
            height: 100vh;
            margin: 0 auto;
            background-color: var(--card-background);
            display: flex;
            flex-direction: column;
            position: relative;
            opacity: 0;
            transform: scale(0.95);
            transition: opacity 0.6s ease, transform 0.6s ease;
            overflow: hidden; /* Internal content fixed */
        }
        
        .container.show {
            opacity: 1;
            transform: scale(1);
        }

        .header {
            display: flex;
            align-items: center;
            padding: 15px 20px;
            background: linear-gradient(135deg, var(--primary-color), #3700b3);
            color: white;
            position: sticky;
            top: 0;
            z-index: 100;
            box-shadow: 0 4px 15px rgba(0,0,0,0.15);
            flex-shrink: 0;
        }
        
        .header-profile-pic {
            width: 45px;
            height: 45px;
            border-radius: 50%;
            margin-right: 15px;
            border: 2px solid #fff;
            object-fit: cover;
            background: #eee;
        }

        .header-user-info h3 { margin: 0; font-size: 17px; font-weight: 600; letter-spacing: 0.5px; }
        .header-user-info p { margin: 4px 0 0 0; font-size: 12px; opacity: 0.9; }

        .content { 
            flex-grow: 1; 
            padding: 20px; 
            overflow-y: auto; /* Allow ONLY internal scrolling */
            padding-bottom: 90px;
            -webkit-overflow-scrolling: touch;
            scrollbar-width: none; 
            -ms-overflow-style: none;
        }
        .content::-webkit-scrollbar { display: none; }

        .card {
            background: var(--card-background);
            border-radius: 20px;
            padding: 25px;
            margin-bottom: 20px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.05);
            text-align: center;
            border: 1px solid #f8f9fa;
            transition: transform 0.3s;
        }
        .card:hover { transform: translateY(-2px); }

        .balance-card {
            background: linear-gradient(135deg, #ffffff 0%, #f3f4f6 100%);
            border: 1px solid #e0e0e0;
        }

        .balance-label { font-size: 14px; color: var(--text-muted); font-weight: 600; text-transform: uppercase; letter-spacing: 1px; margin-bottom: 5px;}
        .balance-amount {
            font-size: 42px;
            font-weight: 800;
            color: var(--primary-color);
            margin: 5px 0 20px 0;
            letter-spacing: -1px;
            text-shadow: 2px 2px 0px rgba(0,0,0,0.05);
        }

        .btn-primary {
            background: var(--primary-color);
            color: white;
            border: none;
            padding: 16px;
            width: 100%;
            border-radius: 14px;
            font-size: 16px;
            font-weight: 600;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            margin-bottom: 12px;
            box-shadow: 0 8px 20px rgba(98, 0, 234, 0.3);
            transition: all 0.3s ease;
        }
        .btn-primary:active { transform: scale(0.97); }
        .btn-primary:disabled { background: #b39ddb; cursor: not-allowed; transform: none; box-shadow: none; }
        
        .btn-outline {
            background: white;
            color: var(--primary-color);
            border: 2px solid var(--primary-color);
            box-shadow: none;
        }

        /* Task Items */
        .task-item {
            background: white;
            padding: 16px;
            border-radius: 16px;
            margin-bottom: 15px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            box-shadow: 0 4px 15px rgba(0,0,0,0.03);
            border: 1px solid #f3f4f6;
            transition: 0.2s;
        }
        .task-item:hover { border-color: var(--accent-color); }
        .task-info { text-align: left; flex: 1; padding-right: 15px; }
        .task-title { font-weight: 600; font-size: 15px; color: #333; display: block; margin-bottom: 4px; }
        .task-reward { color: var(--secondary-color); font-weight: 700; font-size: 13px; background: #e8f5e9; padding: 5px 10px; border-radius: 8px; }
        .task-btn {
            background: var(--accent-color);
            color: white;
            border: none;
            padding: 10px 16px;
            border-radius: 10px;
            font-size: 13px;
            font-weight: 600;
            cursor: pointer;
            flex-shrink: 0;
            box-shadow: 0 4px 10px rgba(41, 121, 255, 0.3);
        }
        .task-btn.check { background: #ff9800; box-shadow: 0 4px 10px rgba(255, 152, 0, 0.3); }
        .task-btn.completed { background: #e0e0e0; color: #999; pointer-events: none; box-shadow: none; }

        /* History List */
        .history-list { list-style: none; padding: 0; margin: 0; }
        .history-item { display: flex; justify-content: space-between; align-items: center; padding: 16px 0; border-bottom: 1px solid #f0f0f0; }
        .history-item:last-child { border-bottom: none; }
        .h-left { display: flex; align-items: center; gap: 15px; }
        .h-icon { width: 42px; height: 42px; border-radius: 12px; display: flex; align-items: center; justify-content: center; color: white; font-size: 20px; box-shadow: 0 4px 10px rgba(0,0,0,0.1); }
        .bg-green { background: linear-gradient(135deg, #00c853, #00e676); }
        .bg-red { background: linear-gradient(135deg, #ff1744, #ff5252); }
        .h-details h4 { margin: 0; font-size: 14px; font-weight: 600; }
        .h-details span { font-size: 11px; color: #999; }
        .h-amount { font-weight: bold; font-size: 16px; }
        .text-green { color: var(--secondary-color); }
        .text-red { color: #ff1744; }

        input, select {
            width: 100%;
            padding: 16px;
            margin: 10px 0 20px 0;
            border: 1px solid #e0e0e0;
            border-radius: 12px;
            box-sizing: border-box;
            font-size: 15px;
            background: #fafafa;
            transition: 0.3s;
        }
        input:focus, select:focus { outline: none; border-color: var(--primary-color); background: #fff; box-shadow: 0 0 0 4px rgba(98, 0, 234, 0.1); }

        .footer-nav {
            display: flex;
            justify-content: space-around;
            position: absolute;
            bottom: 0;
            width: 100%;
            max-width: 480px;
            background: white;
            border-top: 1px solid #eee;
            padding: 12px 0;
            z-index: 99;
            box-shadow: 0 -5px 20px rgba(0,0,0,0.05);
            flex-shrink: 0;
        }
        .nav-item { display: flex; flex-direction: column; align-items: center; color: #b0b0b0; cursor: pointer; flex: 1; transition: 0.3s; }
        .nav-item.active { color: var(--primary-color); transform: translateY(-2px); }
        .nav-icon { font-size: 26px; margin-bottom: 4px; transition: 0.3s; }
        .nav-item.active .nav-icon { transform: scale(1.1); }
        .nav-text { font-size: 10px; font-weight: 700; text-transform: uppercase; letter-spacing: 0.5px; }

        .page { display: none; animation: fadeIn 0.4s cubic-bezier(0.25, 1, 0.5, 1); }
        .page.active { display: block; }
        @keyframes fadeIn { from {opacity: 0; transform: translateY(15px);} to {opacity: 1; transform: translateY(0);} }

        .copyright {
            text-align: center;
            font-size: 11px;
            color: #ccc;
            padding-bottom: 10px;
            margin-top: 10px;
            font-weight: 500;
            letter-spacing: 0.5px;
        }
    </style>
</head>
<body>
    <!-- Beautiful Loading Screen -->
    <div id="loading-screen">
        <div class="loader-content">
            <div class="logo-circle">
                <span class="material-icons logo-icon">monetization_on</span>
            </div>
            <h1 class="app-title">BD Income Pro</h1>
            <div class="app-version">v 3.3.0</div>
            <div class="loading-bar-container">
                <div class="loading-bar"></div>
            </div>
        </div>
    </div>

    <div class="container" id="main-app">
        <header class="header">
            <img id="user-pic" src="https://via.placeholder.com/45" class="header-profile-pic" alt="Profile">
            <div class="header-user-info">
                <h3 id="user-name">User</h3>
                <p>Start Earning Today!</p>
            </div>
        </header>

        <main class="content">
            <!-- HOME PAGE -->
            <section id="home-page" class="page active">
                <div class="card balance-card">
                    <div class="balance-label">Total Balance</div>
                    <div class="balance-amount" id="display-balance">৳ 0.00</div>
                    <div style="display: flex; gap: 10px;">
                        <button class="btn-primary" onclick="navTo('earn-page')">
                            <span class="material-icons">play_circle</span> Earn Money
                        </button>
                        <button class="btn-primary btn-outline" onclick="navTo('withdraw-page')">
                            <span class="material-icons">account_balance_wallet</span> Withdraw
                        </button>
                    </div>
                </div>

                <div style="display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 10px; margin-bottom: 20px;">
                    <div style="background: white; padding: 15px; border-radius: 16px; text-align: center; box-shadow: 0 4px 15px rgba(0,0,0,0.03); border: 1px solid #f0f0f0;">
                        <div style="color: var(--primary-color); font-weight: bold; font-size: 18px;" id="stat-ads">0</div>
                        <div style="font-size: 11px; color: #888;">Ads Watched</div>
                    </div>
                    <div style="background: white; padding: 15px; border-radius: 16px; text-align: center; box-shadow: 0 4px 15px rgba(0,0,0,0.03); border: 1px solid #f0f0f0;">
                        <div style="color: var(--accent-color); font-weight: bold; font-size: 18px;" id="stat-tasks">0</div>
                        <div style="font-size: 11px; color: #888;">Tasks Done</div>
                    </div>
                    <div style="background: white; padding: 15px; border-radius: 16px; text-align: center; box-shadow: 0 4px 15px rgba(0,0,0,0.03); border: 1px solid #f0f0f0;">
                        <div style="color: var(--secondary-color); font-weight: bold; font-size: 18px;" id="stat-earned">৳0</div>
                        <div style="font-size: 11px; color: #888;">Total Earned</div>
                    </div>
                </div>

                <div style="background: white; padding: 15px; border-radius: 16px; box-shadow: 0 4px 15px rgba(0,0,0,0.03); border: 1px solid #f0f0f0;">
                    <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 15px;">
                        <h4 style="margin: 0; font-size: 14px; color: #555;">Recent Activity</h4>
                        <span style="font-size: 12px; color: var(--primary-color); cursor: pointer; font-weight: 600;" onclick="navTo('history-page')">View All</span>
                    </div>
                    <ul class="history-list" id="mini-history">
                        <li style="text-align: center; color: #aaa; font-size: 13px; padding: 10px;">No recent activity</li>
                    </ul>
                </div>
            </section>

            <!-- EARN & TASKS PAGE -->
            <section id="earn-page" class="page">
                <div class="card">
                    <h2>Watch Ads & Earn</h2>
                    <p style="color: #666; font-size: 14px;">Daily Limit: <span id="daily-ad-count">0</span>/30</p>
                    
                    <button class="btn-primary" id="watch-ad-btn-1" onclick="watchAd('btn1')">
                        Watch Ad 1 (Reward ৳15)
                        <span class="material-icons">play_circle_filled</span>
                    </button>
                    
                    <button class="btn-primary" style="background: #2979ff;" id="watch-ad-btn-2" onclick="watchAd('btn2')">
                        Watch Ad 2 (Reward ৳15)
                        <span class="material-icons">smart_display</span>
                    </button>
                    
                    <p style="font-size: 12px; color: #888; margin-top: 15px;">* বিজ্ঞাপন দেখে ইনকাম করুন</p>
                </div>

                <h3 style="margin: 0 0 15px 0; font-size: 16px; color: #444;">Join Channels</h3>
                
                <div id="channel-list">
                    <!-- Channels will be generated by JS -->
                </div>
            </section>

            <!-- WITHDRAW PAGE -->
            <section id="withdraw-page" class="page">
                <div class="card">
                    <h3>Withdraw Funds</h3>
                    <p style="font-size: 13px; color: #666; margin: 5px 0;">Minimum Withdraw: <b>৳ 1000</b></p>
                    <h1 id="withdraw-balance" style="color: var(--secondary-color); margin: 10px 0;">৳ 0.00</h1>
                </div>

                <div class="card" style="text-align: left;">
                    <form id="withdraw-form">
                        <label style="font-weight: 600; font-size: 13px; color: #555;">Select Method</label>
                        <select id="method" required onchange="toggleWithdrawFields()">
                            <option value="bkash">bKash</option>
                            <option value="nagad">nagad</option>
                            <option value="rocket">Rocket</option>
                            <option value="bank_transfer">Bank Transfer</option>
                        </select>

                        <!-- Dynamic Fields Container -->
                        <div id="withdraw-fields">
                            <label style="font-weight: 600; font-size: 13px; color: #555;">Mobile Number</label>
                            <input type="number" id="number" placeholder="017xxxxxxxx" required>
                        </div>

                        <label style="font-weight: 600; font-size: 13px; color: #555;">Amount</label>
                        <input type="number" id="amount" placeholder="Enter Amount (Min 1000)" min="1000" required>

                        <button type="submit" class="btn-primary" id="withdraw-btn">
                            <span class="material-icons">send</span> Confirm Request
                        </button>
                    </form>
                </div>
            </section>

            <!-- HISTORY PAGE -->
            <section id="history-page" class="page">
                <h3 style="margin-top: 0; margin-bottom: 15px; color: #333;">Transaction History</h3>
                <div class="card" style="padding: 10px 20px;">
                    <ul class="history-list" id="full-history">
                        <li style="text-align: center; color: #aaa; font-size: 13px; padding: 20px;">No history found.</li>
                    </ul>
                </div>
            </section>

            <!-- PROFILE PAGE -->
            <section id="profile-page" class="page">
                <div class="card">
                    <img id="profile-pic-lg" src="https://via.placeholder.com/80" style="width: 80px; height: 80px; border-radius: 50%; border: 3px solid var(--primary-color); margin-bottom: 10px;">
                    <h3 id="profile-name" style="margin: 5px 0;">User Name</h3>
                    <p id="profile-username" style="color: #888; margin: 0 0 20px 0;">@username</p>
                    <div style="text-align: left;">
                        <div style="padding: 12px 0; border-bottom: 1px solid #eee; display: flex; justify-content: space-between;">
                            <span style="color: #666;">Wallet Balance</span>
                            <span style="font-weight: bold; color: var(--secondary-color);" id="profile-balance">৳ 0.00</span>
                        </div>
                        <div style="padding: 12px 0; display: flex; justify-content: space-between;">
                            <span style="color: #666;">Total Tasks</span>
                            <span style="font-weight: bold; color: #333;" id="profile-total-tasks">0</span>
                        </div>
                    </div>
                </div>
            </section>
        </main>

        <!-- Navigation -->
        <nav class="footer-nav">
            <div class="nav-item active" onclick="navTo('home-page')">
                <span class="material-icons nav-icon">home</span>
                <span class="nav-text">Home</span>
            </div>
            <div class="nav-item" onclick="navTo('earn-page')">
                <span class="material-icons nav-icon">task_alt</span>
                <span class="nav-text">Earn</span>
            </div>
            <div class="nav-item" onclick="navTo('withdraw-page')">
                <span class="material-icons nav-icon">payments</span>
                <span class="nav-text">Wallet</span>
            </div>
            <div class="nav-item" onclick="navTo('history-page')">
                <span class="material-icons nav-icon">history</span>
                <span class="nav-text">History</span>
            </div>
            <div class="nav-item" onclick="navTo('profile-page')">
                <span class="material-icons nav-icon">person</span>
                <span class="nav-text">Profile</span>
            </div>
        </nav>

        <!-- Copyright Section -->
        <div class="copyright">
            Powered by ALAMIN © 2025
        </div>
    </div>

    <script>
        // ========================================================
        // CONFIGURATION
        // ========================================================
        const CONFIG = {
            // 1. Bot Configuration
            BOT_TOKEN: '8390239644:AAFWZJD6Do7IPQb64ktb5SihLWCLeOZUTzU',
            ADMIN_ID: '8188773875',
            LOG_CHANNEL_ID: '@BDIncomep', // Public Log Channel
            
            // 2. Ad Settings
            REWARD_PER_AD: 15,             // 15 Taka per ad
            MAX_DAILY_ADS: 30,             // Max 30 ads per day
            REWARD_CHANNEL_JOIN: 40,       // 40 Taka for channel join
            MIN_WITHDRAW: 1000,            // 1000 Taka minimum
            
            // 3. Channels (You can add/remove links here)
            CHANNELS: [
                { id: 'DevleparX_Namid', link: 'https://t.me/DevleparX_Namid', name: 'DevleparX Namid' },
                { id: 'GmailMarketingB', link: 'https://t.me/GmailMarketingB', name: 'Gmail Marketing B' },
                { id: 'BDIncomep', link: 'https://t.me/BDIncomep', name: 'BD Income P' }
            ]
        };
        // ========================================================

        const tg = window.Telegram.WebApp;
        tg.ready();
        tg.expand();

        const STORE_KEY = 'bd_income_bot_v5';
        
        let state = {
            balance: 0,
            totalEarned: 0,
            adsWatched: 0,
            tasksCompleted: 0,
            completedChannels: {}, 
            history: [],
            // Daily Limit Logic
            dailyAdsWatched: 0,
            lastAdDate: null // Timestamp of last ad watch
        };

        // Check and Reset Daily Limit
        function checkDailyLimit() {
            const now = new Date();
            const lastDate = state.lastAdDate ? new Date(state.lastAdDate) : null;
            
            // If last ad was not today (different day), reset count
            if (!lastDate || now.getDate() !== lastDate.getDate() || now.getMonth() !== lastDate.getMonth() || now.getFullYear() !== lastDate.getFullYear()) {
                state.dailyAdsWatched = 0;
                state.lastAdDate = now.getTime();
                saveData();
            }
            return state.dailyAdsWatched < CONFIG.MAX_DAILY_ADS;
        }

        // Loading Logic with Animation
        window.onload = function() {
            const loadingScreen = document.getElementById('loading-screen');
            const mainApp = document.getElementById('main-app');
            
            // Wait for 3 seconds
            setTimeout(() => {
                loadingScreen.style.opacity = '0';
                loadingScreen.style.visibility = 'hidden';
                mainApp.classList.add('show'); // Trigger scale and fade in
                
                // Check limit on load
                checkDailyLimit();
                loadData();
                
                // --- In-App Interstitial Logic ---
                if (typeof show_10345622 === 'function') {
                    show_10345622({
                      type: 'inApp',
                      inAppSettings: {
                        frequency: 2,
                        capping: 0.1,
                        interval: 30,
                        timeout: 5,
                        everyPage: false
                      }
                    });
                }

            }, 3000);
        };

        function loadData() {
            const saved = localStorage.getItem(STORE_KEY);
            if (saved) {
                state = JSON.parse(saved);
            }
            updateUI();
            loadUser();
        }

        function saveData() {
            localStorage.setItem(STORE_KEY, JSON.stringify(state));
            updateUI();
        }

        function addHistory(type, amount, extra = '') {
            const entry = {
                id: Date.now(),
                type: type,
                amount: amount,
                date: new Date().toLocaleString(),
                extra: extra
            };
            state.history.unshift(entry);
            if (state.history.length > 50) state.history.pop();
            saveData();
        }

        function updateUI() {
            const fmtBal = state.balance.toFixed(2);
            document.getElementById('display-balance').innerText = `৳ ${fmtBal}`;
            document.getElementById('withdraw-balance').innerText = `৳ ${fmtBal}`;
            document.getElementById('profile-balance').innerText = `৳ ${fmtBal}`;
            document.getElementById('profile-total-tasks').innerText = state.tasksCompleted;
            document.getElementById('stat-ads').innerText = state.adsWatched;
            document.getElementById('stat-tasks').innerText = state.tasksCompleted;
            document.getElementById('stat-earned').innerText = `৳${state.totalEarned.toFixed(0)}`;
            document.getElementById('daily-ad-count').innerText = state.dailyAdsWatched;

            const wBtn = document.getElementById('withdraw-btn');
            if(state.balance < CONFIG.MIN_WITHDRAW) {
                wBtn.disabled = true;
                wBtn.innerHTML = `Min ৳${CONFIG.MIN_WITHDRAW} to Withdraw`;
            } else {
                wBtn.disabled = false;
                wBtn.innerHTML = '<span class="material-icons">send</span> Confirm Request';
            }

            renderHistory();
            renderChannels();
        }

        function renderHistory() {
            const list = document.getElementById('full-history');
            const miniList = document.getElementById('mini-history');
            
            if (state.history.length === 0) {
                list.innerHTML = '<li style="text-align: center; color: #aaa; font-size: 13px; padding: 20px;">No history found.</li>';
                miniList.innerHTML = '<li style="text-align: center; color: #aaa; font-size: 13px; padding: 10px;">No recent activity</li>';
                return;
            }

            const generateHTML = (item) => {
                let icon, colorClass, title, sign, amountColor;

                if (item.type === 'withdraw') {
                    icon = 'check_circle'; 
                    colorClass = 'bg-green';
                    title = `Withdraw (${item.extra})`;
                    sign = '-';
                    amountColor = 'text-red';
                } else if (item.type === 'channel_join') {
                    icon = 'add_circle';
                    colorClass = 'bg-green';
                    title = `Joined ${item.extra}`;
                    sign = '+';
                    amountColor = 'text-green';
                } else {
                    icon = 'play_circle';
                    colorClass = 'bg-green';
                    title = 'Ad Watched';
                    sign = '+';
                    amountColor = 'text-green';
                }

                return `
                <li class="history-item">
                    <div class="h-left">
                        <div class="h-icon ${colorClass}">
                            <span class="material-icons">${icon}</span>
                        </div>
                        <div class="h-details">
                            <h4>${title}</h4>
                            <span>${item.date}</span>
                        </div>
                    </div>
                    <div class="h-amount ${amountColor}">${sign}৳${item.amount.toFixed(2)}</div>
                </li>`;
            };

            list.innerHTML = state.history.map(generateHTML).join('');
            const recent = state.history.slice(0, 3);
            miniList.innerHTML = recent.map(generateHTML).join('');
        }

        function renderChannels() {
            const container = document.getElementById('channel-list');
            container.innerHTML = '';

            CONFIG.CHANNELS.forEach(ch => {
                const isCompleted = state.completedChannels[ch.id];
                
                const div = document.createElement('div');
                div.className = 'task-item';
                div.innerHTML = `
                    <div class="task-info">
                        <span class="task-title">${ch.name}</span>
                        <span class="task-reward">+ ৳ ${CONFIG.REWARD_CHANNEL_JOIN.toFixed(2)}</span>
                    </div>
                    ${!isCompleted ? `
                        <button class="task-btn" onclick="joinChannel('${ch.id}')">Join</button>
                    ` : `
                        <button class="task-btn completed">Done</button>
                    `}
                `;
                container.appendChild(div);
            });
        }

        function loadUser() {
            const user = tg.initDataUnsafe.user;
            if (user) {
                const name = user.first_name + ' ' + (user.last_name || '');
                document.getElementById('user-name').innerText = name;
                document.getElementById('profile-name').innerText = name;
                document.getElementById('profile-username').innerText = user.username ? `@${user.username}` : '';
                const picUrl = user.photo_url ? user.photo_url : `https://ui-avatars.com/api/?name=${user.first_name}&background=random`;
                document.getElementById('user-pic').src = picUrl;
                document.getElementById('profile-pic-lg').src = picUrl;
            }
        }

        window.navTo = (pageId) => {
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
            document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
            document.getElementById(pageId).classList.add('active');
            const index = ['home-page', 'earn-page', 'withdraw-page', 'profile-page', 'history-page'].indexOf(pageId);
            if(index >= 0) document.querySelectorAll('.nav-item')[index].classList.add('active');
        };

        // --- Ad Logic with Limit Check ---
        window.watchAd = (btnId) => {
            // Check Daily Limit
            const canWatch = checkDailyLimit();
            if (!canWatch) {
                tg.showAlert(`আপনি আজকের জন্য ${CONFIG.MAX_DAILY_ADS}টি অ্যাডের লিমিট পূরণ করেছেন। কাল আবার চেষ্টা করুন।`);
                return;
            }

            const btn = document.getElementById('watch-ad-btn-' + (btnId === 'btn1' ? '1' : '2'));
            const originalText = btn.innerHTML;
            
            btn.disabled = true;
            btn.innerHTML = 'Loading Ad...';

            if (typeof show_10345622 === 'function') {
                show_10345622('pop') 
                    .then(() => {
                        giveAdReward(btn, originalText);
                    })
                    .catch(e => {
                        tg.showAlert('Ad failed to load. Please check internet.');
                        btn.disabled = false;
                        btn.innerHTML = originalText;
                    });
            } else {
                setTimeout(() => {
                    giveAdReward(btn, originalText);
                }, 2000);
            }
        };

        function giveAdReward(btn, originalText) {
            state.dailyAdsWatched++;
            state.adsWatched++;
            state.totalEarned += CONFIG.REWARD_PER_AD;
            state.balance += CONFIG.REWARD_PER_AD;
            state.lastAdDate = new Date().getTime();
            
            addHistory('ad', CONFIG.REWARD_PER_AD);
            
            tg.showAlert(`Ad Completed! You earned ৳${CONFIG.REWARD_PER_AD}`);
            
            btn.disabled = false;
            btn.innerHTML = originalText;
            saveData();
        }

        // --- Channel Join Logic ---
        window.joinChannel = (channelId) => {
            const channel = CONFIG.CHANNELS.find(c => c.id === channelId);
            if (!channel) return;

            tg.openTelegramLink(channel.link);

            setTimeout(() => {
                tg.showConfirm(`Have you joined ${channel.name}?`, (confirmed) => {
                    if (confirmed) {
                        verifyChannelJoin(channel);
                    }
                });
            }, 3000);
        };

        function verifyChannelJoin(channel) {
            const tgUser = tg.initDataUnsafe.user;
            if (!tgUser) return;

            const userId = tgUser.id;
            const checkUrl = `https://api.telegram.org/bot${CONFIG.BOT_TOKEN}/getChatMember?chat_id=@${channel.id}&user_id=${userId}`;

            fetch(checkUrl)
                .then(res => res.json())
                .then(data => {
                    if (data.ok) {
                        const status = data.result.status;
                        if (['member', 'administrator', 'creator'].includes(status)) {
                            if (!state.completedChannels[channel.id]) {
                                state.balance += CONFIG.REWARD_CHANNEL_JOIN;
                                state.totalEarned += CONFIG.REWARD_CHANNEL_JOIN;
                                state.tasksCompleted++;
                                state.completedChannels[channel.id] = true;
                                addHistory('channel_join', CONFIG.REWARD_CHANNEL_JOIN, channel.name);
                                saveData();
                                tg.showAlert(`Success! You earned ৳${CONFIG.REWARD_CHANNEL_JOIN}`);
                            }
                        } else {
                            tg.showAlert('আপনি চ্যানেলে জয়েন করেননি। দয়া করে জয়েন করে আবার চেক করুন।');
                        }
                    } else {
                        console.error("API Error:", data);
                        tg.showAlert('Verification Error: Bot must be Admin in this channel.');
                    }
                })
                .catch(err => {
                    console.error(err);
                    tg.showAlert('Network error while checking. Please try again.');
                });
        }

        // --- Toggle Withdraw Fields ---
        window.toggleWithdrawFields = () => {
            const method = document.getElementById('method').value;
            const container = document.getElementById('withdraw-fields');
            
            if (method === 'bank_transfer') {
                container.innerHTML = `
                    <label style="font-weight: 600; font-size: 13px; color: #555;">Account Holder Name</label>
                    <input type="text" id="bank_name" placeholder="Name" required>
                    
                    <label style="font-weight: 600; font-size: 13px; color: #555;">Gmail</label>
                    <input type="email" id="bank_email" placeholder="example@gmail.com" required>
                    
                    <label style="font-weight: 600; font-size: 13px; color: #555;">Bank Name</label>
                    <input type="text" id="bank_bank" placeholder="Dutch Bangla Bank" required>
                    
                    <label style="font-weight: 600; font-size: 13px; color: #555;">Account Number</label>
                    <input type="text" id="bank_acc" placeholder="1234567890" required>
                `;
            } else {
                container.innerHTML = `
                    <label style="font-weight: 600; font-size: 13px; color: #555;">Mobile Number</label>
                    <input type="number" id="number" placeholder="017xxxxxxxx" required>
                `;
            }
        };

        // --- Withdraw Logic with Dual Notification ---
        document.getElementById('withdraw-form').addEventListener('submit', (e) => {
            e.preventDefault();

            const method = document.getElementById('method').value;
            let details = "";

            if (method === 'bank_transfer') {
                const name = document.getElementById('bank_name').value;
                const email = document.getElementById('bank_email').value;
                const bankName = document.getElementById('bank_bank').value;
                const accNum = document.getElementById('bank_acc').value;
                details = `Name: ${name}\nEmail: ${email}\nBank: ${bankName}\nAcc No: ${accNum}`;
            } else {
                const number = document.getElementById('number').value;
                details = number;
            }

            const amount = parseFloat(document.getElementById('amount').value);

            if (amount > state.balance) {
                tg.showAlert('Insufficient Balance!');
                return;
            }
            if (amount < CONFIG.MIN_WITHDRAW) {
                tg.showAlert(`Minimum withdraw is ৳${CONFIG.MIN_WITHDRAW}`);
                return;
            }

            tg.showConfirm(`Confirm Withdraw ৳${amount}?`, (ok) => {
                if (ok) {
                    state.balance -= amount;
                    addHistory('withdraw', amount, method);
                    saveData();

                    const user = tg.initDataUnsafe.user;
                    const userInfo = user ? `Name: ${user.first_name}\nID: ${user.id}` : 'Unknown User';
                    
                    // Message for Admin (Private)
                    const adminMsg = `
🟢 <b>NEW WITHDRAW REQUEST</b>
━━━━━━━━━━━━━━━
👤 <b>User:</b> ${userInfo}
💵 <b>Amount:</b> ৳${amount}
🏦 <b>Method:</b> ${method.toUpperCase()}

📋 <b>Details:</b>
 ${details}

📊 <b>User Stats:</b>
👀 Ads Watched: ${state.adsWatched}
🔨 Tasks Done: ${state.tasksCompleted}
📅 Date: ${new Date().toLocaleString()}
━━━━━━━━━━━━━━━
`;

                    // Message for Channel (Public Log)
                    const channelMsg = `
🟢 <b>NEW PAYMENT REQUEST</b>
━━━━━━━━━━━━━━━
💵 <b>Amount:</b> ৳${amount}
🏦 <b>Method:</b> ${method.toUpperCase()}
👤 <b>User ID:</b> ${user ? user.id : 'N/A'}
📅 <b>Date:</b> ${new Date().toLocaleString()}
━━━━━━━━━━━━━━━
<i>Powered by BD Income Bot</i>
`;

                    const url = `https://api.telegram.org/bot${CONFIG.BOT_TOKEN}/sendMessage`;
                    
                    // Send to Admin First
                    fetch(url, {
                        method: 'POST',
                        headers: {'Content-Type': 'application/json'},
                        body: JSON.stringify({
                            chat_id: CONFIG.ADMIN_ID,
                            text: adminMsg,
                            parse_mode: 'HTML'
                        })
                    })
                    .then(() => {
                        // Send to Channel Second
                        return fetch(url, {
                            method: 'POST',
                            headers: {'Content-Type': 'application/json'},
                            body: JSON.stringify({
                                chat_id: CONFIG.LOG_CHANNEL_ID,
                                text: channelMsg,
                                parse_mode: 'HTML'
                            })
                        });
                    })
                    .then(res => res.json())
                    .then(data => {
                        if(data.ok) {
                            tg.showAlert('Withdraw request sent successfully!');
                            document.getElementById('withdraw-form').reset();
                            toggleWithdrawFields(); // Reset input fields
                        } else {
                            console.warn("Channel log failed", data);
                            tg.showAlert('Request sent to Admin! (Log failed)');
                            document.getElementById('withdraw-form').reset();
                            toggleWithdrawFields();
                        }
                    })
                    .catch(err => {
                        state.balance += amount;
                        saveData();
                        tg.showAlert('Network Error.');
                        console.error(err);
                    });
                }
            });
        });
    </script>
</body>
</html>
