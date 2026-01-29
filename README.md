<!doctype html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>具身智能数据采集与训练平台 - 运营大屏</title>
  <style>
    :root{
      --bg1:#040b1e;
      --bg2:#020713;
      --panelTop: rgba(10,26,60,.86);
      --panelBot: rgba(4,10,26,.92);
      --stroke: rgba(92,182,255,.42);
      --stroke2: rgba(99,255,230,.10);
      --text:#eef6ff;
      --sub:#9fb6e6;
      --grid:#213a66;
      --cyan:#45e1ff;
      --teal:#33f2d3;
      --yellow:#ffd166;
      --blue:#3b82ff;
      --purple:#8a7dff;
      --orange:#ff995a;
      --pink:#ff74b8;
    }
    *{ box-sizing:border-box; }
    html,body{ height:100%; margin:0; font-family: Inter,"PingFang SC","Microsoft YaHei",Arial,sans-serif; color:var(--text); }
    body{
      background:
        radial-gradient(1200px 700px at 50% 0%, rgba(14,58,138,.38), transparent 60%),
        linear-gradient(180deg, var(--bg1), var(--bg2));
      overflow:hidden;
    }
    body::before{
      content:"";
      position:fixed;
      inset:0;
      background:
        radial-gradient(900px 520px at 18% 18%, rgba(69,225,255,.16), transparent 62%),
        radial-gradient(780px 460px at 82% 72%, rgba(138,125,255,.12), transparent 62%),
        radial-gradient(620px 380px at 62% 18%, rgba(51,242,211,.10), transparent 60%),
        linear-gradient(180deg, rgba(255,255,255,.04), transparent 35%);
      filter: saturate(1.05);
      opacity:.95;
      pointer-events:none;
      mix-blend-mode: screen;
      animation: floatGlow 26s ease-in-out infinite alternate;
    }
    body::after{
      content:"";
      position:fixed;
      inset:0;
      background:
        repeating-linear-gradient(
          180deg,
          rgba(255,255,255,.00) 0px,
          rgba(255,255,255,.00) 2px,
          rgba(255,255,255,.018) 3px,
          rgba(255,255,255,.00) 6px
        );
      opacity:.20;
      pointer-events:none;
    }
    @keyframes floatGlow{ 0%{ transform: translate3d(0,0,0) scale(1); } 100%{ transform: translate3d(-3.5%, -2.5%, 0) scale(1.03); } }
    /* Stage */
    .stage{ width:1920px; height:1080px; transform-origin:0 0; position:relative; }
    .wrap{ width:100%; height:100%; padding:28px 34px; position:relative; }

    /* Header */
    .header{
      position:relative;
      height:94px;
      border-radius:18px;
      background: linear-gradient(180deg, rgba(11,44,95,.90), rgba(6,18,40,.90));
      border: 1.5px solid rgba(92,182,255,.54);
      box-shadow: inset 0 0 0 1px rgba(66,227,255,.08);
      display:flex;
      align-items:center;
      justify-content:space-between;
      padding: 0 20px;
      z-index:2;
    }
    .header:before{
      content:"";
      position:absolute; inset:0;
      background: linear-gradient(90deg, transparent, rgba(66,227,255,.10), transparent);
      opacity:.85;
      pointer-events:none;
    }
    .titleBlock{ z-index:1; }
    .titleBlock h1{ margin:0; font-size:30px; font-weight:950; letter-spacing:.6px; text-shadow: 0 0 18px rgba(69,225,255,.18); }
    .titleBlock .sub{ margin-top:8px; font-size:12px; font-weight:750; color: var(--sub); }

    .headerRight{ z-index:1; display:flex; align-items:center; gap:10px; }
    .pill{
      height:34px;
      border-radius:999px;
      padding: 0 14px;
      display:flex; align-items:center; gap:8px;
      background: rgba(6,27,58,.70);
      border: 1px solid rgba(47,123,255,.35);
      box-shadow: inset 0 0 0 1px rgba(66,227,255,.08);
      color: var(--sub);
      font-size:12px;
      font-weight:850;
      backdrop-filter: blur(10px);
      -webkit-backdrop-filter: blur(10px);
      user-select:none;
      white-space:nowrap;
    }
    .pill b{ color: var(--text); font-weight:950; }
    .btn{
      height:34px;
      border-radius:999px;
      padding: 0 14px;
      display:flex; align-items:center;
      background: rgba(6,27,58,.75);
      border: 1px solid rgba(47,123,255,.45);
      box-shadow: inset 0 0 0 1px rgba(66,227,255,.08);
      color: var(--text);
      font-size:12px;
      font-weight:900;
      cursor:pointer;
      user-select:none;
      backdrop-filter: blur(10px);
      -webkit-backdrop-filter: blur(10px);
      transition: transform .18s ease, border-color .18s ease, box-shadow .18s ease;
    }
    .btn:hover{
      transform: translateY(-2px);
      border-color: rgba(99,255,230,.55);
      box-shadow: 0 10px 26px rgba(0,0,0,.35), inset 0 0 0 1px rgba(99,255,230,.14);
    }

    /* Sections */
    .sectionTitle{
      margin-top:18px;
      font-size:18px;
      font-weight:950;
      letter-spacing:.3px;
    }
    .sectionHint{
      margin-top:6px;
      font-size:12px;
      font-weight:800;
      color: var(--sub);
    }

    /* KPI Row */
    .kpiRow{
      margin-top:12px;
      display:grid;
      grid-template-columns: repeat(6, 1fr);
      gap: 16px;
      position:relative;
      z-index:2;
    }
    .kpiCard{
      border-radius:16px;
      background: linear-gradient(180deg, var(--panelTop), var(--panelBot));
      border: 1px solid rgba(99,255,230,.18);
      box-shadow: 0 18px 44px rgba(0,0,0,.42), inset 0 0 0 1px rgba(69,225,255,.08);
      padding: 14px 16px 12px 16px;
      position:relative;
      overflow:hidden;
      min-height:110px;
    }
    .kpiCard:after{
      content:"";
      position:absolute; inset:0;
      background-image: radial-gradient(rgba(255,255,255,.05) 1px, transparent 1px);
      background-size: 70px 70px;
      opacity:.14;
      pointer-events:none;
    }
    .kpiTopLine{
      height:3px; width:56px; border-radius:4px;
      background: linear-gradient(90deg, rgba(69,225,255,.95), rgba(69,225,255,.15));
      opacity:.95;
      margin-bottom:10px;
    }
    .kpiName{ font-size:14px; font-weight:950; }
    .kpiNum{ margin-top:14px; font-size:18px; font-weight:950; }
    .kpiSub{ margin-top:8px; font-size:12px; font-weight:850; color: var(--sub); }
    .kpiNum.big{ font-size:22px; font-variant-numeric: tabular-nums; }
    .kpiNum.flash{ transform: translateY(-1px) scale(1.02); text-shadow: 0 0 18px rgba(99,255,230,.40); transition: transform .22s ease, text-shadow .22s ease; }

    /* Middle layout */
    .midGrid{
      margin-top:16px;
      display:grid;
      grid-template-columns: 1.35fr 1fr;
      gap: 18px;
      position:relative;
      z-index:2;
    }
    .panel{
      border-radius:18px;
      background: linear-gradient(180deg, var(--panelTop), var(--panelBot));
      border: 1px solid rgba(99,255,230,.18);
      box-shadow: 0 18px 44px rgba(0,0,0,.45), inset 0 0 0 1px rgba(69,225,255,.08);
      overflow:hidden;
      position:relative;
    }
    .panel:after{
      content:"";
      position:absolute; inset:0;
      background-image: radial-gradient(rgba(255,255,255,.05) 1px, transparent 1px);
      background-size: 70px 70px;
      opacity:.14;
      pointer-events:none;
    }
    .panelInner{ position:absolute; inset:14px 14px 14px 14px; z-index:2; display:flex; flex-direction:column; }
    .chart{ width:100%; height:100%; }
    .mainChartWrap{ height: 340px; }
    .rightGrid{
      display:grid;
      grid-template-columns: 1fr 1fr;
      grid-template-rows: 1fr 1fr;
      gap: 14px;
      height: 340px;
    }
    .miniPanel{ border-radius:16px; position:relative; overflow:hidden; background: rgba(6,27,58,.55); border: 1px solid rgba(47,123,255,.28); box-shadow: inset 0 0 0 1px rgba(66,227,255,.06); }
    .miniPanel .miniTitle{ position:absolute; left:12px; top:10px; font-size:12px; font-weight:950; color:#cfe3ff; z-index:2; }
    .miniPanel .miniChart{ position:absolute; inset:32px 10px 10px 10px; z-index:1; }

    /* Pipeline */
    .pipeTitle{ margin-top:16px; font-size:18px; font-weight:950; letter-spacing:.3px; position:relative; z-index:2; white-space:nowrap;}
    .pipeRow{
      margin-top:12px;
      display:grid;
      grid-template-columns: repeat(7, 1fr);
      gap: 12px;
      position:relative;
      z-index:2;
    }
    .pipeCard{
      border-radius:16px;
      background: rgba(6,27,58,.55);
      border: 1px solid rgba(47,123,255,.28);
      box-shadow: inset 0 0 0 1px rgba(66,227,255,.06);
      padding: 14px 14px;
      min-height:78px;
    }
    .pipeName{ font-size:13px; font-weight:950; }
    .pipeStatus{ margin-top:10px; font-size:12px; font-weight:900; color: var(--sub); }
    .pipeStatus b{ color: var(--text); }
    
    /* Only change color: make these pipeline label texts match the number color */
    #pCollect,#pClean,#pProcess,#pTrain,#pEval,#pDeploy,#pFeedback{ color: var(--sub); }

    
    .bottomTitle{
      margin-top:16px;
      font-size:18px;
      font-weight:950;
      letter-spacing:.3px;
      position:relative;
      z-index:2;
      white-space:nowrap;
    }
    .bottomHint{
      margin-top:6px;
      font-size:12px;
      font-weight:800;
      color: var(--sub);
      position:relative;
      z-index:2;
    }
    .bottomGrid{
      margin-top:12px;
      display:grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 14px;
      position:relative;
      z-index:2;
    }
    .bottomPanel{
      border-radius:16px;
      position:relative;
      overflow:hidden;
      background: rgba(6,27,58,.55);
      border: 1px solid rgba(47,123,255,.28);
      box-shadow: inset 0 0 0 1px rgba(66,227,255,.06);
      height: 240px;
    }
    .bottomPanel .miniTitle{ position:absolute; left:12px; top:10px; font-size:12px; font-weight:950; color:#cfe3ff; z-index:2; }

    .bottomPanel .miniTitle.titleWithLegend{
      width: calc(100% - 24px);
      display:flex;
      align-items:center;
      justify-content:space-between;
      gap:10px;
      pointer-events:none;
    }
    .heatLegend{
      display:flex;
      align-items:center;
      gap:6px;
      font-size:10px;
      font-weight:900;
      color: var(--sub);
      letter-spacing:.2px;
    }
    .heatLegend .bar{
      width:96px;
      height:8px;
      border-radius:999px;
      border:1px solid rgba(255,255,255,.18);
      background: linear-gradient(90deg, rgba(255,255,255,.10), var(--cyan), var(--teal), var(--yellow));
      box-shadow: 0 0 10px rgba(66,227,255,.10) inset;
    }
    .heatLegend .lab{ opacity:.9; }

    .bottomPanel .miniChart{ position:absolute; inset:34px 10px 10px 10px; z-index:1; }
.footer{ position:absolute; right:34px; bottom:14px; font-size:12px; font-weight:850; color: var(--sub); z-index:2; }

  </style>
</head>
<body>
  <div class="stage" id="stage">
    <div class="wrap">

      <div class="header">
        <div class="titleBlock">
          <h1>Stellar 星空数据平台</h1>
          <div class="sub">Real-time Monitoring</div>
        </div>

        <div class="headerRight">
          <div class="pill">系统状态：<b id="sysState">正常</b></div>
          <div class="pill">数据刷新：<b id="refreshState">实时</b></div>
          <div class="pill">在线节点：<b id="onlineNodes">128</b></div>
          <div class="btn" id="btnRefresh">手动刷新</div>
</div>
      </div>

      <div class="sectionTitle">核心运营指标</div>
      <div class="sectionHint">Key Performance Indicators (KPIs)</div>

      <div class="kpiRow">
        <div class="kpiCard">
          <div class="kpiTopLine"></div>
          <div class="kpiName">累计数据量</div>
          <div class="kpiNum big" id="kpiTotal">1,765,803</div>
          <div class="kpiSub">episodes</div>
        </div>

        <div class="kpiCard">
          <div class="kpiTopLine"></div>
          <div class="kpiName">数据时长</div>
          <div class="kpiNum big" id="kpiDuration">12,480</div>
          <div class="kpiSub">hours</div>
        </div>
        <div class="kpiCard">
          <div class="kpiTopLine"></div>
          <div class="kpiName">有效数据率</div>
          <div class="kpiNum big" id="kpiQc">83.6%</div>
          <div class="kpiSub">quality</div>
        </div>
        <div class="kpiCard">
          <div class="kpiTopLine"></div>
          <div class="kpiName">异常事件数（千个）</div>
          <div class="kpiNum big" id="kpiErr">154</div>
          <div class="kpiSub">abnormal</div>
        </div>
        <div class="kpiCard">
          <div class="kpiTopLine"></div>
          <div class="kpiName">行为轨迹总量</div>
          <div class="kpiNum big" id="kpiTracks">1.04M+</div>
          <div class="kpiSub">tracks</div>
        </div>
<div class="kpiCard">
          <div class="kpiTopLine"></div>
          <div class="kpiName">在线机器人</div>
          <div class="kpiNum big" id="kpiRobotsOnline">42</div>
          <div class="kpiSub">agents</div>
        </div>
      </div>

      <div class="midGrid">
        <div>
          <div class="sectionTitle" style="margin-top:10px;">数据采集趋势（近 7 天）</div>
          <div class="sectionHint">采集流量变化与稳定性 · 日采集量/异常量</div>

          <div class="panel mainChartWrap">
            <div class="panelInner">
              <div class="chart" id="chartTrend"></div>
            </div>
          </div>
        </div>

        <div>
          <div class="sectionTitle" style="margin-top:10px;">数据资产结构概览</div>
          <div class="sectionHint">包含：场景/机器人/对象/数据量 </div>

          <div class="panel" style="height: 340px;">
            <div class="panelInner" style="inset:14px 14px 14px 14px;">
              <div class="rightGrid">
                <div class="miniPanel">
                  <div class="miniTitle">(a) 场景数据量占比</div>
                  <div class="miniChart" id="chartB"></div>
                </div>
                <div class="miniPanel">
                  <div class="miniTitle">(b) 机器人类型占比</div>
                  <div class="miniChart" id="chartC"></div>
                </div>
                <div class="miniPanel">
                  <div class="miniTitle">(c) 操作目标对象数量（千个）</div>
                  <div class="miniChart" id="chartObj"></div>
                </div>
                <div class="miniPanel">
                  <div class="miniTitle">(d) 各本体型号数据量（千条）</div>
                  <div class="miniChart" id="chartA"></div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      
      <div class="bottomTitle">采集任务数据概览</div>
      <div class="bottomHint">采集方式 · 常见技能 · 长程任务</div>

      <div class="bottomGrid">
        <div class="bottomPanel">
          <div class="miniTitle">采集方式</div>
          <div class="miniChart" id="chartMode"></div>
        </div>
<div class="bottomPanel">
          <div class="miniTitle">常见高复用技能采集量（千条）</div>
          <div class="miniChart" id="chartD"></div>
        </div>
        <div class="bottomPanel">
          <div class="miniTitle">长程任务数据采集量（千条）</div>
          <div class="miniChart" id="chartFine"></div>
        </div>
      </div>

          </div>
  </div>

  <script src="https://cdn.jsdelivr.net/npm/echarts@5/dist/echarts.min.js"></script>
  <script>
    // ====== 1920x1080 自适应 ======
    function fitStage(){
      const stage = document.getElementById('stage');
      const sx = window.innerWidth / 1920;
      const sy = window.innerHeight / 1080;
      const s = Math.min(sx, sy);
      stage.style.transform = `scale(${s})`;
    }
    window.addEventListener('resize', fitStage);
    fitStage();

    // ====== Colors ======
    const css = getComputedStyle(document.documentElement);
    const COLORS = {
      text: css.getPropertyValue('--text').trim(),
      sub: css.getPropertyValue('--sub').trim(),
      grid: css.getPropertyValue('--grid').trim(),
      cyan: css.getPropertyValue('--cyan').trim(),
      teal: css.getPropertyValue('--teal').trim(),
      yellow: css.getPropertyValue('--yellow').trim(),
      blue: css.getPropertyValue('--blue').trim(),
      purple: css.getPropertyValue('--purple').trim(),
      orange: css.getPropertyValue('--orange').trim(),
      pink: css.getPropertyValue('--pink').trim(),
    };
    const palette = [COLORS.cyan, COLORS.teal, COLORS.yellow, COLORS.blue, COLORS.purple, COLORS.orange, COLORS.pink, '#4ade80', '#60a5fa', '#f472b6'];

    const baseAxis = {
      axisLine: { lineStyle: { color: COLORS.grid, opacity: 0.45 } },
      axisTick: { lineStyle: { color: COLORS.grid, opacity: 0.45 } },
      axisLabel: { color: COLORS.sub, fontWeight: 700 },
      splitLine: { lineStyle: { color: COLORS.grid, opacity: 0.22 } }
    };

    function rand(min,max){ return Math.random()*(max-min)+min; }
    function randint(min,max){ return Math.floor(rand(min,max+1)); }

    function formatNumber(n){
      const x = Number(n);
      if (!Number.isFinite(x)) return String(n);
      return x.toLocaleString('en-US');
    }

    function flashIfChanged(el, prev, next, formatter=(v)=>String(v)){
      const nextText = formatter(next);
      if (String(prev) !== String(next) && el){
        el.textContent = nextText;
        el.classList.remove('flash');
        void el.offsetWidth;
        el.classList.add('flash');
        setTimeout(()=> el.classList.remove('flash'), 350);
      } else if (el) {
        el.textContent = nextText;
      }
    }

    function topN(names, values, n=5){
      const arr = names.map((name,i)=>({name, value:+values[i]})).sort((a,b)=>b.value-a.value);
      const top = arr.slice(0,n);
      const rest = arr.slice(n);
      if(rest.length) top.push({ name:'其他', value: +rest.reduce((s,i)=>s+i.value,0).toFixed(2) });
      return top;
    }

    function donutOption(centerText, names, values){
      return {
        backgroundColor:'transparent',
        color: palette,
        tooltip: {
          trigger:'item',
          borderColor:'rgba(66,227,255,.35)',
          backgroundColor:'rgba(5,12,30,.92)',
          borderWidth: 1.5,
          extraCssText:'backdrop-filter: blur(8px); -webkit-backdrop-filter: blur(8px);',
          textStyle:{ color:'#fff' }
        },
        legend: {
          type:'scroll',
          orient:'vertical',
          right: 6,
          top: 10,
          bottom: 6,
          textStyle: { color: COLORS.sub, fontWeight: 700 },
          itemWidth: 10, itemHeight: 10
        },
        graphic: [{
          type:'text',
          left:'38%',
          top:'50%',
          style:{ text:centerText, fill: COLORS.text, fontSize: 13, fontWeight: 950, textAlign:'center', textVerticalAlign:'middle', textShadowColor:'rgba(69,225,255,.22)', textShadowBlur: 10 }
        }],
        series: [{
          type:'pie',
          radius:['46%','72%'],
          center:['36%','55%'],
          label: { color: COLORS.sub, fontWeight: 700 },
          labelLine: { lineStyle: { color: COLORS.sub, opacity: 0.55 } },
          itemStyle: { borderColor:'rgba(7,19,49,.95)', borderWidth: 2 },
          data: topN(names, values, 4)
        }]
      };
    }

    
    function donutCompactOption(names, values, centerText=''){
      return {
        backgroundColor:'transparent',
        color: palette,
        tooltip: {
          trigger:'item',
          borderColor:'rgba(66,227,255,.35)',
          backgroundColor:'rgba(5,12,30,.92)',
          borderWidth: 1.5,
          extraCssText:'backdrop-filter: blur(8px); -webkit-backdrop-filter: blur(8px);',
          textStyle:{ color:'#fff' }
        },
        graphic: centerText ? [{
          type:'text',
          left:'50%',
          top:'56%',
          style:{ text:centerText, fill: COLORS.text, fontSize: 12, fontWeight: 950, textAlign:'center', textVerticalAlign:'middle', textShadowColor:'rgba(69,225,255,.20)', textShadowBlur: 10 }
        }] : [],
        legend: {
          type:'scroll',
          orient:'horizontal',
          left:'center',
          top: 0,
          textStyle: { color: COLORS.sub, fontWeight: 700 },
          itemWidth: 10, itemHeight: 10
        },
        series: [{
          type:'pie',
          radius:['42%','72%'],
          center:['50%','58%'],
          label: { color: COLORS.sub, fontWeight: 700, formatter:'{b} {d}%' },
          labelLine: { lineStyle: { color: COLORS.sub, opacity: 0.55 } },
          itemStyle: { borderColor:'rgba(7,19,49,.95)', borderWidth: 2 },
          data: topN(names, values, 6)
        }]
      };
    }

// ====== Chart init ======
    const cTrend = echarts.init(document.getElementById('chartTrend'));
    const cA = echarts.init(document.getElementById('chartA'));
    const cB = echarts.init(document.getElementById('chartB'));
    const cC = echarts.init(document.getElementById('chartC'));
    const cD = echarts.init(document.getElementById('chartD'));
    const cFine = echarts.init(document.getElementById('chartFine'));
    const cObj = echarts.init(document.getElementById('chartObj'));
    const cMode = echarts.init(document.getElementById('chartMode'));

    // ====== Baseline mock model ======
    const BASE = {
      total: 1765803,
      duration: 12480,
      qc: 83.6,
      err: 154,
      tracks: 1240000,     // 1.24M
      robots_online: 42,
      nodes_online: 128,

      trendDays: ["D-1","D-2","D-3","D-4","D-5","D-6","D-7"],
      trendVals: [1820, 1860, 1840, 1910, 1940, 1980, 1960], // arbitrary units

      trendAbnVals: [520, 508, 496, 434, 472, 360, 348], // 异常量（下降趋势）

      aNames: ["朱丽叶001","朱丽叶002","朱丽叶003","罗密欧001","罗密欧002","罗密欧003"],
      aVals:  [283,288,235,123,202,199],

      bNames: ["商业","科研高校","职业教育"],
      bPerc:  [15,53,32],

      cNames: ["机械臂","轮式","四足","人形"],
      cPerc:  [37,31,20,12],

      dNames: ["抓取","放置","推","拉","旋转","导航"],
      dVals:  [132,122,118,112,110,86],

      pipe: {
        collectLag: 8,
        cleanRate: 96.4,
        processP95: 14,
        trainGpu: 78,
        evalSr: 88.2,
        deployOnline: 42,
        feedbackRate: 63
      },


      fineTaskNames: ["推拉","拿放","拨动","关闭","擦拭","旋拧"],
      fineTaskVals:  [65, 68, 56, 32, 51, 27],

      objNames:  ["纸张","容器","家具","食物","玩偶","笔"],
      objVals:   [14, 90, 120, 11, 60, 30],

      modeNames: ["主从臂遥操作","数据采集手套采集","VR设备遥操作"],
      modePerc:  [47, 31, 22]
    };

    function nextState(prev){
      const s = JSON.parse(JSON.stringify(prev));
      s.total = Math.max(0, s.total + randint(8, 25));
      // 数据时长保持为整数且不随刷新变化
      s.duration = Math.round(s.duration);
      s.qc = Math.max(86, Math.min(99.6, s.qc + rand(-0.08, 0.06)));
      s.err = Math.max(0, s.err + randint(-1, 2));
      s.tracks = Math.max(100000, s.tracks + randint(20, 1200));
s.robots_online = Math.max(1, s.robots_online + randint(-1, 1));
      s.nodes_online = Math.max(10, s.nodes_online + randint(-1, 1));

      // trend slight movement
      s.trendVals = s.trendVals.map((v,i)=> Math.max(60, Math.round(v + rand(-3, 4))));
      
      // abnormal trend: keep a clear downward slope
      const nAbn = s.trendAbnVals.length;
      let baseAbn = Math.max(10, Math.round(s.trendAbnVals[0] + rand(-1,1) - randint(0,1)));
      const abn = [];
      for(let i=0;i<nAbn;i++){
        if(i>0){ baseAbn = Math.max(0, baseAbn - randint(1,3)); }
        const v = Math.max(0, Math.round(baseAbn + rand(-1,1)));
        abn.push(v);
      }
      // enforce strictly non-increasing
      for(let i=1;i<nAbn;i++){ abn[i] = Math.min(abn[i], Math.max(0, abn[i-1]-randint(0,1))); }
      s.trendAbnVals = abn;

      // small charts wiggle
      s.aVals = s.aVals.map(v=> Math.max(0, v + randint(-1, 1)));
      const jitter = (arr, j)=>{
        const tmp = arr.map(v=> Math.max(1, v + rand(-j, j)));
        const sum = tmp.reduce((a,b)=>a+b,0);
        return tmp.map(v=> v/sum*100);
      };
      s.bPerc = jitter(s.bPerc, 0.6);
      s.cPerc = jitter(s.cPerc, 0.6);
      s.dVals = s.dVals.map(v=> Math.max(0, v + randint(-1, 1)));

      // bottom summary wiggle
      // 细粒度任务：轻微波动，保持递减趋势
      if (Array.isArray(s.fineTaskVals)) {
        s.fineTaskVals = s.fineTaskVals.map((v,i)=> Math.max(0, Math.round(v + rand(-1.2, 1.6) - i*0.05)));
      }
      s.objVals = s.objVals.map(v=> Math.max(0, Math.round(v + rand(-1.2, 1.4))));
      s.modePerc = jitter(s.modePerc, 0.4);

      // pipe
      s.pipe.collectLag = Math.max(0, s.pipe.collectLag + randint(-1, 1));
      s.pipe.cleanRate = Math.max(80, Math.min(99.9, s.pipe.cleanRate + rand(-0.08, 0.06)));
      s.pipe.processP95 = Math.max(1, s.pipe.processP95 + randint(-1, 1));
      s.pipe.trainGpu = Math.max(0, Math.min(100, s.pipe.trainGpu + randint(-2, 2)));
      s.pipe.evalSr = Math.max(60, Math.min(99.9, s.pipe.evalSr + rand(-0.12, 0.10)));
      s.pipe.deployOnline = Math.max(1, s.pipe.deployOnline + randint(-1, 1));
      s.pipe.feedbackRate = Math.max(0, Math.min(100, s.pipe.feedbackRate + randint(-1, 1)));
      return s;
    }

    function renderTrend(days, vals, abnVals){
      cTrend.setOption({
        backgroundColor:'transparent',
        color: [COLORS.cyan, COLORS.orange],
        tooltip: {
          trigger:'axis',
          borderColor:'rgba(66,227,255,.35)',
          backgroundColor:'rgba(5,12,30,.92)',
          borderWidth: 1.5,
          extraCssText:'backdrop-filter: blur(8px); -webkit-backdrop-filter: blur(8px);',
          textStyle:{ color:'#fff' }
        },
        grid: { left: 42, right: 16, top: 22, bottom: 28 },
        xAxis: { type:'category', data: days, ...baseAxis },
        yAxis: { type:'value', ...baseAxis },
        series: [
          {
            name:'采集量',
            type:'line',
            smooth:true,
            symbol:'circle',
            symbolSize: 7,
            lineStyle:{ width:3, shadowColor:'rgba(69,225,255,.18)', shadowBlur:12 },
            areaStyle:{ opacity:.12 },
            data: vals
          },
          {
            name:'异常量',
            type:'line',
            smooth:true,
            symbol:'circle',
            symbolSize: 6,
            lineStyle:{ width:2, type:'dashed', shadowColor:'rgba(255,153,90,.18)', shadowBlur:10 },
            areaStyle:{ opacity: 0 },
            data: abnVals
          }
        ]
      }, true);
    }

    function renderHeatmap(chart, scenes, robots, matrix){
      const data = [];
      let maxV = 0;
      for(let r=0;r<robots.length;r++){
        for(let c=0;c<scenes.length;c++){
          const v = (matrix[r] && matrix[r][c] != null) ? +matrix[r][c] : 0;
          maxV = Math.max(maxV, v);
          data.push([c, r, v]);
        }
      }
      chart.setOption({
        backgroundColor:'transparent',
        tooltip:{
          position:'top',
          borderColor:'rgba(66,227,255,.35)',
          backgroundColor:'rgba(5,12,30,.92)',
          borderWidth: 1.5,
          extraCssText:'backdrop-filter: blur(8px); -webkit-backdrop-filter: blur(8px);',
          textStyle:{ color:'#fff' },
          formatter:(p)=>{
            const s = scenes[p.data[0]];
            const r = robots[p.data[1]];
            const v = p.data[2];
            return `${r}<br/>${s}：<b>${v}</b>`;
          }
        },
        grid:{ left: 64, right: 18, top: 18, bottom: 56 },
        xAxis:{
          type:'category',
          data: scenes,
          ...baseAxis,
          z: 10,
          axisLabel:{ ...baseAxis.axisLabel, interval:0, fontSize:10, margin: 18 }
        },
        yAxis:{
          type:'category',
          data: robots,
          ...baseAxis,
          z: 10,
          axisLabel:{ ...baseAxis.axisLabel, fontSize:10 }
        },
        visualMap:{
          show: false,
          min: 0,
          max: Math.max(10, maxV),
          calculable: false,
          inRange: { color: ['rgba(255,255,255,.10)', COLORS.cyan, COLORS.teal, COLORS.yellow] },
          textStyle:{ color: COLORS.sub, fontWeight: 700 }
        },
        series:[{
          type:'heatmap',
          z: 2,
          data,
          label:{ show:false },
          itemStyle:{ borderColor:'rgba(255,255,255,.06)', borderWidth: 1 },
          emphasis:{ itemStyle:{ shadowBlur: 12, shadowColor:'rgba(69,225,255,.22)' } }
        }]
      }, true);
    }

    function renderHBar(chart, names, vals){
      chart.setOption({
        backgroundColor:'transparent',
        tooltip:{
          trigger:'axis',
          axisPointer:{ type:'shadow' },
          borderColor:'rgba(66,227,255,.35)',
          backgroundColor:'rgba(5,12,30,.92)',
          borderWidth: 1.5,
          extraCssText:'backdrop-filter: blur(8px); -webkit-backdrop-filter: blur(8px);',
          textStyle:{ color:'#fff' },
          formatter:(ps)=>{
            const p = ps && ps[0];
            if(!p) return '';
            return `${p.name}<br/>成功轨迹：<b>${formatNumber(p.value)}</b>`;
          }
        },
        grid:{ left: 92, right: 16, top: 16, bottom: 26 },
        xAxis:{ type:'value', ...baseAxis, axisLabel:{ ...baseAxis.axisLabel, fontSize: 10 } },
        yAxis:{
          type:'category',
          data: names.slice().reverse(),
          ...baseAxis,
          axisLabel:{ ...baseAxis.axisLabel, fontSize: 10 }
        },
        series:[{
          type:'bar',
          barWidth: 10,
          itemStyle:{
            borderRadius: 6,
            opacity: 0.88,
            color: (p)=> new echarts.graphic.LinearGradient(1,0,0,0,[
              { offset: 0, color: palette[(names.length-1-p.dataIndex) % palette.length] },
              { offset: 1, color: 'rgba(255,255,255,.10)' }
            ])
          },
          data: vals.slice().reverse()
        }]
      }, true);
    }

    function renderMiniBar(chart, names, vals){
      chart.setOption({
        backgroundColor:'transparent',
        tooltip: {
          trigger:'axis',
          axisPointer:{ type:'shadow' },
          borderColor:'rgba(66,227,255,.35)',
          backgroundColor:'rgba(5,12,30,.92)',
          borderWidth: 1.5,
          extraCssText:'backdrop-filter: blur(8px); -webkit-backdrop-filter: blur(8px);',
          textStyle:{ color:'#fff' }
        },
        grid: { left: 28, right: 10, top: 10, bottom: 26 },
        xAxis: { type:'category', data: names, ...baseAxis, axisLabel:{ ...baseAxis.axisLabel, interval:0, fontSize: 10 } },
        yAxis: { type:'value', ...baseAxis, axisLabel:{ ...baseAxis.axisLabel, fontSize: 10 } },
        series: [{
          type:'bar',
          barWidth:'58%',
          itemStyle:{
            borderRadius:[6,6,0,0],
            opacity: 0.86,
            color: (p)=> new echarts.graphic.LinearGradient(0,0,0,1,[
              { offset: 0, color: palette[p.dataIndex % palette.length] },
              { offset: 1, color: 'rgba(255,255,255,.10)' }
            ])
          },
          data: vals
        }]
      }, true);
    }

    function renderAll(prev, d){
      // Header stats
      document.getElementById('onlineNodes').textContent = String(d.nodes_online);

      // KPI cards
      flashIfChanged(document.getElementById('kpiTotal'), prev.total, d.total, (v)=>formatNumber(v));
      flashIfChanged(document.getElementById('kpiDuration'), prev.duration, d.duration, (v)=> formatNumber(Math.round(v)));
      flashIfChanged(document.getElementById('kpiQc'), prev.qc, d.qc, (v)=> Number(v).toFixed(1) + '%');
      flashIfChanged(document.getElementById('kpiErr'), prev.err, d.err, (v)=>formatNumber(v));
      // tracks in M+
      const fmtTracks = (v)=> {
        if (!Number.isFinite(Number(v))) return String(v);
        if (v >= 1000000) return (v/1000000).toFixed(2) + 'M+';
        if (v >= 1000) return (v/1000).toFixed(1) + 'K+';
        return String(v);
      };
      flashIfChanged(document.getElementById('kpiTracks'), prev.tracks, d.tracks, fmtTracks);
      flashIfChanged(document.getElementById('kpiRobotsOnline'), prev.robots_online, d.robots_online, (v)=>String(v));

      // Bottom summary
      renderHBar(cFine, d.fineTaskNames, d.fineTaskVals);
      renderMiniBar(cObj, d.objNames, d.objVals);
      cMode.setOption(donutCompactOption(d.modeNames, d.modePerc, ''), true);

      // Charts
      renderTrend(d.trendDays, d.trendVals, d.trendAbnVals);
      renderMiniBar(cA, d.aNames, d.aVals);
      cB.setOption(donutOption('', d.bNames, d.bPerc), true);
      cC.setOption(donutOption('', d.cNames, d.cPerc), true);
      renderMiniBar(cD, d.dNames, d.dVals);
    }

    // ====== Refresh loop ======
    let intervalSec = 30;
    let timer = null;
    let running = true;
    let state = JSON.parse(JSON.stringify(BASE));

    // debounce resize
    let resizeTimer = null;
    window.addEventListener('resize', ()=>{
      fitStage();
      clearTimeout(resizeTimer);
      resizeTimer = setTimeout(()=>{
        cTrend.resize(); cA.resize(); cB.resize(); cC.resize(); cD.resize(); cFine.resize(); cObj.resize(); cMode.resize();
      }, 120);
    });

    function start(){
      stop();
      timer = setInterval(()=>{
        if (!running) return;
        const prev = JSON.parse(JSON.stringify(state));
        state = nextState(state);
        renderAll(prev, state);
      }, intervalSec*1000);
    }
    function stop(){ if (timer){ clearInterval(timer); timer=null; } }

    document.getElementById('btnRefresh').addEventListener('click', ()=>{
      const prev = JSON.parse(JSON.stringify(state));
      state = nextState(state);
      renderAll(prev, state);
    });
    // initial
    renderAll(state, state);
    start();
  </script>
</body>
</html>
