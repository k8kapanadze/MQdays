<!DOCTYPE html>
<html lang="ka">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NurseFlow OS - Professional Edition</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #0f172a;
            --accent: #3b82f6;
            --bg: #f8fafc;
            --card: #ffffff;
            --border: #e2e8f0;
            --text-muted: #64748b;
            --row-real: #f1f5f9;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Inter', sans-serif; }
        body { background-color: var(--bg); color: var(--primary); padding: 2rem; line-height: 1.5; }

        .container { max-width: 1600px; margin: 0 auto; }

        /* Header & Icons */
        .header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 2rem; }
        .logo { display: flex; align-items: center; gap: 12px; font-weight: 700; color: var(--primary); font-size: 1.4rem; letter-spacing: -0.02em; }
        .icon-sm { width: 20px; height: 20px; vertical-align: middle; }

        /* Control Grid */
        .dashboard { display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 1.5rem; margin-bottom: 2.5rem; }
        .card { background: var(--card); padding: 1.5rem; border-radius: 16px; border: 1px solid var(--border); box-shadow: 0 4px 6px -1px rgba(0,0,0,0.05); }
        
        h3 { font-size: 0.75rem; text-transform: uppercase; color: var(--text-muted); margin-bottom: 1.25rem; letter-spacing: 0.1em; font-weight: 700; display: flex; align-items: center; gap: 8px; }
        
        .input-group { display: flex; gap: 10px; margin-bottom: 12px; }
        input, select, textarea { 
            width: 100%; padding: 0.7rem 1rem; border: 1px solid var(--border); 
            border-radius: 10px; outline: none; font-size: 0.9rem; transition: all 0.2s; background: #fff;
        }
        input:focus, select:focus { border-color: var(--accent); box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1); }

        .btn { 
            padding: 0.7rem 1.2rem; border-radius: 10px; border: none; font-weight: 600; 
            cursor: pointer; transition: 0.2s; font-size: 0.85rem; display: inline-flex; align-items: center; justify-content: center; gap: 8px;
        }
        .btn-primary { background: var(--accent); color: white; }
        .btn-secondary { background: #fff; color: var(--primary); border: 1px solid var(--border); }
        .btn-danger { background: #fff; color: #ef4444; border: 1px solid #fee2e2; }
        .btn:hover { transform: translateY(-1px); box-shadow: 0 4px 12px rgba(0,0,0,0.05); }

        /* Modern Table */
        .table-container { 
            background: var(--card); border-radius: 20px; border: 1px solid var(--border); 
            overflow: auto; max-height: 70vh; box-shadow: 0 10px 15px -3px rgba(0,0,0,0.04);
        }
        table { width: 100%; border-collapse: collapse; table-layout: fixed; min-width: 1100px; }
        th, td { border: 1px solid var(--border); text-align: center; padding: 14px; position: relative; }
        
        th { background: #fdfeff; color: var(--text-muted); font-weight: 600; font-size: 0.8rem; position: sticky; top: 0; z-index: 10; }
        .sticky-col { position: sticky; left: 0; background: white; z-index: 20; border-right: 2px solid var(--border); width: 110px; font-weight: 700; color: var(--primary); }

        .row-plan { background: #ffffff; color: var(--text-muted); }
        .row-real { background: var(--row-real); cursor: pointer; color: var(--accent); font-weight: 700; font-size: 1.05rem; }
        
        .row-total { background: #f8fafc; border-top: 2px solid var(--border); }
        .total-cell { padding: 15px; font-size: 0.85rem; line-height: 1.6; }
        .total-label { color: var(--text-muted); font-weight: 500; }
        .total-val { color: var(--primary); font-weight: 700; }

        .badge-note { width: 8px; height: 8px; background: #10b981; border-radius: 50%; display: inline-block; margin-left: 6px; border: 2px solid white; }

        /* Modal */
        .modal { display: none; position: fixed; inset: 0; background: rgba(15, 23, 42, 0.4); backdrop-filter: blur(8px); z-index: 1000; }
        .modal-content { background: white; width: 480px; margin: 100px auto; padding: 2.5rem; border-radius: 24px; box-shadow: 0 25px 50px -12px rgba(0,0,0,0.25); }
        
        @media print { .no-print { display: none !important; } .table-container { max-height: none; border: none; } }
    </style>
</head>
<body>

<div class="container">
    <div class="header no-print">
        <div class="logo">
            <svg class="icon-sm" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M12 21a9 9 0 1 0 0-18 9 9 0 0 0 0 18ZM9 12h6M12 9v6"/></svg>
            NURSEFLOW <span style="font-weight: 300; opacity: 0.6;">OS</span>
        </div>
        <button class="btn btn-secondary" onclick="window.print()">
            <svg class="icon-sm" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M6 9V2h12v7M6 18H4a2 2 0 0 1-2-2v-5a2 2 0 0 1 2-2h16a2 2 0 0 1 2 2v5a2 2 0 0 1-2 2h-2M6 14h12v8H6z"/></svg>
            ბეჭდვა
        </button>
    </div>

    <div class="dashboard no-print">
        <div class="card">
            <h3>
                <svg class="icon-sm" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M16 21v-2a4 4 0 0 0-4-4H6a4 4 0 0 0-4 4v2"/><circle cx="9" cy="7" r="4"/><path d="M22 21v-2a4 4 0 0 0-3-3.87M16 3.13a4 4 0 0 1 0 7.75"/></svg>
                პერსონალის მართვა
            </h3>
            <div class="input-group">
                <input type="text" id="nurseName" placeholder="ექთნის სახელი">
                <button class="btn btn-primary" onclick="addNurse()">დამატება</button>
            </div>
            <div class="input-group">
                <select id="mSelect" onchange="render()"></select>
                <select id="ySelect" onchange="render()"></select>
            </div>
        </div>

        <div class="card">
            <h3>
                <svg class="icon-sm" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="10"/><polyline points="12 6 12 12 16 14"/></svg>
                რეალური აღრიცხვა
            </h3>
            <div style="display: grid; grid-template-columns: 2fr 1fr 1fr; gap: 10px; margin-bottom: 12px;">
                <select id="realNurse"></select>
                <input type="number" id="realDay" placeholder="დღე" min="1" max="31">
                <select id="realHours">
                    <option value="8">8 სთ</option>
                    <option value="16">16 სთ</option>
                    <option value="24">24 სთ</option>
                    <option value="0">0</option>
                </select>
            </div>
            <button class="btn btn-primary" style="width: 100%;" onclick="saveRealTime()">დაფიქსირება</button>
        </div>

        <div class="card">
            <h3>
                <svg class="icon-sm" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="m12 14 4-4-4-4M3 3.41 7.59 8 3 12.59M16 18l4-4-4-4"/></svg>
                ავტომატიზაცია
            </h3>
            <button class="btn btn-secondary" style="width: 100%; margin-bottom: 10px;" onclick="fillYear()">წლის გეგმის შევსება</button>
            <button class="btn btn-danger" style="width: 100%;" onclick="clearSpecificMonth()">
                <svg class="icon-sm" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M3 6h18M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"/></svg>
                თვიური გეგმის გასუფთავება
            </button>
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

<div id="journalModal" class="modal">
    <div class="modal-content">
        <h3 id="jTitle" style="color: var(--primary); font-size: 1.1rem; margin-bottom: 1.5rem;">დღიური</h3>
        <p style="font-size: 0.85rem; color: var(--text-muted); margin-bottom: 1rem; line-height: 1.4;">
            ჩაწერეთ კლინიკური შემთხვევა ან გამოცდილება. რა ისწავლეთ? / როგორ გაერთეთ?
        </p>
        <textarea id="jText" rows="7" placeholder="აღწერეთ სამუშაო პროცესი..."></textarea>
        <div style="display:flex; gap:12px; margin-top: 2rem;">
            <button class="btn btn-secondary" style="flex:1" onclick="closeModal()">დახურვა</button>
            <button class="btn btn-primary" style="flex:1" onclick="saveJ()">შენახვა</button>
        </div>
    </div>
</div>

<script>
    let state = JSON.parse(localStorage.getItem('nurse_flow_pro_v5')) || { nurses: [], shifts: {} };
    const months = ["იანვარი", "თებერვალი", "მარტი", "აპრილი", "მაისი", "ივნისი", "ივლისი", "აგვისტო", "სექტემბერი", "ოქტომბერი", "ნოემბერი", "დეკემბერი"];
    let activeKey = null;

    function init() {
        const ms = document.getElementById('mSelect');
        const ys = document.getElementById('ySelect');
        if(ms.options.length === 0) {
            months.forEach((m, i) => ms.add(new Option(m, i)));
            for(let y=2024; y<=2026; y++) ys.add(new Option(y, y));
            ms.value = new Date().getMonth();
            ys.value = new Date().getFullYear();
        }
        render();
    }

    function addNurse() {
        const name = document.getElementById('nurseName').value.trim();
        if(name && !state.nurses.includes(name)) {
            state.nurses.push(name);
            document.getElementById('nurseName').value = "";
            save(); render();
        }
    }

    function saveRealTime() {
        const nurse = document.getElementById('realNurse').value;
        const day = document.getElementById('realDay').value;
        const hours = document.getElementById('realHours').value;
        const m = document.getElementById('mSelect').value;
        const y = document.getElementById('ySelect').value;

        if(!nurse || !day) return alert("მიუთითეთ მონაცემები");
        const k = `${nurse}_${y}_${m}_${day}`;
        if(!state.shifts[k]) state.shifts[k] = {};
        state.shifts[k].real = hours;
        save(); render();
    }

    function render() {
        const m = parseInt(document.getElementById('mSelect').value);
        const y = parseInt(document.getElementById('ySelect').value);
        const days = new Date(y, m + 1, 0).getDate();
        
        document.getElementById('realNurse').innerHTML = state.nurses.map(n => `<option value="${n}">${n}</option>`).join('');

        // Header
        let hHTML = `<tr><th class="sticky-col">დღე</th>`;
        state.nurses.forEach(n => {
            hHTML += `<th>${n} <span class="no-print" style="color:#e2e8f0; cursor:pointer; margin-left:8px" onclick="delNurse('${n}')">✕</span></th>`;
        });
        hHTML += `</tr>`;
        document.getElementById('hTable').innerHTML = hHTML;

        // Rows
        let bHTML = "";
        let totals = state.nurses.map(() => ({ plan: 0, real: 0 }));

        for(let d=1; d<=days; d++) {
            // Plan Row
            bHTML += `<tr class="row-plan"><td class="sticky-col">${d} <span style="font-weight:400; font-size:0.7rem; opacity:0.5;">გეგმა</span></td>`;
            state.nurses.forEach((n, idx) => {
                const k = `${n}_${y}_${m}_${d}`;
                const v = state.shifts[k]?.plan || "";
                totals[idx].plan += parseInt(v) || 0;
                bHTML += `<td>
                    <select onchange="upd('${k}','plan',this.value)" style="border:none; background:transparent; font-size:0.8rem; color:#64748b; text-align-last:center;">
                        <option value=""></option>
                        <option value="8" ${v=='8'?'selected':''}>8</option>
                        <option value="16" ${v=='16'?'selected':''}>16</option>
                        <option value="24" ${v=='24'?'selected':''}>24</option>
                    </select>
                </td>`;
            });
            bHTML += `</tr>`;

            // Real Row
            bHTML += `<tr class="row-real"><td class="sticky-col">${d} <span style="font-weight:400; font-size:0.7rem; opacity:0.5;">რეალური</span></td>`;
            state.nurses.forEach((n, idx) => {
                const k = `${n}_${y}_${m}_${d}`;
                const rv = state.shifts[k]?.real || "-";
                const hasNote = state.shifts[k]?.note ? `<span class="badge-note"></span>` : "";
                totals[idx].real += parseInt(rv) || 0;
                bHTML += `<td onclick="openJ('${k}', '${n}', ${d})">${rv} ${hasNote}</td>`;
            });
            bHTML += `</tr>`;
        }
        document.getElementById('bTable').innerHTML = bHTML;

        // Footer
        let fHTML = `<tr class="row-total"><td class="sticky-col">თვიური ჯამი</td>`;
        totals.forEach(t => {
            fHTML += `<td class="total-cell">
                <div class="total-label">გეგმა: <span class="total-val">${t.plan}</span></div>
                <div class="total-label">რეალური: <span class="total-val" style="color:var(--accent)">${t.real}</span></div>
            </td>`;
        });
        fHTML += `</tr>`;
        document.getElementById('fTable').innerHTML = fHTML;
    }

    function upd(k, f, v) {
        if(!state.shifts[k]) state.shifts[k] = {};
        state.shifts[k][f] = v;
        save();
    }

    function clearSpecificMonth() {
        const monthNum = prompt("რომელი თვის გეგმა წავშალო? (ჩაწერეთ ნომერი 1-დან 12-მდე):");
        if (!monthNum || isNaN(monthNum) || monthNum < 1 || monthNum > 12) return;
        
        const mIndex = parseInt(monthNum) - 1;
        const year = document.getElementById('ySelect').value;

        if (confirm(`${months[mIndex]} თვის მთელი გეგმა წაიშლება. დარწმუნებული ხართ?`)) {
            Object.keys(state.shifts).forEach(key => {
                if (key.includes(`_${year}_${mIndex}_`)) {
                    state.shifts[key].plan = "";
                }
            });
            save(); render();
        }
    }

    function fillYear() {
        const m = parseInt(document.getElementById('mSelect').value);
        const y = parseInt(document.getElementById('ySelect').value);
        state.nurses.forEach(nurse => {
            let startD = null;
            let hrs = 24;
            for(let d=1; d<=31; d++) {
                if(state.shifts[`${nurse}_${y}_${m}_${d}`]?.plan) {
                    startD = new Date(y, m, d);
                    hrs = state.shifts[`${nurse}_${y}_${m}_${d}`].plan;
                    break;
                }
            }
            if(startD) {
                let curr = new Date(startD);
                while(curr.getFullYear() <= 2026) {
                    const key = `${nurse}_${curr.getFullYear()}_${curr.getMonth()}_${curr.getDate()}`;
                    if(!state.shifts[key]) state.shifts[key] = {};
                    state.shifts[key].plan = hrs;
                    curr.setDate(curr.getDate() + 4);
                }
            }
        });
        save(); render(); alert("წლის გეგმა შეივსო!");
    }

    function openJ(k, n, d) {
        activeKey = k;
        document.getElementById('jTitle').innerText = `${n} | ${d} რიცხვი`;
        document.getElementById('jText').value = state.shifts[k]?.note || "";
        document.getElementById('journalModal').style.display = 'block';
    }

    function saveJ() {
        if(!state.shifts[activeKey]) state.shifts[activeKey] = {};
        state.shifts[activeKey].note = document.getElementById('jText').value;
        save(); render(); closeModal();
    }

    function closeModal() { document.getElementById('journalModal').style.display = 'none'; }
    function delNurse(n) { if(confirm('წავშალო ექთანი?')) { state.nurses = state.nurses.filter(x => x !== n); save(); render(); } }
    function save() { localStorage.setItem('nurse_flow_pro_v5', JSON.stringify(state)); }

    init();
</script>
</body>
</html>
