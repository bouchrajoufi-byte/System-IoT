Annex B : Programme complet pour l’interface web

<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Station de contrôle Arduino</title>
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;500;700&family=DM+Sans:wght@300;400;500;600&display=swap" rel="stylesheet">
<style>
:root {
  --bg: #0d0f14;
  --bg2: #141720;
  --bg3: #1c2030;
  --border: rgba(255,255,255,0.07);
  --border2: rgba(255,255,255,0.13);
  --text: #e8eaf0;
  --muted: #7a7e8a;
  --accent: #4f9cf9;
  --accent2: #6ee7b7;
  --danger: #f87171;
  --warn: #fbbf24;
  --success: #34d399;
  --purple: #a78bfa;
  --orange: #fb923c;
  --mono: 'JetBrains Mono', monospace;
  --sans: 'DM Sans', sans-serif;
}
* { margin: 0; padding: 0; box-sizing: border-box; }
body {
  background: var(--bg);
  color: var(--text);
  font-family: var(--sans);
  min-height: 100vh;
  overflow-x: hidden;
}
.grid-bg {
  position: fixed; inset: 0; z-index: 0;
  background-image:
    linear-gradient(rgba(79,156,249,0.03) 1px, transparent 1px),
    linear-gradient(90deg, rgba(79,156,249,0.03) 1px, transparent 1px);
  background-size: 40px 40px;
  pointer-events: none;
}
.app { position: relative; z-index: 1; max-width: 1280px; margin: 0 auto; padding: 24px 20px; }

/* HEADER */
header {
  display: flex; align-items: center; justify-content: space-between;
  margin-bottom: 28px;
  padding-bottom: 20px;
  border-bottom: 1px solid var(--border);
}
.logo {
  display: flex; align-items: center; gap: 12px;
}
.logo-icon {
  width: 38px; height: 38px; border-radius: 10px;
  background: linear-gradient(135deg, var(--accent), var(--purple));
  display: flex; align-items: center; justify-content: center;
  font-size: 18px;
}
.logo h1 { font-size: 16px; font-weight: 600; letter-spacing: -0.3px; }
.logo p { font-size: 12px; color: var(--muted); font-family: var(--mono); }

