<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bolão Mega da Virada</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.28/jspdf.plugin.autotable.min.js"></script>
    
    <style>
        :root {
            --primary: #1e3c72;
            --secondary: #2a5298;
            --success: #28a745;
            --danger: #dc3545;
            --warning: #ffc107;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: sans-serif; }
        body { background: #f0f2f5; padding: 10px; }
        .container { max-width: 600px; margin: 0 auto; background: white; border-radius: 12px; padding: 20px; shadow: 0 4px 10px rgba(0,0,0,0.1); }
        
        /* UI Elements */
        .btn { width: 100%; padding: 12px; border: none; border-radius: 8px; cursor: pointer; font-weight: bold; margin-top: 10px; }
        .btn-primary { background: var(--primary); color: white; }
        .btn-success { background: var(--success); color: white; }
        .btn-danger { background: var(--danger); color: white; }
        
        input { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ccc; border-radius: 8px; }
        
        /* Grid Mega Sena */
        .numero-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(40px, 1fr)); gap: 5px; margin: 15px 0; }
        .num-btn { aspect-ratio: 1; border: 1px solid #ddd; border-radius: 50%; background: white; font-weight: bold; cursor: pointer; }
        .num-btn.selected { background: var(--success); color: white; }
        .num-btn.duplicate { background: var(--danger); color: white; animation: shake 0.5s; }

        /* Tabs */
        .tabs { display: flex; gap: 5px; margin-bottom: 20px; overflow-x: auto; }
        .tab { padding: 10px 15px; background: #eee; border-radius: 20px; cursor: pointer; white-space: nowrap; font-size: 14px; }
        .tab.active { background: var(--primary); color: white; }

        .card-aposta { background: #f8f9fa; padding: 15px; border-radius: 8px; margin-bottom: 10px; border-left: 5px solid var(--primary); }
        .status-badge { padding: 4px 8px; border-radius: 4px; font-size: 11px; font-weight: bold; }
        
        @keyframes shake { 0%, 100% {transform: translateX(0);} 25% {transform: translateX(-5px);} 75% {transform: translateX(5px);} }
        .hidden { display: none; }
    </style>
</head>
<body>

<div class="container">
    <h2 id="app-title">🏆 Bolão da Virada</h2>
    
    <div id="auth-section">
        <div id="login-box">
            <h3>Entrar</h3>
            <input type="text" id="login-cpf" placeholder="CPF (apenas números)">
            <input type="password" id="login-pass" placeholder="Senha">
            <button class="btn btn-primary" onclick="handleLogin()">Entrar</button>
            <p style="text-align:center; margin-top:15px; font-size:14px;">Não tem conta? <a href="#" onclick="toggleAuth(true)">Cadastre-se</a></p>
        </div>

        <div id="register-box" class="hidden">
            <h3>Cadastro</h3>
            <input type="text" id="reg-nome" placeholder="Nome Completo">
            <input type="text" id="reg-cpf" placeholder="CPF">
            <button class="btn btn-success" onclick="handleRegister()">Gerar Senha e Cadastrar</button>
            <p style="text-align:center; margin-top:15px; font-size:14px;"><a href="#" onclick="toggleAuth(false)">Voltar ao Login</a></p>
        </div>
    </div>

    <div id="main-app" class="hidden">
        <div class="tabs">
            <div class="tab active" onclick="switchTab('minhas')">Minhas Cartelas</div>
            <div class="tab" onclick="switchTab('acompanhamento')">Participantes</div>
            <div class="tab" onclick="switchTab('sorteio')">Conferência</div>
            <div id="adm-tab" class="tab hidden" style="background: #000; color: #fff;" onclick="switchTab('adm')">ADM</div>
        </div>

        <div id="tab-minhas" class="tab-content">
            <div id="status-pagamento-msg"></div>
            <div id="area-jogo" class="hidden">
                <h4 id="label-cartela">Preencher Cartela 1</h4>
                <div class="numero-grid" id="grid-voto"></div>
                <button class="btn btn-success" id="btn-enviar-cartela" onclick="enviarCartela()">Enviar Dezenas</button>
            </div>
            <div id="minhas-registradas"></div>
        </div>

        <div id="tab-acompanhamento" class="tab-content hidden">
            <button class="btn btn-primary" onclick="exportarPDF()">Baixar PDF das Apostas</button>
            <div id="lista-geral-apostas" style="margin-top:15px;"></div>
        </div>

        <div id="tab-sorteio" class="tab-content hidden">
            <h4>Conferir Resultado</h4>
            <p style="font-size:12px; color:666;">Insira as 6 dezenas sorteadas:</p>
            <div id="grid-conferencia" class="numero-grid"></div>
            <div id="ranking-resultado" style="margin-top:20px;"></div>
        </div>

        <div id="tab-adm" class="tab-content hidden">
            <h3>Painel de Controle</h3>
            <div id="adm-settings" style="background:#eee; padding:10px; border-radius:8px; margin-bottom:15px;">
                <label><input type="checkbox" id="lock-system" onchange="toggleSystemLock(this.checked)"> Bloquear novos lançamentos</label>
            </div>
            <div id="adm-lista-usuarios"></div>
        </div>

        <button class="btn btn-danger" onclick="logout()" style="margin-top:30px; opacity:0.7">Sair</button>
    </div>
</div>

<script>
    // --- CONFIGURAÇÃO FIREBASE ---
    const firebaseConfig = {
        apiKey: "AIzaSyBo8G3ZcWk4EepN0cHdVBtXc7tGOfcw-yg",
        authDomain: "inscricaosinuca.firebaseapp.com",
        projectId: "inscricaosinuca",
        databaseURL: "https://inscricaosinuca-default-rtdb.firebaseio.com", // Adicione a URL do seu DB aqui
        storageBucket: "inscricaosinuca.firebasestorage.app",
        messagingSenderId: "338241576305",
        appId: "1:338241576305:web:288b6124384c6be4f76ad0"
    };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    let currentUser = null;
    let selectedNums = [];
    let systemLocked = false;
    const ADM_CPF = "03454627508";

    // --- AUTENTICAÇÃO ---
    function handleRegister() {
        const nome = document.getElementById('reg-nome').value;
        const cpf = document.getElementById('reg-cpf').value.replace(/\D/g, '');
        
        if(nome.length < 3 || cpf.length !== 11) return alert("Preencha os dados corretamente!");
        
        const senha = cpf.slice(-5);
        
        db.ref('users/' + cpf).set({
            nome, cpf, senha,
            pago: false,
            cartelas: [],
            dataCadastro: new Date().toISOString()
        }).then(() => {
            alert(`Cadastro Realizado!\nUsuário: ${cpf}\nSenha: ${senha}`);
            toggleAuth(false);
        });
    }

    function handleLogin() {
        const cpf = document.getElementById('login-cpf').value;
        const pass = document.getElementById('login-pass').value;

        db.ref('users/' + cpf).once('value', (snapshot) => {
            const user = snapshot.val();
            if(user && user.senha === pass) {
                currentUser = user;
                loginSuccess();
            } else {
                alert("CPF ou Senha incorretos!");
            }
        });
    }

    function loginSuccess() {
        document.getElementById('auth-section').classList.add('hidden');
        document.getElementById('main-app').classList.remove('hidden');
        if(currentUser.cpf === ADM_CPF) document.getElementById('adm-tab').classList.remove('hidden');
        initGameGrid();
        updateUI();
        listenToChanges();
    }

    // --- LÓGICA DO JOGO ---
    function initGameGrid() {
        const grid = document.getElementById('grid-voto');
        grid.innerHTML = '';
        for(let i=1; i<=60; i++) {
            const n = i.toString().padStart(2, '0');
            const btn = document.createElement('button');
            btn.className = 'num-btn';
            btn.innerText = n;
            btn.onclick = () => selectNum(i, btn);
            grid.appendChild(btn);
        }
    }

    function selectNum(num, btn) {
        if(selectedNums.includes(num)) {
            selectedNums = selectedNums.filter(x => x !== num);
            btn.classList.remove('selected');
        } else if(selectedNums.length < 6) {
            selectedNums.push(num);
            btn.classList.add('selected');
        }
    }

    async function enviarCartela() {
        if(selectedNums.length < 6) return alert("Selecione 6 dezenas!");
        
        const seq = selectedNums.sort((a,b) => a-b).join('-');
        
        // Verifica duplicidade no Banco
        const snapshot = await db.ref('todas_cartelas').once('value');
        const todas = snapshot.val() || {};
        
        if(Object.values(todas).includes(seq)) {
            alert("ESTA SEQUÊNCIA JÁ EXISTE NO BOLÃO! Escolha outros números.");
            // Logica para mudar cor (opcional: destacar os botões)
            return;
        }

        const numCartelas = (currentUser.cartelas || []).length;
        if(numCartelas >= 2) return alert("Você já atingiu o limite de 2 cartelas.");

        const updates = {};
        const cartelaKey = db.ref().child('todas_cartelas').push().key;
        updates['/todas_cartelas/' + cartelaKey] = seq;
        
        const novasCartelas = currentUser.cartelas || [];
        novasCartelas.push(seq);
        updates['/users/' + currentUser.cpf + '/cartelas'] = novasCartelas;

        db.ref().update(updates).then(() => {
            alert("Cartela registrada!");
            selectedNums = [];
            initGameGrid();
            location.reload(); // Simplificação para atualizar estado
        });
    }

    // --- INTERFACE E ATUALIZAÇÕES ---
    function updateUI() {
        const statusDiv = document.getElementById('status-pagamento-msg');
        const areaJogo = document.getElementById('area-jogo');
        
        if(!currentUser.pago) {
            statusDiv.innerHTML = `<div class="card-aposta" style="background:#fff3cd">Aguardando confirmação de pagamento PIX pelo ADM.</div>`;
            areaJogo.classList.add('hidden');
        } else {
            const qtd = (currentUser.cartelas || []).length;
            if(qtd < 2 && !systemLocked) {
                areaJogo.classList.remove('hidden');
                document.getElementById('label-cartela').innerText = `Preencher Cartela ${qtd + 1}`;
            } else {
                areaJogo.innerHTML = "<h4>Você já completou suas cartelas ou o prazo encerrou.</h4>";
            }
        }
    }

    function listenToChanges() {
        // Escuta trava do sistema
        db.ref('config/locked').on('value', snap => {
            systemLocked = snap.val();
        });

        // Escuta participantes para a lista geral
        db.ref('users').on('value', snap => {
            const users = snap.val();
            renderParticipantes(users);
            if(currentUser.cpf === ADM_CPF) renderADM(users);
        });
    }

    function renderParticipantes(users) {
        const lista = document.getElementById('lista-geral-apostas');
        lista.innerHTML = '';
        Object.values(users).forEach(u => {
            if(u.cartelas) {
                let html = `<div class="card-aposta"><strong>${u.nome}</strong><br>`;
                u.cartelas.forEach((c, idx) => {
                    html += `Cartela ${idx+1}: <span style="color:var(--primary)">${c}</span><br>`;
                });
                html += `</div>`;
                lista.innerHTML += html;
            }
        });
    }

    // --- FUNÇÕES DE ADM ---
    function togglePagamento(cpf, status) {
        db.ref('users/' + cpf).update({ pago: !status });
    }

    function excluirUser(cpf) {
        if(confirm("Deseja realmente excluir este participante?")) {
            db.ref('users/' + cpf).remove();
        }
    }

    function renderADM(users) {
        const lista = document.getElementById('adm-lista-usuarios');
        lista.innerHTML = '<h4>Gerenciar Pagamentos</h4>';
        Object.values(users).forEach(u => {
            lista.innerHTML += `
                <div class="card-aposta" style="display:flex; justify-content:space-between; align-items:center">
                    <div>${u.nome} <br> <small>${u.cpf}</small></div>
                    <div>
                        <button onclick="togglePagamento('${u.cpf}', ${u.pago})" class="btn" style="width:auto; background:${u.pago ? 'green':'gray'}; color:white">
                            ${u.pago ? 'PAGO' : 'PENDENTE'}
                        </button>
                        <button onclick="excluirUser('${u.cpf}')" class="btn" style="width:auto; background:red; color:white">X</button>
                    </div>
                </div>
            `;
        });
    }

    // --- HELPERS ---
    function switchTab(tab) {
        document.querySelectorAll('.tab-content').forEach(c => c.classList.add('hidden'));
        document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
        document.getElementById('tab-' + tab).classList.remove('hidden');
        event.currentTarget.classList.add('active');
    }

    function toggleAuth(isReg) {
        document.getElementById('login-box').classList.toggle('hidden', isReg);
        document.getElementById('register-box').classList.toggle('hidden', !isReg);
    }

    function logout() { location.reload(); }
</script>

</body>
</html>
