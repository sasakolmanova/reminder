# reminder<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>note.</title>
<link href="https://fonts.googleapis.com/css2?family=Spectral:ital,wght@0,300;0,400;1,300;1,400&family=DM+Mono:ital@0;1&display=swap" rel="stylesheet">
<style>
  :root {
    --bg:     #F9F7F4;
    --paper:  #FFFFFF;
    --rule:   #E8E3DB;
    --soft:   #D0C9BE;
    --mid:    #9E9488;
    --ink:    #1C1917;
    --accent: #BC6C25;
    --warm:   #DDA15E;
    --pop:    #FEFAE0;
  }

  *, *::before, *::after { margin:0; padding:0; box-sizing:border-box; }
  html, body { height:100%; }

  body {
    min-height:100vh;
    background: var(--bg);
    font-family: 'DM Mono', monospace;
    color: var(--ink);
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 2rem 1.5rem;
    overflow: hidden;
    cursor: none;
  }

  /* grid texture */
  body::after {
    content:'';
    position:fixed; inset:0;
    background-image:
      linear-gradient(var(--rule) 1px, transparent 1px),
      linear-gradient(90deg, var(--rule) 1px, transparent 1px);
    background-size: 40px 40px;
    opacity: 0.4;
    pointer-events:none;
    z-index:0;
  }

  /* ── cursor ── */
  #cur {
    position:fixed; pointer-events:none; z-index:9999;
    width:10px; height:10px;
    background: var(--accent);
    border-radius:50%;
    transform:translate(-50%,-50%);
    transition: width .2s, height .2s, background .2s;
  }

  #cur.hovering {
    width:40px; height:40px;
    background: rgba(188,108,37,0.15);
    border: 1.5px solid var(--accent);
  }

  #curTrail {
    position:fixed; pointer-events:none; z-index:9998;
    width:24px; height:24px;
    border:1px solid rgba(188,108,37,0.3);
    border-radius:50%;
    transform:translate(-50%,-50%);
  }

  /* ══════════════════════
     SENDER
  ══════════════════════ */
  .sender {
    position:relative; z-index:1;
    width:min(520px,100%);
    display:flex; flex-direction:column;
    animation: rise .9s cubic-bezier(.16,1,.3,1) both;
  }

  .eyebrow {
    display:flex; align-items:center; gap:.8rem;
    margin-bottom:2.6rem;
  }
  .eyebrow-line { flex:1; height:1px; background:var(--rule); }
  .eyebrow-text { font-size:.6rem; letter-spacing:.2em; text-transform:uppercase; color:var(--soft); }

  .headline {
    font-family:'Spectral',serif;
    font-weight:300;
    font-size:clamp(3rem,10vw,5.8rem);
    line-height:.92;
    letter-spacing:-.04em;
    color:var(--ink);
    margin-bottom:2.8rem;
  }
  .headline em { font-style:italic; color:var(--accent); }

  .card {
    background:var(--paper);
    border:1px solid var(--rule);
    box-shadow:0 2px 24px rgba(28,25,23,.06);
  }

  .card-inner { padding:1.8rem 2rem; border-bottom:1px solid var(--rule); }

  textarea {
    width:100%; background:transparent; border:none; outline:none; resize:none;
    font-family:'Spectral',serif; font-style:italic; font-weight:300;
    font-size:1.2rem; color:var(--ink); line-height:1.75;
    caret-color:var(--accent); min-height:8rem;
  }
  textarea::placeholder { color:var(--soft); }

  .card-foot {
    padding:1rem 2rem;
    display:flex; align-items:center; justify-content:space-between; gap:1rem;
  }

  .from-inline { display:flex; align-items:center; gap:.6rem; flex:1; }
  .from-label { font-size:.58rem; letter-spacing:.16em; text-transform:uppercase; color:var(--soft); white-space:nowrap; }

  input[type="text"] {
    background:transparent; border:none; outline:none;
    font-family:'DM Mono',monospace; font-size:.82rem; color:var(--ink);
    caret-color:var(--accent); flex:1; min-width:0;
  }
  input::placeholder { color:var(--soft); }

  .send-btn {
    background:var(--ink); color:var(--bg); border:none;
    padding:.62rem 1.4rem;
    font-family:'DM Mono',monospace; font-size:.72rem;
    letter-spacing:.08em; text-transform:uppercase;
    cursor:none; white-space:nowrap;
    transition:background .2s, transform .15s;
  }
  .send-btn:hover { background:var(--accent); transform:translateY(-2px); }

  /* ══════════════════════
     RECEIVER WRAPPER
  ══════════════════════ */
  .receiver {
    position:relative; z-index:1;
    width:min(520px,100%);
    display:none; flex-direction:column;
  }

  /* ── SEALED ── */
  .sealed {
    display:flex; flex-direction:column;
    animation: rise .9s cubic-bezier(.16,1,.3,1) both;
  }

  .r-eyebrow {
    display:flex; align-items:center; gap:.8rem;
    margin-bottom:2.6rem;
  }
  .r-line { flex:1; height:1px; background:var(--rule); }
  .r-badge {
    font-size:.6rem; letter-spacing:.18em; text-transform:uppercase;
    color:var(--accent); border:1px solid var(--warm); padding:.15rem .5rem;
  }

  .r-headline {
    font-family:'Spectral',serif; font-weight:300;
    font-size:clamp(2.4rem,8vw,5rem);
    line-height:.95; letter-spacing:-.04em;
    color:var(--ink); margin-bottom:2.8rem;
  }
  .r-headline em { font-style:italic; color:var(--accent); }

  /* sealed card — the fun interaction */
  .sealed-card {
    background:var(--paper);
    border:1px solid var(--rule);
    box-shadow:0 2px 24px rgba(28,25,23,.05);
    cursor:none;
    position:relative; overflow:hidden;
    transition:border-color .3s, box-shadow .3s;
    user-select:none;
  }

  .sealed-card:hover { border-color:var(--warm); box-shadow:0 6px 40px rgba(188,108,37,.12); }

  /* ripple on click */
  .ripple {
    position:absolute; border-radius:50%;
    background:rgba(221,161,94,.25);
    transform:scale(0); animation:rippleOut .6s ease forwards;
    pointer-events:none;
  }
  @keyframes rippleOut {
    to { transform:scale(4); opacity:0; }
  }

  .sealed-inner {
    padding:2rem;
    display:flex; align-items:center; gap:1.6rem;
  }

  /* wax seal */
  .wax {
    width:60px; height:60px; border-radius:50%; flex-shrink:0;
    background:radial-gradient(circle at 38% 35%, var(--warm), var(--accent));
    display:flex; align-items:center; justify-content:center;
    font-size:1.5rem;
    box-shadow:0 4px 14px rgba(188,108,37,.3);
    transition:transform .35s cubic-bezier(.34,1.56,.64,1);
    position:relative;
  }

  .wax::after {
    content:'';
    position:absolute; inset:-4px;
    border-radius:50%;
    border:1.5px dashed rgba(188,108,37,.3);
    animation:rotateDash 8s linear infinite;
  }

  @keyframes rotateDash { to { transform:rotate(360deg); } }
  .sealed-card:hover .wax { transform:scale(1.12) rotate(12deg); }

  .sealed-copy { flex:1; }
  .sealed-kicker { font-size:.58rem; letter-spacing:.18em; text-transform:uppercase; color:var(--soft); margin-bottom:.4rem; }
  .sealed-body { font-family:'Spectral',serif; font-style:italic; font-weight:300; font-size:1.15rem; color:var(--mid); }

  .sealed-arrow {
    font-size:1.2rem; color:var(--soft);
    transition:transform .3s, color .3s;
  }
  .sealed-card:hover .sealed-arrow { transform:translateX(5px); color:var(--accent); }

  /* progress bar under card */
  .hold-bar {
    height:3px; background:var(--rule); overflow:hidden;
  }
  .hold-fill {
    height:100%; width:0%;
    background:linear-gradient(90deg, var(--warm), var(--accent));
    transition:width .05s linear;
  }
  .hold-hint {
    margin-top:.7rem;
    font-size:.58rem; letter-spacing:.12em; text-transform:uppercase;
    color:var(--soft); text-align:center;
    transition:color .3s;
  }
  .hold-hint.active { color:var(--accent); }

  /* ── OPEN NOTE ── */
  .open-note {
    display:none; flex-direction:column;
  }

  .o-eyebrow {
    display:flex; align-items:center; gap:.8rem;
    margin-bottom:2.6rem;
    animation: rise .7s cubic-bezier(.16,1,.3,1) both;
  }
  .o-line { flex:1; height:1px; background:var(--rule); }
  .o-stamp {
    font-size:.58rem; letter-spacing:.14em; text-transform:uppercase;
    color:var(--accent); border:1px solid var(--warm); padding:.18rem .5rem;
  }

  .o-from {
    font-size:.6rem; letter-spacing:.16em; text-transform:uppercase;
    color:var(--soft); margin-bottom:2rem;
    animation: rise .7s .08s cubic-bezier(.16,1,.3,1) both;
  }
  .o-from span { color:var(--mid); }

  /* the note — unfolds from top */
  .note-block {
    background:var(--paper);
    border:1px solid var(--rule);
    box-shadow:0 2px 24px rgba(28,25,23,.06);
    position:relative;
    transform-origin:top center;
    animation:unfoldPaper .7s .12s cubic-bezier(.16,1,.3,1) both;
  }

  @keyframes unfoldPaper {
    from { opacity:0; transform:scaleY(0.4) translateY(-20px); }
    to   { opacity:1; transform:scaleY(1) translateY(0); }
  }

  /* folded corner */
  .note-block::before {
    content:''; position:absolute; top:0; right:0;
    width:28px; height:28px;
    background:linear-gradient(225deg, var(--bg) 50%, var(--rule) 50%);
  }

  .note-pad { padding:2.4rem 2rem 2rem; border-bottom:1px solid var(--rule); }

  .note-lines {
    position:absolute; inset:0; pointer-events:none; overflow:hidden; opacity:.35;
  }

  .note-text {
    font-family:'Spectral',serif; font-weight:300; font-style:italic;
    font-size:clamp(1.5rem,4.5vw,2.2rem);
    line-height:1.55; letter-spacing:-.01em; color:var(--ink);
    position:relative; z-index:1;
    /* typewriter reveal */
    overflow:hidden;
  }

  .note-text .char {
    display:inline;
    opacity:0;
    animation:charIn .03s forwards;
  }

  @keyframes charIn { to { opacity:1; } }

  .note-footer {
    padding:1rem 2rem;
    display:flex; align-items:center; justify-content:space-between;
  }

  .note-sig {
    font-family:'Spectral',serif; font-style:italic;
    font-size:.95rem; color:var(--mid);
    animation: rise .6s .5s cubic-bezier(.16,1,.3,1) both;
    opacity:0;
  }
  .note-sig strong { color:var(--ink); font-weight:300; }

  /* confetti hearts */
  .particle {
    position:fixed; pointer-events:none;
    font-size:1rem; opacity:0;
    animation:particleFly 1.2s ease forwards;
    z-index:500;
  }
  @keyframes particleFly {
    0%   { opacity:1; transform:translate(0,0) rotate(0deg) scale(1); }
    100% { opacity:0; transform:translate(var(--dx),var(--dy)) rotate(var(--dr)) scale(.4); }
  }

  /* dot trio */
  .dots { display:flex; gap:5px; align-items:center; }
  .dot  { width:5px; height:5px; border-radius:50%; background:var(--warm); animation:hb 1.8s ease infinite; }
  .dot:nth-child(2) { animation-delay:.18s; background:var(--accent); }
  .dot:nth-child(3) { animation-delay:.36s; }
  @keyframes hb {
    0%,60%,100% { transform:scale(1); opacity:.45; }
    30%          { transform:scale(1.6); opacity:1; }
  }

  /* shared */
  @keyframes rise {
    from { opacity:0; transform:translateY(18px); }
    to   { opacity:1; transform:translateY(0); }
  }
