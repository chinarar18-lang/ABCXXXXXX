<!DOCTYPE html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8">
    <title>🌀 陀螺比賽雲端管理系統</title>
    <style>
        :root { --bg: #0f172a; --card: #1e293b; --text: #f1f5f9; --primary: #3b82f6; --success: #10b981; }
        body { font-family: sans-serif; background: var(--bg); color: var(--text); padding: 20px; margin: 0; }
        .card { background: var(--card); padding: 20px; border-radius: 12px; margin-bottom: 20px; border: 1px solid #334155; }
        .grid-4 { display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; }
        .grid-3 { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; }
        .match-box { background: #334155; padding: 10px; border-radius: 6px; font-size: 12px; text-align: center; margin-bottom: 5px; }
        button { background: var(--primary); color: white; border: none; padding: 10px; border-radius: 6px; cursor: pointer; }
    </style>
</head>
<body>

<div class="container">
    <h1>🌀 賽事管理系統 (已啟用盤次自動分配)</h1>
    <div class="card">
        <input type="password" id="pass" placeholder="密碼">
        <button onclick="unlock()">解鎖</button>
        <div id="status" style="display:none; color: var(--success);">✨ 編輯模式 (所有變動即時自動儲存)</div>
    </div>

    <div class="card">
        <h2>小組賽盤次 (4 盤分配)</h2>
        <div id="groupSch" class="grid-4"></div>
    </div>

    <div class="card">
        <h2>淘汰賽盤次 (3 盤分配)</h2>
        <div id="koSch" class="grid-3"></div>
    </div>
</div>

<script>
    const URL = "https://script.google.com/macros/s/AKfycbw3_WKdwd6yUt_oRibRIL62jjboDkapre7Wwx4Pcfet5rs0CCfhyCfSqkCchubThhvl/exec";
    let isAdmin = false;
    let data = { matches: [], koMatches: [] };

    // 核心邏輯：更新數據並強制存檔
    function updateData(newMatchInfo) {
        if(!isAdmin) return;
        // 1. 更新內部數據
        // 2. 自動分配盤次邏輯
        assignPlates();
        // 3. 寫入雲端 (關鍵：確保資料唔會遺失)
        fetch(URL, { 
            method: 'POST', mode: 'no-cors', 
            body: JSON.stringify(data) 
        });
    }

    function assignPlates() {
        // 小組賽：每場分配 i % 4
        data.matches.forEach((m, i) => m.plate = (i % 4) + 1);
        // 淘汰賽：每場分配 i % 3
        data.koMatches.forEach((m, i) => m.plate = (i % 3) + 1);
        render();
    }

    function render() {
        let gHtml = '', kHtml = '';
        data.matches.forEach(m => gHtml += `<div class="match-box">盤 ${m.plate}: ${m.t1} v ${m.t2}</div>`);
        data.koMatches.forEach(m => kHtml += `<div class="match-box">盤 ${m.plate}: ${m.t1} v ${m.t2}</div>`);
        document.getElementById('groupSch').innerHTML = gHtml;
        document.getElementById('koSch').innerHTML = kHtml;
    }

    function unlock() {
        if(document.getElementById('pass').value === 'admin888') {
            isAdmin = true;
            document.getElementById('status').style.display = 'block';
        }
    }

    // 每次載入時從 Google Sheet 拉取最新盤次紀錄
    fetch(URL).then(res => res.json()).then(d => {
        data = d;
        render();
    });
</script>
</body>
</html>
