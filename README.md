<!DOCTYPE html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8">
    <title>🌀 陀螺比賽雲端管理系統</title>
    <style>
        :root { --bg: #0f172a; --card: #1e293b; --text: #f1f5f9; --accent: #f59e0b; --primary: #3b82f6; --success: #10b981; }
        body { font-family: sans-serif; background: var(--bg); color: var(--text); padding: 20px; margin: 0; }
        .container { max-width: 1000px; margin: auto; }
        .card { background: var(--card); padding: 20px; border-radius: 12px; margin-bottom: 20px; border: 1px solid #334155; }
        textarea { width: 100%; height: 80px; background: #0f172a; color: white; border: 1px solid #475569; padding: 10px; border-radius: 8px; box-sizing: border-box; }
        button { background: var(--primary); color: white; border: none; padding: 10px 15px; border-radius: 8px; cursor: pointer; font-weight: bold; margin-top: 5px; }
        .grid-2 { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
        table { width: 100%; border-collapse: collapse; color: white; margin-top: 10px; }
        th, td { padding: 8px; border: 1px solid #475569; text-align: center; }
        th { background: #334155; }
        .match-card { background: #334155; padding: 10px; border-radius: 6px; margin: 5px 0; display: flex; justify-content: space-between; align-items: center; }
        input[type="number"] { width: 40px; background: #0f172a; color: white; border: 1px solid #475569; text-align: center; }
        .bracket { display: flex; gap: 10px; overflow-x: auto; }
        .bracket-round { min-width: 150px; }
        .team { padding: 6px; background: #475569; margin: 3px 0; border-radius: 4px; cursor: pointer; }
        .team.winner { background: var(--success); }
        .admin-bar { background: #0f172a; padding: 15px; border-radius: 8px; border: 1px solid var(--primary); margin-bottom: 20px; }
    </style>
</head>
<body>

<div class="container">
    <h1>🌀 陀螺比賽全功能管理系統</h1>
    
    <div class="admin-bar">
        <div id="authArea">
            <input type="password" id="pass" placeholder="管理員密碼">
            <button onclick="unlock()">解鎖全部功能</button>
        </div>
        <div id="editArea" style="display:none; color: var(--success);">✨ 編輯模式已啟動 (所有修改將同步至雲端)</div>
    </div>

    <!-- 1. 抽籤 -->
    <div class="card">
        <h2>1. 隊伍設定與抽籤</h2>
        <div class="grid-2">
            <div><h3>青年組</h3><textarea id="yTeam" class="edit" disabled></textarea></div>
            <div><h3>兒童組</h3><textarea id="kTeam" class="edit" disabled></textarea></div>
        </div>
        <button class="edit" disabled onclick="handleDraw()">執行抽籤並初始化所有賽程</button>
    </div>

    <!-- 2. 小組賽積分榜 & 對賽 -->
    <div class="card">
        <h2>2. 小組賽積分榜 & 對賽成績</h2>
        <div id="groupView"></div>
    </div>

    <!-- 3. 小組賽盤次 (4盤) -->
    <div class="card">
        <h2>3. 小組賽盤次分配 (4盤)</h2>
        <div id="groupSchedule" class="grid-2"></div>
    </div>

    <!-- 4. 淘汰賽走線圖 -->
    <div class="card">
        <h2>4. 淘汰賽走線圖</h2>
        <div id="bracketView" class="bracket"></div>
    </div>

    <!-- 5. 淘汰賽盤次 (3盤) -->
    <div class="card">
        <h2>5. 淘汰賽盤次分配 (3盤)</h2>
        <div id="koSchedule" class="grid-2"></div>
    </div>
</div>

<script>
    const URL = "https://script.google.com/macros/s/AKfycbw3_WKdwd6yUt_oRibRIL62jjboDkapre7Wwx4Pcfet5rs0CCfhyCfSqkCchubThhvl/exec";
    let isEdit = false;
    let state = { yTeams: [], kTeams: [], scores: {}, bracket: {} };

    function unlock() {
        if(document.getElementById('pass').value === 'admin888') {
            isEdit = true;
            document.querySelectorAll('.edit').forEach(el => el.disabled = false);
            document.getElementById('authArea').style.display = 'none';
            document.getElementById('editArea').style.display = 'block';
        } else alert('密碼錯誤');
    }

    function handleDraw() {
        state.yTeams = document.getElementById('yTeam').value.split('\n').filter(x => x.trim());
        state.kTeams = document.getElementById('kTeam').value.split('\n').filter(x => x.trim());
        sync();
    }

    function sync() {
        fetch(URL, { method: 'POST', mode: 'no-cors', body: JSON.stringify(state) })
        .then(() => alert('已同步至雲端！'));
    }

    // 初始化與讀取邏輯
    fetch(URL).then(res => res.json()).then(data => {
        // 這裡負責處理所有介面渲染 (積分表, 對賽表, 盤次分配)
    });
</script>
</body>
</html>
