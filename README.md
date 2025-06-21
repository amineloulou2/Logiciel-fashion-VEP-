<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <title>واجهة البرنامج</title>
  <style>
    body {
      font-family: 'Arial', sans-serif;
      background: #f0f0f0;
      margin: 0;
      padding: 20px;
      text-align: center;
    }
    h1, h2, h4 {
      color: #333;
    }
    .grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(160px, 1fr));
      gap: 15px;
      margin-bottom: 20px;
    }
    .btn {
      background: #007bff;
      color: white;
      padding: 20px;
      border-radius: 15px;
      font-size: 16px;
      font-weight: bold;
      border: none;
      cursor: pointer;
      transition: background 0.3s;
    }
    .btn:hover {
      background: #0056b3;
    }
    .section {
      display: none;
      margin-top: 30px;
      background: white;
      padding: 20px;
      border-radius: 12px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
      text-align: right;
    }
    .active {
      display: block !important;
    }
    input, select {
      padding: 8px;
      margin: 5px;
      border: 1px solid #ddd;
      border-radius: 4px;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 10px;
    }
    th, td {
      padding: 8px;
      border: 1px solid #ddd;
      text-align: center;
    }
    th {
      background-color: #f2f2f2;
    }
    @media print {
      body * {
        visibility: hidden;
      }
      #printArea, #printArea * {
        visibility: visible;
      }
      #printArea {
        position: absolute;
        left: 0;
        top: 0;
        width: 100%;
      }
      .barcode-label {
        page-break-inside: avoid;
        margin: 10px;
      }
    }
  </style>
</head>
<body>
<h1>📋 واجهة إدارة المتجر - BAZAR SERDANI</h1>
<div class="grid">
  <button class="btn" onclick="showSection('add')">➕ إضافة منتج</button>
  <button class="btn" onclick="showSection('stock')">📦 المخزون</button>
  <button class="btn" onclick="showSection('sell')">🛒 البيع</button>
  <button class="btn" onclick="showSection('return')">🔁 الإرجاع</button>
  <button class="btn" onclick="showSection('clients')">👥 الزبائن والديون</button>
  <button class="btn" onclick="showSection('stats')">📊 الإحصائيات</button>
  <button class="btn" onclick="showSection('print')">🖨️ الطباعة</button>
</div>
<!-- قسم إضافة منتج -->
<div id="add" class="section">
  <h2>➕ إضافة منتج جديد</h2>
  <form id="productForm">
    <label>📦 اسم المنتج:<br><input type="text" id="productName" required></label><br><br>
    <label>💰 سعر الشراء:<br><input type="number" id="buyPrice" required></label><br><br>
    <label>💵 سعر البيع:<br><input type="number" id="sellPrice" required></label><br><br>
    <label>📦 الكمية:<br><input type="number" id="quantity" required></label><br><br>
    <label>🔢 الباركود (يُولد تلقائيًا إذا ترك فارغًا):<br><input type="text" id="barcode"></label><br><br>
    <button type="submit">📥 حفظ المنتج</button>
  </form>
  <p id="addProductMsg" style="color: green; font-weight: bold;"></p>
</div>
<!-- قسم المخزون -->
<div id="stock" class="section">
  <h2>📦 إدارة المخزون</h2>
  <div id="tableContainer"></div>
</div>
<!-- قسم البيع -->
<div id="sell" class="section">
  <h2>🛒 البيع</h2>
  <input type="text" id="barcodeInput" placeholder="🔍 أدخل أو امسح الباركود" oninput="searchProduct(this.value)" autofocus>
  <div id="saleInfo" style="display:none; background:#f9f9f9; border:1px solid #ccc; padding:10px; margin-top:10px;">
    <p><strong>📦 المنتج:</strong> <span id="pName"></span></p>
    <p><strong>💰 سعر الشراء:</strong> <span id="pBuy"></span> DA</p>
    <p><strong>💵 سعر البيع:</strong> <input type="number" id="customSell" style="width:100px;"> DA</p>
    <p><strong>📦 الكمية:</strong> <span id="pQty"></span></p>
    <p><strong>📈 الربح:</strong> <span id="profit"></span> DA</p>
    <button onclick="confirmSale()">✅ تأكيد البيع</button>
  </div>
  <h4>📜 سجل المبيعات</h4>
  <table>
    <thead>
      <tr>
        <th>المنتج</th>
        <th>شراء</th>
        <th>بيع</th>
        <th>الربح</th>
        <th>الوقت</th>
      </tr>
    </thead>
    <tbody id="salesLog"></tbody>
  </table>
