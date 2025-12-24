<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Bolão da Virada</title>
    <meta name="theme-color" content="#4CAF50">
    
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>

    <style>
        :root {
            --primary: #4CAF50;
            --primary-dark: #388E3C;
            --danger: #f44336;
            --dark: #333;
            --light: #f4f4f4;
            --shadow: 0 4px 6px rgba(0,0,0,0.1);
        }

        * { box-sizing: border-box; }
        
        body { 
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; 
            margin: 0; 
            padding: 20px; 
            background: #eee; 
            display: flex;
            justify-content: center;
            min-height: 100vh;
        }

        .hidden { display: none !important; }
        
        .screen { 
            width: 100%; 
            max-width: 480px;
            background: #fff; 
            padding: 25px; 
            border-radius: 12px; 
            box-shadow: var(--shadow); 
            height: fit-content;
        }

        h2 { text-align: center; color: #1976D2; margin-bottom: 20px; }
        label { font-weight: bold; font-size: 14px; display: block; margin-top: 10px; }
        
        input { 
            width: 100%; 
            padding: 14px; 
            margin: 8px 0; 
            border: 1px solid #ddd; 
            border-radius: 8px; 
            font-size: 16px;
        }
        
        button { 
            width: 100%; 
            padding: 14px; 
            margin-top: 15px; 
            background: var(--primary); 
            color: white; 
            border: none; 
            border-radius: 8px; 
            font-size: 16px; 
            font-weight: bold; 
            cursor: pointer;
        }
        
        button.secondary { background: #757575; }
        button.btn-danger { background: var(--danger); }
        button.btn-small { width: auto; padding: 6px 12px; font-size: 13px; margin-top: 0;}

        .status-pago { color: var(--primary); font-weight: bold; background: #e8f5e9; padding: 5px 10px; border-radius: 4px; border: 1px solid var(--primary); }
        .status-pendente { color: var(--danger); font-weight: bold; background: #ffebee; padding: 5px 10px; border-radius: 4px; border: 1px solid var(--danger); }

        .input-cartela { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; }
        .bolinha { display: inline-block; background: #333; color: #fff; width: 28px; height: 28px; text-align: center; line-height: 28px; border-radius: 50%; margin-right: 4px; font-size: 13px;}

        .tabs { display: flex; gap: 5px; margin-bottom: 20px; border-bottom: 2px solid #ddd; }
        .tabs button { background: transparent; color: #666; border-radius: 5px 5px 0 0; padding: 10px; }
        .tabs button.active { background: var(--primary); color: #fff; }

        .toast { position: fixed; bottom: 30px; left: 50%; transform: translateX(-50%); background: rgba(0,0,0,0.85); color: #fff; padding: 12px 24px; border-radius: 50px; z-index: 2000; font-size: 14px; }
        .aviso { font-size: 13px; color: #666; background: #fff3cd; padding: 10px; border-radius: 5px; margin-bottom: 10px; border-left: 4px solid #ffc107; }
    </style>
</head>
<body>

    <div id="toast" class="toast hidden"></div>

    <section id="login-screen" class="screen">
        <h2>Bolão da Virada</h2>
        <label>CPF</label>
        <input type="tel" id="login-cpf" placeholder="Apenas números" maxlength="11">
        <label>Senha</label>
        <input type="password" id="login-senha" placeholder="Senha">
        <button onclick="fazerLogin()">ENTRAR</button>
        <p style="text-align:center;">Não tem conta? <a href="#" onclick="mostrarTela('register-screen')">Cadastre-se</a></p>
    </section>

    <section id="register-screen" class="screen hidden">
        <h2>Novo Cadastro</h2>
        <label>Nome Completo</label>
        <input type="text" id="reg-nome" placeholder="Seu nome">
        <label>CPF (Será seu Login)</label>
        <input type="tel" id="reg-cpf" placeholder="Apenas números" maxlength="11">
        <div class="aviso">Sua senha será os 5 últimos dígitos do CPF.</div>
        <button onclick="registrarUsuario()">CADASTRAR</button>
        <button class="secondary" onclick="mostrarTela('login-screen')">Voltar</button>
    </section>

    <section id="dashboard-screen" class="screen hidden">
        <header style="display:flex; justify-content:space-between; align-items:center; margin-bottom:15px;">
            <h3 id="user-welcome">Olá</h3>
            <button onclick="logout()" class="btn-small secondary">Sair</button>
        </header>

        <div id="status-pagamento" style="text-align:center; margin-bottom:20px;"></div>

        <div class="tabs">
            <button onclick="mudarAba('tab-jogos', this)" class="active">Jogos</button>
            <button onclick="mudarAba('tab-consulta', this)">Consulta</button>
            <button onclick="mudarAba('tab-admin', this)" id="btn-admin-tab" style="display:none;">Admin</button>
        </div>

        <div id="tab-jogos" class="tab-content">
            <div id="area-envio">
                <div class="input-cartela">
                    <input type="tel" id="num1" maxlength="2"> <input type="tel" id="num2" maxlength="2"> <input type="tel" id="num3" maxlength="2">
                    <input type="tel" id="num4" maxlength="2"> <input type="tel" id="num5" maxlength="2"> <input type="tel" id="num6" maxlength="2">
                </div>
                <button onclick="salvarCartela()">Salvar Cartela</button>
            </div>
            <div id="msg-pendente" class="hidden aviso">Aguarde a liberação do pagamento para enviar cartelas.</div>
            <hr>
            <h4>Minhas Cartelas</h4>
            <div id="lista-minhas-cartelas"></div>
        </div>

        <div id="tab-consulta" class="tab-content hidden">
            <input type="text" id="busca-dezenas" placeholder="Ex: 01 02 03">
            <button onclick="consultarDezenas()">Pesquisar</button>
            <button onclick="exportarPDF()" class="secondary">Baixar PDF</button>
            <div id="resultado-consulta" style="margin-top:10px;"></div>
        </div>

        <div id="tab-admin" class="tab-content hidden">
            <button onclick="resetarSistema()" class="btn-danger btn-small">RESET GERAL</button>
            <div id="lista-participantes" style="margin-top:15px;"></div>
        </div>
    </section>

    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-firestore-compat.js"></script>

    <script>
        // CONFIGURAÇÃO COM SUA NOVA CHAVE
        const firebaseConfig = {
            apiKey: "AIzaSyAUaxZYIGHZpyMpscmoCIui9VgvVNm0E00",
            authDomain: "bolao-73a1c.firebaseapp.com",
            projectId: "bolao-73a1c",
            storageBucket: "bolao-73a1c.firebasestorage.app",
            messagingSenderId: "127440976116",
            appId: "1:127440976116:web:1531950c1001129dbac6a1",
            measurementId: "G-K5ZN2E9KP1"
        };

        firebase.initializeApp(firebaseConfig);
        const auth = firebase.auth();
        const db = firebase.firestore();

        let currentUser = null;
        let userData = null;

        // Funções de Interface
        function mostrarTela(id) {
            document.querySelectorAll('.screen').forEach(s => s.classList.add('hidden'));
            document.getElementById(id).classList.remove('hidden');
        }

        function mudarAba(id, btn) {
            document.querySelectorAll('.tab-content').forEach(t => t.classList.add('hidden'));
            document.getElementById(id).classList.remove('hidden');
            document.querySelectorAll('.tabs button').forEach(b => b.classList.remove('active'));
            btn.classList.add('active');
        }

        function showToast(m) {
            const t = document.getElementById('toast');
            t.innerText = m; t.classList.remove('hidden');
            setTimeout(() => t.classList.add('hidden'), 3000);
        }

        const cpfEmail = (cpf) => `${cpf}@bolao.com`;

        async function registrarUsuario() {
            const nome = document.getElementById('reg-nome').value;
            const cpf = document.getElementById('reg-cpf').value;
            if(!nome || cpf.length < 11) return showToast("Dados inválidos");
            const senha = "0" + cpf.slice(-5);
            try {
                const res = await auth.createUserWithEmailAndPassword(cpfEmail(cpf), senha);
                await db.collection('usuarios').doc(res.user.uid).set({
                    nome, cpf, status: 'pendente', isAdmin: false
                });
                alert("Sucesso! Senha: " + cpf.slice(-5));
                mostrarTela('login-screen');
            } catch(e) { showToast(e.message); }
        }

        async function fazerLogin() {
            const cpf = document.getElementById('login-cpf').value;
            const pass = "0" + document.getElementById('login-senha').value;
            try { await auth.signInWithEmailAndPassword(cpfEmail(cpf), pass); } 
            catch(e) { showToast("Erro no login"); }
        }

        function logout() { auth.signOut(); }

        auth.onAuthStateChanged(async (user) => {
            if(user) {
                currentUser = user;
                const d = await db.collection('usuarios').doc(user.uid).get();
                userData = d.data();
                document.getElementById('user-welcome').innerText = "Olá, " + userData.nome.split(' ')[0];
                const st = document.getElementById('status-pagamento');
                if(userData.status === 'pago') {
                    st.innerHTML = '<span class="status-pago">PAGO</span>';
                    document.getElementById('area-envio').classList.remove('hidden');
                    document.getElementById('msg-pendente').classList.add('hidden');
                } else {
                    st.innerHTML = '<span class="status-pendente">PENDENTE</span>';
                    document.getElementById('area-envio').classList.add('hidden');
                    document.getElementById('msg-pendente').classList.remove('hidden');
                }
                if(userData.isAdmin) {
                    document.getElementById('btn-admin-tab').style.display = 'block';
                    carregarAdmin();
                }
                carregarMinhas();
                mostrarTela('dashboard-screen');
            } else { mostrarTela('login-screen'); }
        });

        async function salvarCartela() {
            let nums = [];
            for(let i=1; i<=6; i++) nums.push(parseInt(document.getElementById('num'+i).value));
            if(nums.includes(NaN)) return showToast("Preencha 6 números");
            nums.sort((a,b) => a-b);
            if(new Set(nums).size !== 6) return showToast("Números repetidos");

            const snap = await db.collection('cartelas').get();
            let existe = false;
            snap.forEach(doc => { if(JSON.stringify(doc.data().numeros) === JSON.stringify(nums)) existe = true; });
            if(existe) return alert("Essa cartela já existe!");

            await db.collection('cartelas').add({
                uid: currentUser.uid, nome: userData.nome, numeros: nums
            });
            showToast("Salvo!");
            for(let i=1; i<=6; i++) document.getElementById('num'+i).value = '';
        }

        function carregarMinhas() {
            db.collection('cartelas').where('uid','==',currentUser.uid).onSnapshot(s => {
                const div = document.getElementById('lista-minhas-cartelas');
                div.innerHTML = '';
                s.forEach(doc => {
                    const d = doc.data();
                    div.innerHTML += `<div style="margin:5px 0;">${d.numeros.map(n => `<span class="bolinha">${n}</span>`).join('')}</div>`;
                });
            });
        }

        async function consultarDezenas() {
            const b = document.getElementById('busca-dezenas').value.split(' ').map(n => parseInt(n));
            const snap = await db.collection('cartelas').get();
            let res = [];
            snap.forEach(doc => {
                const d = doc.data();
                const hits = d.numeros.filter(n => b.includes(n)).length;
                if(hits > 0) res.push({...d, hits});
            });
            res.sort((a,b) => b.hits - a.hits);
            let h = '';
            res.forEach(r => h += `<p>${r.nome}: ${r.numeros.join('-')} (${r.hits} acertos)</p>`);
            document.getElementById('resultado-consulta').innerHTML = h;
            window.lastRes = res;
        }

        function exportarPDF() {
            const { jsPDF } = window.jspdf;
            const doc = new jsPDF();
            doc.text("Relatório Bolão", 10, 10);
            let y = 20;
            window.lastRes.forEach(r => { doc.text(`${r.nome}: ${r.numeros.join('-')} (${r.hits})`, 10, y); y+=7; });
            doc.save("resultado.pdf");
        }

        function carregarAdmin() {
            db.collection('usuarios').onSnapshot(s => {
                const div = document.getElementById('lista-participantes');
                div.innerHTML = '';
                s.forEach(doc => {
                    const u = doc.data();
                    if(u.isAdmin) return;
                    div.innerHTML += `<div style="padding:10px; border-bottom:1px solid #eee;">
                        ${u.nome} - ${u.status} 
                        <button class="btn-small" onclick="mudarStatus('${doc.id}','${u.status}')">Mudar</button>
                    </div>`;
                });
            });
        }

        async function mudarStatus(id, s) {
            await db.collection('usuarios').doc(id).update({ status: s === 'pago' ? 'pendente' : 'pago' });
        }

        async function resetarSistema() {
            if(confirm("Deseja apagar TUDO?")) {
                const c = await db.collection('cartelas').get();
                c.forEach(d => d.ref.delete());
                const u = await db.collection('usuarios').get();
                u.forEach(d => { if(!d.data().isAdmin) d.ref.delete(); });
                alert("Resetado!");
            }
        }
    </script>
</body>
</html>
