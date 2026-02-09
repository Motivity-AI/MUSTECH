<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<title>MUSTECH ERP v24 - Ultimate Global Edition</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
<style>
:root{
  --brand:#1e3a8a;
  --ok:#059669;
  --warn:#f59e0b;
  --bad:#dc2626;
  --bg:#f8fafc;
}
body{margin:0;font-family:'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;background:var(--bg);color:#1e293b;}
header{background:var(--brand);color:#fff;padding:15px;text-align:center;font-weight:900;position:relative;box-shadow:0 2px 10px rgba(0,0,0,0.1);}
.logout-btn{position:absolute;left:10px;top:12px;background:rgba(255,255,255,0.2);padding:5px 10px;border-radius:5px;cursor:pointer;font-size:12px;}
nav{display:flex;background:#fff;border-top:1px solid #ddd;position:fixed;bottom:0;width:100%;height:65px;z-index:1000;}
nav button{flex:1;border:none;background:none;font-weight:bold;cursor:pointer;color:#64748b;font-size:11px;}
nav button.active{color:var(--brand);border-top:3px solid var(--brand);}
.page{display:none;padding:15px;padding-bottom:120px;max-width:1100px;margin:auto;animation:fadeIn 0.3s;}
@keyframes fadeIn{from{opacity:0;}to{opacity:1;}}
.page.active{display:block;}
.card{background:#fff;margin-bottom:15px;padding:18px;border-radius:15px;box-shadow:0 4px 6px rgba(0,0,0,.05);border:1px solid #e2e8f0;}
input,select{width:100%;padding:12px;margin:8px 0;border-radius:10px;border:1px solid #cbd5e1;outline:none;}
input:focus{border-color:var(--brand);box-shadow:0 0 5px rgba(30,58,138,0.2);}
button{padding:12px;border:none;border-radius:10px;font-weight:bold;cursor:pointer;transition:.2s;}
button:active{transform:scale(0.98);}
.btn-main{background:var(--brand);color:#fff;width:100%;}
.btn-ok{background:var(--ok);color:#fff;width:100%;}
.btn-del{background:var(--bad);color:#fff;padding:5px 10px;font-size:11px;}
.btn-edit{background:var(--warn);color:#fff;padding:5px 10px;font-size:11px;}
table{width:100%;border-collapse:collapse;margin-top:10px;}
th,td{padding:10px;border-bottom:1px solid #f1f5f9;text-align:center;font-size:12px;}
th{background:#f8fafc;color:var(--brand);}
.modal{display:none;position:fixed;top:0;left:0;width:100%;height:100%;background:rgba(0,0,0,0.8);z-index:2000;justify-content:center;align-items:center;backdrop-filter:blur(4px);}
.modal-content{background:#fff;padding:20px;border-radius:20px;width:90%;max-width:450px;position:relative;max-height:90vh;overflow-y:auto;}
.qr-box{display:flex;justify-content:center;margin-top:15px;}
.low-stock{background:#fee2e2;}
@media print{.no-print{display:none !important;}}
</style>
</head>
<body>
<header>
  <div class="logout-btn" onclick="logout()">🔒 خروج</div>
  MUSTECH ERP - النسخة العالمية
</header>

<div id="loginView" style="padding:100px 20px;text-align:center;">
  <div class="card" style="max-width:400px;margin:auto;">
    <h2 style="color:var(--brand)">مرحباً بك</h2>
    <input type="password" id="entryPass" placeholder="أدخل كلمة المرور">
    <button class="btn-main" onclick="auth()">دخول النظام</button>
  </div>
</div>

<div id="appView" style="display:none">

<!-- صفحة البيع -->
<div id="posPage" class="page active">
  <div class="card">
    <h3>📑 فاتورة جديدة</h3>
    <div style="display:flex;gap:10px;">
      <input id="cName" placeholder="اسم العميل">
      <input id="cPhone" placeholder="رقم الهاتف">
    </div>
    <select id="itemSelect" onchange="priceLock()"></select>
    <div style="display:grid;grid-template-columns:1fr 1fr;gap:10px;">
      <div>
        <small id="minPriceLbl" style="color:var(--bad);font-weight:bold"></small>
        <input type="number" id="sPrice" placeholder="سعر البيع">
      </div>
      <input type="number" id="sQty" value="1" placeholder="الكمية">
    </div>
    <button class="btn-ok" onclick="addToCart()">إضافة للقائمة +</button>
    <table id="cartTbl">
      <thead><tr><th>الصنف</th><th>السعر</th><th>الكمية</th><th>حذف</th></tr></thead>
      <tbody></tbody>
    </table>
    <div id="cartTotalDisplay" style="text-align:center;font-size:24px;font-weight:bold;color:var(--brand);margin:15px 0;border-top:2px dashed #ddd;padding-top:10px;">0 SDG</div>
    <input type="number" id="paidAmt" placeholder="المبلغ المستلم (كاش)">
    <button class="btn-main" onclick="openPreview()">👁️ معاينة الفاتورة</button>
  </div>
</div>

<!-- المخزن -->
<div id="stockPage" class="page">
  <div class="card">
    <h3>📦 إدارة المخزون</h3>
    <input type="text" id="stockSearch" placeholder="🔍 بحث سريع..." onkeyup="searchStock()">
    <div style="overflow-x:auto">
      <table>
        <thead><tr><th>الماركة</th><th>الصنف</th><th>التكلفة</th><th>الكمية</th><th>إجراء</th></tr></thead>
        <tbody id="stockBody"></tbody>
      </table>
    </div>
  </div>
</div>

<!-- التقارير -->
<div id="reportsPage" class="page">
  <div class="card">
    <h3>📊 التقارير الذكية</h3>
    <div id="repLock">
      <input type="password" id="fKey" placeholder="كود الوصول المالي">
      <button class="btn-main" onclick="unlockRep()">فتح السجلات</button>
    </div>
    <div id="repContent" style="display:none">
      <div style="display:grid;grid-template-columns:1fr 1fr;gap:10px;">
        <select id="repType">
          <option value="7">أسبوعي</option>
          <option value="30">شهري</option>
          <option value="90">ربع سنوي</option>
          <option value="180">نصف سنوي</option>
          <option value="365">سنوي</option>
          <option value="all">كل التاريخ</option>
        </select>
        <button class="btn-ok" onclick="generateReport()">تحليل البيانات</button>
      </div>
      <div id="reportResult"></div>
      <button class="btn-main" style="margin-top:10px" onclick="exportReportPDF()">📄 تصدير التقرير PDF</button>
    </div>
  </div>
</div>

<!-- العملاء -->
<div id="custPage" class="page">
  <div class="card">
    <h3>👥 العملاء والمديونيات</h3>
    <table>
      <thead><tr><th>الاسم</th><th>الهاتف</th><th>المتبقي</th><th>إجراء</th></tr></thead>
      <tbody id="custBody"></tbody>
    </table>
  </div>
</div>

<!-- الإدارة -->
<div id="adminPage" class="page">
  <div class="card" id="adminArea">
    <h3>⚙️ إعدادات متقدمة</h3>
    <div style="background:#f0f4f8;padding:15px;border-radius:10px;margin-bottom:15px;">
      <h4>➕ إضافة صنف جديد</h4>
      <input id="nb" placeholder="الشركة/الماركة">
      <input id="nn" placeholder="اسم المنتج">
      <input id="nc" type="number" placeholder="سعر التكلفة">
      <input id="np" type="number" placeholder="سعر البيع">
      <input id="nq" type="number" placeholder="الكمية">
      <button class="btn-ok" onclick="addNewProduct()">إضافة للمخزن</button>
    </div>
    <div style="background:#fff1f2;padding:15px;border-radius:10px;margin-bottom:15px;">
      <h4>🔐 تحديث كلمات السر</h4>
      <input id="upAdmin" type="password" placeholder="كلمة سر المدير">
      <input id="upStaff" type="password" placeholder="كلمة سر الموظف">
      <input id="upFin" type="password" placeholder="كود التقارير المالية">
      <button class="btn-main" onclick="updateSecurity()">حفظ التغييرات</button>
    </div>
    <div style="background:#e2e8f0;padding:15px;border-radius:10px;">
      <h4>💾 النسخ الاحتياطي</h4>
      <button class="btn-main" onclick="exportData()">تصدير البيانات (Backup)</button>
      <input type="file" id="importFile" style="display:none" onchange="importData(event)">
      <button class="btn-ok" style="margin-top:10px;background:#475569" onclick="document.getElementById('importFile').click()">استيراد البيانات</button>
    </div>
  </div>
</div>

<!-- فاتورة -->
<div id="invModal" class="modal">
  <div class="modal-content" id="printableInvoice">
    <div style="text-align:center;border-bottom:2px solid #333;padding-bottom:10px;">
      <h2 style="margin:0">MUSTECH</h2>
      <small>لحلول الصوتيات والمرئيات المتكاملة</small><br>
      <small>📍 السودان - الخرطوم</small><br>
      <small>📞 +249912949561 | +249917029008</small><br>
      <div id="invDate" style="font-size:10px;margin-top:5px;"></div>
    </div>
    <div style="margin:15px 0;font-size:12px;border-bottom:1px solid #eee;padding-bottom:5px;">
      <b>العميل:</b> <span id="viewC"></span><br>
      <b>الهاتف:</b> <span id="viewP"></span>
    </div>
    <table id="viewItems" style="font-size:11px;width:100%"></table>
    <div style="margin-top:15px;border-top:2px solid #000;padding-top:10px;font-weight:bold;">
      <div style="display:flex;justify-content:space-between"><span>إجمالي الفاتورة:</span><span id="viewT"></span></div>
      <div style="display:flex;justify-content:space-between"><span>المبلغ المدفوع:</span><span id="viewPaid"></span></div>
      <div style="display:flex;justify-content:space-between;color:red"><span>المتبقي (دين):</span><span id="viewRem"></span></div>
    </div>
    <div class="qr-box"><div id="qrcode"></div></div>
    <div style="text-align:center;margin-top:15px;font-size:10px;color:#666;">شكرًا لتعاملكم معنا!</div>
    <div class="no-print" style="margin-top:20px;display:flex;gap:5px;">
      <button class="btn-ok" style="flex:2" onclick="processSale()">تأكيد وحفظ ✅</button>
      <button class="btn-main" style="flex:1" onclick="exportInvoicePDF()">💾 PDF</button>
      <button class="btn-del" style="flex:1" onclick="document.getElementById('invModal').style.display='none'">إلغاء</button>
    </div>
  </div>
</div>

<nav id="navBar" style="display:none">
  <button onclick="nav('posPage',this)" class="active">🏠 البيع</button>
  <button onclick="nav('stockPage',this)">📦 المخزن</button>
  <button onclick="nav('reportsPage',this)">📊 التقارير</button>
  <button onclick="nav('custPage',this)">👥 العملاء</button>
  <button onclick="nav('adminPage',this)">⚙️ الإدارة</button>
</nav>

<script>
// --- قاعدة البيانات ---
let db = JSON.parse(localStorage.getItem("mustech_v24"))||{items:[],sales:[],customers:[],config:{admin:"7878",staff:"0909",fin:"1988"}};
let user=null, cart=[];

// --- تسجيل الدخول ---
function auth(){let p=entryPass.value;if(p===db.config.admin)user='admin';else if(p===db.config.staff)user='staff';else return alert("كلمة المرور خاطئة!");loginView.style.display="none";appView.style.display="block";navBar.style.display="flex";if(user==='staff')adminArea.innerHTML="<h4 style='color:red;text-align:center'>🚫 لوحة المدير فقط</h4>";sync();}

// --- التنقل ---
function nav(id,btn){document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));document.querySelectorAll('nav button').forEach(n=>n.classList.remove('active'));document.getElementById(id).classList.add('active');btn.classList.add('active');sync();}

// --- المزامنة ---
function sync(){itemSelect.innerHTML=db.items.map((it,i)=>`<option value="${i}">${it.brand} - ${it.name} [المتوفر: ${it.qty}]</option>`).join('');priceLock();renderStock();renderCust();}
function searchStock(){let q=stockSearch.value.toLowerCase();document.querySelectorAll("#stockBody tr").forEach(row=>row.style.display=row.innerText.toLowerCase().includes(q)?"":"none");}

// --- البيع ---
function priceLock(){let it=db.items[itemSelect.value];if(!it)return;let min=it.cost*1.45;minPriceLbl.innerText=`أدنى سعر: ${Math.ceil(min)}`;sPrice.value=it.price||Math.ceil(min);}
function addToCart(){let it=db.items[itemSelect.value];let p=parseFloat(sPrice.value);let q=parseInt(sQty.value);if(p<(it.cost*1.45))return alert("السعر أقل من هامش الربح (45%)!");if(q>it.qty)return alert("الكمية غير متوفرة!");cart.push({...it,sP:p,sQ:q,idx:itemSelect.value});renderCart();}
function renderCart(){let h='',total=0;cart.forEach((c,i)=>{h+=`<tr><td>${c.name}</td><td>${c.sP}</td><td>${c.sQ}</td><td><button class="btn-del" onclick="cart.splice(${i},1);renderCart()">❌</button></td></tr>`;total+=c.sP*c.sQ;});document.querySelector('#cartTbl tbody').innerHTML=h;cartTotalDisplay.innerText=total.toLocaleString()+" SDG";}
function openPreview(){if(!cart.length)return alert("السلة فارغة!");let total=cart.reduce((a,b)=>a+b.sP*b.sQ,0);let paid=paidAmt.value===""?total:parseFloat(paidAmt.value);invDate.innerText=new Date().toLocaleString();viewC.innerText=cName.value||"عميل نقدي";viewP.innerText=cPhone.value||"--";viewItems.innerHTML=`<tr><th>الصنف</th><th>الكمية</th><th>المجموع</th></tr>`+cart.map(c=>`<tr><td>${c.name}</td><td>${c.sQ}</td><td>${(c.sP*c.sQ).toLocaleString()}</td></tr>`).join('');viewT.innerText=total.toLocaleString()+" SDG";viewPaid.innerText=paid.toLocaleString()+" SDG";viewRem.innerText=(total-paid).toLocaleString()+" SDG";document.getElementById("qrcode").innerHTML="";new QRCode(document.getElementById("qrcode"),{text:`Mustech-Inv:${total}-Date:${new Date().toLocaleDateString()}`,width:80,height:80});invModal.style.display="flex";}
function processSale(){
  let total=cart.reduce((a,b)=>a+b.sP*b.sQ,0);
  let paid=paidAmt.value===""?total:parseFloat(paidAmt.value);
  cart.forEach(c=>db.items[c.idx].qty-=c.sQ);
  if(cName.value){
    let existing = db.customers.find(cust => cust.phone === cPhone.value);
    let remaining = total - paid;
    if(existing){existing.balance += remaining;}
    else{db.customers.push({name:cName.value,phone:cPhone.value,balance:remaining});}
  }
  db.sales.push({customer:cName.value||"نقدي",total,paid,profit:cart.reduce((a,b)=>a+(b.sP-b.cost)*b.sQ,0),items:[...cart],date:new Date().getTime()});
  save();
  alert("تمت العملية بنجاح!");
  cart=[];
  renderCart();
  invModal.style.display="none";
  renderCust();
}

// --- المخزن ---
function renderStock(){stockBody.innerHTML=db.items.map((it,i)=>`<tr class="${it.qty<3?'low-stock':''}"><td>${it.brand}</td><td>${it.name}</td><td>${it.cost}</td><td>${it.qty}</td><td>${user==='admin'?`<button class="btn-edit" onclick="editItem(${i})">📝</button><button class="btn-del" onclick="deleteItem(${i})">🗑️</button>`:'🔒'}</td></tr>`).join('');}
function editItem(i){let nQ=prompt("الكمية الجديدة:",db.items[i].qty);let nC=prompt("سعر التكلفة:",db.items[i].cost);if(nQ!==null && nC!==null){db.items[i].qty=parseInt(nQ);db.items[i].cost=parseFloat(nC);save();}}
function deleteItem(i){if(confirm("سيتم مسح الصنف نهائياً، هل أنت متأكد؟")){db.items.splice(i,1);save();}}

// --- العملاء ---
function renderCust(){custBody.innerHTML=db.customers.map(c=>`<tr><td>${c.name}</td><td>${c.phone}</td><td style="color:red">${c.balance.toLocaleString()}</td><td><button onclick="payDebt('${c.phone}')">سداد</button></td></tr>`).join('');}
function payDebt(p){let c=db.customers.find(x=>x.phone===p);let amt=prompt(`المبلغ المراد سداده (الحالي: ${c.balance}):`);if(amt){c.balance-=parseFloat(amt);save();renderCust();}}

// --- التقارير ---
function unlockRep(){if(fKey.value===db.config.fin){repLock.style.display="none";repContent.style.display="block";}else alert("كود الوصول خاطئ!");}
function generateReport(){let days=repType.value;let now=new Date().getTime();let filtered=db.sales.filter(s=>days==='all'?true:(now-s.date)<=(days*86400000));let tS=filtered.reduce((a,b)=>a+b.total,0);let tP=filtered.reduce((a,b)=>a+b.profit,0);reportResult.innerHTML=`<div class="rep-box"><h4>📊 ملخص الأداء المالي:</h4><p>إجمالي المبيعات: <b>${tS.toLocaleString()} SDG</b></p>${user==='admin'?`<p style="color:var(--ok)">الربح الإجمالي: <b>${tP.toLocaleString()} SDG</b></p>`:''}<p>عدد الفواتير: ${filtered.length}</p></div>`;}
function exportInvoicePDF(){html2pdf().set({margin:0.5,filename:'Mustech_Invoice.pdf',image:{type:'jpeg',quality:0.98},html2canvas:{scale:2},jsPDF:{unit:'in',format:'letter',orientation:'portrait'}}).from(printableInvoice).save();}
function exportReportPDF(){html2pdf().from(reportResult).save('Mustech_Report.pdf');}

// --- النسخ الاحتياطي ---
function exportData(){let blob=new Blob([JSON.stringify(db)],{type:"application/json"});let url=URL.createObjectURL(blob);let a=document.createElement('a');a.href=url;a.download=`MUSTECH_DB_${new Date().toLocaleDateString()}.json`;a.click();}
function importData(e){let reader=new FileReader();reader.onload=(event)=>{db=JSON.parse(event.target.result);save();alert("تم استيراد البيانات بنجاح!");location.reload();};reader.readAsText(e.target.files[0]);}

// --- الإدارة ---
function addNewProduct(){if(!nb.value||!nn.value)return alert("أكمل البيانات!");db.items.push({brand:nb.value,name:nn.value,cost:parseFloat(nc.value),price:parseFloat(np.value),qty:parseInt(nq.value)});save();alert("تم الإضافة!");sync();}
function updateSecurity(){if(upAdmin.value)db.config.admin=upAdmin.value;if(upStaff.value)db.config.staff=upStaff.value;if(upFin.value)db.config.fin=upFin.value;save();alert("تم تحديث كلمات السر!");}

// --- عام ---
function logout(){location.reload();}
function save(){localStorage.setItem("mustech_v24",JSON.stringify(db));sync();}
</script>
</body>
</html>
