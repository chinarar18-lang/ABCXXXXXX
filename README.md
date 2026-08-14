<!DOCTYPE html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>🌀 陀螺比賽雲端實時管理系統</title>
    <style>
        :root {
            --primary: #2563eb;
            --primary-hover: #1d4ed8;
            --bg-color: #f8fafc;
            --card-bg: #ffffff;
            --text-color: #1e293b;
            --border-color: #e2e8f0;
            --success: #10b981;
            --winner-bg: #d1fae5;
            --readonly-bg: #f1f5f9;
        }
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; background-color: var(--bg-color); color: var(--text-color); margin: 0; padding: 20px; }
        .container { max-width: 1300px; margin: 0 auto; }
        h1, h2, h3 { color: var(--primary); }
        .card { background: var(--card-bg); border-radius: 8px; padding: 20px; margin-bottom: 20px; box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1); }
        button { background-color: var(--primary); color: white; border: none; padding: 10px 16px; border-radius: 6px; cursor: pointer; font-size: 14px; font-weight: bold; transition: background 0.2s; margin-top: 10px; margin-right: 5px; }
        button:hover { background-color: var(--primary-hover); }
        .btn-success { background-color: var(--success); }
        .btn-success:hover { background-color: #059669; }
        input[type="number"] { width: 50px; padding: 8px; border: 1px solid var(--border-color); border-radius: 4px; text-align: center; }
        input[type="number"]:disabled, textarea:disabled { background-color: var(--readonly-bg); color: #64748b; cursor: not-allowed; }
        textarea { width: 100%; height: 120px; padding: 10px; border: 1px solid var(--border-color); border-radius: 4px; font-size: 14px; box-sizing: border-box; resize: vertical; }
        table { width: 100%; border-collapse: collapse; margin-top: 10px; margin-bottom: 20px; }
        th, td { border: 1px solid var(--border-color); padding: 8px; text-align: center; font-size: 14px; }
        th { background-color: #f1f5f9; }
        .tab-buttons { display: flex; gap: 10px; margin-bottom: 20px; flex-wrap: wrap; }
        .tab-btn { background-color: #cbd5e1; color: #334155; margin-top: 0; }
        .tab-btn.active { background-color: var(--primary); color: white; }
        .section { display: none; }
        .section.active { display: block; }
        .grid-2 { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
        .match-row { display: flex; align-items: center; justify-content: center; gap: 10px; margin-bottom: 10px; padding: 10px; background-color: #f8fafc; border-radius: 6px; }
        .team-name { width: 120px; text-align: right; font-weight: bold; }
        .team-name.right { text-align: left; }
        .bracket-container { display: flex; justify-content: space-between; gap: 20px; overflow-x: auto; padding: 10px 0; }
        .bracket-round { display: flex; flex-direction: column; justify-content: space-around; flex: 1; min-width: 220px; }
        .bracket-match { background: #f8fafc; border: 1px solid var(--border-color); border-radius: 6px; padding: 10px; margin: 10px 0; }
        .bracket-team { padding: 8px; margin: 4px 0; background: white; border: 1px solid var(--border-color); border-radius: 4px; cursor: pointer; text-align: center; font-weight: bold; }
        .bracket-team.readonly { cursor: default; }
        .bracket-team:not(.readonly):hover { border-color: var(--primary); background-color: #eff6ff; }
        .bracket-team.winner { background-color: var(--winner-bg); border-color: var(--success); color: #065f46; }
        .admin-bar { background: #e2e8f0; padding: 10px 20px; border-radius: 8px; margin-bottom: 20px; display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 10px; }
        .mode-badge { padding: 4px 10px; border-radius: 4px; font-weight: bold; font-size: 13px; }
        .mode-view { background-color: #cbd5e1; color: #334155; }
        .mode-edit { background-color: #10b981; color: white; }
        @media (max-width: 768px) { .grid-2 { grid-template-columns: 1fr; } }
    </style>
</head>
<body>

<div class="container">
    <h1>🌀 陀螺比賽雲端實時管理系統</h1>
    
    <div class="admin-bar">
        <div>
            <span>目前狀態：</span>
            <span id="modeBadge" class="mode-badge mode-view">🔍 檢視模式 (唯讀)</span>
        </div>
        <div id="adminControlArea">
            <input type="password" id="adminPassword" placeholder="輸入管理員密碼" style="padding: 6px; width: 130px;">
            <button onclick="tryUnlock()">解鎖編輯模式</button>
        </div>
        <div id="editControlArea" style="display:none;">
            <span style="color: #047857; font-weight: bold; margin-right: 10px;">✨ 編輯模式已解鎖</span>
            <button class="btn-success" onclick="syncToCloud()">☁️ 立即同步更新至雲端</button>
            <button onclick="lockAdmin()" style="background-color: #64748b;">鎖定為檢視模式</button>
        </div>
    </div>

    <div class="tab-buttons">
        <button class="tab-btn active" onclick="switchTab('setup')">1. 隊伍名單與抽籤</button>
        <button class="tab-btn" onclick="switchTab('groupStage')">2. 小組賽積分榜</button>
        <button class="tab-btn" onclick="switchTab('schedule')">3. 小組賽盤次分配</button>
        <button class="tab-btn" onclick="switchTab('youthKnockout')">4. 青年組走線圖</button>
        <button class="tab-btn" onclick="switchTab('kidsKnockout')">5. 兒童組走線圖</button>
        <button class="tab-btn" onclick="switchTab('knockoutSchedule')">6. 淘汰賽盤次分配</button>
    </div>

    <div id="setup" class="section active">
        <div class="card">
            <h2>組別與抽籤設定</h2>
            <div class="grid-2">
                <div>
                    <h3>青年組 (分3組 A,B,C)</h3>
                    <textarea id="youthTeamsInput" class="edit-locked" disabled placeholder="每行一隊..."></textarea>
                    <button class="edit-locked" disabled onclick="drawYouth()">進行青年組抽籤</button>
                </div>
                <div>
                    <h3>兒童組 (分2組 D,E)</h3>
                    <textarea id="kidsTeamsInput" class="edit-locked" disabled placeholder="每行一隊..."></textarea>
                    <button class="edit-locked" disabled onclick="drawKids()">進行兒童組抽籤</button>
                </div>
            </div>
        </div>
        <div class="card">
            <h2>抽籤結果預覽</h2>
            <div class="grid-2">
                <div><h3>青年組</h3><div id="youthGroupsResult">尚未抽籤</div></div>
                <div><h3>兒童組</h3><div id="kidsGroupsResult">尚未抽籤</div></div>
            </div>
        </div>
    </div>

    <div id="groupStage" class="section">
        <div class="card">
            <h2>小組賽成績與即時積分榜</h2>
            <div class="tab-buttons">
                <button class="tab-btn active" onclick="switchGroupTab('youthGroupView')">青年組</button>
                <button class="tab-btn" onclick="switchGroupTab('kidsGroupView')">兒童組</button>
            </div>
            <div id="youthGroupView" class="group-sub-section"><div id="youthContentContainer">請先完成抽籤。</div></div>
            <div id="kidsGroupView" class="group-sub-section" style="display:none;"><div id="kidsContentContainer">請先完成抽籤。</div></div>
        </div>
    </div>

    <div id="schedule" class="section">
        <div class="card">
            <h2>小組賽盤次分配表 (4個盤)</h2>
            <button class="edit-locked" disabled onclick="generateSchedule()">生成小組賽盤次</button>
            <div id="scheduleContainer" style="margin-top: 20px;">請先抽籤。</div>
        </div>
    </div>

    <div id="youthKnockout" class="section">
        <div class="card">
            <h2>青年組八強走線圖</h2>
            <p>點擊按鈕後，系統會自動把小組賽出線的真實隊伍名稱填入八強位置。</p>
            <button class="edit-locked" disabled onclick="initYouthBracket()">生成/更新青年組八強對陣</button>
            <div class="bracket-container" id="youthBracketContainer" style="margin-top: 20px;">請先完成小組賽。</div>
        </div>
    </div>

    <div id="kidsKnockout" class="section">
        <div class="card">
            <h2>兒童組四強走線圖</h2>
            <p>點擊按鈕後，系統會自動把小組賽出線的真實隊伍名稱填入四強位置。</p>
            <button class="edit-locked" disabled onclick="initKidsBracket()">生成/更新兒童組四強對陣</button>
            <div class="bracket-container" id="kidsBracketContainer" style="margin-top: 20px;">請先完成小組賽。</div>
        </div>
    </div>

    <div id="knockoutSchedule" class="section">
        <div class="card">
            <h2>淘汰賽盤次分配 (4個盤)</h2>
            <button class="edit-locked" disabled onclick="generateKoSchedule()">生成/更新淘汰賽盤次</button>
            <div id="koScheduleContainer" style="margin-top: 20px;">請先在走線圖分頁生成對陣。</div>
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

    window.onload = function() {
        fetchFromCloud();
    };

    function fetchFromCloud() {
        fetch(WEB_APP_URL)
            .then(res => res.json())
            .then(data => {
                if(data && data.length > 0) {
                    try {
                        let cloudData = JSON.parse(data[0][0]);
                        state = cloudData.state;
                        youthBracketState = cloudData.youthBracketState;
                        kidsBracketState = cloudData.kidsBracketState;
                        updateUI();
                    } catch(e) {}
                }
            })
            .catch(err => console.log('從雲端同步數據中...'));
    }

    function syncToCloud() {
        if(!isAdmin) return;
        let packageData = [[JSON.stringify({ state, youthBracketState, kidsBracketState })]];
        
        fetch(WEB_APP_URL, {
            method: 'POST',
            mode: 'no-cors',
            body: JSON.stringify(packageData)
        })
        .then(() => {
            alert('☁️ 同步指令已發送至雲端試算表！網頁將於 2 秒後重整。');
            setTimeout(() => window.location.reload(), 2000);
        })
        .catch(err => alert('同步失敗，請檢查網絡連線。'));
    }

    function tryUnlock() {
        if(document.getElementById('adminPassword').value === ADMIN_PASSWORD) {
            isAdmin = true;
            document.getElementById('adminControlArea').style.display = 'none';
            document.getElementById('editControlArea').style.display = 'block';
            document.getElementById('modeBadge').innerHTML = '✨ 編輯模式 (已解鎖)';
            document.getElementById('modeBadge').className = 'mode-badge mode-edit';
            document.querySelectorAll('.edit-locked').forEach(el => el.disabled = false);
            updateUI();
            alert('🔓 成功解鎖編輯模式！');
        } else { alert('❌ 密碼錯誤！'); }
    }

    function lockAdmin() {
        isAdmin = false;
        document.getElementById('adminControlArea').style.display = 'block';
        document.getElementById('editControlArea').style.display = 'none';
        document.getElementById('adminPassword').value = '';
        document.getElementById('modeBadge').innerHTML = '🔍 檢視模式 (唯讀)';
        document.getElementById('modeBadge').className = 'mode-badge mode-view';
        document.querySelectorAll('.edit-locked').forEach(el => el.disabled = true);
        updateUI();
        alert('🔒 已鎖定回檢視模式。');
    }

    function updateUI() {
        document.getElementById('youthTeamsInput').value = state.youthTeamsInputText || '';
        document.getElementById('kidsTeamsInput').value = state.kidsTeamsInputText || '';
        renderYouthGroupsResult();
        renderKidsGroupsResult();
        renderGroupView('youth');
        renderGroupView('kids');
        renderYouthBracket();
        renderKidsBracket();
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
        let currentIndex = array.length, randomIndex;
        while (currentIndex != 0) {
            randomIndex = Math.floor(Math.random() * currentIndex);
            currentIndex--;
            [array[currentIndex], array[randomIndex]] = [array[randomIndex], array[currentIndex]];
        }
        return array;
    }
    function generateRoundRobin(teams, groupId, matchObj) {
        matchObj[groupId] = [];
        let matchId = 1;
        for (let i = 0; i < teams.length; i++) {
            for (let j = i + 1; j < teams.length; j++) {
                matchObj[groupId].push({ id: `${groupId}-${matchId++}`, t1: teams[i], t2: teams[j], s1: null, s2: null });
            }
        }
    }
    function drawYouth() {
        if(!isAdmin) return;
        let input = document.getElementById('youthTeamsInput').value.trim();
        state.youthTeamsInputText = input;
        let teams = input.split('\n').map(t => t.trim()).filter(t => t);
        if(teams.length < 4) { alert('青年組最少需要輸入 4 隊！'); return; }
        teams = shuffle(teams);
        state.youthGroups = { 'A': [], 'B': [], 'C': [] };
        let groups = ['A', 'B', 'C'];
        teams.forEach((team, i) => { state.youthGroups[groups[i % 3]].push(team); });
        
        ['A', 'B', 'C'].forEach(g => {
            generateRoundRobin(state.youthGroups[g], g, state.youthMatches);
        });

        renderYouthGroupsResult();
        renderGroupView('youth');
        alert('青年組抽籤完成！');
    }
    function renderYouthGroupsResult() {
        let html = '';
        ['A', 'B', 'C'].forEach(g => {
            html += `<h4>組別 ${g}:</h4><ul>` + (state.youthGroups[g] || []).map(t => `<li>${t}</li>`).join('') + `</ul>`;
        });
        document.getElementById('youthGroupsResult').innerHTML = html;
    }
    function drawKids() {
        if(!isAdmin) return;
        let input = document.getElementById('kidsTeamsInput').value.trim();
        state.kidsTeamsInputText = input;
        let teams = input.split('\n').map(t => t.trim()).filter(t => t);
        if(teams.length < 3) { alert('兒童組最少需要輸入 3 隊！'); return; }
        teams = shuffle(teams);
        state.kidsGroups = { 'D': [], 'E': [] };
        let groups = ['D', 'E'];
        teams.forEach((team, i) => { state.kidsGroups[groups[i % 2]].push(team); });

        ['D', 'E'].forEach(g => {
            generateRoundRobin(state.kidsGroups[g], g, state.kidsMatches);
        });

        renderKidsGroupsResult();
        renderGroupView('kids');
        alert('兒童組抽籤完成！');
    }
    function renderKidsGroupsResult() {
        let html = '';
        ['D', 'E'].forEach(g => {
            html += `<h4>組別 ${g}:</h4><ul>` + (state.kidsGroups[g] || []).map(t => `<li>${t}</li>`).join('') + `</ul>`;
        });
        document.getElementById('kidsGroupsResult').innerHTML = html;
    }
    function updateScore(type, groupId, matchId, tIndex, value) {
        if(!isAdmin) return;
        let matches = type === 'youth' ? state.youthMatches[groupId] : state.kidsMatches[groupId];
        let match = matches.find(m => m.id === matchId);
        if(match) {
            if(tIndex === 1) match.s1 = value !== "" ? parseInt(value) : null;
            if(tIndex === 2) match.s2 = value !== "" ? parseInt(value) : null;
        }
        renderGroupView(type);
    }
    function calculateStandings(groups, matches) {
        let standings = {};
        for(let g in groups) {
            standings[g] = groups[g].map(team => ({ name: team, played: 0, w3: 0, w2: 0, l1: 0, l0: 0, pts: 0 }));
            if(!matches[g]) continue;
            matches[g].forEach(m => {
                if(m.s1 !== null && m.s2 !== null) {
                    let t1 = standings[g].find(t => t.name === m.t1);
                    let t2 = standings[g].find(t => t.name === m.t2);
                    if(t1 && t2) {
                        t1.played++; t2.played++;
                        if(m.s1 === 3 && m.s2 === 0) { t1.w3++; t1.pts += 3; t2.l0++; t2.pts += 0; }
                        else if(m.s1 === 2 && m.s2 === 1) { t1.w2++; t1.pts += 2; t2.l1++; t2.pts += 1; }
                        else if(m.s1 === 1 && m.s2 === 2) { t2.w2++; t2.pts += 2; t1.l1++; t1.pts += 1; }
                        else if(m.s1 === 0 && m.s2 === 3) { t2.w3++; t2.pts += 3; t1.l0++; t1.pts += 0; }
                    }
                }
            });
            standings[g].sort((a, b) => {
                if (b.pts !== a.pts) return b.pts - a.pts;
                let h2h = matches[g].find(m => (m.t1 === a.name && m.t2 === b.name) || (m.t1 === b.name && m.t2 === a.name));
                if (h2h && h2h.s1 !== null && h2h.s2 !== null) {
                    if (h2h.t1 === a.name) return h2h.s2 - h2h.s1;
                    if (h2h.t1 === b.name) return h2h.s1 - h2h.s2;
                }
                return (b.w3 + b.w2) - (a.w3 + a.w2);
            });
        }
        return standings;
    }
    function renderGroupView(type) {
        let groups = type === 'youth' ? state.youthGroups : state.kidsGroups;
        let matches = type === 'youth' ? state.youthMatches : state.kidsMatches;
        let standings = calculateStandings(groups, matches);
        let container = document.getElementById(type === 'youth' ? 'youthContentContainer' : 'kidsContentContainer');
        if(!Object.keys(groups)[0] || groups[Object.keys(groups)[0]].length === 0) return;
        let html = '';
        for(let g in groups) {
            html += `<h3>組別 ${g}</h3><div class="grid-2">`;
            html += `<div><h4>積分榜</h4><table><tr><th>排名</th><th>隊伍</th><th>賽</th><th>勝(3)</th><th>勝(2)</th><th>負(1)</th><th>負(0)</th><th>積分</th></tr>`;
            if(standings[g]) {
                standings[g].forEach((t, i) => {
                    html += `<tr><td>${i+1}</td><td><b>${t.name}</b></td><td>${t.played}</td><td>${t.w3}</td><td>${t.w2}</td><td>${t.l1}</td><td>${t.l0}</td><td><b>${t.pts}</b></td></tr>`;
                });
            }
            html += `</table></div><div><h4>輸入對賽成績 (三盤兩勝)</h4>`;
            if(matches[g]) {
                matches[g].forEach(m => {
                    let disabledAttr = isAdmin ? '' : 'disabled';
                    html += `<div class="match-row">
                        <span class="team-name">${m.t1}</span>
                        <input type="number" min="0" max="3" ${disabledAttr} value="${m.s1 !== null ? m.s1 : ''}" onchange="updateScore('${type}', '${g}', '${m.id}', 1, this.value)">
                        <span>:</span>
                        <input type="number" min="0" max="3" ${disabledAttr} value="${m.s2 !== null ? m.s2 : ''}" onchange="updateScore('${type}', '${g}', '${m.id}', 2, this.value)">
                        <span class="team-name right">${m.t2}</span>
                    </div>`;
                });
            }
            html += `</div></div><hr>`;
        }
        container.innerHTML = html;
    }
    function generateSchedule() {
        if(!isAdmin) return;
        let allMatches = [];
        for(let g in state.youthMatches) { state.youthMatches[g].forEach(m => allMatches.push({ g: g, ...m, type: '青年' })); }
        for(let g in state.kidsMatches) { state.kidsMatches[g].forEach(m => allMatches.push({ g: g, ...m, type: '兒童' })); }
        if(allMatches.length === 0) { alert('請先完成抽籤！'); return; }
        let tables = [ [], [], [], [] ];
        allMatches.forEach((m, i) => { tables[i % 4].push(m); });
        let html = '<div class="grid-2">';
        tables.forEach((tMatches, i) => {
            html += `<div><h3>盤 ${i+1} 賽程</h3><ul>`;
            tMatches.forEach((m, j) => { html += `<li>場次 ${j+1}: [${m.type}組 ${m.g}] ${m.t1} vs ${m.t2}</li>`; });
            html += `</ul></div>`;
        });
        html += '</div>';
        document.getElementById('scheduleContainer').innerHTML = html;
    }

    function generateKoSchedule() {
        let koMatches = [];
        youthBracketState.qf.forEach((m) => { if(m.t1 && m.t2) koMatches.push({ stage: '青年八強', t1: m.t1, t2: m.t2 }); });
        youthBracketState.sf.forEach((m) => { if(m.t1 && m.t2) koMatches.push({ stage: '青年四強', t1: m.t1, t2: m.t2 }); });
        if(youthBracketState.final.t1 && youthBracketState.final.t2) koMatches.push({ stage: '青年決賽', t1: youthBracketState.final.t1, t2: youthBracketState.final.t2 });
        if(youthBracketState.third.t1 && youthBracketState.third.t2) koMatches.push({ stage: '青年季軍戰', t1: youthBracketState.third.t1, t2: youthBracketState.third.t2 });

        kidsBracketState.sf.forEach((m) => { if(m.t1 && m.t2) koMatches.push({ stage: '兒童四強', t1: m.t1, t2: m.t2 }); });
        if(kidsBracketState.final.t1 && kidsBracketState.final.t2) koMatches.push({ stage: '兒童決賽', t1: kidsBracketState.final.t1, t2: kidsBracketState.final.t2 });
        if(kidsBracketState.third.t1 && kidsBracketState.third.t2) koMatches.push({ stage: '兒童季軍戰', t1: kidsBracketState.third.t1, t2: kidsBracketState.third.t2 });

        if(koMatches.length === 0) { alert('暫未生成淘汰賽對陣，請先在走線圖分頁初始化對陣！'); return; }

        let tables = [ [], [], [], [] ];
        koMatches.forEach((m, i) => { tables[i % 4].push(m); });

        let html = '<div class="grid-2">';
        tables.forEach((tMatches, i) => {
            html += `<div><h3>盤 ${i+1} 淘汰賽程</h3><ul>`;
            tMatches.forEach((m) => { html += `<li><b>[${m.stage}]</b> ${m.t1} vs ${m.t2}</li>`; });
            html += `</ul></div>`;
        });
        html += '</div>';
        document.getElementById('koScheduleContainer').innerHTML = html;
    }

    // 自動從真實積分榜提取隊伍進入八強/四強
    function initYouthBracket() {
        if(!isAdmin) return;
        let yStandings = calculateStandings(state.youthGroups, state.youthMatches);
        if(!yStandings['A'] || !yStandings['A'][0]?.name) { alert('請先完成青年組抽籤和小組賽積分！'); return; }

        let t = {
            A1: yStandings['A'][0]?.name || 'A1', A2: yStandings['A'][1]?.name || 'A2',
            B1: yStandings['B'][0]?.name || 'B1', B2: yStandings['B'][1]?.name || 'B2',
            C1: yStandings['C'][0]?.name || 'C1', C2: yStandings['C'][1]?.name || 'C2'
        };

        let thirdList = [
            { group: 'A', ...yStandings['A'][2] },
            { group: 'B', ...yStandings['B'][2] },
            { group: 'C', ...yStandings['C'][2] }
        ].filter(x => x && x.name).sort((a, b) => b.pts - a.pts);

        let T1 = thirdList[0]?.name || '最佳第三名1';
        let T2 = thirdList[1]?.name || '最佳第三名2';

        youthBracketState.qf = [
            { t1: t.A1, t2: T1, w: null },
            { t1: t.B2, t2: t.C2, w: null },
            { t1: t.B1, t2: T2, w: null },
            { t1: t.C1, t2: t.A2, w: null }
        ];
        youthBracketState.sf = [{t1: '', t2: '', w: null}, {t1: '', t2: '', w: null}];
        youthBracketState.final = {t1: '', t2: '', w: null};
        youthBracketState.third = {t1: '', t2: '', w: null};
        renderYouthBracket();
        alert('青年組八強對陣已自動提取真實隊伍名稱生成！');
    }

    function initKidsBracket() {
        if(!isAdmin) return;
        let kStandings = calculateStandings(state.kidsGroups, state.kidsMatches);
        if(!kStandings['D'] || !kStandings['D'][0]?.name) { alert('請先完成兒童組抽籤和小組賽積分！'); return; }

        let d1 = kStandings['D'][0]?.name || 'D1';
        let d2 = kStandings['D'][1]?.name || 'D2';
        let e1 = kStandings['E'][0]?.name || 'E1';
        let e2 = kStandings['E'][1]?.name || 'E2';

        kidsBracketState.sf = [
            { t1: d1, t2: e2, w: null },
            { t1: e1, t2: d2, w: null }
        ];
        kidsBracketState.final = { t1: '', t2: '', w: null };
        kidsBracketState.third = { t1: '', t2: '', w: null };
        renderKidsBracket();
        alert('兒童組四強對陣已自動提取真實隊伍名稱生成！');
    }

    function setYouthWinner(round, matchIndex, teamName) {
        if(!isAdmin) return;
        if(!teamName || teamName.includes('等待') || teamName.includes('最佳')) return;
        if(round === 'qf') {
            youthBracketState.qf[matchIndex].w = teamName;
            if(matchIndex === 0 || matchIndex === 1) { youthBracketState.sf[0][matchIndex === 0 ? 't1' : 't2'] = teamName; }
            else { youthBracketState.sf[1][matchIndex === 2 ? 't1' : 't2'] = teamName; }
        } else if(round === 'sf') {
            youthBracketState.sf[matchIndex].w = teamName;
            if(matchIndex === 0) youthBracketState.final.t1 = teamName;
            if(matchIndex === 1) youthBracketState.final.t2 = teamName;
            let loser = (youthBracketState.sf[matchIndex].t1 === teamName) ? youthBracketState.sf[matchIndex].t2 : youthBracketState.sf[matchIndex].t1;
            if(matchIndex === 0) youthBracketState.third.t1 = loser;
            if(matchIndex === 1) youthBracketState.third.t2 = loser;
        } else if(round === 'final') { youthBracketState.final.w = teamName; }
        else if(round === 'third') { youthBracketState.third.w = teamName; }
        renderYouthBracket();
    }

    function setKidsWinner(round, matchIndex, teamName) {
        if(!isAdmin) return;
        if(!teamName || teamName.includes('等待')) return;
        if(round === 'sf') {
            kidsBracketState.sf[matchIndex].w = teamName;
            if(matchIndex === 0) kidsBracketState.final.t1 = teamName;
            if(matchIndex === 1) kidsBracketState.final.t2 = teamName;
            let loser = (kidsBracketState.sf[matchIndex].t1 === teamName) ? kidsBracketState.sf[matchIndex].t2 : kidsBracketState.sf[matchIndex].t1;
            if(matchIndex === 0) kidsBracketState.third.t1 = loser;
            if(matchIndex === 1) kidsBracketState.third.t2 = loser;
        } else if(round === 'final') { kidsBracketState.final.w = teamName; }
        else if(round === 'third') { kidsBracketState.third.t1 = teamName; }
        renderKidsBracket();
    }

    function renderYouthBracket() {
        let rc = isAdmin ? '' : 'readonly';
        let html = `
            <div class="bracket-round"><h3>八強賽 (QF)</h3>
                ${youthBracketState.qf.map((m, i) => `
                    <div class="bracket-match">
                        <div class="bracket-team ${rc} ${youthBracketState.qf[i].w === m.t1 ? 'winner' : ''}" onclick="setYouthWinner('qf', ${i}, '${m.t1}')">${m.t1 || '等待晉級'}</div>
                        <div style="text-align:center; font-size:12px; color:#64748b; margin:2px 0;">VS</div>
                        <div class="bracket-team ${rc} ${youthBracketState.qf[i].w === m.t2 ? 'winner' : ''}" onclick="setYouthWinner('qf', ${i}, '${m.t2}')">${m.t2 || '等待晉級'}</div>
                    </div>`).join('')}</div>
            <div class="bracket-round"><h3>四強賽 (SF)</h3>
                ${youthBracketState.sf.map((m, i) => `
                    <div class="bracket-match" style="margin: 40px 0;">
                        <div class="bracket-team ${rc} ${youthBracketState.sf[i].w === m.t1 ? 'winner' : ''}" onclick="setYouthWinner('sf', ${i}, '${m.t1}')">${m.t1 || '等待勝方'}</div>
                        <div style="text-align:center; font-size:12px; color:#64748b; margin:2px 0;">VS</div>
                        <div class="bracket-team ${rc} ${youthBracketState.sf[i].w === m.t2 ? 'winner' : ''}" onclick="setYouthWinner('sf', ${i}, '${m.t2}')">${m.t2 || '等待勝方'}</div>
                    </div>`).join('')}</div>
            <div class="bracket-round"><h3>決賽 & 季軍戰</h3>
                <div class="bracket-match" style="border-color: var(--primary);">
                    <h4 style="margin:0 0 5px 0; color:var(--primary);">冠軍戰 (5盤3勝)</h4>
                    <div class="bracket-team ${rc} ${youthBracketState.final.w === youthBracketState.final.t1 ? 'winner' : ''}" onclick="setYouthWinner('final', 0, '${youthBracketState.final.t1}')">${youthBracketState.final.t1 || '等待勝方'}</div>
                    <div style="text-align:center; font-size:12px; color:#64748b; margin:2px 0;">VS</div>
                    <div class="bracket-team ${rc} ${youthBracketState.final.w === youthBracketState.final.t2 ? 'winner' : ''}" onclick="setYouthWinner('final', 0, '${youthBracketState.final.t2}')">${youthBracketState.final.t2 || '等待勝方'}</div>
                </div>
                <div class="bracket-match">
                    <h4 style="margin:0 0 5px 0; color:#64748b;">季軍戰</h4>
                    <div class="bracket-team ${rc} ${youthBracketState.third.w === youthBracketState.third.t1 ? 'winner' : ''}" onclick="setYouthWinner('third', 0, '${youthBracketState.third.t1}')">${youthBracketState.third.t1 || '等待敗方'}</div>
                    <div style="text-align:center; font-size:12px; color:#64748b; margin:2px 0;">VS</div>
                    <div class="bracket-team ${rc} ${youthBracketState.third.w === youthBracketState.third.t2 ? 'winner' : ''}" onclick="setYouthWinner('third', 0, '${youthBracketState.third.t2}')">${youthBracketState.third.t2 || '等待敗方'}</div>
                </div></div>`;
        document.getElementById('youthBracketContainer').innerHTML = html;
    }

    function renderKidsBracket() {
        let rc = isAdmin ? '' : 'readonly';
        let html = `
            <div class="bracket-round"><h3>四強賽 (SF)</h3>
                ${kidsBracketState.sf.map((m, i) => `
                    <div class="bracket-match" style="margin: 20px 0;">
                        <div class="bracket-team ${rc} ${kidsBracketState.sf[i].w === m.t1 ? 'winner' : ''}" onclick="setKidsWinner('sf', ${i}, '${m.t1}')">${m.t1 || '等待晉級'}</div>
                        <div style="text-align:center; font-size:12px; color:#64748b; margin:2px 0;">VS</div>
                        <div class="bracket-team ${rc} ${kidsBracketState.sf[i].w === m.t2 ? 'winner' : ''}" onclick="setKidsWinner('sf', ${i}, '${m.t2}')">${m.t2 || '等待晉級'}</div>
                    </div>`).join('')}</div>
            <div class="bracket-round"><h3>決賽 & 季軍戰</h3>
                <div class="bracket-match" style="border-color: var(--primary);">
                    <h4 style="margin:0 0 5px 0; color:var(--primary);">冠軍戰 (5盤3勝)</h4>
                    <div class="bracket-team ${rc} ${kidsBracketState.final.w === kidsBracketState.final.t1 ? 'winner' : ''}" onclick="setKidsWinner('final', 0, '${kidsBracketState.final.t1}')">${kidsBracketState.final.t1 || '等待勝方'}</div>
                    <div style="text-align:center; font-size:12px; color:#64748b; margin:2px 0;">VS</div>
                    <div class="bracket-team ${rc} ${kidsBracketState.final.w === kidsBracketState.final.t2 ? 'winner' : ''}" onclick="setKidsWinner('final', 0, '${kidsBracketState.final.t2}')">${kidsBracketState.final.t2 || '等待勝方'}</div>
                </div>
                <div class="bracket-match">
                    <h4 style="margin:0 0 5px 0; color:#64748b;">季軍戰</h4>
                    <div class="bracket-team ${rc} ${kidsBracketState.third.w === kidsBracketState.third.t1 ? 'winner' : ''}" onclick="setKidsWinner('third', 0, '${kidsBracketState.third.t1}')">${kidsBracketState.third.t1 || '等待敗方'}</div>
                    <div style="text-align:center; font-size:12px; color:#64748b; margin:2px 0;">VS</div>
                    <div class="bracket-team ${rc} ${kidsBracketState.third.w === kidsBracketState.third.t2 ? 'winner' : ''}" onclick="setKidsWinner('third', 0, '${kidsBracketState.third.t2}')">${kidsBracketState.third.t2 || '等待敗方'}</div>
                </div></div>`;
        document.getElementById('kidsBracketContainer').innerHTML = html;
    }
</script>

</body>
</html>