.status-bar { display: flex; align-items: center; gap: 12px; }
#connection-status {
  display: flex; align-items: center; gap: 7px;
  font-size: 12px; font-family: var(--mono);
  padding: 6px 14px; border-radius: 20px;
  background: var(--bg3); border: 1px solid var(--border);
}
.dot { width: 7px; height: 7px; border-radius: 50%; background: var(--muted); }
.dot.connected { background: var(--success); box-shadow: 0 0 8px var(--success); animation: pulse 2s infinite; }
.dot.error { background: var(--danger); }
@keyframes pulse { 0%,100%{opacity:1} 50%{opacity:0.5} }
#connect-btn, #disconnect-btn {
  padding: 8px 18px; border-radius: 8px; cursor: pointer;
  font-family: var(--sans); font-size: 13px; font-weight: 500;
  border: 1px solid; transition: all 0.2s;
}
#connect-btn {
  background: var(--accent); color: #000; border-color: var(--accent);
}
#connect-btn:hover { background: #6aaeff; }
#disconnect-btn {
  background: transparent; color: var(--danger); border-color: var(--danger);
  display: none;
}
#disconnect-btn:hover { background: rgba(248,113,113,0.1); }
/* SECTION TITLES */
.section-title {
  font-size: 11px; font-weight: 500; letter-spacing: 1.5px;
  text-transform: uppercase; color: var(--muted);
  margin-bottom: 14px; display: flex; align-items: center; gap: 8px;
}
.section-title::after {
  content: ''; flex: 1; height: 1px; background: var(--border);
}
/* SENSOR GRID */
.sensor-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
  gap: 12px;
  margin-bottom: 28px;
}
.sensor-card {
  background: var(--bg2);
  border: 1px solid var(--border);
  border-radius: 14px;
  padding: 16px;
  position: relative;
  overflow: hidden;
  transition: border-color 0.3s, transform 0.2s;
}
.sensor-card:hover { transform: translateY(-1px); border-color: var(--border2); }
.sensor-card.alert { border-color: var(--danger) !important; }
.sensor-card.alert::before {
  content: '';
  position: absolute; inset: 0;
  background: rgba(248,113,113,0.05);
  animation: flash 1s ease-in-out infinite;
}
@keyframes flash { 0%,100%{opacity:0} 50%{opacity:1} }
.sensor-card .icon {
  font-size: 20px; margin-bottom: 10px;
}
.sensor-card .label {
  font-size: 11px; color: var(--muted); margin-bottom: 4px; font-family: var(--mono);
  text-transform: uppercase; letter-spacing: 0.8px;
}
.sensor-card .value {
  font-size: 26px; font-weight: 600; font-family: var(--mono);
  line-height: 1;
}
.sensor-card .unit {
  font-size: 12px; color: var(--muted); margin-left: 2px;
}
.sensor-card .sub {
  font-size: 11px; color: var(--muted); margin-top: 6px;
}
/* BAR INDICATOR */
.bar-wrap {
  height: 3px; background: var(--border); border-radius: 2px; margin-top: 10px;
}
.bar-fill {
  height: 100%; border-radius: 2px; transition: width 0.8s ease;
}
/* ALERT BADGE */
.alert-badge {
  position: absolute; top: 10px; right: 10px;
  background: var(--danger); color: #fff;
  font-size: 9px; font-weight: 700; font-family: var(--mono);
  padding: 2px 6px; border-radius: 4px; letter-spacing: 0.5px;
  display: none;
}
/* RELAY GRID */
.relay-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 12px;
  margin-bottom: 28px;
}
.relay-card {
  background: var(--bg2);
  border: 1px solid var(--border);
  border-radius: 14px;
  padding: 18px;
  transition: border-color 0.2s;
}
.relay-card.active { border-color: rgba(79,156,249,0.4); background: rgba(79,156,249,0.05); }
.relay-header {
  display: flex; align-items: center; justify-content: space-between;
  margin-bottom: 14px;
}
.relay-name {
  display: flex; align-items: center; gap: 8px;
  font-size: 14px; font-weight: 500;
}
.relay-icon { font-size: 18px; }
/* TOGGLE SWITCH */
.toggle {
  position: relative; width: 44px; height: 24px;
}
.toggle input { display: none; }
.toggle .track {
  position: absolute; inset: 0;
  background: var(--bg3); border: 1px solid var(--border2);
  border-radius: 12px; cursor: pointer; transition: all 0.25s;
}
.toggle input:checked + .track { background: var(--accent); border-color: var(--accent); }
.toggle .thumb {
  position: absolute; top: 3px; left: 3px;
  width: 16px; height: 16px; border-radius: 50%;
  background: var(--muted); transition: all 0.25s; pointer-events: none;
}
.toggle input:checked ~ .thumb { left: 23px; background: #fff; }
.relay-mode {
  display: flex; gap: 6px; margin-bottom: 10px;
}
.mode-btn {
  flex: 1; padding: 5px 8px; border-radius: 6px; cursor: pointer;
  font-size: 11px; font-weight: 500; font-family: var(--mono);
  border: 1px solid var(--border2); background: transparent; color: var(--muted);
  transition: all 0.15s;
}
.mode-btn.active { background: rgba(79,156,249,0.15); color: var(--accent); border-color: rgba(79,156,249,0.4); }
.relay-status {
  font-size: 11px; font-family: var(--mono);
  display: flex; align-items: center; gap: 5px;
}
.status-dot { width: 6px; height: 6px; border-radius: 50%; }
.status-dot.on { background: var(--success); box-shadow: 0 0 6px var(--success); }
.status-dot.off { background: var(--muted); }
/* DISTANCE GAUGE */
.distance-gauge {
  margin: 0 auto; text-align: center;
  padding: 20px;
  background: var(--bg2);
  border: 1px solid var(--border);
  border-radius: 14px;
  margin-bottom: 28px;
}
/* LOG CONSOLE */
.console {
  background: var(--bg2);
  border: 1px solid var(--border);
  border-radius: 14px;
  overflow: hidden;
}
.console-header {
  display: flex; align-items: center; justify-content: space-between;
  padding: 12px 16px;
  border-bottom: 1px solid var(--border);
  background: var(--bg3);
}
.console-title {
  font-size: 12px; font-family: var(--mono); color: var(--muted);
  display: flex; align-items: center; gap: 8px;
}
.console-dots { display: flex; gap: 5px; }
.console-dots span { width: 10px; height: 10px; border-radius: 50%; }
.console-dots span:nth-child(1) { background: #ff5f57; }
.console-dots span:nth-child(2) { background: #febc2e; }
.console-dots span:nth-child(3) { background: #28c840; }
#clear-log {
  font-size: 11px; font-family: var(--mono); color: var(--muted);
  background: none; border: none; cursor: pointer; padding: 2px 8px;
  border-radius: 4px; transition: color 0.2s;
}
#clear-log:hover { color: var(--text); }
#log {
  height: 160px; overflow-y: auto; padding: 12px 16px;
  font-family: var(--mono); font-size: 12px; line-height: 1.8;
  scrollbar-width: thin; scrollbar-color: var(--border) transparent;
}
.log-line { display: flex; gap: 12px; }
.log-time { color: var(--muted); flex-shrink: 0; }
.log-data { color: var(--accent2); }
.log-cmd { color: var(--purple); }
.log-error { color: var(--danger); }
.log-info { color: var(--muted); font-style: italic; }
/* ALERT BAR */
#alert-bar {
  display: none;
  background: rgba(248,113,113,0.12);
  border: 1px solid rgba(248,113,113,0.4);
  border-radius: 10px; padding: 12px 16px;
  margin-bottom: 18px;
  font-size: 13px; color: var(--danger);
  display: flex; align-items: center; gap: 10px;
}
#alert-bar.hidden { display: none !important; }
#alert-bar .alert-icon { font-size: 16px; }
/* NO SERIAL API WARNING */
#no-serial {
  background: rgba(251,191,36,0.1);
  border: 1px solid rgba(251,191,36,0.3);
  border-radius: 10px; padding: 14px 18px;
  font-size: 13px; color: var(--warn);
  margin-bottom: 20px; display: none;
}
#no-serial a { color: var(--accent); }
/* RESPONSIVE */
@media (max-width: 640px) {
  .sensor-grid { grid-template-columns: repeat(2, 1fr); }
  .relay-grid { grid-template-columns: 1fr; }
  header { flex-direction: column; gap: 14px; align-items: flex-start; }
}
</style>
</head>
<body>
<div class="grid-bg"></div>
<div class="app">
  <!-- HEADER -->
  <header>
    <div class="logo">
      <div class="logo-icon">🌱</div>
      <div>
        <h1>Station de contrôle</h1>
        <p>Arduino Mega · Serial Dashboard</p>
      </div>
    </div>
    <div class="status-bar">
      <div id="connection-status">
        <span class="dot" id="status-dot"></span>
        <span id="status-text">Déconnecté</span>
      </div>
      <button id="connect-btn" onclick="connectSerial()">⚡ Connecter</button>
      <button id="disconnect-btn" onclick="disconnectSerial()">✕ Déconnecter</button>
    </div>
  </header>
  <!-- NO WEB SERIAL WARNING -->
  <div id="no-serial" style="display:none">
    ⚠️ Web Serial API non supportée. Utilisez <strong>Chrome</strong> ou <strong>Edge</strong> (desktop).
    <a href="https://caniuse.com/web-serial" target="_blank">En savoir plus →</a>
  </div>
  <!-- ALERTS -->
  <div id="alert-bar" class="hidden">
    <span class="alert-icon">🚨</span>
    <span id="alert-text">Alerte détectée</span>
  </div>
  <!-- SENSORS -->
  <div class="section-title">Capteurs</div>
  <div class="sensor-grid">
    <div class="sensor-card" id="card-temp">
      <span class="alert-badge" id="badge-temp">CHAUD</span>
      <div class="icon">🌡️</div>
      <div class="label">Temp. DHT11</div>
      <div class="value" id="val-temp">—<span class="unit">°C</span></div>
      <div class="bar-wrap"><div class="bar-fill" id="bar-temp" style="width:0%;background:var(--accent)"></div></div>
    </div>
    <div class="sensor-card" id="card-hum">
      <span class="alert-badge" id="badge-hum">ÉLEVÉE</span>
      <div class="icon">💧</div>
      <div class="label">Humidité</div>
      <div class="value" id="val-hum">—<span class="unit">%</span></div>
      <div class="bar-wrap"><div class="bar-fill" id="bar-hum" style="width:0%;background:var(--accent)"></div></div>
    </div>
    <div class="sensor-card" id="card-temp2">
      <div class="icon">🔵</div>
      <div class="label">Temp. DS18B20</div>
      <div class="value" id="val-temp2">—<span class="unit">°C</span></div>
      <div class="bar-wrap"><div class="bar-fill" id="bar-temp2" style="width:0%;background:var(--purple)"></div></div>
    </div>
    <div class="sensor-card" id="card-gaz">
      <span class="alert-badge" id="badge-gaz">ALERTE</span>
      <div class="icon">🟠</div>
      <div class="label">Gaz / CO2</div>
      <div class="value" id="val-gaz">—</div>
      <div class="bar-wrap"><div class="bar-fill" id="bar-gaz" style="width:0%;background:var(--orange)"></div></div>
    </div>
    <div class="sensor-card" id="card-flamme">
      <span class="alert-badge" id="badge-flamme">FEU!</span>
      <div class="icon">🔥</div>
      <div class="label">Flamme</div>
      <div class="value" id="val-flamme">—</div>
      <div class="sub" id="sub-flamme">Aucune flamme</div>
    </div>
    <div class="sensor-card" id="card-pluie">
      <span class="alert-badge" id="badge-pluie">PLUIE</span>
      <div class="icon">🌧️</div>
      <div class="label">Pluie</div>
      <div class="value" id="val-pluie">—</div>
      <div class="sub" id="sub-pluie">Sec</div>
    </div>
    <div class="sensor-card" id="card-sol">
      <span class="alert-badge" id="badge-sol">SEC</span>
      <div class="icon">🌱</div>
      <div class="label">Sol</div>
      <div class="value" id="val-sol">—<span class="unit">%</span></div>
      <div class="bar-wrap"><div class="bar-fill" id="bar-sol" style="width:0%;background:var(--accent2)"></div></div>
    </div>
    <div class="sensor-card" id="card-eau">
      <span class="alert-badge" id="badge-eau">HAUT</span>
      <div class="icon">🪣</div>
      <div class="label">Niveau eau</div>
      <div class="value" id="val-eau">—<span class="unit">%</span></div>
      <div class="bar-wrap"><div class="bar-fill" id="bar-eau" style="width:0%;background:var(--accent)"></div></div>
    </div>
    <div class="sensor-card" id="card-dist" style="grid-column: span 1;">
      <div class="icon">📡</div>
      <div class="label">Distance</div>
      <div class="value" id="val-dist">—<span class="unit">cm</span></div>
      <div class="sub" id="sub-dist">Aucun objet</div>
    </div>
  </div>
  <!-- RELAYS -->
  <div class="section-title">Actionneurs</div>
  <div class="relay-grid">
    <div class="relay-card" id="relay-card-1">
      <div class="relay-header">
        <div class="relay-name">
          <span class="relay-icon">💨</span>
          <span>Ventilateur</span>
        </div>
        <label class="toggle">
          <input type="checkbox" id="toggle-1" onchange="manualToggle(1, this.checked)">
          <div class="track"></div>
          <div class="thumb"></div>
        </label>
      </div>
      <div class="relay-mode">
        <button class="mode-btn active" id="mode-1-auto" onclick="setMode(1,'auto')">AUTO</button>
        <button class="mode-btn" id="mode-1-manual" onclick="setMode(1,'manual')">MANU</button>
      </div>
      <div class="relay-status">
        <span class="status-dot off" id="dot-1"></span>
        <span id="state-1">Éteint</span>
      </div>
    </div>
    <div class="relay-card" id="relay-card-2">
      <div class="relay-header">
        <div class="relay-name">
          <span class="relay-icon">🚿</span>
          <span>Pompe eau</span>
        </div>
        <label class="toggle">
          <input type="checkbox" id="toggle-2" onchange="manualToggle(2, this.checked)">
          <div class="track"></div>
          <div class="thumb"></div>
        </label>
      </div>
      <div class="relay-mode">
        <button class="mode-btn active" id="mode-2-auto" onclick="setMode(2,'auto')">AUTO</button>
        <button class="mode-btn" id="mode-2-manual" onclick="setMode(2,'manual')">MANU</button>
      </div>
      <div class="relay-status">
        <span class="status-dot off" id="dot-2"></span>
        <span id="state-2">Éteint</span>
      </div>
    </div>
    <div class="relay-card" id="relay-card-3">
      <div class="relay-header">
        <div class="relay-name">
          <span class="relay-icon">🔔</span>
          <span>Buzzer</span>
        </div>
        <label class="toggle">
          <input type="checkbox" id="toggle-3" onchange="manualToggle(3, this.checked)">
          <div class="track"></div>
          <div class="thumb"></div>
        </label>
      </div>
      <div class="relay-mode">
        <button class="mode-btn active" id="mode-3-auto" onclick="setMode(3,'auto')">AUTO</button>
        <button class="mode-btn" id="mode-3-manual" onclick="setMode(3,'manual')">MANU</button>
      </div>
      <div class="relay-status">
        <span class="status-dot off" id="dot-3"></span>
        <span id="state-3">Éteint</span>
      </div>
    </div>
    <div class="relay-card" id="relay-card-4">
      <div class="relay-header">
        <div class="relay-name">
          <span class="relay-icon">⚡</span>
          <span>Relais 4</span>
        </div>
        <label class="toggle">
          <input type="checkbox" id="toggle-4" onchange="manualToggle(4, this.checked)">
          <div class="track"></div>
          <div class="thumb"></div>
        </label>
      </div>
      <div class="relay-mode">
        <button class="mode-btn active" id="mode-4-manual" onclick="setMode(4,'manual')" style="pointer-events:none">MANU</button>
      </div>
      <div class="relay-status">
        <span class="status-dot off" id="dot-4"></span>
        <span id="state-4">Éteint</span>
      </div>
    </div>
    <div class="relay-card" id="relay-card-5">
      <div class="relay-header">
        <div class="relay-name">
          <span class="relay-icon">⚡</span>
          <span>Relais 5</span>
        </div>
        <label class="toggle">
          <input type="checkbox" id="toggle-5" onchange="manualToggle(5, this.checked)">
          <div class="track"></div>
          <div class="thumb"></div>
        </label>
      </div>
      <div class="relay-mode">
        <button class="mode-btn active" id="mode-5-manual" onclick="setMode(5,'manual')" style="pointer-events:none">MANU</button>
      </div>
      <div class="relay-status">
        <span class="status-dot off" id="dot-5"></span>
        <span id="state-5">Éteint</span>
      </div>
    </div>
  </div>
  <!-- CONSOLE -->
  <div class="console">
    <div class="console-header">
      <div class="console-dots">
        <span></span><span></span><span></span>
      </div>
      <div class="console-title">terminal · serial monitor</div>
      <button id="clear-log" onclick="clearLog()">effacer</button>
    </div>
    <div id="log">
      <div class="log-line"><span class="log-info">— En attente de connexion série...</span></div>
    </div>
  </div>
