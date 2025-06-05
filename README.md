<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Movimientos del Caballo 🐴</title>
    <style>
        body { 
            font-family: Arial, sans-serif; 
            margin: 20px; 
            background-color: #f4f4f4;
            color: #333;
        }
        h1 {
            color: #2c3e50;
            text-align: center;
        }
        label { 
            display: block; 
            margin-top: 15px; 
            margin-bottom: 5px;
            font-weight: bold;
        }
        input[type="number"] { 
            width: 60px; 
            padding: 8px;
            margin-bottom: 10px; 
            border: 1px solid #ccc;
            border-radius: 4px;
            box-sizing: border-box;
        }
        button { 
            padding: 10px 18px; 
            margin-top: 15px; 
            cursor: pointer; 
            background-color: #3498db;
            color: white;
            border: none;
            border-radius: 4px;
            font-size: 16px;
        }
        button:hover {
            background-color: #2980b9;
        }
        #controls {
            background-color: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            margin-bottom: 20px;
            text-align: center;
        }
        #resultsArea { 
            margin-top: 20px; 
            background-color: white;
            padding: 15px; 
            min-height: 50px; 
            border: 1px solid #ddd;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.05);
        }
        .error { 
            color: #e74c3c; 
            font-weight: bold; 
        }
        .move { 
            color: #27ae60; 
            list-style-type: disc;
        }
        #chessboardContainer {
            display: flex;
            justify-content: center;
            margin-top: 20px;
        }
        #chessboard {
            display: grid;
            grid-template-columns: repeat(8, 50px);
            grid-template-rows: repeat(8, 50px);
            width: 400px; /* 8 * 50px */
            height: 400px; /* 8 * 50px */
            border: 3px solid #333;
            box-shadow: 0 5px 15px rgba(0,0,0,0.2);
        }
        .square {
            width: 50px;
            height: 50px;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 11px;
            font-weight: bold;
            box-sizing: border-box; /* Important for grid sizing */
            color: #333; /* Default text color for coordinates */
        }
        .square.light { background-color: #f0d9b5; } /* Beige */
        .square.dark { background-color: #b58863; } /* Brown */
        
        .square.current { 
            background-color: #3498db; /* Blue */
            color: white;
            position: relative; /* For pseudo-element if needed or icon */
        }
        .square.current .knight-icon {
            font-size: 30px; /* Make icon larger */
        }
        .square.possible-move { 
            background-color: #2ecc71; /* Green */
            color: white;
            opacity: 0.8;
        }
        .knight-icon { 
            font-size: 30px; 
            line-height: 1; /* Ensure it doesn't affect square height too much */
        }
    </style>
</head>
<body>
    <h1>Calculadora de Movimientos del Caballo 🐴</h1>

    <div id="controls">
        <div>
            <label for="rowInput">Fila (1-8):</label>
            <input type="number" id="rowInput" min="1" max="8" value="1">
        </div>
        <div>
            <label for="colInput">Columna (1-8):</label>
            <input type="number" id="colInput" min="1" max="8" value="1">
        </div>
        <button id="calculateButton">Calcular Movimientos</button>
    </div>

    <div id="resultsArea">
        <p>Ingresa las coordenadas y presiona "Calcular Movimientos".</p>
    </div>

    <div id="chessboardContainer">
        <div id="chessboard"></div>
    </div>

    <script src="script.js"></script>
</body>
</html>

document.addEventListener('DOMContentLoaded', () => {
    const rowInput = document.getElementById('rowInput');
    const colInput = document.getElementById('colInput');
    const calculateButton = document.getElementById('calculateButton');
    const resultsArea = document.getElementById('resultsArea');
    const chessboardDiv = document.getElementById('chessboard');

    function createChessboard() {
        chessboardDiv.innerHTML = ''; // Limpiar tablero previo
        for (let r = 1; r <= 8; r++) {
            for (let c = 1; c <= 8; c++) {
                const square = document.createElement('div');
                square.classList.add('square');
                // Alternar colores de las casillas
                square.classList.add((r + c) % 2 === 0 ? 'dark' : 'light');
                square.dataset.row = r; // Guardar fila en data attribute
                square.dataset.col = c; // Guardar columna en data attribute
                // Opcional: mostrar coordenadas en cada casilla (puede ser útil para depurar)
                // square.textContent = `(${r},${c})`; 
                chessboardDiv.appendChild(square);
            }
        }
    }

    // Dibujar el tablero inicialmente
    createChessboard();

    calculateButton.addEventListener('click', () => {
        resultsArea.innerHTML = ''; // Limpiar resultados de texto previos
        createChessboard(); // Redibujar el tablero para limpiar resaltados previos

        const row = parseInt(rowInput.value);
        const col = parseInt(colInput.value);

        // 1. Validar entrada del usuario
        if (isNaN(row) || isNaN(col) || row < 1 || row > 8 || col < 1 || col > 8) {
            resultsArea.innerHTML = '<p class="error">Error: Las coordenadas deben ser números entre 1 y 8.</p>';
            return;
        }

        // Marcar la posición actual del caballo en el tablero visual
        const currentSquare = chessboardDiv.querySelector(`.square[data-row='${row}'][data-col='${col}']`);
        if (currentSquare) {
            currentSquare.classList.add('current');
            currentSquare.innerHTML = '<span class="knight-icon">🐴</span>'; // Usar el emoji del caballo
        }

        // 2. Definir los 8 posibles movimientos del caballo (delta fila, delta columna)
        const knightMoves = [
            [2, 1], [2, -1], [-2, 1], [-2, -1],
            [1, 2], [1, -2], [-1, 2], [-1, -2]
        ];

        const possibleMoves = [];

        // 3. Calcular y validar cada movimiento potencial
        knightMoves.forEach(move => {
            const newRow = row + move[0];
            const newCol = col + move[1];

            // Asegurar que las nuevas coordenadas estén dentro del tablero (1-8)
            if (newRow >= 1 && newRow <= 8 && newCol >= 1 && newCol <= 8) {
                possibleMoves.push([newRow, newCol]);
            }
        });

        // 4. Presentar los resultados
        if (possibleMoves.length === 0) {
            // Este caso es improbable en un tablero estándar de 8x8 si la entrada es válida.
            resultsArea.innerHTML = '<p>No hay movimientos posibles desde esta casilla.</p>';
        } else {
            let resultsHTML = '<h3>El caballo puede moverse a:</h3><ul>';
            possibleMoves.forEach(move => {
                resultsHTML += `<li class="move">Fila: ${move[0]}, Columna: ${move[1]}</li>`;
                
                // Resaltar las casillas de movimiento posible en el tablero visual
                const targetSquare = chessboardDiv.querySelector(`.square[data-row='${move[0]}'][data-col='${move[1]}']`);
                if (targetSquare) {
                    targetSquare.classList.add('possible-move');
                    targetSquare.textContent = `(${move[0]},${move[1]})`; // Mostrar coordenadas en la casilla
                }
            });
            resultsHTML += '</ul>';
            resultsArea.innerHTML = resultsHTML;
        }
    });
});
