<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<title>Sistema Informa - Enterprise v5.2</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script src="https://cdn.jsdelivr.net/npm/xlsx@0.18.5/dist/xlsx.full.min.js"></script>
<style>
:root { --primary: #2563eb; --danger: #dc2626; --success: #10b981; --warning: #eab308; --gray: #64748b; --indigo: #6366f1; }

/* Alterado para carregar o ficheiro local fundo.jpg */
body { 
    font-family:'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; 
    background-image: linear-gradient(rgba(240, 242, 245, 0.6), rgba(240, 242, 245, 0.6)), url('fundo.jpg');
    background-size: cover;
    background-position: center;
    background-attachment: fixed;
    min-height:100vh; 
    display:flex; 
    flex-direction:column; 
    margin:0; 
    padding: 10px; 
    box-sizing: border-box; 
}

.container{ 
    width: 100%;
    max-width: 1400px; 
    margin: 10px auto; 
    background: rgba(255, 255, 255, 0.85); 
    padding: 20px; 
    border-radius:12px; 
    flex:1; 
    box-shadow:0 10px 25px rgba(0,0,0,0.15); 
    box-sizing: border-box;
    backdrop-filter: blur(10px); 
}

input, select, textarea, button { 
    width:100%; 
    padding: 12px; 
    margin-bottom:12px; 
    border:1px solid #ddd; 
    border-radius:8px; 
    box-sizing: border-box; 
    font-size: 16px; 
}

button { background: var(--primary); color:#fff; border:none; cursor:pointer; font-weight:bold; transition: 0.2s; }
button:hover { opacity: 0.9; transform: translateY(-1px); }
button.danger { background: var(--danger); }
button.secondary { background: var(--gray); }
button.success { background: var(--success); }

.card { border:1px solid #eee; padding:15px; border-radius:8px; margin:10px 0; background: rgba(255, 255, 255, 0.95); position: relative; word-wrap: break-word; }
.bloqueado { background: #fee2e2 !important; border: 1px solid #ef4444; }

.elogio, .reclamacao, .melhorar, .excluida, .feedback-item, .log-item { padding:12px; margin:10px 0; position: relative; border-radius: 4px; border-left-width: 5px; border-left-style: solid; }
.elogio { background:#f0fdf4; border-left-color: var(--success); }
.reclamacao { background:#fef2f2; border-left-color: var(--danger); }
.melhorar { background:#fffbeb; border-left-color: var(--warning); }
.excluida { border-left-color: var(--gray); background:#f1f5f9; opacity: 0.7; font-style: italic; }
.feedback-item { background: #f8fafc; border-left-color: var(--indigo); }
.log-item { background: #f1f5f9; border-left-color: var(--gray); font-size: 13px; }

.respostas-gestao {
    margin-top: 10px;
    padding: 10px;
    background: #fff;
    border-radius: 8px;
    border: 1px solid #e2e8f0;
}

.btn-del-nota { position: absolute; top: 10px; right: 10px; color: var(--danger); cursor: pointer; font-size: 20px; }
.btn-restore-nota { position: absolute; top: 10px; right: 40px; color: var(--success); cursor: pointer; font-size: 20px; }

#login { width: 100%; max-width:400px; margin:auto; background: rgba(255, 255, 255, 0.85); padding:30px; border-radius:12px; text-align:center; box-shadow:0 15px 35px rgba(0,0,0,0.2); box-sizing: border-box; backdrop-filter: blur(10px); }

#adminGear { 
    position:fixed; 
    bottom:25px; 
    right:25px; 
    font-size:35px; 
    cursor:pointer; 
    display:none; 
    z-index:999; 
    background: var(--primary); 
    color: white;
    border-radius: 50%; 
    width: 60px;
    height: 60px;
    display: flex;
    align-items: center;
    justify-content: center;
    box-shadow: 0 4px 15px rgba(0,0,0,0.3); 
    border: 2px solid white;
}

footer { text-align:center; padding:20px; color:#111; font-size:13px; font-weight: bold; text-shadow: 0 1px 2px rgba(255,255,255,0.5); }

.grid-form { 
    display: grid; 
    grid-template-columns: repeat(auto-fill, minmax(280px, 1fr)); 
    gap: 15px; 
    margin-bottom: 10px;
}

hr { border: 0; border-top: 1px solid #ddd; margin: 30px 0; }
.btn-mini { width: auto; padding: 8px 15px; font-size: 13px; margin: 4px 2px; }

.actions-header { 
    display: flex; 
    gap: 10px; 
    flex-wrap: wrap; 
    margin-bottom: 20px; 
}

@media (max-width: 600px) {
    body { padding: 5px; }
    .container { padding: 15px; border-radius: 0; margin: 0; }
    h1 { font-size: 24px; }
    .grid-form { grid-template-columns: 1fr; }
    .actions-header button { width: 100%; }
}
</style>
</head>
<body>

<div id="login">
    <h2>🔐 Sistema Informa</h2>
    <input id="loginUsuario" placeholder="Usuário">
    <input id="loginSenha" type="password" placeholder="Senha">
    <button id="btnLogin">Entrar</button>
    <p id="erro" style="color:var(--danger); font-size:13px"></p>
</div>

<div id="adminGear" style="display:none" title="Configurações de Admin">⚙️</div>

<div class="container" id="sistema" style="display:none">
    <div style="display:flex; justify-content:space-between; align-items:center; flex-wrap: wrap; margin-bottom: 20px;">
        <h1 style="margin: 0;">Dashboard</h1>
        <button id="btnLogout" class="secondary" style="width:auto; margin-bottom: 0;">Sair</button>
    </div>
    
    <div class="actions-header">
        <button id="btnExcel" style="background:var(--success)">📊 Exportar Excel (Por Categorias)</button>
        <button onclick="window.location.href='https://clx-trem20.github.io/gerenciado-informa/'" style="background: #6366f1;">🌐 Gerenciador Externo</button>
        <button id="btnToggleForm" style="background: var(--indigo)">📋 Central de Formulários</button>
    </div>

    <div id="secaoFormularios" style="display:none; background:rgba(248, 250, 252, 0.9); padding:20px; border-radius:12px; border:1px solid #e2e8f0; margin-bottom:20px;">
        <h3>📋 Central de Formulários e Pesquisas</h3>
        <p style="color:var(--gray); font-size:14px;">Envie sugestões internas ou acesse os formulários oficiais abaixo.</p>
        
        <div class="grid-form">
            <div class="card">
                <h4>📩 Sugestão Interna</h4>
                <input id="formAssunto" placeholder="Assunto (ex: Infraestrutura, Eventos)">
                <textarea id="formMensagem" style="height:80px;" placeholder="Sua mensagem para a gestão..."></textarea>
                <button id="btnEnviarSugestao" class="success">Enviar para Gestores</button>
                <button id="btnVerRespostasSugestoes" class="secondary btn-mini" style="margin-top:10px">Ver Mensagens e Respostas</button>
            </div>
            
            <div class="card">
                <h4>🔗 Links Externos de Avaliação</h4>
                <div class="external-links-list">
                    <button onclick="window.open('https://forms.gle/EqHJSSPBqnHiJ5YbA', '_blank')" style="background:var(--primary)">⭐ Avaliação Equipe Gestora</button>
                    <button onclick="window.open('https://forms.gle/MSRYyYtL5QQmdjXV6', '_blank')" style="background:var(--primary)">📰 Avaliação do Jornal</button>
                    <button onclick="window.open('https://forms.gle/Lt5ekRsa1cweDDTV6', '_blank')" style="background:var(--primary)">📂 Avaliação Direção de Categoria</button>
                    <button onclick="window.open('https://forms.gle/XASGVVkvvH52kzRq5', '_blank')" style="background:var(--danger)">⚠️ Formulário de Denúncias</button>
                </div>
            </div>
        </div>

        <div id="listaSugestoesGestao" style="display:none; margin-top:20px; max-height: 500px; overflow-y: auto;">
            <h4>📥 Histórico de Sugestões e Respostas</h4>
            <div id="containerMensagens"></div>
        </div>
    </div>

    <div id="secaoCadastro" style="display:none">
        <hr>
        <h3>📝 Cadastro de Colaboradores</h3>
        <div class="grid-form">
            <input id="nome" placeholder="Nome Completo">
            <select id="categoria">
                <option value="">Selecione a Categoria</option>
                <option value="Meio Ambiente">Meio Ambiente</option>
                <option value="Linguagens">Linguagens</option>
                <option value="Comunicações">Comunicações</option>
                <option value="Edição de Vídeo">Edição de Vídeo</option>
                <option value="Cultura">Cultura</option>
                <option value="Secretaria">Secretaria</option>
                <option value="Esportes">Esportes</option>
                <option value="Presidência">Presidência</option>
                <option value="Informações">Informações</option>
                <option value="Designer">Designer</option>
            </select>
            <input id="matricula" placeholder="Matrícula">
            <input id="email" placeholder="E-mail">
            <input id="telefone" placeholder="Telefone Principal" maxlength="15">
            <input id="contato" placeholder="Número de Contato" maxlength="15">
            <input id="cpf" placeholder="CPF" maxlength="14">
            <input id="rg" placeholder="RG" maxlength="12">
            <input id="dataNascimento" type="date">
            <input id="anoEntrada" placeholder="Ano de Entrada">
        </div>
        <button id="btnSalvarPessoa">Salvar Colaborador</button>
    </div>

    <hr>
    <h3>✍️ Registrar Nota</h3>
    <div class="grid-form">
        <select id="pessoaNota"></select>
        <select id="tipoNota">
            <option value="elogio">Elogio</option>
            <option value="reclamacao">Reclamação</option>
            <option value="melhorar">A melhorar</option>
        </select>
    </div>
    <textarea id="nota" style="height: 100px;" placeholder="Escreva o feedback aqui..."></textarea>
    <button id="btnSalvarNota">Salvar Nota</button>

    <hr>
    <h3>🔍 Pesquisar</h3>
    <div class="grid-form">
        <input id="buscaNome" placeholder="Nome do colaborador">
        <select id="buscaCategoria">
            <option value="">Todas as Categorias</option>
            <option value="Meio Ambiente">Meio Ambiente</option>
            <option value="Linguagens">Linguagens</option>
            <option value="Comunicações">Comunicações</option>
            <option value="Edição de Vídeo">Edição de Vídeo</option>
            <option value="Cultura">Cultura</option>
            <option value="Secretaria">Secretaria</option>
            <option value="Esportes">Esportes</option>
            <option value="Presidência">Presidência</option>
            <option value="Informações">Informações</option>
            <option value="Designer">Designer</option>
        </select>
    </div>
    <button id="btnBuscar">Consultar</button>
    <div id="resultado"></div>

    <div id="secaoNotas" style="display:none; margin-top:20px; background: rgba(255, 255, 255, 0.95); padding: 15px; border: 1px solid #ddd; border-radius: 8px;">
        <h3 id="tituloNotas">Histórico</h3>
        <div id="listaNotas"></div>
        <div id="gavetaExcluidas" style="margin-top:20px; border-top: 1px dashed #ccc; padding-top:10px; display:none;">
            <h4 style="color:var(--gray)">📁 Gaveta de Notas Excluídas (Admin)</h4>
            <div id="listaExcluidas"></div>
        </div>
    </div>

    <hr>
    <div style="width: 100%; max-width:400px; margin:auto">
        <canvas id="grafico"></canvas>
    </div>
</div>

<div id="painelAdmin" class="container" style="display:none; border-top: 5px solid var(--primary)">
    <h2>⚙️ Gestão Administrativa</h2>
    
    <div id="gavetaUsuarios" style="background:rgba(248, 250, 252, 0.9); padding:20px; border-radius:12px; border:1px solid #e2e8f0; margin-bottom:20px;">
        <h3>👥 Gestão de Usuários e Acessos</h3>
        
        <div class="card">
            <h4>🆕 Criar Novo Usuário</h4>
            <div class="grid-form">
                <input id="novoUsuario" placeholder="Username">
                <input id="senhaUsuario" type="password" placeholder="Senha">
                <select id="nivelUsuario">
                    <option value="user">Usuário Comum</option>
                    <option value="gestao">Gestão</option>
                    <option value="presidencia">Presidência</option>
                    <option value="admin">Administrador</option>
                </select>
                <select id="categoriaUsuario">
                    <option value="">-- Categoria (Se for comum) --</option>
                    <option value="Meio Ambiente">Meio Ambiente</option>
                    <option value="Linguagens">Linguagens</option>
                    <option value="Comunicações">Comunicações</option>
                    <option value="Edição de Vídeo">Edição de Vídeo</option>
                    <option value="Cultura">Cultura</option>
                    <option value="Secretaria">Secretaria</option>
                    <option value="Esportes">Esportes</option>
                    <option value="Presidência">Presidência</option>
                    <option value="Informações">Informações</option>
                    <option value="Designer">Designer</option>
                </select>
            </div>
            <button id="btnAddUsuario">Adicionar Usuário ao Sistema</button>
        </div>

        <h4>Lista de Usuários Cadastrados</h4>
        <div id="listaUsuarios" style="display: grid; grid-template-columns: repeat(auto-fill, minmax(250px, 1fr)); gap: 10px;"></div>
    </div>

    <hr>
    <h4>🕵️ Registro de Acessos (Log)</h4>
    <button class="secondary btn-mini" id="btnCarregarLogs">Atualizar Logs de Acesso</button>
    <button class="danger btn-mini" id="btnLimparLogs">Limpar Logs</button>
    <div id="containerLogs" style="max-height: 300px; overflow-y: auto; margin-top: 10px; background: rgba(241, 245, 249, 0.9); padding: 10px; border-radius: 8px;">
        <p style="font-size: 12px; color: var(--gray);">Clique em atualizar para ver quem acessou o sistema.</p>
    </div>

    <hr>
    <h4>🗑️ Lixeira de Itens Excluídos</h4>
    <button class="danger btn-mini" id="btnLimparLixeira">Esvaziar Lixeira Permanentemente</button>
    <div id="listaLixeira" style="font-size: 11px; margin-top:10px;"></div>
</div>

<footer>© 2025 – Sistema Informa – Criado por <b>CLX</b></footer>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
import { getFirestore, collection, addDoc, getDocs, updateDoc, deleteDoc, doc, query, where, orderBy, limit } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";

const firebaseConfig = {
    apiKey: "AIzaSyCtJytArZciWTcAaVI--bY7mSiFVE-K6Zw",
    authDomain: "informa-a8d4d.firebaseapp.com",
    projectId: "informa-a8d4d",
    storageBucket: "informa-a8d4d.firebasestorage.app",
    messagingSenderId: "201808467376",
    appId: "1:201808467376:web:bb06f0fd7e57dfa747b275"
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

let usuarios = [], usuarioLogado = null, pessoas = [], pessoaEditando = null, chart = null;
let el = {};

window.addEventListener('DOMContentLoaded', async () => {
    const IDs = ['login','sistema','adminGear','painelAdmin','erro','loginUsuario','loginSenha','btnLogin','btnLogout','btnSalvarPessoa','btnSalvarNota','btnBuscar','btnAddUsuario','listaUsuarios','nome','categoria','anoEntrada','matricula','email','telefone','contato','cpf','rg','dataNascimento','pessoaNota','tipoNota','nota','buscaNome','buscaCategoria','resultado','grafico','listaNotas','secaoNotas','novoUsuario','senhaUsuario','nivelUsuario','categoriaUsuario','listaLixeira','btnLimparLixeira','btnExcel','gavetaExcluidas','listaExcluidas','secaoCadastro',
    'btnToggleForm','secaoFormularios','btnEnviarSugestao','formAssunto','formMensagem','btnVerRespostasSugestoes','listaSugestoesGestao','containerMensagens',
    'btnCarregarLogs', 'btnLimparLogs', 'containerLogs'];
    
    IDs.forEach(id => el[id] = document.getElementById(id));

    if(el.btnLogin) el.btnLogin.onclick = login;
    if(el.btnLogout) el.btnLogout.onclick = logout;
    if(el.btnSalvarPessoa) el.btnSalvarPessoa.onclick = salvarPessoa;
    if(el.btnSalvarNota) el.btnSalvarNota.onclick = salvarNota;
    if(el.btnBuscar) el.btnBuscar.onclick = buscar;
    if(el.btnAddUsuario) el.btnAddUsuario.onclick = addUsuario;
    if(el.btnLimparLixeira) el.btnLimparLixeira.onclick = limparLixeira;
    if(el.btnExcel) el.btnExcel.onclick = exportarExcel;
    if(el.btnCarregarLogs) el.btnCarregarLogs.onclick = carregarLogs;
    if(el.btnLimparLogs) el.btnLimparLogs.onclick = limparLogs;

    if(el.btnToggleForm) el.btnToggleForm.onclick = () => {
        el.secaoFormularios.style.display = el.secaoFormularios.style.display === 'none' ? 'block' : 'none';
    };
    if(el.btnEnviarSugestao) el.btnEnviarSugestao.onclick = enviarSugestao;
    if(el.btnVerRespostasSugestoes) el.btnVerRespostasSugestoes.onclick = carregarSugestoes;
    
    if(el.adminGear) {
        el.adminGear.onclick = () => {
            el.painelAdmin.style.display = el.painelAdmin.style.display==='none' || el.painelAdmin.style.display==='' ? 'block' : 'none';
            if(el.painelAdmin.style.display === 'block') {
                el.painelAdmin.scrollIntoView({behavior: 'smooth'});
                carregarLogs();
            }
        };
    }

    const maskTel = (e) => { let v = e.target.value.replace(/\D/g,""); v = v.replace(/^(\d{2})(\d)/g,"($1) $2"); v = v.replace(/(\d)(\d{4})$/,"$1-$2"); e.target.value = v; };
    if(el.cpf) el.cpf.oninput = (e) => e.target.value = e.target.value.replace(/\D/g,"").replace(/(\d{3})(\d)/,"$1.$2").replace(/(\d{3})(\d)/,"$1.$2").replace(/(\d{3})(\d{1,2})$/,"$1-$2");
    if(el.rg) el.rg.oninput = (e) => e.target.value = e.target.value.replace(/\D/g,"").replace(/(\d{2})(\d)/,"$1.$2").replace(/(\d{3})(\d)/,"$1.$2").replace(/(\d{3})(\d{1,2})$/,"$1-$2");
    if(el.telefone) el.telefone.oninput = maskTel;
    if(el.contato) el.contato.oninput = maskTel;

    await carregarUsuarios();
});

async function registrarAcesso(usuario) {
    try {
        await addDoc(collection(db, 'acessos'), {
            usuario: usuario.usuario,
            nivel: usuario.nivel,
            data: new Date().toLocaleString('pt-BR'),
            timestamp: Date.now()
        });
    } catch(e) { console.error("Erro ao registrar log"); }
}

async function carregarLogs() {
    if(usuarioLogado.nivel !== 'admin') return;
    el.containerLogs.innerHTML = "Carregando históricos...";
    const s = await getDocs(collection(db, 'acessos'));
    const logs = s.docs.map(d => ({id: d.id, ...d.data()})).sort((a,b) => b.timestamp - a.timestamp);
    
    el.containerLogs.innerHTML = "";
    if(logs.length === 0) el.containerLogs.innerHTML = "Nenhum acesso registrado.";
    
    logs.slice(0, 50).forEach(l => {
        el.containerLogs.innerHTML += `
            <div class="log-item">
                👤 <b>${l.usuario}</b> (${l.nivel}) acessou em 📅 ${l.data}
            </div>
        `;
    });
}

async function limparLogs() {
    if(!confirm("Deseja apagar todo o histórico de acessos?")) return;
    const s = await getDocs(collection(db, 'acessos'));
    for (const d of s.docs) { await deleteDoc(doc(db, 'acessos', d.id)); }
    carregarLogs();
}

async function enviarSugestao() {
    if(!el.formAssunto.value || !el.formMensagem.value) return alert("Por favor, preencha o assunto e a mensagem.");
    
    const dados = {
        assunto: el.formAssunto.value,
        mensagem: el.formMensagem.value,
        autor: usuarioLogado.usuario,
        categoriaAutor: usuarioLogado.categoria || "N/A",
        data: new Date().toLocaleString('pt-BR'),
        timestamp: Date.now(),
        respostas: [] 
    };

    try {
        await addDoc(collection(db, 'sugestoes'), dados);
        alert("Sugestão enviada com sucesso aos gestores!");
        el.formAssunto.value = "";
        el.formMensagem.value = "";
    } catch (e) {
        alert("Erro ao enviar sugestão.");
    }
}

async function carregarSugestoes() {
    el.listaSugestoesGestao.style.display = 'block';
    el.containerMensagens.innerHTML = "Carregando mensagens...";
    
    const s = await getDocs(collection(db, 'sugestoes'));
    const lista = s.docs.map(d => ({id: d.id, ...d.data()})).sort((a,b) => b.timestamp - a.timestamp);
    
    el.containerMensagens.innerHTML = "";
    if(lista.length === 0) el.containerMensagens.innerHTML = "<p>Nenhuma mensagem recebida ainda.</p>";
    
    lista.forEach(msg => {
        const temPermissaoGeral = (usuarioLogado.nivel === 'admin' || usuarioLogado.nivel === 'presidencia' || usuarioLogado.nivel === 'gestao');
        
        if(temPermissaoGeral || msg.autor === usuarioLogado.usuario) {
            
            let htmlRespostas = "";
            if(msg.respostas && msg.respostas.length > 0) {
                htmlRespostas = `<div class="respostas-gestao"><strong>📢 Respostas da Gestão:</strong><br>`;
                msg.respostas.forEach(r => {
                    htmlRespostas += `<div style="margin-top:5px; border-bottom:1px solid #eee; padding-bottom:5px;">
                        <i>"${r.texto}"</i> — <b>${r.autor}</b> <small>(${r.data})</small>
                    </div>`;
                });
                htmlRespostas += `</div>`;
            }

            let campoResposta = "";
            if(temPermissaoGeral) {
                campoResposta = `
                    <div style="margin-top:10px; display:flex; gap:5px;">
                        <input id="resp_${msg.id}" placeholder="Escreva uma resposta..." style="margin-bottom:0; font-size:13px; height:35px;">
                        <button class="success btn-mini" onclick="window.responderSugestao('${msg.id}')" style="margin:0;">Responder</button>
                    </div>
                `;
            }

            el.containerMensagens.innerHTML += `
                <div class="feedback-item">
                    <div style="display:flex; justify-content:space-between;">
                        <strong>Assunto: ${msg.assunto}</strong>
                        ${usuarioLogado.nivel === 'admin' ? `<button class="danger btn-mini" onclick="window.excluirSugestao('${msg.id}')" style="padding:2px 8px;">×</button>` : ''}
                    </div>
                    <p style="margin:5px 0;">${msg.mensagem}</p>
                    <small>Enviado por: ${msg.autor} (${msg.categoriaAutor}) em ${msg.data}</small>
                    ${htmlRespostas}
                    ${campoResposta}
                </div>
            `;
        }
    });
}

window.responderSugestao = async (id) => {
    const input = document.getElementById(`resp_${id}`);
    const texto = input.value;
    if(!texto) return;

    try {
        const s = await getDocs(collection(db, 'sugestoes'));
        const docRef = doc(db, 'sugestoes', id);
        const msg = s.docs.find(d => d.id === id).data();
        
        const novasRespostas = msg.respostas || [];
        novasRespostas.push({
            texto: texto,
            autor: usuarioLogado.usuario,
            data: new Date().toLocaleString('pt-BR'),
            timestamp: Date.now()
        });

        await updateDoc(docRef, { respostas: novasRespostas });
        alert("Resposta enviada!");
        carregarSugestoes();
    } catch (e) {
        alert("Erro ao salvar resposta.");
    }
}

window.excluirSugestao = async (id) => {
    if(confirm("Deseja apagar esta mensagem permanentemente?")) {
        await deleteDoc(doc(db, 'sugestoes', id));
        carregarSugestoes();
    }
}

async function login(){
    try {
        const s = await getDocs(collection(db, 'usuarios'));
        usuarios = s.docs.map(d => ({id: d.id, ...d.data()}));
        const userVal = el.loginUsuario.value;
        const passVal = el.loginSenha.value;
        
        const u = usuarios.find(u => u.usuario === userVal && u.senha === passVal);
        
        if(!u) {
            el.erro.innerText = "Falha no login: Usuário ou senha incorretos.";
            return;
        }
        if(!u.ativo) {
            el.erro.innerText = "Acesso bloqueado por um administrador.";
            return;
        }
        
        localStorage.setItem('sessao_informa', JSON.stringify(u));
        await registrarAcesso(u);
        entrarNoSistema(u);
    } catch (err) {
        el.erro.innerText = "Erro ao conectar com o banco de dados.";
    }
}

function entrarNoSistema(u) {
    usuarioLogado = u;
    el.login.style.display = 'none';
    el.sistema.style.display = 'block';
    
    el.adminGear.style.display = 'none';

    if(u.nivel === 'admin'){
        el.adminGear.style.display = 'flex';
        el.secaoCadastro.style.display = 'block';
        carregarLixeira();
    } else {
        el.secaoCadastro.style.display = 'none';
        
        if(u.nivel === 'presidencia' || u.nivel === 'gestao') {
            if(el.buscaCategoria) { 
                el.buscaCategoria.disabled = false; 
                el.buscaCategoria.value = ""; 
            }
        } else {
            if(el.buscaCategoria) { 
                el.buscaCategoria.value = u.categoria; 
                el.buscaCategoria.disabled = true; 
            }
        }
    }
    carregarPessoas();
}

function logout() { 
    localStorage.removeItem('sessao_informa'); 
    location.reload(); 
}

async function carregarUsuarios(){
    const s = await getDocs(collection(db, 'usuarios'));
    usuarios = s.docs.map(d => ({id: d.id, ...d.data()}));
    if(el.listaUsuarios){
        el.listaUsuarios.innerHTML = "";
        usuarios.forEach(u => {
            el.listaUsuarios.innerHTML += `
                <div class="card ${u.ativo ? '' : 'bloqueado'}">
                    <b>${u.usuario}</b> (${u.nivel})<br>
                    <button class="btn-mini success" onclick="window.resetarSenha('${u.id}', '${u.usuario}')">Reset Senha</button>
                    <button class="btn-mini secondary" onclick="window.toggleUser('${u.id}', ${u.ativo})">${u.ativo ? 'Bloquear' : 'Ativar'}</button>
                    ${u.usuario !== 'CLX' ? `<button class="danger btn-mini" onclick="window.excluirUsuario('${u.id}')">Excluir</button>` : ''}
                </div>`;
        });
    }
}

window.resetarSenha = async (id, nome) => {
    const nS = prompt(`Nova senha para ${nome}:`);
    if(nS) { await updateDoc(doc(db, 'usuarios', id), { senha: nS }); carregarUsuarios(); }
};

window.toggleUser = async (id, stat) => { 
    await updateDoc(doc(db, 'usuarios', id), { ativo: !stat }); 
    carregarUsuarios(); 
};

window.excluirUsuario = async (id) => { 
    if(confirm("Excluir acesso permanentemente?")) { 
        await deleteDoc(doc(db, 'usuarios', id)); 
        carregarUsuarios(); 
    } 
};

async function carregarPessoas(){
    const s = await getDocs(collection(db, 'pessoas'));
    const listaGeralPessoas = s.docs.map(d => ({id: d.id, ...d.data()}));
    pessoas = listaGeralPessoas;

    if(el.pessoaNota){
        el.pessoaNota.innerHTML = '<option value="">Selecione um colaborador...</option>';
        pessoas.forEach((p, i) => {
            if(usuarioLogado.nivel === 'admin' || usuarioLogado.nivel === 'presidencia' || usuarioLogado.nivel === 'gestao' || p.categoria === usuarioLogado.categoria)
                el.pessoaNota.add(new Option(p.nome, i));
        });
    }
    atualizarGrafico();
}

async function salvarPessoa(){
    const d = { 
        nome: el.nome.value, 
        categoria: el.categoria.value, 
        matricula: el.matricula.value, 
        email: el.email.value, 
        telefone: el.telefone.value, 
        contato: el.contato.value, 
        cpf: el.cpf.value, 
        rg: el.rg.value, 
        dataNascimento: el.dataNascimento.value, 
        anoEntrada: el.anoEntrada.value, 
        notas: pessoaEditando ? (pessoaEditando.notas || []) : [], 
        notasExcluidas: pessoaEditando ? (pessoaEditando.notasExcluidas || []) : [] 
    };
    
    if(pessoaEditando) await updateDoc(doc(db, 'pessoas', pessoaEditando.id), d);
    else await addDoc(collection(db, 'pessoas'), d);
    
    alert("Colaborador salvo com sucesso!");
    ['nome','matricula','email','telefone','contato','cpf','rg','dataNascimento','anoEntrada'].forEach(f => el[f].value = "");
    pessoaEditando = null; 
    carregarPessoas();
}

async function salvarNota(){
    const pIdx = el.pessoaNota.value;
    if(pIdx === "") return alert("Selecione um colaborador");
    
    const p = pessoas[pIdx];
    const n = { 
        tipo: el.tipoNota.value, 
        texto: el.nota.value, 
        autor: usuarioLogado.usuario, 
        data: new Date().toLocaleDateString('pt-BR') 
    };
    
    p.notas = p.notas || []; 
    p.notas.push(n);
    
    await updateDoc(doc(db, 'pessoas', p.id), { notas: p.notas });
    el.nota.value = ""; 
    alert("Nota registrada!"); 
    atualizarGrafico();
    if(el.secaoNotas.style.display === 'block') window.verNotas(pIdx);
}

window.apagarNota = async (pIdx, nIdx) => {
    if(!confirm("Mover esta nota para a gaveta de excluídas?")) return;
    const p = pessoas[pIdx];
    const nRem = p.notas.splice(nIdx, 1)[0];
    p.notasExcluidas = p.notasExcluidas || []; 
    p.notasExcluidas.push(nRem);
    
    await updateDoc(doc(db, 'pessoas', p.id), { notas: p.notas, notasExcluidas: p.notasExcluidas });
    window.verNotas(pIdx); 
    atualizarGrafico();
};

window.restaurarNota = async (pIdx, nIdx) => {
    const p = pessoas[pIdx];
    const nRes = p.notasExcluidas.splice(nIdx, 1)[0];
    p.notas.push(nRes);
    
    await updateDoc(doc(db, 'pessoas', p.id), { notas: p.notas, notasExcluidas: p.notasExcluidas });
    window.verNotas(pIdx); 
    atualizarGrafico();
};

window.verNotas = function(idx){
    const p = pessoas[idx]; 
    el.secaoNotas.style.display = 'block'; 
    el.listaNotas.innerHTML = "";
    
    p.notas?.forEach((n, ni) => {
        const btn = usuarioLogado.nivel === 'admin' ? `<span class="btn-del-nota" onclick="window.apagarNota(${idx}, ${ni})">🗑️</span>` : '';
        el.listaNotas.innerHTML += `
            <div class="${n.tipo}">
                ${btn}
                <strong>${n.tipo.toUpperCase()}</strong>: ${n.texto}
                <br><small>${n.data} por ${n.autor}</small>
            </div>`;
    });
    
    if(usuarioLogado.nivel === 'admin' && p.notasExcluidas?.length > 0) {
        el.gavetaExcluidas.style.display = 'block'; 
        el.listaExcluidas.innerHTML = "";
        p.notasExcluidas.forEach((n, ni) => { 
            el.listaExcluidas.innerHTML += `
                <div class="excluida">
                    <span class="btn-restore-nota" onclick="window.restaurarNota(${idx}, ${ni})">🔄</span>
                    ${n.texto}
                </div>`; 
        });
    } else { 
        el.gavetaExcluidas.style.display = 'none'; 
    }
    el.secaoNotas.scrollIntoView({behavior: 'smooth'});
}

window.buscar = function(){
    el.resultado.innerHTML = ""; 
    el.secaoNotas.style.display = 'none';
    
    const buscaN = el.buscaNome.value.toLowerCase();
    const buscaC = el.buscaCategoria.value;
    
    const filt = pessoas.filter(p => {
        const nMatch = p.nome.toLowerCase().includes(buscaN);
        const cMatch = buscaC === "" || p.categoria === buscaC;
        if(usuarioLogado.nivel === 'admin' || usuarioLogado.nivel === 'presidencia' || usuarioLogado.nivel === 'gestao') return nMatch && cMatch;
        return p.categoria === usuarioLogado.categoria && nMatch;
    });
    
    filt.forEach(p => {
        const idx = pessoas.indexOf(p);
        el.resultado.innerHTML += `
            <div class="card">
                <b>${p.nome}</b> (${p.categoria})<br>
                <button class="btn-mini" onclick="window.verNotas(${idx})">Ver Histórico</button>
                ${usuarioLogado.nivel==='admin' ? `
                    <button class="btn-mini secondary" onclick="window.editarPessoa(${idx})">Editar</button> 
                    <button class="btn-mini danger" onclick="window.excluirPessoa('${p.id}')">Excluir</button>
                ` : ''}
            </div>`;
    });
}

window.excluirPessoa = async (id) => {
    if(confirm("Mover colaborador para a lixeira?")){
        const pIdx = pessoas.findIndex(x => x.id === id);
        if(pIdx === -1) return;
        
        await addDoc(collection(db, 'lixeira'), { 
            dados: pessoas[pIdx], 
            data: new Date().toLocaleString() 
        });
        await deleteDoc(doc(db, 'pessoas', id)); 
        carregarPessoas(); 
        window.buscar();
    }
};

window.editarPessoa = (idx) => {
    const p = pessoas[idx]; 
    pessoaEditando = p;
    ['nome','categoria','matricula','email','telefone','contato','cpf','rg','dataNascimento','anoEntrada'].forEach(f => {
        el[f].value = p[f] || "";
    });
    window.scrollTo({top: 0, behavior: 'smooth'});
};

async function carregarLixeira(){
    const s = await getDocs(collection(db, 'lixeira'));
    if(el.listaLixeira){
        el.listaLixeira.innerHTML = "<b>Itens na lixeira:</b>";
        s.forEach(d => {
            const item = d.data();
            el.listaLixeira.innerHTML += `<div>• ${item.dados?.nome || 'Sem nome'} (${item.data})</div>`;
        });
    }
}

async function limparLixeira(){
    if(!confirm("Esvaziar lixeira permanentemente?")) return;
    const s = await getDocs(collection(db, 'lixeira'));
    for (const d of s.docs) {
        await deleteDoc(doc(db, 'lixeira', d.id));
    }
    carregarLixeira();
}

async function addUsuario(){
    if(!el.novoUsuario.value || !el.senhaUsuario.value) return alert("Preencha utilizador e senha");
    
    await addDoc(collection(db, 'usuarios'), { 
        usuario: el.novoUsuario.value, 
        senha: el.senhaUsuario.value, 
        nivel: el.nivelUsuario.value, 
        categoria: el.categoriaUsuario.value, 
        ativo: true 
    });
    
    el.novoUsuario.value = ""; 
    el.senhaUsuario.value = ""; 
    carregarUsuarios();
}

function atualizarGrafico(){
    let e=0, r=0, m=0;
    const ctx = el.grafico.getContext('2d');
    pessoas.forEach(p => {
        if(usuarioLogado.nivel === 'admin' || usuarioLogado.nivel === 'presidencia' || usuarioLogado.nivel === 'gestao' || (usuarioLogado.categoria && p.categoria === usuarioLogado.categoria)) {
            p.notas?.forEach(n => { 
                if(n.tipo==='elogio') e++; 
                else if(n.tipo==='reclamacao') r++; 
                else m++; 
            });
        }
    });
    
    if(chart) chart.destroy();
    chart = new Chart(ctx, { 
        type: 'pie', 
        data: { 
            labels: ['Elogios', 'Reclamações', 'Melhorar'], 
            datasets: [{ 
                data: [e,r,m], 
                backgroundColor: ['#10b981','#ef4444','#f59e0b'] 
            }] 
        }, 
        options: { 
            responsive: true, 
            maintainAspectRatio: true,
            plugins: {
                legend: { position: 'bottom' }
            }
        } 
    });
}

function exportarExcel() {
    const wb = XLSX.utils.book_new();
    const categoriasUnicas = [...new Set(pessoas.map(p => p.categoria))].filter(c => c);

    categoriasUnicas.forEach(cat => {
        const pessoasDaCategoria = pessoas.filter(p => p.categoria === cat);
        const data = pessoasDaCategoria.map(p => ({
            "Nome": p.nome,
            "Matrícula": p.matricula,
            "CPF": p.cpf,
            "RG": p.rg,
            "E-mail": p.email,
            "Telefone": p.telefone,
            "Elogios": p.notas?.filter(n => n.tipo === 'elogio').length || 0,
            "Reclamações": p.notas?.filter(n => n.tipo === 'reclamacao').length || 0,
            "A Melhorar": p.notas?.filter(n => n.tipo === 'melhorar').length || 0
        }));
        
        if(data.length > 0) {
            const ws = XLSX.utils.json_to_sheet(data);
            XLSX.utils.book_append_sheet(wb, ws, cat.substring(0, 31));
        }
    });

    if (wb.SheetNames.length === 0) {
        alert("Sem dados para exportar.");
        return;
    }

    XLSX.writeFile(wb, "Relatorio_Informa_Completo.xlsx");
}
</script>
</body>
</html>
