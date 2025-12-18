<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Income Ad</title>
    <!-- SDKs -->
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    
    <!-- Monetag Ads SDK -->
    <script src='https://libtl.com/sdk.js' data-zone='10345622' data-sdk='show_10345622' defer></script>

    <style>
        :root { --blue: #5d5fef; --bg: #f4f7fe; }
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background: var(--bg); margin: 0; overflow-x: hidden; padding-bottom: 80px; }
        
        /* এনিমেশন লোডার */
        #loader { position: fixed; width: 100%; height: 100%; background: #ffffff; z-index: 9999; display: flex; flex-direction: column; align-items: center; justify-content: center; }
        .spinner { width: 50px; height: 50px; border: 5px solid #f3f3f3; border-top: 5px solid var(--blue); border-radius: 50%; animation: spin 1s linear infinite; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        .app-name { margin-top: 15px; font-size: 26px; font-weight: 800; color: var(--blue); letter-spacing: 2px; animation: pulse 1.5s infinite; }
        @keyframes pulse { 0% { opacity: 0.4; } 50% { opacity: 1; } 100% { opacity: 0.4; } }

        /* মেইনটেন্যান্স */
        #maintenance { display: none; position: fixed; top: 0; width: 100%; height: 100%; background: #fff; z-index: 8888; text-align: center; padding-top: 80px; }

        .timer-strip { background: #222; color: #00ff00; text-align: center; padding: 6px; font-size: 13px; font-weight: bold; border-bottom: 2px solid var(--blue); }
        .header { background: var(--blue); color: white; padding: 25px 20px 45px; border-radius: 0 0 25px 25px; box-shadow: 0 4px 12px rgba(0,0,0,0.1); }
        .stats-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; padding: 0 15px; margin-top: -30px; }
        .stat-card { background: white; padding: 15px; border-radius: 12px; text-align: center; box-shadow: 0 4px 10px rgba(0,0,0,0.05); }
        
        .nav-bar { position: fixed; bottom: 0; width: 100%; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; z-index: 1000; }
        .nav-item { color: #aaa; text-align: center; font-size: 11px; cursor: pointer; }
        .nav-item.active { color: var(--blue); font-weight: bold; }
        .nav-item i { font-size: 22px; display: block; margin-bottom: 2px; }

        .page { display: none; padding: 15px; }
        .active-page { display: block; }
        .btn-ad { background: var(--blue); color: white; border: none; padding: 15px; width: 100%; border-radius: 12px; font-weight: bold; font-size: 16px; box-shadow: 0 4px 15px rgba(93, 95, 239, 0.3); }
    </style>
</head>
<body>

    <div id="loader">
        <div class="spinner"></div>
        <div class="app-name">INCOME AD</div>
    </div>

    <div id="maintenance">
        <i class="fas fa-tools" style="font-size:60px; color:red;"></i>
        <h2 id="m-title">Closed</h2>
        <p id="m-msg">App is under maintenance.</p>
        <h3 id="m-countdown" style="color:red;">Opening in: 00:00</h3>
    </div>

    <div class="timer-strip"> RUNTIME: <span id="uptime">00:00:00</span> </div>

    <div id="content">
        <div class="header">
            <h2 style="margin:0;">Income Ad</h2>
            <p style="margin:5px 0 0; opacity:0.9;">Main Balance: <span id="balance">0.00</span> TK</p>
        </div>

        <!-- Home -->
        <div id="home" class="page active-page">
            <div class="stats-grid">
                <div class="stat-card">Today Ads<br><h3 id="t-ads">0/200</h3></div>
                <div class="stat-card">Total Income<br><h3 id="t-inc">0.00</h3></div>
                <div class="stat-card">Watched<br><h3 id="t-watch">0</h3></div>
                <div class="stat-card">Referrals<br><h3 id="t-ref">0</h3></div>
            </div>
            <div class="stat-card" style="margin-top:20px; text-align:left;">
                <b>Earnings Graph</b>
                <div style="height:80px; border-bottom:1px solid #eee;"></div>
            </div>
        </div>

        <!-- Tasks -->
        <div id="tasks" class="page">
            <h3>Daily Tasks</h3>
            <div class="stat-card" style="display:flex; justify-content:space-between; align-items:center;">
                <div style="text-align:left;">
                    <b>Rewarded Ad</b><br>
                    <small>Earn <span id="ad-price">2</span> TK per ad</small>
                </div>
                <button onclick="startAd()" style="background:var(--blue); color:white; border:none; padding:10px 20px; border-radius:8px; font-weight:bold;">Watch</button>
            </div>
        </div>

        <!-- Profile -->
        <div id="profile" class="page">
            <div class="stat-card">
                <h3>Withdraw</h3>
                <p style="color:red; font-size:12px;">Minimum: <span id="min-w">30</span> TK</p>
                <input type="number" id="w-num" placeholder="Account Number" style="width:100%; padding:15px; border:1px solid #ddd; border-radius:10px; box-sizing:border-box; margin-bottom:10px;">
                <input type="number" id="w-amt" placeholder="Amount" style="width:100%; padding:15px; border:1px solid #ddd; border-radius:10px; box-sizing:border-box;">
                <button class="btn-ad" style="margin-top:15px;" onclick="doWithdraw()">Withdraw Now</button>
            </div>
        </div>
    </div>

    <div class="nav-bar">
        <div class="nav-item active" onclick="showTab('home', this)"><i class="fas fa-home"></i>Home</div>
        <div class="nav-item" onclick="showTab('tasks', this)"><i class="fas fa-play"></i>Tasks</div>
        <div class="nav-item" onclick="showTab('profile', this)"><i class="fas fa-wallet"></i>Wallet</div>
    </div>

<script>
    // Firebase Config (আপনার দেওয়া)
    const firebaseConfig = {
        apiKey: "AIzaSyA5n5gmq45GMwDjh9ZxmtH7LCgRv_Cmiuo",
        databaseURL: "https://income-ad-default-rtdb.firebaseio.com"
    };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    const tg = window.Telegram.WebApp;
    tg.expand();
    const uid = tg.initDataUnsafe.user?.id || "admin_test_123";
    const userRef = db.ref('users/' + uid);

    window.onload = () => {
        // এনিমেশন লোডার
        setTimeout(() => document.getElementById('loader').style.display='none', 3000);
        sync();
        initUptime();
    };

    // ১. মেইনটেন্যান্স ও ডাটা সিঙ্ক
    function sync() {
        db.ref('settings').on('value', snap => {
            const s = snap.val() || {};
            const now = Date.now();
            if(s.status === "closed" && now < s.offUntil) {
                document.getElementById('maintenance').style.display = 'block';
                document.getElementById('m-msg').innerText = s.notice || "Maintenance Mode";
                startMCount(s.offUntil);
            } else {
                document.getElementById('maintenance').style.display = 'none';
            }
            window.reward = parseFloat(s.adPrice || 2);
            document.getElementById('ad-price').innerText = window.reward;
            document.getElementById('min-w').innerText = s.minWithdraw || 30;
        });
    }

    function startMCount(end) {
        setInterval(() => {
            let left = end - Date.now();
            if(left <= 0) location.reload();
            let m = Math.floor(left/60000);
            let s = Math.floor((left%60000)/1000);
            document.getElementById('m-countdown').innerText = `Opening in: ${m}:${s.toString().padStart(2,'0')}`;
        }, 1000);
    }

    // ২. জ্যান্ত টাইমার
    function initUptime() {
        db.ref('runtime').on('value', snap => {
            const r = snap.val() || {};
            if(r.running) {
                setInterval(() => {
                    let diff = Math.floor((Date.now() - r.startTime)/1000);
                    let h = Math.floor(diff/3600);
                    let m = Math.floor((diff%3600)/60);
                    let s = diff%60;
                    document.getElementById('uptime').innerText = 
                        `${h.toString().padStart(2,'0')}:${m.toString().padStart(2,'0')}:${s.toString().padStart(2,'0')}`;
                }, 1000);
            }
        });
    }

    // ৩. অ্যাড ফিক্সড ফাংশন
    function startAd() {
        tg.showConfirm("Loading Ad... Watch till the end to earn reward.", (ok) => {
            if(ok) {
                if (typeof show_10345622 !== 'undefined') {
                    show_10345622().then(() => {
                        reward();
                    }).catch(e => {
                        show_10345622('pop').then(() => reward());
                    });
                } else {
                    tg.showAlert("Ad SDK not ready. Please refresh the app.");
                }
            }
        });
    }

    function reward() {
        userRef.transaction(u => {
            if(u) { u.balance += window.reward; u.todayAds += 1; u.totalAds += 1; }
            return u;
        });
        tg.showAlert(`Successfully Earned ${window.reward} TK!`);
    }

    // ইউজার ব্যালেন্স লোড
    userRef.on('value', snap => {
        const d = snap.val();
        if(d) {
            document.getElementById('balance').innerText = d.balance.toFixed(2);
            document.getElementById('t-inc').innerText = d.balance.toFixed(2);
            document.getElementById('t-ads').innerText = d.todayAds + "/200";
            document.getElementById('t-watch').innerText = d.totalAds || 0;
        } else {
            userRef.set({ balance: 0, todayAds: 0, totalAds: 0, referrals: 0 });
        }
    });

    function showTab(id, el) {
        document.querySelectorAll('.page').forEach(p => p.classList.remove('active-page'));
        document.getElementById(id).classList.add('active-page');
        document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
        el.classList.add('active');
    }

    function doWithdraw() {
        const num = document.getElementById('w-num').value;
        const amt = document.getElementById('w-amt').value;
        if(amt >= 30) {
            db.ref('withdraws').push({ userId: uid, num, amt, status: 'pending' });
            tg.showAlert("Withdraw Request Sent!");
        } else { tg.showAlert("Minimum 30 TK required."); }
    }
</script>
</body>
</html>
