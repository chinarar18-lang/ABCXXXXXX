<!DOCTYPE html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>🌀 陀螺比賽雲端管理系統</title>
    <style>
        :root { --primary: #3b82f6; --primary-dark: #1d4ed8; --bg-color: #f8fafc; --card-bg: #ffffff; --text-main: #0f172a; --text-muted: #64748b; --border: #e2e8f0; --success: #10b981; --success-bg: #ecfdf5; --radius: 12px; }
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; background-color: var(--bg-color); color: var(--text-main); margin: 0; padding: 20px; }
        .container { max-width: 1200px; margin: 0 auto; }
        .header-box { display: flex; justify-content: space-between; align-items: center; background: var(--card-bg); padding: 20px 24px; border-radius: var(--radius); box-shadow: 0 1px 3px rgba(0,0,0,0.05); margin-bottom: 20px; }
        h1 { margin: 0; font-size: 24px; color: var(--primary-dark); }
        .admin-bar { background: var(--card-bg); padding: 15px 24px; border-radius: var(--radius); box-shadow: 0 1px 3px rgba(0,0,0,0.05); margin-bottom: 24px; display: flex; justify-content: space-between; align-items: center; }
        .mode-badge { padding: 6px 12px; border-radius: 20px; font-weight: 600; font-size: 13px; }
        .mode-view { background-color: #f1f5f9; color: var(--text-muted); }
        .mode-edit { background-color: var(--success-bg); color: var(--success); }
        .card { background: var(--card-bg); border-radius: var(--radius); padding: 24px; margin-bottom: 24px; box-shadow: 0 1px 3px rgba(0,0,0,0.05); }
        .tab-buttons { display: flex; gap: 8px; margin-bottom: 24px; flex-wrap: wrap; }
        .tab-btn { background-color: #e2e8f0; color: var(--text-muted); border: none; padding: 10px 18px; border-radius: 8px; cursor: pointer; font-size: 14px; font-weight: 600; transition: all 0.2s; }
        .tab-btn.active { background-color: var(--primary); color: white; box-shadow: 0 4px 12px rgba(59, 130, 246, 0.3); }
        .section { display: none; }
        .section.active { display: block; }
        button { background-color: var(--primary); color: white; border: none; padding: 10px 20px; border-radius: 8px; cursor: pointer; font-size: 14px; font-weight: 600; transition: background 0.2s; }
        button:hover { background-color: var(--primary-dark); }
        .btn-success { background-color: var(--success); }
        input[type="number"] { width: 50px; padding: 6px; border: 1px solid var(--border); border-radius: 6px; text-align: center; font-size: 15px; }
        input:disabled, textarea:disabled { background-color: #f8fafc; color: var(--text-muted); cursor: not-allowed; }
        textarea { width: 100%; height: 120px; padding: 12px; border: 1px solid var(--border); border-radius: 8px; font-size: 14px; box-sizing: border-box; resize: vertical; }
        table { width: 100%; border-collapse: collapse; margin-top: 12px; margin-bottom: 20px; border-radius: 8px; overflow: hidden; }
        th, td { border: 1px solid var(--border); padding: 10px 14px; text-align: center; font-size: 14px; }
        th { background-color: #f8fafc; color: var(--text-muted); font-weight: 600; }
        .grid-2 { display: grid; grid-template-columns: 1fr 1fr; gap: 24px; }
        .grid-3 { display: grid; grid-template-columns: repeat(3, 1fr); gap: 15px; }
        .match-row { display: flex; align-items: center; justify-content: center; gap: 12px; margin-bottom: 12px; padding: 12px; background-color: #f8fafc; border-radius: 8px; border: 1px solid var(--border); }
        .team-name { width: 140px; text-align: right; font-weight: 600; }
        .team-name.right { text-align: left; }
        .bracket-container { display: flex; justify-content: space-between; gap: 16px; overflow-x: auto; padding: 10px 0; }
        .bracket-round { display: flex; flex-direction: column; justify-content: space-around; flex: 1; min-width: 200px; }
        .bracket-match { background: #f8fafc; border: 1px solid var(--border); border-radius: 8px; padding: 12px; margin: 8px 0; }
        .bracket-team { padding: 10px; margin: 4px 0; background: white; border: 1px solid var(--border); border-radius: 6px; cursor: pointer; text-align: center; font-weight: 600; transition: all 0.2s; }
        .bracket-team.readonly { cursor: default; }
        .bracket-team:not(.readonly):hover { border-color: var(--primary); background-color: #eff6ff; }
        .bracket-team.winner { background-color: var(--success-bg); border-color: var(--success); color: var(--success); }
        @media (max-width: 768px) { .grid-2, .grid-3 { grid-template-columns: 1fr; } }
    </style>
</head>
<body>

<div class="container">
    <div class="header-box">
        <h1>🌀 陀螺比賽雲端實時管理系統</h1>
    </div>
    
    <div class="admin-bar">
        <div>
            <span>系統狀態：</span>
            <span id="modeStatus" class="mode-badge mode-view">🔍 檢視模式 (唯讀)</span>
        </div>
        <div id="authArea">
            <input type="password" id="adminPassword" placeholder="管理員密碼" style="padding: 8px 12px; border: 1px solid var(--border); border-radius: 6px; width: 130px;">
            <button onclick="tryUnlock()">解鎖編輯</button>
        </div>
        <div id="editArea" style="display:none;">
            <span style="color: var(--success); font-weight: 600; margin-right: 12px;">✨ 編輯中 (持續有效)</span>
            <button class="btn-success" onclick="syncToCloud()">☁️ 同步所有修改</button>
            <button onclick="lockAdmin()" style="background-color: var(--text-muted);">鎖定</button>
        </div>
    </div>

    <div class="tab-buttons">
        <button class="tab-btn active" onclick="switchTab('setup')">1. 隊伍名單與抽籤</button>
        <button class="tab-btn" onclick="switchTab('groupStage')">2. 小組賽積分榜</button>
        <button class="tab-btn" onclick="switchTab('schedule')">3. 小組賽盤次(4盤)</button>
        <button class="tab-btn" onclick="switchTab('youthKnockout')">4. 青年組走線圖</button>
        <button class="tab-btn" onclick="switchTab('kidsKnockout')">5. 兒童組走線圖</button>
        <button class="tab-btn" onclick="switchTab('knockoutSchedule')">6. 淘汰賽盤次(3盤)</button>
    </div>

    <!-- 第一頁：抽籤 -->
    <div id="setup" class="section active">
        <div class="card">
            <h2>組別與抽籤設定</h2>
            <div class="grid-2">
                <div>
                    <h3>青年組 (分3組 A, B, C，最少4隊)</h3>
                    <textarea id="youthTeamsInput" class="edit-locked" disabled placeholder="請輸入隊伍名稱，每行一隊..."></textarea>
                    <button class="edit-locked" disabled onclick="drawYouth()">進行青年組抽籤</button>
                </div>
                <div>
                    <h3>兒童組 (分2組 D, E，最少3隊)</h3>
                    <textarea id="kidsTeamsInput" class="edit-locked" disabled placeholder="請輸入隊伍名稱，每行一隊..."></textarea>
                    <button class="edit-locked" disabled onclick="drawKids()">進行兒童組抽籤</button>
                </div>
            </div>
        </div>
        <div class="card">
            <h2>抽籤結果</h2>
            <div class="grid-2">
                <div><h3>青年組</h3><div id="youthGroupsResult" style="color: var(--text-muted);">尚未進行抽籤</div></div>
                <div><h3>兒童組</h3><div id="kidsGroupsResult" style="color: var(--text-muted);">尚未進行抽籤</div></div>
            </div>
        </div>
    </div>

    <!-- 第二頁：小組賽積分 -->
    <div id="groupStage" class="section">
        <div class="card">
            <h2>小組賽成績與即時積分榜</h2>
            <div class="tab-buttons">
                <button class="tab-btn active" onclick="switchGroupTab('youthGroupView')">青年組</button>
                <button class="tab-btn" onclick="switchGroupTab('kidsGroupView')">兒童組</button>
            </div>
            <div id="youthGroupView" class="group-sub-section"><div id="youthContentContainer" style="color: var(--text-muted);">請先完成抽籤以生成對賽。</div></div>
            <div id="kidsGroupView" class="group-sub-section" style="display:none;"><div id="kidsContentContainer" style="color: var(--text-muted);">請先完成抽籤以生成對賽。</div></div>
        </div>
    </div>

    <!-- 第三頁：小組賽盤次 (4個盤) -->
    <div id="schedule" class="section">
        <div class="card">
            <h2>小組賽盤次分配 (4 個盤)</h2>
            <button class="edit-locked" disabled onclick="generateSchedule()">重新整理盤次</button>
            <div id="scheduleContainer" style="margin-top: 16px; color: var(--text-muted);">請先完成抽籤。</div>
        </div>
    </div>

    <!-- 第四頁：青年組走線圖 -->
    <div id="youthKnockout" class="section">
        <div class="card">
            <h2>青年組八強淘汰賽</h2>
            <button class="edit-locked" disabled onclick="initYouthBracket()">生成青年組八強對陣</button>
            <div class="bracket-container" id="youthBracketContainer" style="margin-top: 16px; color: var(--text-muted);">請先完成小組賽。</div>
        </div>
    </div>

    <!-- 第五頁：兒童組走線圖 -->
    <div id="kidsKnockout" class="section">
        <div class="card">
            <h2>兒童組四強淘汰賽</h2>
            <button class="edit-locked" disabled onclick="initKidsBracket()">生成兒童組四強對陣</button>
            <div class="bracket-container" id="kidsBracketContainer" style="margin-top: 16px; color: var(--text-muted);">請先完成小組賽。</div>
        </div>
    </div>

    <!-- 第六頁：淘汰賽盤次 (3個盤) -->
    <div id="knockoutSchedule" class="section">
        <div class="card">
            <h2>淘汰賽盤次分配 (3 個盤)</h2>
            <button class="edit-locked" disabled onclick="generateKoSchedule()">生成淘汰賽盤次</button>
            <div id="koScheduleContainer" style="margin-top: 16px; color: var(--text-muted);">請先初始化走線圖。</div>
        </div>
    </div>
</div>

<script>
    const ADMIN_PASSWORD = "admin888"; 
    const WEB_APP_URL = "https://script.google.com/macros/s/AKfycbw3_WKdwd6yUt_oRibRIL62jjboDkapre7Wwx4Pcfet5rs0CCfhyCfSqkCchubThhvl/exec"; 

    let isAdmin = false;
    let state = { youthTeamsInputText: '', kidsTeamsInputText: '', youthGroups: { 'A': [], 'B': [], 'C': [] }, kidsGroups: { 'D': [], 'E': [] }, youthMatches: {}, kidsMatches: {} };
    let youthBracketState = { qf: Array(4).fill({t1:'', t2:'', w:null}), sf: Array(2).fill({t1:'', t2:'', w:null}), final: {t1:'', t2:'', w:null}, third: {t1:'', t2:'', w:null} };
    let kidsBracketState = { sf: Array(2).fill({t1:'', t2:'', w:null}), final: {t1:'', t2:'', w:null}, third: {t1:'', t2:'', w:null} };

    window.onload = function() { fetchFromCloud(); };

    function fetchFromCloud() {
        fetch(WEB_APP_URL).then(res => res.json()).then(data => {
            if(data && data.length > 0) {
                try {
                    let cloudData = JSON.parse(data[0][0]);
                    state = cloudData.state;
                    youthBracketState = cloudData.youthBracketState;
                    kidsBracketState = cloudData.kidsBracketState;
                    updateUI();
                } catch(e) {}
            }
        }).catch(err => {});
    }

    function syncToCloud() {
        let packageData = [[JSON.stringify({ state, youthBracketState, kidsBracketState })]];
        fetch(WEB_APP_URL, { method: 'POST', mode: 'no-cors', body: JSON.stringify(packageData) })
        .then(() => { alert('☁️ 同步成功！網頁將自動重新整理。'); setTimeout(() => window.location.reload(), 1500); })
        .catch(err => alert('同步失敗。'));
    }

    function tryUnlock() {
        if(document.getElementById('adminPassword').value === ADMIN_PASSWORD) {
            isAdmin = true;
            document.getElementById('authArea').style.display = 'none';
            document.getElementById('editArea').style.display = 'block';
            document.getElementById('modeStatus').innerHTML = '✨ 編輯模式 (已解鎖)';
            document.getElementById('modeStatus').className = 'mode-badge mode-edit';
            document.querySelectorAll('.edit-locked').forEach(el => el.disabled = false);
            updateUI();
            alert('已成功解鎖！你可以隨意修改所有內容，完成後點擊右上角「同步所有修改」即可。');
        } else { alert('❌ 密碼錯誤'); }
    }

    function lockAdmin() {
        isAdmin = false;
        document.getElementById('authArea').style.display = 'block';
        document.getElementById('editArea').style.display = 'none';
        document.getElementById('adminPassword').value = '';
        document.getElementById('modeStatus').innerHTML = '🔍 檢視模式 (唯讀)';
        document.getElementById('modeStatus').className = 'mode-badge mode-view';
        document.querySelectorAll('.edit-locked').forEach(el => el.disabled = true);
        updateUI();
    }

    function updateUI() {
        if(document.getElementById('youthTeamsInput')) {
            document.getElementById('youthTeamsInput').value = state.youthTeamsInputText || '';
            document.getElementById('kidsTeamsInput').value = state.kidsTeamsInputText || '';
        }
        renderYouthGroupsResult();
        renderKidsGroupsResult();
        renderGroupView('youth');
        renderGroupView('kids');
        renderYouthBracket();
        renderKidsBracket();
        generateSchedule(); // 自動渲染盤次
    }

    function switchTab(tabId) {
        document.querySelectorAll('.section').forEach(el => el.classList.remove('active'));
        document.querySelectorAll('.tab-buttons > .tab-btn').forEach(el => el.classList.remove('active'));
        document.getElementById(tabId).classList.add('active');
        event.target.classList.add('active');
    }
    function switchGroupTab(subId) {
        document.querySelectorAll('.group-sub-section').forEach(el => el.style.display = 'none');
        document.getElementById(subId).style.display = 'block';
    }
    function shuffle(array) {
        let ci = array.length, ri;
        while (ci !== 0) { ri = Math.floor(Math.random() * ci); ci--; [array[ci], array[ri]] = [array[ri], array[ci]]; }
        return array;
    }
    function generateRoundRobin(teams, groupId, matchObj) {
        matchObj[groupId] = [];
        let mId = 1;
        for (let i = 0; i < teams.length; i++) {
            for (let j = i + 1; j < teams.length; j++) {
                matchObj[groupId].push({ id: `${groupId}-${mId++}`, t1: teams[i], t2: teams[j], s1: null, s2: null });
            }
        }
    }

    function drawYouth() {
        if(!isAdmin) return;
        let input = document.getElementById('youthTeamsInput').value.trim();
        state.youthTeamsInputText = input;
        let teams = input.split('\n').map(t => t.trim()).filter(t => t);
        if(teams.length < 4) { alert('青年組最少需要 4 隊'); return; }
        teams = shuffle(teams);
        state.youthGroups = { 'A': [], 'B': [], 'C': [] };
        let gNames = ['A', 'B', 'C'];
        teams.forEach((t, i) => { state.youthGroups[gNames[i % 3]].push(t); });
        gNames.forEach(g => generateRoundRobin(state.youthGroups[g], g, state.youthMatches));
        renderYouthGroupsResult();
        renderGroupView('youth');
        generateSchedule(); // 自動生成盤次
        alert('青年組抽籤完成！已自動分配小組賽盤次。');
    }

    function renderYouthGroupsResult() {
        let html = '';
        ['A', 'B', 'C'].forEach(g => {
            let list = state.youthGroups[g] || [];
            if(list.length > 0) { html += `<h4>組別 ${g}:</h4><ul>` + list.map(t => `<li>${t}</li>`).join('') + `</ul>`; }
        });
        document.getElementById('youthGroupsResult').innerHTML = html || '尚未進行抽籤';
    }

    function drawKids() {
        if(!isAdmin) return;
        let input = document.getElementById('kidsTeamsInput').value.trim();
        state.kidsTeamsInputText = input;
        let teams = input.split('\n').map(t => t.trim()).filter(t => t);
        if(teams.length < 3) { alert('兒童組最少需要 3 隊'); return; }
        teams = shuffle(teams);
        state.kidsGroups = { 'D': [], 'E': [] };
        let gNames = ['D', 'E'];
        teams.forEach((t, i) => { state.kidsGroups[gNames[i % 2]].push(t); });
        gNames.forEach(g => generateRoundRobin(state.kidsGroups[g], g, state.kidsMatches));
        renderKidsGroupsResult();
        renderGroupView('kids');
        generateSchedule(); // 自動生成盤次
        alert('兒童組抽籤完成！已自動分配小組賽盤次。');
    }

    function renderKidsGroupsResult() {
        let html = '';
        ['D', 'E'].forEach(g => {
            let list = state.kidsGroups[g] || [];
            if(list.length > 0) { html += `<h4>組別 ${g}:</h4><ul>` + list.map(t => `<li>${t}</li>`).join('') + `</ul>`; }
        });
        document.getElementById('kidsGroupsResult').innerHTML = html || '尚未進行抽籤';
    }

    function updateScore(type, groupId, matchId, tIndex, val) {
        if(!isAdmin) return;
        let matches = type === 'youth' ? state.youthMatches[groupId] : state.kidsMatches[groupId];
        let m = matches.find(x => x.id === matchId);
        if(m) {
            if(tIndex === 1) m.s1 = val !== "" ? parseInt(val) : null;
            if(tIndex === 2) m.s2 = val !== "" ? parseInt(val) : null;
        }
        renderGroupView(type);
    }

    function calculateStandings(groups, matches) {
        let st = {};
        for(let g in groups) {
            st[g] = groups[g].map(name => ({ name, played: 0, w3: 0, w2: 0, l1: 0, l0: 0, pts: 0 }));
            if(!matches[g]) continue;
            matches[g].forEach(m => {
                if(m.s1 !== null && m.s2 !== null) {
                    let t1 = st[g].find(x => x.name === m.t1);
                    let t2 = st[g].find(x => x.name === m.t2);
                    if(t1 && t2) {
                        t1.played++; t2.played++;
                        if(m.s1 === 3 && m.s2 === 0) { t1.w3++; t1.pts += 3; t2.l0++; }
                        else if(m.s1 === 2 && m.s2 === 1) { t1.w2++; t1.pts += 2; t2.l1++; t2.pts += 1; }
                        else if(m.s1 === 1 && m.s2 === 2) { t2.w2++; t2.pts += 2; t1.l1++; t1.pts += 1; }
                        else if(m.s1 === 0 && m.s2 === 3) { t2.w3++; t2.pts += 3; t1.l0++; }
                    }
                }
            });
            st[g].sort((a, b) => b.pts - a.pts);
        }
        return st;
    }

    function renderGroupView(type) {
        let groups = type === 'youth' ? state.youthGroups : state.kidsGroups;
        let matches = type === 'youth' ? state.youthMatches : state.kidsMatches;
        let st = calculateStandings(groups, matches);
        let container = document.getElementById(type === 'youth' ? 'youthContentContainer' : 'kidsContentContainer');
        if(!Object.keys(groups).some(g => groups[g].length > 0)) { container.innerHTML = '請先完成抽籤。'; return; }

        let html = '';
        for(let g in groups) {
            if(groups[g].length === 0) continue;
            html += `<h3>組別 ${g}</h3><div class="grid-2">`;
            html += `<div><h4>積分榜</h4><table><tr><th>名</th><th>隊伍</th><th>賽</th><th>3分</th><th>2分</th><th>1分</th><th>0分</th><th>積分</th></tr>`;
            st[g].forEach((t, i) => {
                html += `<tr><td>${i+1}</td><td><b>${t.name}</b></td><td>${t.played}</td><td>${t.w3}</td><td>${t.w2}</td><td>${t.l1}</td><td>${t.l0}</td><td><b>${t.pts}</b></td></tr>`;
            });
            html += `</table></div><div><h4>對賽比分填寫</h4>`;
            matches[g].forEach(m => {
                let dis = isAdmin ? '' : 'disabled';
                html += `<div class="match-row"><span class="team-name">${m.t1}</span><input type="number" min="0" max="3" ${dis} value="${m.s1 !== null ? m.s1 : ''}" onchange="updateScore('${type}','${g}','${m.id}',1,this.value)"><span>:</span><input type="number" min="0" max="3" ${dis} value="${m.s2 !== null ? m.s2 : ''}" onchange="updateScore('${type}','${g}','${m.id}',2,this.value)"><span class="team-name right">${m.t2}</span></div>`;
            });
            html += `</div></div><hr style="border:0; border-top:1px solid var(--border); margin: 20px 0;">`;
        }
        container.innerHTML = html;
    }

    // 小組賽：4個盤
    function generateSchedule() {
        let allM = [];
        for(let g in state.youthMatches) state.youthMatches[g].forEach(m => allM.push({g, ...m, type:'青年'}));
        for(let g in state.kidsMatches) state.kidsMatches[g].forEach(m => allM.push({g, ...m, type:'兒童'}));
        if(allM.length === 0) { 
            document.getElementById('scheduleContainer').innerHTML = '請先完成抽籤。';
            return; 
        }
        let tables = [[],[],[],[]];
        allM.forEach((m, i) => tables[i % 4].push(m));
        let html = '<div class="grid-2">';
        tables.forEach((t, i) => {
            html += `<div><h3>盤 ${i+1}</h3><ul>`;
            t.forEach(m => html += `<li>[${m.type} ${m.g}組] ${m.t1} vs ${m.t2}</li>`);
            html += `</ul></div>`;
        });
        document.getElementById('scheduleContainer').innerHTML = html + '</div>';
    }

    // 淘汰賽：3個盤
    function generateKoSchedule() {
        let ko = [];
        youthBracketState.qf.forEach(m => { if(m.t1 && m.t2) ko.push({stage:'青年八強', t1:m.t1, t2:m.t2}); });
        youthBracketState.sf.forEach(m => { if(m.t1 && m.t2) ko.push({stage:'青年四強', t1:m.t1, t2:m.t2}); });
        if(youthBracketState.final.t1) ko.push({stage:'青年決賽', t1:youthBracketState.final.t1, t2:youthBracketState.final.t2});
        if(youthBracketState.third.t1) ko.push({stage:'青年季軍', t1:youthBracketState.third.t1, t2:youthBracketState.third.t2});
        
        kidsBracketState.sf.forEach(m => { if(m.t1 && m.t2) ko.push({stage:'兒童四強', t1:m.t1, t2:m.t2}); });
        if(kidsBracketState.final.t1) ko.push({stage:'兒童決賽', t1:kidsBracketState.final.t1, t2:kidsBracketState.final.t2});
        if(kidsBracketState.third.t1) ko.push({stage:'兒童季軍', t1:kidsBracketState.third.t1, t2:kidsBracketState.third.t2});

        if(ko.length === 0) { alert('請先在走線圖生成對陣'); return; }
        let tables = [[],[],[]];
        ko.forEach((m, i) => tables[i % 3].push(m));
        let html = '<div class="grid-3">';
        tables.forEach((t, i) => {
            html += `<div><h3>盤 ${i+1}</h3><ul>`;
            t.forEach(m => html += `<li>[${m.stage}] ${m.t1} vs ${m.t2}</li>`);
            html += `</ul></div>`;
        });
        document.getElementById('koScheduleContainer').innerHTML = html + '</div>';
    }

    function initYouthBracket() {
        if(!isAdmin) return;
        let st = calculateStandings(state.youthGroups, state.youthMatches);
        if(!st['A'] || !st['A'][0]) { alert('請先完成小組賽'); return; }
        let t = {
            A1: st['A'][0]?.name || '', A2: st['A'][1]?.name || '',
            B1: st['B'][0]?.name || '', B2: st['B'][1]?.name || '',
            C1: st['C'][0]?.name || '', C2: st['C'][1]?.name || ''
        };
        let thirds = [{g:'A', ...st['A'][2]}, {g:'B', ...st['B'][2]}, {g:'C', ...st['C'][2]}].filter(x => x.name).sort((a,b)=>b.pts-a.pts);
        youthBracketState.qf = [
            {t1: t.A1, t2: thirds[0]?.name || '第三名1', w:null},
            {t1: t.B2, t2: t.C2, w:null},
            {t1: t.B1, t2: thirds[1]?.name || '第三名2', w:null},
            {t1: t.C1, t2: t.A2, w:null}
        ];
        renderYouthBracket();
        alert('青年組八強生成成功！');
    }

    function initKidsBracket() {
        if(!isAdmin) return;
        let st = calculateStandings(state.kidsGroups, state.kidsMatches);
        if(!st['D'] || !st['D'][0]) { alert('請先完成小組賽'); return; }
        kidsBracketState.sf = [
            {t1: st['D'][0]?.name||'', t2: st['E'][1]?.name||'', w:null},
            {t1: st['E'][0]?.name||'', t2: st['D'][1]?.name||'', w:null}
        ];
        renderKidsBracket();
        alert('兒童組四強生成成功！');
    }

    function setYouthWinner(round, idx, name) {
        if(!isAdmin || !name) return;
        if(round==='qf') {
            youthBracketState.qf[idx].w = name;
            if(idx<2) youthBracketState.sf[0][idx===0?'t1':'t2'] = name;
            else youthBracketState.sf[1][idx===2?'t1':'t2'] = name;
        } else if(round==='sf') {
            youthBracketState.sf[idx].w = name;
            if(idx===0) youthBracketState.final.t1 = name; else youthBracketState.final.t2 = name;
            let los = youthBracketState.sf[idx].t1===name ? youthBracketState.sf[idx].t2 : youthBracketState.sf[idx].t1;
            if(idx===0) youthBracketState.third.t1 = los; else youthBracketState.third.t2 = los;
        } else if(round==='final') youthBracketState.final.w = name;
        else if(round==='third') youthBracketState.third.w = name;
        renderYouthBracket();
    }

    function setKidsWinner(round, idx, name) {
        if(!isAdmin || !name) return;
        if(round==='sf') {
            kidsBracketState.sf[idx].w = name;
            if(idx===0) kidsBracketState.final.t1 = name; else kidsBracketState.final.t2 = name;
            let los = kidsBracketState.sf[idx].t1===name ? kidsBracketState.sf[idx].t2 : kidsBracketState.sf[idx].t1;
            if(idx===0) kidsBracketState.third.t1 = los; else kidsBracketState.third.t2 = los;
        } else if(round==='final') kidsBracketState.final.w = name;
        else if(round==='third') kidsBracketState.third.w = name;
        renderKidsBracket();
    }

    function renderYouthBracket() {
        let rc = isAdmin ? '' : 'readonly';
        let html = `<div class="bracket-round"><h3>八強</h3>` + youthBracketState.qf.map((m,i)=>`<div class="bracket-match"><div class="bracket-team ${rc} ${youthBracketState.qf[i].w===m.t1?'winner':''}" onclick="setYouthWinner('qf',${i},'${m.t1}')">${m.t1||'等待'}</div><div style="text-align:center;font-size:12px;color:var(--text-muted);margin:2px 0;">VS</div><div class="bracket-team ${rc} ${youthBracketState.qf[i].w===m.t2?'winner':''}" onclick="setYouthWinner('qf',${i},'${m.t2}')">${m.t2||'等待'}</div></div>`).join('') + `</div>`;
        html += `<div class="bracket-round"><h3>四強</h3>` + youthBracketState.sf.map((m,i)=>`<div class="bracket-match" style="margin:40px 0;"><div class="bracket-team ${rc} ${youthBracketState.sf[i].w===m.t1?'winner':''}" onclick="setYouthWinner('sf',${i},'${m.t1}')">${m.t1||'等待'}</div><div style="text-align:center;font-size:12px;color:var(--text-muted);margin:2px 0;">VS</div><div class="bracket-team ${rc} ${youthBracketState.sf[i].w===m.t2?'winner':''}" onclick="setYouthWinner('sf',${i},'${m.t2}')">${m.t2||'等待'}</div></div>`).join('') + `</div>`;
        html += `<div class="bracket-round"><h3>決賽</h3><div class="bracket-match"><h4 style="margin:0 0 4px;color:var(--primary);">冠軍戰</h4><div class="bracket-team ${rc} ${youthBracketState.final.w===youthBracketState.final.t1?'winner':''}" onclick="setYouthWinner('final',0,'${youthBracketState.final.t1}')">${youthBracketState.final.t1||'等待'}</div><div style="text-align:center;font-size:12px;color:var(--text-muted);margin:2px 0;">VS</div><div class="bracket-team ${rc} ${youthBracketState.final.w===youthBracketState.final.t2?'winner':''}" onclick="setYouthWinner('final',0,'${youthBracketState.final.t2}')">${youthBracketState.final.t2||'等待'}</div></div><div class="bracket-match"><h4 style="margin:0 0 4px;color:var(--text-muted);">季軍戰</h4><div class="bracket-team ${rc} ${youthBracketState.third.w===youthBracketState.third.t1?'winner':''}" onclick="setYouthWinner('third',0,'${youthBracketState.third.t1}')">${youthBracketState.third.t1||'等待'}</div><div style="text-align:center;font-size:12px;color:var(--text-muted);margin:2px 0;">VS</div><div class="bracket-team ${rc} ${youthBracketState.third.w===youthBracketState.third.t2?'winner':''}" onclick="setYouthWinner('third',0,'${youthBracketState.third.t2}')">${youthBracketState.third.t2||'等待'}</div></div></div>`;
        document.getElementById('youthBracketContainer').innerHTML = html;
    }

    function renderKidsBracket() {
        let rc = isAdmin ? '' : 'readonly';
        let html = `<div class="bracket-round"><h3>四強</h3>` + kidsBracketState.sf.map((m,i)=>`<div class="bracket-match" style="margin:20px 0;"><div class="bracket-team ${rc} ${kidsBracketState.sf[i].w===m.t1?'winner':''}" onclick="setKidsWinner('sf',${i},'${m.t1}')">${m.t1||'等待'}</div><div style="text-align:center;font-size:12px;color:var(--text-muted);margin:2px 0;">VS</div><div class="bracket-team ${rc} ${kidsBracketState.sf[i].w===m.t2?'winner':''}" onclick="setKidsWinner('sf',${i},'${m.t2}')">${m.t2||'等待'}</div></div>`).join('') + `</div>`;
        html += `<div class="bracket-round"><h3>決賽</h3><div class="bracket-match"><h4 style="margin:0 0 4px;color:var(--primary);">冠軍戰</h4><div class="bracket-team ${rc} ${kidsBracketState.final.w===kidsBracketState.final.t1?'winner':''}" onclick="setYouthWinner('final',0,'${kidsBracketState.final.t1}')">${kidsBracketState.final.t1||'等待'}</div><div style="text-align:center;font-size:12px;color:var(--text-muted);margin:2px 0;">VS</div><div class="bracket-team ${rc} ${kidsBracketState.final.w===kidsBracketState.final.t2?'winner':''}" onclick="setYouthWinner('final',0,'${kidsBracketState.final.t2}')">${kidsBracketState.final.t2||'等待'}</div></div><div class="bracket-match"><h4 style="margin:0 0 4px;color:var(--text-muted);">季軍戰</h4><div class="bracket-team ${rc} ${kidsBracketState.third.w===kidsBracketState.third.t1?'winner':''}" onclick="setYouthWinner('third',0,'${kidsBracketState.third.t1}')">${kidsBracketState.third.t1||'等待'}</div><div style="text-align:center;font-size:12px;color:var(--text-muted);margin:2px 0;">VS</div><div class="bracket-team ${rc} ${kidsBracketState.third.w===kidsBracketState.third.t2?'winner':''}" onclick="setYouthWinner('third',0,'${kidsBracketState.third.t2}')">${kidsBracketState.third.t2||'等待'}</div></div></div>`;
        document.getElementById('kidsBracketContainer').innerHTML = html;
    }
</script>

</body>
</html>
