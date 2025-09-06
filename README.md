<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>لعبة مقارنة الأعداد – جدول المنازل (من اليمين لليسار)</title>
  <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;700&display=swap" rel="stylesheet">
  <style>
    :root{
      --bg1:#0ea5e9; --bg2:#8b5cf6;
      --card:#ffffff; --ink:#0f172a; --muted:#475569;
      --ok:#16a34a; --bad:#dc2626; --chip:#f1f5f9;
      --head:#0b2a4a; --grid:#e2e8f0; --highlight:#f3e8ff; --highlightBorder:#8b5cf6;
    }
    *{box-sizing:border-box}
    body{
      margin:0;min-height:100vh;padding:24px;
      font-family:"Cairo", system-ui,-apple-system,Segoe UI,Roboto,Arial,sans-serif;
      background: radial-gradient(1200px 600px at 10% 0%, var(--bg1), transparent 60%),
                  radial-gradient(1200px 600px at 90% 0%, var(--bg2), transparent 60%),
                  linear-gradient(180deg,#0b1020,#0b1326 40%,#0b1020);
      color:var(--ink);
    }
    .wrap{max-width:980px;margin:0 auto}
    .card{background:var(--card);border-radius:16px;box-shadow:0 20px 60px rgba(0,0,0,.25);overflow:hidden}
    .header{padding:20px 24px;background:linear-gradient(90deg,rgba(14,165,233,.15),rgba(139,92,246,.15));display:flex;flex-wrap:wrap;gap:16px;align-items:center;justify-content:space-between}
    .title{margin:0;font-weight:800;color:var(--head);font-size:clamp(20px,2.6vw,28px)}
    .subtitle{margin:0;color:var(--muted);font-size:14px}
    .kpis{display:flex;gap:12px;flex-wrap:wrap}
    .chip{background:var(--chip);border-radius:12px;padding:10px 14px;min-width:130px;display:flex;align-items:center;justify-content:space-between;font-weight:700}
    .bar{height:10px;background:#e2e8f0;border-radius:999px;overflow:hidden}
    .bar>span{display:block;height:100%;width:0;background:linear-gradient(90deg,#22c55e,#06b6d4,#8b5cf6)}
    .body{padding:24px}
    .question{display:flex;align-items:center;justify-content:center;gap:18px;flex-wrap:wrap;border:1px dashed #e2e8f0;border-radius:14px;padding:18px;background:#fbfdff}
    .num{font-size:clamp(28px,5vw,44px);font-weight:800;line-height:1;color:var(--head)}
    .sign{font-size:clamp(28px,6vw,56px);opacity:.25}
    .opts{display:flex;gap:12px;justify-content:center;margin-top:16px;flex-wrap:wrap}
    .btn{border:none;border-radius:12px;padding:12px 18px;font-weight:800;cursor:pointer;font-size:20px;background:#0ea5e9;color:white;transition:.15s transform,.15s filter}
    .btn:hover{transform:translateY(-1px);filter:brightness(1.05)}
    .btn:disabled{opacity:.5;cursor:not-allowed}
    .btn.alt{background:#64748b}
    .btn.ok{background:var(--ok)}
    .btn.bad{background:var(--bad)}
    .feedback{margin-top:20px;border:1px solid #e2e8f0;border-radius:14px;overflow:hidden}
    .fb-head{display:flex;gap:8px;align-items:center;justify-content:space-between;padding:12px 14px;background:#f8fafc}
    .fb-body{padding:16px;background:white}
    .small{font-size:12px;color:var(--muted)}

    /* جدول المنازل */
    .pv-wrap{overflow-x:auto}
    table.pv{border-collapse:separate;border-spacing:0;min-width:720px;width:100%;text-align:center}
    .pv th,.pv td{border:1px solid var(--grid);padding:10px;min-width:52px}
    .pv thead th{background:#f1f5f9;font-weight:800}
    .pv .digit{font-weight:800;font-size:22px;display:inline-flex;align-items:center;justify-content:center;min-width:28px;min-height:36px;border-radius:8px}
    .pv .diff{background:var(--highlight);outline:2px solid var(--highlightBorder)}
    .legend{display:flex;gap:12px;flex-wrap:wrap;margin-top:10px;color:var(--muted);font-size:14px}
    .dot{width:10px;height:10px;border-radius:999px;display:inline-block}
    .rowTag{font-weight:700;color:#334155}
    .next-zone{margin-top:18px;display:flex;justify-content:space-between;align-items:center;gap:10px}
    .sum{padding:28px;text-align:center}
    .sum h2{margin:0 0 10px}
  </style>
</head>
<body>
  <div class="wrap">
    <div class="card">
      <div class="header">
        <div>
          <h1 class="title">🧮 لعبة مقارنة الأعداد (جدول المنازل من اليمين لليسار)</h1>
          <p class="subtitle">اختر الإشارة الصحيحة بين عددين عشوائيين (٢٠ مسألة). تظهر تغذية راجعة بمحاذاة العددين داخل جدول المنازل. <strong>الآحاد في أقصى اليمين</strong> ثم العشرات فالمئات… حتى مئات الملايين يسارًا.</p>
        </div>
        <div class="kpis">
          <div class="chip"><span>السؤال</span><strong id="qIdx">١ / ٢٠</strong></div>
          <div class="chip"><span>النتيجة</span><strong id="score">٠</strong></div>
          <div class="chip" style="min-width:220px;">
            <div style="flex:1">
              <div class="small">شريط التقدّم</div>
              <div class="bar"><span id="bar"></span></div>
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
          </div>
          <div class="fb-body">
            <div class="pv-wrap" id="pvContainer"></div>
            <div class="legend">
              <span><i class="dot" style="background:#2563eb"></i>العدد الأوّل</span>
              <span><i class="dot" style="background:#f59e0b"></i>العدد الثاني</span>
              <span>المظلّل = أول منزلة يختلف فيها العددان</span>
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
    const MAX = 999_999_999; // ٩٩٩,٩٩٩,٩٩٩

    // تحويل الأرقام إلى أرقام عربية-هندية
    function toArabicDigits(x){ return String(x).replace(/\d/g, d => "٠١٢٣٤٥٦٧٨٩"[d]); }

    // خلط فيشر–ياتس
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
      if(Math.random() < 0.125){
        const v = MIN + Math.floor(Math.random()*(MAX-MIN+1));
        return [v,v];
      }
      let a = MIN + Math.floor(Math.random()*(MAX-MIN+1));
      let b = MIN + Math.floor(Math.random()*(MAX-MIN+1));
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
    const pvContainer = document.getElementById('pvContainer');
    const nextBtn = document.getElementById('nextBtn');
    const skipBtn = document.getElementById('skipBtn');
    const summary = document.getElementById('summary');
    const game = document.getElementById('game');
    const finalScore = document.getElementById('finalScore');
    const finalRemark = document.getElementById('finalRemark');

    skipBtn.addEventListener('click', ()=>{ if(!locked){ locked=true; showFeedback(null); nextBtn.disabled=false; } });

    function correctSign(a,b){ return a<b?'<':a>b?'>':'='; }

    function renderOptions(a,b){
      elOpts.innerHTML='';
      shuffle(['<','>','=']).forEach(s=>{
        const btn=document.createElement('button');
        btn.className='btn'; btn.textContent=s;
        btn.addEventListener('click', ()=>handleChoice(s,a,b,btn));
        elOpts.appendChild(btn);
      });
    }

    function handleChoice(choice,a,b,btn){
      if(locked) return;
      locked=true;
      const truth = correctSign(a,b);
      [...elOpts.children].forEach(bn=>{
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
      renderPlaceValueTable(a,b);
    }

    function explain(a,b,truth){
      const A = toArabicDigits(a), B = toArabicDigits(b);
      if(truth==='=') return `العدد ${A} يساوي ${B}.`;
      if(truth==='>') return `العدد ${A} أكبر من ${B}.`;
      return `العدد ${A} أصغر من ${B}.`;
    }

    // ====== جدول المنازل (آحاد على اليمين) ======
    const HIGH_TO_LOW = [
      'مئات الملايين','عشرات الملايين','الملايين',
      'مئات الألوف','عشرات الألوف','الألوف',
      'المئات','العشرات','الآحاد'
    ];

    function renderPlaceValueTable(a,b){
      const sa = String(a), sb = String(b);
      const len = Math.max(sa.length, sb.length);

      // عناوين المنازل من أعلى منزلة إلى الآحاد (بطول العدد الأكبر)
      const labels = HIGH_TO_LOW.slice(HIGH_TO_LOW.length - len); // يسار→يمين في الجدول، والآحاد آخر عمود

      // نحاذي الأعداد يسارًا ليكون index 0 أعلى منزلة و index len-1 الآحاد
      const pa = sa.padStart(len,' ');
      const pb = sb.padStart(len,' ');

      // أول منزلة مختلفة تُحسب من أعلى منزلة إلى الآحاد
      let firstDiff = -1;
      for(let i=0;i<len;i++){
        if(pa[i]!==pb[i]){ firstDiff = i; break; }
      }

      // نبني الرؤوس والصفوف بنفس ترتيب labels (أعلى منزلة ← الآحاد)
      const head = labels.map(l => `<th>${l}</th>`).join('');

      const aRow = Array.from({length: len}, (_, i) => {
        const ch = pa[i] === ' ' ? '' : toArabicDigits(pa[i]);
        const mark = (i === firstDiff) ? ' diff' : '';
        return `<td><span class="digit${mark}" style="color:#2563eb">${ch}</span></td>`;
      }).join('');

      const bRow = Array.from({length: len}, (_, i) => {
        const ch = pb[i] === ' ' ? '' : toArabicDigits(pb[i]);
        const mark = (i === firstDiff) ? ' diff' : '';
        return `<td><span class="digit${mark}" style="color:#f59e0b">${ch}</span></td>`;
      }).join('');

      pvContainer.innerHTML = `
        <table class="pv" aria-label="جدول منازل يوضّح محاذاة العددين (آحاد يمينًا)">
          <thead><tr>${head}</tr></thead>
          <tbody>
            <tr><td class="rowTag" colspan="${len}" style="text-align:right;border:none;padding:6px 0 4px 0">العدد الأوّل</td></tr>
            <tr>${aRow}</tr>
            <tr><td class="rowTag" colspan="${len}" style="text-align:right;border:none;padding:10px 0 4px 0">العدد الثاني</td></tr>
            <tr>${bRow}</tr>
          </tbody>
        </table>
      `;
    }

    function updateTop(){
      elQ.textContent = `${toArabicDigits(idx+1)} / ${toArabicDigits(TOTAL)}`;
      elScore.textContent = toArabicDigits(score);
      elBar.style.width = `${((idx)/TOTAL)*100}%`;
    }

    function next(){
      idx++;
      if(idx>=TOTAL){
        game.hidden = true;
        summary.hidden = false;
        finalScore.textContent = `${toArabicDigits(score)}`;
        const pct = Math.round((score/TOTAL)*100);
        let remark = '';
        if(pct===100) remark='امتياز! مقارنة دقيقة جدًا 👏';
        else if(pct>=85) remark='رائع! أداء قوي جدًا.';
        else if(pct>=70) remark='جيد جدًا! استمر.';
        else remark='عمل جيّد، ومع مزيد من التدريب ستتحسّن النتائج.';
        finalRemark.textContent = `${toArabicDigits(pct)}٪ — ${remark}`;
        return;
      }
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

    document.getElementById('nextBtn').addEventListener('click', next);
    restart();
  </script>
</body>
</html>
