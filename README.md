# CapZ9.github.io

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Sains Generator</title>
  <script src="https://cdn.jsdelivr.net/npm/jsbarcode@3.11.5/dist/JsBarcode.all.min.js"></script>

  <style>
    * { box-sizing: border-box; }

    body {
      margin: 0;
      height: 100vh;
      font-family: 'Inter', sans-serif;
      background: radial-gradient(circle at top, #ff7a18, #2b0a05);
      display: flex;
      justify-content: center;
      align-items: center;
      color: white;
    }

    .glass {
      width: 360px;
      padding: 24px;
      border-radius: 24px;
      backdrop-filter: blur(20px);
      background: rgba(255,255,255,0.08);
      box-shadow: 0 20px 50px rgba(0,0,0,0.4);
      border: 1px solid rgba(255,255,255,0.15);
      animation: fade 0.4s ease;
    }

    h1 {
      font-size: 20px;
      margin-bottom: 20px;
      text-align: left;
    }

    label {
      font-size: 12px;
      opacity: 0.7;
      display: block;
      margin-top: 12px;
      margin-bottom: 6px;
    }

    input {
      width: 100%;
      padding: 12px;
      border-radius: 12px;
      border: none;
      outline: none;
      background: rgba(255,255,255,0.12);
      color: white;
      font-size: 14px;
    }

    input::placeholder { color: rgba(255,255,255,0.5); }

    .buttons {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 10px;
      margin-top: 10px;
    }

    .chip {
      padding: 10px;
      border-radius: 12px;
      text-align: center;
      background: rgba(255,255,255,0.12);
      cursor: pointer;
      transition: 0.2s;
    }

    .chip:hover { background: rgba(255,255,255,0.25); }

    .generate {
      margin-top: 16px;
      width: 100%;
      padding: 12px;
      border-radius: 14px;
      border: none;
      background: linear-gradient(135deg, #ff7a18, #ffb347);
      color: white;
      font-weight: bold;
      cursor: pointer;
      transition: 0.2s;
    }

    .generate:hover { transform: scale(1.03); }

    .result-card {
      margin-top: 20px;
      background: white;
      color: black;
      border-radius: 16px;
      padding: 16px;
      text-align: center;
    }

    #barcode { margin-top: 10px; }

    #login { text-align: center; }
    #app { display: none; }

    @keyframes fade {
      from { opacity: 0; transform: translateY(10px); }
      to { opacity: 1; transform: translateY(0); }
    }
  </style>
</head>
<body>

<div id="login" class="glass">
  <h1>Enter Password</h1>
  <input type="password" id="password" placeholder="Password">
  <button class="generate" onclick="checkPassword()">Unlock</button>
</div>

<div id="app" class="glass">
  <h1>Sains Generator</h1>

  <label>PRODUCT BARCODE</label>
  <input type="text" id="product" placeholder="01799095">

  <label>SELECT PRICE</label>
  <div class="buttons">
    <div class="chip" onclick="setPrice(10)">10p</div>
    <div class="chip" onclick="setPrice(49)">49p</div>
    <div class="chip" onclick="setPrice(100)">£1</div>
    <div class="chip" onclick="customPrice()">Custom</div>
  </div>

  <input type="number" id="price" placeholder="Custom price (e.g. 150)">

  <button class="generate" onclick="generate()">Generate</button>

  <div class="result-card" id="resultBox" style="display:none;">
    <div id="result"></div>
    <svg id="barcode"></svg>
  </div>
</div>

<script>
  const PASSWORD = "2763";

  function checkPassword() {
    const input = document.getElementById("password").value;
    if (input === PASSWORD) {
      document.getElementById("login").style.display = "none";
      document.getElementById("app").style.display = "block";
    } else {
      alert("Wrong password");
    }
  }

  function setPrice(p) {
    document.getElementById("price").value = p;
  }

  function customPrice() {
    document.getElementById("price").focus();
  }

  function padLeft(str, length) {
    return str.toString().padStart(length, '0');
  }

  function calculateCheckDigit(input) {
    let sum = 0;
    for (let i = 0; i < input.length; i++) {
      const digit = parseInt(input[input.length - 1 - i], 10);
      const weight = (i % 2 === 0) ? 3 : 1;
      sum += digit * weight;
    }
    let checkDigit = (10 - (sum % 10)) % 10;
    if (checkDigit % 2 === 0) {
      checkDigit = (checkDigit + 1) % 10;
    }
    return checkDigit;
  }

  function generateSainsburysBarcode(productCode, price) {
    let numericProduct = productCode.replace(/\D/g, '').slice(0, 13);
    numericProduct = padLeft(numericProduct, 13);
    const paddedPrice = padLeft(price, 6);

    const baseNumber = `91${numericProduct}${paddedPrice}`;
    const checkDigit = calculateCheckDigit(baseNumber);

    return `${baseNumber}${checkDigit}`;
  }

  function generate() {
    const product = document.getElementById("product").value;
    const price = document.getElementById("price").value;

    if (!product || !price) {
      alert("Enter both fields");
      return;
    }

    const barcodeValue = generateSainsburysBarcode(product, price);

    document.getElementById("resultBox").style.display = "block";
    document.getElementById("result").innerText = barcodeValue;

    JsBarcode("#barcode", barcodeValue, {
      format: "CODE128",
      displayValue: true,
      fontSize: 14
    });
  }
</script>

</body>
</html>