</div>
<script>
let port = null, reader = null, writer = null;
let buffer = '';
let modes = { 1: 'auto', 2: 'auto', 3: 'auto', 4: 'manual', 5: 'manual' };
// Check Web Serial API
if (!('serial' in navigator)) {
  document.getElementById('no-serial').style.display = 'block';
  document.getElementById('connect-btn').disabled = true;
  document.getElementById('connect-btn').style.opacity = '0.4';
}
async function connectSerial() {
  try {
    port = await navigator.serial.requestPort();
    await port.open({ baudRate: 9600 });
    setStatus('connected', 'Connecté');
    document.getElementById('connect-btn').style.display = 'none';
    document.getElementById('disconnect-btn').style.display = 'block';
    logInfo('Port série ouvert @ 9600 baud');
    readLoop();
    const encoder = new TextEncoderStream();
    encoder.readable.pipeTo(port.writable);
    writer = encoder.writable.getWriter();
  } catch (e) {
    logError('Connexion échouée: ' + e.message);
    setStatus('error', 'Erreur');
  }
}
async function disconnectSerial() {
  try {
    if (reader) { await reader.cancel(); reader = null; }
    if (writer) { await writer.close(); writer = null; }
    if (port) { await port.close(); port = null; }
    setStatus('', 'Déconnecté');
    document.getElementById('connect-btn').style.display = 'block';
    document.getElementById('disconnect-btn').style.display = 'none';
    logInfo('Port série fermé');
  } catch (e) {
    logError('Déconnexion: ' + e.message);
  }
}
async function readLoop() {
  const decoder = new TextDecoderStream();
  port.readable.pipeTo(decoder.writable);
  reader = decoder.readable.getReader();
  try {
    while (true) {
      const { value, done } = await reader.read();
      if (done) break;
      buffer += value;
      let lines = buffer.split('\n');
      buffer = lines.pop();
      for (const line of lines) {
        const trimmed = line.trim();
        if (trimmed.startsWith('{')) {
          try {
            const data = JSON.parse(trimmed);
            updateDashboard(data);
            logData(trimmed);
          } catch(e) {}
        }
      }
    }
  } catch (e) {
    if (e.name !== 'AbortError') logError('Lecture: ' + e.message);
  }
}
async function sendCmd(cmd) {
  if (!writer) { logError('Non connecté'); return; }
  await writer.write(cmd + '\n');
  logCmd('→ ' + cmd);
}
function manualToggle(num, state) {
  if (modes[num] !== 'manual') return;
  sendCmd(`R${num}:${state ? 1 : 0}`);
}
function setMode(num, mode) {
  modes[num] = mode;
  document.getElementById(`mode-${num}-auto`) && document.getElementById(`mode-${num}-auto`).classList.toggle('active', mode === 'auto');
  document.getElementById(`mode-${num}-manual`) && document.getElementById(`mode-${num}-manual`).classList.toggle('active', mode === 'manual');
  if (num <= 3) {
    sendCmd(`A${num}:${mode === 'auto' ? 1 : 0}`);
  }
  const toggle = document.getElementById(`toggle-${num}`);
  if (toggle) toggle.disabled = (mode === 'auto');
}
function updateDashboard(d) {
  // TEMPERATURE DHT
  if (d.temp !== undefined) {
    const v = d.temp;
    setText('val-temp', v.toFixed(1) + '<span class="unit">°C</span>');
    setBar('bar-temp', Math.min(v/60*100, 100), v > 38 ? 'var(--danger)' : v > 30 ? 'var(--warn)' : 'var(--accent)');
    setAlert('card-temp', 'badge-temp', v > 38);
  }
  // HUMIDITY
  if (d.hum !== undefined) {
    const v = d.hum;
    setText('val-hum', v.toFixed(1) + '<span class="unit">%</span>');
    setBar('bar-hum', v, v > 70 ? 'var(--accent)' : 'var(--accent2)');
    setAlert('card-hum', 'badge-hum', v > 70);
  }
  // TEMP DS18B20
  if (d.temp2 !== undefined) {
    const v = d.temp2;
    setText('val-temp2', v.toFixed(1) + '<span class="unit">°C</span>');
    setBar('bar-temp2', Math.min(v/60*100, 100), v > 38 ? 'var(--danger)' : 'var(--purple)');
  }
  // GAS
  if (d.gaz !== undefined) {
    const v = d.gaz;
    setText('val-gaz', v);
    setBar('bar-gaz', Math.min(v/1023*100, 100), v > 400 ? 'var(--danger)' : 'var(--orange)');
    setAlert('card-gaz', 'badge-gaz', v > 400);
  }
  // FLAME
  if (d.flamme !== undefined) {
    const v = d.flamme;
    const isFlame = v < 200;
    setText('val-flamme', v);
    document.getElementById('sub-flamme').textContent = isFlame ? '🔥 FLAMME DÉTECTÉE' : 'Aucune flamme';
    document.getElementById('sub-flamme').style.color = isFlame ? 'var(--danger)' : 'var(--muted)';
    setAlert('card-flamme', 'badge-flamme', isFlame);
  }
  // RAIN
  if (d.pluie !== undefined) {
    const v = d.pluie;
    const isRain = v < 600;
    setText('val-pluie', v);
    document.getElementById('sub-pluie').textContent = isRain ? '☔ Pluie détectée' : 'Sec';
    document.getElementById('sub-pluie').style.color = isRain ? 'var(--accent)' : 'var(--muted)';
    setAlert('card-pluie', 'badge-pluie', isRain);
  }
  // SOIL
  if (d.sol !== undefined) {
    const v = d.sol;
    setText('val-sol', v + '<span class="unit">%</span>');
    setBar('bar-sol', v, v < 30 ? 'var(--danger)' : 'var(--accent2)');
    setAlert('card-sol', 'badge-sol', v < 30);
  }
  // WATER LEVEL
  if (d.eau !== undefined) {
    const v = d.eau;
    setText('val-eau', v + '<span class="unit">%</span>');
    setBar('bar-eau', v, v > 80 ? 'var(--danger)' : 'var(--accent)');
    setAlert('card-eau', 'badge-eau', v > 80);
  }
  // DISTANCE
  if (d.dist !== undefined) {
    const v = d.dist;
    const near = v > 0 && v < 20;
    setText('val-dist', (v < 0 ? '—' : v) + '<span class="unit">cm</span>');
    document.getElementById('sub-dist').textContent = near ? `⚠️ Objet à ${v}cm` : (v < 0 ? 'Hors portée' : `${v}cm — libre`);
    document.getElementById('sub-dist').style.color = near ? 'var(--warn)' : 'var(--muted)';
  }
  // RELAY STATES
  [1,2,3].forEach(i => {
    const key = ['vent','pompe','buzzer'][i-1];
    if (d[key] !== undefined) updateRelayUI(i, d[key] === 1);
  });
  // GLOBAL ALERTS
  const hasAlert = (d.flamme !== undefined && d.flamme < 200) ||
                   (d.gaz !== undefined && d.gaz > 400);
  const alertBar = document.getElementById('alert-bar');
  if (hasAlert) {
    alertBar.classList.remove('hidden');
    const msgs = [];
    if (d.flamme < 200) msgs.push('🔥 Flamme détectée');
    if (d.gaz > 400) msgs.push('☁️ Gaz > 400');
    document.getElementById('alert-text').textContent = msgs.join(' · ');
  } else {
    alertBar.classList.add('hidden');
  }
}
function updateRelayUI(num, isOn) {
  const dot = document.getElementById('dot-' + num);
  const state = document.getElementById('state-' + num);
  const card = document.getElementById('relay-card-' + num);
  const toggle = document.getElementById('toggle-' + num);
  if (dot) { dot.className = 'status-dot ' + (isOn ? 'on' : 'off'); }
  if (state) state.textContent = isOn ? 'En marche' : 'Éteint';
  if (card) card.classList.toggle('active', isOn);
  if (toggle && modes[num] === 'auto') {
    toggle.checked = isOn;
  }
}
// UI helpers
function setText(id, html) {
  const el = document.getElementById(id);
  if (el) el.innerHTML = html;
}
function setBar(id, pct, color) {
  const el = document.getElementById(id);
  if (!el) return;
  el.style.width = Math.max(0, Math.min(100, pct)) + '%';
  el.style.background = color;
}
function setAlert(cardId, badgeId, show) {
  const card = document.getElementById(cardId);
  const badge = document.getElementById(badgeId);
  if (card) card.classList.toggle('alert', show);
  if (badge) badge.style.display = show ? 'block' : 'none';
}
// LOG
function logData(msg) { addLog('log-data', msg); }
function logCmd(msg) { addLog('log-cmd', msg); }
function logError(msg) { addLog('log-error', msg); }
function logInfo(msg) { addLog('log-info', msg); }
function addLog(cls, msg) {
  const log = document.getElementById('log');
  const now = new Date();
  const time = now.toTimeString().slice(0,8);
  const div = document.createElement('div');
  div.className = 'log-line';
  div.innerHTML = `<span class="log-time">${time}</span><span class="${cls}">${escHtml(msg.length > 120 ? msg.slice(0,120)+'…' : msg)}</span>`;
  log.appendChild(div);
  log.scrollTop = log.scrollHeight;
  while (log.children.length > 100) log.removeChild(log.firstChild);
}
function escHtml(s) {
  return s.replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;');
}
function clearLog() {
  document.getElementById('log').innerHTML = '';
}
function setStatus(type, text) {
  const dot = document.getElementById('status-dot');
  const label = document.getElementById('status-text');
  dot.className = 'dot ' + type;
  label.textContent = text;
}
// Demo data when not connected
function loadDemoData() {
  if (port) return;
  updateDashboard({
    temp: 24.5 + (Math.random()-0.5)*2,
    hum: 55 + (Math.random()-0.5)*6,
    temp2: 23.8 + (Math.random()-0.5)*1,
    gaz: 150 + Math.floor(Math.random()*80),
    flamme: 800 + Math.floor(Math.random()*200),
    pluie: 800 + Math.floor(Math.random()*100),
    sol: 80,
    eau: 20,
    dist: 45 + Math.floor(Math.random()*20),
    vent: 0, pompe: 0, buzzer: 0
  });
}
setInterval(loadDemoData, 2500);
loadDemoData();
</script>
</body>
</html>


