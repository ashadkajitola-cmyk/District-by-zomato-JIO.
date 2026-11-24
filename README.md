<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>UPI Unlock Calendar & VIP Portal</title>
  <script src="https://cdn.jsdelivr.net/npm/tesseract.js@5/dist/tesseract.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/jsqr@1.4.0/dist/jsQR.min.js"></script>
  <style>
    :root {
      --bg: #0f172a;
      --card: #1e293b;
      --card-2: #0b1220;
      --text: #f8fafc;
      --muted: #94a3b8;
      --accent: #22c55e;
      --accent-dark: #16a34a;
      --primary: #3b82f6;
      --danger: #ef4444;
      --vip: #f59e0b;
      --border: rgba(255, 255, 255, 0.1);
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
    }

    body {
      background: linear-gradient(135deg, #0b1120 0%, #111827 50%, #0f172a 100%);
      color: var(--text);
      min-height: 100vh;
      padding: 20px;
    }

    .container {
      max-width: 980px;
      margin: 0 auto;
    }

    .topbar {
      display: flex;
      flex-wrap: wrap;
      justify-content: space-between;
      align-items: center;
      gap: 12px;
      background: var(--card);
      border: 1px solid var(--border);
      padding: 16px 20px;
      border-radius: 16px;
      margin-bottom: 20px;
    }

    .brand {
      display: flex;
      align-items: center;
      gap: 10px;
      font-size: 1.2rem;
      font-weight: 700;
    }

    .top-right {
      display: flex;
      flex-wrap: wrap;
      align-items: center;
      gap: 10px;
    }

    .badge {
      font-size: 0.8rem;
      padding: 6px 12px;
      border-radius: 999px;
      font-weight: 600;
      background: rgba(59, 130, 246, 0.15);
      color: #93c5fd;
      border: 1px solid rgba(59, 130, 246, 0.3);
    }

    .badge.active {
      background: rgba(34, 197, 94, 0.15);
      color: #86efac;
      border-color: rgba(34, 197, 94, 0.35);
    }

    .badge.vip {
      background: rgba(245, 158, 11, 0.18);
      color: #fcd34d;
      border-color: rgba(245, 158, 11, 0.45);
    }

    .card {
      background: var(--card);
      border: 1px solid var(--border);
      border-radius: 18px;
      padding: 22px;
      margin-bottom: 20px;
      box-shadow: 0 12px 30px rgba(0, 0, 0, 0.25);
    }

    .card.vip-border {
      border-color: rgba(245, 158, 11, 0.45);
    }

    h2 {
      font-size: 1.25rem;
      margin-bottom: 8px;
    }

    h3 {
      font-size: 1.05rem;
    }

    p.sub {
      color: var(--muted);
      font-size: 0.92rem;
      margin-bottom: 18px;
      line-height: 1.45;
    }

    input[type="text"],
    input[type="tel"],
    input[type="number"] {
      width: 100%;
      padding: 11px 13px;
      border-radius: 10px;
      border: 1px solid var(--border);
      background: var(--card-2);
      color: #fff;
      font-size: 0.95rem;
    }

    input:focus {
      outline: none;
      border-color: var(--primary);
    }

    .login-row {
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
      max-width: 460px;
    }

    .login-row input {
      flex: 1 1 240px;
    }

    .plans {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
      gap: 14px;
      margin-bottom: 18px;
    }

    .plan {
      background: var(--card-2);
      border: 2px solid var(--border);
      border-radius: 14px;
      padding: 16px;
      cursor: pointer;
      transition: all 0.2s ease;
    }

    .plan:hover {
      border-color: rgba(59, 130, 246, 0.6);
      transform: translateY(-2px);
    }

    .plan.selected {
      border-color: var(--accent);
      background: rgba(34, 197, 94, 0.08);
    }

    .plan-title {
      font-size: 0.9rem;
      color: var(--muted);
      margin-bottom: 6px;
    }

    .plan-price {
      font-size: 1.7rem;
      font-weight: 800;
      color: #fff;
      margin-bottom: 4px;
    }

    .plan-days {
      font-size: 0.9rem;
      color: #86efac;
      font-weight: 600;
    }

    .btn {
      display: inline-flex;
      align-items: center;
      justify-content: center;
      gap: 8px;
      border: none;
      border-radius: 10px;
      padding: 11px 16px;
      font-size: 0.92rem;
      font-weight: 700;
      cursor: pointer;
      transition: all 0.2s ease;
      text-decoration: none;
    }

    .btn:disabled {
      opacity: 0.5;
      cursor: not-allowed;
    }

    .btn-primary {
      background: var(--primary);
      color: #fff;
    }

    .btn-primary:hover:not(:disabled) {
      background: #2563eb;
    }

    .btn-success {
      background: var(--accent);
      color: #052e16;
    }

    .btn-success:hover:not(:disabled) {
      background: var(--accent-dark);
      color: #fff;
    }

    .btn-vip {
      background: var(--vip);
      color: #1c1917;
    }

    .btn-vip:hover:not(:disabled) {
      background: #d97706;
      color: #fff;
    }

    .btn-danger {
      background: rgba(239, 68, 68, 0.18);
      color: #fca5a5;
      border: 1px solid rgba(239, 68, 68, 0.4);
    }

    .btn-danger:hover:not(:disabled) {
      background: var(--danger);
      color: #fff;
    }

    .btn-ghost {
      background: rgba(255, 255, 255, 0.07);
      color: var(--text);
      border: 1px solid var(--border);
    }

    .btn-sm {
      padding: 7px 12px;
      font-size: 0.82rem;
    }

    /* VIP Admin Sections */
    .vip-box {
      background: var(--card-2);
      border: 1px solid var(--border);
      border-radius: 14px;
      padding: 16px;
      margin-bottom: 16px;
    }

    .vip-qr-row {
      display: flex;
      flex-wrap: wrap;
      gap: 18px;
      align-items: center;
      margin-top: 12px;
    }

    .vip-qr-preview {
      width: 140px;
      height: 140px;
      background: #fff;
      border-radius: 12px;
      padding: 8px;
      display: flex;
      align-items: center;
      justify-content: center;
      color: #475569;
      font-size: 0.8rem;
      text-align: center;
      overflow: hidden;
    }

    .vip-qr-preview img {
      width: 100%;
      height: 100%;
      object-fit: contain;
    }

    .vip-form-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
      gap: 10px;
      margin-top: 12px;
      margin-bottom: 12px;
    }

    .vip-plan-list {
      display: flex;
      flex-direction: column;
      gap: 10px;
      margin-top: 14px;
    }

    .vip-plan-item {
      display: flex;
      flex-wrap: wrap;
      justify-content: space-between;
      align-items: center;
      gap: 10px;
      background: rgba(15, 23, 42, 0.8);
      border: 1px solid var(--border);
      padding: 12px 14px;
      border-radius: 12px;
    }

    .vip-actions {
      display: flex;
      gap: 8px;
    }

    .payment-box {
      display: none;
      margin-top: 20px;
      padding-top: 20px;
      border-top: 1px dashed var(--border);
    }

    .payment-box.show {
      display: block;
    }

    .qr-area {
      display: none;
      margin-top: 18px;
      background: var(--card-2);
      border: 1px solid var(--border);
      border-radius: 14px;
      padding: 18px;
    }

    .qr-area.show {
      display: block;
    }

    .qr-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
      gap: 20px;
      align-items: center;
    }

    .qr-frame {
      background: #fff;
      color: #0f172a;
      padding: 14px;
      border-radius: 12px;
      text-align: center;
      max-width: 260px;
      margin: 0 auto;
    }

    .qr-frame img {
      width: 210px;
      height: 210px;
      object-fit: contain;
      display: block;
      margin: 0 auto 8px;
    }

    .amount-pill {
      display: inline-block;
      background: #16a34a;
      color: #fff;
      font-weight: 800;
      font-size: 1rem;
      padding: 5px 14px;
      border-radius: 999px;
      margin-bottom: 4px;
    }

    .upload-box {
      margin-top: 14px;
      padding: 16px;
      border-radius: 12px;
      border: 1px dashed rgba(148, 163, 184, 0.45);
      background: rgba(15, 23, 42, 0.65);
    }

    .gallery-btn {
      display: inline-flex;
      align-items: center;
      gap: 8px;
      margin-top: 10px;
      padding: 10px 14px;
      border-radius: 10px;
      background: rgba(59, 130, 246, 0.16);
      border: 1px solid rgba(59, 130, 246, 0.45);
      color: #bfdbfe;
      font-weight: 600;
      font-size: 0.9rem;
      cursor: pointer;
    }

    .gallery-btn:hover {
      background: rgba(59, 130, 246, 0.26);
    }

    .file-chosen {
      margin-top: 8px;
      margin-bottom: 12px;
      font-size: 0.85rem;
      color: #cbd5e1;
      word-break: break-all;
    }

    .status-msg {
      margin-top: 12px;
      padding: 12px 14px;
      border-radius: 10px;
      font-size: 0.9rem;
      line-height: 1.45;
      display: none;
    }

    .status-msg.show {
      display: block;
    }

    .status-msg.info {
      background: rgba(59, 130, 246, 0.14);
      border: 1px solid rgba(59, 130, 246, 0.35);
      color: #bfdbfe;
    }

    .status-msg.error {
      background: rgba(239, 68, 68, 0.14);
      border: 1px solid rgba(239, 68, 68, 0.35);
      color: #fecaca;
    }

    .status-msg.success {
      background: rgba(34, 197, 94, 0.14);
      border: 1px solid rgba(34, 197, 94, 0.35);
      color: #bbf7d0;
    }

    .hidden {
      display: none !important;
    }

    .cal-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 16px;
      gap: 10px;
    }

    .cal-grid {
      display: grid;
      grid-template-columns: repeat(7, 1fr);
      gap: 8px;
    }

    .cal-day-name {
      text-align: center;
      font-size: 0.8rem;
      font-weight: 700;
      color: var(--muted);
      padding: 6px 0;
    }

    .cal-cell {
      min-height: 78px;
      background: var(--card-2);
      border: 1px solid var(--border);
      border-radius: 10px;
      padding: 8px;
      cursor: pointer;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      transition: border-color 0.15s ease;
    }

    .cal-cell:hover:not(.empty) {
      border-color: var(--primary);
    }

    .cal-cell.empty {
      background: transparent;
      border-color: transparent;
      cursor: default;
    }

    .cal-cell.today {
      border-color: var(--accent);
      box-shadow: inset 0 0 0 1px var(--accent);
    }

    .cal-date-num {
      font-weight: 700;
      font-size: 0.95rem;
    }

    .cal-note-dot {
      font-size: 0.72rem;
      color: #93c5fd;
      background: rgba(59, 130, 246, 0.16);
      padding: 2px 6px;
      border-radius: 6px;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }

    .note-box {
      margin-top: 16px;
      background: var(--card-2);
      border: 1px solid var(--border);
      border-radius: 12px;
      padding: 14px;
    }

    .note-box input {
      margin: 8px 0 10px;
    }

    .small-note {
      font-size: 0.78rem;
      color: var(--muted);
      margin-top: 10px;
      line-height: 1.4;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="topbar">
      <div class="brand">
        <span>📅 Smart Unlock Calendar</span>
      </div>
      <div class="top-right">
        <span id="userBadge" class="badge hidden"></span>
        <span id="accessBadge" class="badge">🔒 Login Required</span>
        <button id="logoutBtn" class="btn btn-ghost btn-sm hidden" type="button">Logout</button>
      </div>
    </div>

    <!-- LOGIN SECTION -->
    <div id="loginCard" class="card">
      <h2>📱 Mobile Number se Login Karein</h2>
      <p class="sub">
        Aage badhne ke liye apna 10-digit mobile number daalein.
      </p>
      <div class="login-row">
        <input type="tel" id="phoneInput" maxlength="10" placeholder="10-digit Mobile Number daalein..." />
        <button id="loginBtn" class="btn btn-primary" type="button">Login</button>
      </div>
      <div id="loginMsg" class="status-msg"></div>
    </div>

    <!-- VIP ADMIN PANEL (ONLY FOR 9569981484) -->
    <div id="vipAdminCard" class="card vip-border hidden">
      <h2>👑 VIP Admin Panel (9569981484)</h2>
      <p class="sub">
        Yahan se apna Payment QR Code gallery se upload karke save karein aur Recharge Plans manage karein.
      </p>

      <!-- 1. ADMIN QR UPLOAD & SAVE -->
      <div class="vip-box">
        <h3>1. Payment QR Code Set Karein (Gallery se Chunein)</h3>
        <p class="small-note" style="margin-top:4px;">
          Apna QR code gallery se chunein aur Save karein. Jab koi bhi user recharge karega, toh payment lene ke liye yahi QR dikhega aur plan ke paise apne aap set ho jayenge.
        </p>

        <div class="vip-qr-row">
          <div class="vip-qr-preview" id="adminQrPreviewBox">
            <span>Koi QR set nahi hai</span>
          </div>
          <div style="flex:1; min-width:220px;">
            <input type="file" id="adminQrFileInput" accept="image/*" style="display:none;" />
            <div style="display:flex; flex-wrap:wrap; gap:8px;">
              <button id="adminPickQrBtn" class="btn btn-primary btn-sm" type="button">🖼️ Gallery se QR Chunein</button>
              <button id="adminSaveQrBtn" class="btn btn-success btn-sm" type="button">💾 QR Save Karein</button>
              <button id="adminClearQrBtn" class="btn btn-danger btn-sm" type="button">Remove QR</button>
            </div>
            <div id="detectedUpiInfo" class="small-note" style="margin-top:8px;"></div>
            <div id="adminQrMsg" class="status-msg"></div>
          </div>
        </div>
      </div>

      <!-- 2. ADMIN RECHARGE PLANS MANAGER -->
      <div class="vip-box" style="margin-bottom:0;">
        <h3>2. Recharge Plans Banayein / Edit / Delete Karein</h3>
        <div class="vip-form-grid">
          <input type="text" id="vipPlanTitle" placeholder="Plan Name (e.g. 4th Recharge Plan)" />
          <input type="number" id="vipPlanAmount" min="1" placeholder="Amount ₹ (e.g. 149)" />
          <input type="number" id="vipPlanDays" min="1" placeholder="Validity Days (e.g. 20)" />
        </div>

        <div style="display:flex; gap:10px; flex-wrap:wrap;">
          <button id="vipSavePlanBtn" class="btn btn-vip" type="button">+ New Recharge Banayein</button>
          <button id="vipCancelEditBtn" class="btn btn-ghost hidden" type="button">Cancel Edit</button>
        </div>

        <div id="vipMsg" class="status-msg"></div>
        <div class="vip-plan-list" id="vipPlanList"></div>
      </div>
    </div>

    <!-- RECHARGE & UNLOCK PANEL -->
    <div id="rechargeCard" class="card hidden">
      <h2>1. Recharge Plan Chunein</h2>
      <p class="sub">
        Calendar unlock karne ke liye plan chunein, Proceed to Pay dabayein, QR scan karke payment karein, aur <b>3 ghante</b> ke andar <b>1 baar</b> screenshot upload karein.
      </p>

      <div class="plans" id="plansContainer"></div>

      <div id="planDetailsBox" class="payment-box">
        <h3>Selected Plan Details</h3>
        <p class="sub" id="selectedSummary" style="margin-top:6px; margin-bottom:14px;"></p>
        <button id="proceedPayBtn" class="btn btn-primary" type="button">Proceed to Pay</button>

        <div id="qrArea" class="qr-area">
          <div class="qr-grid">
            <div class="qr-frame">
              <img id="qrImage" alt="Payment QR Code" />
              <div class="amount-pill" id="qrAmountLabel">Pay ₹0</div>
              <div style="font-size:0.76rem; color:#334155; font-weight:600; margin-top:4px;" id="qrUpiLabel"></div>
              <a id="directUpiPayLink" class="btn btn-primary btn-sm hidden" style="margin-top:8px; width:100%;" href="#">
                UPI App se Direct Pay Karein
              </a>
            </div>

            <div>
              <h3>UPI Payment & Screenshot Upload</h3>
              <p class="sub" style="margin-top:6px; margin-bottom:8px;">
                1. Diye gaye QR ko scan karke exact <b id="instrAmountText">₹0</b> pay karein.<br />
                2. Payment screenshot lein (<b>3 hours</b> ke andar valid).<br />
                3. Gallery se screenshot chunein aur verify karein.
              </p>

              <div class="upload-box">
                <div style="font-weight:700;">Payment Screenshot</div>
                <input type="file" id="screenshotInput" accept="image/*" style="display:none;" />
                <button id="galleryPickBtn" class="gallery-btn" type="button">🖼️ Gallery se Screenshot Chunein</button>
                <div id="fileChosenText" class="file-chosen">Koi screenshot select nahi hua hai</div>
                <button id="verifyBtn" class="btn btn-success" type="button">Verify & Unlock Calendar</button>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div id="statusMsg" class="status-msg"></div>
      <div class="small-note">
        Rules: Ek screenshot sirf 1 baar valid hoga aur screenshot lene ke 3 ghante ke andar upload karna zaroori hai.
      </div>
    </div>

    <!-- UNLOCKED CALENDAR -->
    <div id="calendarSection" class="card hidden">
      <div class="cal-header">
        <div>
          <h2 id="monthYearTitle">Calendar</h2>
          <p class="sub" id="validityInfo" style="margin-bottom:0;"></p>
        </div>
        <div style="display:flex; gap:8px;">
          <button id="prevMonthBtn" class="btn btn-ghost" type="button">◀</button>
          <button id="nextMonthBtn" class="btn btn-ghost" type="button">▶</button>
        </div>
      </div>

      <div class="cal-grid" id="calendarGrid"></div>

      <div class="note-box">
        <div id="selectedDateLabel" style="font-weight:700;">Kisi date par click karke note likhein</div>
        <input type="text" id="noteInput" placeholder="Is din ka note ya reminder likhein..." />
        <button id="saveNoteBtn" class="btn btn-primary" type="button">Save Note</button>
      </div>
    </div>
  </div>

  <script>
    // =========================================================================
    // CONFIGURATION
    // =========================================================================
    const VIP_NUMBER = "9569981484";

    const DEFAULT_CONFIG = {
      MAX_SCREENSHOT_AGE_MS: 3 * 60 * 60 * 1000, // 3 Hours
      REQUIRE_UPI_IN_OCR: true
    };

    const INITIAL_PLANS = [
      { id: "plan1", title: "1st Recharge Plan", amount: 348, days: 27 },
      { id: "plan2", title: "2nd Recharge Plan", amount: 66, days: 15 },
      { id: "plan3", title: "3rd Recharge Plan", amount: 239, days: 24 }
    ];

    const STORAGE_KEYS = {
      CURRENT_USER: "uc_current_user_v4",
      PLANS: "uc_recharge_plans_v4",
      UNLOCK_MAP: "uc_unlock_by_phone_v4",
      USED_HASHES: "uc_used_hashes_v4",
      NOTES: "uc_calendar_notes_v4",
      ADMIN_QR_IMAGE: "uc_admin_qr_image_v4",
      ADMIN_UPI_ID: "uc_admin_upi_id_v4",
      ADMIN_PAYEE_NAME: "uc_admin_payee_name_v4"
    };

    let selectedPlan = null;
    let editingPlanId = null;
    let currentViewDate = new Date();
    let selectedDateStr = null;

    let draftAdminQrDataUrl = localStorage.getItem(STORAGE_KEYS.ADMIN_QR_IMAGE) || "";
    let draftDetectedUpi = localStorage.getItem(STORAGE_KEYS.ADMIN_UPI_ID) || "";
    let draftDetectedName = localStorage.getItem(STORAGE_KEYS.ADMIN_PAYEE_NAME) || "Merchant";

    // Elements
    const loginCard = document.getElementById("loginCard");
    const phoneInput = document.getElementById("phoneInput");
    const loginBtn = document.getElementById("loginBtn");
    const loginMsg = document.getElementById("loginMsg");
    const logoutBtn = document.getElementById("logoutBtn");
    const userBadge = document.getElementById("userBadge");
    const accessBadge = document.getElementById("accessBadge");

    const vipAdminCard = document.getElementById("vipAdminCard");
    const adminQrPreviewBox = document.getElementById("adminQrPreviewBox");
    const adminQrFileInput = document.getElementById("adminQrFileInput");
    const adminPickQrBtn = document.getElementById("adminPickQrBtn");
    const adminSaveQrBtn = document.getElementById("adminSaveQrBtn");
    const adminClearQrBtn = document.getElementById("adminClearQrBtn");
    const detectedUpiInfo = document.getElementById("detectedUpiInfo");
    const adminQrMsg = document.getElementById("adminQrMsg");

    const vipPlanTitle = document.getElementById("vipPlanTitle");
    const vipPlanAmount = document.getElementById("vipPlanAmount");
    const vipPlanDays = document.getElementById("vipPlanDays");
    const vipSavePlanBtn = document.getElementById("vipSavePlanBtn");
    const vipCancelEditBtn = document.getElementById("vipCancelEditBtn");
    const vipMsg = document.getElementById("vipMsg");
    const vipPlanList = document.getElementById("vipPlanList");

    const rechargeCard = document.getElementById("rechargeCard");
    const plansContainer = document.getElementById("plansContainer");
    const planDetailsBox = document.getElementById("planDetailsBox");
    const selectedSummary = document.getElementById("selectedSummary");
    const proceedPayBtn = document.getElementById("proceedPayBtn");
    const qrArea = document.getElementById("qrArea");
    const qrImage = document.getElementById("qrImage");
    const qrAmountLabel = document.getElementById("qrAmountLabel");
    const qrUpiLabel = document.getElementById("qrUpiLabel");
    const directUpiPayLink = document.getElementById("directUpiPayLink");
    const instrAmountText = document.getElementById("instrAmountText");

    const screenshotInput = document.getElementById("screenshotInput");
    const galleryPickBtn = document.getElementById("galleryPickBtn");
    const fileChosenText = document.getElementById("fileChosenText");
    const verifyBtn = document.getElementById("verifyBtn");

    const statusMsg = document.getElementById("statusMsg");
    const calendarSection = document.getElementById("calendarSection");
    const validityInfo = document.getElementById("validityInfo");

    function setBoxMsg(el, type, text) {
      if (!text) {
        el.className = "status-msg";
        el.innerHTML = "";
        return;
      }
      el.className = `status-msg show ${type}`;
      el.innerHTML = text;
    }

    function normalizePhone(raw) {
      const digits = String(raw || "").replace(/\D/g, "");
      if (digits.length === 12 && digits.startsWith("91")) {
        return digits.slice(2);
      }
      return digits;
    }

    function getCurrentUser() {
      return localStorage.getItem(STORAGE_KEYS.CURRENT_USER) || "";
    }

    function isVipUser(phone = getCurrentUser()) {
      return phone === VIP_NUMBER;
    }

    // Decode UPI ID & Payee Name automatically from Admin's QR Image
    function decodeUpiFromImage(dataUrl) {
      return new Promise((resolve) => {
        const img = new Image();
        img.onload = () => {
          try {
            const canvas = document.createElement("canvas");
            const maxDim = 800;
            const scale = Math.min(1, maxDim / Math.max(img.width, img.height));
            canvas.width = Math.round(img.width * scale);
            canvas.height = Math.round(img.height * scale);
            const ctx = canvas.getContext("2d");
            ctx.drawImage(img, 0, 0, canvas.width, canvas.height);
            const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);

            if (typeof jsQR === "function") {
              const code = jsQR(imageData.data, imageData.width, imageData.height);
              if (code && code.data && code.data.toLowerCase().startsWith("upi://")) {
                const urlObj = new URL(code.data);
                const pa = urlObj.searchParams.get("pa") || "";
                const pn = urlObj.searchParams.get("pn") || "Merchant";
                resolve({ upiId: pa, payeeName: pn });
                return;
              }
            }
          } catch {}
          resolve({ upiId: "", payeeName: "Merchant" });
        };
        img.onerror = () => resolve({ upiId: "", payeeName: "Merchant" });
        img.src = dataUrl;
      });
    }

    function renderAdminQrPreview() {
      const savedImg = localStorage.getItem(STORAGE_KEYS.ADMIN_QR_IMAGE) || "";
      const activeImg = draftAdminQrDataUrl || savedImg;
      const activeUpi = draftDetectedUpi || localStorage.getItem(STORAGE_KEYS.ADMIN_UPI_ID) || "";

      if (activeImg) {
        adminQrPreviewBox.innerHTML = `<img src="${activeImg}" alt="Admin QR" />`;
      } else {
        adminQrPreviewBox.innerHTML = `<span>Koi QR set nahi hai</span>`;
      }

      if (activeUpi) {
        detectedUpiInfo.innerHTML = `Detected UPI ID: <b style="color:#86efac;">${escapeHtml(activeUpi)}</b> (Amount apne aap set hoga)`;
      } else if (activeImg) {
        detectedUpiInfo.innerHTML = `QR Image ready hai. Payment ke waqt selected plan ka amount apne aap set hoga.`;
      } else {
        detectedUpiInfo.innerHTML = "";
      }
    }

    adminPickQrBtn.addEventListener("click", () => {
      adminQrFileInput.click();
    });

    adminQrFileInput.addEventListener("change", () => {
      const file = adminQrFileInput.files && adminQrFileInput.files[0];
      if (!file) return;

      const reader = new FileReader();
      reader.onload = async () => {
        draftAdminQrDataUrl = reader.result;
        const decoded = await decodeUpiFromImage(draftAdminQrDataUrl);
        draftDetectedUpi = decoded.upiId;
        draftDetectedName = decoded.payeeName;
        renderAdminQrPreview();
        setBoxMsg(adminQrMsg, "info", "QR select ho gaya hai. Ab 'QR Save Karein' par click karein.");
      };
      reader.readAsDataURL(file);
    });

    adminSaveQrBtn.addEventListener("click", () => {
      if (!draftAdminQrDataUrl) {
        setBoxMsg(adminQrMsg, "error", "Pehle Gallery se apna QR code chunein.");
        return;
      }

      try {
        localStorage.setItem(STORAGE_KEYS.ADMIN_QR_IMAGE, draftAdminQrDataUrl);
        localStorage.setItem(STORAGE_KEYS.ADMIN_UPI_ID, draftDetectedUpi || "");
        localStorage.setItem(STORAGE_KEYS.ADMIN_PAYEE_NAME, draftDetectedName || "Merchant");
        renderAdminQrPreview();
        if (selectedPlan && qrArea.classList.contains("show")) {
          renderPaymentQrForSelectedPlan();
        }
        setBoxMsg(adminQrMsg, "success", "✅ Aapka QR Save ho gaya! Ab koi bhi recharge karega toh yahi QR aayega aur paise apne aap set ho jayenge.");
      } catch {
        setBoxMsg(adminQrMsg, "error", "❌ Image file bahut badi hai. Kripya chhoti size ki QR image chunein.");
      }
    });

    adminClearQrBtn.addEventListener("click", () => {
      draftAdminQrDataUrl = "";
      draftDetectedUpi = "";
      draftDetectedName = "Merchant";
      localStorage.removeItem(STORAGE_KEYS.ADMIN_QR_IMAGE);
      localStorage.removeItem(STORAGE_KEYS.ADMIN_UPI_ID);
      localStorage.removeItem(STORAGE_KEYS.ADMIN_PAYEE_NAME);
      adminQrFileInput.value = "";
      renderAdminQrPreview();
      setBoxMsg(adminQrMsg, "info", "Saved QR hata diya gaya hai.");
    });

    // Plans Management
    function getPlans() {
      try {
        const stored = localStorage.getItem(STORAGE_KEYS.PLANS);
        if (!stored) {
          localStorage.setItem(STORAGE_KEYS.PLANS, JSON.stringify(INITIAL_PLANS));
          return [...INITIAL_PLANS];
        }
        const parsed = JSON.parse(stored);
        return Array.isArray(parsed) ? parsed : [...INITIAL_PLANS];
      } catch {
        return [...INITIAL_PLANS];
      }
    }

    function savePlans(plans) {
      localStorage.setItem(STORAGE_KEYS.PLANS, JSON.stringify(plans));
    }

    function renderPlans() {
      const plans = getPlans();
      plansContainer.innerHTML = "";

      if (plans.length === 0) {
        plansContainer.innerHTML = `<p class="sub">Abhi koi recharge plan available nahi hai.</p>`;
        return;
      }

      plans.forEach((plan) => {
        const div = document.createElement("div");
        div.className = "plan";
        if (selectedPlan && selectedPlan.id === plan.id) {
          div.classList.add("selected");
        }
        div.innerHTML = `
          <div class="plan-title">${escapeHtml(plan.title)}</div>
          <div class="plan-price">₹${plan.amount}</div>
          <div class="plan-days">${plan.days} Days Validity</div>
        `;
        div.addEventListener("click", () => {
          selectedPlan = { ...plan };
          renderPlans();
          qrArea.classList.remove("show");
          setBoxMsg(statusMsg, "", "");
          planDetailsBox.classList.add("show");
          selectedSummary.innerHTML = `Plan: <b>${escapeHtml(plan.title)}</b> &nbsp;|&nbsp; Amount: <b>₹${plan.amount}</b> &nbsp;|&nbsp; Validity: <b>${plan.days} Days</b>`;
        });
        plansContainer.appendChild(div);
      });
    }

    function renderVipPlanList() {
      const plans = getPlans();
      vipPlanList.innerHTML = "";

      if (plans.length === 0) {
        vipPlanList.innerHTML = `<div class="small-note">Koi plan nahi hai. Upar se naya recharge plan banayein.</div>`;
        return;
      }

      plans.forEach((plan) => {
        const row = document.createElement("div");
        row.className = "vip-plan-item";
        row.innerHTML = `
          <div>
            <div style="font-weight:700;">${escapeHtml(plan.title)}</div>
            <div style="font-size:0.85rem; color:#94a3b8;">Amount: <b style="color:#fff;">₹${plan.amount}</b> &bull; Validity: <b style="color:#86efac;">${plan.days} Days</b></div>
          </div>
          <div class="vip-actions">
            <button type="button" class="btn btn-primary btn-sm edit-btn">Edit</button>
            <button type="button" class="btn btn-danger btn-sm del-btn">Delete</button>
          </div>
        `;

        row.querySelector(".edit-btn").addEventListener("click", () => {
          editingPlanId = plan.id;
          vipPlanTitle.value = plan.title;
          vipPlanAmount.value = plan.amount;
          vipPlanDays.value = plan.days;
          vipSavePlanBtn.textContent = "Update Recharge Plan";
          vipCancelEditBtn.classList.remove("hidden");
          setBoxMsg(vipMsg, "info", `Editing: ${escapeHtml(plan.title)}`);
        });

        row.querySelector(".del-btn").addEventListener("click", () => {
          const updated = getPlans().filter((p) => p.id !== plan.id);
          savePlans(updated);
          if (selectedPlan && selectedPlan.id === plan.id) {
            selectedPlan = null;
            planDetailsBox.classList.remove("show");
          }
          if (editingPlanId === plan.id) {
            resetVipForm();
          }
          renderPlans();
          renderVipPlanList();
          setBoxMsg(vipMsg, "success", "🗑️ Recharge plan delete ho gaya.");
        });

        vipPlanList.appendChild(row);
      });
    }

    function resetVipForm() {
      editingPlanId = null;
      vipPlanTitle.value = "";
      vipPlanAmount.value = "";
      vipPlanDays.value = "";
      vipSavePlanBtn.textContent = "+ New Recharge Banayein";
      vipCancelEditBtn.classList.add("hidden");
    }

    vipSavePlanBtn.addEventListener("click", () => {
      const title = vipPlanTitle.value.trim();
      const amount = Number(vipPlanAmount.value);
      const days = Number(vipPlanDays.value);

      if (!title || !Number.isFinite(amount) || amount <= 0 || !Number.isFinite(days) || days <= 0) {
        setBoxMsg(vipMsg, "error", "Kripya sahi Plan Name, Amount (₹), aur Validity Days daalein.");
        return;
      }

      const plans = getPlans();
      if (editingPlanId) {
        const idx = plans.findIndex((p) => p.id === editingPlanId);
        if (idx !== -1) {
          plans[idx] = { ...plans[idx], title, amount, days };
        }
        savePlans(plans);
        if (selectedPlan && selectedPlan.id === editingPlanId) {
          selectedPlan = { ...plans[idx] };
          selectedSummary.innerHTML = `Plan: <b>${escapeHtml(title)}</b> &nbsp;|&nbsp; Amount: <b>₹${amount}</b> &nbsp;|&nbsp; Validity: <b>${days} Days</b>`;
          if (qrArea.classList.contains("show")) {
            renderPaymentQrForSelectedPlan();
          }
        }
        resetVipForm();
        setBoxMsg(vipMsg, "success", "✅ Recharge plan update ho gaya!");
      } else {
        const newPlan = {
          id: "plan_" + Date.now(),
          title,
          amount,
          days
        };
        plans.push(newPlan);
        savePlans(plans);
        resetVipForm();
        setBoxMsg(vipMsg, "success", "✅ Naya recharge plan ban gaya hai!");
      }

      renderPlans();
      renderVipPlanList();
    });

    vipCancelEditBtn.addEventListener("click", () => {
      resetVipForm();
      setBoxMsg(vipMsg, "", "");
    });

    // Unlock State per Phone
    function getUnlockMap() {
      try {
        return JSON.parse(localStorage.getItem(STORAGE_KEYS.UNLOCK_MAP) || "{}");
      } catch {
        return {};
      }
    }

    function getUserUnlockUntil(phone) {
      const map = getUnlockMap();
      const val = Number(map[phone] || "0");
      return Number.isFinite(val) ? val : 0;
    }

    function setUserUnlockUntil(phone, timestamp) {
      const map = getUnlockMap();
      map[phone] = timestamp;
      localStorage.setItem(STORAGE_KEYS.UNLOCK_MAP, JSON.stringify(map));
    }

    function updateAppUI() {
      const phone = getCurrentUser();

      if (!phone) {
        loginCard.classList.remove("hidden");
        vipAdminCard.classList.add("hidden");
        rechargeCard.classList.add("hidden");
        calendarSection.classList.add("hidden");
        userBadge.classList.add("hidden");
        logoutBtn.classList.add("hidden");
        accessBadge.className = "badge";
        accessBadge.textContent = "🔒 Login Required";
        return;
      }

      loginCard.classList.add("hidden");
      logoutBtn.classList.remove("hidden");
      userBadge.classList.remove("hidden");
      rechargeCard.classList.remove("hidden");

      const vip = isVipUser(phone);
      if (vip) {
        userBadge.className = "badge vip";
        userBadge.textContent = `👑 VIP: ${phone}`;
        vipAdminCard.classList.remove("hidden");
        renderAdminQrPreview();
        renderVipPlanList();
      } else {
        userBadge.className = "badge";
        userBadge.textContent = `📱 ${phone}`;
        vipAdminCard.classList.add("hidden");
      }

      renderPlans();

      const until = getUserUnlockUntil(phone);
      const now = Date.now();

      if (vip || until > now) {
        accessBadge.className = "badge active";
        accessBadge.textContent = vip ? "🔓 VIP Unlimited Access" : "🔓 Unlocked";
        calendarSection.classList.remove("hidden");
        validityInfo.textContent = vip
          ? "VIP Admin Access: Unlimited Validity"
          : `Active Plan Validity: ${new Date(until).toLocaleString("en-IN")} tak`;
        renderCalendar();
      } else {
        accessBadge.className = "badge";
        accessBadge.textContent = "🔒 Locked (Recharge Required)";
        calendarSection.classList.add("hidden");
      }
    }

    // Login & Logout Handlers
    loginBtn.addEventListener("click", () => {
      const phone = normalizePhone(phoneInput.value);
      if (!/^[6-9]\d{9}$/.test(phone)) {
        setBoxMsg(loginMsg, "error", "❌ Kripya sahi 10-digit mobile number daalein.");
        return;
      }
      setBoxMsg(loginMsg, "", "");
      localStorage.setItem(STORAGE_KEYS.CURRENT_USER, phone);
      phoneInput.value = "";
      selectedPlan = null;
      planDetailsBox.classList.remove("show");
      qrArea.classList.remove("show");
      setBoxMsg(statusMsg, "", "");
      updateAppUI();
    });

    phoneInput.addEventListener("keydown", (e) => {
      if (e.key === "Enter") loginBtn.click();
    });

    logoutBtn.addEventListener("click", () => {
      localStorage.removeItem(STORAGE_KEYS.CURRENT_USER);
      selectedPlan = null;
      resetVipForm();
      updateAppUI();
    });

    // Payment QR Display with Auto-Set Amount
    function renderPaymentQrForSelectedPlan() {
      if (!selectedPlan) return false;

      const savedAdminQr = (localStorage.getItem(STORAGE_KEYS.ADMIN_QR_IMAGE) || "").trim();
      const savedUpiId = (localStorage.getItem(STORAGE_KEYS.ADMIN_UPI_ID) || "").trim();
      const savedPayee = (localStorage.getItem(STORAGE_KEYS.ADMIN_PAYEE_NAME) || "Merchant").trim();

      if (!savedAdminQr) {
        setBoxMsg(
          statusMsg,
          "error",
          "❌ Admin ne abhi tak QR Code upload nahi kiya hai. VIP Admin (9569981484) se QR upload karwayein."
        );
        return false;
      }

      // Show Admin's Saved QR Image and auto-set selected plan's amount
      qrImage.src = savedAdminQr;
      qrAmountLabel.textContent = `Pay ₹${selectedPlan.amount}`;
      instrAmountText.textContent = `₹${selectedPlan.amount}`;

      if (savedUpiId) {
        qrUpiLabel.textContent = `UPI: ${savedUpiId}`;
        const dynamicUpiUri = `upi://pay?pa=${encodeURIComponent(savedUpiId)}&pn=${encodeURIComponent(savedPayee)}&am=${selectedPlan.amount}&cu=INR`;
        directUpiPayLink.href = dynamicUpiUri;
        directUpiPayLink.textContent = `Pay ₹${selectedPlan.amount} via UPI App`;
        directUpiPayLink.classList.remove("hidden");
      } else {
        qrUpiLabel.textContent = `Amount: ₹${selectedPlan.amount} (Auto Set)`;
        directUpiPayLink.classList.add("hidden");
      }

      return true;
    }

    proceedPayBtn.addEventListener("click", () => {
      if (!selectedPlan) {
        setBoxMsg(statusMsg, "error", "Pehle ek recharge plan chunein.");
        return;
      }
      const ok = renderPaymentQrForSelectedPlan();
      if (!ok) return;

      qrArea.classList.add("show");
      setBoxMsg(
        statusMsg,
        "info",
        `Admin QR khul gaya hai aur ₹${selectedPlan.amount} apne aap set ho gaya hai. Pay karke gallery se screenshot upload karein.`
      );
    });

    galleryPickBtn.addEventListener("click", () => {
      screenshotInput.click();
    });

    screenshotInput.addEventListener("change", () => {
      const file = screenshotInput.files && screenshotInput.files[0];
      if (file) {
        const ageMins = Math.round((Date.now() - file.lastModified) / 60000);
        fileChosenText.textContent = `Selected: ${file.name} (${ageMins} min purana)`;
      } else {
        fileChosenText.textContent = "Koi screenshot select nahi hua hai";
      }
    });

    function getUsedHashes() {
      try {
        return JSON.parse(localStorage.getItem(STORAGE_KEYS.USED_HASHES) || "[]");
      } catch {
        return [];
      }
    }

    function saveUsedHash(hash) {
      const list = getUsedHashes();
      if (!list.includes(hash)) {
        list.push(hash);
        localStorage.setItem(STORAGE_KEYS.USED_HASHES, JSON.stringify(list));
      }
    }

    async function computeSha256(file) {
      const buffer = await file.arrayBuffer();
      const hashBuffer = await crypto.subtle.digest("SHA-256", buffer);
      return Array.from(new Uint8Array(hashBuffer))
        .map((b) => b.toString(16).padStart(2, "0"))
        .join("");
    }

    verifyBtn.addEventListener("click", async () => {
      setBoxMsg(statusMsg, "", "");
      const phone = getCurrentUser();
      if (!phone) {
        setBoxMsg(statusMsg, "error", "Pehle login karein.");
        return;
      }

      if (!selectedPlan) {
        setBoxMsg(statusMsg, "error", "Pehle plan select karein aur Proceed to Pay par click karein.");
        return;
      }

      const file = screenshotInput.files && screenshotInput.files[0];
      if (!file) {
        setBoxMsg(statusMsg, "error", "Gallery se payment ka screenshot chunein.");
        return;
      }

      // 1. 3-hour validity check
      const ageMs = Date.now() - file.lastModified;
      if (ageMs > DEFAULT_CONFIG.MAX_SCREENSHOT_AGE_MS || ageMs < -5 * 60 * 1000) {
        setBoxMsg(statusMsg, "error", "❌ Yeh screenshot expire ho chuka hai! Screenshot lene ke 3 ghante ke andar upload karna zaroori hai.");
        return;
      }

      // 2. Single-use check via SHA-256 hash
      const fileHash = await computeSha256(file);
      if (getUsedHashes().includes(fileHash)) {
        setBoxMsg(statusMsg, "error", "❌ Yeh screenshot pehle hi use ho chuka hai! Ek screenshot sirf 1 baar valid hai.");
        return;
      }

      // 3. OCR verification
      verifyBtn.disabled = true;
      setBoxMsg(statusMsg, "info", "🔍 Screenshot verify ho raha hai... Kripya wait karein.");

      try {
        const result = await Tesseract.recognize(file, "eng");
        const rawText = (result.data && result.data.text) ? result.data.text : "";
        const normalizedText = rawText.replace(/,/g, "").toLowerCase();

        const hasSuccessKeyword =
          normalizedText.includes("paid") ||
          normalizedText.includes("success") ||
          normalizedText.includes("successful") ||
          normalizedText.includes("completed") ||
          normalizedText.includes("sent");

        const amountStr = String(selectedPlan.amount);
        const amountRegex = new RegExp(`(?:₹|rs\\.?|inr|\\s|^)${amountStr}(?:\\.00)?(?:\\s|$)`, "i");
        const hasAmount = amountRegex.test(normalizedText) || normalizedText.includes(amountStr);

        const savedUpi = (localStorage.getItem(STORAGE_KEYS.ADMIN_UPI_ID) || "").toLowerCase().trim();
        const hasUpi = !DEFAULT_CONFIG.REQUIRE_UPI_IN_OCR ||
          !savedUpi ||
          normalizedText.includes(savedUpi);

        if (!hasSuccessKeyword || !hasAmount) {
          setBoxMsg(
            statusMsg,
            "error",
            `❌ Payment verify nahi ho paya! Screenshot mein ₹${selectedPlan.amount} aur Paid/Success clear nahi hai.`
          );
          verifyBtn.disabled = false;
          return;
        }

        if (!hasUpi) {
          setBoxMsg(
            statusMsg,
            "error",
            `❌ Yeh payment Admin ke QR (${savedUpi}) par match nahi hua! Sahi QR par ₹${selectedPlan.amount} pay karke naya screenshot dalein.`
          );
          verifyBtn.disabled = false;
          return;
        }

        saveUsedHash(fileHash);
        const currentUntil = Math.max(Date.now(), getUserUnlockUntil(phone));
        const newUntil = currentUntil + selectedPlan.days * 24 * 60 * 60 * 1000;
        setUserUnlockUntil(phone, newUntil);

        screenshotInput.value = "";
        fileChosenText.textContent = "Koi screenshot select nahi hua hai";
        verifyBtn.disabled = false;
        setBoxMsg(statusMsg, "success", `✅ Payment verify ho gaya! Calendar ${selectedPlan.days} days ke liye unlock ho gaya hai.`);
        updateAppUI();
      } catch {
        verifyBtn.disabled = false;
        setBoxMsg(statusMsg, "error", "❌ Screenshot padhne mein error aaya. Kripya clear screenshot dubara upload karein.");
      }
    });

    // Calendar Logic
    function getNotes() {
      try {
        return JSON.parse(localStorage.getItem(STORAGE_KEYS.NOTES) || "{}");
      } catch {
        return {};
      }
    }

    function renderCalendar() {
      const grid = document.getElementById("calendarGrid");
      const title = document.getElementById("monthYearTitle");
      grid.innerHTML = "";

      const year = currentViewDate.getFullYear();
      const month = currentViewDate.getMonth();

      title.textContent = currentViewDate.toLocaleDateString("en-IN", {
        month: "long",
        year: "numeric"
      });

      const dayNames = ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"];
      dayNames.forEach((d) => {
        const el = document.createElement("div");
        el.className = "cal-day-name";
        el.textContent = d;
        grid.appendChild(el);
      });

      const firstDayIndex = new Date(year, month, 1).getDay();
      const daysInMonth = new Date(year, month + 1, 0).getDate();
      const notes = getNotes();

      const now = new Date();
      const todayKey = `${now.getFullYear()}-${String(now.getMonth() + 1).padStart(2, "0")}-${String(now.getDate()).padStart(2, "0")}`;

      for (let i = 0; i < firstDayIndex; i++) {
        const emptyCell = document.createElement("div");
        emptyCell.className = "cal-cell empty";
        grid.appendChild(emptyCell);
      }

      for (let day = 1; day <= daysInMonth; day++) {
        const dateKey = `${year}-${String(month + 1).padStart(2, "0")}-${String(day).padStart(2, "0")}`;
        const cell = document.createElement("div");
        cell.className = "cal-cell";
        if (dateKey === todayKey) cell.classList.add("today");

        const numEl = document.createElement("div");
        numEl.className = "cal-date-num";
        numEl.textContent = day;
        cell.appendChild(numEl);

        if (notes[dateKey]) {
          const noteTag = document.createElement("div");
          noteTag.className = "cal-note-dot";
          noteTag.textContent = notes[dateKey];
          cell.appendChild(noteTag);
        }

        cell.addEventListener("click", () => {
          selectedDateStr = dateKey;
          document.getElementById("selectedDateLabel").textContent = `Note for ${dateKey}`;
          document.getElementById("noteInput").value = notes[dateKey] || "";
        });

        grid.appendChild(cell);
      }
    }

    document.getElementById("prevMonthBtn").addEventListener("click", () => {
      currentViewDate.setMonth(currentViewDate.getMonth() - 1);
      renderCalendar();
    });

    document.getElementById("nextMonthBtn").addEventListener("click", () => {
      currentViewDate.setMonth(currentViewDate.getMonth() + 1);
      renderCalendar();
    });

    document.getElementById("saveNoteBtn").addEventListener("click", () => {
      if (!selectedDateStr) return;
      const notes = getNotes();
      const val = document.getElementById("noteInput").value.trim();
      if (val) {
        notes[selectedDateStr] = val;
      } else {
        delete notes[selectedDateStr];
      }
      localStorage.setItem(STORAGE_KEYS.NOTES, JSON.stringify(notes));
      renderCalendar();
    });

    function escapeHtml(str) {
      return String(str)
        .replace(/&/g, "&amp;")
        .replace(/</g, "&lt;")
        .replace(/>/g, "&gt;")
        .replace(/"/g, "&quot;");
    }

    // Init
    updateAppUI();
  </script>
</body>
</html>








