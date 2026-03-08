<!DOCTYPE html>
<html lang="ka">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Nurse Management System Pro</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --accent: #2563eb;
            --bg: #f8fafc;
            --card: #ffffff;
            --text: #1e293b;
            --border: #e2e8f0;
            --row-real: #f1f5f9;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Inter', sans-serif; }
        body { background-color: var(--bg); color: var(--text); padding: 2rem; }

        .container { max-width: 1600px; margin: 0 auto; }

        /* Header & Logo Section */
        .top-bar { display: flex; justify-content: space-between; align-items: center; margin-bottom: 2rem; }
        .logo { display: flex; align-items: center; gap: 10px; font-weight: 700; font-size: 1.25rem; color: var(--accent); }

        /* Control Panel */
        .panel { 
            background: var(--card); border-radius: 12px; padding: 1.5rem; 
            border: 1px solid var(--border); display: grid; 
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 1.5rem;
            margin-bottom: 2rem; box-shadow: 0 1px 3px rgba(0,0,0,0.1);
        }

        .input-box { display: flex; flex-direction: column; gap: 8px; }
        label { font-size: 0.8rem; font-weight: 600; text-transform: uppercase; color: #64748b; }
        
        input, select, textarea { 
            padding: 0.6rem 1rem; border: 1.5px solid var(--border); 
            border-radius: 8px; outline: none; transition: 0.2s; font-size: 0.9rem;
        }
        input:focus { border-color: var(--accent); }

        /* Action Buttons */
        .btn { 
            display: flex; align-items: center; justify-content: center; gap: 8px;
            padding: 0.6rem 1.2rem; border-radius: 8px; border: none; 
            cursor: pointer; font-weight: 600; transition: 0.2s; font-size: 0.9rem;
        }
        .btn-add { background: var(--accent); color: white; width: 100%; }
        .btn-action { background: #f1f5f9; color: var(--text); border: 1px solid var(--border); }
        .btn-action:hover { background: #e2e8f0; }

        /* Table Design */
        .table-container { 
            background: var(--card); border-radius: 12px; border: 1px solid var(--border); 
            overflow: auto; box-shadow: 0 4px 6px -1px rgba(0,0,0,0.1); 
        }
        table { width: 100%; border-collapse: collapse; min-width: 800px; }
        th, td { padding: 12px; border: 1px solid var(--border); text-align: center; }
        
        th { background: #f8fafc; font-weight: 600; font-size: 0.85rem; color: #64748b; }
        .sticky-day { position: sticky; left: 0; background: #fff; font-weight: 700; border-right: 2px solid var(--border); z-index: 10; }
        
        .row-plan { background: #fff; }
        .row-real { background: var(--row-real); cursor: pointer; font-weight: 600; color: var(--accent); }

        /* Modal / Journal */
        .modal { display: none; position: fixed; inset: 0; background: rgba(15, 23, 42, 0.5); backdrop-filter: blur(4px); z-index: 1000; }
        .modal-content { 
            background: white; width: 450px; margin: 100px auto; padding: 2rem; 
            border-radius: 16px; display: flex; flex-direction: column; gap: 1rem;
        }

        /* Utility */
        .no-print { display: flex; flex-direction: column; }
        @media print { .no-print { display: none !important; } .table-container { border: none; } }
        .badge-dot { width: 6px; height: 6px; background: var(--accent); border-radius: 50%; display: inline-block; margin-left: 4px; }
    </style>
</head>
<body>

<div class="container">
    <div class="top-bar no-print">
        <div class="logo">
            <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M19 14c1.49-1.46 3-3.21 3-5.5A5.5 5.5 0 0 0 16.5 3c-1.76 0-3 .5-4.5 2-1.5-1.5-2.74-2-4.5-2A5.5 5.5 0 0 0 2 8.5c0 2.3 1.5 4.05 3 5.5l7 7Z"/></svg>
            NURSEFLOW PRO
        </div>
        <button class="btn btn-action" onclick="window.print()">
            <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="6 9 6 2 18 2 18 9"/><path d="M6 18H4a2 2 0 0 1-2-2v-5a2 2 0 0 1 2-2h16a2 2 0 0 1 2 2v5a2 2 0 0 1-2 2h-2"/><rect x="6" y="14" width="12" height="8"/></svg>
            ბეჭდვა
        </button>
    </div>

    <div class="panel no-print">
        <div class="input-box">
            <label>პერსონალის დამატება</label>
            <div style="display:flex; gap:8px;">
                <input type="text" id="nurseName" placeholder="სახელი გვარი">
                <button class="btn btn-add" onclick="addNurse()" id="addBtn">დამატება</button>
            </div>
        </div>

        <div class="input-box">
            <label>პერიოდის არჩევა</label>
            <div style="display:flex; gap:8px;">
                <select id="mSelect" onchange="render()"></select>
                <select id="ySelect" onchange="render()"></select>
            </div>
        </div>

        <div class="input-box">
            <label>ავტომატიზაცია</label>
            <button class="btn btn-action" style="background: #eef2ff; color: #4338ca; border-color: #c7d2fe;" onclick="fillYear()">
                წლის შევსება (ყოველ მე-4 დღეს)
            </button>
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
        <h3 id="jTitle" style="font-weight: 700;">დღიურის ჩანაწერი</h3>
        <div class="input-box">
            <label>კლინიკური შემთხვევა / დღიური</label>
            <textarea id="jText" rows="6" placeholder="აღწერეთ სამუშაო დღე..."></textarea>
        </div>
        <div style="display:flex; gap:10px; margin-top: 10px;">
            <button class="btn btn-action" style="flex:1" onclick="closeModal()">გაუქმება</button>
            <button class="btn btn-add" style="flex:1" onclick="saveJ()">შენახვა</button>
        </div>
    </div>
</div>

<script>
    let state = JSON.parse(localStorage.getItem('nurse_system_v3')) || { nurses: [], shifts: {} };
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
            save();
            render();
        } else {
            alert("სახელი ცარიელია ან უკვე არსებობს");
        }
    }

    function render() {
        const m = parseInt(document.getElementById('mSelect').value);
        const y = parseInt(document.getElementById('ySelect').value);
        const days = new Date(y, m + 1, 0).getDate();
        
        // Header
        let hHTML = `<tr><th class="sticky-day">დღე</th>`;
        state.nurses.forEach(n => {
            hHTML += `<th>${n} <span style="cursor:pointer; color:red; font-size:10px; margin-left:5px;" onclick="delNurse('${n}')">✕</span></th>`;
        });
        hHTML += `</tr>`;
        document.getElementById('hTable').innerHTML = hHTML;

        // Rows
        let bHTML = "";
        for(let d=1; d<=days; d++) {
            // Plan
            bHTML += `<tr class="row-plan"><td class="sticky-day">${d} <small>გ</small></td>`;
            state.nurses.forEach(n => {
                const k = `${n}_${y}_${m}_${d}`;
                const v = state.shifts[k]?.plan || "";
                bHTML += `<td>
                    <select onchange="upd('${k}','plan',this.value)" style="border:none; background:transparent; width:100%; text-align:center;">
                        <option value=""></option>
                        <option value="8" ${v=='8'?'selected':''}>8</option>
                        <option value="16" ${v=='16'?'selected':''}>16</option>
                        <option value="24" ${v=='24'?'selected':''}>24</option>
                    </select>
                </td>`;
            });
            bHTML += `</tr>`;

            // Real
            bHTML += `<tr class="row-real"><td class="sticky-day">${d} <small>რ</small></td>`;
            state.nurses.forEach(n => {
                const k = `${n}_${y}_${m}_${d}`;
                const rv = state.shifts[k]?.real || "-";
                const dot = state.shifts[k]?.note ? `<span class="badge-dot"></span>` : "";
                bHTML += `<td onclick="openJ('${k}', '${n}', ${d})">${rv} ${dot}</td>`;
            });
            bHTML += `</tr>`;
        }
        document.getElementById('bTable').innerHTML = bHTML;
    }

    function upd(k, f, v) {
        if(!state.shifts[k]) state.shifts[k] = {};
        state.shifts[k][f] = v;
        if(f === 'plan' && !state.shifts[k].real) state.shifts[k].real = v;
        save();
        render();
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
                    state.shifts[key].real = hrs;
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
        closeModal(); save(); render();
    }

    function delNurse(n) { if(confirm("წავშალო?")) { state.nurses = state.nurses.filter(x=>x!==n); save(); render(); } }
    function closeModal() { document.getElementById('journalModal').style.display = 'none'; }
    function save() { localStorage.setItem('nurse_system_v3', JSON.stringify(state)); }

    init();
</script>
</body>
</html>
