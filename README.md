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

        .section {
            margin-bottom: 30px;
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
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .admin-controls {
            display: flex;
            gap: 10px;
        }

        .admin-controls button {
            width: auto;
            padding: 8px 16px;
            margin: 0;
            font-size: 14px;
        }

        .ranking-item {
            background: #f8f9fa;
            padding: 15px;
            border-radius: 8px;
            margin-bottom: 10px;
        }

        .ranking-position {
            display: inline-block;
            width: 35px;
            height: 35px;
            background: #667eea;
            color: white;
            border-radius: 50%;
            text-align: center;
            line-height: 35px;
            font-weight: 600;
            margin-right: 10px;
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
            width: 60px;
            text-align: center;
        }

        .dezenas-sorteadas {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
            margin-bottom: 20px;
        }

        .hidden {
            display: none !important;
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

        <!-- Seção de Login/Cadastro -->
        <div id="authSection" class="section active">
            <div class="form-group">
                <label>CPF:</label>
                <input type="text" id="cpfLogin" placeholder="Digite seu CPF (apenas números)" maxlength="11">
            </div>
            <div class="form-group">
                <label>Senha:</label>
                <input type="password" id="senhaLogin" placeholder="5 últimos dígitos do CPF">
            </div>
            <button onclick="fazerLogin()">Entrar</button>
            <button class="secondary" onclick="mostrarCadastro()">Cadastrar-se</button>
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
            <button onclick="cadastrar()">Cadastrar</button>
            <button class="secondary" onclick="voltarLogin()">Voltar</button>
        </div>

        <!-- Seção Principal (Participante) -->
        <div id="participanteSection" class="section">
            <div class="nav-buttons">
                <button onclick="mostrarEnviarCartela()">Enviar Cartela</button>
                <button onclick="mostrarMinhasCartelas()">Minhas Cartelas</button>
                <button onclick="mostrarParticipantes()">Participantes</button>
                <button onclick="mostrarTodasCartelas()">Todas as Cartelas</button>
                <button onclick="mostrarConferencia()">Conferir Resultado</button>
                <button class="secondary" onclick="logout()">Sair</button>
            </div>

            <!-- Enviar Cartela -->
            <div id="enviarCartelaDiv" class="hidden">
                <h2>Enviar Cartela</h2>
                <div id="statusPagamentoDiv"></div>
                <div id="formCartela" class="hidden">
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
                        <div style="display: flex; gap: 10px; flex-wrap: wrap;">
                            <input type="number" class="numero-input" id="dez1" min="1" max="60" placeholder="01">
                            <input type="number" class="numero-input" id="dez2" min="1" max="60" placeholder="02">
                            <input type="number" class="numero-input" id="dez3" min="1" max="60" placeholder="03">
                            <input type="number" class="numero-input" id="dez4" min="1" max="60" placeholder="04">
                            <input type="number" class="numero-input" id="dez5" min="1" max="60" placeholder="05">
                            <input type="number" class="numero-input" id="dez6" min="1" max="60" placeholder="06">
                        </div>
                    </div>
                    <button onclick="enviarCartela()">Enviar Cartela</button>
                </div>
            </div>

            <!-- Minhas Cartelas -->
            <div id="minhasCartelasDiv" class="hidden">
                <h2>Minhas Cartelas</h2>
                <div id="listaMinhasCartelas"></div>
            </div>

            <!-- Lista de Participantes -->
            <div id="participantesDiv" class="hidden">
                <h2>Participantes</h2>
                <div id="listaParticipantes"></div>
            </div>

            <!-- Todas as Cartelas -->
            <div id="todasCartelasDiv" class="hidden">
                <h2>Todas as Cartelas</h2>
                <div id="listaTodasCartelas"></div>
            </div>

            <!-- Conferência -->
            <div id="conferenciaDiv" class="hidden">
                <h2>Conferir Resultado</h2>
                <div class="form-group">
                    <label>Digite as 6 dezenas sorteadas:</label>
                    <div style="display: flex; gap: 10px; flex-wrap: wrap;">
                        <input type="number" class="numero-input" id="sort1" min="1" max="60" placeholder="01">
                        <input type="number" class="numero-input" id="sort2" min="1" max="60" placeholder="02">
                        <input type="number" class="numero-input" id="sort3" min="1" max="60" placeholder="03">
                        <input type="number" class="numero-input" id="sort4" min="1" max="60" placeholder="04">
                        <input type="number" class="numero-input" id="sort5" min="1" max="60" placeholder="05">
                        <input type="number" class="numero-input" id="sort6" min="1" max="60" placeholder="06">
                    </div>
                </div>
                <button onclick="conferirResultado()">Conferir</button>
                <div id="resultadoConferencia"></div>
            </div>
        </div>

        <!-- Seção Administrador -->
        <div id="adminSection" class="section">
            <div class="nav-buttons">
                <button onclick="mostrarGerenciarBolao()">Gerenciar Bolão</button>
                <button onclick="mostrarGerenciarParticipantes()">Gerenciar Participantes</button>
                <button onclick="mostrarTodasCartelasAdmin()">Todas as Cartelas</button>
                <button onclick="mostrarConferenciaAdmin()">Conferir Resultado</button>
                <button class="secondary" onclick="logout()">Sair</button>
            </div>

            <!-- Gerenciar Bolão -->
            <div id="gerenciarBolaoDiv" class="hidden">
                <h2>Gerenciar Bolão</h2>
                <div id="statusBolao"></div>
                <button onclick="abrirBolao()">Abrir Bolão</button>
                <button class="danger" onclick="fecharBolao()">Fechar Bolão</button>
            </div>

            <!-- Gerenciar Participantes -->
            <div id="gerenciarParticipantesDiv" class="hidden">
                <h2>Gerenciar Participantes</h2>
                <div id="listaParticipantesAdmin"></div>
            </div>

            <!-- Todas as Cartelas (Admin) -->
            <div id="todasCartelasAdminDiv" class="hidden">
                <h2>Todas as Cartelas</h2>
                <div id="listaTodasCartelasAdmin"></div>
            </div>

            <!-- Conferência (Admin) -->
            <div id="conferenciaAdminDiv" class="hidden">
                <h2>Conferir Resultado</h2>
                <div class="form-group">
                    <label>Digite as 6 dezenas sorteadas:</label>
                    <div style="display: flex; gap: 10px; flex-wrap: wrap;">
                        <input type="number" class="numero-input" id="sortAdmin1" min="1" max="60" placeholder="01">
                        <input type="number" class="numero-input" id="sortAdmin2" min="1" max="60" placeholder="02">
                        <input type="number" class="numero-input" id="sortAdmin3" min="1" max="60" placeholder="03">
                        <input type="number" class="numero-input" id="sortAdmin4" min="1" max="60" placeholder="04">
                        <input type="number" class="numero-input" id="sortAdmin5" min="1" max="60" placeholder="05">
                        <input type="number" class="numero-input" id="sortAdmin6" min="1" max="60" placeholder="06">
                    </div>
                </div>
                <button onclick="conferirResultadoAdmin()">Conferir</button>
                <button onclick="exportarPDF()">Exportar PDF</button>
                <div id="resultadoConferenciaAdmin"></div>
            </div>
        </div>
    </div>

    <!-- Firebase -->
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

        // Variáveis globais
        const CPF_ADMIN = '03454627508';
        let usuarioAtual = null;
        let ultimoResultado = null;

        // Funções auxiliares
        function mostrarAlerta(mensagem, tipo = 'info') {
            const alertContainer = document.getElementById('alertContainer');
            const alertClass = tipo === 'success' ? 'alert-success' : tipo === 'error' ? 'alert-error' : 'alert-info';
            alertContainer.innerHTML = `<div class="alert ${alertClass}">${mensagem}</div>`;
            setTimeout(() => {
                alertContainer.innerHTML = '';
            }, 5000);
        }

        function esconderTodasSecoes() {
            document.querySelectorAll('.section').forEach(s => s.classList.remove('active'));
        }

        function esconderTodosDivs() {
            document.querySelectorAll('.hidden').forEach(d => d.classList.add('hidden'));
            document.querySelectorAll('[id$="Div"]').forEach(d => d.classList.add('hidden'));
        }

        function formatarCPF(cpf) {
            return cpf.replace(/\D/g, '');
        }

        function gerarSenha(cpf) {
            return cpf.slice(-5);
        }

        async function verificarBolaoAberto() {
            const doc = await db.collection('configuracoes').doc('bolao').get();
            if (doc.exists) {
                return doc.data().aberto || false;
            }
            return true;
        }

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
                mostrarAlerta('CPF inválido!', 'error');
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
                
                setTimeout(() => {
                    voltarLogin();
                }, 3000);
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

        // Painel Participante
        function mostrarPainelParticipante() {
            esconderTodasSecoes();
            document.getElementById('participanteSection').classList.add('active');
            mostrarEnviarCartela();
        }

        async function mostrarEnviarCartela() {
            esconderTodosDivs();
            document.getElementById('enviarCartelaDiv').classList.remove('hidden');
            
            const statusDiv = document.getElementById('statusPagamentoDiv');
            const formCartela = document.getElementById('formCartela');
            
            if (usuarioAtual.statusPagamento === 'PENDENTE') {
                statusDiv.innerHTML = '<div class="alert alert-error">⚠️ Seu pagamento está PENDENTE. Aguarde a confirmação do administrador para enviar cartelas.</div>';
                formCartela.classList.add('hidden');
            } else {
                const bolaoAberto = await verificarBolaoAberto();
                if (!bolaoAberto) {
                    statusDiv.innerHTML = '<div class="alert alert-error">⚠️ Bolão fechado! Não é possível enviar mais cartelas.</div>';
                    formCartela.classList.add('hidden');
                    return;
                }

                const cartelas = await db.collection('cartelas').where('cpf', '==', usuarioAtual.cpf).get();
                const qtdCartelas = cartelas.size;
                
                if (qtdCartelas >= 2) {
                    statusDiv.innerHTML = '<div class="alert alert-info">✅ Você já enviou 2 cartelas (limite máximo).</div>';
                    formCartela.classList.add('hidden');
                } else {
                    statusDiv.innerHTML = `<div class="alert alert-success">✅ Pagamento confirmado! Você pode enviar ${2 - qtdCartelas} cartela(s).</div>`;
                    formCartela.classList.remove('hidden');
                }
            }
        }

        async function enviarCartela() {
            const confirmaPix = document.getElementById('confirmaPix').value;
            
            if (!confirmaPix) {
                mostrarAlerta('Confirme se já fez o Pix!', 'error');
                return;
            }

            if (confirmaPix === 'nao') {
                mostrarAlerta('Faça o Pix antes de enviar a cartela!', 'error');
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

            // Verificar duplicatas
            const dezenasSet = new Set(dezenas);
            if (dezenasSet.size !== 6) {
                mostrarAlerta('Não pode haver números repetidos na mesma cartela!', 'error');
                return;
            }

            // Verificar se sequência já existe
            const dezenasOrdenadas = [...dezenas].sort((a, b) => a - b);
            const sequenciaStr = dezenasOrdenadas.join('-');
            
            const cartelasExistentes = await db.collection('cartelas').get();
            for (const doc of cartelasExistentes.docs) {
                const cartelaExistente = doc.data().dezenas.sort((a, b) => a - b).join('-');
                if (cartelaExistente === sequenciaStr) {
                    mostrarAlerta('Esta sequência de números já foi registrada!', 'error');
                    return;
                }
            }

            try {
                await db.collection('cartelas').add({
                    cpf: usuarioAtual.cpf,
                    nome: usuarioAtual.nome,
                    dezenas: dezenasOrdenadas,
                    dataEnvio: new Date()
                });

                mostrarAlerta('Cartela enviada com sucesso!', 'success');
                
                // Limpar campos
                for (let i = 1; i <= 6; i++) {
                    document.getElementById('dez' + i).value = '';
                }
                document.getElementById('confirmaPix').value = '';
                
                setTimeout(() => {
                    mostrarEnviarCartela();
                }, 2000);
            } catch (error) {
                mostrarAlerta('Erro ao enviar cartela: ' + error.message, 'error');
            }
        }

        async function mostrarMinhasCartelas() {
            esconderTodosDivs();
            document.getElementById('minhasCartelasDiv').classList.remove('hidden');
            
            const lista = document.getElementById('listaMinhasCartelas');
            const cartelas = await db.collection('cartelas').where('cpf', '==', usuarioAtual.cpf).get();
            
            if (cartelas.empty) {
                lista.innerHTML = '<p>Você ainda não enviou nenhuma cartela.</p>';
                return;
            }

            let html = '';
            cartelas.forEach((doc, index) => {
                const cartela = doc.data();
                html += `
                    <div class="cartela">
                        <strong>Cartela ${index + 1}</strong>
                        <div class="cartela-numeros">
                            ${cartela.dezenas.map(n => `<div class="numero">${String(n).padStart(2, '0')}</div>`).join('')}
                        </div>
                    </div>
                `;
            });
            
            lista.innerHTML = html;
        }

        async function mostrarParticipantes() {
            esconderTodosDivs();
            document.getElementById('participantesDiv').classList.remove('hidden');
            
            const lista = document.getElementById('listaParticipantes');
            const participantes = await db.collection('participantes').orderBy('nome').get();
            
            let html = '';
            participantes.forEach(doc => {
                const p = doc.data();
                const statusClass = p.statusPagamento === 'PAGO' ? 'status-pago' : 'status-pendente';
                html += `
                    <div class="participante-item">
                        <div>
                            <strong>${p.nome}</strong><br>
                            <span class="status-badge ${statusClass}">${p.statusPagamento}</span>
                        </div>
                    </div>
                `;
            });
            
            lista.innerHTML = html;
        }

        async function mostrarTodasCartelas() {
            esconderTodosDivs();
            document.getElementById('todasCartelasDiv').classList.remove('hidden');
            
            const bolaoAberto = await verificarBolaoAberto();
            const lista = document.getElementById('listaTodasCartelas');
            
            if (bolaoAberto) {
                lista.innerHTML = '<div class="alert alert-info">As cartelas ficarão visíveis após o fechamento do bolão.</div>';
                return;
            }

            const cartelas = await db.collection('cartelas').orderBy('nome').get();
            
            if (cartelas.empty) {
                lista.innerHTML = '<p>Nenhuma cartela registrada ainda.</p>';
                return;
            }

            const cartelasPorParticipante = {};
            cartelas.forEach(doc => {
                const c = doc.data();
                if (!cartelasPorParticipante[c.nome]) {
                    cartelasPorParticipante[c.nome] = [];
                }
                cartelasPorParticipante[c.nome].push(c.dezenas);
            });

            let html = '';
            for (const nome in cartelasPorParticipante) {
                html += `<div class="cartela"><strong>${nome}</strong>`;
                cartelasPorParticipante[nome].forEach((dezenas, index) => {
                    html += `
                        <div style="margin-top: 10px;">
                            <em>Cartela ${index + 1}:</em>
                            <div class="cartela-numeros">
                                ${dezenas.map(n => `<div class="numero">${String(n).padStart(2, '0')}</div>`).join('')}
                            </div>
                        </div>
                    `;
                });
                html += '</div>';
            }
            
            lista.innerHTML = html;
        }

        async function mostrarConferencia() {
            esconderTodosDivs();
            document.getElementById('conferenciaDiv').classList.remove('hidden');
        }

        async function conferirResultado() {
            const sorteadas = [];
            for (let i = 1; i <= 6; i++) {
                const valor = parseInt(document.getElementById('sort' + i).value);
                if (!valor || valor < 1 || valor > 60) {
                    mostrarAlerta('Digite 6 números válidos entre 01 e 60!', 'error');
                    return;
                }
                sorteadas.push(valor);
            }

            const sorteadasSet = new Set
