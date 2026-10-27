<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0">
<title>实验一 · 数控车床编程与加工</title>
<style>
:root{
  --bg:#ffffff;--cyan:#1976d2;--cyan2:#1565c0;--txt:#1a202c;--dim:#4a5568;
  --line:rgba(25,118,210,.15);
}
*{margin:0;padding:0;box-sizing:border-box}
html,body{height:100%}
body{font-family:"PingFang SC","Microsoft YaHei",system-ui,-apple-system,sans-serif;
  background:var(--bg);color:var(--txt);overflow:hidden;-webkit-font-smoothing:antialiased;}

/* ============ 幻灯片 ============ */
.slide{position:fixed;inset:0;display:none;overflow-y:auto;padding:38px 56px 110px;
  background:
    radial-gradient(1200px 620px at 12% -12%,rgba(25,118,210,.05),transparent 62%),
    radial-gradient(900px 520px at 92% 112%,rgba(25,118,210,.03),transparent 60%),#ffffff;}
.slide.active{display:block;animation:fadeUp .45s ease both}
@keyframes fadeUp{from{opacity:0;transform:translateY(16px)}to{opacity:1;transform:none}}
.slide::-webkit-scrollbar{width:8px}
.slide::-webkit-scrollbar-thumb{background:rgba(25,118,210,.2);border-radius:4px}

/* ============ 标题 ============ */
.ch-head{display:flex;align-items:flex-end;gap:16px;margin-bottom:26px;flex-wrap:wrap}
.ch-num{font-size:52px;font-weight:800;line-height:.9;font-family:Georgia,serif;
  background:linear-gradient(180deg,#1976d2,#0d47a1);-webkit-background-clip:text;
  background-clip:text;color:transparent;}
.ch-title{font-size:30px;font-weight:700;letter-spacing:2px;color:#1a202c}
.ch-sub{font-size:14px;color:var(--dim);letter-spacing:3px;margin-bottom:6px}
.ch-line{height:2px;width:100%;margin-top:12px;border-radius:2px;
  background:linear-gradient(90deg,rgba(25,118,210,.6),rgba(25,118,210,0));}

/* ============ 封面 ============ */
#cover .wrap{min-height:100%;display:flex;flex-direction:column;justify-content:center;align-items:flex-start;gap:26px;position:relative}
.badge{font-size:13px;letter-spacing:6px;color:var(--cyan);border:1px solid var(--line);padding:7px 18px;border-radius:20px;background:#f8faff}
#cover h1{font-size:60px;font-weight:800;letter-spacing:6px;
  background:linear-gradient(120deg,#333333,#1565c0 55%,#1976d2);-webkit-background-clip:text;background-clip:text;color:transparent;}
#cover h2{font-size:30px;font-weight:500;letter-spacing:4px;color:#4a5568}
.meta{display:flex;flex-direction:column;gap:8px;font-size:16px;color:var(--dim);letter-spacing:2px;margin-top:10px}
.meta b{color:#2d3748;font-weight:500}
.start-btn{margin-top:26px;padding:14px 46px;border-radius:40px;cursor:pointer;font-size:17px;letter-spacing:4px;
  color:#fff;font-weight:700;border:none;background:linear-gradient(120deg,#1976d2,#1565c0);
  box-shadow:0 8px 24px rgba(25,118,210,.3);transition:.28s;font-family:inherit;}
.start-btn:hover{transform:translateY(-3px);box-shadow:0 14px 34px rgba(25,118,210,.4)}
.gear{position:absolute;right:40px;top:50%;transform:translateY(-50%);opacity:.08;width:360px;height:360px;animation:spin 40s linear infinite}
@keyframes spin{to{transform:translateY(-50%) rotate(360deg)}}

/* ============ 目录 ============ */
.toc-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(320px,1fr));gap:20px;margin-top:10px}
.toc-card{position:relative;padding:26px 26px 26px 96px;border-radius:16px;cursor:pointer;
  background:#f8faff;border:1px solid #e0e8f0;transition:.3s;overflow:hidden;box-shadow:0 2px 10px rgba(0,0,0,.02);}
.toc-card::after{content:"";position:absolute;left:0;top:0;bottom:0;width:3px;
  background:linear-gradient(180deg,var(--cyan),transparent);opacity:0;transition:.3s}
.toc-card:hover{transform:translateY(-5px);border-color:rgba(25,118,210,.5);
  box-shadow:0 12px 30px rgba(25,118,210,.08),0 0 0 1px rgba(25,118,210,.1) inset}
.toc-card:hover::after{opacity:1}
.toc-card .n{position:absolute;left:24px;top:24px;font-size:36px;font-weight:800;font-family:Georgia,serif;
  color:transparent;-webkit-text-stroke:1.4px rgba(25,118,210,.5)}
.toc-card h3{font-size:20px;font-weight:600;margin-bottom:8px;letter-spacing:1px;color:#2d3748}
.toc-card p{font-size:13.5px;color:var(--dim);line-height:1.7}

/* ============ 底部线性导航 ============ */
.nav-bar{position:fixed;bottom:24px;left:50%;transform:translateX(-50%);
  display:flex;gap:16px;z-index:99;
  background:rgba(255,255,255,.95);padding:10px 24px;border-radius:30px;
  box-shadow:0 4px 20px rgba(0,0,0,.08);border:1px solid rgba(0,0,0,.06);
  backdrop-filter:blur(10px);}
.nav-btn{padding:8px 24px;border-radius:20px;border:none;background:#1976d2;color:#fff;
  font-size:14px;cursor:pointer;font-family:inherit;transition:.2s;font-weight:500;letter-spacing:1px;}
.nav-btn:hover{background:#1565c0;}
.nav-btn:disabled{opacity:.4;cursor:not-allowed;}

/* ============ 3D 舞台 ============ */
.stage{perspective:2200px;perspective-origin:50% 46%;margin:0 -10px}
.stage3d{transform-style:preserve-3d;transform:rotateY(-9deg) rotateX(5deg);
  transition:transform .18s ease-out;filter:drop-shadow(0 24px 30px rgba(0,0,0,.12));}
.part{width:100%;height:auto;display:block}
.hint{text-align:center;font-size:13px;color:var(--dim);letter-spacing:2px;margin-bottom:18px}
.hint span{color:var(--cyan);font-weight:600}

/* ============ 结构卡片 ============ */
.seg-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(150px,1fr));gap:12px;margin-top:6px}
.seg{padding:14px 16px;border-radius:12px;background:#f8faff;border:1px solid #e0e8f0;transition:.25s}
.seg:hover{border-color:rgba(25,118,210,.5);background:#f0f7ff;transform:translateY(-3px)}
.seg .t{font-size:12px;color:var(--dim);letter-spacing:1px;margin-bottom:6px}
.seg .v{font-size:16px;font-weight:600;color:var(--cyan2)}

/* ============ 两栏 ============ */
.two-col{display:grid;grid-template-columns:1.15fr .85fr;gap:24px}
@media(max-width:980px){.two-col{grid-template-columns:1fr}}
.panel{background:#f8faff;border:1px solid #e0e8f0;border-radius:16px;padding:26px 28px;box-shadow:0 2px 10px rgba(0,0,0,.02);}
.panel h3{font-size:18px;color:var(--cyan2);letter-spacing:2px;margin-bottom:18px;display:flex;align-items:center;gap:10px}
.panel h3::before{content:"";width:4px;height:18px;border-radius:2px;background:linear-gradient(180deg,#1976d2,#1565c0)}
.aim{display:flex;gap:14px;margin-bottom:18px}
.aim:last-child{margin-bottom:0}
.aim .idx{flex:0 0 30px;height:30px;border-radius:50%;display:grid;place-items:center;font-size:14px;
  font-weight:700;color:#fff;background:linear-gradient(140deg,#1976d2,#1565c0);}
.aim p{font-size:15px;line-height:1.85;color:#2d3748}
.aim p b{color:var(--cyan2);font-weight:600}

.equip{display:flex;flex-direction:column;gap:14px}
.eq{display:flex;gap:14px;align-items:center;padding:14px 16px;border-radius:12px;
  background:#ffffff;border:1px solid #e0e8f0}
.eq .ico{width:42px;height:42px;flex:0 0 42px;border-radius:10px;display:grid;place-items:center;
  background:linear-gradient(140deg,rgba(25,118,210,.1),rgba(25,118,210,.02));
  border:1px solid rgba(25,118,210,.2);font-size:20px}
.eq .txt b{display:block;font-size:15px;color:#1a202c;margin-bottom:3px}
.eq .txt span{font-size:12.5px;color:var(--dim);letter-spacing:.5px}

/* ============ 安全卡片网格 ============ */
.safety-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(250px,1fr));gap:16px}
.safety-card{position:relative;border-radius:14px;overflow:hidden;cursor:pointer;
  background:#f8faff;border:1px solid #e0e8f0;transition:.3s;padding:18px 18px 16px;
  display:flex;flex-direction:column;gap:10px;box-shadow:0 2px 8px rgba(0,0,0,.02);}
.safety-card:hover{transform:translateY(-5px);border-color:rgba(25,118,210,.5);
  box-shadow:0 12px 28px rgba(25,118,210,.1),0 0 0 1px rgba(25,118,210,.1) inset}
.safety-card .sc-top{display:flex;align-items:center;gap:12px}
.safety-card .sc-no{flex:0 0 34px;height:34px;border-radius:9px;display:grid;place-items:center;
  font-size:13px;font-weight:700;color:#d84315;background:#fbe9e7;
  border:1px solid #ffab91}
.safety-card .sc-icon{font-size:24px;line-height:1}
.safety-card .sc-thumb{width:100%;height:78px;display:flex;align-items:center;justify-content:center;
  background:#ffffff;border-radius:8px;border:1px solid #f0f0f0}
.safety-card .sc-thumb svg{max-height:72px;width:auto;max-width:100%}
.safety-card .sc-title{font-size:15px;font-weight:600;color:#1a202c;letter-spacing:.5px}
.safety-card .sc-brief{font-size:12.5px;color:var(--dim);line-height:1.6;flex:1;
  display:-webkit-box;-webkit-line-clamp:2;-webkit-box-orient:vertical;overflow:hidden}
.safety-card .sc-more{font-size:12px;color:var(--cyan);letter-spacing:1px;opacity:.85;font-weight:500;}
.safety-card:hover .sc-more{opacity:1;letter-spacing:2px}

/* ============ 弹窗 ============ */
.modal{position:fixed;inset:0;z-index:200;display:none;place-items:center;
  background:rgba(0,0,0,.4);backdrop-filter:blur(4px);padding:30px;}
.modal.on{display:grid;animation:fadeIn .25s ease both}
@keyframes fadeIn{from{opacity:0}to{opacity:1}}
.modal-box{width:min(620px,92vw);max-height:88vh;overflow-y:auto;border-radius:20px;padding:26px 28px 24px;
  background:#ffffff;border:1px solid #e0e8f0;box-shadow:0 20px 60px rgba(0,0,0,.15);
  animation:pop .3s cubic-bezier(.2,.9,.3,1.3) both;}
@keyframes pop{from{transform:scale(.92);opacity:0}to{transform:scale(1);opacity:1}}
.modal-img{background:#f8faff;
  border-radius:14px;padding:10px;border:1px solid #e0e8f0;margin-bottom:16px;
  min-height:130px;display:flex;align-items:center;justify-content:center}
.modal-img svg{width:100%;height:auto;display:block;max-height:46vh}
.modal-emoji{font-size:96px;line-height:1;text-align:center;padding:24px 0}
.modal-title{font-size:21px;font-weight:700;color:var(--cyan2);letter-spacing:2px;margin-bottom:10px}
.modal-desc{font-size:14.5px;line-height:1.9;color:#2d3748}
.modal-desc b{color:#d84315}
.modal-desc em{font-style:normal;color:var(--cyan2)}
.modal-close{margin-top:20px;padding:10px 32px;border-radius:30px;cursor:pointer;font-family:inherit;
  font-size:14px;letter-spacing:2px;color:var(--cyan);background:rgba(25,118,210,.08);
  border:1px solid rgba(25,118,210,.3);transition:.25s;font-weight:500;}
.modal-close:hover{background:rgba(25,118,210,.15)}

/* ============ 表格 ============ */
table{width:100%;border-collapse:collapse;font-size:14.5px}
th,td{padding:13px 16px;text-align:left;border-bottom:1px solid #e0e8f0}
th{color:var(--cyan2);font-weight:600;letter-spacing:1.5px;font-size:13.5px;background:#f0f7ff}
td{color:#2d3748}
tr:last-child td{border-bottom:none}
.code{font-family:Consolas,Monaco,monospace;color:#c62828;background:#ffebee;
  padding:2px 8px;border-radius:5px;font-size:13.5px;display:inline-block;margin:2px 4px 2px 0}

/* ================= 工艺路线交互 ================= */
.route-wrap{display:grid;grid-template-columns:1fr 1.15fr;gap:22px;align-items:start}
@media(max-width:980px){.route-wrap{grid-template-columns:1fr}}

.route-panel{padding:20px}
.route-list{display:flex;flex-direction:column;gap:8px}
.route-item{display:flex;align-items:center;gap:14px;padding:12px 14px;border-radius:12px;cursor:pointer;
  background:#f8faff;border:1px solid #e0e8f0;transition:.25s;position:relative;overflow:hidden}
.route-item:hover{border-color:rgba(25,118,210,.4);background:#f0f7ff;transform:translateX(4px)}
.route-item.active{background:linear-gradient(100deg,rgba(25,118,210,.12),rgba(25,118,210,.03));
  border-color:rgba(25,118,210,.6);box-shadow:0 0 0 1px rgba(25,118,210,.15) inset,0 4px 12px rgba(0,0,0,.05)}
.route-item.active::before{content:"";position:absolute;left:0;top:0;bottom:0;width:3px;
  background:linear-gradient(180deg,#1976d2,#1565c0)}
.ri-no{flex:0 0 32px;height:32px;border-radius:9px;display:grid;place-items:center;font-size:13px;font-weight:700;
  color:#718096;background:#fff;border:1px solid #cbd5e0;transition:.25s}
.route-item.active .ri-no{color:#fff;background:#1976d2;
  border-color:transparent;box-shadow:0 2px 8px rgba(25,118,210,.3)}
.ri-body{flex:1;min-width:0}
.ri-title{font-size:15px;font-weight:600;color:#2d3748;margin-bottom:2px;transition:.25s}
.ri-desc{font-size:12.5px;color:#718096;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.route-item.active .ri-title{color:var(--cyan2)}
.ri-arrow{font-size:18px;color:#cbd5e0;transition:.25s}
.route-item.active .ri-arrow{color:#1976d2;transform:translateX(3px)}

.route-nav{display:flex;align-items:center;justify-content:space-between;margin-top:16px;gap:10px}
.rnav-btn{flex:1;padding:10px 0;border-radius:10px;cursor:pointer;font-family:inherit;font-size:14px;
  color:var(--cyan);background:rgba(25,118,210,.06);border:1px solid rgba(25,118,210,.3);transition:.25s;font-weight:500;}
.rnav-btn:hover:not(:disabled){background:rgba(25,118,210,.15);box-shadow:0 0 12px rgba(25,118,210,.15)}
.rnav-btn:disabled{opacity:.35;cursor:not-allowed}
.rnav-idx{font-size:14px;color:var(--dim);letter-spacing:1px;white-space:nowrap}
.rnav-idx b{color:var(--cyan);font-size:16px;font-weight:700}

.detail-panel{padding:24px 26px;min-height:430px}
.detail-head{display:flex;align-items:center;gap:16px;margin-bottom:20px;padding-bottom:18px;
  border-bottom:1px solid #e0e8f0}
.detail-icon{width:52px;height:52px;flex:0 0 52px;border-radius:14px;display:grid;place-items:center;font-size:26px;
  background:linear-gradient(140deg,rgba(25,118,210,.1),rgba(25,118,210,.02));
  border:1px solid rgba(25,118,210,.2)}
.detail-step{font-size:12px;color:var(--dim);letter-spacing:2px;margin-bottom:4px}
.detail-title{font-size:22px;font-weight:700;color:var(--cyan2);letter-spacing:1px}
.detail-body{animation:detailIn .35s ease both}
@keyframes detailIn{from{opacity:0;transform:translateY(8px)}to{opacity:1;transform:none}}
.detail-lead{font-size:14.5px;line-height:1.85;color:#2d3748;margin-bottom:18px}
.param-grid{display:grid;grid-template-columns:1fr 1fr;gap:10px;margin-bottom:16px}
@media(max-width:560px){.param-grid{grid-template-columns:1fr}}
.param{padding:10px 14px;border-radius:10px;background:#ffffff;border:1px solid #e2e8f0}
.param .pk{display:block;font-size:11.5px;color:#718096;letter-spacing:1px;margin-bottom:5px}
.param .pv{font-size:14px;font-weight:600;color:var(--cyan2);line-height:1.5}
.tip-box{padding:13px 16px;border-radius:10px;font-size:13.5px;line-height:1.8;
  color:#d84315;background:#fbe9e7;border:1px solid #ffab91}
.tip-box b{color:#d84315}

.quick-tags{display:flex;flex-wrap:wrap;gap:10px;margin-top:20px}
.quick-tags span{font-size:12.5px;color:var(--cyan2);padding:7px 14px;border-radius:20px;
  background:#f0f7ff;border:1px solid rgba(25,118,210,.15);letter-spacing:.5px}

/* ============ 加工模拟器 ============ */
.sim{display:grid;grid-template-columns:1.05fr .95fr;gap:22px;align-items:start}
@media(max-width:1000px){.sim{grid-template-columns:1fr}}
.sim-panel{background:#f8faff;border:1px solid #e0e8f0;border-radius:18px;padding:22px 24px;box-shadow:0 2px 10px rgba(0,0,0,.02);}
.sim-head{display:flex;justify-content:space-between;align-items:center;margin-bottom:14px}
.sim-head .ttl{font-size:16px;color:var(--cyan2);letter-spacing:2px;font-weight:600}
.sim-count{font-size:14px;color:var(--dim);letter-spacing:1px}
.sim-count b{color:var(--cyan);font-size:20px;font-weight:700}
.sim-bar{height:8px;border-radius:6px;background:#e2e8f0;overflow:hidden;margin-bottom:18px;
  border:1px solid #e2e8f0}
.sim-bar-fill{height:100%;width:0%;border-radius:6px;transition:width .45s cubic-bezier(.3,.9,.3,1);
  background:linear-gradient(90deg,#1976d2,#42a5f5,#66bb6a);box-shadow:0 0 10px rgba(25,118,210,.4)}

.step-item{display:flex;align-items:center;gap:14px;padding:13px 15px;border-radius:12px;margin-bottom:10px;
  cursor:pointer;background:#ffffff;border:1px solid #e2e8f0;
  transition:.25s;user-select:none;position:relative;overflow:hidden}
.step-item:hover{border-color:rgba(25,118,210,.4);transform:translateX(5px);box-shadow:0 2px 8px rgba(0,0,0,.04)}
.step-item .st-dot{flex:0 0 28px;height:28px;border-radius:50%;display:grid;place-items:center;font-size:13px;
  font-weight:700;color:#718096;background:#f0f4f8;border:1px solid #cbd5e0;
  transition:.3s}
.step-item .st-body{flex:1;min-width:0}
.step-item .st-title{font-size:15px;font-weight:600;color:#2d3748;margin-bottom:3px;transition:.3s}
.step-item .st-desc{font-size:12.5px;color:#718096;line-height:1.6;transition:.3s}
.step-item .st-ck{flex:0 0 24px;font-size:16px;font-weight:700;color:transparent;transition:.3s;text-align:center}
.step-item.done{background:#e8f5e9;border-color:#a5d6a7;
  box-shadow:0 0 0 1px rgba(129,199,132,.3) inset,0 4px 12px rgba(0,0,0,.04)}
.step-item.done .st-dot{background:#4caf50;color:#fff;border-color:transparent;
  box-shadow:0 2px 8px rgba(76,175,80,.4)}
.step-item.done .st-title{color:#2e7d32}
.step-item.done .st-ck{color:#4caf50;text-shadow:0 0 8px rgba(76,175,80,.5)}
.step-item.done::after{content:"";position:absolute;left:0;top:0;bottom:0;width:3px;
  background:linear-gradient(180deg,#4caf50,transparent)}

.sim-actions{display:flex;gap:12px;margin-top:16px}
.sim-actions button{flex:1;padding:11px 0;border-radius:10px;cursor:pointer;font-family:inherit;
  font-size:14px;letter-spacing:2px;color:var(--cyan);background:rgba(25,118,210,.06);
  border:1px solid rgba(25,118,210,.3);transition:.25s;font-weight:500;}
.sim-actions button:hover{background:rgba(25,118,210,.15);box-shadow:0 0 12px rgba(25,118,210,.15)}
.sim-actions button:disabled{opacity:.4;cursor:not-allowed}

.viz-wrap{border-radius:14px;background:#ffffff;
  border:1px solid #e2e8f0;padding:10px 8px}
#vizPart{width:100%;height:auto;display:block}
.viz-layer{opacity:0;transition:opacity .55s ease}
.viz-layer.on{opacity:1}
.viz-status{margin-top:14px;padding:12px 16px;border-radius:11px;font-size:14px;letter-spacing:1px;
  color:var(--cyan2);background:#e3f2fd;border:1px solid #90caf9;line-height:1.7;font-weight:500;}
.viz-status.ok{color:#2e7d32;background:#e8f5e9;border-color:#a5d6a7}
.viz-status b{color:#fff}

/* ============ 结果 ============ */
.res-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(260px,1fr));gap:16px}
.res{padding:22px 24px;border-radius:15px;position:relative;overflow:hidden;
  background:#e8f5e9;border:1px solid #a5d6a7}
.res::before{content:"✓";position:absolute;right:18px;top:12px;font-size:34px;font-weight:800;color:rgba(76,175,80,.2)}
.res .k{font-size:13px;color:#2e7d32;letter-spacing:1.5px;margin-bottom:10px}
.res .v{font-size:22px;font-weight:700;color:#1b5e20;margin-bottom:8px;letter-spacing:1px}
.res .d{font-size:13px;color:#4a5568;line-height:1.7}

.ana{display:flex;gap:16px;padding:20px 22px;border-radius:14px;margin-bottom:14px;
  background:#f8faff;border:1px solid #e0e8f0;transition:.25s}
.ana:hover{border-color:rgba(25,118,210,.4);transform:translateX(6px)}
.ana .tag{flex:0 0 auto;padding:5px 14px;height:fit-content;border-radius:8px;font-size:13px;
  color:#fff;font-weight:700;letter-spacing:1px;background:linear-gradient(140deg,#1976d2,#1565c0)}
.ana p{font-size:14.5px;line-height:1.85;color:#2d3748}
.ana p b{color:var(--cyan2)}

.final{margin-top:18px;padding:24px 28px;border-radius:16px;
  background:#e3f2fd;border:1px solid #90caf9;font-size:16px;line-height:1.95;color:var(--cyan2)}
.final b{color:#0d47a1}
</style>
</head>
<body>

<!-- 公共箭头定义 -->
<svg width="0" height="0" style="position:absolute">
  <defs>
    <marker id="arw" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse">
      <path d="M0,0 L10,5 L0,10 L2.6,5 Z" fill="#ff5a5a"/>
    </marker>
  </defs>
</svg>

<!-- ==================== 封面 ==================== -->
<section class="slide active" id="cover">
  <div class="wrap">
    <div class="badge">数 控 技 术</div>
    <h1>实验一</h1>
    <h2>数控车床编程与加工</h2>
    <div class="meta">
      <div><b>河南信息科技学院</b></div>
      <div><b>智能制造学院</b> / 综合实验组</div>
      <div>主讲人：<b>任燕</b></div>
      <div style="margin-top:6px;font-size:14px;color:#718096">实验内容：外圆加工 · 槽的加工 · 螺纹加工</div>
    </div>
    <button class="start-btn" onclick="go('toc')">进 入 课 件</button>
  </div>
  <svg class="gear" viewBox="0 0 200 200" fill="none" stroke="#1976d2" stroke-width="6">
    <circle cx="100" cy="100" r="46"/><circle cx="100" cy="100" r="20"/>
    <g stroke-width="14" stroke-linecap="round">
      <path d="M100 30v-16M100 170v16M30 100H14M170 100h16M50.5 50.5 39 39M149.5 149.5 161 161M50.5 149.5 39 161M149.5 50.5 161 39"/>
    </g>
  </svg>
</section>

<!-- ==================== 目录 ==================== -->
<section class="slide" id="toc">
  <div class="ch-head">
    <div class="ch-title"> 目录</div>
  </div>
  <div class="ch-line" style="margin-bottom:26px"></div>
  <div class="toc-grid">
    <div class="toc-card" onclick="go('ch1')"><div class="n">01</div><h1>零件图与结构分析</h1></div>
    <div class="toc-card" onclick="go('ch2')"><div class="n">02</div><h1>实验目的与设备</h1></div>
    <div class="toc-card" onclick="go('ch3')"><div class="n">03</div><h1>安全操作规程</h1></div>
    <div class="toc-card" onclick="go('ch4')"><div class="n">04</div><h1>工艺分析与实验方案</h1></div>
    <div class="toc-card" onclick="go('ch5')"><div class="n">05</div><h1>加工过程与对刀</h1></div>
    <div class="toc-card" onclick="go('ch6')"><div class="n">06</div><h1>实验结果与分析</h1></div>
  </div>
</section>

<!-- ==================== 第1章 零件图 ==================== -->
<section class="slide" id="ch1">
  <div class="ch-head">
    <div class="ch-num">01</div>
    <div class="ch-title">零件图与结构分析</div>
  </div>
  <div class="ch-line" style="margin-bottom:20px"></div>

  <div class="stage" id="stage">
    <div class="stage3d" id="stage3d">
      <svg class="part" viewBox="0 0 1200 580" xmlns="http://www.w3.org/2000/svg">
        <defs>
          <linearGradient id="steel" x1="0" y1="0" x2="0" y2="1">
            <stop offset="0%" stop-color="#13202c"/><stop offset="11%" stop-color="#5b7c96"/>
            <stop offset="28%" stop-color="#c6dff2"/><stop offset="42%" stop-color="#f2faff"/>
            <stop offset="58%" stop-color="#a9c6dd"/><stop offset="79%" stop-color="#45606f"/>
            <stop offset="100%" stop-color="#0d1720"/>
          </linearGradient>
          <linearGradient id="steelDark" x1="0" y1="0" x2="0" y2="1">
            <stop offset="0%" stop-color="#08111a"/><stop offset="16%" stop-color="#3a5468"/>
            <stop offset="42%" stop-color="#7d9db4"/><stop offset="62%" stop-color="#5c7a91"/>
            <stop offset="100%" stop-color="#070e15"/>
          </linearGradient>
          <radialGradient id="capFace" cx="0.36" cy="0.30" r="0.82">
            <stop offset="0%" stop-color="#ffffff"/><stop offset="38%" stop-color="#a9c9e0"/>
            <stop offset="100%" stop-color="#1d2f3d"/>
          </radialGradient>
          <linearGradient id="ballGrad" x1="0.1" y1="0" x2="0.7" y2="1">
            <stop offset="0%" stop-color="#f2fbff"/><stop offset="32%" stop-color="#a8c6dd"/>
            <stop offset="68%" stop-color="#4d6a80"/><stop offset="100%" stop-color="#101c26"/>
          </linearGradient>
          <style>
            .dim{stroke:#1976d2;stroke-width:1.4;fill:none}
            .ext{stroke:rgba(25,118,210,.5);stroke-width:1;fill:none;stroke-dasharray:5 4}
            .dt{fill:#1565c0;font-size:19px;font-family:"Microsoft YaHei",sans-serif;paint-order:stroke;stroke:#ffffff;stroke-width:4px;stroke-linejoin:round}
            .dts{fill:#1565c0;font-size:16px;font-family:"Microsoft YaHei",sans-serif;paint-order:stroke;stroke:#ffffff;stroke-width:4px;stroke-linejoin:round}
            .thread{stroke:rgba(0,0,0,.25);stroke-width:1.6}
          </style>
        </defs>

        <line x1="120" y1="240" x2="1060" y2="240" stroke="rgba(25,118,210,.4)" stroke-width="1.2" stroke-dasharray="26 7 5 7"/>

        <rect x="170" y="90" width="96" height="300" fill="url(#steel)"/>
        <ellipse cx="170" cy="240" rx="16" ry="150" fill="url(#capFace)"/>
        <path d="M266,90 C274,90 278,99 278,114 L278,366 C278,381 274,390 266,390 Z" fill="url(#steel)"/>
        <polygon points="278,114 506,150 506,330 278,366" fill="url(#steel)"/>
        <rect x="506" y="150" width="228" height="180" fill="url(#steel)"/>
        <rect x="734" y="162" width="24" height="156" fill="url(#steelDark)"/>
        <rect x="758" y="150" width="171" height="180" fill="url(#steel)"/>
        <g class="thread">
          <line x1="766" y1="150" x2="766" y2="330"/><line x1="778" y1="150" x2="778" y2="330"/>
          <line x1="790" y1="150" x2="790" y2="330"/><line x1="802" y1="150" x2="802" y2="330"/>
          <line x1="814" y1="150" x2="814" y2="330"/><line x1="826" y1="150" x2="826" y2="330"/>
          <line x1="838" y1="150" x2="838" y2="330"/><line x1="850" y1="150" x2="850" y2="330"/>
          <line x1="862" y1="150" x2="862" y2="330"/><line x1="874" y1="150" x2="874" y2="330"/>
          <line x1="886" y1="150" x2="886" y2="330"/><line x1="898" y1="150" x2="898" y2="330"/>
          <line x1="910" y1="150" x2="910" y2="330"/><line x1="922" y1="150" x2="922" y2="330"/>
        </g>
        <polygon points="929,150 938,159 938,321 929,330" fill="url(#steel)"/>
        <path d="M938,159 L938,186 C980,188 1010,208 1010,240 C1010,272 980,292 938,294 L938,321 Z" fill="url(#ballGrad)"/>

        <line class="dim" x1="152" y1="90" x2="152" y2="390" marker-start="url(#arw)" marker-end="url(#arw)"/>
        <line class="ext" x1="152" y1="90" x2="170" y2="90"/><line class="ext" x1="152" y1="390" x2="170" y2="390"/>
        <text class="dt" x="142" y="247" text-anchor="end">φ50</text>

        <line class="dim" x1="340" y1="124" x2="340" y2="356" marker-start="url(#arw)" marker-end="url(#arw)"/>
        <text class="dt" x="340" y="247" text-anchor="middle">φ42±0.05</text>

        <line class="dim" x1="620" y1="150" x2="620" y2="330" marker-start="url(#arw)" marker-end="url(#arw)"/>
        <text class="dt" x="620" y="247" text-anchor="middle">φ30</text>

        <line class="ext" x1="746" y1="318" x2="746" y2="412"/>
        <text class="dts" x="746" y="432" text-anchor="middle">φ26</text>

        <line class="dim" x1="880" y1="140" x2="880" y2="150" marker-end="url(#arw)"/>
        <text class="dts" x="880" y="128" text-anchor="middle">M30×2</text>

        <line class="dim" x1="972" y1="118" x2="936" y2="152" marker-end="url(#arw)"/>
        <text class="dts" x="978" y="112" text-anchor="start">C1.5</text>

        <line class="dim" x1="1060" y1="176" x2="1004" y2="212" marker-end="url(#arw)"/>
        <text class="dts" x="1066" y="170" text-anchor="start">R9</text>

        <line class="dim" x1="238" y1="118" x2="270" y2="94" marker-end="url(#arw)"/>
        <text class="dts" x="232" y="114" text-anchor="end">R4</text>

        <line class="ext" x1="170" y1="396" x2="170" y2="478"/><line class="ext" x1="278" y1="396" x2="278" y2="478"/>
        <line class="ext" x1="506" y1="396" x2="506" y2="478"/><line class="ext" x1="734" y1="396" x2="734" y2="478"/>
        <line class="ext" x1="758" y1="396" x2="758" y2="478"/><line class="ext" x1="938" y1="340" x2="938" y2="478"/>
        <line class="ext" x1="1010" y1="330" x2="1010" y2="478"/>

        <line class="dim" x1="170" y1="470" x2="278" y2="470" marker-start="url(#arw)" marker-end="url(#arw)"/>
        <text class="dts" x="224" y="460" text-anchor="middle">18</text>
        <line class="dim" x1="278" y1="470" x2="506" y2="470" marker-start="url(#arw)" marker-end="url(#arw)"/>
        <text class="dts" x="392" y="460" text-anchor="middle">38±0.03</text>
        <line class="dim" x1="734" y1="470" x2="758" y2="470" marker-start="url(#arw)" marker-end="url(#arw)"/>
        <text class="dts" x="746" y="460" text-anchor="middle">4</text>
        <line class="dim" x1="758" y1="470" x2="938" y2="470" marker-start="url(#arw)" marker-end="url(#arw)"/>
        <text class="dts" x="848" y="460" text-anchor="middle">30</text>
        <line class="dim" x1="938" y1="470" x2="1010" y2="470" marker-start="url(#arw)" marker-end="url(#arw)"/>
        <text class="dts" x="974" y="460" text-anchor="middle">12</text>

        <line class="dim" x1="170" y1="524" x2="1010" y2="524" marker-start="url(#arw)" marker-end="url(#arw)"/>
        <text class="dt" x="590" y="514" text-anchor="middle">140</text>
      </svg>
    </div>
  </div>

  <div class="seg-grid">
    <div class="seg"><div class="t">左端圆柱</div><div class="v">φ50 × 18</div></div>
    <div class="seg"><div class="t">圆角过渡</div><div class="v">R4</div></div>
    <div class="seg"><div class="t">圆锥段</div><div class="v">φ42±0.05 ➡ φ30，38±0.03</div></div>
    <div class="seg"><div class="t">中间圆柱</div><div class="v">φ30</div></div>
    <div class="seg"><div class="t">退刀槽</div><div class="v">φ26 × 4</div></div>
    <div class="seg"><div class="t">螺纹段</div><div class="v">M30×2 × 30，C1.5</div></div>
    <div class="seg"><div class="t">球头</div><div class="v">R9 × 12</div></div>
    <div class="seg"><div class="t">总长</div><div class="v">140 mm</div></div>
  </div>
</section>

<!-- ==================== 第2章 目的与设备 ==================== -->
<section class="slide" id="ch2">
  <div class="ch-head">
    <div class="ch-num">02</div>
    <div class="ch-title">实验目的与设备</div>
  </div>
  <div class="ch-line" style="margin-bottom:26px"></div>
  <div class="two-col">
    <div class="panel">
      <h3>实验目的</h3>
      <div class="aim"><div class="idx">1</div><p>熟悉 <b>FANUC 0i-TF Plus</b> 数控系统的基本操作，掌握 <b>CKA6140I 数控车床</b>的手动操作、程序输入与编辑、自动加工等基本功能。</p></div>
      <div class="aim"><div class="idx">2</div><p>掌握数控车床<b>坐标系的建立方法</b>，理解<b>机床坐标系、工件坐标系及对刀原理</b>。</p></div>
      <div class="aim"><div class="idx">3</div><p>通过实际加工，了解数控车削加工的<b>工艺特点、刀具选择、切削参数设定及尺寸精度控制方法</b>。</p></div>
    </div>
    <div class="panel">
      <h3>实验设备与内容</h3>
      <div class="equip">
        <div class="eq"><div class="ico">⚙️</div><div class="txt"><b>CKA6140I 数控车床</b><span>实验用主机设备</span></div></div>
        <div class="eq"><div class="ico">🖥️</div><div class="txt"><b>FANUC 0i-TF Plus 数控系统</b><span>程序输入 / 编辑 / 自动加工</span></div></div>
        <div class="eq"><div class="ico">🔩</div><div class="txt"><b>外圆加工 · 槽的加工 · 螺纹加工</b><span>本次实验三项核心加工内容</span></div></div>
        <div class="eq"><div class="ico">📏</div><div class="txt"><b>量具与螺纹环规</b><span>游标卡尺 / 千分尺 / 螺纹环规</span></div></div>
      </div>
    </div>
  </div>
</section>

<!-- ==================== 第3章 安全操作规程 ==================== -->
<section class="slide" id="ch3">
  <div class="ch-head">
    <div class="ch-num">03</div>
    <div class="ch-title">安全操作规程</div>
  </div>
  <div class="ch-line" style="margin-bottom:22px"></div>

  <div class="hint" style="text-align:left;margin-bottom:16px">
    ▍ 点击任意卡片放大查看详细要点与图示
  </div>

  <div class="safety-grid" id="safetyGrid"></div>
</section>

<!-- ==================== 第4章 工艺方案 ==================== -->
<section class="slide" id="ch4">
  <div class="ch-head">
    <div class="ch-num">04</div>
    <div class="ch-title">工艺分析与实验方案</div>
  </div>
  <div class="ch-line" style="margin-bottom:22px"></div>

  <div class="hint" style="text-align:left;margin-bottom:16px">
    ▍ 点击左侧工序查看详细参数，或用下方按钮顺序浏览七步工艺路线
  </div>

  <div class="route-wrap">
    <div class="panel route-panel">
      <div class="route-list" id="routeList"></div>
      <div class="route-nav">
        <button id="prevStep" class="rnav-btn">← 上一步</button>
        <span class="rnav-idx" id="stepProgress"><b>1</b> / 7</span>
        <button id="nextStep" class="rnav-btn">下一步 →</button>
      </div>
    </div>

    <div class="panel detail-panel">
      <div class="detail-head">
        <div class="detail-icon" id="dIcon">🔧</div>
        <div>
          <div class="detail-step" id="dStep">第 1 步</div>
          <div class="detail-title" id="dTitle">加工准备</div>
        </div>
      </div>
      <div class="detail-body" id="dBody"></div>
    </div>
  </div>

  <div class="quick-tags">
    <span>装夹：三爪自定心卡盘夹 φ50</span>
    <span>工序：先粗后精 · 先外圆后切槽再螺纹</span>
    <span>刀具：外圆刀 / 切槽刀 / 螺纹刀</span>
    <span>精度：对刀 + 刀补修正，保 φ42±0.05</span>
    <span>螺纹：6 刀递减，留引入距离防乱扣</span>
  </div>
</section>

<!-- ==================== 第5章 加工过程 ==================== -->
<section class="slide" id="ch5">
  <div class="ch-head">
    <div class="ch-num">05</div>
    <div class="ch-title">加工过程与对刀</div>
  </div>
  <div class="ch-line" style="margin-bottom:22px"></div>
  <div class="hint" style="text-align:left;margin-bottom:16px">
    ▍ 点击左侧工序可 <span>标记完成</span>，或点击「自动演示」按顺序走完整个流程
  </div>

  <div class="sim">
    <div class="sim-panel">
      <div class="sim-head">
        <div class="ttl">实训工序流程</div>
        <div class="sim-count"><b id="doneCount">0</b> / 7</div>
      </div>
      <div class="sim-bar"><div class="sim-bar-fill" id="simBar"></div></div>

      <div id="stepList">
        <div class="step-item" data-i="0">
          <div class="st-dot">1</div>
          <div class="st-body"><div class="st-title">加工准备</div>
            <div class="st-desc">开机回零 → 装夹工件 → 装刀 → 输入程序</div></div>
          <div class="st-ck">✓</div>
        </div>
        <div class="step-item" data-i="1">
          <div class="st-dot">2</div>
          <div class="st-body"><div class="st-title">对刀操作</div>
            <div class="st-desc">试切对刀，建立工件坐标系，输入刀补值</div></div>
          <div class="st-ck">✓</div>
        </div>
        <div class="step-item" data-i="2">
          <div class="st-dot">3</div>
          <div class="st-body"><div class="st-title">粗加工</div>
            <div class="st-desc">G71 循环粗车外圆，留精车余量 0.4 mm</div></div>
          <div class="st-ck">✓</div>
        </div>
        <div class="step-item" data-i="3">
          <div class="st-dot">4</div>
          <div class="st-body"><div class="st-title">精加工</div>
            <div class="st-desc">G70 循环精车各表面到尺寸</div></div>
          <div class="st-ck">✓</div>
        </div>
        <div class="step-item" data-i="4">
          <div class="st-dot">5</div>
          <div class="st-body"><div class="st-title">切退刀槽</div>
            <div class="st-desc">G01 切 φ26 退刀槽（宽 4 mm）</div></div>
          <div class="st-ck">✓</div>
        </div>
        <div class="step-item" data-i="5">
          <div class="st-dot">6</div>
          <div class="st-body"><div class="st-title">车螺纹</div>
            <div class="st-desc">G92 车 M30×2 螺纹，分 6 刀递减</div></div>
          <div class="st-ck">✓</div>
        </div>
        <div class="step-item" data-i="6">
          <div class="st-dot">7</div>
          <div class="st-body"><div class="st-title">测量检验</div>
            <div class="st-desc">加工后测量各尺寸，记录数据</div></div>
          <div class="st-ck">✓</div>
        </div>
      </div>

      <div class="sim-actions">
        <button id="autoBtn">▶ 自动演示</button>
        <button id="resetBtn">↺ 重置流程</button>
      </div>
    </div>

    <div class="sim-panel">
      <div class="sim-head"><div class="ttl">零件加工状态</div></div>
      <div class="viz-wrap">
        <svg id="vizPart" viewBox="0 0 400 220">
          <defs>
            <linearGradient id="vSteel" x1="0" y1="0" x2="0" y2="1">
              <stop offset="0%" stop-color="#2b4358"/><stop offset="35%" stop-color="#8fb9d4"/>
              <stop offset="52%" stop-color="#e6f5ff"/><stop offset="72%" stop-color="#7ea3bd"/>
              <stop offset="100%" stop-color="#1c2c3a"/>
            </linearGradient>
            <linearGradient id="vRough" x1="0" y1="0" x2="0" y2="1">
              <stop offset="0%" stop-color="#243a4d"/><stop offset="40%" stop-color="#5c819a"/>
              <stop offset="60%" stop-color="#7a9cb4"/><stop offset="100%" stop-color="#152430"/>
            </linearGradient>
            <linearGradient id="vThread" x1="0" y1="0" x2="0" y2="1">
              <stop offset="0%" stop-color="#2b4358"/><stop offset="38%" stop-color="#a3c9e2"/>
              <stop offset="55%" stop-color="#eaf7ff"/><stop offset="75%" stop-color="#88aec8"/>
              <stop offset="100%" stop-color="#1a2a37"/>
            </linearGradient>
          </defs>

          <line x1="20" y1="110" x2="385" y2="110" stroke="rgba(25,118,210,.3)" stroke-width="1" stroke-dasharray="18 6 4 6"/>

          <g id="gBlank" class="viz-layer on">
            <rect x="40" y="58" width="330" height="104" rx="3" fill="#e2e8f0" stroke="#cbd5e0" stroke-width="1.5"/>
          </g>
          <g id="gRough" class="viz-layer">
            <rect x="40" y="66" width="330" height="88" rx="2" fill="url(#vRough)"/>
          </g>
          <g id="gFinish" class="viz-layer">
            <path d="M40,85 L86,85 Q92,85 92,91 L92,95 L188,95 L278,95 L278,98 L290,98 L290,95
                     L342,95 L342,102 A8,8 0 0 1 342,118 L342,125 L290,125 L290,122 L278,122
                     L278,125 L188,125 L92,125 L92,129 Q92,135 86,135 L40,135 Z"
                  fill="url(#vSteel)" stroke="rgba(0,0,0,.2)" stroke-width="1.2"/>
          </g>
          <g id="gGroove" class="viz-layer">
            <rect x="278" y="98" width="12" height="24" fill="#94a3b8" stroke="#64748b" stroke-width="1"/>
          </g>
          <g id="gThread" class="viz-layer">
            <rect x="290" y="95" width="52" height="30" fill="url(#vThread)"/>
            <g stroke="rgba(0,0,0,.3)" stroke-width="1.3">
              <line x1="294" y1="95" x2="294" y2="125"/><line x1="300" y1="95" x2="300" y2="125"/>
              <line x1="306" y1="95" x2="306" y2="125"/><line x1="312" y1="95" x2="312" y2="125"/>
              <line x1="318" y1="95" x2="318" y2="125"/><line x1="324" y1="95" x2="324" y2="125"/>
              <line x1="330" y1="95" x2="330" y2="125"/><line x1="336" y1="95" x2="336" y2="125"/>
            </g>
          </g>
          <g id="gPass" class="viz-layer">
            <circle cx="205" cy="110" r="46" fill="none" stroke="#4caf50" stroke-width="3" opacity=".9"/>
            <circle cx="205" cy="110" r="40" fill="rgba(76,175,80,.08)"/>
            <path d="M185 110 L200 126 L228 94" fill="none" stroke="#4caf50" stroke-width="6"
                  stroke-linecap="round" stroke-linejoin="round"/>
            <text x="205" y="172" text-anchor="middle" fill="#2e7d32" font-size="18"
                  font-family="Microsoft YaHei" letter-spacing="6" font-weight="bold">合 格</text>
          </g>
        </svg>
      </div>
      <div class="viz-status" id="vizStatus">待加工 · 毛坯已装夹</div>
    </div>
  </div>

  <div class="two-col" style="grid-template-columns:1fr 1fr;align-items:start;margin-top:22px">
    <div class="panel">
      <h3>指令与工序对照</h3>
      <table>
        <tr><th>工序</th><th>指令</th><th>说明</th></tr>
        <tr><td>粗车外圆</td><td><span class="code">G71</span></td><td>复合循环粗车，留精车余量 0.4 mm</td></tr>
        <tr><td>精车</td><td><span class="code">G70</span></td><td>精车循环，各表面加工到尺寸</td></tr>
        <tr><td>切退刀槽</td><td><span class="code">G01</span></td><td>切 φ26 退刀槽，槽宽 4 mm</td></tr>
        <tr><td>车螺纹</td><td><span class="code">G92</span></td><td>车 M30×2 螺纹，分 6 刀递减</td></tr>
      </table>
    </div>
    <div class="panel">
      <h3>试切对刀原理</h3>
      <p style="font-size:14.5px;line-height:1.9;color:#2d3748;margin-bottom:14px">
        采用<b style="color:var(--cyan2)">试切法</b>对刀：刀具分别试切工件端面与外圆，记录机床当前坐标，
        将该坐标值输入对应刀补号中，系统即可自动建立<b style="color:var(--cyan2)">工件坐标系</b>。
      </p>
      <div style="display:grid;grid-template-columns:1fr 1fr;gap:12px">
        <div class="seg"><div class="t">Z 向对刀</div><div class="v">试切端面 → 输入 Z 值</div></div>
        <div class="seg"><div class="t">X 向对刀</div><div class="v">试切外圆 → 输入 X 值</div></div>
        <div class="seg"><div class="t">刀补号</div><div class="v">T0101 / T0202 / T0303</div></div>
        <div class="seg"><div class="t">检验</div><div class="v">首件试切测量后修正</div></div>
      </div>
    </div>
  </div>
</section>

<!-- ==================== 第6章 结果分析 ==================== -->
<section class="slide" id="ch6">
  <div class="ch-head">
    <div class="ch-num">06</div>
    <div class="ch-title">实验结果与分析</div>
  </div>
  <div class="ch-line" style="margin-bottom:24px"></div>

  <h3 style="font-size:17px;color:var(--cyan2);letter-spacing:2px;margin-bottom:14px">▍ 测量检验结果</h3>
  <div class="res-grid" style="margin-bottom:26px">
    <div class="res"><div class="k">φ42±0.05 mm 外圆</div><div class="v">实测 φ42.02 mm</div><div class="d">在公差范围内 ✓</div></div>
    <div class="res"><div class="k">39±0.03 mm 段长</div><div class="v">实测 39.02 mm</div><div class="d">在公差范围内 ✓</div></div>
    <div class="res"><div class="k">M30×2 外螺纹</div><div class="v">环规检测合格</div><div class="d">通规顺利旋入、止规不旋入 ✓</div></div>
  </div>

  <h3 style="font-size:17px;color:var(--cyan2);letter-spacing:2px;margin-bottom:14px">▍ 结果分析</h3>
  <div class="ana"><div class="tag">尺寸精度</div><p>对刀精度和刀补修正保证了 <b>φ42±0.05 mm</b> 公差；粗车后<b>冷却再精车</b>，有效减少热变形影响。</p></div>
  <div class="ana"><div class="tag">表面质量</div><p><b>提高转速、减小进给量</b>是改善表面粗糙度的关键；<b>刀尖圆弧半径越小</b>，表面越光整。</p></div>
  <div class="ana"><div class="tag">螺纹加工</div><p>分 <b>6 刀逐刀递减</b>切削，留足<b>引入距离</b>保证首尾螺距均匀，避免乱扣。</p></div>

  <div class="final">💡 <b>实验心得：</b>数控加工编程只是基础，<b>工艺分析、切削参数匹配、对刀精度和及时测量调整</b>，才是保证加工质量的关键。</div>
</section>

<!-- ==================== 底部导航 ==================== -->
<div class="nav-bar" id="navBar">
  <button class="nav-btn" id="prevPageBtn">◀ 上一页</button>
  <button class="nav-btn" id="nextPageBtn">下一页 ▶</button>
</div>

<!-- 放大查看弹窗 -->
<div class="modal" id="modal" onclick="if(event.target===this)closeModal()">
  <div class="modal-box">
    <div class="modal-img" id="modalImg"></div>
    <div class="modal-title" id="modalTitle"></div>
    <div class="modal-desc" id="modalDesc"></div>
    <button class="modal-close" onclick="closeModal()">关 闭</button>
  </div>
</div>

<script>
/* ================= 页面切换与线性导航 ================= */
const slides = Array.from(document.querySelectorAll('.slide'));
const slideIds = ['cover', 'toc', 'ch1', 'ch2', 'ch3', 'ch4', 'ch5', 'ch6'];
let currentIndex = 0;

function go(id){
  const idx = slideIds.indexOf(id);
  if(idx === -1) return;
  currentIndex = idx;
  
  slides.forEach(s => s.classList.remove('active'));
  const target = document.getElementById(id);
  if(target){
    target.classList.add('active');
    target.scrollTop = 0;
  }
  updateNav();
}

function updateNav(){
  const navBar = document.getElementById('navBar');
  if(currentIndex === 0){
    navBar.style.display = 'none';
  } else {
    navBar.style.display = 'flex';
  }
  
  const prevBtn = document.getElementById('prevPageBtn');
  const nextBtn = document.getElementById('nextPageBtn');
  
  if(prevBtn) prevBtn.disabled = (currentIndex === 0);
  if(nextBtn) nextBtn.disabled = (currentIndex === slideIds.length - 1);
}

document.getElementById('prevPageBtn').addEventListener('click', () => {
  if(currentIndex > 0) go(slideIds[currentIndex - 1]);
});
document.getElementById('nextPageBtn').addEventListener('click', () => {
  if(currentIndex < slideIds.length - 1) go(slideIds[currentIndex + 1]);
});

document.addEventListener('keydown', e => {
  if(e.key === 'Escape'){ 
    if(modal.classList.contains('on')) closeModal(); 
    else go('toc'); 
  }
});

/* ================= 3D 视差 ================= */
const stage = document.getElementById('stage'), stage3d = document.getElementById('stage3d');
if(stage && stage3d){
  let tx = -9, ty = 5, cx = -9, cy = 5;
  stage.addEventListener('mousemove', e => {
    const r = stage.getBoundingClientRect();
    tx = -9 + ((e.clientX - r.left) / r.width  - .5) * 16;
    ty =  5 - ((e.clientY - r.top ) / r.height - .5) * 12;
  });
  stage.addEventListener('mouseleave', () => { tx = -9; ty = 5; });
  (function loop(){
    cx += (tx - cx) * .09; cy += (ty - cy) * .09;
    stage3d.style.transform = `rotateY(${cx.toFixed(2)}deg) rotateX(${cy.toFixed(2)}deg)`;
    requestAnimationFrame(loop);
  })();
}

/* ================= 放大查看弹窗 ================= */
const modal = document.getElementById('modal');
const modalImg = document.getElementById('modalImg');
const modalTitle = document.getElementById('modalTitle');
const modalDesc = document.getElementById('modalDesc');

function openSafety(idx){
  const d = SAFETY_DATA[idx];
  if(!d) return;
  modalImg.innerHTML = d.svg || `<div class="modal-emoji">${d.icon}</div>`;
  modalTitle.textContent = `第 ${d.no} 条 · ${d.title}`;
  modalDesc.innerHTML = d.desc;
  modal.classList.add('on');
}
function closeModal(){
  modal.classList.remove('on');
  setTimeout(() => { modalImg.innerHTML = ''; }, 200);
}

/* ================= 安全卡片数据与渲染 ================= */
const SVG_CHUCK = `
<svg viewBox="0 0 200 160">
  <circle cx="62" cy="86" r="42" fill="#e2e8f0" stroke="#94a3b8" stroke-width="2"/>
  <circle cx="62" cy="86" r="28" fill="none" stroke="#cbd5e0" stroke-width="2"/>
  <rect x="50" y="46" width="24" height="16" rx="2" fill="#94a3b8"/>
  <rect x="26" y="90" width="16" height="24" rx="2" fill="#94a3b8" transform="rotate(-55 34 102)"/>
  <rect x="82" y="90" width="16" height="24" rx="2" fill="#94a3b8" transform="rotate(55 90 102)"/>
  <line x1="98" y1="60" x2="168" y2="30" stroke="#64748b" stroke-width="9" stroke-linecap="round"/>
  <rect x="88" y="50" width="22" height="22" rx="3" fill="#cbd5e0" transform="rotate(-24 99 61)"/>
  <path d="M172 104 L134 76" stroke="#ef4444" stroke-width="3.5" marker-end="url(#arw)"/>
</svg>`;

const SVG_ESTOP = `
<svg viewBox="0 0 200 160">
  <rect x="26" y="26" width="148" height="108" rx="10" fill="#f1f5f9" stroke="#94a3b8" stroke-width="2"/>
  <rect x="38" y="38" width="62" height="34" rx="4" fill="#ffffff" stroke="#cbd5e0"/>
  <circle cx="64" cy="102" r="17" fill="#ef4444" stroke="#fca5a5" stroke-width="3"/>
  <circle cx="64" cy="102" r="7" fill="#fecaca"/>
  <circle cx="122" cy="102" r="14" fill="#22c55e" stroke="#86efac" stroke-width="2.5"/>
  <rect x="112" y="46" width="50" height="8" rx="3" fill="#cbd5e0"/>
  <rect x="112" y="60" width="38" height="8" rx="3" fill="#cbd5e0"/>
  <path d="M30 140 L60 122" stroke="#ef4444" stroke-width="3.5" marker-end="url(#arw)"/>
</svg>`;

const SVG_HOOK = `
<svg viewBox="0 0 200 160">
  <path d="M26 26 L104 104 Q124 124 146 112 Q164 101 152 84 Q143 72 128 80" fill="none" stroke="#64748b" stroke-width="7" stroke-linecap="round"/>
  <path d="M40 128 q16 -18 34 -4 q16 14 2 26 q-16 10 -24 -4" fill="none" stroke="#f59e0b" stroke-width="3"/>
  <path d="M92 128 q12 -14 26 -2 q12 12 0 22" fill="none" stroke="#f59e0b" stroke-width="3"/>
  <path d="M170 44 L140 66" stroke="#ef4444" stroke-width="3.5" marker-end="url(#arw)"/>
</svg>`;

const SAFETY_DATA = [
  { no:'01', icon:'🦺', title:'穿戴防护用品',
    brief:'工作服、安全帽、扎好衣扣袖口，严禁戴手套、敞开衣服…',
    desc:'操作机床前穿戴好防护用品（<b>工作服、安全帽</b>），必须扎好衣扣、袖口，<b>严禁戴手套、敞开衣服</b>，防止卷入卡盘和刀具。女生应将辫子卷入帽内，不得外露。严禁穿拖鞋、凉鞋、高跟鞋。' },
  { no:'02', icon:'⚠️', title:'保护警告标牌',
    brief:'不要移动或损坏安装在机床上的警告标牌。',
    desc:'不要<b>移动或损坏</b>安装在机床上的警告标牌，它们是安全操作的重要提示。' },
  { no:'03', icon:'🧹', title:'保持工作空间',
    brief:'机床周围不要放置障碍物，如板子、凳子、扫把等。',
    desc:'不要在机床周围放置<b>障碍物</b>，工作空间应足够大，例如板子、凳子、扫把等，确保紧急情况时能迅速撤离。' },
  { no:'04', icon:'🚪', title:'关好防护门',
    brief:'机床开动前，必须关好机床防护门。',
    desc:'机床开动前，必须<b>关好机床防护门</b>，防止切屑飞溅和冷却液喷出造成伤害。' },
  { no:'05', icon:'🔧', title:'工具不遗忘',
    brief:'卡盘扳手、刀架扳手、套筒等用后必须取出。',
    desc:'安装工件、调整刀具所用工具<b>不要遗忘在机床内</b>，如<em>卡盘扳手、刀架扳手、套筒</em>。主轴启动后扳手飞出会造成严重伤害。',
    svg: SVG_CHUCK },
  { no:'06', icon:'👤', title:'一人操作',
    brief:'不允许两个或两个以上同学同时操作机床及面板。',
    desc:'不允许<b>两个或两个以上同学同时操作</b>机床以及数控系统操作面板，避免指令冲突与误操作。' },
  { no:'07', icon:'🛑', title:'不得离岗 · 急停',
    brief:'发现异常立即按急停或进给保持，操作者不得离开岗位。',
    desc:'严禁在车间内嬉戏玩耍；机床运转过程中<b>操作者不得离开岗位</b>，发现异常现象立即停车（<em>进给保持 / 急停按钮</em>）。',
    svg: SVG_ESTOP },
  { no:'08', icon:'🪝', title:'铁屑用钩子 / 毛刷',
    brief:'禁止用手接触刀尖和铁屑，必须用铁钩子或毛刷清理。',
    desc:'禁止用手接触刀尖和铁屑，铁屑必须用<b>铁钩子或毛刷</b>来清理。清理时应避免触碰旋转中的工件。',
    svg: SVG_HOOK },
  { no:'09', icon:'🚫', title:'不接触运动部位',
    brief:'禁止用手或其它方式接触正在旋转的主轴、工件。',
    desc:'禁止用手或其它方式接触<b>正在旋转的主轴、工件</b>或其它运动部位，防止缠绕与卷入。' },
  { no:'10', icon:'🎯', title:'回零顺序',
    brief:'各轴距原点 -100 mm 以上；先 +X 轴，再 +Z 轴。',
    desc:'手动回归原点时，注意机床各轴位置要距离原点 <b>-100 mm 以上</b>；机床原点回归顺序为：<em>首先 +X 轴，其次 +Z 轴</em>。' },
  { no:'11', icon:'🎛️', title:'手轮移动',
    brief:'看清 X、Z 轴“+、-”号标牌，先慢转确认方向。',
    desc:'使用手轮或快速移动方式移动各轴时，一定看清 <b>X、Z 轴“+、-”号标牌</b>后再移动；移动时先<b>慢转手轮</b>观察方向无误后方可加快速度。' },
  { no:'12', icon:'⏸️', title:'停车再测量',
    brief:'待机床完全停止、主轴停转后方可测量工件。',
    desc:'在程序运行中须暂停测量工件尺寸时，要待<b>机床完全停止、主轴停转</b>后方可进行测量，以免发生人身事故。' },
  { no:'13', icon:'⏱️', title:'关机等 3 分钟',
    brief:'主轴停转 3 分钟后方可关机。',
    desc:'关机时，要等<b>主轴停转 3 分钟</b>后方可关机，以保护主轴系统。' }
];

const safetyGrid = document.getElementById('safetyGrid');
if(safetyGrid){
  SAFETY_DATA.forEach((d, i) => {
    const card = document.createElement('div');
    card.className = 'safety-card';
    card.innerHTML = `
      <div class="sc-top">
        <div class="sc-no">${d.no}</div>
        <div class="sc-icon">${d.icon}</div>
      </div>
      ${d.svg ? `<div class="sc-thumb">${d.svg}</div>` : ''}
      <div class="sc-title">${d.title}</div>
      <div class="sc-brief">${d.brief}</div>
      <div class="sc-more">点击放大查看 ›</div>
    `;
    card.addEventListener('click', () => openSafety(i));
    safetyGrid.appendChild(card);
  });
}

/* ================= 工艺路线交互 ================= */
const ROUTE_DATA = [
  { icon:'🔧', title:'加工准备', desc:'开机回零 · 装夹 · 装刀 · 输入程序',
    lead:'完成机床开机回零、工件装夹、刀具安装与程序输入，为加工做好全部前置准备。',
    params:[
      ['回零顺序','先 +X 轴，再 +Z 轴'],
      ['安全距离','各轴距原点 -100 mm 以上'],
      ['装夹方式','三爪自定心卡盘夹持 φ50 左端'],
      ['刀具安装','外圆车刀 · 切槽刀 · 螺纹车刀'],
      ['程序输入','FANUC 0i-TF Plus 编辑并校验']
    ],
    tip:'卡盘扳手、刀架扳手用后必须取出；确认防护门关闭后才能启动主轴。'
  },
  { icon:'🎯', title:'对刀操作', desc:'试切对刀 · 建立工件坐标系',
    lead:'采用试切法分别确定 Z 向与 X 向刀补值，建立工件坐标系。',
    params:[
      ['Z 向对刀','试切端面 → 记录 Z 坐标 → 输入刀补'],
      ['X 向对刀','试切外圆 → 记录 X 坐标 → 输入刀补'],
      ['刀补号','T0101 / T0202 / T0303'],
      ['验证方法','首件试切后测量，偏差修正刀补']
    ],
    tip:'手轮移动先慢速确认方向再加速；对刀前必须回参考点。'
  },
  { icon:'⚙️', title:'粗加工', desc:'G71 循环 · 留精车余量 0.4 mm',
    lead:'使用 G71 复合循环粗车外圆，快速去除大部分余量，为精车留出均匀余量。',
    params:[
      ['指令','G71 复合循环'],
      ['背吃刀量','2 ~ 3 mm'],
      ['进给量','0.2 ~ 0.3 mm/r'],
      ['主轴转速','600 ~ 800 r/min'],
      ['精车余量','X 向留 0.4 mm']
    ],
    tip:'粗车后测量余量是否均匀，避免精车时切削力突变影响尺寸精度。'
  },
  { icon:'✨', title:'精加工', desc:'G70 循环 · 精车到尺寸',
    lead:'使用 G70 循环精车各表面到尺寸，保证 φ42±0.05 等公差要求。',
    params:[
      ['指令','G70 精车循环'],
      ['进给量','0.08 ~ 0.15 mm/r'],
      ['主轴转速','1000 ~ 1200 r/min'],
      ['冷却策略','粗车后冷却再精车，减少热变形'],
      ['精度目标','φ42±0.05 · 38±0.03']
    ],
    tip:'刀尖圆弧半径越小表面越光整；精车前确认刀补值准确。'
  },
  { icon:'🔪', title:'切退刀槽', desc:'G01 · φ26 × 4 mm',
    lead:'使用 G01 切 φ26 退刀槽，槽宽 4 mm，为螺纹加工提供退刀空间。',
    params:[
      ['指令','G01 直线插补'],
      ['槽尺寸','φ26 × 4 mm'],
      ['切槽刀宽','4 mm'],
      ['主轴转速','400 ~ 600 r/min'],
      ['进给量','0.05 ~ 0.1 mm/r']
    ],
    tip:'切槽切削力较大，注意排屑；槽底粗糙度应满足退刀要求。'
  },
  { icon:'🌀', title:'车螺纹', desc:'G92 · M30×2 分 6 刀',
    lead:'使用 G92 车 M30×2 螺纹，分 6 刀逐刀递减切削，保证螺纹精度。',
    params:[
      ['指令','G92 螺纹循环'],
      ['螺纹规格','M30×2'],
      ['分刀次数','6 刀逐刀递减'],
      ['主轴转速','400 ~ 500 r/min'],
      ['引入距离','≥ 3 mm，保证首尾螺距均匀']
    ],
    tip:'必须留足引入距离避免乱扣；通规进、止规不进为合格。'
  },
  { icon:'📏', title:'测量检验', desc:'量具检测 · 记录数据',
    lead:'加工完成后对关键尺寸进行测量记录，判断零件是否合格。',
    params:[
      ['量具','游标卡尺 / 千分尺 / 螺纹环规'],
      ['检测项 1','φ42±0.05 外圆'],
      ['检测项 2','39±0.03 段长'],
      ['检测项 3','M30×2 螺纹（通止规）'],
      ['记录','填写实验数据记录表']
    ],
    tip:'测量前必须等主轴完全停转；发现超差及时分析原因并修正刀补。'
  }
];

const routeList    = document.getElementById('routeList');
const dIcon        = document.getElementById('dIcon');
const dStep        = document.getElementById('dStep');
const dTitle       = document.getElementById('dTitle');
const dBody        = document.getElementById('dBody');
const stepProgress = document.getElementById('stepProgress');
const prevBtn      = document.getElementById('prevStep');
const nextBtn      = document.getElementById('nextStep');
let currentStep = 0;

if(routeList){
  ROUTE_DATA.forEach((d, i) => {
    const el = document.createElement('div');
    el.className = 'route-item' + (i === 0 ? ' active' : '');
    el.dataset.step = i;
    el.innerHTML = `
      <div class="ri-no">${String(i + 1).padStart(2, '0')}</div>
      <div class="ri-body">
        <div class="ri-title">${d.title}</div>
        <div class="ri-desc">${d.desc}</div>
      </div>
      <div class="ri-arrow">›</div>
    `;
    el.addEventListener('click', () => selectRoute(i));
    routeList.appendChild(el);
  });

  function selectRoute(i){
    if(i < 0 || i >= ROUTE_DATA.length) return;
    currentStep = i;
    const d = ROUTE_DATA[i];

    document.querySelectorAll('.route-item').forEach((el, idx) => {
      el.classList.toggle('active', idx === i);
    });

    dIcon.textContent  = d.icon;
    dStep.textContent  = `第 ${i + 1} 步`;
    dTitle.textContent = d.title;
    dBody.innerHTML = `
      <p class="detail-lead">${d.lead}</p>
      <div class="param-grid">
        ${d.params.map(p =>
          `<div class="param">
             <span class="pk">${p[0]}</span>
             <span class="pv">${p[1]}</span>
           </div>`).join('')}
      </div>
      <div class="tip-box">⚠ 要点：${d.tip}</div>
    `;

    dBody.style.animation = 'none';
    void dBody.offsetWidth;
    dBody.style.animation = '';

    stepProgress.innerHTML = `<b>${i + 1}</b> / ${ROUTE_DATA.length}`;
    prevBtn.disabled = (i === 0);
    nextBtn.disabled = (i === ROUTE_DATA.length - 1);
  }

  prevBtn.addEventListener('click', () => selectRoute(currentStep - 1));
  nextBtn.addEventListener('click', () => selectRoute(currentStep + 1));
  selectRoute(0);
}

/* ================= 工序模拟器 ================= */
const items = Array.from(document.querySelectorAll('.step-item'));
const bar = document.getElementById('simBar');
const doneCountEl = document.getElementById('doneCount');
const vizStatus = document.getElementById('vizStatus');
const autoBtn = document.getElementById('autoBtn');
const resetBtn = document.getElementById('resetBtn');

const TOTAL = items.length;
const done = new Set();

const STATUS_TEXT = [
  '待加工 · 毛坯已装夹',
  '已完成开机回零与对刀，工件坐标系建立完成',
  '粗车完成，外圆留 0.4 mm 精车余量',
  '精车完成，各外圆与锥面已到尺寸',
  '退刀槽 φ26×4 加工完成',
  'M30×2 螺纹加工完成（分 6 刀递减切削）',
  '全部工序完成，检测合格 ✓'
];

const layerMap = [
  { el:'gRough',  min:2 },
  { el:'gFinish', min:3 },
  { el:'gGroove', min:4 },
  { el:'gThread', min:5 },
  { el:'gPass',   min:6 }
];

function refresh(){
  const pct = done.size / TOTAL * 100;
  bar.style.width = pct + '%';
  doneCountEl.textContent = done.size;

  items.forEach((it, i) => it.classList.toggle('done', done.has(i)));

  let maxDone = -1;
  done.forEach(i => { if(i > maxDone) maxDone = i; });

  layerMap.forEach(m => {
    const el = document.getElementById(m.el);
    if(el) el.classList.toggle('on', maxDone >= m.min);
  });
  const blank = document.getElementById('gBlank');
  if(blank) blank.style.opacity = (maxDone >= 3) ? '0.3' : '1';

  vizStatus.textContent = STATUS_TEXT[Math.min(maxDone + 1, STATUS_TEXT.length - 1)];
  vizStatus.classList.toggle('ok', maxDone >= 6);
}

let timer = null, autoRunning = false, stopAuto = () => {};

items.forEach((it, i) => {
  it.addEventListener('click', () => {
    if(stopAuto) stopAuto();
    if(done.has(i)) done.delete(i); else done.add(i);
    refresh();
  });
});

autoBtn.addEventListener('click', () => {
  if(autoRunning) return;
  done.clear(); refresh();
  autoRunning = true;
  autoBtn.disabled = true;
  autoBtn.textContent = '演示中…';

  let i = 0;
  stopAuto = () => {
    clearInterval(timer);
    autoRunning = false;
    autoBtn.disabled = false;
    autoBtn.textContent = '▶ 自动演示';
  };

  timer = setInterval(() => {
    if(i >= TOTAL){ stopAuto(); return; }
    done.add(i);
    refresh();
    i++;
  }, 700);
});

resetBtn.addEventListener('click', () => {
  if(stopAuto) stopAuto();
  stopAuto = () => {};
  autoRunning = false;
  autoBtn.disabled = false;
  autoBtn.textContent = '▶ 自动演示';
  done.clear();
  refresh();
});

refresh();
updateNav(); // 初始化导航状态
</script>
</body>
</html>
