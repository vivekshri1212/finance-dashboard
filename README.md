<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>FinanceIQ Dashboard</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.0/chart.umd.min.js"></script>
<link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display&family=DM+Sans:wght@300;400;500;600;700&display=swap" rel="stylesheet"/>
<style>
:root {
  --bg: #0d0f14;
  --surface: #161b24;
  --surface2: #1e2533;
  --border: #2a3347;
  --accent: #4ade80;
  --accent2: #f472b6;
  --accent3: #60a5fa;
  --text: #e8edf5;
  --muted: #6b7a99;
  --danger: #f87171;
}
* { box-sizing: border-box; margin: 0; padding: 0; }
body { background: var(--bg); color: var(--text); font-family: 'DM Sans', sans-serif; min-height: 100vh; }
 
.sidebar {
  width: 220px; background: var(--surface); border-right: 1px solid var(--border);
  padding: 24px 14px; display: flex; flex-direction: column; gap: 4px;
  position: fixed; top: 0; left: 0; height: 100vh; z-index: 100;
}
.logo { font-family: 'DM Serif Display', serif; font-size: 22px; color: var(--accent); margin-bottom: 28px; padding-left: 10px; }
.nav-item {
  padding: 11px 14px; border-radius: 10px; cursor: pointer; font-size: 14px;
  color: var(--muted); display: flex; align-items: center; gap: 10px;
  transition: all 0.2s; font-weight: 500; border: none; background: none;
  width: 100%; text-align: left; font-family: 'DM Sans', sans-serif;
}
.nav-item:hover { background: var(--surface2); color: var(--text); }
.nav-item.active { background: rgba(74,222,128,0.12); color: var(--accent); }
.nav-icon { font-size: 17px; width: 22px; text-align: center; }
.sidebar-footer { margin-top: auto; padding-top: 20px; border-top: 1px solid var(--border); }
.user-info { display: flex; align-items: center; gap: 10px; padding: 8px; }
.avatar { width: 36px; height: 36px; border-radius: 50%; background: rgba(74,222,128,0.2); display: flex; align-items: center; justify-content: center; font-size: 16px; }
.user-name { font-size: 13px; font-weight: 600; }
.user-role-label { font-size: 11px; color: var(--muted); }
 
.main { margin-left: 220px; display: flex; flex-direction: column; min-height: 100vh; }
.topbar {
  padding: 16px 32px; display: flex; align-items: center; justify-content: space-between;
  border-bottom: 1px solid var(--border); background: var(--surface);
  position: sticky; top: 0; z-index: 50;
}
.page-title { font-family: 'DM Serif Display', serif; font-size: 22px; }
.topbar-right { display: flex; align-items: center; gap: 12px; }
.role-select {
  background: var(--surface2); border: 1px solid var(--border); color: var(--text);
  padding: 8px 14px; border-radius: 8px; font-family: 'DM Sans', sans-serif;
  font-size: 13px; cursor: pointer; outline: none;
}
.role-badge { padding: 5px 14px; border-radius: 20px; font-size: 12px; font-weight: 700; letter-spacing: 0.5px; }
.role-badge.admin { background: rgba(96,165,250,0.15); color: var(--accent3); }
.role-badge.viewer { background: rgba(107,122,153,0.15); color: var(--muted); }
 
.content { padding: 28px 32px; }
.section { display: none; }
.section.active { display: block; animation: fadeIn 0.3s ease; }
@keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: translateY(0); } }
 
.cards { display: grid; grid-template-columns: repeat(3, 1fr); gap: 18px; margin-bottom: 26px; }
.card {
  background: var(--surface); border: 1px solid var(--border); border-radius: 16px;
  padding: 22px 24px; position: relative; overflow: hidden; transition: transform 0.2s, box-shadow 0.2s;
}
.card:hover { transform: translateY(-3px); box-shadow: 0 8px 30px rgba(0,0,0,0.3); }
.card-label { font-size: 11px; color: var(--muted); font-weight: 600; text-transform: uppercase; letter-spacing: 0.9px; margin-bottom: 10px; }
.card-value { font-family: 'DM Serif Display', serif; font-size: 32px; margin-bottom: 6px; }
.card-sub { font-size: 12px; color: var(--muted); }
.card-icon { position: absolute; top: 20px; right: 20px; width: 42px; height: 42px; border-radius: 12px; display: flex; align-items: center; justify-content: center; font-size: 19px; }
.card.balance .card-icon { background: rgba(74,222,128,0.12); }
.card.income .card-icon { background: rgba(96,165,250,0.12); }
.card.expense .card-icon { background: rgba(244,114,182,0.12); }
.card.balance .card-value { color: var(--accent); }
.card.income .card-value { color: var(--accent3); }
.card.expense .card-value { color: var(--accent2); }
 