</style>
</head>
<body>

<div id="cur"></div>
<div id="curTrail"></div>

<!-- FLOATING PARTICLES CANVAS -->
<canvas id="bg" style="position:fixed;inset:0;z-index:0;pointer-events:none;"></canvas>

<!-- ══ SENDER ══ -->
<div class="sender" id="senderView">
  <div class="eyebrow">
    <div class="eyebrow-line"></div>
    <div class="eyebrow-text">compose a note</div>
    <div class="eyebrow-line"></div>
  </div>

  <div class="headline">write<br>her a <em>note</em></div>

  <div class="card">
    <div class="card-inner">
      <textarea id="msgInput" rows="5" placeholder="Don't forget to eat something today…"></textarea>
    </div>
    <div class="card-foot">
      <div class="from-inline">
        <span class="from-label">from</span>
        <input type="text" id="fromInput" placeholder="your name">
      </div>
      <button class="send-btn" id="sendBtn" onclick="showNote()">hand it over →</button>
    </div>
  </div>
</div>

<!-- ══ RECEIVER ══ -->
<div class="receiver" id="receiverView">

  <!-- sealed -->
  <div class="sealed" id="sealedState">
    <div class="r-eyebrow">
      <div class="r-line"></div>
      <div class="r-badge">1 new note</div>
      <div class="r-line"></div>
    </div>

    <div class="r-headline">hey,<br>look <em>here</em></div>

    <div class="sealed-card" id="sealedCard">
      <div class="sealed-inner">
        <div class="wax" id="wax">✦</div>
        <div class="sealed-copy">
          <div class="sealed-kicker">waiting for you</div>
          <div class="sealed-body">hold to unseal your note</div>
        </div>
        <div class="sealed-arrow">→</div>
      </div>
      <div class="hold-bar"><div class="hold-fill" id="holdFill"></div></div>
    </div>
    <div class="hold-hint" id="holdHint">hold the card to open</div>
  </div>

  <!-- open -->
  <div class="open-note" id="openState">
    <div class="o-eyebrow">
      <div class="o-line"></div>
      <div class="o-stamp">opened ✦</div>
      <div class="o-line"></div>
    </div>

    <div class="o-from">a reminder from <span id="displayFrom"></span></div>

    <div class="note-block">
      <div class="note-pad">
        <div class="note-text" id="displayMsg"></div>
      </div>
      <div class="note-footer">
        <div class="note-sig" id="noteSig">with love, <strong id="sigName"></strong></div>
        <div class="dots"><div class="dot"></div><div class="dot"></div><div class="dot"></div></div>
      </div>
    </div>
  </div>

