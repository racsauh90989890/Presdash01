<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Dashboard Intervenciones 2026 — CENGOB</title>
<link href="https://fonts.googleapis.com/css2?family=Barlow+Condensed:wght@300;400;600;700;800;900&family=Barlow:wght@300;400;500;600&display=swap" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
<style>
  :root {
    --verde:       #0A3D2B;
    --verde-mid:   #155a3e;
    --verde-light: #1e7a52;
    --dorado:      #C8A050;
    --dorado-light:#e0bc76;
    --dorado-dim:  #96762a;
    --crema:       #F5EFE0;
    --blanco:      #FFFFFF;
    --gris-bg:     #F0EDE6;
    --gris-borde:  #D8D2C4;
    --sombra:      0 4px 24px rgba(10,61,43,0.13);
    --sombra-kpi:  0 8px 32px rgba(10,61,43,0.18);
  }
  *, *::before, *::after { box-sizing:border-box; margin:0; padding:0; }
  html, body {
    width:100%; height:100%; overflow:hidden;
    background:var(--gris-bg);
    font-family:'Barlow',sans-serif;
  }

  /* ── LAYOUT ───────────────────────────── */
  .dashboard {
    display:grid;
    width:100vw; height:100vh;
    grid-template-rows:58px 1fr;
  }

  /* ── HEADER ───────────────────────────── */
  .header {
    background:var(--verde);
    display:flex; align-items:center; justify-content:space-between;
    padding:0 20px;
    border-bottom:3px solid var(--dorado);
    box-shadow:0 2px 16px rgba(10,61,43,0.3);
    z-index:10;
  }
  .header-left { display:flex; align-items:center; gap:14px; }
  .header-logo img { height:38px; display:block; }
  .header-divider { width:1px; height:30px; background:rgba(200,160,80,0.4); }
  .header-title {
    font-family:'Barlow Condensed',sans-serif;
    font-weight:700; font-size:20px; color:var(--blanco); letter-spacing:0.3px;
  }
  .header-title span { color:var(--dorado); }
  .header-right {
    font-family:'Barlow Condensed',sans-serif;
    font-size:12px; color:rgba(255,255,255,0.55);
    letter-spacing:1px; text-transform:uppercase;
  }

  /* ── BODY: 4 cols, 2 rows ─────────────── */
  /*
    Col 1 (210px) : KPIs — span 2 rows
    Col 2 (1fr)   : Torta cantidad (fila 1) | Rangos (fila 2)
    Col 3 (1.4fr) : Barras monto (fila 1)   | Cronograma (fila 2)
  */
  .body {
    display:grid;
    grid-template-columns: 210px 1fr 1.35fr;
    grid-template-rows: 50% 50%;
    gap:10px;
    padding:10px 14px;
    overflow:hidden;
  }

  /* ── KPIs ─────────────────────────────── */
  .col-kpis {
    grid-column:1; grid-row:1/3;
    display:flex; flex-direction:column; gap:9px;
  }
  .kpi-card {
    background:var(--verde); border-radius:8px;
    padding:14px 13px 12px; box-shadow:var(--sombra-kpi);
    position:relative; overflow:hidden;
    flex:1; display:flex; flex-direction:column; justify-content:center;
    animation:fadeUp .5s ease both;
  }
  .kpi-card::before {
    content:''; position:absolute; bottom:-18px; right:-18px;
    width:80px; height:80px; border-radius:50%;
    background:rgba(200,160,80,0.10);
  }
  .kpi-card:nth-child(1){ animation-delay:.05s }
  .kpi-card:nth-child(2){ animation-delay:.10s }
  .kpi-accent {
    display:inline-block; width:24px; height:3px;
    background:var(--dorado); border-radius:2px; margin-bottom:7px;
  }
  .kpi-label {
    font-family:'Barlow Condensed',sans-serif;
    font-size:10px; font-weight:600; letter-spacing:1.5px;
    text-transform:uppercase; color:var(--dorado-light); margin-bottom:5px;
  }
  .kpi-value {
    font-family:'Barlow Condensed',sans-serif;
    font-weight:900; font-size:52px; line-height:1;
    color:var(--blanco); letter-spacing:-1px;
  }
  .kpi-value.small { font-size:36px; }
  .kpi-unit {
    font-size:11px; color:rgba(255,255,255,0.52);
    margin-top:4px; line-height:1.3;
  }

  /* KPI tipo intervención */
  .kpi-tipo {
    background:var(--verde); border-radius:8px;
    padding:13px 13px; box-shadow:var(--sombra-kpi);
    flex:1.8; display:flex; flex-direction:column;
    animation:fadeUp .5s ease both; animation-delay:.15s;
  }
  .kpi-tipo-accent {
    display:inline-block; width:24px; height:3px;
    background:var(--dorado); border-radius:2px; margin-bottom:7px;
  }
  .kpi-tipo-title {
    font-family:'Barlow Condensed',sans-serif;
    font-size:10px; font-weight:600; letter-spacing:1.5px;
    text-transform:uppercase; color:var(--dorado-light); margin-bottom:10px;
  }
  .tipo-row {
    display:flex; align-items:center; justify-content:space-between;
    padding:5px 0; border-bottom:1px solid rgba(255,255,255,0.07);
  }
  .tipo-row:last-child { border-bottom:none; }
  .tipo-nombre { font-size:11px; color:rgba(255,255,255,0.78); flex:1; padding-right:6px; }
  .tipo-badge {
    font-family:'Barlow Condensed',sans-serif;
    font-weight:700; font-size:15px; color:var(--dorado-light);
    min-width:28px; text-align:right;
  }
  .tipo-bar-wrap {
    width:38px; height:4px; background:rgba(255,255,255,0.1);
    border-radius:2px; margin-left:8px; overflow:hidden;
  }
  .tipo-bar-fill { height:100%; border-radius:2px; background:var(--dorado); }

  /* ── TARJETAS ─────────────────────────── */
  .card {
    background:var(--blanco); border-radius:8px;
    box-shadow:var(--sombra); display:flex; flex-direction:column;
    overflow:hidden; animation:fadeUp .5s ease both;
  }
  .card-header {
    background:var(--verde); padding:7px 13px;
    display:flex; align-items:center; gap:8px; flex-shrink:0;
  }
  .card-header-dot { width:7px; height:7px; border-radius:50%; background:var(--dorado); flex-shrink:0; }
  .card-title {
    font-family:'Barlow Condensed',sans-serif;
    font-weight:700; font-size:11.5px; letter-spacing:1px;
    text-transform:uppercase; color:var(--blanco);
  }
  .card-body {
    flex:1; padding:8px;
    display:flex; align-items:center; justify-content:center;
    min-height:0;
  }
  .card-body canvas { max-height:100%; max-width:100%; }

  /* posiciones */
  .col-torta-cant  { grid-column:2; grid-row:1; animation-delay:.10s }
  .col-barras-monto{ grid-column:3; grid-row:1; animation-delay:.15s }
  .col-rangos      { grid-column:2; grid-row:2; animation-delay:.20s }
  .col-cronograma  { grid-column:3; grid-row:2; animation-delay:.25s }

  /* torta: contenedor con aspect-ratio libre */
  .torta-wrap {
    width:100%; height:100%;
    display:flex; align-items:center; justify-content:center;
  }
  .torta-wrap canvas { width:100% !important; height:100% !important; }

  /* ── TABLA CRONOGRAMA ─────────────────── */
  .tabla-wrap { width:100%; height:100%; overflow-y:auto; }
  .tabla-wrap table { width:100%; border-collapse:collapse; font-size:12px; }
  .tabla-wrap thead th {
    background:var(--verde); color:var(--blanco);
    font-family:'Barlow Condensed',sans-serif; font-weight:700;
    font-size:11px; letter-spacing:1px; text-transform:uppercase;
    padding:7px 10px; text-align:left; position:sticky; top:0;
  }
  .tabla-wrap thead th:last-child { text-align:center; }
  .tabla-wrap tbody tr { border-bottom:1px solid var(--gris-borde); transition:background .15s; }
  .tabla-wrap tbody tr:hover { background:var(--crema); }
  .tabla-wrap tbody tr:nth-child(even) { background:#f8f5ef; }
  .tabla-wrap tbody tr:nth-child(even):hover { background:var(--crema); }
  .tabla-wrap td { padding:6px 10px; font-size:12px; color:#222; }
  .tabla-wrap td:last-child {
    text-align:center;
    font-family:'Barlow Condensed',sans-serif; font-weight:700;
    font-size:14px; color:var(--dorado-dim);
  }
  .pill {
    display:inline-block; font-family:'Barlow Condensed',sans-serif;
    font-size:10px; font-weight:700; padding:1px 6px;
    border-radius:10px; margin-left:5px; letter-spacing:.5px;
  }
  .pill-2026 { background:rgba(10,61,43,.12); color:var(--verde); }
  .pill-2027 { background:rgba(200,160,80,.22); color:var(--dorado-dim); }
  .tfoot-total td {
    padding:7px 10px; background:var(--verde); color:var(--blanco);
    font-family:'Barlow Condensed',sans-serif; font-weight:700; font-size:13px;
  }
  .tfoot-total td:last-child { text-align:center; color:var(--dorado-light); font-size:15px; }

  ::-webkit-scrollbar { width:4px; }
  ::-webkit-scrollbar-thumb { background:var(--dorado); border-radius:3px; }

  @keyframes fadeUp {
    from { opacity:0; transform:translateY(12px); }
    to   { opacity:1; transform:translateY(0); }
  }
</style>
</head>
<body>
<div class="dashboard">

  <header class="header">
    <div class="header-left">
      <div class="header-logo">
        <img src="data:image/png;base64,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" alt="CENGOB">
      </div>
      <div class="header-divider"></div>
      <div class="header-title">INTERVENCIONES <span>2026–2027</span> · Dashboard Ejecutivo</div>
    </div>
    <div class="header-right">Análisis de Proyectos y Presupuestos</div>
  </header>

  <div class="body">

    <!-- KPIs -->
    <div class="col-kpis">
      <div class="kpi-card">
        <div class="kpi-accent"></div>
        <div class="kpi-label">Total de Intervenciones</div>
        <div class="kpi-value">430</div>
        <div class="kpi-unit">Proyectos registrados hasta Dic. 2026</div>
      </div>
      <div class="kpi-card">
        <div class="kpi-accent"></div>
        <div class="kpi-label">Inversión Total</div>
        <div class="kpi-value small">4,225</div>
        <div class="kpi-unit">Millones de Bs. destinados a intervenciones</div>
      </div>
      <div class="kpi-tipo">
        <div class="kpi-tipo-accent"></div>
        <div class="kpi-tipo-title">Proyectos por Tipo de Intervención</div>
        <div id="tipoRows"></div>
      </div>
    </div>

    <!-- TORTA CANTIDAD -->
    <div class="col-torta-cant card">
      <div class="card-header">
        <div class="card-header-dot"></div>
        <div class="card-title">Distribución por Departamento — Cantidad de Proyectos</div>
      </div>
      <div class="card-body" style="padding:6px 4px;">
        <div class="torta-wrap">
          <canvas id="tortaCant"></canvas>
        </div>
      </div>
    </div>

    <!-- BARRAS MONTO -->
    <div class="col-barras-monto card">
      <div class="card-header">
        <div class="card-header-dot"></div>
        <div class="card-title">Distribución por Departamento — Monto de Inversión (Millones Bs.)</div>
      </div>
      <div class="card-body" style="padding:8px 12px;">
        <canvas id="barrasMonto"></canvas>
      </div>
    </div>

    <!-- RANGOS -->
    <div class="col-rangos card">
      <div class="card-header">
        <div class="card-header-dot"></div>
        <div class="card-title">Clasificación por Rango de Inversión</div>
      </div>
      <div class="card-body" style="padding:10px 14px;">
        <canvas id="chartRangos"></canvas>
      </div>
    </div>

    <!-- CRONOGRAMA -->
    <div class="col-cronograma card">
      <div class="card-header">
        <div class="card-header-dot"></div>
        <div class="card-title">Cronograma de Entrega — Mes y Año</div>
      </div>
      <div class="card-body" style="padding:0; align-items:stretch;">
        <div class="tabla-wrap">
          <table>
            <thead>
              <tr>
                <th>Mes — Año de Entrega</th>
                <th style="text-align:center">Proyectos</th>
              </tr>
            </thead>
            <tbody id="tbodyCrono"></tbody>
            <tfoot>
              <tr class="tfoot-total">
                <td>TOTAL</td><td>430</td>
              </tr>
            </tfoot>
          </table>
        </div>
      </div>
    </div>

  </div>
</div>

<script>
const VERDE       = '#0A3D2B';
const VERDE_LIGHT = '#1e7a52';
const VERDE_PALE  = '#3a9d5b';
const DORADO      = '#C8A050';
const DORADO_DIM  = '#96762a';

function paleta(n){
  const c=['#0A3D2B','#1a5e3f','#2d8057','#3fa070','#52be8a','#68d4a0',
           '#C8A050','#d9b46a','#b88935','#8a631c','#e0bc76','#5b3a10'];
  return c.slice(0,n);
}

// ── TIPO INTERVENCIÓN ──
const tipos = [
  { nombre:'Proyecto de inversión/Obra', cant:408 },
  { nombre:'Actividad',                  cant:10  },
  { nombre:'Programa',                   cant:8   },
  { nombre:'Decreto/Norma',              cant:2   },
  { nombre:'Otro',                       cant:2   },
];
const maxT = 408;
const container = document.getElementById('tipoRows');
tipos.forEach(t=>{
  const pct = Math.round((t.cant/maxT)*100);
  container.innerHTML += `
    <div class="tipo-row">
      <span class="tipo-nombre">${t.nombre}</span>
      <div class="tipo-bar-wrap"><div class="tipo-bar-fill" style="width:${pct}%"></div></div>
      <span class="tipo-badge">${t.cant}</span>
    </div>`;
});

// ── TORTA CANTIDAD ──
new Chart(document.getElementById('tortaCant'),{
  type:'pie',
  data:{
    labels:['LA PAZ','SANTA CRUZ','BENI','SIN REF.','COCHABAMBA','POTOSÍ','CHUQUISACA','TARIJA','LP-EL ALTO','ORURO','PANDO','CBBA-CHQ-SCZ'],
    datasets:[{ data:[138,65,51,35,35,31,24,17,14,10,9,1], backgroundColor:paleta(12), borderColor:'#fff', borderWidth:2 }]
  },
  options:{
    responsive:true, maintainAspectRatio:false,
    plugins:{
      legend:{
        position:'right',
        labels:{
          font:{family:'Barlow',size:10}, color:'#333',
          boxWidth:11, padding:6,
          generateLabels(chart){
            const d=chart.data;
            return d.labels.map((lbl,i)=>{
              const v=d.datasets[0].data[i];
              const t=d.datasets[0].data.reduce((a,b)=>a+b,0);
              return { text:`${lbl} — ${v} (${((v/t)*100).toFixed(1)}%)`,
                       fillStyle:d.datasets[0].backgroundColor[i],
                       strokeStyle:'#fff', lineWidth:1, index:i };
            });
          }
        }
      },
      tooltip:{ callbacks:{ label:(c)=>{
        const t=c.dataset.data.reduce((a,b)=>a+b,0);
        return `  ${c.label}: ${c.parsed} proy. (${((c.parsed/t)*100).toFixed(1)}%)`;
      }}}
    }
  }
});

// ── BARRAS HORIZONTALES MONTO ──
const deptoLabels = ['COCHABAMBA','SANTA CRUZ','LA PAZ','BENI','SIN REF.','POTOSÍ','ORURO','TARIJA','CHUQUISACA','PANDO','CBBA-CHQ-SCZ','LP-EL ALTO'];
const deptoMontos = [1201.4,1020.7,553.3,332.5,312.1,220.1,191.2,114.4,101.0,95.9,51.3,31.7];

// colores alternados verde/dorado
const barColors = deptoLabels.map((_,i)=> i%2===0 ? VERDE : DORADO);

new Chart(document.getElementById('barrasMonto'),{
  type:'bar',
  data:{
    labels: deptoLabels,
    datasets:[{
      label:'Millones Bs.',
      data: deptoMontos,
      backgroundColor: barColors,
      borderRadius:4,
      borderSkipped:false
    }]
  },
  options:{
    indexAxis:'y',
    responsive:true, maintainAspectRatio:false,
    plugins:{
      legend:{ display:false },
      tooltip:{ callbacks:{ label:(c)=>`  Bs. ${c.parsed.x.toFixed(1)}M` } }
    },
    scales:{
      x:{
        grid:{ color:'rgba(0,0,0,0.06)' },
        ticks:{ font:{family:'Barlow',size:10}, color:'#666',
                callback:(v)=>`${v}M` }
      },
      y:{
        grid:{ display:false },
        ticks:{ font:{family:'Barlow Condensed',size:11,weight:'600'}, color:VERDE }
      }
    },
    layout:{ padding:{ right:52 } },
    animation:{ duration:900, easing:'easeOutQuart' }
  },
  plugins:[{
    id:'montoVals',
    afterDatasetsDraw(chart){
      const {ctx,data}=chart;
      chart.getDatasetMeta(0).data.forEach((bar,i)=>{
        const v=data.datasets[0].data[i];
        ctx.save();
        ctx.fillStyle='#333';
        ctx.font="bold 11px 'Barlow Condensed',sans-serif";
        ctx.textAlign='left'; ctx.textBaseline='middle';
        ctx.fillText(`Bs. ${v.toFixed(0)}M`, bar.x+5, bar.y);
        ctx.restore();
      });
    }
  }]
});

// ── BARRAS RANGOS ──
new Chart(document.getElementById('chartRangos'),{
  type:'bar',
  data:{
    labels:['< 2 Millones','2 a 7 Millones','> 7 Millones','Sin Referencia'],
    datasets:[{
      data:[200,100,99,31],
      backgroundColor:[VERDE,DORADO,VERDE_LIGHT,'#b0a080'],
      borderRadius:5, borderSkipped:false
    }]
  },
  options:{
    indexAxis:'y',
    responsive:true, maintainAspectRatio:false,
    plugins:{
      legend:{ display:false },
      tooltip:{ callbacks:{ label:(c)=>`  ${c.parsed.x} proyectos` } }
    },
    scales:{
      x:{ grid:{ color:'rgba(0,0,0,0.06)' }, ticks:{ font:{family:'Barlow',size:11}, color:'#555' } },
      y:{ grid:{ display:false }, ticks:{ font:{family:'Barlow Condensed',size:13,weight:'600'}, color:VERDE } }
    },
    layout:{ padding:{ right:36 } },
    animation:{ duration:900, easing:'easeOutQuart' }
  },
  plugins:[{
    id:'rangoVals',
    afterDatasetsDraw(chart){
      const {ctx,data}=chart;
      chart.getDatasetMeta(0).data.forEach((bar,i)=>{
        const v=data.datasets[0].data[i];
        ctx.save();
        ctx.fillStyle=VERDE;
        ctx.font="bold 13px 'Barlow Condensed',sans-serif";
        ctx.textAlign='left'; ctx.textBaseline='middle';
        ctx.fillText(v, bar.x+6, bar.y);
        ctx.restore();
      });
    }
  }]
});

// ── TABLA CRONOGRAMA ──
const cronograma=[
  {mes:'Mayo',año:2026,cant:150},{mes:'Junio',año:2026,cant:119},
  {mes:'Julio',año:2026,cant:113},{mes:'Agosto',año:2026,cant:32},
  {mes:'Septiembre',año:2026,cant:3},{mes:'Octubre',año:2026,cant:2},
  {mes:'Noviembre',año:2026,cant:1},{mes:'Diciembre',año:2026,cant:2},
  {mes:'Febrero',año:2027,cant:2},{mes:'Marzo',año:2027,cant:1},
  {mes:'Julio',año:2027,cant:1},{mes:'Diciembre',año:2027,cant:4},
];
const tbody=document.getElementById('tbodyCrono');
cronograma.forEach(r=>{
  const tr=document.createElement('tr');
  tr.innerHTML=`<td>${r.mes} <span class="pill pill-${r.año}">${r.año}</span></td><td>${r.cant}</td>`;
  tbody.appendChild(tr);
});
</script>
</body>
</html>
