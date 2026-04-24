# CapZ9.github.io

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Barcode Generator</title>
  <script src="https://cdn.jsdelivr.net/npm/jsbarcode@3.11.5/dist/JsBarcode.all.min.js"></script>
  <style>
    body { font-family: Arial; text-align: center; margin-top: 50px; }
    input { margin: 5px; padding: 8px; }
    button { padding: 10px 15px; }
    #app { display: none; }
  </style>
</head>
<body>

<div id="login">
  <h2>Enter Password</h2>
  <input type="password" id="password" placeholder="Password">
  <button onclick="checkPassword()">Login</button>
</div>

<div id="app">
  <h2>Sainsbury's Barcode Generator</h2>
  <input type="text" id="product" placeholder="Product Code">
  <input type="number" id="price" placeholder="Price (e.g. 150 for £1.50)">
  <br>
  <button onclick="generate()">Generate Barcode</button>
  <h3 id="result"></h3>
  <svg id="barcode"></svg>
</div>

<script>
  const PASSWORD = "1234"; // change this

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

    const barcodeValue = generateSainsburysBarcode(product, price);

    document.getElementById("result").innerText = barcodeValue;

    JsBarcode("#barcode", barcodeValue, {
      format: "CODE128",
      displayValue: true
    });
  }
</script>

</body>
</html>
