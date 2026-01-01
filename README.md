<!DOCTYPE html>
<html lang="vi">
<head>
<meta charset="UTF-8">
<title>anhtubantool</title>

<style>
*{box-sizing:border-box;margin:0;padding:0}
body{
    min-height:100vh;
    font-family:Arial,Helvetica,sans-serif;
    background:linear-gradient(270deg,red,orange,yellow,green,cyan,blue,violet);
    background-size:1600% 1600%;
    animation:rainbow 15s ease infinite;
    display:flex;
    justify-content:center;
    align-items:center;
    color:#fff;
}
@keyframes rainbow{
    0%{background-position:0% 50%}
    50%{background-position:100% 50%}
    100%{background-position:0% 50%}
}
.app{
    width:100%;
    max-width:520px;
    background:rgba(0,0,0,.75);
    border-radius:22px;
    padding:20px;
    border:2px solid #fff;
}
h1{
    text-align:center;
    margin-bottom:16px;
    font-size:34px;
    text-shadow:
        -2px -2px 0 #fff,
         2px -2px 0 #fff,
        -2px  2px 0 #fff,
         2px  2px 0 #fff,
         0 0 15px gold;
}
.tabs{
    display:flex;
    gap:10px;
    margin-bottom:16px;
}
.tab{
    flex:1;
    text-align:center;
    padding:10px;
    border-radius:14px;
    border:2px solid #fff;
    cursor:pointer;
    font-weight:bold;
}
.tab.active{
    background:rgba(255,255,255,.25);
}
.content{display:none}
.content.active{display:block}

/* common box */
.box{
    background:#020617;
    border-radius:16px;
    padding:16px;
}
input,button{
    width:100%;
    padding:12px;
    border-radius:10px;
    border:none;
    margin-top:8px;
    font-size:15px;
}
button{
    background:#2563eb;
    color:white;
    font-weight:bold;
    cursor:pointer;
}
pre{
    background:#0b0e1d;
    padding:12px;
    border-radius:10px;
    margin-top:10px;
    white-space:pre-wrap;
}
.row{
    display:flex;
    justify-content:space-between;
    padding:6px 0;
    border-bottom:1px solid rgba(255,255,255,.15);
}
.row:last-child{border-bottom:none}
.timer{text-align:center;margin-bottom:10px;color:#ffd9a3}
</style>
</head>

<body>
<div class="app">

<h1>anhtubantool</h1>

<div class="tabs">
    <div class="tab active" onclick="openTab('sicbo',this)">🎲 SICBO</div>
    <div class="tab" onclick="openTab('sunwin',this)">🔥 SUNWIN</div>
    <div class="tab" onclick="openTab('md5',this)">🔐 CHẴN / LẺ</div>
</div>

<!-- ================= SICBO ================= -->
<div id="sicbo" class="content active">
<div class="box">
<h3 style="text-align:center">🎲 SICBO AI – MD5</h3>
<input id="md5Input" placeholder="Nhập MD5 32 ký tự hex">
<button onclick="analyzeSicbo()">PHÂN TÍCH</button>
<pre id="sicboOut"></pre>
</div>
</div>

<!-- ================= SUNWIN ================= -->
<div id="sunwin" class="content">
<div class="box">
<h3 style="text-align:center">🔥 SUNWIN AI</h3>
<div class="timer">⏳ Làm mới sau <span id="countdown">60</span>s</div>
<div class="row"><span>Phiên</span><span id="phien">--</span></div>
<div class="row"><span>Xúc xắc</span><span id="xx">--</span></div>
<div class="row"><span>Tổng</span><span id="tong">--</span></div>
<div class="row"><span>Kết quả</span><span id="ketqua">--</span></div>
<div class="row"><span>Dự đoán</span><span id="dudoan">--</span></div>
</div>
</div>

<!-- ================= MD5 CHẴN LẺ ================= -->
<div id="md5" class="content">
<div class="box">
<h3 style="text-align:center">🔐 MD5 → CHẴN / LẺ</h3>
<input id="md5CL" placeholder="Nhập MD5 32 ký tự">
<button onclick="analyzeCL()">PHÂN TÍCH</button>
<div id="outCL"></div>
</div>
</div>

</div>

<script>
function openTab(id,e){
    document.querySelectorAll('.tab').forEach(t=>t.classList.remove('active'));
    document.querySelectorAll('.content').forEach(c=>c.classList.remove('active'));
    e.classList.add('active');
    document.getElementById(id).classList.add('active');
}

/* ========= SICBO (giữ logic gốc) ========= */
function seededRandom(seed){let x=Math.sin(seed++)*10000;return x-Math.floor(x);}
function analyzeSicbo(){
    const md5=md5Input.value.trim().toLowerCase();
    if(!/^[0-9a-f]{32}$/.test(md5)){sicboOut.textContent="❌ MD5 không hợp lệ";return;}
    let s=parseInt(md5.slice(0,16),16);
    let d1=Math.floor(seededRandom(s++)*6)+1;
    let d2=Math.floor(seededRandom(s++)*6)+1;
    let d3=Math.floor(seededRandom(s++)*6)+1;
    let total=d1+d2+d3;
    sicboOut.textContent=
`🎲 ${d1}-${d2}-${d3}
TỔNG: ${total}`;
}

/* ========= SUNWIN ========= */
let cd=60;
async function loadSun(){
    try{
        let r=await fetch("https://sunwinsaygex-tzz9.onrender.com/api/sun");
        let d=await r.json();
        phien.textContent=d.phien;
        xx.textContent=`${d.xuc_xac_1}-${d.xuc_xac_2}-${d.xuc_xac_3}`;
        tong.textContent=d.tong;
        ketqua.textContent=d.ket_qua;
        dudoan.textContent=d.du_doan;
    }catch{ketqua.textContent="Lỗi API";}
}
setInterval(()=>{
    countdown.textContent=cd--;
    if(cd<0){cd=60;loadSun();}
},1000);
loadSun();

/* ========= CHẴN LẺ ========= */
function analyzeCL(){
    const md5=md5CL.value.trim().toLowerCase();
    if(!/^[0-9a-f]{32}$/.test(md5)){outCL.innerHTML="❌ MD5 không hợp lệ";return;}
    const seed=parseInt(md5.slice(0,8),16);
    const r=seededRandom(seed);
    const res=r>0.5?"LẺ":"CHẴN";
    outCL.innerHTML=`<h3 style="text-align:center">${res}</h3>`;
}
</script>
</body>
</html>
