<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Happy Birthday My Love 🎂</title>
  <meta name="description" content="A romantic birthday surprise page for Kajarin" />
  <meta property="og:title" content="Happy Birthday My Love 🎂">
  <meta property="og:description" content="I still love you… and more with every year">
  <meta name="theme-color" content="#111827">
  <style>
    :root{
      --bg:#0b1020;--fg:#f8fafc;--accent:#ffba08;--muted:#cbd5e1;
      --card:#111827;--btn:#22c55e;--btnText:#0b1020;
    }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0; font:16px/1.6 ui-sans-serif,-apple-system,BlinkMacSystemFont,"Segoe UI",
      Roboto,Inter,"Helvetica Neue",Arial; color:var(--fg);
      background: radial-gradient(1200px 600px at 10% 10%, #1f2937 0%, transparent 60%),
                  radial-gradient(1200px 600px at 90% 20%, #0f172a 0%, transparent 60%),
                  radial-gradient(1200px 600px at 50% 100%, #1f2937 0%, transparent 60%),
                  var(--bg);
      display:grid; place-items:center; padding:24px;
    }
    .card{
      width:min(720px, 100%);
      background:linear-gradient(180deg, rgba(255,255,255,0.04), rgba(255,255,255,0.02));
      border:1px solid rgba(255,255,255,.08);
      border-radius:24px; padding:28px; box-shadow:0 10px 30px rgba(0,0,0,.35);
      backdrop-filter:saturate(120%) blur(6px);
    }
    h1{margin:0 0 8px; font-size:clamp(28px,4vw,40px)}
    p{margin:0 0 16px; color:var(--muted)}
    .hero{display:flex; gap:18px; align-items:center; justify-content:space-between; flex-wrap:wrap}
    .hero__text{flex:1 1 300px}
    .hero__cta{display:flex; gap:12px; align-items:center; flex-wrap:wrap}
    button{
      border:0; border-radius:14px; padding:12px 18px; cursor:pointer; font-weight:700;
      background:var(--btn); color:var(--btnText);
      box-shadow:0 6px 18px rgba(34,197,94,.35); transition:transform .12s ease;
    }
    button:active{transform:translateY(1px) scale(.99)}
    .ghost{background:transparent; color:var(--fg); border:1px solid rgba(255,255,255,.15)}
    .hidden{display:none}
    .reveal{margin-top:18px; padding:16px; border-radius:16px; background:#0e1726; border:1px solid rgba(255,255,255,.08)}
    .badge{
      display:inline-flex; align-items:center; gap:8px;
      background:#0e1726; color:#e2e8f0; border:1px solid rgba(255,255,255,.1);
      padding:8px 12px; border-radius:999px; font-size:14px
    }
    .confetti-canvas{position:fixed; inset:0; pointer-events:none}
    @media (prefers-reduced-motion: reduce){
      *{animation:none!important; transition:none!important}
    }
  </style>
</head>
<body>
  <canvas class="confetti-canvas" aria-hidden="true"></canvas>

  <main class="card" role="main">
    <div class="hero">
      <div class="hero__text">
        <span class="badge" aria-hidden="true">🎂 Birthday Mode Active</span>
        <h1 id="title">Happy Birthday, <span id="name">Kajarin</span> my love 🎉</h1>
        <p id="lead">
          Every day you make my world softer and brighter. Today is your day—click below for your surprise 💝
        </p>
        <div class="hero__cta" style="margin-top:10px">
          <button id="surpriseBtn" aria-controls="surprise" aria-expanded="false">Open Surprise 🎁</button>
          <button class="ghost" id="setNameBtn" aria-describedby="title">Edit Name/Birthday ✍️</button>
        </div>
      </div>
      <div aria-hidden="true" style="flex:0 1 160px; font-size:54px">🎈🎈🎈</div>
    </div>

    <section id="surprise" class="reveal hidden" aria-live="polite">
      <p><strong>Happy Birthday my dearest,</strong> may this year be full of joy, hope and beautiful moments for us 💫</p>
      <p style="margin:6px 0">
        <span id="loveLine">I <strong>still love you just the same… and even more every year</strong> ❤️</span>
      </p>
      <p id="ageLine" class="hidden">This year you turn <span id="age"></span> ✨</p>
      <p id="bdayLine" class="hidden">Born on <span id="bdayText"></span></p>
      <p style="margin:8px 0 0; font-size:14px; color:#94a3b8">Tip: turn on sound for a little celebration 🎵</p>
      <audio id="cheer" preload="auto">
        <source src="data:audio/mp3;base64,//uQZAAAAAAAAAAAAAAAAAAAA" type="audio/mpeg">
      </audio>
    </section>
  </main>

  <script>
    // Defaults
    const DEFAULT_NAME = "Kajarin";
    const DEFAULT_BIRTHDAY_ISO = "2005-09-15"; // YYYY-MM-DD

    // DOM
    const $ = (s, r=document)=>r.querySelector(s);
    const surpriseBtn = $("#surpriseBtn");
    const setNameBtn = $("#setNameBtn");
    const revealBox = $("#surprise");
    const nameSpan = $("#name");
    const ageLine = $("#ageLine");
    const ageSpan = $("#age");
    const bdayLine = $("#bdayLine");
    const bdayText = $("#bdayText");
    const cheer = $("#cheer");
    const canvas = $(".confetti-canvas");
    const ctx = canvas.getContext("2d",{alpha:true});
    let w, h, particles = [], rafId;
    const DPR = Math.min(2, window.devicePixelRatio || 1);

    // Canvas
    function resize(){
      w = canvas.width = Math.floor(innerWidth * DPR);
      h = canvas.height = Math.floor(innerHeight * DPR);
      canvas.style.width = innerWidth+"px"; canvas.style.height = innerHeight+"px";
    }
    addEventListener("resize", resize); resize();

    // Confetti
    function rand(min,max){return Math.random()*(max-min)+min}
    function createBurst(x=w/2, y=0.25*h){
      const colors = ["#fde047","#60a5fa","#34d399","#f472b6","#fca5a5","#c4b5fd"];
      for(let i=0;i<120;i++){
        particles.push({
          x, y, r: rand(2,4)*DPR, c: colors[i%colors.length],
          vx: rand(-3,3)*DPR, vy: rand(-6,-2)*DPR, g: 0.08*DPR, rot: rand(0,Math.PI*2),
          vr: rand(-0.1,0.1)
        });
      }
      loop();
    }
    function loop(){
      cancelAnimationFrame(rafId);
      rafId = requestAnimationFrame(loop);
      ctx.clearRect(0,0,w,h);
      for (let p of particles){
        p.vy += p.g; p.x += p.vx; p.y += p.vy; p.rot += p.vr;
        ctx.save(); ctx.translate(p.x,p.y); ctx.rotate(p.rot);
        ctx.fillStyle = p.c; ctx.fillRect(-p.r, -p.r, p.r*2, p.r*2);
        ctx.restore();
      }
      particles = particles.filter(p => p.y < h + 20*DPR);
      if(!particles.length) cancelAnimationFrame(rafId);
    }

    // Age & date
    function calcAge(iso){
      const today = new Date();
      const [y,m,d] = iso.split("-").map(Number);
      const bd = new Date(y, m-1, d);
      let age = today.getFullYear() - y;
      const hasHadBirthdayThisYear =
        (today.getMonth() > m-1) || (today.getMonth() === m-1 && today.getDate() >= d);
      if(!hasHadBirthdayThisYear) age--;
      return { age: Math.max(0, age), bd };
    }
    function fmtDate(date){
      const months = ["January","February","March","April","May","June",
        "July","August","September","October","November","December"];
      return `${months[date.getMonth()]} ${date.getDate()}, ${date.getFullYear()}`;
    }

    // Events
    surpriseBtn.addEventListener("click", () => {
      const isHidden = revealBox.classList.contains("hidden");
      revealBox.classList.toggle("hidden");
      surpriseBtn.setAttribute("aria-expanded", String(isHidden));
      if (isHidden){
        try{ cheer.currentTime = 0; cheer.play().catch(()=>{});}catch(e){}
        createBurst(w*0.5, h*0.25);
        setTimeout(()=>createBurst(w*0.25, h*0.3), 180);
        setTimeout(()=>createBurst(w*0.75, h*0.28), 360);

        const iso = localStorage.getItem("bornISO") || DEFAULT_BIRTHDAY_ISO;
        const info = calcAge(iso);
        ageSpan.textContent = info.age;
        ageLine.classList.remove("hidden");
        bdayText.textContent = fmtDate(info.bd);
        bdayLine.classList.remove("hidden");
      }
    });

    setNameBtn.addEventListener("click", () => {
      const nm = prompt("Type the name:", localStorage.getItem("friendName") || DEFAULT_NAME);
      if(nm){ nameSpan.textContent = nm; localStorage.setItem("friendName", nm); }
      const by = prompt("Birthday (YYYY-MM-DD):",
                        localStorage.getItem("bornISO") || DEFAULT_BIRTHDAY_ISO);
      if(by && /^\d{4}-\d{2}-\d{2}$/.test(by)){ localStorage.setItem("bornISO", by); }
    });

    // Init
    (function init(){
      const nm = localStorage.getItem("friendName") || DEFAULT_NAME;
      nameSpan.textContent = nm;
      document.getElementById("lead").textContent =
        "Thank you for being the light in my life. Even if we are sometimes apart, my heart is always with you 💖";
    })();
  </script>
</body>
</html>
