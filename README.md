<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>لعبة مقارنة الأعداد – جدول المنازل</title>
  <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;700&display=swap" rel="stylesheet">
  <style>
    body{margin:0;font-family:"Cairo",sans-serif;background:#f1f5f9;min-height:100vh;padding:24px}
    .wrap{max-width:900px;margin:auto;background:white;padding:20px;border-radius:12px;box-shadow:0 10px 25px rgba(0,0,0,.1)}
    .question{display:flex;justify-content:center;align-items:center;gap:16px;font-size:32px;font-weight:700;margin-bottom:20px}
    .opts{display:flex;gap:12px;justify-content:center;margin-bottom:20px}
    .btn{padding:10px 16px;font-size:20px;font-weight:700;border:none;border-radius:8px;cursor:pointer;background:#0ea5e9;color:white}
    .btn.ok{background:#16a34a}.btn.bad{background:#dc2626}.btn.alt{background:#64748b}
    .feedback{margin-top:20px}
    table{border-collapse:collapse;width:100%;direction:rtl;text-align:center}
    th,td{border:1px solid #e2e8f0;padding:8px;min-width:40px}
    th{background:#f8fafc}
    .digit{font-size:20px;font-weight:700;display:inline-block;min-width:24px}
    .diff{background:#f3e8ff;outline:2px solid #8b5cf6}
    .rowTag{font-weight:700;text-align:right;padding:6px 0;color:#334155}
  </style>
</head>
<body>
  <div class="wrap">
    <div class="question"><span id="numA">٠</span><span>؟</span><span id="numB">٠</span></div>
    <div class="opts" id="options"></div>
    <div class="feedback" id="feedback" hidden>
      <p id="resultMsg"></p>
      <div id="pvContainer"></div>
    </div>
    <button class="btn" id="nextBtn" disabled>التالي ⟵</button>
  </div>

<script>
const TOTAL=20,MIN=1000,MAX=999_999_999;
function toArabicDigits(x){return String(x).replace(/\d/g,d=>"٠١٢٣٤٥٦٧٨٩"[d]);}
function shuffle(a){for(let i=a.length-1;i>0;i--){const j=Math.floor(Math.random()*(i+1));[a[i],a[j]]=[a[j],a[i]];}return a;}
function randomPair(){if(Math.random()<.125){let v=MIN+Math.floor(Math.random()*(MAX-MIN+1));return[v,v];}
let a=MIN+Math.floor(Math.random()*(MAX-MIN+1)),b=MIN+Math.floor(Math.random()*(MAX-MIN+1));
if(a===b)b++;return[a,b];}
function correctSign(a,b){return a<b?'<':a>b?'>':'=';}

let idx=0,score=0,pair=null,locked=false;
const elA=document.getElementById('numA'),elB=document.getElementById('numB'),
elOpts=document.getElementById('options'),elFb=document.getElementById('feedback'),
elMsg=document.getElementById('resultMsg'),pvContainer=document.getElementById('pvContainer'),
nextBtn=document.getElementById('nextBtn');

function renderOptions(a,b){
  elOpts.innerHTML='';
  shuffle(['<','>','=']).forEach(s=>{
    let btn=document.createElement('button');btn.className='btn';btn.textContent=s;
    btn.onclick=()=>handleChoice(s,a,b,btn);elOpts.appendChild(btn);
  });
}
function handleChoice(choice,a,b,btn){
  if(locked)return;locked=true;
  const truth=correctSign(a,b);
  [...elOpts.children].forEach(bn=>{
    bn.disabled=true;
    if(bn.textContent===truth)bn.classList.add('ok');
    else if(bn===btn)bn.classList.add('bad');
    else bn.classList.add('alt');
  });
  const isRight=(choice===truth);if(isRight)score++;
  showFeedback(isRight,a,b,truth);nextBtn.disabled=false;
}
function showFeedback(ok,a,b,truth){
  elFb.hidden=false;
  elMsg.textContent=ok?`إجابة صحيحة ✅ (${explain(a,b,truth)})`:`إجابة غير صحيحة ❌ (${explain(a,b,truth)})`;
  renderTable(a,b);
}
function explain(a,b,truth){
  const A=toArabicDigits(a),B=toArabicDigits(b);
  if(truth==='=')return`${A} = ${B}`;
  if(truth==='>')return`${A} > ${B}`;
  return`${A} < ${B}`;
}

const placeLabels=['مئات الملايين','عشرات الملايين','الملايين','مئات الألوف','عشرات الألوف','الألوف','المئات','العشرات','الآحاد'];

function renderTable(a,b){
  const sa=String(a),sb=String(b);
  const len=Math.max(sa.length,sb.length);
  const pa=sa.padStart(len,' '),pb=sb.padStart(len,' ');
  // أول منزلة مختلفة من اليسار
  let firstDiff=-1;for(let i=0;i<len;i++){if(pa[i]!==pb[i]){firstDiff=i;break;}}
  // بناء الخلايا من اليمين (آحاد أقصى اليمين)
  let head='',rowA='',rowB='';
  for(let i=0;i<len;i++){
    const pos=len-1-i; // 0=آحاد, 1=عشرات ...
    const label=placeLabels[i+placeLabels.length-len];
    head=`<th>${label}</th>`+head;
    const ca=pa[pos]===' ' ? '' : toArabicDigits(pa[pos]);
    const cb=pb[pos]===' ' ? '' : toArabicDigits(pb[pos]);
    const mark=(pos===firstDiff)?' diff':'';
    rowA=`<td><span class="digit${mark}" style="color:#2563eb">${ca}</span></td>`+rowA;
    rowB=`<td><span class="digit${mark}" style="color:#f59e0b">${cb}</span></td>`+rowB;
  }
  pvContainer.innerHTML=`
    <table>
      <thead><tr>${head}</tr></thead>
      <tbody>
        <tr class="rowTag"><td colspan="${len}">العدد الأول</td></tr>
        <tr>${rowA}</tr>
        <tr class="rowTag"><td colspan="${len}">العدد الثاني</td></tr>
        <tr>${rowB}</tr>
      </tbody>
    </table>`;
}

function next(){
  idx++;if(idx>=TOTAL){elMsg.textContent=`انتهت اللعبة! نتيجتك ${toArabicDigits(score)} من ${TOTAL}`;nextBtn.disabled=true;return;}
  pair=randomPair();locked=false;elFb.hidden=true;nextBtn.disabled=true;
  elA.textContent=toArabicDigits(pair[0]);elB.textContent=toArabicDigits(pair[1]);renderOptions(pair[0],pair[1]);
}
function restart(){idx=0;score=0;next();}
nextBtn.onclick=next;restart();
</script>
</body>
</html>
