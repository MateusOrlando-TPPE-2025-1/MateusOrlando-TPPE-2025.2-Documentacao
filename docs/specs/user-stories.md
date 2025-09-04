# User Stories - SpendWise

> **Visão**: App de finanças pessoais para registrar receitas/despesas, controlar orçamento mensal, fechar meses e analisar relatórios.  
> **Personas**: Usuário final (pessoa física).  
> **Prioridade (MoSCoW)**: Must / Should / Could.

## 📋 **Sumário**

- [E1 — Autenticação & Sessão](#e1--autenticação--sessão)
- [E2 — Categorias](#e2--categorias)
- [E3 — Transações](#e3--transações)
- [E4 — Orçamento Mensal](#e4--orçamento-mensal)
- [E5 — Fechamento Mensal](#e5--fechamento-mensal)
- [E6 — Relatórios](#e6--relatórios)
- [E7 — Metas Financeiras](#e7--metas-financeiras)
- [E8 — Alertas](#e8--alertas)
- [E9 — Observabilidade & Saúde](#e9--observabilidade--saúde)
- [E10 — Infra/DevEx](#e10--infradevex)

---

## 🔐 **E1 — Autenticação & Sessão**

**Objetivo**: permitir acesso seguro ao app (criar conta, login, logout, recuperação).  
**Valor**: segurança e personalização por usuário.  
**Regras**: e-mail único; feedback claro de erros.  
**Dependências**: —  

### Stories

#### SW-001 — Cadastrar conta com e-mail/senha _(Must · 3SP)_
**Descrição**: criar nova conta informando nome, e-mail e senha forte.  
**Critérios de Aceite**
```gherkin
Dado que estou na tela de cadastro
Quando informo nome, email válido e senha forte
Então devo ver minha conta criada e ser autenticado
E devo ver meu nome no topo da aplicação
```

#### SW-002 — Entrar com e-mail/senha _(Must · 2SP)_
**Descrição**: autenticar usuário existente.  
**Critérios**: e-mail case-insensitive; mensagem clara em credenciais inválidas; "lembrar de mim" opcional.

#### SW-003 — Sair da sessão _(Must · 1SP)_
**Descrição**: encerrar sessão atual.  
**Critérios**: limpar token/cookies e redirecionar para /login.

#### SW-004 — Recuperar senha (link por e-mail) _(Should · 5SP)_
**Descrição**: fluxo de reset por link enviado ao e-mail.  
**Critérios**: envio do link (mock no protótipo); confirmação de redefinição.

---

## 🏷️ **E2 — Categorias**

**Objetivo**: organizar **despesas** por tipo e limites.  
**Valor**: visibilidade de gastos e alertas por categoria.  
**Regras**: nome único por usuário; tipo {ESSENCIAL, SUPERFLUO}; limite ≥ 0.  
**Dependências**: E1.

### Stories

#### SW-010 — Criar categoria (nome, tipo, limite opcional) _(Must · 3SP)_
**Critérios**
```gherkin
Dado que estou autenticado
Quando crio uma categoria com nome único no meu usuário e tipo (ESSENCIAL|SUPERFLUO)
Então a categoria aparece na lista
E se informar limite, deve ser >= 0
```

#### SW-011 — Editar categoria _(Must · 2SP)_
**Critérios**: impedir nomes duplicados por usuário; validar tipo/limite.

#### SW-012 — Excluir categoria _(Must · 2SP)_
**Critérios**: confirmar exclusão; orientar sobre despesas vinculadas.

#### SW-013 — Reatribuir despesas ao excluir categoria _(Should · 3SP)_
**Critérios**: oferecer mover despesas para categoria "Outros" (ou escolhida).

#### SW-014 — Visualizar categorias com progresso de limite _(Must · 2SP)_
**Critérios**: mostrar gasto do mês, % usado, badge de >80% e "Ultrapassado".

---

## 💳 **E3 — Transações**

**Objetivo**: CRUD de receitas e despesas com filtros.  
**Valor**: registrar e analisar movimentações.  
**Regras**: valor > 0; data não futura; **despesa exige categoria**; receita não tem categoria.  
**Dependências**: E1, E2.

### Stories

#### SW-030 — Criar despesa (categoria obrigatória) _(Must · 3SP)_
```gherkin
Dado que estou autenticado
E existe ao menos uma categoria
Quando cadastro uma DESPESA com valor > 0, data não futura e categoria selecionada
Então a despesa aparece na lista e atualiza o dashboard
```

#### SW-031 — Criar receita (sem categoria) _(Must · 2SP)_
**Critérios**: não exibir select de categoria; valor > 0; data não futura.

#### SW-032 — Listar/filtrar/paginar transações _(Must · 3SP)_
**Critérios**: filtros por tipo, categoria, intervalo de datas e busca textual; paginação (ou lazy load).

#### SW-033 — Editar transação (mês aberto) _(Must · 3SP)_
**Critérios**: se mês fechado, edição desabilitada com tooltip "Mês fechado"; se aberto, validar regras e salvar.

#### SW-034 — Excluir transação (mês aberto) _(Must · 2SP)_
**Critérios**: confirmação; bloquear se mês fechado.

#### SW-035 — Importar CSV de transações (mock) _(Could · 5SP)_
**Critérios**: upload, pré-visualização, mapeamento simples de colunas (sem persistência real).

---

## 💰 **E4 — Orçamento Mensal**

**Objetivo**: definir teto de gastos do mês e acompanhar uso.  
**Valor**: controle financeiro por período.  
**Regras**: um orçamento por usuário por mês; valor ≥ 0.  
**Dependências**: E1, E3.

### Stories

#### SW-040 — Definir orçamento do mês (único por anoMes) _(Must · 3SP)_
```gherkin
Dado que estou autenticado
Quando defino o orçamento para um mês (valor >= 0)
Então o sistema salva apenas um orçamento por mês por usuário
E o dashboard mostra % usado
```

#### SW-041 — Exibir % do orçamento usado e restante _(Must · 2SP)_
**Critérios**: indicadores no Dashboard e na tela de Orçamento.

---

## 🔒 **E5 — Fechamento Mensal**

**Objetivo**: consolidar o mês e **travar** alterações.  
**Valor**: integridade histórica.  
**Regras**: 1 fechamento por mês por usuário; transações do mês fechado não podem ser editadas/excluídas.  
**Dependências**: E3.

### Stories

#### SW-050 — Fechar mês (consolidar e travar edições) _(Must · 3SP)_
```gherkin
Dado que estou no mês corrente e tenho transações
Quando clico em "Fechar mês" e confirmo
Então o mês fica com status FECHADO
E as transações do mês ficam bloqueadas para edição/exclusão
```

#### SW-051 — Exibir status do mês (Aberto/Fechado) _(Must · 1SP)_
**Critérios**: badge nas telas; estado persiste ao recarregar.

#### SW-052 — Bloquear editar/excluir transação de mês fechado _(Must · 2SP)_
**Critérios**: botões desabilitados + tooltip "Mês fechado"; tentativa retorna erro amigável.

#### SW-053 — Reabrir mês (permissão especial) _(Could · 5SP)_
**Critérios**: fluxo protegido; registrar log/auditoria (mock).

---

## 📊 **E6 — Relatórios**

**Objetivo**: visão analítica por categoria e período.  
**Valor**: decisões informadas sobre gastos.  
**Dependências**: E3, E4.

### Stories

#### SW-060 — Relatório por categoria (mês) _(Should · 3SP)_
**Critérios**: donut/top 5; tabela de totais por categoria.

#### SW-061 — Evolução no mês (linha diária) _(Should · 3SP)_
**Critérios**: gráfico de saldo acumulado por dia.

#### SW-062 — Comparativo de meses _(Could · 5SP)_
**Critérios**: barras por mês; seleção de intervalo; destacar melhor/pior.

#### SW-063 — Exportar CSV/PDF (mock) _(Could · 2SP)_
**Critérios**: botões de export que simulam arquivos.

---

## 🎯 **E7 — Metas Financeiras**

**Objetivo**: definir objetivos (valor-alvo e prazo) e acompanhar progresso.  
**Valor**: incentivo ao planejamento financeiro.  
**Regras**: valor-alvo > 0; prazo futuro.  
**Dependências**: E1, E3 (para progresso).

### Stories

#### SW-070 — Criar meta (descrição, valor-alvo, prazo) _(Should · 3SP)_
**Critérios**: validação de valor e prazo; exibição em listagem.

#### SW-071 — Progresso da meta (acumulado) _(Could · 3SP)_
**Critérios**: percentual alcançado; projeção simples (mock).

#### SW-072 — Editar/excluir meta _(Could · 2SP)_
**Critérios**: permitir ajustes e remoção com confirmação.

---

## 🚨 **E8 — Alertas**

**Objetivo**: avisar usuário sobre limites e orçamento.  
**Valor**: evitar ultrapassar gastos.  
**Dependências**: E2, E3, E4.

### Stories

#### SW-080 — Alerta de categoria > 80% do limite _(Should · 2SP)_
**Critérios**: badge/alert card quando gasto ≥ 80% do limite da categoria.

#### SW-081 — Alerta de orçamento excedido _(Should · 2SP)_
**Critérios**: aviso e destaque visual quando orçamento do mês for excedido.

#### SW-082 — Central de alertas do mês _(Could · 3SP)_
**Critérios**: listagem consolidada com filtros por severidade.

---

## 🔍 **E9 — Observabilidade & Saúde**

**Objetivo**: monitorar disponibilidade e registrar logs.  
**Valor**: diagnóstico rápido e qualidade operacional.  
**Dependências**: —  

### Stories

#### SW-090 — Health check `/health` _(Must · 1SP)_
**Critérios**: responde `200 OK` com liveness/readiness.

#### SW-091 — Logs estruturados (Serilog) _(Must · 2SP)_
**Critérios**: correlação request/response; níveis INFO/WARN/ERROR.

---

## 🚀 **E10 — Infra/DevEx**

**Objetivo**: base de execução, testes e entrega contínua.  
**Valor**: produtividade e estabilidade do time.  
**Dependências**: —  

### Stories

#### SW-100 — Dockerfile multi-stage (API) + Compose dev _(Must · 2SP)_
**Critérios**: subir API + PostgreSQL com `.env`.

#### SW-101 — Pipeline CI (build/test backend e frontend) _(Must · 3SP)_
**Critérios**: GitHub Actions com cache; `dotnet build/test` e `pnpm build/test` no PR.

#### SW-102 — Testes unitários (domínio e aplicação) _(Must · 5SP)_
**Critérios**: cobrir VO Money e regra "Despesa exige Categoria"; ≥ 70% no domínio.

#### SW-103 — Testes de integração com Testcontainers (API + DB) _(Should · 5SP)_
**Critérios**: CRUD + fechamento de mês contra banco real em container.

#### SW-104 — E2E (Playwright) — fluxo crítico _(Should · 5SP)_
**Critérios**: Criar despesa → atingir limite → fechar mês → bloquear edição (todos verdes).

#### SW-105 — Deploy (API container + Front na Vercel) _(Should · 3SP)_
**Critérios**: API publicada em provider de containers; Front na Vercel com URL pública.

---

## 📋 **Apêndice**

### **Definition of Ready (DoR)**
História com benefício claro, AC definidos, sem bloqueios e UI rascunhada.

### **Definition of Done (DoD)**
Código revisado, testes verdes, lint ok, pipeline ok, docs atualizadas e deploy no ambiente de teste.

### **Não Funcionais**
Acessibilidade básica; TTFB razoável em listas; logs estruturados; rate limit simples.

---

## 📊 **Status de Implementação**

### **✅ Implementado (Must)**
- SW-001: Cadastrar conta ✅
- SW-002: Entrar com e-mail/senha ✅
- SW-003: Sair da sessão ✅
- SW-010: Criar categoria ✅
- SW-011: Editar categoria ✅
- SW-012: Excluir categoria ✅
- SW-014: Visualizar categorias com progresso ✅
- SW-030: Criar despesa ✅
- SW-031: Criar receita ✅
- SW-032: Listar/filtrar transações ✅
- SW-033: Editar transação ✅
- SW-034: Excluir transação ✅
- SW-040: Definir orçamento do mês ✅
- SW-041: Exibir % do orçamento ✅
- SW-050: Fechar mês ✅
- SW-051: Exibir status do mês ✅
- SW-052: Bloquear edição de mês fechado ✅
- SW-090: Health check ✅
- SW-091: Logs estruturados ✅
- SW-100: Docker + Compose ✅
- SW-101: Pipeline CI ✅
- SW-102: Testes unitários ✅

### **🔄 Em Desenvolvimento (Should)**
- SW-004: Recuperar senha
- SW-013: Reatribuir despesas
- SW-060: Relatório por categoria
- SW-061: Evolução no mês
- SW-070: Criar meta
- SW-080: Alerta de categoria
- SW-081: Alerta de orçamento
- SW-103: Testes de integração

### **📋 Planejado (Could)**
- SW-035: Importar CSV
- SW-053: Reabrir mês
- SW-062: Comparativo de meses
- SW-063: Exportar CSV/PDF
- SW-071: Progresso da meta
- SW-072: Editar/excluir meta
- SW-082: Central de alertas
- SW-104: E2E Playwright
- SW-105: Deploy produção

---

## 🎯 **Métricas de Progresso**

- **Total de Stories**: 42
- **Implementadas**: 22 (52%)
- **Em Desenvolvimento**: 8 (19%)
- **Planejadas**: 12 (29%)

**Status Geral**: 🟢 **Em Andamento** - Funcionalidades essenciais implementadas, foco em melhorias e funcionalidades avançadas.
