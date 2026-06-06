<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>6-Player Dino Arena (Secure Save Edition)</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mqtt/5.2.2/mqtt.min.js"></script>
    <style>
        * { box-sizing: border-box; user-select: none; -webkit-user-select: none; }
        body { font-family: sans-serif; text-align: center; background: #f0f2f5; margin: 0; padding: 10px; }
        canvas { background: #ffffff; border: 2px solid #333; border-radius: 6px; display: block; margin: 10px auto; width: 100%; max-width: 600px; height: auto; cursor: default; }
        .panel { background: white; padding: 15px; border-radius: 8px; max-width: 600px; margin: 0 auto 10px auto; box-shadow: 0 2px 4px rgba(0,0,0,0.1); }
        .btn { padding: 12px 15px; font-size: 14px; border: none; border-radius: 4px; font-weight: bold; cursor: pointer; color: white; margin: 5px; }
        .blue { background: #0070f3; } .green { background: #16a34a; } .red { background: #dc2626; } .orange { background: #ea580c; } .charcoal { background: #4b5563; }
        input[type="text"], input[type="number"], select { padding: 11px; font-size: 14px; width: 90%; max-width: 240px; text-align: center; border: 1px solid #ccc; border-radius: 4px; margin: 5px 2px; user-select: text; -webkit-user-select: text; }
        input[type="file"] { display: none; }
        .file-label { display: inline-block; background: #4b5563; color: white; padding: 8px 12px; font-size: 12px; border-radius: 4px; font-weight: bold; cursor: pointer; margin-top: 5px; }
        #username-input { width: 80%; max-width: 200px; margin-bottom: 10px; font-weight: bold; border: 2px solid #0070f3; }
        #touch-pad { background: #222; color: white; width: 100%; max-width: 600px; margin: 10px auto; padding: 25px; font-size: 1.2rem; font-weight: bold; border-radius: 8px; touch-action: manipulation; }
        #status { font-weight: bold; color: #d97706; margin: 8px 0; }
        #lobby-count { font-size: 1.1rem; color: #7c3aed; font-weight: bold; margin: 5px 0; }
        .row { margin: 10px 0; display: flex; justify-content: center; align-items: center; gap: 10px; flex-wrap: wrap; }
        .group { border: 1px solid #e5e7eb; padding: 10px; border-radius: 6px; margin: 5px; background: #fafafa; display: flex; flex-direction: column; align-items: center; }
        .cheats { margin-top: 8px; display: flex; gap: 15px; justify-content: center; align-items: center; font-size: 0.9rem; flex-wrap: wrap; }
        #gameSpeed { width: 60px; padding: 6px; margin-left: 4px; text-align: center; display: inline-block; }
        .chat-container { background: white; border-radius: 8px; max-width: 600px; margin: 10px auto; padding: 10px; box-shadow: 0 2px 4px rgba(0,0,0,0.1); text-align: left; }
        #chat-log { height: 100px; overflow-y: auto; border: 1px solid #ddd; padding: 8px; border-radius: 4px; background: #fafafa; font-size: 13px; margin-bottom: 8px; user-select: text; -webkit-user-select: text; }
        .chat-row { display: flex; }
        #chat-input { flex-grow: 1; padding: 10px; font-size: 14px; border: 1px solid #ccc; border-radius: 4px 0 0 4px; text-align: left; max-width: none; }
        #chat-send { border-radius: 0 4px 4px 0; margin: 0; padding: 10px 15px; background: #374151; }
        #build-tools { display: none; margin-top: 5px; font-weight: bold; background: #f3f4f6; padding: 8px; border-radius: 6px; border: 1px dashed #ccc; }
        .tool-select { cursor: pointer; padding: 5px 10px; border-radius: 4px; display: inline-block; margin: 0 5px; font-size: 13px; }
        .active-tool { background: #3b82f6; color: white; }

        .modal-overlay { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.6); z-index: 9999; justify-content: center; align-items: center; padding: 20px; }
        .modal-box { background: white; padding: 20px; border-radius: 10px; max-width: 400px; width: 100%; box-shadow: 0 4px 12px rgba(0,0,0,0.3); animation: popIn 0.2s ease-out; }
        @keyframes popIn { from { transform: scale(0.9); opacity: 0; } to { transform: scale(1); opacity: 1; } }
        .modal-box h3 { margin-top: 0; color: #dc2626; font-size: 1.3rem; }
        .modal-box p { color: #4b5563; font-size: 14px; line-height: 1.5; }
        .modal-actions { margin-top: 20px; display: flex; justify-content: center; gap: 10px; }
        #cmd-box-container { display: none; margin-top: 8px; background: #1e1e1e; padding: 10px; border-radius: 6px; border: 1px solid #333; text-align: left; }
        #custom-block-cmd { font-family: monospace; font-size: 12px; color: #00ff00; background: black; width: 100%; border: none; padding: 6px; text-align: left; margin-bottom: 8px; }
        .prompt-generator { border-top: 1px dashed #555; margin-top: 8px; padding-top: 8px; }
        .prompt-btn { background: #7c3aed; color: white; border: none; padding: 6px 10px; border-radius: 4px; font-size: 11px; cursor: pointer; font-weight: bold; margin-top: 4px; }
        #ai-ideas-select { font-size: 12px; padding: 4px; width: auto; max-width: 100%; margin-top: 4px; }
        .block-pic-setup { margin-bottom: 10px; padding-bottom: 8px; border-bottom: 1px dashed #555; }
    </style>
</head>
<body>

    <h1>🦖 6-Player Dino Arena</h1>
    
    <div class="panel">
        <div class="row">
            <div class="group">
                <label style="font-weight: bold; margin-bottom: 3px;">✍️ Profile Name:</label>
                <input type="text" id="username-input" placeholder="Your Name" maxlength="12" oninput="saveMyName(this.value)">
            </div>
            
            <div class="group">
                <label style="font-weight: bold; margin-bottom: 3px;">🎭 Costume Engine / Block Texture:</label>
                <select id="costume-select" onchange="changeCostumePreset(this.value)">
                    <option value="none">Standard Dino</option>
                    <option value="ninja">Ninja Mask</option>
                    <option value="king">Crown King</option>
                    <option value="space">Astronaut</option>
                    <option value="custom">-- Custom File / URL --</option>
                </select>
                <input type="text" id="custom-costume-url" placeholder="Paste Web URL image link" oninput="loadCustomCostumeAsset(this.value)">
                
                <label class="file-label costume-lbl" for="costume-file-input">📁 Open File Browser</label>
                <input type="file" id="costume-file-input" accept="image/*,video/mp4" onchange="handleCostumeFile(this)">
            </div>
        </div>

        <div>Your Room ID: <strong id="my-id" style="color:#0070f3; font-size: 1.2rem;">...</strong></div>
        <div id="lobby-count">👥 Players on screen: 1 / 6</div>

        <div class="row">
            <button class="btn orange" id="buildModeBtn" onclick="toggleBuildMode()">🧱 Build Mode: OFF</button>
            
            <div class="group" style="text-align:center;">
                <label style="font-weight: bold; margin-bottom:3px;">📹 Video Map Wallpaper:</label>
                <input type="text" id="map-video-url" placeholder="Paste Web URL link" oninput="loadMapVideoWallpaper(this.value)">
                
                <label class="file-label map-lbl" for="map-file-input">📁 Open File Browser</label>
                <input type="file" id="map-file-input" accept="video/mp4" onchange="handleMapFile(this)">
                
                <select id="map-select" onchange="loadPresetMap(this.value)" style="width:auto; padding:6px; margin-top:5px;">
                    <option value="">-- Layout Presets --</option>
                    <option value="flat">Flat Field (Clear)</option>
                    <option value="gauntlet">The Gauntlet 🌵</option>
                    <option value="tower">High-Rise Tower 🧱</option>
                </select>
            </div>
        </div>

        <div id="build-tools">
            <div>
                <span>Select Item:</span>
                <div id="tool-brick" class="tool-select active-tool" onclick="setBuildTool('brick')">🧱 Brick</div>
                <div id="tool-cactus" class="tool-select" onclick="setBuildTool('cactus')">🌵 Cactus</div>
                <div id="tool-custom" class="tool-select" onclick="setBuildTool('custom')">🖼️ Custom Block</div>
                <button class="btn charcoal" style="padding: 4px 8px; font-size:11px; margin-left:10px;" onclick="openClearModal()">💥 Clear World Storage</button>
            </div>
            
            <div id="cmd-box-container">
                <div class="block-pic-setup">
                    <label style="color: #60a5fa; font-size: 11px; display:block; font-weight:bold; margin-bottom:4px;">🖼️ STEP 1: Add Custom Block Picture (REQUIRED):</label>
                    <input type="text" id="custom-block-pic-url" placeholder="Paste Custom Block Image URL" style="width:100%; max-width:none; padding:6px; margin-bottom:5px; background:#2d2d2d; color:#fff; border:1px solid #444;" oninput="updateBlockPicUrl(this.value)">
                    <label class="file-label" for="block-pic-file-input" style="background:#2563eb; padding:6px 10px; font-size:11px; margin:0;">📁 Upload Picture from Device</label>
                    <input type="file" id="block-pic-file-input" accept="image/*" onchange="handleBlockPicFile(this)">
                    <span id="block-pic-status" style="color:#ef4444; font-size:11px; margin-left:8px; display:inline-block;">❌ No image loaded</span>
                </div>

                <label style="color: #aaa; font-size: 11px; display:block; text-align:left; margin-bottom:4px; font-family: monospace;">⚙️ STEP 2: Custom Block Trigger Script (CMD Code):</label>
                <input type="text" id="custom-block-cmd" placeholder="e.g. p1.vy = -18; or p1.score += 500;" value="p1.vy = -15;">
                
                <div class="prompt-generator">
                    <label style="color: #eab308; font-size: 11px; display:block; font-weight:bold; margin-bottom:2px;">🤖 Quick Prompt Presets Engine:</label>
                    <select id="ai-ideas-select" onchange="generatePresetPrompt(this.value)">
                        <option value="">-- Select custom action preset --</option>
                        <option value="highjump">🚀 Super Launchpad High Jump</option>
                        <option value="points">💰 Add 500 Score Points</option>
                        <option value="god">🛡️ Auto-Activate God Mode</option>
                        <option value="slow">🛑 Extreme Slow Motion</option>
                        <option value="speed">⚡ Max Running Velocity Boost</option>
                        <option value="trap">💀 Secret Invisible Death Trap</option>
                    </select>
                    <button class="prompt-btn" onclick="copyPromptToClipboard()">📋 Copy Prompt To Clipboard for AI</button>
                </div>
            </div>
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

    <div id="clearModal" class="modal-overlay">
        <div class="modal-box">
            <h3>⚠️ Are you absolutely sure?</h3>
            <p>This action will permanently erase your entire custom-built map layout from your device's memory. This cannot be undone!</p>
            <div class="modal-actions">
                <button class="btn charcoal" onclick="closeClearModal()">No, Keep My Map</button>
                <button class="btn red" onclick="confirmClearWorld()">Yes, Delete Everything</button>
            </div>
        </div>
    </div>

    <script>
        function detectDeviceAndAdjustUI() {
            const ua = navigator.userAgent.toLowerCase();
            const isMobile = /android|webos|iphone|ipad|ipod|blackberry|iemobile|opera mini/i.test(ua);
            const costumeLabels = document.querySelectorAll('.costume-lbl');
            const mapLabels = document.querySelectorAll('.map-lbl');
            if (isMobile) {
                costumeLabels.forEach(el => el.innerText = "📁 Open Google Files / Pic");
                mapLabels.forEach(el => el.innerText = "📁 Open Google Files / Video");
            } else {
                costumeLabels.forEach(el => el.innerText = "📁 Open Windows Explorer / Pic");
                mapLabels.forEach(el => el.innerText = "📁 Open Windows Explorer / Video");
            }
        }
        detectDeviceAndAdjustUI();

        const canvas = document.getElementById('game');
        const ctx = canvas.getContext('2d');
        
        const myShortId = Math.floor(10000 + Math.random() * 90000).toString();
        document.getElementById('my-id').innerText = myShortId;

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
        let worldXOffset = 0; 

        let bgVideoElement = null; 
        let currentMapVideoUrl = '';
        let lastScoreTick = Date.now();

        let currentBlockImageSource = ''; 

        let savedName = localStorage.getItem('dino_saved_name');
        if (savedName) document.getElementById('username-input').value = savedName;
        else document.getElementById('username-input').value = "Player_" + myShortId.substring(0,3);

        let savedCostumeType = localStorage.getItem('dino_saved_costume_type') || 'none';
        let savedCostumeUrl = localStorage.getItem('dino_saved_costume_url') || '';
        document.getElementById('costume-select').value = savedCostumeType;
        document.getElementById('custom-costume-url').value = savedCostumeUrl;
        
        if (savedCostumeType !== 'none') {
            p1.costume = savedCostumeType;
            if (savedCostumeType === 'custom' && savedCostumeUrl) {
                loadCustomCostumeAsset(savedCostumeUrl);
            }
        }

        let savedWallpaperUrl = localStorage.getItem('dino_saved_wallpaper_url') || '';
        if (savedWallpaperUrl) {
            document.getElementById('map-video-url').value = savedWallpaperUrl;
            loadMapVideoWallpaper(savedWallpaperUrl);
        }

        let savedBlocksJson = localStorage.getItem('dino_saved_map_blocks');
        if (savedBlocksJson) {
            try { mapBlocks = JSON.parse(savedBlocksJson); } catch(e) { mapBlocks = []; }
        }

        function saveMyName(val) {
            let cleanName = val.trim();
            if(cleanName) localStorage.setItem('dino_saved_name', cleanName);
        }

        function saveWorldLayout() {
            localStorage.setItem('dino_saved_map_blocks', JSON.stringify(mapBlocks));
        }

        function openClearModal() {
            document.getElementById('clearModal').style.display = 'flex';
        }

        function closeClearModal() {
            document.getElementById('clearModal').style.display = 'none';
        }

        function confirmClearWorld() {
            mapBlocks = [];
            saveWorldLayout();
            broadcastBlocks();
            closeClearModal();
        }

        function handleMapFile(input) {
            const file = input.files[0];
            if (!file) return;
            const objectURL = URL.createObjectURL(file);
            currentMapVideoUrl = "local_blob"; 
            try {
                let tempVideo = document.createElement('video');
                tempVideo.src = objectURL;
                tempVideo.loop = true;
                tempVideo.muted = true;
                tempVideo.playsInline = true;
                tempVideo.oncanplay = function() {
                    bgVideoElement = tempVideo; 
                    let p = bgVideoElement.play();
                    if(p !== undefined) p.catch(() => {});
                };
            } catch(e) { bgVideoElement = null; }
        }

        function handleCostumeFile(input) {
            const file = input.files[0];
            if (!file) return;
            p1.costume = 'custom';
            p1.assetUrl = 'local_blob';
            p1.isVideoAsset = file.type.startsWith('video/');
            const objectURL = URL.createObjectURL(file);
            try {
                if(p1.isVideoAsset) {
                    let tempVid = document.createElement('video');
                    tempVid.src = objectURL;
                    tempVid.loop = true;
                    tempVid.muted = true;
                    tempVid.playsInline = true;
                    tempVid.oncanplay = function() {
                        p1.customElement = tempVid;
                        let p = tempVid.play();
                        if(p !== undefined) p.catch(() => {});
                    };
                } else {
                    let tempImg = new Image();
                    tempImg.src = objectURL;
                    tempImg.onload = function() { p1.customElement = tempImg; };
                }
            } catch(e) { p1.customElement = null; }
            document.getElementById('costume-select').value = 'custom';
            localStorage.setItem('dino_saved_costume_type', 'custom');
            localStorage.setItem('dino_saved_costume_url', '');
        }

        function updateBlockPicUrl(val) {
            const clean = val.trim();
            currentBlockImageSource = clean;
            const statusLabel = document.getElementById('block-pic-status');
            if (clean) {
                statusLabel.innerText = "✅ Link Ready";
                statusLabel.style.color = "#10b981";
            } else {
                statusLabel.innerText = "❌ No image loaded";
                statusLabel.style.color = "#ef4444";
            }
        }

        function handleBlockPicFile(input) {
            const file = input.files[0];
            if (!file) return;
            const objectURL = URL.createObjectURL(file);
            currentBlockImageSource = objectURL;
            const statusLabel = document.getElementById('block-pic-status');
            statusLabel.innerText = "✅ Picture Uploaded";
            statusLabel.style.color = "#10b981";
        }

        function loadMapVideoWallpaper(url) {
            const cleanUrl = url.trim();
            currentMapVideoUrl = cleanUrl;
            localStorage.setItem('dino_saved_wallpaper_url', cleanUrl);
            if(!cleanUrl) { bgVideoElement = null; return; }
            try {
                let tempVideo = document.createElement('video');
                tempVideo.src = cleanUrl;
                tempVideo.crossOrigin = "anonymous";
                tempVideo.loop = true;
                tempVideo.muted = true;
                tempVideo.playsInline = true;
                tempVideo.oncanplay = function() {
                    bgVideoElement = tempVideo; 
                    let p = bgVideoElement.play();
                    if(p !== undefined) p.catch(() => {});
                };
            } catch(e) { bgVideoElement = null; }
        }

        function changeCostumePreset(val) {
            p1.costume = val;
            localStorage.setItem('dino_saved_costume_type', val);
            if(val !== 'custom') {
                p1.assetUrl = '';
                p1.customElement = null;
                localStorage.setItem('dino_saved_costume_url', '');
            } else {
                loadCustomCostumeAsset(document.getElementById('custom-costume-url').value);
            }
        }

        function loadCustomCostumeAsset(url) {
            const cleanUrl = url.trim();
            localStorage.setItem('dino_saved_costume_url', cleanUrl);
            if(!cleanUrl) { p1.customElement = null; return; }
            p1.costume = 'custom';
            p1.assetUrl = cleanUrl;
            p1.isVideoAsset = cleanUrl.toLowerCase().endsWith('.mp4');
            try {
                if(p1.isVideoAsset) {
                    let tempVid = document.createElement('video');
                    tempVid.src = cleanUrl;
                    tempVid.crossOrigin = "anonymous";
                    tempVid.loop = true;
                    tempVid.muted = true;
                    tempVid.playsInline = true;
                    tempVid.oncanplay = function() {
                        p1.customElement = tempVid;
                        let p = tempVid.play();
                        if(p !== undefined) p.catch(() => {});
                    };
                } else {
                    let tempImg = new Image();
                    tempImg.src = cleanUrl;
                    tempImg.crossOrigin = "anonymous";
                    tempImg.onload = function() { p1.customElement = tempImg; };
                }
            } catch(e) { p1.customElement = null; }
        }

        function verifyOpponentAsset(opp) {
            if (opp.costume === 'custom' && opp.assetUrl && opp.assetUrl !== 'local_blob' && !opp.customElement) {
                try {
                    const isVid = opp.assetUrl.toLowerCase().endsWith('.mp4');
                    if(isVid) {
                        let tempVid = document.createElement('video');
                        tempVid.src = opp.assetUrl;
                        tempVid.crossOrigin = "anonymous";
                        tempVid.loop = true;
                        tempVid.muted = true;
                        tempVid.playsInline = true;
                        tempVid.oncanplay = function() {
                            opp.customElement = tempVid;
                            let p = tempVid.play();
                            if(p !== undefined) p.catch(() => {});
                        };
                    } else {
                        let tempImg = new Image();
                        tempImg.src = opp.assetUrl;
                        tempImg.crossOrigin = "anonymous";
                        tempImg.onload = function() { opp.customElement = tempImg; };
                    }
                } catch(e) { opp.customElement = null; }
            }
        }

        function loadPresetMap(mapType) {
            if(!mapType) return;
            mapBlocks = []; 
            let baseln = Math.floor(worldXOffset / GRID_SIZE);
            if(mapType === 'gauntlet') {
                mapBlocks.push({r: 5, c: baseln + 8, t: 'brick'}, {r: 5, c: baseln + 9, t: 'brick'});
                mapBlocks.push({r: 4, c: baseln + 13, t: 'brick'}, {r: 5, c: baseln + 13, t: 'cactus'});
                mapBlocks.push({r: 5, c: baseln + 17, t: 'cactus'}, {r: 5, c: baseln + 20, t: 'brick'});
            } else if (mapType === 'tower') {
                mapBlocks.push({r: 5, c: baseln + 6, t: 'brick'}, {r: 4, c: baseln + 9, t: 'brick'});
                mapBlocks.push({r: 3, c: baseln + 12, t: 'brick'}, {r: 2, c: baseln + 15, t: 'brick'});
                mapBlocks.push({r: 3, c: baseln + 18, t: 'brick'}, {r: 5, c: baseln + 21, t: 'brick'});
            }
            saveWorldLayout();
            broadcastBlocks();
            document.getElementById('map-select').value = ""; 
        }

        function generatePresetPrompt(type) {
            const cmdInput = document.getElementById('custom-block-cmd');
            if (type === 'highjump') cmdInput.value = "p1.vy = -18;";
            if (type === 'points') cmdInput.value = "p1.score += 500;";
            if (type === 'god') cmdInput.value = "document.getElementById('godMode').checked = true;";
            if (type === 'slow') cmdInput.value = "document.getElementById('gameSpeed').value = 1; changeSpeed(1);";
            if (type === 'speed') cmdInput.value = "document.getElementById('gameSpeed').value = 12; changeSpeed(12);";
            if (type === 'trap') cmdInput.value = "if(!document.getElementById('godMode').checked) { p1.isDead = true; document.getElementById('respawnBtn').style.display = 'inline-block'; }";
        }

        function copyPromptToClipboard() {
            const selectedText = document.getElementById('ai-ideas-select').options[document.getElementById('ai-ideas-select').selectedIndex].text;
            const fullPrompt = `I am making a JavaScript HTML5 canvas platformer game. My player object is named "p1". 

Player features:
- p1.vy (vertical velocity number, negative values move up)
- p1.y (vertical screen coordinate position)
- p1.score (player score integer score data)
- p1.isDead (boolean layout tracker)

Please write a short, one-line JavaScript snippet built for this system to accomplish this custom action block feature rule: "${selectedText}". Return only the clean code line string text data.`;
            
            navigator.clipboard.writeText(fullPrompt).then(() => {
                alert("📋 AI prompt template copied! Paste it directly into your AI browser chat tab.");
            }).catch(() => {
                alert("Failed to copy automatically. Copy the input code structure manual text form.");
            });
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
                            saveWorldLayout(); 
                            if(data.mapVideoUrl !== undefined && data.mapVideoUrl !== currentMapVideoUrl && data.mapVideoUrl !== 'local_blob') {
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
            currentRoom = roomName; players = {}; 
            let savedMap = localStorage.getItem('dino_saved_map_blocks');
            if(savedMap) { try { mapBlocks = JSON.parse(savedMap); } catch(e) {} } else { mapBlocks = []; }
            worldXOffset = 0; p1.x = 40; p1.score = 0; p1.isDead = false;
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
            document.getElementById('tool-custom').classList.remove('active-tool');
            
            const cmdContainer = document.getElementById('cmd-box-container');
            if (tool === 'custom') {
                cmdContainer.style.display = "block";
            } else {
                cmdContainer.style.display = "none";
            }

            if(tool === 'brick') document.getElementById('tool-brick').classList.add('active-tool');
            if(tool === 'cactus') document.getElementById('tool-cactus').classList.add('active-tool');
            if(tool === 'custom') document.getElementById('tool-custom').classList.add('active-tool');
        }

        function broadcastBlocks() {
            if (currentRoom && isServerConnected) {
                mqttClient.publish('dino_arena/' + currentRoom, JSON.stringify({
                    type: 'mapSync', id: myShortId, blocks: mapBlocks, mapVideoUrl: (currentMapVideoUrl === 'local_blob' ? '' : currentMapVideoUrl)
                }));
            }
        }

        canvas.addEventListener('mousedown', function(e) {
            if(!isBuildMode) return;
            const rect = canvas.getBoundingClientRect();
            const scaleX = canvas.width / rect.width; const scaleY = canvas.height / rect.height;
            const clickX = (e.clientX - rect.left) * scaleX; const clickY = (e.clientY - rect.top) * scaleY;
            const row = Math.floor(clickY / GRID_SIZE);
            if(clickX < 80 && row > 3) return; 

            let targetIndex = -1;
            for (let i = 0; i < mapBlocks.length; i++) {
                let b = mapBlocks[i];
                if (b.r === row) {
                    let bx = (b.c * GRID_SIZE) - worldXOffset;
                    while (bx < -GRID_SIZE) { bx += 700; }
                    if (clickX >= bx && clickX < bx + GRID_SIZE) {
                        targetIndex = i;
                        break;
                    }
                }
            }

            if (targetIndex > -1) {
                mapBlocks.splice(targetIndex, 1);
            } else {
                // If trying to place a custom block, force them to input a picture first
                if(selectedTool === 'custom') {
                    if (!currentBlockImageSource || currentBlockImageSource.trim() === "") {
                        alert("⚠️ ERROR: You MUST upload a picture file or paste an image URL first before you can add a Custom Block onto the map!");
                        return;
                    }
                    let textUrl = currentBlockImageSource;
                    let commandCode = document.getElementById('custom-block-cmd').value;
                    mapBlocks.push({ r: row, c: col = Math.floor((clickX + worldXOffset) / GRID_SIZE), t: 'custom', url: textUrl, cmd: commandCode });
                } else {
                    const targetWorldX = clickX + worldXOffset;
                    const col = Math.floor(targetWorldX / GRID_SIZE); 
                    mapBlocks.push({ r: row, c: col, t: selectedTool });
                }
            }
            saveWorldLayout(); 
            broadcastBlocks();
        });

        function drawPlayerWithCostume(x, y, color, costumeType, name, isDeadPlayer, assetElement) {
            let successfullyDrawn = false;
            if (costumeType === 'custom' && assetElement) {
                try {
                    ctx.drawImage(assetElement, x, y, 25, 25);
                    if(isDeadPlayer) {
                        ctx.fillStyle = "rgba(156, 163, 175, 0.6)"; 
                        ctx.fillRect(x, y, 25, 25);
                    }
                    successfullyDrawn = true;
                } catch(err) { successfullyDrawn = false; }
            } 
            if (!successfullyDrawn) {
                ctx.fillStyle = isDeadPlayer ? '#9ca3af' : color; 
                ctx.fillRect(x, y, 25, 25); 
                if(!isDeadPlayer) {
                    if(costumeType === 'ninja') {
                        ctx.fillStyle = '#111827'; ctx.fillRect(x, y + 4, 25, 7);
                        ctx.fillStyle = '#ffffff'; ctx.fillRect(x + 14, y + 6, 4, 3);
                    } else if(costumeType === 'king') {
                        ctx.fillStyle = '#eab308'; ctx.beginPath();
                        ctx.moveTo(x, y); ctx.lineTo(x + 4, y - 8); ctx.lineTo(x + 10, y - 3);
                        ctx.lineTo(x + 15, y - 8); ctx.lineTo(x + 21, y - 3); ctx.lineTo(x + 25, y);
                        ctx.closePath(); ctx.fill();
                    } else if(costumeType === 'space') {
                        ctx.strokeStyle = '#e5e7eb'; ctx.lineWidth = 2; ctx.strokeRect(x - 2, y - 2, 29, 29);
                        ctx.fillStyle = '#38bdf8'; ctx.fillRect(x + 10, y + 4, 12, 10);
                    }
                }
            }
            ctx.fillStyle = '#374151'; ctx.font = '10px sans-serif'; ctx.fillText(name, x - 5, y - 8);
        }

        function sendChat() {
            const input = document.getElementById('chat-input'); const text = input.value.trim();
            const uName = document.getElementById('username-input').value.trim() || "Player";
            if (!text) return;
            if (currentRoom && isServerConnected) {
                mqttClient.publish('dino_arena/' + currentRoom, JSON.stringify({ type: 'chat', id: myShortId, name: uName, color: myColor, text: text }));
                appendLog(`<b style="color: ${myColor};">You:</b> ${escapeHTML(text)}`); input.value = '';
            }
        }

        function leaveMatch() { setupRoomChannels(myShortId); }
        function appendLog(htmlContent) {
            const log = document.getElementById('chat-log'); log.innerHTML += "<div>" + htmlContent + "</div>"; log.scrollTop = log.scrollHeight; 
        }
        function escapeHTML(str) { return str.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;"); }

        function jump() {
            if (!p1.isDead && !isBuildMode) {
                if (document.getElementById('infJump').checked || !p1.isJumping) { p1.vy = -10; p1.isJumping = true; }
            }
        }
        window.addEventListener('keydown', e => { 
            if(document.activeElement.tagName === 'INPUT') return;
            if(e.code === 'Space') jump(); 
        });
        document.getElementById('touch-pad').addEventListener('touchstart', (e) => { e.preventDefault(); jump(); });
        document.getElementById('touch-pad').addEventListener('mousedown', jump);

        function respawnMe() {
            p1.isDead = false; p1.y = 110; p1.vy = 0; p1.isJumping = false; p1.score = 0;            
            lastScoreTick = Date.now(); document.getElementById('respawnBtn').style.display = 'none';
            if (isHost()) { cactus.x = 600; cactus.speed = parseFloat(document.getElementById('gameSpeed').value) || 5; }
        }

        const blockTextureCache = {};

        function loop() {
            try {
                const currentNameSetting = document.getElementById('username-input').value.trim() || "Player";
                if (!isBuildMode) { p1.vy += 0.6; p1.y += p1.vy; }
                if (p1.y > 110) { p1.y = 110; p1.vy = 0; p1.isJumping = false; }

                if (!isBuildMode && !p1.isDead) {
                    let runSpeed = parseFloat(document.getElementById('gameSpeed').value) || 5;
                    worldXOffset += runSpeed;
                }

                mapBlocks.forEach(b => {
                    let bx = (b.c * GRID_SIZE) - worldXOffset;
                    while (bx < -GRID_SIZE) { bx += 700; } 
                    let by = b.r * GRID_SIZE;

                    if (p1.x + 25 > bx && p1.x < bx + GRID_SIZE) {
                        if (b.t === 'brick' || b.t === undefined || b.t === 'custom') {
                            if (p1.y + 25 >= by && p1.y + 25 - p1.vy <= by + 8 && p1.vy >= 0) {
                                p1.y = by - 25; p1.vy = 0; p1.isJumping = false;
                                
                                if (b.t === 'custom' && b.cmd && !isBuildMode && !p1.isDead) {
                                    try {
                                        let executeBlockFunction = new Function(b.cmd);
                                        executeBlockFunction();
                                    } catch(e) {}
                                }
                            } else if (p1.y + 25 > by && p1.y < by + GRID_SIZE) {
                                if(p1.x < bx) p1.x = bx - 25;
                            }
                        } else if (b.t === 'cactus') {
                            if (p1.y + 25 > by && p1.y < by + GRID_SIZE) {
                                if (!document.getElementById('godMode').checked && !p1.isDead && !isBuildMode) {
                                    p1.isDead = true; document.getElementById('respawnBtn').style.display = 'inline-block';
                                }
                            }
                        }
                    }
                });

                if (!p1.isDead && !isBuildMode) {
                    if (p1.x < cactus.x + cactus.width && p1.x + 25 > cactus.x &&
                        p1.y < cactus.y + cactus.height && p1.y + 25 > cactus.y && !document.getElementById('godMode').checked) {
                            p1.isDead = true; document.getElementById('respawnBtn').style.display = 'inline-block';
                    }
                    let currentTime = Date.now();
                    if (currentTime - lastScoreTick >= 100) { p1.score++; lastScoreTick = currentTime; }
                }

                if (isHost() && !isBuildMode) {
                    let targetSpeed = parseFloat(document.getElementById('gameSpeed').value) || 5;
                    if(cactus.speed !== targetSpeed && !p1.isDead) cactus.speed = targetSpeed;
                    cactus.x -= cactus.speed;
                    if (cactus.x < -20) { 
                        cactus.x = 600; 
                        let currentSetting = parseFloat(document.getElementById('gameSpeed').value) || 5;
                        document.getElementById('gameSpeed').value = (currentSetting + 0.2).toFixed(1);
                        changeSpeed(document.getElementById('gameSpeed').value);
                    }
                } else if (!isHost() && !isBuildMode) {
                    cactus.x -= (parseFloat(document.getElementById('gameSpeed').value) || 5);
                    if (cactus.x < -20) cactus.x = 600;
                }

                let now = Date.now();
                Object.keys(players).forEach(id => { if (now - players[id].lastSeen > 3000) delete players[id]; });

                if (currentRoom && isServerConnected) {
                    mqttClient.publish('dino_arena/' + currentRoom, JSON.stringify({
                        type: 'update', id: myShortId, name: currentNameSetting, y: p1.y, isDead: p1.isDead, score: p1.score, color: myColor, costume: p1.costume, assetUrl: (p1.assetUrl === 'local_blob' ? '' : p1.assetUrl), isHostPlayer: isHost(), cx: cactus.x, speed: cactus.speed
                    }));
                }

                ctx.fillStyle = "#ffffff";
                ctx.fillRect(0, 0, canvas.width, canvas.height);
                if(bgVideoElement) { try { ctx.drawImage(bgVideoElement, 0, 0, canvas.width, canvas.height); } catch(e) {} }

                if(isBuildMode) {
                    ctx.strokeStyle = "rgba(0,0,0,0.15)"; ctx.lineWidth = 1;
                    for(let x=0; x<canvas.width; x+=GRID_SIZE) { ctx.beginPath(); ctx.moveTo(x, 0); ctx.lineTo(x, canvas.height); ctx.stroke(); }
                    for(let y=0; y<canvas.height; y+=GRID_SIZE) { ctx.beginPath(); ctx.moveTo(0, y); ctx.lineTo(canvas.width, y); ctx.stroke(); }
                }

                ctx.fillStyle = '#6b7280'; ctx.fillRect(0, 135, 600, 2); 
                
                mapBlocks.forEach(b => {
                    let bx = (b.c * GRID_SIZE) - worldXOffset;
                    while (bx < -GRID_SIZE) { bx += 700; } 
                    let by = b.r * GRID_SIZE;

                    if (b.t === 'brick' || b.t === undefined) {
                        ctx.fillStyle = "#b45309"; ctx.fillRect(bx, by, GRID_SIZE, GRID_SIZE);
                        ctx.strokeStyle = "#78350f"; ctx.lineWidth = 1.5; ctx.strokeRect(bx, by, GRID_SIZE, GRID_SIZE);
                    } else if (b.t === 'cactus') {
                        ctx.fillStyle = '#16a34a'; ctx.fillRect(bx + 5, by + 4, GRID_SIZE - 10, GRID_SIZE - 4);
                        ctx.fillRect(bx + 2, by + 8, 4, 6); ctx.fillRect(bx + GRID_SIZE - 6, by + 6, 4, 6);
                    } else if (b.t === 'custom') {
                        if (b.url) {
                            if (!blockTextureCache[b.url]) {
                                blockTextureCache[b.url] = new Image();
                                blockTextureCache[b.url].src = b.url;
                                blockTextureCache[b.url].crossOrigin = "anonymous";
                            }
                            try { ctx.drawImage(blockTextureCache[b.url], bx, by, GRID_SIZE, GRID_SIZE); } catch(err) {
                                ctx.fillStyle = "#f59e0b"; ctx.fillRect(bx, by, GRID_SIZE, GRID_SIZE);
                            }
                        } else {
                            ctx.fillStyle = "#f59e0b"; ctx.fillRect(bx, by, GRID_SIZE, GRID_SIZE);
                            ctx.strokeStyle = "#d97706"; ctx.lineWidth = 1.5; ctx.strokeRect(bx, by, GRID_SIZE, GRID_SIZE);
                            ctx.fillStyle = "#ffffff"; ctx.font = "10px sans-serif"; ctx.fillText(b.cmd ? "⚙️" : "⭐", bx + 6, by + 16);
                        }
                    }
                });

                drawPlayerWithCostume(p1.x, p1.y, p1.color, p1.costume, currentNameSetting, p1.isDead, p1.customElement);
                Object.keys(players).forEach(id => {
                    let opp = players[id];
                    drawPlayerWithCostume(opp.x, opp.y, opp.color, opp.costume, opp.name, opp.isDead, opp.customElement);
                });
                
                ctx.fillStyle = '#15803d'; ctx.fillRect(cactus.x, cactus.y, cactus.width, cactus.height); 
                ctx.fillStyle = '#374151'; ctx.font = 'bold 11px sans-serif'; 
                let scoreString = `${currentNameSetting}: ${p1.score}`;
                Object.keys(players).forEach(id => { scoreString += `  |  ${players[id].name}: ${players[id].score}`; });
                ctx.fillText(scoreString, 10, 20);
            } catch(errorLoop) {}
            requestAnimationFrame(loop);
        }
        loop();
    </script>
</body>
</html>
