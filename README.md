<!DOCTYPE html>
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>স্মার্ট শপ ক্যাশবুক & রেজিস্টার খাতা</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- PWA / Web App Manifest Data -->
    <link rel="manifest" id="manifestPlaceholder">
    <meta name="theme-color" content="#00796b">
    <meta name="mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="apple-mobile-web-app-title" content="স্মার্ট ক্যাশবুক">
    
    <!-- Firebase SDK Modules (Auth & Database) -->
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-database-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-auth-compat.js"></script>

    <style>
        * { box-sizing: border-box; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body { background-color: #f4f7f6; margin: 0; padding: 20px; padding-bottom: 90px; font-size: 16px; color: #333; }
        .container { max-width: 1100px; margin: 0 auto; background: #ffffff; padding: 30px; border-radius: 16px; box-shadow: 0 4px 15px rgba(0, 0, 0, 0.05); display: none; }
        
        #loginScreen, #setupScreen, #forgotModalContent { 
            max-width: 420px; 
            margin: 60px auto; 
            background: #ffffff; 
            padding: 30px; 
            border-radius: 16px; 
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.08); 
            text-align: center; 
            display: block; 
        }
        #loginScreen h2, #setupScreen h2 { color: #00796b; margin-bottom: 15px; }
        input[type="password"], input[type="text"], input[type="number"], input[type="tel"], input[type="date"], input[type="email"], select {
            width: 100%; padding: 12px 14px; margin-bottom: 12px; border: 1.5px solid #cfd8dc; border-radius: 8px; font-size: 15px; outline: none; background: #ffffff; color: #333;
        }
        input.input-error, select.input-error { border: 2px solid #d32f2f !important; background-color: #ffebee; }
        .header { background: linear-gradient(135deg, #00796b, #004d40); color: white; padding: 20px; border-radius: 12px; text-align: center; margin-bottom: 25px; position: relative; }
        .header h2 { margin: 0; font-size: 24px; }
        .install-pwa-btn { background: #ffb300; color: #000; border: none; padding: 8px 14px; border-radius: 20px; font-weight: bold; cursor: pointer; margin-top: 10px; display: inline-block; font-size: 13px; }
        .nav-tabs { display: flex; gap: 6px; margin-bottom: 20px; overflow-x: auto; border-bottom: 3px solid #00796b; padding-bottom: 8px; }
        .tab-btn { background: #e0f2f1; color: #004d40; border: none; padding: 10px 14px; border-radius: 8px 8px 0 0; cursor: pointer; font-weight: bold; flex: 1; text-align: center; white-space: nowrap; font-size: 13px; }
        .tab-btn.active { background: #00796b; color: white; }
        .bottom-nav { position: fixed; bottom: 0; left: 0; right: 0; background: #ffffff; display: flex; justify-content: space-around; align-items: center; border-top: 1px solid #e0e0e0; box-shadow: 0 -2px 10px rgba(0, 0, 0, 0.05); z-index: 1000; padding: 6px 0; }
        .bottom-nav-item { flex: 1; text-align: center; color: #555; text-decoration: none; padding: 4px 0; border: none; background: none; cursor: pointer; font-size: 9.5px; }
        .bottom-nav-item i { font-size: 16px; display: block; margin-bottom: 3px; }
        .bottom-nav-item.active { color: #00796b; font-weight: bold; }
        .tab-content { display: none; }
        .tab-content.active { display: block; }
        .summary-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 15px; margin-bottom: 25px; }
        .summary-card { padding: 18px; border-radius: 12px; color: white; text-align: center; }
        .card-today { background: #2e7d32; }
        .card-blue { background: #0288d1; }
        .card-purple { background: #6a1b9a; }
        .card-orange { background: #d84315; }
        .card-teal { background: #00695c; }
        .summary-card h4 { margin: 0 0 8px 0; font-size: 15px; }
        .summary-card p { margin: 0; font-size: 22px; font-weight: bold; }
        .card-box { background: #ffffff; padding: 20px; border-radius: 12px; margin-bottom: 20px; border: 1px solid #e0e0e0; box-shadow: 0 2px 8px rgba(0,0,0,0.02); }
        .section-title { margin-top: 0; margin-bottom: 15px; font-size: 18px; color: #00796b; border-bottom: 2px solid #b2dfdb; padding-bottom: 5px; }
        .form-group { display: flex; flex-wrap: wrap; gap: 10px; margin-bottom: 10px; }
        .form-group input, .form-group select { flex: 1; min-width: 180px; }
        .custom-dropdown-container { position: relative; flex: 1; min-width: 180px; margin-bottom: 12px; }
        .custom-dropdown-input { width: 100%; padding: 12px 14px; border: 1.5px solid #cfd8dc; border-radius: 8px; font-size: 15px; outline: none; background: #ffffff; color: #333; cursor: pointer; text-align: left; }
        .custom-dropdown-list { position: absolute; top: 100%; left: 0; right: 0; background: #ffffff; border: 1.5px solid #cfd8dc; border-top: none; border-radius: 0 0 8px 8px; max-height: 200px; overflow-y: auto; z-index: 999; display: none; box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1); }
        .custom-dropdown-item { padding: 10px 14px; cursor: pointer; border-bottom: 1px solid #f0f0f0; font-size: 14px; color: #333; display: flex; justify-content: space-between; align-items: center; }
        .custom-dropdown-item:hover { background: #e0f2f1; color: #004d40; font-weight: bold; }
        .custom-red-tag { width: 14px; height: 18px; background-color: #d32f2f; border-radius: 3px; display: inline-block; margin-left: 8px; cursor: pointer; vertical-align: middle; box-shadow: 0 1px 3px rgba(0,0,0,0.2); }
        .custom-red-tag.inactive { background-color: #e0e0e0; }
        tr.red-marked-row { background-color: #ffebee !important; }
        .due-preview { background: #fff3e0; padding: 10px 15px; border-radius: 8px; border: 1px solid #ffe0b2; color: #e65100; font-weight: bold; margin-bottom: 12px; display: flex; justify-content: space-between; }
        .btn { background: #00796b; color: white; border: none; padding: 12px; border-radius: 8px; cursor: pointer; font-size: 15px; font-weight: bold; width: 100%; }
        .btn:hover { background: #004d40; }
        .btn-green { background: #2e7d32; }
        .btn-orange { background: #f57c00; }
        .btn-action { padding: 5px 10px; font-size: 12px; border-radius: 4px; border: none; cursor: pointer; color: white; }
        .btn-delete { background: #d32f2f; }
        .btn-edit { background: #0288d1; }
        .table-responsive { width: 100%; overflow-x: auto; }
        table { width: 100%; border-collapse: collapse; margin-top: 10px; background: #ffffff; }
        th, td { padding: 10px; text-align: center; border-bottom: 1px solid #e0e0e0; font-size: 14px; color: #333; }
        th { background: #00796b; color: white; }
        #notificationToast { visibility: hidden; min-width: 280px; background-color: #333; color: #fff; text-align: center; border-radius: 8px; padding: 12px; position: fixed; z-index: 4000; left: 50%; bottom: 80px; transform: translateX(-50%); font-weight: bold; }
        #notificationToast.show { visibility: visible; }
        .cust-info-header { background: #e0f2f1; padding: 15px; border-radius: 8px; margin-bottom: 15px; display: flex; justify-content: space-between; flex-wrap: wrap; align-items: center; color: #004d40; }
        .modal { display: none; position: fixed; z-index: 3000; left: 0; top: 0; width: 100%; height: 100%; background-color: rgba(0, 0, 0, 0.5); overflow-y: auto; padding: 20px; }
        .modal-content { background-color: #ffffff; margin: 40px auto; padding: 25px; border-radius: 12px; max-width: 500px; box-shadow: 0 5px 20px rgba(0, 0, 0, 0.2); }
        .role-badge { background: #e0f2f1; color: #00796b; padding: 4px 10px; border-radius: 12px; font-size: 13px; font-weight: bold; display: inline-block; margin-top: 5px; }
    </style>
</head>
<body>

    <div id="notificationToast">মেসেজ</div>

    <!-- প্রথমবার সেটআপ স্ক্রিন -->
    <div id="setupScreen" style="display:none;">
        <h2>⚙ সিকিউরিটি সেটআপ</h2>
        <p style="color: #666; font-size: 13px;">অ্যাডমিন জিমেইল ও পাসওয়ার্ড সেট করুন</p>
        <input type="email" id="setRecoveryEmail" placeholder="অ্যাডমিন জিমেইল (লগইন ও রিসেটের জন্য) *" required>
        <input type="password" id="setAdminPin" placeholder="অ্যাডমিন পাসওয়ার্ড (কমপক্ষে ৬ ডিজিট) *" required>
        
        <p style="color: #666; font-size: 13px; margin-top: 15px;">ম্যানেজারের জন্য ফায়ারবেস অথেন্টিকেশন ইমেইল ও পাসওয়ার্ড সেট করুন</p>
        <input type="email" id="setManagerEmail" placeholder="ম্যানেজার জিমেইল (লগইন ও রিসেটের জন্য) *" required>
        <input type="password" id="setManagerPin" placeholder="ম্যানেজার পাসওয়ার্ড (কমপক্ষে ৬ ডিজিট) *" required>

        <button class="btn btn-green" onclick="saveInitialSetup()">সেভ করুন</button>
    </div>

    <!-- লগইন স্ক্রিন -->
    <div id="loginScreen" style="display:none;">
        <h2>🔐 লগইন করুন</h2>
        <select id="loginRoleSelect" style="margin-bottom: 15px; text-align-last: center;" onchange="handleRoleChange()">
            <option value="admin">অ্যাডমিন (Admin)</option>
            <option value="manager">ম্যানেজার (Manager)</option>
        </select>
        <input type="email" id="loginEmailInput" placeholder="ইউজার জিমেইল লিখুন" style="display:none;">
        <input type="password" id="pinInput" placeholder="পাসওয়ার্ড লিখুন">
        <button class="btn btn-green" onclick="verifyPin()">লগইন</button>
        <p style="margin-top: 15px;" id="forgotPassContainer">
            <a href="#" onclick="showForgotModal()" style="color: #00796b; font-size: 14px; text-decoration: none; font-weight: bold;">
                👉 পাসওয়ার্ড ভুলে গেছেন? ইমেইলে লিঙ্ক পাঠান
            </a>
        </p>
        <button id="pwaInstallBtnLogin" class="install-pwa-btn" style="display:none;" onclick="triggerPwaInstall()">
            📲 মোবাইলে / পিসিতে অ্যাপ ইনস্টল করুন
        </button>
    </div>

    <!-- ইমেইলে রিসেট লিঙ্ক পাঠানোর মোডাল -->
    <div id="forgotModal" class="modal">
        <div class="modal-content">
            <h3 class="section-title">📧 ইমেইলে পাসওয়ার্ড রিসেট লিঙ্ক পাঠান</h3>
            <p style="font-size: 13px; color: #666;">আপনার (অ্যাডমিন বা ম্যানেজার) রেজিস্টার্ড জিমেইল অ্যাকাউন্টটি দিন। আমরা ইমেইলে পাসওয়ার্ড পরিবর্তনের সরাসরি লিঙ্ক পাঠাবো।</p>
            <input type="email" id="recoveryEmailInput" placeholder="আপনার রেজিস্টার্ড জিমেইল দিন">
            <button class="btn btn-green" onclick="sendEmailResetLink()">রিসেট লিঙ্ক পাঠান</button>
            <button class="btn" style="background:#777; margin-top:10px;" onclick="closeForgotModal()">বন্ধ করুন</button>
        </div>
    </div>

    <!-- নতুন পাসওয়ার্ড রিসেট মোডাল -->
    <div id="newPinResetModal" class="modal" style="display: none;">
        <div class="modal-content">
            <h3 class="section-title">🔑 নতুন পাসওয়ার্ড সেট করুন</h3>
            <p style="font-size: 13px; color: #666; margin-bottom: 15px;">
                ইমেইল ভেরিফিকেশন সফল হয়েছে। এখন আপনার নতুন পাসওয়ার্ড (কমপক্ষে ৬ ডিজিট) সেট করুন।
            </p>
            <input type="password" id="newAdminPinInput" placeholder="নতুন পাসওয়ার্ড (কমপক্ষে ৬ ডিজিট) *" style="width: 100%; padding: 10px; margin-bottom: 15px; border: 1px solid #ddd; border-radius: 5px; background: #fff; color: #333;">
            <button class="btn btn-green" onclick="confirmNewPinReset()" style="width: 100%; padding: 10px; background-color: #28a745; color: white; border: none; border-radius: 5px; cursor: pointer;">
                নতুন পাসওয়ার্ড সেভ করুন
            </button>
        </div>
    </div>

    <!-- মূল অ্যাপ -->
    <div class="container" id="appMainContainer">
        <div class="header">
            <h2>স্মার্ট শপ ক্যাশবুক & রেজিস্টার খাতা</h2>
            <div id="currentRoleBadge" class="role-badge">লগইন: অ্যাডমিন</div>
            <br>
            <button id="pwaInstallBtnHeader" class="install-pwa-btn" style="display:none;" onclick="triggerPwaInstall()">
                📲 মোবাইলে / পিসিতে অ্যাপ ইনস্টল করুন
            </button>
        </div>

        <!-- টপ নেভিগেশন ট্যাব -->
        <div class="nav-tabs">
            <button class="tab-btn active btn-dashboardTab" onclick="switchTab('dashboardTab')"><i class="fa-solid fa-chart-pie"></i> ড্যাশবোর্ড</button>
            <button class="tab-btn btn-revenueTab" onclick="switchTab('revenueTab')"><i class="fa-solid fa-sack-dollar"></i> রেভিনিউ</button>
            <button class="tab-btn btn-adminReceiveTab" id="navAdminReceiveTab" onclick="switchTab('adminReceiveTab')"><i class="fa-solid fa-hand-holding-dollar"></i> টাকা জমা</button>
            <button class="tab-btn btn-transactionTab" onclick="switchTab('transactionTab')"><i class="fa-solid fa-cart-plus"></i> লেনদেন</button>
            <button class="tab-btn btn-stockTab" onclick="switchTab('stockTab')"><i class="fa-solid fa-boxes-stacked"></i> স্টক</button>
            <button class="tab-btn btn-registerBookTab" onclick="switchTab('registerBookTab')"><i class="fa-solid fa-clock-rotate-left"></i> হিস্ট্রি</button>
            <button class="tab-btn btn-customersTab" onclick="switchTab('customersTab')"><i class="fa-solid fa-receipt"></i> জমা হিস্ট্রি</button>
            <button class="tab-btn btn-dueTab" onclick="switchTab('dueTab')"><i class="fa-solid fa-book-bookmark"></i> বাকি হিস্ট্রি</button>
            <button class="tab-btn btn-addCustomerTab" onclick="switchTab('addCustomerTab')"><i class="fa-solid fa-user-plus"></i> নতুন</button>
            <button class="tab-btn btn-adminTab" id="navSettingsTab" onclick="switchTab('adminTab')"><i class="fa-solid fa-gear"></i> সেটিংস</button>
        </div>

        <!-- ড্যাশবোর্ড -->
        <div id="dashboardTab" class="tab-content active">
            <div class="summary-grid">
                <div class="summary-card card-today">
                    <h4>আজকের ক্যাশ আদায়/জমা</h4>
                    <p id="dashTodayPaid">0 Tk</p>
                </div>
                <div class="summary-card card-purple">
                    <h4>আজকের মোট বিক্রি</h4>
                    <p id="dashTodaySales">0 Tk</p>
                </div>
                <div class="summary-card card-teal">
                    <h4>মোট পণ্যের স্টক</h4>
                    <p id="dashTotalStock">0 টি</p>
                </div>
                <div class="summary-card card-orange">
                    <h4>১ মাসের বেশি অলস বাকি</h4>
                    <p id="dashOverdueCount">0 জন</p>
                </div>
            </div>

            <!-- অ্যাডমিন কিস্তি পারমিশন নোটিফিকেশন প্যানেল -->
            <div class="card-box" id="adminApprovalPanel" style="display:none; background: #fffde7; border: 1px solid #ffb300;">
                <h3 class="section-title" style="color: #f57c00;">🔔 কিস্তি মেয়াদ উত্তীর্ণ ও পারমিশন রিকোয়েস্ট</h3>
                <div id="approvalListContent"></div>
            </div>

            <div class="card-box" style="margin-top: 20px;">
                <h3 class="section-title" style="color: #d84315;">⚠ ১ মাসের বেশি সময় লেনদেন না করা বকেয়া কাস্টমার</h3>
                <div class="table-responsive">
                    <table>
                        <thead>
                            <tr>
                                <th>নাম</th>
                                <th>মোবাইল</th>
                                <th>ঠিকানা</th>
                                <th>সর্বশেষ লেনদেনের তারিখ</th>
                                <th>মোট বাকি</th>
                            </tr>
                        </thead>
                        <tbody id="dashOverdueTableBody">
                            <tr><td colspan="5" style="color:#777;">তথ্য লোড হচ্ছে...</td></tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>

        <!-- রেভিনিউ & রিপোর্ট সেকশন -->
        <div id="revenueTab" class="tab-content">
            <div class="card-box">
                <h3 class="section-title">📈 বাৎসরিক, মাসিক ও সাপ্তাহিক রেভিনিউ রিপোর্ট</h3>
                <div class="form-group" style="margin-bottom: 15px;">
                    <label style="font-weight:bold; width: 100%; display:block; margin-bottom: 5px; color:#333;">সময়সীমা সিলেক্ট করুন:</label>
                    <select id="revenueFilter" onchange="calculateRevenue()">
                        <option value="today">আজকের হিসাব (Today)</option>
                        <option value="week">চলতি সপ্তাহ (This Week)</option>
                        <option value="month" selected>চলতি মাস (This Month)</option>
                        <option value="year">চলতি বছর (This Year)</option>
                        <option value="custom">ক্যালেন্ডার দিয়ে নির্দিষ্ট তারিখ (Custom Dates)</option>
                        <option value="all">সর্বমোট (All Time)</option>
                    </select>
                </div>

                <!-- ক্যালেন্ডার ডেট ফিল্টার অপশন -->
                <div id="customDateFilterContainer" class="form-group" style="display: none; background: #e0f2f1; padding: 12px; border-radius: 8px; margin-bottom: 20px;">
                    <div style="flex: 1;">
                        <label style="font-size: 13px; font-weight: bold; color: #004d40; display: block; margin-bottom: 4px;">শুরু তারিখ (From):</label>
                        <input type="date" id="revenueStartDate" onchange="calculateRevenue()">
                    </div>
                    <div style="flex: 1;">
                        <label style="font-size: 13px; font-weight: bold; color: #004d40; display: block; margin-bottom: 4px;">শেষ তারিখ (To):</label>
                        <input type="date" id="revenueEndDate" onchange="calculateRevenue()">
                    </div>
                </div>

                <div class="summary-grid">
                    <div class="summary-card card-blue">
                        <h4>টোটাল বিক্রি (Revenue)</h4>
                        <p id="revTotalSales">0 Tk</p>
                    </div>
                    <div class="summary-card card-today">
                        <h4>টোটাল ক্যাশ আদায়/জমা</h4>
                        <p id="revTotalPaid">0 Tk</p>
                    </div>
                    <div class="summary-card card-orange">
                        <h4>নতুন বাকি পড়েছে</h4>
                        <p id="revTotalDue">0 Tk</p>
                    </div>
                </div>
            </div>
        </div>

        <!-- শুধুমাত্র অ্যাডমিনের জন্য সরাসরি টাকা জমা নেওয়ার ট্যাব -->
        <div id="adminReceiveTab" class="tab-content">
            <div class="card-box" style="border: 2px dashed #00796b;">
                <h3 class="section-title">💵 অ্যাডমিন ডিরেক্ট টাকা জমা (পণ্যের নাম বা দাম ছাড়াই)</h3>
                <p style="font-size: 13px; color: #666; margin-bottom: 15px;">এখানে পণ্যের নাম বা দাম না বসিয়ে সরাসরি কাস্টমারের অ্যাকাউন্ট বা বকেয়া বিপরীতে টাকা জমা নেওয়া যাবে। মেমো নম্বর দিয়েও সার্চ বা সিলেক্ট করা যাবে।</p>
                
                <div class="form-group">
                    <div class="custom-dropdown-container">
                        <input type="text" id="adminRecCustSearchInput" class="custom-dropdown-input" placeholder="🔍 কাস্টমার সিলেক্ট বা সার্চ করুন (নাম, মোবাইল, ঠিকানা বা মেমো) *" onfocus="openAdminRecDropdown()" oninput="filterAdminRecDropdownItems()">
                        <input type="hidden" id="adminRecCustSelect">
                        <div id="adminRecDropdownList" class="custom-dropdown-list"></div>
                    </div>
                </div>

                <div class="form-group">
                    <input type="date" id="adminRecDate">
                    <input type="number" id="adminRecAmount" inputmode="numeric" placeholder="জমা নেওয়ার পরিমাণ (Tk) *" min="0" oninput="preventNegative(this)">
                </div>
                <div class="form-group">
                    <input type="text" id="adminRecMemo" placeholder="মেমো নম্বর বা বিবরণ (ঐচ্ছিক)">
                </div>

                <button class="btn btn-green" onclick="saveAdminDirectPayment()" style="margin-top: 10px;">টাকা জমা সেভ করুন</button>
            </div>
        </div>

        <!-- লেনদেন এন্ট্রি -->
        <div id="transactionTab" class="tab-content">
            <div class="card-box">
                <h3 class="section-title">পণ্যের বিক্রি ও হিসাব সেভ করুন</h3>
                <div class="form-group">
                    <div class="custom-dropdown-container">
                        <input type="text" id="txCustSearchInput" class="custom-dropdown-input" placeholder="🔍 কাস্টমার সিলেক্ট বা সার্চ করুন (নাম, মোবাইল বা ঠিকানা) *" onfocus="openTxDropdown()" oninput="filterTxDropdownItems()">
                        <input type="hidden" id="txCustSelect">
                        <div id="txDropdownList" class="custom-dropdown-list"></div>
                    </div>
                    <div class="custom-dropdown-container">
                        <input type="text" id="txMemo" class="custom-dropdown-input" placeholder="🔍 মেমো নং (সিলেক্ট বা নতুন লিখুন)" onfocus="openMemoDropdown()" oninput="filterMemoDropdownItems()">
                        <div id="memoDropdownList" class="custom-dropdown-list"></div>
                    </div>
                </div>
                <div class="form-group">
                    <input type="text" id="txItem" list="productList" placeholder="পণ্যের নাম (বাধ্যতামূলক) *" oninput="checkTxStockAvailability()">
                    <input type="number" id="txQuantity" inputmode="numeric" placeholder="পিস সংখ্যা (Quantity)" min="1" value="1" oninput="preventNegative(this)">
                    <input type="date" id="txDate">
                    <datalist id="productList">
                        <!-- ডাইনামিকালি স্টক থেকে প্রোডাক্ট এখানে যুক্ত হবে -->
                    </datalist>
                </div>
                <div id="txStockWarning" style="font-size: 13px; color: #d84315; font-weight: bold; margin-bottom: 10px;"></div>

                <!-- কিস্তি ও রেট ইনপুট অপশন -->
                <div style="background: #e0f2f1; padding: 15px; border-radius: 8px; margin-bottom: 12px;">
                    <label style="font-weight: bold; color: #00796b; display: block; margin-bottom: 8px;">
                        <input type="checkbox" id="txInstallmentToggle" onchange="toggleInstallmentOptions('tx')"> কিস্তি সুবিধা ও রেট অপশন চালু করুন
                    </label>
                    <div id="txInstallmentOptionsContainer" style="display: none; margin-top: 10px;">
                        <label style="font-size: 13px; font-weight: bold; display: block; margin-bottom: 5px; color:#004d40;">কিস্তির মেয়াদ নির্বাচন করুন:</label>
                        <select id="txInstallmentType" onchange="calculateDuePreview()">
                            <option value="3_6">৩ মাস ও ৬ মাস (প্রথমাংশ ৩ মাস, দ্বিতীয়াংশ পরবর্তী ৩ মাস)</option>
                            <option value="6_9">৬ মাস ও ৯ মাস (প্রথমাংশ ৬ মাস, দ্বিতীয়াংশ পরবর্তী ৩ মাস)</option>
                        </select>
                        <div class="form-group" style="margin-top: 10px;">
                            <input type="number" id="txFirstRate" inputmode="numeric" placeholder="প্রথম ধাপের রেট / মোট দাম (Tk) *" min="0" oninput="preventNegative(this); calculateDuePreview();">
                            <input type="number" id="txSecondRate" inputmode="numeric" placeholder="পরবর্তী ধাপের রেট (Tk) *" min="0" oninput="preventNegative(this); calculateDuePreview();">
                        </div>
                        <div style="font-size: 12px; color: #004d40; margin-top: 5px;" id="txInstallmentBreakdownText"></div>
                    </div>
                </div>

                <div class="form-group" id="txNormalPriceContainer">
                    <input type="number" id="txTotalPrice" inputmode="numeric" pattern="[0-9]*" placeholder="পণ্যের মোট দাম (Tk) *" min="0" oninput="preventNegative(this); calculateDuePreview();">
                    <input type="number" id="txPaidAmount" inputmode="numeric" pattern="[0-9]*" placeholder="জমা দিয়েছে (Tk, খালি রাখা যাবে)" min="0" oninput="preventNegative(this); calculateDuePreview();">
                </div>
                <div class="form-group" id="txInstallmentPaidContainer" style="display:none;">
                    <input type="number" id="txPaidAmountInst" inputmode="numeric" pattern="[0-9]*" placeholder="জমা দিয়েছে (Tk, খালি রাখা যাবে)" min="0" oninput="preventNegative(this); calculateDuePreview();">
                </div>

                <div class="due-preview">
                    <span>হিসাব অনুযায়ী নতুন বাকি:</span>
                    <span id="calculatedDueText">0 Tk</span>
                </div>
                <button class="btn btn-green" onclick="addTransactionDirect()">বিক্রি/লেনদেন সেভ করুন</button>
            </div>

            <div class="card-box" style="margin-top: 20px;">
                <h3 class="section-title">🔍 মেমো নম্বর দিয়ে লেনদেন খুঁজুন</h3>
                <div class="form-group">
                    <input type="text" id="globalMemoSearchInput" placeholder="এখানে মেমো নম্বর লিখুন (যেমন: M-101)..." oninput="filterGlobalTransactionsByMemo()">
                </div>
                <div class="table-responsive">
                    <table>
                        <thead>
                            <tr>
                                <th>তারিখ</th>
                                <th>মেমো নং</th>
                                <th>কাস্টমারের নাম</th>
                                <th>মোবাইল নম্বর</th>
                                <th>বিবরণ / পণ্য</th>
                                <th>পিস</th>
                                <th>মোট দাম</th>
                                <th>জমা</th>
                            </tr>
                        </thead>
                        <tbody id="globalMemoSearchResultBody">
                            <tr><td colspan="8" style="color:#777;">মেমো নম্বর লিখে সার্চ করুন...</td></tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>

        <!-- প্রোডাক্ট স্টক ম্যানেজমেন্ট ট্যাব -->
        <div id="stockTab" class="tab-content">
            <div class="card-box">
                <h3 class="section-title">📦 পণ্যের স্টক তালিকা ও নতুন পণ্য যুক্ত করুন</h3>
                <div class="form-group">
                    <input type="text" id="stockProductName" placeholder="পণ্যের নাম (যেমন: রাইস কুকার) *" list="productList">
                    <input type="text" id="stockProductModel" placeholder="মডেল নম্বর (Model No) *">
                    <input type="number" id="stockProductQty" placeholder="পরিমাণ (Quantity) *" min="1" inputmode="numeric" oninput="preventNegative(this)">
                </div>
                <button class="btn btn-green" onclick="addNewStockProduct()">স্টকে নতুন পণ্য যোগ / পরিমাণ বাড়ান</button>
            </div>

            <div class="card-box" style="margin-top: 20px;">
                <h3 class="section-title">📋 বর্তমান স্টক রেজিস্টার</h3>
                <div class="form-group">
                    <input type="text" id="stockSearchInput" placeholder="🔍 মডেল নম্বর বা নাম দিয়ে স্টক খুঁজুন..." oninput="filterStockTable()">
                </div>
                <div class="table-responsive">
                    <table>
                        <thead>
                            <tr>
                                <th>পণ্যের নাম</th>
                                <th>মডেল নম্বর</th>
                                <th>মজুদ পরিমাণ</th>
                                <th>স্ট্যাটাস</th>
                                <th>অ্যাকশন</th>
                            </tr>
                        </thead>
                        <tbody id="stockTableBody">
                            <tr><td colspan="5" style="color:#777;">স্টক লোড হচ্ছে...</td></tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>

        <!-- রেজিস্টার খাতা & হিস্ট্রি -->
        <div id="registerBookTab" class="tab-content">
            <div class="card-box">
                <h3 class="section-title">📜 লেনদেনের ইতিহাস & কাস্টমার রেজিস্টার বই</h3>
                <div class="form-group">
                    <div class="custom-dropdown-container">
                        <input type="text" id="regCustSearchInput" class="custom-dropdown-input" placeholder="🔍 ইতিহাস দেখতে কাস্টমার সার্চ বা '0' লিখে পরিশোধিত কাস্টমার খুঁজুন..." onfocus="openRegDropdown()" oninput="filterRegDropdownItems()">
                        <input type="hidden" id="regCustSelect" onchange="loadCustomerLedger()">
                        <div id="regDropdownList" class="custom-dropdown-list"></div>
                    </div>
                </div>
                <div class="form-group" style="margin-top: 10px;">
                    <div class="custom-dropdown-container">
                        <input type="text" id="regMemoSearchInput" class="custom-dropdown-input" placeholder="🔍 নির্দিষ্ট মেমো নম্বর দিয়ে খুঁজুন..." onfocus="openRegMemoDropdown()" oninput="filterRegMemoDropdownItems()">
                        <div id="regMemoDropdownList" class="custom-dropdown-list"></div>
                    </div>
                </div>

                <div id="ledgerContent" style="display: none;">
                    <div class="cust-info-header">
                        <div>
                            <b>কাস্টমারের নাম:</b> <span id="ledgerCustName">-</span> | 
                            <b>মোবাইল:</b> <span id="ledgerCustPhone">-</span> | 
                            <b>ঠিকানা:</b> <span id="ledgerCustAddr">-</span>
                        </div>
                        <div>
                            <button class="btn-action btn-delete admin-only" onclick="deleteCurrentCustomer()"><i class="fa-solid fa-trash"></i> কাস্টমার ডিলিট</button>
                        </div>
                    </div>

                    <div class="summary-grid">
                        <div class="summary-card card-blue">
                            <h4>মোট পণ্য ক্রয়</h4>
                            <p id="ledgerTotalCost">0 Tk</p>
                        </div>
                        <div class="summary-card card-today">
                            <h4>মোট জমা</h4>
                            <p id="ledgerTotalPaid">0 Tk</p>
                        </div>
                        <div class="summary-card card-orange">
                            <h4>বর্তমান মোট বাকি</h4>
                            <p id="ledgerCurrentBalance">0 Tk</p>
                        </div>
                    </div>

                    <h4 style="color:#00796b; margin-top:20px;">লেনদেনের সময় ও তারিখ ভিত্তিক রেজিস্টার ইতিহাস (পেপার সহ):</h4>
                    <div class="table-responsive">
                        <table>
                            <thead>
                                <tr>
                                    <th>তারিখ</th>
                                    <th>মেমো নং</th>
                                    <th>বিবরণ / পণ্য</th>
                                    <th>পিস</th>
                                    <th>মোট দাম</th>
                                    <th>জমা দিয়েছে</th>
                                    <th>চলতি বাকি</th>
                                    <th>কিস্তি স্ট্যাটাস & রেট</th>
                                    <th>সর্বশেষ এডিট</th>
                                    <th class="admin-action-col">অ্যাকশন</th>
                                </tr>
                            </thead>
                            <tbody id="ledgerTableBody"></tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>

        <!-- জমা হিস্ট্রি সেকশন -->
        <div id="customersTab" class="tab-content">
            <div class="card-box">
                <h3 class="section-title">টাকা জমা নেওয়ার হিস্ট্রি</h3>
                <div class="form-group" style="margin-bottom: 10px;">
                    <input type="text" id="globalCustomerSearch" placeholder="🔍 নাম বা মোবাইল নম্বর দিয়ে খুঁজুন..." oninput="filterGlobalCustomers()">
                </div>
                <div class="form-group" style="margin-bottom: 15px;">
                    <input type="text" id="globalAddressSearch" placeholder="🏠 শুধুমাত্র ঠিকানা দিয়ে খুঁজুন (যেমন: ঢাকা, মিরপুর)..." oninput="filterGlobalCustomers()">
                </div>
                <div class="table-responsive">
                    <table>
                        <thead>
                            <tr>
                                <th>নাম</th>
                                <th>মোবাইল</th>
                                <th>ঠিকানা</th>
                                <th>মেমো নং</th>
                                <th>তারিখ</th>
                                <th>টাকা জমা / পরিশোধ</th>
                                <th>বর্তমান বাকি</th>
                            </tr>
                        </thead>
                        <tbody id="customerTableBody"></tbody>
                    </table>
                </div>
            </div>
        </div>

        <!-- বাকি হিস্ট্রি সেকশন -->
        <div id="dueTab" class="tab-content">
            <div class="card-box">
                <h3 class="section-title">বাকি হিস্ট্রি (যাদের কাছে টাকা পাওয়া যাবে)</h3>
                <div class="form-group" style="margin-bottom: 10px;">
                    <input type="text" id="globalDueSearch" placeholder="🔍 নাম বা মোবাইল নম্বর দিয়ে বাকি কাস্টমার খুঁজুন..." oninput="filterGlobalDueCustomers()">
                </div>
                <div class="form-group" style="margin-bottom: 15px;">
                    <input type="text" id="globalDueAddressSearch" placeholder="🏠 শুধুমাত্র ঠিকানা দিয়ে বাকি কাস্টমার খুঁজুন..." oninput="filterGlobalDueCustomers()">
                </div>
                <div class="table-responsive">
                    <table>
                        <thead>
                            <tr>
                                <th>নাম</th>
                                <th>মোবাইল</th>
                                <th>ঠিকানা</th>
                                <th>সর্বশেষ মেমো নং</th>
                                <th>মোট বাকি টাকা</th>
                                <th>লাল মার্ক</th>
                                <th>অ্যাকশন</th>
                            </tr>
                        </thead>
                        <tbody id="dueTableBody"></tbody>
                    </table>
                </div>
            </div>
        </div>

        <!-- নতুন কাস্টমার সেভ করার সেকশন -->
        <div id="addCustomerTab" class="tab-content">
            <div class="card-box">
                <h3 class="section-title">👤 নতুন কাস্টমার প্রোফাইল</h3>
                <div class="form-group">
                    <input type="text" id="newCustName" placeholder="কাস্টমারের নাম *">
                    <input type="tel" id="newCustPhone" placeholder="মোবাইল নম্বর *">
                    <div class="custom-dropdown-container" style="margin-bottom:0;">
                        <input type="text" id="newCustAddress" class="custom-dropdown-input" placeholder="ঠিকানা *" onfocus="openAddressDropdown()" oninput="filterAddressDropdown()">
                        <div id="addressDropdownList" class="custom-dropdown-list"></div>
                    </div>
                </div>

                <h3 class="section-title" style="margin-top: 20px;">🛒 প্রথম কেনাকাটা / ক্যাশ জমা</h3>
                <div class="form-group">
                    <input type="text" id="newCustMemo" placeholder="মেমো নং">
                    <input type="text" id="newCustItem" list="productList" placeholder="পণ্যের নাম (বাধ্যতামূলক) *" oninput="checkNewCustStockAvailability()">
                    <input type="number" id="newCustQuantity" inputmode="numeric" placeholder="পিস সংখ্যা (Quantity)" min="1" value="1" oninput="preventNegative(this)">
                    <input type="date" id="newCustDate">
                </div>
                <div id="newCustStockWarning" style="font-size: 13px; color: #d84315; font-weight: bold; margin-bottom: 10px;"></div>

                <!-- নতুন কাস্টমার ফর্মে কিস্তি ও দ্বৈত রেট অপশন -->
                <div style="background: #e0f2f1; padding: 15px; border-radius: 8px; margin-bottom: 12px;">
                    <label style="font-weight: bold; color: #00796b; display: block; margin-bottom: 8px;">
                        <input type="checkbox" id="newCustInstallmentToggle" onchange="toggleInstallmentOptions('newCust')"> কিস্তি সুবিধা ও রেট অপশন চালু করুন
                    </label>
                    <div id="newCustInstallmentOptionsContainer" style="display: none; margin-top: 10px;">
                        <label style="font-size: 13px; font-weight: bold; display: block; margin-bottom: 5px; color:#004d40;">কিস্তির মেয়াদ নির্বাচন করুন:</label>
                        <select id="newCustInstallmentType" onchange="calculateNewCustDuePreview()">
                            <option value="3_6">৩ মাস ও ৬ মাস (প্রথমাংশ ৩ মাস, দ্বিতীয়াংশ পরবর্তী ৩ মাস)</option>
                            <option value="6_9">৬ মাস ও ৯ মাস (প্রথমাংশ ৬ মাস, দ্বিতীয়াংশ পরবর্তী ৩ মাস)</option>
                        </select>
                        <div class="form-group" style="margin-top: 10px;">
                            <input type="number" id="newCustFirstRate" inputmode="numeric" placeholder="প্রথম ধাপের রেট / মোট দাম (Tk) *" min="0" oninput="preventNegative(this); calculateNewCustDuePreview();">
                            <input type="number" id="newCustSecondRate" inputmode="numeric" placeholder="পরবর্তী ধাপের রেট (Tk) *" min="0" oninput="preventNegative(this); calculateNewCustDuePreview();">
                        </div>
                        <div style="font-size: 12px; color: #004d40; margin-top: 5px;" id="newCustInstallmentBreakdownText"></div>
                    </div>
                </div>

                <div class="form-group" id="newCustNormalPriceContainer">
                    <input type="number" id="newCustTotalPrice" inputmode="numeric" pattern="[0-9]*" placeholder="পণ্যের মোট দাম (Tk) *" min="0" oninput="preventNegative(this); calculateNewCustDuePreview();">
                    <input type="number" id="newCustPaidAmount" inputmode="numeric" pattern="[0-9]*" placeholder="জমা দিয়েছে (Tk, খালি রাখা যাবে)" min="0" oninput="preventNegative(this); calculateNewCustDuePreview();">
                </div>
                <div class="form-group" id="newCustInstallmentPaidContainer" style="display:none;">
                    <input type="number" id="newCustPaidAmountInst" inputmode="numeric" pattern="[0-9]*" placeholder="জমা দিয়েছে (Tk, খালি রাখা যাবে)" min="0" oninput="preventNegative(this); calculateNewCustDuePreview();">
                </div>

                <div class="due-preview">
                    <span>হিসাব অনুযায়ী বাকি:</span>
                    <span id="newCustCalculatedDueText">0 Tk</span>
                </div>
                <button class="btn btn-green" onclick="addNewCustomer()">কাস্টমার ও কেনাকাটার তথ্য সেভ করুন</button>
            </div>
        </div>

        <!-- সেটিংস ও প্রোফাইল ম্যানেজমেন্ট -->
        <div id="adminTab" class="tab-content">
            <div class="card-box">
                <h3 class="section-title">সিকিউরিটি ও প্রাইভেসী সেটিংস</h3>
                <p style="font-size: 13px; color: #666;">পাসওয়ার্ড বা ম্যানেজার জিমেইল/পাসওয়ার্ড পরিবর্তন করতে বর্তমান অ্যাডমিন পাসওয়ার্ড বাধ্যতামূলক।</p>
                <input type="password" id="currentAdminPinVerify" placeholder="বর্তমান অ্যাডমিন পাসওয়ার্ড দিন *" style="border-color: #d84315;">
                <input type="password" id="resetPinInput" placeholder="নতুন অ্যাডমিন পাসওয়ার্ড">
                <input type="email" id="resetManagerEmailInput" placeholder="নতুন ম্যানেজার জিমেইল">
                <input type="password" id="resetManagerPinInput" placeholder="নতুন ম্যানেজার পাসওয়ার্ড">
                <button class="btn btn-orange" onclick="resetPinAndEmail()">পিন ও সেটিংস আপডেট করুন</button>

                <h3 class="section-title" style="margin-top: 25px;">📋 লগইন হিস্ট্রি (Login History & IP Tracking)</h3>
                <div class="table-responsive">
                    <table>
                        <thead>
                            <tr>
                                <th>রোল / ব্যবহারকারী</th>
                                <th>ডিভাইস / ব্রাউজার</th>
                                <th>আইপি অ্যাড্রেস</th>
                                <th>লগইনের তারিখ ও সময়</th>
                            </tr>
                        </thead>
                        <tbody id="loginHistoryTableBody">
                            <tr><td colspan="4" style="color:#777;">কোনো লগইন রেকর্ড নেই।</td></tr>
                        </tbody>
                    </table>
                </div>

                <hr style="margin: 20px 0; border:0; border-top:1px solid #ddd;">
                <button class="btn" style="background:#424242;" onclick="logout()">লগআউট</button>
            </div>
        </div>

        <!-- বটম নেভিগেশন বার -->
        <div class="bottom-nav">
            <button class="bottom-nav-item active btn-dashboardTab" onclick="switchTab('dashboardTab')">
                <i class="fa-solid fa-chart-pie"></i>
                <span>ড্যাশবোর্ড</span>
            </button>
            <button class="bottom-nav-item btn-revenueTab" onclick="switchTab('revenueTab')">
                <i class="fa-solid fa-sack-dollar"></i>
                <span>রেভিনিউ</span>
            </button>
            <button class="bottom-nav-item btn-adminReceiveTab" id="bottomAdminReceiveTab" onclick="switchTab('adminReceiveTab')">
                <i class="fa-solid fa-hand-holding-dollar"></i>
                <span>জমা</span>
            </button>
            <button class="bottom-nav-item btn-transactionTab" onclick="switchTab('transactionTab')">
                <i class="fa-solid fa-cart-plus"></i>
                <span>লেনদেন</span>
            </button>
            <button class="bottom-nav-item btn-stockTab" onclick="switchTab('stockTab')">
                <i class="fa-solid fa-boxes-stacked"></i>
                <span>স্টক</span>
            </button>
            <button class="bottom-nav-item btn-registerBookTab" onclick="switchTab('registerBookTab')">
                <i class="fa-solid fa-clock-rotate-left"></i>
                <span>হিস্ট্রি</span>
            </button>
            <button class="bottom-nav-item btn-customersTab" onclick="switchTab('customersTab')">
                <i class="fa-solid fa-receipt"></i>
                <span>জমা</span>
            </button>
            <button class="bottom-nav-item btn-dueTab" onclick="switchTab('dueTab')">
                <i class="fa-solid fa-book-bookmark"></i>
                <span>বাকি</span>
            </button>
            <button class="bottom-nav-item btn-addCustomerTab" onclick="switchTab('addCustomerTab')">
                <i class="fa-solid fa-user-plus"></i>
                <span>নতুন</span>
            </button>
            <button class="bottom-nav-item btn-adminTab" id="bottomSettingsTab" onclick="switchTab('adminTab')">
                <i class="fa-solid fa-gear"></i>
                <span>সেটিংস</span>
            </button>
        </div>
    </div>

    <!-- লেনদেন এডিট করার মোডাল উইন্ডো -->
    <div id="editTxModal" class="modal">
        <div class="modal-content">
            <h3 class="section-title">✏ লেনদেন এডিট করুন</h3>
            <input type="hidden" id="editTxId">
            <div class="form-group">
                <input type="text" id="editTxMemo" placeholder="মেমো নং">
                <input type="text" id="editTxItem" list="productList" placeholder="পণ্যের নাম">
                <input type="number" id="editTxQuantity" inputmode="numeric" placeholder="পিস সংখ্যা" min="1" value="1" oninput="preventNegative(this)">
            </div>
            <div class="form-group">
                <input type="date" id="editTxDate">
            </div>
            <div class="form-group">
                <input type="number" id="editTxTotalPrice" inputmode="numeric" pattern="[0-9]*" placeholder="মোট দাম (Tk)" min="0" oninput="preventNegative(this)">
                <input type="number" id="editTxPaidAmount" inputmode="numeric" pattern="[0-9]*" placeholder="জমা দিয়েছে (Tk)" min="0" oninput="preventNegative(this)">
            </div>
            <div style="display: flex; gap: 10px; margin-top: 15px;">
                <button class="btn btn-green" onclick="saveEditedTransaction()">আপডেট করুন</button>
                <button class="btn" style="background:#777;" onclick="closeEditModal()">বাতিল</button>
            </div>
        </div>
    </div>

    <!-- স্টক এডিট করার মোডাল উইন্ডো -->
    <div id="editStockModal" class="modal">
        <div class="modal-content">
            <h3 class="section-title">✏ স্টক পণ্য এডিট / পরিমাণ বাড়ান</h3>
            <input type="hidden" id="editStockId">
            <div class="form-group">
                <input type="text" id="editStockName" placeholder="পণ্যের নাম *">
                <input type="text" id="editStockModel" placeholder="মডেল নম্বর *">
            </div>
            <div class="form-group">
                <input type="number" id="editStockQty" placeholder="পরিমাণ *" min="0" inputmode="numeric" oninput="preventNegative(this)">
            </div>
            <div style="display: flex; gap: 10px; margin-top: 15px;">
                <button class="btn btn-green" onclick="saveEditedStock()">আপডেট করুন</button>
                <button class="btn" style="background:#777;" onclick="closeEditStockModal()">বাতিল</button>
            </div>
        </div>
    </div>

    <script>
        function getLocalDateString() {
            const d = new Date();
            const year = d.getFullYear();
            const month = String(d.getMonth() + 1).padStart(2, '0');
            const day = String(d.getDate()).padStart(2, '0');
            return `${year}-${month}-${day}`;
        }

        function getYesterdayDateString() {
            const d = new Date();
            d.setDate(d.getDate() - 1);
            const year = d.getFullYear();
            const month = String(d.getMonth() + 1).padStart(2, '0');
            const day = String(d.getDate()).padStart(2, '0');
            return `${year}-${month}-${day}`;
        }

        const manifestData = {
            "name": "স্মার্ট শপ ক্যাশবুক & রেজিস্টার",
            "short_name": "ক্যাশবুক",
            "start_url": ".",
            "display": "standalone",
            "background_color": "#ffffff",
            "theme_color": "#00796b",
            "icons": [
                {
                    "src": "https://cdn-icons-png.flaticon.com/512/3135/3135715.png",
                    "sizes": "512x512",
                    "type": "image/png"
                }
            ]
        };
        const stringManifest = JSON.stringify(manifestData);
        const blob = new Blob([stringManifest], {type: 'application/json'});
        const manifestURL = URL.createObjectURL(blob);
        document.getElementById('manifestPlaceholder').setAttribute('href', manifestURL);

        let deferredPrompt;
        window.addEventListener('beforeinstallprompt', (e) => {
            e.preventDefault();
            deferredPrompt = e;
            document.getElementById('pwaInstallBtnLogin').style.display = 'inline-block';
            document.getElementById('pwaInstallBtnHeader').style.display = 'inline-block';
        });

        function triggerPwaInstall() {
            if (deferredPrompt) {
                deferredPrompt.prompt();
                deferredPrompt.userChoice.then((choiceResult) => {
                    if (choiceResult.outcome === 'accepted') {
                        showToast("🎉 অ্যাপ ইনস্টল করা সম্পন্ন হয়েছে!");
                    }
                    deferredPrompt = null;
                });
            } else {
                showToast("ℹ️ ব্রাউজারের 'Add to Home Screen' বা 'Install' অপশন ব্যবহার করুন।");
            }
        }

        const firebaseConfig = {
            apiKey: "AIzaSyCtYKg1x3R-79LxOIbgmKHe0xUwRhcrRJU",
            authDomain: "ahsna-441f8.firebaseapp.com",
            databaseURL: "https://ahsna-441f8-default-rtdb.firebaseio.com",
            projectId: "ahsna-441f8",
            storageBucket: "ahsna-441f8.firebasestorage.app",
            messagingSenderId: "837983040035",
            appId: "1:837983040035:web:38e27ad247668757923796",
            measurementId: "G-TYLVEWZ2GR"
        };

        if (!firebase.apps.length) {
            firebase.initializeApp(firebaseConfig);
        }
        
        const db = firebase.database();
        try { 
            db.goOnline(); 
            db.setPersistenceEnabled(true);
        } catch(e) {}

        const dbRef = db.ref('cashbook_data');
        const settingsRef = db.ref('cashbook_settings');
        const auth = firebase.auth();

        let appData = { customers: [], transactions: [], stock: [] };
        let hasUnsavedChanges = false;
        let currentUserRole = 'admin';
        let inactivityTimer;

        function resetInactivityTimer() {
            clearTimeout(inactivityTimer);
            inactivityTimer = setTimeout(() => {
                const appMainContainer = document.getElementById('appMainContainer');
                if (appMainContainer && appMainContainer.style.display === 'block') {
                    alert("⚠️ ৫ মিনিট কোনো কার্যক্রম না থাকায় সিকিউরিটির জন্য স্বয়ংক্রিয়ভাবে লগআউট করা হয়েছে।");
                    logout();
                }
            }, 300000);
        }

        function preventNegative(inputElement) {
            inputElement.value = inputElement.value.replace(/[^0-9]/g, '');
            if (inputElement.value < 0) { inputElement.value = 0; }
        }

        document.addEventListener('input', function (e) {
            if (e.target.tagName === 'INPUT' || e.target.tagName === 'SELECT') {
                hasUnsavedChanges = true;
                e.target.classList.remove('input-error');
            }
        });

        document.addEventListener('click', function (e) {
            if (!e.target.closest('.custom-dropdown-container')) {
                const txList = document.getElementById('txDropdownList');
                if(txList) txList.style.display = 'none';
                const regList = document.getElementById('regDropdownList');
                if(regList) regList.style.display = 'none';
                const memoList = document.getElementById('memoDropdownList');
                if(memoList) memoList.style.display = 'none';
                const regMemoList = document.getElementById('regMemoDropdownList');
                if(regMemoList) regMemoList.style.display = 'none';
                const addrList = document.getElementById('addressDropdownList');
                if(addrList) addrList.style.display = 'none';
                const adminRecList = document.getElementById('adminRecDropdownList');
                if(adminRecList) adminRecList.style.display = 'none';
            }
        });

        window.onload = function () {
            const today = getLocalDateString();
            document.getElementById('txDate').value = today;
            document.getElementById('newCustDate').value = today;
            document.getElementById('adminRecDate').value = today;
            document.getElementById('revenueStartDate').value = today;
            document.getElementById('revenueEndDate').value = today;

            const events = ['mousemove', 'keydown', 'click', 'scroll', 'touchstart'];
            events.forEach(event => {
                document.addEventListener(event, resetInactivityTimer, true);
            });

            const urlParams = new URLSearchParams(window.location.search);
            const mode = urlParams.get('mode');
            const actionCode = urlParams.get('oobCode');

            if (mode === 'resetPassword' && actionCode) {
                auth.verifyPasswordResetCode(actionCode).then((email) => {
                    window.pendingResetEmail = email;
                    window.pendingActionCode = actionCode;
                    document.getElementById('setupScreen').style.display = 'none';
                    document.getElementById('loginScreen').style.display = 'none';
                    document.getElementById('newPinResetModal').style.display = 'block';
                }).catch((error) => {
                    showToast("❌ রিসেট লিঙ্কটি মেয়াদোত্তীর্ণ বা ভুল!", true);
                    checkExistingSettings();
                });
            } else {
                checkExistingSettings();
            }
        };

        function checkExistingSettings() {
            settingsRef.once('value').then((snapshot) => {
                window.savedSecuritySettings = snapshot.val();
                if (window.savedSecuritySettings && window.savedSecuritySettings.email) {
                    document.getElementById('setupScreen').style.display = 'none';
                    document.getElementById('loginScreen').style.display = 'block';
                    handleRoleChange();
                } else {
                    document.getElementById('setupScreen').style.display = 'block';
                    document.getElementById('loginScreen').style.display = 'none';
                }
            }).catch(err => {
                document.getElementById('setupScreen').style.display = 'block';
            });
        }

        function handleRoleChange() {
            const role = document.getElementById('loginRoleSelect').value;
            const emailInput = document.getElementById('loginEmailInput');
            
            if (role === 'admin') {
                if (window.savedSecuritySettings && window.savedSecuritySettings.email) {
                    emailInput.value = window.savedSecuritySettings.email;
                }
                emailInput.style.display = 'none';
            } else {
                if (window.savedSecuritySettings && window.savedSecuritySettings.managerEmail) {
                    emailInput.value = window.savedSecuritySettings.managerEmail;
                }
                emailInput.style.display = 'block';
            }
        }

        async function confirmNewPinReset() {
            const newPin = document.getElementById('newAdminPinInput').value.trim();
            if (newPin.length < 6) {
                showToast("⚠ নতুন পাসওয়ার্ড অন্তত ৬ ডিজিটের হতে হবে!", true);
                return;
            }
            if (window.pendingActionCode && window.pendingResetEmail) {
                try {
                    await auth.confirmPasswordReset(window.pendingActionCode, newPin);
                    showToast("✅ সফলভাবে নতুন পাসওয়ার্ড সেট করা হয়েছে!");
                    document.getElementById('newPinResetModal').style.display = 'none';
                    window.location.href = window.location.pathname;
                } catch(e) {
                    showToast("❌ পাসওয়ার্ড আপডেট করতে সমস্যা হয়েছে: " + e.message, true);
                }
            }
        }

        function showToast(msg, isError = false) {
            const toast = document.getElementById("notificationToast");
            if(!toast) return;
            toast.innerText = msg;
            toast.style.backgroundColor = isError ? "#c62828" : "#333";
            toast.className = "show";
            setTimeout(() => { toast.className = ""; }, 3000);
        }

        function switchTab(tabId) {
            const tabs = document.querySelectorAll('.tab-content');
            tabs.forEach(tab => tab.classList.remove('active'));
            const activeTab = document.getElementById(tabId);
            if (activeTab) activeTab.classList.add('active');

            const navBtns = document.querySelectorAll('.tab-btn, .bottom-nav-item');
            navBtns.forEach(btn => {
                if (btn.classList.contains(`btn-${tabId}`)) {
                    btn.classList.add('active');
                } else {
                    btn.classList.remove('active');
                }
            });

            if (tabId === 'revenueTab') calculateRevenue();
            if (tabId === 'dashboardTab') refreshUI();
        }

        function toggleInstallmentOptions(prefix) {
            const toggleEl = document.getElementById(`${prefix}InstallmentToggle`);
            if(!toggleEl) return;
            const isChecked = toggleEl.checked;
            const container = document.getElementById(`${prefix}InstallmentOptionsContainer`);
            if(container) container.style.display = isChecked ? 'block' : 'none';

            if (prefix === 'tx') {
                const normPrice = document.getElementById('txNormalPriceContainer');
                const instPaid = document.getElementById('txInstallmentPaidContainer');
                if(normPrice) normPrice.style.display = isChecked ? 'none' : 'flex';
                if(instPaid) instPaid.style.display = isChecked ? 'flex' : 'none';
                calculateDuePreview();
            } else if (prefix === 'newCust') {
                const normPrice = document.getElementById('newCustNormalPriceContainer');
                const instPaid = document.getElementById('newCustInstallmentPaidContainer');
                if(normPrice) normPrice.style.display = isChecked ? 'none' : 'flex';
                if(instPaid) instPaid.style.display = isChecked ? 'flex' : 'none';
                calculateNewCustDuePreview();
            }
        }

        function calculateDuePreview() {
            const toggleEl = document.getElementById('txInstallmentToggle');
            const isInstChecked = toggleEl ? toggleEl.checked : false;
            let totalPrice = 0;
            let paidAmount = 0;

            if (isInstChecked) {
                totalPrice = parseFloat(document.getElementById('txFirstRate').value) || 0;
                paidAmount = parseFloat(document.getElementById('txPaidAmountInst').value) || 0;
                const type = document.getElementById('txInstallmentType').value;
                const firstRate = parseFloat(document.getElementById('txFirstRate').value) || 0;
                const secondRate = parseFloat(document.getElementById('txSecondRate').value) || 0;
                const breakdownText = document.getElementById('txInstallmentBreakdownText');
                if(breakdownText) {
                    if(type === '3_6') {
                        breakdownText.innerText = `১ম পর্যায় (৩ মাস রেট/মূল দাম): ${firstRate} Tk | ২য় পর্যায় (৬ মাস রেট): ${secondRate} Tk`;
                    } else {
                        breakdownText.innerText = `১ম পর্যায় (৬ মাস রেট/মূল দাম): ${firstRate} Tk | ২য় পর্যায় (৯ মাস রেট): ${secondRate} Tk`;
                    }
                }
            } else {
                totalPrice = parseFloat(document.getElementById('txTotalPrice').value) || 0;
                paidAmount = parseFloat(document.getElementById('txPaidAmount').value) || 0;
                const breakdownText = document.getElementById('txInstallmentBreakdownText');
                if(breakdownText) breakdownText.innerText = '';
            }

            const due = totalPrice - paidAmount;
            const dueText = document.getElementById('calculatedDueText');
            if(dueText) dueText.innerText = `${due >= 0 ? due : 0} Tk`;
        }

        function calculateNewCustDuePreview() {
            const toggleEl = document.getElementById('newCustInstallmentToggle');
            const isInstChecked = toggleEl ? toggleEl.checked : false;
            let totalPrice = 0;
            let paidAmount = 0;

            if (isInstChecked) {
                totalPrice = parseFloat(document.getElementById('newCustFirstRate').value) || 0;
                paidAmount = parseFloat(document.getElementById('newCustPaidAmountInst').value) || 0;
                const type = document.getElementById('newCustInstallmentType').value;
                const firstRate = parseFloat(document.getElementById('newCustFirstRate').value) || 0;
                const secondRate = parseFloat(document.getElementById('newCustSecondRate').value) || 0;
                const breakdownText = document.getElementById('newCustInstallmentBreakdownText');
                if(breakdownText) {
                    if(type === '3_6') {
                        breakdownText.innerText = `১ম পর্যায় (৩ মাস রেট/মূল দাম): ${firstRate} Tk | ২য় পর্যায় (৬ মাস রেট): ${secondRate} Tk`;
                    } else {
                        breakdownText.innerText = `১ম পর্যায় (৬ মাস রেট/মূল দাম): ${firstRate} Tk | ২য় পর্যায় (৯ মাস রেট): ${secondRate} Tk`;
                    }
                }
            } else {
                totalPrice = parseFloat(document.getElementById('newCustTotalPrice').value) || 0;
                paidAmount = parseFloat(document.getElementById('newCustPaidAmount').value) || 0;
                const breakdownText = document.getElementById('newCustInstallmentBreakdownText');
                if(breakdownText) breakdownText.innerText = '';
            }

            const due = totalPrice - paidAmount;
            const dueText = document.getElementById('newCustCalculatedDueText');
            if(dueText) dueText.innerText = `${due >= 0 ? due : 0} Tk`;
        }

        async function saveInitialSetup() {
            const email = document.getElementById('setRecoveryEmail').value.trim();
            const pin = document.getElementById('setAdminPin').value.trim();
            const managerEmail = document.getElementById('setManagerEmail').value.trim();
            const managerPin = document.getElementById('setManagerPin').value.trim();

            if (!email || !email.includes('@') || !managerEmail || !managerEmail.includes('@')) {
                showToast("⚠ সঠিক জিমেইল অ্যাড্রেস প্রদান করুন!", true);
                return;
            }
            if (pin.length < 6 || managerPin.length < 6) {
                showToast("⚠ অ্যাডমিন ও ম্যানেজার পাসওয়ার্ড কমপক্ষে ৬ ডিজিটের হতে হবে!", true);
                return;
            }

            try {
                await auth.createUserWithEmailAndPassword(email, pin);
                
                const secondaryApp = firebase.initializeApp(firebaseConfig, "SecondaryApp");
                await secondaryApp.auth().createUserWithEmailAndPassword(managerEmail, managerPin);
                await secondaryApp.delete();

                const secData = {
                    email: email,
                    managerEmail: managerEmail,
                    loginHistory: []
                };

                await settingsRef.set(secData);
                hasUnsavedChanges = false;
                showToast("✅ অ্যাডমিন ও ম্যানেজার অ্যাকাউন্ট সফলভাবে তৈরি হয়েছে!");
                document.getElementById('setupScreen').style.display = 'none';
                document.getElementById('loginScreen').style.display = 'block';
                checkExistingSettings();
            } catch (error) {
                showToast("❌ সেটআপ করতে সমস্যা হয়েছে: " + error.message, true);
            }
        }

        function showForgotModal() { document.getElementById('forgotModal').style.display = 'block'; }
        function closeForgotModal() { document.getElementById('forgotModal').style.display = 'none'; }

        function sendEmailResetLink() {
            const email = document.getElementById('recoveryEmailInput').value.trim();
            if (!email || !email.includes('@')) {
                showToast("⚠ সঠিক ইমেইল আইডি লিখুন!", true);
                return;
            }
            
            const adminEmail = window.savedSecuritySettings ? window.savedSecuritySettings.email : '';
            const managerEmail = window.savedSecuritySettings ? window.savedSecuritySettings.managerEmail : '';

            const isMatchAdmin = adminEmail && adminEmail.toLowerCase() === email.toLowerCase();
            const isMatchManager = managerEmail && managerEmail.toLowerCase() === email.toLowerCase();

            if (!isMatchAdmin && !isMatchManager) {
                showToast("❌ এই ইমেইল আইডিটি অ্যাডমিন বা ম্যানেজার কারোরই রেজিস্টার্ড নয়!", true);
                return;
            }

            auth.sendPasswordResetEmail(email)
            .then(() => {
                showToast("📧 সফলভাবে আপনার ইমেইলে পাসওয়ার্ড রিসেট লিঙ্ক পাঠানো হয়েছে!");
                closeForgotModal();
            })
            .catch((error) => {
                showToast("❌ রিসেট লিঙ্ক পাঠাতে সমস্যা হয়েছে: " + error.message, true);
            });
        }

        function initRealtimeSync() {
            dbRef.on('value', (snapshot) => {
                const val = snapshot.val();
                if (val) {
                    appData.customers = Array.isArray(val.customers) ? val.customers : Object.values(val.customers || {});
                    appData.transactions = Array.isArray(val.transactions) ? val.transactions : Object.values(val.transactions || {});
                    appData.stock = Array.isArray(val.stock) ? val.stock : Object.values(val.stock || {});
                } else {
                    appData = { customers: [], transactions: [], stock: [] };
                }
                updateProductDatalist();
                refreshUI();
                calculateRevenue();
                const regSelect = document.getElementById('regCustSelect');
                if (regSelect && regSelect.value) {
                    loadCustomerLedger();
                }
            }, (error) => {
                showToast("❌ ফায়ারবেস ডাটা সিঙ্ক করতে সমস্যা হয়েছে!", true);
            });
        }

        function updateProductDatalist() {
            const datalist = document.getElementById('productList');
            if (!datalist) return;
            let uniqueNames = [];
            appData.stock.forEach(s => {
                if (s.name && !uniqueNames.includes(s.name)) {
                    uniqueNames.push(s.name);
                }
            });
            let html = '';
            uniqueNames.forEach(name => {
                html += `<option value="${name}"></option>`;
            });
            datalist.innerHTML = html;
        }

        function checkTxStockAvailability() {
            const itemName = document.getElementById('txItem').value.trim();
            const warningEl = document.getElementById('txStockWarning');
            if(!warningEl) return;
            if(!itemName) {
                warningEl.innerText = '';
                return;
            }
            const matchingStocks = appData.stock.filter(s => s.name && s.name.toLowerCase() === itemName.toLowerCase());
            if(matchingStocks.length > 0) {
                let totalAvailable = matchingStocks.reduce((sum, s) => sum + (parseInt(s.qty) || 0), 0);
                warningEl.innerText = `✅ স্টকে মজুত আছে: ${totalAvailable} টি`;
                warningEl.style.color = '#2e7d32';
            } else {
                warningEl.innerText = `⚠️ এই নামে কোনো পণ্য স্টকে নেই!`;
                warningEl.style.color = '#d84315';
            }
        }

        function checkNewCustStockAvailability() {
            const itemName = document.getElementById('newCustItem').value.trim();
            const warningEl = document.getElementById('newCustStockWarning');
            if(!warningEl) return;
            if(!itemName) {
                warningEl.innerText = '';
                return;
            }
            const matchingStocks = appData.stock.filter(s => s.name && s.name.toLowerCase() === itemName.toLowerCase());
            if(matchingStocks.length > 0) {
                let totalAvailable = matchingStocks.reduce((sum, s) => sum + (parseInt(s.qty) || 0), 0);
                warningEl.innerText = `✅ স্টকে মজুত আছে: ${totalAvailable} টি`;
                warningEl.style.color = '#2e7d32';
            } else {
                warningEl.innerText = `⚠️ এই নামে কোনো পণ্য স্টকে নেই!`;
                warningEl.style.color = '#d84315';
            }
        }

        function getUniqueAddresses() {
            let addrs = [];
            appData.customers.forEach(c => {
                if (c.address && c.address.trim() !== '' && !addrs.includes(c.address.trim())) {
                    addrs.push(c.address.trim());
                }
            });
            return addrs;
        }

        function openAddressDropdown() {
            renderAddressDropdown(getUniqueAddresses());
            document.getElementById('addressDropdownList').style.display = 'block';
        }

        function filterAddressDropdown() {
            const query = document.getElementById('newCustAddress').value.toLowerCase().trim();
            const addrs = getUniqueAddresses();
            const filtered = addrs.filter(a => a.toLowerCase().includes(query));
            renderAddressDropdown(filtered);
            document.getElementById('addressDropdownList').style.display = 'block';
        }

        function renderAddressDropdown(list) {
            const listDiv = document.getElementById('addressDropdownList');
            if(!listDiv) return;
            if (list.length === 0) {
                listDiv.innerHTML = '<div class="custom-dropdown-item" style="color:#888;">নতুন ঠিকানা লিখুন</div>';
                return;
            }
            let html = '';
            list.forEach(addr => {
                html += `<div class="custom-dropdown-item" onclick="selectAddress('${addr.replace(/'/g, "\\'")}')"><b>${addr}</b></div>`;
            });
            listDiv.innerHTML = html;
        }

        function selectAddress(addr) {
            document.getElementById('newCustAddress').value = addr;
            document.getElementById('addressDropdownList').style.display = 'none';
        }

        // অ্যাডমিন ডিরেক্ট টাকা জমা বা মেমো দিয়ে সার্চ করার ড্রপডাউন ফাংশন
        function openAdminRecDropdown() {
            renderAdminRecDropdown(appData.customers);
            document.getElementById('adminRecDropdownList').style.display = 'block';
        }

        function filterAdminRecDropdownItems() {
            const query = document.getElementById('adminRecCustSearchInput').value.toLowerCase().trim();
            
            let matchedCustIds = [];
            if(query !== '') {
                appData.transactions.forEach(t => {
                    if(t.memo && t.memo.toLowerCase().includes(query)) {
                        if(t.custId && !matchedCustIds.includes(String(t.custId))) {
                            matchedCustIds.push(String(t.custId));
                        }
                    }
                });
            }

            const filtered = appData.customers.filter(c => 
                c.name.toLowerCase().includes(query) || 
                (c.phone && c.phone.includes(query)) || 
                (c.address && c.address.toLowerCase().includes(query)) ||
                matchedCustIds.includes(String(c.id))
            );

            renderAdminRecDropdown(filtered);
            document.getElementById('adminRecDropdownList').style.display = 'block';
        }

        function renderAdminRecDropdown(list) {
            const listDiv = document.getElementById('adminRecDropdownList');
            if(!listDiv) return;
            if(list.length === 0) {
                listDiv.innerHTML = '<div class="custom-dropdown-item" style="color:#888;">কোনো কাস্টমার বা মেমো পাওয়া যায়নি</div>';
                return;
            }
            let html = '';
            list.forEach(c => {
                const custTxs = appData.transactions.filter(t => String(t.custId) === String(c.id));
                const lastMemo = custTxs.length > 0 ? (custTxs[custTxs.length - 1].memo || 'মেমো নেই') : 'মেমো নেই';

                html += `<div class="custom-dropdown-item" onclick="selectAdminRecCustomer('${c.id}', '${c.name.replace(/'/g, "\\'")}', '${c.phone || ''}', '${(c.address || '').replace(/'/g, "\\'")}', '${lastMemo}')">
                    <div>
                        <b>${c.name}</b> (${c.phone || 'মোবাইল নেই'})<br>
                        <span style="font-size: 11px; color: #00796b;">মেমো নং: ${lastMemo}</span>
                    </div>
                    <span style="font-size: 12px; color: #666;">${c.address || ''}</span>
                </div>`;
            });
            listDiv.innerHTML = html;
        }

        function selectAdminRecCustomer(id, name, phone, address, memo) {
            document.getElementById('adminRecCustSelect').value = id;
            document.getElementById('adminRecCustSearchInput').value = `${name} - ${phone} (${address}) [মেমো: ${memo}]`;
            if(memo && memo !== 'মেমো নেই') {
                document.getElementById('adminRecMemo').value = memo;
            }
            document.getElementById('adminRecDropdownList').style.display = 'none';
        }

        async function saveAdminDirectPayment() {
            if (currentUserRole !== 'admin') {
                showToast("⚠️ দুঃখিত, ম্যানেজার এই ফিচারটি ব্যবহার করতে পারবেন না!", true);
                return;
            }

            const custId = document.getElementById('adminRecCustSelect').value;
            const amount = parseFloat(document.getElementById('adminRecAmount').value) || 0;
            const date = document.getElementById('adminRecDate').value || getLocalDateString();
            const memo = document.getElementById('adminRecMemo').value.trim();

            if (!custId) {
                showToast("⚠ অনুগ্রহ করে কাস্টমার বা মেমো দিয়ে কাস্টমার সিলেক্ট করুন!", true);
                return;
            }
            if (amount <= 0) {
                showToast("⚠ জমার সঠিক পরিমাণ লিখুন!", true);
                return;
            }

            const newTx = {
                id: 'tx_' + Date.now(),
                custId: custId,
                memo: memo || 'Direct-Payment',
                item: 'নগদ টাকা জমা (Direct Pay)',
                quantity: 0,
                date: date,
                totalPrice: 0,
                paidAmount: amount,
                hasInstallment: false,
                lastEdited: '-'
            };

            appData.transactions.push(newTx);
            await saveData();
            refreshUI();
            showToast("✅ সফলভাবে টাকা জমা নেওয়া হয়েছে!");

            document.getElementById('adminRecCustSelect').value = '';
            document.getElementById('adminRecCustSearchInput').value = '';
            document.getElementById('adminRecAmount').value = '';
            document.getElementById('adminRecMemo').value = '';
        }

        function openTxDropdown() {
            renderTxDropdown(appData.customers);
            document.getElementById('txDropdownList').style.display = 'block';
        }

        function filterTxDropdownItems() {
            const query = document.getElementById('txCustSearchInput').value.toLowerCase().trim();
            const filtered = appData.customers.filter(c => 
                c.name.toLowerCase().includes(query) || 
                (c.phone && c.phone.includes(query)) || 
                (c.address && c.address.toLowerCase().includes(query))
            );
            renderTxDropdown(filtered);
            document.getElementById('txDropdownList').style.display = 'block';
        }

        function renderTxDropdown(list) {
            const listDiv = document.getElementById('txDropdownList');
            if(!listDiv) return;
            if(list.length === 0) {
                listDiv.innerHTML = '<div class="custom-dropdown-item" style="color:#888;">কোনো কাস্টমার পাওয়া যায়নি</div>';
                return;
            }
            let html = '';
            list.forEach(c => {
                html += `<div class="custom-dropdown-item" onclick="selectTxCustomer('${c.id}', '${c.name.replace(/'/g, "\\'")}', '${c.phone || ''}', '${(c.address || '').replace(/'/g, "\\'")}')">
                    <span><b>${c.name}</b> (${c.phone || 'মোবাইল নেই'})</span>
                    <span style="font-size: 12px; color: #666;">${c.address || ''}</span>
                </div>`;
            });
            listDiv.innerHTML = html;
        }

        function selectTxCustomer(id, name, phone, address) {
            document.getElementById('txCustSelect').value = id;
            document.getElementById('txCustSearchInput').value = `${name} - ${phone} (${address})`;
            document.getElementById('txDropdownList').style.display = 'none';
        }

        function openMemoDropdown() {
            renderMemoDropdown(appData.transactions);
            document.getElementById('memoDropdownList').style.display = 'block';
        }

        function filterMemoDropdownItems() {
            const query = document.getElementById('txMemo').value.toLowerCase().trim();
            const filtered = appData.transactions.filter(t => t.memo && t.memo.toLowerCase().includes(query));
            renderMemoDropdown(filtered);
            document.getElementById('memoDropdownList').style.display = 'block';
        }

        function renderMemoDropdown(list) {
            const listDiv = document.getElementById('memoDropdownList');
            if(!listDiv) return;
            if(list.length === 0) {
                listDiv.innerHTML = '<div class="custom-dropdown-item" style="color:#888;">নতুন মেমো হিসেবে সেভ হবে</div>';
                return;
            }
            let uniqueMemos = [];
            let html = '';
            list.forEach(t => {
                if(t.memo && !uniqueMemos.includes(t.memo)) {
                    uniqueMemos.push(t.memo);
                    const cust = appData.customers.find(c => String(c.id) === String(t.custId));
                    const custName = cust ? cust.name : 'অজানা কাস্টমার';
                    html += `<div class="custom-dropdown-item" onclick="selectMemoItem('${t.memo}', '${t.custId}', '${custName.replace(/'/g, "\\'")}', '${cust ? (cust.phone || '') : ''}')">
                        <span><b>মেমো: ${t.memo}</b></span>
                        <span style="font-size: 12px; color: #00796b;">কাস্টমার: ${custName}</span>
                    </div>`;
                }
            });
            listDiv.innerHTML = html || '<div class="custom-dropdown-item" style="color:#888;">কোনো মেমো নেই</div>';
        }

        function selectMemoItem(memoNo, custId, custName, custPhone) {
            document.getElementById('txMemo').value = memoNo;
            document.getElementById('memoDropdownList').style.display = 'none';
            
            if(custId) {
                const customer = appData.customers.find(c => String(c.id) === String(custId));
                if(customer) {
                    selectTxCustomer(customer.id, customer.name, customer.phone, customer.address);
                }
            }
        }

        function openRegDropdown() {
            renderRegDropdown(appData.customers);
            document.getElementById('regDropdownList').style.display = 'block';
        }

        function filterRegDropdownItems() {
            const query = document.getElementById('regCustSearchInput').value.toLowerCase().trim();
            if (query === '0') {
                let paidCusts = appData.customers.filter(c => {
                    const custTxs = appData.transactions.filter(t => String(t.custId) === String(c.id));
                    const totalCost = custTxs.reduce((sum, t) => sum + (parseFloat(t.totalPrice) || 0), 0);
                    const totalPaid = custTxs.reduce((sum, t) => sum + (parseFloat(t.paidAmount) || 0), 0);
                    return (totalCost - totalPaid) <= 0 && custTxs.length > 0;
                });
                renderRegDropdown(paidCusts);
                document.getElementById('regDropdownList').style.display = 'block';
                return;
            }
            const filtered = appData.customers.filter(c => 
                c.name.toLowerCase().includes(query) || 
                (c.phone && c.phone.includes(query)) || 
                (c.address && c.address.toLowerCase().includes(query))
            );
            renderRegDropdown(filtered);
            document.getElementById('regDropdownList').style.display = 'block';
        }

        function renderRegDropdown(list) {
            const listDiv = document.getElementById('regDropdownList');
            if(!listDiv) return;
            if(list.length === 0) {
                listDiv.innerHTML = '<div class="custom-dropdown-item" style="color:#888;">কোনো কাস্টমার পাওয়া যায়নি</div>';
                return;
            }
            let html = '';
            list.forEach(c => {
                html += `<div class="custom-dropdown-item" onclick="selectRegCustomer('${c.id}', '${c.name.replace(/'/g, "\\'")}', '${c.phone || ''}', '${(c.address || '').replace(/'/g, "\\'")}')">
                    <span><b>${c.name}</b> (${c.phone || 'মোবাইল নেই'})</span>
                    <span style="font-size: 12px; color: #666;">${c.address || ''}</span>
                </div>`;
            });
            listDiv.innerHTML = html;
        }

        function selectRegCustomer(id, name, phone, address) {
            document.getElementById('regCustSelect').value = id;
            document.getElementById('regCustSearchInput').value = `${name} - ${phone} (${address})`;
            document.getElementById('regDropdownList').style.display = 'none';
            loadCustomerLedger();
        }

        function openRegMemoDropdown() {
            renderRegMemoDropdown(appData.transactions);
            document.getElementById('regMemoDropdownList').style.display = 'block';
        }

        function filterRegMemoDropdownItems() {
            const query = document.getElementById('regMemoSearchInput').value.toLowerCase().trim();
            const filtered = appData.transactions.filter(t => t.memo && t.memo.toLowerCase().includes(query));
            renderRegMemoDropdown(filtered);
            document.getElementById('regMemoDropdownList').style.display = 'block';
        }

        function renderRegMemoDropdown(list) {
            const listDiv = document.getElementById('regMemoDropdownList');
            if(!listDiv) return;
            if(list.length === 0) {
                listDiv.innerHTML = '<div class="custom-dropdown-item" style="color:#888;">কোনো মেমো পাওয়া যায়নি</div>';
                return;
            }
            let uniqueMemos = [];
            let html = '';
            list.forEach(t => {
                if(t.memo && !uniqueMemos.includes(t.memo)) {
                    uniqueMemos.push(t.memo);
                    const cust = appData.customers.find(c => String(c.id) === String(t.custId));
                    const custName = cust ? cust.name : 'অজানা কাস্টমার';
                    html += `<div class="custom-dropdown-item" onclick="selectRegMemoItem('${t.custId}', '${t.memo}')">
                        <span><b>মেমো: ${t.memo}</b></span>
                        <span style="font-size: 12px; color: #00796b;">কাস্টমার: ${custName}</span>
                    </div>`;
                }
            });
            listDiv.innerHTML = html || '<div class="custom-dropdown-item" style="color:#888;">কোনো মেমো নেই</div>';
        }

        function selectRegMemoItem(custId, memoNo) {
            document.getElementById('regMemoDropdownList').style.display = 'none';
            document.getElementById('regMemoSearchInput').value = memoNo;
            if(custId) {
                selectCustomerForLedger(custId);
            }
        }

        async function verifyPin() {
            const entered = document.getElementById('pinInput').value.trim();
            const selectedRole = document.getElementById('loginRoleSelect').value;
            const emailInputVal = document.getElementById('loginEmailInput').value.trim();

            if (!window.savedSecuritySettings) {
                showToast("⚠ সেটিংস ডাটাবেজে পাওয়া যায়নি!", true);
                return;
            }

            try {
                if (selectedRole === 'admin') {
                    const adminEmail = window.savedSecuritySettings.email;
                    await auth.signInWithEmailAndPassword(adminEmail, entered);
                    currentUserRole = 'admin';
                    await recordLoginHistory("অ্যাডমিন (Admin)");
                    loginSuccess("অ্যাডমিন (Admin)");
                } else if (selectedRole === 'manager') {
                    const managerEmail = window.savedSecuritySettings.managerEmail || emailInputVal;
                    if (!managerEmail) {
                        showToast("⚠ ম্যানেজারের জিমেইল প্রদান করুন!", true);
                        return;
                    }
                    await auth.signInWithEmailAndPassword(managerEmail, entered);
                    currentUserRole = 'manager';
                    await recordLoginHistory("ম্যানেজার (Manager)");
                    loginSuccess("ম্যানেজার (Manager)");
                }
            } catch (e) {
                showToast("❌ ভুল জিমেইল বা পাসওয়ার্ড! লগইন ব্যর্থ হয়েছে।", true);
            }
        }

        async function recordLoginHistory(roleText) {
            try {
                let ipAddress = "অজানা আইপি";
                let locationInfo = "";
                try {
                    const response = await fetch('https://ipapi.co/json/');
                    const data = await response.json();
                    if (data && data.ip) {
                        ipAddress = data.ip;
                        locationInfo = ` (${data.city || ''}, ${data.country_name || ''})`;
                    }
                } catch (err) {
                    try {
                        const fallbackRes = await fetch('https://api.ipify.org?format=json');
                        const fallbackData = await fallbackRes.json();
                        if (fallbackData && fallbackData.ip) {
                            ipAddress = fallbackData.ip;
                        }
                    } catch(e2) {}
                }

                const ua = navigator.userAgent;
                let deviceName = "ল্যাপটপ / পিসি (Desktop)";
                if (/android/i.test(ua)) {
                    deviceName = "অ্যান্ড্রয়েড ফোন (Android)";
                } else if (/iphone|ipad|ipod/i.test(ua)) {
                    deviceName = "আইফোন / আইপ্যাড (iOS)";
                } else if (/mobile/i.test(ua)) {
                    deviceName = "মোবাইল ডিভাইস";
                }

                const now = new Date();
                const formattedTime = now.toLocaleString('bn-BD', { day: '2-digit', month: '2-digit', year: 'numeric', hour: '2-digit', minute: '2-digit', second: '2-digit' });

                let history = window.savedSecuritySettings.loginHistory || [];
                if (!Array.isArray(history)) {
                    history = Object.values(history);
                }

                const newLogEntry = {
                    role: roleText,
                    time: formattedTime,
                    ip: ipAddress + locationInfo,
                    device: deviceName
                };

                history.unshift(newLogEntry);
                if (history.length > 15) {
                    history = history.slice(0, 15);
                }

                await settingsRef.update({ loginHistory: history });
            } catch (e) {
                console.error("Login history error:", e);
            }
        }

        function renderLoginHistory() {
            const tbody = document.getElementById('loginHistoryTableBody');
            if (!tbody) return;
            tbody.innerHTML = '';

            if (!window.savedSecuritySettings || !window.savedSecuritySettings.loginHistory) {
                tbody.innerHTML = '<tr><td colspan="4" style="color:#777;">কোনো লগইন রেকর্ড নেই।</td></tr>';
                return;
            }

            let history = window.savedSecuritySettings.loginHistory;
            if (!Array.isArray(history)) {
                history = Object.values(history);
            }

            if (history.length === 0) {
                tbody.innerHTML = '<tr><td colspan="4" style="color:#777;">কোনো লগইন রেকর্ড নেই।</td></tr>';
                return;
            }

            let html = '';
            history.forEach(h => {
                html += `
                    <tr>
                        <td><b>${h.role}</b></td>
                        <td>${h.device || 'অজানা ডিভাইস'}</td>
                        <td style="font-family: monospace; font-size: 13px;">${h.ip || 'N/A'}</td>
                        <td>${h.time}</td>
                    </tr>
                `;
            });
            tbody.innerHTML = html;
        }

        function loginSuccess(roleName) {
            document.getElementById('pinInput').value = '';
            document.getElementById('loginScreen').style.display = 'none';
            document.getElementById('appMainContainer').style.display = 'block';
            document.getElementById('currentRoleBadge').innerText = `লগইন: ${roleName}`;

            const adminOnlyElements = document.querySelectorAll('.admin-only');
            const adminActionCols = document.querySelectorAll('.admin-action-col');
            const adminReceiveTabBtn = document.getElementById('navAdminReceiveTab');
            const bottomAdminReceiveTabBtn = document.getElementById('bottomAdminReceiveTab');

            if(currentUserRole !== 'admin') {
                const navSet = document.getElementById('navSettingsTab');
                const botSet = document.getElementById('bottomSettingsTab');
                if(navSet) navSet.style.display = 'none';
                if(botSet) botSet.style.display = 'none';
                if(adminReceiveTabBtn) adminReceiveTabBtn.style.display = 'none';
                if(bottomAdminReceiveTabBtn) bottomAdminReceiveTabBtn.style.display = 'none';

                adminOnlyElements.forEach(el => el.style.display = 'none');
                adminActionCols.forEach(el => el.style.display = 'none');
            } else {
                const navSet = document.getElementById('navSettingsTab');
                const botSet = document.getElementById('bottomSettingsTab');
                if(navSet) navSet.style.display = 'inline-block';
                if(botSet) botSet.style.display = 'flex';
                if(adminReceiveTabBtn) adminReceiveTabBtn.style.display = 'inline-block';
                if(bottomAdminReceiveTabBtn) bottomAdminReceiveTabBtn.style.display = 'flex';

                adminOnlyElements.forEach(el => el.style.display = 'inline-block');
                adminActionCols.forEach(el => el.style.display = 'table-cell');
            }

            initRealtimeSync();
            renderLoginHistory();
            resetInactivityTimer();
        }

        function logout() {
            dbRef.off();
            auth.signOut();
            document.getElementById('appMainContainer').style.display = 'none';
            document.getElementById('loginScreen').style.display = 'block';
            document.getElementById('pinInput').value = '';
            showToast("🔒 আপনি সফলভাবে লগআউট হয়েছেন।");
        }

        async function saveData() {
            if (dbRef) {
                try {
                    await dbRef.set(appData);
                } catch (err) {
                    showToast("❌ ডাটা সেভ করতে সমস্যা হয়েছে!", true);
                }
            }
        }

        async function toggleCustomerRedMark(custId) {
            const cust = appData.customers.find(c => String(c.id) === String(custId));
            if(cust) {
                cust.isRedMarked = !cust.isRedMarked;
                await saveData();
                refreshUI();
                showToast(cust.isRedMarked ? "🔴 কাস্টমারকে লাল রঙ দিয়ে চিহ্নিত করা হয়েছে!" : "⚪ কাস্টমারকে আনমার্ক করা হয়েছে!");
            }
        }

        function checkInstallmentDatesAndApprovals() {
            const now = new Date();
            let approvalRequests = [];

            appData.transactions.forEach(t => {
                if (t.hasInstallment && t.date) {
                    const custTxs = appData.transactions.filter(tx => String(tx.custId) === String(t.custId));
                    const totalCost = custTxs.reduce((sum, tx) => sum + (parseFloat(tx.totalPrice) || 0), 0);
                    const totalPaid = custTxs.reduce((sum, tx) => sum + (parseFloat(tx.paidAmount) || 0), 0);
                    const dueAmount = totalCost - totalPaid;

                    if (dueAmount > 0) {
                        const txDate = new Date(t.date);
                        const diffTime = Math.abs(now - txDate);
                        const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));
                        const firstPeriodDays = t.installmentType === '3_6' ? 90 : 180;

                        if (diffDays >= firstPeriodDays && t.installmentPhase === 1 && !t.phase2Approved) {
                            const customer = appData.customers.find(c => String(c.id) === String(t.custId));
                            const custName = customer ? customer.name : "N/A";
                            const custPhone = customer ? customer.phone : "N/A";
                            const custAddress = customer ? customer.address : "N/A";
                            const productName = t.item || "N/A";

                            approvalRequests.push({
                                txId: t.id,
                                memo: t.memo || "N/A",
                                date: t.date,
                                type: t.installmentType,
                                secondRate: t.secondRate || 0,
                                name: custName,
                                phone: custPhone,
                                address: custAddress,
                                productName: productName,
                                dueAmount: dueAmount
                            });
                        }
                    }
                }
            });

            const panel = document.getElementById('adminApprovalPanel');
            const content = document.getElementById('approvalListContent');
            if(!panel || !content) return;

            if (approvalRequests.length > 0) {
                let html = '';
                approvalRequests.forEach(req => {
                    const periodName = req.type === '3_6' ? '৩ মাস' : '৬ মাস';
                    let actionBtnHtml = currentUserRole === 'admin' 
                        ? `<button class="btn-action btn-green" style="padding: 8px 14px;" onclick="approveInstallmentPhase2('${req.txId}')">অনুমোদন দিন</button>`
                        : `<span style="color:#d84315; font-weight:bold;">অ্যাডমিনের অনুমোদনের অপেক্ষায়</span>`;

                    html += `
                        <div style="background:#ffffff; padding:12px; border-radius:8px; margin-bottom:10px; border: 1px solid #ffe0b2; display:flex; justify-content:space-between; align-items:center; flex-wrap:wrap; gap:10px;">
                            <div style="font-size: 14px; line-height: 1.5; color:#333;">
                                🔔 <b>কিস্তি মেয়াদ উত্তীর্ণ নোটিফিকেশন</b><br>
                                👤 <b>নাম:</b> ${req.name} | 📞 <b>মোবাইল:</b> ${req.phone} | 📍 <b>ঠিকানা:</b> ${req.address}<br>
                                📦 <b>পণ্যের নাম:</b> ${req.productName} | 📝 <b>মেমো নং:</b> ${req.memo} | 💰 <b>বকেয়া (Due):</b> ${req.dueAmount} Tk<br>
                                <span style="color: #d84315;">মেমো: ${req.memo} (তারিখ: ${req.date}) - ${periodName} মেয়াদ পূর্ণ হয়েছে। পরবর্তী ধাপের রেট (${req.secondRate} Tk) কার্যকর করুন।</span>
                            </div>
                            ${actionBtnHtml}
                        </div>
                    `;
                });
                content.innerHTML = html;
                panel.style.display = 'block';
            } else {
                panel.style.display = 'none';
            }
        }

        async function approveInstallmentPhase2(txId) {
            if(currentUserRole !== 'admin') {
                showToast("⚠️ শুধু অ্যাডমিন কিস্তি অনুমোদন করতে পারবেন!", true);
                return;
            }
            const tx = appData.transactions.find(t => String(t.id) === String(txId));
            if(tx) {
                tx.phase2Approved = true;
                tx.installmentPhase = 2;
                tx.totalPrice = tx.secondRate || tx.totalPrice;
                await saveData();
                refreshUI();
                showToast("✅ সফলভাবে পরবর্তী মেয়াদের রেট ও মোট দাম আপডেট করা হয়েছে!");
            }
        }

        function refreshUI() {
            let todayPaid = 0, todaySales = 0;
            const todayStr = getLocalDateString();
            const now = new Date();

            appData.transactions.forEach(t => {
                if (t.date === todayStr) {
                    todayPaid += (parseFloat(t.paidAmount) || 0);
                    todaySales += (parseFloat(t.totalPrice) || 0);
                }
            });

            let totalStockQty = 0;
            appData.stock.forEach(s => {
                totalStockQty += (parseInt(s.qty) || 0);
            });
            const totalStockEl = document.getElementById('dashTotalStock');
            if(totalStockEl) totalStockEl.innerText = `${totalStockQty} টি`;

            const overdueTbody = document.getElementById('dashOverdueTableBody');
            let overdueRows = [];
            let overdueCustomersCount = 0;

            appData.customers.forEach(c => {
                const custTxs = appData.transactions.filter(t => String(t.custId) === String(c.id));
                const totalCost = custTxs.reduce((sum, t) => sum + (parseFloat(t.totalPrice) || 0), 0);
                const totalPaid = custTxs.reduce((sum, t) => sum + (parseFloat(t.paidAmount) || 0), 0);
                const bal = totalCost - totalPaid;

                if (bal > 0) {
                    const sortedTxs = custTxs.sort((a, b) => new Date(b.date) - new Date(a.date));
                    const lastTx = sortedTxs[0];
                    let isOverdue = false;
                    let lastTxDateStr = 'কোনো লেনদেন নেই';

                    if (lastTx && lastTx.date) {
                        lastTxDateStr = lastTx.date;
                        const lastTxDate = new Date(lastTx.date);
                        const diffTime = Math.abs(now - lastTxDate);
                        const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));
                        if (diffDays > 30) {
                            isOverdue = true;
                        }
                    } else {
                        isOverdue = true;
                    }

                    if (isOverdue) {
                        overdueCustomersCount++;
                        const isRed = c.isRedMarked ? true : false;
                        const rowClass = isRed ? 'red-marked-row' : '';
                        const displayName = isRed ? `${c.name} <span class="custom-red-tag" onclick="toggleCustomerRedMark('${c.id}')" title="আনমার্ক করুন"></span>` : `${c.name} <span class="custom-red-tag inactive" onclick="toggleCustomerRedMark('${c.id}')" title="লাল মার্ক করুন"></span>`;
                        
                        overdueRows.push({
                            isRed: isRed,
                            balance: bal,
                            html: `
                                <tr class="${rowClass}">
                                    <td><b>${displayName}</b></td>
                                    <td>${c.phone || '-'}</td>
                                    <td>${c.address || '-'}</td>
                                    <td>${lastTxDateStr}</td>
                                    <td style="color:#c62828;"><b>${bal} Tk</b></td>
                                </tr>`
                        });
                    }
                }
            });

            overdueRows.sort((a, b) => {
                if ((b.isRed === true) !== (a.isRed === true)) {
                    return (b.isRed === true) - (a.isRed === true);
                }
                return b.balance - a.balance;
            });
            let overdueHtml = overdueRows.map(r => r.html).join('');

            if (overdueTbody) {
                overdueTbody.innerHTML = overdueHtml || '<tr><td colspan="5" style="color:#2e7d32;">১ মাসের বেশি অলস বাকি থাকা কোনো কাস্টমার নেই!</td></tr>';
            }

            const todayPaidEl = document.getElementById('dashTodayPaid');
            const todaySalesEl = document.getElementById('dashTodaySales');
            const overdueCountEl = document.getElementById('dashOverdueCount');

            if(todayPaidEl) todayPaidEl.innerText = `${todayPaid} Tk`;
            if(todaySalesEl) todaySalesEl.innerText = `${todaySales} Tk`;
            if(overdueCountEl) overdueCountEl.innerText = `${overdueCustomersCount} জন`;

            renderGlobalCustomerTable(appData.transactions);
            renderGlobalDueTable();
            renderStockTable();
            checkInstallmentDatesAndApprovals();
        }

        function renderGlobalCustomerTable(transactionsToRender) {
            const tbody = document.getElementById('customerTableBody');
            if(!tbody) return;

            const nameQuery = document.getElementById('globalCustomerSearch') ? document.getElementById('globalCustomerSearch').value.toLowerCase().trim() : '';
            const addrQuery = document.getElementById('globalAddressSearch') ? document.getElementById('globalAddressSearch').value.toLowerCase().trim() : '';

            const todayStr = getLocalDateString();
            const yesterdayStr = getYesterdayDateString();

            let rowsWithMeta = [];

            appData.customers.forEach(c => {
                const custTxs = transactionsToRender.filter(t => String(t.custId) === String(c.id));
                if(custTxs.length === 0) return;

                custTxs.sort((a, b) => new Date(b.date) - new Date(a.date));
                const latestTx = custTxs[0];

                const totalCost = custTxs.reduce((sum, t) => sum + (parseFloat(t.totalPrice) || 0), 0);
                const totalPaid = custTxs.reduce((sum, t) => sum + (parseFloat(t.paidAmount) || 0), 0);
                const currentBalance = totalCost - totalPaid;

                const nameMatch = c.name.toLowerCase().includes(nameQuery) || (c.phone && c.phone.includes(nameQuery));
                const addrMatch = !addrQuery || (c.address && c.address.toLowerCase().includes(addrQuery));

                if(nameMatch && addrMatch) {
                    let hasToday = custTxs.some(t => t.date === todayStr && (parseFloat(t.paidAmount) || 0) > 0);
                    let hasYesterday = !hasToday && custTxs.some(t => t.date === yesterdayStr && (parseFloat(t.paidAmount) || 0) > 0);

                    let priority = 2;
                    if(hasToday) priority = 0;
                    else if(hasYesterday) priority = 1;

                    rowsWithMeta.push({
                        priority: priority,
                        date: latestTx.date || '',
                        html: `
                            <tr onclick="selectCustomerForLedger('${c.id}')" style="cursor: pointer;">
                                <td><b>${c.name}</b></td>
                                <td>${c.phone || '-'}</td>
                                <td>${c.address || '-'}</td>
                                <td style="color:#00796b; font-weight:bold;">${latestTx.memo || '-'}</td>
                                <td>${latestTx.date || '-'}</td>
                                <td style="color:#2e7d32; font-weight:bold;">${latestTx.paidAmount || 0} Tk</td>
                                <td style="color:#c62828; font-weight:bold;">${currentBalance} Tk</td>
                            </tr>
                        `
                    });
                }
            });

            rowsWithMeta.sort((a, b) => {
                if (a.priority !== b.priority) {
                    return a.priority - b.priority;
                }
                return new Date(b.date) - new Date(a.date);
            });

            let finalHtml = rowsWithMeta.map(r => r.html).join('');
            tbody.innerHTML = finalHtml || '<tr><td colspan="7" style="color:#777;">কোনো কাস্টমার পাওয়া যায়নি।</td></tr>';
        }

        function filterGlobalCustomers() {
            renderGlobalCustomerTable(appData.transactions);
        }

        function renderGlobalDueTable() {
            const tbody = document.getElementById('dueTableBody');
            if(!tbody) return;

            const nameQuery = document.getElementById('globalDueSearch') ? document.getElementById('globalDueSearch').value.toLowerCase().trim() : '';
            const addrQuery = document.getElementById('globalDueAddressSearch') ? document.getElementById('globalDueAddressSearch').value.toLowerCase().trim() : '';

            let dueRows = [];

            appData.customers.forEach(c => {
                const custTxs = appData.transactions.filter(t => String(t.custId) === String(c.id));
                const totalCost = custTxs.reduce((sum, t) => sum + (parseFloat(t.totalPrice) || 0), 0);
                const totalPaid = custTxs.reduce((sum, t) => sum + (parseFloat(t.paidAmount) || 0), 0);
                const currentBalance = totalCost - totalPaid;

                if (currentBalance > 0) {
                    const nameMatch = c.name.toLowerCase().includes(nameQuery) || (c.phone && c.phone.includes(nameQuery));
                    const addrMatch = !addrQuery || (c.address && c.address.toLowerCase().includes(addrQuery));

                    if(nameMatch && addrMatch) {
                        custTxs.sort((a, b) => new Date(b.date) - new Date(a.date));
                        const latestTx = custTxs[0];
                        const lastMemo = latestTx ? (latestTx.memo || '-') : '-';

                        const isRed = c.isRedMarked ? true : false;
                        const rowClass = isRed ? 'red-marked-row' : '';
                        const displayName = isRed ? `${c.name} <span class="custom-red-tag" onclick="toggleCustomerRedMark('${c.id}')" title="আনমার্ক করুন"></span>` : `${c.name} <span class="custom-red-tag inactive" onclick="toggleCustomerRedMark('${c.id}')" title="লাল মার্ক করুন"></span>`;

                        dueRows.push({
                            isRed: isRed,
                            balance: currentBalance,
                            html: `
                                <tr class="${rowClass}">
                                    <td><b>${displayName}</b></td>
                                    <td>${c.phone || '-'}</td>
                                    <td>${c.address || '-'}</td>
                                    <td style="color:#00796b; font-weight:bold;">${lastMemo}</td>
                                    <td style="color:#c62828; font-weight:bold;">${currentBalance} Tk</td>
                                    <td>
                                        <button class="btn-action ${isRed ? 'btn-delete' : 'btn-orange'}" onclick="toggleCustomerRedMark('${c.id}')">${isRed ? 'আনমার্ক' : 'লাল মার্ক'}</button>
                                    </td>
                                    <td>
                                        <button class="btn-action btn-edit" onclick="selectCustomerForLedger('${c.id}')">হিস্ট্রি দেখুন</button>
                                    </td>
                                </tr>
                            `
                        });
                    }
                }
            });

            dueRows.sort((a, b) => {
                if ((b.isRed === true) !== (a.isRed === true)) {
                    return (b.isRed === true) - (a.isRed === true);
                }
                return b.balance - a.balance;
            });

            tbody.innerHTML = dueRows.map(r => r.html).join('') || '<tr><td colspan="7" style="color:#2e7d32;">কারো কাছেই কোনো বাকি নেই!</td></tr>';
        }

        function filterGlobalDueCustomers() {
            renderGlobalDueTable();
        }

        function loadCustomerLedger() {
            const custId = document.getElementById('regCustSelect').value;
            const ledgerContent = document.getElementById('ledgerContent');
            if(!custId) {
                if(ledgerContent) ledgerContent.style.display = 'none';
                return;
            }

            const customer = appData.customers.find(c => String(c.id) === String(custId));
            if(!customer) return;

            document.getElementById('ledgerCustName').innerText = customer.name;
            document.getElementById('ledgerCustPhone').innerText = customer.phone || '-';
            document.getElementById('ledgerCustAddr').innerText = customer.address || '-';

            const custTxs = appData.transactions.filter(t => String(t.custId) === String(custId));
            custTxs.sort((a, b) => new Date(b.date) - new Date(a.date));

            let totalCost = 0;
            let totalPaid = 0;
            let tbodyHtml = '';

            let chronologicalTxs = [...custTxs].sort((a, b) => new Date(a.date) - new Date(b.date));
            let runningBalances = {};
            let tempCost = 0, tempPaid = 0;
            chronologicalTxs.forEach(t => {
                tempCost += (parseFloat(t.totalPrice) || 0);
                tempPaid += (parseFloat(t.paidAmount) || 0);
                runningBalances[t.id] = tempCost - tempPaid;
                totalCost = tempCost;
                totalPaid = tempPaid;
            });

            custTxs.forEach(t => {
                let currentBal = runningBalances[t.id] !== undefined ? runningBalances[t.id] : 0;

                let instInfo = '-';
                if(t.hasInstallment) {
                    const pName = t.installmentType === '3_6' ? '৩ ও ৬ মাস' : '৬ ও ৯ মাস';
                    instInfo = `<span style="color:#00796b; font-weight:bold;">কিস্তি (${pName})<br>১ম: ${t.firstRate} | ২য়: ${t.secondRate}</span>`;
                }

                let actionBtns = '-';
                if (currentUserRole === 'admin') {
                    actionBtns = `
                        <button class="btn-action btn-edit" onclick="openEditModal('${t.id}')" title="এডিট করুন"><i class="fa-solid fa-pen"></i></button>
                        <button class="btn-action btn-delete" onclick="deleteTransaction('${t.id}')" title="ডিলিট"><i class="fa-solid fa-trash"></i></button>
                    `;
                }

                tbodyHtml += `
                    <tr>
                        <td>${t.date || '-'}</td>
                        <td style="color:#00796b; font-weight:bold;">${t.memo || '-'}</td>
                        <td>${t.item || '-'}</td>
                        <td>${t.quantity > 0 ? t.quantity + ' টি' : '-'}</td>
                        <td>${t.totalPrice || 0} Tk</td>
                        <td style="color:#2e7d32; font-weight:bold;">${t.paidAmount || 0} Tk</td>
                        <td style="color:#c62828; font-weight:bold;">${currentBal} Tk</td>
                        <td>${instInfo}</td>
                        <td style="font-size:12px; color:#555;">${t.lastEdited || '-'}</td>
                        <td class="admin-action-col" style="${currentUserRole !== 'admin' ? 'display:none;' : ''}">${actionBtns}</td>
                    </tr>
                `;
            });

            document.getElementById('ledgerTotalCost').innerText = `${totalCost} Tk`;
            document.getElementById('ledgerTotalPaid').innerText = `${totalPaid} Tk`;
            document.getElementById('ledgerCurrentBalance').innerText = `${totalCost - totalPaid} Tk`;
            document.getElementById('ledgerTableBody').innerHTML = tbodyHtml || '<tr><td colspan="10" style="color:#777;">কোনো লেনদেন পাওয়া যায়নি।</td></tr>';
            ledgerContent.style.display = 'block';
        }

        async function deleteCurrentCustomer() {
            if(currentUserRole !== 'admin') {
                showToast("⚠ ম্যানেজার হিসেবে কাস্টমার ডিলিট করা নিষেধ!", true);
                return;
            }
            const custId = document.getElementById('regCustSelect').value;
            if(!custId) {
                showToast("⚠️ অনুগ্রহ করে প্রথমে একজন কাস্টমার সিলেক্ট করুন!", true);
                return;
            }
            
            if(confirm("⚠️ সাবধান! এই কাস্টমার এবং তার সমস্ত লেনদেনের ইতিহাস পাকাপাকিভাবে মুছে ফেলতে চান?")) {
                appData.customers = appData.customers.filter(c => String(c.id) !== String(custId));
                appData.transactions = appData.transactions.filter(t => String(t.custId) !== String(custId));
                
                document.getElementById('regCustSelect').value = '';
                document.getElementById('regCustSearchInput').value = '';
                const ledgerContent = document.getElementById('ledgerContent');
                if(ledgerContent) ledgerContent.style.display = 'none';

                await saveData();
                refreshUI();
                showToast("🗑 কাস্টমার এবং তার সমস্ত হিস্ট্রি সফলভাবে মুছে ফেলা হয়েছে!");
            }
        }

        function openEditModal(txId) {
            if(currentUserRole !== 'admin') {
                showToast("⚠️ ম্যানেজার হিসেবে লেনদেন এডিট করা নিষেধ!", true);
                return;
            }
            const tx = appData.transactions.find(t => String(t.id) === String(txId));
            if(!tx) {
                showToast("❌ লেনদেনটি পাওয়া যায়নি!", true);
                return;
            }
            document.getElementById('editTxId').value = tx.id;
            document.getElementById('editTxMemo').value = tx.memo || '';
            document.getElementById('editTxItem').value = tx.item || '';
            document.getElementById('editTxQuantity').value = tx.quantity || 1;
            document.getElementById('editTxDate').value = tx.date || getLocalDateString();
            document.getElementById('editTxTotalPrice').value = tx.totalPrice || 0;
            document.getElementById('editTxPaidAmount').value = tx.paidAmount || 0;
            document.getElementById('editTxModal').style.display = 'block';
        }

        function closeEditModal() {
            document.getElementById('editTxModal').style.display = 'none';
        }

        async function saveEditedTransaction() {
            if(currentUserRole !== 'admin') {
                showToast("⚠️ ম্যানেজার হিসেবে লেনদেন এডিট করা নিষেধ!", true);
                return;
            }
            const txId = document.getElementById('editTxId').value;
            const txIndex = appData.transactions.findIndex(t => String(t.id) === String(txId));
            if(txIndex === -1) {
                showToast("❌ এডিট করার সময় লেনদেনটি খুঁজে পাওয়া যায়নি!", true);
                return;
            }

            appData.transactions[txIndex].memo = document.getElementById('editTxMemo').value.trim() || 'N/A';
            appData.transactions[txIndex].item = document.getElementById('editTxItem').value.trim() || 'N/A';
            appData.transactions[txIndex].quantity = parseInt(document.getElementById('editTxQuantity').value) || 1;
            appData.transactions[txIndex].date = document.getElementById('editTxDate').value || getLocalDateString();
            appData.transactions[txIndex].totalPrice = parseFloat(document.getElementById('editTxTotalPrice').value) || 0;
            appData.transactions[txIndex].paidAmount = parseFloat(document.getElementById('editTxPaidAmount').value) || 0;
            appData.transactions[txIndex].lastEdited = new Date().toLocaleString('bn-BD');

            await saveData();
            closeEditModal();
            refreshUI();
            loadCustomerLedger();
            showToast("✅ লেনদেন সফলভাবে আপডেট করা হয়েছে!");
        }

        async function deleteTransaction(txId) {
            if(currentUserRole !== 'admin') {
                showToast("⚠️ ম্যানেজার হিসেবে লেনদেন ডিলিট করা নিষেধ!", true);
                return;
            }
            if(confirm("⚠️ এই লেনদেনটি ডিলিট করতে চান?")) {
                appData.transactions = appData.transactions.filter(t => String(t.id) !== String(txId));
                await saveData();
                refreshUI();
                loadCustomerLedger();
                showToast("🗑 লেনদেনটি ডিলিট করা হয়েছে!");
            }
        }

        function selectCustomerForLedger(custId) {
            const customer = appData.customers.find(c => String(c.id) === String(custId));
            if(customer) {
                document.getElementById('regCustSelect').value = customer.id;
                document.getElementById('regCustSearchInput').value = `${customer.name} - ${customer.phone || ''} (${customer.address || ''})`;
                loadCustomerLedger();
                switchTab('registerBookTab');
            }
        }

        // স্টক থেকে অটো মাইনাস করার লজিক ফাংশন
        function deductStockQuantity(itemName, soldQty) {
            if (!itemName || soldQty <= 0) return;
            let remainingToDeduct = soldQty;
            for (let i = 0; i < appData.stock.length; i++) {
                if (appData.stock[i].name && appData.stock[i].name.toLowerCase() === itemName.toLowerCase()) {
                    let currentStockQty = parseInt(appData.stock[i].qty) || 0;
                    if (currentStockQty >= remainingToDeduct) {
                        appData.stock[i].qty = currentStockQty - remainingToDeduct;
                        remainingToDeduct = 0;
                        break;
                    } else {
                        remainingToDeduct -= currentStockQty;
                        appData.stock[i].qty = 0;
                    }
                }
            }
        }

        async function addNewCustomer() {
            const name = document.getElementById('newCustName').value.trim();
            const phone = document.getElementById('newCustPhone').value.trim();
            const address = document.getElementById('newCustAddress').value.trim();
            const memo = document.getElementById('newCustMemo').value.trim();
            const item = document.getElementById('newCustItem').value.trim();
            const quantity = parseInt(document.getElementById('newCustQuantity').value) || 1;
            const date = document.getElementById('newCustDate').value || getLocalDateString();

            const isInstChecked = document.getElementById('newCustInstallmentToggle').checked;
            let totalPrice = 0;
            let paidAmount = 0;
            let firstRate = 0;
            let secondRate = 0;
            let installmentType = '';

            if (isInstChecked) {
                installmentType = document.getElementById('newCustInstallmentType').value;
                firstRate = parseFloat(document.getElementById('newCustFirstRate').value) || 0;
                secondRate = parseFloat(document.getElementById('newCustSecondRate').value) || 0;
                totalPrice = firstRate;
                paidAmount = parseFloat(document.getElementById('newCustPaidAmountInst').value) || 0;
            } else {
                totalPrice = parseFloat(document.getElementById('newCustTotalPrice').value) || 0;
                paidAmount = parseFloat(document.getElementById('newCustPaidAmount').value) || 0;
            }

            if (!name || !phone || !address || !item || totalPrice <= 0) {
                showToast("⚠ কাস্টমারের নাম, মোবাইল, ঠিকানা, পণ্যের নাম এবং সঠিক দাম পূরণ করুন!", true);
                return;
            }

            // স্টক থেকে অটো পরিমাণ মাইনাস করা
            deductStockQuantity(item, quantity);

            const newCustId = 'cust_' + Date.now();
            const newCustomer = {
                id: newCustId,
                name: name,
                phone: phone,
                address: address,
                isRedMarked: false
            };

            const newTx = {
                id: 'tx_' + Date.now(),
                custId: newCustId,
                memo: memo || 'N/A',
                item: item,
                quantity: quantity,
                date: date,
                totalPrice: totalPrice,
                paidAmount: paidAmount,
                hasInstallment: isInstChecked,
                installmentType: installmentType,
                firstRate: firstRate,
                secondRate: secondRate,
                installmentPhase: isInstChecked ? 1 : 0,
                phase2Approved: false,
                lastEdited: '-'
            };

            appData.customers.push(newCustomer);
            appData.transactions.push(newTx);
            await saveData();
            refreshUI();
            showToast("✅ সফলভাবে নতুন কাস্টমার ও লেনদেন সেভ হয়েছে!");

            document.getElementById('newCustName').value = '';
            document.getElementById('newCustPhone').value = '';
            document.getElementById('newCustAddress').value = '';
            document.getElementById('newCustMemo').value = '';
            document.getElementById('newCustItem').value = '';
            document.getElementById('newCustQuantity').value = '1';
            document.getElementById('newCustTotalPrice').value = '';
            document.getElementById('newCustPaidAmount').value = '';
            document.getElementById('newCustFirstRate').value = '';
            document.getElementById('newCustSecondRate').value = '';
            document.getElementById('newCustPaidAmountInst').value = '';
            document.getElementById('newCustCalculatedDueText').innerText = '0 Tk';
            document.getElementById('newCustStockWarning').innerText = '';

            switchTab('registerBookTab');
            selectCustomerForLedger(newCustId);
        }

        async function addTransactionDirect() {
            const custId = document.getElementById('txCustSelect').value;
            const memo = document.getElementById('txMemo').value.trim();
            const item = document.getElementById('txItem').value.trim();
            const quantity = parseInt(document.getElementById('txQuantity').value) || 1;
            const date = document.getElementById('txDate').value || getLocalDateString();

            const isInstChecked = document.getElementById('txInstallmentToggle').checked;
            let totalPrice = 0;
            let paidAmount = 0;
            let firstRate = 0;
            let secondRate = 0;
            let installmentType = '';

            if (isInstChecked) {
                installmentType = document.getElementById('txInstallmentType').value;
                firstRate = parseFloat(document.getElementById('txFirstRate').value) || 0;
                secondRate = parseFloat(document.getElementById('txSecondRate').value) || 0;
                totalPrice = firstRate;
                paidAmount = parseFloat(document.getElementById('txPaidAmountInst').value) || 0;
            } else {
                totalPrice = parseFloat(document.getElementById('txTotalPrice').value) || 0;
                paidAmount = parseFloat(document.getElementById('txPaidAmount').value) || 0;
            }

            if (!custId || !item || totalPrice <= 0) {
                showToast("⚠ কাস্টমার সিলেক্ট করুন, পণ্যের নাম এবং সঠিক দাম লিখুন!", true);
                return;
            }

            // স্টক থেকে অটো পরিমাণ মাইনাস করা
            deductStockQuantity(item, quantity);

            const newTx = {
                id: 'tx_' + Date.now(),
                custId: custId,
                memo: memo || 'N/A',
                item: item,
                quantity: quantity,
                date: date,
                totalPrice: totalPrice,
                paidAmount: paidAmount,
                hasInstallment: isInstChecked,
                installmentType: installmentType,
                firstRate: firstRate,
                secondRate: secondRate,
                installmentPhase: isInstChecked ? 1 : 0,
                phase2Approved: false,
                lastEdited: '-'
            };

            appData.transactions.push(newTx);
            await saveData();
            refreshUI();
            showToast("✅ সফলভাবে লেনদেন সেভ হয়েছে!");

            document.getElementById('txMemo').value = '';
            document.getElementById('txItem').value = '';
            document.getElementById('txQuantity').value = '1';
            document.getElementById('txTotalPrice').value = '';
            document.getElementById('txPaidAmount').value = '';
            document.getElementById('txFirstRate').value = '';
            document.getElementById('txSecondRate').value = '';
            document.getElementById('txPaidAmountInst').value = '';
            document.getElementById('calculatedDueText').innerText = '0 Tk';
            document.getElementById('txStockWarning').innerText = '';

            selectCustomerForLedger(custId);
        }

        function filterGlobalTransactionsByMemo() {
            const query = document.getElementById('globalMemoSearchInput').value.toLowerCase().trim();
            const tbody = document.getElementById('globalMemoSearchResultBody');
            if(!tbody) return;

            if(!query) {
                tbody.innerHTML = '<tr><td colspan="8" style="color:#777;">মেমো নম্বর লিখে সার্চ করুন...</td></tr>';
                return;
            }

            const filtered = appData.transactions.filter(t => t.memo && t.memo.toLowerCase().includes(query));
            if(filtered.length === 0) {
                tbody.innerHTML = '<tr><td colspan="8" style="color:#777;">এই মেমো নম্বর দিয়ে কোনো লেনদেন পাওয়া যায়নি।</td></tr>';
                return;
            }

            let html = '';
            filtered.forEach(t => {
                const cust = appData.customers.find(c => String(c.id) === String(t.custId));
                html += `
                    <tr onclick="selectCustomerForLedger('${t.custId}')" style="cursor: pointer;">
                        <td>${t.date || '-'}</td>
                        <td style="color:#00796b; font-weight:bold;">${t.memo || '-'}</td>
                        <td><b>${cust ? cust.name : 'অজানা'}</b></td>
                        <td>${cust ? (cust.phone || '-') : '-'}</td>
                        <td>${t.item || '-'}</td>
                        <td>${t.quantity > 0 ? t.quantity + ' টি' : '-'}</td>
                        <td>${t.totalPrice || 0} Tk</td>
                        <td style="color:#2e7d32; font-weight:bold;">${t.paidAmount || 0} Tk</td>
                    </tr>
                `;
            });
            tbody.innerHTML = html;
        }

        function calculateRevenue() {
            const filterType = document.getElementById('revenueFilter').value;
            const customContainer = document.getElementById('customDateFilterContainer');
            if(customContainer) {
                customContainer.style.display = filterType === 'custom' ? 'flex' : 'none';
            }

            const todayStr = getLocalDateString();
            const now = new Date();
            let totalSales = 0;
            let totalPaid = 0;
            let totalDue = 0;

            appData.transactions.forEach(t => {
                const tDateStr = t.date || todayStr;
                const tDate = new Date(tDateStr);
                let include = false;

                if (filterType === 'today') {
                    if (tDateStr === todayStr) include = true;
                } else if (filterType === 'week') {
                    const firstDayOfWeek = new Date(now);
                    firstDayOfWeek.setDate(now.getDate() - now.getDay());
                    firstDayOfWeek.setHours(0,0,0,0);
                    if (tDate >= firstDayOfWeek && tDate <= now) include = true;
                } else if (filterType === 'month') {
                    if (tDate.getFullYear() === now.getFullYear() && tDate.getMonth() === now.getMonth()) include = true;
                } else if (filterType === 'year') {
                    if (tDate.getFullYear() === now.getFullYear()) include = true;
                } else if (filterType === 'custom') {
                    const sDateStr = document.getElementById('revenueStartDate').value;
                    const eDateStr = document.getElementById('revenueEndDate').value;
                    if (sDateStr && eDateStr) {
                        const sDate = new Date(sDateStr);
                        const eDate = new Date(eDateStr);
                        eDate.setHours(23,59,59,999);
                        if (tDate >= sDate && tDate <= eDate) include = true;
                    }
                } else if (filterType === 'all') {
                    include = true;
                }

                if (include) {
                    const price = parseFloat(t.totalPrice) || 0;
                    const paid = parseFloat(t.paidAmount) || 0;
                    totalSales += price;
                    totalPaid += paid;
                    totalDue += (price - paid > 0 ? price - paid : 0);
                }
            });

            document.getElementById('revTotalSales').innerText = `${totalSales} Tk`;
            document.getElementById('revTotalPaid').innerText = `${totalPaid} Tk`;
            document.getElementById('revTotalDue').innerText = `${totalDue} Tk`;
        }

        async function addNewStockProduct() {
            const name = document.getElementById('stockProductName').value.trim();
            const model = document.getElementById('stockProductModel').value.trim();
            const qty = parseInt(document.getElementById('stockProductQty').value) || 0;

            if (!name || !model || qty <= 0) {
                showToast("⚠ সঠিক নাম, মডেল এবং পরিমাণ দিন!", true);
                return;
            }

            const existing = appData.stock.find(s => s.name && s.name.toLowerCase() === name.toLowerCase() && s.model && s.model.toLowerCase() === model.toLowerCase());
            if (existing) {
                existing.qty = (parseInt(existing.qty) || 0) + qty;
            } else {
                appData.stock.push({
                    id: 'stock_' + Date.now(),
                    name: name,
                    model: model,
                    qty: qty
                });
            }

            await saveData();
            refreshUI();
            showToast("✅ স্টকে পণ্য যোগ / পরিমাণ বাড়ানো হয়েছে!");
            document.getElementById('stockProductName').value = '';
            document.getElementById('stockProductModel').value = '';
            document.getElementById('stockProductQty').value = '';
        }

        function renderStockTable() {
            const tbody = document.getElementById('stockTableBody');
            if(!tbody) return;

            const query = document.getElementById('stockSearchInput') ? document.getElementById('stockSearchInput').value.toLowerCase().trim() : '';
            const filtered = appData.stock.filter(s => 
                (s.name && s.name.toLowerCase().includes(query)) || 
                (s.model && s.model.toLowerCase().includes(query))
            );

            if(filtered.length === 0) {
                tbody.innerHTML = '<tr><td colspan="5" style="color:#777;">কোনো পণ্য পাওয়া যায়নি।</td></tr>';
                return;
            }

            let html = '';
            filtered.forEach(s => {
                const qty = parseInt(s.qty) || 0;
                let statusBadge = qty > 0 ? `<span style="color:#2e7d32; font-weight:bold;">স্টকে আছে (${qty} টি)</span>` : `<span style="color:#d84315; font-weight:bold;">স্টক আউট (0)</span>`;
                
                let actionBtns = `
                    <button class="btn-action btn-edit" onclick="openEditStockModal('${s.id}')"><i class="fa-solid fa-pen"></i> এডিট</button>
                `;
                if(currentUserRole === 'admin') {
                    actionBtns += ` <button class="btn-action btn-delete" onclick="deleteStockItem('${s.id}')"><i class="fa-solid fa-trash"></i></button>`;
                }

                html += `
                    <tr>
                        <td><b>${s.name || '-'}</b></td>
                        <td>${s.model || '-'}</td>
                        <td style="font-weight:bold; font-size:15px;">${qty} টি</td>
                        <td>${statusBadge}</td>
                        <td>${actionBtns}</td>
                    </tr>
                `;
            });
            tbody.innerHTML = html;
        }

        function filterStockTable() {
            renderStockTable();
        }

        function openEditStockModal(stockId) {
            const s = appData.stock.find(item => String(item.id) === String(stockId));
            if(!s) return;
            document.getElementById('editStockId').value = s.id;
            document.getElementById('editStockName').value = s.name || '';
            document.getElementById('editStockModel').value = s.model || '';
            document.getElementById('editStockQty').value = s.qty || 0;
            document.getElementById('editStockModal').style.display = 'block';
        }

        function closeEditStockModal() {
            document.getElementById('editStockModal').style.display = 'none';
        }

        async function saveEditedStock() {
            const stockId = document.getElementById('editStockId').value;
            const s = appData.stock.find(item => String(item.id) === String(stockId));
            if(!s) return;

            const newQty = parseInt(document.getElementById('editStockQty').value) || 0;
            const oldQty = parseInt(s.qty) || 0;

            // ম্যানেজার যেন স্টক কমাতে না পারে (শুধু বাড়াতে পারবে)
            if (currentUserRole === 'manager' && newQty < oldQty) {
                showToast("⚠️ ম্যানেজার হিসেবে স্টক কমানো নিষেধ! স্টক কেবল বাড়ানো যাবে।", true);
                return;
            }

            s.name = document.getElementById('editStockName').value.trim() || s.name;
            s.model = document.getElementById('editStockModel').value.trim() || s.model;
            s.qty = newQty;

            await saveData();
            closeEditStockModal();
            refreshUI();
            showToast("✅ স্টক সফলভাবে আপডেট করা হয়েছে!");
        }

        async function deleteStockItem(stockId) {
            if(currentUserRole !== 'admin') {
                showToast("⚠️ ম্যানেজার হিসেবে স্টক ডিলিট করা নিষেধ!", true);
                return;
            }
            if(confirm("⚠️️ এই পণ্যটি স্টক থেকে মুছে ফেলতে চান?")) {
                appData.stock = appData.stock.filter(item => String(item.id) !== String(stockId));
                await saveData();
                refreshUI();
                showToast("🗑 পণ্যটি স্টক থেকে মুছে ফেলা হয়েছে!");
            }
        }
    </script>
</body>
</html>
