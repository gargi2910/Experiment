# Experiment
Life Experiment Tracker
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Lab Rat — Life Experiment Tracker</title>
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:ital,wght@0,400;0,700;1,400&family=Syne:wght@400;600;700;800&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0d0e0f;
    --surface: #141516;
    --surface2: #1c1d1f;
    --border: #2a2b2d;
    --accent: #c8f135;
    --accent2: #f13565;
    --accent3: #35c8f1;
    --text: #f0f0ee;
    --muted: #6b6c6e;
    --mood: #f1a535;
    --prod: #c8f135;
    --focus: #35c8f1;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'Syne', sans-serif;
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* Grid background decoration */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image:
      linear-gradient(rgba(200,241,53,0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(200,241,53,0.03) 1px, transparent 1px);
    background-size: 40px 40px;
    pointer-events: none;
    z-index: 0;
  }

  .container {
    max-width: 960px;
    margin: 0 auto;
    padding: 0 24px 80px;
    position: relative;
    z-index: 1;
  }

  header {
    padding: 40px 0 32px;
    border-bottom: 1px solid var(--border);
    margin-bottom: 40px;
    display: flex;
    align-items: flex-end;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 16px;
  }

  .logo-block h1 {
    font-size: clamp(2rem, 5vw, 3.2rem);
    font-weight: 800;
    letter-spacing: -0.04em;
    line-height: 1;
  }

  .logo-block h1 span { color: var(--accent); }

  .logo-block p {
    font-family: 'Space Mono', monospace;
    font-size: 0.72rem;
    color: var(--muted);
    margin-top: 6px;
    letter-spacing: 0.1em;
    text-transform: uppercase;
  }

  .header-stats { display: flex; gap: 24px; }

  .stat-pill {
    text-align: center;
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 10px 18px;
  }

  .stat-pill .val {
    font-size: 1.4rem;
    font-weight: 700;
    color: var(--accent);
    line-height: 1;
  }

  .stat-pill .lbl {
    font-family: 'Space Mono', monospace;
    font-size: 0.6rem;
    color: var(--muted);
    text-transform: uppercase;
    letter-spacing: 0.1em;
    margin-top: 4px;
  }

  .btn-new {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    background: var(--accent);
    color: #0d0e0f;
    border: none;
    border-radius: 6px;
    padding: 12px 22px;
    font-family: 'Syne', sans-serif;
    font-size: 0.88rem;
    font-weight: 700;
    cursor: pointer;
    letter-spacing: 0.02em;
    transition: transform 0.15s, box-shadow 0.15s;
    margin-bottom: 32px;
  }

  .btn-new:hover {
    transform: translateY(-2px);
    box-shadow: 0 6px 24px rgba(200,241,53,0.25);
  }

  /* Modal Styles */
  .overlay {
    display: none;
    position: fixed;
    inset: 0;
    background: rgba(0,0,0,0.7);
    z-index: 100;
    backdrop-filter: blur(4px);
    align-items: center;
    justify-content: center;
    padding: 24px;
  }

  .overlay.open { display: flex; }

  .modal {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 32px;
    width: 100%;
    max-width: 520px;
    max-height: 90vh;
    overflow-y: auto;
    animation: slideUp 0.2s ease;
  }

  @keyframes slideUp {
    from { transform: translateY(16px); opacity: 0; }
    to { transform: translateY(0); opacity: 1; }
  }

  .modal h2 { font-size: 1.3rem; margin-bottom: 24px; }

  .field { margin-bottom: 18px; }
  .field label {
    display: block;
    font-family: 'Space Mono', monospace;
    font-size: 0.65rem;
    text-transform: uppercase;
    color: var(--muted);
    margin-bottom: 7px;
  }

  .field input, .field textarea, .field select {
    width: 100%;
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 10px 14px;
    color: var(--text);
    outline: none;
  }

  /* Rating Stars */
  .stars { display: flex; gap: 6px; }
  .star { font-size: 1.3rem; cursor: pointer; opacity: 0.25; }
  .star.lit { opacity: 1; color: var(--accent); }

  /* Experiment Grid */
  .experiments-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
    gap: 16px;
  }

  .exp-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 10px;
    padding: 20px;
    position: relative;
    cursor: pointer;
    transition: transform 0.15s;
  }

  .exp-card:hover { transform: translateY(-3px); border-color: var(--muted); }

  .status-running { border-top: 3px solid var(--accent3); }
  .status-completed { border-top: 3px solid var(--accent); }
  .status-abandoned { border-top: 3px solid var(--accent2); }

  .card-actions {
    position: absolute;
    top: 10px;
    right: 10px;
    display: none;
    gap: 5px;
  }

  .exp-card:hover .card-actions { display: flex; }

  .card-btn {
    background: var(--surface2);
    border: 1px solid var(--border);
    padding: 4px 8px;
    border-radius: 4px;
    font-size: 0.7rem;
  }
