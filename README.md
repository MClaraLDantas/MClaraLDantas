<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Maria Clara — Retro Terminal</title>
<link href="https://fonts.googleapis.com/css2?family=VT323&family=Share+Tech+Mono&display=swap" rel="stylesheet">
<style>
  :root {
    --green: #00ff41;
    --green-dim: #00b32c;
    --green-glow: #00ff4188;
    --green-dark: #003b0f;
    --amber: #ffb000;
    --bg: #020d02;
    --scanline: rgba(0,0,0,0.18);
    --border: #00ff41;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: var(--bg);
    color: var(--green);
    font-family: 'Share Tech Mono', monospace;
    font-size: 15px;
    line-height: 1.7;
    min-height: 100vh;
    overflow-x: hidden;
    position: relative;
  }

  /* CRT scanlines overlay */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background: repeating-linear-gradient(
      0deg,
      transparent,
      transparent 2px,
      var(--scanline) 2px,
      var(--scanline) 4px
    );
    pointer-events: none;
    z-index: 100;
  }

  /* CRT vignette */
  body::after {
    content: '';
    position: fixed;
    inset: 0;
    background: radial-gradient(ellipse at center,
      transparent 60%,
      rgba(0,0,0,0.7) 100%);
    pointer-events: none;
    z-index: 99;
  }

  /* CRT screen flicker */
  @keyframes flicker {
    0%   { opacity: 1; }
    92%  { opacity: 1; }
    93%  { opacity: 0.94; }
    94%  { opacity: 1; }
    96%  { opacity: 0.97; }
    100% { opacity: 1; }
  }

  .crt-wrapper {
    animation: flicker 6s infinite;
    padding: 2rem 1rem;
    max-width: 860px;
    margin: 0 auto;
  }

  /* Glow text */
  .glow {
    text-shadow:
      0 0 6px var(--green),
      0 0 14px var(--green-glow),
      0 0 30px var(--green-glow);
  }

  .amber-glow {
    color: var(--amber);
    text-shadow:
      0 0 6px var(--amber),
      0 0 18px rgba(255,176,0,0.5);
  }

  /* ===== ASCII HEADER ===== */
  .ascii-header {
    font-family: 'VT323', monospace;
    font-size: clamp(14px, 2.5vw, 22px);
    white-space: pre;
    line-height: 1.2;
    color: var(--green);
    text-shadow:
      0 0 8px var(--green),
      0 0 20px var(--green-glow);
    overflow-x: auto;
    margin-bottom: 0.5rem;
  }

  /* ===== BOOT SEQUENCE ===== */
  .boot-block {
    border: 1px solid var(--green-dim);
    padding: 1rem 1.2rem;
    margin-bottom: 1.8rem;
    position: relative;
    background: rgba(0,255,65,0.02);
    box-shadow: 0 0 10px rgba(0,255,65,0.1), inset 0 0 20px rgba(0,0,0,0.5);
  }

  .boot-block::before {
    content: '[ BIOS v4.2.1 — SysInit OK ]';
    position: absolute;
    top: -0.6rem;
    left: 1rem;
    background: var(--bg);
    padding: 0 0.4rem;
    font-size: 11px;
    color: var(--green-dim);
    letter-spacing: 0.1em;
  }

  .boot-line {
    display: flex;
    gap: 1rem;
  }

  .boot-ok  { color: var(--green); }
  .boot-tag { color: var(--green-dim); min-width: 80px; display: inline-block; }

  /* ===== SECTION HEADERS ===== */
  .section-title {
    font-family: 'VT323', monospace;
    font-size: 1.6rem;
    color: var(--amber);
    text-shadow: 0 0 10px rgba(255,176,0,0.6);
    border-bottom: 1px solid var(--green-dim);
    padding-bottom: 0.2rem;
    margin: 2rem 0 1rem;
    letter-spacing: 0.08em;
  }

  .section-title::before { content: '>> '; color: var(--green); }

  /* ===== PANEL BOXES ===== */
  .panel {
    border: 1px solid var(--green-dim);
    padding: 1rem 1.2rem;
    margin-bottom: 1.4rem;
    position: relative;
    background: rgba(0,255,65,0.015);
    box-shadow: 0 0 8px rgba(0,255,65,0.06);
  }

  .panel-label {
    position: absolute;
    top: -0.6rem;
    left: 1rem;
    background: var(--bg);
    padding: 0 0.4rem;
    font-size: 11px;
    color: var(--green-dim);
    letter-spacing: 0.12em;
    text-transform: uppercase;
  }

  /* ===== SKILLS GRID ===== */
  .skills-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
    gap: 0.6rem;
    margin-top: 0.5rem;
  }

  .skill-chip {
    border: 1px solid var(--green-dim);
    padding: 0.3rem 0.6rem;
    font-size: 13px;
    text-align: center;
    position: relative;
    overflow: hidden;
    transition: all 0.2s;
    cursor: default;
  }

  .skill-chip::before {
    content: '';
    position: absolute;
    inset: 0;
    background: var(--green);
    transform: translateX(-100%);
    transition: transform 0.25s ease;
    z-index: 0;
    opacity: 0.12;
  }

  .skill-chip:hover::before { transform: translateX(0); }
  .skill-chip:hover {
    color: #fff;
    border-color: var(--green);
    box-shadow: 0 0 10px var(--green-glow);
    text-shadow: 0 0 6px var(--green);
  }

  .skill-chip span { position: relative; z-index: 1; }

  /* ===== PROJECTS TABLE ===== */
  .proj-table {
    width: 100%;
    border-collapse: collapse;
    font-size: 13.5px;
  }

  .proj-table th {
    font-family: 'VT323', monospace;
    font-size: 1.05rem;
    color: var(--amber);
    text-align: left;
    padding: 0.4rem 0.8rem;
    border-bottom: 1px solid var(--green-dim);
    letter-spacing: 0.06em;
  }

  .proj-table td {
    padding: 0.5rem 0.8rem;
    border-bottom: 1px solid rgba(0,255,65,0.08);
    vertical-align: top;
    color: var(--green-dim);
  }

  .proj-table td:first-child {
    color: var(--green);
    white-space: nowrap;
  }

  .proj-table tr:hover td {
    background: rgba(0,255,65,0.04);
    color: var(--green);
  }

  .proj-tag {
    display: inline-block;
    border: 1px solid var(--green-dim);
    padding: 0 0.4rem;
    font-size: 11px;
    color: var(--green-dim);
    margin-left: 0.3rem;
  }

  /* ===== INTERNALS TABLE ===== */
  .int-table {
    width: 100%;
    border-collapse: collapse;
    font-size: 13px;
  }

  .int-table th {
    font-family: 'VT323', monospace;
    font-size: 1rem;
    color: var(--amber);
    padding: 0.4rem 0.7rem;
    text-align: left;
    border-bottom: 1px solid var(--green-dim);
  }

  .int-table td {
    padding: 0.45rem 0.7rem;
    border-bottom: 1px solid rgba(0,255,65,0.07);
    color: var(--green-dim);
  }

  .int-table td:first-child {
    color: var(--green);
    font-weight: bold;
  }

  .int-table tr:hover td { color: var(--green); background: rgba(0,255,65,0.03); }

  /* ===== PROGRESS BARS ===== */
  .progress-row {
    display: flex;
    align-items: center;
    gap: 0.8rem;
    margin-bottom: 0.5rem;
  }

  .progress-label {
    min-width: 120px;
    font-size: 13px;
  }

  .progress-bar-wrap {
    flex: 1;
    height: 12px;
    border: 1px solid var(--green-dim);
    background: rgba(0,0,0,0.3);
    position: relative;
    overflow: hidden;
  }

  .progress-fill {
    height: 100%;
    background: var(--green);
    box-shadow: 0 0 8px var(--green-glow);
    position: relative;
    animation: loadbar 1.5s ease-out forwards;
    transform-origin: left;
  }

  @keyframes loadbar {
    from { width: 0 !important; }
    to   { /* width set inline */ }
  }

  .progress-val {
    font-size: 12px;
    color: var(--green-dim);
    min-width: 36px;
    text-align: right;
  }

  /* Phosphor scanline on bar */
  .progress-fill::after {
    content: '';
    position: absolute;
    inset: 0;
    background: repeating-linear-gradient(
      90deg,
      transparent,
      transparent 3px,
      rgba(0,0,0,0.2) 3px,
      rgba(0,0,0,0.2) 4px
    );
  }

  /* ===== BLINKING CURSOR ===== */
  @keyframes blink { 50% { opacity: 0; } }

  .cursor {
    display: inline-block;
    width: 9px;
    height: 1.1em;
    background: var(--green);
    vertical-align: middle;
    margin-left: 2px;
    animation: blink 1s step-end infinite;
    box-shadow: 0 0 6px var(--green);
  }

  /* ===== PROMPT LINE ===== */
  .prompt {
    color: var(--green-dim);
    margin-bottom: 0.15rem;
  }
  .prompt .user { color: var(--green); }
  .prompt .at   { color: var(--green-dim); }
  .prompt .path { color: var(--amber); }
  .prompt .sym  { color: var(--green); }

  /* ===== TYPING ANIMATION ===== */
  @keyframes typing {
    from { width: 0; }
    to   { width: 100%; }
  }

  .type-line {
    overflow: hidden;
    white-space: nowrap;
    display: inline-block;
    animation: typing 1.4s steps(40, end) forwards;
  }

  /* ===== LINKS / CONNECT ===== */
  .connect-row {
    display: flex;
    flex-wrap: wrap;
    gap: 0.8rem;
    margin-top: 0.5rem;
  }

  .connect-btn {
    display: inline-flex;
    align-items: center;
    gap: 0.4rem;
    border: 1px solid var(--green-dim);
    padding: 0.35rem 0.9rem;
    color: var(--green-dim);
    text-decoration: none;
    font-family: 'Share Tech Mono', monospace;
    font-size: 13px;
    transition: all 0.2s;
    position: relative;
    overflow: hidden;
  }

  .connect-btn::before {
    content: '';
    position: absolute;
    inset: 0;
    background: var(--green);
    transform: translateX(-100%);
    transition: 0.25s ease;
    opacity: 0.1;
    z-index: 0;
  }

  .connect-btn:hover::before { transform: translateX(0); }
  .connect-btn:hover {
    color: var(--green);
    border-color: var(--green);
    box-shadow: 0 0 12px var(--green-glow);
    text-shadow: 0 0 4px var(--green);
  }

  .connect-btn span { position: relative; z-index: 1; }

  /* ===== FOOTER ===== */
  .footer-block {
    border: 1px solid var(--green-dim);
    padding: 1rem 1.2rem;
    text-align: center;
    margin-top: 2rem;
    background: rgba(0,255,65,0.015);
    box-shadow: 0 0 20px rgba(0,255,65,0.08);
  }

  .footer-block code {
    font-family: 'VT323', monospace;
    font-size: 1.2rem;
    color: var(--green);
    text-shadow: 0 0 8px var(--green-glow);
  }

  /* ===== STATUS BAR ===== */
  .status-bar {
    display: flex;
    justify-content: space-between;
    align-items: center;
    border: 1px solid var(--green-dim);
    padding: 0.3rem 1rem;
    margin-bottom: 1.6rem;
    font-size: 12px;
    color: var(--green-dim);
    background: rgba(0,0,0,0.4);
  }

  .status-dot {
    width: 8px; height: 8px;
    border-radius: 50%;
    background: var(--green);
    display: inline-block;
    margin-right: 6px;
    box-shadow: 0 0 6px var(--green);
    animation: blink 2s ease-in-out infinite;
  }

  /* ===== HORIZONTAL RULE ===== */
  .retro-hr {
    border: none;
    border-top: 1px solid var(--green-dim);
    margin: 0.4rem 0 1.2rem;
    box-shadow: 0 0 6px rgba(0,255,65,0.15);
  }

  /* ===== RESPONSIVE ===== */
  @media (max-width: 600px) {
    .ascii-header { font-size: 10px; }
    .skills-grid  { grid-template-columns: repeat(2, 1fr); }
    .proj-table th:nth-child(3),
    .proj-table td:nth-child(3),
    .int-table th:nth-child(3),
    .int-table td:nth-child(3) { display: none; }
  }
