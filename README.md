<script>
const placeLabels = [
  'مئات الملايين','عشرات الملايين','الملايين',
  'مئات الألوف','عشرات الألوف','الألوف',
  'المئات','العشرات','الآحاد'
];

// توليد جدول المنازل من اليسار (أكبر منزلة) إلى اليمين (الآحاد)
// الآحاد تبقى في أقصى اليمين لأننا لا نعكس الصفوف ولا الرؤوس.
function renderTable(a,b){
  const sa = String(a), sb = String(b);
  const len = Math.max(sa.length, sb.length);

  // نأخذ فقط العناوين المطابقة لطول العدد الأكبر، مع ترتيبها من الأعلى إلى الآحاد
  const labels = placeLabels.slice(placeLabels.length - len); // [.., المئات, العشرات, الآحاد]

  // نملأ يسارًا حتى يتساوى الطولان، بحيث يكون index 0 هو أعلى منزلة
  const pa = sa.padStart(len, ' ');
  const pb = sb.padStart(len, ' ');

  // أول منزلة مختلفة تُحسب بالمسار الطبيعي من أعلى منزلة إلى الآحاد
  let firstDiff = -1;
  for (let i = 0; i < len; i++) {
    if (pa[i] !== pb[i]) { firstDiff = i; break; }
  }

  // رؤوس الأعمدة من الأعلى -> الآحاد (لا نعكس الترتيب)
  const head = labels.map(l => `<th>${l}</th>`).join('');

  // صف العدد الأول: الخلية i تقابل الحرف pa[i] (أعلى منزلة يسارًا)
  const aRow = Array.from({length: len}, (_, i) => {
    const ch = pa[i] === ' ' ? '' : "٠١٢٣٤٥٦٧٨٩"[pa[i]];
    const mark = (i === firstDiff) ? ' diff' : '';
    return `<td><span class="digit${mark}" style="color:#2563eb">${ch}</span></td>`;
  }).join('');

  // صف العدد الثاني: بنفس الترتيب
  const bRow = Array.from({length: len}, (_, i) => {
    const ch = pb[i] === ' ' ? '' : "٠١٢٣٤٥٦٧٨٩"[pb[i]];
    const mark = (i === firstDiff) ? ' diff' : '';
    return `<td><span class="digit${mark}" style="color:#f59e0b">${ch}</span></td>`;
  }).join('');

  // تجميع الجدول (من دون أي عكس للسلاسل)
  pvContainer.innerHTML = `
    <table style="border-collapse:collapse;width:100%;text-align:center;direction:rtl">
      <thead><tr>${head}</tr></thead>
      <tbody>
        <tr class="rowTag"><td colspan="${len}" style="text-align:right;border:none;padding:6px 0 4px 0">العدد الأول</td></tr>
        <tr>${aRow}</tr>
        <tr class="rowTag"><td colspan="${len}" style="text-align:right;border:none;padding:10px 0 4px 0">العدد الثاني</td></tr>
        <tr>${bRow}</tr>
      </tbody>
    </table>`;
}
</script>
