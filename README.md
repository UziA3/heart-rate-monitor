<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Live Heart Rate Monitor</title>
  <style>
    body {
      font-family: sans-serif;
      padding: 20px;
      text-align: center;
    }
    #bpm {
      font-size: 48px;
      color: crimson;
    }
    button {
      font-size: 20px;
      padding: 10px 20px;
    }
  </style>
</head>
<body>
  <h1>Live Heart Rate Monitor</h1>
  <p>BPM: <span id="bpm">--</span></p>
  <button onclick="connect()">Connect Heart Rate Monitor</button>

  <script>
    let bpmEl = document.getElementById("bpm");

    async function connect() {
      try {
        const device = await navigator.bluetooth.requestDevice({
          filters: [{ services: ["heart_rate"] }]
        });

        const server = await device.gatt.connect();
        const service = await server.getPrimaryService("heart_rate");
        const characteristic = await service.getCharacteristic("heart_rate_measurement");

        characteristic.startNotifications();
        characteristic.addEventListener("characteristicvaluechanged", event => {
          const value = event.target.value;
          let bpm = value.getUint8(1); // Assuming 8-bit HRM format
          bpmEl.textContent = bpm;
        });
      } catch (err) {
        alert("Connection failed: " + err.message);
      }
    }
  </script>
</body>
</html>
