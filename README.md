# jfhdjsfg
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AutoMATH-Project</title>
    <style>
        /* --- CSS untuk Tampilan Kalkulator --- */
        html {
            box-sizing: border-box;
            font-size: 62.5%; /* 1rem = 10px */
        }

        *, *::before, *::after {
            box-sizing: inherit;
        }

        body {
            padding: 0;
            margin: 0;
            background-color: #f0f0f0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }
        
        /* Container Kalkulator */
        .calculator {
            border-radius: 10px;
            width: 350px;
            box-shadow: 0 10px 20px rgba(0,0,0,0.19), 0 6px 6px rgba(0,0,0,0.23);
            background-color: #3f3f3f; /* Warna latar belakang gelap */
            padding: 20px;
        }

        /* Layar Kalkulator */
        .calculator-screen {
            width: 100%;
            height: 80px;
            background-color: #2e2e2e;
            color: #ffffff;
            text-align: right;
            padding: 10px 15px;
            font-size: 5rem;
            border: none;
            border-radius: 8px;
            margin-bottom: 15px;
            pointer-events: none; /* Mencegah input manual */
        }

        /* Tombol-tombol */
        .calculator-keys {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 10px;
        }

        .calculator-keys button {
            height: 60px;
            background-color: #606060;
            color: #fff;
            font-size: 2.8rem;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            transition: background-color 0.15s ease;
        }

        .calculator-keys button:hover {
            background-color: #757575;
        }

        /* Tombol Operator */
        .operator {
            background-color: #ff9500 !important;
        }

        .operator:hover {
            background-color: #e68a00 !important;
        }

        /* Tombol Clear */
        .all-clear {
            background-color: #a6a6a6 !important;
            color: #000 !important;
        }
        
        .all-clear:hover {
            background-color: #bfbfbf !important;
        }

        /* Tombol Sama Dengan */
        .equal-sign {
            grid-column: 4 / 5;
            grid-row: 5 / 7;
            background-color: #4CAF50 !important;
            height: 130px !important; /* Menyatukan 2 baris */
        }

        .equal-sign:hover {
            background-color: #45a049 !important;
        }

        /* Tombol Nol */
        [value="0"] {
            grid-column: span 2; /* Membuat tombol 0 lebih lebar */
        }
    </style>
</head>
<body>
    <div class="calculator">
        <h1 style="color: white; font-size: 2rem; text-align: center; margin-bottom: 5px;">AutoMATH-Project</h1>
        <input type="text" class="calculator-screen" value="0" disabled />
        
        <div class="calculator-keys">
            <button type="button" class="all-clear" value="all-clear">AC</button>
            <button type="button" class="operator" value="/">/</button>
            <button type="button" class="operator" value="*">&times;</button>
            <button type="button" class="operator" value="-">-</button>

            <button type="button" value="7">7</button>
            <button type="button" value="8">8</button>
            <button type="button" value="9">9</button>
            
            <button type="button" value="4">4</button>
            <button type="button" value="5">5</button>
            <button type="button" value="6">6</button>

            <button type="button" value="1">1</button>
            <button type="button" value="2">2</button>
            <button type="button" value="3">3</button>

            <button type="button" value="0">0</button>
            <button type="button" class="decimal" value=".">.</button>
            <button type="button" class="operator equal-sign" value="=">=</button>
            <button type="button" class="operator" value="+">+</button>
        </div>
    </div>

    <script>
        // --- JavaScript untuk Fungsionalitas Kalkulator ---
        const calculator = {
            displayValue: '0',
            firstOperand: null,
            waitingForSecondOperand: false,
            operator: null,
        };

        const display = document.querySelector('.calculator-screen');
        const keys = document.querySelector('.calculator-keys');

        function updateDisplay() {
            display.value = calculator.displayValue;
        }

        function inputDigit(digit) {
            const { displayValue, waitingForSecondOperand } = calculator;

            if (waitingForSecondOperand === true) {
                calculator.displayValue = digit;
                calculator.waitingForSecondOperand = false;
            } else {
                calculator.displayValue = displayValue === '0' ? digit : displayValue + digit;
            }
        }

        function inputDecimal(dot) {
            if (calculator.waitingForSecondOperand === true) {
                calculator.displayValue = '0.';
                calculator.waitingForSecondOperand = false;
                return;
            }
            if (!calculator.displayValue.includes(dot)) {
                calculator.displayValue += dot;
            }
        }

        function handleOperator(nextOperator) {
            const { firstOperand, displayValue, operator } = calculator;
            const inputValue = parseFloat(displayValue);

            if (operator && calculator.waitingForSecondOperand) {
                calculator.operator = nextOperator;
                return;
            }

            if (firstOperand === null) {
                calculator.firstOperand = inputValue;
            } else if (operator) {
                const result = performCalculation[operator](firstOperand, inputValue);

                // Mengatasi pembulatan masalah floating point (Opsional)
                calculator.displayValue = String(parseFloat(result.toFixed(7))); 
                calculator.firstOperand = result;
            }

            calculator.waitingForSecondOperand = true;
            calculator.operator = nextOperator;
        }

        const performCalculation = {
            '/': (firstOperand, secondOperand) => firstOperand / secondOperand,
            '*': (firstOperand, secondOperand) => firstOperand * secondOperand,
            '+': (firstOperand, secondOperand) => firstOperand + secondOperand,
            '-': (firstOperand, secondOperand) => firstOperand - secondOperand,
            '=': (firstOperand, secondOperand) => secondOperand
        };

        function resetCalculator() {
            calculator.displayValue = '0';
            calculator.firstOperand = null;
            calculator.waitingForSecondOperand = false;
            calculator.operator = null;
        }

        // Event Listener Utama
        keys.addEventListener('click', (event) => {
            const { target } = event;
            const { value } = target;

            if (!target.matches('button')) {
                return;
            }

            if (target.classList.contains('operator')) {
                handleOperator(value);
                updateDisplay();
                return;
            }

            if (target.classList.contains('decimal')) {
                inputDecimal(value);
                updateDisplay();
                return;
            }

            if (target.classList.contains('all-clear')) {
                resetCalculator();
                updateDisplay();
                return;
            }

            inputDigit(value);
            updateDisplay();
        });

        updateDisplay(); // Inisialisasi tampilan awal
    </script>
</body>
</html>
