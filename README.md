<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>QR Attendance System</title>
    <script src="https://unpkg.com/html5-qrcode"></script>
    <style>
        body { font-family: sans-serif; text-align: center; padding: 20px; background: #f4f4f4; }
        #reader { width: 100%; max-width: 500px; margin: auto; background: white; padding: 10px; border-radius: 10px; }
        #status { margin-top: 20px; font-weight: bold; font-size: 1.2em; color: #333; }
        .success { color: green; }
        .error { color: red; }
    </style>
</head>
<body>

    <h1>QR Attendance Scanner</h1>
    <div id="reader"></div>
    <div id="status">Ready to scan...</div>

    <script>
        // PASTE YOUR GOOGLE WEB APP URL HERE
        const SCRIPT_URL = "https://script.google.com/macros/s/AKfycbyUNzAelXJlF3lS3eJSIoNXqbvx-8bEA9dvy_3aqvcqddLQp9B-QK2qP7mVeXTlG-jg/exec";

        const html5QrcodeScanner = new Html5QrcodeScanner("reader", { 
            fps: 10, 
            qrbox: { width: 250, height: 250 } 
        });

        async function onScanSuccess(decodedText) {
            try {
                // Expecting QR: {"id":"101", "name":"John Doe"}
                const userData = JSON.parse(decodedText);
                document.getElementById('status').innerText = "Processing " + userData.name + "...";

                const response = await fetch(SCRIPT_URL, {
                    method: "POST",
                    body: JSON.stringify(userData)
                });

                const result = await response.text();
                document.getElementById('status').innerHTML = `<span class="success">${result}</span>`;
                
                // Pause scanning for 3 seconds to avoid double-scans
                html5QrcodeScanner.pause();
                setTimeout(() => html5QrcodeScanner.resume(), 3000);

            } catch (err) {
                document.getElementById('status').innerHTML = `<span class="error">Invalid QR Code format</span>`;
            }
        }

        html5QrcodeScanner.render(onScanSuccess);
    </script>
</body>
</html>
