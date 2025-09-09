<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Neon Wake — A Tiny Browser Game</title>
  <style>
    :root{
      --bg:#0b1020;
      --panel:#121a33;
      --accent:#5af2ff;
      --accent2:#ff6ad5;
      --accent3:#8aff6a;
      --text:#e6f1ff;
      --muted:#99a6c4;
      --warn:#ffaf40;
      --good:#5aff9f;
      --bad:#ff6969;
    }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0;
      font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Helvetica, Arial, "Apple Color Emoji", "Segoe UI Emoji";
      color:var(--text);
      background:
        radial-gradient(1000px 700px at 80% -10%, rgba(90,242,255,0.10), transparent 50%),
        radial-gradient(800px 600px at 10% 110%, rgba(255,106,213,0.10), transparent 50%),
        linear-gradient(160deg, #060a18, var(--bg) 35%);
    }

    /* Simulated website shell */
    header{
      position:sticky; top:0; z-index:10;
      backdrop-filter: blur(8px);
      background: linear-gradient(0deg, rgba(18,26,51,0.7), rgba(18,26,51,0.9));
      border-bottom:1px solid rgba(255,255,255,0.05);
    }
    .container{max-width:1100px; margin:0 auto; padding:0 16px}
    .topbar{
      display:flex; align-items:center; gap:16px; padding:14px 0;
    }
    .logo{
      display:flex; gap:10px; align-items:center; font-weight:800; letter-spacing:.4px;
    }
    .logo-badge{
      width:28px; height:28px; border-radius:8px;
      background: radial-gradient(120% 120% at 0% 0%, var(--accent), transparent 60%),
                  radial-gradient(120% 120% at 100% 100%, var(--accent2), transparent 60%),
                  #0f1430; 
      box-shadow: 0 0 16px rgba(90,242,255,0.35), inset 0 0 12px rgba(255,255,255,0.06);
      position:relative;
    }
    .logo-badge:after{
      content:""; position:absolute; inset:6px;
      border-radius:6px; border:2px solid rgba(255,255,255,0.08)
    }
    nav{
      margin-left:auto; display:flex; gap:10px; flex-wrap:wrap;
    }
    nav a{
      color:var(--text); text-decoration:none; font-weight:600; font-size:14px;
      padding:10px 12px; border-radius:10px;
      border:1px solid rgba(255,255,255,0.06);
      background: rgba(255,255,255,0.02);
    }
    nav a.active, nav a:hover{border-color:rgba(255,255,255,0.18); background:rgba(255,255,255,0.06)}
    main{padding:26px 0 40px}
    .grid{
      display:grid; gap:18px; grid-template-columns: 1.3fr .7fr;
    }
    .panel{
      background: linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.005));
      border:1px solid rgba(255,255,255,0.06);
      border-radius:18px; padding:18px; position:relative; overflow:hidden;
    }
    .panel h2{margin:0 0 8px 0; font-size:18px}
    .sub{color:var(--muted); font-size:13px}
    .btn{
      appearance:none; border:none; cursor:pointer;
      padding:12px 14px; border-radius:12px; font-weight:700; font-size:14px;
      color:#061023; background:linear-gradient(180deg, var(--accent), #56e6f2);
      box-shadow: 0 6px 20px rgba(90,242,255,0.26);
    }
    .btn.secondary{
      color:var(--text);
      background: linear-gradient(180deg, #232a4a, #1a2242);
      border:1px solid rgba(255,255,255,0.06);
      box-shadow:none;
    }
    .row{display:flex; gap:10px; align-items:center; flex-wrap:wrap}
    .pill{
      font-size:12px; padding:6px 10px; border-radius:999px;
      border:1px solid rgba(255,255,255,0.08); color:var(--muted);
      background: rgba(255,255,255,0.03);
    }
    .kbd{font-family: ui-monospace, SFMono-Regular, Menlo, Consolas, "Liberation Mono", monospace; font-weight:700; color:var(--text)}

    /* Game canvas area */
    #gameWrap{position:relative; min-height:520px}
    canvas{display:block; width:100%; height:420px; background: radial-gradient(140% 100% at 50% 0%, #0d1736, #060a1a 60%); border-radius:14px; border:1px solid rgba(255,255,255,0.07)}
    .hud{
      display:flex; gap:10px; align-items:center; flex-wrap:wrap;
      margin-top:12px;
    }
    .indicator{padding:8px 10px; border-radius:10px; border:1px solid rgba(255,255,255,0.08); background:rgba(255,255,255,0.03); font-size:13px}
    .colorDot{display:inline-block; width:12px; height:12px; border-radius:50%; margin-right:6px; box-shadow:0 0 8px currentColor}
    .c1{color:var(--accent)}
    .c2{color:var(--accent2)}
    .c3{color:var(--accent3)}
    .mobile-controls{display:none; gap:8px; margin-top:6px}
    .mobile-controls .btn{padding:10px 12px; font-weight:800}

    /* Secondary column */
    .card{
      background: linear-gradient(180deg, rgba(255,255,255,0.03), rgba(255,255,255,0.01));
      border:1px solid rgba(255,255,255,0.06);
      border-radius:16px; padding:14px;
    }
    .card h3{margin:6px 0 8px 0; font-size:16px}
    .list{display:flex; flex-direction:column; gap:6px}
    .list li{display:flex; justify-content:space-between; gap:10px; font-size:14px}
    .dim{color:var(--muted)}

    /* Sections (SPA) */
    section{display:none}
    section.active{display:block}
    .content{max-width:900px}
    .content h1{margin:0 0 8px 0}
    .content p{color:var(--muted); line-height:1.6}

    /* Settings */
    label.switch{
      display:inline-flex; align-items:center; gap:10px; padding:10px 12px; border-radius:12px;
      border:1px solid rgba(255,255,255,0.06);
      background: rgba(255,255,255,0.02);
    }
    .switch input{appearance:none; width:42px; height:24px; background:#1b2446; border-radius:999px; position:relative; outline:none; border:1px solid rgba(255,255,255,0.08)}
    .switch input:before{
      content:""; position:absolute; top:2px; left:2px; width:18px; height:18px; border-radius:50%; background:#fff; transition:transform .2s ease;
    }
    .switch input:checked{background:#244f7a}
    .switch input:checked:before{transform:translateX(18px)}

    footer{
      text-align:center; color:var(--muted); font-size:12px; padding:24px 0 40px; 
    }

    @media (max-width: 980px){
      .grid{grid-template-columns: 1fr}
      .mobile-controls{display:flex}
    }
  </style>
</head>
<body>
  <header>
    <div class="container">
      <div class="topbar">
        <div class="logo">
          <span class="logo-badge" aria-hidden="true"></span>
          <span>Neon Wake</span>
          <span class="pill">alpha</span>
        </div>
        <nav id="nav">
          <a href="#/home" data-route>Home</a>
          <a href="#/how" data-route>How to Play</a>
          <a href="#/hall" data-route>Hall of Fame</a>
          <a href="#/about" data-route>About</a>
          <a href="#/settings" data-route>Settings</a>
        </nav>
      </div>
    </div>
  </header>

  <main class="container">
    <!-- HOME / GAME -->
    <section id="home" class="active">
      <div class="grid">
        <div class="panel">
          <h2>Arcade</h2>
          <p class="sub">Surf a neon sea. Shift your boat's light-frequency to phase through matching hazards.</p>
          <div id="gameWrap">
            <canvas id="game" width="1024" height="540" aria-label="Neon Wake game canvas"></canvas>
            <div class="hud">
              <span class="indicator">Score: <b id="score">0</b></span>
              <span class="indicator">Level: <b id="level">1</b></span>
              <span class="indicator">Lives: <b id="lives">3</b></span>
              <span class="indicator">Mode:
                <span class="colorDot c1"></span><b id="modeName">Cyan</b>
              </span>
              <span class="indicator">Switch: <span class="kbd">1</span>/<span class="kbd">2</span>/<span class="kbd">3</span> • Move: <span class="kbd">←</span>/<span class="kbd">→</span></span>
              <div class="mobile-controls">
                <button class="btn secondary" id="leftBtn">◀</button>
                <button class="btn secondary" id="rightBtn">▶</button>
                <button class="btn" id="m1">1</button>
                <button class="btn" id="m2">2</button>
                <button class="btn" id="m3">3</button>
                <button class="btn secondary" id="pauseBtn">⏸</button>
              </div>
            </div>
          </div>
        </div>
        <div class="panel">
          <div class="card">
            <h3>How it works</h3>
            <p class="sub">Obstacles are color‑coded waves. You can safely pass through any wave that matches your light mode.</p>
            <ul class="list">
              <li><span><span class="colorDot c1"></span>Cyan Mode</span><span class="dim">safe through cyan</span></li>
              <li><span><span class="colorDot c2"></span>Magenta Mode</span><span class="dim">safe through magenta</span></li>
              <li><span><span class="colorDot c3"></span>Lime Mode</span><span class="dim">safe through lime</span></li>
            </ul>
          </div>
          <div class="card">
            <h3>Quick actions</h3>
            <div class="row">
              <button id="startBtn" class="btn">Start</button>
              <button id="pauseBtn2" class="btn secondary">Pause</button>
              <button id="resetBtn" class="btn secondary">Reset</button>
            </div>
          </div>
          <div class="card">
            <h3>Recent Scores</h3>
            <div id="recentScores" class="list"></div>
          </div>
        </div>
      </div>
    </section>

    <!-- HOW TO PLAY -->
    <section id="how">
      <div class="content">
        <h1>How to Play</h1>
        <p><b>Goal:</b> survive as long as possible, collect beacons (+50), and avoid mismatched waves (−1 life).</p>
        <ol>
          <li>Use <span class="kbd">←</span> / <span class="kbd">→</span> to steer.</li>
          <li>Tap <span class="kbd">1</span>, <span class="kbd">2</span>, or <span class="kbd">3</span> to change your light-frequency mode.</li>
          <li>You can <u>pass through</u> any obstacle that matches your current mode color.</li>
          <li>Survive waves to increase your <b>Level</b>; higher levels move faster and spawn trickier patterns.</li>
          <li>Grab <b>beacons</b> for points. Every 1000 points grants an extra life.</li>
        </ol>
      </div>
    </section>

    <!-- HALL OF FAME -->
    <section id="hall">
      <div class="content">
        <h1>Hall of Fame</h1>
        <p>Highest runs on this device (stored locally).</p>
        <div id="hallTable"></div>
      </div>
    </section>

    <!-- ABOUT -->
    <section id="about">
      <div class="content">
        <h1>About Neon Wake</h1>
        <p>Designed as a mini‑site to feel like a full game portal, but it’s all in one file. Built with Canvas and love.</p>
        <p>Concept: a luminous boat surfing spectral seas. Flip your LEDs to phase with the world.</p>
      </div>
    </section>

    <!-- SETTINGS -->
    <section id="settings">
      <div class="content">
        <h1>Settings</h1>
        <p class="sub">These settings are saved to your browser.</p>
        <div class="row" style="gap:14px; flex-wrap:wrap">
          <label class="switch">
            <span>Screen shake</span>
            <input id="shakeToggle" type="checkbox" checked />
          </label>
          <label class="switch">
            <span>SFX</span>
            <input id="sfxToggle" type="checkbox" checked />
          </label>
          <button id="wipeData" class="btn secondary">Clear saved data</button>
        </div>
      </div>
    </section>
  </main>

  <footer>
    © <span id="year"></span> Neon Wake. Made for fun.
  </footer>

<script>
(() => {
  const $ = sel => document.querySelector(sel);
  const $$ = sel => Array.from(document.querySelectorAll(sel));
  const clamp = (v, a, b) => Math.max(a, Math.min(b, v));
  const rand = (a,b) => Math.random()*(b-a)+a;
  const choice = (arr) => arr[(Math.random()*arr.length)|0];

  // SPA Router
  function route(){
    const hash = location.hash || "#/home";
    const page = hash.split("/")[1];
    $$("nav a").forEach(a => a.classList.toggle("active", a.getAttribute("href") === "#/"+page));
    $$("main section").forEach(s => s.classList.remove("active"));
    const sec = $("#"+page);
    if(sec) sec.classList.add("active");
    if(page==="hall") renderHall();
  }
  window.addEventListener("hashchange", route);
  route();

  // Year
  $("#year").textContent = new Date().getFullYear();

  // Settings
  const Settings = {
    key: "neonwake_settings",
    data: { shake:true, sfx:true },
    load(){
      try{
        const raw = localStorage.getItem(this.key);
        if(raw) this.data = Object.assign(this.data, JSON.parse(raw));
      }catch(e){}
      $("#shakeToggle").checked = !!this.data.shake;
      $("#sfxToggle").checked = !!this.data.sfx;
    },
    save(){
      this.data.shake = $("#shakeToggle").checked;
      this.data.sfx = $("#sfxToggle").checked;
      localStorage.setItem(this.key, JSON.stringify(this.data));
    }
  };
  Settings.load();
  $("#shakeToggle").addEventListener("change", ()=>Settings.save());
  $("#sfxToggle").addEventListener("change", ()=>Settings.save());
  $("#wipeData").addEventListener("click", ()=>{
    if(confirm("Clear local highscores and settings?")){
      localStorage.removeItem(Hiscore.key);
      localStorage.removeItem(Settings.key);
      Settings.load();
      renderHall();
      alert("Cleared!");
    }
  });

  // Scores
  const Hiscore = {
    key: "neonwake_scores",
    list: [],
    load(){ try{ this.list = JSON.parse(localStorage.getItem(this.key)||"[]"); }catch(e){ this.list=[] } },
    save(){ localStorage.setItem(this.key, JSON.stringify(this.list.slice(0,20))); },
    add(entry){
      this.list.push(entry);
      this.list.sort((a,b)=>b.score-a.score);
      this.save();
    }
  };
  Hiscore.load();

  function renderRecent(){
    const el = $("#recentScores");
    el.innerHTML = Hiscore.list.slice(0,5).map(s => 
      `<div class="row" style="justify-content:space-between">
         <span>${new Date(s.date).toLocaleString()}</span>
         <b>${s.score}</b>
       </div>`
    ).join("") || '<div class="dim">No runs yet.</div>';
  }
  function renderHall(){
    const el = $("#hallTable");
    if(!Hiscore.list.length){ el.innerHTML = '<p class="dim">No scores yet.</p>'; return; }
    el.innerHTML = `
      <div class="panel">
        <div class="list">
          ${Hiscore.list.slice(0,20).map((s,i)=>`
            <div style="display:grid; grid-template-columns: 40px 1fr 120px 100px; gap:8px; align-items:center">
              <div class="pill">${i+1}</div>
              <div><b>${s.name||"Pilot"}</b><div class="dim" style="font-size:12px">${new Date(s.date).toLocaleString()}</div></div>
              <div><b>${s.score}</b> pts</div>
              <div class="dim">Lv ${s.level}</div>
            </div>
          `).join("")}
        </div>
      </div>
    `;
  }
  renderRecent();

  // Audio (tiny synth beeps)
  let ac;
  function ping(freq=440, dur=0.08, type="sine", vol=0.2){
    if(!Settings.data.sfx) return;
    try{
      ac = ac || new (window.AudioContext||window.webkitAudioContext)();
      const o = ac.createOscillator();
      const g = ac.createGain();
      o.type = type; o.frequency.value = freq;
      g.gain.setValueAtTime(vol, ac.currentTime);
      g.gain.exponentialRampToValueAtTime(0.0001, ac.currentTime + dur);
      o.connect(g); g.connect(ac.destination);
      o.start(); o.stop(ac.currentTime + dur);
    }catch(e){}
  }

  // Game
  const canvas = $("#game");
  const ctx = canvas.getContext("2d");
  let W, H, DPR=window.devicePixelRatio||1;
  function resize(){
    const rect = canvas.getBoundingClientRect();
    W = Math.floor(rect.width*DPR);
    H = Math.floor(rect.height*DPR);
    canvas.width = W; canvas.height = H;
  }
  resize(); window.addEventListener("resize", resize);

  const Modes = [
    {name:"Cyan", color:"#5af2ff"},
    {name:"Magenta", color:"#ff6ad5"},
    {name:"Lime", color:"#8aff6a"},
  ];

  const state = {
    running:false, paused:false,
    score:0, level:1, lives:3,
    mode:0, speed:2.0, tick:0,
    entities:[], beacons:[],
    px:0.5, vx:0, inputLeft:false, inputRight:false,
  };

  const ui = {
    score: $("#score"), level: $("#level"), lives: $("#lives"), modeName: $("#modeName")
  };

  function reset(){
    state.running=false; state.paused=false;
    state.score=0; state.level=1; state.lives=3; state.speed=2;
    state.tick=0; state.entities.length=0; state.beacons.length=0;
    state.px=0.5; state.vx=0; state.mode=0;
    shake(0);
    updateHUD();
    drawSplash();
  }

  function updateHUD(){
    ui.score.textContent = state.score|0;
    ui.level.textContent = state.level;
    ui.lives.textContent = state.lives;
    ui.modeName.textContent = Modes[state.mode].name;
    ui.modeName.parentElement.parentElement.querySelector(".colorDot").className = "colorDot " + (state.mode===0?'c1':state.mode===1?'c2':'c3');
  }

  function start(){
    if(state.running) return;
    state.running = true;
    state.paused = false;
    state.score = 0;
    state.level = 1;
    state.lives = 3;
    state.speed = 2;
    state.entities = [];
    state.beacons = [];
    state.tick = 0;
    spawnPattern();
    ping(660,0.09,"triangle");
  }

  function togglePause(){
    if(!state.running) return;
    state.paused = !state.paused;
  }

  // Input
  window.addEventListener("keydown", (e)=>{
    if(e.key==="ArrowLeft" || e.key==="a"){ state.inputLeft = true; }
    if(e.key==="ArrowRight" || e.key==="d"){ state.inputRight = true; }
    if(e.key==="1") setMode(0);
    if(e.key==="2") setMode(1);
    if(e.key==="3") setMode(2);
    if(e.key===" "){ e.preventDefault(); if(!state.running) start(); else togglePause(); }
  });
  window.addEventListener("keyup", (e)=>{
    if(e.key==="ArrowLeft" || e.key==="a"){ state.inputLeft = false; }
    if(e.key==="ArrowRight" || e.key==="d"){ state.inputRight = false; }
  });

  $("#leftBtn").addEventListener("touchstart", ()=>state.inputLeft=true);
  $("#leftBtn").addEventListener("touchend", ()=>state.inputLeft=false);
  $("#rightBtn").addEventListener("touchstart", ()=>state.inputRight=true);
  $("#rightBtn").addEventListener("touchend", ()=>state.inputRight=false);
  $("#m1").addEventListener("click", ()=>setMode(0));
  $("#m2").addEventListener("click", ()=>setMode(1));
  $("#m3").addEventListener("click", ()=>setMode(2));
  $("#pauseBtn").addEventListener("click", togglePause);
  $("#startBtn").addEventListener("click", start);
  $("#pauseBtn2").addEventListener("click", togglePause);
  $("#resetBtn").addEventListener("click", reset);

  function setMode(m){
    if(m===state.mode) return;
    state.mode = m;
    updateHUD();
    ping([550,700,420][m],0.05,"square",0.18);
  }

  // Entities
  function spawnPattern(){
    // Spawn a wave row of obstacles with gaps and beacons
    const cols = 8;
    const gapCount = Math.max(1, 3 - Math.floor(state.level/3));
    const safeCols = new Set();
    while(safeCols.size < gapCount) safeCols.add((Math.random()*cols)|0);
    const modeForRow = Math.random()<0.65 ? (Math.random()*3|0) : -1; // either consistent or mixed row

    for(let c=0; c<cols; c++){
      if(safeCols.has(c) && Math.random()<0.7){
        // maybe place a beacon in a gap
        if(Math.random() < 0.35){
          state.beacons.push({
            x:(c+0.5)/cols, y:-0.05, r:0.012, vy: state.speed*0.0025, take:false
          });
        }
        continue;
      }
      const mode = modeForRow===-1 ? (Math.random()*3|0) : modeForRow;
      state.entities.push({
        x:(c+0.5)/cols, y:-0.05, w:0.12, h:0.08, mode,
        vy: state.speed*0.003
      });
    }
  }

  let shakePower = 0;
  function shake(p){ shakePower = Settings.data.shake ? Math.max(shakePower, p) : 0; }

  function tick(dt){
    if(!state.running || state.paused) return;

    state.tick += dt;
    const accel = 0.0035*dt;
    const fric = 0.92;

    if(state.inputLeft) state.vx -= accel;
    if(state.inputRight) state.vx += accel;
    state.vx *= fric;
    state.px = clamp(state.px + state.vx, 0.05, 0.95);

    // Progress difficulty
    if(state.tick > 2500){
      state.tick = 0;
      state.level++;
      state.speed *= 1.08;
      ping(800,0.06,"sawtooth",0.12);
    }

    // Spawn rows
    if(Math.random() < 0.02 + state.speed*0.002){
      spawnPattern();
    }

    // Move and collide
    const boatX = state.px*W;
    const boatY = H*0.82;
    const boatR = Math.max(10, Math.min(26, H*0.03));

    // Entities
    for(let i=state.entities.length-1;i>=0;i--){
      const e = state.entities[i];
      e.y += e.vy * dt;
      const ex = e.x*W, ey = e.y*H, ew = e.w*W, eh = e.h*H;
      // AABB vs circle
      const cx = clamp(boatX, ex-ew/2, ex+ew/2);
      const cy = clamp(boatY, ey-eh/2, ey+eh/2);
      const dx = boatX - cx, dy = boatY - cy;
      const hit = (dx*dx + dy*dy) <= (boatR*boatR);
      if(hit && e.mode !== state.mode){
        // Lose life
        state.lives--;
        ping(200,0.2,"sawtooth",0.15);
        shake(10);
        state.entities.splice(i,1);
        if(state.lives<=0){ gameOver(); return; }
      }else if(hit && e.mode === state.mode){
        // Safe pass: small score
        state.score += 5;
        state.entities.splice(i,1);
      }else if(e.y > 1.2){
        state.entities.splice(i,1);
      }
    }

    // Beacons
    for(let i=state.beacons.length-1;i>=0;i--){
      const b = state.beacons[i];
      b.y += b.vy * dt;
      const bx = b.x*W, by = b.y*H, br = b.r*W;
      const dx = boatX - bx, dy = boatY - by;
      if(dx*dx + dy*dy <= (boatR+br)*(boatR+br)){
        state.score += 50;
        ping(880,0.08,"triangle",0.18);
        state.beacons.splice(i,1);
      }else if(b.y > 1.2){
        state.beacons.splice(i,1);
      }
    }

    // Extra life per 1000
    if((state.scorePrevMilestone|0)!==((state.score/1000)|0)){
      state.scorePrevMilestone = (state.score/1000)|0;
      if(state.scorePrevMilestone>0){
        state.lives++;
        ping(520,0.08,"sine",0.18);
      }
    }

    updateHUD();

    // Render
    drawScene(boatX, boatY, boatR);
  }

  function gameOver(){
    state.running=false; state.paused=false;
    drawScene(state.px*W, H*0.82, Math.max(10, Math.min(26, H*0.03)));
    ctx.save();
    ctx.translate(W/2, H*0.4);
    ctx.fillStyle = "#fff";
    ctx.textAlign="center";
    ctx.font = (H*0.06)+"px ui-sans-serif,system-ui";
    ctx.fillText("Game Over", 0, 0);
    ctx.font = (H*0.03)+"px ui-sans-serif,system-ui";
    ctx.fillStyle = "rgba(255,255,255,0.85)";
    ctx.fillText("Score: "+(state.score|0)+"  •  Level: "+state.level, 0, H*0.06);
    ctx.restore();

    setTimeout(()=>{
      const name = prompt("Add your name to the Hall of Fame?", "Pilot");
      Hiscore.add({name, score: state.score|0, level: state.level, date: Date.now()});
      renderRecent(); renderHall();
      location.hash = "#/hall";
    }, 100);
  }

  function drawSplash(){
    drawScene(state.px*W, H*0.82, Math.max(10, Math.min(26, H*0.03)));
    ctx.save();
    ctx.translate(W/2, H*0.36);
    ctx.textAlign="center";
    const g = ctx.createLinearGradient(-200,0,200,0);
    g.addColorStop(0, "#5af2ff");
    g.addColorStop(1, "#ff6ad5");
    ctx.fillStyle = g;
    ctx.shadowColor = "#5af2ff"; ctx.shadowBlur = 20;
    ctx.font = (H*0.08)+"px ui-sans-serif,system-ui";
    ctx.fillText("NEON WAKE", 0, 0);
    ctx.shadowBlur = 0;
    ctx.fillStyle = "rgba(255,255,255,0.8)";
    ctx.font = (H*0.03)+"px ui-sans-serif,system-ui";
    ctx.fillText("Press SPACE or click Start to play", 0, H*0.07);
    ctx.restore();
  }

  function drawScene(boatX, boatY, boatR){
    // camera shake
    const sx = (Math.random()*2-1)*shakePower;
    const sy = (Math.random()*2-1)*shakePower;
    shakePower *= 0.9;

    ctx.clearRect(0,0,W,H);

    // Sea gradient
    const sea = ctx.createLinearGradient(0,0,0,H);
    sea.addColorStop(0, "#0e1740");
    sea.addColorStop(1, "#050a1d");
    ctx.fillStyle = sea;
    ctx.fillRect(0,0,W,H);

    // Horizon glow
    const glowGrad = ctx.createRadialGradient(W/2, H*0.1, 10, W/2, H*0.1, H*0.7);
    glowGrad.addColorStop(0, "rgba(90,242,255,0.25)");
    glowGrad.addColorStop(1, "transparent");
    ctx.fillStyle = glowGrad;
    ctx.fillRect(0,0,W,H*0.7);

    ctx.save();
    ctx.translate(sx, sy);

    // Draw entities (waves)
    for(const e of state.entities){
      const col = Modes[e.mode].color;
      const x = e.x*W, y = e.y*H, w=e.w*W, h=e.h*H;
      ctx.fillStyle = col;
      ctx.globalAlpha = 0.86;
      roundRect(ctx, x-w/2, y-h/2, w, h, Math.min(18, h*0.6));
      ctx.fill();
      ctx.globalAlpha=1;
      // emission
      ctx.strokeStyle = col;
      ctx.lineWidth = Math.max(2, h*0.2);
      ctx.globalAlpha = 0.25;
      ctx.strokeRect(x-w/2, y-h/2, w, h);
      ctx.globalAlpha=1;
    }

    // Beacons
    for(const b of state.beacons){
      const x = b.x*W, y = b.y*H, r = b.r*W;
      const g = ctx.createRadialGradient(x,y,2, x,y,r*2.5);
      g.addColorStop(0, "#fff");
      g.addColorStop(1, "rgba(255,255,255,0)");
      ctx.fillStyle = g;
      ctx.beginPath(); ctx.arc(x,y,r,0,Math.PI*2); ctx.fill();
    }

    // Boat
    const mcol = Modes[state.mode].color;
    drawBoat(ctx, boatX, boatY, boatR, mcol);

    // Wake trail
    ctx.globalAlpha = 0.08;
    ctx.fillStyle = mcol;
    for(let i=0;i<8;i++){
      const r = boatR + i*4;
      ctx.beginPath(); ctx.arc(boatX, boatY+10+i*6, r, Math.PI, 0); ctx.fill();
    }
    ctx.globalAlpha = 1;

    ctx.restore();
  }

  function roundRect(ctx, x, y, w, h, r){
    ctx.beginPath();
    ctx.moveTo(x+r, y);
    ctx.lineTo(x+w-r, y);
    ctx.quadraticCurveTo(x+w, y, x+w, y+r);
    ctx.lineTo(x+w, y+h-r);
    ctx.quadraticCurveTo(x+w, y+h, x+w-r, y+h);
    ctx.lineTo(x+r, y+h);
    ctx.quadraticCurveTo(x, y+h, x, y+h-r);
    ctx.lineTo(x, y+r);
    ctx.quadraticCurveTo(x, y, x+r, y);
    ctx.closePath();
  }

  function drawBoat(ctx, x, y, r, color){
    // hull
    ctx.fillStyle = "#0b132f";
    ctx.strokeStyle = "rgba(255,255,255,0.12)";
    ctx.lineWidth = 2;
    ctx.beginPath();
    ctx.moveTo(x, y-r*1.3);
    ctx.lineTo(x+r*0.8, y+r*0.9);
    ctx.lineTo(x-r*0.8, y+r*0.9);
    ctx.closePath();
    ctx.fill(); ctx.stroke();

    // cabin
    ctx.fillStyle = "#101944";
    roundRect(ctx, x-r*0.45, y-r*0.3, r*0.9, r*0.5, r*0.12);
    ctx.fill();

    // LEDs strip glow
    ctx.shadowColor = color; ctx.shadowBlur = 18;
    ctx.strokeStyle = color; ctx.lineWidth = 3;
    ctx.beginPath();
    ctx.moveTo(x-r*0.9, y+r*0.8);
    ctx.lineTo(x+r*0.9, y+r*0.8);
    ctx.stroke();
    ctx.shadowBlur = 0;
  }

  let last = performance.now();
  function loop(t){
    const dt = Math.min(40, t-last); // clamp to avoid huge jumps
    last = t;
    tick(dt);
    if(!state.running && !state.paused){
      // idle animation / splash gets redrawn occasionally
    }
    requestAnimationFrame(loop);
  }
  requestAnimationFrame(loop);

  // Initial draw
  drawSplash();

  // Utility: ensure focus for key input
  canvas.addEventListener("click", ()=>canvas.focus({preventScroll:true}));

  // Populate recent on load
  renderRecent();

})();
</script>

</body>
</html>
