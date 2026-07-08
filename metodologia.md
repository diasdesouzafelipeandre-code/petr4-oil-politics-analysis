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
## Descoberta técnica: o artefato do preço negativo do petróleo

Durante a fase de correção, um resultado da direção "PETR4 causa WTI" apareceu extremamente significativo (p ≈ 0,0000 em várias defasagens) — magnitude estatisticamente implausível para uma única ação prever o contrato futuro de petróleo mais líquido do mundo. A investigação desse resultado revelou um problema técnico específico, não uma relação real:

Em 20/04/2020, o WTI fechou em **-US$ 37,63** (primeiro preço negativo de petróleo da história). Como `pct_change()` calcula `(preço_hoje − preço_ontem) / preço_ontem`, o cruzamento de um preço positivo para um negativo produz retornos sem significado econômico — por exemplo, um "retorno" de **-306%** em 20/04/2020 e de **-127%** no dia seguinte, quando na prática o petróleo *subiu* de -37,63 para 10,01. Esse artefato aritmético inflou o desvio-padrão da série de retornos do WTI de ~2,3% (valor típico) para ~4,8% — quase o dobro — concentrado numa janela de poucos dias, o suficiente para dominar um teste F mesmo numa amostra de mais de 6 mil observações.

Excluir a janela de mar–mai/2020 reduziu o desvio-padrão de volta a ~2,3%, mas o resultado "PETR4 causa WTI" **permaneceu significativo** (p ≈ 0,0000, F ≈ 30) mesmo sem o outlier — o que descartou a hipótese de que o choque de 2020 era a explicação. A causa real era outra: o lag usado no teste (lag 2) havia sido escolhido pelo critério BIC calculado *antes* da limpeza dos dados. Recalculando a seleção de lag nos dados limpos, o BIC passou a apontar **lag 0** — ou seja, nenhuma defasagem sustenta estrutura de causalidade nos dados corrigidos.

### Antes e depois das correções

| Teste | Versão inicial | Versão corrigida |
|---|---|---|
| Merge das séries | por posição (`reset_index`) | por `Date` (`pd.merge(..., how='inner')`) |
| ADF — níveis normalizados | não testado | não estacionário (tendência presente) |
| ADF — retornos (`pct_change`) | não testado | estacionário |
| Seleção de lag (VAR + BIC) | não aplicada | **lag 0** nos dados limpos |
| Granger, WTI → PETR4 (lag do BIC) | p = 0,0053 (lag 1, níveis, merge por posição) | p = 0,83 (lag 2, retornos, merge por data) |
| Granger, PETR4 → WTI (lag 1–2, dados limpos) | não testado | p ≈ 0,0000 — investigado e não explicado por outlier isolado; permanece sem justificativa econômica plausível |

### O que essa descoberta significa para as conclusões

O resultado "PETR4 causa WTI" não é um artefato do preço negativo de abril/2020 — ele resiste à remoção dessa janela. Isso o torna um achado que precisa de investigação adicional (possível heterocedasticidade não testada formalmente via ARCH, ou uma variável comum não incluída no modelo, como câmbio USD/BRL) antes de ser descartado ou aceito. Por prudência, ele **não é tratado como evidência de causalidade real** neste projeto, dado que não há mecanismo econômico plausível para uma única ação prever o mercado global de petróleo com essa magnitude.

## Interpretação e limitações

- Significância em testes de Granger pode refletir fatores externos comuns (ex.: câmbio USD/BRL) ou choques simultâneos, não causalidade direta.
- O estudo é exploratório: conclusões causais exigiriam modelos com controles adicionais, dados exógenos e testagem estrutural.
- Não foi aplicado teste formal de heterocedasticidade (ex.: ARCH-LM) sobre os retornos. A robustez dos resultados de Granger a clusters de volatilidade — um padrão comum em séries financeiras e sugerido pela própria natureza dos outliers encontrados — é uma limitação conhecida e não resolvida nesta versão da análise.

## Próximos passos recomendados

- Investigar variáveis comuns (ex.: câmbio) como potenciais drivers das semelhanças entre séries.
- Construir modelos multivariados que incluam fatores macroeconômicos e medidas de risco.
- Analisar qualitativamente as datas de maior divergência para identificar eventos específicos.

### Séries normalizadas (PETR4 vs WTI)

![Séries normalizadas](Images/series_normalizadas.png)

## Conclusão

A hipótese inicial de causalidade direta entre WTI e PETR4 não foi confirmada de forma robusta. A análise corrigida (segunda fase) é metodologicamente mais adequada e sugere que semelhanças entre as séries podem ser explicadas por fatores externos comuns; pesquisas futuras devem focar nesses fatores.