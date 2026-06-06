<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>6-Player Dino (Room Join Fix)</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mqtt/5.2.2/mqtt.min.js"></script>
    <style>
        * { box-sizing: border-box; user-select: none; -webkit-user-select: none; }
        body { font-family: sans-serif; text-align: center; background: #f0f2f5; margin: 0; padding: 10px; }
        canvas { background: white; border: 2px solid #333; border-radius: 6px; display: block; margin: 10px auto; width: 100%; max-width: 600px; height: auto; }
        .panel { background: white; padding: 15px; border-radius: 8px; max-width: 600px; margin: 0 auto 10px auto; box-shadow: 0 2px 4px rgba(0,0,0,0.1); }
        .btn { padding: 12px 15px; font-size: 14px; border: none; border-radius: 4px; font-weight: bold; cursor: pointer; color: white; margin: 5px; }
        .blue { background: #0070f3; } .green { background: #16a34a; } .red { background: #dc2626; }
        input[type="text"], input[type="number"] { padding: 11px; font-size: 14px; width: 50%; max-width: 180px; text-align: center; border: 1px solid #ccc; border-radius: 4px; margin-right: 5px; user-select: text; -webkit-user-select: text; }
        #username-input { width: 80%; max-width: 200px; margin-bottom: 10px; font-weight: bold; }
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
            <input type="text" id="username-input" placeholder="Your Name" maxlength="12">
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
        
        // Generate a clean 5 digit room ID code
        const myShortId = Math.floor(10000 + Math.random() * 90000).toString();
        document.getElementById('my-id').innerText = myShortId;
        document.getElementById('username-input').value = "Player_" + myShortId.substring(0,3);

        let currentRoom = null;
        let mqttClient = null;
        let isServerConnected = false;

        const colorPalette = ['#0070f3', '#e11d48', '#16a34a', '#d97706', '#7c3aed', '#db2777'];
        let myColor = colorPalette[Math.floor(Math.random() * colorPalette.length)];

        let p1 = { id: myShortId, name: "", x: 40, y: 110, vy: 0, isJumping: false, isDead: false, color: myColor, score: 0 }; 
        let players = {}; 
        let cactus = { x: 600, y: 115, width: 15, height: 20, speed: 5 };
        
        let lastScoreTick = Date.now();

        // Connect to broker
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
                    
                    // Host your own individual room code initially
                    setupRoomChannels(myShortId);
                });

                mqttClient.on('message', (topic, msg) => {
                    try {
                        const data = JSON.parse(msg.toString());
                        if (data.id === myShortId) return; // ignore myself

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
                            // If a brand new player is detected, log it!
                            if (!players[data.id]) {
                                appendLog(`<i>System: ${escapeHTML(data.name)} linked up!</i>`);
                            }

                            // Sort player IDs dynamically so everyone sits on a separate tile lane
                            let activeKeys = Object.keys(players);
                            if (!players[data.id]) activeKeys.push(data.id);
                            activeKeys.push(myShortId);
                            activeKeys.sort();

                            p1.x = 40 + (activeKeys.indexOf(myShortId) * 45);
                            let layoutIndex = activeKeys.indexOf(data.id);

                            players[data.id] = {
                                x: 40 + (layoutIndex * 45),
                                y: data.y,
                                isDead: data.isDead,
                                score: data.score,
                                color: data.color,
                                name: data.name,
                                lastSeen: Date.now()
                            };

                            updateLobbyCount();

                            // Sync obstacle track physics from room host
                            if (isHost()) {
                                if (data.speedSync) {
                                    cactus.speed = data.speedSync;
                                    document.getElementById('gameSpeed').value = data.speedSync;
                                }
                            } else {
                                if (data.isHostPlayer) {
                                    cactus.x = data.cx;
                                    cactus.speed = data.speed;
                                    document.getElementById('gameSpeed').value = data.speed;
                                }
                            }
                        }
                    } catch (e) {}
                });
            }
        } catch(err) {}

        function updateLobbyCount() {
            let total = Object.keys(players).length + 1;
            document.getElementById('lobby-count').innerText = `👥 Players on screen: ${total} / 6`;
        }

        // FIXED JOIN ROOM ENGINE SWITCH HANDSHAKE
        function joinRoomCode() {
            const targetRoom = document.getElementById('friend-code').value.trim();
            if (!targetRoom) return alert("Type your friend's room ID first!");
            setupRoomChannels(targetRoom);
        }

        function setupRoomChannels(roomName) {
            if (!isServerConnected) return alert("Network layer connecting, wait a second.");
            
            // 1. Inform old room we are cleanly breaking connection
            if (currentRoom) {
                mqttClient.publish('dino_arena/' + currentRoom, JSON.stringify({ type: 'leave', id: myShortId }));
                mqttClient.unsubscribe('dino_arena/' + currentRoom);
            }
            
            // 2. Clear out local cache completely to prevent ghost overlaps
            currentRoom = roomName;
            players = {}; 
            p1.x = 40;
            p1.score = 0;
            p1.isDead = false;
            document.getElementById('respawnBtn').style.display = 'none';
            
            // 3. Bind to the brand new room target channel subscription
            mqttClient.subscribe('dino_arena/' + currentRoom);
            
            // Adjust UI buttons based on whether you are solo or in a friend's room
            if (currentRoom === myShortId) {
                document.getElementById('connection-controls').style.display = 'flex';
                document.getElementById('leaveBtn').style.display = 'none';
                document.getElementById('status').innerText = `🏠 Hosting Room: ${currentRoom}`;
            } else {
                document.getElementById('connection-controls').style.display = 'none';
                document.getElementById('leaveBtn').style.display = 'inline-block';
                document.getElementById('status').innerText = `🔗 Connected to Room: ${currentRoom}`;
            }
            
            updateLobbyCount();
            appendLog(`<i>System: Switch room channel to [${roomName}]. Connecting...</i>`);
        }

        function isHost() {
            if (!currentRoom) return true;
            let activeIds = Object.keys(players);
            activeIds.push(myShortId);
            activeIds.sort();
            return activeIds[0] === myShortId;
        }

        function changeSpeed(val) {
            let num = parseFloat(val);
            if (isNaN(num) || num < 1) num = 1;
            cactus.speed = num;
        }

        function sendChat() {
            const input = document.getElementById('chat-input');
            const text = input.value.trim();
            const uName = document.getElementById('username-input').value.trim() || "Player";
            if (!text) return;

            if (currentRoom && isServerConnected) {
                mqttClient.publish('dino_arena/' + currentRoom, JSON.stringify({ type: 'chat', id: myShortId, name: uName, color: myColor, text: text }));
                appendLog(`<b style="color: ${myColor};">You:</b> ${escapeHTML(text)}`);
                input.value = '';
            }
        }

        function leaveMatch() {
            // Drop back out to your own custom solo room base
            setupRoomChannels(myShortId);
        }

        function appendLog(htmlContent) {
            const log = document.getElementById('chat-log');
            log.innerHTML += "<div>" + htmlContent + "</div>";
            log.scrollTop = log.scrollHeight; 
        }

        function escapeHTML(str) {
            return str.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
        }

        function jump() {
            if (!p1.isDead) {
                if (document.getElementById('infJump').checked || !p1.isJumping) {
                    p1.vy = -10;
                    p1.isJumping = true;
                }
            }
        }
        
        window.addEventListener('keydown', e => { 
            if(document.activeElement.tagName === 'INPUT') return;
            if(e.code === 'Space') jump(); 
        });
        document.getElementById('touch-pad').addEventListener('touchstart', (e) => { e.preventDefault(); jump(); });
        document.getElementById('touch-pad').addEventListener('mousedown', jump);

        function respawnMe() {
            p1.isDead = false;
            p1.y = 110; p1.vy = 0; p1.isJumping = false; 
            p1.score = 0;            
            lastScoreTick = Date.now(); 
            document.getElementById('respawnBtn').style.display = 'none';

            if (isHost()) {
                cactus.x = 600;
                cactus.speed = parseFloat(document.getElementById('gameSpeed').value) || 5;
            }
        }

        function loop() {
            const currentNameSetting = document.getElementById('username-input').value.trim() || "Player";
            
            p1.vy += 0.6; p1.y += p1.vy;
            if (p1.y > 110) { p1.y = 110; p1.vy = 0; p1.isJumping = false; }

            if (!p1.isDead) {
                if (p1.x < cactus.x + cactus.width && p1.x + 25 > cactus.x &&
                    p1.y < cactus.y + cactus.height && p1.y + 25 > cactus.y && 
                    !document.getElementById('godMode').checked) {
                        p1.isDead = true;
                        document.getElementById('respawnBtn').style.display = 'inline-block';
                }

                let currentTime = Date.now();
                if (currentTime - lastScoreTick >= 100) {
                    p1.score++;
                    lastScoreTick = currentTime;
                }
            }

            if (isHost()) {
                let targetSpeed = parseFloat(document.getElementById('gameSpeed').value) || 5;
                if(cactus.speed !== targetSpeed && !p1.isDead) {
                    cactus.speed = targetSpeed;
                }
                
                cactus.x -= cactus.speed;
                if (cactus.x < -20) { 
                    cactus.x = 600; 
                    let currentSetting = parseFloat(document.getElementById('gameSpeed').value) || 5;
                    document.getElementById('gameSpeed').value = (currentSetting + 0.2).toFixed(1);
                    changeSpeed(document.getElementById('gameSpeed').value);
                }
            }

            // Cleanup timeouts
            let now = Date.now();
            Object.keys(players).forEach(id => {
                if (now - players[id].lastSeen > 3000) {
                    delete players[id];
                    updateLobbyCount();
                }
            });

            // Network synchronization transmit
            if (currentRoom && isServerConnected) {
                mqttClient.publish('dino_arena/' + currentRoom, JSON.stringify({
                    type: 'update',
                    id: myShortId,
                    name: currentNameSetting,
                    y: p1.y,
                    isDead: p1.isDead,
                    score: p1.score,
                    color: myColor,
                    isHostPlayer: isHost(),
                    cx: cactus.x,
                    speed: cactus.speed
                }));
            }

            // RENDER CANVAS
            ctx.clearRect(0, 0, 600, 150);
            ctx.fillStyle = '#6b7280'; ctx.fillRect(0, 135, 600, 2); 
            
            // Draw You
            ctx.fillStyle = p1.isDead ? '#9ca3af' : p1.color; 
            ctx.fillRect(p1.x, p1.y, 25, 25); 
            ctx.fillStyle = '#374151';
            ctx.font = '10px sans-serif';
            ctx.fillText(currentNameSetting, p1.x - 5, p1.y - 8);

            // Draw Friend
            Object.keys(players).forEach(id => {
                let opp = players[id];
                ctx.fillStyle = opp.isDead ? '#d1d5db' : opp.color;
                ctx.fillRect(opp.x, opp.y, 25, 25);
                ctx.fillStyle = '#374151';
                ctx.fillText(opp.name, opp.x - 5, opp.y - 8);
            });
            
            ctx.fillStyle = '#15803d'; ctx.fillRect(cactus.x, cactus.y, cactus.width, cactus.height); 
            
            ctx.fillStyle = '#374151'; 
            ctx.font = 'bold 11px sans-serif'; 
            let scoreString = `${currentNameSetting}: ${p1.score}`;
            Object.keys(players).forEach(id => {
                scoreString += `  |  ${players[id].name}: ${players[id].score}`;
            });
            ctx.fillText(scoreString, 10, 20);

            requestAnimationFrame(loop);
        }
        loop();
    </script>
</body>
</html>