.charts { display: grid; grid-template-columns: 2fr 1fr; gap: 18px; margin-bottom: 26px; }
.chart-box { background: var(--surface); border: 1px solid var(--border); border-radius: 16px; padding: 22px 24px; }
.chart-title { font-size: 15px; font-weight: 600; margin-bottom: 18px; }
 
.section-header { display: flex; align-items: center; justify-content: space-between; margin-bottom: 18px; flex-wrap: wrap; gap: 12px; }
.section-heading { font-family: 'DM Serif Display', serif; font-size: 20px; }
.controls { display: flex; gap: 10px; flex-wrap: wrap; align-items: center; }
.search-input, .filter-select {
  background: var(--surface); border: 1px solid var(--border); color: var(--text);
  padding: 9px 14px; border-radius: 9px; font-family: 'DM Sans', sans-serif;
  font-size: 13px; outline: none; transition: border-color 0.2s;
}
.search-input:focus, .filter-select:focus { border-color: var(--accent); }
.search-input { width: 190px; }
.add-btn {
  background: var(--accent); color: #0d0f14; border: none; padding: 9px 18px;
  border-radius: 9px; font-family: 'DM Sans', sans-serif; font-size: 13px;
  font-weight: 700; cursor: pointer; transition: all 0.2s;
}
.add-btn:hover { opacity: 0.85; transform: translateY(-1px); }
.add-btn:disabled { opacity: 0.3; cursor: not-allowed; transform: none; }
 
.table-wrap { background: var(--surface); border: 1px solid var(--border); border-radius: 16px; overflow: hidden; }
.tx-table { width: 100%; border-collapse: collapse; }
.tx-table th {
  text-align: left; font-size: 11px; color: var(--muted); font-weight: 600;
  text-transform: uppercase; letter-spacing: 0.8px; padding: 14px 18px;
  border-bottom: 1px solid var(--border);
}
.tx-table td { padding: 14px 18px; border-bottom: 1px solid var(--border); font-size: 14px; }
.tx-table tr:last-child td { border-bottom: none; }
.tx-table tr:hover td { background: var(--surface2); }
.type-badge { padding: 3px 10px; border-radius: 20px; font-size: 11px; font-weight: 700; }
.type-badge.income { background: rgba(74,222,128,0.12); color: var(--accent); }
.type-badge.expense { background: rgba(244,114,182,0.12); color: var(--accent2); }
.amount { font-weight: 600; }
.amount.income { color: var(--accent); }
.amount.expense { color: var(--accent2); }
.action-btn {
  background: var(--surface2); border: 1px solid var(--border); color: var(--text);
  padding: 5px 11px; border-radius: 6px; font-size: 12px; cursor: pointer;
  margin-right: 4px; font-family: 'DM Sans', sans-serif; transition: all 0.2s;
}
.action-btn:hover { border-color: var(--accent); color: var(--accent); }
.del-btn:hover { border-color: var(--danger) !important; color: var(--danger) !important; }
.empty { text-align: center; padding: 48px; color: var(--muted); font-size: 14px; }
 
.modal-overlay {
  display: none; position: fixed; top: 0; left: 0; right: 0; bottom: 0;
  background: rgba(0,0,0,0.65); z-index: 999; align-items: center; justify-content: center;
}
.modal-overlay.open { display: flex; }
.modal {
  background: var(--surface); border: 1px solid var(--border); border-radius: 18px;
  padding: 28px; width: 420px; max-width: 95vw; animation: fadeIn 0.25s ease;
}
.modal-title { font-family: 'DM Serif Display', serif; font-size: 20px; margin-bottom: 22px; }
.form-group { margin-bottom: 16px; }
.form-label { display: block; font-size: 12px; color: var(--muted); font-weight: 600; text-transform: uppercase; letter-spacing: 0.7px; margin-bottom: 7px; }
.form-input, .form-select {
  width: 100%; background: var(--surface2); border: 1px solid var(--border);
  color: var(--text); padding: 10px 14px; border-radius: 9px;
  font-family: 'DM Sans', sans-serif; font-size: 14px; outline: none; transition: border-color 0.2s;
}
.form-input:focus, .form-select:focus { border-color: var(--accent); }
.modal-actions { display: flex; gap: 10px; margin-top: 22px; justify-content: flex-end; }
.cancel-btn {
  background: var(--surface2); border: 1px solid var(--border); color: var(--text);
  padding: 10px 20px; border-radius: 9px; font-family: 'DM Sans', sans-serif; font-size: 14px; cursor: pointer;
}
.save-btn {
  background: var(--accent); color: #0d0f14; border: none; padding: 10px 22px;
  border-radius: 9px; font-family: 'DM Sans', sans-serif; font-size: 14px; font-weight: 700; cursor: pointer;
}
 
