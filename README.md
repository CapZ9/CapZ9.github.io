# CapZ9.github.io

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Barcode Generator</title>
  <script src="https://cdn.jsdelivr.net/npm/jsbarcode@3.11.5/dist/JsBarcode.all.min.js"></script>
  <style>
    body {
      font-family: 'Segoe UI', Tahoma, sans-serif;
      background: linear-gradient(135deg, #1e3c72, #2a5298);
      margin: 0;
      height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      color: #333;
    }

    .card {
      background: white;
      padding: 30px;
      border-radius: 16px;
      box-shadow: 0 10px 30px rgba(0,0,0,0.2);
      width: 320px;
      text-align: center;
      animation: fadeIn 0.4s ease;
    }

    h2 {
      margin-bottom: 20px;
    }

    input {
      width: 100%;
      padding: 10px;
      margin: 8px 0;
      border-radius: 8px;
      border: 1px solid #ccc;
      font-size: 14px;
    }

    button {
      width: 100%;
      padding: 12px;
      margin-top: 10px;
      border: none;
      border-radius: 10px;
      background: #2a5298;
      color: white;
      font-size: 15px;
      cursor: pointer;
      transition: 0.2s;
    }

    button:hover {
      background: #1e3c72;
    }

    #app { display: none; }

    #result {
      margin-top: 15px;
      font-size: 14px;
      word-break: break-all;
    }

    svg {
      margin-top: 15px;
    }

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(10px); }
      to { opacity: 1; transform: translateY(0); }
    }
  </style>
</head>
<body>

<div id="login" class="card">
  <h2>Enter Password</h2>
  <input type="password" id="password" placeholder="Password">
  <button onclick="checkPassword()">Login</button>
</div>

<div id="app" class="card">
  <h2>Barcode Generator</h2>
  <input type="text" id="product" placeholder="Product Code">
  <input type="number" id="price" placeholder="Price (e.g. 150 = £1.50)">
  <button onclick="generate()">Generate</button>
  <div id="result"></div>
  <svg id="barcode"></svg>
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
