<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Ghana Expansion – Budget Dashboard</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.min.js"></script>
<style>
  *{box-sizing:border-box;margin:0;padding:0}
  body{font-family:'Georgia',serif;background:#f8fafc;color:#0f172a;min-height:100vh}
  /* Header */
  .header{background:linear-gradient(135deg,#f1f5f9,#e2e8f0);border-bottom:1px solid #e2e8f0;padding:28px 32px 20px}
  .header-top{display:flex;justify-content:space-between;align-items:flex-start;flex-wrap:wrap;gap:12px;margin-bottom:20px}
  .eyebrow{font-size:11px;letter-spacing:.2em;color:#d97706;text-transform:uppercase;margin-bottom:6px}
  .title{font-size:26px;font-weight:700;letter-spacing:-.5px}
  .subtitle{color:#64748b;font-size:13px;font-family:monospace;margin-top:6px}
  .header-right{text-align:right}
  .header-right .label{font-size:11px;color:#64748b;margin-bottom:4px}
  .header-right .big{font-size:24px;font-weight:700;color:#d97706}
  .header-right .small{font-size:13px;color:#059669}
  /* KPI Grid */
  .kpi-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:12px}
  .kpi-card{background:#fff;border-radius:10px;padding:14px 16px;border:1px solid #e2e8f0}
  .kpi-label{font-size:11px;color:#64748b;letter-spacing:.1em;margin-bottom:6px}
  .kpi-value{font-size:20px;font-weight:700}
  .kpi-sub{font-size:11px;color:#64748b;margin-top:4px}
  /* Tabs */
  .tabs{display:flex;gap:4px;padding:16px 32px 0;border-bottom:1px solid #e2e8f0;background:#fff}
  .tab{background:transparent;color:#64748b;border:none;border-radius:8px 8px 0 0;padding:8px 20px;cursor:pointer;font-size:13px;font-family:'Georgia',serif;transition:all .2s}
  .tab.active{background:#d97706;color:#000;font-weight:700}
  /* Content */
  .content{padding:24px 32px}
  /* Charts row */
  .charts-row{display:grid;grid-template-columns:1fr 1fr;gap:20px;margin-bottom:24px}
  .chart-card{background:#fff;border-radius:12px;padding:20px;border:1px solid #e2e8f0}
  .chart-title{font-size:13px;font-weight:600;margin-bottom:4px}
  .chart-sub{font-size:11px;color:#64748b;margin-bottom:16px}
  /* Summary Cards */
  .summary-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:16px;margin-bottom:20px}
  .summary-card{background:#fff;border-radius:12px;padding:20px;border:1px solid #e2e8f0}
  .summary-header{display:flex;justify-content:space-between;align-items:flex-start;margin-bottom:16px}
  .dot{width:10px;height:10px;border-radius:50%;display:inline-block;margin-right:8px}
  .cat-name{font-size:13px;font-weight:600}
  .badge{border-radius:6px;padding:2px 8px;font-size:11px;font-weight:700}
  .badge.pos{background:rgba(16,185,129,.15);color:#10b981}
  .badge.neg{background:rgba(239,68,68,.15);color:#ef4444}
  .row-line{display:flex;justify-content:space-between;margin-bottom:6px}
  .row-label{font-size:11px;color:#64748b}
  .row-val{font-size:13px;font-weight:700}
  .bar-track{height:6px;background:#e2e8f0;border-radius:3px;overflow:hidden;margin-top:12px}
  .bar-fill{height:100%;border-radius:3px;transition:width .6s ease}
  .util-text{font-size:11px;color:#64748b;margin-top:6px;text-align:right}
  /* Grand Total */
  .grand-total{background:linear-gradient(135deg,#f8fafc,#f1f5f9);border-radius:12px;padding:20px 24px;border:1px solid rgba(217,119,6,.25)}
  .gt-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:24px}
  .gt-label{font-size:11px;color:#64748b;margin-bottom:6px}
  .gt-val{font-size:22px;font-weight:700}
  .gt-note{font-size:11px;color:#64748b;margin-top:2px}
  /* Table */
  .table-card{background:#fff;border-radius:12px;border:1px solid #e2e8f0;overflow:hidden;margin-top:20px}
  .table-head{display:grid;grid-template-columns:2fr 1fr 1fr 1fr 1fr;padding:12px 20px;background:#f1f5f9;font-size:11px;color:#64748b;letter-spacing:.08em;text-transform:uppercase}
  .table-row{display:grid;grid-template-columns:2fr 1fr 1fr 1fr 1fr;padding:13px 20px;border-top:1px solid #e2e8f0;cursor:pointer;transition:background .15s}
  .table-row:nth-child(even){background:#f8fafc}
  .table-row:hover{background:#fef3c7}
  .cell{font-size:13px}
  .cell.mono{font-family:monospace}
  .cell.right{text-align:right}
  .cell.amber{color:#d97706}
  .cell.muted{color:#94a3b8}
  .cell.green{color:#10b981}
  .cell.red{color:#ef4444}
  .pending-badge{font-size:11px;color:#94a3b8;background:#f1f5f9;border-radius:5px;padding:2px 8px}
  .table-footer{display:grid;grid-template-columns:2fr 1fr 1fr 1fr 1fr;padding:14px 20px;border-top:2px solid rgba(217,119,6,.25);background:#f8fafc}
  .footer-label{font-size:13px;font-weight:700;color:#d97706}
  .footer-val{font-size:13px;font-weight:700;text-align:right;font-family:monospace}
  /* hidden */
  .tab-panel{display:none}
  .tab-panel.active{display:block}
  @media(max-width:768px){
    .kpi-grid,.charts-row,.summary-grid,.gt-grid{grid-template-columns:1fr 1fr}
    .table-head,.table-row,.table-footer{grid-template-columns:2fr 1fr 1fr}
    .table-head div:nth-child(n+4),.table-row div:nth-child(n+4),.table-footer div:nth-child(n+4){display:none}
  }
</style>
</head>
<body>

<!-- HEADER -->
<div class="header">
  <div class="header-top">
    <div>
      <div class="eyebrow">Ghana Expansion Project</div>
      <h1 class="title">Budget Utilization Dashboard</h1>
      <p class="subtitle">Capacity Expansion · All values in USD</p>
    </div>
    <div class="header-right">
      <div class="label">Total Budget vs Actual</div>
      <div class="big">$3.92M</div>
      <div class="small">Actual: $3.35M</div>
    </div>
  </div>
  <div class="kpi-grid">
    <div class="kpi-card" style="border-top:3px solid #d97706">
      <div class="kpi-label">Total Budget</div>
      <div class="kpi-value" style="color:#d97706">$3.92M</div>
      <div class="kpi-sub">Estimated</div>
    </div>
    <div class="kpi-card" style="border-top:3px solid #059669">
      <div class="kpi-label">Total Actual</div>
      <div class="kpi-value" style="color:#059669">$3.35M</div>
      <div class="kpi-sub">Incurred to Date</div>
    </div>
    <div class="kpi-card" style="border-top:3px solid #2563eb">
      <div class="kpi-label">Variance (Savings)</div>
      <div class="kpi-value" style="color:#2563eb">$572.6K</div>
      <div class="kpi-sub">+14.6% under budget</div>
    </div>
    <div class="kpi-card" style="border-top:3px solid #a78bfa">
      <div class="kpi-label">Utilization Rate</div>
      <div class="kpi-value" style="color:#a78bfa">85.4%</div>
      <div class="kpi-sub">Budget consumed</div>
    </div>
  </div>
</div>

<!-- TABS -->
<div class="tabs">
  <button class="tab active" onclick="switchTab('overview',this)">Overview</button>
  <button class="tab" onclick="switchTab('main',this)">Main Machinery</button>
  <button class="tab" onclick="switchTab('ancillary',this)">Ancillary Equipment</button>
  <button class="tab" onclick="switchTab('civil',this)">Civil Work</button>
</div>

<!-- CONTENT -->
<div class="content">

  <!-- OVERVIEW -->
  <div id="panel-overview" class="tab-panel active">
    <div class="charts-row">
      <div class="chart-card">
        <div class="chart-title">Budget vs Actual by Category</div>
        <div class="chart-sub">USD comparison</div>
        <canvas id="barChart" height="220"></canvas>
      </div>
      <div class="chart-card">
        <div class="chart-title">Budget Allocation</div>
        <div class="chart-sub">Share by category</div>
        <canvas id="pieChart" height="220"></canvas>
      </div>
    </div>

    <div class="summary-grid">
      <div class="summary-card">
        <div class="summary-header">
          <div><span class="dot" style="background:#d97706"></span><span class="cat-name">Main Machinery</span></div>
          <span class="badge pos">▲ 2.1%</span>
        </div>
        <div class="row-line"><span class="row-label">Budget</span><span class="row-val" style="color:#d97706">$2.77M</span></div>
        <div class="row-line"><span class="row-label">Actual</span><span class="row-val">$2.71M</span></div>
        <div class="row-line"><span class="row-label">Variance</span><span class="row-val" style="color:#10b981">+$58.1K</span></div>
        <div class="bar-track"><div class="bar-fill" style="width:49%;background:#10b981"></div></div>
        <div class="util-text">97.9% utilized</div>
      </div>
      <div class="summary-card">
        <div class="summary-header">
          <div><span class="dot" style="background:#059669"></span><span class="cat-name">Ancillary Equipment</span></div>
          <span class="badge pos">▲ 41.2%</span>
        </div>
        <div class="row-line"><span class="row-label">Budget</span><span class="row-val" style="color:#059669">$510.8K</span></div>
        <div class="row-line"><span class="row-label">Actual</span><span class="row-val">$300.3K</span></div>
        <div class="row-line"><span class="row-label">Variance</span><span class="row-val" style="color:#10b981">+$210.5K</span></div>
        <div class="bar-track"><div class="bar-fill" style="width:29%;background:#10b981"></div></div>
        <div class="util-text">58.8% utilized</div>
      </div>
      <div class="summary-card">
        <div class="summary-header">
          <div><span class="dot" style="background:#2563eb"></span><span class="cat-name">Civil Work</span></div>
          <span class="badge pos">▲ 19.8%</span>
        </div>
        <div class="row-line"><span class="row-label">Budget</span><span class="row-val" style="color:#2563eb">$422.6K</span></div>
        <div class="row-line"><span class="row-label">Actual</span><span class="row-val">$338.9K</span></div>
        <div class="row-line"><span class="row-label">Variance</span><span class="row-val" style="color:#10b981">+$83.6K</span></div>
        <div class="bar-track"><div class="bar-fill" style="width:40%;background:#10b981"></div></div>
        <div class="util-text">80.2% utilized</div>
      </div>
    </div>

    <div class="grand-total">
      <div class="gt-grid">
        <div><div class="gt-label">Estimated Total Cost</div><div class="gt-val" style="color:#d97706">$3.92M</div></div>
        <div><div class="gt-label">Actual Spend to Date</div><div class="gt-val" style="color:#059669">$3.35M</div></div>
        <div><div class="gt-label">Remaining Budget</div><div class="gt-val" style="color:#2563eb">$572.6K</div></div>
        <div><div class="gt-label">Unbudgeted Items</div><div class="gt-val" style="color:#ef4444">$109.1K</div><div class="gt-note">Transformer &amp; Electrical</div></div>
      </div>
    </div>
  </div>

  <!-- MAIN MACHINERY -->
  <div id="panel-main" class="tab-panel">
    <div class="chart-card" style="margin-bottom:20px">
      <div class="chart-title">Budget vs Actual — Main Machinery</div>
      <div class="chart-sub">USD comparison</div>
      <canvas id="mainChart" height="200"></canvas>
    </div>
    <div class="table-card" id="table-main"></div>
  </div>

  <!-- ANCILLARY -->
  <div id="panel-ancillary" class="tab-panel">
    <div class="chart-card" style="margin-bottom:20px">
      <div class="chart-title">Budget vs Actual — Ancillary Equipment</div>
      <div class="chart-sub">USD comparison</div>
      <canvas id="ancillaryChart" height="200"></canvas>
    </div>
    <div class="table-card" id="table-ancillary"></div>
  </div>

  <!-- CIVIL -->
  <div id="panel-civil" class="tab-panel">
    <div class="chart-card" style="margin-bottom:20px">
      <div class="chart-title">Budget vs Actual — Civil Work</div>
      <div class="chart-sub">USD comparison</div>
      <canvas id="civilChart" height="200"></canvas>
    </div>
    <div class="table-card" id="table-civil"></div>
  </div>

</div><!-- /content -->

<script>
const DATA = {
  main:[
    {item:"SD Plant Cost",budget:1805000,actual:1933000},
    {item:"On Edge Packing Machine",budget:435120,actual:274388},
    {item:"Pile Pack Machine",budget:125000,actual:137817},
    {item:"Dumping Station & Tanks",budget:231250,actual:298925},
    {item:"Palm Oil Storage",budget:116667,actual:0},
    {item:"Sugar Grinder",budget:29400,actual:36478},
    {item:"Biscuit Grinder",budget:25000,actual:28750},
  ],
  ancillary:[
    {item:"Carton Sealers",budget:12000,actual:7234},
    {item:"Lab Equipment",budget:100000,actual:0},
    {item:"Tables, Stools etc.",budget:5000,actual:0},
    {item:"Electrical Power",budget:33333,actual:116703},
    {item:"Compressed Air Supply",budget:2000,actual:46849},
    {item:"GAS Supply & Flow Meter",budget:15000,actual:8011},
    {item:"Fire Extinguishers",budget:2667,actual:0},
    {item:"Racking - Wrappers",budget:19680,actual:73700},
    {item:"Racking - FGS",budget:65000,actual:0},
    {item:"New PMS",budget:48710,actual:0},
    {item:"Pallets",budget:25000,actual:0},
    {item:"Forklifts (2→1)",budget:35000,actual:0},
    {item:"Electric Pallet Truck",budget:32200,actual:0},
    {item:"Crates 450nos",budget:5500,actual:0},
    {item:"Ventilation & Aircon",budget:109667,actual:47783},
  ],
  civil:[
    {item:"Relocation Office & Lab",budget:2667,actual:0},
    {item:"Removing Existing Plant",budget:10000,actual:0},
    {item:"Internal Partition Mod.",budget:6667,actual:0},
    {item:"DH Room",budget:16000,actual:0},
    {item:"Epoxy & Minor Repairs",budget:10000,actual:0},
    {item:"Minor Civil Work",budget:5000,actual:9242},
    {item:"PMS Internal Modification",budget:2500,actual:0},
    {item:"Lunch Room & Plumbing",budget:20444,actual:329685},
    {item:"New Wash Room",budget:5810,actual:0},
    {item:"Main Office Relocation",budget:54661,actual:0},
    {item:"Building Expansion RMS",budget:25824,actual:0},
    {item:"Dayou Office Relocation",budget:10760,actual:0},
    {item:"Civil - Wafer Stick Area",budget:28326,actual:0},
    {item:"New PMS Civil Works",budget:46833,actual:0},
    {item:"Warehouse Expansion",budget:144231,actual:0},
  ]
};

function fmt(v){
  const a=Math.abs(v);
  if(a>=1e6) return '$'+(v/1e6).toFixed(2)+'M';
  if(a>=1e3) return '$'+(v/1e3).toFixed(1)+'K';
  return '$'+v.toFixed(0);
}

// ── Charts ──────────────────────────────────────────────
const commonBarOpts = (labels) => ({
  responsive:true,
  plugins:{legend:{labels:{color:'#64748b',font:{family:'Georgia'}}}},
  scales:{
    x:{ticks:{color:'#64748b',maxRotation:35,font:{size:11}},grid:{display:false}},
    y:{ticks:{color:'#64748b',callback:fmt},grid:{color:'#f1f5f9'}}
  }
});

// Overview bar
new Chart(document.getElementById('barChart'),{
  type:'bar',
  data:{
    labels:['Main Machinery','Ancillary Equip.','Civil Work'],
    datasets:[
      {label:'Budget',data:[2767437,510757,422562],backgroundColor:'rgba(217,119,6,.8)',borderRadius:4},
      {label:'Actual',data:[2709358,300280,338927],backgroundColor:'rgba(5,150,105,.8)',borderRadius:4},
    ]
  },
  options:commonBarOpts()
});

// Overview pie
new Chart(document.getElementById('pieChart'),{
  type:'doughnut',
  data:{
    labels:['Main Machinery','Ancillary Equip.','Civil Work'],
    datasets:[{data:[2767437,510757,422562],backgroundColor:['#d97706','#059669','#2563eb'],hoverOffset:6}]
  },
  options:{
    responsive:true,
    plugins:{legend:{position:'bottom',labels:{color:'#64748b',font:{family:'Georgia',size:12}}}}
  }
});

// Detail charts
function makeDetailChart(id, rows){
  new Chart(document.getElementById(id),{
    type:'bar',
    data:{
      labels: rows.map(r=>r.item.length>16?r.item.slice(0,15)+'…':r.item),
      datasets:[
        {label:'Budget',data:rows.map(r=>r.budget),backgroundColor:'rgba(217,119,6,.8)',borderRadius:4},
        {label:'Actual',data:rows.map(r=>r.actual),backgroundColor:'rgba(5,150,105,.8)',borderRadius:4},
      ]
    },
    options: commonBarOpts()
  });
}
makeDetailChart('mainChart', DATA.main);
makeDetailChart('ancillaryChart', DATA.ancillary);
makeDetailChart('civilChart', DATA.civil);

// ── Tables ──────────────────────────────────────────────
function buildTable(containerId, rows){
  const container = document.getElementById(containerId);
  const totalB = rows.reduce((s,r)=>s+r.budget,0);
  const totalA = rows.reduce((s,r)=>s+r.actual,0);
  const totalV = totalB - totalA;

  let html = `<div class="table-head">
    <div>Item</div><div style="text-align:right">Budget (USD)</div>
    <div style="text-align:right">Actual (USD)</div>
    <div style="text-align:right">Variance</div>
    <div style="text-align:right">Status</div>
  </div>`;

  rows.forEach((r,i)=>{
    const v = r.budget - r.actual;
    const pct = r.budget>0 ? (v/r.budget*100) : 0;
    const pos = v>=0;
    const badge = r.actual===0
      ? `<span class="pending-badge">Pending</span>`
      : `<span class="badge ${pos?'pos':'neg'}">${pos?'▲':'▼'} ${Math.abs(pct).toFixed(1)}%</span>`;
    html+=`<div class="table-row">
      <div class="cell">${r.item}</div>
      <div class="cell mono right amber">${fmt(r.budget)}</div>
      <div class="cell mono right ${r.actual===0?'muted':''}">${r.actual===0?'–':fmt(r.actual)}</div>
      <div class="cell mono right ${pos?'green':'red'}">${pos?'+':''}${fmt(v)}</div>
      <div class="cell right">${badge}</div>
    </div>`;
  });

  html+=`<div class="table-footer">
    <div class="footer-label">Sub-Total</div>
    <div class="footer-val amber">${fmt(totalB)}</div>
    <div class="footer-val">${fmt(totalA)}</div>
    <div class="footer-val green">+${fmt(totalV)}</div>
    <div></div>
  </div>`;

  container.innerHTML = html;
}
buildTable('table-main', DATA.main);
buildTable('table-ancillary', DATA.ancillary);
buildTable('table-civil', DATA.civil);

// ── Tab switching ────────────────────────────────────────
function switchTab(id, btn){
  document.querySelectorAll('.tab-panel').forEach(p=>p.classList.remove('active'));
  document.querySelectorAll('.tab').forEach(t=>t.classList.remove('active'));
  document.getElementById('panel-'+id).classList.add('active');
  btn.classList.add('active');
}
</script>
</body>
</html>
