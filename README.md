<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>لعبة مقارنة الأعداد</title>
  <!-- خط عربي اختياري -->
  <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;700&display=swap" rel="stylesheet">
  <style>
    :root{
      --bg1:#0ea5e9; /* أزرق سماوي */
      --bg2:#8b5cf6; /* بنفسجي */
      --card:#ffffff;
      --ink:#0f172a; /* slate-900 */
      --muted:#475569; /* slate-600 */
      --ok:#16a34a;
      --bad:#dc2626;
      --brand:#2563eb;
      --chip:#f1f5f9;
    }
    *{box-sizing:border-box}
    body{
      margin:0;min-height:100vh;
      font-family:"Cairo", system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
      background: radial-gradient(1200px 600px at 10% 0%, var(--bg1), transparent 60%),
                  radial-gradient(1200px 600px at 90% 0%, var(--bg2), transparent 60%),
                  linear-gradient(180deg,#0b1020,#0b1326 40%,#0b1020);
      color:var(--ink);
      padding:24px;
    }
    .wrap{max-width:900px;margin:0 auto}
    .card{
      background:var(--card); border-radius:16px; box-shadow:0 20px 60px rgba(0,0,0,.25);
      overflow:hidden
    }
    .header{
      padding:20px 24px;
      background:linear-gradient(90deg,rgba(14,165,233,.15),rgba(139,92,246,.15));
      display:flex;flex-wrap:wrap;gap:16px;align-items:center;justify-content:space-between
    }
    .title{font-size:clamp(20px,2.6vw,28px);font-weight:800;margin:0;color:#0b2a4a}
    .subtitle{margin:0;color:var(--muted);font-size:14px}
    .kpis{display:flex;gap:12px;flex-wrap:wrap}
    .chip{
      background:var(--chip);border-radius:12px;padding:10px 14px;min-width:130px;
      display:flex;align-items:center;justify-content:space-between;font-weight:700
    }
    .bar{
      height:10px;background:#e2e8f0;border-radius:999px;overflow:hidden
    }
    .bar>span{display:block;height:100%;width:0;background:linear-gradient(90deg,#22c55e,#06b6d4,#8b5cf6)}
    .body{padding:24px}
    .question{
      display:flex;align-items:center;justify-content:center;gap:18px;flex-wrap:wrap;
      border:1px dashed #e2e8f0;border-radius:14px;padding:18px;background:#fbfdff
    }
    .num{
      font-size:clamp(28px,5vw,44px);font-weight:800;line-height:1;color:#0b2a4a
    }
    .sign{font-size:clamp(28px,6vw,56px);opacity:.25}
    .opts{display:flex;gap:12px;justify-content:center;margin-top:16px;flex-wrap:wrap}
    .btn{
      border:none;border-radius:12px;padding:12px 18px;font-weight:800;cursor:pointer;
      font-size:20px;background:#0ea5e9;color:white;transition:.15s transform, .15s filter
    }
    .btn:hover{transform:translateY(-1px);filter:brightness(1.05)}
    .btn:disabled{opacity:.5;cursor:not-allowed}
    .btn.alt{background:#64748b}
    .btn.ok{background:var(--ok)}
    .btn.bad{background:var(--bad)}
    .feedback{
      margin-top:20px;border:1px solid #e2e8f0;border-radius:14px;overflow:hidden
    }
    .fb-head{
      display:flex;gap:8px;align-items:center;justify-content:space-between;
      padding:12px 14px;background:#f8fafc
    }
    .tabs{display:flex;gap:8px}
    .tab{
      padding:8px 12px;border-radius:999px;border:1px solid #e2e8f0;background:white;cursor:pointer;font-weight:700
    }
    .tab.active{background:#0ea5e9;color:white;border-color:#0ea5e9}
    .fb-body{padding:16px;background:white}
    .stack{
      display:inline-block;border:1px solid #e2e8f0;border-radius:10px;padding:10px 12px;background:#fcfcff
    }
    .stack .row{
      display:flex;gap:6px;justify-content:flex-end;font-size:28px;font-weight:800
    }
    .digit{
      width:32px;height:42px;display:inline-flex;align-items:center;justify-content:center;border-radius:8px
    }
    .digit.diff{background:#f3e8ff;outline:2px solid #8b5cf6}
    .legend{display:flex;gap:12px;flex-wrap:wrap;margin-top:10px;color:var(--muted);font-size:14px}
    .legend span{display:inline-flex;align-items:center;gap:6px}
    .dot{width:10px;height:10px;border-radius:999px;display:inline-block}
    .next-zone{margin-top:18px;display:flex;justify-content:space-between;align-items:center;gap:10px}
    .sum{padding:28px;text-align:center}
    .sum h2{margin:0 0 10px}
    .small{font-size:12px;color:var(--muted)}
    /* SVG container */
    .svg-wrap{max-width:100%;overflow-x:auto}
  </style>
</head>
<body>
  <div class="wrap">
    <div class="card">
      <div class="header">
        <div>
          <h1 class="title">🧮 لعبة مقارنة الأعداد</h1>
          <p class="subtitle">اختر الإشارة الصحيحة بين عددين عشوائيين. هناك ٢٠ مسألة. بعد كل إجابة ستظهر تغذية راجعة بصريّة.</p>
        </div>
        <div class="kpis">
          <div class="chip"><span>السؤال</span><strong id="qIdx">١ / ٢٠</strong></div>
          <div class="chip"><span>النتيجة</span><strong id="score">٠</strong></div>
          <div class="chip" style="min-width:220px;">
            <div style="flex:1">
              <div class="small">شريط التقدّم</div>
              <div class="bar" aria-label="Progress"><span id="bar"></span></div>
            </div>
          </div>
        </div>
      </div>

      <div class="body" id="game">
        <div class="question" aria-live="polite">
          <div class="num" id="numA">٠</div>
          <div class="sign">؟</div>
          <div class="num" id="numB">٠</div>
        </div>

        <div class="opts" id="options"></div>

        <div class="feedback" id="feedback" hidden>
          <div class="fb-head">
            <div>
              <strong id="resultMsg">—</strong>
              <div class="small" id="whyMsg"></div>
            </div>
            <div class="tabs">
              <button class="tab active" id="tabLine">خطّ الأعداد</button>
              <button class="tab" id="tabStack">تمثيل عمودي</button>
            </div>
          </div>
          <div class="fb-body">
            <div id="viewLine" class="svg-wrap"></div>
            <div id="viewStack" style="display:none"></div>
            <div class="legend">
              <span><i class="dot" style="background:#2563eb"></i>العدد الأوّل</span>
              <span><i class="dot" style="background:#f59e0b"></i>العدد الثاني</span>
              <span><i class="dot" style="background:#64748b"></i>المنتصف</span>
            </div>
          </div>
        </div>

        <div class="next-zone">
          <button class="btn alt" id="skipBtn" title="تخطي هذه المسألة (لا تُحتسب)">تخطي</button>
          <button class="btn" id="nextBtn" disabled>التالي ⟵</button>
        </div>
      </div>

      <div class="sum" id="summary" hidden>
        <h2>أحسنت! انتهت اللعبة 🎉</h2>
        <p>نتيجتك: <strong id="finalScore">٠</strong> من ٢٠</p>
        <p class="small" id="finalRemark"></p>
        <button class="btn" onclick="restart()">إعادة اللعب</button>
      </div>
    </div>
  </div>

  <script>
    // ====== إعدادات عامة ======
    const TOTAL = 20;
    const MIN = 1000;
    const MAX = 999_999_999; // ٩٩٩٩٩٩٩٩٩

    // تحويل الأرقام إلى أرقام عربية (٠١٢٣٤٥٦٧٨٩)
    function toArabicDigits(x){
      return String(x).replace(/\d/g, d => "٠١٢٣٤٥٦٧٨٩"[d]);
    }

    // خلط عشوائي (فيشر–ياتس)
    function shuffle(arr){
      const a = arr.slice();
      for(let i=a.length-1;i>0;i--){
        const j = Math.floor(Math.random()*(i+1));
        [a[i],a[j]]=[a[j],a[i]];
      }
      return a;
    }

    // توليد زوج أعداد عشوائي مع احتمال للمساواة
    function randomPair(){
      // 12.5% مساواة لزيادة ظهور حالة "="
      if(Math.random() < 0.125){
        const v = MIN + Math.floor(Math.random()*(MAX-MIN+1));
        return [v,v];
      }
      let a = MIN + Math.floor(Math.random()*(MAX-MIN+1));
      let b = MIN + Math.floor(Math.random()*(MAX-MIN+1));
      // تأكد من عدم التطابق غالبًا
      if(a===b) b = Math.min(MAX, b+1);
      return [a,b];
    }

    // الحالة
    let idx=0, score=0, pair=null, locked=false;

    const elA = document.getElementById('numA');
    const elB = document.getElementById('numB');
    const elOpts = document.getElementById('options');
    const elQ = document.getElementById('qIdx');
    const elScore = document.getElementById('score');
    const elBar = document.getElementById('bar');
    const elFeedback = document.getElementById('feedback');
    const elRes = document.getElementById('resultMsg');
    const elWhy = document.getElementById('whyMsg');
    const vLine = document.getElementById('viewLine');
    const vStack = document.getElementById('viewStack');
    const nextBtn = document.getElementById('nextBtn');
    const skipBtn = document.getElementById('skipBtn');
    const tabLine = document.getElementById('tabLine');
    const tabStack = document.getElementById('tabStack');
    const summary = document.getElementById('summary');
    const game = document.getElementById('game');
    const finalScore = document.getElementById('finalScore');
    const finalRemark = document.getElementById('finalRemark');

    tabLine.addEventListener('click', ()=>switchView('line'));
    tabStack.addEventListener('click', ()=>switchView('stack'));
    skipBtn.addEventListener('click', ()=>{ if(!locked){ locked=true; showFeedback(null); nextBtn.disabled=false; } });

    function switchView(which){
      if(which==='line'){
        tabLine.classList.add('active'); tabStack.classList.remove('active');
        vLine.style.display='block'; vStack.style.display='none';
      }else{
        tabStack.classList.add('active'); tabLine.classList.remove('active');
        vStack.style.display='block'; vLine.style.display='none';
      }
    }

    function correctSign(a,b){
      if(a<b) return '<';
      if(a>b) return '>';
      return '=';
    }

    function renderOptions(a,b){
      elOpts.innerHTML='';
      const signs = shuffle(['<','>','=']);
      signs.forEach(s=>{
        const btn = document.createElement('button');
        btn.className='btn';
        btn.textContent = s;
        btn.addEventListener('click', ()=>handleChoice(s,a,b,btn));
        elOpts.appendChild(btn);
      });
    }

    function handleChoice(choice,a,b,btn){
      if(locked) return;
      locked=true;
      const truth = correctSign(a,b);
      // تلوين الأزرار
      ;[...elOpts.children].forEach(bn=>{
        bn.disabled=true;
        if(bn.textContent===truth) bn.classList.add('ok');
        else if(bn===btn) bn.classList.add('bad');
        else bn.classList.add('alt');
      });
      const isRight = (choice===truth);
      if(isRight) score++;
      updateTop();
      showFeedback(isRight);
      nextBtn.disabled=false;
    }

    function showFeedback(isRight){
      elFeedback.hidden=false;
      const [a,b] = pair;
      const truth = correctSign(a,b);
      // رسالة
      if(isRight===null){
        elRes.textContent = 'تم تخطي المسألة.';
        elWhy.textContent = `كانت الإجابة الصحيحة: ${truth}`;
      }else if(isRight){
        elRes.textContent = 'إجابة صحيحة ✅';
        elWhy.textContent = explain(a,b,truth);
      }else{
        elRes.textContent = 'إجابة غير صحيحة ❌';
        elWhy.textContent = explain(a,b,truth);
      }
      // تمثيل خط الأعداد والتمثيل العمودي
      renderLine(a,b);
      renderStack(a,b);
      // افتراضيًا نعرض خط الأعداد، وإن كان العددان متقاربين نبدّل للتمثيل العمودي
      const diff = Math.abs(a-b);
      if(diff<=9){ switchView('stack'); } else { switchView('line'); }
    }

    function explain(a,b,truth){
      const A = toArabicDigits(a), B = toArabicDigits(b);
      if(truth==='=') return `العدد ${A} يساوي ${B}.`;
      if(truth==='>') return `العدد ${A} أكبر من ${B}.`;
      return `العدد ${A} أصغر من ${B}.`;
    }

    // ====== خط الأعداد (SVG) ======
    function renderLine(a,b){
      const min = Math.min(a,b), max = Math.max(a,b);
      let pad = Math.ceil((max-min)*0.1);
      if(pad<1) pad = 1;
      const L = min - pad, R = max + pad;
      const W = 560, H = 120, M = 40; // أبعاد
      const scale = x => M + ( (x - L) / (R - L) ) * (W - 2*M);
      const mid = Math.floor((L+R)/2);

      const xa = scale(a), xb = scale(b), xm = scale(mid);
      const xL = scale(L), xR = scale(R);

      const svg = `
        <svg width="${W}" height="${H}" xmlns="http://www.w3.org/2000/svg" role="img" aria-label="تمثيل على خط الأعداد">
          <line x1="${xL}" y1="${H/2}" x2="${xR}" y2="${H/2}" stroke="#334155" stroke-width="2"/>
          <!-- علامات -->
          <line x1="${xL}" y1="${H/2-6}" x2="${xL}" y2="${H/2+6}" stroke="#94a3b8"/>
          <line x1="${xR}" y1="${H/2-6}" x2="${xR}" y2="${H/2+6}" stroke="#94a3b8"/>
          <line x1="${xm}" y1="${H/2-8}" x2="${xm}" y2="${H/2+8}" stroke="#94a3b8"/>
          <!-- نقاط -->
          <circle cx="${xa}" cy="${H/2}" r="7" fill="#2563eb"/>
          <circle cx="${xb}" cy="${H/2}" r="7" fill="#f59e0b"/>
          <!-- تسميات -->
          <text x="${xa}" y="${H/2-14}" font-size="14" text-anchor="middle" fill="#0b2a4a" direction="rtl">${toArabicDigits(a)}</text>
          <text x="${xb}" y="${H/2-14}" font-size="14" text-anchor="middle" fill="#0b2a4a" direction="rtl">${toArabicDigits(b)}</text>
          <text x="${xm}" y="${H/2+26}" font-size="12" text-anchor="middle" fill="#64748b" direction="rtl">${toArabicDigits(mid)}</text>
        </svg>
      `;
      vLine.innerHTML = svg;
    }

    // ====== تمثيل عمودي (محاذاة منازل) ======
    function renderStack(a,b){
      const sa = String(a), sb = String(b);
      const len = Math.max(sa.length, sb.length);
      const pa = sa.padStart(len,' ');
      const pb = sb.padStart(len,' ');
      // أول منزلة مختلفة
      let firstDiff = -1;
      for(let i=0;i<len;i++){
        if(pa[i]!==pb[i]){ firstDiff = i; break; }
      }
      const row = (str, markIndex) => {
        const r = document.createElement('div'); r.className='row';
        for(let i=0;i<len;i++){
          const ch = str[i]===' ' ? ' ' : toArabicDigits(str[i]);
          const d = document.createElement('span');
          d.className = 'digit' + (i===markIndex ? ' diff':'');
          d.textContent = ch;
          r.appendChild(d);
        }
        return r;
      };
      const wrap = document.createElement('div');
      wrap.className='stack';
      const r1 = row(pa, firstDiff);
      const r2 = row(pb, firstDiff);
      wrap.appendChild(r1);
      wrap.appendChild(document.createElement('hr'));
      wrap.appendChild(r2);
      vStack.innerHTML = '';
      vStack.appendChild(wrap);
    }

    function updateTop(){
      elQ.textContent = `${toArabicDigits(idx+1)} / ${toArabicDigits(TOTAL)}`;
      elScore.textContent = toArabicDigits(score);
      elBar.style.width = `${((idx)/TOTAL)*100}%`;
    }

    function next(){
      idx++;
      if(idx>=TOTAL){
        // إنهاء
        game.hidden = true;
        summary.hidden = false;
        finalScore.textContent = `${toArabicDigits(score)}`;
        const pct = Math.round((score/TOTAL)*100);
        let remark = '';
        if(pct===100) remark='امتياز! مقارنة دقيقة جدًا 👏';
        else if(pct>=85) remark='رائع! أداء قوي جدًا.';
        else if(pct>=70) remark='جيد جدًا! استمر.';
        else remark='عمل جيد، مع مزيد من التدريب ستتحسن النتائج.';
        finalRemark.textContent = `${toArabicDigits(pct)}٪ — ${remark}`;
        return;
      }
      // تحضير سؤال جديد
      pair = randomPair();
      locked=false;
      elFeedback.hidden = true;
      nextBtn.disabled = true;
      elA.textContent = toArabicDigits(pair[0]);
      elB.textContent = toArabicDigits(pair[1]);
      renderOptions(pair[0], pair[1]);
      updateTop();
    }

    function restart(){
      idx=0; score=0; locked=false; summary.hidden=true; game.hidden=false;
      elBar.style.width='0%';
      pair = randomPair();
      elA.textContent = toArabicDigits(pair[0]);
      elB.textContent = toArabicDigits(pair[1]);
      renderOptions(pair[0], pair[1]);
      updateTop();
      elFeedback.hidden = true;
      nextBtn.disabled = true;
    }

    nextBtn.addEventListener('click', next);

    // بدء اللعبة
    restart();
  </script>
</body>
</html>
