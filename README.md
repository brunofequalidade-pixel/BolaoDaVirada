<!DOCTYPE html>
<html lang="pt-br">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Bolão Mega da Virada</title>

<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>

<style>
body{font-family:sans-serif;background:#f2f2f2;padding:10px}
.container{max-width:600px;margin:auto;background:#fff;padding:20px;border-radius:10px}
.hidden{display:none}
.grid{display:grid;grid-template-columns:repeat(10,1fr);gap:5px}
.num{padding:10px;border-radius:50%;border:1px solid #ccc;font-weight:bold}
.num.sel{background:green;color:#fff}
.num.dup{background:red;color:#fff}
button{padding:10px;margin-top:10px;width:100%}
.card{background:#eee;padding:10px;margin-top:10px;border-radius:8px}
</style>
</head>

<body>
<div class="container">

<h2>🎯 Bolão Mega da Virada</h2>

<div id="auth">
<input id="nome" placeholder="Nome">
<input id="cpf" placeholder="CPF">
<button onclick="cadastrar()">Cadastrar</button>

<input id="cpfLogin" placeholder="CPF">
<input id="senhaLogin" placeholder="Senha">
<button onclick="login()">Entrar</button>
</div>

<div id="app" class="hidden">
<h3 id="userNome"></h3>
<p>Status: <span id="statusPago"></span></p>

<div id="areaCartela">
<h4>Escolha 6 dezenas</h4>
<div class="grid" id="grid"></div>
<button onclick="enviarCartela()">Enviar Cartela</button>
</div>

<h4>Participantes</h4>
<div id="lista"></div>

<div id="admin" class="hidden">
<h4>Administração</h4>
<button onclick="toggleLock()">Bloquear / Desbloquear</button>
</div>

<button onclick="sair()">Sair</button>
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
appId: "1:338241576305:web:288b6124384c6be4f76ad0",
measurementId: "G-PEDG30FS2R"
};

firebase.initializeApp(firebaseConfig);
const db = firebase.database();

let user=null;
let nums=[];

function cadastrar(){
 const n=nome.value;
 const c=cpf.value.replace(/\D/g,'');
 const s=c.slice(-5);
 db.ref('users/'+c).set({nome:n,cpf:c,senha:s,pago:false,cartelas:[]});
 alert(`Cadastro OK\nCPF: ${c}\nSenha: ${s}`);
}

function login(){
 const c=cpfLogin.value;
 const s=senhaLogin.value;
 db.ref('users/'+c).once('value',snap=>{
  if(snap.val() && snap.val().senha===s){
    user=snap.val();
    auth.classList.add('hidden');
    app.classList.remove('hidden');
    userNome.innerText=user.nome;
    statusPago.innerText=user.pago?'PAGO':'PENDENTE';
    if(c==="03454627508") admin.classList.remove('hidden');
    montarGrid();
    listar();
  } else alert("Erro login");
 });
}

function montarGrid(){
 grid.innerHTML='';
 for(let i=1;i<=60;i++){
  const b=document.createElement('button');
  b.innerText=i.toString().padStart(2,'0');
  b.className='num';
  b.onclick=()=>sel(i,b);
  grid.appendChild(b);
 }
}

function sel(n,b){
 if(nums.includes(n)){nums=nums.filter(x=>x!==n);b.classList.remove('sel')}
 else if(nums.length<6){nums.push(n);b.classList.add('sel')}
}

function enviarCartela(){
 if(!user.pago) return alert("Aguardando pagamento");
 if(nums.length<6) return alert("Selecione 6");
 const seq=nums.sort((a,b)=>a-b).map(x=>x.toString().padStart(2,'0')).join('-');
 db.ref('cartelas/'+seq).once('value',snap=>{
  if(snap.exists()){
   document.querySelectorAll('.num.sel').forEach(b=>b.classList.add('dup'));
   alert("Sequência já existe");
  }else{
   user.cartelas.push(seq);
   db.ref('users/'+user.cpf+'/cartelas').set(user.cartelas);
   db.ref('cartelas/'+seq).set(user.cpf);
   alert("Cartela enviada");
   nums=[];
   montarGrid();
   listar();
  }
 });
}

function listar(){
 db.ref('users').once('value',snap=>{
  lista.innerHTML='';
  Object.values(snap.val()).forEach(u=>{
   lista.innerHTML+=`<div class="card">${u.nome}<br>${u.cartelas?.join('<br>')||''}</div>`;
  });
 });
}

function toggleLock(){
 db.ref('config/locked').set(true);
}

function sair(){location.reload();}
</script>

</body>
</html>
