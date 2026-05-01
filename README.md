
const PASSWORD = "2763";

/* ================= LOGIN ================= */
function login(){
  if(document.getElementById("password").value === PASSWORD){
    document.getElementById("login").style.display = "none";
    document.getElementById("app").style.display = "block";
  } else alert("wrong");
}

function openSains(){
  document.getElementById("app").style.display = "none";
  document.getElementById("sains").style.display = "block";
}

function openAsda(){
  document.getElementById("app").style.display = "none";
  document.getElementById("asda").style.display = "block";
}

/* ================= HELPERS ================= */
function padLeft(str, len){
  return str.toString().padStart(len,'0');
}

/* ================= SAINSBURY CHECKSUM ================= */
/* Independent system (91 + product + price) */
function calcSainsCheck(input){
  let sum = 0;

  for(let i = 0; i < input.length; i++){
    const d = parseInt(input[input.length - 1 - i], 10);
    sum += d * (i % 2 === 0 ? 3 : 1);
  }

  return (10 - (sum % 10)) % 10;
}

/* ================= ASDA CHECKSUM ================= */
/* Independent system (510 + EAN + price + 1960) */
function calcAsdaCheck(input){
  let sum = 0;

  for(let i = 0; i < input.length; i++){
    const d = parseInt(input[input.length - 1 - i], 10);
    sum += d * (i % 2 === 0 ? 3 : 1);
  }

  return (10 - (sum % 10)) % 10;
}

/* ================= SAINSBURY GENERATOR ================= */
function genSains(){
  let product = document.getElementById("sainsProduct").value
    .replace(/\D/g,'')
    .slice(0,13);

  product = padLeft(product, 13);

  let price = parseFloat(document.getElementById("sainsPrice").value);
  if(isNaN(price)){
    alert("Invalid price");
    return;
  }

  let priceBlock = Math.round(price * 100)
    .toString()
    .padStart(6,'0');

  const base = `91${product}${priceBlock}`;
  const code = base + calcSainsCheck(base);

  document.getElementById("sainsOut").style.display = "block";
  document.getElementById("sainsText").innerText = code;

  document.getElementById("sainsBarcode").innerHTML = "";
  JsBarcode("#sainsBarcode", String(code));
}

/* ================= ASDA GENERATOR ================= */
function generateAsdaBarcode(ean13, pricePounds){
  const cleanEAN = ean13.replace(/\D/g,'').slice(0,13);

  if(cleanEAN.length !== 13){
    throw new Error("EAN must be 13 digits");
  }

  const priceBlock = Math.round(pricePounds * 100)
    .toString()
    .padStart(5,'0');

  const base = "510" + cleanEAN + priceBlock + "1960";

  const check = calcAsdaCheck(base);

  return base + check;
}

function genAsda(){
  try{
    const ean = document.getElementById("asdaProduct").value;
    const price = parseFloat(document.getElementById("asdaPrice").value);

    if(isNaN(price)){
      alert("Invalid price");
      return;
    }

    const code = generateAsdaBarcode(ean, price);

    document.getElementById("asdaOut").style.display = "block";
    document.getElementById("asdaText").innerText = code;

    document.getElementById("asdaBarcode").innerHTML = "";
    JsBarcode("#asdaBarcode", String(code));

  } catch(err){
    alert(err.message);
  }
}
