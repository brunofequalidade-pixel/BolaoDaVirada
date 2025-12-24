<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Inscrição Sinuca PWA</title>
    <meta name="theme-color" content="#4CAF50">
    
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>

    <style>
        /* ================= ESTILOS CSS ================= */
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
            color: #333;
            display: flex;
            justify-content: center;
            min-height: 100vh;
        }

        /* Classes Utilitárias */
        .hidden { display: none !important; }
        
        .screen { 
            width: 100%; 
            max-width: 480px; /* Tamanho Mobile */
            background: #fff; 
            padding: 25px; 
            border-radius: 12px; 
            box-shadow: var(--shadow); 
            height: fit-content;
            animation: fadeIn 0.3s ease-in-out;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        h1, h2, h3, h4 { text-align: center; margin-top: 0; color: var(--dark); }
        
        /* Formulários */
        label { font-weight: bold; font-size: 14px; display: block; margin-top: 10px; }
        
        input { 
            width: 100%; 
            padding: 14px; 
            margin: 8px 0; 
            border: 1px solid #ddd; 
            border-radius: 8px; 
            font-size: 16px; 
            background: #fafafa;
        }
        
        input:focus { border-color: var(--primary); outline: none; }

        /* Botões */
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
            transition: background 0.2s;
        }
        
        button:active { transform: scale(0.98); }
        button:hover { background: var(--primary-dark); }
        button.secondary { background: #757575; }
        button.btn-danger { background: var(--danger); }
        button.btn-small { width: auto; padding: 6px 12px; font-size: 13px; margin-top: 0;}

        /* Links */
        .link-text { text-align: center; margin-top: 15px; font-size: 14px; }
        .link-text a { color: var(--primary); text-decoration: none; font-weight: bold; }

        /* Status */
        .card-status { text-align: center; margin-bottom: 20px; font-size: 18px; }
        .status-pago { color: var(--primary); font-weight: bold; background: #e8f5e9; padding: 5px 10px; border-radius: 4px; border: 1px solid var(--primary); }
        .status-pendente { color: var(--danger); font-weight: bold; background: #ffebee; padding: 5px 10px; border-radius: 4px; border: 1px solid var(--danger); }

        /* Cartelas */
        .aviso { font-size: 13px; color: #666; background: #fff3cd; padding: 10px; border-radius: 5px; margin-bottom: 10px; border-left: 4px solid #ffc107; }
        
        .input-cartela { 
            display: grid; 
            grid-template-columns: repeat(3, 1fr); 
            gap: 10px; 
        }
        
        .input-cartela input { text-align: center; font-weight: bold; font-size: 18px; }

        .cartela-item { 
            background: #f9f9f9; 
            padding: 12px; 
            margin: 8px 0; 
            border-radius: 8px; 
            border: 1px solid #eee;
        }
        
        .bolinha { 
            display: inline-block; 
            background: #333; 
            color: #fff; 
            width: 28px; 
            height: 28px; 
            text-align: center; 
            line-height: 28px; 
            border-radius: 50%; 
            margin-right: 4px; 
            font-size: 13px; 
            font-weight: bold;
        }

        /* Tabs (Menu) */
        .tabs { display: flex; gap: 5px; margin-bottom: 20px; border-bottom: 2px solid #ddd; }
        .tabs button { 
            margin: 0; 
            background: transparent; 
            color: #666; 
            border-radius: 5px 5px 0 0; 
            border-bottom: none;
            padding: 10px;
        }
        .tabs button.active { background: var(--primary); color: #fff; }

        /* Toast (Notificação) */
        .toast { 
            position: fixed; 
            bottom: 30px; 
            left: 50%; 
            transform: translateX(-50%); 
            background: rgba(0,0,0,0.85); 
            color: #fff; 
            padding: 12px 24px; 
            border-radius: 50px; 
            z-index: 2000; 
            font-size: 14px;
            text-align: center;
        }

        /* Admin Styles */
        .admin-controls { background: #eee; padding: 10px; border-radius: 8px; margin-bottom: 15px; }
        .admin-user-row { 
            display: flex; 
            justify-content: space-between; 
            align-items: center; 
            border-bottom: 1px solid #eee; 
            padding: 12px 0; 
        }
        .toggles { display: flex; gap: 15px; margin-top: 10px; font-size: 14px; }

        /* Ajustes para telas muito pequenas */
        @media (max-width: 350px) {
            .input-cartela { grid-template-columns: repeat(2, 1fr); }
        }
    </style>
</head>
<body>

    <div id="toast" class="toast hidden"></div>

    <section id="login-screen" class="screen">
        <h2>🎱 Inscrição Sinuca</h2>
        <p style="text-align:center; color:#666;">Entre para gerenciar seus jogos</p>
        
        <label>CPF</label>
        <input type="tel" id="login-cpf" placeholder="Digite apenas números" maxlength="11">
        
        <label>Senha</label>
        <input type="password" id="login-senha" placeholder="Senha">
        
        <button onclick="fazerLogin()">ENTRAR</button>
        
        <div class="link-text">
            Não tem cadastro? <a href="#" onclick="mostrarTela('register-screen')">Criar conta</a>
        </div>
    </section>

    <section id="register-screen" class="screen hidden">
        <h2>Novo Cadastro</h2>
        
        <label>Nome Completo</label>
        <input type="text" id="reg-nome" placeholder="Seu nome">
        
        <label>CPF (Será seu Login)</label>
        <input type="tel" id="reg-cpf" placeholder="Apenas números" maxlength="11">
        
        <div class="aviso">
            Sua senha será gerada automaticamente com os <strong>5 últimos dígitos</strong> do seu CPF.
        </div>

        <button onclick="registrarUsuario()">CADASTRAR</button>
        <button class="secondary" onclick="mostrarTela('login-screen')">Voltar</button>
    </section>

    <section id="dashboard-screen" class="screen hidden">
        <header style="display:flex; justify-content:space-between; align-items:center; margin-bottom:15px;">
            <h3 id="user-welcome" style="margin:0; text-align:left;">Olá, Usuário</h3>
            <button onclick="logout()" class="btn-small secondary">Sair</button>
        </header>

        <div id="status-pagamento" class="card-status">Carregando...</div>

        <div class="tabs">
            <button onclick="mudarAba('tab-jogos', this)" class="active">Cartelas</button>
            <button onclick="mudarAba('tab-consulta', this)">Consultar</button>
            <button onclick="mudarAba('tab-admin', this)" id="btn-admin-tab" style="display:none;">Admin</button>
        </div>

        <div id="tab-jogos" class="tab-content">
            <h4>Cadastrar Nova Cartela</h4>
            <p class="aviso">Escolha 6 números únicos. O sistema bloqueia cartelas repetidas.</p>
            
            <div id="area-envio-cartela">
                <div class="input-cartela">
                    <input type="tel" id="num1" placeholder="01" maxlength="2">
                    <input type="tel" id="num2" placeholder="02" maxlength="2">
                    <input type="tel" id="num3" placeholder="03" maxlength="2">
                    <input type="tel" id="num4" placeholder="04" maxlength="2">
                    <input type="tel" id="num5" placeholder="05" maxlength="2">
                    <input type="tel" id="num6" placeholder="06" maxlength="2">
                </div>
                <button onclick="salvarCartela()" id="btn-salvar-cartela">Salvar Cartela</button>
            </div>
            <div id="bloqueio-pagamento" class="hidden aviso" style="color:var(--danger); border-color:var(--danger); text-align:center;">
                Pagamento Pendente. Aguarde liberação para jogar.
            </div>
            
            <hr>
            <h4>Suas Cartelas Salvas</h4>
            <div id="lista-minhas-cartelas">Nenhuma cartela salva.</div>
        </div>

        <div id="tab-consulta" class="tab-content hidden">
            <h4>Consultar Dezenas</h4>
            <input type="text" id="busca-dezenas" placeholder="Ex: 01 05 10 (separados por espaço)">
            <button onclick="consultarDezenas()">Pesquisar</button>
            <button onclick="exportarPDF()" class="secondary">📄 Baixar PDF</button>
            <div id="resultado-consulta" style="margin-top:15px;"></div>
        </div>

        <div id="tab-admin" class="tab-content hidden">
            <h4>Painel do Administrador</h4>
            <div class="admin-controls">
                <button onclick="resetarSistema()" class="btn-danger btn-small">⚠ ZERAR SISTEMA</button>
                <div class="toggles">
                    <small>Gerencie os pagamentos abaixo:</small>
                </div>
            </div>
            <div id="lista-participantes">Carregando participantes...</div>
        </div>
    </section>

    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-firestore-compat.js"></script>

    <script>
        // 1. CONFIGURAÇÃO (Sua chave inserida aqui)
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
        const auth = firebase.auth();
        const db = firebase.firestore();

        // Variáveis de Estado
        let currentUser = null;
        let userData = null;

        // --- UTILITÁRIOS ---
        function mostrarTela(telaId) {
            document.querySelectorAll('.screen').forEach(s => s.classList.add('hidden'));
            document.getElementById(telaId).classList.remove('hidden');
        }

        function mudarAba(abaId, btnElement) {
            document.querySelectorAll('.tab-content').forEach(t => t.classList.add('hidden'));
            document.getElementById(abaId).classList.remove('hidden');
            
            // Atualiza botões
            document.querySelectorAll('.tabs button').forEach(b => b.classList.remove('active'));
            if(btnElement) btnElement.classList.add('active');
        }

        function showToast(msg) {
            const t = document.getElementById('toast');
            t.innerText = msg;
            t.classList.remove('hidden');
            setTimeout(() => t.classList.add('hidden'), 3500);
        }

        // --- AUTENTICAÇÃO ---
        // Hack: Firebase exige email, criamos um email fake baseado no CPF
        const cpfParaEmail = (cpf) => `${cpf}@sinucaapp.com`;

        async function registrarUsuario() {
            const nome = document.getElementById('reg-nome').value.trim();
            const cpf = document.getElementById('reg-cpf').value.trim();

            if (!nome || !cpf || cpf.length < 11) return showToast("Preencha Nome e CPF corretamente (11 dígitos).");

            // Senha: "0" + os 5 últimos do CPF (para ter 6 digitos minimo exigido pelo firebase)
            const senha = "0" + cpf.slice(-5);

            try {
                // Criar Auth
                const credencial = await auth.createUserWithEmailAndPassword(cpfParaEmail(cpf), senha);
                
                // Salvar dados extras no Firestore
                await db.collection('usuarios').doc(credencial.user.uid).set({
                    nome: nome,
                    cpf: cpf,
                    status: 'pendente', 
                    isAdmin: false,
                    createdAt: new Date()
                });

                alert(`✅ Cadastro Sucesso!\n\nUsuário: ${cpf}\nSenha: ${cpf.slice(-5)} (Seus 5 últimos números)`);
                mostrarTela('login-screen');
            } catch (error) {
                if(error.code === 'auth/email-already-in-use') {
                    showToast("Esse CPF já está cadastrado.");
                } else {
                    showToast("Erro: " + error.message);
                }
            }
        }

        async function fazerLogin() {
            const cpf = document.getElementById('login-cpf').value.trim();
            const senhaUser = document.getElementById('login-senha').value.trim();

            if(!cpf || !senhaUser) return showToast("Preencha CPF e Senha.");

            // Adiciona o '0' fixo para validar
            const senhaAuth = "0" + senhaUser;

            try {
                await auth.signInWithEmailAndPassword(cpfParaEmail(cpf), senhaAuth);
            } catch (error) {
                showToast("CPF ou Senha incorretos.");
                console.error(error);
            }
        }

        function logout() {
            auth.signOut();
            document.getElementById('login-cpf').value = "";
            document.getElementById('login-senha').value = "";
        }

        // --- OBSERVADOR DE ESTADO (Onde a mágica acontece) ---
        auth.onAuthStateChanged(async (user) => {
            if (user) {
                currentUser = user;
                // Busca dados
                try {
                    const doc = await db.collection('usuarios').doc(user.uid).get();
                    if (!doc.exists) {
                        // Caso raro onde tem auth mas não tem doc
                        showToast("Erro de cadastro. Contate admin.");
                        return;
                    }
                    userData = doc.data();

                    // Atualiza Interface
                    document.getElementById('user-welcome').innerText = `Olá, ${userData.nome.split(' ')[0]}`;
                    
                    const statusDiv = document.getElementById('status-pagamento');
                    const areaEnvio = document.getElementById('area-envio-cartela');
                    const avisoBloqueio = document.getElementById('bloqueio-pagamento');

                    if(userData.status === 'pago') {
                        statusDiv.innerHTML = '<span class="status-pago">✅ PAGO - Liberado</span>';
                        areaEnvio.classList.remove('hidden');
                        avisoBloqueio.classList.add('hidden');
                    } else {
                        statusDiv.innerHTML = '<span class="status-pendente">⏳ PENDENTE - Aguardando Pagamento</span>';
                        areaEnvio.classList.add('hidden');
                        avisoBloqueio.classList.remove('hidden');
                    }

                    carregarMinhasCartelas();

                    // Admin Check
                    if (userData.isAdmin) {
                        document.getElementById('btn-admin-tab').style.display = 'block';
                        carregarDadosAdmin();
                    } else {
                        document.getElementById('btn-admin-tab').style.display = 'none';
                    }

                    mostrarTela('dashboard-screen');
                } catch (e) {
                    console.error(e);
                    showToast("Erro ao carregar perfil.");
                }
            } else {
                currentUser = null;
                userData = null;
                mostrarTela('login-screen');
            }
        });

        // --- CARTELAS ---
        async function salvarCartela() {
            // Coletar números
            let numeros = [];
            for(let i=1; i<=6; i++) {
                let val = document.getElementById('num'+i).value;
                if(val) numeros.push(parseInt(val));
            }

            // Validações
            if (numeros.length !== 6) return showToast("Preencha todos os 6 números.");
            
            // Ordena
            numeros.sort((a, b) => a - b);

            // Verifica repetidos na própria cartela
            const setNums = new Set(numeros);
            if (setNums.size !== 6) return showToast("Não repita números na mesma cartela.");

            // Verifica duplicidade GLOBAL no banco
            const btn = document.getElementById('btn-salvar-cartela');
            btn.innerText = "Verificando...";
            btn.disabled = true;

            try {
                const snapshot = await db.collection('cartelas').get();
                let existe = false;
                
                snapshot.forEach(doc => {
                    if (JSON.stringify(doc.data().numeros) === JSON.stringify(numeros)) {
                        existe = true;
                    }
                });

                if (existe) {
                    alert("❌ Essa combinação já foi escolhida por outra pessoa! Tente outros números.");
                } else {
                    // Salva
                    await db.collection('cartelas').add({
                        uid: currentUser.uid,
                        nomeUsuario: userData.nome,
                        numeros: numeros,
                        data: new Date()
                    });
                    showToast("✅ Cartela salva com sucesso!");
                    // Limpa inputs
                    for(let i=1; i<=6; i++) document.getElementById('num'+i).value = '';
                }

            } catch (e) {
                alert("Erro: " + e.message);
            } finally {
                btn.innerText = "Salvar Cartela";
                btn.disabled = false;
            }
        }

        function carregarMinhasCartelas() {
            db.collection('cartelas')
              .where("uid", "==", currentUser.uid)
              .onSnapshot(snap => {
                const div = document.getElementById('lista-minhas-cartelas');
                div.innerHTML = '';
                if(snap.empty) {
                    div.innerHTML = '<p style="text-align:center; color:#999;">Você ainda não tem cartelas.</p>';
                    return;
                }
                snap.forEach(doc => {
                    const d = doc.data();
                    const html = `
                        <div class="cartela-item">
                            ${d.numeros.map(n => `<span class="bolinha">${n < 10 ? '0'+n : n}</span>`).join('')}
                        </div>
                    `;
                    div.innerHTML += html;
                });
            });
        }

        // --- CONSULTA E PDF ---
        async function consultarDezenas() {
            const busca = document.getElementById('busca-dezenas').value;
            if(!busca) return showToast("Digite as dezenas para buscar.");
            
            const numsBusca = busca.split(' ').map(n => parseInt(n)).filter(n => !isNaN(n));
            const divRes = document.getElementById('resultado-consulta');
            divRes.innerHTML = 'Pesquisando...';

            const snap = await db.collection('cartelas').get();
            let resultados = [];
            
            let resumo = { 6:0, 5:0, 4:0, 3:0, 2:0, 1:0 }; // Contadores

            snap.forEach(doc => {
                const d = doc.data();
                const acertos = d.numeros.filter(num => numsBusca.includes(num));
                
                if (acertos.length > 0) {
                    resultados.push({ ...d, acertos: acertos.length });
                    if(resumo[acertos.length] !== undefined) resumo[acertos.length]++;
                }
            });

            // Ordena
            resultados.sort((a,b) => b.acertos - a.acertos);

            // Monta HTML
            let html = '<div style="background:#fff; padding:10px; border-radius:8px;">';
            html += '<strong>Resumo:</strong><br>';
            for(let k=6; k>=1; k--) {
                if(resumo[k] > 0) html += `• ${resumo[k]} cartelas com ${k} acertos<br>`;
            }
            html += '<hr>';
            
            resultados.forEach(r => {
                const numsFormatados = r.numeros.map(n => {
                    // Destacar números acertados
                    const style = numsBusca.includes(n) ? 'background:#4CAF50;' : 'background:#333;';
                    return `<span class="bolinha" style="${style}">${n < 10 ? '0'+n : n}</span>`;
                }).join('');

                html += `<div style="margin-bottom:10px;">
                    <small>${r.nomeUsuario}</small><br>
                    ${numsFormatados}
                </div>`;
            });
            html += '</div>';

            divRes.innerHTML = html;
            window.lastSearchResults = resultados; // Para o PDF
        }

        function exportarPDF() {
            if (!window.lastSearchResults || window.lastSearchResults.length === 0) return alert("Faça uma pesquisa com resultados primeiro.");
            
            const { jsPDF } = window.jspdf;
            const doc = new jsPDF();

            doc.setFontSize(16);
            doc.text("Relatório - Sinuca App", 10, 10);
            
            doc.setFontSize(10);
            doc.text(`Gerado em: ${new Date().toLocaleString()}`, 10, 16);

            let y = 25;
            window.lastSearchResults.forEach((r) => {
                if (y > 270) { doc.addPage(); y = 20; }
                const linha = `[${r.acertos} pts] ${r.nomeUsuario}: ${r.numeros.join(' - ')}`;
                doc.text(linha, 10, y);
                y += 8;
            });

            doc.save("resultado-sinuca.pdf");
        }

        // --- ADMINISTRAÇÃO ---
        function carregarDadosAdmin() {
            db.collection('usuarios').orderBy('nome').onSnapshot(snap => {
                const div = document.getElementById('lista-participantes');
                div.innerHTML = '';
                snap.forEach(doc => {
                    const u = doc.data();
                    if(u.isAdmin) return; // Não mostra o próprio admin na lista de edição

                    div.innerHTML += `
                        <div class="admin-user-row">
                            <div>
                                <strong>${u.nome}</strong><br>
                                <small>CPF: ${u.cpf}</small>
                            </div>
                            <div>
                                <button class="btn-small ${u.status === 'pago' ? 'secondary' : ''}" 
                                        onclick="mudarStatus('${doc.id}', '${u.status}')">
                                    ${u.status === 'pago' ? 'Desmarcar' : '✔ Confirmar Pagamento'}
                                </button>
                                <button class="btn-small btn-danger" style="margin-left:5px;" onclick="excluirUsuario('${doc.id}')">🗑</button>
                            </div>
                        </div>
                    `;
                });
            });
        }

        async function mudarStatus(uid, statusAtual) {
            const novoStatus = statusAtual === 'pago' ? 'pendente' : 'pago';
            await db.collection('usuarios').doc(uid).update({ status: novoStatus });
            showToast("Status atualizado!");
        }

        async function excluirUsuario(uid) {
            if(confirm("Tem certeza que deseja remover este usuário?")) {
                await db.collection('usuarios').doc(uid).delete();
                // Nota: As cartelas não são apagadas automaticamente aqui para segurança histórica, 
                // mas o usuário perde acesso.
            }
        }

        async function resetarSistema() {
            const confirma = prompt("⚠️ PERIGO: Isso apagará TODAS as cartelas e todos os usuários (exceto Admins).\n\nDigite 'ZERAR' para confirmar:");
            if(confirma === "ZERAR") {
                const snapC = await db.collection('cartelas').get();
                const batch = db.batch();
                snapC.forEach(d => batch.delete(d.ref));
                
                const snapU = await db.collection('usuarios').get();
                snapU.forEach(d => {
                    if(!d.data().isAdmin) batch.delete(d.ref);
                });
                
                await batch.commit();
                alert("Sistema reiniciado com sucesso.");
            }
        }
    </script>
</body>
</html>
