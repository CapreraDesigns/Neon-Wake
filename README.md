<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Idea Roast — Pitch it. Get roasted. Score revealed.</title>
  <meta name="description" content="Submit an idea, get a comical roast and a score. Mobile-friendly, works offline as a single page." />
  <style>
    :root{
      --bg: #0b0d10;
      --panel:#12161b;
      --ink:#e8eef8;
      --muted:#9fb0c3;
      --brand:#7aa2ff;
      --good:#2ecc71;
      --meh:#f1c40f;
      --bad:#ff6b6b;
      --accent:#a78bfa;
      --shadow: 0 10px 30px rgba(0,0,0,.35);
      --radius:18px;
    }
    @media (prefers-color-scheme: light){
      :root{
        --bg:#f6f8fb;
        --panel:#ffffff;
        --ink:#0f172a;
        --muted:#526581;
        --brand:#315cff;
        --accent:#6d28d9;
        --shadow: 0 10px 30px rgba(0,0,0,.08);
      }
    }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0;
      font:16px/1.5 system-ui, -apple-system, Segoe UI, Roboto, "Helvetica Neue", Arial, "Noto Sans", "Apple Color Emoji", "Segoe UI Emoji";
      background: radial-gradient(1200px 800px at 80% -10%, rgba(122,162,255,.2), transparent),
                  radial-gradient(900px 600px at -10% 110%, rgba(167,139,250,.18), transparent),
                  var(--bg);
      color:var(--ink);
    }
    .wrap{
      max-width:940px;
      margin:0 auto;
      padding:18px clamp(16px, 3.5vw, 28px) 40px;
    }
    header{
      display:flex;align-items:center;justify-content:space-between;gap:12px;
      padding:14px 0 8px;
    }
    .logo{
      display:flex;align-items:center;gap:12px;
      font-weight:800;font-size:1.2rem;letter-spacing:.2px;
    }
    .logo .bubble{
      width:36px;height:36px;border-radius:10px;
      background:linear-gradient(135deg,var(--brand),var(--accent));
      box-shadow: var(--shadow);
      position:relative;overflow:hidden;
    }
    .logo .bubble::after{
      content:"🔥"; position:absolute; inset:0;
      display:grid; place-items:center; font-size:20px; filter:drop-shadow(0 2px 2px rgba(0,0,0,.2));
    }
    .card{
      background:rgba(255,255,255,.03);
      backdrop-filter:saturate(140%) blur(6px);
      border:1px solid rgba(255,255,255,.06);
      border-radius:var(--radius);
      box-shadow: var(--shadow);
    }
    .intro{
      padding:18px 18px 8px;
      color:var(--muted); font-size:.98rem;
    }
    form{
      display:grid; gap:12px; padding:8px 8px 12px;
    }
    label{font-weight:600}
    textarea{
      width:100%; min-height:140px; resize:vertical;
      border-radius:14px; border:1px solid rgba(255,255,255,.12);
      padding:14px 14px 44px; outline:none;
      background:var(--panel); color:var(--ink);
      box-shadow: inset 0 1px 0 rgba(255,255,255,.04);
      font-size:1rem;
    }
    .toolbar{
      display:flex; flex-wrap:wrap; gap:10px; align-items:center; justify-content:space-between;
      margin-top:-34px; padding:0 12px 6px;
    }
    .muted{color:var(--muted); font-size:.9rem}
    .btn{
      appearance:none; border:0; border-radius:14px; padding:12px 16px;
      font-weight:700; cursor:pointer; transition:transform .04s ease, filter .2s;
      background:linear-gradient(135deg,var(--brand),var(--accent)); color:white;
      box-shadow: var(--shadow);
      min-height:44px;
    }
    .btn:active{transform:translateY(1px)}
    .btn.secondary{
      background:transparent; color:var(--ink);
      border:1px solid rgba(255,255,255,.18);
    }
    .result{
      margin-top:16px; padding:14px;
      display:none;
    }
    .result.show{display:block; animation:pop .35s ease}
    @keyframes pop{from{transform:scale(.98);opacity:0} to{transform:scale(1);opacity:1}}
    .scoreRow{
      display:grid; grid-template-columns: 90px 1fr auto; gap:14px; align-items:center;
    }
    .scoreBadge{
      width:90px; height:90px; border-radius:18px; display:grid; place-items:center;
      background:linear-gradient(135deg, rgba(122,162,255,.25), rgba(167,139,250,.25));
      font-size:1.9rem; font-weight:900;
      border:1px solid rgba(255,255,255,.14);
    }
    .meter{height:14px; background:rgba(255,255,255,.07); border-radius:10px; overflow:hidden}
    .meter > i{display:block; height:100%; width:0%}
    .meter.good i{background:linear-gradient(90deg,var(--good),#a3e635)}
    .meter.meh i{background:linear-gradient(90deg,var(--meh),#fb923c)}
    .meter.bad i{background:linear-gradient(90deg,var(--bad),#ff8fab)}
    .verdict{margin:8px 0 2px; font-size:1.05rem}
    .chips{display:flex; flex-wrap:wrap; gap:8px}
    .chip{
      font-size:.82rem; padding:6px 10px; border-radius:999px;
      background:rgba(255,255,255,.06); border:1px solid rgba(255,255,255,.12);
    }
    .actions{display:flex; gap:8px; flex-wrap:wrap}
    .stack{display:grid; gap:12px}
    .leader{
      margin-top:18px; padding:10px 14px;
    }
    .leader h3{margin:4px 0 8px}
    .list{display:grid; gap:10px}
    .row{
      display:grid; gap:10px; grid-template-columns:1fr auto; align-items:center;
      padding:10px 12px; border-radius:12px; background:rgba(255,255,255,.04); border:1px solid rgba(255,255,255,.08)
    }
    .row .s{font-weight:700}
    .row .idea{color:var(--muted); font-size:.95rem}
    footer{margin:28px 0 0; color:var(--muted); font-size:.86rem; text-align:center}
    .sr-only{position:absolute!important;height:1px;width:1px;overflow:hidden;clip:rect(1px,1px,1px,1px);white-space:nowrap}
    /* Ensure hit sizes on mobile */
    .btn, textarea{touch-action:manipulation}
  </style>
</head>
<body>
  <div class="wrap">
    <header>
      <div class="logo" aria-label="Idea Roast">
        <div class="bubble" aria-hidden="true"></div>
        <span>Idea Roast</span>
      </div>
      <button id="randomBtn" class="btn secondary" type="button" aria-label="Surprise me with a random idea">🎲 Random</button>
    </header>

    <section class="card">
      <div class="intro">
        <p>Pitch your idea, and our lovingly sarcastic judge will rate it from 0–100 and deliver a roast (with affection). Works great on your phone.</p>
      </div>
      <form id="form" novalidate>
        <label for="idea">Your idea</label>
        <textarea id="idea" name="idea" placeholder="e.g., A self-watering coffee mug that also reminds you to call your mom..."></textarea>
        <div class="toolbar">
          <div class="muted" id="counter" aria-live="polite">0 / 3000 chars</div>
          <div class="actions">
            <button class="btn secondary" type="button" id="remixBtn">🎯 Remix</button>
            <button class="btn" type="submit" id="submitBtn">🔥 Roast me</button>
          </div>
        </div>
      </form>
    </section>

    <section id="result" class="card result" aria-live="polite" aria-atomic="true">
      <div class="stack">
        <div class="scoreRow">
          <div class="scoreBadge" id="scoreBadge" aria-label="Score">–</div>
          <div class="stack">
            <div class="meter" id="meter" role="img" aria-label="Score meter"><i></i></div>
            <div class="verdict" id="verdict">Your roast will appear here.</div>
            <div class="chips" id="badges"></div>
          </div>
          <div class="stack">
            <button class="btn secondary" id="copyBtn" type="button">📋 Copy</button>
            <button class="btn secondary" id="shareBtn" type="button">🔗 Share</button>
          </div>
        </div>
      </div>
    </section>

    <section class="card leader" aria-labelledby="leaderTitle">
      <h3 id="leaderTitle">Local Hall of Fame (this device)</h3>
      <div class="list" id="leader"></div>
    </section>

    <footer>
      Pro tip: funny ≠ wrong. If it stings, iterate. If it slays, ship it. ✨
    </footer>
  </div>

  <script>
    // ---------- Utilities ----------
    const $ = (sel) => document.querySelector(sel);
    const ideaEl = $('#idea');
    const form = $('#form');
    const counter = $('#counter');
    const resultEl = $('#result');
    const verdictEl = $('#verdict');
    const scoreBadge = $('#scoreBadge');
    const meter = $('#meter');
    const meterFill = meter.querySelector('i');
    const badgesEl = $('#badges');
    const leaderEl = $('#leader');
    const copyBtn = $('#copyBtn');
    const shareBtn = $('#shareBtn');
    const remixBtn = $('#remixBtn');
    const randomBtn = $('#randomBtn');

    const BUZZ = [
      'AI','blockchain','crypto','web3','metaverse','NFT','synergy','pivot','viral','SaaS',
      'marketplace','platform','ecosystem','disrupt','scale','gamify','LLM','edge','5G','IoT',
      'quantum','AR','VR','XR','fintech','healthtech','adtech','deep learning','machine learning'
    ];
    const CLICHES = [
      'Uber for','Airbnb for','Tinder for','like Uber but','social network for','platform to connect',
      'all-in-one','next-gen','world-class','cutting-edge','seamless experience'
    ];
    const NICE = ['clever','surprisingly sane','niche-smart','customer-obsessed','ship-ready','earnest'];
    const SILLY = ['unhinged (in a good way)','caffeine-fueled','meme-adjacent','vibe-forward','delusionally confident'];

    // Deterministic hash & PRNG so the same idea always gets the same score + jokes
    function hashString(str){
      let h = 2166136261 >>> 0;
      for (let i=0;i<str.length;i++){
        h ^= str.charCodeAt(i);
        h = Math.imul(h, 16777619);
      }
      return h >>> 0;
    }
    function mulberry32(a){
      return function(){
        let t = a += 0x6D2B79F5;
        t = Math.imul(t ^ t >>> 15, t | 1);
        t ^= t + Math.imul(t ^ t >>> 7, t | 61);
        return ((t ^ t >>> 14) >>> 0) / 4294967296;
      }
    }

    function clamp(n, lo, hi){ return Math.max(lo, Math.min(hi, n)); }
    function uniq(arr){ return [...new Set(arr)]; }
    function words(s){ return s.toLowerCase().match(/[a-z0-9’']+/g) || []; }

    function analyzeIdea(text){
      const w = words(text);
      const unique = uniq(w);
      const len = text.trim().length;
      const wc = w.length;
      const buzzCount = BUZZ.map(b => (text.toLowerCase().includes(b.toLowerCase()) ? 1:0)).reduce((a,b)=>a+b,0);
      const clicheHit = CLICHES.find(c => text.toLowerCase().includes(c.toLowerCase()));
      const avgWordLen = w.length ? w.join('').length / w.length : 0;
      const uniqueRatio = w.length ? unique.length / w.length : 0;
      const hasProblem = /(pain point|problem|need|job to be done|JTBD)/i.test(text);
      const hasCustomer = /(for (moms|students|gamers|developers|nurses|teachers|seniors|boaters|sailors|founders))/i.test(text);
      const hasHow = /(how it works|we do this by|using|with|via|because)/i.test(text);
      return {len, wc, buzzCount, clicheHit, avgWordLen, uniqueRatio, hasProblem, hasCustomer, hasHow, uniqueWords:unique.length};
    }

    function scoreIdea(text){
      const a = analyzeIdea(text);
      const seed = hashString(text);
      const rnd = mulberry32(seed);

      // Start at 50; add/subtract based on signals
      let s = 50;

      // Brevity/clarity: sweet spot ~ 18–80 words
      if(a.wc >= 18 && a.wc <= 80) s += 18;
      else if(a.wc >= 8 && a.wc < 18) s += 8;
      else if(a.wc > 80 && a.wc < 200) s += 2;
      else s -= 8;

      // Explainability / specificity
      if(a.hasProblem) s += 8;
      if(a.hasCustomer) s += 6;
      if(a.hasHow) s += 10;

      // Novelty proxy
      s += Math.round((a.uniqueRatio - 0.45) * 20); // usually -10..+10

      // Buzzword drag (light roast) but allow 0 or 1 for flavor
      if(a.buzzCount === 0) s += 4;
      if(a.buzzCount === 1) s += 1;
      if(a.buzzCount >= 3) s -= 10;

      // Cliché penalty
      if(a.clicheHit) s -= 12;

      // Word length sanity: too many tiny words or very long
      if(a.avgWordLen < 3.8) s -= 4;
      if(a.avgWordLen > 7) s -= 4;

      // Sprinkle deterministic chaos ±6
      s += Math.round((rnd()*12)-6);

      return clamp(Math.round(s), 0, 100);
    }

    function badgesFor(text, score){
      const a = analyzeIdea(text);
      const arr = [];
      if(a.hasCustomer) arr.push('🎯 Clear customer');
      if(a.hasProblem) arr.push('🩹 Real problem');
      if(a.hasHow) arr.push('🔧 How it works');
      if(a.uniqueRatio > 0.65) arr.push('🧪 Spicy novelty');
      if(a.buzzCount>=3) arr.push('🧱 Buzzword salad');
      if(a.clicheHit) arr.push('♻️ Seen this before');
      if(a.wc<10) arr.push('🫥 Needs details');
      if(score>=85) arr.push('💎 Fundable vibe');
      if(score<=30) arr.push('🧯 Scope it down');
      return arr.slice(0,6);
    }

    function roast(text, score){
      const a = analyzeIdea(text);
      const seed = hashString(text)+score;
      const rnd = mulberry32(seed);

      const openers = [
        "Verdict:", "Spit-take:", "Immediate thought:", "Boardroom whisper:", "Honest take:"
      ];
      const hooksHigh = [
        "If you don't ship this, someone else will and take the yacht.", 
        "This is so sensible it's almost suspicious.",
        "My eyebrows just invested.",
        "It’s giving ‘users would actually pay.’",
        "Congrats, you’ve discovered capitalism’s favorite button."
      ];
      const hooksMid = [
        "It could work—once you wrestle it out of the buzzword swamp.",
        "Solid B+. Sand it, stain it, sell it.",
        "Put this in front of 10 humans; 3 will nod, 1 will ask to pay.",
        "I can see the product; your deck can’t yet.",
        "Promising. Now remove 40% of the adjectives."
      ];
      const hooksLow = [
        "Ambitious! Now tell us what it does.",
        "Currently a vibe, not a venture.",
        "Reads like a horoscope for VCs.",
        "It’s ‘Uber for’… déjà vu for me.",
        "Great dream. Prototype’s alarm just went off."
      ];

      const zingers = [
        "Add a price and a person, not another platform.",
        "Clichés are calories: you burned none.",
        "Scope creep is tapping on the window.",
        "Users don’t want ‘AI’; they want a nap and a button.",
        "Your TAM includes my grandma and three of her cats."
      ];

      let hook;
      if(score>=85) hook = hooksHigh[Math.floor(rnd()*hooksHigh.length)];
      else if(score>=50) hook = hooksMid[Math.floor(rnd()*hooksMid.length)];
      else hook = hooksLow[Math.floor(rnd()*hooksLow.length)];

      const opener = openers[Math.floor(rnd()*openers.length)];
      const zing = (a.clicheHit || a.buzzCount>=3 || a.wc<10) ? zingers[Math.floor(rnd()*zingers.length)] : "";

      // Bonus keyword-aware quips
      const kw = BUZZ.find(b => text.toLowerCase().includes(b.toLowerCase()));
      const kwJoke = kw ? ` Also, "${kw}" jar: put a dollar in.` : "";

      return `${opener} ${hook} ${zing}${kwJoke}`.trim();
    }

    function setMeter(score){
      meterFill.style.width = score + '%';
      meter.classList.remove('good','meh','bad');
      if(score>=70) meter.classList.add('good');
      else if(score>=40) meter.classList.add('meh');
      else meter.classList.add('bad');
      scoreBadge.textContent = score;
      scoreBadge.setAttribute('aria-label', `Score ${score} out of 100`);
    }

    function updateLeader(text, score){
      const key = 'ideaRoast.leader';
      const list = JSON.parse(localStorage.getItem(key) || '[]');
      const now = new Date().toISOString();
      list.push({ text, score, ts: now });
      // keep top 8 by score, tie-break newer
      const top = list.sort((a,b)=> b.score - a.score || (b.ts.localeCompare(a.ts))).slice(0,8);
      localStorage.setItem(key, JSON.stringify(top));
      renderLeader(top);
    }

    function renderLeader(list){
      const data = list || JSON.parse(localStorage.getItem('ideaRoast.leader') || '[]');
      leaderEl.innerHTML = '';
      if(!data.length){
        const div = document.createElement('div');
        div.className='muted';
        div.textContent = 'No legends yet. Be the first.';
        leaderEl.appendChild(div);
        return;
      }
      data.forEach(({text,score})=>{
        const row = document.createElement('div');
        row.className='row';
        const left = document.createElement('div');
        const title = document.createElement('div'); title.className='idea'; title.textContent = crop(text, 120);
        const scr = document.createElement('div'); scr.className='s'; scr.textContent = score;
        left.appendChild(title);
        row.appendChild(left);
        row.appendChild(scr);
        leaderEl.appendChild(row);
      });
    }

    function crop(s, n){ return s.length>n ? s.slice(0, n-1)+'…' : s; }

    function confetti(score){
      if(score < 85) return; // Celebrate only the bangers
      const count = 36;
      for(let i=0;i<count;i++){
        const span = document.createElement('span');
        const size = 6 + Math.random()*10;
        span.textContent = ['✨','🎉','💥','🌟','🧨'][i%5];
        span.style.position='fixed';
        span.style.left = (Math.random()*100)+'%';
        span.style.top = '-20px';
        span.style.fontSize = size+'px';
        span.style.zIndex = 9999;
        span.style.transition = 'transform 1.2s ease, opacity 1.2s ease';
        document.body.appendChild(span);
        requestAnimationFrame(()=>{
          span.style.transform = `translateY(${window.innerHeight+40}px) rotate(${(Math.random()*120-60)}deg)`;
          span.style.opacity = '0';
        });
        setTimeout(()=> span.remove(), 1300);
      }
    }

    function evaluate(){
      const text = (ideaEl.value || "").trim();
      if(!text){
        ideaEl.focus();
        ideaEl.setAttribute('aria-invalid','true');
        return;
      }
      ideaEl.removeAttribute('aria-invalid');

      const score = scoreIdea(text);
      setMeter(score);
      verdictEl.textContent = roast(text, score);

      const tags = badgesFor(text, score);
      badgesEl.innerHTML = '';
      tags.forEach(t=>{
        const chip = document.createElement('div');
        chip.className='chip';
        chip.textContent = t;
        badgesEl.appendChild(chip);
      });

      resultEl.classList.add('show');
      updateLeader(text, score);
      confetti(score);
      history.replaceState({}, '', '#score-'+score); // simple deep link hint
    }

    // ---------- Events ----------
    ideaEl.addEventListener('input', ()=>{
      const v = ideaEl.value;
      counter.textContent = `${v.length} / 3000 chars`;
      if(v.length>3000){
        ideaEl.value = v.slice(0,3000);
      }
    });

    form.addEventListener('submit', (e)=>{
      e.preventDefault();
      evaluate();
    });

    copyBtn.addEventListener('click', async ()=>{
      const text = ideaEl.value.trim();
      if(!text) return;
      const out = `Idea: ${text}\nScore: ${scoreBadge.textContent}\nRoast: ${verdictEl.textContent}`;
      try{
        await navigator.clipboard.writeText(out);
        copyBtn.textContent = '✅ Copied';
        setTimeout(()=> copyBtn.textContent = '📋 Copy', 1200);
      }catch{
        alert('Copy failed. You can select and copy manually.');
      }
    });

    shareBtn.addEventListener('click', async ()=>{
      const idea = ideaEl.value.trim();
      if(!idea) return;
      const score = scoreBadge.textContent;
      const text = `I pitched an idea and scored ${score}/100 on Idea Roast.\n"${crop(idea, 160)}"`;
      const url = location.href.split('#')[0];
      if(navigator.share){
        try{ await navigator.share({ title:'Idea Roast', text, url }); }catch{}
      }else{
        prompt('Share this link:', url);
      }
    });

    remixBtn.addEventListener('click', ()=>{
      const seed = ideaEl.value.trim();
      const s = seed ? hashString(seed) : Math.floor(Math.random()*1e9);
      const rnd = mulberry32(s);
      const twists = [
        "…but as a weekend kit you assemble with friends.",
        "…for a town of 8,000 people with one grumpy mayor.",
        "…without an app. Only SMS + magnets.",
        "…with a $0 marketing budget and a meme account.",
        "…for boats only. Yes, boats.",
        "…that works offline and hates subscriptions.",
        "…bundled with a sticker that becomes the product.",
        "…limited to 10 customers paying a lot.",
        "…as a physical card game you can sell at checkout.",
        "…by deleting features until my grandma understands."
      ];
      const starters = [
        "Take your idea and reimagine it",
        "Now pivot recklessly",
        "Chaotic good version",
        "Constraint speedrun",
        "Make it delightful and weird"
      ];
      const t1 = starters[Math.floor(rnd()*starters.length)];
      const t2 = twists[Math.floor(rnd()*twists.length)];
      ideaEl.value = (seed ? seed.replace(/\.*\s*$/,'') : "A tiny service that fixes one recurring annoyance") + " " + t2;
      ideaEl.dispatchEvent(new Event('input'));
    });

    randomBtn.addEventListener('click', ()=>{
      const samples = [
        "A pocket-sized whiteboard that auto-scans your math and tutors you",
        "A marketplace where seniors rent out kitchen time to college students who miss home cooking",
        "A mailbox that only opens when you’ve taken 5,000 steps (anti-doomscroll lock)",
        "A thermostat that negotiates with your roommates via emoji",
        "An app that schedules your chores like Uber shifts with surge pricing for laundry",
        "LED strips that show your boat’s fuel level by color on the hull"
      ];
      const s = samples[Math.floor(Math.random()*samples.length)];
      ideaEl.value = s;
      ideaEl.dispatchEvent(new Event('input'));
    });

    // ---------- Init ----------
    renderLeader();
    // Focus textarea on load for quick play (but avoid stealing focus on mobile)
    if(!/Mobi|Android/i.test(navigator.userAgent)){
      setTimeout(()=> ideaEl.focus(), 200);
    }
  </script>
</body>
</html>
