<!DOCTYPE html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>🌀 陀螺比賽雲端實時管理系統</title>
    <style>
        :root { --primary: #3b82f6; --primary-dark: #1d4ed8; --bg-color: #f8fafc; --card-bg: #ffffff; --text-main: #0f172a; --text-muted: #64748b; --border: #e2e8f0; --success: #10b981; --success-bg: #ecfdf5; --radius: 12px; }
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; background-color: var(--bg-color); color: var(--text-main); margin: 0; padding: 20px; }
        .container { max-width: 1100px; margin: 0 auto; }
        .admin-bar { background: #fff; padding: 15px 24px; border-radius: var(--radius); box-shadow: 0 1px 3px rgba(0,0,0,0.1); margin-bottom: 24px; display: flex; justify-content: space-between; align-items: center; }
        .card { background: var(--card-bg); border-radius: var(--radius); padding: 24px; margin-bottom: 24px; box-shadow: 0 1px 3px rgba(0,0,0,0.05); }
        .tab-buttons { display: flex; gap: 8px; margin-bottom: 20px; flex-wrap: wrap; }
        .tab-btn { background-color: #e2e8f0; color: var(--text-muted); border: none; padding: 10px 16px; border-radius: 8px; cursor: pointer; font-weight: 600; }
        .tab-btn.active { background-color: var(--primary); color: white; }
        .section { display: none; }
        .section.active { display: block; }
        button { background-color: var(--primary); color: white; border: none; padding: 10px 20px; border-radius: 8px; cursor: pointer; font-weight: 600; }
        .btn-success { background-color: var(--success); }
        textarea { width: 100%; height: 100px; padding: 10px; border: 1px solid var(--border); border-radius: 8px; box-sizing: border-box; }
        .grid-3 { display: grid; grid-template-columns: repeat(3, 1fr); gap: 15px; }
        .match-row { display: flex; align-items: center; justify-content: center; gap: 10px; padding: 8px; background: #f8fafc; border-radius: 6px; }
        input[type="number"] { width: 45px; padding: 6px; border: 1px solid var(--border); border-radius: 4px; text-align: center; }
        .bracket-container { display: flex; justify-content: space-between; gap: 10px; overflow-x: auto; }
        .bracket-match { background: #f8fafc; border: 1px solid var(--border); border-radius: 6px; padding: 8px; text-align: center; font-size: 13px; }
        .bracket-team { padding: 6px; background: white; border: 1px solid var(--border); margin: 2px 0; border-radius: 4px; cursor: pointer; }
        .bracket-team.winner { background-color: var(--success-bg); border-color: var(--success); }
    </style>
</head>
<body>

<div class="container">
    <div class="admin-bar">
        <div>狀態：<span id="modeStatus">🔍 檢視模式</span></div>
        <div id="authArea">
            <input type="password" id="adminPassword" placeholder="密碼" style="padding: 6px;">
            <button onclick="tryUnlock()">解鎖</button>
        </div>
        <div id="editArea" style="display:none; color: var(--success); font-weight:bold;">✨ 編輯模式 (所有變更即時同步)</div>
    </div>

    <div class="tab-buttons">
        <button class="tab-btn active" onclick="switchTab('setup')">1. 抽籤</button>
        <button class="tab-btn" onclick="switchTab('groupStage')">2. 積分/對賽</button>
        <button class="tab-btn" onclick="switchTab('schedule')">3. 盤次</button>
        <button class="tab-btn" onclick="switchTab('knockout')">4. 淘汰賽</button>
    </div>

    <div id="setup" class="section active"><div class="card"><div class="grid-2"><div><h3>青年抽籤</h3><textarea id="youthTeamsInput" class="edit-locked" disabled></textarea><button class="edit-locked" disabled onclick="drawYouth()">抽籤並同步</button></div><div><h3>兒童抽籤</h3><textarea id="kidsTeamsInput" class="edit-locked" disabled></textarea><button class="edit-locked" disabled onclick="drawKids()">抽籤並同步</button></div></div></div></div>
    <div id="groupStage" class="section"><div class="card"><div id="youthContentContainer"></div><hr><div id="kidsContentContainer"></div></div></div>
    <div id="schedule" class="section"><div class="card"><h2>盤次分配 (小組賽4盤 | 淘汰賽3盤)</h2><div id="scheduleContainer"></div></div></div>
    <div id="knockout" class="section"><div class="card"><h2>走線圖</h2><button class="edit-locked" disabled onclick="initBrackets()">初始化對陣</button><div id="youthBracketContainer"></div><hr><div id="kidsBracketContainer"></div></div></div>
</div>

<script>
    const WEB_APP_URL = "https://script.google.com/macros/s/AKfycbw3_WKdwd6yUt_oRibRIL62jjboDkapre7Wwx4Pcfet5rs0CCfhyCfSqkCchubThhvl/exec"; 
    let isAdmin = false;
    let state = { youthTeamsInputText: '', kidsTeamsInputText: '', youthGroups: {A:[],B:[],C:[]}, kidsGroups: {D:[],E:[]}, youthMatches: {}, kidsMatches: {} };
    let youthBracketState = { qf: Array(4).fill({t1:'',t2:'',w:null}), sf: Array(2).fill({t1:'',t2:'',w:null}), final: {t1:'',t2:'',w:null}, third: {t1:'',t2:'',w:null} };
    let kidsBracketState = { sf: Array(2).fill({t1:'',t2:'',w:null}), final: {t1:'',t2:'',w:null}, third: {t1:'',t2:'',w:null} };

    window.onload = () => fetchFromCloud();

    // 核心同步功能：每次改變都強制寫入雲端
    function autoSync() {
        if(!isAdmin) return;
        let packageData = [[JSON.stringify({ state, youthBracketState, kidsBracketState })]];
        fetch(WEB_APP_URL, { method: 'POST', mode: 'no-cors', body: JSON.stringify(packageData) })
        .then(() => updateUI());
    }

    function fetchFromCloud() {
        fetch(WEB_APP_URL).then(res => res.json()).then(data => {
            if(data && data.length > 0) {
                let cloudData = JSON.parse(data[0][0]);
                state = cloudData.state; youthBracketState = cloudData.youthBracketState; kidsBracketState = cloudData.kidsBracketState;
                updateUI();
            }
        });
    }

    function tryUnlock() {
        if(document.getElementById('adminPassword').value === 'admin888') {
            isAdmin = true;
            document.getElementById('authArea').style.display = 'none';
            document.getElementById('editArea').style.display = 'block';
            document.getElementById('modeStatus').innerHTML = '✨ 編輯中';
            document.querySelectorAll('.edit-locked').forEach(el => el.disabled = false);
            alert('已解鎖！所有操作會自動同步。');
        } else { alert('密碼錯'); }
    }

    // 自動更新邏輯 (抽籤後直接 render + sync)
    function drawYouth() {
        let teams = document.getElementById('youthTeamsInput').value.split('\n').filter(t => t.trim());
        state.youthTeamsInputText = teams.join('\n');
        // ... (抽籤邏輯) ...
        autoSync();
    }
    
    // 將所有產生盤次的 function 合併入 updateUI
    function updateUI() {
        renderGroupView('youth'); renderGroupView('kids');
        renderYouthBracket(); renderKidsBracket();
        generateSchedules(); // 這裡自動更新所有盤次分配
    }
    
    function generateSchedules() {
        // 自動生成 4 盤小組賽 + 3 盤淘汰賽
        // ... (邏輯同前，自動執行，不需按鈕)
    }
</script>
</body>
</html>
