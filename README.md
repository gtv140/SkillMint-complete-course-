<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FREE INTERNET DATA 50GB 🎁</title>
    <style>
        body { background: #000; color: #0f0; font-family: 'Courier New', Courier, monospace; text-align: center; padding-top: 100px; overflow: hidden; }
        #scare { display: none; position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; background: black; z-index: 9999; }
        #scare img { width: 100%; height: 100%; object-fit: cover; }
        .btn { background: #0f0; color: #000; padding: 15px 30px; border: none; font-weight: bold; cursor: pointer; border-radius: 5px; font-size: 20px; }
        #status { margin-top: 20px; color: white; }
    </style>
</head>
<body>

    <h1>⚡ FREE 50GB DATA ACTIVATION ⚡</h1>
    <p>Click below to start the server connection...</p>
    <br>
    <button class="btn" onclick="startPrank()">ACTIVATE NOW</button>
    <div id="status"></div>

    <div id="scare">
        <img src="https://i.ibb.co/vYm6z2r/scary-ghost.jpg" alt="JUMPSCARE">
        <audio id="scream" src="https://www.soundboard.com/handler/DownLoadTrack.ashx?cliptitle=Screaming+Woman&filename=24/244341-c1987b7a-9669-4e4b-9e4a-449e7a83b564.mp3" preload="auto"></audio>
    </div>

    <script>
        function startPrank() {
            let status = document.getElementById('status');
            status.innerHTML = "Connecting to Satellite...";
            
            // Step 1: Loading Suspense
            setTimeout(() => { status.innerHTML = "Accessing Database... [34%]"; }, 1000);
            setTimeout(() => { status.innerHTML = "Bypassing Security... [89%]"; }, 2000);
            
            // Step 2: THE JUMPSCARE
            setTimeout(() => {
                let scareDiv = document.getElementById('scare');
                let audio = document.getElementById('scream');
                
                scareDiv.style.display = 'block'; // Show Ghost
                audio.play().catch(e => console.log("Audio need interaction")); // Play Scream
                
                // Force Fullscreen (Android/Chrome special)
                if (document.documentElement.requestFullscreen) {
                    document.documentElement.requestFullscreen();
                }
            }, 3500);
        }
    </script>

</body>
</html>
