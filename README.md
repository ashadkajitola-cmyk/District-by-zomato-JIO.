<!DOCTYPE html>
<html lang="hi">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Calendar, Weather & Recharge App</title>
  <script src="https://cdn.jsdelivr.net/npm/tesseract.js@5/dist/tesseract.min.js"></script>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', sans-serif; }
    body { background: #0f172a; color: #f8fafc; min-height: 100vh; display: flex; justify-content: center; align-items: flex-start; padding: 14px; }
    .app-container { width: 100%; max-width: 430px; background: #1e293b; border-radius: 20px; padding: 16px; box-shadow: 0 20px 40px rgba(0,0,0,0.5); border: 1px solid #334155; }

    /* Login */
    .login-card { background: #0f172a; border: 1px solid #334155; border-radius: 16px; padding: 20px; }
    .login-card h2 { font-size: 20px; color: #38bdf8; margin-bottom: 6px; }
    .phone-input, .select-box, .text-input { width: 100%; padding: 11px 12px; border-radius: 10px; border: 1px solid #475569; background: #1e293b; color: #fff; font-size: 14px; font-weight: 600; margin-top: 8px; }

    /* Top Bar & 4 Tabs */
    .topbar { display: flex; justify-content: space-between; align-items: center; gap: 8px; padding-bottom: 10px; margin-bottom: 10px; border-bottom: 1px solid #334155; }
    .topbar h2 { font-size: 18px; color: #38bdf8; }
    .user-pill { font-size: 11px; font-weight: 700; padding: 5px 10px; border-radius: 20px; background: rgba(56,189,248,0.15); color: #38bdf8; border: 1px solid #38bdf8; }
    .vip-pill { background: rgba(250,204,21,0.18); color: #facc15; border-color: #facc15; }
    .btn-mini { padding: 6px 10px; border-radius: 8px; border: 1px solid #475569; background: #0f172a; color: #e2e8f0; font-size: 12px; font-weight: 700; cursor: pointer; }

    .nav-tabs { display: grid; grid-template-columns: repeat(4, 1fr); gap: 6px; margin-bottom: 12px; }
    .nav-tab { padding: 9px 4px; border-radius: 11px; border: 2px solid #334155; background: #0f172a; color: #cbd5e1; font-size: 12px; font-weight: 800; cursor: pointer; text-align: center; }
    .nav-tab.active { background: #0284c7; border-color: #38bdf8; color: #fff; }

    /* Status Message */
    .message-box { display: none; padding: 13px; border-radius: 12px; text-align: center; font-weight: 800; font-size: 15px; margin-bottom: 12px; }
    .msg-success { display: block; background: #14532d; color: #4ade80; border: 2px solid #22c55e; }
    .msg-failed { display: block; background: #7f1d1d; color: #fca5a5; border: 2px solid #ef4444; }

    .section-card { display: none; background: #0f172a; border: 1px solid #334155; border-radius: 16px; padding: 14px; margin-bottom: 14px; position: relative; }
    .section-card.active-view { display: block; }
    .section-title { font-size: 15px; font-weight: 700; color: #e2e8f0; margin-bottom: 8px; }
    .muted { font-size: 12px; color: #94a3b8; line-height: 1.45; }

    /* Lock Overlay */
    .module-lock { position: absolute; inset: 0; background: rgba(15,23,42,0.93); backdrop-filter: blur(5px); border-radius: 16px; display: none; flex-direction: column; justify-content: center; align-items: center; text-align: center; padding: 16px; z-index: 8; }
    .module-lock.active { display: flex; }

    /* Calendar */
    .cal-head { display: flex; justify-content: space-between; align-items: center; margin-bottom: 10px; }
    .cal-head button { background: #1e293b; color: #fff; border: 1px solid #475569; padding: 6px 12px; border-radius: 8px; cursor: pointer; }
    .weekdays, .days-grid { display: grid; grid-template-columns: repeat(7, 1fr); gap: 5px; text-align: center; }
    .weekdays div { font-size: 11px; color: #94a3b8; font-weight: 700; padding-bottom: 4px; }
    .days-grid div { padding: 10px 0; background: #1e293b; border-radius: 8px; font-size: 13px; font-weight: 600; }
    .days-grid div.today { background: #0284c7; color: #fff; }
    .days-grid div.empty { background: transparent; }

    /* Weather */
    .place-chips { display: flex; flex-wrap: wrap; gap: 7px; margin-top: 10px; }
    .place-chip { background: #1e293b; border: 1px solid #475569; color: #e2e8f0; padding: 7px 11px; border-radius: 20px; font-size: 12px; font-weight: 700; cursor: pointer; }
    .place-chip.active { background: #0284c7; border-color: #38bdf8; color: #fff; }
    .forecast-list { margin-top: 12px; display: grid; gap: 8px; }
    .forecast-item { display: flex; justify-content: space-between; align-items: center; background: #1e293b; border: 1px solid #334155; border-radius: 10px; padding: 10px 12px; font-size: 13px; }
    .temp-badge { font-weight: 800; color: #38bdf8; }

    /* Plans List (Photo 1 Style) */
    .plans-stack { display: grid; gap: 12px; }
    .plan-card-row { background: #f8fafc; color: #0f172a; border-radius: 16px; padding: 14px; border: 1px solid #cbd5e1; }
    .plan-tag-pill { display: inline-block; background: #ffedd5; color: #c2410c; font-size: 10px; font-weight: 800; padding: 3px 8px; border-radius: 6px; margin-bottom: 8px; text-transform: uppercase; }
    .plan-main-line { display: flex; justify-content: space-between; align-items: center; gap: 8px; }
    .plan-price-big { font-size: 24px; font-weight: 900; color: #0f172a; }
    .plan-meta-col { font-size: 12px; color: #334155; line-height: 1.4; }
    .plan-meta-col b { color: #0f172a; }
    .buy-pill-btn { background: #1d4ed8; color: #fff; border: none; border-radius: 999px; padding: 9px 18px; font-size: 13px; font-weight: 800; cursor: pointer; white-space: nowrap; }
    .plan-sub-footer { margin-top: 10px; padding-top: 8px; border-top: 1px solid #e2e8f0; display: flex; justify-content: space-between; align-items: center; font-size: 11px; color: #475569; font-weight: 700; }
    .vip-card-controls { display: flex; gap: 8px; margin-top: 10px; }
    .btn-sm { padding: 6px 10px; border-radius: 8px; border: none; font-size: 12px; font-weight: 700; cursor: pointer; }
    .btn-edit { background: #eab308; color: #0f172a; }
    .btn-del { background: #ef4444; color: #fff; }

    /* Plan Details Sheet (Photo 2 Style) */
    .details-sheet { display: none; background: #f8fafc; color: #0f172a; border-radius: 18px; padding: 16px; margin-top: 14px; border: 1px solid #cbd5e1; }
    .details-sheet.active { display: block; }
    .ds-head { display: flex; justify-content: space-between; align-items: center; margin-bottom: 8px; }
    .ds-price { font-size: 28px; font-weight: 900; color: #0f172a; }
    .ds-badge { display: inline-block; background: #ffedd5; color: #c2410c; font-size: 11px; font-weight: 800; padding: 4px 10px; border-radius: 6px; margin-bottom: 12px; }
    .ds-grid { display: grid; grid-template-columns: 1fr 1fr; border: 1px solid #e2e8f0; border-radius: 12px; overflow: hidden; background: #fff; margin-bottom: 14px; }
    .ds-cell { padding: 10px 12px; border-bottom: 1px solid #f1f5f9; font-size: 12px; }
    .ds-cell.lbl { color: #475569; font-weight: 600; }
    .ds-cell.val { color: #0f172a; font-weight: 800; text-align: right; word-break: break-all; }
    .pay-cta-btn { width: 100%; padding: 14px; border: none; border-radius: 999px; background: #1d4ed8; color: #fff; font-size: 15px; font-weight: 800; cursor: pointer; }

    /* Payment Options Box (Directly after Pay button — No Apply Voucher) */
    .pay-methods-box { display: none; background: #f8fafc; color: #0f172a; border-radius: 18px; padding: 16px; margin-top: 14px; border: 1px solid #cbd5e1; }
    .pay-methods-box.active { display: block; }
    .pm-section-lbl { font-size: 14px; font-weight: 800; color: #0f172a; margin-bottom: 10px; }
    .pay-qr-row { display: flex; align-items: center; justify-content: space-between; background: #ffffff; border: 1px solid #e2e8f0; border-radius: 14px; padding: 13px 14px; cursor: pointer; margin-bottom: 14px; }
    .pay-qr-left { display: flex; align-items: center; gap: 12px; font-size: 15px; font-weight: 700; color: #0f172a; }
    .qr-glyph { width: 34px; height: 34px; border-radius: 10px; background: #eff6ff; color: #1d4ed8; display: flex; align-items: center; justify-content: center; font-size: 18px; font-weight: 900; border: 1px solid #bfdbfe; }

    .upi-apps-list { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
    .upi-choice-btn { display: flex; align-items: center; justify-content: center; gap: 8px; padding: 12px 10px; border-radius: 12px; background: #ffffff; border: 1.5px solid #cbd5e1; color: #0f172a; font-size: 13px; font-weight: 800; cursor: pointer; }
    .upi-logo-dot { width: 22px; height: 22px; border-radius: 50%; display: inline-flex; align-items: center; justify-content: center; font-size: 11px; font-weight: 900; color: #fff; }
    .dot-phonepe { background: #5f259f; }
    .dot-paytm { background: #00baf2; }
    .dot-gpay { background: #1a73e8; }
    .dot-navi { background: #00b894; }

    .not-installed-banner { display: none; margin-top: 10px; padding: 10px; border-radius: 10px; background: #fee2e2; border: 1px solid #ef4444; color: #991b1b; font-size: 13px; font-weight: 800; text-align: center; }
    .pending-4hr-note { display: none; margin-top: 10px; padding: 10px; border-radius: 10px; background: #eff6ff; border: 1px solid #93c5fd; color: #1e3a8a; font-size: 12px; font-weight: 700; }

    /* My Plan Card (Photo 3 Style) */
    .myplan-card { background: #1d4ed8; color: #ffffff; border-radius: 18px; overflow: hidden; margin-bottom: 12px; box-shadow: 0 10px 24px rgba(29,78,216,0.35); }
    .myplan-top { padding: 16px; }
    .myplan-num-row { display: flex; justify-content: space-between; align-items: center; font-size: 15px; font-weight: 700; margin-bottom: 10px; }
    .myplan-crown-line { font-size: 13px; font-weight: 700; color: #fde047; margin-bottom: 6px; }
    .myplan-big-days { font-size: 32px; font-weight: 900; line-height: 1.1; }
    .myplan-sub-row { display: flex; justify-content: space-between; font-size: 12px; color: #dbeafe; margin-top: 8px; }
    .myplan-bottom { background: #ffffff; color: #0f172a; padding: 14px 16px; }
    .myplan-meta-grid { display: flex; justify-content: space-between; align-items: flex-start; gap: 10px; }
    .myplan-label { font-size: 11px; color: #64748b; font-weight: 700; }
    .myplan-val { font-size: 16px; font-weight: 900; color: #0f172a; margin-top: 2px; }
    .myplan-benefits-box { margin-top: 10px; padding-top: 10px; border-top: 1px solid #e2e8f0; font-size: 12px; font-weight: 700; color: #1e293b; }

    /* VIP Global Free Offer Box */
    .vip-free-box { display: none; background: #1e293b; border: 1px solid #facc15; border-radius: 14px; padding: 12px; margin-bottom: 14px; }
    .vip-active-offer-bar { background: rgba(250,204,21,0.12); border: 1px dashed #facc15; border-radius: 10px; padding: 10px; margin-top: 10px; }

    .btn { width: 100%; padding: 12px; border: none; border-radius: 12px; font-size: 14px; font-weight: 700; cursor: pointer; margin-top: 8px; }
    .btn-success { background: #16a34a; color: #fff; }
    .btn-success:disabled { background: #334155; color: #64748b; cursor: not-allowed; }
    .btn-danger { background: #dc2626; color: #fff; }

    /* Fullscreen Clean QR Modal */
    .full-qr-modal { display: none; position: fixed; inset: 0; background: #ffffff; color: #0f172a; z-index: 999; overflow-y: auto; padding: 16px 18px 32px; }
    .full-qr-modal.active { display: block; }
    .fqr-inner { max-width: 420px; margin: 0 auto; }
    .fqr-header { display: flex; align-items: center; gap: 10px; margin-bottom: 4px; }
    .fqr-back { background: transparent; border: none; font-size: 22px; font-weight: 700; color: #0f172a; cursor: pointer; padding: 4px 8px; }
    .fqr-title { font-size: 19px; font-weight: 700; color: #0f172a; }
    .fqr-payable { font-size: 17px; font-weight: 600; color: #1e293b; margin-left: 36px; margin-bottom: 16px; }
    .fqr-payable b { font-weight: 800; color: #0f172a; }
    .fqr-box { width: 100%; max-width: 320px; aspect-ratio: 1 / 1; margin: 0 auto 14px; background: #fff; border: 1px solid #e2e8f0; border-radius: 14px; padding: 8px; display: flex; align-items: center; justify-content: center; }
    .fqr-box img { width: 100%; height: 100%; object-fit: contain; display: block; }
    .fqr-share-row { display: flex; justify-content: center; margin-bottom: 12px; }
    .fqr-share-btn { width: 44px; height: 44px; border-radius: 50%; border: 1px solid #cbd5e1; background: #f8fafc; color: #2563eb; font-size: 18px; font-weight: 700; cursor: pointer; }
    .fqr-status { display: flex; justify-content: center; align-items: center; gap: 10px; font-size: 15px; font-weight: 600; color: #1e293b; margin-bottom: 18px; }
    .fqr-spin { width: 19px; height: 19px; border: 2.5px solid #cbd5e1; border-top-color: #2563eb; border-radius: 50%; animation: spin 0.9s linear infinite; }
    @keyframes spin { to { transform: rotate(360deg); } }
    .fqr-scan-lbl { text-align: center; font-size: 14px; font-weight: 600; color: #334155; margin-bottom: 12px; }
    .fqr-icons { display: flex; justify-content: center; gap: 8px; flex-wrap: wrap; margin-bottom: 8px; }
    .fqr-icon { width: 34px; height: 34px; border-radius: 50%; border: 1px solid #e2e8f0; background: #f8fafc; font-size: 10px; font-weight: 800; display: flex; align-items: center; justify-content: center; color: #0f172a; }
    .fqr-more { text-align: center; font-size: 12px; color: #64748b; margin-bottom: 14px; }
    .fqr-secure { text-align: center; font-size: 14px; font-weight: 700; color: #16a34a; margin-bottom: 6px; }
    .fqr-powered { text-align: center; font-size: 13px; color: #334155; font-weight: 600; margin-bottom: 14px; }

    .qr-ss-panel { display: none; background: #0f172a; color: #f8fafc; border-radius: 14px; padding: 14px; margin-top: 14px; border: 1px solid #334155; }
    .qr-ss-panel.active { display: block; }
    .file-input { width: 100%; padding: 10px; background: #1e293b; border: 1px dashed #38bdf8; border-radius: 10px; color: #e2e8f0; margin: 8px 0; font-size: 12px; }
    .preview-img { width: 100%; max-height: 170px; object-fit: contain; border-radius: 10px; margin-top: 6px; display: none; }
    .checklist { margin-top: 8px; font-size: 12px; line-height: 1.55; background: #1e293b; padding: 10px; border-radius: 10px; }
    .pass { color: #4ade80; font-weight: 600; }
    .fail { color: #f87171; font-weight: 600; }
  </style>
</head>
<body>

<div class="app-container">
  <!-- Login Gate -->
  <div id="loginScreen" class="login-card">
    <h2>📱 Phone Number Login</h2>
    <p class="muted">Apna 10-digit phone number daalein. VIP number <b>9120468311</b> par sab free aur Plan + Free Offer Manager open hoga.</p>
    <input type="tel" id="loginPhoneInput" class="phone-input" maxlength="10" placeholder="10-Digit Phone Number" />
    <button class="btn" style="background:#0284c7;color:#fff;" onclick="loginWithPhone()">Login &amp; Start</button>
  </div>

  <!-- Main App -->
  <div id="mainApp" style="display:none;">
    <div class="topbar">
      <h2>📱 Prepaid App</h2>
      <div style="display:flex;align-items:center;gap:6px;">
        <span id="userBadge" class="user-pill">-</span>
        <button class="btn-mini" onclick="logoutUser()">Logout</button>
      </div>
    </div>

    <!-- 4 Top Tabs: Calendar, Weather, Recharge, My Plan -->
    <div class="nav-tabs">
      <button id="tabCalendar" class="nav-tab active" onclick="switchMainTab('calendar')">Calendar</button>
      <button id="tabWeather" class="nav-tab" onclick="switchMainTab('weather')">Weather</button>
      <button id="tabRecharge" class="nav-tab" onclick="switchMainTab('recharge')">Recharge</button>
      <button id="tabMyPlan" class="nav-tab" onclick="switchMainTab('myplan')">My Plan</button>
    </div>

    <div id="messageBox" class="message-box"></div>

    <!-- Tab 1: Calendar -->
    <div id="viewCalendar" class="section-card active-view">
      <div id="calendarLock" class="module-lock">
        <div style="font-size:32px;margin-bottom:6px;">🔒</div>
        <div style="font-weight:700;">Calendar Locked Hai</div>
        <p class="muted" style="margin-top:4px;">Recharge tab se plan lein ya VIP free offer check karein.</p>
      </div>
      <div class="section-title">📅 Calendar <span id="calExpiryTag" style="font-size:12px;color:#4ade80;"></span></div>
      <div class="cal-head">
        <button onclick="changeMonth(-1)">◀</button>
        <strong id="monthYear"></strong>
        <button onclick="changeMonth(1)">▶</button>
      </div>
      <div class="weekdays">
        <div>Sun</div><div>Mon</div><div>Tue</div><div>Wed</div><div>Thu</div><div>Fri</div><div>Sat</div>
      </div>
      <div id="daysGrid" class="days-grid"></div>
    </div>

    <!-- Tab 2: India 7-Day Real Weather -->
    <div id="viewWeather" class="section-card">
      <div id="weatherLock" class="module-lock">
        <div style="font-size:32px;margin-bottom:6px;">🔒</div>
        <div style="font-weight:700;">Weather Locked Hai</div>
        <p class="muted" style="margin-top:4px;">Recharge tab se plan lein ya VIP free offer check karein.</p>
      </div>
      <div class="section-title">🌦️ India 7-Day Real Weather <span id="weatherExpiryTag" style="font-size:12px;color:#4ade80;"></span></div>
      <label class="muted">Rajya (State) Chunein:</label>
      <select id="stateSelect" class="select-box" onchange="onStateChange()">
        <option value="">-- Rajya Select Karein --</option>
      </select>

      <div id="placesContainer" style="display:none;margin-top:10px;">
        <div class="muted">Is rajya ke Jile aur Kasbe (Click karke 7-day weather dekhein):</div>
        <div id="placeChips" class="place-chips"></div>
        <input type="text" id="customTownInput" class="text-input" placeholder="Koi aur jila ya kasba search karein..." onkeydown="if(event.key==='Enter') searchCustomTown()" />
        <button class="btn-mini" style="margin-top:6px;" onclick="searchCustomTown()">Search Jila / Kasba</button>
      </div>

      <div id="weatherResult" class="forecast-list"></div>
    </div>

    <!-- Tab 3: Recharge Plans -> Buy -> Details -> Pay (QR / UPI Apps) -->
    <div id="viewRecharge" class="section-card">
      <!-- VIP Plan CRUD Manager (Only for 9120468311) -->
      <div id="vipPlanManager" style="display:none;background:#1e293b;border:1px solid #facc15;border-radius:14px;padding:12px;margin-bottom:14px;">
        <div class="section-title" style="color:#facc15;">👑 VIP Plan Manager (9120468311)</div>
        <input type="hidden" id="editPlanId" />
        <input type="number" id="planPriceInput" class="text-input" placeholder="Plan Price (₹ me, e.g. 99)" />
        <input type="number" id="planValidityInput" class="text-input" placeholder="Validity (Days me, e.g. 10)" />
        <select id="planBenefitInput" class="select-box">
          <option value="CALENDAR_ONLY">Keval Calendar Open Hoga (Weather Nahi)</option>
          <option value="WEATHER_ONLY">Keval Weather Open Hoga (Calendar Nahi)</option>
          <option value="BOTH">Dono Open Honge (Calendar + Weather)</option>
        </select>
        <button id="savePlanBtn" class="btn btn-success" onclick="saveVipPlan()">Plan Create Karein</button>
        <button id="cancelEditBtn" class="btn-mini" style="display:none;width:100%;margin-top:6px;" onclick="resetPlanForm()">Cancel Edit</button>
      </div>

      <!-- Pending 4-Hour Screenshot Banner -->
      <div id="pending4hrBanner" class="pending-4hr-note">
        ⏳ 4-Hour Pending Order Active: <span id="pending4hrText"></span>
        <button class="btn-mini" style="margin-top:6px;display:block;" onclick="reopenPending4hrOrder()">Screenshot Upload Karein</button>
      </div>

      <!-- Step A: Plans List with Buy Buttons -->
      <div id="plansListSection">
        <div class="section-title">Recharge Plans</div>
        <div id="plansList" class="plans-stack"></div>
      </div>

      <!-- Step B: Plan Details View with Pay Button -->
      <div id="planDetailsSheet" class="details-sheet">
        <div class="ds-head">
          <div class="ds-price" id="dsPrice">₹0</div>
          <button class="btn-mini" onclick="backToPlansList()">✕</button>
        </div>
        <div class="ds-badge" id="dsBadge">PLAN DETAILS</div>
        <div class="ds-grid">
          <div class="ds-cell lbl">Validity</div>
          <div class="ds-cell val" id="dsValidity">-</div>
          <div class="ds-cell lbl">Benefits</div>
          <div class="ds-cell val" id="dsBenefit">-</div>
          <div class="ds-cell lbl">Order ID</div>
          <div class="ds-cell val" id="dsOrderId">-</div>
          <div class="ds-cell lbl">Receiver</div>
          <div class="ds-cell val">9696898751 / Q955492157@ybl</div>
        </div>
        <button id="dsPayBtn" class="pay-cta-btn" onclick="openPayMethodsAfterPayClick()">Pay ₹0.00</button>
      </div>

      <!-- Step C: Directly after Pay -> Pay via QR + PhonePe / Paytm / Navi / Google Pay -->
      <div id="payMethodsBox" class="pay-methods-box">
        <div class="pm-section-lbl">UPI</div>
        <div class="pay-qr-row" onclick="openFullScreenQr()">
          <div class="pay-qr-left">
            <div class="qr-glyph">▦</div>
            <span>Pay via QR</span>
          </div>
          <span style="font-size:18px;font-weight:800;color:#475569;">&gt;</span>
        </div>

        <div class="pm-section-lbl">UPI Apps</div>
        <div class="upi-apps-list">
          <button class="upi-choice-btn" onclick="openUpiAppDirect('phonepe')">
            <span class="upi-logo-dot dot-phonepe">Pe</span>
            <span>PhonePe</span>
          </button>
          <button class="upi-choice-btn" onclick="openUpiAppDirect('paytm')">
            <span class="upi-logo-dot dot-paytm">Pt</span>
            <span>Paytm</span>
          </button>
          <button class="upi-choice-btn" onclick="openUpiAppDirect('navi')">
            <span class="upi-logo-dot dot-navi">Nv</span>
            <span>Navi</span>
          </button>
          <button class="upi-choice-btn" onclick="openUpiAppDirect('gpay')">
            <span class="upi-logo-dot dot-gpay">G</span>
            <span>Google Pay</span>
          </button>
        </div>

        <div id="appNotInstalledAlert" class="not-installed-banner">ye app install nahi he</div>
      </div>
    </div>

    <!-- Tab 4: My Plan + VIP-Only Free Offer Control -->
    <div id="viewMyPlan" class="section-card">
      <!-- VIP Free Offer Creator & Remover (Only for 9120468311) -->
      <div id="vipFreeOfferCreator" class="vip-free-box">
        <div class="section-title" style="color:#facc15;">👑 VIP Free Offer Control (9120468311)</div>
        <input type="text" id="freeOfferNameInput" class="text-input" placeholder="Free Offer Ka Naam (e.g. Maha Diwali Free Pass)" />
        <input type="number" id="freeOfferDaysInput" class="text-input" placeholder="Kitne Din Tak Free (Days, e.g. 7)" />
        <select id="freeOfferScopeInput" class="select-box">
          <option value="BOTH">Free Me Kya Chalega: Calendar + 7-Day Weather (Dono)</option>
          <option value="CALENDAR_ONLY">Free Me Kya Chalega: Keval Calendar</option>
          <option value="WEATHER_ONLY">Free Me Kya Chalega: Keval 7-Day Weather</option>
        </select>
        <button class="btn btn-success" onclick="publishGlobalFreeOffer()">Done</button>

        <div id="vipActiveFreeOfferPanel" class="vip-active-offer-bar" style="display:none;">
          <div id="vipActiveFreeOfferText" style="font-size:12px;font-weight:700;color:#fde047;"></div>
          <button class="btn btn-danger" style="padding:9px;font-size:12px;" onclick="removeGlobalFreeOfferByVip()">Sabhi User Se Free Offer Hatayein</button>
        </div>
      </div>

      <div class="section-title">My Plan</div>
      <div id="myPlanContent"></div>
    </div>
  </div>
</div>

<!-- Full Screen Clean QR Overlay -->
<div id="fullQrModal" class="full-qr-modal">
  <div class="fqr-inner">
    <div class="fqr-header">
      <button class="fqr-back" onclick="closeFullScreenQr()">&lt;</button>
      <div class="fqr-title">Payment</div>
    </div>
    <div class="fqr-payable">Amount payable: <b id="fqrAmount">₹0</b></div>

    <div class="fqr-box">
      <img id="fqrImg" alt="Full Screen Payment QR" />
    </div>

    <div class="fqr-share-row">
      <button class="fqr-share-btn" onclick="shareFullQr()">⇪</button>
    </div>

    <div class="fqr-status">
      <span>Fetching your payment status</span>
      <span class="fqr-spin"></span>
    </div>

    <div class="fqr-scan-lbl">Scan QR code with any UPI app to pay</div>
    <div class="fqr-icons">
      <div class="fqr-icon">CRED</div>
      <div class="fqr-icon">GPay</div>
      <div class="fqr-icon">Pay</div>
      <div class="fqr-icon">HDFC</div>
      <div class="fqr-icon">BHIM</div>
      <div class="fqr-icon">Paytm</div>
      <div class="fqr-icon">UPI</div>
    </div>
    <div class="fqr-more">&amp; more</div>

    <div class="fqr-secure">100% Secure &amp; Encrypted</div>
    <div class="fqr-powered">Powered by <b>Q955492157@ybl</b> • <span id="fqrOrder">-</span></div>

    <button class="btn btn-success" onclick="revealScreenshotInsideQr()">Payment Ho Gaya — Screenshot Upload Karein</button>

    <div id="qrScreenshotBox" class="qr-ss-panel">
      <div class="section-title" style="margin-bottom:4px;">Payment Screenshot Upload Karein</div>
      <p class="muted">Order ID: <b id="qrSsOrderLabel" style="color:#38bdf8;">-</b> (4 ghante tak valid)</p>
      <input type="file" id="ssInput" class="file-input" accept="image/*" onchange="onScreenshotChange(event)" />
      <img id="ssPreview" class="preview-img" alt="Screenshot Preview" />
      <button id="verifySsBtn" class="btn btn-success" disabled onclick="verifyScreenshotAndUnlock()">Verify Screenshot &amp; Unlock</button>
      <div id="ssReport" class="checklist" style="display:none;"></div>
    </div>
  </div>
</div>

<script>
  const VIP_PHONE = "9120468311";
  const UPI_ID = "Q955492157@ybl";
  const MERCHANT_PHONE = "9696898751";
  const PAYEE_NAME = "Prepaid Calendar & Weather";
  const FOUR_HOURS_MS = 4 * 60 * 60 * 1000;

  const INDIA_STATES_PLACES = {
    "Uttar Pradesh": ["Lucknow", "Varanasi", "Kanpur", "Agra", "Prayagraj", "Gorakhpur", "Noida", "Barabanki", "Faizabad"],
    "Bihar": ["Patna", "Gaya", "Muzaffarpur", "Bhagalpur", "Darbhanga", "Purnia"],
    "Maharashtra": ["Mumbai", "Pune", "Nagpur", "Nashik", "Aurangabad", "Thane"],
    "Rajasthan": ["Jaipur", "Jodhpur", "Udaipur", "Kota", "Ajmer", "Bikaner"],
    "Madhya Pradesh": ["Bhopal", "Indore", "Jabalpur", "Gwalior", "Ujjain", "Rewa"],
    "Delhi": ["New Delhi", "Dwarka", "Rohini", "Saket", "Shahdara"],
    "Gujarat": ["Ahmedabad", "Surat", "Vadodara", "Rajkot", "Bhavnagar", "Jamnagar"],
    "West Bengal": ["Kolkata", "Howrah", "Siliguri", "Durgapur", "Asansol"],
    "Tamil Nadu": ["Chennai", "Coimbatore", "Madurai", "Tiruchirappalli", "Salem"],
    "Karnataka": ["Bengaluru", "Mysuru", "Hubballi", "Mangaluru", "Belagavi"],
    "Telangana": ["Hyderabad", "Warangal", "Nizamabad", "Karimnagar"],
    "Punjab": ["Chandigarh", "Ludhiana", "Amritsar", "Jalandhar", "Patiala"],
    "Haryana": ["Gurugram", "Faridabad", "Panipat", "Karnal", "Hisar"],
    "Jharkhand": ["Ranchi", "Jamshedpur", "Dhanbad", "Bokaro"],
    "Chhattisgarh": ["Raipur", "Bhilai", "Bilaspur", "Korba"],
    "Odisha": ["Bhubaneswar", "Cuttack", "Rourkela", "Puri"],
    "Assam": ["Guwahati", "Dibrugarh", "Silchar", "Jorhat"],
    "Uttarakhand": ["Dehradun", "Haridwar", "Haldwani", "Nainital"],
    "Himachal Pradesh": ["Shimla", "Manali", "Dharamshala", "Mandi"],
    "Kerala": ["Kochi", "Thiruvananthapuram", "Kozhikode", "Thrissur"]
  };

  const DEFAULT_PLANS = [
    { id: "p_cal_44", price: 44, days: 3, benefit: "CALENDAR_ONLY" },
    { id: "p_wea_66", price: 66, days: 6, benefit: "WEATHER_ONLY" },
    { id: "p_both_155", price: 155, days: 15, benefit: "BOTH" },
    { id: "p_both_300", price: 300, days: 72, benefit: "BOTH" }
  ];

  let currentUserPhone = null;
  let selectedPlan = null;
  let activeOrderId = null;
  let calDate = new Date();

  window.onload = function () {
    populateStates();
    renderCalendar();
    ensureDefaultPlans();
    const savedPhone = localStorage.getItem("logged_in_phone");
    if (savedPhone && /^\d{10}$/.test(savedPhone)) {
      activateSession(savedPhone);
    }
  };

  function switchMainTab(tabName) {
    document.getElementById("tabCalendar").classList.toggle("active", tabName === "calendar");
    document.getElementById("tabWeather").classList.toggle("active", tabName === "weather");
    document.getElementById("tabRecharge").classList.toggle("active", tabName === "recharge");
    document.getElementById("tabMyPlan").classList.toggle("active", tabName === "myplan");

    document.getElementById("viewCalendar").classList.toggle("active-view", tabName === "calendar");
    document.getElementById("viewWeather").classList.toggle("active-view", tabName === "weather");
    document.getElementById("viewRecharge").classList.toggle("active-view", tabName === "recharge");
    document.getElementById("viewMyPlan").classList.toggle("active-view", tabName === "myplan");

    if (tabName === "myplan") renderMyPlanTab();
  }

  function loginWithPhone() {
    const ph = document.getElementById("loginPhoneInput").value.replace(/\D/g, "");
    if (!/^\d{10}$/.test(ph)) {
      alert("Kripya 10-digit sahi phone number daalein.");
      return;
    }
    localStorage.setItem("logged_in_phone", ph);
    activateSession(ph);
  }

  function logoutUser() {
    localStorage.removeItem("logged_in_phone");
    currentUserPhone = null;
    document.getElementById("mainApp").style.display = "none";
    document.getElementById("loginScreen").style.display = "block";
    document.getElementById("loginPhoneInput").value = "";
  }

  function activateSession(phone) {
    currentUserPhone = phone;
    const isVip = phone === VIP_PHONE;
    document.getElementById("loginScreen").style.display = "none";
    document.getElementById("mainApp").style.display = "block";

    const badge = document.getElementById("userBadge");
    badge.innerText = isVip ? `VIP ${phone}` : phone;
    badge.className = isVip ? "user-pill vip-pill" : "user-pill";

    document.getElementById("vipPlanManager").style.display = isVip ? "block" : "none";
    document.getElementById("vipFreeOfferCreator").style.display = isVip ? "block" : "none";

    restoreValid4HrPendingOrder();
    renderPlans();
    refreshModuleLocks();
    renderMyPlanTab();
    switchMainTab("calendar");
  }

  function isVipUser() {
    return currentUserPhone === VIP_PHONE;
  }

  function getUserPassStorageKey() {
    return `user_access_${currentUserPhone || "guest"}`;
  }

  function getPending4HrKey() {
    return `pending_4hr_order_${currentUserPhone || "guest"}`;
  }

  function savePending4HrOrder(orderId, plan) {
    const payload = {
      orderId,
      plan,
      createdAt: Date.now(),
      expiresAt: Date.now() + FOUR_HOURS_MS
    };
    localStorage.setItem(getPending4HrKey(), JSON.stringify(payload));
    renderPending4HrBanner(payload);
  }

  function restoreValid4HrPendingOrder() {
    const raw = localStorage.getItem(getPending4HrKey());
    if (!raw) {
      renderPending4HrBanner(null);
      return;
    }
    const data = JSON.parse(raw);
    if (!data.expiresAt || Date.now() > data.expiresAt) {
      localStorage.removeItem(getPending4HrKey());
      renderPending4HrBanner(null);
      return;
    }
    activeOrderId = data.orderId;
    selectedPlan = data.plan;
    renderPending4HrBanner(data);
  }

  function renderPending4HrBanner(data) {
    const banner = document.getElementById("pending4hrBanner");
    if (!data || isVipUser()) {
      banner.style.display = "none";
      return;
    }
    const hrsLeft = Math.max(0.1, ((data.expiresAt - Date.now()) / (60 * 60 * 1000))).toFixed(1);
    document.getElementById("pending4hrText").innerText = `${data.orderId} (₹${data.plan.price}) • ${hrsLeft} hr baaki`;
    banner.style.display = "block";
  }

  function reopenPending4hrOrder() {
    const raw = localStorage.getItem(getPending4HrKey());
    if (!raw) return;
    const data = JSON.parse(raw);
    if (Date.now() > data.expiresAt) {
      localStorage.removeItem(getPending4HrKey());
      renderPending4HrBanner(null);
      return;
    }
    activeOrderId = data.orderId;
    selectedPlan = data.plan;
    openFullScreenQr();
    revealScreenshotInsideQr();
  }

  /* VIP-Only Global Free Offer */
  function getActiveGlobalFreeOffer() {
    const raw = localStorage.getItem("global_vip_free_offer_v1");
    if (!raw) return null;
    const offer = JSON.parse(raw);
    if (!offer.expiresAt || Date.now() > offer.expiresAt) {
      localStorage.removeItem("global_vip_free_offer_v1");
      return null;
    }
    return offer;
  }

  function publishGlobalFreeOffer() {
    if (!isVipUser()) return;
    const name = document.getElementById("freeOfferNameInput").value.trim();
    const days = parseInt(document.getElementById("freeOfferDaysInput").value, 10);
    const scope = document.getElementById("freeOfferScopeInput").value;
    if (!name || !days || days <= 0) {
      alert("Offer ka naam aur din (days) daalein.");
      return;
    }
    const offer = {
      id: "free_" + Date.now(),
      name,
      days,
      scope,
      expiresAt: Date.now() + days * 24 * 60 * 60 * 1000
    };
    localStorage.setItem("global_vip_free_offer_v1", JSON.stringify(offer));
    document.getElementById("freeOfferNameInput").value = "";
    document.getElementById("freeOfferDaysInput").value = "";
    refreshModuleLocks();
    renderMyPlanTab();
    alert(`Sabhi user ke liye "${name}" (${days} din) free offer lag gaya hai.`);
  }

  function removeGlobalFreeOfferByVip() {
    if (!isVipUser()) return;
    localStorage.removeItem("global_vip_free_offer_v1");
    refreshModuleLocks();
    renderMyPlanTab();
    alert("Free offer sabhi user se hata diya gaya hai.");
  }

  function getEffectiveAccess() {
    const now = Date.now();
    if (isVipUser()) {
      return { calActive: true, weaActive: true, calUntil: null, weaUntil: null, source: "VIP (9120468311)" };
    }
    const raw = localStorage.getItem(getUserPassStorageKey());
    const userAcc = raw ? JSON.parse(raw) : { calendarUntil: 0, weatherUntil: 0, lastPlanPrice: null };
    let calUntil = userAcc.calendarUntil || 0;
    let weaUntil = userAcc.weatherUntil || 0;
    let sources = [];

    if (calUntil > now || weaUntil > now) sources.push("Paid Recharge");

    const freeOffer = getActiveGlobalFreeOffer();
    if (freeOffer) {
      if (freeOffer.scope === "CALENDAR_ONLY" || freeOffer.scope === "BOTH") {
        calUntil = Math.max(calUntil, freeOffer.expiresAt);
      }
      if (freeOffer.scope === "WEATHER_ONLY" || freeOffer.scope === "BOTH") {
        weaUntil = Math.max(weaUntil, freeOffer.expiresAt);
      }
      sources.push(`Free Offer: ${freeOffer.name}`);
    }

    return {
      calActive: now < calUntil,
      weaActive: now < weaUntil,
      calUntil,
      weaUntil,
      lastPlanPrice: userAcc.lastPlanPrice,
      source: sources.join(" + ") || "None"
    };
  }

  function refreshModuleLocks() {
    const acc = getEffectiveAccess();
    const calLock = document.getElementById("calendarLock");
    const weaLock = document.getElementById("weatherLock");
    const calTag = document.getElementById("calExpiryTag");
    const weaTag = document.getElementById("weatherExpiryTag");

    calLock.classList.toggle("active", !acc.calActive);
    weaLock.classList.toggle("active", !acc.weaActive);

    if (isVipUser()) {
      calTag.innerText = "• VIP Free";
      weaTag.innerText = "• VIP Free";
      return;
    }

    calTag.innerText = acc.calActive ? `• Till ${new Date(acc.calUntil).toLocaleDateString()}` : "• Locked";
    weaTag.innerText = acc.weaActive ? `• Till ${new Date(acc.weaUntil).toLocaleDateString()}` : "• Locked";
  }

  /* My Plan Tab */
  function renderMyPlanTab() {
    const wrap = document.getElementById("myPlanContent");
    const now = Date.now();
    const acc = getEffectiveAccess();
    const freeOffer = getActiveGlobalFreeOffer();

    const vipOfferBar = document.getElementById("vipActiveFreeOfferPanel");
    if (isVipUser()) {
      if (freeOffer) {
        const dLeft = Math.max(1, Math.ceil((freeOffer.expiresAt - now) / (24 * 60 * 60 * 1000)));
        document.getElementById("vipActiveFreeOfferText").innerText =
          `Active Free Offer: "${freeOffer.name}" (${benefitText(freeOffer.scope)}) • ${dLeft} Din Baaki`;
        vipOfferBar.style.display = "block";
      } else {
        vipOfferBar.style.display = "none";
      }

      wrap.innerHTML = `
        <div class="myplan-card">
          <div class="myplan-top">
            <div class="myplan-num-row">
              <span>VIP Account ${currentUserPhone}</span>
              <span>VIP</span>
            </div>
            <div class="myplan-crown-line">👑 VIP Unlimited Access</div>
            <div class="myplan-big-days">Unlimited</div>
            <div class="myplan-sub-row"><span>All Features Active</span><span>Free Forever</span></div>
          </div>
          <div class="myplan-bottom">
            <div class="myplan-meta-grid">
              <div><div class="myplan-label">Plan</div><div class="myplan-val">₹0 (VIP)</div></div>
              <div style="text-align:right;"><div class="myplan-label">Status</div><div class="myplan-val">Active</div></div>
            </div>
            <div class="myplan-benefits-box">Open: Calendar + 7-Day Weather + Plan &amp; Free Offer Control</div>
          </div>
        </div>`;
      return;
    }

    const maxUntil = Math.max(acc.calUntil || 0, acc.weaUntil || 0);
    if (maxUntil <= now) {
      wrap.innerHTML = `<div class="muted">Abhi aapka koi active plan nahi hai. Recharge tab se plan chunein.</div>`;
      return;
    }

    const daysLeft = Math.max(1, Math.ceil((maxUntil - now) / (24 * 60 * 60 * 1000)));
    const featuresOpen = [];
    if (acc.calActive) featuresOpen.push("Calendar");
    if (acc.weaActive) featuresOpen.push("7-Day Weather");

    wrap.innerHTML = `
      <div class="myplan-card">
        <div class="myplan-top">
          <div class="myplan-num-row">
            <span>User ${currentUserPhone}</span>
            <span>Active</span>
          </div>
          <div class="myplan-crown-line">👑 ${featuresOpen.join(" + ") || "Unlocked"}</div>
          <div class="myplan-big-days">${daysLeft} Days</div>
          <div class="myplan-sub-row">
            <span>left of ${daysLeft} days</span>
            <span>Source: ${acc.source}</span>
          </div>
        </div>
        <div class="myplan-bottom">
          <div class="myplan-meta-grid">
            <div>
              <div class="myplan-label">Plan</div>
              <div class="myplan-val">${acc.lastPlanPrice ? "₹" + acc.lastPlanPrice : (freeOffer ? "Free Offer" : "Active")}</div>
            </div>
            <div style="text-align:right;">
              <div class="myplan-label">Expires on</div>
              <div class="myplan-val">${new Date(maxUntil).toLocaleDateString("en-IN", { day: "numeric", month: "short", year: "numeric" })}</div>
            </div>
          </div>
          <div class="myplan-benefits-box">Kya-Kya Mila Hai: ${featuresOpen.join(" &amp; ")}</div>
        </div>
      </div>`;
  }

  /* Calendar */
  function renderCalendar() {
    const y = calDate.getFullYear();
    const m = calDate.getMonth();
    const names = ["January","February","March","April","May","June","July","August","September","October","November","December"];
    document.getElementById("monthYear").innerText = `${names[m]} ${y}`;
    const first = new Date(y, m, 1).getDay();
    const total = new Date(y, m + 1, 0).getDate();
    const today = new Date();
    const grid = document.getElementById("daysGrid");
    grid.innerHTML = "";

    for (let i = 0; i < first; i++) {
      const e = document.createElement("div");
      e.className = "empty";
      grid.appendChild(e);
    }
    for (let d = 1; d <= total; d++) {
      const cell = document.createElement("div");
      cell.innerText = d;
      if (d === today.getDate() && m === today.getMonth() && y === today.getFullYear()) {
        cell.classList.add("today");
      }
      grid.appendChild(cell);
    }
  }

  function changeMonth(step) {
    calDate.setMonth(calDate.getMonth() + step);
    renderCalendar();
  }

  /* India State -> District/Town -> Real 7-Day Weather */
  function populateStates() {
    const sel = document.getElementById("stateSelect");
    Object.keys(INDIA_STATES_PLACES).sort().forEach(st => {
      const opt = document.createElement("option");
      opt.value = st;
      opt.innerText = st;
      sel.appendChild(opt);
    });
  }

  function onStateChange() {
    const st = document.getElementById("stateSelect").value;
    const container = document.getElementById("placesContainer");
    const chips = document.getElementById("placeChips");
    document.getElementById("weatherResult").innerHTML = "";

    if (!st) {
      container.style.display = "none";
      return;
    }

    container.style.display = "block";
    chips.innerHTML = "";
    INDIA_STATES_PLACES[st].forEach(place => {
      const btn = document.createElement("button");
      btn.className = "place-chip";
      btn.innerText = place;
      btn.onclick = () => fetch7DayWeather(`${place}, ${st}, India`, place, btn);
      chips.appendChild(btn);
    });
  }

  function searchCustomTown() {
    const st = document.getElementById("stateSelect").value;
    const q = document.getElementById("customTownInput").value.trim();
    if (!q) return;
    fetch7DayWeather(`${q}${st ? ", " + st : ""}, India`, q, null);
  }

  function weatherCodeLabel(code) {
    if (code === 0) return "☀️ Clear Sky";
    if ([1, 2, 3].includes(code)) return "⛅ Partly Cloudy";
    if ([45, 48].includes(code)) return "🌫️ Fog / Mist";
    if ([51, 53, 55, 61, 63, 65, 80, 81, 82].includes(code)) return "🌧️ Rain";
    if ([71, 73, 75].includes(code)) return "❄️ Snow";
    if ([95, 96, 99].includes(code)) return "⛈️ Thunderstorm";
    return "🌤️ Weather";
  }

  async function fetch7DayWeather(queryText, labelTitle, btnEl) {
    if (!getEffectiveAccess().weaActive) {
      alert("Weather dekhne ke liye Weather ya Both wala plan/offer activate karein.");
      return;
    }

    document.querySelectorAll(".place-chip").forEach(c => c.classList.remove("active"));
    if (btnEl) btnEl.classList.add("active");

    const out = document.getElementById("weatherResult");
    out.innerHTML = `<div class="muted">🔍 ${labelTitle} ka 7-day weather load ho raha hai...</div>`;

    try {
      const geoRes = await fetch(`https://geocoding-api.open-meteo.com/v1/search?name=${encodeURIComponent(queryText)}&count=1&language=en&format=json`);
      const geoData = await geoRes.json();
      if (!geoData.results || !geoData.results.length) {
        out.innerHTML = `<div class="fail">Jila / kasba nahi mila. Dusra naam try karein.</div>`;
        return;
      }

      const loc = geoData.results[0];
      const wxRes = await fetch(
        `https://api.open-meteo.com/v1/forecast?latitude=${loc.latitude}&longitude=${loc.longitude}&daily=weather_code,temperature_2m_max,temperature_2m_min,precipitation_probability_max&timezone=auto&forecast_days=7`
      );
      const wxData = await wxRes.json();
      const daily = wxData.daily;

      let html = `<div style="font-size:13px;font-weight:700;color:#38bdf8;margin-bottom:4px;">📍 ${loc.name}${loc.admin1 ? ", " + loc.admin1 : ""} — Next 7 Days</div>`;
      for (let i = 0; i < daily.time.length; i++) {
        const dt = new Date(daily.time[i]).toLocaleDateString("en-IN", { weekday: "short", day: "numeric", month: "short" });
        const cond = weatherCodeLabel(daily.weather_code[i]);
        const hi = Math.round(daily.temperature_2m_max[i]);
        const lo = Math.round(daily.temperature_2m_min[i]);
        const rain = daily.precipitation_probability_max?.[i] ?? 0;
        html += `
          <div class="forecast-item">
            <div><b>${dt}</b> • <span style="color:#cbd5e1;">${cond}</span><div style="font-size:11px;color:#94a3b8;">Rain chance: ${rain}%</div></div>
            <div class="temp-badge">${hi}°C / ${lo}°C</div>
          </div>`;
      }
      out.innerHTML = html;
    } catch (err) {
      out.innerHTML = `<div class="fail">Weather data fetch nahi ho paaya. Internet check karein.</div>`;
    }
  }

  /* Plans & VIP CRUD */
  function ensureDefaultPlans() {
    if (!localStorage.getItem("custom_plans_v2")) {
      localStorage.setItem("custom_plans_v2", JSON.stringify(DEFAULT_PLANS));
    }
  }

  function getPlans() {
    return JSON.parse(localStorage.getItem("custom_plans_v2") || "[]");
  }

  function benefitText(code) {
    if (code === "CALENDAR_ONLY") return "Keval Calendar Open";
    if (code === "WEATHER_ONLY") return "Keval Weather Open";
    return "Dono Open (Calendar + Weather)";
  }

  function renderPlans() {
    const plans = getPlans();
    const list = document.getElementById("plansList");
    list.innerHTML = "";
    const vip = isVipUser();

    if (!plans.length) {
      list.innerHTML = `<div class="muted">Abhi koi plan nahi hai.</div>`;
      return;
    }

    plans.forEach(p => {
      const card = document.createElement("div");
      card.className = "plan-card-row";
      card.innerHTML = `
        <div class="plan-tag-pill">${benefitText(p.benefit)}</div>
        <div class="plan-main-line">
          <div class="plan-price-big">₹${p.price}</div>
          <div class="plan-meta-col">
            <div>Validity: <b>${p.days} Days</b></div>
            <div>Access: <b>${benefitText(p.benefit)}</b></div>
          </div>
          <button class="buy-pill-btn" onclick="onBuyPlanClick('${p.id}')">Buy</button>
        </div>
        <div class="plan-sub-footer">
          <span>Prepaid Pack</span>
          <span>${p.days} Days Validity</span>
        </div>
        ${vip ? `
          <div class="vip-card-controls">
            <button class="btn-sm btn-edit" onclick="editVipPlan('${p.id}')">Edit</button>
            <button class="btn-sm btn-del" onclick="deleteVipPlan('${p.id}')">Delete</button>
          </div>` : ""}
      `;
      list.appendChild(card);
    });
  }

  function saveVipPlan() {
    const price = parseInt(document.getElementById("planPriceInput").value, 10);
    const days = parseInt(document.getElementById("planValidityInput").value, 10);
    const benefit = document.getElementById("planBenefitInput").value;
    const editId = document.getElementById("editPlanId").value;

    if (!price || price <= 0 || !days || days <= 0) {
      alert("Sahi Price aur Validity (days) daalein.");
      return;
    }

    let plans = getPlans();
    if (editId) {
      plans = plans.map(p => p.id === editId ? { ...p, price, days, benefit } : p);
    } else {
      plans.push({ id: "plan_" + Date.now(), price, days, benefit });
    }
    localStorage.setItem("custom_plans_v2", JSON.stringify(plans));
    resetPlanForm();
    renderPlans();
  }

  function editVipPlan(id) {
    const p = getPlans().find(x => x.id === id);
    if (!p) return;
    document.getElementById("editPlanId").value = p.id;
    document.getElementById("planPriceInput").value = p.price;
    document.getElementById("planValidityInput").value = p.days;
    document.getElementById("planBenefitInput").value = p.benefit;
    document.getElementById("savePlanBtn").innerText = "Plan Update Karein";
    document.getElementById("cancelEditBtn").style.display = "block";
  }

  function deleteVipPlan(id) {
    const plans = getPlans().filter(x => x.id !== id);
    localStorage.setItem("custom_plans_v2", JSON.stringify(plans));
    if (selectedPlan && selectedPlan.id === id) selectedPlan = null;
    if (document.getElementById("editPlanId").value === id) resetPlanForm();
    renderPlans();
  }

  function resetPlanForm() {
    document.getElementById("editPlanId").value = "";
    document.getElementById("planPriceInput").value = "";
    document.getElementById("planValidityInput").value = "";
    document.getElementById("planBenefitInput").value = "BOTH";
    document.getElementById("savePlanBtn").innerText = "Plan Create Karein";
    document.getElementById("cancelEditBtn").style.display = "none";
  }

  /* Buy -> Details -> Pay -> 4-Hr Screenshot Verify */
  function createOrderId(amount) {
    const rnd = Math.floor(10000 + Math.random() * 90000);
    const t = Date.now().toString().slice(-4);
    return `CALRECHARGE-${amount}-${t}${rnd}`;
  }

  function onBuyPlanClick(planId) {
    const plan = getPlans().find(p => p.id === planId);
    if (!plan) return;
    selectedPlan = plan;
    activeOrderId = createOrderId(plan.price);

    const issued = JSON.parse(localStorage.getItem("issued_order_ids_v3") || "[]");
    issued.push(activeOrderId);
    localStorage.setItem("issued_order_ids_v3", JSON.stringify(issued));

    savePending4HrOrder(activeOrderId, plan);

    document.getElementById("dsPrice").innerText = `₹${plan.price}`;
    document.getElementById("dsBadge").innerText = benefitText(plan.benefit).toUpperCase();
    document.getElementById("dsValidity").innerText = `${plan.days} Days`;
    document.getElementById("dsBenefit").innerText = benefitText(plan.benefit);
    document.getElementById("dsOrderId").innerText = activeOrderId;
    document.getElementById("dsPayBtn").innerText = `Pay ₹${plan.price}.00`;

    document.getElementById("plansListSection").style.display = "none";
    document.getElementById("planDetailsSheet").classList.add("active");
    document.getElementById("payMethodsBox").classList.remove("active");
  }

  function backToPlansList() {
    document.getElementById("planDetailsSheet").classList.remove("active");
    document.getElementById("payMethodsBox").classList.remove("active");
    document.getElementById("plansListSection").style.display = "block";
  }

  function openPayMethodsAfterPayClick() {
    if (!selectedPlan || !activeOrderId) return;
    document.getElementById("appNotInstalledAlert").style.display = "none";
    document.getElementById("payMethodsBox").classList.add("active");
    document.getElementById("payMethodsBox").scrollIntoView({ behavior: "smooth", block: "nearest" });
  }

  function buildUpiQuery(orderId) {
    const amt = Number(selectedPlan.price).toFixed(2);
    return `pa=${encodeURIComponent(UPI_ID)}&pn=${encodeURIComponent(PAYEE_NAME)}&am=${amt}&mam=${amt}&tr=${encodeURIComponent(orderId)}&cu=INR&tn=${encodeURIComponent(orderId)}`;
  }

  function getSpecificUpiUrl(appKey, orderId) {
    const q = buildUpiQuery(orderId);
    if (appKey === "phonepe") return `phonepe://pay?${q}`;
    if (appKey === "paytm") return `paytmmp://pay?${q}`;
    if (appKey === "navi") return `navi://upi/pay?${q}`;
    if (appKey === "gpay") return `tez://upi/pay?${q}`;
    return `upi://pay?${q}`;
  }

  function openUpiAppDirect(appKey) {
    if (!selectedPlan || !activeOrderId) return;
    savePending4HrOrder(activeOrderId, selectedPlan);

    const alertBox = document.getElementById("appNotInstalledAlert");
    alertBox.style.display = "none";

    const isMobile = /Android|iPhone|iPad|iPod/i.test(navigator.userAgent);
    if (!isMobile) {
      alertBox.style.display = "block";
      return;
    }

    let leftPage = false;
    const onVis = () => { if (document.hidden) leftPage = true; };
    document.addEventListener("visibilitychange", onVis);

    window.location.href = getSpecificUpiUrl(appKey, activeOrderId);

    setTimeout(() => {
      document.removeEventListener("visibilitychange", onVis);
      if (!leftPage && !document.hidden) {
        alertBox.style.display = "block";
      }
    }, 1700);
  }

  function openFullScreenQr() {
    if (!selectedPlan || !activeOrderId) return;
    savePending4HrOrder(activeOrderId, selectedPlan);
    const intent = `upi://pay?${buildUpiQuery(activeOrderId)}`;
    document.getElementById("fqrAmount").innerText = `₹${selectedPlan.price}`;
    document.getElementById("fqrOrder").innerText = activeOrderId;
    document.getElementById("fqrImg").src = `https://api.qrserver.com/v1/create-qr-code/?size=420x420&margin=12&data=${encodeURIComponent(intent)}`;
    document.getElementById("qrScreenshotBox").classList.remove("active");
    document.getElementById("ssReport").style.display = "none";
    document.getElementById("fullQrModal").classList.add("active");
  }

  function closeFullScreenQr() {
    document.getElementById("fullQrModal").classList.remove("active");
  }

  async function shareFullQr() {
    if (!selectedPlan || !activeOrderId) return;
    const text = `Pay ₹${selectedPlan.price} to ${UPI_ID} (${MERCHANT_PHONE}) | Order: ${activeOrderId}`;
    if (navigator.share) {
      try { await navigator.share({ title: "Payment QR", text }); return; } catch (e) {}
    }
    navigator.clipboard?.writeText(text).catch(() => {});
    alert("Payment details copy ho gayi hain.");
  }

  function revealScreenshotInsideQr() {
    document.getElementById("qrSsOrderLabel").innerText = activeOrderId || "-";
    const box = document.getElementById("qrScreenshotBox");
    box.classList.add("active");
    box.scrollIntoView({ behavior: "smooth", block: "nearest" });
  }

  function onScreenshotChange(ev) {
    const f = ev.target.files[0];
    const img = document.getElementById("ssPreview");
    const btn = document.getElementById("verifySsBtn");
    if (!f) {
      img.style.display = "none";
      btn.disabled = true;
      return;
    }
    const r = new FileReader();
    r.onload = e => {
      img.src = e.target.result;
      img.style.display = "block";
      btn.disabled = false;
    };
    r.readAsDataURL(f);
  }

  async function getImageHash(file) {
    const dataUrl = await new Promise((res, rej) => {
      const r = new FileReader();
      r.onload = () => res(r.result);
      r.onerror = rej;
      r.readAsDataURL(file);
    });
    const img = await new Promise((res, rej) => {
      const i = new Image();
      i.onload = () => res(i);
      i.onerror = rej;
      i.src = dataUrl;
    });
    const c = document.createElement("canvas");
    c.width = 16; c.height = 16;
    const ctx = c.getContext("2d");
    ctx.drawImage(img, 0, 0, 16, 16);
    const d = ctx.getImageData(0, 0, 16, 16).data;
    let bits = "";
    for (let i = 0; i < d.length; i += 4) {
      bits += ((d[i] + d[i+1] + d[i+2]) / 3) > 128 ? "1" : "0";
    }
    return `${file.size}_${bits}`;
  }

  function showFinalStatus(isSuccess) {
    closeFullScreenQr();
    const msgBox = document.getElementById("messageBox");
    if (isSuccess) {
      msgBox.className = "message-box msg-success";
      msgBox.innerText = "Recharge successfull";
    } else {
      msgBox.className = "message-box msg-failed";
      msgBox.innerText = "Recharge faild";
    }
    msgBox.scrollIntoView({ behavior: "smooth", block: "nearest" });
  }

  async function verifyScreenshotAndUnlock() {
    restoreValid4HrPendingOrder();
    const file = document.getElementById("ssInput").files[0];
    const report = document.getElementById("ssReport");
    const btn = document.getElementById("verifySsBtn");

    if (!file || !selectedPlan || !activeOrderId) {
      showFinalStatus(false);
      return;
    }

    btn.disabled = true;
    btn.innerText = "Checking Screenshot...";

    const issued = JSON.parse(localStorage.getItem("issued_order_ids_v3") || "[]");
    const usedOrders = JSON.parse(localStorage.getItem("used_order_ids_v3") || "[]");
    const usedHashes = JSON.parse(localStorage.getItem("used_ss_hashes_v3") || "[]");

    try {
      const hash = await getImageHash(file);
      const uniqueImg = !usedHashes.includes(hash);

      const ocr = await Tesseract.recognize(file, "eng");
      const raw = (ocr.data.text || "").toUpperCase();
      const clean = raw.replace(/[^A-Z0-9@.]/g, "");

      const receiverOk = clean.includes("Q955492157@YBL") || clean.includes("Q955492157") || clean.includes(MERCHANT_PHONE);
      const statusOk = /(PAID|SUCCESS|SUCCESSFUL|COMPLETED|SENT|DONE)/.test(raw);
      const amtRegex = new RegExp(`(?:₹|RS\\.?|INR)?\\s*${selectedPlan.price}(?:\\.00)?\\b`);
      const amountOk = amtRegex.test(raw) || clean.includes(String(selectedPlan.price));
      const orderOk = issued.includes(activeOrderId) && !usedOrders.includes(activeOrderId) &&
        (clean.includes(activeOrderId.replace(/[^A-Z0-9]/g, "")) || clean.includes("CALRECHARGE"));

      report.style.display = "block";
      report.innerHTML = [
        { ok: uniqueImg, label: "Duplicate Screenshot Nahi Hai" },
        { ok: receiverOk, label: "Receiver Sahi Hai (Q955492157@ybl / 9696898751)" },
        { ok: statusOk && amountOk, label: `Payment Success & Amount ₹${selectedPlan.price}` },
        { ok: orderOk, label: `Recharge Purpose & 4-Hr Order (${activeOrderId})` }
      ].map(x => `<div class="${x.ok ? 'pass' : 'fail'}">${x.ok ? '✅' : '❌'} ${x.label}</div>`).join("");

      if (uniqueImg && receiverOk && statusOk && amountOk && orderOk) {
        usedHashes.push(hash);
        usedOrders.push(activeOrderId);
        localStorage.setItem("used_ss_hashes_v3", JSON.stringify(usedHashes));
        localStorage.setItem("used_order_ids_v3", JSON.stringify(usedOrders));

        const key = getUserPassStorageKey();
        const cur = localStorage.getItem(key) ? JSON.parse(localStorage.getItem(key)) : { calendarUntil: 0, weatherUntil: 0 };
        const addMs = selectedPlan.days * 24 * 60 * 60 * 1000;
        const now = Date.now();

        if (selectedPlan.benefit === "CALENDAR_ONLY" || selectedPlan.benefit === "BOTH") {
          cur.calendarUntil = Math.max(cur.calendarUntil || 0, now) + addMs;
        }
        if (selectedPlan.benefit === "WEATHER_ONLY" || selectedPlan.benefit === "BOTH") {
          cur.weatherUntil = Math.max(cur.weatherUntil || 0, now) + addMs;
        }
        cur.lastPlanPrice = selectedPlan.price;
        localStorage.setItem(key, JSON.stringify(cur));

        localStorage.removeItem(getPending4HrKey());
        renderPending4HrBanner(null);
        activeOrderId = null;

        refreshModuleLocks();
        renderMyPlanTab();
        backToPlansList();
        showFinalStatus(true);
      } else {
        showFinalStatus(false);
      }
    } catch (e) {
      showFinalStatus(false);
    }

    btn.disabled = false;
    btn.innerText = "Verify Screenshot & Unlock";
  }
</script>

</body>
</html>











