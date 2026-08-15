<!DOCTYPE html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>🌀 陀螺比賽雲端實時管理系統</title>
    <link href="https://fonts.googleapis.com/css2?family=ZCOOL+KuaiLe&family=M+PLUS+Rounded+1c:wght@400;700&display=swap" rel="stylesheet">
    <style>
        :root { 
            --primary: #3b82f6; 
            --primary-dark: #1e3a8a; 
            --primary-light: #60a5fa;
            --accent: #f43f5e;
            --bg-color: #0f172a; 
            --card-bg: #1e293b; 
            --text-main: #f8fafc; 
            --text-muted: #94a3b8; 
            --border: #334155; 
            --success: #10b981; 
            --success-bg: #064e3b; 
            --radius: 16px; 
        }

        body { 
            font-family: 'M PLUS Rounded 1c', 'ZCOOL KuaiLe', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; 
            background-color: var(--bg-color); 
            color: var(--text-main); 
            margin: 0; 
            padding: 20px; 
            letter-spacing: 0.5px;
        }

        .container { max-width: 1200px; margin: 0 auto; }

        .header-box { 
            display: flex; 
            justify-content: space-between; 
            align-items: center; 
            background: linear-gradient(135deg, #1e3a8a, #1e293b); 
            padding: 24px 32px; 
            border-radius: var(--radius); 
            box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.3); 
            margin-bottom: 24px; 
            border: 1px solid var(--border);
        }

        h1, h2, h3, h4 { 
            font-family: 'ZCOOL KuaiLe', 'M PLUS Rounded 1c', sans-serif;
            letter-spacing: 1px;
        }

        h1 { margin: 0; font-size: 28px; color: #93c5fd; text-shadow: 0 2px 4px rgba(0,0,0,0.3); }

        .admin-bar { 
            background: var(--card-bg); 
            padding: 16px 24px; 
            border-radius: var(--radius); 
            box-shadow: 0 4px 12px rgba(0,0,0,0.2); 
            margin-bottom: 24px; 
            display: flex; 
            justify-content: space-between; 
            align-items: center; 
            border: 1px solid var(--border);
        }

        .mode-badge { padding: 6px 14px; border-radius: 20px; font-weight: 600; font-size: 13px; }
        .mode-view { background-color: #334155; color: var(--text-muted); }
        .mode-edit { background-color: var(--success-bg); color: #34d399; border: 1px solid var(--success); }

        .card { 
            background: var(--card-bg); 
            border-radius: var(--radius); 
            padding: 28px; 
            margin-bottom: 24px; 
            box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.2); 
            border: 1px solid var(--border);
        }

        .tab-buttons { display: flex; gap: 10px; margin-bottom: 24px; flex-wrap: wrap; }

        .tab-btn { 
            background-color: #334155; 
            color: var(--text-muted); 
            border: none; 
            padding: 12px 20px; 
            border-radius: 12px; 
            cursor: pointer; 
            font-size: 14px; 
            font-weight: 600; 
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1); 
            font-family: inherit;
        }

        .tab-btn.active { 
            background: linear-gradient(135deg, #3b82f6, #1d4ed8);
            color: white; 
            box-shadow: 0 6px 15px rgba(59, 130, 246, 0.4); 
            transform: translateY(-2px);
        }

        .section { display: none; }
        .section.active { display: block; animation: fadeIn 0.4s ease; }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(6px); }
            to { opacity: 1; transform: translateY(0); }
        }

        button { 
            background: linear-gradient(135deg, #3b82f6, #1d4ed8); 
            color: white; 
            border: none; 
            padding: 10px 22px; 
            border-radius: 10px; 
            cursor: pointer; 
            font-size: 14px; 
            font-weight: 600; 
            transition: all 0.2s; 
            font-family: inherit;
            box-shadow: 0 4px 10px rgba(59, 130, 246, 0.3);
        }
        button:hover { filter: brightness(1.1); transform: translateY(-1px); }

        .btn-success { background: linear-gradient(135deg, #10b981, #047857); box-shadow: 0 4px 10px rgba(16, 185, 129, 0.3); }

        select, input[type="text"], input[type="password"] { 
            background-color: #0f172a;
            color: white;
            padding: 8px 12px; 
            border: 1px solid var(--border); 
            border-radius: 8px; 
            font-size: 15px; 
            font-family: inherit;
        }

        select:disabled, input:disabled, textarea:disabled { background-color: #0b1120; color: var(--text-muted); cursor: not-allowed; }

        textarea { 
            width: 100%; 
            height: 120px; 
            padding: 14px; 
            background-color: #0f172a;
            color: white;
            border: 1px solid var(--border); 
            border-radius: 10px; 
            font-size: 14px; 
            box-sizing: border-box; 
            resize: vertical; 
            font-family: inherit;
        }

        table { width: 100%; border-collapse: separate; border-spacing: 0; margin-top: 12px; margin-bottom: 20px; border-radius: 10px; overflow: hidden; border: 1px solid var(--border); }

        th, td { border-bottom: 1px solid var(--border); border-right: 1px solid var(--border); padding: 12px 14px; text-align: center; font-size: 14px; }
        th:last-child, td:last-child { border-right: none; }
        th { background-color: #0f172a; color: #93c5fd; font-weight: 600; }
        tr:hover td { background-color: rgba(59, 130, 246, 0.05); }

        .grid-2 { display: grid; grid-template-columns: 1fr 1fr; gap: 24px; }
        .grid-3 { display: grid; grid-template-columns: repeat(3, 1fr); gap: 15px; }

        .match-row { display: flex; align-items: center; justify-content: center; gap: 12px; margin-bottom: 12px; padding: 12px; background-color: #0f172a; border-radius: 10px; border: 1px solid var(--border); }
        .team-name { width: 140px; text-align: right; font-weight: 600; color: #e2e8f0; }
        .team-name.right { text-align: left; }

        .bracket-container { display: flex; justify-content: space-between; gap: 16px; overflow-x: auto; padding: 10px 0; }
        .bracket-round { display: flex; flex-direction: column; justify-content: space-around; flex: 1; min-width: 220px; }
        .bracket-match { background: #0f172a; border: 1px solid var(--border); border-radius: 10px; padding: 12px; margin: 8px 0; }
        .bracket-team-box { background: #1e293b; border: 1px solid var(--border); border-radius: 8px; padding: 8px; margin: 4px 0; display: flex; align-items: center; justify-content: space-between; gap: 6px; }
        .bracket-team-name { font-weight: 600; color: #f8fafc; font-size: 14px; flex: 1; text-align: left; }
        .bracket-score-select { width: 60px !important; padding: 4px !important; text-align: center; font-size: 14px !important; }

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
            <input type="password" id="adminPassword" placeholder="管理員密碼" style="padding: 8px 12px; border: 1px solid var(--border); border-radius: 8px; width: 130px;">
            <button onclick="tryUnlock()">解鎖編輯</button>
        </div>
        <div id="editArea" style="display:none;">
            <span style="color: #34d399; font-weight: 600; margin-right: 12px;">✨ 編輯中 (持續有效)</span>
            <button class="btn-success" onclick="syncToCloud()">☁️ 同步所有修改</button>
            <button onclick="lockAdmin()" style="background-color: #475569;">鎖定</button>
        </div>
    </div>

    <div class="tab-buttons">
        <button class="tab-btn active" onclick="switchTab('setup')">1. 隊伍名單與設定</button>
        <button class="tab-btn" onclick="switchTab('groupStage')">2. 聯賽積分榜</button>
        <button class="tab-btn" onclick="switchTab('schedule')">3. 聯賽同時段盤次(4張枱)</button>
        <button class="tab-btn" onclick="switchTab('youthKnockout')">4. 青年組走線圖</button>
        <button class="tab-btn" onclick="switchTab('kidsKnockout')">5. 兒童組走線圖</button>
        <button class="tab-btn" onclick="switchTab('knockoutSchedule')">6. 淘汰賽盤次(3盤)</button>
    </div>

    <!-- 第一頁：設定 -->
    <div id="setup" class="section active">
        <div class="card">
            <h2>聯賽隊伍設定</h2>
            <div class="grid-2">
                <div>
                    <h3>青年組 (單一聯賽，最少4隊)</h3>
                    <textarea id="youthTeamsInput" class="edit-locked" disabled placeholder="請輸入隊伍名稱，每行一隊..."></textarea>
                    <button class="edit-locked" disabled onclick="initYouthLeague()">生成青年組聯賽</button>
                </div>
                <div>
                    <h3>兒童組 (單一聯賽，最少4隊)</h3>
                    <textarea id="kidsTeamsInput" class="edit-locked" disabled placeholder="請輸入隊伍名稱，每行一隊..."></textarea>
                    <button class="edit-locked" disabled onclick="initKidsLeague()">生成兒童組聯賽</button>
                </div>
            </div>
        </div>
    </div>

    <!-- 第二頁：聯賽積分 -->
    <div id="groupStage" class="section">
        <div class="card">
            <h2>聯賽成績與即時積分榜 (前4名晉級四強)</h2>
            <div class="tab-buttons">
                <button class="tab-btn active" onclick="switchGroupTab('youthGroupView')">青年組聯賽</button>
                <button class="tab-btn" onclick="switchGroupTab('kidsGroupView')">兒童組聯賽</button>
            </div>
            <div id="youthGroupView" class="group-sub-section"><div id="youthContentContainer" style="color: var(--text-muted);">請先生成青年組聯賽。</div></div>
            <div id="kidsGroupView" class="group-sub-section" style="display:none;"><div id="kidsContentContainer" style="color: var(--text-muted);">請先生成兒童組聯賽。</div></div>
        </div>
    </div>

    <!-- 第三頁：聯賽盤次 (4個盤同時進行) -->
    <div id="schedule" class="section">
        <div class="card">
            <h2>聯賽同時段盤次分配 (4 張枱同時進行 - 確保同一隊不會同時出賽)</h2>
            <button class="edit-locked" disabled onclick="generateSchedule()">重新整理盤次</button>
            <div id="scheduleContainer" style="margin-top: 16px; color: var(--text-muted);">請先生成聯賽。</div>
        </div>
    </div>

    <!-- 第四頁：青年組走線圖 -->
    <div id="youthKnockout" class="section">
        <div class="card">
            <h2>青年組四強淘汰賽 (聯賽前4名晉級)</h2>
            <button class="edit-locked" disabled onclick="initYouthBracket()">生成青年組四強對陣</button>
            <div class="bracket-container" id="youthBracketContainer" style="margin-top: 16px; color: var(--text-muted);">請先完成聯賽。</div>
        </div>
    </div>

    <!-- 第五頁：兒童組走線圖 -->
    <div id="kidsKnockout" class="section">
        <div class="card">
            <h2>兒童組四強淘汰賽 (聯賽前4名晉級)</h2>
            <button class="edit-locked" disabled onclick="initKidsBracket()">生成兒童組四強對陣</button>
            <div class="bracket-container" id="kidsBracketContainer" style="margin-top: 16px; color: var(--text-muted);">請先完成聯賽。</div>
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
    const ADMIN_PASSWORD = "123"; 
    const WEB_APP_URL = "https://script.google.com/macros/s/AKfycbw3_WKdwd6yUt_oRibRIL62jjboDkapre7Wwx4Pcfet5rs0CCfhyCfSqkCchubThhvl/exec"; 

    let isAdmin = false;
    let state = { youthTeamsInputText: '', kidsTeamsInputText: '', youthTeams: [], kidsTeams: [], youthMatches: [], kidsMatches: [] };
    let youthBracketState = { sf: Array(2).fill({t1:'', t2:'', s1:'', s2:'', w:''}), final: {t1:'', t2:'', s1:'', s2:'', w:''}, third: {t1:'', t2:'', s1:'', s2:'', w:''} };
    let kidsBracketState = { sf: Array(2).fill({t1:'', t2:'', s1:'', s2:'', w:''}), final: {t1:'', t2:'', s1:'', s2:'', w:''}, third: {t1:'', t2:'', s1:'', s2:'', w:''} };

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
            alert('已成功解鎖！');
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
        renderLeagueView('youth');
        renderLeagueView('kids');
        renderYouthBracket();
        renderKidsBracket();
        generateSchedule();
        generateKoSchedule();
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

    function generateRoundRobin(teams, matchObj) {
        let matches = [];
        let mId = 1;
        for (let i = 0; i < teams.length; i++) {
            for (let j = i + 1; j < teams.length; j++) {
                matches.push({ id: `m-${mId++}`, t1: teams[i], t2: teams[j], s1: "", s2: "" });
            }
        }
        return matches;
    }

    function initYouthLeague() {
        if(!isAdmin) return;
        let input = document.getElementById('youthTeamsInput').value.trim();
        state.youthTeamsInputText = input;
        let teams = input.split('\n').map(t => t.trim()).filter(t => t);
        if(teams.length < 4) { alert('青年組聯賽最少需要 4 隊'); return; }
        state.youthTeams = teams;
        state.youthMatches = generateRoundRobin(teams);
        renderLeagueView('youth');
        generateSchedule();
        alert('青年組聯賽生成成功！');
    }

    function initKidsLeague() {
        if(!isAdmin) return;
        let input = document.getElementById('kidsTeamsInput').value.trim();
        state.kidsTeamsInputText = input;
        let teams = input.split('\n').map(t => t.trim()).filter(t => t);
        if(teams.length < 4) { alert('兒童組聯賽最少需要 4 隊'); return; }
        state.kidsTeams = teams;
        state.kidsMatches = generateRoundRobin(teams);
        renderLeagueView('kids');
        generateSchedule();
        alert('兒童組聯賽生成成功！');
    }

    function updateScore(type, matchId, tIndex, val) {
        if(!isAdmin) return;
        let matches = type === 'youth' ? state.youthMatches : state.kidsMatches;
        let m = matches.find(x => x.id === matchId);
        if(m) {
            if(tIndex === 1) m.s1 = val;
            if(tIndex === 2) m.s2 = val;
        }
        renderLeagueView(type);
    }

    function calculateStandings(teams, matches) {
        let st = teams.map(name => ({ name, played: 0, winGames: 0, loseGames: 0, pts: 0 }));
        matches.forEach(m => {
            if(m.s1 !== "" && m.s2 !== "") {
                let s1 = parseInt(m.s1);
                let s2 = parseInt(m.s2);
                let t1 = st.find(x => x.name === m.t1);
                let t2 = st.find(x => x.name === m.t2);
                if(t1 && t2) {
                    t1.played++; t2.played++;
                    t1.winGames += s1;
                    t1.loseGames += s2;
                    t2.winGames += s2;
                    t2.loseGames += s1;
                    t1.pts += s1;
                    t2.pts += s2;
                }
            }
        });
        st.sort((a, b) => {
            if (b.pts !== a.pts) return b.pts - a.pts;
            if (b.winGames !== a.winGames) return b.winGames - a.winGames;
            return (b.winGames - b.loseGames) - (a.winGames - a.loseGames);
        });
        return st;
    }

    function renderLeagueView(type) {
        let teams = type === 'youth' ? state.youthTeams : state.kidsTeams;
        let matches = type === 'youth' ? state.youthMatches : state.kidsMatches;
        let container = document.getElementById(type === 'youth' ? 'youthContentContainer' : 'kidsContentContainer');
        if(!teams || teams.length === 0) { container.innerHTML = '請先生成聯賽。'; return; }

        let st = calculateStandings(teams, matches);
        let html = `<div class="grid-2">`;
        
        // 積分榜
        html += `<div><h4>聯賽積分榜 (前4名晉級)</h4><table><tr><th>名</th><th>隊伍</th><th>賽</th><th>贏局</th><th>輸局</th><th>得失差</th><th>積分</th></tr>`;
        st.forEach((t, i) => {
            let diff = t.winGames - t.loseGames;
            let diffStr = diff > 0 ? `+${diff}` : diff;
            let rankColor = i < 4 ? 'color:#34d399; font-weight:bold;' : '';
            html += `<tr style="${rankColor}"><td>${i+1}</td><td><b>${t.name}</b></td><td>${t.played}</td><td style="color:#34d399;">${t.winGames}</td><td style="color:#f43f5e;">${t.loseGames}</td><td>${diffStr}</td><td><b>${t.pts}</b></td></tr>`;
        });
        html += `</table></div>`;

        // 賽果填寫
        html += `<div><h4>對賽比分填寫 (3盤贏幾盤填幾盤)</h4>`;
        matches.forEach(m => {
            let dis = isAdmin ? '' : 'disabled';
            let opts = ['','0','1','2','3'];
            let optHtml1 = opts.map(o => `<option value="${o}" ${m.s1===o?'selected':''}>${o===''?'-':o}</option>`).join('');
            let optHtml2 = opts.map(o => `<option value="${o}" ${m.s2===o?'selected':''}>${o===''?'-':o}</option>`).join('');

            html += `<div class="match-row"><span class="team-name">${m.t1}</span><select ${dis} onchange="updateScore('${type}','${m.id}',1,this.value)">${optHtml1}</select><span>:</span><select ${dis} onchange="updateScore('${type}','${m.id}',2,this.value)">${optHtml2}</select><span class="team-name right">${m.t2}</span></div>`;
        });
        html += `</div></div>`;
        container.innerHTML = html;
    }

    // 嚴格同時段防撞演算法 (Round-based scheduling)
    function generateSchedule() {
        let allM = [];
        state.youthMatches.forEach(m => allM.push({...m, type:'青年'}));
        state.kidsMatches.forEach(m => allM.push({...m, type:'兒童'}));

        if(allM.length === 0) { 
            document.getElementById('scheduleContainer').innerHTML = '請先生成聯賽。';
            return; 
        }

        // 打散比賽順序
        allM.sort(() => Math.random() - 0.5);

        let rounds = []; // 儲存每一個時間場次 (每個場次最多4場比賽，且所有隊伍不能重複)
        let remainingMatches = [...allM];

        while (remainingMatches.length > 0) {
            let currentRoundMatches = [];
            let busyTeamsInThisRound = new Set();
            let nextRemaining = [];

            for (let m of remainingMatches) {
                // 如果呢個時間場次入面，呢場比賽嘅雙方隊伍都未出過場，而且這個時間場次未滿4場
                if (!busyTeamsInThisRound.has(m.t1) && !busyTeamsInThisRound.has(m.t2) && currentRoundMatches.length < 4) {
                    currentRoundMatches.push(m);
                    busyTeamsInThisRound.add(m.t1);
                    busyTeamsInThisRound.add(m.t2);
                } else {
                    nextRemaining.push(m);
                }
            }
            rounds.push(currentRoundMatches);
            remainingMatches = nextRemaining;
        }

        // 將所有時間場次嘅比賽平鋪展開去 4 張枱 (Round 1 嘅 4 場分別放落 4 張枱，如此類推)
        let tables = [[], [], [], []];
        rounds.forEach((roundMatches, roundIdx) => {
            roundMatches.forEach((m, tableIdx) => {
                // 幫比賽加上場次標籤顯示
                tables[tableIdx].push({ ...m, roundNum: roundIdx + 1 });
            });
        });

        let html = '<div class="grid-2">';
        tables.forEach((t, i) => {
            html += `<div><h3>盤 ${i+1} (枱 ${i+1})</h3><ul>`;
            t.forEach(m => html += `<li><b>第 ${m.roundNum} 輪</b> [${m.type}聯賽] ${m.t1} vs ${m.t2}</li>`);
            html += `</ul></div>`;
        });
        document.getElementById('scheduleContainer').innerHTML = html + '</div>';
    }

    function generateKoSchedule() {
        let koFinals = []; 
        let koOthers = []; 

        if(youthBracketState.sf[0].t1) youthBracketState.sf.forEach(m => { if(m.t1 && m.t2) koOthers.push({stage:'青年四強', t1:m.t1, t2:m.t2}); });
        if(kidsBracketState.sf[0].t1) kidsBracketState.sf.forEach(m => { if(m.t1 && m.t2) koOthers.push({stage:'兒童四強', t1:m.t1, t2:m.t2}); });

        if(youthBracketState.third.t1 && youthBracketState.third.t2) koFinals.push({stage:'青年季軍', t1:youthBracketState.third.t1, t2:youthBracketState.third.t2});
        if(kidsBracketState.third.t1 && kidsBracketState.third.t2) koFinals.push({stage:'兒童季軍', t1:kidsBracketState.third.t1, t2:kidsBracketState.third.t2});
        if(youthBracketState.final.t1 && youthBracketState.final.t2) koFinals.push({stage:'青年決賽', t1:youthBracketState.final.t1, t2:youthBracketState.final.t2});
        if(kidsBracketState.final.t1 && kidsBracketState.final.t2) koFinals.push({stage:'兒童決賽', t1:kidsBracketState.final.t1, t2:kidsBracketState.final.t2});

        let allKo = [...koOthers, ...koFinals];
        if(allKo.length === 0) { 
            document.getElementById('koScheduleContainer').innerHTML = '請先初始化走線圖。';
            return; 
        }

        let tables = [[],[],[]];
        let finalIdx = 0;
        koFinals.forEach(m => {
            tables[finalIdx % 2].push(m);
            finalIdx++;
        });
        koOthers.forEach((m, i) => {
            tables[i % 3].push(m);
        });

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
        let st = calculateStandings(state.youthTeams, state.youthMatches);
        if(!st[3]) { alert('聯賽隊伍不足4隊或尚未完成'); return; }
        youthBracketState.sf = [
            {t1: st[0].name, t2: st[3].name, s1:'', s2:'', w:''},
            {t1: st[1].name, t2: st[2].name, s1:'', s2:'', w:''}
        ];
        renderYouthBracket();
        generateKoSchedule();
        alert('青年組四強生成成功 (1st vs 4th, 2nd vs 3rd)！');
    }

    function initKidsBracket() {
        if(!isAdmin) return;
        let st = calculateStandings(state.kidsTeams, state.kidsMatches);
        if(!st[3]) { alert('聯賽隊伍不足4隊或尚未完成'); return; }
        kidsBracketState.sf = [
            {t1: st[0].name, t2: st[3].name, s1:'', s2:'', w:''},
            {t1: st[1].name, t2: st[2].name, s1:'', s2:'', w:''}
        ];
        renderKidsBracket();
        generateKoSchedule();
        alert('兒童組四強生成成功 (1st vs 4th, 2nd vs 3rd)！');
    }

    function updateBracketMatch(category, round, idx, s1Val, s2Val) {
        if(!isAdmin) return;
        let bracket = category === 'youth' ? youthBracketState : kidsBracketState;
        let m = round === 'sf' ? bracket.sf[idx] : round === 'final' ? bracket.final : bracket.third;

        m.s1 = s1Val;
        m.s2 = s2Val;

        if(s1Val !== '' && s2Val !== '') {
            let v1 = parseInt(s1Val);
            let v2 = parseInt(s2Val);
            if(v1 > v2) {
                m.w = m.t1;
                propagateWinner(category, round, idx, m.t1, m.t2);
            } else if(v2 > v1) {
                m.w = m.t2;
                propagateWinner(category, round, idx, m.t2, m.t1);
            } else {
                m.w = '';
            }
        } else {
            m.w = '';
        }

        if(category === 'youth') renderYouthBracket();
        else renderKidsBracket();
        generateKoSchedule();
    }

    function propagateWinner(category, round, idx, winner, loser) {
        let bracket = category === 'youth' ? youthBracketState : kidsBracketState;
        if(round === 'sf') {
            if(idx === 0) {
                bracket.final.t1 = winner;
                bracket.third.t1 = loser;
            } else {
                bracket.final.t2 = winner;
                bracket.third.t2 = loser;
            }
        }
    }

    function renderYouthBracket() { renderBracket('youth'); }
    function renderKidsBracket() { renderBracket('kids'); }

    function renderBracket(category) {
        let bracket = category === 'youth' ? youthBracketState : kidsBracketState;
        let containerId = category === 'youth' ? 'youthBracketContainer' : 'kidsBracketContainer';
        let dis = isAdmin ? '' : 'disabled';
        let opts5 = ['','3','2','1','0'];

        let html = `<div class="bracket-round"><h3>四強 (5盤3勝) [1vs4, 2vs3]</h3>` + bracket.sf.map((m,i)=>{
            let o1 = opts5.map(o => `<option value="${o}" ${m.s1===o?'selected':''}>${o===''?'-':o}</option>`).join('');
            let o2 = opts5.map(o => `<option value="${o}" ${m.s2===o?'selected':''}>${o===''?'-':o}</option>`).join('');
            return `<div class="bracket-match" style="margin:20px 0;">
                <div class="bracket-team-box ${bracket.sf[i].w===m.t1 && m.t1?'winner':''}">
                    <span class="bracket-team-name">${m.t1||'等待'}</span>
                    <select class="bracket-score-select" ${dis} onchange="updateBracketMatch('${category}','sf',${i},this.value,'${m.s2}')">${o1}</select>
                </div>
                <div style="text-align:center;font-size:12px;color:var(--text-muted);margin:2px 0;">VS</div>
                <div class="bracket-team-box ${bracket.sf[i].w===m.t2 && m.t2?'winner':''}">
                    <span class="bracket-team-name">${m.t2||'等待'}</span>
                    <select class="bracket-score-select" ${dis} onchange="updateBracketMatch('${category}','sf',${i},'${m.s1}',this.value)">${o2}</select>
                </div>
            </div>`;
        }).join('') + `</div>`;

        let fO1 = opts5.map(o => `<option value="${o}" ${bracket.final.s1===o?'selected':''}>${o===''?'-':o}</option>`).join('');
        let fO2 = opts5.map(o => `<option value="${o}" ${bracket.final.s2===o?'selected':''}>${o===''?'-':o}</option>`).join('');
        let tO1 = opts5.map(o => `<option value="${o}" ${bracket.third.s1===o?'selected':''}>${o===''?'-':o}</option>`).join('');
        let tO2 = opts5.map(o => `<option value="${o}" ${bracket.third.s2===o?'selected':''}>${o===''?'-':o}</option>`).join('');

        html += `<div class="bracket-round"><h3>決賽</h3>
            <div class="bracket-match">
                <h4 style="margin:0 0 4px;color:var(--primary-light);">冠軍戰 (5盤3勝)</h4>
                <div class="bracket-team-box ${bracket.final.w===bracket.final.t1 && bracket.final.t1?'winner':''}">
                    <span class="bracket-team-name">${bracket.final.t1||'等待'}</span>
                    <select class="bracket-score-select" ${dis} onchange="updateBracketMatch('${category}','final',0,this.value,'${bracket.final.s2}')">${fO1}</select>
                </div>
                <div style="text-align:center;font-size:12px;color:var(--text-muted);margin:2px 0;">VS</div>
                <div class="bracket-team-box ${bracket.final.w===bracket.final.t2 && bracket.final.t2?'winner':''}">
                    <span class="bracket-team-name">${bracket.final.t2||'等待'}</span>
                    <select class="bracket-score-select" ${dis} onchange="updateBracketMatch('${category}','final',0,'${bracket.final.s1}',this.value)">${fO2}</select>
                </div>
            </div>
            <div class="bracket-match">
                <h4 style="margin:0 0 4px;color:var(--text-muted);">季軍戰 (5盤3勝)</h4>
                <div class="bracket-team-box ${bracket.third.w===bracket.third.t1 && bracket.third.t1?'winner':''}">
                    <span class="bracket-team-name">${bracket.third.t1||'等待'}</span>
                    <select class="bracket-score-select" ${dis} onchange="updateBracketMatch('${category}','third',0,this.value,'${bracket.third.s2}')">${tO1}</select>
                </div>
                <div style="text-align:center;font-size:12px;color:var(--text-muted);margin:2px 0;">VS</div>
                <div class="bracket-team-box ${bracket.third.w===bracket.third.t2 && bracket.third.t2?'winner':''}">
                    <span class="bracket-team-name">${bracket.third.t2||'等待'}</span>
                    <select class="bracket-score-select" ${dis} onchange="updateBracketMatch('${category}','third',0,'${bracket.third.s1}',this.value)">${tO2}</select>
                </div>
            </div>
        </div>`;
        document.getElementById(containerId).innerHTML = html;
    }
</script>

</body>
</html>
