<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Bolão Mega da Virada</title>

    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-database.js"></script>
    
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.28/jspdf.plugin.autotable.min.js"></script>

    <style>
        :root {
            --primary: #1e3c72;
            --secondary: #2a5298;
            --success: #28a745;
            --danger: #dc3545;
            --bg: #f0f2f5;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', sans-serif; }
        
        body { 
            background: var(--bg); 
            display: flex; 
            justify-content: center; 
            min-height: 100vh;
        }

        /* Layout Responsivo Base */
        .app-container {
            width: 100%;
            max-width: 500px; /* Largura de um celular grande */
            background: white;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            box-shadow: 0 0 20px rgba(0,0,0,0.1);
        }

        .header {
            background: var(--primary);
            color: white;
            padding: 20px;
            text-align: center;
            border-bottom-left-radius: 20px;
            border-bottom-right-radius: 20px;
        }

        .content { padding: 20px; flex: 1; }

        /* Estilo dos Inputs */
        input {
            width: 100%;
            padding: 15px;
            margin: 10px 0;
            border: 1px solid #ddd;
            border-radius: 10px;
            font-size: 16px; /* Evita zoom no iPhone */
        }

        .btn {
            width: 100%;
            padding: 15px;
            border: none;
            border-radius: 10px;
            font-weight: bold;
            font-size: 16px;
            cursor: pointer;
            transition: 0.3s;
            margin-top: 10px;
        }

        .btn-primary { background: var(--primary); color: white; }
        .btn-success { background: var(--success); color: white; }
        .btn-danger { background: var(--danger); color: white; }

        /* Grid da Mega Sena */
        .numero-grid {
            display: grid;
            grid-template-columns: repeat(6, 1fr);
            gap: 8px;
            margin: 20px 0;
        }

        .num-btn {
            aspect-ratio: 1;
            border: 2px solid #eee;
            border-radius: 50%;
            background: white;
            font-weight: bold;
            font-size: 14px;
        }

        .num-btn.selected { background: var(--success); color: white; border-color: #1a6d2c; }

        /* Tabs (Menu Inferior) */
        .nav-tabs {
            display: flex;
            background: white;
            border-top: 1px solid #eee;
            padding: 10px 0;
            position: sticky;
            bottom: 0;
        }

        .tab-item {
            flex: 1;
            text-align: center;
            font-size: 12px;
            color: #666;
            cursor: pointer;
        }

        .tab-item.active { color: var(--primary); font-weight: bold; }

        .card-aposta {
            background: #f9f9f9;
            padding: 15px;
            border-radius: 12px;
            margin-bottom: 15px;
            border-left: 5px solid var(--primary);
        }

        .hidden { display: none !important; }

        /* Toast de Alerta */
        #toast {
            position: fixed;
            bottom: 80px;
            left: 50%;
            transform: translateX(-50%);
            background: #333;
            color: white;
            padding: 10px 20px;
            border-radius: 20px;
            font-size: 14px;
            z-index: 1000;
        }
    </style>
</head>
<body>

<div class="app-container">
    <div class="header">
        <h1>Bolão 2025 🍀</h1>
        <p id="user-display"></p>
    </div>

    <div class="content">
        <div id="section-login">
            <h3>Acesse sua conta</h3>
            <input type="number" id="login-cpf" placeholder="CPF (somente números)">
            <input type="password" id="login-pass" placeholder="Senha">
            <button class="btn btn-primary" onclick="handleLogin()">ENTRAR</button>
            <button class="btn" onclick="toggleView('reg')">Criar nova conta</button>
        </div>

        <div id="section-register" class="hidden">
            <h3>Cadastro de Participante</h3>
            <input type="text" id="reg-nome" placeholder="Nome Completo">
            <input type="number" id="reg-cpf" placeholder="CPF">
            <p style="font-size: 12px; color: #666;">*Sua senha será os 5 últimos dígitos do CPF.</p>
            <button class="btn btn-success" onclick="handleRegister()">CADASTRAR</button>
            <button class="btn" onclick="toggleView('login')">Voltar ao Login</button>
        </div>

        <div id="section-app" class="hidden">
            <div id="msg-pagamento" class="card-aposta" style="background: #fff3cd;">
                ⚠️ <strong>Aguardando Pagamento:</strong><br>
                Sua chave PIX: <strong>SUA_CHAVE_AQUI</strong><br>
                O ADM liberará seu acesso após o PIX.
            </div>

            <div id="area-jogo" class="hidden">
                <h4 id="label-cartela">Escolha 6 números</h4>
                <div class="numero-grid" id="grid-voto"></div>
                <button class="btn btn-success" onclick="enviarCartela()">ENVIAR CARTELA</button>
            </div>

            <div id="minhas-apostas-lista"></div>
        </div>

        <div id="section-lista" class="hidden">
            <h3>Apostas do Grupo</h3>
            <div id="lista-geral"></div>
        </div>

        <div id="section-adm" class="hidden">
            <h3>Painel do Administrador</h3>
            <div id="lista-adm-users"></div>
        </div>
    </div>

    <div id="nav-bar" class="nav-tabs hidden">
        <div class="tab-item active" onclick="navTo('app')">🏠<br>Início</div>
        <div class="tab-item" onclick="navTo('lista')">👥<br>Grupo</div>
        <div id="tab-adm-icon" class="tab-item hidden" onclick="navTo('adm')">⚙️<br>ADM</div>
    </div>
</div>

<div id="toast" class="hidden"></div>

<script>
    // 1. CONFIGURAÇÃO
    const firebaseConfig = {
        apiKey: "AIzaSyBo8G3ZcWk4EepN0cHdVBtXc7tGOfcw-yg",
        authDomain: "inscricaosinuca.firebaseapp.com",
        projectId: "inscricaosinuca",
        databaseURL: "https://inscricaosinuca-default-rtdb.firebaseio.com",
        storageBucket: "inscricaosinuca.firebasestorage.app",
        messagingSenderId: "338241576305",
        appId: "1:338241576305:web:288b6124384c6be4f76ad0"
    };

    firebase.initializeApp(firebaseConfig);
    const database = firebase.database();

    let currentUser = null;
    let selectedNums = [];
    const ADM_CPF = "03454627508";

    // 2. FUNÇÕES DE INTERFACE
    function toggleView(view) {
        document.getElementById('section-login').classList.add('hidden');
        document.getElementById('section-register').classList.add('hidden');
        if(view === 'reg') document.getElementById('section-register').classList.remove('hidden');
        else document.getElementById('section-login').classList.remove('hidden');
    }

    function navTo(sec) {
        document.querySelectorAll('.tab-content, [id^="section-"]').forEach(s => s.classList.add('hidden'));
        document.getElementById('section-' + sec).classList.remove('hidden');
        document.querySelectorAll('.tab-item').forEach(t => t.classList.remove('active'));
    }

    function showToast(msg) {
        const t = document.getElementById('toast');
        t.innerText = msg;
        t.classList.remove('hidden');
        setTimeout(() => t.classList.add('hidden'), 3000);
    }

    // 3. LOGICA DE NEGÓCIO
    function handleRegister() {
        const nome = document.getElementById('reg-nome').value;
        const cpf = document.getElementById('reg-cpf').value;
        
        if(!nome || cpf.length < 11) return alert("Preencha nome e CPF corretamente!");

        const senha = cpf.slice(-5);
        
        database.ref('participantes/' + cpf).set({
            nome: nome,
            cpf: cpf,
            senha: senha,
            pago: false,
            cartelas: []
        }).then(() => {
            alert(`Sucesso! Seu usuário é o CPF e sua senha é: ${senha}`);
            toggleView('login');
        });
    }

    function handleLogin() {
        const cpf = document.getElementById('login-cpf').value;
        const pass = document.getElementById('login-pass').value;

        database.ref('participantes/' + cpf).once('value').then((snapshot) => {
            const user = snapshot.val();
            if(user && user.senha === pass) {
                currentUser = user;
                startApp();
            } else {
                alert("Dados incorretos!");
            }
        });
    }

    function startApp() {
        document.getElementById('section-login').classList.add('hidden');
        document.getElementById('section-app').classList.remove('hidden');
        document.getElementById('nav-bar').classList.remove('hidden');
        document.getElementById('user-display').innerText = "Olá, " + currentUser.nome;

        if(currentUser.cpf === ADM_CPF) {
            document.getElementById('tab-adm-icon').classList.remove('hidden');
        }

        renderGrid();
        checkStatus();
        listenUpdates();
    }

    function renderGrid() {
        const grid = document.getElementById('grid-voto');
        grid.innerHTML = '';
        for(let i=1; i<=60; i++) {
            const btn = document.createElement('button');
            btn.className = 'num-btn';
            btn.innerText = i.toString().padStart(2, '0');
            btn.onclick = () => {
                if(selectedNums.includes(i)) {
                    selectedNums = selectedNums.filter(n => n !== i);
                    btn.classList.remove('selected');
                } else if(selectedNums.length < 6) {
                    selectedNums.push(i);
                    btn.classList.add('selected');
                }
            };
            grid.appendChild(btn);
        }
    }

    function checkStatus() {
        database.ref('participantes/' + currentUser.cpf).on('value', snap => {
            const user = snap.val();
            if(user.pago) {
                document.getElementById('msg-pagamento').classList.add('hidden');
                if((user.cartelas || []).length < 2) {
                    document.getElementById('area-jogo').classList.remove('hidden');
                } else {
                    document.getElementById('area-jogo').innerHTML = "✅ Você já enviou suas 2 cartelas.";
                }
            }
            
            // Renderiza minhas cartelas
            const lista = document.getElementById('minhas-apostas-lista');
            lista.innerHTML = '<h4>Minhas Apostas:</h4>';
            (user.cartelas || []).forEach(c => {
                lista.innerHTML += `<div class="card-aposta">${c}</div>`;
            });
        });
    }

    async function enviarCartela() {
        if(selectedNums.length < 6) return alert("Selecione 6 números!");
        const sequencia = selectedNums.sort((a,b) => a-b).join(' - ');

        // Verificar duplicidade no banco todo
        const snap = await database.ref('participantes').once('value');
        const todos = snap.val();
        let duplicado = false;

        Object.values(todos).forEach(u => {
            if(u.cartelas && u.cartelas.includes(sequencia)) duplicado = true;
        });

        if(duplicado) {
            alert("Essa sequência já foi registrada por outra pessoa! Escolha outra.");
            return;
        }

        const novasCartelas = currentUser.cartelas || [];
        novasCartelas.push(sequencia);

        database.ref('participantes/' + currentUser.cpf).update({
            cartelas: novasCartelas
        }).then(() => {
            showToast("Cartela Enviada!");
            selectedNums = [];
            renderGrid();
        });
    }

    function listenUpdates() {
        database.ref('participantes').on('value', snap => {
            const users = snap.val();
            const listaGeral = document.getElementById('lista-geral');
            const listaAdm = document.getElementById('lista-adm-users');
            
            listaGeral.innerHTML = '';
            listaAdm.innerHTML = '';

            Object.values(users).forEach(u => {
                // Lista Grupo
                if(u.cartelas) {
                    listaGeral.innerHTML += `
                        <div class="card-aposta">
                            <strong>${u.nome}</strong><br>
                            ${u.cartelas.join('<br>')}
                        </div>`;
                }

                // Painel ADM
                listaAdm.innerHTML += `
                    <div class="card-aposta" style="display:flex; justify-content:space-between">
                        <div>${u.nome}<br><small>${u.pago ? 'PAGO' : 'PENDENTE'}</small></div>
                        <div>
                            <button onclick="setPago('${u.cpf}', ${!u.pago})" class="btn" style="width:auto; padding:5px 10px; background:${u.pago ? 'green':'gray'}">✓</button>
                            <button onclick="remover('${u.cpf}')" class="btn" style="width:auto; padding:5px 10px; background:red">X</button>
                        </div>
                    </div>`;
            });
        });
    }

    function setPago(cpf, status) { database.ref('participantes/' + cpf).update({ pago: status }); }
    function remover(cpf) { if(confirm("Remover participante?")) database.ref('participantes/' + cpf).remove(); }

</script>
</body>
</html>
