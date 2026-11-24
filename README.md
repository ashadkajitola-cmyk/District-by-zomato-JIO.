<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>UPI Unlock Calendar</title>
  <script src="https://cdn.jsdelivr.net/npm/tesseract.js@5/dist/tesseract.min.js"></script>
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
      max-width: 960px;
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

    .card {
      background: var(--card);
      border: 1px solid var(--border);
      border-radius: 18px;
      padding: 22px;
      margin-bottom: 20px;
      box-shadow: 0 12px 30px rgba(0, 0, 0, 0.25);
    }

    h2 {
      font-size: 1.25rem;
      margin-bottom: 8px;
    }

    p.sub {
      color: var(--muted);
      font-size: 0.92rem;
      margin-bottom: 18px;
      line-height: 1.45;
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
      border-radius: 12px;
      padding: 12px 18px;
      font-size: 0.95rem;
      font-weight: 700;
      cursor: pointer;
      transition: all 0.2s ease;
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

    .btn-ghost {
      background: rgba(255, 255, 255, 0.07);
      color: var(--text);
      border: 1px solid var(--border);
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
      max-width: 250px;
      margin: 0 auto;
    }

    .qr-frame img {
      width: 200px;
      height: 200px;
      object-fit: contain;
      display: block;
      margin: 0 auto 8px;
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

    .qr-setup-box {
      margin-top: 16px;
      padding: 14px;
      border-radius: 12px;
      background: rgba(15, 23, 42, 0.6);
      border: 1px solid var(--border);
    }

    .qr-setup-box input[type="text"] {
      width: 100%;
      padding: 9px 11px;
      border-radius: 8px;
      border: 1px solid var(--border);
      background: #0b1220;
      color: #fff;
      margin-top: 8px;
      font-size: 0.88rem;
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

    .calendar-section {
      display: none;
    }

    .calendar-section.unlocked {
      display: block;
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
      width: 100%;
      padding: 10px 12px;
      border-radius: 8px;
      border: 1px solid var(--border);
      background: #0f172a;
      color: #fff;
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
      <div id="accessBadge" class="badge">🔒 Locked (Recharge Required)</div>
    </div>

    <!-- RECHARGE & UNLOCK PANEL -->
    <div id="rechargeCard" class="card">
      <h2>1. Recharge Plan Chunein</h2>
      <p class="sub">
        Calendar unlock karne ke liye plan chunein, Proceed to Pay dabayein, QR scan karke payment karein, aur <b>3 ghante</b> ke andar <b>1 baar</b> screenshot upload karein.
      </p>

      <div class="plans" id="plansContainer">
        <div class="plan" data-id="plan1" data-amount="348" data-days="27">
          <div class="plan-title">1st Recharge Plan</div>
          <div class="plan-price">₹348</div>
          <div class="plan-days">27 Days Validity</div>
        </div>
        <div class="plan" data-id="plan2" data-amount="66" data-days="15">
          <div class="plan-title">2nd Recharge Plan</div>
          <div class="plan-price">₹66</div>
          <div class="plan-days">15 Days Validity</div>
        </div>
        <div class="plan" data-id="plan3" data-amount="239" data-days="24">
          <div class="plan-title">3rd Recharge Plan</div>
          <div class="plan-price">₹239</div>
          <div class="plan-days">24 Days Validity</div>
        </div>
      </div>

      <div id="planDetailsBox" class="payment-box">
        <h3>Selected Plan Details</h3>
        <p class="sub" id="selectedSummary" style="margin-top:6px; margin-bottom:14px;"></p>
        <button id="proceedPayBtn" class="btn btn-primary" type="button">Proceed to Pay</button>

        <div id="qrArea" class="qr-area">
          <div class="qr-grid">
            <div class="qr-frame">
              <img id="qrImage" alt="Payment QR Code" />
              <div style="font-weight:700; font-size:0.85rem;" id="qrAmountLabel">Scan & Pay</div>
              <div style="font-size:0.75rem; color:#475569; margin-top:2px;" id="qrUpiLabel"></div>
            </div>

            <div>
              <h3>UPI Payment & Screenshot Upload</h3>
              <p class="sub" style="margin-top:6px; margin-bottom:8px;">
                1. Diye gaye QR ko scan karke exact amount pay karein.<br />
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

          <div class="qr-setup-box">
            <div style="font-weight:700; font-size:0.9rem;">⚙️ Apna QR Code Set Karein (Owner Option)</div>
            <p class="small-note" style="margin-top:4px; margin-bottom:6px;">
              Apni QR image gallery/file se upload karein ya GitHub repo wali image file ka naam (jaise <code>my-qr.png</code>) aur UPI ID daalein.
            </p>
            <input type="file" id="ownerQrFileInput" accept="image/*" style="display:none;" />
            <button id="ownerQrGalleryBtn" class="btn btn-ghost" type="button" style="padding:8px 12px; font-size:0.82rem;">
              🖼️ Gallery se Apna QR Upload Karein
            </button>
            <input type="text" id="ownerUpiInput" placeholder="Apni UPI ID likhein (e.g. name@okaxis)" />
            <button id="saveOwnerQrBtn" class="btn btn-primary" type="button" style="margin-top:8px; padding:8px 14px; font-size:0.85rem;">
              Save QR & UPI
            </button>
          </div>
        </div>
      </div>

      <div id="statusMsg" class="status-msg"></div>
      <div class="small-note">
        Note: Static GitHub Pages par client-side verification chalta hai. Har screenshot ka SHA-256 hash save hota hai (1 baar hi valid) aur file ka time 3 ghante se purana nahi hona chahiye.
      </div>
    </div>

    <!-- UNLOCKED CALENDAR -->
    <div id="calendarSection" class="card calendar-section">
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
    // DEFAULT OWNER SETTINGS — YAHAN BHI APNI UPI / QR FILE SET KAR SAKTE HAIN
    // =========================================================================
    const DEFAULT_CONFIG = {
      UPI_ID: "yourupi@okaxis",
      PAYEE_NAME: "Unlock Calendar",
      QR_IMAGE_URL: "", // e.g. "qr.png" agar GitHub repo mein rakhi hai
      MAX_SCREENSHOT_AGE_MS: 3 * 60 * 60 * 1000, // 3 Hours
      REQUIRE_UPI_IN_OCR: true
    };

    const STORAGE_KEYS = {
      UNLOCK_UNTIL: "unlock_calendar_until_v2",
      USED_HASHES: "unlock_calendar_used_hashes_v2",
      NOTES: "unlock_calendar_notes_v2",
      CUSTOM_QR_DATA_URL: "unlock_calendar_custom_qr_v2",
      CUSTOM_UPI_ID: "unlock_calendar_custom_upi_v2"
    };

    let selectedPlan = null;
    let currentViewDate = new Date();
    let selectedDateStr = null;
    let pendingOwnerQrDataUrl = localStorage.getItem(STORAGE_KEYS.CUSTOM_QR_DATA_URL) || "";

    const plans = document.querySelectorAll(".plan");
    const planDetailsBox = document.getElementById("planDetailsBox");
    const selectedSummary = document.getElementById("selectedSummary");
    const proceedPayBtn = document.getElementById("proceedPayBtn");
    const qrArea = document.getElementById("qrArea");
    const qrImage = document.getElementById("qrImage");
    const qrAmountLabel = document.getElementById("qrAmountLabel");
    const qrUpiLabel = document.getElementById("qrUpiLabel");

    const screenshotInput = document.getElementById("screenshotInput");
    const galleryPickBtn = document.getElementById("galleryPickBtn");
    const fileChosenText = document.getElementById("fileChosenText");
    const verifyBtn = document.getElementById("verifyBtn");

    const ownerQrFileInput = document.getElementById("ownerQrFileInput");
    const ownerQrGalleryBtn = document.getElementById("ownerQrGalleryBtn");
    const ownerUpiInput = document.getElementById("ownerUpiInput");
    const saveOwnerQrBtn = document.getElementById("saveOwnerQrBtn");

    const statusMsg = document.getElementById("statusMsg");
    const accessBadge = document.getElementById("accessBadge");
    const calendarSection = document.getElementById("calendarSection");
    const validityInfo = document.getElementById("validityInfo");

    function getActiveUpiId() {
      const saved = (localStorage.getItem(STORAGE_KEYS.CUSTOM_UPI_ID) || "").trim();
      return saved || DEFAULT_CONFIG.UPI_ID;
    }

    function getActiveQrSrc(amount) {
      const savedQr = (localStorage.getItem(STORAGE_KEYS.CUSTOM_QR_DATA_URL) || "").trim();
      if (savedQr) return savedQr;
      if (DEFAULT_CONFIG.QR_IMAGE_URL && DEFAULT_CONFIG.QR_IMAGE_URL.trim() !== "") {
        return DEFAULT_CONFIG.QR_IMAGE_URL.trim();
      }
      const upiId = getActiveUpiId();
      const upiUri = `upi://pay?pa=${encodeURIComponent(upiId)}&pn=${encodeURIComponent(DEFAULT_CONFIG.PAYEE_NAME)}&am=${amount}&cu=INR`;
      return `https://api.qrserver.com/v1/create-qr-code/?size=220x220&data=${encodeURIComponent(upiUri)}`;
    }

    function showStatus(type, message) {
      statusMsg.className = `status-msg show ${type}`;
      statusMsg.innerHTML = message;
    }

    function clearStatus() {
      statusMsg.className = "status-msg";
      statusMsg.innerHTML = "";
    }

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

    function getUnlockUntil() {
      const val = Number(localStorage.getItem(STORAGE_KEYS.UNLOCK_UNTIL) || "0");
      return Number.isFinite(val) ? val : 0;
    }

    function checkUnlockState() {
      const until = getUnlockUntil();
      const now = Date.now();
      if (until > now) {
        accessBadge.textContent = "🔓 Unlocked";
        accessBadge.classList.add("active");
        calendarSection.classList.add("unlocked");
        const expDate = new Date(until).toLocaleString("en-IN");
        validityInfo.textContent = `Active Plan Validity: ${expDate} tak`;
        renderCalendar();
      } else {
        accessBadge.textContent = "🔒 Locked (Recharge Required)";
        accessBadge.classList.remove("active");
        calendarSection.classList.remove("unlocked");
      }
    }

    function refreshQrDisplay() {
      if (!selectedPlan) return;
      const upiId = getActiveUpiId();
      qrImage.src = getActiveQrSrc(selectedPlan.amount);
      qrAmountLabel.textContent = `Pay ₹${selectedPlan.amount}`;
      qrUpiLabel.textContent = `UPI: ${upiId}`;
    }

    plans.forEach((planEl) => {
      planEl.addEventListener("click", () => {
        plans.forEach((p) => p.classList.remove("selected"));
        planEl.classList.add("selected");

        selectedPlan = {
          id: planEl.dataset.id,
          amount: Number(planEl.dataset.amount),
          days: Number(planEl.dataset.days)
        };

        qrArea.classList.remove("show");
        clearStatus();
        planDetailsBox.classList.add("show");
        selectedSummary.innerHTML = `Amount: <b>₹${selectedPlan.amount}</b> &nbsp;|&nbsp; Validity: <b>${selectedPlan.days} Days</b>`;
      });
    });

    proceedPayBtn.addEventListener("click", () => {
      if (!selectedPlan) {
        showStatus("error", "Pehle ek recharge plan chunein.");
        return;
      }
      ownerUpiInput.value = getActiveUpiId();
      refreshQrDisplay();
      qrArea.classList.add("show");
      showStatus("info", `QR open ho gaya hai. ₹${selectedPlan.amount} pay karke gallery se screenshot upload karein.`);
    });

    // Gallery button for payment screenshot
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

    // Gallery button for owner's QR image
    ownerQrGalleryBtn.addEventListener("click", () => {
      ownerQrFileInput.click();
    });

    ownerQrFileInput.addEventListener("change", () => {
      const file = ownerQrFileInput.files && ownerQrFileInput.files[0];
      if (!file) return;
      const reader = new FileReader();
      reader.onload = () => {
        pendingOwnerQrDataUrl = reader.result;
        qrImage.src = pendingOwnerQrDataUrl;
        showStatus("info", "QR preview update ho gaya hai. Save QR & UPI par click karein.");
      };
      reader.readAsDataURL(file);
    });

    saveOwnerQrBtn.addEventListener("click", () => {
      const upiVal = ownerUpiInput.value.trim();
      if (upiVal) {
        localStorage.setItem(STORAGE_KEYS.CUSTOM_UPI_ID, upiVal);
      }
      if (pendingOwnerQrDataUrl) {
        try {
          localStorage.setItem(STORAGE_KEYS.CUSTOM_QR_DATA_URL, pendingOwnerQrDataUrl);
        } catch {
          showStatus("error", "QR image size thoda bada hai; chhoti image chunein ya repo mein file rakhein.");
          return;
        }
      }
      refreshQrDisplay();
      showStatus("success", "✅ Aapka QR aur UPI save ho gaya hai!");
    });

    verifyBtn.addEventListener("click", async () => {
      clearStatus();

      if (!selectedPlan) {
        showStatus("error", "Pehle plan select karein aur Proceed to Pay par click karein.");
        return;
      }

      const file = screenshotInput.files && screenshotInput.files[0];
      if (!file) {
        showStatus("error", "Gallery se payment ka screenshot chunein.");
        return;
      }

      // 1. 3-hour validity check
      const ageMs = Date.now() - file.lastModified;
      if (ageMs > DEFAULT_CONFIG.MAX_SCREENSHOT_AGE_MS || ageMs < -5 * 60 * 1000) {
        showStatus("error", "❌ Yeh screenshot expire ho chuka hai! Screenshot lene ke 3 ghante ke andar upload karna zaroori hai.");
        return;
      }

      // 2. Single-use check via SHA-256 hash
      const fileHash = await computeSha256(file);
      if (getUsedHashes().includes(fileHash)) {
        showStatus("error", "❌ Yeh screenshot pehle hi use ho chuka hai! Ek screenshot sirf 1 baar valid hai.");
        return;
      }

      // 3. OCR verification (amount, payment status, and UPI/QR match)
      verifyBtn.disabled = true;
      showStatus("info", "🔍 Screenshot verify ho raha hai... Kripya wait karein.");

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

        const activeUpi = getActiveUpiId().toLowerCase().trim();
        const hasUpi = !DEFAULT_CONFIG.REQUIRE_UPI_IN_OCR ||
          !activeUpi ||
          activeUpi === "yourupi@okaxis" ||
          normalizedText.includes(activeUpi);

        if (!hasSuccessKeyword || !hasAmount) {
          showStatus(
            "error",
            `❌ Payment verify nahi ho paya! Screenshot mein ₹${selectedPlan.amount} aur Paid/Success clear nahi hai.`
          );
          verifyBtn.disabled = false;
          return;
        }

        if (!hasUpi) {
          showStatus(
            "error",
            `❌ Yeh payment aapke QR/UPI (${getActiveUpiId()}) par match nahi hua! Sahi QR par ₹${selectedPlan.amount} pay karke naya screenshot dalein.`
          );
          verifyBtn.disabled = false;
          return;
        }

        // Mark screenshot as used & unlock calendar
        saveUsedHash(fileHash);
        const currentUntil = Math.max(Date.now(), getUnlockUntil());
        const newUntil = currentUntil + selectedPlan.days * 24 * 60 * 60 * 1000;
        localStorage.setItem(STORAGE_KEYS.UNLOCK_UNTIL, String(newUntil));

        screenshotInput.value = "";
        fileChosenText.textContent = "Koi screenshot select nahi hua hai";
        verifyBtn.disabled = false;
        showStatus("success", `✅ Payment verify ho gaya! Calendar ${selectedPlan.days} days ke liye unlock ho gaya hai.`);
        checkUnlockState();
      } catch {
        verifyBtn.disabled = false;
        showStatus("error", "❌ Screenshot padhne mein error aaya. Kripya clear screenshot dubara upload karein.");
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

    checkUnlockState();
  </script>
</body>
</html>





