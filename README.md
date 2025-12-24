<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bolão Mega da Virada</title>
    
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-firestore-compat.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.25/jspdf.plugin.autotable.min.js"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">

    <style>
        /* --- ESTILOS CSS (Mobile First) --- */
        :root {
            --primary: #209869; /* Verde Mega Sena */
            --secondary: #f4ab03; /* Amarelo Ouro */
            --dark: #1a1a1a;
            --light: #f4f4f4;
            --danger: #e74c3c;
            --success: #27ae60;
        }

        body { font-family: 'Segoe UI', sans-serif; background-color: var(--light); margin: 0; padding: 0; color: var(--dark); }
        .container { max-width: 600px; margin: 0 auto; padding: 20px; background: white; min-height: 100vh; box-shadow: 0 0 10px rgba(0,0,0,0.1); }
        
        h1, h2, h3 { text-align: center; color: var(--primary); }
        .logo { display: block; margin: 0 auto 20px; font-size: 3rem; color: var(--primary); }

        /* Formulários e Inputs */
        input, select, button { width: 100%; padding: 12px; margin-bottom: 10px; border-radius: 8px; border: 1px solid #ccc; box-sizing: border-box; }
        button { background-color: var(--primary); color: white; border: none; font-weight: bold; cursor: pointer; transition: 0.3s; }
        button:hover { background-color: #166e4b; }
        button.secondary { background-color: #666; }
        button.danger { background-color: var(--danger); }
        button.download { background-color: var(--secondary); color: #000; }

        /* Cards e Listas */
        .card { border: 1px solid #ddd; padding: 15px; border-radius: 8px; margin-bottom: 10px; background: #fff; }
        .card.pago { border-left: 5px solid var(--success); }
        .card.pendente { border-left: 5px solid var(--danger); }
        
        .badge { padding: 5px 10px; border-radius: 20px; font-size: 0.8em; color: white; }
        .bg-pago { background-color: var(--success); }
        .bg-pendente { background-color: var(--danger); }

        /* Área de Apostas */
        .bet-grid { display: grid; grid-template-columns: repeat(6, 1fr); gap: 5px; margin-bottom: 10px; }
        .bet-input { text-align: center; font-size: 1.2em; padding: 5px; }

        /* Bolinhas do sorteio */
        .ball { display: inline-block; width: 30px; height: 30px; background: var(--primary); color: white; border-radius: 50%; text-align: center; line-height: 30px; margin: 2px; font-weight: bold; font-size: 0.9em; }
        .ball.hit { background: var(--secondary); color: black; transform: scale(1.1); box-shadow: 0 0 5px rgba(0,0,0,0.3); }

        /* Utilitários */
        .hidden { display: none !important; }
        .text-center { text-align: center; }
        .admin-panel { border: 2px dashed var(--secondary); padding: 10px; margin-top: 20px; background: #fffbf0; }
        
        /* Modal de Loading */
        #loading { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(255,255,255,0.9); z-index: 999; display: flex; justify-content: center; align-items: center; flex-direction: column; }
    </style>
</head>
<body>

    <div id="loading">
        <i class="fas fa-spinner fa-spin fa-3x" style="color:var(--primary)"></i>
        <p>Carregando Bolão...</p>
    </div>

    <div class="container">
        <i class="fas fa-clover logo"></i>
        <h1>Bolão da Virada</h1>

        <div id="auth-screen">
            <div class="card">
                <h3>Acesso</h3>
                <input type="text" id="auth-cpf" placeholder="CPF (somente números)" maxlength="11">
                <input type="text" id="auth-name" placeholder="Nome Completo (apenas para cadastro)" class="hidden">
                <p id="auth-msg" class="text-center" style="font-size: 0.9em; color: #666;"></p>
                
                <button onclick="handleLogin()" id="btn-login">Entrar</button>
                <button onclick="toggleRegister()" id="btn-toggle" class="secondary">Criar Conta</button>
            </div>
        </div>

        <div id="user-dashboard" class="hidden">
            <div style="display: flex; justify-content: space-between; align-items: center;">
                <h2 id="user-greeting">Olá, Usuário</h2>
                <button onclick="logout()" style="width: auto; padding: 5px 10px; background: #666;">Sair</button>
            </div>

            <div class="card text-center" id="payment-status-card">
                <p>Status do Pagamento:</p>
                <span id="user-status-badge" class="badge">Carregando...</span>
                <div id="pix-area" class="hidden" style="margin-top: 10px;">
                    <p><strong>Chave Pix:</strong> SEU_PIX_AQUI</p>
                    <button onclick="alert('Aguarde a confirmação do ADM!')" class="secondary">Já fiz o Pix</button>
                </div>
            </div>

            <div id="betting-area" class="hidden">
                <h3>Minhas Cartelas (<span id="bet-count">0</span>/2)</h3>
                <div class="card">
                    <p>Escolha 6 números (01-60)</p>
                    <div class="bet-grid" id="input-bet-container">
                        </div>
                    <button onclick="submitBet()">Enviar Cartela</button>
                </div>
            </div>
            
            <div id="my-bets-list"></div>

            <button onclick="showRanking()" id="btn-ranking" class="hidden download"><i class="fas fa-trophy"></i> Ver Ranking Geral</button>
        </div>

        <div id="admin-dashboard" class="hidden">
            <div style="display: flex; justify-content: space-between; align-items: center;">
                <h2>Painel ADM</h2>
                <button onclick="logout()" style="width: auto; background: #666;">Sair</button>
            </div>

            <div class="admin-panel">
                <h3>Controle do Bolão</h3>
                <p>Status: <strong id="pool-status-display">ABERTO</strong></p>
                <button onclick="togglePoolStatus()" id="btn-pool-toggle">Fechar Bolão</button>
                <hr>
                <h3>Conferência</h3>
                <div class="bet-grid" id="draw-input-container">
                    </div>
                <button onclick="saveDrawResult()" class="secondary">Salvar Resultado</button>
                <button onclick="generatePDF()" class="download">Exportar PDF</button>
            </div>

            <h3>Participantes</h3>
            <div id="admin-users-list"></div>
        </div>

        <div id="ranking-screen" class="hidden">
            <h2>Resultado Final</h2>
            <div class="card text-center">
                <p>Dezenas Sorteadas:</p>
                <div id="draw-numbers-display"></div>
            </div>
            <div id="ranking-list"></div>
            <button onclick="backToDash()" class="secondary">Voltar</button>
        </div>

    </div>

    <script>
        // --- 1. CONFIGURAÇÃO DO FIREBASE (Sua Chave Integrada) ---
        const firebaseConfig = {
            apiKey: "AIzaSyBo8G3ZcWk4EepN0cHdVBtXc7tGOfcw-yg",
            authDomain: "inscricaosinuca.firebaseapp.com",
            projectId: "inscricaosinuca",
            storageBucket: "inscricaosinuca.firebasestorage.app",
            messagingSenderId: "338241576305",
            appId: "1:338241576305:web:288b6124384c6be4f76ad0",
            measurementId: "G-PEDG30FS2R"
        };

        // Inicializa Firebase
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();

        // --- 2. VARIÁVEIS GLOBAIS ---
        const ADMIN_CPF = "03454627508";
        let currentUser = null;
        let poolConfig = { status: 'open', draw: [] };
        let isRegistering = false;

        // --- 3. FUNÇÕES DE INICIALIZAÇÃO ---
        window.onload = async () => {
            try {
                await loadConfig();
                document.getElementById('loading').classList.add('hidden');
                generateInputs('input-bet-container', 'bet-num');
                generateInputs('draw-input-container', 'draw-num');
            } catch (e) {
                console.error("Erro de conexão:", e);
                alert("Erro ao conectar no banco de dados. Verifique se o Firestore Database foi criado no console do Firebase.");
            }
        };

        async function loadConfig() {
            // Tenta acessar a configuração
            let doc = await db.collection('config').doc('main').get();
            
            if (doc.exists) {
                poolConfig = doc.data();
            } else {
                // Cria config inicial se não existir
                // Se isso falhar, provavelmente é permissão ou banco não criado
                await db.collection('config').doc('main').set({ status: 'open', draw: [] });
                // Tenta ler novamente
                doc = await db.collection('config').doc('main').get();
                poolConfig = doc.data();
            }
            updateUIStatus();
        }

        // --- 4. AUTENTICAÇÃO (LÓGICA) ---
        function toggleRegister() {
            isRegistering = !isRegistering;
            const nameInput = document.getElementById('auth-name');
            const btnLogin = document.getElementById('btn-login');
            const btnToggle = document.getElementById('btn-toggle');
            
            if (isRegistering) {
                nameInput.classList.remove('hidden');
                btnLogin.innerText = "Cadastrar";
                btnToggle.innerText = "Já tenho conta";
            } else {
                nameInput.classList.add('hidden');
                btnLogin.innerText = "Entrar";
                btnToggle.innerText = "Criar Conta";
            }
        }

        async function handleLogin() {
            const cpf = document.getElementById('auth-cpf').value.replace(/\D/g, '');
            const name = document.getElementById('auth-name').value;
            const msg = document.getElementById('auth-msg');

            if (cpf.length !== 11) {
                alert("CPF inválido. Digite 11 números.");
                return;
            }

            document.getElementById('loading').classList.remove('hidden');

            try {
                if (isRegistering) {
                    // Lógica de Cadastro
                    if (poolConfig.status === 'closed') throw new Error("O bolão está fechado para novos cadastros.");
                    if (!name) throw new Error("Nome é obrigatório.");

                    const userRef = db.collection('users').doc(cpf);
                    const userDoc = await userRef.get();

                    if (userDoc.exists) throw new Error("CPF já cadastrado.");

                    const newUser = {
                        cpf: cpf,
                        name: name,
                        paymentStatus: 'PENDENTE',
                        role: (cpf === ADMIN_CPF) ? 'admin' : 'participant',
                        bets: [],
                        createdAt: new Date()
                    };

                    await userRef.set(newUser);
                    currentUser = newUser;
                    enterApp();

                } else {
                    // Lógica de Login
                    const userDoc = await db.collection('users').doc(cpf).get();
                    if (!userDoc.exists) throw new Error("Usuário não encontrado.");
                    
                    // Validação simples (últimos 5 dígitos)
                    const passwordInput = prompt("Digite sua senha (5 últimos dígitos do CPF):");
                    const correctPass = cpf.slice(-5);
                    
                    if(passwordInput !== correctPass) throw new Error("Senha incorreta.");

                    currentUser = userDoc.data();
                    enterApp();
                }
            } catch (error) {
                alert(error.message);
            } finally {
                document.getElementById('loading').classList.add('hidden');
            }
        }

        function enterApp() {
            document.getElementById('auth-screen').classList.add('hidden');
            
            if (currentUser.role === 'admin') {
                document.getElementById('admin-dashboard').classList.remove('hidden');
                loadAdminData();
            } else {
                document.getElementById('user-dashboard').classList.remove('hidden');
                loadUserData();
            }
        }

        function logout() {
            location.reload();
        }

        // --- 5. LÓGICA DO USUÁRIO ---
        function loadUserData() {
            document.getElementById('user-greeting').innerText = `Olá, ${currentUser.name.split(' ')[0]}`;
            
            // Status Pagamento
            const badge = document.getElementById('user-status-badge');
            const pixArea = document.getElementById('pix-area');
            const betArea = document.getElementById('betting-area');
            const betsList = document.getElementById('my-bets-list');

            badge.innerText = currentUser.paymentStatus;
            badge.className = `badge ${currentUser.paymentStatus === 'PAGO' ? 'bg-pago' : 'bg-pendente'}`;

            // Regras de Exibição
            if (currentUser.paymentStatus === 'PENDENTE') {
                pixArea.classList.remove('hidden');
                betArea.classList.add('hidden');
            } else if (poolConfig.status === 'open') {
                pixArea.classList.add('hidden');
                // Verifica limite de cartelas
                if (currentUser.bets && currentUser.bets.length >= 2) {
                    betArea.classList.add('hidden'); // Já apostou o máximo
                } else {
                    betArea.classList.remove('hidden');
                }
            } else {
                // Bolão Fechado
                pixArea.classList.add('hidden');
                betArea.classList.add('hidden');
                document.getElementById('btn-ranking').classList.remove('hidden');
            }

            document.getElementById('bet-count').innerText = currentUser.bets ? currentUser.bets.length : 0;
            renderUserBets(currentUser.bets || [], 'my-bets-list');
        }

        async function submitBet() {
            const inputs = document.querySelectorAll('.bet-num');
            let numbers = [];
            
            // Coleta números
            inputs.forEach(inp => {
                if(inp.value) numbers.push(parseInt(inp.value));
            });

            // Validações
            if (numbers.length !== 6) return alert("Preencha 6 dezenas.");
            if (numbers.some(n => n < 1 || n > 60 || isNaN(n))) return alert("Números devem ser entre 01 e 60.");
            if (new Set(numbers).size !== numbers.length) return alert("Não pode haver números repetidos.");

            // Ordena
            numbers.sort((a, b) => a - b);

            // Verifica duplicidade de cartela (regra exata)
            const stringBet = JSON.stringify(numbers);
            const existingBets = currentUser.bets || [];
            if (existingBets.some(b => JSON.stringify(b) === stringBet)) {
                return alert("Você já enviou uma cartela com essa sequência exata.");
            }

            if (confirm(`Confirmar cartela: ${numbers.join(' - ')}?`)) {
                document.getElementById('loading').classList.remove('hidden');
                try {
                    const newBets = [...existingBets, numbers];
                    await db.collection('users').doc(currentUser.cpf).update({
                        bets: newBets
                    });
                    
                    // Atualiza local e UI
                    currentUser.bets = newBets;
                    alert("Cartela salva com sucesso!");
                    
                    // Limpa inputs
                    inputs.forEach(inp => inp.value = '');
                    loadUserData();

                } catch (e) {
                    alert("Erro ao salvar: " + e.message);
                } finally {
                    document.getElementById('loading').classList.add('hidden');
                }
            }
        }

        // --- 6. LÓGICA DO ADMIN ---
        async function loadAdminData() {
            // Atualiza status do bolão
            const statusDisplay = document.getElementById('pool-status-display');
            const btnToggle = document.getElementById('btn-pool-toggle');
            
            statusDisplay.innerText = poolConfig.status === 'open' ? "ABERTO" : "FECHADO";
            statusDisplay.style.color = poolConfig.status === 'open' ? 'green' : 'red';
            btnToggle.innerText = poolConfig.status === 'open' ? "Fechar Bolão" : "Reabrir Bolão";

            // Se tiver sorteio salvo, preenche
            if (poolConfig.draw && poolConfig.draw.length === 6) {
                const inputs = document.querySelectorAll('.draw-num');
                poolConfig.draw.forEach((n, i) => inputs[i].value = n);
            }

            // Carrega todos os usuários
            const snapshot = await db.collection('users').get();
            const listDiv = document.getElementById('admin-users-list');
            listDiv.innerHTML = '';

            snapshot.forEach(doc => {
                const u = doc.data();
                if(u.role === 'admin') return; // Pula admin na lista

                const div = document.createElement('div');
                div.className = `card ${u.paymentStatus === 'PAGO' ? 'pago' : 'pendente'}`;
                
                let betsHtml = '';
                if(u.bets) {
                    u.bets.forEach((bet, i) => {
                        betsHtml += `<div>Cartela ${i+1}: <strong>${bet.map(formatNum).join('-')}</strong></div>`;
                    });
                }

                div.innerHTML = `
                    <div style="display:flex; justify-content:space-between;">
                        <strong>${u.name}</strong>
                        <span>${u.cpf}</span>
                    </div>
                    <div style="margin: 10px 0;">
                        Status: <span class="badge ${u.paymentStatus === 'PAGO' ? 'bg-pago' : 'bg-pendente'}">${u.paymentStatus}</span>
                    </div>
                    <div style="font-size: 0.9em; color: #555;">${betsHtml || 'Nenhuma cartela enviada'}</div>
                    <div style="margin-top: 10px; display: flex; gap: 5px;">
                        ${u.paymentStatus === 'PENDENTE' ? 
                            `<button onclick="setPayment('${u.cpf}', 'PAGO')" style="padding: 5px;">Confirmar Pagamento</button>` : 
                            `<button onclick="setPayment('${u.cpf}', 'PENDENTE')" style="padding: 5px; background:#666">Revogar Pagto</button>`
                        }
                        <button onclick="deleteUser('${u.cpf}')" class="danger" style="padding: 5px;">Excluir</button>
                    </div>
                `;
                listDiv.appendChild(div);
            });
        }

        async function setPayment(cpf, status) {
            if(!confirm(`Mudar status para ${status}?`)) return;
            await db.collection('users').doc(cpf).update({ paymentStatus: status });
            loadAdminData();
        }

        async function deleteUser(cpf) {
            if(!confirm("Tem certeza? Isso apagará o usuário e as cartelas.")) return;
            await db.collection('users').doc(cpf).delete();
            loadAdminData();
        }

        async function togglePoolStatus() {
            const newStatus = poolConfig.status === 'open' ? 'closed' : 'open';
            await db.collection('config').doc('main').update({ status: newStatus });
            poolConfig.status = newStatus;
            loadAdminData();
        }

        async function saveDrawResult() {
            const inputs = document.querySelectorAll('.draw-num');
            let numbers = [];
            inputs.forEach(inp => { if(inp.value) numbers.push(parseInt(inp.value)); });

            if (numbers.length !== 6) return alert("Insira as 6 dezenas sorteadas.");
            
            await db.collection('config').doc('main').update({ draw: numbers });
            poolConfig.draw = numbers;
            alert("Resultado salvo!");
        }

        // --- 7. RANKING E RELATÓRIOS ---
        async function showRanking() {
            document.getElementById('loading').classList.remove('hidden');
            
            // Recarrega config para garantir sorteio atualizado
            await loadConfig();
            
            if (!poolConfig.draw || poolConfig.draw.length !== 6) {
                document.getElementById('loading').classList.add('hidden');
                return alert("Sorteio ainda não foi lançado pelo administrador.");
            }

            document.getElementById('user-dashboard').classList.add('hidden');
            document.getElementById('ranking-screen').classList.remove('hidden');

            // Exibir dezenas sorteadas
            const drawDiv = document.getElementById('draw-numbers-display');
            drawDiv.innerHTML = poolConfig.draw.map(n => `<span class="ball hit">${formatNum(n)}</span>`).join('');

            // Buscar todos usuários e calcular
            const snapshot = await db.collection('users').get();
            let allBets = [];

            snapshot.forEach(doc => {
                const u = doc.data();
                if (u.bets && u.paymentStatus === 'PAGO') {
                    u.bets.forEach((bet, idx) => {
                        const hits = bet.filter(n => poolConfig.draw.includes(n));
                        allBets.push({
                            name: u.name,
                            cartelaIdx: idx + 1,
                            numbers: bet,
                            hits: hits.length,
                            hitNumbers: hits
                        });
                    });
                }
            });

            // Ordenar por acertos (maior para menor)
            allBets.sort((a, b) => b.hits - a.hits);

            // Renderizar Lista
            const listDiv = document.getElementById('ranking-list');
            listDiv.innerHTML = '';
            
            if (allBets.length === 0) {
                listDiv.innerHTML = '<p class="text-center">Nenhuma aposta paga registrada.</p>';
            }

            allBets.forEach((item, pos) => {
                const isWinner = item.hits >= 4; // Quadra, Quina, Sena
                const div = document.createElement('div');
                div.className = `card ${isWinner ? 'pago' : ''}`;
                
                // Formata números destacando acertos
                const numbersHtml = item.numbers.map(n => {
                    const hit = poolConfig.draw.includes(n);
                    return `<span class="ball ${hit ? 'hit' : ''}" style="width:25px;height:25px;font-size:0.8em;line-height:25px;">${formatNum(n)}</span>`;
                }).join(' ');

                div.innerHTML = `
                    <div style="display:flex; justify-content:space-between; align-items:center;">
                        <strong>#${pos+1} ${item.name}</strong>
                        <span class="badge ${isWinner ? 'bg-pago' : 'bg-pendente'}">${item.hits} acertos</span>
                    </div>
                    <div style="margin-top:5px;">${numbersHtml}</div>
                `;
                listDiv.appendChild(div);
            });
            
            document.getElementById('loading').classList.add('hidden');
        }

        function backToDash() {
            document.getElementById('ranking-screen').classList.add('hidden');
            document.getElementById('user-dashboard').classList.remove('hidden');
        }

        // --- 8. GERAR PDF (Via Admin) ---
        async function generatePDF() {
            const { jsPDF } = window.jspdf;
            const doc = new jsPDF();
            
            // Título
            doc.setFontSize(18);
            doc.text("Relatório Final - Bolão da Virada", 14, 22);
            
            // Dezenas
            doc.setFontSize(12);
            doc.text(`Dezenas Sorteadas: ${poolConfig.draw.join(' - ')}`, 14, 32);

            // Dados da tabela
            const snapshot = await db.collection('users').get();
            let rows = [];
            
            snapshot.forEach(d => {
                const u = d.data();
                if(u.bets && u.paymentStatus === 'PAGO') {
                    u.bets.forEach((b, i) => {
                        const hits = b.filter(n => poolConfig.draw.includes(n)).length;
                        rows.push([u.name, `Cartela ${i+1}`, b.join('-'), hits]);
                    });
                }
            });

            // Ordena por acertos no PDF também
            rows.sort((a, b) => b[3] - a[3]);

            doc.autoTable({
                head: [['Participante', 'Cartela', 'Números', 'Acertos']],
                body: rows,
                startY: 40,
                theme: 'grid',
                styles: { halign: 'center' },
                headStyles: { fillColor: [32, 152, 105] }
            });

            doc.save("resultado-bolao.pdf");
        }

        // --- 9. HELPERS ---
        function generateInputs(containerId, className) {
            const container = document.getElementById(containerId);
            container.innerHTML = '';
            for(let i=0; i<6; i++) {
                const inp = document.createElement('input');
                inp.type = 'number';
                inp.className = `bet-input ${className}`;
                inp.min = 1;
                inp.max = 60;
                container.appendChild(inp);
            }
        }

        function renderUserBets(bets, containerId) {
            const container = document.getElementById(containerId);
            container.innerHTML = '';
            bets.forEach((bet, i) => {
                const div = document.createElement('div');
                div.className = 'card';
                div.innerHTML = `<strong>Cartela ${i+1}:</strong> ${bet.map(formatNum).join(' - ')}`;
                container.appendChild(div);
            });
        }

        function formatNum(n) {
            return n < 10 ? '0'+n : n;
        }

        function updateUIStatus() {
            // Atualiza UI global se necessário
        }
    </script>
</body>
</html>
