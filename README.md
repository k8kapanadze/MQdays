<!DOCTYPE html>
<html lang="ka">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NurseFlow Glass-OS</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --glass: rgba(255, 255, 255, 0.7);
            --glass-border: rgba(255, 255, 255, 0.4);
            --accent: #2563eb;
            --text: #0f172a;
            --shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.15);
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Inter', sans-serif; }
        
        body { 
            background: linear-gradient(135deg, #e0e7ff 0%, #f1f5f9 100%);
            background-attachment: fixed;
            color: var(--text);
            padding: 15px;
            min-height: 100vh;
        }

        .container { max-width: 900px; margin: 0 auto; }

        /* Glassmorphism Card Style */
        .glass-card {
            background: var(--glass);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
            border: 1px solid var(--glass-border);
            border-radius: 24px;
            box-shadow: var(--shadow);
            padding: 20px;
            margin-bottom: 20px;
            transition: transform 0.3s ease;
        }

        .glass-card:hover { transform: translateY(-2px); }

        /* Header */
        .header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
        .logo { display: flex; align-items: center; gap: 10px; font-weight: 700; font-size: 1.2rem; color: var(--accent); }

        /* Inputs Grid */
        .control-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
            gap: 12px;
        }

        .input-field {
            display: flex;
            flex-direction: column;
            gap: 5px;
        }

        label { font-size: 0.75rem; font-weight: 700; color: #64748b; text-transform: uppercase; margin-left: 5px; }

        input, select {
            background: rgba(255, 255, 255, 0.5);
            border: 1px solid var(--glass-border);
            border-radius: 12px;
            padding: 12px;
            font-size: 0.9rem;
            outline: none;
            transition: 0.2s;
        }

        input:focus, select:focus { background: #fff; border-color: var(--accent); }

        /* Buttons - 3D Effect */
        .btn {
            border-radius: 12px;
            border: none;
            padding: 12px;
            font-weight: 700;
            cursor: pointer;
            transition: 0.2s;
            font-size: 0.85rem;
            box-shadow: 0 4px 0 rgba(0,0,0,0.1);
            position: relative;
        }

        .btn:active { transform: translateY(2px); box-shadow: none; }

        .btn-primary { background: var(--accent); color: white; }
        .btn-secondary { background: #fff; color: var(--text); border: 1px solid var(--glass-border); }

        /* Table Design */
        .table-wrap { overflow-x: auto; border-radius: 20px; box-shadow: var(--shadow); }
        table { width: 100%; border-collapse: collapse; background: var(--glass); backdrop-filter: blur(10px); }
        
        th { background: rgba(255, 255, 255, 0.4); padding: 15px; font-size: 0.75rem; font-weight: 700; color: #64748b; border-bottom: 1px solid var(--glass-border); }
        td { padding: 10px; text-align: center; border-bottom: 1px solid rgba(255,255,255,0.2); }

        .day-num { font-weight: 800; color: #1e293b; width: 50px; }
        
        /* Plan select in table */
        .plan-cell select {
            width: 70px;
            padding: 5px;
            border-radius: 8px;
            border: none;
            background: rgba(255,255,255,0.5);
            font-weight: 700;
            text-align: center;
        }

        /* Real Cell - 3D Glass Look */
        .real-cell {
            background: rgba(37, 99, 235, 0.08);
            color: var(--accent);
            font-weight: 700;
            border-radius: 10px;
            cursor: pointer;
            transition: 0.2s;
        }
        .real-cell:hover { background: rgba(37, 99, 235, 0.15); }

        /* Footer Totals */
        .totals-bar {
            display: flex;
            justify-content: space-around;
            padding: 15px;
            background: rgba(255, 255, 255, 0.4);
            border-top: 1px solid var(--glass-border);
        }

        .total-item { display: flex; flex-direction: column; align-items: center; }
        .total-label { font-size: 0.7rem; color: #64748b; font-weight: 600; }
        .total-num { font-size: 1.1rem; font-weight: 800; color: var(--accent); }

        /* Modal Journal */
        .modal { 
            display: none; position: fixed; inset: 0; 
            background: rgba(15, 23, 42, 0.4); 
            backdrop-filter: blur(8px); 
            z-index: 1000; 
            padding: 20px;
        }
        .modal-content { 
            background: white; 
            max-width: 450px; 
            margin: 50px auto; 
            padding: 30px; 
            border-radius: 28px; 
            box-shadow: var(--shadow);
        }
        textarea { 
            width: 100%; border: 1px solid var(--glass-border); 
            border-radius: 16px; padding: 15px; font-size: 1rem; 
            margin: 15px 0; outline: none; background: #f8fafc;
        }

        .no-print { display: block; }
        @media print { .no-print { display: none !important; } .glass-card { box-shadow: none; border: 1px solid #000; } }
    </style>
</head>
<body>

<div class="container">
    <div class="header no-print">
        <div class="logo">
            <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3"><path d="M12 21a9 9 0 1 0 0-18 9 9 0 0 0 0 18ZM9 12h6M12 9v6"/></svg>
            NURSEFLOW PRO
        </div>
        <button class="btn btn-secondary" onclick="window.print()">ბეჭდვა</button>
    </div>

    <div class="glass-card no-print">
        <div class="control-grid">
            <div class="input-field">
                <label>თვე</label>
                <select id="mSelect" onchange="render()"></select>
            </div>
            <div class="input-field">
                <label>წელი</label>
                <select id="ySelect" onchange="render()"></select>
            </div>
            <div class="input-field">
                <label>დღე</label>
                <input type="number" id="realDay" placeholder="რიცხვი" min="1" max="31">
            </div>
            <div class="input-field">
                <label>საათი (რეალ)</label>
                <select id="realHours">
                    <option value="24">24 სთ</option>
                    <option value="16">16 სთ</option>
                    <option value="8">8 სთ</option>
                    <option value="0">0 (გაცდენა)</option>
                </select>
            </div>
        </div>
        <div style="display: grid; grid-template-columns: 2fr 1fr; gap: 10px; margin-top: 15px;">
            <button class="btn btn-primary" onclick="saveRealTime()">მონაცემის დაფიქსირება</button>
            <button class="btn btn-secondary" onclick="fillYear()">წლის შევსება</button>
        </div>
        <button class="btn btn-secondary" style="width: 100%; margin-top: 10px; color: #ef4444;" onclick="clearMonth()">თვის გეგმის გასუფთავება</button>
    </div>

    <div class="table-wrap">
        <table id="mainTable">
            <thead>
                <tr>
                    <th class="day-num">#</th>
                    <th>გეგმა</th>
                    <th>რეალური</th>
                </tr>
            </thead>
            <tbody id="bTable"></tbody>
            <tfoot>
                <tr class="totals-bar">
                    <td colspan="3" style="padding: 0;">
                        <div class="totals-bar">
                            <div class="total-item">
                                <span class="total-label">გეგმიური ჯამი</span>
                                <span id="totalPlan" class="total-num">0</span>
                            </div>
                            <div class="total-item">
                                <span class="total-label">რეალური ნამუშევარი</span>
                                <span id="totalReal" class="total-num" style="color: #10b981;">0</span>
                            </div>
                        </div>
                    </td>
                </tr>
            </tfoot>
        </table>
    </div>
</div>

<div id="jModal" class="modal">
    <div class="modal-content">
        <h2 id="jTitle" style="font-size: 1.2rem; font-weight: 700;"></h2>
        <p style="font-size: 0.85rem; color: #64748b; margin-top: 8px; line-height: 1.5;">
            ჩაწერეთ კლინიკური შემთხვევა ან გამოცდილება. <br>
            <strong>რა ისწავლეთ? / როგორ გაერთეთ?</strong>
        </p>
        <textarea id="jText" rows="6" placeholder="აღწერეთ სამუშაო დღე..."></textarea>
        <div style="display:flex; gap:12px;">
            <button class="btn btn-secondary" style="flex:1" onclick="closeM()">გაუქმება</button>
            <button class="btn btn-primary" style="flex:1" onclick="saveJ()">შენახვა</button>
        </div>
    </div>
</div>

<script>
    let state = JSON.parse(localStorage.getItem('nurse_glass_v8')) || { shifts: {} };
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
            const k = `user_${y}_${m}_${d}`;
            const pVal = state.shifts[k]?.plan || "";
            const rVal = state.shifts[k]?.real || "-";
            const hasNote = state.shifts[k]?.note ? `<span style="width:6px; height:6px; background:#10b981; border-radius:50%; display:inline-block; margin-left:5px;"></span>` : "";
            
            tPlan += parseInt(pVal) || 0;
            tReal += parseInt(rVal) || 0;

            bHTML += `
                <tr>
                    <td class="day-num">${d}</td>
                    <td class="plan-cell">
                        <select onchange="upd('${k}','plan',this.value)">
                            <option value=""></option>
                            <option value="8" ${pVal=='8'?'selected':''}>8</option>
                            <option value="16" ${pVal=='16'?'selected':''}>16</option>
                            <option value="24" ${pVal=='24'?'selected':''}>24</option>
                        </select>
                    </td>
                    <td class="real-cell" onclick="openM('${k}', ${d})">${rVal} ${hasNote}</td>
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
        const k = `user_${y}_${m}_${d}`;
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
            if(state.shifts[`user_${y}_${m}_${d}`]?.plan) { 
                startD = new Date(y, m, d); 
                hrs = state.shifts[`user_${y}_${m}_${d}`].plan; break; 
            } 
        }
        if(startD) {
            let c = new Date(startD);
            while(c.getFullYear() <= 2026) {
                const k = `user_${c.getFullYear()}_${c.getMonth()}_${c.getDate()}`;
                if(!state.shifts[k]) state.shifts[k] = {};
                state.shifts[k].plan = hrs;
                c.setDate(c.getDate() + 4);
            }
            save(); render();
        } else {
            alert("ჯერ მონიშნეთ პირველი დღე ცხრილში!");
        }
    }

    function upd(k, f, v) { if(!state.shifts[k]) state.shifts[k] = {}; state.shifts[k][f] = v; save(); render(); }
    function openM(k, d) { activeKey = k; document.getElementById('jTitle').innerText = `${d} რიცხვის დღიური`; document.getElementById('jText').value = state.shifts[k]?.note || ""; document.getElementById('jModal').style.display='block'; }
    function saveJ() { if(!state.shifts[activeKey]) state.shifts[activeKey] = {}; state.shifts[activeKey].note = document.getElementById('jText').value; save(); render(); closeM(); }
    function closeM() { document.getElementById('jModal').style.display='none'; }
    function save() { localStorage.setItem('nurse_glass_v8', JSON.stringify(state)); }
    init();
</script>
</body>
</html>
