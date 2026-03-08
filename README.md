<!DOCTYPE html>
<html lang="ka">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NurseFlow Single - Mobile Pro</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #0f172a;
            --accent: #2563eb;
            --bg: #f1f5f9;
            --row-real: #eff6ff;
            --border: #e2e8f0;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Inter', sans-serif; }
        body { background-color: var(--bg); color: var(--primary); padding: 10px; }

        .app-wrapper { 
            display: grid; 
            grid-template-columns: 1fr 350px; 
            gap: 15px; 
            max-width: 1200px; 
            margin: 0 auto; 
        }

        /* Mobile Responsive adjustment */
        @media (max-width: 850px) {
            .app-wrapper { grid-template-columns: 1fr; }
            .side-panel { order: -1; } /* მართვის პანელი ავა ზემოთ */
        }

        /* Table Style */
        .table-container { 
            background: #fff; border-radius: 16px; border: 1px solid var(--border); 
            overflow: hidden; box-shadow: 0 4px 6px -1px rgba(0,0,0,0.05);
        }
        table { width: 100%; border-collapse: collapse; }
        th, td { border-bottom: 1px solid var(--border); text-align: center; padding: 12px 8px; }
        th { background: #f8fafc; font-size: 0.8rem; color: #64748b; text-transform: uppercase; }

        .day-col { font-weight: 700; width: 50px; border-right: 1px solid var(--border); background: #fdfefe; }
        .row-plan { height: 55px; }
        .row-real { background: var(--row-real); color: var(--accent); font-weight: 700; cursor: pointer; height: 55px; }

        /* Select Styling in Table */
        .shift-select {
            width: 80%; padding: 8px; border: 1px solid #cbd5e1; border-radius: 8px;
            background: #fff; font-weight: 600; color: #475569; outline: none;
        }

        /* Side Panel Style */
        .side-panel { display: flex; flex-direction: column; gap: 15px; position: sticky; top: 10px; height: fit-content; }
        .card { background: #fff; padding: 20px; border-radius: 16px; border: 1px solid var(--border); box-shadow: 0 2px 4px rgba(0,0,0,0.05); }
        
        h3 { font-size: 0.75rem; color: #64748b; margin-bottom: 15px; display: flex; align-items: center; gap: 8px; }

        .input-group { margin-bottom: 12px; }
        label { display: block; font-size: 0.8rem; margin-bottom: 5px; font-weight: 600; }
        
        input, select { 
            width: 100%; padding: 12px; border: 1px solid var(--border); 
            border-radius: 10px; outline: none; font-size: 1rem;
        }

        .btn { 
            width: 100%; padding: 12px; border-radius: 10px; border: none; font-weight: 700; 
            cursor: pointer; transition: 0.2s; font-size: 0.9rem; margin-top: 5px;
        }
        .btn-primary { background: var(--accent); color: white; }
        .btn-secondary { background: #f8fafc; color: var(--primary); border: 1px solid var(--border); }
        .btn-danger { color: #ef4444; background: #fff; border: 1px solid #fee2e2; }

        .total-box { display: flex; justify-content: space-between; align-items: center; padding-top: 10px; border-top: 1px solid #eee; }
        .total-val { font-size: 1.2rem; font-weight: 800; color: var(--accent); }

        .badge-dot { width: 8px; height: 8px; background: #10b981; border-radius: 50%; display: inline-block; }

        /* Modal */
        .modal { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.5); backdrop-filter: blur(4px); z-index: 1000; }
        .modal-content { background: white; width: 90%; max-width: 400px; margin: 100px auto; padding: 25px; border-radius: 20px; }
        textarea { width: 100%; padding: 12px; border: 1px solid var(--border); border-radius: 10px; margin-top: 10px; font-size: 1rem; }
    </style>
</head>
<body>

<div class="app-wrapper">
    <div class="table-container">
        <table id="mainTable">
            <thead>
                <tr>
                    <th class="day-col">რიცხვი</th>
                    <th>გეგმიური</th>
                    <th>რეალური</th>
                </tr>
            </thead>
            <tbody id="bTable"></tbody>
        </table>
    </div>

    <div class="side-panel no-print">
        <div class="card" style="display: flex; align-items: center; gap: 10px;">
            <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M12 21a9 9 0 1 0 0-18 9 9 0 0 0 0 18ZM9 12h6M12 9v6"/></svg>
            <h1 style="font-size: 1.1rem;">NURSEFLOW</h1>
        </div>

        <div class="card">
            <h3>📅 პერიოდი</h3>
            <div style="display: flex; gap: 8px;">
                <select id="mSelect" onchange="render()"></select>
                <select id="ySelect" onchange="render()"></select>
            </div>
        </div>

        <div class="card">
            <h3>⏱️ რეალური აღრიცხვა</h3>
            <div class="input-group">
                <label>რიცხვი</label>
                <input type="number" id="realDay" placeholder="მაგ: 15" min="1" max="31">
            </div>
            <div class="input-group">
                <label>საათები</label>
                <select id="realHours">
                    <option value="8">8 საათი</option>
                    <option value="16">16 საათი</option>
                    <option value="24">24 საათი</option>
                    <option value="0">0 (გაცდენა)</option>
                </select>
            </div>
            <button class="btn btn-primary" onclick="saveRealTime()">დაფიქსირება</button>
        </div>

        <div class="card">
            <h3>📊 თვიური ჯამი</h3>
            <div class="total-box">
                <span style="font-size: 0.9rem; color: #64748b;">ნამუშევარი საათები:</span>
                <span id="totalReal" class="total-val">0</span>
            </div>
            <div class="total-box" style="border:none; padding-top: 5px;">
                <span style="font-size: 0.8rem; color: #94a3b8;">გეგმიური ჯამი:</span>
                <span id="totalPlan" style="font-weight: 600; color: #94a3b8;">0</span>
            </div>
        </div>

        <div class="card">
            <h3>⚙️ მართვა</h3>
            <button class="btn btn-secondary" onclick="fillYear()">წლის გეგმის შევსება</button>
            <button class="btn btn-danger" onclick="clearMonth()">თვის გასუფთავება</button>
            <button class="btn btn-secondary" style="margin-top: 10px;" onclick="window.print()">ბეჭდვა</button>
        </div>
    </div>
</div>

<div id="jModal" class="modal">
    <div class="modal-content">
        <h2 id="jTitle" style="font-size: 1.1rem; margin-bottom: 10px;"></h2>
        <p style="font-size: 0.85rem; color: #64748b;">რა ისწავლეთ დღეს? / როგორ გაერთეთ?</p>
        <textarea id="jText" rows="6" placeholder="ჩაწერეთ..."></textarea>
        <div style="display:flex; gap:10px; margin-top:1.5rem;">
            <button class="btn btn-secondary" onclick="closeM()">გაუქმება</button>
            <button class="btn btn-primary" onclick="saveJ()">შენახვა</button>
        </div>
    </div>
</div>

<script>
    let state = JSON.parse(localStorage.getItem('nurse_single_v7')) || { shifts: {} };
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

    function render() {
        const m = parseInt(document.getElementById('mSelect').value);
        const y = parseInt(document.getElementById('ySelect').value);
        const days = new Date(y, m + 1, 0).getDate();
        
        let bHTML = "";
        let tPlan = 0, tReal = 0;

        for(let d=1; d<=days; d++) {
            const k = `nurse1_${y}_${m}_${d}`;
            const pVal = state.shifts[k]?.plan || "";
            const rVal = state.shifts[k]?.real || "-";
            const hasNote = state.shifts[k]?.note ? `<span class="badge-dot"></span>` : "";
            
            tPlan += parseInt(pVal) || 0;
            tReal += parseInt(rVal) || 0;

            bHTML += `
                <tr class="row-plan">
                    <td class="day-col">${d}</td>
                    <td>
                        <select class="shift-select" onchange="upd('${k}','plan',this.value)">
                            <option value=""></option>
                            <option value="8" ${pVal=='8'?'selected':''}>8</option>
                            <option value="16" ${pVal=='16'?'selected':''}>16</option>
                            <option value="24" ${pVal=='24'?'selected':''}>24</option>
                        </select>
                    </td>
                    <td class="row-real" onclick="openM('${k}', ${d})">${rVal} ${hasNote}</td>
                </tr>`;
        }
        document.getElementById('bTable').innerHTML = bHTML;
        document.getElementById('totalPlan').innerText = tPlan;
        document.getElementById('totalReal').innerText = tReal;
    }

    function saveRealTime() {
        const d = document.getElementById('realDay').value;
        const h = document.getElementById('realHours').value;
        const m = document.getElementById('mSelect').value;
        const y = document.getElementById('ySelect').value;
        if(!d) return alert("მიუთითეთ რიცხვი");
        const k = `nurse1_${y}_${m}_${d}`;
        if(!state.shifts[k]) state.shifts[k] = {};
        state.shifts[k].real = h;
        save(); render();
    }

    function clearMonth() {
        const input = prompt("რომელი თვის გეგმა წავშალო? (1-12):");
        if(!input) return;
        const m = parseInt(input) - 1;
        const y = document.getElementById('ySelect').value;
        Object.keys(state.shifts).forEach(k => {
            if(k.includes(`_${y}_${m}_`)) state.shifts[k].plan = "";
        });
        save(); render();
    }

    function fillYear() {
        const m = parseInt(document.getElementById('mSelect').value);
        const y = parseInt(document.getElementById('ySelect').value);
        let startD = null, hrs = 24;
        for(let d=1; d<=31; d++) { 
            if(state.shifts[`nurse1_${y}_${m}_${d}`]?.plan) { 
                startD = new Date(y, m, d); 
                hrs = state.shifts[`nurse1_${y}_${m}_${d}`].plan; break; 
            } 
        }
        if(startD) {
            let c = new Date(startD);
            while(c.getFullYear() <= 2026) {
                const k = `nurse1_${c.getFullYear()}_${c.getMonth()}_${c.getDate()}`;
                if(!state.shifts[k]) state.shifts[k] = {};
                state.shifts[k].plan = hrs;
                c.setDate(c.getDate() + 4);
            }
            save(); render(); alert("გრაფიკი შეივსო წლის ბოლომდე");
        } else {
            alert("ჯერ მონიშნეთ პირველი დღე ცხრილში!");
        }
    }

    function upd(k, f, v) { if(!state.shifts[k]) state.shifts[k] = {}; state.shifts[k][f] = v; save(); render(); }
    function openM(k, d) { activeKey = k; document.getElementById('jTitle').innerText = `${d} რიცხვის დღიური`; document.getElementById('jText').value = state.shifts[k]?.note || ""; document.getElementById('jModal').style.display='block'; }
    function saveJ() { if(!state.shifts[activeKey]) state.shifts[activeKey] = {}; state.shifts[activeKey].note = document.getElementById('jText').value; save(); render(); closeM(); }
    function closeM() { document.getElementById('jModal').style.display='none'; }
    function save() { localStorage.setItem('nurse_single_v7', JSON.stringify(state)); }
    init();
</script>
</body>
</html>