</div>

<script>
/* ── cursor ── */
const cur = document.getElementById('cur');
const trail = document.getElementById('curTrail');
let mx=0,my=0,tx=0,ty=0;

document.addEventListener('mousemove', e => {
  mx=e.clientX; my=e.clientY;
  cur.style.left=mx+'px'; cur.style.top=my+'px';
});

document.addEventListener('mouseover', e => {
  cur.classList.toggle('hovering', !!e.target.closest('button,.sealed-card,textarea,input'));
});

(function tickTrail(){
  tx+=(mx-tx)*.13; ty+=(my-ty)*.13;
  trail.style.left=tx+'px'; trail.style.top=ty+'px';
  requestAnimationFrame(tickTrail);
})();

/* ── floating dots bg ── */
const canvas = document.getElementById('bg');
const ctx = canvas.getContext('2d');
let W,H,dots=[];

function resizeCanvas(){
  W=canvas.width=window.innerWidth;
  H=canvas.height=window.innerHeight;
}

function makeDot(){
  return {
    x: Math.random()*W, y: Math.random()*H,
    r: 1+Math.random()*2,
    vx:(Math.random()-.5)*.18, vy:(Math.random()-.5)*.18,
    a: .08+Math.random()*.2,
    c: Math.random()<.5 ? '#DDA15E' : '#BC6C25'
  };
}

