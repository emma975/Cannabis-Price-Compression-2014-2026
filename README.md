<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Cannabis Price Compression Dashboard</title>
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  :root {
    --bg: #ffffff; --bg2: #f5f4f0; --bg3: #eeede8;
    --text: #1a1a18; --text2: #5f5e5a; --text3: #888780;
    --border: rgba(0,0,0,0.12); --border2: rgba(0,0,0,0.2);
    --radius: 8px; --radius-lg: 12px;
  }
  @media (prefers-color-scheme: dark) {
    :root {
      --bg: #1c1c1a; --bg2: #252523; --bg3: #2e2e2b;
      --text: #e8e6de; --text2: #9c9a92; --text3: #666660;
      --border: rgba(255,255,255,0.1); --border2: rgba(255,255,255,0.18);
    }
  }
  body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif; background: var(--bg); color: var(--text); padding: 24px; max-width: 860px; margin: 0 auto; }
  h1 { font-size: 18px; font-weight: 500; margin-bottom: 4px; }
  .subtitle { font-size: 13px; color: var(--text2); margin-bottom: 20px; }
  .mgrid { display: grid; grid-template-columns: repeat(4, minmax(0,1fr)); gap: 10px; margin-bottom: 18px; }
  .mc { background: var(--bg2); border-radius: var(--radius); padding: 12px; }
  .ml { font-size: 11px; color: var(--text2); margin-bottom: 3px; }
  .mv { font-size: 20px; font-weight: 500; line-height: 1.2; }
  .ms { font-size: 11px; color: var(--text3); margin-top: 2px; }
  .tabs { display: flex; gap: 4px; margin-bottom: 14px; }
  .tab { padding: 5px 13px; font-size: 13px; border-radius: var(--radius); border: 0.5px solid var(--border2); cursor: pointer; background: transparent; color: var(--text2); transition: all .15s; }
  .tab.active { background: var(--bg2); color: var(--text); font-weight: 500; }
  .panel { display: none; }
  .panel.active { display: block; }
  .lrow { display: flex; flex-wrap: wrap; gap: 7px; margin-bottom: 10px; }
  .li { display: flex; align-items: center; gap: 5px; font-size: 11px; color: var(--text2); padding: 3px 8px; border-radius: 4px; border: 0.5px solid var(--border); cursor: pointer; user-select: none; transition: opacity .15s; }
  .li.off { opacity: .3; }
  .ld { width: 10px; height: 10px; border-radius: 2px; flex-shrink: 0; }
  .note { font-size: 11px; color: var(--text3); margin-top: 10px; line-height: 1.5; }
  .scleg { display: flex; flex-wrap: wrap; gap: 14px; margin-top: 12px; }
  .sli { display: flex; align-items: center; gap: 6px; font-size: 11px; color: var(--text2); }
  canvas { display: block; }
</style>
</head>
<body>
<h1>Cannabis price compression — 8 markets (2014–2026)</h1>
<p class="subtitle">Avg retail flower $/gram · NY vs. national benchmarks and Northeast regional markets · MFNY PEST Economic Analysis</p>

<div class="mgrid">
  <div class="mc"><div class="ml">NY flower $/gram</div><div class="mv">$14.38</div><div class="ms">−17% from 2024 peak</div></div>
  <div class="mc"><div class="ml">NE regional floor (MA)</div><div class="mv">$4.01</div><div class="ms">Year 7 · 3.6× below NY</div></div>
  <div class="mc"><div class="ml">NY illicit market share</div><div class="mv">~80%</div><div class="ms">vs. 60% CA · 30% mature MI</div></div>
  <div class="mc"><div class="ml">Years to mature floor</div><div class="mv">5–8 yrs</div><div class="ms">IL model vs MA/CT model</div></div>
</div>

<div class="tabs">
  <button class="tab active" onclick="switchTab('traj',this)">Price trajectories</button>
  <button class="tab" onclick="switchTab('snap',this)">2026 snapshot</button>
  <button class="tab" onclick="switchTab('out',this)">NY outlook</button>
</div>

<div class="panel active" id="p-traj">
  <div class="lrow" id="lrow"></div>
  <div style="position:relative;width:100%;height:340px"><canvas id="cTraj"></canvas></div>
  <p class="note">Avg retail flower $/gram since market launch. Gaps = no data for that year. Click legend items to toggle states. Sources: state regulators, MJBizDaily, Headset, Cannabis Business Times (2014–2026).</p>
</div>

