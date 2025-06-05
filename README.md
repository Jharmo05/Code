<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Caballo de Ajedrez</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            margin-top: 50px;
        }
        .container {
            text-align: center;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
        .input-group {
            margin-bottom: 20px;
        }
        .input-group label {
            margin-right: 10px;
        }
        .input-group input[type="number"] {
            width: 50px;
            padding: 5px;
            border: 1px solid #ccc;
            border-radius: 4px;
            text-align: center;
        }
        button {
            padding: 8px 15px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
        }
        button:hover {
            background-color: #0056b3;
        }
        #resultado {
            margin-top: 20px;
            white-space: pre-wrap; /* Para mantener los saltos de línea */
        }
        .error {
            color: red;
            font-weight: bold;
        }
    </style>
</head>
<body>
    <h1>Caballo de Ajedrez</h1>

    <div class="container">
        <div class="input-group">
            <label for="fila">Fila:</label>
            <input type="number" id="fila" value="1" min="1" max="8">
            <label for="columna">Columna:</label>
            <input type="number" id="columna" value="1" min="1" max="8">
            <button onclick="calcularMovimientos()">Calcular Movimientos</button>
        </div>

        <div id="resultado">
            <p>El caballo puede saltar de 1 1 a:</p>
            <p>2 3</p>
            <p>3 2</p>
        </div>
    </div>

    <script>
        function calcularMovimientos() {
            const filaInput = document.getElementById('fila');
            const columnaInput = document.getElementById('columna');
            const resultadoDiv = document.getElementById('resultado');

            const fila = parseInt(filaInput.value);
            const columna = parseInt(columnaInput.value);

            // Limpiar resultados anteriores y mensajes de error
            resultadoDiv.innerHTML = '';

            // Validar las coordenadas ingresadas
            if (isNaN(fila) || isNaN(columna) || fila < 1 || fila > 8 || columna < 1 || columna > 8) {
                resultadoDiv.innerHTML = '<p class="error">Coordenada ingresada inválida. Por favor, ingrese un número entre 1 y 8 para la fila y la columna.</p>';
                return;
            }

            // Posibles movimientos del caballo (dr, dc):
            // (delta_fila, delta_columna)
            const movimientosPosibles = [
                [-2, -1], [-2, 1],
                [-1, -2], [-1, 2],
                [1, -2], [1, 2],
                [2, -1], [2, 1]
            ];

            const movimientosValidos = [];

            movimientosPosibles.forEach(movimiento => {
                const nuevaFila = fila + movimiento[0];
                const nuevaColumna = columna + movimiento[1];

                // Verificar si el nuevo movimiento está dentro del tablero (1 a 8)
                if (nuevaFila >= 1 && nuevaFila <= 8 && nuevaColumna >= 1 && nuevaColumna <= 8) {
                    movimientosValidos.push(`${nuevaFila} ${nuevaColumna}`);
                }
            });

            if (movimientosValidos.length > 0) {
                resultadoDiv.innerHTML = `<p>El caballo puede saltar de ${fila} ${columna} a:</p>`;
                movimientosValidos.forEach(mov => {
                    resultadoDiv.innerHTML += `<p>${mov}</p>`;
                });
            } else {
                resultadoDiv.innerHTML = `<p>El caballo en ${fila} ${columna} no tiene movimientos válidos dentro del tablero.</p>`;
            }
        }

        // Simular el estado inicial de la imagen
        window.onload = function() {
            document.getElementById('fila').value = 1;
            document.getElementById('columna').value = 1;
            calcularMovimientos(); // Calcular los movimientos iniciales para que coincida con la imagen
        };
    </script>
</body>
</html>
