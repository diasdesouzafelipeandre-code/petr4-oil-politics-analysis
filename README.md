# Análise comparativa entre PETR4 e o preço do petróleo (versão corrigida)

Este repositório contém a análise final baseada em `Comparação_corrigido.ipynb`. O trabalho começou como uma prática de manipulação e visualização com `pandas` e evoluiu para uma investigação estatística mais cuidadosa.

## Objetivo

Verificar se os movimentos de PETR4 acompanham as variações do preço do petróleo (WTI) e identificar períodos de divergência. A versão corrigida aplica alinhamento por data, normalização e análise em retornos, além de testes de estacionariedade e causalidade.

## Diferença entre as abordagens

- Abordagem inicial (exploratória/prática): manipulação e visualização das séries com `pandas`, sem verificação rigorosa de alinhamento ou estacionariedade. Útil para aprendizado e descoberta inicial.
- Abordagem atual (`Comparação_corrigido.ipynb`): correções metodológicas — merge por `Date`, normalização por máximo, cálculo de retornos, teste ADF, escolha de lag via VAR e testes de Granger em ambas as direções. Esta versão é a base das conclusões do projeto.

## Metodologia resumida (versão corrigida)

1. Carregamento dos dados de `DATA_SET/`.
2. Alinhamento das séries por `Date` (inner join).
3. Normalização das séries pelo máximo para comparação relativa.
4. Cálculo de retornos diários (`pct_change()`) e verificação de estacionariedade com ADF.
5. Seleção de lag via `VAR(...).select_order(maxlags=30)`.
6. Teste de causalidade de Granger sobre retornos em ambas as direções.

## Dados utilizados

- `DATA_SET/Global_Commodity_Prices_2000_2026.csv`
- `DATA_SET/Petrobras Historical Stock Data (2000-2026).csv`

## Resultados e conclusão

- As séries normalizadas evidenciam períodos de divergência entre PETR4 e WTI, mas também muitos períodos de comportamento parecido.
- Após as correções metodológicas, a bateria de testes (ADF, VAR, Granger) não forneceu evidência robusta de causalidade direta entre WTI e PETR4. A hipótese inicial de causalidade clara foi enfraquecida.
- As semelhanças observadas entre as séries são provavelmente influenciadas por fatores externos comuns (ex.: câmbio USD/BRL, condições macroeconômicas, choques de liquidez, eventos corporativos).

## Gráfico central

O gráfico principal do estudo são as séries normalizadas de PETR4 e WTI. Para gerar e salvar esse gráfico a partir do notebook `Comparação_corrigido.ipynb`, execute a célula de plot e, em seguida, salve com:

```python
plt.savefig('Images/series_normalizadas.png', dpi=150, bbox_inches='tight')
```

O arquivo resultante ficará em `Images/series_normalizadas.png`.

## Visualizações

### Séries normalizadas (PETR4 vs WTI)

![Séries normalizadas](Images/series_normalizadas.png)


## Próximos passos recomendados

- Investigar variáveis externas comuns (ex.: câmbio USD/BRL) como potenciais drivers das semelhanças entre as séries.
- Incluir controles macroeconômicos e modelos multivariados que incorporem fatores exógenos.
- Realizar análises qualitativas dos eventos em datas de maior divergência para entender causas específicas.

## Como executar

1. Instale dependências: `pip install -r requirements.txt`
2. Abra `Comparação_corrigido.ipynb` em Jupyter ou VS Code e execute as células.
3. Gere e salve o gráfico com `plt.savefig(...)` se desejar um arquivo de imagem.

---
Esta versão relata explicitamente as diferenças entre a abordagem inicial e a análise corrigida e conclui que a relação de causalidade é fraca; os próximos passos devem focar em fatores externos compartilhados.