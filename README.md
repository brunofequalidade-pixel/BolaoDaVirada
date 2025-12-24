<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bolão Mega da Virada</title>

    <!-- jsPDF -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.28/jspdf.plugin.autotable.min.js"></script>

    <!-- 🔴 CORREÇÃO 1: Firebase SDK -->
    <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>

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
        .container { max-width: 600px; margin: 0 auto; background: white; border-radius: 12px; padding: 20px; }

        .btn { width: 100%; padding: 12px; border: none; border-radius: 8px; cursor: pointer; font-weight: bold; margin-top: 10px; }
        .btn-primary { background: var(--primary); color: white; }
        .btn-success { background: var(--success); color: white; }
        .btn-danger { background: var(--danger); color: white; }

        input { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ccc; border-radius: 8px; }

        .numero-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(40px, 1fr)); gap: 5px; margin: 15px 0; }
        .num-btn { aspect-ratio: 1; border: 1px solid #ddd; border-radius: 50%; background: white; font-weight: bold; cursor: pointer; }
        .num-btn.selected { background: var(--success); color: white; }

        .tabs { display: flex; gap: 5px; margin-bottom: 20px; overflow-x: auto; }
        .tab { padding: 10px 15px; background: #eee; border-radius: 20px; cursor: pointer; white-space: nowrap; font-size: 14px; }
        .tab.active { background: var(--primary); color: white; }

        .card-aposta { background: #f8f9fa; padding: 15px; border-radius: 8px; margin-bottom: 10px; border-left: 5px solid var(--primary); }
        .hidden { display: none; }
    </style>
</head>
<body>

<div class="container">
    <h2>🏆 Bolão da Virada</h2>

    <div id="auth-section">
        <div id="login-box">
            <h3>Entrar</h3>
            <input type="text" id="login-cpf" placeholder="CPF">
            <input type="password" id="login-pass" placeholder="Senha">
            <button class="btn btn-primary" onclick="handleLogin()">Entrar</button>
            <p style="text-align:center;margin-top:15px;">
                <a href="#" onclick="toggleAuth(true)">Cadastre-se</a>
            </p>
        </div>

        <div id="register-box" class="hidden">
            <h3>Cadastro</h3>
            <input type="text" id="reg-nome" placeholder="Nome Completo">
            <input type="text" id="reg-cpf" placeholder="CPF">
            <button class="btn btn-success" onclick="handleRegister()">Gerar Senha e Cadastrar</button>
            <p style="text-align:center;margin-top:15px;">
                <a href="#" onclick="toggleAuth(false)">Voltar</a>
            </p>
        </div>
    </div>

    <div id="main-app" class="hidden">
        <div class="tabs">
            <div class="tab active" onclick="switchTab('minhas', this)">Minhas Cartelas</div>
            <div class="tab" onclick="switchTab('acompanhamento', this)">Participantes</div>
            <div class="tab" onclick="switchTab('sorteio', this)">Conferência</div>
            <div id="adm-tab" class="tab hidden" onclick="switchTab('adm', this)">ADM</div>
        </div>

        <div id="tab-minhas" class="tab-content">
            <div id="status-pagamento-msg"></div>
            <div id="area-jogo" class="hidden">
                <h4 id="label-cartela"></h4>
                <div class="numero-grid" id="grid-voto"></div>
                <button class="btn btn-success" onclick="enviarCartela()">Enviar Dezenas</button>
            </div>
        </div>

        <div id="tab-acompanhamento" class="tab-content hidden">
            <div id="lista-geral-apostas"></div>
        </div>

        <div id="tab-sorteio" class="tab-content hidden"></div>
        <div id="tab-adm" class="tab-content hidden"></div>

        <button class="btn btn-danger" onclick="logout()">Sair</button>
    </div>
</div>

<script>
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
    const db = firebase.database();

    let currentUser = null;
    let selectedNums = [];
    const ADM_CPF = "03454627508";

    function handleRegister() {
        const nome = reg-nome.value;
        const cpf = reg-cpf.value.replace(/\D/g,'');
        const senha = cpf.slice(-5);

        db.ref('users/' + cpf).set({ nome, cpf, senha, pago:false, cartelas:[] })
            .then(() => { alert("Cadastro realizado"); toggleAuth(false); });
    }

    function handleLogin() {
        const cpf = login-cpf.value;
        const pass = login-pass.value;

        db.ref('users/' + cpf).once('value', snap => {
            const u = snap.val();
            if(u && u.senha === pass) {
                currentUser = u;
                auth-section.classList.add('hidden');
                main-app.classList.remove('hidden');
                if(cpf === ADM_CPF) adm-tab.classList.remove('hidden');
                initGameGrid();
                updateUI();
            } else alert("Dados inválidos");
        });
    }

    function initGameGrid() {
        grid-voto.innerHTML = '';
        for(let i=1;i<=60;i++){
            const b = document.createElement('button');
            b.className = 'num-btn';
            b.innerText = i.toString().padStart(2,'0');
            b.onclick = ()=>toggleNum(i,b);
            grid-voto.appendChild(b);
        }
    }

    function toggleNum(n,b){
        if(selectedNums.includes(n)){
            selectedNums = selectedNums.filter(x=>x!==n);
            b.classList.remove('selected');
        } else if(selectedNums.length<6){
            selectedNums.push(n);
            b.classList.add('selected');
        }
    }

    function enviarCartela(){
        alert("Cartela enviada (teste ok)");
    }

    function updateUI(){
        if(!currentUser.pago){
            status-pagamento-msg.innerHTML = "Aguardando pagamento";
        } else area-jogo.classList.remove('hidden');
    }

    // 🔴 CORREÇÃO 2
    function switchTab(tab, el){
        document.querySelectorAll('.tab-content').forEach(t=>t.classList.add('hidden'));
        document.querySelectorAll('.tab').forEach(t=>t.classList.remove('active'));
        document.getElementById('tab-'+tab).classList.remove('hidden');
        el.classList.add('active');
    }

    function toggleAuth(r){
        login-box.classList.toggle('hidden', r);
        register-box.classList.toggle('hidden', !r);
    }

    function logout(){ location.reload(); }
</script>

</body>
</html>
