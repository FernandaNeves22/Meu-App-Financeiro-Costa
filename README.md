<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Controle Financeiro Familia Costa</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Inter', sans-serif; background-color: #f3f4f6; -webkit-tap-highlight-color: transparent; }
        .no-scrollbar::-webkit-scrollbar { display: none; }
        .no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
        /* Safe area for modern phones with notches/home bars */
        .safe-bottom { padding-bottom: env(safe-area-inset-bottom); }
    </style>
</head>
<body class="h-screen flex flex-col bg-gray-100">

    <!-- Header Fixo (Muda o título dinamicamente) -->
    <header class="bg-slate-800 text-white p-4 shadow-md flex-none z-10">
        <h1 id="header-title" class="text-xl font-bold">Visão Geral</h1>
    </header>

    <!-- Área Principal (Conteúdo das Abas) -->
    <main class="flex-grow overflow-y-auto pb-24 safe-bottom scroll-smooth">
        
        <!-- ================= TAB 1: PAINEL GERAL (Financeiro) ================= -->
        <section id="tab-dashboard" class="p-4 space-y-6">
             <!-- Resumo Financeiro -->
             <div class="grid grid-cols-1 md:grid-cols-3 gap-3">
                <div class="bg-white p-5 rounded-2xl shadow-sm border-l-4 border-blue-500">
                    <h2 class="text-sm font-medium text-gray-500 mb-1">Entradas</h2>
                    <p id="total-entradas" class="text-2xl font-bold text-blue-600">R$ 0,00</p>
                </div>
                <div class="bg-white p-5 rounded-2xl shadow-sm border-l-4 border-red-500">
                    <h2 class="text-sm font-medium text-gray-500 mb-1">Saídas</h2>
                    <p id="total-saidas" class="text-2xl font-bold text-red-600">R$ 0,00</p>
                </div>
                <div class="bg-white p-5 rounded-2xl shadow-sm border-l-4 border-gray-300" id="card-saldo">
                    <h2 class="text-sm font-medium text-gray-500 mb-1">Saldo Total</h2>
                    <p id="saldo-total" class="text-3xl font-bold text-gray-800">R$ 0,00</p>
                </div>
            </div>

            <!-- Formulário Rápido -->
            <div class="bg-white p-5 rounded-2xl shadow-sm">
                <h3 class="text-lg font-bold text-gray-800 mb-4">Nova Movimentação</h3>
                <form id="transaction-form" class="space-y-3">
                    <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
                        <input type="text" id="descricao" placeholder="Descrição (ex: Salário)" required class="p-3 bg-gray-50 border border-gray-200 rounded-xl focus:ring-2 focus:ring-blue-500 outline-none w-full transition-all">
                        <input type="number" id="valor" placeholder="Valor (R$)" step="0.01" min="0.01" required class="p-3 bg-gray-50 border border-gray-200 rounded-xl focus:ring-2 focus:ring-blue-500 outline-none w-full transition-all">
                    </div>
                    <div class="flex space-x-2">
                        <select id="tipo" required class="p-3 bg-gray-50 border border-gray-200 rounded-xl focus:ring-2 focus:ring-blue-500 outline-none flex-grow transition-all">
                            <option value="saida">📉 Saída (-)</option>
                            <option value="entrada">📈 Entrada (+)</option>
                        </select>
                        <button type="submit" class="bg-slate-800 text-white px-6 rounded-xl font-bold hover:bg-slate-900 transition-transform active:scale-95">
                            Adicionar
                        </button>
                    </div>
                </form>
            </div>

            <!-- Histórico -->
            <div class="bg-white rounded-2xl shadow-sm overflow-hidden">
                <div class="p-4 border-b border-gray-100 flex justify-between items-center">
                    <h3 class="font-bold text-gray-800">Histórico Recente</h3>
                    <button onclick="limparDadosFinanceiros()" class="text-xs text-red-400 hover:text-red-600">Limpar Tudo</button>
                </div>
                <div id="sem-transacoes" class="p-8 text-center text-gray-400 hidden">
                    Nenhuma movimentação ainda.
                </div>
                <ul id="lista-transacoes" class="divide-y divide-gray-100">
                    <!-- Inserido via JS -->
                </ul>
            </div>
        </section>

        <!-- ================= TAB 2: CONTAS A PAGAR ================= -->
        <section id="tab-bills" class="hidden p-4">
            <div id="bills-empty-state" class="hidden flex flex-col items-center justify-center py-20 text-gray-400">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-16 w-16 mb-4 opacity-50" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-3 7h3m-3 4h3m-6-4h.01M9 16h.01" />
                </svg>
                <p>Nenhuma conta pendente.</p>
            </div>
            <div id="bills-container" class="space-y-4">
                <!-- Inserido via JS -->
            </div>
        </section>

    </main>

    <!-- Botão Flutuante (FAB) - Apenas visível na aba Contas -->
    <button id="fab-add-bill" onclick="openBillModal()" class="hidden fixed bottom-24 right-6 bg-blue-600 text-white rounded-full p-4 shadow-lg hover:bg-blue-700 transition-all active:scale-90 z-20">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6v6m0 0v6m0-6h6m-6 0H6" />
        </svg>
    </button>

    <!-- Barra de Navegação Inferior -->
    <nav class="fixed bottom-0 left-0 right-0 bg-white border-t border-gray-200 flex justify-around p-2 safe-bottom z-30 shadow-[0_-4px_6px_-1px_rgba(0,0,0,0.05)]">
        <button onclick="switchTab('dashboard')" id="nav-dashboard" class="flex flex-col items-center w-full p-2 text-blue-600 transition-colors">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 mb-1" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 3.055A9.001 9.001 0 1020.945 13H11V3.055z" />
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20.488 9H15V3.512A9.025 9.025 0 0120.488 9z" />
            </svg>
            <span class="text-xs font-medium">Painel</span>
        </button>
        <button onclick="switchTab('bills')" id="nav-bills" class="flex flex-col items-center w-full p-2 text-gray-400 hover:text-gray-600 transition-colors">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 mb-1" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" />
            </svg>
            <span class="text-xs font-medium">Contas a Pagar</span>
        </button>
    </nav>

    <!-- Modal Adicionar Conta (App 2) -->
    <div id="modal-add-bill" class="fixed inset-0 bg-black bg-opacity-50 hidden items-end sm:items-center justify-center z-50 transition-opacity duration-300 backdrop-blur-sm">
        <div class="bg-white w-full sm:max-w-md sm:rounded-2xl rounded-t-3xl p-6 shadow-2xl transform transition-transform duration-300 translate-y-full sm:translate-y-0" id="modal-bill-content">
            <div class="flex justify-between items-center mb-6">
                <h2 class="text-xl font-bold text-gray-800">Nova Conta</h2>
                <button onclick="closeBillModal()" class="bg-gray-100 p-2 rounded-full text-gray-500 hover:bg-gray-200">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                        <path fill-rule="evenodd" d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z" clip-rule="evenodd" />
                    </svg>
                </button>
            </div>
            <form id="add-bill-form" onsubmit="handleAddBill(event)" class="space-y-4">
                <input type="text" id="bill-name" required placeholder="Descrição (ex: Luz, Internet)" class="w-full p-4 bg-gray-50 border border-gray-200 rounded-xl focus:ring-2 focus:ring-blue-500 outline-none">
                <div class="flex space-x-3">
                    <input type="number" step="0.01" id="bill-value" required placeholder="Valor (R$)" class="w-full p-4 bg-gray-50 border border-gray-200 rounded-xl focus:ring-2 focus:ring-blue-500 outline-none">
                    <input type="date" id="bill-due" required class="w-full p-4 bg-gray-50 border border-gray-200 rounded-xl focus:ring-2 focus:ring-blue-500 outline-none">
                </div>
                <button type="submit" class="w-full bg-blue-600 text-white py-4 rounded-xl font-bold text-lg hover:bg-blue-700 transition-transform active:scale-95 mt-2">
                    Salvar Conta
                </button>
            </form>
        </div>
    </div>

    <script>
        // ==================== GESTÃO DE ABAS ====================
        let activeTab = 'dashboard';
        const headerTitle = document.getElementById('header-title');
        const tabDashboard = document.getElementById('tab-dashboard');
        const tabBills = document.getElementById('tab-bills');
        const navDashboard = document.getElementById('nav-dashboard');
        const navBills = document.getElementById('nav-bills');
        const fabAddBill = document.getElementById('fab-add-bill');

        function switchTab(tab) {
            activeTab = tab;
            if (tab === 'dashboard') {
                headerTitle.textContent = 'Visão Geral';
                tabDashboard.classList.remove('hidden');
                tabBills.classList.add('hidden');
                navDashboard.classList.add('text-blue-600');
                navDashboard.classList.remove('text-gray-400');
                navBills.classList.remove('text-blue-600');
                navBills.classList.add('text-gray-400');
                fabAddBill.classList.add('hidden');
            } else {
                headerTitle.textContent = 'Contas a Pagar';
                tabDashboard.classList.add('hidden');
                tabBills.classList.remove('hidden');
                navBills.classList.add('text-blue-600');
                navBills.classList.remove('text-gray-400');
                navDashboard.classList.remove('text-blue-600');
                navDashboard.classList.add('text-gray-400');
                fabAddBill.classList.remove('hidden');
            }
        }

        // ==================== APP 1: CONTROLE FINANCEIRO ====================
        let transacoes = JSON.parse(localStorage.getItem('financas_transacoes')) || [];
        const formTransacao = document.getElementById('transaction-form');
        const listaTransacoesEl = document.getElementById('lista-transacoes');
        const semTransacoesEl = document.getElementById('sem-transacoes');

        function formatarMoeda(valor) {
            return valor.toLocaleString('pt-BR', { style: 'currency', currency: 'BRL' });
        }

        function atualizarPainelFinanceiro() {
            listaTransacoesEl.innerHTML = '';
            if (transacoes.length === 0) {
                semTransacoesEl.classList.remove('hidden');
            } else {
                semTransacoesEl.classList.add('hidden');
                // Mostrar as últimas 30 transações primeiro
                transacoes.slice().reverse().forEach(t => {
                    const li = document.createElement('li');
                    li.className = 'p-4 flex justify-between items-center hover:bg-gray-50';
                    li.innerHTML = `
                        <div class="flex items-center">
                            <div class="w-10 h-10 rounded-full flex items-center justify-center mr-3 ${t.tipo === 'entrada' ? 'bg-green-100 text-green-600' : 'bg-red-100 text-red-600'}">
                                ${t.tipo === 'entrada' ? '↓' : '↑'}
                            </div>
                            <div>
                                <p class="font-medium text-gray-800">${t.descricao}</p>
                                <p class="text-xs text-gray-500">${new Date(t.id).toLocaleDateString('pt-BR')}</p>
                            </div>
                        </div>
                        <div class="flex items-center">
                             <span class="font-semibold ${t.tipo === 'entrada' ? 'text-green-600' : 'text-red-600'}">
                                ${t.tipo === 'entrada' ? '+' : '-'} ${formatarMoeda(t.valor)}
                            </span>
                            <button onclick="removerTransacao(${t.id})" class="ml-3 text-gray-300 hover:text-red-500 p-1">
                                ×
                            </button>
                        </div>
                    `;
                    listaTransacoesEl.appendChild(li);
                });
            }

            const entradas = transacoes.filter(t => t.tipo === 'entrada').reduce((acc, t) => acc + t.valor, 0);
            const saidas = transacoes.filter(t => t.tipo === 'saida').reduce((acc, t) => acc + t.valor, 0);
            const saldo = entradas - saidas;

            document.getElementById('total-entradas').textContent = formatarMoeda(entradas);
            document.getElementById('total-saidas').textContent = formatarMoeda(saidas);
            const saldoEl = document.getElementById('saldo-total');
            saldoEl.textContent = formatarMoeda(saldo);
            document.getElementById('card-saldo').className = `bg-white p-5 rounded-2xl shadow-sm border-l-4 ${saldo >= 0 ? 'border-green-500' : 'border-red-500'}`;
            
            localStorage.setItem('financas_transacoes', JSON.stringify(transacoes));
        }

        formTransacao.addEventListener('submit', (e) => {
            e.preventDefault();
            transacoes.push({
                id: Date.now(),
                descricao: document.getElementById('descricao').value,
                valor: +document.getElementById('valor').value,
                tipo: document.getElementById('tipo').value
            });
            formTransacao.reset();
            atualizarPainelFinanceiro();
        });

        function removerTransacao(id) {
            if (confirm('Remover esta movimentação?')) {
                transacoes = transacoes.filter(t => t.id !== id);
                atualizarPainelFinanceiro();
            }
        }
        function limparDadosFinanceiros() {
            if (confirm('Apagar todo o histórico financeiro?')) {
                transacoes = [];
                atualizarPainelFinanceiro();
            }
        }

        // ==================== APP 2: CONTAS A PAGAR ====================
        let bills = JSON.parse(localStorage.getItem('bills')) || [];
        const billsContainer = document.getElementById('bills-container');
        const billsEmptyState = document.getElementById('bills-empty-state');
        const billModal = document.getElementById('modal-add-bill');
        const billModalContent = document.getElementById('modal-bill-content');

        function renderBills() {
            billsContainer.innerHTML = '';
            if (bills.length === 0) {
                billsEmptyState.classList.remove('hidden');
                billsEmptyState.classList.add('flex');
            } else {
                billsEmptyState.classList.add('hidden');
                billsEmptyState.classList.remove('flex');
            }

            bills.sort((a, b) => {
                if (a.paid === b.paid) return new Date(a.dueDate) - new Date(b.dueDate);
                return a.paid ? 1 : -1;
            });

            bills.forEach((bill, index) => {
                const today = new Date(); today.setHours(0,0,0,0);
                const dueDate = new Date(bill.dueDate + 'T00:00:00');
                const diffDays = Math.ceil((today - dueDate) / (1000 * 60 * 60 * 24));
                const isOverdue = !bill.paid && diffDays > 0;

                let statusColor = isOverdue ? 'bg-red-50 border-red-500' : (bill.paid ? 'bg-green-50 border-green-500' : 'bg-white border-yellow-400');
                let statusText = bill.paid ? 'Pago' : (isOverdue ? `Atrasado ${diffDays} dias` : 'Pendente');
                let statusTextColor = isOverdue ? 'text-red-600' : (bill.paid ? 'text-green-600' : 'text-yellow-600');

                const card = document.createElement('div');
                card.className = `rounded-2xl shadow-sm border-l-4 p-4 transition-all ${statusColor}`;
                card.innerHTML = `
                    <div class="flex items-start justify-between">
                        <div class="flex-grow">
                            <h3 class="font-bold text-gray-800 text-lg">${bill.name}</h3>
                            <p class="text-gray-600 font-semibold">${formatarMoeda(parseFloat(bill.value))}</p>
                             <p class="text-sm ${statusTextColor} font-medium mt-1 flex items-center">
                                <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-1" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z" />
                                </svg>
                                ${new Date(bill.dueDate).toLocaleDateString('pt-BR', {timeZone: 'UTC'})} • ${statusText}
                            </p>
                        </div>
                         <label class="relative inline-flex items-center cursor-pointer ml-4">
                            <input type="checkbox" class="sr-only peer" ${bill.paid ? 'checked' : ''} onchange="toggleBillPaid(${index}, this.checked)">
                            <div class="w-11 h-6 bg-gray-200 peer-focus:outline-none rounded-full peer peer-checked:after:translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-white after:border-gray-300 after:border after:rounded-full after:h-5 after:w-5 after:transition-all peer-checked:bg-blue-600"></div>
                        </label>
                    </div>
                    <div class="${bill.paid ? 'block' : 'hidden'} mt-4 pt-3 border-t border-gray-200/50 grid gap-3" id="pay-details-${index}">
                        <div class="grid grid-cols-2 gap-3">
                            <div><label class="text-xs text-gray-500">Pago em</label><input type="date" value="${bill.paidDate || ''}" onchange="updateBill(${index}, 'paidDate', this.value)" class="w-full p-2 text-sm bg-white border rounded-lg"></div>
                            <div><label class="text-xs text-gray-500">Banco</label><input type="text" placeholder="Qual banco?" value="${bill.paidBank || ''}" onchange="updateBill(${index}, 'paidBank', this.value)" class="w-full p-2 text-sm bg-white border rounded-lg"></div>
                        </div>
                        <button onclick="deleteBill(${index})" class="text-red-500 text-xs flex items-center justify-end mt-1 hover:text-red-700"><svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-1" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" /></svg> Excluir</button>
                    </div>
                `;
                billsContainer.appendChild(card);
            });
            localStorage.setItem('bills', JSON.stringify(bills));
        }

        function handleAddBill(e) {
            e.preventDefault();
            bills.push({
                name: document.getElementById('bill-name').value,
                value: document.getElementById('bill-value').value,
                dueDate: document.getElementById('bill-due').value,
                paid: false, paidDate: null, paidBank: ''
            });
            document.getElementById('add-bill-form').reset();
            closeBillModal();
            renderBills();
        }

        function toggleBillPaid(index, isPaid) {
            bills[index].paid = isPaid;
            if (isPaid && !bills[index].paidDate) bills[index].paidDate = new Date().toISOString().split('T')[0];
            renderBills();
        }
        function updateBill(index, field, value) {
            bills[index][field] = value;
            localStorage.setItem('bills', JSON.stringify(bills));
        }
        function deleteBill(index) {
            if(confirm('Excluir esta conta?')) { bills.splice(index, 1); renderBills(); }
        }
        function openBillModal() {
            billModal.classList.remove('hidden');
            billModal.classList.add('flex');
            setTimeout(() => billModalContent.classList.remove('translate-y-full'), 10);
        }
        function closeBillModal() {
            billModalContent.classList.add('translate-y-full');
            setTimeout(() => { billModal.classList.add('hidden'); billModal.classList.remove('flex'); }, 300);
        }
        billModal.addEventListener('click', (e) => { if (e.target === billModal) closeBillModal(); });

        // INICIALIZAÇÃO
        atualizarPainelFinanceiro();
        renderBills();
    </script>
</body>
</html>
