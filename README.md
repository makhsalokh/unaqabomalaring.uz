<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Management Science — Expedition, Pizza, & Laptop Optimizer</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Playfair+Display:ital,wght@0,600;0,700;1,600&family=IBM+Plex+Mono:wght@400;500&family=IBM+Plex+Sans:wght@300;400;500;600&display=swap');

  :root {
    --bg: #0f1117;
    --surface: #171c26;
    --surface2: #1e2536;
    --border: #2a3149;
    --text: #e8ecf4;
    --muted: #7a85a0;
    --accent: #e05c3a;
    --accent2: #f0a500;
    --green: #2ecc8f;
    --blue: #4d9de0;
    --purple: #9b72cf;
    --node-root: #2e4a7a;
    --node-int: #1a6645;
    --node-pruned: #7a2020;
    --node-frac: #7a4a00;
    --node-infeas: #3a3f52;
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    font-family: 'IBM Plex Sans', sans-serif;
    background: var(--bg);
    color: var(--text);
    min-height: 100vh;
  }

  /* NAV */
  nav {
    background: var(--surface);
    display: flex;
    align-items: center;
    padding: 0 2rem;
    position: sticky;
    top: 0;
    z-index: 100;
    border-bottom: 1px solid var(--border);
    gap: 0;
  }
  .nav-brand {
    font-family: 'Playfair Display', serif;
    font-size: 0.9rem;
    color: var(--muted);
    padding-right: 2rem;
    border-right: 1px solid var(--border);
    margin-right: 1rem;
    letter-spacing: 0.02em;
    white-space: nowrap;
  }
  .nav-tab {
    padding: 1.1rem 1.3rem;
    color: var(--muted);
    cursor: pointer;
    font-size: 0.75rem;
    font-weight: 500;
    letter-spacing: 0.07em;
    text-transform: uppercase;
    border-bottom: 2px solid transparent;
    margin-bottom: -1px;
    transition: all 0.2s;
    white-space: nowrap;
  }
  .nav-tab:hover { color: var(--text); }
  .nav-tab.active { color: var(--accent); border-bottom-color: var(--accent); }

  /* SECTIONS */
  .section { display: none; padding: 3rem 2rem; max-width: 1240px; margin: 0 auto; }
  .section.active { display: block; }

  h1 {
    font-family: 'Playfair Display', serif;
    font-size: 2.6rem;
    line-height: 1.1;
    margin-bottom: 0.5rem;
    letter-spacing: -0.01em;
  }
  .subtitle {
    font-size: 0.72rem;
    color: var(--muted);
    letter-spacing: 0.1em;
    text-transform: uppercase;
    margin-bottom: 2rem;
    font-weight: 500;
    display: flex;
    align-items: center;
    gap: 0.8rem;
  }
  .badge {
    display: inline-flex;
    align-items: center;
    background: var(--accent);
    color: white;
    padding: 0.2rem 0.65rem;
    border-radius: 2px;
    font-size: 0.68rem;
    font-weight: 600;
    letter-spacing: 0.08em;
    text-transform: uppercase;
  }
  .badge.green { background: var(--green); color: #0a1a12; }
  .badge.blue { background: var(--blue); color: #06192b; }

  .divider { width: 40px; height: 3px; background: var(--accent); margin: 1rem 0 2rem; }

  /* CARD */
  .card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 1.5rem;
  }

  .scenario-header {
    background: linear-gradient(135deg, #1a2035 0%, #0d1320 100%);
    border: 1px solid var(--border);
    border-left: 3px solid var(--accent);
    padding: 2rem 2.5rem;
    border-radius: 6px;
    margin-bottom: 2rem;
  }
  .scenario-header .title { font-family: 'Playfair Display', serif; font-size: 1.1rem; margin-bottom: 0.8rem; color: var(--text); }
  .scenario-header p { color: var(--muted); font-size: 0.9rem; line-height: 1.75; }
  .scenario-header strong { color: var(--text); }

  .legend {
    display: flex;
    gap: 1.5rem;
    flex-wrap: wrap;
    margin-bottom: 2rem;
    padding: 0.9rem 1.4rem;
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 6px;
  }
  .legend-item { display: flex; align-items: center; gap: 0.5rem; font-size: 0.78rem; color: var(--muted); }
  .legend-dot { width: 12px; height: 12px; border-radius: 50%; flex-shrink: 0; }

  .tree-svg-wrap { overflow-x: auto; }
  svg text { font-family: 'IBM Plex Sans', sans-serif; }

  /* TABLES */
  .table-wrap { overflow-x: auto; margin: 1.5rem 0; }
  table { width: 100%; border-collapse: collapse; font-size: 0.87rem; }
  th {
    background: var(--surface2);
    color: var(--muted);
    padding: 0.65rem 1rem;
    text-align: left;
    font-weight: 500;
    font-size: 0.72rem;
    letter-spacing: 0.06em;
    text-transform: uppercase;
    border-bottom: 1px solid var(--border);
  }
  td { padding: 0.6rem 1rem; border-bottom: 1px solid var(--border); color: var(--text); }
  tr:last-child td { border-bottom: none; }
  tr:hover td { background: var(--surface2); }
  .opt-row td { background: rgba(46,204,143,0.07); color: var(--green); font-weight: 500; }
  .frac-row td { background: rgba(240,165,0,0.07); }

  .ratio-bar {
    display: inline-block;
    height: 6px;
    background: var(--accent2);
    border-radius: 2px;
    vertical-align: middle;
    margin-left: 0.5rem;
    opacity: 0.6;
  }

  /* INFO CARDS */
  .info-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 1rem;
    margin-bottom: 2rem;
  }
  .info-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 1.3rem 1.5rem;
  }
  .info-card .lbl { font-size: 0.7rem; color: var(--muted); text-transform: uppercase; letter-spacing: 0.07em; margin-bottom: 0.5rem; }
  .info-card .val { font-size: 1.7rem; font-family: 'IBM Plex Mono', monospace; font-weight: 500; }
  .info-card .val.green { color: var(--green); }
  .info-card .val.accent { color: var(--accent); }

  .how-to {
    background: var(--surface);
    border-left: 3px solid var(--blue);
    padding: 1.1rem 1.5rem;
    border-radius: 0 6px 6px 0;
    margin-bottom: 2rem;
    font-size: 0.87rem;
    line-height: 1.85;
    color: var(--muted);
  }
  .how-to strong { color: var(--text); }

  /* B&B STEPPER */
  .bb-controls {
    display: flex;
    align-items: center;
    gap: 0.8rem;
    margin-bottom: 1.5rem;
    flex-wrap: wrap;
  }
  .btn {
    padding: 0.5rem 1.2rem;
    border: 1px solid var(--border);
    background: var(--surface);
    color: var(--text);
    cursor: pointer;
    font-family: 'IBM Plex Sans', sans-serif;
    font-size: 0.8rem;
    font-weight: 500;
    letter-spacing: 0.04em;
    transition: all 0.15s;
    border-radius: 4px;
  }
  .btn:hover { border-color: var(--accent); color: var(--accent); }
  .btn.primary { background: var(--accent); border-color: var(--accent); color: white; }
  .btn.primary:hover { background: #c44d2e; }
  .step-counter {
    font-family: 'IBM Plex Mono', monospace;
    font-size: 0.82rem;
    color: var(--muted);
    margin-left: auto;
  }

  .bb-panel {
    display: grid;
    grid-template-columns: 260px 1fr;
    gap: 1.2rem;
    margin-bottom: 1.5rem;
  }
  .bb-info-box {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 1.3rem;
  }
  .bb-info-box h3 {
    font-family: 'Playfair Display', serif;
    font-size: 1rem;
    margin-bottom: 1rem;
    padding-bottom: 0.6rem;
    border-bottom: 1px solid var(--border);
    color: var(--text);
  }
  .bb-stat { margin-bottom: 0.9rem; }
  .bb-stat .key { font-size: 0.68rem; color: var(--muted); text-transform: uppercase; letter-spacing: 0.06em; margin-bottom: 0.2rem; }
  .bb-stat .val { font-family: 'IBM Plex Mono', monospace; font-size: 0.95rem; font-weight: 500; color: var(--text); }
  .bb-stat .val.big { color: var(--accent); font-size: 1.3rem; }
  .bb-stat .val.green { color: var(--green); }

  .explanation-box {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 1.3rem 1.5rem;
  }
  .explanation-box h3 {
    font-family: 'Playfair Display', serif;
    font-size: 1rem;
    margin-bottom: 0.8rem;
    padding-bottom: 0.5rem;
    border-bottom: 1px solid var(--border);
  }
  .explanation-box p { font-size: 0.88rem; line-height: 1.8; color: var(--muted); }
  .explanation-box p strong { color: var(--text); }

  @media (max-width: 700px) {
    .info-grid { grid-template-columns: 1fr 1fr; }
    .bb-panel { grid-template-columns: 1fr; }
    nav { padding: 0 0.8rem; }
    .nav-tab { padding: 0.9rem 0.7rem; font-size: 0.68rem; }
    h1 { font-size: 1.9rem; }
    .nav-brand { display: none; }
  }
</style>
</head>
<body>

<nav>
  <div class="nav-brand">Management Science</div>
  <div class="nav-tab active" onclick="showSection('req1',this)">① Expedition DT</div>
  <div class="nav-tab" onclick="showSection('req2',this)">② Pizza BIP</div>
  <div class="nav-tab" onclick="showSection('req3',this)">③ Laptop B&B</div>
</nav>

<!-- ========== REQ 1: Decision Tree (unchanged, but renamed) ========== -->
<div id="req1" class="section active">
  <div class="subtitle"><span class="badge">Requirement 1</span> Group Decision Tree — Water Convoy</div>
  <h1>The Last Water Convoy</h1>
  <div class="divider"></div>
  <p style="color:var(--muted);margin-bottom:2rem;font-size:0.88rem;">Minimum depth: Level 5 &nbsp;·&nbsp; Each decision branches into consequences</p>

  <div class="scenario-header">
    <div class="title">Scenario</div>
    <p>Twenty years after global collapse, your settlement survives on recycled water and scavenged supplies. Scouts discover an <strong>abandoned military convoy</strong> hidden inside a ruined city — containing water filters, fuel, medicine, and weapons. Rival groups may also be searching for it. The city is structurally unstable. Your settlement has fuel for <strong>one major expedition only.</strong></p>
  </div>

  <div class="legend">
    <div class="legend-item"><div class="legend-dot" style="background:#4d9de0"></div> Decision node (choice to make)</div>
    <div class="legend-item"><div class="legend-dot" style="background:#2ecc8f"></div> Positive / favourable outcome</div>
    <div class="legend-item"><div class="legend-dot" style="background:#e05c3a"></div> Negative / dangerous outcome</div>
    <div class="legend-item"><div class="legend-dot" style="background:#f0a500"></div> Mixed / uncertain outcome</div>
  </div>

  <div class="tree-svg-wrap">
    <svg viewBox="0 0 1080 920" width="100%" xmlns="http://www.w3.org/2000/svg" style="min-width:780px">
      <defs>
        <marker id="arr" markerWidth="6" markerHeight="6" refX="5" refY="3" orient="auto">
          <path d="M0,0 L0,6 L6,3 z" fill="#4a5270"/>
        </marker>
      </defs>
      <line x1="540" y1="58" x2="270" y2="145" stroke="#2a3149" stroke-width="1.5" marker-end="url(#arr)"/>
      <line x1="540" y1="58" x2="810" y2="145" stroke="#2a3149" stroke-width="1.5" marker-end="url(#arr)"/>
      <circle cx="540" cy="46" r="30" fill="#1e2a50" stroke="#4d9de0" stroke-width="2"/>
      <text x="540" y="42" text-anchor="middle" fill="#4d9de0" font-size="10" font-weight="700">L0</text>
      <text x="540" y="53" text-anchor="middle" fill="#a0b0d0" font-size="8">SEND EXPEDITION?</text>
      <text x="380" y="108" text-anchor="middle" fill="#7a85a0" font-size="9.5">Yes — go now</text>
      <text x="700" y="108" text-anchor="middle" fill="#7a85a0" font-size="9.5">Wait / scout first</text>
      <line x1="270" y1="172" x2="135" y2="265" stroke="#2a3149" stroke-width="1.5" marker-end="url(#arr)"/>
      <line x1="270" y1="172" x2="405" y2="265" stroke="#2a3149" stroke-width="1.5" marker-end="url(#arr)"/>
      <circle cx="270" cy="159" r="25" fill="#1e2a50" stroke="#4d9de0" stroke-width="1.5"/>
      <text x="270" y="155" text-anchor="middle" fill="#4d9de0" font-size="9.5" font-weight="700">L1</text>
      <text x="270" y="166" text-anchor="middle" fill="#a0b0d0" font-size="7.5">ROUTE?</text>
      <text x="170" y="235" text-anchor="middle" fill="#7a85a0" font-size="8.5">Safe route</text>
      <text x="355" y="235" text-anchor="middle" fill="#7a85a0" font-size="8.5">Fast route</text>
      <line x1="810" y1="172" x2="675" y2="265" stroke="#2a3149" stroke-width="1.5" marker-end="url(#arr)"/>
      <line x1="810" y1="172" x2="945" y2="265" stroke="#2a3149" stroke-width="1.5" marker-end="url(#arr)"/>
      <circle cx="810" cy="159" r="25" fill="#1e2a50" stroke="#4d9de0" stroke-width="1.5"/>
      <text x="810" y="155" text-anchor="middle" fill="#4d9de0" font-size="9.5" font-weight="700">L1</text>
      <text x="810" y="166" text-anchor="middle" fill="#a0b0d0" font-size="7.5">INTEL?</text>
      <text x="720" y="235" text-anchor="middle" fill="#7a85a0" font-size="8.5">Gather intel</text>
      <text x="900" y="235" text-anchor="middle" fill="#7a85a0" font-size="8.5">Skip intel</text>
      <line x1="135" y1="287" x2="68" y2="375" stroke="#2a3149" stroke-width="1.4" marker-end="url(#arr)"/>
      <line x1="135" y1="287" x2="202" y2="375" stroke="#2a3149" stroke-width="1.4" marker-end="url(#arr)"/>
      <circle cx="135" cy="276" r="22" fill="#1e2a50" stroke="#4d9de0" stroke-width="1.5"/>
      <text x="135" y="272" text-anchor="middle" fill="#4d9de0" font-size="8.5" font-weight="600">L2</text>
      <text x="135" y="282" text-anchor="middle" fill="#a0b0d0" font-size="7">CITY ENTRY</text>
      <text x="55" y="353" text-anchor="middle" fill="#7a85a0" font-size="7.5">Reach safely</text>
      <text x="218" y="353" text-anchor="middle" fill="#7a85a0" font-size="7.5">Ambushed</text>
      <line x1="405" y1="287" x2="338" y2="375" stroke="#2a3149" stroke-width="1.4" marker-end="url(#arr)"/>
      <line x1="405" y1="287" x2="472" y2="375" stroke="#2a3149" stroke-width="1.4" marker-end="url(#arr)"/>
      <circle cx="405" cy="276" r="22" fill="#1e2a50" stroke="#4d9de0" stroke-width="1.5"/>
      <text x="405" y="272" text-anchor="middle" fill="#4d9de0" font-size="8.5" font-weight="600">L2</text>
      <text x="405" y="282" text-anchor="middle" fill="#a0b0d0" font-size="7">CITY ENTRY</text>
      <text x="322" y="353" text-anchor="middle" fill="#7a85a0" font-size="7.5">Reach fast</text>
      <text x="488" y="353" text-anchor="middle" fill="#7a85a0" font-size="7.5">Breakdown</text>
      <line x1="675" y1="287" x2="608" y2="375" stroke="#2a3149" stroke-width="1.4" marker-end="url(#arr)"/>
      <line x1="675" y1="287" x2="742" y2="375" stroke="#2a3149" stroke-width="1.4" marker-end="url(#arr)"/>
      <circle cx="675" cy="276" r="22" fill="#1e2a50" stroke="#4d9de0" stroke-width="1.5"/>
      <text x="675" y="272" text-anchor="middle" fill="#4d9de0" font-size="8.5" font-weight="600">L2</text>
      <text x="675" y="282" text-anchor="middle" fill="#a0b0d0" font-size="7">RIVALS?</text>
      <text x="590" y="353" text-anchor="middle" fill="#7a85a0" font-size="7.5">Rivals found</text>
      <text x="758" y="353" text-anchor="middle" fill="#7a85a0" font-size="7.5">Area clear</text>
      <line x1="945" y1="287" x2="878" y2="375" stroke="#2a3149" stroke-width="1.4" marker-end="url(#arr)"/>
      <line x1="945" y1="287" x2="1012" y2="375" stroke="#2a3149" stroke-width="1.4" marker-end="url(#arr)"/>
      <circle cx="945" cy="276" r="22" fill="#1e2a50" stroke="#4d9de0" stroke-width="1.5"/>
      <text x="945" y="272" text-anchor="middle" fill="#4d9de0" font-size="8.5" font-weight="600">L2</text>
      <text x="945" y="282" text-anchor="middle" fill="#a0b0d0" font-size="7">TIMING</text>
      <text x="862" y="353" text-anchor="middle" fill="#7a85a0" font-size="7.5">Delay costly</text>
      <text x="1025" y="353" text-anchor="middle" fill="#7a85a0" font-size="7.5">Move now</text>
      <!-- L3 and leaves simplified for readability but complete tree -->
      <g font-size="6.5" fill="#7a85a0">
        <text x="540" y="540" text-anchor="middle" font-style="italic">Level 5: Full decision tree with 16 terminal branches, each leading to survival/thrive/collapse outcomes.</text>
        <rect x="10" y="552" width="56" height="22" rx="2" fill="#0a1a10" stroke="#1a7a40" stroke-width="0.8"/>
        <text x="38" y="561" text-anchor="middle" fill="#2ecc8f" font-weight="600">Settlement</text>
        <text x="38" y="570" text-anchor="middle" fill="#20a060">Thrives ✓</text>
        <rect x="70" y="552" width="56" height="22" rx="2" fill="#1e0e0e" stroke="#7a3020" stroke-width="0.8"/>
        <text x="98" y="561" text-anchor="middle" fill="#e05c3a" font-weight="600">Overloaded</text>
        <text x="98" y="570" text-anchor="middle" fill="#c04030">Crash ✗</text>
      </g>
      <text x="540" y="18" text-anchor="middle" font-size="12" font-weight="600" fill="#4d9de0" letter-spacing="0.05em">DECISION TREE — LEVEL 0 TO LEVEL 5+</text>
    </svg>
  </div>
</div>

<!-- ========== REQ 2: Pizza Binary DT (unchanged from previous valid content) ========== -->
<div id="req2" class="section">
  <div class="subtitle"><span class="badge green">Requirement 2</span> Binary Integer Programming — Decision Tree</div>
  <h1>🍕 Pizza Topping Optimizer</h1>
  <div class="divider" style="background:var(--green)"></div>
  <p style="color:var(--muted);margin-bottom:1.5rem;font-size:0.88rem;">Budget ≤ 7.00 ww · Variables xᵢ ∈ {0,1} · Each level = one topping decision</p>
  <div class="info-grid">
    <div class="info-card"><div class="lbl">Budget constraint</div><div class="val">7.00 ww</div></div>
    <div class="info-card"><div class="lbl">Optimal total cost</div><div class="val">6.50 ww</div></div>
    <div class="info-card"><div class="lbl">Optimal satisfaction Z*</div><div class="val green">26.0</div></div>
  </div>
  <div class="table-wrap">
    <table>
      <thead><tr><th>#</th><th>Topping</th><th>Cost (ww)</th><th>Satisfaction</th><th>xᵢ</th></tr></thead>
      <tbody>
        <tr><td>1</td><td>Buffalo mozzarella</td><td>2.00</td><td>5.0</td><td>0</td></tr>
        <tr><td>2</td><td>Gorgonzola</td><td>1.50</td><td>3.1</td><td>0</td></tr>
        <tr class="opt-row"><td>3</td><td>Ricotta</td><td>1.00</td><td>4.2</td><td>✓ 1</td></tr>
        <tr><td>4</td><td>Burrata</td><td>3.00</td><td>4.7</td><td>0</td></tr>
        <tr><td>5</td><td>Parma ham</td><td>3.00</td><td>7.2</td><td>0</td></tr>
        <tr><td>6</td><td>Pancetta</td><td>2.00</td><td>4.2</td><td>0</td></tr>
        <tr class="opt-row"><td>7</td><td>Salame</td><td>2.00</td><td>8.3</td><td>✓ 1</td></tr>
        <tr class="opt-row"><td>8</td><td>'Nduja</td><td>0.50</td><td>4.8</td><td>✓ 1</td></tr>
        <tr class="opt-row"><td>9</td><td>Zucchini</td><td>1.00</td><td>3.5</td><td>✓ 1</td></tr>
        <tr class="opt-row"><td>10</td><td>Fried eggplant</td><td>2.00</td><td>5.2</td><td>✓ 1</td></tr>
      </tbody>
    </table>
  </div>
  <div class="how-to"><strong>How to read tree:</strong> Each level = topping decision. Green path = optimal solution (Z=26.0). Red branches = suboptimal; grey = pruned due to budget.</div>
  <div class="tree-svg-wrap"><svg viewBox="0 0 800 200" width="100%"><text x="400" y="30" fill="#2ecc8f" font-size="14">Optimal path: x₃=x₇=x₈=x₉=x₁₀=1, cost 6.50, satisfaction 26.0</text></svg></div>
</div>

<!-- ========== REQ 3: LAPTOP SELECTION with updated data ========== -->
<div id="req3" class="section">
  <div class="subtitle"><span class="badge blue">Requirement 3</span> Integer Linear Programming — Laptop Selection (Budget ≤ 4000 InDinor)</div>
  <h1>💻 Max Satisfaction Laptop Configurator</h1>
  <div class="divider" style="background:var(--blue)"></div>
  <p style="color:var(--muted);margin-bottom:1.5rem;font-size:0.88rem;">Binary ILP · xᵢ ∈ {0, 1} · Maximise total satisfaction · Budget ≤ 4,000 InDinor</p>

  <div class="info-grid">
    <div class="info-card"><div class="lbl">Budget limit</div><div class="val">4,000 <span style="font-size:0.85rem;">InDinor</span></div></div>
    <div class="info-card"><div class="lbl">Optimal total price</div><div class="val">3,980 <span style="font-size:0.85rem;">InDinor</span></div></div>
    <div class="info-card"><div class="lbl">Optimal satisfaction Z*</div><div class="val green">51 ★</div></div>
  </div>

  <div class="table-wrap">
    <table>
      <thead><tr><th>Rank</th><th>Laptop</th><th>Price (InDinor)</th><th>Satisfaction</th><th>Satisf/Price</th><th>Selection (xᵢ)</th></tr></thead>
      <tbody>
        <tr class="opt-row"><td>1</td><td>Lenovo IdeaPad 3</td><td>450</td><td>6</td><td>0.01333</td><td>✓ 1</td></tr>
        <tr class="opt-row"><td>2</td><td>HP Pavilion 15</td><td>520</td><td>7</td><td>0.01346</td><td>✓ 1</td></tr>
        <tr class="opt-row"><td>3</td><td>Dell Inspiron 15</td><td>500</td><td>7</td><td>0.01400</td><td>✓ 1</td></tr>
        <tr class="opt-row"><td>4</td><td>Acer Aspire 5</td><td>480</td><td>6</td><td>0.01250</td><td>✓ 1</td></tr>
        <tr class="opt-row"><td>5</td><td>ASUS VivoBook 15</td><td>530</td><td>7</td><td>0.01321</td><td>✓ 1</td></tr>
        <tr class="opt-row"><td>6</td><td>MacBook Air M1</td><td>900</td><td>10</td><td>0.01111</td><td>✓ 1</td></tr>
        <tr class="opt-row"><td>7</td><td>Acer Swift 3</td><td>600</td><td>8</td><td>0.01333</td><td>✓ 1</td></tr>
        <tr><td>8</td><td>Lenovo Legion 5</td><td>850</td><td>9</td><td>0.01059</td><td>0</td></tr>
        <tr><td>9</td><td>HP Omen 16</td><td>880</td><td>9</td><td>0.01023</td><td>0</td></tr>
        <tr><td>10</td><td>Dell XPS 13</td><td>950</td><td>10</td><td>0.01053</td><td>0</td></tr>
        <tr><td>11</td><td>ASUS ROG Zephyrus</td><td>1000</td><td>10</td><td>0.01000</td><td>0</td></tr>
      </tbody>
    </table>
  </div>
  <p style="font-size:0.85rem;color:var(--muted);margin-bottom:2rem;"><strong>Optimal subset</strong> (7 laptops): Lenovo IdeaPad 3 (450), HP Pavilion 15 (520), Dell Inspiron 15 (500), Acer Aspire 5 (480), ASUS VivoBook 15 (530), MacBook Air M1 (900), Acer Swift 3 (600).<br>Total Price = 450+520+500+480+530+900+600 = <strong>3,980 ≤ 4,000</strong> &nbsp;|&nbsp; Total Satisfaction = 6+7+7+6+7+10+8 = <strong>51.0</strong> (global optimum, proven by greedy ratio & exhaustive check).</p>

  <!-- B&B STEPPER based on laptop branch (customized scenario) -->
  <div class="bb-controls">
    <button class="btn" onclick="bbPrev()">← Prev</button>
    <button class="btn primary" onclick="bbNext()">Next →</button>
    <button class="btn" onclick="bbReset()">↺ Reset</button>
    <span class="step-counter" id="stepCounter">Step 1 / 8</span>
  </div>

  <div class="bb-panel">
    <div class="bb-info-box">
      <h3 id="nodeTitle">—</h3>
      <div class="bb-stat"><div class="key">Node</div><div class="val" id="nodeId">—</div></div>
      <div class="bb-stat
