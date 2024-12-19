<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Calculadora de Bigornas</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #e6f7ff; /* Fundo azul claro */
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }
        .container {
            max-width: 600px;
            width: 100%;
            margin: auto;
            text-align: center; /* Alinhamento centralizado */
        }
        label {
            display: block;
            margin: 10px 0 5px;
        }
        select, input, button {
            width: 90%; /* Ajusta o tamanho para uniformidade */
            padding: 10px;
            margin-bottom: 15px;
            border: 1px solid #ccc;
            border-radius: 5px;
        }
        button {
            background-color: #007bff;
            color: white;
            font-weight: bold;
            cursor: pointer;
        }
        button:hover {
            background-color: #0056b3;
        }
        .result {
            background: #f9f9f9;
            padding: 15px;
            border: 1px solid #ccc;
            border-radius: 5px;
            margin-top: 20px;
        }
        .credits {
            margin-top: 20px;
            font-size: 0.9em;
            color: #555;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Calculadora de Bigornas</h1>
        <p class="credits">Criado por @Yamiwolf, bugs ou sugestões fique livre para enviar, se isso te ajudou de alguma forma e queira contribuir:<br>
        Wallet TON - UQA8TDQlel5soAPZec3H9aylSKMmwGNpOTqbn-NUrFRLiV1S</p>

        <label for="bigorna">Selecione o tipo de bigorna:</label>
        <select id="bigorna">
            <option value="Copper">Copper</option>
            <option value="Iron">Iron</option>
            <option value="Silver">Silver</option>
            <option value="Gold">Gold</option>
        </select>

        <label for="quantidade">Quantidade:</label>
        <input type="number" id="quantidade" min="1" value="1">

        <button onclick="calcular()">Calcular</button>

        <div class="result" id="resultado">
            Insira os valores e clique em calcular para ver o resultado.
        </div>
    </div>

    <script>
        const dadosBigornas = {
            Copper: { cobre: 150, carvao: 200, ferro: 0, prata: 0, ouro: 0, tempo: 6 },
            Iron: { cobre: 300, carvao: 700, ferro: 300, prata: 0, ouro: 0, tempo: 12 },
            Silver: { cobre: 0, carvao: 600, ferro: 0, prata: 400, ouro: 0, tempo: 30, dependencias: { Iron: 4 } },
            Gold: { cobre: 0, carvao: 1200, ferro: 0, prata: 0, ouro: 1000, tempo: 60, dependencias: { Silver: 6 } },
        };

        function calcularRecursivo(tipo, quantidade) {
            const bigorna = dadosBigornas[tipo];
            let total = { cobre: 0, carvao: 0, ferro: 0, prata: 0, ouro: 0, tempo: 0 };

            // Calcular custos de dependências
            if (bigorna.dependencias) {
                for (const [dependencia, qtd] of Object.entries(bigorna.dependencias)) {
                    const subTotal = calcularRecursivo(dependencia, qtd * quantidade);
                    for (const material in subTotal) {
                        if (material !== 'tempo') {
                            total[material] += subTotal[material];
                        }
                    }
                }
            }

            // Somar os materiais diretos
            for (const material in bigorna) {
                if (total.hasOwnProperty(material) && material !== 'tempo') {
                    total[material] += (bigorna[material] || 0) * quantidade;
                }
            }

            // O tempo é calculado apenas para as bigornas solicitadas diretamente
            total.tempo = bigorna.tempo * quantidade;

            return total;
        }

        function calcular() {
            const tipo = document.getElementById('bigorna').value;
            const quantidade = parseInt(document.getElementById('quantidade').value);

            if (quantidade < 1) {
                document.getElementById('resultado').innerText = 'Por favor, insira uma quantidade válida.';
                return;
            }

            const total = calcularRecursivo(tipo, quantidade);

            document.getElementById('resultado').innerHTML = `
                <p><strong>Resultados para ${quantidade} ${tipo}(s):</strong></p>
                <p><strong>Cobre:</strong> ${total.cobre}</p>
                <p><strong>Carvão:</strong> ${total.carvao}</p>
                <p><strong>Ferro:</strong> ${total.ferro}</p>
                <p><strong>Prata:</strong> ${total.prata}</p>
                <p><strong>Ouro:</strong> ${total.ouro}</p>
                <p><strong>Tempo Total:</strong> ${total.tempo} minutos</p>
            `;
        }
    </script>
</body>
</html>