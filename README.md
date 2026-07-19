<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Servtec Gestão - v47</title>
    <!-- Biblioteca para gerar PDF direto no navegador -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
    <style>
        :root {
            --primary: #2c3e50; --success: #27ae60; --warning: #f39c12;
            --danger: #e74c3c; --info: #3498db; --light: #f1f2f6; --white: #ffffff;
            --dark: #2d3436;
        }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body { font-family: 'Segoe UI', sans-serif; background-color: #ced6e0; margin: 0; padding: 10px; display: flex; justify-content: center; align-items: center; min-height: 100vh; }
        .container { width: 100%; max-width: 1200px; background: var(--white); padding: 20px; border-radius: 15px; box-shadow: 0 10px 30px rgba(0,0,0,0.15); align-self: flex-start; }
        
        /* TELA DE LOGIN */
        .login-box { max-width: 400px; margin: 80px auto; background: var(--white); padding: 30px; border-radius: 12px; box-shadow: 0 5px 15px rgba(0,0,0,0.2); text-align: center; }
        .login-box h2 { color: var(--primary); margin-bottom: 20px; }
        .login-box input { margin-bottom: 15px; }

        h1 { text-align: center; color: var(--primary); margin-bottom: 20px; }
        .grid-menu { display: grid; grid-template-columns: repeat(auto-fit, minmax(130px, 1fr)); gap: 10px; margin-bottom: 25px; }
        .card-btn { padding: 15px 10px; border-radius: 12px; border: none; color: white; font-weight: bold; cursor: pointer; display: flex; flex-direction: column; align-items: center; gap: 8px; font-size: 0.85rem; }
        
        .tela { display: none; animation: fadeIn 0.2s ease; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        
        .btn-voltar { background: #95a5a6; color: white; border: none; padding: 5px 15px; border-radius: 5px; cursor: pointer; font-size: 0.8rem; font-weight: bold; }
        .header-tela { display: flex; justify-content: space-between; align-items: center; border-bottom: 2px solid var(--light); margin-bottom: 20px; padding-bottom: 10px; }

        .form-box { background: var(--light); padding: 20px; border-radius: 10px; display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }
        .info-resumo { background: #dfe6e9; padding: 15px; border-radius: 10px; margin-bottom: 15px; grid-column: 1 / -1; border-left: 5px solid var(--info); }
        .full-width { grid-column: 1 / -1; }
        
        /* ÁREA DO PDF REMODELADA - SEM BORDAS FALSAS OU LIMITES DE CAIXA */
        #pdf-area { width: 100%; display: flex; justify-content: center; align-items: flex-start; background: #95a5a6; padding: 10px; border-radius: 8px; }
        .folha-os { background: white; border: none; padding: 20px; width: 100%; line-height: 1.6; color: #000; box-shadow: none; display: block; }
        
        .topo-os { display: flex; justify-content: space-between; align-items: center; margin-bottom: 25px; border-bottom: 2px solid #000; padding-bottom: 15px; }
        .topo-os h2 { margin: 0; font-size: 1.65rem; text-transform: uppercase; color: #000; letter-spacing: 1px; }
        
        .faixa-os { background: #333333; color: white; padding: 8px 12px; font-weight: bold; font-size: 1.05rem; margin-top: 20px; margin-bottom: 12px; text-transform: uppercase; letter-spacing: 0.5px; }
        
        .conteudo-os { padding: 2px 8px; font-size: 1.05rem; }
        .conteudo-os p { margin: 6px 0; }
        
        .bloco-info { background: white; border: 1px solid #ddd; padding: 15px; border-radius: 8px; margin-bottom: 15px; }
        .bloco-info h3 { margin-top: 0; border-bottom: 2px solid var(--primary); padding-bottom: 5px; color: var(--primary); font-size: 1rem; }
        
        input, textarea, select { padding: 12px; border: 1px solid #bdc3c7; border-radius: 8px; font-size: 16px; width: 100%; outline: none; background: white; }
        .btn-acao { width: 100%; padding: 14px; border: none; border-radius: 8px; color: white; font-weight: bold; cursor: pointer; font-size: 0.95rem; margin-top: 10px; }
        
        .busca-resultados { background: var(--white); border: 1px solid #ccc; border-top: none; max-height: 200px; overflow-y: auto; position: absolute; width: calc(100% - 20px); z-index: 10; border-radius: 0 0 8px 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.1); display: none;}
        .busca-item { padding: 10px; cursor: pointer; border-bottom: 1px solid #eee; }
        .busca-item:hover { background: #f1f2f6; }
        .busca-container { position: relative; width: 100%; margin-bottom: 20px; }

        .table-container { overflow-x: auto; margin-top: 10px; }
        table { width: 100%; border-collapse: collapse; min-width: 900px; }
        th { background: var(--primary); color: white; padding: 12px; text-align: left; }
        td { padding: 12px; border-bottom: 1px solid #eee; }
        .linha-clicavel { cursor: pointer; }
        .linha-clicavel:hover { background: #f8f9fa; }

        .status-container { display: flex; align-items: center; gap: 8px; }
        .status-dot { width: 12px; height: 12px; border-radius: 50%; display: inline-block; }
        .dot-pendente { background-color: #f1c40f; box-shadow: 0 0 8px #f1c40f; }
        .dot-aprovado { background-color: #2ecc71; box-shadow: 0 0 8px #2ecc71; }
        .dot-reprovado { background-color: #e74c3c; box-shadow: 0 0 8px #e74c3c; }
        .dot-entregue { background-color: #27ae60; box-shadow: 0 0 8px #27ae60; }
        .dot-cancelado { background-color: #7f8c8d; box-shadow: 0 0 8px #7f8c8d; }

        .status-badge { padding: 4px 10px; border-radius: 20px; font-weight: bold; font-size: 0.75rem; text-transform: uppercase; }
        .st-pendente { background: #dff9fb; color: #0984e3; }
        .st-aprovado { background: #e3faf2; color: #00b894; }
        .st-reprovado { background: #ffeaa7; color: #d35400; }
        .st-entregue { background: #e5f9e0; color: #27ae60; }
        .st-cancelado { background: #fab1a0; color: #e17055; }
        .st-sem-conserto { background: #dfe6e9; color: #636e72; }

        .card-fin { background: white; padding: 15px; border-radius: 10px; border-left: 5px solid var(--success); box-shadow: 0 2px 5px rgba(0,0,0,0.1); }
        .card-fin h4 { margin: 0; font-size: 0.8rem; color: #666; }
        .card-fin p { margin: 5px 0 0; font-size: 1.2rem; font-weight: bold; color: var(--primary); }

        .vendas-grid { display: grid; grid-template-columns: 1fr 2fr; gap: 20px; }
        @media (max-width: 800px) { .vendas-grid { grid-template-columns: 1fr; } .form-box { grid-template-columns: 1fr; } }
    </style>
</head>
<body>

<!-- BLOC DE LOGIN INICIAL -->
<div id="tela-login" class="login-box">
    <h2>Servtec Gestão</h2>
    <input type="text" id="login-usuario" placeholder="Usuário">
    <input type="password" id="login-senha" placeholder="Senha">
    <button class="btn-acao" style="background:var(--primary); margin:0;" onclick="efetuarLogin()">Entrar</button>
</div>

<div class="container" id="painel-sistema" style="display: none;">
    <!-- MENU PRINCIPAL -->
    <div id="menu-inicial" class="tela" style="display: block;">
        <div style="display:flex; justify-content: space-between; align-items:center; margin-bottom: 15px;">
            <h1 style="margin:0;">Servtec Eletrônica</h1>
            <button class="btn-voltar" style="background:var(--danger);" onclick="logout()">Sair 🚪</button>
        </div>
        
        <div class="grid-menu">
            <button class="card-btn" style="background:var(--primary)" onclick="mudarTela('tela-entrada')">📥 ENTRADA</button>
            <button class="card-btn" style="background:var(--info)" onclick="mudarTela('tela-orcamento')">💰 ORÇAMENTO</button>
            <button class="card-btn" style="background:var(--warning)" onclick="mudarTela('tela-saida')">📤 FINALIZAR</button>
            <button class="card-btn" id="menu-btn-financeiro" style="background:#6c5ce7; display:none;" onclick="mudarTela('tela-financeiro')">📊 FINANCEIRO</button>
            <button class="card-btn" style="background:#00b894" onclick="mudarTela('tela-vendas')">🛒 ESTOQUE/VENDAS</button>
        </div>
        
        <div style="margin-bottom: 15px;">
            <input type="text" id="busca-geral-input" placeholder="Pesquisar OS, Cliente ou Aparelho..." oninput="filtrarTabelaPrincipal()">
        </div>
        
        <div class="table-container" id="lista-geral"></div>
    </div>

    <!-- TELA ENTRADA -->
    <div id="tela-entrada" class="tela">
        <div class="header-tela"><h2>Nova Entrada</h2><button class="btn-voltar" onclick="mudarTela('menu-inicial')">Voltar</button></div>
        <div class="form-box">
            <div class="campo-grupo"><label>Nome do Cliente</label><input type="text" id="ent-cliente"></div>
            <div class="campo-grupo"><label>WhatsApp</label><input type="text" id="ent-contato" placeholder="(00) 00000-0000"></div>
            <div class="campo-grupo"><label>Tipo Equipamento</label><input type="text" id="ent-tipo" placeholder="Ex: TV, Caixa de Som"></div>
            <div class="campo-grupo"><label>Nº de Série</label><input type="text" id="ent-serie"></div>
            <div class="campo-grupo"><label>Marca</label><input type="text" id="ent-marca"></div>
            <div class="campo-grupo"><label>Modelo</label><input type="text" id="ent-modelo"></div>
            <div class="campo-grupo full-width"><label>Acessórios Inclusos</label><input type="text" id="ent-acessorios"></div>
            <div class="campo-grupo full-width"><label>Defeito Reclamado</label><textarea id="ent-defeito" rows="2"></textarea></div>
            <button class="btn-acao full-width" style="background:var(--success)" onclick="salvarEntrada()">CRIAR OS</button>
        </div>
    </div>

    <!-- TELA VISUALIZAR OS (FOLHA IMPRESSÃO / PDF) -->
    <div id="tela-detalhes" class="tela">
        <div class="header-tela">
            <h2>Impressão / Envio de OS</h2>
            <div>
                <button class="btn-voltar" style="background:#2980b9;" id="btn-mudar-modelo">Mudar para Orçamento 🔁</button>
                <button class="btn-voltar" onclick="mudarTela('menu-inicial')">Voltar</button>
            </div>
        </div>
        
        <div id="pdf-area">
            <div id="folha-os" class="folha-os"></div>
        </div>
        
        <div style="max-width: 800px; margin: 20px auto; display: flex; flex-direction: column; gap: 10px;">
            <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 10px;">
                <button class="btn-acao" style="background:var(--success); margin:0;" id="btn-aprov-os">Orçamento Aprovado ✅</button>
                <button class="btn-acao" style="background:var(--danger); margin:0;" id="btn-reprov-os">Orçamento Reprovado ❌</button>
            </div>
            
            <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 10px;">
                <button class="btn-acao" style="background:#0984e3; margin:0;" id="btn-pdf-os">GERAR PDF PARA IMPRIMIR 📄</button>
                <button class="btn-acao" style="background:#25d366; margin:0;" id="btn-zap-os">ENVIAR NO WHATSAPP 💬</button>
            </div>
        </div>
    </div>

    <!-- TELA ORÇAMENTO -->
    <div id="tela-orcamento" class="tela">
        <div class="header-tela"><h2>Orçamento Técnico</h2><button class="btn-voltar" onclick="mudarTela('menu-inicial')">Voltar</button></div>
        <div class="busca-container">
            <input type="text" id="busca-orc-input" placeholder="Pesquisar por OS, Nome de Cliente ou Aparelho para Orçamento..." oninput="filtrarPesquisaInterna('busca-orc-input', 'busca-orc-resultados', 'orc')">
            <div id="busca-orc-resultados" class="busca-resultados"></div>
        </div>
        <div id="area-orcamento"></div>
    </div>

    <!-- TELA FINALIZAR -->
    <div id="tela-saida" class="tela">
        <div class="header-tela"><h2>Finalizar Entrega</h2><button class="btn-voltar" onclick="mudarTela('menu-inicial')">Voltar</button></div>
        <div class="busca-container">
            <input type="text" id="busca-fin-input" placeholder="Pesquisar por OS, Nome de Cliente ou Aparelho para Entrega..." oninput="filtrarPesquisaInterna('busca-fin-input', 'busca-fin-resultados', 'fin')">
            <div id="busca-fin-resultados" class="busca-resultados"></div>
        </div>
        <div id="area-finalizar"></div>
    </div>

    <!-- TELA FINANCEIRO -->
    <div id="tela-financeiro" class="tela">
        <div class="header-tela"><h2>Financeiro</h2><button class="btn-voltar" onclick="mudarTela('menu-inicial')">Voltar</button></div>
        <div id="financeiro-dashboard"></div>
        
        <h3 style="margin-top:25px; color:var(--primary);">📋 Histórico de Equipamentos Entregues</h3>
        <div class="table-container" id="historico-equipamentos"></div>
        
        <h3 style="margin-top:25px; color:var(--primary);">🛒 Histórico de Vendas do Estoque</h3>
        <div class="table-container" id="historico-vendas-estoque"></div>
    </div>

    <!-- TELA VENDAS E ESTOQUE -->
    <div id="tela-vendas" class="tela">
        <div class="header-tela"><h2>Controle de Vendas e Estoque</h2><button class="btn-voltar" onclick="mudarTela('menu-inicial')">Voltar</button></div>
        <div class="vendas-grid">
            <div style="background: var(--light); padding: 15px; border-radius: 10px; height: fit-content;">
                <h3 id="titulo-prod-form" style="margin-top:0; color:var(--primary)">Cadastrar Produto</h3>
                <input type="hidden" id="prod-index">
                <div style="margin-bottom: 10px;"><label style="font-size:0.85rem; font-weight:bold;">Nome do Produto</label><input type="text" id="prod-nome" placeholder="Ex: Controle Remoto"></div>
                <div style="margin-bottom: 10px;"><label style="font-size:0.85rem; font-weight:bold;">Quantidade</label><input type="number" id="prod-qtd"></div>
                <div style="margin-bottom: 10px;"><label style="font-size:0.85rem; font-weight:bold;">Preço de Custo</label><input type="number" id="prod-custo" step="0.01"></div>
                <div style="margin-bottom: 10px;"><label style="font-size:0.85rem; font-weight:bold;">Preço de Venda</label><input type="number" id="prod-venda" step="0.01"></div>
                <button class="btn-acao" style="background:var(--success); width:100%" onclick="salvarProduto()">SALVAR PRODUTO</button>
                <button class="btn-acao" id="btn-cancelar-edit" style="background:#7f8c8d; width:100%; display:none; margin-top:5px" onclick="limparFormProduto()">CANCELAR EDIÇÃO</button>
            </div>
            <div>
                <!-- MÓDULO DE VENDA RÁPIDA ATUALIZADO -->
                <div style="background: #dfe6e9; padding: 15px; border-radius: 10px; margin-bottom: 15px;">
                    <strong style="display:block; margin-bottom:8px;">Venda Rápida de Balcão:</strong>
                    <div style="display: flex; gap:10px; align-items: center; position:relative;" class="busca-container">
                        <input type="hidden" id="venda-selected-idx" value="">
                        <input type="text" id="venda-busca-prod-input" placeholder="Pesquise o produto aqui..." oninput="filtrarProdutosVendaRapida()" style="flex:2;">
                        <input type="number" id="venda-qtd" style="width: 80px;" value="1" placeholder="Qtd">
                        <button class="btn-acao" style="background:#00b894; margin:0; width:120px" onclick="venderProduto()">VENDER</button>
                        <div id="busca-venda-resultados" class="busca-resultados" style="top:48px; width:100%;"></div>
                    </div>
                </div>
                
                <h3 style="margin-top:0; color:var(--primary)">Itens Cadastrados em Estoque</h3>
                <div class="table-container" style="max-height: 250px;" id="tabela-estoque-container"></div>
                
                <h3 style="margin-top:15px; color:var(--primary)">📋 Registro Geral de Vendas Rápidas</h3>
                <div class="table-container" style="max-height: 250px;" id="tabela-registro-vendas-rapidas"></div>
            </div>
        </div>
    </div>
</div>

<script>
    const DB_NAME = 'servtec_v33_db';
    const PROD_DB_NAME = 'servtec_v33_produtos';
    const CAIXA_VENDAS_DB = 'servtec_v33_caixavendas';
    
    let usuarioLogado = null; 
    let nivelAcesso = ""; 

    function efetuarLogin() {
        const u = document.getElementById('login-usuario').value.trim();
        const s = document.getElementById('login-senha').value.trim();
        
        if (u === "laerson" && s === "martini2026") {
            nivelAcesso = "admin";
            logarSucesso();
        } else if (u === "servtec" && s === "123456") {
            nivelAcesso = "func";
            logarSucesso();
        } else {
            alert("Usuário ou Senha incorretos!");
        }
    }

    function logarSucesso() {
        document.getElementById('tela-login').style.display = 'none';
        document.getElementById('painel-sistema').style.display = 'block';
        
        if(nivelAcesso === "admin") {
            document.getElementById('menu-btn-financeiro').style.display = 'flex';
        } else {
            document.getElementById('menu-btn-financeiro').style.display = 'none';
        }
        mudarTela('menu-inicial');
    }

    function logout() {
        nivelAcesso = "";
        document.getElementById('login-usuario').value = "";
        document.getElementById('login-senha').value = "";
        document.getElementById('painel-sistema').style.display = 'none';
        document.getElementById('tela-login').style.display = 'block';
    }

    let modeloAtualFolha = "entrada";

    function mudarTela(id) {
        document.querySelectorAll('.tela').forEach(t => t.style.display = 'none');
        document.getElementById(id).style.display = 'block';
        
        if(id === 'tela-orcamento') {
            document.getElementById('busca-orc-input').value = "";
            document.getElementById('busca-orc-resultados').style.display = "none";
            document.getElementById('area-orcamento').innerHTML = "";
        }
        if(id === 'tela-saida') {
            document.getElementById('busca-fin-input').value = "";
            document.getElementById('busca-fin-resultados').style.display = "none";
            document.getElementById('area-finalizar').innerHTML = "";
        }
        if(id === 'tela-entrada') {
            document.querySelectorAll('#tela-entrada input, #tela-entrada textarea').forEach(i => i.value = "");
        }
        if(id === 'menu-inicial') {
            document.getElementById('busca-geral-input').value = "";
            atualizarTabela();
        }
        if(id === 'tela-financeiro') {
            if(nivelAcesso !== 'admin') { mudarTela('menu-inicial'); return; }
            atualizarFinanceiro();
        }
        if(id === 'tela-vendas') {
            document.getElementById('venda-busca-prod-input').value = "";
            document.getElementById('venda-selected-idx').value = "";
            document.getElementById('busca-venda-resultados').style.display = "none";
            atualizarEstoque();
        }
    }

    function obterClasseBolinha(status) {
        switch(status.toLowerCase()) {
            case 'pendente': return 'dot-pendente';
            case 'aprovado': return 'dot-aprovado';
            case 'reprovado': return 'dot-reprovado';
            case 'entregue': return 'dot-entregue';
            case 'cancelado': case 'sem conserto': return 'dot-cancelado';
            default: return 'dot-pendente';
        }
    }

    // ================== CONTROLE GERAL / ENTRADA ==================
    function salvarEntrada() {
        const cliente = document.getElementById('ent-cliente').value;
        if(!cliente) return alert("Cliente obrigatório!");
        let db = JSON.parse(localStorage.getItem(DB_NAME)) || [];
        let novoId = db.length > 0 ? Math.max(...db.map(o => o.id)) + 1 : 1001;
        db.unshift({
            id: novoId, cliente, contato: document.getElementById('ent-contato').value,
            tipo: document.getElementById('ent-tipo').value, marca: document.getElementById('ent-marca').value,
            modelo: document.getElementById('ent-modelo').value, serie: document.getElementById('ent-serie').value,
            acessorios: document.getElementById('ent-acessorios').value, defeito: document.getElementById('ent-defeito').value,
            pecaDefeito: "", servico: "Troca", viavel: "Sim", valorPeca: 0, valorObra: 0, total: 0,
            status: "Pendente", dataE: new Date().toLocaleDateString()
        });
        localStorage.setItem(DB_NAME, JSON.stringify(db));
        mudarTela('menu-inicial');
    }

    function atualizarTabela() {
        const db = JSON.parse(localStorage.getItem(DB_NAME)) || [];
        renderizarTabela(db);
    }

    function renderizarTabela(dados) {
        let html = `<table><tr><th>OS</th><th>Cliente</th><th>Equipamento</th><th>Total</th><th>Status</th></tr>`;
        dados.forEach(o => {
            const st = o.status.toLowerCase().replace(/ /g, '-');
            const dotClass = obterClasseBolinha(o.status);
            html += `<tr class="linha-clicavel" onclick="abrirOS(${o.id})">
                <td>#${o.id}</td>
                <td>${o.cliente}</td>
                <td>${o.tipo} ${o.marca}</td>
                <td>R$ ${o.total.toFixed(2)}</td>
                <td>
                    <div class="status-container">
                        <span class="status-dot ${dotClass}"></span>
                        <span class="status-badge st-${st}">${o.status}</span>
                    </div>
                </td>
            </tr>`;
        });
        document.getElementById('lista-geral').innerHTML = html + "</table>";
    }

    function filtrarTabelaPrincipal() {
        const query = document.getElementById('busca-geral-input').value.toLowerCase();
        const db = JSON.parse(localStorage.getItem(DB_NAME)) || [];
        const filtrado = db.filter(o => 
            o.id.toString().includes(query) || 
            o.cliente.toLowerCase().includes(query) || 
            o.tipo.toLowerCase().includes(query) || 
            o.marca.toLowerCase().includes(query)
        );
        renderizarTabela(filtrado);
    }

    function filtrarPesquisaInterna(inputId, resultadosId, tipoTela) {
        const query = document.getElementById(inputId).value.toLowerCase();
        const resultadosContainer = document.getElementById(resultadosId);
        if (!query) { resultadosContainer.style.display = "none"; return; }
        const db = JSON.parse(localStorage.getItem(DB_NAME)) || [];
        let filtrados = db.filter(o => o.id.toString().includes(query) || o.cliente.toLowerCase().includes(query) || o.tipo.toLowerCase().includes(query));
        if (filtrados.length === 0) {
            resultadosContainer.innerHTML = `<div class="busca-item">Nenhuma OS encontrada</div>`;
        } else {
            let html = "";
            filtrados.forEach(os => {
                html += `<div class="busca-item" onclick="selecionarOsPesquisada(${os.id}, '${tipoTela}', '${resultadosId}', '${inputId}')"><strong>#${os.id}</strong> - ${os.cliente} (${os.tipo})</div>`;
            });
            resultadosContainer.innerHTML = html;
        }
        resultadosContainer.style.display = "block";
    }

    function selecionarOsPesquisada(id, tipoTela, resultadosId, inputId) {
        document.getElementById(resultadosId).style.display = "none";
        document.getElementById(inputId).value = id;
        if (tipoTela === 'orc') carregarOrcamentoForm(id);
        if (tipoTela === 'fin') carregarFinalizarForm(id);
    }

    // ================== GERADOR DE MODELO RÁPIDO ==================
    function renderizarFolhaDocumento(os) {
        const divFolha = document.getElementById('folha-os');
        const btnAlternar = document.getElementById('btn-mudar-modelo');
        
        if (modeloAtualFolha === "entrada") {
            btnAlternar.innerText = "Mudar para Orçamento 💰";
            divFolha.innerHTML = `
                <div class="topo-os">
                    <div><h2>SERVTEC ELETRÔNICA</h2><small style="font-size:1.05rem;">Manutenção Corretiva - Entrada</small></div>
                    <div style="text-align:right; font-size:1.05rem;"><strong>Ordem de Serviço #${os.id}</strong><br>Data: ${os.dataE}</div>
                </div>
                <div class="faixa-os">Cliente</div>
                <div class="conteudo-os">
                    <p><strong>Nome:</strong> ${os.cliente}</p>
                    <p><strong>Telefone/WhatsApp:</strong> ${os.contato || '---'}</p>
                </div>
                <div class="faixa-os">Equipamento ou Ativo</div>
                <div class="conteudo-os">
                    <p><strong>Nome/Tipo:</strong> ${os.tipo} | <strong>Marca:</strong> ${os.marca || '---'}</p>
                    <p><strong>Modelo:</strong> ${os.modelo || '---'} | <strong>Nº de Série:</strong> ${os.serie || '---'}</p>
                    <p><strong>Acessórios:</strong> ${os.acessorios || 'Nenhum'}</p>
                    <p><strong>Defeito Reclamado:</strong> ${os.defeito}</p>
                </div>
                <div style="margin-top: 100px; text-align: center; font-size: 1rem;">
                    <p>__________________________________________________________________</p>
                    <p style="font-weight:bold; margin-top:5px;">Assinatura do Responsável Técnico</p>
                </div>`;
        } else {
            btnAlternar.innerText = "Mudar para Comprovante Entrada 📥";
            
            let faixaStatusHtml = "";
            if(os.status === 'Aprovado'){
                faixaStatusHtml = `<div class="faixa-os" style="background:#27ae60; text-align:center; padding:12px; margin-top:20px;">Orçamento Aprovado - Pronto/Em manutenção</div>`;
            } else if(os.status === 'Reprovado'){
                faixaStatusHtml = `<div class="faixa-os" style="background:#e74c3c; text-align:center; padding:12px; margin-top:20px;">Orçamento Reprovado pelo Cliente</div>`;
            } else {
                faixaStatusHtml = `<div class="faixa-os" style="background:#7f8c8d; text-align:center; padding:12px; margin-top:20px;">Aguardando Resposta / Aprovação do Cliente</div>`;
            }

            divFolha.innerHTML = `
                <div class="topo-os">
                    <div><h2>SERVTEC ELETRÔNICA</h2><small style="font-size:1.05rem;">Orçamento Técnico</small></div>
                    <div style="text-align:right; font-size:1.05rem;"><strong>Ordem de Serviço #${os.id}</strong><br>Data: ${os.dataE}</div>
                </div>
                <div class="faixa-os">Cliente e Equipamento</div>
                <div class="conteudo-os">
                    <p><strong>Cliente:</strong> ${os.cliente}</p>
                    <p><strong>Equipamento:</strong> ${os.tipo} ${os.marca} ${os.modelo || ''}</p>
                </div>
                <div class="faixa-os">Diagnóstico / O que vai ser feito</div>
                <div class="conteudo-os">
                    <p style="font-size: 1.15rem; padding: 5px 0;"><strong>Serviço planejado:</strong> ${os.pecaDefeito ? os.pecaDefeito : 'Aparelho em triagem técnica.'}</p>
                    <p>• <strong>Tipo de intervenção:</strong> ${os.servico}</p>
                    <p>• <strong>Viabilidade de Reparo:</strong> ${os.viavel}</p>
                </div>
                <div class="faixa-os">Preços e Valores</div>
                <div class="conteudo-os">
                    <p><strong>Valor das Peças:</strong> R$ ${os.valorPeca.toFixed(2)}</p>
                    <p><strong>Mão de Obra Técnica:</strong> R$ ${os.valorObra.toFixed(2)}</p>
                    <h2 style="margin-top:10px; color:#27ae60; font-size:1.5rem;">TOTAL DO SERVIÇO: R$ ${os.total.toFixed(2)}</h2>
                </div>
                
                <div class="faixa-os" style="background:#2c3e50; font-size:0.95rem;">Termo de Garantia</div>
                <div class="conteudo-os" style="font-size:0.9rem; line-height:1.4; color:#555;">
                    <p>Garantia legal de 90 dias (três meses) a partir da data de entrega/retirada do equipamento, válida exclusivamente para as peças substituídas ou para o serviço específico discriminado neste laudo técnico.</p>
                </div>

                ${faixaStatusHtml}`;
        }
    }

    function abrirOS(id) {
        const db = JSON.parse(localStorage.getItem(DB_NAME)) || [];
        const os = db.find(o => o.id == id);
        if(!os) return;
        
        modeloAtualFolha = "entrada"; 
        renderizarFolhaDocumento(os);

        document.getElementById('btn-mudar-modelo').onclick = () => {
            modeloAtualFolha = (modeloAtualFolha === "entrada") ? "orcamento" : "entrada";
            renderizarFolhaDocumento(os);
        };

        document.getElementById('btn-aprov-os').onclick = () => { 
            atualizarStatusRapido(os.id, 'Aprovado'); 
            os.status = 'Aprovado';
            renderizarFolhaDocumento(os); 
        };
        document.getElementById('btn-reprov-os').onclick = () => { 
            atualizarStatusRapido(os.id, 'Reprovado'); 
            os.status = 'Reprovado'; 
            renderizarFolhaDocumento(os); 
        };

        document.getElementById('btn-pdf-os').onclick = () => {
            const element = document.getElementById('folha-os');
            const opt = {
                margin: [8, 8, 8, 8], // Margem fluida milimétrica padrão para impressão A4 limpa
                filename: `OS_${os.id}_${os.cliente.replace(/ /g, '_')}.pdf`,
                image: { type: 'jpeg', quality: 0.98 },
                html2canvas: { scale: 2.5, useCORS: true, scrollY: 0, scrollX: 0 },
                jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' }
            };
            html2pdf().set(opt).from(element).save();
        };

        document.getElementById('btn-zap-os').onclick = () => {
            let textoMensagem = "";
            
            if (modeloAtualFolha === "entrada") {
                textoMensagem = `Olá *${os.cliente}*, aqui é da *Servtec Eletrônica*! 👋\n\n` +
                                `Confirmamos a entrada do seu equipamento em nosso laboratório.\n` +
                                `*Ordem de Serviço:* #${os.id}\n` +
                                `*Aparelho:* ${os.tipo} ${os.marca} ${os.modelo || ''}\n` +
                                `*Problema relatado:* ${os.defeito}\n\n` +
                                `Assim que a triagem for finalizada, enviaremos o laudo técnico com o orçamento para sua aprovação.`;
            } else {
                let servicoPlanejado = os.pecaDefeito ? os.pecaDefeito : 'Aparelho avaliado em triagem técnica.';
                textoMensagem = `Olá *${os.cliente}*, aqui é da *Servtec Eletrônica*! 👋\n\n` +
                                `O orçamento técnico do seu aparelho já está pronto.\n` +
                                `*Ordem de Serviço:* #${os.id}\n` +
                                `*Aparelho:* ${os.tipo} ${os.marca} ${os.modelo || ''}\n\n` +
                                `*O que vai ser feito:* ${servicoPlanejado}\n` +
                                `*Tipo de Intervenção:* ${os.servico}\n` +
                                `*Valor Total:* R$ ${os.total.toFixed(2)}\n\n` +
                                `*Garantia:* Inclusa garantia de 90 dias nos serviços executados e peças trocadas! 🛠️\n\n` +
                                `Ficamos no aguardo da sua autorização para iniciar o reparo! 👍`;
            }

            const txt = window.encodeURIComponent(textoMensagem);
            window.open(`https://api.whatsapp.com/send?phone=55${os.contato.replace(/\D/g,'')}&text=${txt}`);
        };
        mudarTela('tela-detalhes');
    }

    function atualizarStatusRapido(id, status) {
        let db = JSON.parse(localStorage.getItem(DB_NAME));
        let i = db.findIndex(o => o.id == id);
        db[i].status = status;
        localStorage.setItem(DB_NAME, JSON.stringify(db));
        alert(`OS #${id} alterada para "${status}"!`);
    }

    // ================== ORÇAMENTO FORMS ==================
    function carregarOrcamentoForm(id) {
        const os = (JSON.parse(localStorage.getItem(DB_NAME)) || []).find(o => o.id == id);
        if(!os) return alert("OS não encontrada!");
        document.getElementById('area-orcamento').innerHTML = `
            <div class="form-box">
                <div class="info-resumo"><strong>CLIENTE:</strong> ${os.cliente} | <strong>EQUIPAMENTO:</strong> ${os.tipo} ${os.marca}</div>
                <div class="campo-grupo full-width"><label>O que será feito? (Laudo / Descrição para o cliente)</label><input type="text" id="e-peca" value="${os.pecaDefeito}" placeholder="Ex: Efetuar troca de capacitores e conector de carga estragado"></div>
                <div class="campo-grupo"><label>Tipo de Serviço</label><select id="e-serv"><option value="Troca">Troca</option><option value="Reparo">Reparo</option></select></div>
                <div class="campo-grupo"><label>Viabilidade</label><select id="e-viavel"><option value="Sim">Sim</option><option value="Não">Não</option></select></div>
                <div class="campo-grupo"><label>Valor Peça (R$)</label><input type="number" id="e-vp" value="${os.valorPeca}" oninput="calcTotal()"></div>
                <div class="campo-grupo"><label>Mão de Obra (R$)</label><input type="number" id="e-vo" value="${os.valorObra}" oninput="calcTotal()"></div>
                <div class="campo-grupo full-width"><label>TOTAL</label><input type="text" id="e-total" value="R$ ${os.total.toFixed(2)}" disabled style="background:#fffbe6; color:green; font-weight:bold;"></div>
                <button class="btn-acao full-width" style="background:var(--success)" onclick="gravarOrc(${os.id})">SALVAR ORÇAMENTO</button>
            </div>`;
    }

    function calcTotal() {
        const p = parseFloat(document.getElementById('e-vp').value) || 0;
        const o = parseFloat(document.getElementById('e-vo').value) || 0;
        document.getElementById('e-total').value = "R$ " + (p + o).toFixed(2);
    }

    function gravarOrc(id) {
        let db = JSON.parse(localStorage.getItem(DB_NAME));
        let i = db.findIndex(o => o.id == id);
        db[i].pecaDefeito = document.getElementById('e-peca').value;
        db[i].servico = document.getElementById('e-serv').value;
        db[i].viavel = document.getElementById('e-viavel').value;
        db[i].valorPeca = parseFloat(document.getElementById('e-vp').value) || 0;
        db[i].valorObra = parseFloat(document.getElementById('e-vo').value) || 0;
        db[i].total = db[i].valorPeca + db[i].valorObra;
        localStorage.setItem(DB_NAME, JSON.stringify(db));
        mudarTela('menu-inicial');
    }

    // ================== FINALIZAR ==================
    function carregarFinalizarForm(id) {
        const os = (JSON.parse(localStorage.getItem(DB_NAME)) || []).find(o => o.id == id);
        if(!os) return alert("OS não encontrada!");
        document.getElementById('area-finalizar').innerHTML = `
            <div class="bloco-info"><h3>Informações do Cliente</h3><p><strong>Nome:</strong> ${os.cliente}</p><p><strong>WhatsApp:</strong> ${os.contato}</p></div>
            <div class="bloco-info"><h3>Aparelho Recebido</h3><p><strong>Equipamento:</strong> ${os.tipo} | <strong>Marca/Modelo:</strong> ${os.marca} ${os.modelo}</p></div>
            <div class="bloco-info" style="border-left: 5px solid var(--success);"><h3>Total</h3><h2>R$ ${os.total.toFixed(2)}</h2></div>
            <div style="display:grid; grid-template-columns:1fr 1fr 1fr; gap:10px;"><button class="btn-acao" style="background:var(--success); margin:0" onclick="finalizarOS(${os.id}, 'Entregue')">PAGO E ENTREGUE</button><button class="btn-acao" style="background:var(--warning); margin:0" onclick="finalizarOS(${os.id}, 'Sem Conserto')">SEM CONSERTO</button><button class="btn-acao" style="background:var(--danger); margin:0" onclick="finalizarOS(${os.id}, 'Cancelado')">CANCELADO</button></div>`;
    }

    function finalizarOS(id, status) {
        let db = JSON.parse(localStorage.getItem(DB_NAME));
        let i = db.findIndex(o => o.id == id);
        db[i].status = status; db[i].dataS = new Date().toISOString();
        localStorage.setItem(DB_NAME, JSON.stringify(db));
        mudarTela('menu-inicial');
    }

    // ================== BUSCA REAL E PREDITIVA PARA VENDAS ==================
    function filtrarProdutosVendaRapida() {
        const query = document.getElementById('venda-busca-prod-input').value.toLowerCase();
        const resultadosContainer = document.getElementById('busca-venda-resultados');
        
        if(!query) { resultadosContainer.style.display = "none"; return; }
        
        const produtos = JSON.parse(localStorage.getItem(PROD_DB_NAME)) || [];
        let filtrados = produtos.map((p, idx) => ({...p, idx})).filter(p => p.nome.toLowerCase().includes(query) && p.qtd > 0);
        
        if(filtrados.length === 0) {
            resultadosContainer.innerHTML = `<div class="busca-item">Nenhum produto em estoque</div>`;
        } else {
            let html = "";
            filtrados.forEach(p => {
                html += `<div class="busca-item" onclick="selecionarProdutoVendaRapida(${p.idx}, '${p.nome}', ${p.venda}, ${p.qtd})">
                    <strong>${p.nome}</strong> - R$ ${p.venda.toFixed(2)} <span style="font-size:0.8rem; color:#7f8c8d;">(${p.qtd} un dispo.)</span>
                </div>`;
            });
            resultadosContainer.innerHTML = html;
        }
        resultadosContainer.style.display = "block";
    }

    function selecionarProdutoVendaRapida(idx, nome, preco, estoqueMax) {
        document.getElementById('busca-venda-resultados').style.display = "none";
        document.getElementById('venda-selected-idx').value = idx;
        document.getElementById('venda-busca-prod-input').value = `${nome} (R$ ${preco.toFixed(2)})`;
    }

    // ================== VENDAS / ESTOQUE & FINANCEIRO ==================
    function atualizarEstoque() {
        const produtos = JSON.parse(localStorage.getItem(PROD_DB_NAME)) || [];
        const vendas = JSON.parse(localStorage.getItem(CAIXA_VENDAS_DB)) || [];
        
        let html = `<table><tr><th>Produto</th><th>Estoque</th><th>P. Custo</th><th>P. Venda</th><th>Ações</th></tr>`;
        produtos.forEach((p, idx) => {
            html += `<tr><td><strong>${p.nome}</strong></td><td>${p.qtd} un</td><td>R$ ${p.custo.toFixed(2)}</td><td style="color:green; font-weight:bold;">R$ ${p.venda.toFixed(2)}</td><td><button style="background:var(--info); color:white; border:none; padding:4px 8px; border-radius:4px;" onclick="prepararEdicao(${idx})">Editar</button> <button style="background:var(--danger); color:white; border:none; padding:4px 8px; border-radius:4px;" onclick="deletarProduto(${idx})">Excluir</button></td></tr>`;
        });
        document.getElementById('tabela-estoque-container').innerHTML = html + "</table>";

        let htmlVR = `<table><tr><th>Data</th><th>Item</th><th>Qtd</th><th>Total</th></tr>`;
        if(vendas.length === 0){
            htmlVR += `<tr><td colspan="4" style="text-align:center; color:#95a5a6;">Nenhuma venda registrada.</td></tr>`;
        } else {
            [...vendas].reverse().forEach(v => {
                let d = new Date(v.data).toLocaleDateString('pt-BR');
                htmlVR += `<tr><td>${d}</td><td>${v.nome}</td><td>${v.qtd} un</td><td style="color:green; font-weight:bold;">R$ ${v.valorVenda.toFixed(2)}</td></tr>`;
            });
        }
        document.getElementById('tabela-registro-vendas-rapidas').innerHTML = htmlVR + "</table>";
    }

    function salvarProduto() {
        const nome = document.getElementById('prod-nome').value;
        const qtd = parseInt(document.getElementById('prod-qtd').value) || 0;
        const custo = parseFloat(document.getElementById('prod-custo').value) || 0;
        const venda = parseFloat(document.getElementById('prod-venda').value) || 0;
        const index = document.getElementById('prod-index').value;
        if(!nome) return alert("Nome obrigatório!");
        let produtos = JSON.parse(localStorage.getItem(PROD_DB_NAME)) || [];
        if(index !== "") produtos[index] = { nome, qtd, custo, venda };
        else produtos.push({ nome, qtd, custo, venda });
        localStorage.setItem(PROD_DB_NAME, JSON.stringify(produtos));
        limparFormProduto(); atualizarEstoque();
    }

    function prepararEdicao(idx) {
        const p = (JSON.parse(localStorage.getItem(PROD_DB_NAME)))[idx];
        document.getElementById('prod-index').value = idx; document.getElementById('prod-nome').value = p.nome;
        document.getElementById('prod-qtd').value = p.qtd; document.getElementById('prod-custo').value = p.custo;
        document.getElementById('prod-venda').value = p.venda; document.getElementById('btn-cancelar-edit').style.display = "block";
    }

    function limparFormProduto() {
        document.getElementById('prod-index').value = ""; document.getElementById('prod-nome').value = "";
        document.getElementById('prod-qtd').value = ""; document.getElementById('prod-custo').value = "";
        document.getElementById('prod-venda').value = ""; document.getElementById('btn-cancelar-edit').style.display = "none";
    }

    function deletarProduto(idx) {
        let produtos = JSON.parse(localStorage.getItem(PROD_DB_NAME)); produtos.splice(idx, 1);
        localStorage.setItem(PROD_DB_NAME, JSON.stringify(produtos)); atualizarEstoque();
    }

    function venderProduto() {
        const idx = document.getElementById('venda-selected-idx').value;
        const qtdVenda = parseInt(document.getElementById('venda-qtd').value) || 1;
        
        if(idx === "") return alert("Por favor, pesquise e clique em um produto válido da lista!");
        
        let produtos = JSON.parse(localStorage.getItem(PROD_DB_NAME));
        const p = produtos[idx];
        
        if(p.qtd < qtdVenda) return alert(`Estoque insuficiente! Você só possui ${p.qtd} unidades.`);
        
        p.qtd -= qtdVenda; 
        localStorage.setItem(PROD_DB_NAME, JSON.stringify(produtos));
        
        let vendas = JSON.parse(localStorage.getItem(CAIXA_VENDAS_DB)) || [];
        vendas.push({ nome: p.nome, qtd: qtdVenda, valorVenda: p.venda * qtdVenda, data: new Date().toISOString() });
        localStorage.setItem(CAIXA_VENDAS_DB, JSON.stringify(vendas));
        
        alert("Venda realizada com sucesso!");
        
        document.getElementById('venda-busca-prod-input').value = "";
        document.getElementById('venda-selected-idx').value = "";
        document.getElementById('venda-qtd').value = 1;
        
        atualizarEstoque();
    }

    function atualizarFinanceiro() {
        const db = JSON.parse(localStorage.getItem(DB_NAME)) || [];
        const entregues = db.filter(o => o.status === 'Entregue' && o.dataS);
        const vendas = JSON.parse(localStorage.getItem(CAIXA_VENDAS_DB)) || [];
        
        const agora = new Date(); let hoje = 0, semana = 0, mes = 0;
        
        entregues.forEach(o => {
            const dS = new Date(o.dataS); const diff = (agora - dS) / (1000 * 60 * 60 * 24);
            if (dS.toDateString() === agora.toDateString()) hoje += o.total;
            if (diff <= 7) semana += o.total; if (diff <= 30) mes += o.total;
        });
        vendas.forEach(v => {
            const dS = new Date(v.data); const diff = (agora - dS) / (1000 * 60 * 60 * 24);
            if (dS.toDateString() === agora.toDateString()) hoje += v.valorVenda;
            if (diff <= 7) semana += v.valorVenda; if (diff <= 30) mes += v.valorVenda;
        });
        
        document.getElementById('financeiro-dashboard').innerHTML = `
            <div style="display:grid; grid-template-columns:1fr 1fr 1fr; gap:10px; margin-bottom:20px">
                <div class="card-fin"><h4>Hoje</h4><p>R$ ${hoje.toFixed(2)}</p></div>
                <div class="card-fin" style="border-left-color:var(--info)"><h4>7 dias</h4><p>R$ ${semana.toFixed(2)}</p></div>
                <div class="card-fin" style="border-left-color:var(--primary)"><h4>30 dias</h4><p>R$ ${mes.toFixed(2)}</p></div>
            </div>`;

        let htmlEquip = `<table><tr><th>Data Saída</th><th>OS</th><th>Cliente</th><th>Equipamento</th><th>Laudo Executado</th><th>Total Pago</th></tr>`;
        if(entregues.length === 0) {
            htmlEquip += `<tr><td colspan="6" style="text-align:center; color:#7f8c8d;">Nenhum equipamento entregue até o momento.</td></tr>`;
        } else {
            entregues.forEach(o => {
                let dataFormatada = new Date(o.dataS).toLocaleDateString('pt-BR');
                htmlEquip += `<tr><td>${dataFormatada}</td><td><strong>#${o.id}</strong></td><td>${o.cliente}</td><td>${o.tipo} ${o.marca}</td><td>${o.pecaDefeito || 'Reparo Geral'}</td><td style="color:green; font-weight:bold;">R$ ${o.total.toFixed(2)}</td></tr>`;
            });
        }
        document.getElementById('historico-equipamentos').innerHTML = htmlEquip + "</table>";

        let htmlVendas = `<table><tr><th>Data Venda</th><th>Produto</th><th>Qtd Vendida</th><th>Total Recebido</th></tr>`;
        if(vendas.length === 0) {
            htmlVendas += `<tr><td colspan="4" style="text-align:center; color:#7f8c8d;">Nenhuma venda realizada no estoque ainda.</td></tr>`;
        } else {
            vendas.forEach(v => {
                let dataFormatada = new Date(v.data).toLocaleDateString('pt-BR');
                htmlVendas += `<tr><td>${dataFormatada}</td><td><strong>${v.nome}</strong></td><td>${v.qtd} un</td><td style="color:green; font-weight:bold;">R$ ${v.valorVenda.toFixed(2)}</td></tr>`;
            });
        }
        document.getElementById('historico-vendas-estoque').innerHTML = htmlVendas + "</table>";
    }

    document.addEventListener('click', function(e) { if (!e.target.closest('.busca-container')) { document.querySelectorAll('.busca-resultados').forEach(el => el.style.display = 'none'); } });
</script>
</body>
</html>
