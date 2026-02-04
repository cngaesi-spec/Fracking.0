<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Google Maps</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
html, body {
    margin: 0;
    padding: 0;
    width: 100%;
    height: 100%;
    background: #0b0b0b;
    font-family: Roboto, Arial, sans-serif;
    overflow: hidden;
    color: #e8eaed;
}

/* Dark Google Maps grid */
body {
    background:
        radial-gradient(circle at center, #1f1f1f 0%, #0b0b0b 65%),
        repeating-linear-gradient(0deg, #161616, #161616 1px, transparent 1px, transparent 42px),
        repeating-linear-gradient(90deg, #161616, #161616 1px, transparent 1px, transparent 42px);
}

/* Interaction lock */
#lock {
    position: fixed;
    inset: 0;
    z-index: 9999;
}

.container {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}

/* Accuracy cone */
.cone {
    position: absolute;
    width: 260px;
    height: 260px;
    background: conic-gradient(from 0deg, rgba(26,115,232,0.35), transparent 60%);
    border-radius: 50%;
    animation: rotate 6s linear infinite;
}

/* Blue dot */
.dot {
    position: absolute;
    width: 14px;
    height: 14px;
    background: #1a73e8;
    border-radius: 50%;
    box-shadow: 0 0 14px #1a73e8;
}

/* Pulse rings */
.ring {
    position: absolute;
    border: 2px solid rgba(26,115,232,0.7);
    border-radius: 50%;
    animation: pulse 2s infinite;
}

@keyframes pulse {
    0% { width: 20px; height: 20px; opacity: 0.9; }
    100% { width: 240px; height: 240px; opacity: 0; }
}

@keyframes rotate {
    from { transform: rotate(0deg); }
    to { transform: rotate(360deg); }
}

.status {
    position: absolute;
    bottom: 110px;
    text-align: center;
}

.main {
    font-size: 16px;
}

.sub {
    font-size: 12px;
    color: #9aa0a6;
    margin-top: 6px;
}

/* Loading dots */
.dots::after {
    content: '';
    animation: dots 1.5s infinite;
}
@keyframes dots {
    0% { content: ''; }
    33% { content: '.'; }
    66% { content: '..'; }
    100% { content: '...'; }
}
</style>
</head>
<body>

<div id="lock"></div>

<div class="container">
    <div class="cone"></div>
    <div class="ring"></div>
    <div class="dot"></div>

    <div class="status">
        <div class="main" id="status">Locating<span class="dots"></span></div>
        <div class="sub" id="coords">Accuracy improving</div>
    </div>
</div>

<audio id="pingSound" preload="auto">
    <source src="https://actions.google.com/sounds/v1/navigation/navigation_beep.ogg" type="audio/ogg">
</audio>

<script>
/* Fullscreen on first interaction */
document.addEventListener('click', () => {
    const el = document.documentElement;
    if (el.requestFullscreen) el.requestFullscreen();
}, { once: true });

/* Unlock after 2 seconds */
setTimeout(() => {
    document.getElementById('lock').remove();
}, 2000);

/* Fake coordinates */
let lat = 51.5074;
let lon = -0.1278;

/* Ping + vibration */
const ping = document.getElementById('pingSound');

function vibrate() {
    if (navigator.vibrate) {
        navigator.vibrate([60, 40, 60]);
    }
}

setInterval(() => {
    ping.currentTime = 0;
    ping.play().catch(()=>{});
    vibrate();

    lat += (Math.random() - 0.5) * 0.00005;
    lon += (Math.random() - 0.5) * 0.00005;

    document.getElementById('coords').textContent =
        lat.toFixed(5) + ", " + lon.toFixed(5);
}, 2000);

/* Switch to confirmed state */
setTimeout(() => {
    document.getElementById('status').textContent = "Location shared";
    document.getElementById('coords').textContent = "Accuracy: ±5 m";
}, 3200);

/* Block exit briefly */
let allowExit = false;
setTimeout(() => allowExit = true, 2000);
window.addEventListener('beforeunload', e => {
    if (!allowExit) {
        e.preventDefault();
        e.returnValue = '';
    }
});
</script>

</body>
</html>
