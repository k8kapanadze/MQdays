<!DOCTYPE html>
<html lang="ka">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NurseFlow Ultra-Min</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #0f172a;
            --accent: #2563eb;
            --bg: #f8fafc;
            --border: #e2e8f0;
            --row-real: #eff6ff; /* ნაზი ცისფერი რეალური საათებისთვის */
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Inter', sans-serif; }
        body { background-color: var(--bg); color: var(--primary); padding: 2rem; }

        .container { max-width: 1600px; margin: 0 auto; }

        /* Header & Logo */
        .header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 2rem; }
        .logo { display: flex; align-items: center; gap: 10px; font-weight: 700; font-size: 1.3rem; color: var(--primary); }
        
        /* Dashboard */
        .dashboard { display: grid; grid-template-columns: repeat(auto-fit, minmax(280px, 1fr)); gap: 1.2rem; margin-bottom: 2rem; }
        .card { background: #fff; padding: 1.5rem; border-radius: 12px; border: 1px solid var(--border); box-shadow: 0 1px 3px rgba(0,0,0,0.05); }
        
        h3 { font-size: 0.7rem; text-transform: uppercase; color: #64748b; margin-bottom: 1rem; letter-spacing: 0.05em; font-weight: 700; }
        
        .input-group { display: flex; gap: 8px; margin-bottom: 10px; }
        input, select { 
            width: 100%; padding: 0.6rem; border: 1px solid var(--border); 
            border-radius: 6px; outline: none; font-size: 0.9rem;
        }

        .btn { 
            padding: 0.6rem 1rem; border-radius: 6px; border: none; font-weight: 600; 
            cursor: pointer; font-size: 0.85rem; transition: 0.2s;
        }
        .btn-primary { background: var(--accent); color: white; }
        .btn-secondary { background: #fff; color: var(--primary); border: 1px solid var(--border); }
        .btn-danger { background: #fff; color: #ef4444; border: 1px solid #fee2e2; }

        /* Table */
        .table-container { background: #fff; border-radius: 12px; border: 1px solid var(--border); overflow: auto; max-height: 75vh; }
        table { width: 100%; border-collapse: collapse; table-layout: fixed; }
        th, td { border: 1px solid var(--border); text-align: center; padding: 12px; }
        
        th { background: #f1f5f9; font-weight: 600; font-size: 0.85rem; position: sticky; top: 0; z-index: 10; }
        .sticky-col { position: sticky; left: 0; background: #fff; z-index: 20; border-right: 2px solid var(--border); width: 60px; font-weight: 700; }

        .row-plan { background: #ffffff; color: #94a3b8; } /* გეგმა - ნაცრისფერი */
        .row-real { background: var(--row-real); color: var(--accent); font-weight: 700; cursor: pointer; } /* რეალური - ცისფერი */
        
        .row-total { background: #f8fafc; font-weight: 700; font-size: 0.85rem; }
        .badge-dot { width: 6px; height: 6px; background: #10b981; border-radius: 50%; display: inline-block; margin-left: 4px; }

        @media print { .no-print { display: none !important; } }
    </style>
</head>
<body>

<div class="container">
    <div class="header no-print">
        <div class="logo">
            <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M12 21a9 9 0 1 0 0-18 9 9 0 0 0 0 18ZM9 12h6M12 9v6"/></svg>
            NURSEFLOW
        </div>
        <button class="btn btn-secondary" onclick="window.print()">ბეჭდვა</button>
    </div>

    <div class="dashboard no-print">
        <div class="card">
            <h3>პერსონალი</h3>
            <div class="input-group">
                <input type="text" id="nurseName" placeholder="ექთანი">
                <button class="btn btn-primary" onclick="addNurse()">+</button>
            </div>
            <div class="input-group">
                <select id="mSelect" onchange="render()"></select>
                <select id="ySelect" onchange="render()"></select>
            </div>
        </div>

        <div class="card">
            <h3>რეალური აღრიცხვა</h3>
            <div style="display: grid; grid-template-columns: 2fr 1fr 1fr; gap: 8px;">
                <select id="realNurse"></select>
                <input type="number" id="realDay" placeholder="დღე">
                <select id="realHours">
                    <option value="8">8</option><option value="16">16</option><option value="24">24</option><option value="0">0</option>
                </select>
            </div>
            <button class="btn btn-primary" style="width: 100%; margin-top: 10px;" onclick="saveRealTime()">შენახვა</button>
        </div>

        <div class="card">
            <h3>მართვა</h3>
            <button class="btn btn-secondary" style="width: 100%; margin-bottom: 8px;" onclick="fillYear()">წლის შევსება</button>
            <button class="btn btn-danger" style="width: 100%;" onclick="clearMonth()">თვის გეგმის გასუფთავება</button>
        </div>
    </div>

    <div class="table-container">
        <table id="mainTable">
            <thead id="hTable"></thead>
            <tbody id="bTable"></tbody>
            <tfoot id="fTable"></tfoot>
        </table>
    </div>
</div>

<div id="jModal" class="no-print" style="display:none; position:fixed; inset:0; background:rgba(0,0,0,0.4); z-index:1000;">
    <div style="background:#fff; width:400px; margin:100px auto; padding:2rem; border-radius:12px;">
        <h3 id="jTitle"></h3>
        <p style="font-size:0.8rem; color:#64748b; margin-bottom:10px;">კლინიკური შემთხვევა, რა ისწავლეთ? / როგორ გაერთეთ?</p>
        <textarea id="jText" rows="6" style="width:100%; padding:10px; border:1px solid #ddd; border-radius:6px;"></textarea>
        <div style="display:flex; gap:10px; margin-top:1rem;">
            <button class="btn btn-secondary" style="flex:1" onclick="closeM()">დახურვა</button>
            <button class="btn btn-primary" style="flex:1" onclick="saveJ()">შენახვა</button>
        </div>
    </div>
</div>

<script>
    let state = JSON.parse(localStorage.getItem('nurse_flow_v6')) || { nurses: [], shifts: {} };
    const months = ["იანვარი", "თებერვალი", "მარტი", "აპრილი", "მაისი", "ივნისი", "ივლისი", "აგვისტო", "სექტემბერი", "ოქტომბერი", "ნოემბერი", "დეკემბერი"];
    let activeKey = null;

    function init() {
        const ms = document.getElementById('mSelect');
        const ys = document.getElementById('ySelect');
        if(ms.options.length === 0) {
            months.forEach((m, i) => ms.add(new Option(m, i)));
            for(let y=2024; y<=2026; y++) ys.add(new Option(y, y));
            ms.value = new Date().getMonth(); ys.value = new Date().getFullYear();
        }
        render();
    }

    function addNurse() {
        const n = document.getElementById('nurseName').value.trim();
        if(n && !state.nurses.includes(n)) { state.nurses.push(n); save(); render(); }
    }

    function saveRealTime() {
        const n = document.getElementById('realNurse').value;
        const d = document.getElementById('realDay').value;
        const h = document.getElementById('realHours').value;
        const m = document.getElementById('mSelect').value;
        const y = document.getElementById('ySelect').value;
        if(!n || !d) return;
        const k = `${n}_${y}_${m}_${d}`;
        if(!state.shifts[k]) state.shifts[k] = {};
        state.shifts[k].real = h;
        save(); render();
    }

    function render() {
        const m = parseInt(document.getElementById('mSelect').value);
        const y = parseInt(document.getElementById('ySelect').value);
        const days = new Date(y, m + 1, 0).getDate();
        document.getElementById('realNurse').innerHTML = state.nurses.map(n => `<option value="${n}">${n}</option>`).join('');

        let hHTML = `<tr><th class="sticky-col">📅</th>`;
        state.nurses.forEach(n => hHTML += `<th>${n} <span class="no-print" style="color:#ccc;cursor:pointer" onclick="delN('${n}')">✕</span></th>`);
        hHTML += `</tr>`;
        document.getElementById('hTable').innerHTML = hHTML;

        let bHTML = "";
        let totals = state.nurses.map(() => ({ p: 0, r: 0 }));

        for(let d=1; d<=days; d++) {
            // Plan Row
            bHTML += `<tr class="row-plan"><td class="sticky-col">${d}</td>`;
            state.nurses.forEach((n, idx) => {
                const k = `${n}_${y}_${m}_${d}`;
                const v = state.shifts[k]?.plan || "";
                totals[idx].p += parseInt(v) || 0;
                bHTML += `<td><input type="text" value="${v}" onchange="upd('${k}','plan',this.value)" style="width:100%; border:none; text-align:center; color:inherit; background:transparent;"></td>`;
            });
            bHTML += `</tr>`;

            // Real Row
            bHTML += `<tr class="row-real"><td class="sticky-col">${d}</td>`;
            state.nurses.forEach((n, idx) => {
                const k = `${n}_${y}_${m}_${d}`;
                const rv = state.shifts[k]?.real || "-";
                const dot = state.shifts[k]?.note ? `<span class="badge-dot"></span>` : "";
                totals[idx].r += parseInt(rv) || 0;
                bHTML += `<td onclick="openM('${k}','${n}',${d})">${rv} ${dot}</td>`;
            });
            bHTML += `</tr>`;
        }
        document.getElementById('bTable').innerHTML = bHTML;

        let fHTML = `<tr class="row-total"><td class="sticky-col">∑</td>`;
        totals.forEach(t => fHTML += `<td>${t.p}<br><span style="color:var(--accent)">${t.r}</span></td>`);
        document.getElementById('fTable').innerHTML = fHTML;
    }

    function clearMonth() {
        const input = prompt("რომელი თვის გეგმა წავშალო? (ჩაწერეთ ნომერი 1-12):");
        if(!input) return;
        const m = parseInt(input) - 1;
        const y = document.getElementById('ySelect').value;
        Object.keys(state.shifts).forEach(k => {
            if(k.includes(`_${y}_${m}_`)) state.shifts[k].plan = "";
        });
        save(); render();
    }

    function upd(k, f, v) { if(!state.shifts[k]) state.shifts[k] = {}; state.shifts[k][f] = v; save(); }
    function openM(k, n, d) { activeKey = k; document.getElementById('jTitle').innerText = `${n} | ${d} რიცხვი`; document.getElementById('jText').value = state.shifts[k]?.note || ""; document.getElementById('jModal').style.display='block'; }
    function saveJ() { if(!state.shifts[activeKey]) state.shifts[activeKey] = {}; state.shifts[activeKey].note = document.getElementById('jText').value; save(); render(); closeM(); }
    function closeM() { document.getElementById('jModal').style.display='none'; }
    function delN(n) { if(confirm('წავშალო?')) { state.nurses = state.nurses.filter(x => x !== n); save(); render(); } }
    function fillYear() {
        const m = parseInt(document.getElementById('mSelect').value);
        const y = parseInt(document.getElementById('ySelect').value);
        state.nurses.forEach(nurse => {
            let startD = null; let hrs = 24;
            for(let d=1; d<=31; d++) { if(state.shifts[`${nurse}_${y}_${m}_${d}`]?.plan) { startD = new Date(y, m, d); hrs = state.shifts[`${nurse}_${y}_${m}_${d}`].plan; break; } }
            if(startD) {
                let c = new Date(startD);
                while(c.getFullYear() <= 2026) {
                    const k = `${nurse}_${c.getFullYear()}_${c.getMonth()}_${c.getDate()}`;
                    if(!state.shifts[k]) state.shifts[k] = {};
                    state.shifts[k].plan = hrs;
                    c.setDate(c.getDate() + 4);
                }
            }
        });
        save(); render();
    }
    function save() { localStorage.setItem('nurse_flow_v6', JSON.stringify(state)); }
    init();
</script>
</body>
</html>
