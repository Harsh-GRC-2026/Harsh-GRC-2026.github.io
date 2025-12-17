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
        const SCRIPT_URL = "https://script.google.com/macros/s/AKfycbx57fnckT8R9nKOJMV2SI5kqi8ZVggRl97MJd-wzf15me6XPXXMyjJBBVAmBDRdJnQ/exec";

        const html5QrcodeScanner = new Html5QrcodeScanner("reader", { 
            fps: 10, 
            qrbox: { width: 250, height: 250 } 
        });
        async function onScanSuccess(decodedText) {
    // decodedText is now just "101,John Doe"
    document.getElementById('status').innerText = "Scanning: " + decodedText;

    try {
        const response = await fetch(SCRIPT_URL, {
            method: "POST",
            body: decodedText // Send the raw string directly
        });

        const result = await response.text();
        document.getElementById('status').innerHTML = `<span class="success">${result}</span>`;
        
        // Pause to prevent double scans
        html5QrcodeScanner.pause();
        setTimeout(() => html5QrcodeScanner.resume(), 3000);

    } catch (err) {
        document.getElementById('status').innerHTML = `<span class="error">Server Error</span>`;
    }
}



        html5QrcodeScanner.render(onScanSuccess);
    </script>
</body>
</html>
