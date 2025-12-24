<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema de Bolão - Mega-Sena</title>
    <style>
        /* --- RESET E BASE --- */
        * { margin: 0; padding: 0; box-sizing: border-box; }
        
        body {
            font-family: 'Segoe UI', system-ui, -apple-system, sans-serif;
            background: linear-gradient(135deg, #1e3c72 0%, #2a5298 100%);
            background-attachment: fixed;
            min-height: 100vh;
            padding: 15px;
            color: #333;
            display: flex;
            justify-content: center;
        }

        .container {
            width: 100%;
            max-width: 800px;
            background: #ffffff;
            border-radius: 16px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
            padding: clamp(15px, 4vw, 30px);
            margin-bottom: 20px;
        }

        /* --- TIPOGRAFIA --- */
        h1 { 
            color: #1e3c72; 
            text-align: center; 
            margin-bottom: 25px; 
            font-size: clamp(1.5rem, 6vw, 2.2rem);
            font-weight: 800;
        }

        h2 { 
            color: #2a5298; 
            margin: 25px 0 15px; 
            font-size: clamp(1.1rem, 4vw, 1.4rem);
            border-bottom: 2px solid #f0f0f0;
            padding-bottom: 10px;
        }

        /* --- NAVEGAÇÃO --- */
        .tab-buttons { 
            display: grid; 
            grid-template-columns: repeat(auto-fit, minmax(120px, 1fr)); 
            gap: 10px; 
            margin-bottom: 30px; 
        }

        .btn {
            padding: 12px;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-weight: 600;
            font-size: 14px;
            transition: all 0.2s;
            background: #f0f2f5;
            color: #555;
        }

        .btn.active {
            background: #2a5298;
            color: white;
            box-shadow: 0 4px 12px rgba(42, 82, 152, 0.3);
        }

        .btn-primary { background: #28a745; color: white; width: 100%; margin-top: 15px; font-size: 16px; }
        .btn-primary:hover { background: #218838; }

        /* --- GRID DA MEGA-SENA --- */
        .numero-grid { 
            display: grid; 
            grid-template-columns: repeat(auto-fill, minmax(45px, 1fr)); 
            gap: 8px; 
            margin: 20px 0;
        }

        .numero-btn {
            aspect-ratio: 1 / 1;
            border: 2px solid #eee;
            background: white;
            border-radius: 50%; /* Bolinhas estilo lotérica */
            font-weight: bold;
            font-size: 16px;
            cursor: pointer;
            transition: 0.2s;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .numero-btn.selected { 
            background: #20c997; 
            color: white; 
            border-color: #1a936f;
            transform: scale(1.1);
        }

        /* --- CARTELAS E RANKING --- */
        .card {
            background: #f8f9fa;
            border-radius: 12px;
            padding: 20px;
            margin-bottom: 15px;
            border-left: 6px solid #2a5298;
        }

        .dezena-container {
            display: flex;
            flex-wrap: wrap;
            gap: 8px;
            margin-top: 12px;
        }

        .dezena {
            width: 35px;
            height: 35px;
            background: #6c757d;
            color: white;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 14px;
            font-weight: bold;
        }

        .dezena.hit { background: #28a745; animation: pulse 1s infinite; }

        @keyframes pulse {
            0% { box-shadow: 0 0 0 0 rgba(40, 167, 69, 0.4); }
            70% { box-shadow: 0 0 0 10px rgba(40, 167, 69, 0); }
            100% { box-shadow: 0 0 0 0 rgba(40, 167, 69, 0); }
        }

        /* --- INPUTS --- */
        input[type="text"] {
            width: 100%;
            padding: 12px;
            border: 2px solid #ddd;
            border-radius: 8px;
            margin-bottom: 10px;
            font-size: 16px;
        }

        /* --- RESPONSIVIDADE ADICIONAL --- */
        @media (max-width: 480px) {
            .container { padding: 15px; border-radius: 0; }
            .numero-grid { gap: 5px; }
            .numero-btn { font-size: 14px; }
        }

        .hidden { display: none; }
    </style>
</head>
<body>

<div class="container">
    <h1>🏆 Bolão da Sorte</h1>

    <div class="tab-buttons">
        <button class="btn active" onclick="showTab('apostar')">Apostar</button>
        <button class="btn" onclick="showTab('participantes')">Participantes</button>
        <button class="btn" onclick="showTab('resultado')">Sortear/Ranking</button>
    </div>

    <div id="tab-apostar" class="tab-content">
        <h2>Nova Aposta</h2>
        <input type="text" id="nome-participante" placeholder="Nome do Jogador">
        <p style="font-size: 0.9rem; color: #666; margin-bottom: 10px;">Selecione 6 números:</p>
        <div class="numero-grid" id="grid-numeros"></div>
        <button class="btn btn-primary" onclick="registrarAposta()">Confirmar Aposta</button>
    </div>

    <div id="tab-participantes" class="tab-content hidden">
        <h2>Apostas Registradas</h2>
        <div id="lista-apostas"></div>
    </div>

    <div id="tab-resultado" class="tab-content hidden">
        <h2>Resultado Oficial</h2>
        <button class="btn btn-primary" style="background:#dc3545;" onclick="realizarSorteio()">Gerar Sorteio Aleatório</button>
        
        <div id="resultado-sorteio" class="mt-1">
            </div>

        <h2 style="margin-top:40px;">Ranking de Acertos</h2>
        <div id="ranking-lista"></div>
    </div>
</div>

<script>
    let apostas = JSON.parse(localStorage.getItem('apostas')) || [];
    let selecionados = [];
    let numerosSorteados = [];

    // Inicializa o grid de 1 a 60
    function initGrid() {
        const grid = document.getElementById('grid-numeros');
        grid.innerHTML = '';
        for (let i = 1; i <= 60; i++) {
            const btn = document.createElement('button');
            btn.className = 'numero-btn';
            btn.innerText = i.toString().padStart(2, '0');
            btn.onclick = () => toggleNumero(i, btn);
            grid.appendChild(btn);
        }
    }

    function toggleNumero(num, btn) {
        if (selecionados.includes(num)) {
            selecionados = selecionados.filter(n => n !== num);
            btn.classList.remove('selected');
        } else if (selecionados.length < 6) {
            selecionados.push(num);
            btn.classList.add('selected');
        }
    }

    function registrarAposta() {
        const nome = document.getElementById('nome-participante').value;
        if (!nome || selecionados.length < 6) {
            alert("Por favor, preencha o nome e escolha 6 números!");
            return;
        }

        apostas.push({ nome, numeros: [...selecionados.sort((a,b) => a-b)] });
        localStorage.setItem('apostas', JSON.stringify(apostas));
        
        // Limpar
        selecionados = [];
        document.getElementById('nome-participante').value = '';
        initGrid();
        alert("Aposta registrada com sucesso!");
        renderApostas();
    }

    function renderApostas() {
        const lista = document.getElementById('lista-apostas');
        lista.innerHTML = apostas.map(aposta => `
            <div class="card">
                <strong>${aposta.nome}</strong>
                <div class="dezena-container">
                    ${aposta.numeros.map(n => `<div class="dezena">${n.toString().padStart(2, '0')}</div>`).join('')}
                </div>
            </div>
        `).join('');
    }

    function realizarSorteio() {
        numerosSorteados = [];
        while(numerosSorteados.length < 6) {
            let n = Math.floor(Math.random() * 60) + 1;
            if(!numerosSorteados.includes(n)) numerosSorteados.push(n);
        }
        numerosSorteados.sort((a,b) => a-b);
        
        const resDiv = document.getElementById('resultado-sorteio');
        resDiv.innerHTML = `
            <h3>Números Sorteados:</h3>
            <div class="dezena-container">
                ${numerosSorteados.map(n => `<div class="dezena" style="background:#e67e22">${n.toString().padStart(2, '0')}</div>`).join('')}
            </div>
        `;
        calcularRanking();
    }

    function calcularRanking() {
        const rankingLista = document.getElementById('ranking-lista');
        
        const resultados = apostas.map(aposta => {
            const acertos = aposta.numeros.filter(n => numerosSorteados.includes(n));
            return { ...aposta, acertos: acertos.length, listaAcertos: acertos };
        });

        resultados.sort((a, b) => b.acertos - a.acertos);

        rankingLista.innerHTML = resultados.map(res => `
            <div class="card" style="border-left-color: ${res.acertos >= 4 ? '#28a745' : '#6c757d'}">
                <div style="display:flex; justify-content:space-between; align-items:center">
                    <strong>${res.nome}</strong>
                    <span class="btn active" style="padding: 2px 10px">${res.acertos} Acertos</span>
                </div>
                <div class="dezena-container">
                    ${res.numeros.map(n => `
                        <div class="dezena ${numerosSorteados.includes(n) ? 'hit' : ''}">
                            ${n.toString().padStart(2, '0')}
                        </div>
                    `).join('')}
                </div>
            </div>
        `).join('');
    }

    function showTab(tab) {
        document.querySelectorAll('.tab-content').forEach(content => content.classList.add('hidden'));
        document.querySelectorAll('.tab-buttons .btn').forEach(btn => btn.classList.remove('active'));
        
        document.getElementById('tab-' + tab).classList.remove('hidden');
        event.currentTarget.classList.add('active');
        
        if(tab === 'participantes') renderApostas();
    }

    // Inicialização
    initGrid();
    renderApostas();
</script>

</body>
</html>
