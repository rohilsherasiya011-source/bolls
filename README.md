<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Color Tubes — Advanced v2 (Music, SFX, Responsive)</title>
<style>
  :root{
    --glass: rgba(255,255,255,0.06);
    --muted-light: rgba(0,0,0,0.55);
    --tube-w: 110px;
    --tube-h: 220px;
    --ball-size: 36px;
    --gap: 14px;
  }
  html,body{height:100%;margin:0;font-family:Inter,system-ui,-apple-system,"Segoe UI",Roboto,Arial;background:#0b1220;color:#eaf4ff;display:flex;align-items:center;justify-content:center;padding:12px;box-sizing:border-box;}
  body[data-theme="light"]{background:linear-gradient(180deg,#f7f9fc,#ffffff);color:#0b1220;}
  .container{width:100%;max-width:1100px}
  .header{display:flex;align-items:center;justify-content:space-between;gap:12px;margin-bottom:10px;flex-wrap:wrap}
  .logo{display:flex;gap:12px;align-items:center}
  .logo-box{width:48px;height:48px;border-radius:10px;background:linear-gradient(90deg,#ffd36a,#ff9ab0);display:flex;align-items:center;justify-content:center;font-weight:700;color:#111827}
  .title h1{margin:0;font-size:16px}
  .title p{margin:0;font-size:12px;color:var(--muted-light)}
  .hud{display:flex;gap:8px;align-items:center;flex-wrap:wrap}
  .pill{background:linear-gradient(180deg, rgba(255,255,255,0.03), rgba(255,255,255,0.01));padding:6px 10px;border-radius:12px;font-size:13px;color:var(--muted-light);backdrop-filter:blur(6px);border:1px solid rgba(255,255,255,0.03)}
  body[data-theme="light"] .pill{background:rgba(15,23,42,0.03); color:var(--muted-light)}
  .controls{display:flex;gap:8px;align-items:center;flex-wrap:wrap}
  .btn{background:transparent;border:1px solid rgba(255,255,255,0.06);padding:8px 10px;border-radius:10px;color:inherit;cursor:pointer;font-size:13px}
  .btn.primary{background:linear-gradient(90deg,#ffd36a,#ff9ab0);color:#111827;border:none}
  .board{background: linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));border-radius:14px;padding:14px;display:grid;gap:var(--gap);grid-template-columns:repeat(auto-fit,minmax(96px,1fr));align-items:start;justify-items:center;border:1px solid rgba(255,255,255,0.03);box-shadow:0 20px 40px rgba(2,6,23,0.6)}
  body[data-theme="light"] .board{box-shadow:0 6px 18px rgba(15,23,42,0.06); border:1px solid rgba(15,23,42,0.04)}
  .tube{width:var(--tube-w);height:var(--tube-h);border-radius:12px;background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));display:flex;flex-direction:column-reverse;align-items:center;padding:10px;box-sizing:border-box;border:1px dashed rgba(255,255,255,0.03);transition:transform .18s ease, box-shadow .18s ease, border-color .12s;position:relative;touch-action:none;user-select:none}
  .tube.empty{border-style:dashed}
  .tube.hint{transform:scale(1.06);box-shadow:0 18px 40px rgba(0,0,0,0.5);border-color:rgba(255,255,255,0.12)}
  .slot{width:100%;height:calc((var(--tube-h)-34px)/4 - 6px);display:flex;align-items:center;justify-content:center;margin:6px 0;pointer-events:none}
  .ball{width:var(--ball-size);height:var(--ball-size);border-radius:50%;box-shadow:0 8px 18px rgba(2,6,23,0.6), inset 0 -6px 8px rgba(255,255,255,0.08);transition:transform .18s cubic-bezier(.2,.9,.3,1), box-shadow .12s;border:1px solid rgba(255,255,255,0.12)}
  .ball.bump{transform:translateY(-6px) scale(1.02);box-shadow:0 16px 36px rgba(2,6,23,0.7)}
  .tube-index{margin-top:8px;font-size:12px;color:rgba(255,255,255,0.7)}
  .footer{display:flex;justify-content:space-between;align-items:center;margin-top:10px;color:rgba(255,255,255,0.7);font-size:13px;gap:12px;flex-wrap:wrap}
  .floating{position:fixed;z-index:9999;width:var(--ball-size);height:var(--ball-size);border-radius:50%;transform:translate(-50%,-50%);pointer-events:none;box-shadow:0 24px 48px rgba(2,6,23,0.6);transition:transform .08s, opacity .12s;opacity:0;display:none;border:1px solid rgba(255,255,255,0.12)}
  .solved-overlay{position:fixed;inset:0;display:flex;align-items:center;justify-content:center;pointer-events:none;opacity:0;transition:opacity .3s}
  .solved-overlay.show{opacity:1}
  .solved-card{background:linear-gradient(90deg,#ffffff,#fffafb);color:#0b1220;padding:18px 30px;border-radius:12px;font-weight:700;box-shadow:0 24px 60px rgba(2,6,23,0.6)}
  /* Level select modal */
  .modal{position:fixed;inset:0;background:rgba(2,6,23,0.6);display:flex;align-items:center;justify-content:center;z-index:2000;backdrop-filter:blur(4px);padding:12px;box-sizing:border-box}
  .modal-card{width:100%;max-width:920px;background:linear-gradient(180deg,rgba(255,255,255,0.02),rgba(255,255,255,0.01));border-radius:12px;padding:12px;box-sizing:border-box}
  .levels-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(72px,1fr));gap:8px}
  .level-tile{background:rgba(255,255,255,0.02);border-radius:8px;padding:10px;text-align:center;cursor:pointer;border:1px solid rgba(255,255,255,0.03);font-size:13px}
  .level-tile.locked{opacity:0.45;cursor:default;position:relative}
  .level-tile .lock{position:absolute;right:6px;top:6px;font-size:12px}
  .theme-switch{display:flex;align-items:center;gap:8px;padding:6px 10px;border-radius:10px;border:1px solid rgba(255,255,255,0.04)}
  .small{font-size:12px;padding:6px 8px;border-radius:8px}
  /* confetti canvas */
  #confetti-canvas{position:fixed;inset:0;pointer-events:none;z-index:1500;display:none}
  /* glow trail */
  .trail-dot{position:fixed;width:12px;height:12px;border-radius:50%;pointer-events:none;mix-blend-mode:screen;opacity:0.9;transform:translate(-50%,-50%);filter:blur(2px)}
  /* responsive tweaks */
  @media(max-width:920px){:root{--tube-w:86px;--tube-h:180px;--ball-size:30px;--gap:10px}}
  @media(max-width:560px){
    :root{--tube-w:78px;--tube-h:160px;--ball-size:28px;--gap:8px}
    .header{padding:6px}
    .pill{padding:6px 8px;font-size:12px}
    .btn{padding:6px 8px;font-size:12px}
    .title h1{font-size:15px}
  }
</style>
</head>
<body data-theme="dark">
<div class="container" id="app">
  <div class="header" role="banner">
    <div class="logo" aria-hidden="true">
      <div class="logo-box">CT</div>
      <div class="title">
        <h1>Color Tubes — Advanced <small style="opacity:0.7">v2</small></h1>
        <p>Sound • Confetti • Music • Responsive</p>
      </div>
    </div>

    <div class="hud" role="toolbar" aria-label="Game controls">
      <div class="pill">Level: <strong id="levelNum">1</strong></div>
      <div class="pill">Moves: <strong id="moves">0</strong></div>
      <div class="pill">Time: <strong id="time">0:00</strong></div>
      <div class="controls">
        <button class="btn" id="btnLevels" title="Open levels">Levels</button>
        <button class="btn" id="btnRestart" title="Restart level">Restart</button>
        <button class="btn" id="btnHint" title="Show hint">Hint</button>
        <button class="btn primary" id="btnNext" title="Next level">Next</button>

        <!-- Music & SFX controls -->
        <div class="theme-switch" style="display:flex;align-items:center;gap:8px">
          <label style="font-size:13px">Theme</label>
          <select id="themeSelect" class="small" aria-label="Theme select">
            <option value="dark">Dark</option>
            <option value="light">Light</option>
          </select>
        </div>
        <button class="btn" id="btnMusic" title="Toggle music">Music</button>
        <button class="btn" id="btnMuteSfx" title="Toggle SFX">SFX</button>
      </div>
    </div>
  </div>

  <div id="board" class="board" aria-live="polite"></div>

  <div class="footer" role="contentinfo">
    <div id="levelInfo">Colors: 0 • Tubes: 0 • Capacity: 0</div>
    <div id="scoreInfo">Score: 0</div>
  </div>
</div>

<!-- Floating dragged ball + confetti -->
<div id="floating" class="floating" aria-hidden="true"></div>
<canvas id="confetti-canvas" aria-hidden="true"></canvas>
<div id="solved" class="solved-overlay" aria-hidden="true"><div class="solved-card">Level Complete!</div></div>

<!-- Level modal -->
<div id="levelModal" class="modal" style="display:none" aria-hidden="true">
  <div class="modal-card" role="dialog" aria-modal="true" aria-label="Levels">
    <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:10px">
      <strong>Levels</strong>
      <div><button id="closeLevels" class="btn small">Close</button></div>
    </div>
    <div class="levels-grid" id="levelsGrid"></div>
    <div style="margin-top:12px;font-size:13px;color:rgba(255,255,255,0.8)">
      Tip: Levels unlock sequentially. Progress saved locally.
    </div>
  </div>
</div>

<script>
(() => {
  /********************
   * Configuration
   * - Added music & sfx toggles (persisted)
   * - Responsive improvements in CSS
   ********************/
  const baseColors = ["#FF6B6B","#FFD93D","#6BCB77","#4D96FF","#C77DFF","#FF8AB8","#00C2CB","#FFA070","#B7E0A8"];
  function shuffle(a){ const b=a.slice(); for(let i=b.length-1;i>0;i--){const j=Math.floor(Math.random()*(i+1));[b[i],b[j]]=[b[j],b[i]];} return b;}
  function generateLevel(index){
    const difficulty = Math.min(7, 3 + Math.floor(index/3));
    const colors = baseColors.slice(0, difficulty);
    const ballsPerColor = 4;
    const totalBalls = ballsPerColor * colors.length;
    const emptyTubes = Math.min(4 + Math.floor(index/5), 6);
    const tubeCapacity = ballsPerColor;
    const totalTubes = Math.ceil(totalBalls / tubeCapacity) + emptyTubes;
    const ballList = [];
    colors.forEach(c=>{for(let i=0;i<ballsPerColor;i++) ballList.push(c)});
    const shuffled = shuffle(ballList);
    const tubes = Array.from({length: totalTubes}, ()=>[]);
    let t = 0;
    for(const b of shuffled){ while(tubes[t].length >= tubeCapacity) t++; tubes[t].push(b); }
    return { tubes: tubes.map(x=>x.slice()), colors, tubeCapacity };
  }
  const LEVEL_COUNT = 30;
  const levels = Array.from({length: LEVEL_COUNT}, (_,i)=>generateLevel(i));

  // Storage keys (v2 to avoid clashing with older saves)
  const STORAGE_KEYS = {
    unlocked: 'ct_unlocked_v2',
    last: 'ct_last_v2',
    stats: 'ct_stats_v2',
    theme: 'ct_theme_v2',
    music: 'ct_music_v2',
    sfx: 'ct_sfx_v2'
  };

  // persistent state
  let unlocked = Number(localStorage.getItem(STORAGE_KEYS.unlocked) || '1');
  let lastPlayed = Number(localStorage.getItem(STORAGE_KEYS.last) || '0');
  if(lastPlayed < 0) lastPlayed = 0;
  let stats = JSON.parse(localStorage.getItem(STORAGE_KEYS.stats) || '{}');
  let savedTheme = localStorage.getItem(STORAGE_KEYS.theme) || 'dark';
  let musicEnabled = localStorage.getItem(STORAGE_KEYS.music) !== '0'; // default true
  let sfxEnabled = localStorage.getItem(STORAGE_KEYS.sfx) !== '0'; // default true

  document.body.setAttribute('data-theme', savedTheme);
  document.getElementById('themeSelect').value = savedTheme;

  /********************
   * UI & runtime state
   ********************/
  let levelIndex = Math.max(0, lastPlayed || 0);
  let tubes = JSON.parse(JSON.stringify(levels[levelIndex].tubes));
  let tubeCapacity = levels[levelIndex].tubeCapacity;
  let moves = 0;
  let moveStack = [];
  let redoStack = [];
  let startTime = null;
  let timer = null;
  let elapsedMs = 0;

  let dragging = null;
  let pickedByClick = null;
  const floating = document.getElementById('floating');

  // DOM refs
  const board = document.getElementById('board');
  const levelNumEl = document.getElementById('levelNum');
  const movesEl = document.getElementById('moves');
  const timeEl = document.getElementById('time');
  const levelInfoEl = document.getElementById('levelInfo');
  const scoreInfoEl = document.getElementById('scoreInfo');
  const solvedOverlay = document.getElementById('solved');
  const levelModal = document.getElementById('levelModal');
  const levelsGrid = document.getElementById('levelsGrid');

  // control buttons
  document.getElementById('btnRestart').addEventListener('click', ()=> startLevel(levelIndex));
  document.getElementById('btnNext').addEventListener('click', ()=> setLevel(Math.min(LEVEL_COUNT-1, levelIndex+1)));
  document.getElementById('btnHint').addEventListener('click', showHint);
  document.getElementById('btnLevels').addEventListener('click', openLevels);
  document.getElementById('closeLevels').addEventListener('click', closeLevels);
  document.getElementById('themeSelect').addEventListener('change', (e) => {
    const v = e.target.value; document.body.setAttribute('data-theme', v); localStorage.setItem(STORAGE_KEYS.theme, v);
  });

  // Music element (uses an embedded base64 small loop or remote URL)
  // For demo I'll use a short base64-encoded small MP3 (silence/soft loop would be large).
  // Instead, we create an Audio element and allow user to set music URL later.
  const music = new Audio();
  music.loop = true;
  music.volume = 0.45;
  // Optional: set a default music URL if you host one. Left blank to avoid auto-download.
  // music.src = "https://example.com/your-loop.mp3";

  // Music & SFX UI
  const btnMusic = document.getElementById('btnMusic');
  const btnMuteSfx = document.getElementById('btnMuteSfx');
  function updateMusicButton(){ btnMusic.textContent = musicEnabled ? (music.paused ? 'Play Music' : 'Pause Music') : 'Music Off'; }
  function updateSfxButton(){ btnMuteSfx.textContent = sfxEnabled ? 'SFX On' : 'SFX Off'; }
  btnMusic.addEventListener('click', () => {
    // toggle musicEnabled if currently off; otherwise toggle play/pause
    if(!musicEnabled){ musicEnabled = true; localStorage.setItem(STORAGE_KEYS.music, '1'); ensureAudioUnlocked(); updateMusicButton(); return; }
    if(music.paused){
      // if no src set, try to use a small built-in melody using WebAudio fallback
      if(!music.src){
        // create an oscillator-based ambient loop to emulate music when a file isn't provided
        startAmbientMusic();
      } else {
        music.play().catch(()=>{ /* autoplay blocked until gesture */ });
      }
    } else {
      music.pause();
    }
    updateMusicButton();
  });
  btnMuteSfx.addEventListener('click', ()=>{
    sfxEnabled = !sfxEnabled;
    localStorage.setItem(STORAGE_KEYS.sfx, sfxEnabled ? '1' : '0');
    updateSfxButton();
  });
  updateMusicButton(); updateSfxButton();

  /********************
   * WebAudio SFX (requires user gesture)
   ********************/
  const AudioCtx = window.AudioContext || window.webkitAudioContext;
  let audioCtx = null;
  function ensureAudioUnlocked(){
    if(!AudioCtx) return null;
    if(!audioCtx) try{ audioCtx = new AudioCtx(); }catch(e){ audioCtx = null; }
    // resume if suspended (some browsers start suspended until user gesture)
    if(audioCtx && audioCtx.state === 'suspended') audioCtx.resume().catch(()=>{});
    // if music has no src but musicEnabled, start ambient oscillator
    if(musicEnabled && !music.src && (!window.__ambientPlaying)) startAmbientMusic();
    return audioCtx;
  }
  function playTone(freq, time=0.06, type='sine', gain=0.12){
    if(!sfxEnabled) return;
    const actx = ensureAudioUnlocked(); if(!actx) return;
    const o = actx.createOscillator(); const g = actx.createGain();
    o.type = type; o.frequency.value = freq; g.gain.value = gain;
    o.connect(g); g.connect(actx.destination);
    o.start();
    g.gain.exponentialRampToValueAtTime(0.001, actx.currentTime + time);
    o.stop(actx.currentTime + time + 0.02);
  }
  function playDrop(){ playTone(480, 0.08,'sine', 0.14); }
  function playFill(){ playTone(760, 0.12,'triangle', 0.16); setTimeout(()=>playTone(620,0.08,'sine',0.08),40); }
  function playSolved(){ playTone(880, 0.12,'sawtooth',0.18); setTimeout(()=>playTone(1180,0.12,'sine',0.12),90); }

  // Ambient "music" fallback using WebAudio oscillator loop (light)
  function startAmbientMusic(){
    if(window.__ambientPlaying) return;
    const actx = ensureAudioUnlocked(); if(!actx) return;
    window.__ambientPlaying = true;
    // create a few oscillators with slow LFO-style gain to emulate soft music
    const master = actx.createGain(); master.gain.value = 0.04;
    master.connect(actx.destination);
    const o1 = actx.createOscillator(); o1.type='sine'; o1.frequency.value = 220; const g1=actx.createGain(); g1.gain.value = 0.0;
    const o2 = actx.createOscillator(); o2.type='sine'; o2.frequency.value = 330; const g2=actx.createGain(); g2.gain.value = 0.0;
    o1.connect(g1); g1.connect(master); o2.connect(g2); g2.connect(master);
    o1.start(); o2.start();
    // simple periodic volume automation
    const now = actx.currentTime;
    for(let i=0;i<10;i++){
      g1.gain.linearRampToValueAtTime(0.02 + Math.random()*0.03, now + i*2);
      g1.gain.linearRampToValueAtTime(0.01, now + i*2 + 1.2);
      g2.gain.linearRampToValueAtTime(0.015 + Math.random()*0.03, now + i*2 + 0.6);
      g2.gain.linearRampToValueAtTime(0.005, now + i*2 + 1.6);
    }
    window.__ambientNodes = {o1,o2,g1,g2,master};
    updateMusicButton();
  }
  function stopAmbientMusic(){
    if(!window.__ambientPlaying) return;
    try{
      const n = window.__ambientNodes;
      n.o1.stop(); n.o2.stop(); n.master.disconnect();
    }catch(e){}
    window.__ambientPlaying = false;
    window.__ambientNodes = null;
  }

  /********************
   * Confetti system
   ********************/
  const confettiCanvas = document.getElementById('confetti-canvas');
  const cctx = confettiCanvas.getContext && confettiCanvas.getContext('2d');
  let confettiParticles = [];
  function resizeCanvas(){ confettiCanvas.width = innerWidth; confettiCanvas.height = innerHeight; }
  window.addEventListener('resize', resizeCanvas); resizeCanvas();
  function spawnConfetti(x = innerWidth/2, y = innerHeight/3, count = 60){
    if(!cctx) return;
    confettiCanvas.style.display = 'block';
    for(let i=0;i<count;i++){
      confettiParticles.push({
        x: x + (Math.random()-0.5)*200,
        y: y + (Math.random()-0.5)*60,
        vx: (Math.random()-0.5)*6,
        vy: Math.random()*-6 - 2,
        size: 6 + Math.random()*8,
        color: baseColors[Math.floor(Math.random()*baseColors.length)],
        rot: Math.random()*360,
        gravity: 0.18 + Math.random()*0.2,
        life: 80 + Math.random()*40
      });
    }
    if(confettiLooping) return;
    confettiLooping = true; requestAnimationFrame(confettiStep);
  }
  let confettiLooping = false;
  function confettiStep(){
    if(!cctx){ confettiLooping=false; return; }
    cctx.clearRect(0,0,confettiCanvas.width, confettiCanvas.height);
    confettiParticles.forEach(p=>{
      p.vy += p.gravity; p.x += p.vx; p.y += p.vy; p.rot += p.vx; p.life--;
      cctx.save();
      cctx.translate(p.x, p.y);
      cctx.rotate(p.rot * Math.PI / 180);
      cctx.fillStyle = p.color;
      cctx.fillRect(-p.size/2, -p.size/2, p.size, p.size*0.6);
      cctx.restore();
    });
    confettiParticles = confettiParticles.filter(p=>p.life>0 && p.y < confettiCanvas.height + 200);
    if(confettiParticles.length>0) requestAnimationFrame(confettiStep);
    else { confettiLooping = false; confettiCanvas.style.display='none'; }
  }

  /********************
   * Glow trail (drag)
   ********************/
  let trailEnabled = true;
  function createTrailDot(color, x, y){
    const d = document.createElement('div');
    d.className = 'trail-dot';
    d.style.left = x + 'px';
    d.style.top = y + 'px';
    d.style.background = color;
    document.body.appendChild(d);
    setTimeout(()=> d.style.opacity = '0', 10);
    setTimeout(()=> { try{document.body.removeChild(d);}catch(e){} }, 700);
  }

  /********************
   * Core game logic
   ********************/
  function cloneTubes(src){ return src.map(t=>t.slice()); }
  function canMove(from, to){
    if(from===to) return false;
    const f = tubes[from]; if(!f || f.length===0) return false;
    const t = tubes[to]; if(t.length >= tubeCapacity) return false;
    const color = f[f.length-1];
    if(t.length===0) return true;
    return t[t.length-1] === color;
  }
  function isSolved(){
    return tubes.every(tube=>{
      if(tube.length===0) return true;
      if(tube.length !== tubeCapacity) return false;
      return tube.every(c=>c===tube[0]);
    });
  }
  function formatTime(ms){ const s=Math.floor(ms/1000); const mm=Math.floor(s/60); const ss=s%60; return `${mm}:${String(ss).padStart(2,'0')}`; }
  function computeScore(){ return Math.max(0, Math.round(10000/(1 + moves + elapsedMs/1000))); }

  function doMove(from,to,record=true){
    if(!canMove(from,to)) return false;
    const next = cloneTubes(tubes);
    const ball = next[from].pop();
    next[to].push(ball);
    tubes = next;
    render();
    if(record){ moveStack.push({from,to}); redoStack=[]; moves++; movesEl.textContent = moves; }
    // sound & animations
    if(sfxEnabled) playDrop();
    const dest = tubes[to];
    if(dest.length === tubeCapacity && dest.every(c=>c===dest[0])) { setTimeout(()=> { if(sfxEnabled) playFill(); }, 80); }
    checkSolved();
    saveProgressAuto();
    return true;
  }

  function undo(){ const last = moveStack.pop(); if(!last) return; const next = cloneTubes(tubes); if(next[last.to].length===0) return; const ball = next[last.to].pop(); next[last.from].push(ball); tubes = next; redoStack.push(last); moves = Math.max(0, moves-1); movesEl.textContent = moves; render(); saveProgressAuto(); }
  function redo(){ const last = redoStack.pop(); if(!last) return; if(!canMove(last.from,last.to)) return; const next = cloneTubes(tubes); const ball = next[last.from].pop(); next[last.to].push(ball); tubes = next; moveStack.push(last); moves++; movesEl.textContent = moves; render(); saveProgressAuto(); }

  function computeHint(){ for(let i=0;i<tubes.length;i++) for(let j=0;j<tubes.length;j++) if(canMove(i,j)) return {from:i,to:j}; return null; }
  let hintTimer = null;
  function showHint(){
    const h = computeHint(); const els = board.querySelectorAll('.tube');
    if(!h){ els.forEach(el=> el.animate([{transform:'translateY(0)'},{transform:'translateY(-6px)'},{transform:'translateY(0)'}],{duration:360})); return; }
    els.forEach((el,idx)=> el.classList.toggle('hint', idx===h.from || idx===h.to));
    clearTimeout(hintTimer); hintTimer = setTimeout(()=> els.forEach(el=> el.classList.remove('hint')), 1200);
  }

  function checkSolved(){
    if(isSolved()){
      solvedOverlay.classList.add('show');
      if(sfxEnabled) playSolved();
      spawnConfetti(innerWidth/2, innerHeight/3, 96);
      if(levelIndex+1 < LEVEL_COUNT && unlocked < levelIndex+2){
        unlocked = levelIndex+2; localStorage.setItem(STORAGE_KEYS.unlocked, String(unlocked)); renderLevelsGrid();
      }
      saveLevelStats(levelIndex);
      setTimeout(()=> solvedOverlay.classList.remove('show'), 700);
      setTimeout(()=> { if(levelIndex < LEVEL_COUNT-1) setLevel(levelIndex+1); else setLevel(0); }, 900);
    }
  }

  /********************
   * Render / UI
   ********************/
  function render(){
    board.innerHTML = '';
    tubes.forEach((tube, idx)=>{
      const tubeEl = document.createElement('div');
      tubeEl.className = 'tube' + (tube.length===0 ? ' empty' : '');
      tubeEl.dataset.idx = idx; tubeEl.style.touchAction = 'none';
      for(let s=0;s<tubeCapacity;s++){
        const slot = document.createElement('div'); slot.className = 'slot';
        const color = tube[s] !== undefined ? tube[s] : null;
        if(color){
          const ball = document.createElement('div'); ball.className='ball'; ball.style.background = color;
          if(s === tube.length - 1){ ball.classList.add('bump'); setTimeout(()=> ball.classList.remove('bump'), 220); }
          slot.appendChild(ball);
        } else {
          const ph = document.createElement('div'); ph.style.width='36px'; ph.style.height='36px'; ph.style.borderRadius='50%'; ph.style.border='1px dashed rgba(255,255,255,0.04)';
          slot.appendChild(ph);
        }
        tubeEl.appendChild(slot);
      }
      const idxLabel = document.createElement('div'); idxLabel.className='tube-index'; idxLabel.textContent = 'Tube ' + (idx+1);
      tubeEl.appendChild(idxLabel);

      // pointer events
      tubeEl.addEventListener('pointerdown', (ev) => onPointerDown(ev, idx), {passive:true});
      tubeEl.addEventListener('pointerup', (ev) => onPointerUp(ev, idx));
      tubeEl.addEventListener('pointercancel', onPointerCancel);
      tubeEl.addEventListener('pointerleave', (ev)=>{ if(!dragging){ const el = ev.currentTarget; el.style.transform=''; } });

      tubeEl.addEventListener('click', (ev)=>{
        if(dragging) return;
        if(pickedByClick !== null){
          if(pickedByClick === idx){ pickedByClick = null; clearFloating(); return; }
          if(doMove(pickedByClick, idx, true)) pickedByClick = null;
          clearFloating();
        } else {
          if(tubes[idx] && tubes[idx].length > 0){
            pickedByClick = idx;
            const color = tubes[idx][tubes[idx].length - 1];
            showFloating(color, ev.clientX, ev.clientY);
            const el = board.querySelector('.tube[data-idx="' + idx + '"]');
            if(el) el.classList.add('hint'); setTimeout(()=> el && el.classList.remove('hint'), 600);
            ensureAudioUnlocked();
          }
        }
      });

      board.appendChild(tubeEl);
    });

    levelNumEl.textContent = levelIndex + 1;
    movesEl.textContent = moves;
    levelInfoEl.textContent = `Colors: ${levels[levelIndex].colors.length} • Tubes: ${tubes.length} • Capacity: ${tubeCapacity}`;
    scoreInfoEl.textContent = `Score: ${computeScore()}`;
  }

  /********************
   * Dragging handlers
   ********************/
  function onPointerDown(e, idx){
    if(!(tubes[idx] && tubes[idx].length > 0)) return;
    ensureAudioUnlocked();
    const cur = e.currentTarget;
    try{ cur.setPointerCapture && cur.setPointerCapture(e.pointerId); }catch(err){}
    dragging = { from: idx, color: tubes[idx][tubes[idx].length - 1], pointerId: e.pointerId, srcEl: cur };
    showFloating(dragging.color, e.clientX, e.clientY);
    window.addEventListener('pointermove', onPointerMove);
    cur.style.transform = 'translateY(-6px) scale(1.02)';
  }
  function onPointerMove(e){
    if(!dragging) return;
    floating.style.display = 'block';
    floating.style.left = e.clientX + 'px';
    floating.style.top = e.clientY + 'px';
    floating.style.opacity = '1';
    floating.style.transform = 'translate(-50%,-50%) scale(1.02)';
    if(trailEnabled) createTrailDot(dragging.color, e.clientX, e.clientY);
  }
  function onPointerUp(e, idxParam){
    if(!dragging) return;
    window.removeEventListener('pointermove', onPointerMove);
    try{ dragging.srcEl && dragging.srcEl.releasePointerCapture && dragging.srcEl.releasePointerCapture(dragging.pointerId); }catch(err){}
    let dropIdx = null;
    const el = document.elementFromPoint(e.clientX, e.clientY);
    const tubeEl = el && el.closest ? el.closest('.tube') : null;
    if(tubeEl && tubeEl.dataset && tubeEl.dataset.idx !== undefined) dropIdx = Number(tubeEl.dataset.idx);
    else dropIdx = idxParam;
    const srcEl = board.querySelector('.tube[data-idx="' + dragging.from + '"]'); if(srcEl) srcEl.style.transform = '';
    doMove(dragging.from, dropIdx, true);
    dragging = null; clearFloating();
  }
  function onPointerCancel(e){
    if(dragging && dragging.srcEl){ try{ dragging.srcEl.releasePointerCapture && dragging.srcEl.releasePointerCapture(dragging.pointerId); }catch(err){} }
    dragging = null; pickedByClick = null; window.removeEventListener('pointermove', onPointerMove); clearFloating();
    const els = board.querySelectorAll('.tube'); els.forEach(el=> el.style.transform='');
  }
  function showFloating(color,x,y){ floating.style.display='block'; floating.style.left = x + 'px'; floating.style.top = y + 'px'; floating.style.background = color; floating.style.opacity='1'; }
  function clearFloating(){ floating.style.display='none'; floating.style.opacity='0'; floating.style.background='transparent'; }

  /********************
   * Timer & level
   ********************/
  function startTimer(){ if(timer) clearInterval(timer); startTime = Date.now(); timer = setInterval(()=>{ elapsedMs = Date.now() - startTime; timeEl.textContent = formatTime(elapsedMs); scoreInfoEl.textContent = `Score: ${computeScore()}`; }, 300); }
  function stopTimer(){ if(timer){ clearInterval(timer); timer=null; } }
  function startLevel(idx){
    levelIndex = idx; const lvl = levels[levelIndex];
    tubes = lvl.tubes.map(x=>x.slice());
    tubeCapacity = lvl.tubeCapacity; moveStack = []; redoStack = []; moves = 0; movesEl.textContent = 0;
    elapsedMs = 0; timeEl.textContent = '0:00'; render(); startTimer();
    lastPlayed = levelIndex; localStorage.setItem(STORAGE_KEYS.last, String(lastPlayed)); saveProgressAuto();
  }
  function setLevel(idx){ startLevel(idx); }

  /********************
   * Levels modal
   ********************/
  function openLevels(){ renderLevelsGrid(); levelModal.style.display='flex'; levelModal.setAttribute('aria-hidden','false'); }
  function closeLevels(){ levelModal.style.display='none'; levelModal.setAttribute('aria-hidden','true'); }
  function renderLevelsGrid(){
    levelsGrid.innerHTML = '';
    for(let i=0;i<LEVEL_COUNT;i++){
      const tile = document.createElement('div');
      tile.className = 'level-tile' + ((i+1) > unlocked ? ' locked':'' );
      tile.dataset.idx = i;
      tile.innerHTML = `<div style="font-weight:700">${i+1}</div><div style="font-size:12px;margin-top:6px;color:rgba(255,255,255,0.7)">${levels[i].colors.length} colors</div>`;
      if((i+1) > unlocked){
        tile.classList.add('locked');
        const lock = document.createElement('div'); lock.className='lock'; lock.textContent='🔒'; tile.appendChild(lock);
      } else {
        tile.addEventListener('click', ()=>{ setLevel(i); closeLevels(); });
      }
      const s = stats[String(i)];
      if(s){
        const st = document.createElement('div'); st.style.fontSize='11px'; st.style.marginTop='6px'; st.style.opacity='0.85';
        st.textContent = `Moves: ${s.moves} • Time: ${formatTime(s.timeMs)}`;
        tile.appendChild(st);
      }
      levelsGrid.appendChild(tile);
    }
  }

  /********************
   * Save / stats
   ********************/
  function saveProgressAuto(){
    localStorage.setItem(STORAGE_KEYS.last, String(levelIndex));
    localStorage.setItem(STORAGE_KEYS.unlocked, String(unlocked));
    const cur = { moves: moves, timeMs: elapsedMs }; stats[String(levelIndex)] = cur;
    localStorage.setItem(STORAGE_KEYS.stats, JSON.stringify(stats));
  }
  function saveLevelStats(idx){ stats[String(idx)] = { moves: moves, timeMs: elapsedMs }; localStorage.setItem(STORAGE_KEYS.stats, JSON.stringify(stats)); }

  /********************
   * Keyboard & misc
   ********************/
  window.addEventListener('keydown', (e)=>{ if((e.ctrlKey||e.metaKey) && e.key==='z') undo(); if((e.ctrlKey||e.metaKey) && e.key==='y') redo(); if(e.key==='h') showHint(); if(e.key==='r') startLevel(levelIndex); });
  window.addEventListener('pointerdown', (ev)=>{ const t = ev.target.closest && ev.target.closest('.tube'); if(!t && pickedByClick !== null){ pickedByClick = null; clearFloating(); } });
  document.addEventListener('visibilitychange', ()=>{ if(document.hidden) stopTimer(); else startTimer(); });

  /********************
   * Init & helpers
   ********************/
  function updateMusicPrefs(){ localStorage.setItem(STORAGE_KEYS.music, musicEnabled ? '1' : '0'); }
  function updateSfxPrefs(){ localStorage.setItem(STORAGE_KEYS.sfx, sfxEnabled ? '1' : '0'); }
  updateMusicPrefs(); updateSfxPrefs();

  // ensure buttons show current state
  function refreshButtons(){
    btnMusic.textContent = musicEnabled ? (music.paused ? 'Play Music' : 'Pause Music') : 'Music Off';
    btnMuteSfx.textContent = sfxEnabled ? 'SFX On' : 'SFX Off';
  }
  refreshButtons();

  // When the user first interacts, unlock WebAudio and optionally start music
  function userGestureInit(){
    const actx = ensureAudioUnlocked();
    if(musicEnabled){
      // try to play audio element if source set
      if(music.src){
        music.play().catch(()=>{});
      } else {
        // start ambient oscillator fallback
        startAmbientMusic();
      }
    }
    // remove this listener after first gesture
    window.removeEventListener('pointerdown', userGestureInit);
  }
  window.addEventListener('pointerdown', userGestureInit, {once:true});

  // helper to set music source quickly (callable by developer)
  window.setMusicSource = function(url){ music.src = url; music.loop = true; if(musicEnabled){ music.play().catch(()=>{}); } refreshButtons(); };

  // expose debug API
  window.__colorTubes = { getState: ()=> ({levelIndex,tubes,moves,elapsedMs}), nextLevel: ()=> setLevel(Math.min(LEVEL_COUNT-1, levelIndex+1)) };

  // initial render & start
  render();
  startLevel(levelIndex);

})();
</script>
</body>
</html>
