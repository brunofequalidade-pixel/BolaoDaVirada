# BolaoDaVirada
Bolão da Virada
<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<title>Bolão Mega da Virada</title>

<!-- Firebase -->
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-firestore-compat.js"></script>

<!-- jsPDF -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>

<style>
body { font-family: Arial; background:#f2f2f2; padding:20px }
section { background:#fff; padding:15px; margin-bottom:20px; border-radius:8px }
input, button { width:100%; padding:8px; margin:5px 0 }
.pago { color:green; font-weight:bold }
.pendente { color:red; font-weight:bold }
hr { margin:10px 0 }
</style>
</head>

<body>

<h2>🎯 Bolão Mega da Virada</h2>
<p id="statusPublico"></p>

<section>
<h3>Cadastro</h3>
<input id="nome" placeholder="Nome completo">
<input id="cpf" placeholder="CPF">
<button onclick="cadastrar()">Cadastrar</button>
<p id="msgCadastro"></p>
</section>

<section>
<h3>Login</h3>
<input id="cpfLogin" placeholder="CPF">
<input id="senhaLogin" placeholder="Senha">
<button onclick="login()">Entrar</button>
<p id="msgLogin"></p>
</section>

<section id="areaCartelas" style="display:none;">
<h3>Enviar Cartelas</h3>
<label><input type="checkbox" id="confirmPix"> Já fiz o Pix</label>
<input id="c1" placeholder="Cartela 1: 01,02,03,04,05,06">
<button onclick="enviarCartela(1)">Enviar Cartela 1</button>
<input id="c2" placeholder="Cartela 2: 10,20,30,40,50,60">
<button onclick="enviarCartela(2)">Enviar Cartela 2</button>
<p id="msgCartela"></p>
</section>

<section>
<h3>💰 Status de Pagamento</h3>
<div id="listaPagamentos"></div>
</section>

<section>
<h3>📋 Cartelas Registradas</h3>
<div id="listaCartelas"></div>
</section>

<section>
<h3>🔍 Conferência</h3>
<input id="sorteadas" placeholder="01,10,20,30,40,50">
<button onclick="conferir()">Conferir</button>
<button onclick="exportarPDF()">📄 Exportar PDF</button>
<div id="resultado"></div>
</section>

<section>
<h3>🏆 Ranking</h3>
<div id="ranking"></div>
</section>

<section id="areaAdmin" style="display:none;">
<h3>🔐 Administração</h3>
<button onclick="abrirSistema()">🟢 Abrir Bolão</button>
<button onclick="fecharSistema()">🔴 Fechar Bolão</button>
<div id="adminPagamentos"></div>
</section>

<script>
// ================= FIREBASE CONFIG (SUA CHAVE) =================
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
// ===============================================================

const CPF_ADMIN = "03454627508";
let usuarioAtual = null;

const norm = arr => arr.map(n => parseInt(n)).sort((a,b)=>a-b);

// ----------------- STATUS DO SISTEMA -----------------
async function sistemaAberto(){
  const d = await db.collection("config").doc("sistema").get();
  return d.exists ? d.data().aberto : true;
}

// ----------------- CADASTRO -----------------
async function cadastrar(){
  if(!(await sistemaAberto())) return msgCadastro.innerText="Bolão fechado";
  const nome = document.getElementById("nome").value;
  const cpf = document.getElementById("cpf").value;
  const senha = cpf.slice(-5);
  await db.collection("usuarios").doc(cpf).set({ nome, senha, pago:false });
  msgCadastro.innerText = `Cadastro realizado! Senha: ${senha}`;
  carregarPagamentos();
}

// ----------------- LOGIN -----------------
async function login(){
  const cpf = cpfLogin.value;
  const senha = senhaLogin.value;
  const d = await db.collection("usuarios").doc(cpf).get();
  if(!d.exists || d.data().senha !== senha) {
    msgLogin.innerText="Login inválido"; return;
  }
  usuarioAtual = { cpf, nome: d.data().nome };
  areaCartelas.style.display="block";
  if(cpf === CPF_ADMIN) areaAdmin.style.display="block";
  msgLogin.innerText="Login realizado";
  carregarCartelas(); carregarPagamentos(); carregarAdmin();
}

// ----------------- ENVIAR CARTELA -----------------
async function enviarCartela(n){
  if(!(await sistemaAberto())) return msgCartela.innerText="Bolão fechado";

  const u = await db.collection("usuarios").doc(usuarioAtual.cpf).get();
  if(!u.data().pago) return msgCartela.innerText="Pagamento pendente";
  if(!confirmPix.checked) return msgCartela.innerText="Confirme que já fez o Pix";

  const qtd = await db.collection("cartelas")
    .where("cpf","==",usuarioAtual.cpf).get();
  if(qtd.size >= 2) return msgCartela.innerText="Limite de 2 cartelas atingido";

  const dezenas = norm((n===1?c1:c2).value.split(","));
  if(dezenas.length !== 6) return msgCartela.innerText="Cartela inválida";

  const seq = dezenas.join("-");
  const dup = await db.collection("cartelas")
    .where("sequencia","==",seq).get();
  if(!dup.empty) return msgCartela.innerText="Sequência já existente";

  await db.collection("cartelas").add({
    cpf: usuarioAtual.cpf,
    nome: usuarioAtual.nome,
    cartela: n,
    dezenas,
    sequencia: seq
  });

  msgCartela.innerText="Cartela enviada com sucesso";
  carregarCartelas();
}

// ----------------- LISTAGENS -----------------
async function carregarCartelas(){
  listaCartelas.innerHTML="";
  const s = await db.collection("cartelas").get();
  s.forEach(d=>{
    const c = d.data();
    listaCartelas.innerHTML +=
      `<p><b>${c.nome}</b><br>Cartela ${c.cartela}: ${c.sequencia}</p>`;
  });
}

async function carregarPagamentos(){
  listaPagamentos.innerHTML="";
  const s = await db.collection("usuarios").get();
  s.forEach(d=>{
    const u = d.data();
    listaPagamentos.innerHTML +=
      `<p>${u.nome} - <span class="${u.pago?'pago':'pendente'}">
        ${u.pago?'PAGO':'PENDENTE'}</span></p>`;
  });
}

// ----------------- ADMIN -----------------
async function carregarAdmin(){
  if(usuarioAtual.cpf !== CPF_ADMIN) return;
  adminPagamentos.innerHTML="";
  const s = await db.collection("usuarios").get();
  s.forEach(d=>{
    const u = d.data();
    adminPagamentos.innerHTML +=
      `<p>${u.nome}
        <button onclick="pagar('${d.id}')">✔️</button>
        <button onclick="excluir('${d.id}')">❌</button>
      </p>`;
  });
}

async function pagar(cpf){
  await db.collection("usuarios").doc(cpf).update({ pago:true });
  carregarPagamentos(); carregarAdmin();
}

async function excluir(cpf){
  if(!confirm("Excluir participante?")) return;
  await db.collection("usuarios").doc(cpf).delete();
  const c = await db.collection("cartelas").where("cpf","==",cpf).get();
  c.forEach(d=>d.ref.delete());
  carregarPagamentos(); carregarCartelas(); carregarAdmin();
}

async function abrirSistema(){
  if(usuarioAtual.cpf === CPF_ADMIN)
    await db.collection("config").doc("sistema").set({ aberto:true });
}
async function fecharSistema(){
  if(usuarioAtual.cpf === CPF_ADMIN)
    await db.collection("config").doc("sistema").set({ aberto:false });
}

// ----------------- CONFERÊNCIA + RANKING -----------------
async function conferir(){
  const sorte = norm(sorteadas.value.split(","));
  let res={6:0,5:0,4:0,3:0}, rank=[];
  const s = await db.collection("cartelas").get();
  s.forEach(d=>{
    const c=d.data();
    const a=c.dezenas.filter(n=>sorte.includes(n));
    if(res[a.length]!=null) res[a.length]++;
    rank.push({...c, acertos:a.length});
  });
  resultado.innerHTML = Object.entries(res)
    .map(r=>`${r[0]} acertos: ${r[1]}`).join("<br>");
  rank.sort((a,b)=>b.acertos-a.acertos);
  ranking.innerHTML = rank
    .filter(r=>r.acertos>=3)
    .map(r=>`<p>${r.nome} - ${r.acertos} acertos</p>`).join("");
}

// ----------------- PDF -----------------
async function exportarPDF(){
  const { jsPDF } = window.jspdf;
  const pdf = new jsPDF();
  pdf.text("Bolão Mega da Virada",10,10);
  let y=20;
  const s = await db.collection("cartelas").get();
  s.forEach(d=>{
    const c=d.data();
    pdf.text(`${c.nome} - ${c.sequencia}`,10,y);
    y+=8; if(y>280){pdf.addPage(); y=10;}
  });
  pdf.save("bolao-mega-da-virada.pdf");
}
</script>

</body>
</html>
