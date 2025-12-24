<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bolão Mega-Sena da Virada</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.29/jspdf.plugin.autotable.min.js"></script>

    <style>
        /* --- ESTILOS GERAIS --- */
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #1e3c72 0%, #2a5298 100%);
            min-height: 100vh;
            padding: 20px;
        }
        .container {
            max-width: 800px;
            margin: 0 auto;
            background: white;
            border-radius: 15px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
            padding: 30px;
        }
        h1 { color: #1e3c72; text-align: center; margin-bottom: 30px; font-size: 28px; }
        h2 { color: #2a5298; margin: 20px 0 15px 0; font-size: 22px; border-bottom: 2px solid #2a5298; padding-bottom: 10px; }
        
        /* --- UTILITÁRIOS --- */
        .hidden { display: none !important; }
        
        /* --- BOTÕES --- */
        .btn {
            background: #2a5298; color: white; border: none; padding: 12px 25px;
            border-radius: 8px; cursor: pointer; font-size: 16px; margin: 5px;
            transition: background 0.3s;
        }
        .btn:hover { background: #1e3c72; }
        .btn-danger { background: #dc3545; }
        .btn-danger:hover { background: #c82333; }
        .btn-success { background: #28a745; }
        .btn-success:hover { background: #218838; }
        
        /* --- FORMULÁRIOS --- */
        .form-group { margin-bottom: 20px; }
        label { display: block; margin-bottom: 8px; color: #333; font-weight: 600; }
        input, select {
            width: 100%; padding: 12px; border: 2px solid #ddd;
            border-radius: 8px; font-size: 16px; transition: border 0.3s;
        }
        input:focus { outline: none; border-color: #2a5298; }

        /* --- STATUS --- */
        .status-badge { display: inline-block; padding: 5px 15px; border-radius: 20px; font-weight: bold; font-size: 14px; }
        .status-pago { background: #d4edda; color: #155724; }
        .status-pendente { background: #fff3cd; color: #856404; }
        .bolao-status { text-align: center; padding: 15px; margin: 20px 0; border-radius: 8px; font-size: 18px; font-weight: bold; }
        .bolao-aberto { background: #d4edda; color: #155724; }
        .bolao-fechado { background: #f8d7da; color: #721c24; }

        /* --- CARTELA E NÚMEROS --- */
        .cartela-item { background: #f8f9fa; padding: 15px; margin: 10px 0; border-radius: 8px; border-left: 4px solid #2a5298; }
        .numero-grid { display: grid; grid-template-columns: repeat(10, 1fr); gap: 5px; margin: 20px 0; }
        .numero-btn {
            padding: 10px; border: 1px solid #ddd; background: white; border-radius: 5px;
            cursor: pointer; font-weight: bold; transition: all 0.2s;
        }
        .numero-btn.selected { background: #2a5298; color: white; border-color: #1e3c72; }
        .numero-btn:hover { transform: scale(1.1); }

        /* --- DEZENAS VISUAIS --- */
        .dezena {
            display: inline-block; width: 35px; height: 35px; line-height: 35px;
            text-align: center; background: #6c757d; color: white;
            border-radius: 50%; margin: 2px; font-weight: bold; font-size: 14px;
        }
        .dezena.acerto { background: #28a745; box-shadow: 0 0 10px #28a745; animation: pulse 1s infinite; }
        .dezena.erro { background: #dc3545; opacity: 0.7; }

        /* --- ALERTS --- */
        .alert { padding: 15px; margin: 15px 0; border-radius: 8px; font-weight: 500; }
        .alert-info { background: #d1ecf1; color: #0c5460; border: 1px solid #bee5eb; }
        .alert-warning { background: #fff3cd; color: #856404; border: 1px solid #ffeaa7; }
        .alert-success { background: #d4edda; color: #155724; border: 1px solid #c3e6cb; }
        .alert-danger { background: #f8d7da; color: #721c24; border: 1px solid #f5c6cb; }

        /* --- TAB NAVIGATION --- */
        .tab-buttons { display: flex; gap: 10px; margin-bottom: 20px; flex-wrap: wrap; justify-content: center; }

        /* --- RANKING --- */
        .ranking-card { margin-bottom: 15px; border-left-width: 8px; }
        .rank-1 { border-left-color: #ffd700; background: #fffae6; } /* Ouro */
        .rank-2 { border-left-color: #c0c0c0; background: #f8f9fa; } /* Prata */
        .rank-3 { border-left-color: #cd7f32; background: #fff5ee; } /* Bronze */

        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.1); }
            100% { transform: scale(1); }
        }

        @media (max-width: 600px) {
            .numero-grid { grid-template-columns: repeat(6, 1fr); }
            .btn { width: 100%; margin: 5px 0; }
            .container { padding: 15px; }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🎰 Bolão Mega-Sena da Virada</h1>

        <div id="authScreen">
            <div class="tab-buttons">
                <button class="btn" onclick="showLogin()">Login</button>
                <button class="btn" onclick="showCadastro()">Cadastrar</button>
            </div>

            <div id="loginForm">
                <h2>Login</h2>
                <div class="form-group">
                    <label>CPF:</label>
                    <input type="text" id="loginCpf" placeholder="Digite seu CPF (apenas números)" maxlength="11">
                </div>
                <div class="form-group">
                    <label>Senha (últimos 5 dígitos do CPF):</label>
                    <input type="password" id="loginSenha" maxlength="5" placeholder="Digite a senha">
                </div>
                <button class="btn btn-success" onclick="login()">Entrar</button>
            </div>

            <div id="cadastroForm" class="hidden">
                <h2>Cadastro</h2>
                <div id="cadastroAlert"></div>
                <div class="form-group">
                    <label>Nome Completo:</label>
                    <input type="text" id="cadastroNome" placeholder="Digite seu nome completo">
                </div>
                <div class="form-group">
                    <label>CPF:</label>
                    <input type="text" id="cadastroCpf" placeholder="Digite seu CPF (apenas números)" maxlength="11">
                </div>
                <button class="btn btn-success" onclick="cadastrar()">Cadastrar</button>
            </div>
        </div>

        <div id="participanteArea" class="hidden">
            <div id="bemVindo"></div>
            <div id="statusBolao"></div>
            
            <div class="tab-buttons">
                <button class="btn" onclick="showEnviarCartela()">📝 Nova Cartela</button>
                <button class="btn" onclick="showMinhasCartelas()">🎫 Minhas Cartelas</button>
                <button class="btn" onclick="showTodasCartelas()">👥 Todas Cartelas</button>
                <button class="btn" onclick="showResultados()">🏆 Resultados</button>
                <button class="btn btn-danger" onclick="logout()">Sair</button>
            </div>

            <div id="enviarCartelaSection" class="hidden">
                <h2>Enviar Cartela</h2>
                <div id="cartelaAlert"></div>
                <div class="alert alert-info">Escolha 6 números de 01 a 60.</div>
                <div id="numerosGrid" class="numero-grid"></div>
                <button class="btn btn-success" onclick="enviarCartela()">Confirmar Cartela</button>
            </div>

            <div id="minhasCartelasSection" class="hidden">
                <h2>Minhas Cartelas</h2>
                <div id="minhasCartelasList"></div>
            </div>

            <div id="todasCartelasSection" class="hidden">
                <h2>Todas as Cartelas</h2>
                <div id="todasCartelasList"></div>
            </div>

            <div id="resultadosSection" class="hidden">
                <h2>Conferir Resultados</h2>
                <div id="resultadosContent"></div>
            </div>
        </div>

        <div id="adminArea" class="hidden">
            <div id="bemVindoAdmin"></div>
            <div id="statusBolaoAdmin"></div>

            <div class="tab-buttons">
                <button class="btn" onclick="showGerenciarBolao()">⚙️ Status Bolão</button>
                <button class="btn" onclick="showGerenciarParticipantes()">busts_in_silhouette Participantes</button>
                <button class="btn" onclick="showTodasCartelasAdmin()">👁️ Ver Cartelas</button>
                <button class="btn" onclick="showConferirResultados()">✅ Conferir Sorteio</button>
                <button class="btn btn-danger" onclick="logout()">Sair</button>
            </div>

            <div id="gerenciarBolaoSection" class="hidden">
                <h2>Gerenciar Bolão</h2>
                <div id="bolaoControls"></div>
            </div>

            <div id="gerenciarParticipantesSection" class="hidden">
                <h2>Participantes</h2>
                <div id="participantesList"></div>
            </div>

            <div id="todasCartelasAdminSection" class="hidden">
                <h2>Todas as Cartelas (Admin)</h2>
                <div id="todasCartelasAdminList"></div>
            </div>

            <div id="conferirResultadosSection" class="hidden">
                <h2>Conferir Resultados</h2>
                <div class="alert alert-warning">
                    Insira os números sorteados para gerar o ranking automaticamente.
                </div>
                <div class="form-group">
                    <label>Dezenas Sorteadas (separadas por vírgula ou espaço):</label>
                    <input type="text" id="dezenasSorteadas" placeholder="Ex: 04, 12, 23, 35, 42, 58">
                </div>
                <button class="btn btn-success" onclick="conferirResultados()">Conferir e Publicar</button>
                <button class="btn" onclick="exportarPDF()">📄 Exportar PDF</button>
                <div id="rankingResultados"></div>
            </div>
        </div>
    </div>

    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>

    <script>
        // --- CONFIGURAÇÃO FIREBASE ---
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

        // Variáveis Globais
        const ADMIN_CPF = '03454627508';
        let usuarioAtual = null;
        let listaGlobalCartelas = []; // Usado para exportação PDF

        // --- INICIALIZAÇÃO ---
        window.onload = function() {
            criarNumerosGrid();
        };

        function criarNumerosGrid() {
            const grid = document.getElementById('numerosGrid');
            grid.innerHTML = '';
            for (let i = 1; i <= 60; i++) {
                const btn = document.createElement('button');
                btn.className = 'numero-btn';
                btn.textContent = String(i).padStart(2, '0');
                btn.onclick = () => toggleNumero(btn);
                grid.appendChild(btn);
            }
        }

        // --- LÓGICA DE UI E NAVEGAÇÃO ---
        function esconderTodasSecoes() {
            const sections = [
                'enviarCartelaSection', 'minhasCartelasSection', 'todasCartelasSection', 'resultadosSection'
            ];
            sections.forEach(id => document.getElementById(id).classList.add('hidden'));
        }

        function esconderTodasSecoesAdmin() {
            const sections = [
                'gerenciarBolaoSection', 'gerenciarParticipantesSection', 
                'todasCartelasAdminSection', 'conferirResultadosSection'
            ];
            sections.forEach(id => document.getElementById(id).classList.add('hidden'));
        }

        function mostrarAlerta(id, msg, tipo) {
            const el = document.getElementById(id);
            el.innerHTML = `<div class="alert alert-${tipo}">${msg}</div>`;
            setTimeout(() => el.innerHTML = '', 5000);
        }

        // --- AUTENTICAÇÃO ---
        function showLogin() {
            document.getElementById('loginForm').classList.remove('hidden');
            document.getElementById('cadastroForm').classList.add('hidden');
        }

        function showCadastro() {
            document.getElementById('cadastroForm').classList.remove('hidden');
            document.getElementById('loginForm').classList.add('hidden');
        }

        async function login() {
            const cpf = document.getElementById('loginCpf').value.trim();
            const senha = document.getElementById('loginSenha').value.trim();

            if (!cpf || !senha) return alert('Preencha os campos!');

            try {
                const doc = await db.collection('participantes').doc(cpf).get();
                if (!doc.exists) return alert('CPF não encontrado!');
                
                const senhaCorreta = cpf.slice(-5);
                if (senha !== senhaCorreta) return alert('Senha incorreta!');

                usuarioAtual = { cpf, ...doc.data() };
                document.getElementById('authScreen').classList.add('hidden');

                if (cpf === ADMIN_CPF) {
                    mostrarAreaAdmin();
                } else {
                    mostrarAreaParticipante();
                }
            } catch (error) {
                alert('Erro: ' + error.message);
            }
        }

        async function cadastrar() {
            const nome = document.getElementById('cadastroNome').value.trim();
            const cpf = document.getElementById('cadastroCpf').value.trim();

            if (!nome || cpf.length !== 11) {
                return mostrarAlerta('cadastroAlert', 'Dados inválidos.', 'danger');
            }

            try {
                // Verificar bolão
                const bolaoDoc = await db.collection('config').doc('bolao').get();
                if (bolaoDoc.exists && bolaoDoc.data().status === 'fechado') {
                    return mostrarAlerta('cadastroAlert', 'Bolão Fechado para novos cadastros.', 'danger');
                }

                const doc = await db.collection('participantes').doc(cpf).get();
                if (doc.exists) return mostrarAlerta('cadastroAlert', 'CPF já cadastrado.', 'warning');

                await db.collection('participantes').doc(cpf).set({
                    nome, cpf, statusPagamento: 'PENDENTE',
                    dataCadastro: new Date()
                });

                mostrarAlerta('cadastroAlert', `Sucesso! Senha: ${cpf.slice(-5)}`, 'success');
                setTimeout(() => { showLogin(); document.getElementById('loginCpf').value = cpf; }, 2000);
            } catch (error) {
                mostrarAlerta('cadastroAlert', 'Erro ao cadastrar.', 'danger');
            }
        }

        function logout() {
            location.reload();
        }

        // --- STATUS DO BOLÃO ---
        async function getStatusBolao() {
            const doc = await db.collection('config').doc('bolao').get();
            return doc.exists ? doc.data().status : 'aberto';
        }

        // --- ÁREA DO PARTICIPANTE ---
        async function mostrarAreaParticipante() {
            document.getElementById('participanteArea').classList.remove('hidden');
            document.getElementById('bemVindo').innerHTML = `<h2>Olá, ${usuarioAtual.nome}</h2>`;
            atualizarStatusBadge('statusBolao');
            showEnviarCartela();
        }

        async function atualizarStatusBadge(elementId) {
            const status = await getStatusBolao();
            const el = document.getElementById(elementId);
            if (status === 'aberto') {
                el.innerHTML = `<div class="bolao-status bolao-aberto">🟢 APOSTAS ABERTAS</div>`;
            } else {
                el.innerHTML = `<div class="bolao-status bolao-fechado">🔴 APOSTAS ENCERRADAS</div>`;
            }
            return status;
        }

        // --- ENVIO DE CARTELAS ---
        function toggleNumero(btn) {
            const selecionados = document.querySelectorAll('.numero-btn.selected');
            if (btn.classList.contains('selected')) {
                btn.classList.remove('selected');
            } else if (selecionados.length < 6) {
                btn.classList.add('selected');
            } else {
                alert('Máximo de 6 números!');
            }
        }

        function showEnviarCartela() {
            esconderTodasSecoes();
            document.getElementById('enviarCartelaSection').classList.remove('hidden');
        }

        async function enviarCartela() {
            if (usuarioAtual.statusPagamento !== 'PAGO') {
                return mostrarAlerta('cartelaAlert', 'Aguardando confirmação do seu pagamento pelo Admin.', 'warning');
            }

            const status = await getStatusBolao();
            if (status === 'fechado') return mostrarAlerta('cartelaAlert', 'O bolão já fechou!', 'danger');

            const selecionados = Array.from(document.querySelectorAll('.numero-btn.selected'))
                .map(btn => parseInt(btn.textContent)).sort((a,b) => a-b);

            if (selecionados.length !== 6) return mostrarAlerta('cartelaAlert', 'Selecione 6 números.', 'warning');

            // Validação de duplicidade global
            const todas = await db.collection('cartelas').get();
            const seqAtual = selecionados.join(',');
            
            for (let doc of todas.docs) {
                const seqExiste = doc.data().dezenas.sort((a,b)=>a-b).join(',');
                if (seqExiste === seqAtual) {
                    return mostrarAlerta('cartelaAlert', 'Essa combinação já foi escolhida por outra pessoa!', 'danger');
                }
            }

            // Salvar
            await db.collection('cartelas').add({
                cpf: usuarioAtual.cpf,
                nome: usuarioAtual.nome,
                dezenas: selecionados,
                data: new Date()
            });

            mostrarAlerta('cartelaAlert', 'Cartela salva com sucesso!', 'success');
            document.querySelectorAll('.selected').forEach(b => b.classList.remove('selected'));
            setTimeout(showMinhasCartelas, 1500);
        }

        // --- LISTAGEM DE CARTELAS ---
        async function showMinhasCartelas() {
            esconderTodasSecoes();
            document.getElementById('minhasCartelasSection').classList.remove('hidden');
            
            const snapshot = await db.collection('cartelas').where('cpf', '==', usuarioAtual.cpf).get();
            renderCartelasList(snapshot, 'minhasCartelasList');
        }

        async function showTodasCartelas() {
            esconderTodasSecoes();
            document.getElementById('todasCartelasSection').classList.remove('hidden');
            
            const status = await getStatusBolao();
            if (status === 'aberto') {
                document.getElementById('todasCartelasList').innerHTML = 
                    '<div class="alert alert-info">As cartelas dos outros participantes só ficarão visíveis após o fechamento do bolão.</div>';
                return;
            }

            const snapshot = await db.collection('cartelas').orderBy('nome').get();
            renderCartelasList(snapshot, 'todasCartelasList', true);
        }

        function renderCartelasList(snapshot, elementId, mostrarDono = false) {
            const div = document.getElementById(elementId);
            if (snapshot.empty) {
                div.innerHTML = '<p>Nenhuma cartela encontrada.</p>';
                return;
            }
            
            let html = '';
            snapshot.forEach((doc, idx) => {
                const data = doc.data();
                const dezenasHtml = data.dezenas
                    .sort((a,b)=>a-b)
                    .map(n => `<span class="dezena">${String(n).padStart(2,'0')}</span>`)
                    .join('');
                
                html += `
                    <div class="cartela-item">
                        ${mostrarDono ? `<strong>${data.nome}</strong><br>` : `<strong>Cartela ${idx+1}</strong><br>`}
                        <div style="margin-top:5px">${dezenasHtml}</div>
                    </div>
                `;
            });
            div.innerHTML = html;
        }

        // --- ÁREA DO ADMINISTRADOR ---
        async function mostrarAreaAdmin() {
            document.getElementById('adminArea').classList.remove('hidden');
            document.getElementById('bemVindoAdmin').innerHTML = `<h2>Painel Admin</h2>`;
            atualizarStatusBadge('statusBolaoAdmin');
            showGerenciarBolao();
        }

        async function showGerenciarBolao() {
            esconderTodasSecoesAdmin();
            document.getElementById('gerenciarBolaoSection').classList.remove('hidden');
            
            const status = await getStatusBolao();
            const div = document.getElementById('bolaoControls');
            
            if (status === 'aberto') {
                div.innerHTML = `
                    <div class="alert alert-success">Status: ABERTO</div>
                    <button class="btn btn-danger" onclick="alterarStatusBolao('fechado')">FECHAR BOLÃO</button>
                    <p class="mt-2"><small>Ao fechar, ninguém mais poderá apostar e as cartelas ficarão visíveis para todos.</small></p>
                `;
            } else {
                div.innerHTML = `
                    <div class="alert alert-danger">Status: FECHADO</div>
                    <button class="btn btn-success" onclick="alterarStatusBolao('aberto')">REABRIR BOLÃO</button>
                `;
            }
        }

        async function alterarStatusBolao(novoStatus) {
            if(!confirm(`Tem certeza que deseja mudar para ${novoStatus.toUpperCase()}?`)) return;
            await db.collection('config').doc('bolao').set({ status: novoStatus });
            showGerenciarBolao();
        }

        async function showGerenciarParticipantes() {
            esconderTodasSecoesAdmin();
            document.getElementById('gerenciarParticipantesSection').classList.remove('hidden');
            
            const snaps = await db.collection('participantes').orderBy('nome').get();
            let html = '';
            
            snaps.forEach(doc => {
                const p = doc.data();
                const classeBtn = p.statusPagamento === 'PAGO' ? 'hidden' : ''; // Esconde botão se já pago
                const badge = p.statusPagamento === 'PAGO' ? 'status-pago' : 'status-pendente';
                
                html += `
                    <div class="cartela-item" style="display:flex; justify-content:space-between; align-items:center; flex-wrap:wrap;">
                        <div>
                            <strong>${p.nome}</strong><br>
                            <small>CPF: ${p.cpf}</small><br>
                            <span class="status-badge ${badge}">${p.statusPagamento}</span>
                        </div>
                        <div>
                            <button class="btn btn-success ${classeBtn}" onclick="marcarPago('${p.cpf}')">Confirmar Pagamento</button>
                            <button class="btn btn-danger" onclick="excluirParticipante('${p.cpf}')">Excluir</button>
                        </div>
                    </div>
                `;
            });
            document.getElementById('participantesList').innerHTML = html;
        }

        async function marcarPago(cpf) {
            await db.collection('participantes').doc(cpf).update({ statusPagamento: 'PAGO' });
            showGerenciarParticipantes();
        }

        async function excluirParticipante(cpf) {
            if(!confirm('Isso apagará o usuário e as cartelas dele. Continuar?')) return;
            
            // Apagar cartelas
            const cartelas = await db.collection('cartelas').where('cpf', '==', cpf).get();
            cartelas.forEach(doc => doc.ref.delete());
            
            // Apagar usuário
            await db.collection('participantes').doc(cpf).delete();
            showGerenciarParticipantes();
        }

        async function showTodasCartelasAdmin() {
            esconderTodasSecoesAdmin();
            document.getElementById('todasCartelasAdminSection').classList.remove('hidden');
            const snap = await db.collection('cartelas').orderBy('nome').get();
            renderCartelasList(snap, 'todasCartelasAdminList', true);
        }

        // --- LÓGICA DE RESULTADOS E RANKING (A PARTE QUE FALTAVA) ---
        
        function showConferirResultados() {
            esconderTodasSecoesAdmin();
            document.getElementById('conferirResultadosSection').classList.remove('hidden');
        }

        async function conferirResultados() {
            const input = document.getElementById('dezenasSorteadas').value;
            // Extrair números da string (aceita virgula, espaço, traço)
            const sorteados = input.match(/\d+/g)?.map(Number);

            if (!sorteados || sorteados.length !== 6) {
                alert('Por favor, digite exatamente 6 números válidos.');
                return;
            }

            // Salvar resultado no banco para todos verem
            await db.collection('config').doc('resultados').set({
                dezenas: sorteados,
                dataConferencia: new Date()
            });

            // Buscar todas as cartelas
            const snapshot = await db.collection('cartelas').get();
            let ranking = [];

            snapshot.forEach(doc => {
                const data = doc.data();
                // Interseção entre escolhidos e sorteados
                const acertos = data.dezenas.filter(num => sorteados.includes(num));
                
                ranking.push({
                    id: doc.id,
                    nome: data.nome,
                    escolhidos: data.dezenas.sort((a,b)=>a-b),
                    acertos: acertos.sort((a,b)=>a-b),
                    qtdAcertos: acertos.length
                });
            });

            // Ordenar por quantidade de acertos (descendente)
            ranking.sort((a, b) => b.qtdAcertos - a.qtdAcertos);
            listaGlobalCartelas = ranking; // Guardar para o PDF
            sorteioGlobal = sorteados;

            renderizarRanking(ranking, sorteados, 'rankingResultados');
        }

        // Função compartilhada para Admin e Participante verem o resultado
        function renderizarRanking(ranking, sorteados, elementId) {
            let html = `<h3>Números Sorteados:</h3> 
                        <div style="margin:10px 0">
                            ${sorteados.map(n => `<span class="dezena acerto" style="background:#000">${String(n).padStart(2,'0')}</span>`).join(' ')}
                        </div>
                        <hr style="margin:20px 0">
                        <h3>Ranking de Ganhadores</h3>`;

            if (ranking.length === 0) {
                html += '<p>Nenhuma aposta registrada.</p>';
            } else {
                ranking.forEach((item, index) => {
                    let rankClass = '';
                    let label = `${item.qtdAcertos} acertos`;
                    
                    if (index === 0) rankClass = 'rank-1'; // Ouro
                    else if (index === 1) rankClass = 'rank-2'; // Prata
                    else if (index === 2) rankClass = 'rank-3'; // Bronze

                    if (item.qtdAcertos === 6) label = '🏆 SENA!';
                    else if (item.qtdAcertos === 5) label = '🌟 QUINA!';
                    else if (item.qtdAcertos === 4) label = '✨ QUADRA!';

                    // Gerar bolinhas (verde se acertou, cinza se errou)
                    const bolinhas = item.escolhidos.map(num => {
                        const acertou = sorteados.includes(num);
                        const classe = acertou ? 'acerto' : 'erro';
                        return `<span class="dezena ${classe}">${String(num).padStart(2,'0')}</span>`;
                    }).join(' ');

                    html += `
                        <div class="cartela-item ranking-card ${rankClass}">
                            <div style="display:flex; justify-content:space-between; align-items:center">
                                <strong>#${index+1} ${item.nome}</strong>
                                <span class="status-badge" style="background:#333; color:white">${label}</span>
                            </div>
                            <div style="margin-top:10px">${bolinhas}</div>
                        </div>
                    `;
                });
            }

            document.getElementById(elementId).innerHTML = html;
        }

        // Visualizar Resultados (Participante)
        async function showResultados() {
            esconderTodasSecoes();
            document.getElementById('resultadosSection').classList.remove('hidden');
            
            const docRes = await db.collection('config').doc('resultados').get();
            if (!docRes.exists) {
                document.getElementById('resultadosContent').innerHTML = 
                    '<div class="alert alert-warning">O sorteio ainda não foi conferido pelo administrador.</div>';
                return;
            }

            const sorteados = docRes.data().dezenas;
            
            // Recalcular ranking localmente para exibir
            const snapshot = await db.collection('cartelas').get();
            let ranking = [];
            
            snapshot.forEach(doc => {
                const data = doc.data();
                const acertos = data.dezenas.filter(n => sorteados.includes(n));
                ranking.push({
                    nome: data.nome,
                    escolhidos: data.dezenas.sort((a,b)=>a-b),
                    qtdAcertos: acertos.length
                });
            });
            
            ranking.sort((a,b) => b.qtdAcertos - a.qtdAcertos);
            renderizarRanking(ranking, sorteados, 'resultadosContent');
        }

        // --- EXPORTAÇÃO PDF ---
        function exportarPDF() {
            if (listaGlobalCartelas.length === 0) {
                alert('Confira o resultado primeiro!');
                return;
            }

            const { jsPDF } = window.jspdf;
            const doc = new jsPDF();

            doc.text("Resultado Bolão Mega da Virada", 14, 20);
            doc.setFontSize(10);
            doc.text(`Sorteio: ${sorteioGlobal.join(' - ')}`, 14, 30);

            const tableData = listaGlobalCartelas.map((item, index) => [
                (index + 1).toString(),
                item.nome,
                item.escolhidos.join(', '),
                item.qtdAcertos.toString()
            ]);

            doc.autoTable({
                startY: 40,
                head: [['Pos', 'Participante', 'Dezenas Escolhidas', 'Acertos']],
                body: tableData,
                theme: 'grid',
                headStyles: { fillColor: [42, 82, 152] }
            });

            doc.save('resultado_bolao.pdf');
        }

    </script>
</body>
</html>
