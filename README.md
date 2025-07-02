# pitch.analyzer.speed
A baseball pitching speed movement and analysis web
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Pitch Analyzer</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      text-align: center;
      padding: 20px;
    }
    video, canvas {
      max-width: 100%;
      margin-top: 20px;
      border: 1px solid #ccc;
    }
    button {
      margin-top: 10px;
      padding: 10px 20px;
      font-size: 16px;
    }
    input {
      margin-top: 10px;
    }
  </style>
</head>
<body>

<h1>Pitch Analyzer</h1>
<p>Upload a pitching video and mark the frame when the ball is released and when it crosses the plate.</p>

<input type="file" id="videoInput" accept="video/*">
<br>
<video id="video" controls width="640" height="360"></video>
<br>
<button onclick="markRelease()">Mark Release Frame</button>
<button onclick="markPlate()">Mark Plate Frame</button>
<br>
<p id="frameInfo">No frames marked yet.</p>
<h2 id="result"></h2>

<script>
  const video = document.getElementById('video');
  const videoInput = document.getElementById('videoInput');
  const result = document.getElementById('result');
  const frameInfo = document.getElementById('frameInfo');

  let releaseTime = null;
  let plateTime = null;

  videoInput.onchange = function () {
    const file = videoInput.files[0];
    if (file) {
      const url = URL.createObjectURL(file);
      video.src = url;
      resetMarks();
    }
  };

  function resetMarks() {
    releaseTime = null;
    plateTime = null;
    result.textContent = '';
    frameInfo.textContent = 'No frames marked yet.';
  }

  function markRelease() {
    releaseTime = video.currentTime;
    updateFrameInfo();
  }

  function markPlate() {
    plateTime = video.currentTime;
    updateFrameInfo();
    if (releaseTime !== null && plateTime !== null) {
      calculateSpeed();
    }
  }

  function updateFrameInfo() {
    frameInfo.textContent = `Release Time: ${releaseTime?.toFixed(3) || '—'} sec, Plate Time: ${plateTime?.toFixed(3) || '—'} sec`;
  }

  function calculateSpeed() {
    const timeDiff = plateTime - releaseTime;
    if (timeDiff <= 0) {
      result.textContent = 'Error: plate time must be after release time.';
      return;
    }

    const distanceFt = 60.5; // 60'6" in feet
    const speedFtPerSec = distanceFt / timeDiff;
    const speedMph = speedFtPerSec * 0.681818;

    result.innerHTML = `⚾ Estimated Pitch Speed: <strong>${speedMph.toFixed(1)} MPH</strong>`;
  }
</script>

</body>
</html>
