# Metodologia revisada e conclusões

## Objetivo

Comparar PETR4 e WTI de forma metodologicamente consistente, identificando divergências e avaliando evidências de relação entre as séries.

## Duas fases do trabalho

- **Fase 1 — prática/exploração:** leitura e visualização das séries com `pandas` — bom para aprender e inspecionar dados, mas sujeito a vieses (ex.: desalinhamento por posição).
- **Fase 2 — análise corrigida:** `Comparação_corrigido.ipynb` aplica correções essenciais: merge por data, normalização, análise em retornos, teste ADF, seleção de lag via VAR e testes de Granger em ambas as direções.

## Procedimento (versão corrigida)

1. Carregamento e inspeção dos arquivos em `DATA_SET/`.
2. Alinhamento por `Date` via `pd.merge(..., on='Date', how='inner')`.
3. Normalização das séries pelo máximo de cada série.
4. Cálculo de retornos diários (`pct_change()`) para análise estacionária.
5. Teste ADF em níveis e retornos para verificar estacionariedade.
6. Seleção de defasagem com `VAR(...).select_order(maxlags=30)`.
7. Teste de Granger sobre retornos, nas duas direções.

## Resultados principais

- A versão corrigida elimina problemas metodológicos da análise inicial (merge por posição; Granger em níveis não estacionários; direção de teste invertida).
- As séries normalizadas mostram padrões semelhantes em muitos períodos e divergências em outros; essas divergências foram investigadas qualitativamente.
- A bateria de testes não apresentou evidência robusta de causalidade direta entre WTI e PETR4. A hipótese inicial de causalidade clara foi enfraquecida.

## Interpretação e limitações

- Significância em testes de Granger pode refletir fatores externos comuns (ex.: câmbio USD/BRL) ou choques simultâneos, não causalidade direta.
- O estudo é exploratório: conclusões causais exigiriam modelos com controles adicionais, dados exógenos e testagem estrutural.

## Próximos passos recomendados

- Investigar variáveis comuns (ex.: câmbio) como potenciais drivers das semelhanças entre séries.
- Construir modelos multivariados que incluam fatores macroeconômicos e medidas de risco.
- Analisar qualitativamente as datas de maior divergência para identificar eventos específicos.

### Séries normalizadas (PETR4 vs WTI)

![Séries normalizadas](Images/series_normalizadas.png)

## Conclusão

A hipótese inicial de causalidade direta entre WTI e PETR4 não foi confirmada de forma robusta. A análise corrigida (segunda fase) é metodologicamente mais adequada e sugere que semelhanças entre as séries podem ser explicadas por fatores externos comuns; pesquisas futuras devem focar nesses fatores.