</style>
</head>
<body>
<div class="crt-wrapper">

  <!-- STATUS BAR -->
  <div class="status-bar">
    <div><span class="status-dot"></span>ONLINE — TERMINAL v6.66</div>
    <div>CPU: 99.9% | MEM: FULLY COMMITTED</div>
    <div>2026-03-05 · CE-BR · UNIFOR</div>
  </div>

  <!-- ASCII ART HEADER -->
  <pre class="ascii-header glow">
 ███╗   ███╗ █████╗ ██████╗ ██╗ █████╗      ██████╗██╗      █████╗ ██████╗  █████╗
 ████╗ ████║██╔══██╗██╔══██╗██║██╔══██╗    ██╔════╝██║     ██╔══██╗██╔══██╗██╔══██╗
 ██╔████╔██║███████║██████╔╝██║███████║    ██║     ██║     ███████║██████╔╝███████║
 ██║╚██╔╝██║██╔══██║██╔══██╗██║██╔══██║    ██║     ██║     ██╔══██║██╔══██╗██╔══██║
 ██║ ╚═╝ ██║██║  ██║██║  ██║██║██║  ██║    ╚██████╗███████╗██║  ██║██║  ██║██║  ██║
 ╚═╝     ╚═╝╚═╝  ╚═╝╚═╝  ╚═╝╚═╝╚═╝  ╚═╝    ╚═════╝╚══════╝╚═╝  ╚═╝╚═╝  ╚═╝╚═╝  ╚═╝</pre>

  <div style="text-align:center; font-family:'VT323',monospace; font-size:1.1rem; color:var(--green-dim); margin-bottom:1.5rem; letter-spacing:.2em;">
    ━━━ LOW-LEVEL &amp; EMBEDDED SYSTEMS ENGINEER ━━━
  </div>

  <!-- BOOT SEQUENCE -->
  <div class="boot-block">
    <div style="font-family:'VT323',monospace; font-size:1.3rem; color:var(--amber); margin-bottom:.6rem; letter-spacing:.1em;">SYSTEM BOOT LOG</div>
    <div class="boot-line"><span class="boot-tag amber-glow">[  OK  ]</span> <span>Kernel carregado — foco em Sistemas Embarcados &amp; Baixo Nível</span></div>
    <div class="boot-line"><span class="boot-tag amber-glow">[  OK  ]</span> <span>Módulo de pesquisa iniciado — Computação Paralela &amp; Visão Computacional</span></div>
    <div class="boot-line"><span class="boot-tag amber-glow">[  OK  ]</span> <span>Localização detectada — <code>Fortaleza, CE, BR</code></span></div>
    <div class="boot-line"><span class="boot-tag amber-glow">[  OK  ]</span> <span>Status acadêmico — Eng. Computação · UNIFOR · Conclusão 2026.2</span></div>
    <div class="boot-line"><span class="boot-tag amber-glow">[ LANG ]</span> <span>Idiomas carregados — PT-BR (nativo) &amp; EN (avançado)</span></div>
    <div class="boot-line" style="margin-top:.5rem;">
      <span class="boot-tag" style="color:var(--green);">[ RUN  ]</span>
      <span class="glow">while(alive) { code(); }<span class="cursor"></span></span>
    </div>
  </div>

  <!-- PROMPT -->
  <div class="prompt">
    <span class="user">mariaclara</span><span class="at">@</span><span class="user">unifor-kernel</span><span class="sym">:</span><span class="path">~/hardware-stack</span><span class="sym">$</span>
    <span style="margin-left:.5rem;">ls -la skills/</span>
  </div>
  <hr class="retro-hr">

  <!-- HARDWARE STACK -->
  <div class="section-title">Hardware Stack</div>
  <div class="panel">
    <div class="panel-label">MODULES LOADED</div>
    <div class="skills-grid">
      <div class="skill-chip"><span>■ C</span></div>
      <div class="skill-chip"><span>■ C++</span></div>
      <div class="skill-chip"><span>■ Python</span></div>
      <div class="skill-chip"><span>■ Linux Kernel</span></div>
      <div class="skill-chip"><span>■ Arduino</span></div>
      <div class="skill-chip"><span>■ YOLO / PDI</span></div>
      <div class="skill-chip"><span>■ SQL / MySQL</span></div>
      <div class="skill-chip"><span>■ JS / Node.js</span></div>
      <div class="skill-chip"><span>■ UML / Req.</span></div>
      <div class="skill-chip"><span>■ Parallel HPC</span></div>
    </div>
  </div>

  <!-- SKILL LEVELS -->
  <div class="panel">
    <div class="panel-label">PERFORMANCE METRICS</div>
    <div style="margin-top:.4rem;">
      <div class="progress-row">
        <div class="progress-label">C / C++</div>
        <div class="progress-bar-wrap"><div class="progress-fill" style="width:88%"></div></div>
        <div class="progress-val">88%</div>
      </div>
      <div class="progress-row">
        <div class="progress-label">Linux Kernel</div>
        <div class="progress-bar-wrap"><div class="progress-fill" style="width:80%"></div></div>
        <div class="progress-val">80%</div>
      </div>
      <div class="progress-row">
        <div class="progress-label">Parallel HPC</div>
        <div class="progress-bar-wrap"><div class="progress-fill" style="width:78%"></div></div>
        <div class="progress-val">78%</div>
      </div>
      <div class="progress-row">
        <div class="progress-label">Edge AI / YOLO</div>
        <div class="progress-bar-wrap"><div class="progress-fill" style="width:74%"></div></div>
        <div class="progress-val">74%</div>
      </div>
      <div class="progress-row">
        <div class="progress-label">Python</div>
        <div class="progress-bar-wrap"><div class="progress-fill" style="width:82%"></div></div>
        <div class="progress-val">82%</div>
      </div>
    </div>
  </div>

  <!-- PROJECTS -->
  <div class="section-title">Projetos Académicos</div>
  <div class="panel">
    <div class="panel-label">PROJECT INDEX</div>
    <table class="proj-table">
      <thead>
        <tr>
          <th>ID</th>
          <th>Nome</th>
          <th>Stack</th>
          <th>Descrição</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>P-001</td>
          <td>WSClock<span class="proj-tag">OS</span></td>
          <td>[C]</td>
          <td>Substituição de páginas no kernel — implementação &amp; benchmark</td>
        </tr>
        <tr>
          <td>P-002</td>
          <td>MatMul-Dist<span class="proj-tag">HPC</span></td>
          <td>[C/PY]</td>
          <td>Multiplicação de Matrizes Distribuída com computação concorrente</td>
        </tr>
        <tr>
          <td>P-003</td>
          <td>EdgeAI-YOLO<span class="proj-tag">CV</span></td>
          <td>[PY]</td>
          <td>Detecção de objetos com redes neurais YOLO + técnicas de PDI</td>
        </tr>
        <tr>
          <td>P-004</td>
          <td>DataEnv-SQL<span class="proj-tag">DB</span></td>
          <td>[SQL]</td>
          <td>Modelos relacionais e queries otimizadas em MySQL</td>
        </tr>
        <tr>
          <td>P-005</td>
          <td>SysReq-UML<span class="proj-tag">SE</span></td>
          <td>[UML]</td>
          <td>Levantamento de requisitos funcionais &amp; modelagem em UML</td>
        </tr>
      </tbody>
    </table>
  </div>

  <!-- SYSTEM INTERNALS -->
  <div class="section-title">System Internals</div>
  <div class="panel">
    <div class="panel-label">KERNEL MODULES</div>
    <table class="int-table">
      <thead>
        <tr>
          <th>Módulo</th>
          <th>Conhecimento Técnico</th>
          <th>Aplicação</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>Kernel &amp; OS</td>
          <td>Memória &amp; Processos</td>
          <td>Escalonamento e paginação</td>
        </tr>
        <tr>
          <td>HPC</td>
          <td>Parallel Computing</td>
          <td>Sistemas distribuídos &amp; concorrentes</td>
        </tr>
        <tr>
          <td>Edge AI</td>
          <td>YOLO &amp; PDI</td>
          <td>Visão computacional em baixo nível</td>
        </tr>
        <tr>
          <td>Full Stack</td>
          <td>JS &amp; Node.js</td>
          <td>Apps de precificação &amp; gestão</td>
        </tr>
      </tbody>
    </table>
  </div>

  <!-- CONNECT -->
  <div class="section-title">System Connectivity</div>
  <div class="panel">
    <div class="panel-label">NETWORK INTERFACES</div>
    <div class="connect-row">
      <a class="connect-btn" href="https://www.linkedin.com/in/lucas-nathan-a83418242/" target="_blank">
        <span>▶ LINKEDIN · /in/mariaclara</span>
      </a>
      <a class="connect-btn" href="mailto:lucasnathan.marc@gmail.com" target="_blank">
        <span>▶ EMAIL · lucasnathan.marc@gmail.com</span>
      </a>
    </div>
  </div>

  <!-- FOOTER -->
  <div class="footer-block">
    <div style="font-family:'VT323',monospace; font-size:2rem; color:var(--green-dim); letter-spacing:.3em; margin-bottom:.4rem;">
      ░░░ DEBUG OUTPUT ░░░
    </div>
    <code>
      [ SUCCESS ] Maria Clara is ready for new engineering challenges.<span class="cursor"></span>
    </code>
    <div style="margin-top:.8rem; font-size:12px; color:var(--green-dim); letter-spacing:.1em;">
      ── SYSTEM UPTIME: SINCE 2002 ── MISSION: KERNEL &amp; BEYOND ──
    </div>
  </div>

  <!-- FINAL PROMPT -->
  <div style="margin-top:1.5rem;" class="prompt">
    <span class="user">mariaclara</span><span class="at">@</span><span class="user">unifor-kernel</span><span class="sym">:</span><span class="path">~</span><span class="sym">$</span>
    <span style="margin-left:.5rem;"></span><span class="cursor"></span>
  </div>

</div><!-- end .crt-wrapper -->
</body>
</html>
