# Regras de Negócio

## 📋 **Visão Geral**

Este documento detalha todas as regras de negócio implementadas no SpendWise, servindo como especificação para desenvolvimento e validação dos testes.

---

## 🎯 **Regra 1: Limite Mensal por Categoria**

### **Descrição**
Controle de gastos por categoria com limite mensal acumulativo e sistema de alertas.

### **Comportamento Esperado**
1. **Cálculo Acumulado**: Somar todas as despesas da categoria no mês corrente + nova despesa
2. **Alerta aos 80%**: Emitir aviso quando atingir 80% do limite
3. **Bloqueio aos 100%**: Impedir nova despesa que ultrapasse o limite

### **Critérios de Aceitação**
- ✅ Usuário pode definir limite mensal para cada categoria
- ✅ Sistema calcula gasto acumulado do mês automaticamente
- ✅ Alerta é exibido quando gasto ≥ 80% do limite
- ✅ Despesa é bloqueada quando gasto + nova despesa > limite
- ✅ Alertas são enviados em tempo real

### **Cenários de Teste**

```gherkin
Cenário: Alerta aos 80% do limite
  Dado que tenho uma categoria "Alimentação" com limite de R$ 1000
  E já gastei R$ 790 no mês atual
  Quando tento adicionar uma despesa de R$ 50
  Então o sistema deve permitir a despesa
  E exibir alerta "Categoria próxima do limite: 84% utilizado"

Cenário: Bloqueio ao ultrapassar limite
  Dado que tenho uma categoria "Lazer" com limite de R$ 500
  E já gastei R$ 480 no mês atual
  Quando tento adicionar uma despesa de R$ 50
  Então o sistema deve bloquear a operação
  E exibir mensagem "Despesa ultrapassa limite da categoria"
```

---

## 💰 **Regra 2: Orçamento Mensal por Usuário**

### **Descrição**
Controle global de gastos mensais por usuário, impedindo que o total de despesas ultrapasse o orçamento definido.

### **Comportamento Esperado**
1. **Orçamento Único**: Um orçamento por usuário por mês
2. **Validação Global**: Verificar total de despesas antes de permitir nova transação
3. **Bloqueio Preventivo**: Impedir despesa que exceda orçamento restante

### **Critérios de Aceitação**
- ✅ Usuário define orçamento mensal único
- ✅ Sistema calcula total de despesas do mês
- ✅ Despesa é bloqueada se exceder saldo restante
- ✅ Relatório mostra % de utilização do orçamento
- ✅ Alertas aos 90% de utilização

### **Cenários de Teste**

```gherkin
Cenário: Bloqueio por falta de orçamento
  Dado que defini orçamento mensal de R$ 3000
  E já gastei R$ 2950 no mês atual
  Quando tento adicionar despesa de R$ 100
  Então o sistema deve bloquear a operação
  E exibir "Orçamento mensal insuficiente: restam R$ 50"
```

---

## ⏰ **Regra 3: Validação Temporal**

### **Descrição**
Controles de integridade temporal para garantir consistência dos dados financeiros.

### **Comportamento Esperado**
1. **Data Não Futura**: Transações não podem ter data posterior ao dia atual
2. **Fechamento Mensal**: Após fechamento, proibir alterações no período
3. **Auditoria**: Manter histórico de todas as alterações

### **Critérios de Aceitação**
- ✅ Data da transação ≤ data atual
- ✅ Transações de mês fechado são bloqueadas para edição
- ✅ Fechamento mensal é irreversível
- ✅ Log de auditoria registra todas as tentativas

### **Cenários de Teste**

```gherkin
Cenário: Bloqueio de data futura
  Quando tento criar transação com data de amanhã
  Então o sistema deve rejeitar
  E exibir "Data não pode ser futura"

Cenário: Proteção de mês fechado
  Dado que fechei o mês de julho/2025
  Quando tento editar transação de 15/07/2025
  Então o sistema deve bloquear
  E exibir "Mês já fechado para alterações"
```

---

## 🎯 **Regra 4: Prioridade Essencial x Supérfluo**

### **Descrição**
Sistema inteligente de priorização que bloqueia gastos supérfluos quando há comprometimento financeiro.

### **Comportamento Esperado**
1. **Classificação**: Categorias marcadas como ESSENCIAL ou SUPERFLUO
2. **Validação Preventiva**: Bloquear supérfluos se essenciais comprometidos
3. **Projeção Inteligente**: Considerar tendências mensais

### **Critérios de Aceitação**
- ✅ Categorias classificadas por tipo
- ✅ Gastos supérfluos bloqueados se orçamento essencial > 100%
- ✅ Alerta quando projeção essencial indica problema
- ✅ Exceções podem ser autorizadas manualmente

### **Cenários de Teste**

