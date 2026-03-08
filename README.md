<!DOCTYPE html>
<html lang="ka">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ექთნების მართვის სისტემა v2.0</title>
    <style>
        :root {
            --primary: #2c3e50;
            --accent: #3498db;
            --bg: #f8f9fa;
            --border: #dee2e6;
            --plan-row: #ffffff;
            --real-row: #e9ecef;
        }

        body { font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; background: var(--bg); margin: 0; padding: 20px; color: #333; }

        /* მართვის პანელი */
        .admin-panel { background: white; padding: 20px; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); margin-bottom: 25px; }
        .control-row { display: flex; flex-wrap: wrap; gap: 15px; align-items: flex-end; margin-bottom: 15px; }
        .control-group { display: flex; flex-direction: column; gap: 5px; }
        
        input, select, button { padding: 10px; border: 1px solid var(--border); border-radius: 6px; font-size: 14px; }
        button { cursor: pointer; border: none; font-weight: bold; transition: 0.2s; }
        
        .btn-add { background: #2ecc71; color: white; }
        .btn-save { background: var(--accent); color: white; }
        .btn-auto { background: #9b59b6; color: white; }
        .btn-clear { background: #e74c3c; color: white; }
        .btn-print { background: #f39c12; color: white; }
        button:hover { opacity: 0.8; transform: translateY(-1px); }

        /* ცხრილის სტილი - ვერტიკალური თარიღებით */
        .table-container { background: white; padding: 10px; border-radius: 8px; overflow-x: auto; box-shadow: 0 2px 10px rgba(0,0,0,0.05); }
        table { width: 100%; border-collapse: collapse; table-layout: fixed; }
        th, td { border: 1px solid var(--border); text-align: center; padding: 5px; position: relative; }
        
        th { background: var(--primary); color: white; height: 40px; }
        .sticky-col { position: sticky; left: 0; background: var(--primary); z-index: 10; width: 60px; }

        .nurse-header { background: #eee; color: var(--primary); font-weight: bold; width: 120px; }
        .row-plan { background: var(--plan-row); }
        .row-real { background: var(--real-row); cursor: pointer; font-size: 0.9em; font-weight: bold; }
        
        /* კონტექსტური მენიუ */
        .dots { cursor: pointer; padding: 5px; float: right; }
        .context-menu { position: absolute; background: white; border: 1px solid #ccc; box-shadow: 2px 2px 5px rgba(0,0,0,0.2); z-index: 100; display: none; }
        .context-menu div { padding: 8px 12px; cursor: pointer; color: red; }
        .context-menu div:hover { background: #f8f8f8; }

        /* მოდალური ფანჯარა */
        .modal { display: none; position: fixed; z-index: 1000; left: 0; top: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.6); }
        .modal-content { background: white; margin: 10% auto; padding: 25px; width: 400px; border-radius: 12px; display: flex; flex-direction: column; gap: 15px; }
        textarea { padding: 10px; border: 1px solid #ddd; border-radius: 5px; resize: none; }

        /* ბეჭდვა */
        @media print {
            .no-print { display: none !important; }
            body { padding: 0; }
            .table-container { box-shadow: none; }
        }

        /* ძებნის შედეგი */
        #searchInfo { background: #fff3cd; padding: 10px; border-radius: 6px; margin-top: 10px; display: none; }
    </style>
</head>
<body>

<div class="no-print admin-panel">
    <h2>🏥 ექთნების მართვის სისტემა</h2>
    
    <div class="control-row">
        <div class="control-group">
            <label>ახალი ექთანი</label>
            <div style="display:flex; gap:5px;">
                <input type="text" id="newNurseName" placeholder="სახელი გვარი">
                <button class="btn-add" onclick="addNurse()">➕</button>
            </div>
        </div>

        <div class="control-group">
            <label>პერიოდი</label>
            <div style="display:flex; gap:5px;">
                <select id="selectMonth" onchange="initSystem()"></select>
                <select id="selectYear" onchange="initSystem()"></select>
            </div>
        </div>

        <div class="control-group">
            <label>ძებნა</label>
            <input type="text" id="searchInput" placeholder="სახელი ან რიცხვი..." oninput="handleSearch()">
        </div>
    </div>

    <div class="control-row" style="background: #f0f7ff; padding: 15px; border-radius: 8px;">
        <div class="control-group">
            <label>ექთნის არჩევა</label>
            <input list="nurseDatalist" id="targetNurse" placeholder="ჩაწერეთ სახელი">
            <datalist id="nurseDatalist"></datalist>
        </div>
        <div class="control-group">
            <label>რიცხვი</label>
            <input type="number" id="targetDay" min="1" max="31">
        </div>
        <div class="control-group">
            <label>საათი (რეალური)</label>
            <select id="targetHours">
                <option value="8">8 სთ</option>
                <option value="16">16 სთ</option>
                <option value="24">24 სთ</option>
            </select>
        </div>
        <button class="btn-save" onclick="saveRealData()">💾 დაფიქსირება</button>
    </div>

    <div class="control-row">
        <button class="btn-auto" onclick="fillEveryFourth()">🔄 მე-4 დღეს შევსება (მონიშნულებზე)</button>
        <button class="btn-clear" onclick="clearPlan()">🗑️ გეგმის გასუფთავება</button>
        <button class="btn-print" onclick="window.print()">🖨️ ბეჭდვა</button>
    </div>

    <div id="searchInfo"></div>
</div>

<div class="table-container">
    <table id="mainTable">
        <thead id="tableHead"></thead>
        <tbody id="tableBody"></tbody>
    </table>
</div>

<div id="journalModal" class="modal">
    <div class="modal-content">
        <h3 id="modalTitle">პროფესიული ჟურნალი</h3>
        <label>კლინიკური შემთხვევა:</label>
        <textarea id="clinCase" rows="3"></textarea>
        <label>პირადი დღიური:</label>
        <textarea id="persNote" rows="3"></textarea>
        <div style="display:flex; gap:10px; justify-content: flex-end;">
            <button onclick="closeModal()">გაუქმება</button>
            <button class="btn-save" onclick="confirmJournal()">შენახვა</button>
        </div>
    </div>
</div>

<div id="contextMenu" class="context-menu">
    <div onclick="deleteNurseAction()">❌ წაშლა ბაზიდან</div>
</div>

<script>
    // მონაცემთა ბაზის იმიტაცია (შეგიძლიათ Firebase-ზე მიაბათ)
    let state = JSON.parse(localStorage.getItem('nurseDB')) || {
        nurses: [],
        schedule: {} // ფორმატი: { "nurseName_YYYY_MM_DD": { plan: 8, real: 8, case: "", note: "" } }
    };

    const months = ["იანვარი", "თებერვალი", "მარტი", "აპრილი", "მაისი", "ივნისი", "ივლისი", "აგვისტო", "სექტემბერი", "ოქტომბერი", "ნოემბერი", "დეკემბერი"];
    let selectedNurseForDelete = null;
    let activeLogKey = null;

    function initSystem() {
        const mSelect = document.getElementById('selectMonth');
        const ySelect = document.getElementById('selectYear');
        
        if(mSelect.options.length === 0) {
            months.forEach((m, i) => mSelect.add(new Option(m, i)));
            for(let y=2024; y<=2026; y++) ySelect.add(new Option(y, y));
            mSelect.value = new Date().getMonth();
            ySelect.value = new Date().getFullYear();
        }

        renderTable();
        updateDatalist();
        saveState();
    }

    function renderTable() {
        const month = parseInt(document.getElementById('selectMonth').value);
        const year = parseInt(document.getElementById('selectYear').value);
        const daysInMonth = new Date(year, month + 1, 0).getDate();
        
        const head = document.getElementById('tableHead');
        const body = document.getElementById('tableBody');
        
        // Header
        let headHTML = `<tr><th class="sticky-col">დღე</th>`;
        state.nurses.forEach(n => {
            headHTML += `<th class="nurse-header">
                <input type="checkbox" class="nurse-check" value="${n}"> ${n}
                <span class="dots" onclick="showContext(event, '${n}')">⋮</span>
            </th>`;
        });
        headHTML += `</tr>`;
        head.innerHTML = headHTML;

        // Body (ვერტიკალური დღეები)
        let bodyHTML = "";
        for (let d = 1; d <= daysInMonth; d++) {
            // გეგმის მწკრივი
            bodyHTML += `<tr class="row-plan"><td class="sticky-col">${d} (გ)</td>`;
            state.nurses.forEach(n => {
                const key = `${n}_${year}_${month}_${d}`;
                const val = state.schedule[key]?.plan || "";
                bodyHTML += `<td>
                    <select onchange="updateCell('${key}', 'plan', this.value)">
                        <option value=""></option>
                        <option value="8" ${val==8?'selected':''}>8</option>
                        <option value="16" ${val==16?'selected':''}>16</option>
                        <option value="24" ${val==24?'selected':''}>24</option>
                    </select>
                </td>`;
            });
            bodyHTML += `</tr>`;

            // რეალური მწკრივი
            bodyHTML += `<tr class="row-real"><td class="sticky-col">${d} (რ)</td>`;
            state.nurses.forEach(n => {
                const key = `${n}_${year}_${month}_${d}`;
                const realVal = state.schedule[key]?.real || "";
                const hasLog = (state.schedule[key]?.case || state.schedule[key]?.note) ? "📝" : "";
                bodyHTML += `<td onclick="openJournal('${key}', '${n}', ${d})">${realVal} ${hasLog}</td>`;
            });
            bodyHTML += `</tr>`;
        }

        // ჯამების მწკრივი
        bodyHTML += `<tr style="background:#2c3e50; color:white;"><td class="sticky-col">ჯამი</td>`;
        state.nurses.forEach(n => {
            let totalPlan = 0, totalReal = 0;
            for(let d=1; d<=daysInMonth; d++) {
                const k = `${n}_${year}_${month}_${d}`;
                totalPlan += parseInt(state.schedule[k]?.plan || 0);
                totalReal += parseInt(state.schedule[k]?.real || 0);
            }
            bodyHTML += `<td>გ:${totalPlan}<br>რ:${totalReal}</td>`;
        });
        bodyHTML += `</tr>`;

        body.innerHTML = bodyHTML;
    }

    function addNurse() {
        const name = document.getElementById('newNurseName').value.trim();
        if(!name || state.nurses.includes(name)) return alert("სახელი ცარიელია ან უკვე არსებობს");
        state.nurses.push(name);
        document.getElementById('newNurseName').value = "";
        initSystem();
    }

    function updateCell(key, field, value) {
        if(!state.schedule[key]) state.schedule[key] = {};
        state.schedule[key][field] = value;
        saveState();
        renderTable();
    }

    function saveRealData() {
        const name = document.getElementById('targetNurse').value;
        const day = document.getElementById('targetDay').value;
        const hrs = document.getElementById('targetHours').value;
        const month = document.getElementById('selectMonth').value;
        const year = document.getElementById('selectYear').value;

        if(!name || !day) return alert("შეავსეთ სახელი და რიცხვი");
        const key = `${name}_${year}_${month}_${day}`;
        updateCell(key, 'real', hrs);
    }

    function fillEveryFourth() {
        const selected = Array.from(document.querySelectorAll('.nurse-check:checked')).map(cb => cb.value);
        if(selected.length === 0) return alert("მონიშნეთ ექთნები ცხრილში");
        
        const month = parseInt(document.getElementById('selectMonth').value);
        const year = parseInt(document.getElementById('selectYear').value);
        const daysInMonth = new Date(year, month + 1, 0).getDate();

        selected.forEach(nurse => {
            let firstDay = -1;
            let hourValue = 0;
            
            for(let d=1; d<=daysInMonth; d++) {
                const k = `${nurse}_${year}_${month}_${d}`;
                if(state.schedule[k]?.plan) {
                    firstDay = d;
                    hourValue = state.schedule[k].plan;
                    break;
                }
            }

            if(firstDay !== -1) {
                for(let d = firstDay; d <= daysInMonth; d += 4) {
                    const k = `${nurse}_${year}_${month}_${d}`;
                    if(!state.schedule[k]) state.schedule[k] = {};
                    state.schedule[k].plan = hourValue;
                }
            }
        });
        saveState();
        renderTable();
    }

    function clearPlan() {
        if(!confirm("დარწმუნებული ხართ, რომ გსურთ გეგმის წაშლა?")) return;
        const selected = Array.from(document.querySelectorAll('.nurse-check:checked')).map(cb => cb.value);
        const month = document.getElementById('selectMonth').value;
        const year = document.getElementById('selectYear').value;

        Object.keys(state.schedule).forEach(key => {
            const parts = key.split('_');
            if(selected.includes(parts[0]) && parts[2] == month && parts[1] == year) {
                state.schedule[key].plan = "";
            }
        });
        saveState();
        renderTable();
    }

    function handleSearch() {
        const query = document.getElementById('searchInput').value.trim();
        const info = document.getElementById('searchInfo');
        if(!query) { info.style.display = 'none'; return; }

        const month = document.getElementById('selectMonth').value;
        const year = document.getElementById('selectYear').value;
        let results = [];

        if(!isNaN(query)) { // რიცხვით ძებნა
            state.nurses.forEach(n => {
                const k = `${n}_${year}_${month}_${query}`;
                if(state.schedule[k]?.plan) results.push(n);
            });
            info.innerHTML = `📅 <strong>${query} რიცხვში მორიგეები:</strong> ${results.join(', ') || 'არავინ'}`;
        } else { // სახელით ძებნა
            const dates = [];
            for(let d=1; d<=31; d++) {
                const k = `${query}_${year}_${month}_${d}`;
                if(state.schedule[k]?.plan) dates.push(d);
            }
            info.innerHTML = `👩‍⚕️ <strong>${query}-ს მორიგეობები:</strong> ${dates.join(', ') || 'ვერ მოიძებნა'}`;
        }
        info.style.display = 'block';
    }

    // ჟურნალის ფუნქციები
    function openJournal(key, nurse, day) {
        activeLogKey = key;
        document.getElementById('modalTitle').innerText = `${nurse} - ${day} რიცხვი`;
        document.getElementById('clinCase').value = state.schedule[key]?.case || "";
        document.getElementById('persNote').value = state.schedule[key]?.note || "";
        document.getElementById('journalModal').style.display = 'block';
    }

    function confirmJournal() {
        if(!state.schedule[activeLogKey]) state.schedule[activeLogKey] = {};
        state.schedule[activeLogKey].case = document.getElementById('clinCase').value;
        state.schedule[activeLogKey].note = document.getElementById('persNote').value;
        closeModal();
        saveState();
        renderTable();
    }

    function closeModal() { document.getElementById('journalModal').style.display = 'none'; }

    // კონტექსტური მენიუ
    function showContext(e, nurse) {
        e.preventDefault();
        selectedNurseForDelete = nurse;
        const menu = document.getElementById('contextMenu');
        menu.style.display = 'block';
        menu.style.left = e.pageX + 'px';
        menu.style.top = e.pageY + 'px';
    }

    function deleteNurseAction() {
        state.nurses = state.nurses.filter(n => n !== selectedNurseForDelete);
        saveState();
        initSystem();
        document.getElementById('contextMenu').style.display = 'none';
    }

    document.addEventListener('click', (e) => {
        if(!e.target.classList.contains('dots')) document.getElementById('contextMenu').style.display = 'none';
    });

    function updateDatalist() {
        const dl = document.getElementById('nurseDatalist');
        dl.innerHTML = "";
        state.nurses.forEach(n => dl.add(new Option(n, n)));
    }

    function saveState() { localStorage.setItem('nurseDB', JSON.stringify(state)); }

    window.onload = initSystem;
</script>

</body>
</html>
