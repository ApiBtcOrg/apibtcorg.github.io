---
title: "Paywall"

# To set og:image:
# image: ...
---
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sample Lightning Network Paywall with APIBTC</title>
    <script src="https://cdn.jsdelivr.net/npm/qrcode@1.5.1/build/qrcode.min.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
        }
        #canvas {
            margin: 20px 0;
        }
        .payment-info {
            background: #f5f5f5;
            padding: 15px;
            border-radius: 5px;
            margin: 10px 0;
        }
        .input-group {
            margin-bottom: 15px;
        }
    </style>
</head>
<body>
    <h1>Sample Lightning Network Paywall with APIBTC</h1>
    <div class="input-group" id="input-group">
        <label for="memo" style="width: 20%;">Email: </label>
        <input type="text" id="memo" placeholder="anonymous@email.com" style="width: 80%; padding: 8px; box-sizing: border-box;">
        <div style=" display: flex;justify-content: center; width: 100%; margin-top: 10px;">
        <button onclick="generateInvoice()" style="padding: 0px 20px; background-color: #f7931a; color: white; border: none; border-radius: 5px; font-size: 16px; cursor: pointer; display: flex; align-items: center; gap: 8px; margin-top: 10px;"
                onmouseover="this.style.backgroundColor='#e07d0b'; this.style.transform='scale(1.03)'" 
        onmouseout="this.style.backgroundColor='#f7931a'; this.style.transform='scale(1)'"
        onmousedown="this.style.backgroundColor='#c06a0a'; this.style.transform='scale(0.98)'"
        onmouseup="this.style.backgroundColor='#e07d0b'; this.style.transform='scale(1.03)'"
        >
<svg xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#" xmlns="http://www.w3.org/2000/svg" height="64" width="64" version="1.1" xmlns:cc="http://creativecommons.org/ns#" xmlns:dc="http://purl.org/dc/elements/1.1/">
<g transform="translate(0.00630876,-0.00301984)">
<path fill="#f7931a" d="m63.033,39.744c-4.274,17.143-21.637,27.576-38.782,23.301-17.138-4.274-27.571-21.638-23.295-38.78,4.272-17.145,21.635-27.579,38.775-23.305,17.144,4.274,27.576,21.64,23.302,38.784z"/>
<path fill="#FFF" d="m46.103,27.444c0.637-4.258-2.605-6.547-7.038-8.074l1.438-5.768-3.511-0.875-1.4,5.616c-0.923-0.23-1.871-0.447-2.813-0.662l1.41-5.653-3.509-0.875-1.439,5.766c-0.764-0.174-1.514-0.346-2.242-0.527l0.004-0.018-4.842-1.209-0.934,3.75s2.605,0.597,2.55,0.634c1.422,0.355,1.679,1.296,1.636,2.042l-1.638,6.571c0.098,0.025,0.225,0.061,0.365,0.117-0.117-0.029-0.242-0.061-0.371-0.092l-2.296,9.205c-0.174,0.432-0.615,1.08-1.609,0.834,0.035,0.051-2.552-0.637-2.552-0.637l-1.743,4.019,4.569,1.139c0.85,0.213,1.683,0.436,2.503,0.646l-1.453,5.834,3.507,0.875,1.439-5.772c0.958,0.26,1.888,0.5,2.798,0.726l-1.434,5.745,3.511,0.875,1.453-5.823c5.987,1.133,10.489,0.676,12.384-4.739,1.527-4.36-0.076-6.875-3.226-8.515,2.294-0.529,4.022-2.038,4.483-5.155zm-8.022,11.249c-1.085,4.36-8.426,2.003-10.806,1.412l1.928-7.729c2.38,0.594,10.012,1.77,8.878,6.317zm1.086-11.312c-0.99,3.966-7.1,1.951-9.082,1.457l1.748-7.01c1.982,0.494,8.365,1.416,7.334,5.553z"/>
</g>
</svg>
            Pay with APIBTC
        </button>
        </div>
    </div>
    <br/>
    <canvas id="canvas"></canvas>
    <div id="payment-info" class="payment-info" style="visibility: hidden;"></div>

    <script>
        async function generateInvoice() {
            try {
                const memoValue = document.getElementById('memo').value || 'anonymous@email.com';
                const response = await fetch(`https://hook.eu2.make.com/8x4us2mg56ppntjcjx8241a8yf7u2nad?memo=${encodeURIComponent(memoValue)}`);
                const data = await response.json();
                
                // Clear previous QR code
                const canvas = document.getElementById('canvas');
                const context = canvas.getContext('2d');
                context.clearRect(0, 0, canvas.width, canvas.height);
                
                // Generate QR code
                QRCode.toCanvas(canvas, data.paymentRequest, function (error) {
                    if (error) console.error(error);
                });

                // Display payment information
                document.getElementById('payment-info').innerHTML = `
                    <h3>Payment Details:</h3>
                    <p><strong>Amount:</strong> ${data.satoshis} satoshis</p>
                    <p><strong>Email:</strong> ${data.memo}</p>
                    <p><strong>Expires:</strong> ${new Date(data.expiryTime).toLocaleString()}</p>
                    <p><strong>Payment Request:</strong><br>
                    <textarea readonly style="width: 100%; height: 200px">${data.paymentRequest}</textarea></p>
                    <p><strong>Payment Hash:</strong><br>
                    <textarea readonly style="width: 100%; ">${data.paymentHash}</textarea></p>

                `;
                document.getElementById('payment-info').style.visibility = 'visible';
                if (window.pollingInterval) {
                    clearInterval(window.pollingInterval);
                }
                window.pollingInterval = setInterval(async () => {
                    try {
                        const pollResponse = await fetch(`https://hook.eu2.make.com/8x4us2mg56ppntjcjx8241a8yf7u2nad?paymenthash=${encodeURIComponent(data.paymentHash)}`);
                        const pollData = await pollResponse.json();
                        if (pollData === 2) {
                            clearInterval(window.pollingInterval);
                            document.getElementById('input-group').style.visibility='visible';
                            document.getElementById('payment-info').style.visibility = 'hidden';
                            // Clear previous QR code
                            const canvas = document.getElementById('canvas');
                            const context = canvas.getContext('2d');
                            context.clearRect(0, 0, canvas.width, canvas.height);
                        }
                        else if (pollData === 1) {
                            clearInterval(window.pollingInterval);
                            document.getElementById('payment-info').innerHTML += `<p><strong>Payment received!</strong></p>`;
                            document.getElementById('payment-info').style.backgroundColor = '#ccffcc';
                            // Draw a green checkmark over the QR code
                            const canvas = document.getElementById('canvas');
                            const ctx = canvas.getContext('2d');
                            ctx.strokeStyle = '#00cc00';
                            ctx.lineWidth = 20;
                            ctx.beginPath();
                            const centerX = canvas.width / 2;
                            const centerY = canvas.height / 2;
                            // First line of the checkmark
                            ctx.moveTo(centerX - 60, centerY);
                            ctx.lineTo(centerX - 20, centerY + 40);
                            // Second line of the checkmark
                            ctx.lineTo(centerX + 60, centerY - 40);
                            ctx.stroke();
                        }
                    } catch (err) {
                        // Optionally handle polling errors
                    }
                }, 3000);


            } catch (error) {
                console.error('Error:', error);
                alert('Failed to generate invoice. Please try again.');
            }
        }
    </script>
</body>
</html>