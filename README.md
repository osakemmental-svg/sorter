<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>dateiensortiert – Automatisches Datei-Ordnen</title>
  <style>
    /* === RESET & FONTS === */
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      font-family: 'Inter', system-ui, sans-serif;
      background: linear-gradient(135deg, #0f172a, #1e293b);
      color: #f1f5f9;
      line-height: 1.5;
    }
    a { text-decoration: none; color: inherit; }
    h1, h2, h3 { font-weight: 700; }

    /* === NAVBAR === */
    header {
      padding: 1rem 2rem;
      display: flex;
      justify-content: space-between;
      align-items: center;
      background: rgba(15, 23, 42, 0.6);
      backdrop-filter: blur(10px);
      border-bottom: 1px solid rgba(255, 255, 255, 0.1);
      position: sticky;
      top: 0;
      z-index: 100;
    }
    .logo {
      font-size: 1.2rem;
      font-weight: 800;
      background: linear-gradient(to right, #38bdf8, #06b6d4);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }

    /* === HERO === */
    .hero {
      max-width: 900px;
      margin: 4rem auto;
      padding: 2rem;
      text-align: center;
    }
    .hero h1 {
      font-size: clamp(2.2rem, 5vw, 3.5rem);
      margin-bottom: 1rem;
    }
    .hero p {
      color: #cbd5e1;
      font-size: 1.2rem;
      margin-bottom: 2rem;
    }

    /* === BUTTONS === */
    .btn {
      display: inline-flex;
      align-items: center;
      gap: .5rem;
      padding: 0.9rem 1.6rem;
      border-radius: 12px;
      font-weight: 600;
      font-size: 1rem;
      cursor: pointer;
      border: none;
      background: linear-gradient(135deg, #38bdf8, #0ea5e9);
      color: #0f172a;
      transition: transform 0.15s ease, box-shadow 0.2s ease;
      box-shadow: 0 4px 14px rgba(14,165,233,.3);
    }
    .btn:hover {
      transform: translateY(-2px);
      box-shadow: 0 6px 20px rgba(14,165,233,.4);
    }
    .btn.secondary {
      background: rgba(255,255,255,0.05);
      border: 1px solid rgba(255,255,255,0.2);
      color: #f8fafc;
      box-shadow: none;
    }
    .btn.secondary:hover {
      background: rgba(255,255,255,0.1);
    }

    /* === CARD === */
    .card {
      max-width: 800px;
      margin: 2rem auto;
      padding: 2rem;
      border-radius: 20px;
      background: rgba(255,255,255,0.05);
      border: 1px solid rgba(255,255,255,0.1);
      backdrop-filter: blur(10px);
      text-align: center;
    }

    /* === FEATURES === */
    .features {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
      gap: 1.2rem;
      margin-top: 2rem;
    }
    .feature {
      padding: 1.2rem;
      border-radius: 16px;
      background: rgba(255,255,255,0.05);
      border: 1px solid rgba(255,255,255,0.1);
      text-align: left;
    }
    .feature strong {
      color: #38bdf8;
      margin-right: .5rem;
    }

    /* === FOOTER === */
    footer {
      margin-top: 3rem;
      padding: 1.5rem;
      text-align: center;
      font-size: 0.9rem;
      color: #94a3b8;
      border-top: 1px solid rgba(255,255,255,0.1);
    }

    /* === RESPONSIVE === */
    @media(max-width:600px){
      .hero h1 { font-size: 2rem; }
    }
  </style>
</head>
<body>
  <!-- NAV -->
  <header>
    <div class="logo">dateiensortiert</div>
    <a href="#downloads" class="btn secondary">Download</a>
  </header>

  <!-- HERO -->
  <section class="hero">
    <h1>Ordnung schaffen – automatisch.</h1>
    <p><strong>dateiensortiert</strong> sortiert deine Dateien nach Regeln: Endungen, Datum, Größe oder Muster. Kein Chaos mehr im Download-Ordner!</p>
    <button class="btn" id="mainDownload">⬇️ Jetzt herunterladen</button>
  </section>

  <!-- FEATURES -->
  <div class="card">
    <h2>Warum dateiensortiert?</h2>
    <div class="features">
      <div class="feature"><strong>✔</strong> Regeln für Dateitypen, Größe & Datum</div>
      <div class="feature"><strong>✔</strong> Simulation („Trockenlauf“) vor dem Verschieben</div>
      <div class="feature"><strong>✔</strong> Protokoll + Rückgängig-Funktion</div>
      <div class="feature"><strong>✔</strong> Funktioniert auf Windows & macOS</div>
    </div>
  </div>

  <!-- DOWNLOADS -->
  <div class="card" id="downloads">
    <h2>Download</h2>
    <p>Dein System: <span id="osLabel">erkennen...</span></p>
    <button class="btn" id="downloadBtn">⬇️ Download</button>
    <p style="margin-top:1rem; font-size:0.9rem; color:#94a3b8">
      Version <span id="version">v1.2.3</span> · Release: <span id="releaseDate">2025-08-01</span>
    </p>
  </div>

  <footer>
    © <span id="year"></span> dateiensortiert · <a href="#" style="color:#38bdf8">Impressum</a> · <a href="#" style="color:#38bdf8">Datenschutz</a>
  </footer>

  <script>
    // === METADATEN ANPASSEN ===
    const LATEST_VERSION = "v1.2.3";
    const RELEASE_DATE   = "2025-08-01";
    const DOWNLOADS = {
      windows: "https://example.com/dateiensortiert-setup.exe",
      macos:   "https://example.com/dateiensortiert.dmg"
    };

    // === OS erkennen ===
    function detectOS(){
      const ua = navigator.userAgent.toLowerCase();
      if (ua.includes("windows")) return "windows";
      if (ua.includes("mac os x") || ua.includes("macintosh")) return "macos";
      return "windows"; // Fallback
    }

    // === INIT ===
    document.addEventListener("DOMContentLoaded", () => {
      const os = detectOS();
      document.getElementById("osLabel").textContent = os;
      document.getElementById("version").textContent = LATEST_VERSION;
      document.getElementById("releaseDate").textContent = RELEASE_DATE;
      document.getElementById("year").textContent = new Date().getFullYear();

      const url = DOWNLOADS[os];
      const btn = document.getElementById("downloadBtn");
      btn.addEventListener("click", () => window.location.href = url);

      // Hero-Button scrollt runter
      document.getElementById("mainDownload").addEventListener("click", () => {
        window.scrollTo({ top: document.getElementById("downloads").offsetTop - 50, behavior: "smooth" });
      });
    });
  </script>
</body>
</html>
