# Prank<!doctype html>
<html lang="ar">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1.0,viewport-fit=cover">
<title>اختراق؟</title>
<style>
  :root{--bg:#000;--green:#32ff7e;--muted:#99a;}
  *{box-sizing:border-box;font-family:system-ui,-apple-system,Segoe UI,Roboto,"Helvetica Neue",Arial;}
  html,body{height:100%;margin:0;background:var(--bg);color:var(--green);}
  .center{
    position:fixed;inset:0;display:flex;align-items:center;justify-content:center;flex-direction:column;
    gap:18px;padding:20px;
  }
  button{
    padding:14px 22px;border-radius:10px;border:2px solid var(--green);background:transparent;color:var(--green);
    font-weight:700;font-size:16px;cursor:pointer;
  }
  #canvas{position:fixed;inset:0;z-index:0;display:block;}
  .overlay{position:relative;z-index:2;text-align:center;max-width:920px;width:100%;}
  .title{font-size:20px;letter-spacing:1px}
  .big{font-size:28px;font-weight:800}
  .countdown{font-size:72px;font-weight:900;margin-top:10px}
  .progressWrap{background:rgba(255,255,255,0.03);border:1px solid rgba(255,255,255,0.03);height:18px;border-radius:10px;overflow:hidden;margin-top:10px;}
  .progressBar{height:100%;width:0%;background:linear-gradient(90deg,#00ff9c,#20ff5f);transition:width .2s;}
  .reveal{display:none;background:rgba(0,0,0,0.7);padding:20px;border-radius:12px;margin-top:16px;}
  .reveal.show{display:block;}
  .muted{color:var(--muted);font-size:13px;margin-top:8px}
  .small{font-size:13px}
  @media(min-width:600px){
    .title{font-size:22px}.big{font-size:36px}.countdown{font-size:96px}
  }
</style>
</head>
<body>
<canvas id="canvas"></canvas>

<div class="center overlay" id="ui">
  <div class="title big">اكتشاف نشاط مشبوه... هل تريد المتابعة؟</div>
  <div class="muted">انقر "ابدأ" لمتابعة — (هذا مقلب آمن، لا يخترق فعليًا).</div>
  <button id="startBtn">ابدأ</button>
  <div class="muted small">ملاحظة: المتصفح سيطلب ملء الشاشة — هذه خطوة لازمة لتأثير المقلب.</div>
</div>

<div class="center overlay" id="hackUI" style="display:none;">
  <div class="title">جاري الوصول إلى النظام <span id="deviceInfo" class="muted"></span></div>
  <div class="countdown" id="count">10</div>
  <div class="progressWrap" style="width:70%">
    <div class="progressBar" id="pbar"></div>
  </div>
  <div class="reveal" id="revealBox">
    <div class="big" id="revealTitle">مقلب! 😂</div>
    <div class="muted" id="revealText">يا سلام — أنت تم خداعك من قبل: <strong id="owner">رزان</strong></div>
    <button id="finishBtn" style="margin-top:12px">خلاص، رجعني للخارج</button>
  </div>
</div>

<script>
/* ====== Matrix-like canvas animation ====== */
const canvas = document.getElementById('canvas');
const ctx = canvas.getContext('2d');
let w, h, columns, drops;
function resize(){
  w = canvas.width = innerWidth;
  h = canvas.height = innerHeight;
  const fontSize = Math.max(12, Math.min(22, Math.floor(w/60)));
  ctx.font = fontSize + 'px monospace';
  columns = Math.floor(w / fontSize);
  drops = Array.from({length:columns}).map(()=>Math.floor(Math.random()*h));
}
resize();
addEventListener('resize', resize);
const chars = "01{}()<>=+-*/%$#@!abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ٠١٢٣٤٥٦٧٨٩";
function frame(){
  ctx.fillStyle = 'rgba(0,0,0,0.18)';
  ctx.fillRect(0,0,w,h);
  ctx.fillStyle = '#00ff77';
  for(let i=0;i<columns;i++){
    const text = chars.charAt(Math.floor(Math.random()*chars.length));
    const x = i * (ctx.measureText('M').width);
    const y = drops[i] * (parseInt(ctx.font,10));
    ctx.fillText(text, x, y);
    if(y > h && Math.random() > 0.975) drops[i] = 0;
    drops[i]++;
  }
  requestAnimationFrame(frame);
}
frame();

/* ====== Interaction & prank logic ====== */
const startBtn = document.getElementById('startBtn');
const hackUI = document.getElementById('hackUI');
const ui = document.getElementById('ui');
const countEl = document.getElementById('count');
const pbar = document.getElementById('pbar');
const revealBox = document.getElementById('revealBox');
const ownerEl = document.getElementById('owner');
const deviceInfo = document.getElementById('deviceInfo');
const finishBtn = document.getElementById('finishBtn');

function simpleBeep(freq=440,dur=120){
  try{
    const ctx = new (window.AudioContext || window.webkitAudioContext)();
    const o = ctx.createOscillator();
    const g = ctx.createGain();
    o.connect(g); g.connect(ctx.destination);
    o.type = 'sawtooth';
    o.frequency.value = freq;
    g.gain.value = 0.05;
    o.start();
    setTimeout(()=>{ o.stop(); ctx.close(); }, dur);
  }catch(e){}
}

function tryFullscreen(el){
  if(el.requestFullscreen) return el.requestFullscreen();
  if(el.webkitRequestFullscreen) return el.webkitRequestFullscreen();
  if(el.msRequestFullscreen) return el.msRequestFullscreen();
  return Promise.resolve();
}

function getDeviceShort(){
  const ua = navigator.userAgent || '';
  if(/Android/i.test(ua)) return ' — Android';
  if(/iPhone|iPad|iPod/i.test(ua)) return ' — iOS';
  if(/Windows/i.test(ua)) return ' — Windows';
  if(/Macintosh/i.test(ua)) return ' — macOS';
  return '';
}

startBtn.addEventListener('click', async ()=>{
  await tryFullscreen(document.documentElement).catch(()=>{});
  ui.style.display = 'none';
  hackUI.style.display = 'flex';
  deviceInfo.textContent = getDeviceShort();
  let count = 10;
  const countdownInterval = setInterval(()=>{
    count--;
    countEl.textContent = count;
    simpleBeep(600,70);
    if(count <= 0){
      clearInterval(countdownInterval);
      startFakeDeletion();
    }
  }, 900);
});

function startFakeDeletion(){
  const steps = 60;
  let cur = 0;
  const stepTime = 45;
  const texts = [
    "الوصول إلى ذاكرة التخزين...",
    "تفريغ السجلات...",
    "حذف الملفات المؤقتة...",
    "تشفير المحتوى... (وهمي)",
    "تنظيف الآثار..."
  ];
  const msg = document.createElement('div');
  msg.className = 'muted small';
  msg.style.marginTop='10px';
  hackUI.appendChild(msg);
  const prog = setInterval(()=>{
    cur++;
    const pct = Math.min(100, Math.round((cur/steps)*100));
    pbar.style.width = pct + '%';
    msg.textContent = texts[cur % texts.length] + ' — ' + pct + '%';
    simpleBeep(200 + Math.random()*800, 40);
    if(cur >= steps){
      clearInterval(prog);
      setTimeout(()=>showReveal(), 600);
    }
  }, stepTime);
}

function showReveal(){
  revealBox.classList.add('show');
  ownerEl.textContent = 'رزان';
  revealTitle.textContent = 'مقلب! تم خداعك 😂';
  simpleBeep(880,200);
}

finishBtn.addEventListener('click', ()=>{
  try{ if (document.exitFullscreen) document.exitFullscreen(); } catch(e){}
  location.reload();
});
</script>
</body>
</html>