</style>
</head>
<body>

<div class="container">
  <header>
    <div class="logo-block">
      <h1>Lab <span>Rat</span> 🐀</h1>
      <p>Personal life experiment tracker</p>
    </div>
    <div class="header-stats">
      <div class="stat-pill"><div class="val" id="stat-total">0</div><div class="lbl">Total</div></div>
      <div class="stat-pill"><div class="val" id="stat-running">0</div><div class="lbl">Running</div></div>
    </div>
  </header>

  <button class="btn-new" onclick="openModal()">⚗️ New Experiment</button>

  <div class="experiments-grid" id="grid">
    </div>
</div>

<div class="overlay" id="modal-overlay">
  <div class="modal">
    <h2 id="modal-title">New Experiment</h2>
    <div class="field">
      <label>Name</label>
      <input type="text" id="exp-name">
    </div>
    <div class="field">
      <label>Emoji</label>
      <input type="text" id="exp-emoji" placeholder="🧪">
    </div>
    <div class="field">
      <label>Status</label>
      <select id="exp-status">
        <option value="running">Running</option>
        <option value="completed">Completed</option>
        <option value="abandoned">Abandoned</option>
      </select>
    </div>
    <div class="field">
      <label>Notes</label>
      <textarea id="exp-notes"></textarea>
    </div>
    <button class="btn-new" style="width:100%" onclick="saveExperiment()">Save</button>
    <button onclick="closeModal()" style="background:none; border:none; color:var(--muted); width:100%; margin-top:10px; cursor:pointer;">Cancel</button>
  </div>
</div>

<script>
  let experiments = JSON.parse(localStorage.getItem('labrat-data') || '[]');

  function save() {
    localStorage.setItem('labrat-data', JSON.stringify(experiments));
    render();
  }

  function openModal() {
    document.getElementById('modal-overlay').classList.add('open');
  }

  function closeModal() {
    document.getElementById('modal-overlay').classList.remove('open');
  }

  function saveExperiment() {
    const name = document.getElementById('exp-name').value;
    if (!name) return alert("Name required");

    const newExp = {
      id: Date.now(),
      name: name,
      emoji: document.getElementById('exp-emoji').value || '🧪',
      status: document.getElementById('exp-status').value,
      notes: document.getElementById('exp-notes').value
    };

    experiments.unshift(newExp);
    save();
    closeModal();
  }

  function deleteExp(id) {
    experiments = experiments.filter(e => e.id !== id);
    save();
  }

  function render() {
    const grid = document.getElementById('grid');
    document.getElementById('stat-total').textContent = experiments.length;
    document.getElementById('stat-running').textContent = experiments.filter(e => e.status === 'running').length;

    grid.innerHTML = experiments.map(exp => `
      <div class="exp-card status-${exp.status}">
        <div class="card-actions">
           <button class="card-btn" onclick="deleteExp(${exp.id})">🗑</button>
        </div>
        <div style="font-size: 2rem; margin-bottom: 10px;">${exp.emoji}</div>
        <div style="font-weight: 800; margin-bottom: 5px;">${exp.name}</div>
        <div style="font-size: 0.8rem; color: var(--muted);">${exp.notes}</div>
      </div>
    `).join('');
  }

  // Initial Render
  render();
</script>
</body>
</html>
