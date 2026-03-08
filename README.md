<!DOCTYPE html>
<html lang="ka">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Nurse Management System - Professional</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #1e293b;
            --accent: #2563eb;
            --bg: #f1f5f9;
            --card: #ffffff;
            --border: #e2e8f0;
            --real-row: #f8fafc;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Inter', sans-serif; }
        body { background-color: var(--bg); color: var(--primary); padding: 1.5rem; }

        .container { max-width: 1600px; margin: 0 auto; }

        /* Navigation & Logo */
        .header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 1.5rem; }
        .logo { display: flex; align-items: center; gap: 8px; font-weight: 700; color: var(--accent); }

        /* Control Panels */
        .dashboard { display: grid; grid-template-columns: 1fr 1.5fr 1fr; gap: 1rem; margin-bottom: 1.5rem; }
        .card { background: var(--card); padding: 1.25rem; border-radius: 12px; border: 1px solid var(--border); box-shadow: 0 1px 3px rgba(0,0,0,0.1); }
        
        h3 { font-size: 0.85rem; text-transform: uppercase; color: #64748b; margin-bottom: 1rem; letter-spacing: 0.05em; }
        
        .input-group { display: flex; gap: 8px; margin-bottom: 10px; }
        input, select, textarea { 
            width: 100%; padding: 0.6rem; border: 1.5px solid var(--border); 
            border-radius: 6px; outline: none; font-size: 0.9rem;
        }
        input:focus { border-color: var(--accent); }

        .btn { 
            padding: 0.6rem 1rem; border-radius: 6px; border: none; font-weight: 600; 
            cursor: pointer; transition: 0.2s; font-size: 0.85rem;
        }
        .btn-primary { background: var(--accent); color: white; }
        .btn-secondary { background: #f1f5f9; color: var(--primary); border: 1px solid var(--border); }
        .btn:hover { opacity: 0.9; }

        /* Table Area */
        .table-container { 
            background: var(--card); border-radius: 12px; border: 1px solid var(--border); 
            overflow: auto; max-height: 70vh; 
        }
        table { width: 100%; border-collapse: collapse; table-layout: fixed; }
        th, td { border: 1px solid var(--border); text-align: center; padding: 10px; position: relative; }
        
        th { background: #f8fafc; color: #475569; font-weight: 600; font-size: 0.8rem; }
        .sticky-col { position: sticky; left: 0; background: white; z-index: 20; border-right: 2px solid var(--border); width: 80px; }

        .row-plan { background: #ffffff; }
        .row-real { background: var(--real-row); cursor: pointer; color: var(--accent); font-weight: 700; }
        
        .badge-note { width: 5px; height: 5px; background: #10b981; border-radius: 50%; display: inline-block; margin-left: 3px; }

        /* Modal */
        .modal { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.5); backdrop-filter: blur(2px); z-index: 1000; }
        .modal-content { background: white; width: 400px; margin: 100px auto; padding: 1.5rem; border-radius: 12px; }

        @media print { .no-print { display: none !important; } }
    </style>
</head>
<body>

<div class="container">
    <div class="header no-print">
        <div class="logo">
            <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M11 2a2 2 0 0 0-2 2v5H4a2 2 0 0 0-2 2v2c0 1.1.9 2 2 2h5v5c0 1.1.9 2 2 2h2a2 2 0 0 0 2-2v-5h5a2 2 0 0 0 2-2v-2a2 2 0 0 0-2-2h-5V4a2 2 0 0 0-2-2h-2z"/></svg>
            NURSE OS
        </div>
        <button class="btn btn-secondary" onclick="window.print()">ბეჭდვის რეჟიმი</button>
    </div>

    <div class="dashboard no-print">
        <div class="card">
            <h3>პერსონალის მართვა</h3>
            <div class="input-group">
                <input type="text" id="nurseName" placeholder="ექთნის სახელი">
                <button class="btn btn-primary" onclick="addNurse()">+</button>
            </div>
            <div class="input-group">
                <select id="mSelect" onchange="render()"></select>
                <select id="ySelect" onchange="render()"></select>
            </div>
        </div>

        <div class="card">
            <h3>რეალური დროის დაფიქსირება</h3>
            <div style="display: grid; grid-template-columns: 2fr 1fr 1.5fr; gap: 8px;">
                <select id="realNurse"></select>
                <input type="number" id="realDay" placeholder="რიცხვი" min="1" max="31">
                <select id="realHours">
                    <option value="8">8 სთ</option>
                    <option value="16">16 სთ</option>
                    <option value="24">24 სთ</option>
                    <option value="0">0 (გაცდენა)</option>
                </select>
            </div>
            <button class="btn btn-primary" style="width: 100%; margin-top: 10px;" onclick="saveRealTime()">მონაცემის ასახვა ცხრილში</button>
        </div>

        <div class="card">
            <h3>ავტომატიზაცია</h3>
            <button class="btn btn-secondary" style="width: 100%; margin-bottom: 8px;" onclick="fillYear()">წლის გეგმის შევსება (1/4)</button>
            <button class="btn btn-secondary" style="width: 100%; color: #ef4444;" onclick="resetData()">მონაცემების გასუფთავება</button>
        </div>
    </div>

    <div class="table-container">
        <table id="mainTable">
            <thead id="hTable"></thead>
            <tbody id="bTable"></tbody>
        </table>
    </div>
</div>

<div id="journalModal" class="modal">
    <div class="modal-content">
        <h3 id="jTitle">დღიური</h3>
        <textarea id="jText" rows="6" placeholder="ჩაწერეთ შენიშვნა ან კლინიკური შემთხვევა..."></textarea>
        <div style="display:flex; gap:10px; margin-top: 1rem;">
            <button class="btn btn-secondary" style="flex:1" onclick="closeModal()">დახურვა</button>
            <button class="btn btn-primary" style="flex:1" onclick="saveJ()">შენახვა</button>
        </div>
    </div>
</div>

<script>
    let state = JSON.parse(localStorage.getItem('nurse_pro_v4')) || { nurses: [], shifts: {} };
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

        if(!nurse || !day) return alert("აირჩიეთ ექთანი და რიცხვი");
        
        const k = `${nurse}_${y}_${m}_${day}`;
        if(!state.shifts[k]) state.shifts[k] = {};
        state.shifts[k].real = hours;
        
        save(); render();
    }

    function render() {
        const m = parseInt(document.getElementById('mSelect').value);
        const y = parseInt(document.getElementById('ySelect').value);
        const days = new Date(y, m + 1, 0).getDate();
        
        // Update Real Time Nurse List
        const rn = document.getElementById('realNurse');
        rn.innerHTML = state.nurses.map(n => `<option value="${n}">${n}</option>`).join('');

        // Header
        let hHTML = `<tr><th class="sticky-col">დღე</th>`;
        state.nurses.forEach(n => {
            hHTML += `<th>${n} <span class="no-print" style="color:red; cursor:pointer; margin-left:4px" onclick="delNurse('${n}')">×</span></th>`;
        });
        hHTML += `</tr>`;
        document.getElementById('hTable').innerHTML = hHTML;

        // Rows
        let bHTML = "";
        for(let d=1; d<=days; d++) {
            // Plan Row
            bHTML += `<tr class="row-plan"><td class="sticky-col">${d} გ</td>`;
            state.nurses.forEach(n => {
                const k = `${n}_${y}_${m}_${d}`;
                const v = state.shifts[k]?.plan || "";
                bHTML += `<td>
                    <select onchange="upd('${k}','plan',this.value)" style="border:none; background:transparent; font-size:0.85rem">
                        <option value=""></option>
                        <option value="8" ${v=='8'?'selected':''}>8</option>
                        <option value="16" ${v=='16'?'selected':''}>16</option>
                        <option value="24" ${v=='24'?'selected':''}>24</option>
                    </select>
                </td>`;
            });
            bHTML += `</tr>`;

            // Real Row
            bHTML += `<tr class="row-real"><td class="sticky-col">${d} რ</td>`;
            state.nurses.forEach(n => {
                const k = `${n}_${y}_${m}_${d}`;
                const rv = state.shifts[k]?.real || "-";
                const note = state.shifts[k]?.note ? `<span class="badge-note"></span>` : "";
                bHTML += `<td onclick="openJ('${k}', '${n}', ${d})">${rv} ${note}</td>`;
            });
            bHTML += `</tr>`;
        }
        document.getElementById('bTable').innerHTML = bHTML;
    }

    function upd(k, f, v) {
        if(!state.shifts[k]) state.shifts[k] = {};
        state.shifts[k][f] = v;
        save();
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
        save(); render();
    }

    function openJ(k, n, d) {
        activeKey = k;
        document.getElementById('jTitle').innerText = `დღიური: ${n} (${d} რიცხვი)`;
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
    function resetData() { if(confirm('ყველაფერი წაიშლება! დარწმუნებული ხართ?')) { state = { nurses: [], shifts: {} }; save(); render(); } }
    function save() { localStorage.setItem('nurse_pro_v4', JSON.stringify(state)); }

    init();
</script>
</body>
</html>