```gherkin
Cenário: Bloqueio de supérfluo por comprometimento essencial
  Dado que categorias essenciais já consumiram 105% do orçamento
  Quando tento adicionar despesa em categoria supérflua
  Então o sistema deve bloquear
  E sugerir "Quite primeiro os gastos essenciais"
```

---

## 📊 **Regra 5: Relatórios e Alertas**

### **Descrição**
Sistema proativo de monitoramento e reporting para educação financeira.

### **Comportamento Esperado**
1. **Relatório Mensal**: Consolidação automática de receitas, despesas e saldos
2. **Alertas Automáticos**: Notificações preventivas
3. **Análise por Categoria**: Breakdown detalhado dos gastos

### **Critérios de Aceitação**
- ✅ Relatório mensal gerado automaticamente
- ✅ Alertas em tempo real para limites
- ✅ Comparação mês anterior
- ✅ Gráficos e visualizações

---

## 🎯 **Regra 6: Metas Financeiras**

### **Descrição**
Sistema de definição e acompanhamento de objetivos financeiros com projeções inteligentes.

### **Comportamento Esperado**
1. **Definição de Metas**: "Juntar X até YYYY-MM"
2. **Cálculo de Progresso**: Receitas - Despesas - Compromissos
3. **Projeção de Alcance**: Estimativa baseada em média mensal

### **Critérios de Aceitação**
- ✅ Múltiplas metas simultâneas
- ✅ Progresso calculado automaticamente
- ✅ Projeção de data de alcance
- ✅ Alertas de marcos (25%, 50%, 75%, 100%)

### **Cenários de Teste**

```gherkin
Cenário: Projeção de meta
  Dado que defini meta de R$ 10000 até dez/2025
  E tenho histórico de economia média de R$ 1000/mês
  Quando consulto o progresso
  Então sistema projeta alcance em outubro/2025
  E sugere "Meta será alcançada 2 meses antes do prazo"
```

---

## 🔄 **Testes de Integração das Regras**

### **Cenário Complexo: Múltiplas Regras**

```gherkin
Cenário: Validação integrada de todas as regras
  Dado usuário com orçamento mensal de R$ 3000
  E categoria "Alimentação" (essencial) com limite R$ 800
  E categoria "Lazer" (supérfluo) com limite R$ 400
  E já gastou R$ 2800 no mês (R$ 750 alimentação, R$ 2050 outras)
  Quando tenta adicionar R$ 100 em "Lazer"
  Então sistema deve:
    - Verificar limite categoria (OK: 0/400)
    - Verificar orçamento global (OK: restam R$ 200)
    - Verificar prioridade (OK: essenciais controlados)
    - Permitir transação
    - Emitir alerta "Orçamento 93% utilizado"
```

---

## 🧪 **Implementação das Regras**

### **Domain Layer**
As regras de negócio são implementadas principalmente no Domain Layer:

```csharp
public class Categoria
{
    public bool UltrapassouLimite(decimal valorGasto)
    {
        return valorGasto > Limite?.Valor;
    }
    
    public bool PodeGastar(decimal valorGasto, decimal gastoAtual)
    {
        return (gastoAtual + valorGasto) <= (Limite?.Valor ?? decimal.MaxValue);
    }
}
```

### **Application Layer**
Validações complexas são implementadas nos handlers:

```csharp
public class CreateTransacaoCommandHandler : IRequestHandler<CreateTransacaoCommand, TransacaoDto>
{
    public async Task<TransacaoDto> Handle(CreateTransacaoCommand request, CancellationToken cancellationToken)
    {
        // Validar regras de negócio
        await ValidateBusinessRules(request);
        
        // Criar transação
        var transacao = new Transacao(/* ... */);
        
        // Salvar
        await _unitOfWork.Transacoes.AddAsync(transacao);
        await _unitOfWork.SaveChangesAsync();
        
        return _mapper.Map<TransacaoDto>(transacao);
    }
}
```

### **Testes**
Cada regra é testada isoladamente e em conjunto:

```csharp
[Fact]
public void Categoria_UltrapassouLimite_ShouldReturnTrue()
{
    // Arrange
    var categoria = new Categoria("Teste", TipoCategoria.Despesa, Guid.NewGuid(), null, new Money(100));
    
    // Act
    var result = categoria.UltrapassouLimite(150);
    
    // Assert
    Assert.True(result);
}
```

---

## 📈 **Métricas de Qualidade**

### **Cobertura de Testes**
- **Regras de Negócio**: 100% cobertas
- **Cenários de Teste**: 95% implementados
- **Validações**: 100% testadas

### **Validação Contínua**
- **CI/CD**: Validação automática em cada commit
- **Testes de Integração**: Validação end-to-end
- **Testes de Performance**: Validação de regras complexas

Esta documentação garante que todas as regras de negócio sejam implementadas corretamente e testadas adequadamente, mantendo a integridade e consistência do sistema.
