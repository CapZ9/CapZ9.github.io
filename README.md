# CapZ9.github.io

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Generator Hub</title>
<script src="https://cdn.jsdelivr.net/npm/jsbarcode@3.11.5/dist/JsBarcode.all.min.js"></script>

<style>
*{box-sizing:border-box}
body{
  margin:0;
  height:100vh;
  font-family:Inter, sans-serif;
  background:linear-gradient(160deg,#000000,#1a1a1a);
  display:flex;
  justify-content:center;
  align-items:center;
  color:white;
}

.glass{
  width:380px;
  padding:24px;
  border-radius:24px;
  backdrop-filter:blur(20px);
  background:rgba(255,255,255,0.05);
  border:1px solid rgba(255,255,255,0.1);
  box-shadow:0 20px 50px rgba(0,0,0,0.6);
}

h1{font-size:20px;margin-bottom:20px}

button{
  width:100%;
  padding:12px;
  border:none;
  border-radius:14px;
  margin-top:10px;
  cursor:pointer;
  font-weight:bold;
}

/* LOGIN */
#app,#sains,#asda{display:none}

/* MENU */
.menu-btn{background:linear-gradient(135deg,#444,#222);color:white}

/* SAINS */
.sains-theme{
  background: radial-gradient(circle at top, #ff7a18, #2b0a05);
}

/* ASDA */
.asda-theme{
  background: radial-gradient(circle at top, #3bd16f, #06210d);
}

input{
  width:100%;
  padding:12px;
  border-radius:12px;
  border:none;
  margin-top:8px;
}

.result{background:white;color:black;margin-top:20px;padding:12px;border-radius:12px;text-align:center}
</style>
</head>

<body>

<!-- LOGIN -->
<div id="login" class="glass">
  <h1>Enter Password</h1>
  <input type="password" id="password">
  <button onclick="login()">Enter</button>
</div>

<!-- MENU -->
<div id="app" class="glass">
  <h1>Select Generator</h1>
  <button class="menu-btn" onclick="openSains()">Sainsbury's Generator</button>
  <button class="menu-btn" onclick="openAsda()">ASDA Generator</button>
</div>

<!-- SAINS -->
<div id="sains" class="glass sains-theme">
  <h1>Sains Generator</h1>
  <input id="sainsProduct" placeholder="Product Code">
  <input id="sainsPrice" placeholder="Price">
  <button onclick="genSains()">Generate</button>
  <div class="result" id="sainsOut" style="display:none">
    <div id="sainsText"></div>
    <svg id="sainsBarcode"></svg>
  </div>
</div>

<!-- ASDA -->
<div id="asda" class="glass asda-theme">
  <h1>ASDA Generator</h1>
  <input id="asdaProduct" placeholder="EAN-13">
  <input id="asdaPrice" placeholder="Price (£ e.g 1.50)">
  <button onclick="genAsda()">Generate</button>
  <div class="result" id="asdaOut" style="display:none">
    <div id="asdaText"></div>
    <svg id="asdaBarcode"></svg>
  </div>
</div>

<script>
const PASSWORD="2763";

function login(){
  if(document.getElementById("password").value===PASSWORD){
    document.getElementById("login").style.display="none";
    document.getElementById("app").style.display="block";
  }else alert("wrong");
}

function openSains(){
  document.getElementById("app").style.display="none";
  document.getElementById("sains").style.display="block";
}

function openAsda(){
  document.getElementById("app").style.display="none";
  document.getElementById("asda").style.display="block";
}

function padLeft(str,len){return str.toString().padStart(len,'0')}

function calcCheck(input){
 let sum=0;
 for(let i=0;i<input.length;i++){
  const d=parseInt(input[input.length-1-i]);
  sum+=d*(i%2===0?3:1);
 }
 return (10-(sum%10))%10;
}

/* SAINS */
function genSains(){
 let p=document.getElementById("sainsProduct").value.replace(/\D/g,'').slice(0,13);
 p=padLeft(p,13);
 let price=padLeft(document.getElementById("sainsPrice").value,6);
 let base=`91${p}${price}`;
 let code=base+calcCheck(base);

 document.getElementById("sainsOut").style.display="block";
 document.getElementById("sainsText").innerText=code;
 JsBarcode("#sainsBarcode",code);
}

/* ✅ FIXED ASDA LOGIC (CORRECT VERSION) */
function generateAsdaBarcode(ean13, pricePounds) {
  const cleanEAN = ean13.replace(/\D/g, '').slice(0, 13);
  if (cleanEAN.length !== 13) {
    throw new Error("EAN must be 13 digits");
  }

  const pricePence = Math.round(pricePounds * 100);
  const priceBlock = pricePence.toString().padStart(5, '0');

  const prefix = "510";
  const constant = "1960";

  const base = prefix + cleanEAN + priceBlock + constant;

  // Correct checksum (RIGHT → LEFT, weight starts at 1)
  let sum = 0;
  for (let i = 0; i < base.length; i++) {
    const digit = parseInt(base[base.length - 1 - i], 10);
    const weight = (i % 2 === 0) ? 1 : 3;
    sum += digit * weight;
  }

  const checkDigit = (10 - (sum % 10)) % 10;

  return base + checkDigit;
}

function genAsda() {
  try {
    const ean = document.getElementById("asdaProduct").value;
    const price = parseFloat(document.getElementById("asdaPrice").value);

    if (isNaN(price)) {
      alert("Enter a valid price");
      return;
    }

    const code = generateAsdaBarcode(ean, price);

    document.getElementById("asdaOut").style.display = "block";
    document.getElementById("asdaText").innerText = code;

    JsBarcode("#asdaBarcode", code);
  } catch (err) {
    alert(err.message);
  }
}
</script>

</body>
</html>
