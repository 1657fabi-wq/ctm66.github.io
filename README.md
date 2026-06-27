<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<title>MVP — Módulo de IA para CNC</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@500;600;700&family=Inter:wght@400;500;600&family=JetBrains+Mono:wght@400;500;600&display=swap');

  :root{
    --bg:#14171A;
    --panel:#1B1F23;
    --panel-2:#20252A;
    --line:#2B3036;
    --text:#ECE9E2;
    --muted:#90969D;
    --amber:#F2A33D;
    --teal:#46D5C1;
    --red:#E2574C;
  }
  *{box-sizing:border-box;}
  body{
    margin:0;
    background:var(--bg);
    color:var(--text);
    font-family:'Inter',sans-serif;
    -webkit-font-smoothing:antialiased;
  }
  body::before{
    content:"";
    position:fixed; inset:0;
    background-image:
      linear-gradient(var(--line) 1px, transparent 1px),
      linear-gradient(90deg, var(--line) 1px, transparent 1px);
    background-size: 38px 38px;
    opacity:.18;
    pointer-events:none;
    z-index:0;
  }
  .wrap{position:relative; z-index:1;}

  header{
    display:flex; align-items:center; justify-content:space-between;
    padding:18px 32px;
    border-bottom:1px solid var(--line);
    background:rgba(20,23,26,.85);
    position:sticky; top:0; backdrop-filter:blur(6px);
    z-index:10;
  }
  .brand{display:flex; align-items:center; gap:10px;}
  .brand .mark{
    width:30px; height:30px; border-radius:7px;
    background:linear-gradient(135deg, var(--amber), #C97A1E);
    display:flex; align-items:center; justify-content:center;
    font-family:'JetBrains Mono'; font-weight:700; color:#1A1305; font-size:13px;
  }
  .brand .name{font-family:'Space Grotesk'; font-weight:700; font-size:17px; letter-spacing:.2px;}
  .brand .name span{color:var(--amber);}
  nav{display:flex; gap:6px; background:var(--panel); padding:4px; border-radius:10px; border:1px solid var(--line);}
  nav button{
    font-family:'Inter'; font-size:13px; font-weight:600; color:var(--muted);
    background:transparent; border:none; padding:8px 16px; border-radius:7px; cursor:pointer;
    transition:.15s;
  }
  nav button.active{background:var(--amber); color:#1A1305;}

  section.view{display:none; padding:0 0 60px 0;}
  section.view.active{display:block;}

  /* ---------- HERO ---------- */
  .hero{
    display:flex; gap:50px;
    padding:64px 60px 48px; align-items:center;
    max-width:1280px; margin:0 auto;
  }
  .eyebrow{
    font-family:'JetBrains Mono'; font-size:11.5px; letter-spacing:1.5px; color:var(--amber);
    text-transform:uppercase; margin-bottom:14px; display:flex; align-items:center; gap:8px;
  }
  .eyebrow .dot{width:7px;height:7px;border-radius:50%;background:var(--teal); box-shadow:0 0 0 4px rgba(70,213,193,.18);}
  h1{
    font-family:'Space Grotesk'; font-size:43px; line-height:1.12; font-weight:700;
    margin:0 0 18px; letter-spacing:-.5px;
  }
  h1 em{font-style:normal; color:var(--amber);}
  .hero p.lead{font-size:16px; line-height:1.65; color:var(--muted); max-width:480px; margin:0 0 28px;}
  .hero .ctarow{display:flex; gap:12px;}
  .btn{
    font-family:'Inter'; font-weight:600; font-size:14px; padding:13px 22px; border-radius:8px;
    border:1px solid transparent; cursor:pointer; transition:.15s;
  }
  .btn.primary{background:var(--amber); color:#1A1305;}
  .btn.primary:hover{background:#ffb854;}
  .btn.ghost{background:transparent; color:var(--text); border-color:var(--line);}
  .btn.ghost:hover{border-color:var(--muted);}

  .scope{
    background:var(--panel); border:1px solid var(--line); border-radius:14px; padding:22px;
  }
  .scope .label{font-family:'JetBrains Mono'; font-size:11px; color:var(--muted); display:flex; justify-content:space-between; margin-bottom:10px;}
  canvas#wave{width:100%; height:140px; display:block;}
  .scope .readout{display:flex; justify-content:space-between; margin-top:12px; font-family:'JetBrains Mono';}
  .scope .readout div{font-size:11px; color:var(--muted);}
  .scope .readout strong{display:block; font-size:19px; color:var(--text); font-weight:600;}
  .scope .readout strong.amber{color:var(--amber);}

  /* ---------- PROBLEMA ---------- */
  .section-title{
    font-family:'Space Grotesk'; font-size:24px; font-weight:700; margin:0 0 6px; letter-spacing:-.3px;
  }
  .section-sub{color:var(--muted); font-size:14px; margin:0 0 30px; max-width:560px;}
  .band{padding:48px 60px; max-width:1280px; margin:0 auto;}
  .band.alt{background:var(--panel); border-top:1px solid var(--line); border-bottom:1px solid var(--line);}
  .cards3{display:flex; gap:18px;}
  .cards3 .pcard{flex:1;}
  .pcard{background:var(--panel-2); border:1px solid var(--line); border-radius:12px; padding:22px;}
  .pcard .stat{font-family:'JetBrains Mono'; font-size:28px; font-weight:600; color:var(--red); margin-bottom:8px;}
  .pcard h3{font-family:'Space Grotesk'; font-size:15px; margin:0 0 8px;}
  .pcard p{font-size:13px; color:var(--muted); line-height:1.55; margin:0;}

  /* ---------- COMO FUNCIONA ---------- */
  .steps{display:flex; gap:0; position:relative;}
  .steps .step{flex:1;}
  .step{padding:0 22px; position:relative;}
  .step:not(:last-child)::after{
    content:"→"; position:absolute; right:-4px; top:8px; color:var(--line); font-size:20px;
  }
  .step .n{font-family:'JetBrains Mono'; color:var(--amber); font-size:12px; margin-bottom:10px;}
  .step h3{font-family:'Space Grotesk'; font-size:16px; margin:0 0 8px;}
  .step p{font-size:13px; color:var(--muted); line-height:1.55;}

  /* ---------- CTA / FORM ---------- */
  .ctaform{
    background:var(--panel); border:1px solid var(--line); border-radius:16px;
    padding:40px; display:flex; gap:40px; align-items:center;
  }
  .ctaform > div, .ctaform > form{flex:1;}
  .ctaform h2{font-family:'Space Grotesk'; font-size:23px; margin:0 0 10px;}
  .ctaform p{color:var(--muted); font-size:13.5px; line-height:1.6; margin:0;}
  form{display:flex; flex-direction:column; gap:12px;}
  form input, form select{
    background:var(--panel-2); border:1px solid var(--line); border-radius:8px; color:var(--text);
    padding:11px 13px; font-family:'Inter'; font-size:13.5px; outline:none;
  }
  form input:focus, form select:focus{border-color:var(--amber);}
  form label{font-size:11.5px; color:var(--muted); margin-bottom:-6px; font-family:'JetBrains Mono';}
  #confirm{display:none; background:rgba(70,213,193,.1); border:1px solid var(--teal); border-radius:8px; padding:14px; font-size:13px; color:var(--teal); margin-top:6px;}

  footer{padding:26px 60px; text-align:center; color:var(--muted); font-size:12px; font-family:'JetBrains Mono'; border-top:1px solid var(--line);}

  /* ---------- DASHBOARD ---------- */
  .dash{max-width:1280px; margin:0 auto; padding:30px 40px 60px;}
  .dash-head{display:flex; justify-content:space-between; align-items:flex-end; margin-bottom:22px; flex-wrap:wrap; gap:14px;}
  .dash-head h2{font-family:'Space Grotesk'; font-size:22px; margin:0;}
  .machine-status{display:flex; align-items:center; gap:8px; font-family:'JetBrains Mono'; font-size:12px; color:var(--teal);}
  .machine-status .led{width:8px; height:8px; border-radius:50%; background:var(--teal); box-shadow:0 0 8px var(--teal); animation:blink 2s infinite;}
  @keyframes blink{0%,100%{opacity:1;}50%{opacity:.35;}}

  .grid4{display:flex; gap:16px; margin-bottom:18px;}
  .grid4 .kpi{flex:1;}
  .kpi{background:var(--panel); border:1px solid var(--line); border-radius:12px; padding:18px;}
  .kpi .lbl{font-family:'JetBrains Mono'; font-size:11px; color:var(--muted); text-transform:uppercase; letter-spacing:.5px;}
  .kpi .val{font-family:'JetBrains Mono'; font-size:26px; font-weight:600; margin-top:6px;}
  .kpi .val.amber{color:var(--amber);} .kpi .val.teal{color:var(--teal);} .kpi .val.red{color:var(--red);}
  .kpi .delta{font-size:11px; color:var(--muted); margin-top:4px;}

  .grid2{display:flex; gap:16px;}
  .grid2 > .panel:first-child{flex:1.4;} .grid2 > .panel:last-child{flex:1;}
  .panel{background:var(--panel); border:1px solid var(--line); border-radius:12px; padding:20px;}
  .panel h3{font-family:'Space Grotesk'; font-size:14px; margin:0 0 14px; display:flex; justify-content:space-between; align-items:center;}
  .panel h3 span{font-family:'JetBrains Mono'; font-size:10.5px; color:var(--muted); font-weight:400;}
  canvas#vib{width:100%; height:170px;}

  .alert{background:rgba(242,163,61,.08); border:1px solid var(--amber); border-radius:10px; padding:14px; margin-bottom:12px;}
  .alert .tag{font-family:'JetBrains Mono'; font-size:10px; color:var(--amber); text-transform:uppercase; letter-spacing:1px;}
  .alert p{font-size:13px; margin:8px 0 0; line-height:1.5;}

  .loglist{list-style:none; margin:0; padding:0; font-family:'JetBrains Mono'; font-size:11.5px;}
  .loglist li{display:flex; justify-content:space-between; padding:8px 0; border-bottom:1px solid var(--line); color:var(--muted);}
  .loglist li b{color:var(--text); font-family:'Inter'; font-weight:500;}

  @media (max-width:900px){
    .hero{flex-direction:column; padding:40px 24px;}
    .cards3, .steps, .grid4, .grid2, .ctaform{flex-direction:column;}
    .band{padding:36px 24px;}
  }
</style>
</head>
<body>
<div class="wrap">

<header>
  <div class="brand">
    <div class="mark">AI</div>
    <div class="name">retroFIT<span>.ia</span></div>
  </div>
  <nav>
    <button class="active" onclick="showView('landing', this)">Visão Geral</button>
    <button onclick="showView('dashboard', this)">Painel de Monitoramento (demo)</button>
  </nav>
</header>

<!-- ===================== LANDING ===================== -->
<section class="view active" id="landing">

  <div class="hero">
    <div>
      <div class="eyebrow"><span class="dot"></span> MVP — TESTE DE PROPOSTA DE VALOR</div>
      <h1>Sua CNC já sabe<br>usinar. Agora ela<br><em>aprende a se cuidar.</em></h1>
      <p class="lead">Um módulo de Inteligência Artificial que se acopla à sua máquina CNC já existente — sem trocar o equipamento — e antecipa quebra de ferramenta, reduz retrabalho e mostra tudo em tempo real.</p>
      <div class="ctarow">
        <button class="btn primary" onclick="document.getElementById('form-section').scrollIntoView({behavior:'smooth'})">Quero participar do piloto</button>
        <button class="btn ghost" onclick="showView('dashboard', document.querySelectorAll('nav button')[1])">Ver painel de demonstração</button>
      </div>
    </div>
    <div class="scope">
      <div class="label"><span>SENSOR — VIBRAÇÃO (EIXO Z)</span><span id="liveclock">ao vivo</span></div>
      <canvas id="wave" width="500" height="140"></canvas>
      <div class="readout">
        <div>Amplitude<br><strong id="ampVal">0.42 mm/s</strong></div>
        <div>Frequência<br><strong>118 Hz</strong></div>
        <div>Diagnóstico IA<br><strong class="amber">Dentro do padrão</strong></div>
      </div>
    </div>
  </div>

  <div class="band alt">
    <div class="section-title">O problema que essa hipótese testa</div>
    <p class="section-sub">Dores levantadas com PMEs metalmecânicas, moldes e matrizes durante a pesquisa metaprojetual do projeto.</p>
    <div class="cards3">
      <div class="pcard">
        <div class="stat">~28%</div>
        <h3>Baixa digitalização</h3>
        <p>Parcela de indústrias brasileiras de médio/grande porte que já adotou alguma tecnologia 4.0 — a maioria ainda opera no escuro quanto a dados de máquina.</p>
      </div>
      <div class="pcard">
        <div class="stat">81%</div>
        <h3>Falta de mão de obra qualificada</h3>
        <p>Dos empregadores brasileiros relatam dificuldade para encontrar profissionais com a qualificação técnica necessária.</p>
      </div>
      <div class="pcard">
        <div class="stat">R$ 9,3 mil</div>
        <h3>Perda mensal estimada</h3>
        <p>Em quebra de ferramenta, retrabalho, parada não planejada e desperdício de material, por máquina, em uma PME metalmecânica típica.</p>
      </div>
    </div>
  </div>

  <div class="band">
    <div class="section-title">Como o módulo funciona</div>
    <p class="section-sub">A solução que estamos testando com este MVP — simples de instalar, sem trocar a máquina.</p>
    <div class="steps">
      <div class="step">
        <div class="n">01</div>
        <h3>Sensoriamento</h3>
        <p>Sensores de vibração, temperatura e som são acoplados à máquina CNC já existente, sem interromper a produção.</p>
      </div>
      <div class="step">
        <div class="n">02</div>
        <h3>IA de borda</h3>
        <p>Um computador local processa os dados em tempo real e identifica padrões de desgaste e risco de quebra de ferramenta.</p>
      </div>
      <div class="step">
        <div class="n">03</div>
        <h3>Painel e alerta</h3>
        <p>Operador e gestor recebem alertas e relatórios simples, sem precisar interpretar dados técnicos brutos.</p>
      </div>
    </div>
  </div>

  <div class="band alt" id="form-section">
    <div class="ctaform">
      <div>
        <h2>Testar essa hipótese com você</h2>
        <p>Este formulário é o próprio instrumento de teste do MVP: ele mede se gestores reais — do seu segmento — têm interesse genuíno em participar de um piloto gratuito do módulo de IA na própria máquina. Não envolve nenhum cobrança nesta etapa.</p>
      </div>
      <form onsubmit="submitForm(event)">
        <label>NOME</label>
        <input required placeholder="Seu nome">
        <label>EMPRESA / INSTITUIÇÃO</label>
        <input required placeholder="Nome da empresa">
        <label>SEGMENTO</label>
        <select>
          <option>PME metalmecânica</option>
          <option>Moldes e matrizes</option>
          <option>Escola técnica / SENAI</option>
          <option>Aeroespacial / Automotivo</option>
        </select>
        <label>WHATSAPP OU E-MAIL</label>
        <input required placeholder="Contato">
        <button class="btn primary" type="submit">Confirmar interesse no piloto</button>
        <div id="confirm">✓ Registrado. Esse contato é um dado de teste — exporte-o na ficha de campo após a conversa presencial.</div>
      </form>
    </div>
  </div>

</section>

<!-- ===================== DASHBOARD ===================== -->
<section class="view" id="dashboard">
  <div class="dash">
    <div class="dash-head">
      <div>
        <div class="eyebrow"><span class="dot"></span> DEMONSTRAÇÃO — DADOS SIMULADOS</div>
        <h2>CNC-01 · Centro de Usinagem Vertical</h2>
      </div>
      <div class="machine-status"><span class="led"></span> EM OPERAÇÃO — turno 14:00–22:00</div>
    </div>

    <div class="grid4">
      <div class="kpi"><div class="lbl">Vibração eixo Z</div><div class="val amber" id="kpiVib">0.42 mm/s</div><div class="delta">limite de alerta: 0.65 mm/s</div></div>
      <div class="kpi"><div class="lbl">Temperatura da ferramenta</div><div class="val" id="kpiTemp">61 °C</div><div class="delta">faixa ideal: 45–70°C</div></div>
      <div class="kpi"><div class="lbl">Eficiência energética</div><div class="val teal">−27%</div><div class="delta">vs. média antes do módulo</div></div>
      <div class="kpi"><div class="lbl">Peças usinadas (turno)</div><div class="val" id="kpiPecas">184</div><div class="delta">0 refeitas neste turno</div></div>
    </div>

    <div class="grid2">
      <div class="panel">
        <h3>Assinatura de vibração em tempo real <span>eixo Z · mm/s</span></h3>
        <canvas id="vib" width="760" height="170"></canvas>
      </div>
      <div class="panel">
        <h3>Diagnóstico da IA</h3>
        <div class="alert">
          <div class="tag">Manutenção preditiva</div>
          <p>Padrão de vibração de alta frequência indica desgaste progressivo na fresa de topo. Estimativa: <b>~8h de uso restante</b> antes da troca recomendada.</p>
        </div>
        <div class="alert" style="border-color:var(--teal); background:rgba(70,213,193,.07);">
          <div class="tag" style="color:var(--teal);">Simulação prévia</div>
          <p>Trajetória do próximo lote validada por gêmeo digital — nenhuma colisão detectada, tempo de ciclo otimizado em 6%.</p>
        </div>
      </div>
    </div>

    <div class="panel" style="margin-top:16px;">
      <h3>Histórico de eventos</h3>
      <ul class="loglist">
        <li><span>14:02</span><b>Início de turno registrado</b><span>operador: J. Silva</span></li>
        <li><span>15:41</span><b>Vibração dentro do padrão</b><span>0.31 mm/s</span></li>
        <li><span>17:18</span><b>Alerta preditivo emitido</b><span>desgaste de fresa</span></li>
        <li><span>19:55</span><b>Simulação de trajetória validada</b><span>lote #112</span></li>
      </ul>
    </div>
  </div>
</section>

<footer>MVP — Projeto Módulo de IA para Máquinas CNC · SENAI Linhares · Linhares/ES · dados desta tela são simulados para fins de teste de hipótese</footer>
</div>

<script>
function showView(id, btn){
  document.querySelectorAll('.view').forEach(v=>v.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
}
function submitForm(e){
  e.preventDefault();
  document.getElementById('confirm').style.display='block';
}

// Hero waveform
const waveCv = document.getElementById('wave');
const wctx = waveCv.getContext('2d');
let t = 0;
function drawWave(){
  wctx.clearRect(0,0,waveCv.width,waveCv.height);
  wctx.strokeStyle = '#F2A33D';
  wctx.lineWidth = 2;
  wctx.beginPath();
  for(let x=0;x<waveCv.width;x++){
    const y = waveCv.height/2
      + Math.sin((x*0.06)+t)*22
      + Math.sin((x*0.13)+t*1.7)*8
      + (Math.random()-0.5)*4;
    if(x===0) wctx.moveTo(x,y); else wctx.lineTo(x,y);
  }
  wctx.stroke();
  t += 0.09;
  document.getElementById('ampVal').textContent = (0.38+Math.random()*0.08).toFixed(2)+' mm/s';
  requestAnimationFrame(drawWave);
}
drawWave();

// Dashboard vibration chart
const vibCv = document.getElementById('vib');
const vctx = vibCv.getContext('2d');
let t2 = 0;
function drawVib(){
  vctx.clearRect(0,0,vibCv.width,vibCv.height);
  vctx.strokeStyle = '#2B3036';
  vctx.beginPath(); vctx.moveTo(0, vibCv.height*0.25); vctx.lineTo(vibCv.width, vibCv.height*0.25); vctx.stroke();
  vctx.strokeStyle = '#46D5C1';
  vctx.lineWidth = 2;
  vctx.beginPath();
  for(let x=0;x<vibCv.width;x++){
    const y = vibCv.height/2
      + Math.sin((x*0.045)+t2)*30
      + Math.sin((x*0.11)+t2*1.4)*12
      + (Math.random()-0.5)*6;
    if(x===0) vctx.moveTo(x,y); else vctx.lineTo(x,y);
  }
  vctx.stroke();
  t2 += 0.07;
  requestAnimationFrame(drawVib);
}
drawVib();

// KPI ticking
setInterval(()=>{
  document.getElementById('kpiVib').textContent = (0.38+Math.random()*0.1).toFixed(2)+' mm/s';
  document.getElementById('kpiTemp').textContent = (58+Math.random()*6).toFixed(0)+' °C';
  const p = document.getElementById('kpiPecas');
  if(Math.random()>0.6) p.textContent = (parseInt(p.textContent)+1);
}, 1800);
</script>
</body>
</html>
