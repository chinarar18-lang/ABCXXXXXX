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
        button { background: var(--primary); color: white; border: none; padding: 10px 20px; border-radius: 8px; cursor: pointer; font-weight: bold; margin-top: 10px; }
        .grid-2 { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
        .grid-4 { display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; }
        .grid-3 { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; }
        .match-box { background: #334155; padding: 10px; border-radius: 8px; text-align: center; margin-bottom: 5px; }
        .admin-bar { background: #0f172a; padding: 15px; border-radius: 8px; border: 1px solid var(--primary); margin-bottom: 20px; display: flex; justify-content: space-between; align-items: center; }
        h1, h2 { color: var(--accent); }
    </style>
</head>
<body>

<div class="container">
    <div style="text-align:center;"><h1>🌀 陀螺比賽實時系統</h1></div>
    
    <div id="adminPanel" class="admin-bar">
        <div id="authArea">
            <input type="password" id="pass" placeholder="管理員密碼">
            <button onclick="unlock()">解鎖管理</button>
        </div>
        <div id="editStatus" style="display:none; color: var(--success); font-weight:bold;">✨ 編輯模式已開啟 (自動同步中...)</div>
    </div>

    <div class="card">
        <h2>1. 隊伍設定與抽籤</h2>
        <div class="grid-2">
            <div><h3>青年組</h3><textarea id="yTeam" class="edit" disabled></textarea></div>
            <div><h3>兒童組</h3><textarea id="kTeam" class="edit" disabled></textarea></div>
        </div>
        <button id="drawBtn" class="edit" disabled onclick="handleProcess()">執行抽籤並自動分配盤次</button>
    </div>

    <div class="card">
        <h2>2. 小組賽盤次 (4個盤)</h2>
        <div id="groupSchedule" class="grid-4"></div>
    </div>

    <div class="card">
        <h2>3. 淘汰賽盤次 (3個盤)</h2>
        <div id="koSchedule" class="grid-3"></div>
    </div>
</div>

<script>
    const URL = "https://script.google.com/macros/s/AKfycbw3_WKdwd6yUt_oRibRIL62jjboDkapre7Wwx4Pcfet5rs0CCfhyCfSqkCchubThhvl/exec";
    
    function unlock() {
        if(document.getElementById('pass').value === 'admin888') {
            document.querySelectorAll('.edit').forEach(el => el.disabled = false);
            document.getElementById('authArea').style.display = 'none';
            document.getElementById('editStatus').style.display = 'block';
        } else alert('密碼錯誤');
    }

    function handleProcess() {
        let y = document.getElementById('yTeam').value.split('\n').filter(x => x.trim());
        let k = document.getElementById('kTeam').value.split('\n').filter(x => x.trim());
        
        // 分配小組盤次 (4盤)
        let gHtml = '';
        [1,2,3,4].forEach(p => {
            gHtml += `<div><h4>盤 ${p}</h4>` + (y[p-1] ? `<div class="match-box">${y[p-1]} v ${y[p+3] || '輪空'}</div>` : '') + '</div>';
        });
        document.getElementById('groupSchedule').innerHTML = gHtml;

        // 分配淘汰賽盤次 (3盤)
        let kHtml = '';
        [1,2,3].forEach(p => {
            kHtml += `<div><h4>盤 ${p}</h4>` + (y[p+7] ? `<div class="match-box">${y[p+7]} v ${y[p+8] || '輪空'}</div>` : '') + '</div>';
        });
        document.getElementById('koSchedule').innerHTML = kHtml;

        // 同步雲端
        fetch(URL, { method: 'POST', mode: 'no-cors', body: JSON.stringify({y, k}) })
        .then(() => alert('已抽籤並同步至雲端！'));
    }

    // 初始化自動讀取
    fetch(URL).then(res => res.json()).then(data => {
        // 這裡會加入從雲端讀取並顯示數據的邏輯
    });
</script>
</body>
</html>
