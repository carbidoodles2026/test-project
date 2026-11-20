
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Scientific Calculator</title>

<style>
* { box-sizing: border-box; }

body {
    margin: 0;
    min-height: 100vh;
    padding: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
    background: linear-gradient(135deg,#101827,#283b59);
    font-family: Arial,sans-serif;
}

.calculator {
    width: 100%;
    max-width: 480px;
    padding: 22px;
    border-radius: 25px;
    background: #202b3c;
    box-shadow: 0 20px 60px #0008;
}

h2 {
    color: white;
    text-align: center;
    margin: 0 0 15px;
}

.top {
    display: flex;
    justify-content: space-between;
    align-items: center;
    color: #b8c6d9;
    margin-bottom: 12px;
    font-size: 13px;
}

.mode {
    border: none;
    background: #16a085;
    color: white;
    padding: 9px 15px;
    border-radius: 9px;
    font-weight: bold;
    cursor: pointer;
}

.display {
    background: #101827;
    color: white;
    padding: 18px;
    border-radius: 15px;
    min-height: 115px;
    text-align: right;
    margin-bottom: 15px;
    overflow: hidden;
}

#expression {
    min-height: 25px;
    color: #aab8cc;
    overflow-wrap: anywhere;
    font-size: 16px;
}

#result {
    font-size: 34px;
    font-weight: bold;
    margin-top: 10px;
    overflow-wrap: anywhere;
}

.buttons {
    display: grid;
    grid-template-columns: repeat(5,1fr);
    gap: 8px;
}

button {
    border: none;
    border-radius: 11px;
    height: 54px;
    background: #34445c;
    color: white;
    font-size: 17px;
    font-weight: bold;
    cursor: pointer;
}

button:hover { filter: brightness(1.25); }
button:active { transform: scale(.95); }

