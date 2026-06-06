<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>6-Player Dino Arena (Mobile Video Fix)</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mqtt/5.2.2/mqtt.min.js"></script>
    <style>
        * { box-sizing: border-box; user-select: none; -webkit-user-select: none; }
        body { font-family: sans-serif; text-align: center; background: #f0f2f5; margin: 0; padding: 10px; }
        canvas { background: #ffffff; border: 2px solid #333; border-radius: 6px; display: block; margin: 10px auto; width: 100%; max-width: 600px; height: auto; cursor: default; }
        .panel { background: white; padding: 15px; border-radius: 8px; max-width: 600px; margin: 0 auto 10px auto; box-shadow: 0 2px 4px rgba(0,0,0,0.1); }
        .btn { padding: 12px 15px; font-size: 14px; border: none; border-radius: 4px; font-weight: bold; cursor: pointer; color: white; margin: 5px; }
        .blue { background: #0070f3; } .green { background: #16a34a; } .red { background: #dc2626; } .orange { background: #ea580c; }
        input[type="text"], input[type="number"], select { padding: 11px; font-size: 14px; width: 90%; max-width: 240px; text-align: center; border: 1px solid #ccc; border-radius: 4px; margin: 5px 2px; user-select: text; -webkit-user-select: text; }
        #username-input { width: 80%; max-width: 200px; margin-bottom: 10px; font-weight: bold; border: 2px solid #0070f3; }
        #touch-pad { background: #222; color: white; width: 100%; max-width: 600px; margin: 10px auto; padding: 25px; font-size: 1.2rem; font-weight: bold; border-radius: 8px; touch-action: manipulation; }
        #status { font-weight: bold; color: #d97706; margin: 8px 0; }
        #lobby-count { font-size: 1.1rem; color: #7c3aed; font-weight: bold; margin: 5px 0; }
        .row { margin: 10px 0; display: flex; justify-content: center; align-items: center; gap: 10px; flex-wrap: wrap; }
        .group { border: 1px solid #e5e7eb; padding: 10px; border-radius: 6px; margin: 5px; background: #fafafa; }
        .cheats { margin-top: 8px; display: flex; gap: 15px; justify-content: center; align-items: center; font-size: 0.9rem; flex-wrap: wrap; }
        #gameSpeed { width: 60px; padding: 6px; margin-left: 4px; text-align: center; display: inline-block; }
        .chat-container { background: white; border-radius: 8px; max-width: 600px; margin: 10px auto; padding: 10px; box-shadow: 0 2px 4px rgba(0,0,0,0.1); text-align: left; }
        #chat-log { height: 100px; overflow-y: auto; border: 1px solid #ddd; padding: 8px; border-radius: 4px; background: #fafafa; font-size: 13px; margin-bottom: 8px; user-select: text; -webkit-user-select: text; }
        .chat-row { display: flex; }
        #chat-input { flex-grow: 1; padding: 10px; font-size: 14px; border: 1px solid #ccc; border-radius: 4px 0 0 4px; text-align: left; max-width: none; }
        #chat-send { border-radius: 0 4px 4px 0; margin: 0; padding: 10px 15px; background: #374151; }
        #build-tools { display: none; margin-top: 5px; font-weight: bold; background: #f3f4f6; padding: 8px; border-radius: 6px; border: 1px dashed #ccc; }
        .tool-select { cursor: pointer; padding: 5px 10px; border-radius: 4px; display: inline-block; margin: 0 5px; }
        .active-tool { background: #3b82f6; color: white; }
    </style>
</head>
<body>

    <h1>🦖 6-Player Dino Arena</h1>
    
    <div class="panel">
        <div class="row">
            <div class="group">
                <label style="font-weight: bold; display: block; margin-bottom: 3px;">✍️ Profile Name:</label>
                <input type="text" id="username-input" placeholder="Your Name" maxlength="12" oninput="saveMyName(this.value)">
            </div>
            
            <div class="group">
                <label style="font-weight: bold; display: block; margin-bottom: 3px;">🎭 Costume Engine:</label>
                <select id="costume-select" onchange="changeCostumePreset(this.value)">
                    <option value="none">Standard Dino</option>
                    <option value="ninja">Ninja Mask</option>
                    <option value="king">Crown King</option>
                    <option value="space">Astronaut</option>
                    <option value="custom">-- Use Custom URL Below --</option>
                </select>
                <input type="text" id="custom-costume-url" placeholder="Paste Image/Video (.mp4) URL" oninput="loadCustomCostumeAsset(this.value)">
            </div>
        </div>

        <div>Your Room ID: <strong id="my-id" style="color:#0070f3; font-size: 1.2rem;">...</strong></div>
        <div id="lobby-count">👥 Players on screen: 1 / 6</div>

        <div class="row">
            <button class="btn orange" id="buildModeBtn" onclick="toggleBuildMode()">🧱 Build Mode: OFF</button>
            
            <div class="group" style="display:inline-block; text-align:center;">
                <label style="font-weight: bold; display:block; margin-bottom:3px;">📹 Video Map URL Wallpaper:</label>
                <input type="text" id="map-video-url" placeholder="Paste background loop .mp4 URL" oninput="loadMapVideoWallpaper(this.value)">
                <select id="map-select" onchange="loadPresetMap(this.value)" style="width:auto; padding:6px; margin-top:5px;">
                    <option value="">-- Layout Presets --</option>
                    <option value="flat">Flat Field (Clear)</option>
                    <option value="gauntlet">The Gauntlet 🌵</option>
                    <option value="tower">High-Rise Tower 🧱</option>
                </select>
            </div>
        </div>

        <div id="build-tools">
            <span>Select Item:</span>
            <div id="tool-brick" class="tool-select active-tool" onclick="setBuildTool('brick')">🧱 Brick Block</div>
            <div id="tool-cactus" class="tool-select" onclick="setBuildTool('cactus')">🌵 Cactus Hazard</div>
        </div>

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

        let savedName = localStorage.getItem('dino_saved_name');
        if (savedName) {
            document.getElementById('username-input').value = savedName;
        } else {
            document.getElementById('username-input').value = "Player_" + myShortId.substring(0,3);
        }

        function saveMyName(val) {
            let cleanName = val.trim();
            if(cleanName) localStorage.setItem('dino_saved_name', cleanName);
        }

        let currentRoom = null;
        let mqttClient = null;
        let isServerConnected = false;

        const colorPalette = ['#0070f3', '#e11d48', '#16a34a', '#d97706', '#7c3aed', '#db2777'];
        let myColor = colorPalette[Math.floor(Math.random() * colorPalette.length)];

        let p1 = { id: myShortId, x: 40, y: 110, vy: 0, isJumping: false, isDead: false, color: myColor, score: 0, costume: 'none', assetUrl: '', customElement: null, isVideoAsset: false }; 
        let players = {}; 
        let cactus = { x: 600, y: 115, width: 15, height: 20, speed: 5 };
        
        let isBuildMode = false;
        let selectedTool = 'brick'; 
        let mapBlocks = []; 
        const GRID_SIZE = 25; 

        let bgVideoElement = null; 
        let currentMapVideoUrl = '';
        let lastScoreTick = Date.now();

        // Safe Video Wallpaper Loader
        function loadMapVideoWallpaper(url) {
            const cleanUrl = url.trim();
            currentMapVideoUrl = cleanUrl;
            if(!cleanUrl) {
                bgVideoElement = null;
                return;
            }
            try {
                bgVideoElement = document.createElement('video');
                bgVideoElement.src = cleanUrl;
                bgVideoElement.crossOrigin = "anonymous";
                bgVideoElement.loop = true;
                bgVideoElement.muted = true;
                bgVideoElement.playsInline = true;
                // Avoid breaking if user hasn't interacted yet
                let playPromise = bgVideoElement.play();
                if (playPromise !== undefined) {
                    playPromise.catch(e => console.log("Video background waiting for gesture..."));
                }
            } catch(e) {
                console.log("CORS background error caught safely.");
                bgVideoElement = null;
            }
        }

        function changeCostumePreset(val) {
            p1.costume = val;
            if(val !== 'custom') {
                p1.assetUrl = '';
                p1.customElement = null;
            } else {
                loadCustomCostumeAsset(document.getElementById('custom-costume-url').value);
            }
        }

        function loadCustomCostumeAsset(url) {
            const cleanUrl = url.trim();
            if(!cleanUrl) {
                p1.customElement = null;
                return;
            }
            p1.costume = 'custom';
            p1.assetUrl = cleanUrl;
            p1.isVideoAsset = cleanUrl.toLowerCase().endsWith('.mp4');

            try {
                if(p1.isVideoAsset) {
                    p1.customElement = document.createElement('video');
                    p1.customElement.src = cleanUrl;
                    p1.customElement.crossOrigin = "anonymous";
                    p1.customElement.loop = true;
                    p1.customElement.muted = true;
                    p1.customElement.playsInline = true;
                    let p = p1.customElement.play();
                    if(p !== undefined) p.catch(e => {});
                } else {
                    p1.customElement = new Image();
                    p1.customElement.src = cleanUrl;
                    p1.customElement.crossOrigin = "anonymous";
                }
            } catch(e) {
                p1.customElement = null;
            }
        }

        function verifyOpponentAsset(opp) {
            if (opp.costume === 'custom' && opp.assetUrl && !opp.customElement) {
                try {
                    const isVid = opp.assetUrl.toLowerCase().endsWith('.mp4');
                    if(isVid) {
                        opp.customElement = document.createElement('video');
                        opp.customElement.src = opp.assetUrl;
                        opp.customElement.crossOrigin = "anonymous";
                        opp.customElement.loop = true;
                        opp.customElement.muted = true;
                        opp.customElement.playsInline = true;
                        let p = opp.customElement.play();
                        if(p !== undefined) p.catch(e => {});
                    } else {
                        opp.customElement = new Image();
                        opp.customElement.src = opp.assetUrl;
                        opp.customElement.crossOrigin = "anonymous";
                    }
                } catch(e) {
                    opp.customElement = null;
                }
            }
        }

        function loadPresetMap(mapType) {
            if(!mapType) return;
            mapBlocks = []; 
            if(mapType === 'gauntlet') {
                mapBlocks.push({r: 5, c: 8, t: 'brick'}, {r: 5, c: 9, t: 'brick'});
                mapBlocks.push({r: 4, c: 13, t: 'brick'}, {r: 5, c: 13, t: 'cactus'});
                mapBlocks.push({r: 5, c: 17, t: 'cactus'}, {r: 5, c: 20, t: 'brick'});
            } else if (mapType === 'tower') {
                mapBlocks.push({r: 5, c: 6, t: 'brick'}, {r: 4, c: 9, t: 'brick'});
                mapBlocks.push({r: 3, c: 12, t: 'brick'}, {r: 2, c: 15, t: 'brick'});
                mapBlocks.push({r: 3, c: 18, t: 'brick'}, {r: 5, c: 21, t: 'brick'});
            }
            broadcastBlocks();
            document.getElementById('map-select').value = ""; 
        }

        try {
            if (typeof mqtt !== 'undefined') {
                mqttClient = mqtt.connect('wss://broker.hivemq.com:8884/mqtt', {
                    keepalive: 10, reconnectPeriod: 1000, connectTimeout: 5000
                });
                
                mqttClient.on('connect', () => {
                    isServerConnected = true;
                    document.getElementById('status').innerText = "✅ Network Connected! Ready.";
                    document.getElementById('status').style.color = "#16a34a";
                    setupRoomChannels(myShortId);
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
                            if (players[data.id]) delete players[data.id];
                            updateLobbyCount(); return;
                        }
                        if (data.type === 'mapSync') {
                            mapBlocks = data.blocks; 
                            if(data.mapVideoUrl !== undefined && data.mapVideoUrl !== currentMapVideoUrl) {
                                document.getElementById('map-video-url').value = data.mapVideoUrl;
                                loadMapVideoWallpaper(data.mapVideoUrl);
                            }
                            return;
                        }

                        if (data.type === 'update') {
                            let activeKeys = Object.keys(players);
                            if (!players[data.id]) activeKeys.push(data.id);
                            activeKeys.push(myShortId); activeKeys.sort();

                            p1.x = 40 + (activeKeys.indexOf(myShortId) * 45);
                            let layoutIndex = activeKeys.indexOf(data.id);

                            const prevElement = (players[data.id] && players[data.id].assetUrl === data.assetUrl) ? players[data.id].customElement : null;

                            players[data.id] = {
                                x: 40 + (layoutIndex * 45),
                                y: data.y,
                                isDead: data.isDead,
                                score: data.score,
                                color: data.color,
                                name: data.name,
                                costume: data.costume || 'none',
                                assetUrl: data.assetUrl || '',
                                customElement: prevElement,
                                lastSeen: Date.now()
                            };

                            verifyOpponentAsset(players[data.id]);
                            updateLobbyCount();

                            if (isHost()) {
                                if (data.speedSync) cactus.speed = data.speedSync;
                            } else {
                                if (data.isHostPlayer) {
                                    cactus.x = data.cx; cactus.speed = data.speed;
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

        function joinRoomCode() {
            const targetRoom = document.getElementById('friend-code').value.trim();
            if (!targetRoom) return alert("Type your friend's room ID first!");
            setupRoomChannels(targetRoom);
        }

        function setupRoomChannels(roomName) {
            if (!isServerConnected) return;
            if (currentRoom) {
                mqttClient.publish('dino_arena/' + currentRoom, JSON.stringify({ type: 'leave', id: myShortId }));
                mqttClient.unsubscribe('dino_arena/' + currentRoom);
            }
            currentRoom = roomName; players = {}; mapBlocks = [];
            p1.x = 40; p1.score = 0; p1.isDead = false;
            document.getElementById('respawnBtn').style.display = 'none';
            mqttClient.subscribe('dino_arena/' + currentRoom);
            updateLobbyCount();
        }

        function isHost() {
            if (!currentRoom) return true;
            let activeIds = Object.keys(players); activeIds.push(myShortId); activeIds.sort();
            return activeIds[0] === myShortId;
        }

        function changeSpeed(val) {
            let num = parseFloat(val);
            if (!isNaN(num) && num >= 1) cactus.speed = num;
        }

        function toggleBuildMode() {
            isBuildMode = !isBuildMode;
            const btn = document.getElementById('buildModeBtn');
            const toolsMenu = document.getElementById('build-tools');
            if(isBuildMode) {
                btn.innerText = "🧱 Build Mode: ON"; btn.style.background = "#16a34a"; toolsMenu.style.display = "block";
            } else {
                btn.innerText = "🧱 Build Mode: OFF"; btn.style.background = "#ea580c"; toolsMenu.style.display = "none";
            }
        }

        function setBuildTool(tool) {
            selectedTool = tool;
            document.getElementById('tool-brick').classList.remove('active-tool');
            document.getElementById('tool-cactus').classList.remove('active-tool');
            if(tool === 'brick') document.getElementById('tool-brick').classList.add('active-tool');
            if(tool === 'cactus') document.getElementById('tool-cactus').classList.add('active-tool');
        }

        function broadcastBlocks() {
            if (currentRoom && isServerConnected) {
                mqttClient.publish('dino_arena/' + currentRoom, JSON.stringify({
                    type: 'mapSync', id: myShortId, blocks: mapBlocks, mapVideoUrl: currentMapVideoUrl
                }));
            }
        }

        canvas.addEventListener('mousedown', function(e) {
            if(!isBuildMode) return;
            const rect = canvas.getBoundingClientRect();
            const scaleX = canvas.width / rect.width; const scaleY = canvas.height / rect.height;
      
