# TEST-RAM
<!DOCTYPE html>
<html lang="pl">
<head>
  <meta charset="UTF-8">
  <title>₿FREE RAM!!!!!!!!</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    body {
      margin: 0;
      background: #0a0a0a;
      color: #00ff99;
      font-family: 'Segoe UI', sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 50px 20px;
    }

    h1 {
      font-size: 2.5rem;
      margin-bottom: 10px;
    }

    p {
      font-size: 1.1rem;
      margin-bottom: 30px;
      color: #ccc;
      max-width: 600px;
      text-align: center;
    }

    .buttons {
      display: flex;
      gap: 20px;
      margin-bottom: 20px;
    }

    button {
      background: #111;
      border: 2px solid #00ff99;
      color: #00ff99;
      padding: 15px 25px;
      border-radius: 10px;
      font-size: 1rem;
      cursor: pointer;
      transition: all 0.2s ease-in-out;
    }

    button:hover {
      background: #00ff99;
      color: #000;
    }

    #status {
      margin-top: 20px;
      font-size: 1.2rem;
      color: #88ffcc;
    }
  </style>
</head>
<body>
  <h1>💻 Test Obciążenia CPU i RAM</h1>
  <p>NIE ODPOWIADAM ZA TWOJEGO PC</p>

  <div class="buttons">
    <button onclick="startTest()">ZACZNIJ POBIERAĆ RAM</button>
    <button onclick="stopTest()">SKOŃCZ POBIERAĆ RAM</button>
  </div>

  <div id="status">Status: Idle</div>

  <script>
    let cpuWorkers = [];
    let memoryHog = [];
    let running = false;

    function createCpuWorker() {
      const blob = new Blob([`
        self.onmessage = function(e) {
          if (e.data === 'start') {
            while (true) {
              Math.sqrt(Math.random() * Math.random());
            }
          }
        };
      `]);
      const url = URL.createObjectURL(blob);
      const worker = new Worker(url);
      return worker;
    }

    function startTest() {
      if (running) return;
      running = true;
      document.getElementById("status").innerText = "Status: Test aktywny (CPU + RAM)...";

      // CPU workers
      const cores = navigator.hardwareConcurrency || 4;
      for (let i = 0; i < cores; i++) {
        const w = createCpuWorker();
        w.postMessage("start");
        cpuWorkers.push(w);
      }

      // RAM: tworzenie dużych tablic co sekundę
      const ramInterval = setInterval(() => {
        if (!running) return clearInterval(ramInterval);
        const bigArray = new Array(10 ** 6).fill(Math.random());
        memoryHog.push(bigArray);
      }, 1000);
    }

    function stopTest() {
      running = false;
      cpuWorkers.forEach(w => w.terminate());
      cpuWorkers = [];

      memoryHog = []; // RAM zwalniamy przez odłączenie referencji

      document.getElementById("status").innerText = "Status: Zatrzymano test.";
    }
  </script>
</body>
</html>