.function { background: #405d80; }
.operator { background: #d58b43; }
.utility { background: #52627a; }
.equals { background: #16a085; }

@media(max-width:380px) {
    .calculator { padding: 12px; }
    .buttons { gap: 5px; }
    button { height: 48px; font-size: 14px; }
}
</style>
</head>

<body>

<div class="calculator">

<h2>SCIENTIFIC CALCULATOR</h2>

<div class="top">
    <span>ADVANCED CALCULATIONS</span>
    <button class="mode" id="angleMode">DEG</button>
</div>

<div class="display">
    <div id="expression">Ready</div>
    <div id="result">0</div>
</div>

<div class="buttons">

<button class="utility" data-action="clear">AC</button>
<button class="utility" data-action="delete">DEL</button>
<button class="utility" data-value="(">(</button>
<button class="utility" data-value=")">)</button>
<button class="operator" data-value="/">÷</button>

<button class="function" data-value="sin(">sin</button>
<button class="function" data-value="cos(">cos</button>
<button class="function" data-value="tan(">tan</button>
<button class="function" data-value="sqrt(">√</button>
<button class="operator" data-value="*">×</button>

<button class="function" data-value="asin(">sin⁻¹</button>
<button class="function" data-value="acos(">cos⁻¹</button>
<button class="function" data-value="atan(">tan⁻¹</button>
<button class="function" data-value="cbrt(">∛</button>
<button class="operator" data-value="-">−</button>

<button class="function" data-value="log(">log</button>
<button class="function" data-value="ln(">ln</button>
<button class="function" data-value="^2">x²</button>
<button class="function" data-value="^3">x³</button>
<button class="operator" data-value="+">+</button>

<button class="function" data-value="^">xʸ</button>
<button class="function" data-value="pi">π</button>
<button class="function" data-value="e">e</button>
<button class="function" data-action="factorial">n!</button>
<button class="function" data-action="percent">%</button>

<button data-value="7">7</button>
<button data-value="8">8</button>
<button data-value="9">9</button>
<button class="function" data-action="inverse">1/x</button>
<button class="function" data-action="sign">±</button>

<button data-value="4">4</button>
<button data-value="5">5</button>
<button data-value="6">6</button>
<button data-value=".">.</button>
<button class="utility" data-action="clear">C</button>

<button data-value="1">1</button>
<button data-value="2">2</button>
<button data-value="3">3</button>
<button data-value="0">0</button>
<button class="equals" data-action="calculate">=</button>

</div>
</div>

<script>
const expressionDisplay = document.getElementById("expression");
const resultDisplay = document.getElementById("result");
const angleButton = document.getElementById("angleMode");

let expression = "";
let isDegree = true;
let finished = false;

function show() {
    expressionDisplay.textContent = expression
        .replaceAll("pi","π")
        .replaceAll("*","×")
        .replaceAll("/","÷")
        .replaceAll("^","^");

    resultDisplay.textContent = expression || "0";
}

function format(n) {
    if (!Number.isFinite(n)) throw Error("Math error");
    return Number(n.toPrecision(12)).toString();
}

function factorial(n) {
    if (!Number.isInteger(n) || n < 0 || n > 170)
        throw Error("Invalid factorial");

    let result = 1;
    for(let i=2;i<=n;i++) result *= i;
    return result;
}

function calculateExpression(source) {
    const allowed = /^(?:[0-9+\-*/().^!]|pi|e|sin|cos|tan|asin|acos|atan|sqrt|cbrt|log|ln|\s)+$/;

    if (!allowed.test(source)) throw Error("Invalid expression");

    let code = source
        .replace(/\bpi\b/g, "Math.PI")
        .replace(/\be\b/g, "Math.E")
        .replace(/\bsqrt\b/g, "Math.sqrt")
        .replace(/\bcbrt\b/g, "Math.cbrt")
        .replace(/\blog\b/g, "Math.log10")
        .replace(/\bln\b/g, "Math.log")
        .replace(/\bsin\b/g, "sin")
        .replace(/\bcos\b/g, "cos")
        .replace(/\btan\b/g, "tan")
        .replace(/\basin\b/g, "asin")
        .replace(/\bacos\b/g, "acos")
        .replace(/\batan\b/g, "atan");

    // Convert powers into JavaScript exponentiation.
    code = code.replace(/\^/g, "**");

    // Factorial is calculated before evaluating.
    while (/(\d+(?:\.\d+)?|\([^()]*\))!/.test(code)) {
        code = code.replace(
            /(\d+(?:\.\d+)?|\([^()]*\))!/g,
            "fact($1)"
        );
    }

    const toRad = x => x * Math.PI / 180;
    const toDeg = x => x * 180 / Math.PI;

    const sin = x => Math.sin(isDegree ? toRad(x) : x);
    const cos = x => Math.cos(isDegree ? toRad(x) : x);
    const tan = x => Math.tan(isDegree ? toRad(x) : x);
    const asin = x => isDegree ? toDeg(Math.asin(x)) : Math.asin(x);
    const acos = x => isDegree ? toDeg(Math.acos(x)) : Math.acos(x);
    const atan = x => isDegree ? toDeg(Math.atan(x)) : Math.atan(x);

    const fact = factorial;

    return Function(
        "sin","cos","tan","asin","acos","atan","fact",
        '"use strict"; return (' + code + ')'
    )(sin,cos,tan,asin,acos,atan,fact);
}

function calculate() {
    if(!expression) return;

    try {
        const answer = calculateExpression(expression);
        const formatted = format(answer);

        expressionDisplay.textContent = expression
            .replaceAll("pi","π")
            .replaceAll("*","×")
            .replaceAll("/","÷") + " =";

        resultDisplay.textContent = formatted;
        expression = formatted;
        finished = true;

    } catch(error) {
        resultDisplay.textContent = "Error";
        finished = true;
    }
}

function add(value) {
    if(finished) {
        if ("+-*/^".includes(value)) {
            finished = false;
        } else {
            expression = "";
            finished = false;
        }
    }

    const last = expression.slice(-1);

    if ("+-*/^".includes(value)) {
        if(!expression && value !== "-") return;

        if("+-*/^".includes(last)) {
            expression = expression.slice(0,-1);
        }
    }

    expression += value;
    show();
}

function clearAll() {
    expression = "";
    finished = false;
    expressionDisplay.textContent = "Ready";
    resultDisplay.textContent = "0";
}

function deleteLast() {
    if(finished) {
        clearAll();
        return;
    }

    const functions = ["asin(","acos(","atan(","sqrt(","cbrt(","sin(","cos(","tan(","log(","ln("];
    const match = functions.find(f => expression.endsWith(f));

    expression = match
        ? expression.slice(0,-match.length)
        : expression.slice(0,-1);

    show();
}

function modifyLastNumber(fn) {
    if(!expression || finished) return;

    const match = expression.match(/(\d*\.?\d+)$/);
    if(!match) return;

    const number = fn(Number(match[0]));

    expression = expression.slice(0,-match[0].length) + format(number);
    show();
}

function factorialLast() {
    modifyLastNumber(factorial);
}

function inverse() {
    modifyLastNumber(n => 1/n);
}

function percent() {
    modifyLastNumber(n => n/100);
}

function sign() {
    modifyLastNumber(n => -n);
}

document.querySelector(".buttons").addEventListener("click", event => {
    const button = event.target.closest("button");
    if(!button) return;

    const value = button.dataset.value;
    const action = button.dataset.action;

    if(value !== undefined) add(value);

    if(action === "clear") clearAll();
    if(action === "delete") deleteLast();
    if(action === "calculate") calculate();
    if(action === "factorial") factorialLast();
    if(action === "inverse") inverse();
    if(action === "percent") percent();
    if(action === "sign") sign();
});

angleButton.addEventListener("click", () => {
    isDegree = !isDegree;
    angleButton.textContent = isDegree ? "DEG" : "RAD";
});

document.addEventListener("keydown", event => {
    if(/^[0-9.]$/.test(event.key)) add(event.key);
    if("+-*/^()".includes(event.key)) add(event.key);
    if(event.key === "Enter") calculate();
    if(event.key === "Backspace") deleteLast();
    if(event.key === "Escape") clearAll();
});
</script>

</body>
</html>
