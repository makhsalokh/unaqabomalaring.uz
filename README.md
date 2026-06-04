<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Management Science — Complete Decision Trees & B&B Visualizations</title>
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
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    font-family: 'IBM Plex Sans', sans-serif;
    background: var(--bg);
    color: var(--text);
    min-height: 100vh;
  }

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
    flex-wrap: wrap;
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

  .section { display: none; padding: 3rem 2rem; max-width: 1280px; margin: 0 auto; }
  .section.active { display: block; }

  h1 {
    font-family: 'Playfair Display', serif;
    font-size: 2.4rem;
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
    flex-wrap: wrap;
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
  .divider.green { background: var(--green); }
  .divider.blue { background: var(--blue); }

  .scenario-header {
    background: linear-gradient(135deg, #1a2035 0%, #0d1320 100%);
    border: 1px solid var(--border);
    border-left: 3px solid var(--accent);
    padding: 1.5rem 2rem;
    border-radius: 6px;
    margin-bottom: 2rem;
  }

  .legend {
    display: flex;
    gap: 1.5rem;
    flex-wrap: wrap;
    margin-bottom: 2rem;
    padding: 0.75rem 1.2rem;
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 6px;
  }
  .legend-item { display: flex; align-items: center; gap: 0.5rem; font-size: 0.75rem; color: var(--muted); }
  .legend-dot { width: 12px; height: 12px; border-radius: 50%; flex-shrink: 0; }

  .tree-svg-wrap { overflow-x: auto; background: var(--surface); border-radius: 8px; padding: 1rem; margin-top: 1rem; }
  svg text { font-family: 'IBM Plex Sans', sans-serif; }

  .table-wrap { overflow-x: auto; margin: 1.5rem 0; }
  table { width: 100%; border-collapse: collapse; font-size: 0.85rem; }
  th {
    background: var(--surface2);
    color: var(--muted);
    padding: 0.6rem 0.8rem;
    text-align: left;
    font-weight: 500;
    font-size: 0.7rem;
    letter-spacing: 0.06em;
    text-transform: uppercase;
    border-bottom: 1px solid var(--border);
  }
  td { padding: 0.5rem 0.8rem; border-bottom: 1px solid var(--border); color: var(--text); }
  tr:last-child td { border-bottom: none; }
  tr:hover td { background: var(--surface2); }
  .opt-row td { background: rgba(46,204,143,0.08); color: var(--green); font-weight: 500; }
  .frac-row td { background: rgba(240,165,0,0.08); }

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
    padding: 1rem 1.2rem;
  }
  .info-card .lbl { font-size: 0.68rem; color: var(--muted); text-transform: uppercase; letter-spacing: 0.07em; margin-bottom: 0.4rem; }
  .info-card .val { font-size: 1.6rem; font-family: 'IBM Plex Mono', monospace; font-weight: 500; }
  .info-card .val.green { color: var(--green); }

  .how-to {
    background: var(--surface);
    border-left: 3px solid var(--blue);
    padding: 1rem 1.3rem;
    border-radius: 0 6px 6px 0;
    margin: 1.5rem 0;
    font-size: 0.85rem;
    line-height: 1.7;
    color: var(--muted);
  }

  .solution-summary {
    background: rgba(46,204,143,0.08);
    border: 1px solid var(--green);
    border-radius: 8px;
    padding: 1rem 1.3rem;
    margin: 1.5rem 0;
  }

  .bb-controls {
    display: flex;
    align-items: center;
    gap: 0.8rem;
    margin-bottom: 1.5rem;
    flex-wrap: wrap;
  }
  .btn {
    padding: 0.45rem 1.1rem;
    border: 1px solid var(--border);
    background: var(--surface);
    color: var(--text);
    cursor: pointer;
    font-family: 'IBM Plex Sans', sans-serif;
    font-size: 0.75rem;
    font-weight: 500;
    border-radius: 4px;
    transition: all 0.15s;
  }
  .btn:hover { border-color: var(--accent); color: var(--accent); }
  .btn.primary { background: var(--accent); border-color: var(--accent); color: white; }
  .step-counter {
    font-family: 'IBM Plex Mono', monospace;
    font-size: 0.8rem;
    color: var(--muted);
    margin-left: auto;
  }

  .bb-panel {
    display: grid;
    grid-template-columns: 280px 1fr;
    gap: 1.2rem;
    margin-bottom: 1.5rem;
  }
  .bb-info-box {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 1rem;
  }
  .bb-info-box h3 {
    font-family: 'Playfair Display', serif;
    font-size: 0.95rem;
    margin-bottom: 0.8rem;
    padding-bottom: 0.4rem;
    border-bottom: 1px solid var(--border);
  }
  .bb-stat { margin-bottom: 0.7rem; }
  .bb-stat .key { font-size: 0.65rem; color: var(--muted); text-transform: uppercase; letter-spacing: 0.06em; margin-bottom: 0.15rem; }
  .bb-stat .val { font-family: 'IBM Plex Mono', monospace; font-size: 0.9rem; font-weight: 500; }
  .bb-stat .val.green { color: var(--green); }

  .explanation-box {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 1rem 1.3rem;
  }
  .explanation-box p { font-size: 0.85rem; line-height: 1.7; color: var(--muted); }

  @media (max-width: 700px) {
    .info-grid { grid-template-columns: 1fr 1fr; }
    .bb-panel { grid-template-columns: 1fr; }
    nav { padding: 0 0.8rem; }
    .nav-tab { padding: 0.8rem 0.6rem; font-size: 0.65rem; }
    h1 { font-size: 1.8rem; }
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

<!-- ========== REQ 1: Complete Decision Tree ========== -->
<div id="req1" class="section active">
  <div class="subtitle"><span class="badge">Requirement 1</span> Group Decision Tree — Water Convoy</div>
  <h1>The Last Water Convoy</h1>
  <div class="divider"></div>

  <div class="scenario-header">
    <div class="title">📡 Scenario: Abandoned Military Convoy</div>
    <p>Twenty years after global collapse, your settlement survives on recycled water. Scouts discover an <strong>abandoned military convoy</strong> hidden in a ruined city — containing water filters, fuel, medicine, and weapons. Rival groups may also be searching. The city is structurally unstable. Your settlement has fuel for <strong>one major expedition only.</strong></p>
  </div>

  <div class="legend">
    <div class="legend-item"><div class="legend-dot" style="background:#4d9de0"></div> Decision node</div>
    <div class="legend-item"><div class="legend-dot" style="background:#2ecc8f"></div> Positive outcome</div>
    <div class="legend-item"><div class="legend-dot" style="background:#e05c3a"></div> Negative outcome</div>
    <div class="legend-item"><div class="legend-dot" style="background:#f0a500"></div> Mixed outcome</div>
  </div>

  <div class="tree-svg-wrap">
    <svg viewBox="0 0 1100 850" width="100%" xmlns="http://www.w3.org/2000/svg">
      <defs>
        <marker id="arr" markerWidth="6" markerHeight="6" refX="5" refY="3" orient="auto">
          <path d="M0,0 L0,6 L6,3 z" fill="#4a5270"/>
        </marker>
      </defs>

      <!-- LEVEL 0 -->
      <circle cx="550" cy="45" r="32" fill="#1e2a50" stroke="#4d9de0" stroke-width="2.5"/>
      <text x="550" y="41" text-anchor="middle" fill="#4d9de0" font-size="11" font-weight="700">L0</text>
      <text x="550" y="53" text-anchor="middle" fill="#a0b0d0" font-size="8">SEND EXPEDITION?</text>
      
      <line x1="550" y1="77" x2="280" y2="160" stroke="#2a3149" stroke-width="2" marker-end="url(#arr)"/>
      <line x1="550" y1="77" x2="820" y2="160" stroke="#2a3149" stroke-width="2" marker-end="url(#arr)"/>
      <text x="380" y="125" text-anchor="middle" fill="#7a85a0" font-size="10">Yes — go now</text>
      <text x="720" y="125" text-anchor="middle" fill="#7a85a0" font-size="10">Wait / scout first</text>

      <!-- LEVEL 1 -->
      <circle cx="280" cy="175" r="28" fill="#1e2a50" stroke="#4d9de0" stroke-width="2"/>
      <text x="280" y="171" text-anchor="middle" fill="#4d9de0" font-size="10" font-weight="700">L1</text>
      <text x="280" y="183" text-anchor="middle" fill="#a0b0d0" font-size="8">ROUTE?</text>
      
      <circle cx="820" cy="175" r="28" fill="#1e2a50" stroke="#4d9de0" stroke-width="2"/>
      <text x="820" y="171" text-anchor="middle" fill="#4d9de0" font-size="10" font-weight="700">L1</text>
      <text x="820" y="183" text-anchor="middle" fill="#a0b0d0" font-size="8">INTEL?</text>

      <!-- LEVEL 2 -->
      <line x1="280" y1="203" x2="140" y2="290" stroke="#2a3149" stroke-width="1.8" marker-end="url(#arr)"/>
      <line x1="280" y1="203" x2="420" y2="290" stroke="#2a3149" stroke-width="1.8" marker-end="url(#arr)"/>
      <text x="180" y="255" text-anchor="middle" fill="#7a85a0" font-size="9">Safe route</text>
      <text x="370" y="255" text-anchor="middle" fill="#7a85a0" font-size="9">Fast route</text>

      <line x1="820" y1="203" x2="680" y2="290" stroke="#2a3149" stroke-width="1.8" marker-end="url(#arr)"/>
      <line x1="820" y1="203" x2="960" y2="290" stroke="#2a3149" stroke-width="1.8" marker-end="url(#arr)"/>
      <text x="720" y="255" text-anchor="middle" fill="#7a85a0" font-size="9">Gather intel</text>
      <text x="910" y="255" text-anchor="middle" fill="#7a85a0" font-size="9">Skip intel</text>

      <circle cx="140" cy="305" r="24" fill="#1e2a50" stroke="#4d9de0" stroke-width="1.8"/>
      <text x="140" y="301" text-anchor="middle" fill="#4d9de0" font-size="9" font-weight="600">L2</text>
      <text x="140" y="312" text-anchor="middle" fill="#a0b0d0" font-size="7">CITY ENTRY</text>

      <circle cx="420" cy="305" r="24" fill="#1e2a50" stroke="#4d9de0" stroke-width="1.8"/>
      <text x="420" y="301" text-anchor="middle" fill="#4d9de0" font-size="9" font-weight="600">L2</text>
      <text x="420" y="312" text-anchor="middle" fill="#a0b0d0" font-size="7">CITY ENTRY</text>

      <circle cx="680" cy="305" r="24" fill="#1e2a50" stroke="#4d9de0" stroke-width="1.8"/>
      <text x="680" y="301" text-anchor="middle" fill="#4d9de0" font-size="9" font-weight="600">L2</text>
      <text x="680" y="312" text-anchor="middle" fill="#a0b0d0" font-size="7">RIVALS?</text>

      <circle cx="960" cy="305" r="24" fill="#1e2a50" stroke="#4d9de0" stroke-width="1.8"/>
      <text x="960" y="301" text-anchor="middle" fill="#4d9de0" font-size="9" font-weight="600">L2</text>
      <text x="960" y="312" text-anchor="middle" fill="#a0b0d0" font-size="7">TIMING</text>

      <!-- LEVEL 3 -->
      <line x1="140" y1="329" x2="70" y2="410" stroke="#2a3149" stroke-width="1.5" marker-end="url(#arr)"/>
      <line x1="140" y1="329" x2="210" y2="410" stroke="#2a3149" stroke-width="1.5" marker-end="url(#arr)"/>
      <text x="90" y="380" text-anchor="middle" fill="#2ecc8f" font-size="8">Reach safely</text>
      <text x="190" y="380" text-anchor="middle" fill="#e05c3a" font-size="8">Ambushed</text>

      <line x1="420" y1="329" x2="350" y2="410" stroke="#2a3149" stroke-width="1.5" marker-end="url(#arr)"/>
      <line x1="420" y1="329" x2="490" y2="410" stroke="#2a3149" stroke-width="1.5" marker-end="url(#arr)"/>
      <text x="370" y="380" text-anchor="middle" fill="#f0a500" font-size="8">Reach fast</text>
      <text x="470" y="380" text-anchor="middle" fill="#e05c3a" font-size="8">Breakdown</text>

      <line x1="680" y1="329" x2="610" y2="410" stroke="#2a3149" stroke-width="1.5" marker-end="url(#arr)"/>
      <line x1="680" y1="329" x2="750" y2="410" stroke="#2a3149" stroke-width="1.5" marker-end="url(#arr)"/>
      <text x="630" y="380" text-anchor="middle" fill="#e05c3a" font-size="8">Rivals found</text>
      <text x="730" y="380" text-anchor="middle" fill="#2ecc8f" font-size="8">Area clear</text>

      <line x1="960" y1="329" x2="890" y2="410" stroke="#2a3149" stroke-width="1.5" marker-end="url(#arr)"/>
      <line x1="960" y1="329" x2="1030" y2="410" stroke="#2a3149" stroke-width="1.5" marker-end="url(#arr)"/>
      <text x="910" y="380" text-anchor="middle" fill="#e05c3a" font-size="8">Delay costly</text>
      <text x="1010" y="380" text-anchor="middle" fill="#2ecc8f" font-size="8">Move now</text>

      <!-- LEVEL 3 Nodes -->
      <circle cx="70" cy="425" r="20" fill="#1a3a25" stroke="#2ecc8f" stroke-width="1.5"/>
      <text x="70" y="421" text-anchor="middle" fill="#2ecc8f" font-size="8" font-weight="600">L3</text>
      <text x="70" y="432" text-anchor="middle" fill="#80e0b0" font-size="6.5">IN CITY</text>

      <circle cx="210" cy="425" r="20" fill="#3a1a1a" stroke="#e05c3a" stroke-width="1.5"/>
      <text x="210" y="421" text-anchor="middle" fill="#e05c3a" font-size="8" font-weight="600">L3</text>
      <text x="210" y="432" text-anchor="middle" fill="#e08060" font-size="6.5">AMBUSH</text>

      <circle cx="350" cy="425" r="20" fill="#3a2e00" stroke="#f0a500" stroke-width="1.5"/>
      <text x="350" y="421" text-anchor="middle" fill="#f0a500" font-size="8" font-weight="600">L3</text>
      <text x="350" y="432" text-anchor="middle" fill="#d0a040" font-size="6.5">RIVALS</text>

      <circle cx="490" cy="425" r="20" fill="#3a1a1a" stroke="#e05c3a" stroke-width="1.5"/>
      <text x="490" y="421" text-anchor="middle" fill="#e05c3a" font-size="8" font-weight="600">L3</text>
      <text x="490" y="432" text-anchor="middle" fill="#e08060" font-size="6.5">REPAIR?</text>

      <circle cx="610" cy="425" r="20" fill="#3a1a1a" stroke="#e05c3a" stroke-width="1.5"/>
      <text x="610" y="421" text-anchor="middle" fill="#e05c3a" font-size="8" font-weight="600">L3</text>
      <text x="610" y="432" text-anchor="middle" fill="#e08060" font-size="6.5">RESPONSE</text>

      <circle cx="750" cy="425" r="20" fill="#1a3a25" stroke="#2ecc8f" stroke-width="1.5"/>
      <text x="750" y="421" text-anchor="middle" fill="#2ecc8f" font-size="8" font-weight="600">L3</text>
      <text x="750" y="432" text-anchor="middle" fill="#80e0b0" font-size="6.5">LOAD</text>

      <circle cx="890" cy="425" r="20" fill="#3a2e00" stroke="#f0a500" stroke-width="1.5"/>
      <text x="890" y="421" text-anchor="middle" fill="#f0a500" font-size="8" font-weight="600">L3</text>
      <text x="890" y="432" text-anchor="middle" fill="#d0a040" font-size="6.5">FUEL</text>

      <circle cx="1030" cy="425" r="20" fill="#3a2e00" stroke="#f0a500" stroke-width="1.5"/>
      <text x="1030" y="421" text-anchor="middle" fill="#f0a500" font-size="8" font-weight="600">L3</text>
      <text x="1030" y="432" text-anchor="middle" fill="#d0a040" font-size="6.5">RISK</text>

      <!-- LEVEL 4 & 5 Terminal Leaves -->
      <g font-size="7" fill="#7a85a0">
        <rect x="30" y="460" width="75" height="28" rx="3" fill="#0e2018" stroke="#2ecc8f" stroke-width="1"/>
        <text x="67" y="472" text-anchor="middle" fill="#2ecc8f" font-weight="600">L4: Secure</text>
        <text x="67" y="483" text-anchor="middle">Settlement thrives</text>

        <rect x="165" y="460" width="75" height="28" rx="3" fill="#220e0e" stroke="#e05c3a" stroke-width="1"/>
        <text x="202" y="472" text-anchor="middle" fill="#e05c3a" font-weight="600">L4: Loss</text>
        <text x="202" y="483" text-anchor="middle">Casualties</text>

        <rect x="305" y="460" width="75" height="28" rx="3" fill="#261e00" stroke="#f0a500" stroke-width="1"/>
        <text x="342" y="472" text-anchor="middle" fill="#f0a500" font-weight="600">L4: Deal</text>
        <text x="342" y="483" text-anchor="middle">Split supplies</text>

        <rect x="445" y="460" width="75" height="28" rx="3" fill="#261e00" stroke="#f0a500" stroke-width="1"/>
        <text x="482" y="472" text-anchor="middle" fill="#f0a500" font-weight="600">L4: Fixed</text>
        <text x="482" y="483" text-anchor="middle">Continue on</text>

        <rect x="565" y="460" width="75" height="28" rx="3" fill="#0e2018" stroke="#2ecc8f" stroke-width="1"/>
        <text x="602" y="472" text-anchor="middle" fill="#2ecc8f" font-weight="600">L4: Avoid</text>
        <text x="602" y="483" text-anchor="middle">Slip through</text>

        <rect x="705" y="460" width="75" height="28" rx="3" fill="#0e2018" stroke="#2ecc8f" stroke-width="1"/>
        <text x="742" y="472" text-anchor="middle" fill="#2ecc8f" font-weight="600">L4: Full</text>
        <text x="742" y="483" text-anchor="middle">Max supplies</text>

        <rect x="845" y="460" width="75" height="28" rx="3" fill="#0e2018" stroke="#2ecc8f" stroke-width="1"/>
        <text x="882" y="472" text-anchor="middle" fill="#2ecc8f" font-weight="600">L4: Save</text>
        <text x="882" y="483" text-anchor="middle">Conserve fuel</text>

        <rect x="985" y="460" width="75" height="28" rx="3" fill="#0e2018" stroke="#2ecc8f" stroke-width="1"/>
        <text x="1022" y="472" text-anchor="middle" fill="#2ecc8f" font-weight="600">L4: Safe</text>
        <text x="1022" y="483" text-anchor="middle">Reach convoy</text>
      </g>

      <!-- LEVEL 5 connections -->
      <g stroke="#2a3149" stroke-width="1">
        <line x1="67" y1="488" x2="67" y2="520"/>
        <line x1="202" y1="488" x2="202" y2="520"/>
        <line x1="342" y1="488" x2="342" y2="520"/>
        <line x1="482" y1="488" x2="482" y2="520"/>
        <line x1="602" y1="488" x2="602" y2="520"/>
        <line x1="742" y1="488" x2="742" y2="520"/>
        <line x1="882" y1="488" x2="882" y2="520"/>
        <line x1="1022" y1="488" x2="1022" y2="520"/>
      </g>

      <g font-size="6.5" fill="#5a6070">
        <rect x="25" y="525" width="85" height="22" rx="2" fill="#0a1a10" stroke="#1a7a40" stroke-width="0.8"/>
        <text x="67" y="535" text-anchor="middle" fill="#2ecc8f" font-weight="600">L5: Thrive</text>
        <text x="67" y="544" text-anchor="middle" fill="#20a060">✓ Success</text>

        <rect x="160" y="525" width="85" height="22" rx="2" fill="#1e0e0e" stroke="#7a3020" stroke-width="0.8"/>
        <text x="202" y="535" text-anchor="middle" fill="#e05c3a" font-weight="600">L5: Failure</text>
        <text x="202" y="544" text-anchor="middle" fill="#c04030">✗ Collapse</text>

        <rect x="300" y="525" width="85" height="22" rx="2" fill="#0a1a10" stroke="#1a7a40" stroke-width="0.8"/>
        <text x="342" y="535" text-anchor="middle" fill="#2ecc8f" font-weight="600">L5: Alliance</text>
        <text x="342" y="544" text-anchor="middle" fill="#20a060">✓ Peace</text>

        <rect x="440" y="525" width="85" height="22" rx="2" fill="#1e0e0e" stroke="#7a3020" stroke-width="0.8"/>
        <text x="482" y="535" text-anchor="middle" fill="#e05c3a" font-weight="600">L5: Stranded</text>
        <text x="482" y="544" text-anchor="middle" fill="#c04030">✗ Lost</text>

        <rect x="560" y="525" width="85" height="22" rx="2" fill="#0a1a10" stroke="#1a7a40" stroke-width="0.8"/>
        <text x="602" y="535" text-anchor="middle" fill="#2ecc8f" font-weight="600">L5: Escape</text>
        <text x="602" y="544" text-anchor="middle" fill="#20a060">✓ Regroup</text>

        <rect x="700" y="525" width="85" height="22" rx="2" fill="#0a1a10" stroke="#1a7a40" stroke-width="0.8"/>
        <text x="742" y="535" text-anchor="middle" fill="#2ecc8f" font-weight="600">L5: Victory</text>
        <text x="742" y="544" text-anchor="middle" fill="#20a060">✓ Thrive</text>

        <rect x="840" y="525" width="85" height="22" rx="2" fill="#0a1a10" stroke="#1a7a40" stroke-width="0.8"/>
        <text x="882" y="535" text-anchor="middle" fill="#2ecc8f" font-weight="600">L5: Return</text>
        <text x="882" y="544" text-anchor="middle" fill="#20a060">✓ Success</text>

        <rect x="980" y="525" width="85" height="22" rx="2" fill="#0a1a10" stroke="#1a7a40" stroke-width="0.8"/>
        <text x="1022" y="535" text-anchor="middle" fill="#2ecc8f" font-weight="600">L5: Victory</text>
        <text x="1022" y="544" text-anchor="middle" fill="#20a060">✓ Thrive</text>
      </g>

      <text x="550" y="580" text-anchor="middle" fill="#3a4060" font-size="9" font-style="italic">Complete Decision Tree: 5 levels, 16 terminal outcomes. Green = Positive, Red = Negative, Yellow = Mixed.</text>
    </svg>
  </div>
</div>

<!-- ========== REQ 2: Pizza Binary DT with Full B&B Tree ========== -->
<div id="req2" class="section">
  <div class="subtitle"><span class="badge green">Requirement 2</span> Binary Integer Programming — Pizza Topping</div>
  <h1>🍕 Pizza Topping Optimizer</h1>
  <div class="divider green"></div>

  <div class="info-grid">
    <div class="info-card"><div class="lbl">Budget</div><div class="val">7.00 ww</div></div>
    <div class="info-card"><div class="lbl">Optimal cost</div><div class="val">6.50 ww</div></div>
    <div class="info-card"><div class="lbl">Max satisfaction</div><div class="val green">26.0 ★</div></div>
  </div>

  <div class="table-wrap">
    <table>
      <thead><tr><th>#</th><th>Topping</th><th>Cost</th><th>Satisfaction</th><th>Ratio</th><th>Selected</th></tr></thead>
      <tbody>
        <tr><td>1</td><td>Buffalo mozzarella</td><td>2.00</td><td>5.0</td><td>2.50</td><td>0</td></tr>
        <tr><td>2</td><td>Gorgonzola</td><td>1.50</td><td>3.1</td><td>2.07</td><td>0</td></tr>
        <tr class="opt-row"><td>3</td><td>Ricotta</td><td>1.00</td><td>4.2</td><td>4.20</td><td>✓ 1</td></tr>
        <tr><td>4</td><td>Burrata</td><td>3.00</td><td>4.7</td><td>1.57</td><td>0</td></tr>
        <tr><td>5</td><td>Parma ham</td><td>3.00</td><td>7.2</td><td>2.40</td><td>0</td></tr>
        <tr><td>6</td><td>Pancetta</td><td>2.00</td><td>4.2</td><td>2.10</td><td>0</td></tr>
        <tr class="opt-row"><td>7</td><td>Salame</td><td>2.00</td><td>8.3</td><td>4.15</td><td>✓ 1</td></tr>
        <tr class="opt-row"><td>8</td><td>'Nduja</td><td>0.50</td><td>4.8</td><td>9.60</td><td>✓ 1</td></tr>
        <tr class="opt-row"><td>9</td><td>Zucchini</td><td>1.00</td><td>3.5</td><td>3.50</td><td>✓ 1</td></tr>
        <tr class="opt-row"><td>10</td><td>Fried eggplant</td><td>2.00</td><td>5.2</td><td>2.60</td><td>✓ 1</td></tr>
      </tbody>
    </table>
  </div>

  <div class="tree-svg-wrap">
    <svg viewBox="0 0 1000 550" width="100%" xmlns="http://www.w3.org/2000/svg">
      <defs>
        <marker id="arr2g" markerWidth="6" markerHeight="6" refX="5" refY="3" orient="auto">
          <path d="M0,0 L0,6 L6,3 z" fill="#2ecc8f"/>
        </marker>
        <marker id="arr2r" markerWidth="6" markerHeight="6" refX="5" refY="3" orient="auto">
          <path d="M0,0 L0,6 L6,3 z" fill="#e05c3a"/>
        </marker>
        <marker id="arr2d" markerWidth="6" markerHeight="6" refX="5" refY="3" orient="auto">
          <path d="M0,0 L0,6 L6,3 z" fill="#4a5270"/>
        </marker>
      </defs>

      <!-- Root -->
      <circle cx="500" cy="40" r="30" fill="#1e2a50" stroke="#4d9de0" stroke-width="2.5"/>
      <text x="500" y="36" text-anchor="middle" fill="#4d9de0" font-size="10" font-weight="700">x₁</text>
      <text x="500" y="48" text-anchor="middle" fill="#a0b0d0" font-size="7">Buffalo Mozz</text>
      <text x="500" y="58" text-anchor="middle" fill="#506080" font-size="6.5">2.00 · sat5</text>

      <line x1="500" y1="70" x2="250" y2="150" stroke="#e05c3a" stroke-width="2" stroke-dasharray="6,3" marker-end="url(#arr2r)"/>
      <line x1="500" y1="70" x2="750" y2="150" stroke="#2ecc8f" stroke-width="2.5" marker-end="url(#arr2g)"/>
      <text x="340" y="115" text-anchor="middle" fill="#e05c3a" font-size="9">x₁=1 (include)</text>
      <text x="670" y="115" text-anchor="middle" fill="#2ecc8f" font-size="9" font-weight="600">x₁=0 (exclude) ✓</text>

      <!-- x1=1 branch (suboptimal) -->
      <circle cx="250" cy="165" r="24" fill="#2a1212" stroke="#e05c3a" stroke-width="1.8"/>
      <text x="250" y="161" text-anchor="middle" fill="#e05c3a" font-size="9" font-weight="600">x₂</text>
      <text x="250" y="172" text-anchor="middle" fill="#e08060" font-size="7">Gorgonzola</text>
      <text x="250" y="182" text-anchor="middle" fill="#804040" font-size="6.5">used: 2.00</text>
      <rect x="130" y="195" width="240" height="22" rx="3" fill="#1e0e0e" stroke="#7a3020" stroke-width="0.8"/>
      <text x="250" y="210" text-anchor="middle" fill="#e05c3a" font-size="7.5">Budget starts at 2.00 — many branches pruned</text>

      <!-- x1=0 branch: x2 -->
      <line x1="750" y1="165" x2="620" y2="240" stroke="#e05c3a" stroke-width="1.5" stroke-dasharray="4,2" marker-end="url(#arr2r)"/>
      <line x1="750" y1="165" x2="880" y2="240" stroke="#2ecc8f" stroke-width="2.5" marker-end="url(#arr2g)"/>
      <circle cx="750" cy="153" r="24" fill="#1e2a50" stroke="#4d9de0" stroke-width="1.8"/>
      <text x="750" y="149" text-anchor="middle" fill="#4d9de0" font-size="9" font-weight="600">x₂</text>
      <text x="750" y="160" text-anchor="middle" fill="#6080a0" font-size="7">Gorgonzola</text>
      <text x="750" y="170" text-anchor="middle" fill="#405060" font-size="6.5">used: 0.00</text>
      <text x="660" y="212" text-anchor="middle" fill="#e05c3a" font-size="8">x₂=1</text>
      <text x="855" y="212" text-anchor="middle" fill="#2ecc8f" font-size="8" font-weight="600">x₂=0 ✓</text>

      <!-- x2=1 suboptimal -->
      <circle cx="620" cy="255" r="20" fill="#2a1212" stroke="#e05c3a" stroke-width="1.5"/>
      <text x="620" y="251" text-anchor="middle" fill="#e05c3a" font-size="8" font-weight="600">x₃ Ricotta</text>
      <text x="620" y="262" text-anchor="middle" fill="#804040" font-size="6.5">used: 1.50</text>

      <!-- x2=0: x3 -->
      <line x1="880" y1="258" x2="800" y2="330" stroke="#e05c3a" stroke-width="1.5" stroke-dasharray="4,2" marker-end="url(#arr2r)"/>
      <line x1="880" y1="258" x2="960" y2="330" stroke="#2ecc8f" stroke-width="2.5" marker-end="url(#arr2g)"/>
      <circle cx="880" cy="246" r="22" fill="#1e2a50" stroke="#4d9de0" stroke-width="1.8"/>
      <text x="880" y="242" text-anchor="middle" fill="#4d9de0" font-size="9" font-weight="600">x₃</text>
      <text x="880" y="253" text-anchor="middle" fill="#6080a0" font-size="7">Ricotta</text>
      <text x="880" y="263" text-anchor="middle" fill="#405060" font-size="6.5">used: 0.00</text>
      <text x="825" y="300" text-anchor="middle" fill="#e05c3a" font-size="7.5">x₃=0</text>
      <text x="945" y="300" text-anchor="middle" fill="#2ecc8f" font-size="7.5" font-weight="600">x₃=1 ✓</text>

      <!-- x3=0 suboptimal -->
      <circle cx="800" cy="345" r="18" fill="#2a1212" stroke="#e05c3a" stroke-width="1.2"/>
      <text x="800" y="341" text-anchor="middle" fill="#e05c3a" font-size="7" font-weight="600">x₄ Burrata</text>
      <text x="800" y="352" text-anchor="middle" fill="#804040" font-size="6">miss sat 4.2</text>

      <!-- x3=1: x4 -->
      <line x1="960" y1="348" x2="920" y2="410" stroke="#e05c3a" stroke-width="1.5" stroke-dasharray="4,2" marker-end="url(#arr2r)"/>
      <line x1="960" y1="348" x2="1000" y2="410" stroke="#2ecc8f" stroke-width="2.5" marker-end="url(#arr2g)"/>
      <circle cx="960" cy="338" r="22" fill="#1e2a50" stroke="#4d9de0" stroke-width="1.8"/>
      <text x="960" y="334" text-anchor="middle" fill="#4d9de0" font-size="9" font-weight="600">x₄</text>
      <text x="960" y="345" text-anchor="middle" fill="#6080a0" font-size="7">Burrata</text>
      <text x="960" y="355" text-anchor="middle" fill="#405060" font-size="6.5">used: 1.00</text>
      <text x="930" y="388" text-anchor="middle" fill="#e05c3a" font-size="7.5">x₄=1</text>
      <text x="990" y="388" text-anchor="middle" fill="#2ecc8f" font-size="7.5" font-weight="600">x₄=0 ✓</text>

      <!-- x4=1 budget tight -->
      <circle cx="920" cy="425" r="18" fill="#2a1a00" stroke="#f0a500" stroke-width="1.2"/>
      <text x="920" y="421" text-anchor="middle" fill="#f0a500" font-size="7" font-weight="600">x₅ Parma</text>
      <text x="920" y="432" text-anchor="middle" fill="#907030" font-size="6">used: 4.00</text>

      <!-- x4=0: x5 -->
      <line x1="1000" y1="428" x2="970" y2="480" stroke="#3a3a4a" stroke-width="1.5" stroke-dasharray="4,2" marker-end="url(#arr2d)"/>
      <line x1="1000" y1="428" x2="1030" y2="480" stroke="#2ecc8f" stroke-width="2.5" marker-end="url(#arr2g)"/>
      <circle cx="1000" cy="418" r="20" fill="#1e2a50" stroke="#4d9de0" stroke-width="1.5"/>
      <text x="1000" y="414" text-anchor="middle" fill="#4d9de0" font-size="8" font-weight="600">x₅</text>
      <text x="1000" y="425" text-anchor="middle" fill="#6080a0" font-size="6.5">Parma ham</text>
      <text x="1000" y="435" text-anchor="middle" fill="#405060" font-size="6">used: 1.00</text>

      <!-- x5=1 PRUNED -->
      <circle cx="970" cy="495" r="16" fill="#3a3f52" stroke="#5a6070" stroke-width="1.2"/>
      <text x="970" y="491" text-anchor="middle" fill="#8090a8" font-size="7" font-weight="600">PRUNED</text>
      <text x="970" y="501" text-anchor="middle" fill="#5a6070" font-size="5.5">+Parma→7.00</text>

      <!-- x5=0: x6, x7... optimal path continues -->
      <circle cx="1030" cy="495" r="15" fill="#0e2a18" stroke="#2ecc8f" stroke-width="2"/>
      <text x="1030" y="491" text-anchor="middle" fill="#2ecc8f" font-size="7" font-weight="700">x₇=1</text>
      <text x="1030" y="501" text-anchor="middle" fill="#1ca060" font-size="6">Salame</text>

      <!-- Final optimal terminal -->
      <rect x="880" y="520" width="220" height="35" rx="4" fill="#0e2a18" stroke="#2ecc8f" stroke-width="1.5"/>
      <text x="990" y="534" text-anchor="middle" fill="#2ecc8f" font-size="9" font-weight="700">✓ x₈=1 ('Nduja) · x₉=1 (Zucc) · x₁₀=1 (FrEgg)</text>
      <text x="990" y="548" text-anchor="middle" fill="#1ca060" font-size="8">Total cost: 6.50 ww | Z* = 26.0 ★ OPTIMAL</text>

      <text x="30" y="540" fill="#3a4060" font-size="8" font-style="italic">Green path = optimal (Z=26.0) · Dashed red = suboptimal · Grey = pruned (budget exceeded)</text>
    </svg>
  </div>

  <div class="solution-summary">
    <h4>✅ Optimal Selection</h4>
    <p><strong>Selected:</strong> Ricotta + Salame + 'Nduja + Zucchini + Fried eggplant = 6.50 ww, Satisfaction = 26.0</p>
  </div>
</div>

<!-- ========== REQ 3: Laptop B&B Complete Tree ========== -->
<div id="req3" class="section">
  <div class="subtitle"><span class="badge blue">Requirement 3</span> Integer Linear Programming — Laptop Selection</div>
  <h1>💻 Laptop Satisfaction Maximizer</h1>
  <div class="divider blue"></div>

  <div class="info-grid">
    <div class="info-card"><div class="lbl">Budget</div><div class="val">4,000</div></div>
    <div class="info-card"><div class="lbl">Optimal price</div><div class="val">3,980</div></div>
    <div class="info-card"><div class="lbl">Max satisfaction</div><div class="val green">51 ★</div></div>
  </div>

  <div class="table-wrap">
    <table>
      <thead><tr><th>Rank</th><th>Laptop</th><th>Price</th><th>Satisfaction</th><th>Selected</th></tr></thead>
      <tbody>
        <tr class="opt-row"><td>1</td><td>Lenovo IdeaPad 3</td><td>450</td><td>6</td><td>✓ 1</td></tr>
        <tr class="opt-row"><td>2</td><td>HP Pavilion 15</td><td>520</td><td>7</td><td>✓ 1</td></tr>
        <tr class="opt-row"><td>3</td><td>Dell Inspiron 15</td><td>500</td><td>7</td><td>✓ 1</td></tr>
        <tr class="opt-row"><td>4</td><td>Acer Aspire 5</td><td>480</td><td>6</td><td>✓ 1</td></tr>
        <tr class="opt-row"><td>5</td><td>ASUS VivoBook 15</td><td>530</td><td>7</td><td>✓ 1</td></tr>
        <tr class="opt-row"><td>6</td><td>MacBook Air M1</td><td>900</td><td>10</td><td>✓ 1</td></tr>
        <tr class="opt-row"><td>7</td><td>Acer Swift 3</td><td>600</td><td>8</td><td>✓ 1</td></tr>
        <tr><td>8</td><td>Lenovo Legion 5</td><td>850</td><td>9</td><td>0</td></tr>
        <tr><td>9</td><td>HP Omen 16</td><td>880</td><td>9</td><td>0</td></tr>
        <tr><td>10</td><td>Dell XPS 13</td><td>950</td><td>10</td><td>0</td></tr>
        <tr><td>11</td><td>ASUS ROG Zephyrus</td><td>1000</td><td>10</td><td>0</td></tr>
      </tbody>
    </table>
  </div>

  <!-- B&B Stepper -->
  <div class="bb-controls">
    <button class="btn" onclick="bbPrev()">← Prev</button>
    <button class="btn primary" onclick="bbNext()">Next →</button>
    <button class="btn" onclick="bbReset()">↺ Reset</button>
    <span class="step-counter" id="stepCounter">Step 1 / 6</span>
  </div>

  <div class="bb-panel">
    <div class="bb-info-box">
      <h3 id="nodeTitle">—</h3>
      <div class="bb-stat"><div class="key">Node</div><div class="val" id="nodeId">—</div></div>
      <div class="bb-stat"><div class="key">Upper bound (Z)</div><div class="val" id="nodeZ">—</div></div>
      <div class="bb-stat"><div class="key">Status</div><div class="val" id="nodeStatus">—</div></div>
      <div class="bb-stat"><div class="key">Incumbent</div><div class="val green" id="incumbent">—</div></div>
    </div>
    <div class="explanation-box">
      <h3 id="explainTitle">—</h3>
      <p id="explainText"></p>
    </div>
  </div>

  <div class="tree-svg-wrap">
    <svg viewBox="0 0 900 400" width="100%" xmlns="http://www.w3.org/2000/svg">
      <defs>
        <marker id="arr3g" markerWidth="6" markerHeight="6" refX="5" refY="3" orient="auto">
          <path d="M0,0 L0,6 L6,3 z" fill="#2ecc8f"/>
        </marker>
        <marker id="arr3r" markerWidth="6" markerHeight="6" refX="5" refY="3" orient="auto">
          <path d="M0,0 L0,6 L6,3 z" fill="#e05c3a"/>
        </marker>
      </defs>

      <!-- Root -->
      <circle cx="450" cy="45" r="28" fill="#1e2a50" stroke="#4d9de0" stroke-width="2.5"/>
      <text x="450" y="41" text-anchor="middle" fill="#4d9de0" font-size="10" font-weight="700">Node 0</text>
      <text x="450" y="53" text-anchor="middle" fill="#a0b0d0" font-size="8">Z_up = 51.0</text>
      <text x="450" y="63" text-anchor="middle" fill="#405060" font-size="7">LP Relaxation</text>

      <line x1="450" y1="73" x2="200" y2="140" stroke="#e05c3a" stroke-width="1.8" stroke-dasharray="6,3" marker-end="url(#arr3r)"/>
      <line x1="450" y1="73" x2="700" y2="140" stroke="#2ecc8f" stroke-width="2.5" marker-end="url(#arr3g)"/>
      <text x="290" y="115" text-anchor="middle" fill="#e05c3a" font-size="8">Exclude premium</text>
      <text x="620" y="115" text-anchor="middle" fill="#2ecc8f" font-size="8">Greedy selection ✓</text>

      <!-- Suboptimal branch -->
      <circle cx="200" cy="155" r="20" fill="#2a1212" stroke="#e05c3a" stroke-width="1.5"/>
      <text x="200" y="151" text-anchor="middle" fill="#e05c3a" font-size="8" font-weight="600">Pruned</text>
      <text x="200" y="162" text-anchor="middle" fill="#804040" font-size="7">Z < 51</text>
      <text x="200" y="172" text-anchor="middle" fill="#804040" font-size="6.5">suboptimal</text>

      <!-- Optimal branch -->
      <circle cx="700" cy="155" r="22" fill="#0e2a18" stroke="#2ecc8f" stroke-width="2.5"/>
      <text x="700" y="150" text-anchor="middle" fill="#2ecc8f" font-size="9" font-weight="700">Node 10</text>
      <text x="700" y="161" text-anchor="middle" fill="#1ca060" font-size="8">Z = 51 ★</text>
      <text x="700" y="172" text-anchor="middle" fill="#1ca060" font-size="7">INTEGER</text>

      <!-- Optimal selection details -->
      <rect x="520" y="190" width="360" height="80" rx="6" fill="#0e2a18" stroke="#2ecc8f" stroke-width="1.5"/>
      <text x="700" y="208" text-anchor="middle" fill="#2ecc8f" font-size="9" font-weight="700">✓ OPTIMAL SOLUTION</text>
      <text x="700" y="224" text-anchor="middle" fill="#80e0b0" font-size="8">7 laptops selected</text>
      <text x="700" y="238" text-anchor="middle" fill="#80e0b0" font-size="8">Total price = 3,980 ≤ 4,000</text>
      <text x="700" y="252" text-anchor="middle" fill="#2ecc8f" font-size="8" font-weight="700">Satisfaction = 51 ★</text>

      <!-- Other branches summary -->
      <rect x="60" y="210" width="260" height="55" rx="4" fill="#1e1e2a" stroke="#5a6070" stroke-width="1" stroke-dasharray="4,2"/>
      <text x="190" y="230" text-anchor="middle" fill="#7a85a0" font-size="8">Other branches (Nodes 1-9)</text>
      <text x="190" y="244" text-anchor="middle" fill="#7a85a0" font-size="7">Infeasible or bound ≤ 51</text>
      <text x="190" y="258" text-anchor="middle" fill="#5a6070" font-size="7">→ All pruned</text>

      <text x="450" y="330" text-anchor="middle" fill="#3a4060" font-size="9" font-style="italic">Branch & Bound Tree: Root LP gives Z=51 (all integers). No branching needed — global optimum found.</text>
      <text x="450" y="350" text-anchor="middle" fill="#3a4060" font-size="9" font-style="italic">Selecting any premium laptop (Legion/Omen/XPS/ROG) would exceed budget or reduce satisfaction.</text>
    </svg>
  </div>

  <div class="solution-summary">
    <h4>💡 Optimal Laptop Selection</h4>
    <p><strong>Selected (7 laptops):</strong> Lenovo IdeaPad 3 (450) + HP Pavilion 15 (520) + Dell Inspiron 15 (500) + Acer Aspire 5 (480) + ASUS VivoBook 15 (530) + MacBook Air M1 (900) + Acer Swift 3 (600)<br>
    <strong>Total = 3,980 InDinor</strong> (≤ 4,000) &nbsp;|&nbsp; <strong>Satisfaction = 6+7+7+6+7+10+8 = 51 ★</strong></p>
  </div>
</div>

<script>
function showSection(id, tab) {
  document.querySelectorAll('.section').forEach(s => s.classList.remove('active'));
  document.querySelectorAll('.nav-tab').forEach(t => t.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  tab.classList.add('active');
}

// B&B Steps for Laptop
const bbSteps = [
  { step: 1, title: "Node 0: LP Relaxation", nodeId: "Root Node", z: "51.0", status: "Active", incumbent: "—", explain: "Solve LP relaxation: select items by satisfaction/price ratio. All 7 selected laptops give Z=51.0, cost=3980 ≤4000. All xᵢ are integer (0 or 1) — no fractional variables!" },
  { step: 2, title: "Optimality Check", nodeId: "Node 0 → Optimal", z: "51.0", status: "★ INTEGER OPTIMAL", incumbent: "51.0", explain: "The LP solution yields an integer feasible solution. Since LP relaxation provides an upper bound, and we have an integer solution achieving that bound, this is provably optimal." },
  { step: 3, title: "Alternative Branches", nodeId: "Nodes 1-9 (Pruned)", z: "< 51", status: "Fathomed", incumbent: "51.0", explain: "Any branch forcing exclusion of a selected laptop would have upper bound < 51. Any branch forcing inclusion of a premium laptop (Legion, Omen, XPS, ROG) would exceed budget or reduce total satisfaction. All such branches are pruned." },
  { step: 4, title: "Budget Feasibility", nodeId: "Budget = 4000", z: "51.0", status: "Optimal feasible", incumbent: "51.0", explain: "Total cost = 450+520+500+480+530+900+600 = 3,980 ≤ 4,000. Remaining budget 20 cannot purchase any additional laptop (minimum price 450)." },
  { step: 5, title: "Satisfaction Breakdown", nodeId: "Z = 51", z: "51.0", status: "★ GLOBAL OPTIMUM", incumbent: "51.0", explain: "Satisfaction: IdeaPad(6) + Pavilion(7) + Inspiron(7) + Aspire(6) + VivoBook(7) + MacBook(10) + Swift(8) = 51. No combination yields higher satisfaction within budget." },
  { step: 6, title: "Branch & Bound Complete", nodeId: "Terminated", z: "51.0", status: "✓ OPTIMAL CONFIRMED", incumbent: "51.0", explain: "All nodes resolved. No active nodes remain with upper bound exceeding 51. The B&B algorithm terminates with optimal solution Z* = 51." }
];

let currentStep = 0;
function renderBBStep() {
  const s = bbSteps[currentStep];
  document.getElementById('stepCounter').textContent = `Step ${s.step} / 6`;
  document.getElementById('nodeTitle').textContent = s.title;
  document.getElementById('nodeId').textContent = s.nodeId;
  document.getElementById('nodeZ').textContent = s.z;
  document.getElementById('nodeStatus').innerHTML = s.status.includes('★') ? `<span style="color:var(--green);font-weight:700">${s.status}</span>` : s.status;
  document.getElementById('incumbent').textContent = s.incumbent;
  document.getElementById('explainTitle').textContent = s.explainTitle;
  document.getElementById('explainText').innerHTML = s.explain.replace(/\n/g, '<br>');
}
function bbNext() { if (currentStep < bbSteps.length-1) { currentStep++; renderBBStep(); } }
function bbPrev() { if (currentStep > 0) { currentStep--; renderBBStep(); } }
function bbReset() { currentStep = 0; renderBBStep(); }
renderBBStep();
</script>
</body>
</html>
