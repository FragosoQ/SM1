# Testes para Funcionalidade PSMulti com Múltiplos Slots

## Alterações Implementadas

### 1. Mudança de Sheet (PS1 → PSMulti)
- **Arquivo**: `scripts/charts.js`
- **Alteração**: `chartConfig.sheetName` mudou de 'PS1' para 'PSMulti'

### 2. Nova Função: `fetchAllSlotsData()`
Detecta quais linhas contêm "Slot_1_Em Curso" ou "Slot_2_Em Curso" na coluna STATUS (AJ).

**Lógica**:
- Itera sobre todas as linhas da sheet PSMulti
- Procura no índice 35 (coluna AJ - STATUS)
- Extrai o valor de Lote1 (coluna AK - índice 36)
- Retorna array com slots encontrados

**Exemplo de Retorno**:
```javascript
[
  { slotNumber: 1, rowIndex: 2, loteId: 'ABC123' },
  { slotNumber: 2, rowIndex: 3, loteId: 'DEF456' }
]
```

### 3. Modificação: `fetchPercentage(columnName, rowNumber)`
- **Antes**: Usava apenas `chartConfig.posto` para determinar a linha
- **Depois**: Aceita um parâmetro `rowNumber` para buscar dados de linhas específicas

### 4. Nova Função: `drawMultipleDonutCharts(containerId, chartsData)`
Desenha múltiplos gráficos side-by-side num container.

**Características**:
- Flexbox layout para organizar múltiplos gráficos
- Suporta 1 ou 2 gráficos
- Mostra ID do Lote1 abaixo de cada gráfico
- Cores diferentes: cor original para Slot_1, amarelo (#FFD700) para Slot_2

### 5. Modificação: `updateAllCharts()`
Nova lógica para determinar qual desenhar:

```
Se não existem slots:
  → Desenha 1 gráfico usando linha 2 como fallback

Se existe 1 slot:
  → Desenha 1 gráfico usando os dados desse slot

Se existem 2+ slots:
  → Desenha múltiplos gráficos (um para cada slot)
  → Slot_1 usa cor original
  → Slot_2 usa cor amarela
```

## Testes Esperados

### Cenário 1: Sem Slots
- **Condição**: Nenhuma linha contém "Slot_1_Em Curso" ou "Slot_2_Em Curso"
- **Resultado**: Mostra 1 gráfico por container (mesmo que antes)

### Cenário 2: Um Slot (Slot_1)
- **Condição**: Uma linha contém "Slot_1_Em Curso"
- **Resultado**: Mostra 1 gráfico por container com dados desse slot

### Cenário 3: Dois Slots
- **Condição**: Uma linha com "Slot_1_Em Curso" e outra com "Slot_2_Em Curso"
- **Resultado**: Mostra 2 gráficos side-by-side em cada container
  - Esquerda: Slot_1 (cor original)
  - Direita: Slot_2 (amarelo)
  - Ambos mostram ID do Lote1

## Mapeamento de Colunas (Corrigido)
```
Coluna AC (índice 28) = CUBA
Coluna AD (índice 29) = ESTRUTURA
Coluna AE (índice 30) = INTERIOR
Coluna AF (índice 31) = ENVOLVENTES
Coluna AJ (índice 35) = STATUS
Coluna AK (índice 36) = Lote1
```

## Validação no Navegador

1. Abrir console do navegador (F12)
2. Verificar logs de `fetchAllSlotsData()`:
   - Deve mostrar quantos slots foram encontrados
   - Deve listar rowIndex e loteId de cada slot

3. Verificar que os gráficos renderizam corretamente:
   - Com 1 slot: 1 gráfico simples
   - Com 2 slots: 2 gráficos lado a lado

## Notas de Implementação

- A função `drawMultipleDonutCharts()` usa Flexbox para organizar os gráficos
- O tamanho de cada gráfico é calculado dinamicamente baseado no container
- Cores do Slot_2: `#FFD700` (amarelo)
- As funções mantêm retro-compatibilidade com a lógica anterior (fallback para PS1 ainda é suportado)
