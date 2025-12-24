    <!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bolão Mega-Sena da Virada</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 800px;
            margin: 0 auto;
            background: white;
            border-radius: 15px;
            box-shadow: 0 10px 40px rgba(0,0,0,0.2);
            padding: 30px;
        }

        h1 {
            color: #667eea;
            text-align: center;
            margin-bottom: 30px;
            font-size: 28px;
        }

        h2 {
            color: #667eea;
            margin-bottom: 20px;
            font-size: 22px;
        }

        h3 {
            color: #667eea;
            margin: 20px 0 10px 0;
            font-size: 18px;
        }

        .section {
            display: none;
        }

        .section.active {
            display: block;
        }

        .form-group {
            margin-bottom: 20px;
        }

        label {
            display: block;
            margin-bottom: 8px;
            color: #333;
            font-weight: 600;
        }

        input, select {
            width: 100%;
            padding: 12px;
            border: 2px solid #e0e0e0;
            border-radius: 8px;
            font-size: 16px;
            transition: border-color 0.3s;
        }

        input:focus, select:focus {
            outline: none;
            border-color: #667eea;
        }

        button {
            width: 100%;
            padding: 14px;
            background: #667eea;
            color: white;
            border: none;
            border-radius: 8px;
            font-size: 16px;
            font-weight: 600;
            cursor: pointer;
            transition: background 0.3s;
            margin-top: 10px;
        }

        button:hover {
            background: #5568d3;
        }

        button.secondary {
            background: #6c757d;
        }

        button.secondary:hover {
            background: #5a6268;
        }

        button.danger {
            background: #dc3545;
        }

        button.danger:hover {
            background: #c82333;
        }

        button.success {
            background: #28a745;
        }

        button.success:hover {
            background: #218838;
        }

        .status-badge {
            display: inline-block;
            padding: 5px 12px;
            border-radius: 20px;
            font-size: 12px;
            font-weight: 600;
        }

        .status-pago {
            background: #d4edda;
            color: #155724;
        }

        .status-pendente {
            background: #fff3cd;
            color: #856404;
        }

        .cartela {
            background: #f8f9fa;
            padding: 15px;
            border-radius: 8px;
            margin-bottom: 15px;
        }

        .cartela-numeros {
            display: flex;
            gap: 8px;
            flex-wrap: wrap;
            margin-top: 10px;
        }

        .numero {
            width: 45px;
            height: 45px;
            background: white;
            border: 2px solid #667eea;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: 600;
            color: #667eea;
        }

        .numero.acerto {
            background: #28a745;
            border-color: #28a745;
            color: white;
        }

        .alert {
            padding: 15px;
            border-radius: 8px;
            margin-bottom: 20px;
        }

        .alert-success {
            background: #d4edda;
            color: #155724;
            border: 1px solid #c3e6cb;
        }

        .alert-error {
            background: #f8d7da;
            color: #721c24;
            border: 1px solid #f5c6cb;
        }

        .alert-info {
            background: #d1ecf1;
            color: #0c5460;
            border: 1px solid #bee5eb;
        }

        .participante-item {
            background: #f8f9fa;
            padding: 15px;
            border-radius: 8px;
            margin-bottom: 10px;
        }

        .admin-controls {
            display: flex;
            gap: 10px;
            margin-top: 10px;
        }

        .admin-controls button {
            width: auto;
            padding: 8px 16px;
            margin: 0;
            font-size: 14px;
        }

        .nav-buttons {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
            flex-wrap: wrap;
        }

        .nav-buttons button {
            flex: 1;
            min-width: 150px;
        }

        .numero-input {
            width: 70px;
            text-align: center;
        }

        .dezenas-input-container {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
        }

        @media (max-width: 600px) {
            .container {
                padding: 20px;
            }

            h1 {
                font-size: 24px;
            }

            .nav-buttons button {
                min-width: 100%;
            }

            .numero {
                width: 40px;
                height: 40px;
                font-size: 14px;
            }

            .admin-controls {
                flex-direction: column;
            }

            .admin-controls button {
                width: 100%;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🎰 Bolão Mega-Sena da Virada</h1>
        
        <div id="alertContainer"></div>

        <!-- Seção de Login -->
        <div id="authSection" class="section active">
            <div class="form-group">
                <label>CPF:</label>
                <input type="text" id="cpfLogin" placeholder="Digite seu CPF (apenas números)" maxlength="11">
            </div>
            <div class="form-group">
                <label>Senha:</label>
                <input type="password" id="senhaLogin" placeholder="5 últimos dígitos do CPF">
            </div>
            <button id="btnEntrar">Entrar</button>
            <button class="secondary" id="btnMostrarCadastro">Cadastrar-se</button>
        </div>

        <!-- Seção de Cadastro -->
        <div id="cadastroSection" class="section">
            <div class="form-group">
                <label>Nome Completo:</label>
                <input type="text" id="nomeCompleto" placeholder="Digite seu nome completo">
            </div>
            <div class="form-group">
                <label>CPF:</label>
                <input type="text" id="cpfCadastro" placeholder="Digite seu CPF (apenas números)" maxlength="11">
            </div>
            <button id="btnCadastrar">Cadastrar</button>
            <button class="secondary" id="btnVoltarLogin">Voltar</button>
        </div>

        <!-- Seção Participante -->
        <div id="participanteSection" class="section">
            <div class="nav-buttons">
                <button id="btnEnviarCartela">Enviar Cartela</button>
                <button id="btnMinhasCartelas">Minhas Cartelas</button>
                <button id="btnParticipantes">Participantes</button>
                <button id="btnTodasCartelas">Todas as Cartelas</button>
                <button id="btnConferencia">Conferir Resultado</button>
                <button class="secondary" id="btnLogoutParticipante">Sair</button>
            </div>
            <div id="conteudoParticipante"></div>
        </div>

        <!-- Seção Administrador -->
        <div id="adminSection" class="section">
            <div class="nav-buttons">
                <button id="btnGerenciarBolao">Gerenciar Bolão</button>
                <button id="btnGerenciarParticipantes">Gerenciar Participantes</button>
                <button id="btnTodasCartelasAdmin">Todas as Cartelas</button>
                <button id="btnConferenciaAdmin">Conferir Resultado</button>
                <button class="secondary" id="btnLogoutAdmin">Sair</button>
            </div>
            <div id="conteudoAdmin"></div>
        </div>
    </div>

    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>

    <script>
        // Configuração Firebase
        const firebaseConfig = {
            apiKey: "AIzaSyBo8G3ZcWk4EepN0cHdVBtXc7tGOfcw-yg",
            authDomain: "inscricaosinuca.firebaseapp.com",
            projectId: "inscricaosinuca",
            storageBucket: "inscricaosinuca.firebasestorage.app",
            messagingSenderId: "338241576305",
            appId: "1:338241576305:web:288b6124384c6be4f76ad0",
            measurementId: "G-PEDG30FS2R"
        };

        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();

        const CPF_ADMIN = '03454627508';
        let usuarioAtual = null;

        // Inicializar
        async function inicializar() {
            try {
                const doc = await db.collection('configuracoes').doc('bolao').get();
                if (!doc.exists) {
                    await db.collection('configuracoes').doc('bolao').set({
                        aberto: true,
                        dataCriacao: new Date()
                    });
                }
            } catch (error) {
                console.error('Erro:', error);
            }
        }

        inicializar();

        // Funções auxiliares
        function mostrarAlerta(mensagem, tipo = 'info') {
            const alertContainer = document.getElementById('alertContainer');
            const alertClass = tipo === 'success' ? 'alert-success' : tipo === 'error' ? 'alert-error' : 'alert-info';
            alertContainer.innerHTML = `<div class="alert ${alertClass}">${mensagem}</div>`;
            setTimeout(() => alertContainer.innerHTML = '', 5000);
        }

        function esconderTodasSecoes() {
            document.querySelectorAll('.section').forEach(s => s.classList.remove('active'));
        }

        function formatarCPF(cpf) {
            return cpf.replace(/\D/g, '');
        }

        function gerarSenha(cpf) {
            return cpf.slice(-5);
        }

        async function verificarBolaoAberto() {
            try {
                const doc = await db.collection('configuracoes').doc('bolao').get();
                return doc.exists ? (doc.data().aberto || false) : true;
            } catch (error) {
                return false;
            }
        }

        // Event Listeners
        document.getElementById('btnEntrar').addEventListener('click', fazerLogin);
        document.getElementById('btnMostrarCadastro').addEventListener('click', mostrarCadastro);
        document.getElementById('btnCadastrar').addEventListener('click', cadastrar);
        document.getElementById('btnVoltarLogin').addEventListener('click', voltarLogin);
        document.getElementById('btnLogoutParticipante').addEventListener('click', logout);
        document.getElementById('btnLogoutAdmin').addEventListener('click', logout);
        document.getElementById('btnEnviarCartela').addEventListener('click', mostrarEnviarCartela);
        document.getElementById('btnMinhasCartelas').addEventListener('click', mostrarMinhasCartelas);
        document.getElementById('btnParticipantes').addEventListener('click', mostrarParticipantes);
        document.getElementById('btnTodasCartelas').addEventListener('click', mostrarTodasCartelas);
        document.getElementById('btnConferencia').addEventListener('click', mostrarConferencia);
        document.getElementById('btnGerenciarBolao').addEventListener('click', mostrarGerenciarBolao);
        document.getElementById('btnGerenciarParticipantes').addEventListener('click', mostrarGerenciarParticipantes);
        document.getElementById('btnTodasCartelasAdmin').addEventListener('click', mostrarTodasCartelasAdmin);
        document.getElementById('btnConferenciaAdmin').addEventListener('click', mostrarConferenciaAdmin);

        // Autenticação
        async function fazerLogin() {
            const cpf = formatarCPF(document.getElementById('cpfLogin').value);
            const senha = document.getElementById('senhaLogin').value;

            if (!cpf || !senha) {
                mostrarAlerta('Preencha todos os campos!', 'error');
                return;
            }

            try {
                const doc = await db.collection('participantes').doc(cpf).get();
                
                if (!doc.exists) {
                    mostrarAlerta('CPF não cadastrado!', 'error');
                    return;
                }

                const senhaCorreta = gerarSenha(cpf);
                if (senha !== senhaCorreta) {
                    mostrarAlerta('Senha incorreta!', 'error');
                    return;
                }

                usuarioAtual = { cpf, ...doc.data() };
                
                if (cpf === CPF_ADMIN) {
                    mostrarPainelAdmin();
                } else {
                    mostrarPainelParticipante();
                }

                mostrarAlerta(`Bem-vindo(a), ${usuarioAtual.nome}!`, 'success');
            } catch (error) {
                mostrarAlerta('Erro ao fazer login: ' + error.message, 'error');
            }
        }

        function mostrarCadastro() {
            esconderTodasSecoes();
            document.getElementById('cadastroSection').classList.add('active');
        }

        function voltarLogin() {
            esconderTodasSecoes();
            document.getElementById('authSection').classList.add('active');
        }

        async function cadastrar() {
            const nome = document.getElementById('nomeCompleto').value.trim();
            const cpf = formatarCPF(document.getElementById('cpfCadastro').value);

            if (!nome || !cpf) {
                mostrarAlerta('Preencha todos os campos!', 'error');
                return;
            }

            if (cpf.length !== 11) {
                mostrarAlerta('CPF deve ter 11 dígitos!', 'error');
                return;
            }

            const bolaoAberto = await verificarBolaoAberto();
            if (!bolaoAberto) {
                mostrarAlerta('Bolão fechado! Não é possível fazer novos cadastros.', 'error');
                return;
            }

            try {
                const doc = await db.collection('participantes').doc(cpf).get();
                
                if (doc.exists) {
                    mostrarAlerta('CPF já cadastrado!', 'error');
                    return;
                }

                await db.collection('participantes').doc(cpf).set({
                    nome,
                    cpf,
                    statusPagamento: 'PENDENTE',
                    dataCadastro: new Date()
                });

                const senha = gerarSenha(cpf);
                mostrarAlerta(`Cadastro realizado! Sua senha é: ${senha}`, 'success');
                
                document.getElementById('nomeCompleto').value = '';
                document.getElementById('cpfCadastro').value = '';
                
                setTimeout(voltarLogin, 3000);
            } catch (error) {
                mostrarAlerta('Erro ao cadastrar: ' + error.message, 'error');
            }
        }

        function logout() {
            usuarioAtual = null;
            document.getElementById('cpfLogin').value = '';
            document.getElementById('senhaLogin').value = '';
            voltarLogin();
        }

        // Painéis
        function mostrarPainelParticipante() {
            esconderTodasSecoes();
            document.getElementById('participanteSection').classList.add('active');
            mostrarEnviarCartela();
        }

        function mostrarPainelAdmin() {
            esconderTodasSecoes();
            document.getElementById('adminSection').classList.add('active');
            mostrarGerenciarBolao();
        }

        // Participante - Enviar Cartela
        async function mostrarEnviarCartela() {
            const conteudo = document.getElementById('conteudoParticipante');
            let html = '<h2>Enviar Cartela</h2>';
            
            if (usuarioAtual.statusPagamento === 'PENDENTE') {
                html += '<div class="alert alert-error">⚠️ Seu pagamento está PENDENTE. Aguarde a confirmação do administrador.</div>';
            } else {
                const bolaoAberto = await verificarBolaoAberto();
                if (!bolaoAberto) {
                    html += '<div class="alert alert-error">⚠️ Bolão fechado!</div>';
                } else {
                    const cartelas = await db.collection('cartelas').where('cpf', '==', usuarioAtual.cpf).get();
                    const qtd = cartelas.size;
                    
                    if (qtd >= 2) {
                        html += '<div class="alert alert-info">✅ Você já enviou 2 cartelas (máximo).</div>';
                    } else {
                        html += `
                            <div class="alert alert-success">✅ Você pode enviar ${2 - qtd} cartela(s).</div>
                            <div class="form-group">
                                <label>Já fez o Pix?</label>
                                <select id="confirmaPix">
                                    <option value="">Selecione...</option>
                                    <option value="sim">Sim</option>
                                    <option value="nao">Não</option>
                                </select>
                            </div>
                            <div class="form-group">
                                <label>Digite 6 dezenas (01 a 60):</label>
                                <div class="dezenas-input-container">
                                    ${[1,2,3,4,5,6].map(i => `<input type="number" class="numero-input" id="dez${i}" min="1" max="60" placeholder="${String(i).padStart(2,'0')}">`).join('')}
                                </div>
                            </div>
                            <button onclick="enviarCartela()">Enviar Cartela</button>
                        `;
                    }
                }
            }
            
            conteudo.innerHTML = html;
        }

        async function enviarCartela() {
            const confirmaPix = document.getElementById('confirmaPix').value;
            
            if (!confirmaPix) {
                mostrarAlerta('Confirme se já fez o Pix!', 'error');
                return;
            }

            if (confirmaPix === 'nao') {
                mostrarAlerta('Faça o Pix antes de enviar!', 'error');
                return;
            }

            const dezenas = [];
            for (let i = 1; i <= 6; i++) {
                const valor = parseInt(document.getElementById('dez' + i).value);
                if (!valor || valor < 1 || valor > 60) {
                    mostrarAlerta('Digite 6 números válidos entre 01 e 60!', 'error');
                    return;
                }
                dezenas.push(valor);
            }

            if (new Set(dezenas).size !== 6) {
                mostrarAlerta('Não pode haver números repetidos!', 'error');
                return;
            }

            const dezenasOrdenadas = [...dezenas].sort((a, b) => a - b);
            const sequenciaStr = dezenasOrdenadas.join('-');
            
            try {
                const cartelasExistentes = await db.collection('cartelas').get();
                for (const doc of cartelasExistentes.docs) {
                    const cartelaExistente = doc.data().dezenas.sort((a, b) => a - b).join('-');
                    if (cartelaExistente === sequenciaStr) {
                        mostrarAlerta('Esta sequência já existe!', 'error');
                        return;
                    }
                }

                await db.collection('cartelas').add({
                    cpf: usuarioAtual.cpf,
                    nome: usuarioAtual.nome,
                    dezenas: dezenasOrdenadas,
                    dataEnvio: new Date()
                });

                mostrarAlerta('Cartela enviada!', 'success');
                setTimeout(mostrarEnviarCartela, 2000);
            } catch (error) {
                mostrarAlerta('Erro: ' + error.message, 'error');
            }
        }

        // Participante - Minhas Cartelas
        async function mostrarMinhasCartelas() {
            const conteudo = document.getElementById('conteudoParticipante');
            let html = '<h2>Minhas Cartelas</h2>';
            
            try {
                const cartelas = await db.collection('cartelas').where('cpf', '==', usuarioAtual.cpf).get();
                
                if (cartelas.empty) {
                    html += '<p>Você não enviou nenhuma cartela.</p>';
                } else {
                    cartelas.forEach((doc, i) => {
                        const c = doc.data();
                        html += `
                            <div class="cartela">
                                <strong>Cartela ${i + 1}</strong>
                                <div class="cartela-numeros">
                                    ${c.dezenas.map(n => `<div class="numero">${String(n).padStart(2,'0')}</div>`).join('')}
                                </div>
                            </div>
                        `;
                    });
                }
            } catch (error) {
                html += `<div class="alert alert-error">Erro: ${error.message}</div>`;
            }
            
            conteudo.innerHTML = html;
        }

        // Participante - Participantes
        async function mostrarParticipantes() {
            const conteudo = document.getElementById('conteudoParticipante');
            let html = '<h2>Participantes</h2>';
            
            try {
                const participantes = await db.collection('participantes').orderBy('nome').get();
                
                participantes.forEach(doc => {
                    const p = doc.data();
                    const statusClass = p.statusPagamento === 'PAGO' ? 'status-pago' : 'status-pendente';
                    html += `
                        <div class="participante-item">
                            <strong>${p.nome}</strong><br>
                            <span class="status-badge ${statusClass}">${p.statusPagamento}</span>
                        </div>
                    `;
                });
            } catch (error) {
                html += `<div class="alert alert-error">Erro: ${error.message}</div>`;
            }
            
            conteudo.innerHTML = html;
        }

        // Participante - Todas Cartelas
        async function mostrarTodasCartelas() {
            const conteudo = document.getElementById('conteudoParticipante');
            let html = '<h2>Todas as Cartelas</h2>';
            
            try {
                const bolaoAberto = await verificarBolaoAberto();
                
                if (bolaoAberto) {
                    html += '<div class="alert alert-info">Cartelas visíveis após fechamento do bolão.</div>';
                } else {
                    const cartelas = await db.collection('cartelas').orderBy('nome').get();
                    
                    if (cartelas.empty) {
                        html += '<p>Nenhuma cartela registrada.</p>';
                    } else {
                        const porParticipante = {};
                        cartelas.forEach(doc => {
                            const c = doc.data();
                            if (!porParticipante[c.nome]) porParticipante[c.nome] = [];
                            porParticipante[c.nome].push(c.dezenas);
                        });

                        for (const nome in porParticipante) {
                            html += `<div class="cartela"><strong>${nome}</strong>`;
                            porParticipante[nome].forEach((dez, i) => {
                                html += `
                                    <div style="margin-top: 10px;">
                                        <em>Cartela ${i + 1}:</em>
                                        <div class="cartela-numeros">
                                            ${dez.map(n => `<div class="numero">${String(n).padStart(2,'0')}</div>`).join('')}
                                        </div>
                                    </div>
                                `;
                            });
                            html += '</div>';
                        }
                    }
                }
            } catch (error) {
                html += `<div class="alert alert-error">Erro: ${error.message}</div>`;
            }
            
            conteudo.innerHTML = html;
        }

        // Participante - Conferência
        async function mostrarConferencia() {
            const conteudo = document.getElementById('conteudoParticipante');
            conteudo.innerHTML = `
                <h2>Conferir Resultado</h2>
                <div class="form-group">
                    <label>Digite as 6 dezenas sorteadas:</label>
                    <div class="dezenas-input-container">
                        ${[1,2,3,4,5,6].map(i => `<input type="number" class="numero-input" id="sort${i}" min="1" max="60" placeholder="${String(i).padStart(2,'0')}">`).join('')}
                    </div>
                </div>
                <button onclick="conferirResultado()">Conferir</button>
                <div id="resultadoConferencia"></div>
            `;
        }

        async function conferirResultado() {
            const sorteadas = [];
            for (let i = 1; i <= 6; i++) {
                const valor = parseInt(document.getElementById('sort' + i).value);
                if (!valor || valor < 1 || valor > 60) {
                    mostrarAlerta('Digite 6 números válidos!', 'error');
                    return;
                }
                sorteadas.push(valor);
            }

            if (new Set(sorteadas).size !== 6) {
                mostrarAlerta('Não pode haver números repetidos!', 'error');
                return;
            }

            try {
                const cartelas = await db.collection('cartelas').get();
                const resultados = [];

                cartelas.forEach(doc => {
                    const c = doc.data();
                    const acertos = c.dezenas.filter(n => sorteadas.includes(n));
                    resultados.push({
                        nome: c.nome,
                        dezenas: c.dezenas,
                        acertos: acertos.length,
                        dezenasAcertadas: acertos
                    });
                });

                resultados.sort((a, b) => b.acertos - a.acertos);

                let html = '<div style="margin-top: 20px;">';
                html += '<h3>Dezenas Sorteadas:</h3>';
                html += '<div class="cartela-numeros">';
                sorteadas.forEach(n => html += `<div class="numero acerto">${String(n).padStart(2,'0')}</div>`);
                html += '</div>';

                const agrupados = {
                    6: resultados.filter(r => r.acertos === 6),
                    5: resultados.filter(r => r.acertos === 5),
                    4: resultados.filter(r => r.acertos === 4),
                    3: resultados.filter(r => r.acertos === 3)
                };

                html += '<h3>Resumo:</h3>';
                html += `<p>🏆 Sena (6 acertos): ${agrupados[6].length}</p>`;
                html += `<p>🥈 Quina (5 acertos): ${agrupados[5].length}</p>`;
                html += `<p>🥉 Quadra (4 acertos): ${agrupados[4].length}</p>`;

                html += '<h3>Ranking:</h3>';
                resultados.forEach((r, i) => {
                    if (r.acertos >= 3) {
                        html += `
                            <div class="cartela">
                                <div><span class="ranking-position">${i + 1}º</span><strong>${r.nome}</strong> - ${r.acertos} acertos</div>
                                <div class="cartela-numeros">
                                    ${r.dezenas.map(n => `<div class="numero ${r.dezenasAcertadas.includes(n) ? 'acerto' : ''}">${String(n).padStart(2,'0')}</div>`).join('')}
                                </div>
                            </div>
                        `;
                    }
                });

                html += '</div>';
                document.getElementById('resultadoConferencia').innerHTML = html;
            } catch (error) {
                mostrarAlerta('Erro: ' + error.message, 'error');
            }
        }

        // Admin - Gerenciar Bolão
        async function mostrarGerenciarBolao() {
            const conteudo = document.getElementById('conteudoAdmin');
            const bolaoAberto = await verificarBolaoAberto();
            
            conteudo.innerHTML = `
                <h2>Gerenciar        
