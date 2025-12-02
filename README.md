<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Crypto Wallet Demo</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    body {
      margin: 0;
      font-family: 'Segoe UI', sans-serif;
      background: linear-gradient(to bottom, #0070ba, #005ea6);
      color: white;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
    }
    .slider-container {
      width: 90%;
      max-width: 420px;
      background: rgba(255,255,255,0.2);
      border-radius: 30px;
      padding: 5px;
      margin-bottom: 20px;
    }
    .slider {
      background: white;
      color: #0070ba;
      font-weight: bold;
      padding: 12px;
      border-radius: 25px;
      text-align: center;
      cursor: pointer;
      transition: transform 0.3s ease;
    }
    .container {
      width: 100%;
      max-width: 420px;
      padding: 20px;
      box-sizing: border-box;
      text-align: center;
      display: none;
    }
    .logo {
      font-size: 1.8em;
      font-weight: bold;
      margin-bottom: 15px;
    }
    .balance {
      font-size: 2em;
      margin-bottom: 10px;
    }
    .section {
      background-color: rgba(255,255,255,0.1);
      padding: 15px;
      border-radius: 10px;
      margin-bottom: 15px;
    }
    .crypto-card {
      display: flex;
      justify-content: space-between;
      background-color: #00a8e1;
      padding: 10px 15px;
      border-radius: 8px;
      margin-bottom: 10px;
    }
    .wallet-actions {
      display: flex;
      justify-content: space-around;
      margin-top: 10px;
    }
    .wallet-actions div {
      background-color: white;
      color: #0070ba;
      padding: 10px;
      border-radius: 50%;
      width: 50px;
      height: 50px;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 0.9em;
      font-weight: bold;
      cursor: pointer;
    }
    .button {
      background-color: #00a8e1;
      color: white;
      padding: 12px;
      border: none;
      border-radius: 8px;
      width: 100%;
      font-size: 1em;
      font-weight: bold;
      cursor: pointer;
      margin-top: 15px;
    }
    input, select {
      width: 100%;
      padding: 10px;
      border: none;
      border-radius: 6px;
      margin-bottom: 12px;
      font-size: 1em;
    }
    .spinner {
      margin: 20px auto;
      width: 40px;
      height: 40px;
      border: 4px solid rgba(255,255,255,0.3);
      border-top: 4px solid white;
      border-radius: 50%;
      animation: spin 1s linear infinite;
    }
    @keyframes spin {
      0% { transform: rotate(0deg); }
      100% { transform: rotate(360deg); }
    }
    #receipt {
      background-color: #ffffff;
      color: #003087;
      padding: 20px;
      border-radius: 10px;
      text-align: left;
    }
    #receipt h2 {
      text-align: center;
    }
    #receipt .notice {
      margin-top: 15px;
      background-color: #f0f4f8;
      padding: 12px;
      border-left: 4px solid #0070ba;
    }
  </style>
</head>
<body>
  <!-- Swipe to unlock -->
  <div class="slider-container">
    <div class="slider" id="slider">➡️ Swipe to Unlock</div>
  </div>

  <!-- Wallet dashboard -->
  <div class="container">
    <div class="logo">Crypto Wallet Demo</div>
    <div class="balance">€5,800</div>

    <div class="section">
      <h3>Balances</h3>
      <div class="crypto-card"><span>BTC</span><span>0.25 BTC</span></div>
      <div class="crypto-card"><span>ETH</span><span>2 ETH</span></div>
      <div class="crypto-card"><span>USDT</span><span>500 USDT</span></div>
    </div>

    <div class="section">
      <h3>Wallet</h3>
      <div class="wallet-actions">
        <div>↓</div>
        <div>↑</div>
        <div>⇄</div>
      </div>
      <button class="button" onclick="showStep1()">WITHDRAW</button>
    </div>

    <div class="section" id="step1" style="display:none;">
      <h3>Select withdrawal method</h3>
      <select id="method">
        <option value="BTC">Bitcoin (BTC)</option>
        <option value="ETH">Ethereum (ETH)</option>
        <option value="USDT">Tether (USDT)</option>
      </select>
      <input type="text" id="destination" placeholder="Wallet address">
      <input type="text" placeholder="Account holder name">
      <button class="button" onclick="startLoading()">CONFIRM</button>
    </div>

    <div class="section" id="loading" style="display:none;">
      <div class="spinner"></div>
      <div class="loading-text">Processing transaction...</div>
    </div>

    <div class="section" id="receipt" style="display:none;">
      <h2>Transaction Receipt</h2>
      <p><strong>Status:</strong> Processing ⏳</p>
      <p><strong>Withdrawal Method:</strong> <span id="receiptMethod">—</span></p>
      <p><strong>Destination:</strong> <span id="receiptDestination">—</span></p>
      <p><strong>Amount:</strong> €5,800</p>

      <div class="notice">
        <strong>Notice:</strong>
        <p>This is a demo prototype. No real fees or payments are required.</p>
      </div>
    </div>
  </div>

  <script>
    // Swipe unlock logic
    const slider = document.getElementById('slider');
    let startX = 0;

    slider.addEventListener('touchstart', (e) => {
      startX = e.touches[0].clientX;
    });

    slider.addEventListener('touchmove', (e) => {
      let moveX = e.touches[0].clientX - startX;
      if (moveX > 0 && moveX < 300) {
        slider.style.transform = `translateX(${moveX}px)`;
      }
    });

    slider.addEventListener('touchend', (e) => {
      let moveX = e.changedTouches[0].clientX - startX;
      if (moveX > 200) {
        document.querySelector('.container').style.display = 'block';
        slider.style.display = 'none';
      } else {
        slider.style.transform = 'translateX(0)';
      }
    });

    function showStep1() {
      document.getElementById('step1').style.display = 'block';
    }

    function startLoading() {
      document.getElementById('step1').style.display = 'none';
      document.getElementById('loading').style.display = 'block';

      setTimeout(() => {
        document.getElementById('loading').style.display = 'none';
        document.getElementById('receipt').style.display = 'block';

        const method = document.getElementById('method').value || '—';
        const destination = document.getElementById('destination').value || '—';
        document.getElementById('receiptMethod').textContent = method;
        document.getElementById('receiptDestination').textContent = destination;
      }, 3000); // shorter demo delay
    }
  </script>
</body>
</html>