<div class="panel" id="p-snap">
  <div style="position:relative;width:100%;height:420px"><canvas id="cSnap"></canvas></div>
  <p class="note">Q1 2026 avg retail flower $/gram, ranked cheapest to most expensive. NY commands a 3.6× premium over MA's regional floor. Sources: state agencies, Headset, NJ-CRC, MA CCC, CT DCP.</p>
</div>

<div class="panel" id="p-out">
  <div style="position:relative;width:100%;height:300px"><canvas id="cOut"></canvas></div>
  <div class="scleg">
    <div class="sli"><span style="width:22px;height:3px;background:#7F77DD;display:inline-block;border-radius:1px"></span>NY actual</div>
    <div class="sli"><span style="width:22px;height:2px;background:#888780;display:inline-block;border-radius:1px;opacity:.6"></span>Conservative (IL model −10%/yr)</div>
    <div class="sli"><span style="width:22px;height:2px;background:#EF9F27;display:inline-block;border-radius:1px"></span>Base case (CT model −20%/yr)</div>
    <div class="sli"><span style="width:22px;height:2px;background:#E24B4A;display:inline-block;border-radius:1px"></span>Aggressive (MI model −35%/yr)</div>
    <div class="sli"><span style="width:22px;height:1px;background:#378ADD;display:inline-block;border-radius:1px;opacity:.7"></span>MA floor reference ($4.01)</div>
  </div>
  <p class="note">Scenarios diverge from 2026 onward. Based on cross-market maturation patterns — not a forecast. NY actual data: OCM/Headset.</p>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
<script>
const dk = matchMedia('(prefers-color-scheme: dark)').matches;
const gc = dk ? 'rgba(255,255,255,0.07)' : 'rgba(0,0,0,0.07)';
const tc = dk ? '#888780' : '#73726c';

const ST = [
  {l:'Colorado',      c:'#1D9E75', w:2,   ys:[2014,2015,2017,2019,2021,2023,2025], ps:[12.3,10.7,5.9,4.9,5.5,3.5,3.5]},
  {l:'Michigan',      c:'#E24B4A', w:2,   ys:[2019,2020,2021,2022,2023,2024,2025], ps:[18.4,15.0,8.1,4.6,3.3,2.9,2.1]},
  {l:'California',    c:'#EF9F27', w:2,   ys:[2018,2020,2021,2022,2023,2024,2025], ps:[10.0,10.1,8.5,6.0,4.5,4.3,2.2]},
  {l:'Illinois',      c:'#7F77DD', w:2,   ys:[2020,2021,2022,2023,2024,2025],      ps:[16.2,13.5,11.0,9.5,9.2,5.7]},
  {l:'Massachusetts', c:'#378ADD', w:2,   ys:[2018,2020,2021,2022,2023,2024,2025], ps:[14.1,12.5,14.0,10.3,6.1,5.4,4.0]},
  {l:'New Jersey',    c:'#D85A30', w:2,   ys:[2022,2023,2024,2025],                ps:[14.3,13.0,11.0,8.1]},
  {l:'Connecticut',   c:'#639922', w:2,   ys:[2023,2024,2025],                     ps:[12.0,12.5,8.7]},
  {l:'New York',      c:'#C44D00', w:3.5, ys:[2023,2024,2025,2026],               ps:[18.0,16.0,14.4,14.4]},
];

const yrs = [];
for(let y=2014;y<=2026;y++) yrs.push(y);

const tCtx = document.getElementById('cTraj').getContext('2d');
const tChart = new Chart(tCtx, {
  type:'line',
  data:{
    labels:yrs,
    datasets:ST.map(s=>({
      label:s.l,
      data:yrs.map(y=>{ const i=s.ys.indexOf(y); return i>=0?s.ps[i]:null; }),
      borderColor:s.c,
      backgroundColor:s.c+'18',
      borderWidth:s.w,
      pointRadius:3,
      pointHoverRadius:6,
      spanGaps:true,
      tension:0.35,
    }))
  },
  options:{
    responsive:true, maintainAspectRatio:false,
    plugins:{legend:{display:false},tooltip:{callbacks:{label:ctx=>` ${ctx.dataset.label}: $${Number(ctx.parsed.y).toFixed(2)}/g`}}},
    scales:{
      x:{grid:{color:gc},ticks:{color:tc,font:{size:11}}},
      y:{grid:{color:gc},ticks:{color:tc,font:{size:11},callback:v=>'$'+v},title:{display:true,text:'$/gram (flower)',color:tc,font:{size:11}},min:0}
    }
  }
});