</div>
<!-- قسم الإرجاع -->
<div id="return" class="section">
  <h2>🔁 إرجاع منتج</h2>
  <input type="text" id="returnSearch" placeholder="🔍 ابحث بالباركود أو اسم المنتج" oninput="searchReturnProduct()">
  <div id="returnInfo" style="display:none; margin-top:10px; border:1px solid #ccc; padding:10px;">
    <p>📦 الاسم: <span id="returnName"></span></p>
    <p>📦 الكمية الحالية: <span id="returnQty"></span></p>
    <p>🛒 عدد المبيعات: <span id="salesCount">0</span></p>
    <p>↩️ عدد الإرجاعات السابقة: <span id="returnsCount">0</span></p>
    <p>💸 سعر البيع عند البيع: <input type="number" id="returnSell" style="width:100px"> DA</p>
    <button onclick="confirmReturn()">↩️ تأكيد الإرجاع</button>
  </div>
  <div id="returnResult" style="margin-top:10px; font-weight:bold;"></div>
</div>
<!-- قسم الزبائن والديون -->
<div id="clients" class="section">
  <h2>👥 الزبائن والديون</h2>
  <div>
    <input type="text" id="custName" placeholder="👤 اسم الزبون">
    <input type="text" id="custPhone" placeholder="📞 رقم الهاتف">
    <input type="text" id="custAddress" placeholder="📍 العنوان">
    <input type="number" id="custTotal" placeholder="💰 السعر الإجمالي">
    <input type="number" id="custPaid" placeholder="💵 المدفوع">
    <button onclick="addCustomer()">➕ إضافة زبون</button>
  </div>
  <div id="customersList" style="margin-top: 20px;"></div>
</div>
<!-- قسم الإحصائيات -->
<div id="stats" class="section">
  <h2>📊 الإحصائيات</h2>
  <div style="margin-top: 10px;">
    <p>💰 الربح اليومي الصافي: <span id="dailyProfit">0</span> DA</p>
    <p>📅 الربح الشهري التقريبي: <span id="monthlyProfit">0</span> DA</p>
    <button onclick="resetDailyProfit()">🔄 إعادة تعيين الربح اليومي</button>
  </div>
</div>
<!-- قسم الطباعة المعدل حسب الأبعاد المطلوبة -->
<div id="print" class="section">
  <h2>🖨️ طباعة الباركود</h2>
  <label>اختر المنتج:</label>
  <select id="printProduct">
    <option value="">-- اختر منتج --</option>
  </select>
  <label>عدد النسخ:</label>
  <input type="number" id="printQty" min="1" value="1">
  <button onclick="generateBarcodes()">📦 توليد</button>
  <button onclick="printBarcodes()">🖨️ طباعة</button>
  <button onclick="document.getElementById('printArea').innerHTML=''">🗑️ مسح</button>
  <div id="printArea" style="margin-top: 20px;"></div>