resizeCanvas();
window.addEventListener('resize', resizeCanvas);
for(let i=0;i<38;i++) dots.push(makeDot());

(function drawBg(){
  ctx.clearRect(0,0,W,H);
  dots.forEach(d=>{
    ctx.beginPath();
    ctx.arc(d.x,d.y,d.r,0,Math.PI*2);
    ctx.fillStyle = d.c;
    ctx.globalAlpha = d.a;
    ctx.fill();
    d.x+=d.vx; d.y+=d.vy;
    if(d.x<0||d.x>W) d.vx*=-1;
    if(d.y<0||d.y>H) d.vy*=-1;
  });
  ctx.globalAlpha=1;
  requestAnimationFrame(drawBg);
})();

/* ── state ── */
let _msg='', _from='';

function showNote(){
  _msg  = document.getElementById('msgInput').value.trim();
  _from = document.getElementById('fromInput').value.trim() || '—';
  if(!_msg){
    const ta=document.getElementById('msgInput');
    ta.style.color='#bc4a4a'; ta.focus();
    setTimeout(()=>ta.style.color='',1400);
    return;
  }
  document.getElementById('senderView').style.display='none';
  document.getElementById('receiverView').style.display='flex';
}

/* ── hold-to-open ── */
let holdTimer=null, holdProgress=0, holding=false;