const lrow = document.getElementById('lrow');
ST.forEach((s,i)=>{
  const el = document.createElement('div');
  el.className = 'li';
  el.innerHTML = `<span class="ld" style="background:${s.c}"></span>${s.l}`;
  el.onclick = () => {
    const ds = tChart.data.datasets[i];
    ds.hidden = !ds.hidden;
    el.classList.toggle('off', !!ds.hidden);
    tChart.update();
  };
  lrow.appendChild(el);
});

const snapData = [
  {l:'Michigan',      f:2.14,  c:'#E24B4A'},
  {l:'California',    f:2.22,  c:'#EF9F27'},
  {l:'Colorado',      f:3.50,  c:'#1D9E75'},
  {l:'Massachusetts', f:4.01,  c:'#378ADD'},
  {l:'Illinois',      f:5.72,  c:'#7F77DD'},
  {l:'Connecticut',   f:7.94,  c:'#639922'},
  {l:'New Jersey',    f:10.36, c:'#D85A30'},
  {l:'New York',      f:14.38, c:'#C44D00'},
];
const sCtx = document.getElementById('cSnap').getContext('2d');
new Chart(sCtx, {
  type:'bar',
  data:{
    labels:snapData.map(s=>s.l),
    datasets:[{
      label:'Flower $/gram',
      data:snapData.map(s=>s.f),
      backgroundColor:snapData.map(s=>s.c+'BB'),
      borderColor:snapData.map(s=>s.c),
      borderWidth:1,
      borderRadius:3,
    }]
  },
  options:{
    indexAxis:'y',
    responsive:true, maintainAspectRatio:false,
    plugins:{legend:{display:false},tooltip:{callbacks:{label:ctx=>` $${Number(ctx.parsed.x).toFixed(2)}/gram`}}},
    scales:{
      x:{grid:{color:gc},ticks:{color:tc,font:{size:11},callback:v=>'$'+v},title:{display:true,text:'Avg retail flower $/gram (Q1 2026)',color:tc,font:{size:11}}},
      y:{grid:{display:false},ticks:{color:tc,font:{size:12},autoSkip:false}}
    }
  }
});

const oYrs = [2023,2024,2025,2026,2027,2028,2029,2030];
const oCtx = document.getElementById('cOut').getContext('2d');
new Chart(oCtx, {
  type:'line',
  data:{
    labels:oYrs,
    datasets:[
      {label:'NY actual',    data:[18.0,16.0,14.4,14.4,null,null,null,null],  borderColor:'#7F77DD',borderWidth:3.5,pointRadius:4,spanGaps:false,tension:0.3,backgroundColor:'#7F77DD22'},
      {label:'Conservative', data:[null,null,null,14.4,13.0,11.7,10.5,9.5],  borderColor:'#888780',borderWidth:1.5,borderDash:[7,4],pointRadius:3,spanGaps:false,tension:0.3,backgroundColor:'transparent'},
      {label:'Base case',    data:[null,null,null,14.4,11.5,9.2,7.4,5.9],    borderColor:'#EF9F27',borderWidth:2,pointRadius:3,spanGaps:false,tension:0.3,backgroundColor:'#EF9F2712'},
      {label:'Aggressive',   data:[null,null,null,14.4,9.4,6.1,4.0,2.6],     borderColor:'#E24B4A',borderWidth:2,pointRadius:3,spanGaps:false,tension:0.3,backgroundColor:'#E24B4A0E'},
      {label:'MA floor',     data:[null,null,null,4.01,4.01,4.01,4.01,4.01], borderColor:'#378ADD',borderWidth:1,borderDash:[3,3],pointRadius:0,spanGaps:true,tension:0,backgroundColor:'transparent'},
    ]
  },
  options:{
    responsive:true, maintainAspectRatio:false,
    plugins:{legend:{display:false},tooltip:{callbacks:{label:ctx=>ctx.parsed.y!=null?` ${ctx.dataset.label}: $${Number(ctx.parsed.y).toFixed(2)}/g`:''}}},
    scales:{
      x:{grid:{color:gc},ticks:{color:tc,font:{size:11}}},
      y:{grid:{color:gc},ticks:{color:tc,font:{size:11},callback:v=>'$'+v},title:{display:true,text:'NY flower $/gram',color:tc,font:{size:11}},min:0,max:20}
    }
  }
});

function switchTab(id, btn) {
  document.querySelectorAll('.panel').forEach(p=>p.classList.remove('active'));
  document.querySelectorAll('.tab').forEach(b=>b.classList.remove('active'));
  document.getElementById('p-'+id).classList.add('active');
  btn.classList.add('active');
}
</script>
</body>
</html>