</div>
<script src="https://cdn.jsdelivr.net/npm/jsbarcode@3.11.5/dist/JsBarcode.all.min.js"></script>
<script>
// متغيرات عامة
let products = JSON.parse(localStorage.getItem("products") || "[]");
let sales = JSON.parse(localStorage.getItem("sales") || "[]");
let currentProduct = null;
let returnProductIndex = -1;
// وظائف عامة
function showSection(id) {
  document.querySelectorAll(".section").forEach(s => s.classList.remove("active"));
  document.getElementById(id).classList.add("active");
  window.scrollTo(0, 0);
  
  // تحديث البيانات عند فتح القسم
  if(id === 'stock') renderStock();
  if(id === 'clients') loadCustomers();
  if(id === 'stats') updateStats();
  if(id === 'print') loadProductsForPrint();
  if(id === 'sell') loadSales();
}
function generateEAN13() {
  let base = Math.floor(Math.random() * 100000000000).toString().padStart(12, '0');
  let sum = 0;
  for (let i = 0; i < 12; i++) {
    sum += parseInt(base[i]) * (i % 2 === 0 ? 1 : 3);
  }
  let check = (10 - (sum % 10)) % 10;
  return base + check;
}
// قسم إضافة المنتجات
document.getElementById("productForm").addEventListener("submit", function(e) {
  e.preventDefault();
  let name = document.getElementById("productName").value.trim();
  let buy = parseFloat(document.getElementById("buyPrice").value);
  let sell = parseFloat(document.getElementById("sellPrice").value);
  let qty = parseInt(document.getElementById("quantity").value);
  let barcode = document.getElementById("barcode").value.trim() || generateEAN13();
  let product = { name, buy, sell, qty, barcode };
  products.push(product);
  localStorage.setItem("products", JSON.stringify(products));
  document.getElementById("addProductMsg").textContent = "✅ تم حفظ المنتج بنجاح!";
  this.reset();
});
// قسم المخزون
function renderStock() {
  let html = `
    <table>
      <thead>
        <tr>
          <th>الاسم</th>
          <th>شراء</th>
          <th>بيع</th>
          <th>الكمية</th>
          <th>الباركود</th>
          <th>تعديل</th>
          <th>🗑️ حذف</th>
        </tr>
      </thead><tbody>`;
  products.forEach((p, i) => {
    html += `
      <tr>
        <td>${p.name}</td>
        <td>${p.buy} DA</td>
        <td>${p.sell} DA</td>
        <td>${p.qty}</td>
        <td>${p.barcode}</td>
        <td><button onclick="editProduct(${i})">✏️</button></td>
        <td><button onclick="deleteProduct(${i})">🗑️</button></td>
      </tr>`;
  });
  html += "</tbody></table>";
  document.getElementById("tableContainer").innerHTML = html;
}
function deleteProduct(i) {
  if (confirm("❌ هل تريد حذف هذا المنتج؟")) {
    products.splice(i, 1);
    localStorage.setItem("products", JSON.stringify(products));
    renderStock();
  }
}
function editProduct(i) {
  const p = products[i];
  const name = prompt("📦 اسم المنتج:", p.name);
  const buy = prompt("💰 سعر الشراء:", p.buy);
  const sell = prompt("💵 سعر البيع:", p.sell);
  const qty = prompt("📦 الكمية:", p.qty);
  if (name && buy && sell && qty) {
    products[i] = {
      ...p,
      name,
      buy: parseFloat(buy),
      sell: parseFloat(sell),
      qty: parseInt(qty)
    };
    localStorage.setItem("products", JSON.stringify(products));
    renderStock();
  }
}
// قسم البيع
function searchProduct(code) {
  const p = products.find(prod => prod.barcode === code || prod.name.includes(code));
  if (p) {
    currentProduct = p;
    document.getElementById("saleInfo").style.display = "block";
    document.getElementById("pName").textContent = p.name;
    document.getElementById("pBuy").textContent = p.buy;
    document.getElementById("customSell").value = p.sell;
    document.getElementById("pQty").textContent = p.qty;
    calculateProfit();
  } else {
    document.getElementById("saleInfo").style.display = "none";
    currentProduct = null;
  }
}
document.getElementById("customSell").oninput = calculateProfit;
function calculateProfit() {
  if (!currentProduct) return;
  const sell = parseFloat(document.getElementById("customSell").value);
  const profit = sell - currentProduct.buy;
  document.getElementById("profit").textContent = profit.toFixed(2);
}
function confirmSale() {
  if (!currentProduct) return;
  const sell = parseFloat(document.getElementById("customSell").value);
  const profit = sell - currentProduct.buy;
  const time = new Date().toLocaleString();
  const i = products.findIndex(p => p.barcode === currentProduct.barcode);
  if (products[i].qty <= 0) return alert("❌ لا توجد كمية كافية!");
  products[i].qty -= 1;
  localStorage.setItem("products", JSON.stringify(products));
  sales.push({ name: currentProduct.name, buy: currentProduct.buy, sell, profit, time });
  localStorage.setItem("sales", JSON.stringify(sales));
  let dp = parseFloat(localStorage.getItem("dailyProfit") || "0");
  dp += profit;
  localStorage.setItem("dailyProfit", dp);
  alert("✅ تم البيع!");
  currentProduct = null;
  document.getElementById("barcodeInput").value = "";
  document.getElementById("saleInfo").style.display = "none";
  loadSales();
}
function loadSales() {
  const tbody = document.getElementById("salesLog");
  tbody.innerHTML = "";
  sales.slice().reverse().forEach(s => {
    tbody.innerHTML += `
      <tr>
        <td>${s.name}</td>
        <td>${s.buy}</td>
        <td>${s.sell}</td>
        <td>${s.profit}</td>
        <td>${s.time}</td>
      </tr>`;
  });
}
// قسم الإرجاع
function searchReturnProduct() {
  const val = document.getElementById("returnSearch").value.trim();
  const foundIndex = products.findIndex(p => p.barcode === val || p.name.includes(val));
  if (foundIndex !== -1) {
    const product = products[foundIndex];
    returnProductIndex = foundIndex;
    document.getElementById("returnName").innerText = product.name;
    document.getElementById("returnQty").innerText = product.qty;
    document.getElementById("returnSell").value = product.sell;
    
    // حساب عدد المبيعات والإرجاعات
    const salesCount = sales.filter(s => s.name === product.name).length;
    const returns = JSON.parse(localStorage.getItem("returns") || "{}");
    const returnsCount = returns[product.barcode] || 0;
    
    document.getElementById("salesCount").innerText = salesCount;
    document.getElementById("returnsCount").innerText = returnsCount;
    
    document.getElementById("returnInfo").style.display = "block";
  } else {
    document.getElementById("returnInfo").style.display = "none";
  }
}
function confirmReturn() {
  if (returnProductIndex === -1) return;
  const prod = products[returnProductIndex];
  const barcode = prod.barcode;
  const sellBack = parseFloat(document.getElementById("returnSell").value);
  // حساب عدد المبيعات والإرجاعات
  const salesCount = sales.filter(s => s.name === prod.name).length;
  const returns = JSON.parse(localStorage.getItem("returns") || "{}");
  const returnsCount = returns[barcode] || 0;
  if (returnsCount >= salesCount) {
    alert("❌ لا يمكن إرجاع أكثر من عدد المبيعات!");
    return;
  }
  // تحديث الكمية
  prod.qty += 1;
  localStorage.setItem("products", JSON.stringify(products));
  // تحديث الربح اليومي
  let dailyProfit = parseFloat(localStorage.getItem("dailyProfit") || "0");
  dailyProfit -= sellBack;
  localStorage.setItem("dailyProfit", dailyProfit);
  // تحديث سجل الإرجاعات
  returns[barcode] = returnsCount + 1;
  localStorage.setItem("returns", JSON.stringify(returns));
  // إضافة للسجل
  let logs = JSON.parse(localStorage.getItem("salesLog") || "[]");
  logs.unshift(`${new Date().toLocaleString()} - ↩️ إرجاع ${prod.name} - خصم ${sellBack.toFixed(2)} DA`);
  localStorage.setItem("salesLog", JSON.stringify(logs));
  document.getElementById("returnResult").innerText = `↩️ تم إرجاع ${prod.name} | -${sellBack.toFixed(2)} DA من الربح | الإرجاعات: ${returns[barcode]}/${salesCount}`;
  searchReturnProduct();
}
// قسم الزبائن والديون
function loadCustomers() {
  const customers = JSON.parse(localStorage.getItem("customers") || "[]");
  let html = "";
  customers.forEach((cust, i) => {
    const remaining = cust.total - cust.paid;
    html += `
      <div style="border:1px solid #ccc; margin:10px; padding:10px;">
        <strong>👤 ${cust.name}</strong><br>
        📞 ${cust.phone}<br>
        📍 ${cust.address}<br>
        💰 السعر الإجمالي: ${cust.total} DA<br>
        💵 المدفوع: ${cust.paid} DA<br>
        🧾 الباقي: ${remaining} DA<br>
        <button onclick="editCustomer(${i})">✏️ تعديل</button>
        <button onclick="deleteCustomer(${i})">🗑️ حذف</button>
        ${remaining > 0 ? `<button onclick="recoverDebt(${i})">💵 استرجاع الدين</button>` : "✅ مكتمل"}
      </div>
    `;
  });
  document.getElementById("customersList").innerHTML = html;
}
function addCustomer() {
  const name = document.getElementById("custName").value.trim();
  const phone = document.getElementById("custPhone").value.trim();
  const address = document.getElementById("custAddress").value.trim();
  const total = parseFloat(document.getElementById("custTotal").value);
  const paid = parseFloat(document.getElementById("custPaid").value);
  if (!name || isNaN(total) || isNaN(paid)) return alert("يرجى ملء كل الحقول بشكل صحيح");
  const customers = JSON.parse(localStorage.getItem("customers") || "[]");
  customers.push({ name, phone, address, total, paid });
  localStorage.setItem("customers", JSON.stringify(customers));
  loadCustomers();
}
function deleteCustomer(index) {
  const customers = JSON.parse(localStorage.getItem("customers") || "[]");
  if (confirm("❌ هل تريد حذف هذا الزبون؟")) {
    customers.splice(index, 1);
    localStorage.setItem("customers", JSON.stringify(customers));
    loadCustomers();
  }
}
function editCustomer(index) {
  const customers = JSON.parse(localStorage.getItem("customers") || "[]");
  const c = customers[index];
  const name = prompt("✏️ اسم الزبون:", c.name);
  const phone = prompt("📞 رقم الهاتف:", c.phone);
  const address = prompt("📍 العنوان:", c.address);
  const total = parseFloat(prompt("💰 السعر الإجمالي:", c.total));
  const paid = parseFloat(prompt("💵 المدفوع:", c.paid));
  if (!name || isNaN(total) || isNaN(paid)) return alert("البيانات غير صحيحة");
  customers[index] = { name, phone, address, total, paid };
  localStorage.setItem("customers", JSON.stringify(customers));
  loadCustomers();
}
function recoverDebt(index) {
  const customers = JSON.parse(localStorage.getItem("customers") || "[]");
  const c = customers[index];
  const remaining = c.total - c.paid;
  const amount = parseFloat(prompt("💵 كم المبلغ الذي تم دفعه الآن؟", remaining));
  if (isNaN(amount) || amount <= 0) return alert("المبلغ غير صحيح");
  customers[index].paid += amount;
  if (customers[index].paid > customers[index].total) {
    customers[index].paid = customers[index].total;
  }
  localStorage.setItem("customers", JSON.stringify(customers));
  let dailyProfit = parseFloat(localStorage.getItem("dailyProfit") || "0");
  dailyProfit += amount;
  localStorage.setItem("dailyProfit", dailyProfit);
  loadCustomers();
}
// قسم الإحصائيات
function updateStats() {
  const daily = parseFloat(localStorage.getItem("dailyProfit") || "0");
  const monthly = daily * new Date().getDate(); // التقدير الشهري حسب الأيام الحالية
  document.getElementById("dailyProfit").innerText = daily.toFixed(2);
  document.getElementById("monthlyProfit").innerText = monthly.toFixed(2);
}
function resetDailyProfit() {
  if (confirm("❓ هل أنت متأكد أنك تريد تصفير الربح اليومي؟")) {
    localStorage.setItem("dailyProfit", "0");
    updateStats();
  }
}
// قسم الطباعة المعدل حسب الأبعاد المطلوبة
function loadProductsForPrint() {
  const select = document.getElementById("printProduct");
  select.innerHTML = '<option value="">-- اختر منتج --</option>';
  
  products.forEach(p => {
    let option = document.createElement("option");
    option.value = p.barcode;
    option.textContent = p.name;
    select.appendChild(option);
  });
}
function generateBarcodes() {
  const barcode = document.getElementById("printProduct").value;
  const qty = parseInt(document.getElementById("printQty").value);
  const container = document.getElementById("printArea");
  if (!barcode || qty < 1) {
    alert("❗ اختر منتج وعدد صحيح");
    return;
  }
  const product = products.find(p => p.barcode === barcode);
  if (!product) {
    alert("❌ المنتج غير موجود");
    return;
  }
  container.innerHTML = "";
  for (let i = 0; i < qty; i++) {
    const box = document.createElement("div");
    box.className = "barcode-label";
    box.style.border = "1px solid #000";
    box.style.textAlign = "center";
    box.style.padding = "2px";
    box.style.margin = "5px";
    box.style.display = "inline-block";
    box.style.width = "35mm";
    box.style.height = "20mm";
    box.style.overflow = "hidden";
    box.style.fontFamily = "Arial, sans-serif";
    box.style.boxSizing = "border-box";
    box.innerHTML = `
      <div style="font-weight:bold; font-size:10px; color:#2c3e50; margin-bottom:2px;">BAZAR SERDANI</div>
      <div style="margin:2px 0; font-size:9px; font-weight:bold;">Prix: ${formatPrice(product.sell)} DA</div>
      <div style="margin:1px 0 2px 0;">
        <svg class="barcode" data-code="${product.barcode}" style="display:block; margin:0 auto;"></svg>
      </div>
      <div style="font-size:7px; margin-top:2px; word-break: break-all;">${product.barcode}</div>
    `;
    container.appendChild(box);
  }
  document.querySelectorAll(".barcode").forEach(svg => {
    JsBarcode(svg, svg.dataset.code, {
      format: "CODE128",
      displayValue: false,
      width: 1.2,
      height: 20,
      margin: 2
    });
  });
}
function printBarcodes() {
  if (document.getElementById("printArea").children.length === 0) {
    alert("❗ الرجاء توليد الباركود أولاً");
    return;
  }
  window.print();
}
function formatPrice(p) {
  let s = Math.round(p).toString();
  return s.length > 3 ? s.slice(0, -3) + " " + s.slice(-3) : s;
}
// تهيئة أولية
document.addEventListener("DOMContentLoaded", function() {
  loadSales();
  updateStats();
  loadProductsForPrint();
  
  // إضافة حدث Enter لحقل البحث في الإرجاع
  document.getElementById("returnSearch").addEventListener("keypress", function(e) {
    if (e.key === "Enter") {
      searchReturnProduct();
    }
  });
});
</script>
</body>
</html>