.insights-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 18px; }
.insight-card { background: var(--surface); border: 1px solid var(--border); border-radius: 16px; padding: 22px 24px; }
.insight-label { font-size: 11px; color: var(--muted); font-weight: 600; text-transform: uppercase; letter-spacing: 0.8px; margin-bottom: 12px; }
.insight-value { font-family: 'DM Serif Display', serif; font-size: 28px; color: var(--accent); margin-bottom: 4px; }
.insight-sub { font-size: 13px; color: var(--muted); }
.cat-bar { margin-top: 16px; }
.cat-row { display: flex; align-items: center; gap: 10px; margin-bottom: 10px; }
.cat-label { font-size: 13px; width: 100px; color: var(--text); white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
.cat-track { flex: 1; height: 6px; background: var(--surface2); border-radius: 99px; overflow: hidden; }
.cat-fill { height: 100%; border-radius: 99px; background: var(--accent); transition: width 0.5s ease; }
.cat-pct { font-size: 12px; color: var(--muted); width: 36px; text-align: right; }
.month-compare { display: flex; gap: 12px; margin-top: 14px; flex-wrap: wrap; }
.month-col { flex: 1; background: var(--surface2); border-radius: 12px; padding: 14px; text-align: center; min-width: 80px; }
.month-name { font-size: 11px; color: var(--muted); margin-bottom: 6px; text-transform: uppercase; letter-spacing: 0.6px; }
.month-val { font-family: 'DM Serif Display', serif; font-size: 18px; }
.month-val.up { color: var(--accent2); }
.month-val.down { color: var(--accent); }
.obs-list { margin-top: 14px; display: flex; flex-direction: column; gap: 10px; }
.obs-item { display: flex; gap: 10px; align-items: flex-start; font-size: 13px; color: var(--muted); line-height: 1.5; }
.obs-icon { font-size: 16px; flex-shrink: 0; }
 
@media (max-width: 900px) {
  .sidebar { display: none; }
  .main { margin-left: 0; }
  .cards { grid-template-columns: 1fr 1fr; }
  .charts { grid-template-columns: 1fr; }
  .insights-grid { grid-template-columns: 1fr; }
  .content { padding: 20px 16px; }
  .topbar { padding: 14px 16px; }
}
@media (max-width: 600px) {
  .cards { grid-template-columns: 1fr; }
  .search-input { width: 130px; }
}
</style>
</head>
<body>
 
<!-- SIDEBAR -->
<div class="sidebar">
  <div class="logo">💹 FinanceIQ</div>
  <button class="nav-item active" onclick="showSection('dashboard', this)">
    <span class="nav-icon">🏠</span> Dashboard
  </button>
  <button class="nav-item" onclick="showSection('transactions', this)">
    <span class="nav-icon">💳</span> Transactions
  </button>
  <button class="nav-item" onclick="showSection('insights', this)">
    <span class="nav-icon">📊</span> Insights
  </button>
  <div class="sidebar-footer">
    <div class="user-info">
      <div class="avatar">👤</div>
      <div>
        <div class="user-name">Vivek Kumar</div>
        <div class="user-role-label" id="sidebarRole">Admin</div>
      </div>
    </div>
  </div>
</div>
 
<!-- MAIN -->
<div class="main">
  <div class="topbar">
    <div class="page-title" id="pageTitle">Dashboard</div>
    <div class="topbar-right">
      <span style="font-size:13px;color:var(--muted);">Switch Role:</span>
      <select class="role-select" id="roleSelect" onchange="switchRole(this.value)">
        <option value="admin">👑 Admin</option>
        <option value="viewer">👁 Viewer</option>
      </select>
      <span class="role-badge admin" id="roleBadge">ADMIN</span>
    </div>
  </div>
 
  <div class="content">
 
    <!-- DASHBOARD -->
    <div class="section active" id="sec-dashboard">
      <div class="cards">
        <div class="card balance">
          <div class="card-label">Total Balance</div>
          <div class="card-value" id="totalBalance">₹0</div>
          <div class="card-sub">Income minus Expenses</div>
          <div class="card-icon">💰</div>
        </div>
        <div class="card income">
          <div class="card-label">Total Income</div>
          <div class="card-value" id="totalIncome">₹0</div>
          <div class="card-sub" id="incomeCount">0 transactions</div>
          <div class="card-icon">📈</div>
        </div>
        <div class="card expense">
          <div class="card-label">Total Expenses</div>
          <div class="card-value" id="totalExpense">₹0</div>
          <div class="card-sub" id="expenseCount">0 transactions</div>
          <div class="card-icon">📉</div>
        </div>
      </div>
 
      <div class="charts">
        <div class="chart-box">
          <div class="chart-title">Balance Trend (Monthly)</div>
          <div style="position:relative;height:240px;">
            <canvas id="lineChart"></canvas>
          </div>
        </div>
        <div class="chart-box">
          <div class="chart-title">Spending Breakdown</div>
          <div style="position:relative;height:240px;">
            <canvas id="doughnutChart"></canvas>
          </div>
        </div>
      </div>
 
      <div class="section-header">
        <div class="section-heading">Recent Transactions</div>
        <button class="add-btn" id="dashAddBtn" onclick="openModal()">+ Add Transaction</button>
      </div>
      <div class="table-wrap">
        <table class="tx-table">
          <thead><tr><th>Date</th><th>Description</th><th>Category</th><th>Type</th><th>Amount</th></tr></thead>
          <tbody id="recentTbody"></tbody>
        </table>
      </div>
    </div>
 
    <!-- TRANSACTIONS -->
    <div class="section" id="sec-transactions">
      <div class="section-header">
        <div class="section-heading">All Transactions</div>
        <div class="controls">
          <input class="search-input" type="text" placeholder="🔍 Search..." id="searchInput" oninput="renderTransactions()"/>
          <select class="filter-select" id="filterType" onchange="renderTransactions()">
            <option value="all">All Types</option>
            <option value="income">Income</option>
            <option value="expense">Expense</option>
          </select>
          <select class="filter-select" id="filterCategory" onchange="renderTransactions()">
            <option value="all">All Categories</option>
          </select>
          <select class="filter-select" id="sortSelect" onchange="renderTransactions()">
            <option value="date-desc">Newest First</option>
            <option value="date-asc">Oldest First</option>
            <option value="amount-desc">Amount ↓</option>
            <option value="amount-asc">Amount ↑</option>
          </select>
          <button class="add-btn" id="txAddBtn" onclick="openModal()">+ Add</button>
        </div>
      </div>
      <div class="table-wrap">
        <table class="tx-table">
          <thead><tr><th>Date</th><th>Description</th><th>Category</th><th>Type</th><th>Amount</th><th id="actionHead">Actions</th></tr></thead>
          <tbody id="txTbody"></tbody>
        </table>
      </div>
    </div>
 
    <!-- INSIGHTS -->
    <div class="section" id="sec-insights">
      <div class="section-header">
        <div class="section-heading">Insights & Analytics</div>
      </div>
      <div class="insights-grid">
        <div class="insight-card">
          <div class="insight-label">🏆 Highest Spending Category</div>
          <div class="insight-value" id="topCat">—</div>
          <div class="insight-sub" id="topCatAmt">No data</div>
          <div class="cat-bar" id="catBars"></div>
        </div>
        <div class="insight-card">
          <div class="insight-label">📅 Monthly Comparison</div>
          <div class="month-compare" id="monthCompare"></div>
        </div>
        <div class="insight-card">
          <div class="insight-label">💡 Savings Rate</div>
          <div class="insight-value" id="savingsRate">0%</div>
          <div class="insight-sub" id="savingsSub">of income saved</div>
          <div style="margin-top:14px;height:8px;background:var(--surface2);border-radius:99px;overflow:hidden;">
            <div id="savingsBar" style="height:100%;background:var(--accent);border-radius:99px;transition:width 0.5s;width:0%"></div>
          </div>
        </div>
        <div class="insight-card">
          <div class="insight-label">🔍 Observations</div>
          <div class="obs-list" id="obsList"></div>
        </div>
      </div>
    </div>
 
  </div>
</div>
 
<!-- MODAL -->
<div class="modal-overlay" id="modalOverlay">
  <div class="modal">
    <div class="modal-title" id="modalTitle">Add Transaction</div>
    <div class="form-group">
      <label class="form-label">Description</label>
      <input class="form-input" type="text" id="fDesc" placeholder="e.g. Grocery shopping"/>
    </div>
    <div class="form-group">
      <label class="form-label">Amount (₹)</label>
      <input class="form-input" type="number" id="fAmount" placeholder="e.g. 500" min="1"/>
    </div>
    <div class="form-group">
      <label class="form-label">Category</label>
      <select class="form-select" id="fCategory">
        <option>Food</option><option>Shopping</option><option>Transport</option>
        <option>Entertainment</option><option>Health</option><option>Education</option>
        <option>Salary</option><option>Freelance</option><option>Investment</option><option>Other</option>
      </select>
    </div>
    <div class="form-group">
      <label class="form-label">Type</label>
      <select class="form-select" id="fType">
        <option value="income">Income</option>
        <option value="expense">Expense</option>
      </select>
    </div>
    <div class="form-group">
      <label class="form-label">Date</label>
      <input class="form-input" type="date" id="fDate"/>
    </div>
    <div class="modal-actions">
      <button class="cancel-btn" onclick="closeModal()">Cancel</button>
      <button class="save-btn" onclick="saveTransaction()">Save</button>
    </div>
  </div>
</div>
 
<script>
// ===== STATE =====
let role = 'admin';
let editId = null;
let lineChart = null;
let doughnutChart = null;
 
let transactions = [
  { id:1,  date:'2026-03-01', desc:'Monthly Salary',     category:'Salary',        type:'income',  amount:45000 },
  { id:2,  date:'2026-03-03', desc:'Grocery Shopping',   category:'Food',          type:'expense', amount:2800  },
  { id:3,  date:'2026-03-05', desc:'Netflix',             category:'Entertainment', type:'expense', amount:649   },
  { id:4,  date:'2026-03-08', desc:'Freelance Project',  category:'Freelance',     type:'income',  amount:12000 },
  { id:5,  date:'2026-03-10', desc:'Metro Card',         category:'Transport',     type:'expense', amount:500   },
  { id:6,  date:'2026-03-12', desc:'Online Shopping',    category:'Shopping',      type:'expense', amount:3200  },
  { id:7,  date:'2026-03-15', desc:'Doctor Visit',       category:'Health',        type:'expense', amount:800   },
  { id:8,  date:'2026-03-18', desc:'Udemy Course',       category:'Education',     type:'expense', amount:1199  },
  { id:9,  date:'2026-03-20', desc:'Restaurant Dinner',  category:'Food',          type:'expense', amount:1400  },
  { id:10, date:'2026-03-22', desc:'Stock Dividend',     category:'Investment',    type:'income',  amount:3500  },
  { id:11, date:'2026-03-25', desc:'Electricity Bill',   category:'Other',         type:'expense', amount:1200  },
  { id:12, date:'2026-03-28', desc:'Birthday Gift',      category:'Shopping',      type:'expense', amount:900   },
  { id:13, date:'2026-02-01', desc:'Monthly Salary',     category:'Salary',        type:'income',  amount:45000 },
  { id:14, date:'2026-02-05', desc:'Rent',               category:'Other',         type:'expense', amount:8000  },
  { id:15, date:'2026-02-12', desc:'Food Delivery',      category:'Food',          type:'expense', amount:1800  },
  { id:16, date:'2026-02-18', desc:'Freelance Work',     category:'Freelance',     type:'income',  amount:8000  },
  { id:17, date:'2026-02-22', desc:'Shopping Mall',      category:'Shopping',      type:'expense', amount:4200  },
];
let nextId = 18;
 
// ===== UTILS =====
const fmt = n => '₹' + Number(n).toLocaleString('en-IN');
const getIncome  = txs => txs.filter(t => t.type==='income').reduce((s,t)=>s+t.amount,0);
const getExpense = txs => txs.filter(t => t.type==='expense').reduce((s,t)=>s+t.amount,0);
const formatDate = d => new Date(d).toLocaleDateString('en-IN',{day:'2-digit',month:'short',year:'numeric'});
 
// ===== NAVIGATION =====
function showSection(name, el) {
  document.querySelectorAll('.section').forEach(s => s.classList.remove('active'));
  document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
  document.getElementById('sec-'+name).classList.add('active');
  if(el) el.classList.add('active');
  const titles = {dashboard:'Dashboard',transactions:'Transactions',insights:'Insights'};
  document.getElementById('pageTitle').textContent = titles[name];
  if(name==='transactions') renderTransactions();
  if(name==='insights') renderInsights();
  if(name==='dashboard') renderDashboard();
}
 
// ===== ROLE =====
function switchRole(val) {
  role = val;
  const badge = document.getElementById('roleBadge');
  badge.textContent = val.toUpperCase();
  badge.className = 'role-badge ' + val;
  document.getElementById('sidebarRole').textContent = val==='admin' ? 'Admin' : 'Viewer';
  ['dashAddBtn','txAddBtn'].forEach(id => {
    const btn = document.getElementById(id);
    if(btn){ btn.disabled = role==='viewer'; btn.title = role==='viewer'?'Viewers cannot add transactions':''; }
  });
  renderTransactions();
  renderDashboard();
}
 
// ===== DASHBOARD =====
function renderDashboard() {
  const income  = getIncome(transactions);
  const expense = getExpense(transactions);
  document.getElementById('totalBalance').textContent = fmt(income - expense);
  document.getElementById('totalIncome').textContent  = fmt(income);
  document.getElementById('totalExpense').textContent = fmt(expense);
  document.getElementById('incomeCount').textContent  = transactions.filter(t=>t.type==='income').length + ' transactions';
  document.getElementById('expenseCount').textContent = transactions.filter(t=>t.type==='expense').length + ' transactions';
  renderRecentTable();
  renderLineChart();
  renderDoughnutChart();
}
 
function renderRecentTable() {
  const tbody = document.getElementById('recentTbody');
  const recent = [...transactions].sort((a,b)=>new Date(b.date)-new Date(a.date)).slice(0,5);
  if(!recent.length){ tbody.innerHTML='<tr><td colspan="5" class="empty">No transactions yet</td></tr>'; return; }
  tbody.innerHTML = recent.map(t=>`
    <tr>
      <td>${formatDate(t.date)}</td>
      <td>${t.desc}</td>
      <td>${t.category}</td>
      <td><span class="type-badge ${t.type}">${t.type}</span></td>
      <td class="amount ${t.type}">${t.type==='income'?'+':'-'}${fmt(t.amount)}</td>
    </tr>`).join('');
}
 
// ===== CHARTS =====
function renderLineChart() {
  const months = ['Oct','Nov','Dec','Jan','Feb','Mar'];
  const keys   = ['2025-10','2025-11','2025-12','2026-01','2026-02','2026-03'];
  const data   = keys.map(k => {
    const tx = transactions.filter(t=>t.date.startsWith(k));
    return getIncome(tx) - getExpense(tx);
  });
  if(lineChart) lineChart.destroy();
  lineChart = new Chart(document.getElementById('lineChart'),{
    type:'line',
    data:{
      labels:months,
      datasets:[{ label:'Balance', data, borderColor:'#4ade80', backgroundColor:'rgba(74,222,128,0.08)',
        tension:0.4, fill:true, pointBackgroundColor:'#4ade80', pointRadius:5, pointHoverRadius:7 }]
    },
    options:{
      responsive:true, maintainAspectRatio:false,
      plugins:{legend:{display:false}},
      scales:{
        x:{grid:{color:'#2a3347'},ticks:{color:'#6b7a99'}},
        y:{grid:{color:'#2a3347'},ticks:{color:'#6b7a99',callback:v=>'₹'+v.toLocaleString('en-IN')}}
      }
    }
  });
}
 
function renderDoughnutChart() {
  const catMap = {};
  transactions.filter(t=>t.type==='expense').forEach(t=>{ catMap[t.category]=(catMap[t.category]||0)+t.amount; });
  const labels = Object.keys(catMap);
  const data   = Object.values(catMap);
  const colors = ['#4ade80','#f472b6','#60a5fa','#fbbf24','#a78bfa','#34d399','#f87171','#fb923c','#e879f9','#22d3ee'];
  if(doughnutChart) doughnutChart.destroy();
  if(!labels.length) return;
  doughnutChart = new Chart(document.getElementById('doughnutChart'),{
    type:'doughnut',
    data:{ labels, datasets:[{ data, backgroundColor:colors.slice(0,labels.length), borderWidth:0, hoverOffset:6 }] },
    options:{
      responsive:true, maintainAspectRatio:false, cutout:'65%',
      plugins:{ legend:{ position:'bottom', labels:{ color:'#6b7a99', font:{size:11}, padding:10 } } }
    }
  });
}
 
// ===== TRANSACTIONS =====
function renderTransactions() {
  const search     = (document.getElementById('searchInput')||{value:''}).value.toLowerCase();
  const filterType = document.getElementById('filterType').value;
  const filterCat  = document.getElementById('filterCategory').value;
  const sort       = document.getElementById('sortSelect').value;
 
  // Refresh category dropdown
  const cats   = [...new Set(transactions.map(t=>t.category))].sort();
  const catSel = document.getElementById('filterCategory');
  const cur    = catSel.value;
  catSel.innerHTML = '<option value="all">All Categories</option>' +
    cats.map(c=>`<option value="${c}" ${c===cur?'selected':''}>${c}</option>`).join('');
 
  let filtered = transactions.filter(t => {
    const mS = t.desc.toLowerCase().includes(search) || t.category.toLowerCase().includes(search);
    const mT = filterType==='all' || t.type===filterType;
    const mC = filterCat==='all'  || t.category===filterCat;
    return mS && mT && mC;
  });
 
  if(sort==='date-desc')   filtered.sort((a,b)=>new Date(b.date)-new Date(a.date));
  if(sort==='date-asc')    filtered.sort((a,b)=>new Date(a.date)-new Date(b.date));
  if(sort==='amount-desc') filtered.sort((a,b)=>b.amount-a.amount);
  if(sort==='amount-asc')  filtered.sort((a,b)=>a.amount-b.amount);
 
  const actionHead = document.getElementById('actionHead');
  if(actionHead) actionHead.style.display = role==='viewer'?'none':'';
 
  const tbody = document.getElementById('txTbody');
  if(!filtered.length){ tbody.innerHTML='<tr><td colspan="6" class="empty">😕 No transactions found</td></tr>'; return; }
  tbody.innerHTML = filtered.map(t=>`
    <tr>
      <td>${formatDate(t.date)}</td>
      <td>${t.desc}</td>
      <td>${t.category}</td>
      <td><span class="type-badge ${t.type}">${t.type}</span></td>
      <td class="amount ${t.type}">${t.type==='income'?'+':'-'}${fmt(t.amount)}</td>
      <td style="display:${role==='viewer'?'none':''}">
        <button class="action-btn" onclick="editTransaction(${t.id})">✏️ Edit</button>
        <button class="action-btn del-btn" onclick="deleteTransaction(${t.id})">🗑️ Delete</button>
      </td>
    </tr>`).join('');
}
 
function deleteTransaction(id) {
  if(role==='viewer'){ alert('Viewers cannot delete transactions.'); return; }
  if(confirm('Delete this transaction?')){
    transactions = transactions.filter(t=>t.id!==id);
    renderTransactions(); renderDashboard();
  }
}
 
function editTransaction(id) {
  if(role==='viewer') return;
  const t = transactions.find(t=>t.id===id);
  if(!t) return;
  editId = id;
  document.getElementById('modalTitle').textContent = 'Edit Transaction';
  document.getElementById('fDesc').value     = t.desc;
  document.getElementById('fAmount').value   = t.amount;
  document.getElementById('fCategory').value = t.category;
  document.getElementById('fType').value     = t.type;
  document.getElementById('fDate').value     = t.date;
  document.getElementById('modalOverlay').classList.add('open');
}
 
// ===== MODAL =====
function openModal() {
  if(role==='viewer'){ alert('Viewers cannot add transactions.'); return; }
  editId = null;
  document.getElementById('modalTitle').textContent = 'Add Transaction';
  document.getElementById('fDesc').value     = '';
  document.getElementById('fAmount').value   = '';
  document.getElementById('fCategory').value = 'Food';
  document.getElementById('fType').value     = 'expense';
  document.getElementById('fDate').value     = new Date().toISOString().split('T')[0];
  document.getElementById('modalOverlay').classList.add('open');
}
 
function closeModal() { document.getElementById('modalOverlay').classList.remove('open'); }
 
function saveTransaction() {
  const desc     = document.getElementById('fDesc').value.trim();
  const amount   = parseFloat(document.getElementById('fAmount').value);
  const category = document.getElementById('fCategory').value;
  const type     = document.getElementById('fType').value;
  const date     = document.getElementById('fDate').value;
  if(!desc||!amount||!date){ alert('Please fill all fields!'); return; }
  if(amount<=0){ alert('Amount must be greater than 0!'); return; }
  if(editId){
    const idx = transactions.findIndex(t=>t.id===editId);
    transactions[idx] = { id:editId, date, desc, category, type, amount };
  } else {
    transactions.push({ id:nextId++, date, desc, category, type, amount });
  }
  closeModal(); renderTransactions(); renderDashboard();
}
 
document.getElementById('modalOverlay').addEventListener('click', function(e){ if(e.target===this) closeModal(); });
 
// ===== INSIGHTS =====
function renderInsights() {
  const expTx    = transactions.filter(t=>t.type==='expense');
  const catMap   = {};
  expTx.forEach(t=>{ catMap[t.category]=(catMap[t.category]||0)+t.amount; });
  const sorted   = Object.entries(catMap).sort((a,b)=>b[1]-a[1]);
  const totalExp = getExpense(transactions);
  const totalInc = getIncome(transactions);
 
  document.getElementById('topCat').textContent    = sorted.length ? sorted[0][0] : '—';
  document.getElementById('topCatAmt').textContent = sorted.length ? fmt(sorted[0][1])+' spent in this category' : 'No expense data';
 
  document.getElementById('catBars').innerHTML = sorted.slice(0,5).map(([cat,amt])=>`
    <div class="cat-row">
      <div class="cat-label">${cat}</div>
      <div class="cat-track"><div class="cat-fill" style="width:${totalExp?Math.round(amt/totalExp*100):0}%"></div></div>
      <div class="cat-pct">${totalExp?Math.round(amt/totalExp*100):0}%</div>
    </div>`).join('');
 
  const mar    = transactions.filter(t=>t.date.startsWith('2026-03'));
  const feb    = transactions.filter(t=>t.date.startsWith('2026-02'));
  const marExp = getExpense(mar), febExp = getExpense(feb), marInc = getIncome(mar);
  document.getElementById('monthCompare').innerHTML = `
    <div class="month-col"><div class="month-name">Feb Expenses</div><div class="month-val">${fmt(febExp)}</div></div>
    <div class="month-col"><div class="month-name">Mar Expenses</div><div class="month-val ${marExp>febExp?'up':'down'}">${fmt(marExp)}</div></div>
    <div class="month-col"><div class="month-name">Mar Income</div><div class="month-val" style="color:var(--accent3)">${fmt(marInc)}</div></div>`;
 
  const rate = totalInc>0 ? Math.round((totalInc-totalExp)/totalInc*100) : 0;
  document.getElementById('savingsRate').textContent = rate+'%';
  document.getElementById('savingsSub').textContent  = fmt(totalInc-totalExp)+' saved of '+fmt(totalInc);
  document.getElementById('savingsBar').style.width  = Math.max(0,Math.min(100,rate))+'%';
 
  const obs = [];
  if(sorted.length) obs.push({icon:'🔴',text:`Highest spending on <b>${sorted[0][0]}</b> — ${fmt(sorted[0][1])}`});
  obs.push(marExp>febExp
    ? {icon:'⚠️',text:`Expenses increased by ${fmt(marExp-febExp)} vs February`}
    : {icon:'✅',text:`Expenses decreased by ${fmt(febExp-marExp)} vs February`});
  obs.push(rate>=20
    ? {icon:'🎉',text:`Great! You are saving ${rate}% of your income`}
    : {icon:'💡',text:`Try to save at least 20% of income. Currently ${rate}%`});
  obs.push({icon:'📌',text:`${transactions.length} total transactions recorded`});
  document.getElementById('obsList').innerHTML = obs.map(o=>`
    <div class="obs-item"><span class="obs-icon">${o.icon}</span><span>${o.text}</span></div>`).join('');
}
 
// ===== INIT =====
renderDashboard();
</script>
</body>
</html>
