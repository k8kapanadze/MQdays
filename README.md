<!DOCTYPE html>
<html lang="ka">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NurseFlow Glass Pro</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --glass: rgba(255, 255, 255, 0.75);
            --glass-border: rgba(255, 255, 255, 0.4);
            --accent: #2563eb;
            --text: #0f172a;
            --shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.12);
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Inter', sans-serif; }
        body { 
            background: linear-gradient(135deg, #eef2ff 0%, #f1f5f9 100%);
            background-attachment: fixed;
            color: var(--text);
            padding: 15px;
            min-height: 100vh;
        }

        .container { max-width: 900px; margin: 0 auto; }

        .glass-card {
            background: var(--glass);
            backdrop-filter: blur(15px);
            -webkit-backdrop-filter: blur(15px);
            border: 1px solid var(--glass-border);
            border-radius: 28px;
            box-shadow: var(--shadow);
            padding: 25px;
            margin-bottom: 20px;
        }

        /* Nurse Header */
        .nurse-header {
            text-align: center;
            margin-bottom: 20px;
        }
        .nurse-header h1 { font-size: 1.5rem; font-weight: 800; color: var(--accent); letter-spacing: -0.03em; }

        .control-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(130px, 1fr));
            gap: 15px;
        }

        .field { display: flex; flex-direction: column; gap: 6px; }
        label { font-size: 0.7rem; font-weight: 700; color: #64748b; text-transform: uppercase; padding-left: 5px; }

        input, select {
            background: rgba(255, 255, 255, 0.6);
            border: 1px solid var(--glass-border);
            border-radius: 14px;
            padding: 12px;
            font-size: 0.95rem;
            outline: none;
            transition: 0.3s;
        }
        input:focus { background: #fff; border-color: var(--accent); box-shadow: 0 0 0 4px rgba(37, 99, 235, 0.1); }

        .btn-row { display: grid; grid-template-columns: 2fr 1fr; gap: 10px; margin-top: 20px; }

        .btn {
            border-radius: 14px;
            border: none;
            padding: 14px;
            font-weight: 700;
            cursor: pointer;
            transition: 0.2s;
            font-size: 0.9rem;
            box-shadow: 0 4px 0 rgba(0,0,0,0.08);
        }
        .btn:active { transform: translateY(2px); box-shadow: none; }
        .btn-primary { background: var(--accent); color: white; }
        .btn-secondary { background: white; color: var(--text); border: 1px solid var(--glass-border); }
        .btn-danger { background: rgba(239, 68, 68, 0.1); color: #ef4444; margin-top: 10px; box-shadow: none; }

        /* Table */
        .table-wrap { border-radius: 28px; overflow: hidden; box-shadow: var(--shadow); background: var(--glass); }
        table { width: 100%; border-collapse: collapse; }
        th { background: rgba(255,255,255,0.5); padding: 18px; font-size: 0.75rem; color: #64748b; border-bottom: 1px solid var(--glass-border); }
        td { padding: 12px; text-align: center; border-bottom: 1px solid rgba(255,255,255,0.2); }

        .day-box { font-weight: 800; font-size: 1rem; color: #334155; }
        
        .plan-select {
            width: 75px; padding: 8px; border-radius: 10px; border: 1px solid var(--glass-border);
            background: rgba(255,255,255,0.4); font-weight: 700; text-align: center; cursor: pointer;
        }

        .real-box {
            background: rgba(37, 99, 235, 0.07);
            color: var(--accent);
            font-weight: 800;
            border-radius: 12px;
            padding: 10px;
            cursor: pointer;
            min-width: 50px;
            display: inline-block;
        }

        .totals {
            display: flex; justify-content: space-around; padding: 20px;
            background: rgba(255,255,255,0.3); border-top: 1px solid var(--glass-border);
        }
        .total-item { text-align: center; }
        .t-label { font-size: 0.65rem; font-weight: 700; color: #64748b; display: block; }
        .t-val { font-size: 1.3rem; font-weight: 800; color: var(--accent); }

        /* Modal */
        .modal { display: none; position: fixed; inset: 0; background: rgba(15, 23, 42, 0.3); backdrop-filter: blur(10px); z-index: 1000; padding: 20px; }
        .modal-content { background: white; max-width: 450px; margin: 60px auto; padding: 35px; border-radius: 32px; box-shadow: var(--shadow); }
        textarea { width: 100%; border-radius: 18px; padding: 15px; background: #f1f5f9; border: 1px solid #e2e8f0; margin: 15px 0; font-size: 1rem; }

        @media print { .no-print { display: none !important; } }
    </style>
</head>
<body>

<div class="container">
    <div class="nurse-header">
        <h1 id="displayNurseName">ექთნის გრაფიკი</h1>
    </div>

    <div class="glass-card no-print">
        <div class="field" style="margin-bottom: 20px;">
            <label>ექთნის სახელი</label>
            <input type="text" id="nurseName" placeholder="ჩაწერეთ სახელი..." oninput="updateNameHeader()">
        </div>
        
        <div class="control-grid">
            <div class="field">
                <label>თვე</label>
                <select id="mSelect" onchange="render()"></select>
            </div>
            <div class="field">
                <label>წელი</label>
                <select id="ySelect" onchange="render()"></select>
            </div>
            <div class="field">
                <label>დღე</label>
                <input type="number" id="realDay" placeholder="რიცხვი" min="1" max="31">
            </div>
            <div class="field">
                <label>საათი (რეალური)</label>
                <select id="realHours">
                    <option value="24">24 სთ</option>
                    <option value="16">16 სთ</option>
                    <option value="8">8 სთ</option>
                    <option value="0">გაცდენა</option>
                </select>
            </div>
        </div>

        <div class="btn-row">
            <button class="btn btn-primary" onclick="saveRealTime()">დაფიქსირება</button>
            <button class="btn btn-secondary" onclick="fillYear()">წლის შევსება</button>
        </div>
        <button class="btn btn-danger" style="width: 100%;" onclick="clearMonth()">ამ თვის გეგმის წაშლა</button>
    </div>

    <div class="table-wrap">
        <table id="mainTable">
            <thead>
                <tr>
                    <th>დღე</th>
                    <th>გეგმა</th>
                    <th>რეალური</th>
                </tr>
            </thead>
            <tbody id="bTable"></tbody>
        </table>
        <div class="totals">
            <div class="total-item">
                <span class="t-label">გეგმიური ჯამი</span>
                <span id="totalPlan" class="t-val">0</span>
            </div>
            <div class="total-item">
                <span class="t-label">ნამუშევარი სულ</span>
                <span id="totalReal" class="t-val" style="color: #10b981;">0</span>
            </div>
        </div>
    </div>
</div>

<div id="jModal" class="modal">
    <div class="modal-content">
        <h2 id="jTitle"></h2>
        <p style="font-size: 0.9rem; color: #64748b; margin-top: 10px;">რა ისწავლეთ? / როგორ გაერთეთ?</p>
        <textarea id="jText" rows="6"></textarea>
        <div style="display:flex; gap:12px;">
            <button class="btn btn-secondary" style="flex:1" onclick="closeM()">გაუქმება</button>
            <button class="btn btn-primary" style="flex:1" onclick="saveJ()">შენახვა</button>
        </div>
    </div>
</div>

<script>
    let state = JSON.parse(localStorage.getItem('nurse_glass_v9')) || { nurse: "", shifts: {} };
    const months = ["იანვარი", "თებერვალი", "მარტი", "აპრილი", "მაისი", "ივნისი", "ივლისი", "აგვისტო", "სექტემბერი", "ოქტომბერი", "ნოემბერი", "დეკემბერი"];
    let activeKey = null;

    function init() {
        const ms = document.getElementById('mSelect');
        const ys = document.getElementById('ySelect');
        months.forEach((m, i) => ms.add(new Option(m, i)));
        for(let y=2024; y<=2026; y++) ys.add(new Option(y, y));
        
        ms.value = new Date().getMonth();
        ys.value = new Date().getFullYear();
        document.getElementById('nurseName').value = state.nurse || "";
        updateNameHeader();
        render();
    }

    function updateNameHeader() {
        const name = document.getElementById('nurseName').value;
        document.getElementById('displayNurseName').innerText = name ? name + "-ის გრაფიკი" : "ექთნის გრაფიკი";
        state.nurse = name;
        save();
    }

    function render() {
        const m = parseInt(document.getElementById('mSelect').value);
        const y = parseInt(document.getElementById('ySelect').value);
        const days = new Date(y, m + 1, 0).getDate();
        
        let bHTML = "";
        let tPlan = 0, tReal = 0;

        for(let d=1; d<=days; d++) {
            const k = `d_${y}_${m}_${d}`;
            const pVal = state.shifts[k]?.plan || "";
            const rVal = state.shifts[k]?.real || "-";
            const hasNote = state.shifts[k]?.note ? `<span style="width:6px; height:6px; background:#10b981; border-radius:50%; display:inline-block; margin-left:6px;"></span>` : "";
            
            tPlan += parseInt(pVal) || 0;
            tReal += parseInt(rVal) || 0;

            bHTML += `
                <tr>
                    <td class="day-box">${d}</td>
                    <td>
                        <select class="plan-select" onchange="upd('${k}','plan',this.value)">
                            <option value=""></option>
                            <option value="8" ${pVal=='8'?'selected':''}>8</option>
                            <option value="16" ${pVal=='16'?'selected':''}>16</option>
                            <option value="24" ${pVal=='24'?'selected':''}>24</option>
                        </select>
                    </td>
                    <td><div class="real-box" onclick="openM('${k}', ${d})">${rVal} ${hasNote}</div></td>
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
        if(!d || d < 1 || d > 31) return alert("მიუთითეთ სწორი რიცხვი");
        const k = `d_${y}_${m}_${d}`;
        if(!state.shifts[k]) state.shifts[k] = {};
        state.shifts[k].real = h;
        save(); render();
    }

    function clearMonth() {
        const m = parseInt(document.getElementById('mSelect').value);
        const y = parseInt(document.getElementById('ySelect').value);
        if(confirm(`${months[m]} თვის გეგმიური საათები წაიშლება. დარწმუნებული ხართ?`)) {
            for(let d=1; d<=31; d++) {
                const k = `d_${y}_${m}_${d}`;
                if(state.shifts[k]) state.shifts[k].plan = "";
            }
            save(); render();
        }
    }

    function fillYear() {
        const m = parseInt(document.getElementById('mSelect').value);
        const y = parseInt(document.getElementById('ySelect').value);
        let startD = null, hrs = 24;
        for(let d=1; d<=31; d++) { 
            if(state.shifts[`d_${y}_${m}_${d}`]?.plan) { 
                startD = new Date(y, m, d); 
                hrs = state.shifts[`d_${y}_${m}_${d}`].plan; break; 
            } 
        }
        if(startD) {
            let c = new Date(startD);
            while(c.getFullYear() <= 2026) {
                const k = `d_${c.getFullYear()}_${c.getMonth()}_${c.getDate()}`;
                if(!state.shifts[k]) state.shifts[k] = {};
                state.shifts[k].plan = hrs;
                c.setDate(c.getDate() + 4);
            }
            save(); render();
        } else { alert("ჯერ მონიშნეთ ერთ-ერთი დღის გეგმა ცხრილში!"); }
    }

    function upd(k, f, v) { if(!state.shifts[k]) state.shifts[k] = {}; state.shifts[k][f] = v; save(); render(); }
    function openM(k, d) { activeKey = k; document.getElementById('jTitle').innerText = `${d} რიცხვის დღიური`; document.getElementById('jText').value = state.shifts[k]?.note || ""; document.getElementById('jModal').style.display='block'; }
    function saveJ() { if(!state.shifts[activeKey]) state.shifts[activeKey] = {}; state.shifts[activeKey].note = document.getElementById('jText').value; save(); render(); closeM(); }
    function closeM() { document.getElementById('jModal').style.display='none'; }
    function save() { localStorage.setItem('nurse_glass_v9', JSON.stringify(state)); }
    init();
</script>
</body>
</html>
