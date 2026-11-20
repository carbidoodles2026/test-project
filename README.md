
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>My Calculator</title>

<style>
* {
    box-sizing: border-box;
}

body {
    margin: 0;
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    background: linear-gradient(135deg, #172033, #344563);
    font-family: Arial, sans-serif;
    padding: 20px;
}

.calculator {
    width: 100%;
    max-width: 360px;
    background: #202b3c;
    padding: 24px;
    border-radius: 25px;
    box-shadow: 0 20px 50px #0006;
}

h2 {
    color: white;
    text-align: center;
    margin: 0 0 20px;
    font-size: 20px;
}

.display {
    background: #101827;
    color: white;
    border-radius: 15px;
    min-height: 100px;
    padding: 20px 15px;
    margin-bottom: 20px;
    text-align: right;
    overflow: hidden;
}

#expression {
    color: #aab8cc;
    font-size: 15px;
    min-height: 22px;
    overflow-wrap: anywhere;
}

#result {
    font-size: 34px;
    font-weight: bold;
    margin-top: 8px;
    overflow-wrap: anywhere;
}

.buttons {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 10px;
}

button {
    border: none;
    border-radius: 13px;
    height: 65px;
    font-size: 22px;
    font-weight: bold;
    cursor: pointer;
    background: #34445c;
    color: white;
    transition: 0.15s;
}

button:hover {
    filter: brightness(1.2);
}

button:active {
    transform: scale(0.94);
}

.utility {
    background: #52627a;
}

.operator {
    background: #d58b43;
}

.equals {
    background: #16a085;
}

.zero {
    grid-column: span 2;
}

footer {
    color: #aab8cc;
    text-align: center;
    font-size: 12px;
    margin-top: 20px;
}
</style>
</head>

<body>

<div class="calculator">
    <h2>MY CALCULATOR</h2>

    <div class="display">
        <div id="expression"></div>
        <div id="result">0</div>
    </div>

    <div class="buttons">
        <button class="utility" data-action="clear">AC</button>
        <button class="utility" data-action="delete">⌫</button>
        <button class="utility" data-action="percent">%</button>
        <button class="operator" data-value="/">÷</button>

        <button data-value="7">7</button>
        <button data-value="8">8</button>
        <button data-value="9">9</button>
        <button class="operator" data-value="*">×</button>

        <button data-value="4">4</button>
        <button data-value="5">5</button>
        <button data-value="6">6</button>
        <button class="operator" data-value="-">−</button>

        <button data-value="1">1</button>
        <button data-value="2">2</button>
        <button data-value="3">3</button>
        <button class="operator" data-value="+">+</button>

        <button class="utility" data-action="sign">±</button>
        <button class="zero" data-value="0">0</button>
        <button data-value=".">.</button>
        <button class="equals" data-action="calculate">=</button>
    </div>

    <footer>Built with HTML, CSS & JavaScript</footer>
</div>

<script>
const expressionDisplay = document.getElementById("expression");
const resultDisplay = document.getElementById("result");

let expression = "";
let finished = false;

function updateDisplay() {
    expressionDisplay.textContent =
        expression.replace(/\*/g, "×").replace(/\//g, "÷");

    resultDisplay.textContent = expression || "0";
}

function calculate() {
    if (!expression) return;

    try {
        if (!/^[0-9+\-*/.() ]+$/.test(expression)) {
            throw new Error("Invalid");
        }

        let answer = Function(
            '"use strict"; return (' + expression + ')'
        )();

        if (!Number.isFinite(answer)) {
            throw new Error("Invalid");
        }

        expression = String(
            Number(answer.toPrecision(12))
        );

        expressionDisplay.textContent = "Answer";
        resultDisplay.textContent = expression;
        finished = true;

    } catch (error) {
        resultDisplay.textContent = "Error";
        expression = "";
        finished = false;
    }
}

function addValue(value) {
    if (finished) {
        if ("+-*/".includes(value)) {
            finished = false;
        } else {
            expression = "";
            finished = false;
        }
    }

    if (value === ".") {
        const currentNumber = expression.split(/[+\-*/]/).pop();
        if (currentNumber.includes(".")) return;
        if (!currentNumber) expression += "0";
    }

    const last = expression.slice(-1);

    if ("+-*/".includes(value)) {
        if (!expression && value !== "-") return;
        if ("+-*/".includes(last)) {
            expression = expression.slice(0, -1);
        }
    }

    expression += value;
    updateDisplay();
}

function clearAll() {
    expression = "";
    finished = false;
    expressionDisplay.textContent = "";
    resultDisplay.textContent = "0";
}

function deleteLast() {
    if (finished) {
        clearAll();
        return;
    }

    expression = expression.slice(0, -1);
    updateDisplay();
}

function percent() {
    if (!expression || finished) return;

    const match = expression.match(/(\d*\.?\d+)$/);
    if (!match) return;

    const number = Number(match[0]) / 100;
    expression = expression.slice(0, -match[0].length) + number;
    updateDisplay();
}

function changeSign() {
    if (!expression || finished) return;

    const match = expression.match(/(\d*\.?\d+)$/);
    if (!match) return;

    const number = -Number(match[0]);
    expression = expression.slice(0, -match[0].length) + number;
    updateDisplay();
}

document.querySelector(".buttons").addEventListener("click", function(event) {
    const button = event.target.closest("button");
    if (!button) return;

    const action = button.dataset.action;
    const value = button.dataset.value;

    if (value !== undefined) addValue(value);

    if (action === "clear") clearAll();
    if (action === "delete") deleteLast();
    if (action === "percent") percent();
    if (action === "sign") changeSign();
    if (action === "calculate") calculate();
});

document.addEventListener("keydown", function(event) {
    if (/^[0-9.]$/.test(event.key)) addValue(event.key);
    if ("+-*/".includes(event.key)) addValue(event.key);
    if (event.key === "Enter" || event.key === "=") calculate();
    if (event.key === "Backspace") deleteLast();
    if (event.key === "Escape") clearAll();
    if (event.key === "%") percent();
});
</script>

</body>
</html>
