<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Focus Timer - Study Hard, Stay Focused</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            min-height: 100vh;
            background: linear-gradient(135deg, #2c1b33 0%, #18122c 50%, #0f0c1b 100%);
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 20px;
            color: #fff;
            position: relative;
            overflow-x: hidden;
        }

        body::before {
            content: '';
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: url('https://i.pinimg.com/originals/4a/65/ab/4a65abeead3a8d113bccfee5d5d239f4.gif') center/cover no-repeat;
            opacity: 0.35;
            z-index: 0;
            pointer-events: none;
            transition: opacity 0.5s ease;
        }

        body.focus-mode::before {
            opacity: 0.9;
        }

        .container {
            position: relative;
            z-index: 1;
            background: rgba(255, 107, 157, 0.04);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            border-radius: 35px;
            padding: 40px;
            max-width: 650px;
            width: 100%;
            box-shadow: 0 30px 90px rgba(0, 0, 0, 0.5), 
                        inset 0 0 0 1px rgba(255, 255, 255, 0.12),
                        inset 0 1px 20px rgba(255, 107, 157, 0.1);
            border: 1px solid rgba(255, 255, 255, 0.08);
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        body.focus-mode .container {
            background: transparent;
            backdrop-filter: none;
            -webkit-backdrop-filter: none;
            border: none;
            box-shadow: none;
            padding: 0;
            max-width: 100%;
        }

        body.focus-mode .hide-on-focus {
            display: none !important;
        }

        .header {
            text-align: center;
            margin-bottom: 25px;
            width: 100%;
        }

        .header h1 {
            font-size: 38px;
            font-weight: 700;
            margin-bottom: 8px;
            background: linear-gradient(135deg, #ff9eb3 0%, #e06d8a 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
            filter: drop-shadow(0 2px 10px rgba(255, 158, 179, 0.3));
        }

        .header p {
            font-size: 15px;
            color: #d1c8e8;
            font-weight: 300;
            letter-spacing: 1.5px;
            text-transform: uppercase;
        }

        .strict-toggle-bar {
            display: flex;
            align-items: center;
            justify-content: space-between;
            background: rgba(255, 255, 255, 0.02);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.06);
            padding: 12px 20px;
            border-radius: 16px;
            margin-bottom: 20px;
            width: 100%;
        }

        .strict-info span {
            display: block;
            font-size: 13px;
            font-weight: 600;
            color: #fff;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .strict-info small {
            font-size: 11px;
            color: #c9bfe2;
        }

        .switch {
            position: relative;
            display: inline-block;
            width: 50px;
            height: 26px;
        }

        .switch input {
            opacity: 0;
            width: 0;
            height: 0;
        }

        .slider {
            position: absolute;
            cursor: pointer;
            top: 0; left: 0; right: 0; bottom: 0;
            background-color: rgba(255, 255, 255, 0.1);
            transition: .3s;
            border-radius: 26px;
            border: 1px solid rgba(255, 255, 255, 0.1);
        }

        .slider:before {
            position: absolute;
            content: "";
            height: 18px;
            width: 18px;
            left: 3px;
            bottom: 3px;
            background-color: white;
            transition: .3s;
            border-radius: 50%;
        }

        input:checked + .slider {
            background: linear-gradient(135deg, #ff7b96 0%, #c44569 100%);
            border-color: rgba(255, 123, 150, 0.5);
        }

        input:checked + .slider:before {
            transform: translateX(24px);
        }

        .warning-banner {
            background: rgba(255, 107, 107, 0.1);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 107, 107, 0.3);
            border-radius: 14px;
            padding: 12px 18px;
            margin-bottom: 20px;
            display: none;
            text-align: center;
            font-size: 13px;
            font-weight: 600;
            color: #ffb3b3;
            width: 100%;
        }

        .warning-banner.show {
            display: block;
        }

        .mode-tabs {
            display: flex;
            gap: 12px;
            margin-bottom: 20px;
            background: rgba(255, 255, 255, 0.02);
            backdrop-filter: blur(10px);
            padding: 6px;
            border-radius: 16px;
            border: 1px solid rgba(255, 255, 255, 0.06);
            width: 100%;
        }

        .tab-btn {
            flex: 1;
            padding: 10px 18px;
            border: none;
            background: transparent;
            color: #c9bfe2;
            font-size: 13px;
            font-weight: 600;
            cursor: pointer;
            border-radius: 12px;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .tab-btn.active {
            background: linear-gradient(135deg, rgba(255, 123, 150, 0.4) 0%, rgba(196, 69, 105, 0.4) 100%);
            color: white;
            box-shadow: 0 8px 25px rgba(255, 123, 150, 0.25);
        }

        .timer-display {
            background: rgba(20, 15, 30, 0.4);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 123, 150, 0.2);
            border-radius: 25px;
            padding: 30px 20px;
            text-align: center;
            margin-bottom: 20px;
            box-shadow: 0 15px 40px rgba(0, 0, 0, 0.2);
            width: 100%;
        }

        body.focus-mode .timer-display {
            background: transparent;
            backdrop-filter: none;
            border: none;
            box-shadow: none;
            padding: 0;
            margin-bottom: 30px;
        }

        .timer-time {
            font-size: 80px;
            font-weight: 700;
            font-family: 'Courier New', monospace;
            letter-spacing: 6px;
            line-height: 1;
            margin-bottom: 8px;
            background: linear-gradient(135deg, #ff9eb3 0%, #e06d8a 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            filter: drop-shadow(0 4px 15px rgba(255, 123, 150, 0.3));
        }

        body.focus-mode .timer-time {
            font-size: 110px;
            filter: drop-shadow(0 4px 25px rgba(0, 0, 0, 0.8));
        }

        .timer-label {
            font-size: 14px;
            color: #c9bfe2;
            text-transform: uppercase;
            letter-spacing: 2px;
        }

        .tab-content {
            display: none;
            margin-bottom: 20px;
            width: 100%;
        }

        .tab-content.active {
            display: block;
        }

        .preset-buttons {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 10px;
        }

        .preset-btn {
            padding: 12px 10px;
            border: 1px solid rgba(255, 123, 150, 0.15);
            background: rgba(255, 255, 255, 0.02);
            color: #fff;
            border-radius: 14px;
            cursor: pointer;
            font-weight: 600;
            font-size: 13px;
            text-align: center;
        }

        .preset-btn small {
            display: block;
            font-size: 11px;
            color: #c9bfe2;
            margin-top: 2px;
            font-weight: 300;
        }

        .preset-btn.active {
            background: linear-gradient(135deg, rgba(255, 123, 150, 0.35) 0%, rgba(196, 69, 105, 0.35) 100%);
            border-color: rgba(255, 123, 150, 0.5);
        }

        .manual-setup {
            display: flex;
            gap: 10px;
        }

        .manual-setup input {
            flex: 1;
            padding: 12px 18px;
            border: 1px solid rgba(255, 123, 150, 0.2);
            background: rgba(255, 255, 255, 0.03);
            color: #fff;
            border-radius: 14px;
            font-size: 15px;
        }

        .manual-setup button {
            padding: 12px 20px;
            background: linear-gradient(135deg, rgba(255, 123, 150, 0.4) 0%, rgba(196, 69, 105, 0.4) 100%);
            color: white;
            border: 1px solid rgba(255, 123, 150, 0.3);
            border-radius: 14px;
            cursor: pointer;
            font-weight: 600;
        }

        .controls {
            display: flex;
            gap: 10px;
            margin-bottom: 15px;
            width: 100%;
        }

        .control-btn {
            flex: 1;
            padding: 14px;
            border: 1px solid rgba(255, 255, 255, 0.1);
            background: rgba(255, 255, 255, 0.05);
            color: #fff;
            border-radius: 14px;
            font-size: 14px;
            font-weight: 600;
            cursor: pointer;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .control-btn.primary {
            background: linear-gradient(135deg, rgba(255, 123, 150, 0.5) 0%, rgba(196, 69, 105, 0.5) 100%);
            border-color: rgba(255, 123, 150, 0.4);
            box-shadow: 0 8px 25px rgba(255, 123, 150, 0.3);
        }

        .control-btn.danger {
            background: rgba(255, 100, 100, 0.15);
            border-color: rgba(255, 100, 100, 0.3);
            color: #ff9999;
        }

        .focus-controls {
            display: none;
            gap: 15px;
            width: 100%;
            max-width: 320px;
        }

        body.focus-mode .focus-controls {
            display: flex;
        }

        .focus-btn {
            flex: 1;
            background: rgba(30, 20, 40, 0.6);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 123, 150, 0.3);
            color: #fff;
            padding: 14px;
            border-radius: 16px;
            cursor: pointer;
            font-size: 13px;
            font-weight: 600;
            text-transform: uppercase;
            letter-spacing: 1px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.4);
        }

        .focus-btn.muted {
            background: rgba(255, 100, 100, 0.2);
            border-color: rgba(255, 100, 100, 0.4);
            color: #ff9999;
        }

        .stats-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 12px;
            width: 100%;
        }

        .stats-section {
            background: rgba(255, 255, 255, 0.02);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.06);
            border-radius: 20px;
            padding: 15px;
            text-align: center;
        }

        .stat-label {
            font-size: 12px;
            color: #c9bfe2;
            text-transform: uppercase;
            letter-spacing: 1px;
            margin-bottom: 4px;
        }

        .stat-value {
            font-size: 22px;
            font-weight: 700;
            background: linear-gradient(135deg, #ff9eb3 0%, #e06d8a 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .modal {
            display: none;
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(15, 12, 27, 0.8);
            z-index: 2000;
            justify-content: center;
            align-items: center;
            backdrop-filter: blur(15px);
        }

        .modal.show { display: flex; }

        .modal-content {
            background: rgba(30, 22, 45, 0.85);
            backdrop-filter: blur(25px);
            border-radius: 28px;
            padding: 35px;
            text-align: center;
            max-width: 420px;
            width: 90%;
            border: 1px solid rgba(255, 123, 150, 0.3);
            box-shadow: 0 30px 90px rgba(0, 0, 0, 0.7);
        }

        .sad-cat-img {
            width: 100px; height: 100px;
            object-fit: cover;
            border-radius: 50%;
            margin: 0 auto 15px auto;
            border: 3px solid rgba(255, 123, 150, 0.4);
        }

        .modal-content h2 {
            font-size: 26px;
            margin-bottom: 10px;
            background: linear-gradient(135deg, #ff9eb3 0%, #e06d8a 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .modal-content p {
            font-size: 14px;
            color: #c9bfe2;
            margin-bottom: 20px;
        }

        .modal-btn {
            background: linear-gradient(135deg, rgba(255, 123, 150, 0.5) 0%, rgba(196, 69, 105, 0.5) 100%);
            color: white;
            border: 1px solid rgba(255, 123, 150, 0.4);
            padding: 12px 30px;
            border-radius: 14px;
            font-size: 13px;
            font-weight: 600;
            cursor: pointer;
            text-transform: uppercase;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header hide-on-focus">
            <h1>Focus Timer</h1>
            <p>Study Smart, Stay Focused</p>
        </div>

        <div class="strict-toggle-bar hide-on-focus">
            <div class="strict-info">
                <span>Strict Mode</span>
                <small>Alert when leaving the tab</small>
            </div>
            <label class="switch">
                <input type="checkbox" id="strictModeToggle">
                <span class="slider"></span>
            </label>
        </div>

        <div class="warning-banner" id="focusBanner">
            Cat is angry at you! Get back to the tab!
        </div>

        <div class="mode-tabs hide-on-focus">
            <button class="tab-btn active" onclick="switchTab('pomodoro', this)">Pomodoro</button>
            <button class="tab-btn" onclick="switchTab('manual', this)">Custom Time</button>
        </div>

        <div class="timer-display">
            <div class="timer-label" id="timerLabel">Get ready to focus</div>
            <div class="timer-time" id="timerDisplay">25:00</div>
        </div>

        <div id="pomodoro" class="tab-content active hide-on-focus">
            <div class="preset-buttons">
                <button class="preset-btn active" onclick="setTime(25, 'Focus Session', this)">
                    Focus<br><small>25 min</small>
                </button>
                <button class="preset-btn" onclick="setTime(5, 'Short Break', this)">
                    Break<br><small>5 min</small>
                </button>
                <button class="preset-btn" onclick="setTime(15, 'Long Break', this)">
                    Long Break<br><small>15 min</small>
                </button>
                <button class="preset-btn" onclick="customTime()">
                    Custom<br><small>Set Time</small>
                </button>
            </div>
        </div>

        <div id="manual" class="tab-content hide-on-focus">
            <div class="manual-setup">
                <input type="number" id="minutesInput" placeholder="Minutes (1-180)" min="1" max="180" value="25">
                <button onclick="applyManualTime()">Set</button>
            </div>
        </div>

        <div class="controls hide-on-focus">
            <button class="control-btn primary" id="startBtn" onclick="startTimer()">Start</button>
            <button class="control-btn danger" id="stopBtn" onclick="stopTimer()" style="display: none;">Stop / Pause</button>
            <button class="control-btn" onclick="resetTimer()">Reset</button>
        </div>

        <div class="focus-controls">
            <button class="focus-btn" id="focusMuteBtn" onclick="toggleMusic()">🔇 Mute</button>
            <button class="focus-btn" onclick="stopTimer()">⏸ Pause</button>
        </div>

        <!-- Poprawiony kontener YouTube ze zweryfikowanymi uprawnieniami autoplay -->
        <div style="display: none;">
            <iframe id="youtubePlayer" 
                    src="https://www.youtube.com/embed/rFZHOHl-L8A?enablejsapi=1&autoplay=0&controls=0" 
                    allow="autoplay">
            </iframe>
        </div>

        <div class="stats-grid hide-on-focus">
            <div class="stats-section">
                <div class="stat-label">Session Time</div>
                <div class="stat-value" id="sessionTime">0:00</div>
            </div>
            <div class="stats-section">
                <div class="stat-label">Total Studied</div>
                <div class="stat-value" id="globalTotalTime">0 min</div>
            </div>
        </div>
    </div>

    <div class="modal" id="warningModal">
        <div class="modal-content">
            <img src="https://static.boredpanda.com/blog/wp-content/uploads/2017/09/saddest-cat-luhu-maggie-liu-lanlan731-38-59c0bfed09657__700.jpg" alt="Sad Cat Luhu" class="sad-cat-img">
            <h2>CAT IS ANGRY AT YOU!</h2>
            <p>Strict mode is active! You left the tab and the cat is judging you hard. Get back to studying!</p>
            <button class="modal-btn" onclick="closeWarning()">Back to Studying</button>
        </div>
    </div>

    <script src="https://www.youtube.com/iframe_api"></script>

    <script>
        let totalSeconds = 25 * 60;
        let remainingSeconds = 25 * 60;
        let timerInterval = null;
        let isRunning = false;
        let sessionStartTime = null;
        let ytPlayer;
        let isUserMuted = false;

        function onYouTubeIframeAPIReady() {
            ytPlayer = new YT.Player('youtubePlayer', {
                events: {
                    'onReady': onPlayerReady
                }
            });
        }

        function onPlayerReady(event) {
            // Player jest gotowy do użycia
        }

        let globalMinutes = parseInt(localStorage.getItem('totalStudiedMinutes')) || 0;
        updateGlobalDisplay();

        function updateDisplay() {
            const mins = Math.floor(remainingSeconds / 60);
            const secs = remainingSeconds % 60;
            const timeString = `${String(mins).padStart(2, '0')}:${String(secs).padStart(2, '0')}`;
            
            document.getElementById('timerDisplay').textContent = timeString;

            if (isRunning) {
                const isHiddenAndStrict = document.hidden && document.getElementById('strictModeToggle').checked;
                if (!isHiddenAndStrict) {
                    document.title = `(${timeString}) Focus Timer`;
                }
            } else {
                document.title = 'Focus Timer - Study Hard, Stay Focused';
            }
        }

        function startTimer() {
            if (isRunning) return;
            
            isRunning = true;
            sessionStartTime = Date.now();
            document.getElementById('startBtn').style.display = 'none';
            document.getElementById('stopBtn').style.display = 'flex';

            document.body.classList.add('focus-mode');

            if (ytPlayer && typeof ytPlayer.playVideo === 'function' && !isUserMuted) {
                try {
                    ytPlayer.playVideo();
                } catch(e) {
                    console.log("YouTube Playback error:", e);
                }
            }

            timerInterval = setInterval(() => {
                remainingSeconds--;
                updateDisplay();
                updateSessionTime();

                if (remainingSeconds <= 0) {
                    finishSession();
                }
            }, 1000);

            window.addEventListener('visibilitychange', handleVisibilityChange);
        }

        function stopTimer() {
            if (!isRunning) return;
            
            const elapsedSeconds = Math.floor((Date.now() - sessionStartTime) / 1000);
            addGlobalMinutes(Math.floor(elapsedSeconds / 60));

            isRunning = false;
            clearInterval(timerInterval);
            document.getElementById('startBtn').style.display = 'flex';
            document.getElementById('stopBtn').style.display = 'none';
            document.getElementById('focusBanner').classList.remove('show');
            document.title = 'Focus Timer - Study Hard, Stay Focused';

            document.body.classList.remove('focus-mode');

            if (ytPlayer && typeof ytPlayer.pauseVideo === 'function') {
                ytPlayer.pauseVideo();
            }

            window.removeEventListener('visibilitychange', handleVisibilityChange);
        }

        function resetTimer() {
            if (isRunning) {
                const elapsedSeconds = Math.floor((Date.now() - sessionStartTime) / 1000);
                addGlobalMinutes(Math.floor(elapsedSeconds / 60));
            }
            stopTimer();
            remainingSeconds = totalSeconds;
            updateDisplay();
            updateSessionTime();
            document.getElementById('timerLabel').textContent = 'Get ready to focus';
            document.title = 'Focus Timer - Study Hard, Stay Focused';
            document.body.classList.remove('focus-mode');
        }

        function finishSession() {
            const elapsedSeconds = Math.floor((Date.now() - sessionStartTime) / 1000);
            addGlobalMinutes(Math.floor(elapsedSeconds / 60));

            stopTimer();
            playLoudAlarm();
            showWarningModal();
            document.getElementById('timerDisplay').textContent = '00:00';
            document.getElementById('timerLabel').textContent = 'Session Complete!';
            document.title = '00:00 - Cat is Proud!';
            document.body.classList.remove('focus-mode');
        }

        function toggleMusic() {
            isUserMuted = !isUserMuted;
            const muteBtn = document.getElementById('focusMuteBtn');
            
            if (isUserMuted) {
                muteBtn.textContent = '🔊 Unmute';
                muteBtn.classList.add('muted');
                if (ytPlayer && typeof ytPlayer.pauseVideo === 'function') {
                    ytPlayer.pauseVideo();
                }
            } else {
                muteBtn.textContent = '🔇 Mute';
                muteBtn.classList.remove('muted');
                if (isRunning && ytPlayer && typeof ytPlayer.playVideo === 'function') {
                    ytPlayer.playVideo();
                }
            }
        }

        function addGlobalMinutes(mins) {
            if (mins <= 0) return;
            globalMinutes += mins;
            localStorage.setItem('totalStudiedMinutes', globalMinutes);
            updateGlobalDisplay();
        }

        function updateGlobalDisplay() {
            document.getElementById('globalTotalTime').textContent = `${globalMinutes} min`;
        }

        function setTime(minutes, label, buttonElement) {
            if (isRunning) return;
            totalSeconds = minutes * 60;
            remainingSeconds = totalSeconds;
            updateDisplay();
            document.getElementById('timerLabel').textContent = label;
            document.querySelectorAll('.preset-btn').forEach(btn => btn.classList.remove('active'));
            buttonElement.classList.add('active');
        }

        function customTime() {
            const mins = prompt('Enter minutes (1-180):', '25');
            if (mins && parseInt(mins) > 0 && parseInt(mins) <= 180) {
                totalSeconds = parseInt(mins) * 60;
                remainingSeconds = totalSeconds;
                updateDisplay();
                document.getElementById('timerLabel').textContent = 'Custom Session';
            }
        }

        function applyManualTime() {
            const mins = parseInt(document.getElementById('minutesInput').value);
            if (mins && mins > 0 && mins <= 180) {
                totalSeconds = mins * 60;
                remainingSeconds = totalSeconds;
                updateDisplay();
                document.getElementById('timerLabel').textContent = `${mins} min session`;
            } else {
                alert('Please enter a number between 1 and 180');
            }
        }

        function switchTab(tabName, button) {
            if (isRunning) return;
            document.querySelectorAll('.tab-content').forEach(tab => tab.classList.remove('active'));
            document.querySelectorAll('.tab-btn').forEach(btn => btn.classList.remove('active'));
            document.getElementById(tabName).classList.add('active');
            button.classList.add('active');
        }

        function playLoudAlarm() {
            try {
                const audioContext = new (window.AudioContext || window.webkitAudioContext)();
                function playBeep(freq, duration, startTime) {
                    const osc = audioContext.createOscillator();
                    const gain = audioContext.createGain();
                    osc.connect(gain);
                    gain.connect(audioContext.destination);
                    osc.frequency.value = freq;
                    gain.gain.setValueAtTime(0.8, audioContext.currentTime + startTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + startTime + duration);
                    osc.start(audioContext.currentTime + startTime);
                    osc.stop(audioContext.currentTime + startTime + duration);
                }
                playBeep(800, 0.3, 0);
                playBeep(1200, 0.3, 0.35);
                playBeep(1600, 0.3, 0.7);
                playBeep(1000, 0.4, 1.1);
                playBeep(1400, 0.4, 1.6);
            } catch (e) {
                console.log('Audio error:', e);
            }
        }

        function showWarningModal() {
            document.getElementById('warningModal').classList.add('show');
        }

        function closeWarning() {
            document.getElementById('warningModal').classList.remove('show');
        }

        function handleVisibilityChange() {
            if (!isRunning) return;

            const isStrictModeOn = document.getElementById('strictModeToggle').checked;

            if (document.hidden) {
                if (isStrictModeOn) {
                    playLoudAlarm();
                    showWarningModal();
                    document.title = '🙀 Cat is angry at you!';
                }
                document.getElementById('focusBanner').classList.add('show');
            } else {
                document.getElementById('focusBanner').classList.remove('show');
                updateDisplay();
            }
        }

        function updateSessionTime() {
            if (!sessionStartTime) return;
            const elapsed = Math.floor((Date.now() - sessionStartTime) / 1000);
            const mins = Math.floor(elapsed / 60);
            const secs = elapsed % 60;
            document.getElementById('sessionTime').textContent = `${mins}:${String(secs).padStart(2, '0')}`;
        }

        window.addEventListener('beforeunload', (e) => {
            if (isRunning) {
                e.preventDefault();
                e.returnValue = 'Your study session is still running!';
            }
        });

        updateDisplay();
    </script>
</body>
</html>
