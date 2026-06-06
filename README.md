<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>6-Player Dino Arena (Name Saved)</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mqtt/5.2.2/mqtt.min.js"></script>
    <style>
        * { box-sizing: border-box; user-select: none; -webkit-user-select: none; }
        body { font-family: sans-serif; text-align: center; background: #f0f2f5; margin: 0; padding: 10px; }
        canvas { background: white; border: 2px solid #333; border-radius: 6px; display: block; margin: 10px auto; width: 100%; max-width: 600px; height: auto; }
        .panel { background: white; padding: 15px; border-radius: 8px; max-width: 600px; margin: 0 auto 10px auto; box-shadow: 0 2px 4px rgba(0,0,0,0.1); }
        .btn { padding: 12px 15px; font-size: 14px; border: none; border-radius: 4px; font-weight: bold; cursor: pointer; color: white; margin: 5px; }
        .blue { background: #0070f3; } .green { background: #16a34a; } .red { background: #dc2626; }
        input[type="text"], input[type="number"] { padding: 11px; font-size: 14px; width: 50%; max-width: 180px; text-align: center; border: 1px solid #ccc; border-radius: 4px; margin-right: 5px; user-select: text; -webkit-user-select: text; }
        #username-input { width: 80%; max-width: 200px; margin-bottom: 10px; font-weight: bold; border: 2px solid #0070f3; }
        #touch-pad { background: #222; color: white; width: 100%; max-width: 600px; margin: 10px auto; padding: 25px; font-size: 1.2rem; font-weight: bold; border-radius: 8px; touch-action: manipulation; }
        #status { font-weight: bold; color: #d97706; margin: 8px 0; }
        #lobby-count { font-size: 1.1rem; color: #7c3aed; font-weight: bold; margin: 5px 0; }
        .row { margin: 10px 0; display: flex; justify-content: center; align-items: center; }
        .cheats { margin-top: 8px; display: flex; gap: 15px; justify-content: center; align-items: center; font-size: 0.9rem; flex-wrap: wrap; }
        #gameSpeed { width: 60px; padding: 6px; margin-left: 4px; text-align: center; display: inline-block; }
        .chat-container { background: white; border-radius: 8px; max-width: 600px; margin: 10px auto; padding: 10px; box-shadow: 0 2px 4px rgba(0,0,0,0.1); text-align: left; }
        #chat-log { height: 100px; overflow-y: auto; border: 1px solid #ddd; padding: 8px; border-radius: 4px; background: #fafafa; font-size: 13px; margin-bottom: 8px; user-select: text; -webkit-user-select: text; }
        .chat-row { display: flex; }
        #chat-input { flex-grow: 1; padding: 10px; font-size: 14px; border: 1px solid #ccc; border-radius: 4px 0 0 4px; text-align: left; max-width: none; }
        #chat-send { border-radius: 0 4px 4px 0; margin: 0; padding: 10px 15px; background: #374151; }
    </style>
</head>
<body>

    <h1>🦖 6-Player Dino Arena</h1>
    
    <div class="panel">
        <div>
            <label style="font-weight: bold; display: block; margin-bottom: 3px;">✍️ Your Name (Autosaved):</label>
            <input type="text" id="username-input" placeholder="Your Name" maxlength="12" oninput="saveMyName(this.value)">
        </div>
        <div>Your Room ID: <strong id="my-id" style="color:#0070f3; font-size: 1.2rem;">...</strong></div>
        
        <div id="lobby-count">👥 Players on screen: 1 / 6</div>

        <div class="row" id="connection-controls">
            <input type="text" id="friend-code" placeholder="Enter Friend's ID">
            <button class="btn blue" onclick="joinRoomCode()">🔌 Join Room</button>
        </div>

        <button class="btn red" id="respawnBtn" onclick="respawnMe()" style="display:none;">🔄 Respawn</button>
        <button class="btn red" id="leaveBtn" onclick="leaveMatch()" style="display:none;">🚪 Leave Room</button>
        
        <div id="status">⏳ Connecting to network...</div>
        
        <div class="cheats">
            <label><input type="checkbox" id="godMode"> 🛡️ God Mode</label>
            <label><input type="checkbox" id="infJump"> 🚀 Inf Jump</label>
            <label>🏃 Speed: <input type="number" id="gameSpeed" value="5" min="1" max="30" oninput="changeSpeed(this.value)"></label>
        </div>
    </div>

    <canvas id="game" width="600" height="150"></canvas>
    <div id="touch-pad">TAP TO JUMP</div>

    <div class="chat-container">
        <div id="chat-log"><i>System: Starting game...</i></div>
        <div class="chat-row">
            <input type="text" id="chat-input" placeholder="Type a message..." onkeydown="if(event.key==='Enter') sendChat()">
            <button class="btn" id="chat-send" onclick="sendChat()">💬 Send</button>
        </div>
    </div>

    <script>
        const canvas = document.getElementById('game');
        const ctx = canvas.getContext('2d');
        
        const myShortId = Math.floor(10000 + Math.random() * 90000).toString();
        document.getElementById('my-id').innerText = myShortId;

        // NEW: Check if a saved name already exists inside storage memory
        let savedName = localStorage.getItem('dino_saved_name');
        if (savedName) {
            document.getElementById('username-input').value = savedName;
        } else {
            // Default random backup title if storage is empty
            document.getElementById('username-input').value = "Player_" + myShortId.substring(0,3);
        }

        // NEW: Function running every time you type to store data permanently
        function saveMyName(val) {
            let cleanName = val.trim();
            if(cleanName) {
                localStorage.setItem('dino_saved_name', cleanName);
            }
        }

        let currentRoom = null;
        let mqttClient = null;
        let isServerConnected = false;

        const colorPalette = ['#0070f3', '#e11d48', '#16a34a', '#d97706', '#7c3aed', '#db2777'];
        let myColor = colorPalette[Math.floor(Math.random() * colorPalette.length)];

        let p1 = { id: myShortId, name: "", x: 40, y: 110, vy: 0, isJumping: false, isDead: false, color: myColor, score: 0 }; 
        let players = {}; 
        let cactus = { x: 600, y: 115, width: 15, height: 20, speed: 5 };
        
        let lastScoreTick = Date.now();

        try {
            if (typeof mqtt !== 'undefined') {
                mqttClient = mqtt.connect('wss://broker.hivemq.com:8884/mqtt', {
                    keepalive: 10,
                    reconnectPeriod: 1000,
                    connectTimeout: 5000
                });
                
                mqttClient.on('connect', () => {
                    isServerConnected = true;
                    document.getElementById('status').innerText = "✅ Network Connected! Ready.";
                    document.getElementById('status').style.color = "#16a34a";
                    setupRoomChannels(myShortId); // auto host your own room
                });

                mqttClient.on('message', (topic, msg) => {
                    try {
                        const data = JSON.parse(msg.toString());
                        if (data.id === myShortId) return;

                        if (data.type === 'chat') {
                            appendLog(`<b style="color: ${data.color};">${escapeHTML(data.name)}:</b> ${escapeHTML(data.text)}`);
                            return;
                        }

                        if (data.type === 'leave') {
                            if (players[data.id]) {
                                appendLog(`<i>System: ${escapeHTML(players[data.id].name)} left the room.</i>`);
                                delete players[data.id];
                            }
                            updateLobbyCount();
                            return;
                        }

                        if (data.type === 'update') {
                            if (!players[data.id]) {
                                appendLog(`<i>System: ${escapeHTML(data.name)} linked up!</i>`);
                            }

                            let activeKeys = Object.keys(players);
                            if (!players[data.id]) activeKeys.push(data.id);
                            activeKeys.push(myShortId
                            
