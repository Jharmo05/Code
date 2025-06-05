<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Caballo de Ajedrez</title>
    <style>
        body { 
            font-family: Arial, sans-serif; 
            margin: 20px; 
            padding-top: 30px;
            text-align: center; /* Centra el contenido del body */
        }
        h1 {
            margin-bottom: 30px;
        }
        .controls div {
            display: inline-block; /* Mantiene los inputs en línea */
            margin: 0 5px; /* Espacio entre los elementos de control */
        }
        label {
            margin-right: 5px;
        }
        input[type="number"] { 
            width: 40px; /* Ancho reducido para los números */
            padding: 5px;
        }
        button { 
            padding: 6px 12px; 
            margin-left: 10px;
            cursor: pointer; 
        }
        #resultsArea { 
            margin-top: 25px; 
            white-space: pre-wrap; /* Respeta los saltos de línea (\n) en el texto */
            font-family: monospace; /* Fuente monoespaciada para mejor alineación de números */
            font-size: 1em; /* Tamaño de fuente para los resultados */
            line-height: 1.6; /* Espaciado entre líneas */
            /* Si quieres que el bloque de texto esté centrado pero el texto dentro alineado a la izquierda:
            display: inline-block; 
            text-align: left; 
            */
        }
        .error { 
            color: red; 
            font-weight: bold; 
        }
    </style>
</head>
<body>
    <h1>Caballo de Ajedrez</h1>

    <div class="controls">
        <div>
            <label for="rowInput">Fila:</label>
            <input type="number" id="rowInput" min="1" max="8" value="1">
        </div>
        <div>
            <label for="colInput">Columna:</label>
            <input type="number" id="colInput" min="1" max="8" value="1">
        </div>
        <button id="calculateButton">Calcular Movimientos</button>
    </div>

    <div id="resultsArea">
        </div>

    <script src="script.js"></script>
</body>
</html>

document.addEventListener('DOMContentLoaded', () => {
    const rowInput = document.getElementById('rowInput');
    const colInput = document.getElementById('colInput');
    const calculateButton = document.getElementById('calculateButton');
    const resultsArea = document.getElementById('resultsArea');

    calculateButton.addEventListener('click', () => {
        resultsArea.textContent = ''; // Limpiar resultados previos
        resultsArea.classList.remove('error'); // Limpiar clase de error si existía

        const row = parseInt(rowInput.value);
        const col = parseInt(colInput.value);

        // 1. Validar entrada del usuario
        if (isNaN(row) || isNaN(col) || row < 1 || row > 8 || col < 1 || col > 8) {
            resultsArea.textContent = 'Error: Las coordenadas deben ser números entre 1 y 8.';
            resultsArea.classList.add('error'); // Aplicar estilo de error
            return;
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

        // 4. Presentar los resultados según el formato de la imagen
        if (possibleMoves.length === 0) {
            // Este caso es improbable en un tablero 8x8 si la entrada es válida.
            resultsArea.textContent = `El caballo en ${row} ${col} no tiene movimientos posibles.`;
        } else {
            let resultsText = `El caballo puede saltar de ${row} ${col} a:\n`; // Línea de título
            possibleMoves.forEach(move => {
                resultsText += `${move[0]} ${move[1]}\n`; // Cada movimiento en una nueva línea
            });
            resultsArea.textContent = resultsText.trim(); // Usar textContent y trim para quitar último \n
        }
    });
});