const sealedCard = document.getElementById('sealedCard');
const holdFill   = document.getElementById('holdFill');
const holdHint   = document.getElementById('holdHint');

function startHold(e){
  if(holding) return;
  holding=true;
  holdHint.classList.add('active');
  addRipple(e);
  holdTimer = setInterval(()=>{
    holdProgress = Math.min(100, holdProgress+3.5);
    holdFill.style.width = holdProgress+'%';
    if(holdProgress>=100){ clearInterval(holdTimer); openNote(); }
  },40);
}

function endHold(){
  if(!holding) return;
  holding=false;
  holdHint.classList.remove('active');
  clearInterval(holdTimer);
  if(holdProgress<100){
    holdProgress=0; holdFill.style.width='0%';
  }
}

sealedCard.addEventListener('mousedown',  startHold);
sealedCard.addEventListener('touchstart', e=>{ startHold(e.touches[0]); }, {passive:true});
document.addEventListener('mouseup',  endHold);
document.addEventListener('touchend', endHold);

function addRipple(e){
  const rect = sealedCard.getBoundingClientRect();
  const r = document.createElement('div');
  r.className='ripple';
  const size=Math.max(rect.width,rect.height)*2;
  r.style.cssText=`width:${size}px;height:${size}px;left:${(e.clientX-rect.left)-(size/2)}px;top:${(e.clientY-rect.top)-(size/2)}px;`;
  sealedCard.appendChild(r);
  setTimeout(()=>r.remove(),700);
}

/* ── open note ── */
function openNote(){
  document.getElementById('displayFrom').textContent = _from;
  document.getElementById('sigName').textContent     = _from;
  document.getElementById('sealedState').style.display='none';
  document.getElementById('openState').style.display='flex';

  // typewriter
  typewriterReveal(_msg, document.getElementById('displayMsg'));

  // particles
  setTimeout(burst, 200);

  // fade in sig after text
  setTimeout(()=>{
    const sig=document.getElementById('noteSig');
    sig.style.animation='rise .6s cubic-bezier(.16,1,.3,1) forwards';
  }, _msg.length*28+300);
}

function typewriterReveal(text, el){
  el.innerHTML='';
  const chars=[...text];
  chars.forEach((ch,i)=>{
    const span=document.createElement('span');
    span.className='char';
    span.textContent=ch;
    span.style.animationDelay=(i*28)+'ms';
    el.appendChild(span);
  });
}

/* ── confetti burst ── */
function burst(){
  const symbols=['✦','♥','·','◦','✿','★'];
  const colors=['#DDA15E','#BC6C25','#606C38','#283618','#FEFAE0'];
  for(let i=0;i<22;i++){
    const p=document.createElement('div');
    p.className='particle';
    p.textContent = symbols[Math.floor(Math.random()*symbols.length)];
    p.style.color = colors[Math.floor(Math.random()*colors.length)];
    const angle=Math.random()*360;
    const dist=70+Math.random()*160;
    p.style.cssText+=`
      left:50vw; top:50vh;
      --dx:${Math.cos(angle*Math.PI/180)*dist}px;
      --dy:${Math.sin(angle*Math.PI/180)*dist-80}px;
      --dr:${Math.random()*480-240}deg;
      animation-delay:${Math.random()*0.1}s;
    `;
    document.body.appendChild(p);
    setTimeout(()=>p.remove(),1400);
  }
}
</script>
</body>
</html>
