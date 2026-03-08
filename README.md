<!DOCTYPE html>
<html lang="ka">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Smart Nurse Scheduler v3.0</title>
    <style>
        :root {
            --primary: #4f46e5;
            --secondary: #6366f1;
            --success: #22c55e;
            --danger: #ef4444;
            --bg: #f3f4f6;
            --card: #ffffff;
            --text-main: #1f2937;
            --text-sub: #6b7280;
            --border: #e5e7eb;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Inter', system-ui, sans-serif; }

        body { background: var(--bg); color: var(--text-main); padding: 20px; line-height: 1.5; }

        /* Modern Dashboard Layout */
        .container { max-width: 1400px; margin: 0 auto; }

        .header { 
            display: flex; justify-content: space-between; align-items: center; 
            background: var(--card); padding: 20px; border-radius: 16px; 
            box-shadow: 0 4px 6px -1px rgba(0,0,0,0.1); margin-bottom: 20px;
        }

        .header h1 { font-size: 1.5rem; color: var(--primary); font-weight: 800; }

        /* Control Panel */
        .glass-panel {
            background: var(--card); border-radius: 16px; padding: 24px;
            box-shadow: 0 10px 15px -3px rgba(0,0,0,0.1); margin-bottom: 25px;
            display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 20px;
        }

        .input-group { display: flex; flex-direction: column; gap: 8px; }
        label { font-size: 0.85rem; font-weight: 600; color: var(--text-sub); }

        input, select {
            padding: 10px 14px; border: 1.5px solid var(--border); border-radius: 10px;
            outline: none; transition: all 0.2s; font-size: 0.95rem;
        }

        input:focus, select:focus { border-color: var(--primary); box-shadow: 0 0 0 3px rgba(79, 70, 229, 0.1); }

        /* Buttons */
        .btn {
            padding: 10px 20px; border-radius: 10px; border: none; cursor: pointer;
            font-weight: 600; transition: all 0.2s; display: flex; align-items: center; justify-content: center; gap: 8px;
        }
        .btn-primary { background: var(--primary); color: white; }
        .btn-success { background: var(--success); color: white; }
        .btn-danger { background: var(--danger); color: white; }
        .btn:hover { opacity: 0.9; transform: translateY(-1px); }

        /* Professional Table */
        .table-wrapper { 
            background: var(--card); border-radius: 16px; overflow: hidden; 
            box-shadow: 0 4px 6px -1px rgba(0,0,0,0.1); border: 1px solid var(--border);
        }

        table { width: 100%; border-collapse: collapse; table-layout: fixed; }
        th { background: #f9fafb; padding: 15px; border-bottom: 2px solid var(--border); font-size: 0.85rem; color: var(--text-sub); }
        td { padding: 12px; border-bottom: 1px solid var(--border); text-align: center; }

        .sticky-col { position: sticky; left: 0; background: white; z-index: 5; width: 100px; font-weight: 700; border-right: 2px solid var(--border); }
        .row-real { background: #f8fafc; cursor: pointer; color: var(--primary); }
        .row-real:hover { background: #f1f5f9; }

        .nurse-col-header { width: 180px; position: relative; }

        /* Badges */
        .badge-log { font-size: 10px; background: #dbeafe; color: #1e40af; padding: 2px 6px; border-radius: 4px; margin-left: 4px; }

        /* Modal Customization */
        .modal { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.4); backdrop-filter: blur(4px); z-index: 1000; }
        .modal-content { 
            background: white; width: 90%; max-width: 450px; margin: 10% auto; 
            padding: 30px; border-radius: 20px; box-shadow: 0 25px 50px -12px rgba(0,0,0,0.25);
        }

        .context-menu {
            position: absolute; background: white; border-radius: 12px; box-shadow: 0 10px 15px rgba(0,0,0,0.15);
            padding: 8px; display: none; z-index: 2000; border: 1px solid var(--border);
        }
        .context-item { padding: 10px 15px; border-radius: 8px; color: var(--danger); cursor: pointer; font-weight: 600; }
        .context-item:hover { background: #fee2e2; }

        @media print { .no-print { display: none !important; } .table-wrapper { border: none; box-shadow: none; } }
    </style>
</head>
<body>

<div class="container">
    <header class="header no-print">
        <h1>🏥 SmartScheduler <span style="font-weight: 300; font-size: 0.9rem; color: var(--text-sub);">Nurse Management</span></h1>
        <div style="display: flex; gap: 10px;">
            <button class="btn btn-primary" onclick="window.print()">🖨️ ბეჭდვა</button>
        </div>
    </header>

    <div class="glass-panel no-print">
        <div class="input-group">
            <label>პერსონალის დამატება</label>
            <div style="display:flex; gap:8px;">
                <input type="text" id="nurseInput" placeholder="სახელი გვარი...">
                <button class="btn btn-primary" onclick="addNurse()">+</button>
            </div>
        </div>

        <div class="input-group">
            <label>აქტიური პერიოდი</label>
            <div style="display:flex; gap:8px;">
                <select id="selMonth" onchange="renderUI()"></select>
                <select id="selYear" onchange="renderUI()"></select>
            </div>
        </div>

        <div class="input-group">
            <label>სწრაფი ძებნა</label>
            <input type="text" id="search" placeholder="ექთანი ან რიცხვი..." oninput="handleSearch()">
        </div>

        <div class="input-group" style="justify-content: flex-end; flex-direction: row; align-items: flex-end; gap: 10px;">
            <button class="btn btn-success" onclick="fillWholeYear()">📅 წლის შევსება</button>
            <button class="btn btn-danger" onclick="clearPlan()">🗑️ წაშლა</button>
        </div>
    </div>

    <div id="searchBox" class="no-print" style="display:none; background: #e0e7ff; padding: 15px; border-radius: 12px; margin-bottom: 20px; font-weight: 600; border-left: 5px solid var(--primary);"></div>

    <div class="table-wrapper">
        <table id="nurseTable">
            <thead id="tHead"></thead>
            <tbody id="tBody"></tbody>
        </table>
    </div>
</div>

<div id="logModal" class="modal">
    <div class="modal-content">
        <h2 id="modalTitle" style="margin-bottom: 20px; font-size: 1.2rem;">📝 ჟურნალის ჩანაწერი</h2>
        <div class="input-group" style="margin-bottom: 15px;">
            <label>კლინიკური შემთხვევა</label>
            <textarea id="caseText" rows="4" style="width: 100%; padding: 12px; border-radius: 10px; border: 1.5px solid var(--border);"></textarea>
        </div>
        <div class="input-group" style="margin-bottom: 20px;">
            <label>პირადი შენიშვნა</label>
            <textarea id="noteText" rows="3" style="width: 100%; padding: 12px; border-radius: 10px; border: 1.5px solid var(--border);"></textarea>
        </div>
        <div style="display:flex; justify-content: flex-end; gap: 12px;">
            <button class="btn" style="background: #f3f4f6;" onclick="closeModal()">გაუქმება</button>
            <button class="btn btn-primary" onclick="saveLog()">შენახვა</button>
        </div>
    </div>
</div>

<div id="ctxMenu" class="context-menu">
    <div class="context-item" onclick="deleteNurse()">🗑️ ბაზიდან წაშლა</div>
</div>

<script>
    let db = JSON.parse(localStorage.getItem('nurseCloudDB')) || { nurses: [], data: {} };
    const monthNames = ["იანვარი", "თებერვალი", "მარტი", "აპრილი", "მაისი", "ივნისი", "ივლისი", "აგვისტო", "სექტემბერი", "ოქტომბერი", "ნოემბერი", "დეკემბერი"];
    let activeKey = null;
    let selectedNurseCtx = null;

    function init() {
        const mSel = document.getElementById('selMonth');
        const ySel = document.getElementById('selYear');
        if(mSel.options.length === 0) {
            monthNames.forEach((m, i) => mSel.add(new Option(m, i)));
            for(let y=2024; y<=2027; y++) ySel.add(new Option(y, y));
            mSel.value = new Date().getMonth();
            ySel.value = new Date().getFullYear();
        }
        renderUI();
    }

    function renderUI() {
        const month = parseInt(document.getElementById('selMonth').value);
        const year = parseInt(document.getElementById('selYear').value);
        const days = new Date(year, month + 1, 0).getDate();
        
        // Header
        let headHTML = `<tr><th class="sticky-col">დღე</th>`;
        db.nurses.forEach(n => {
            headHTML += `<th class="nurse-col-header">
                <input type="checkbox" class="n-check" value="${n}"> ${n}
                <span style="cursor:pointer; margin-left:5px;" onclick="showCtx(event, '${n}')">⋮</span>
            </th>`;
        });
        headHTML += `</tr>`;
        document.getElementById('tHead').innerHTML = headHTML;

        // Body
        let bodyHTML = "";
        for(let d=1; d<=days; d++) {
            // Plan Row
            bodyHTML += `<tr style="background:white"><td class="sticky-col">${d} <small style="color:#aaa">გ</small></td>`;
            db.nurses.forEach(n => {
                const key = `${n}_${year}_${month}_${d}`;
                const val = db.data[key]?.plan || "";
                bodyHTML += `<td>
                    <select onchange="updateData('${key}', 'plan', this.value)" style="border:none; background:transparent; font-weight:600;">
                        <option value=""></option>
                        <option value="8" ${val==8?'selected':''}>8</option>
                        <option value="16" ${val==16?'selected':''}>16</option>
                        <option value="24" ${val==24?'selected':''}>24</option>
                    </select>
                </td>`;
            });
            bodyHTML += `</tr>`;

            // Real Row
            bodyHTML += `<tr class="row-real"><td class="sticky-col">${d} <small style="color:#aaa">რ</small></td>`;
            db.nurses.forEach(n => {
                const key = `${n}_${year}_${month}_${d}`;
                const rVal = db.data[key]?.real || "-";
                const hasLog = (db.data[key]?.case || db.data[key]?.note) ? '<span class="badge-log">📝</span>' : "";
                bodyHTML += `<td onclick="openLog('${key}', '${n}', ${d})">${rVal} ${hasLog}</td>`;
            });
            bodyHTML += `</tr>`;
        }
        document.getElementById('tBody').innerHTML = bodyHTML;
        save();
    }

    // 🔥 წლის შევსების ლოგიკა (მე-4 დღის ციკლი)
    function fillWholeYear() {
        const selected = Array.from(document.querySelectorAll('.n-check:checked')).map(c => c.value);
        if(selected.length === 0) return alert("მონიშნეთ ექთნები ცხრილში");

        const startMonth = parseInt(document.getElementById('selMonth').value);
        const startYear = parseInt(document.getElementById('selYear').value);

        selected.forEach(nurse => {
            // 1. ვიპოვოთ პირველი მორიგეობა მიმდინარე თვეში
            let firstDate = null;
            let hourValue = 24;

            for(let d=1; d<=31; d++) {
                const k = `${nurse}_${startYear}_${startMonth}_${d}`;
                if(db.data[k]?.plan) {
                    firstDate = new Date(startYear, startMonth, d);
                    hourValue = db.data[k].plan;
                    break;
                }
            }

            if(!firstDate) return alert(`${nurse}-სთვის მონიშნეთ პირველი დღე ხელით!`);

            // 2. ციკლი წლის ბოლომდე
            let tempDate = new Date(firstDate);
            const endOfYear = new Date(startYear, 11, 31);

            while(tempDate <= endOfYear) {
                const y = tempDate.getFullYear();
                const m = tempDate.getMonth();
                const d = tempDate.getDate();
                const key = `${nurse}_${y}_${m}_${d}`;

                if(!db.data[key]) db.data[key] = {};
                db.data[key].plan = hourValue;

                tempDate.setDate(tempDate.getDate() + 4);
            }
        });

        renderUI();
        alert("გრაფიკი წარმატებით შეივსო წლის ბოლომდე!");
    }

    function addNurse() {
        const val = document.getElementById('nurseInput').value.trim();
        if(val && !db.nurses.includes(val)) {
            db.nurses.push(val);
            document.getElementById('nurseInput').value = "";
            renderUI();
        }
    }

    function updateData(key, field, val) {
        if(!db.data[key]) db.data[key] = {};
        db.data[key][field] = val;
        // ავტომატურად გავხადოთ რეალური გეგმიურის ტოლი საწყის ეტაპზე
        if(field === 'plan' && !db.data[key].real) db.data[key].real = val;
        save();
        renderUI();
    }

    function openLog(key, n, d) {
        activeKey = key;
        document.getElementById('modalTitle').innerText = `${n} - ${d} რიცხვი`;
        document.getElementById('caseText').value = db.data[key]?.case || "";
        document.getElementById('noteText').value = db.data[key]?.note || "";
        document.getElementById('logModal').style.display = 'block';
    }

    function saveLog() {
        if(!db.data[activeKey]) db.data[activeKey] = {};
        db.data[activeKey].case = document.getElementById('caseText').value;
        db.data[activeKey].note = document.getElementById('noteText').value;
        closeModal();
        renderUI();
    }

    function closeModal() { document.getElementById('logModal').style.display = 'none'; }

    function showCtx(e, n) {
        e.preventDefault();
        selectedNurseCtx = n;
        const menu = document.getElementById('ctxMenu');
        menu.style.display = 'block';
        menu.style.left = e.pageX + 'px';
        menu.style.top = e.pageY + 'px';
    }

    function deleteNurse() {
        db.nurses = db.nurses.filter(n => n !== selectedNurseCtx);
        renderUI();
    }

    function handleSearch() {
        const q = document.getElementById('search').value.toLowerCase();
        const box = document.getElementById('searchBox');
        if(!q) { box.style.display = 'none'; return; }
        
        const m = document.getElementById('selMonth').value;
        const y = document.getElementById('selYear').value;
        
        if(!isNaN(q)) {
            const list = db.nurses.filter(n => db.data[`${n}_${y}_${m}_${q}`]?.plan);
            box.innerHTML = `📅 ${q} რიცხვში მორიგეობენ: ${list.join(', ') || 'არავინ'}`;
        } else {
            const dates = [];
            for(let d=1; d<=31; d++) if(db.data[`${q}_${y}_${m}_${d}`]?.plan) dates.push(d);
            box.innerHTML = `👩‍⚕️ ${q}-ს მორიგეობები: ${dates.length ? dates.join(', ') : 'ვერ მოიძებნა'}`;
        }
        box.style.display = 'block';
    }

    function clearPlan() {
        if(confirm("წავშალო მონიშნული ექთნების გეგმა?")) {
            const selected = Array.from(document.querySelectorAll('.n-check:checked')).map(c => c.value);
            const m = document.getElementById('selMonth').value;
            const y = document.getElementById('selYear').value;
            Object.keys(db.data).forEach(k => {
                if(selected.some(n => k.startsWith(n)) && k.includes(`_${y}_${m}_`)) db.data[k].plan = "";
            });
            renderUI();
        }
    }

    function save() { localStorage.setItem('nurseCloudDB', JSON.stringify(db)); }
    
    window.onclick = (e) => { 
        if(e.target == document.getElementById('logModal')) closeModal();
        document.getElementById('ctxMenu').style.display = 'none';
    }
    
    init();
</script>

</body>
</html>
