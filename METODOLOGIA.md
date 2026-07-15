# Metodologia

## 1. Coleta e preparação dos dados

Os preços do WTI são extraídos de `Global_Commodity_Prices_2000_2026.csv`, filtrando a linha `Commodity == "Crude Oil WTI"` e mantendo apenas `Date` e `Close`. Os preços da PBR vêm de `Petrobras Historical Stock Data (2000-2026).csv`, mantendo `Date`, `Close` e `Open`; datas inválidas são descartadas (`errors="coerce"` + `dropna`).

As duas séries são unidas por data com `inner join`, de modo que a base final (`merged`) contém apenas os dias em que há cotação simultânea de PBR e WTI. Essa escolha reduz a amostra em relação a um `outer join`, mas evita problemas de dados faltantes nas etapas seguintes.

## 2. Definição dos regimes

Os regimes são atribuídos por corte de data, usando `np.select`:

- **Regime 0** — antes de 01/10/2016: período intervencionista.
- **Regime 1** — de 01/10/2016 a 31/12/2022: vigência da Política de Paridade de Importação (PPI).
- **Regime 2** — a partir de 01/01/2023: novo modelo de precificação.

Essa segmentação é a base de toda a análise comparativa que segue.

## 3. Normalização e inspeção visual

Antes de qualquer teste estatístico, as séries são normalizadas de duas formas para inspeção visual:

- **Base 100**: cada série dividida pelo seu primeiro valor e multiplicada por 100, permitindo comparar variação percentual acumulada desde o início da amostra.
- **Min-max**: cada série reescalada para o intervalo [0, 1], `(x - min) / (max - min)`.

Ambas são plotadas ao longo do tempo. Essas etapas são exploratórias — não entram nos testes formais — e servem para identificar visualmente possíveis períodos de descolamento entre os ativos antes de testar isso formalmente.

## 4. Cálculo de retornos

Os retornos diários são calculados como variação percentual simples (`pct_change()`) sobre o preço de fechamento de cada ativo, gerando `pbr_ret` e `WTI_ret`. Os retornos, e não os níveis de preço, são a variável usada nas regressões e nos testes de estacionariedade em diferenças — prática padrão em séries financeiras, já que preços em nível costumam ser não-estacionários.

## 5. Teste de estacionariedade (ADF)

O teste Augmented Dickey-Fuller é aplicado a quatro séries:

| Série | Estatística ADF | p-valor | Estacionária a 5%? |
|---|---|---|---|
| PBR normalizada (nível) | -0,90 | 0,787 | Não |
| WTI normalizado (nível) | -2,88 | 0,048 | Sim (no limite) |
| Retorno PBR | -19,16 | <0,0001 | Sim |
| Retorno WTI | -14,78 | <0,0001 | Sim |

Os níveis de preço não são claramente estacionários (esperado para séries de preços de ativos), enquanto os retornos são estacionários com folga — o que justifica usar retornos, e não níveis, nas regressões da seção 7.

## 6. Teste de cointegração (Johansen)

O teste de Johansen (traço, `det_order=0`, `k_ar_diff=5`) é aplicado aos níveis de PBR e WTI, tanto na amostra completa quanto separadamente em cada regime, comparando a estatística de traço ao valor crítico de 95%:

| Amostra | Estatística de traço | Valor crítico (95%) | Cointegração? |
|---|---|---|---|
| Completa | 13,00 | 15,49 | Não |
| Regime 0 | 15,19 | 15,49 | Não (muito próximo do limite) |
| Regime 1 | 11,42 | 15,49 | Não |
| Regime 2 | 9,31 | 15,49 | Não |

Em nenhum caso a hipótese nula de "não cointegração" é rejeitada a 95% — embora o Regime 0 fique muito perto do limiar, o que sugere cautela em vez de uma conclusão definitiva sobre esse subperíodo. Isso significa que não há evidência de uma relação de equilíbrio de longo prazo entre os níveis de preço de PBR e WTI; a relação explorada a seguir é sobre retornos (curto prazo), não sobre os níveis.

## 7. Regressão OLS por regime

Para cada regime (e para a amostra completa), é estimado um modelo OLS simples:

`pbr_ret = α + β · WTI_ret + ε`

Resultados:

| Amostra | N | β (WTI_ret) | R² | p-valor de β |
|---|---|---|---|---|
| Completa | 6.405 | 0,139 | 0,048 | <0,001 |
| Regime 0 (intervencionista) | 4.038 | 0,510 | 0,149 | <0,001 |
| Regime 1 (PPI) | 1.571 | 0,061 | 0,027 | <0,001 |
| Regime 2 (novo modelo) | 796 | 0,406 | 0,167 | <0,001 |

Em todos os regimes, β é positivo e estatisticamente significativo — PBR acompanha o WTI em todos os períodos. O que muda é a magnitude: a sensibilidade cai de forma acentuada durante a PPI (β ≈ 0,06) frente aos outros dois regimes (β ≈ 0,41–0,51), consistente com a leitura de que, sob paridade de importação, o repasse do preço internacional do petróleo ao preço doméstico dos combustíveis (e, por consequência, à ação) segue uma lógica mais direta e mecânica, enquanto nos períodos intervencionista e mais recente a relação de curto prazo entre retornos é mais forte — hipótese que este notebook não testa causalmente, apenas descreve via associação linear.

## 8. Teste de quebra estrutural (Chow)

Para verificar formalmente se o coeficiente β difere entre os regimes 0 e 1, é aplicado um teste de Chow: compara-se a soma dos quadrados dos resíduos (RSS) de um modelo único estimado sobre os dois regimes agrupados (excluindo o regime 2) contra a soma das RSS dos modelos estimados separadamente para cada regime.

```
F = [(RSS_total − (RSS₀ + RSS₁)) / k] / [(RSS₀ + RSS₁) / (n₀ + n₁ − 2k)]
```

com `k = 2` (número de parâmetros: intercepto e inclinação).

Resultado: **F ≈ 217,66, p ≈ 1,1 × 10⁻¹⁶**. A hipótese nula de coeficientes iguais entre os regimes 0 e 1 é rejeitada com folga, confirmando estatisticamente que a mudança de regime de precificação coincide com uma mudança estrutural na relação entre os retornos de PBR e WTI.

## 9. Limitações metodológicas

- **Regressão univariada**: o modelo usa apenas WTI_ret como regressor. Fatores conhecidos por afetar PBR — câmbio (USD/BRL), risco-país, juros, notícias específicas da empresa — não são controlados, então parte da variação atribuída ao WTI pode estar capturando esses fatores correlacionados.
- **Erros-padrão não-robustos**: os resultados de OLS reportados usam erros-padrão clássicos (`nonrobust`). Os resíduos mostram curtose elevada e assimetria em todos os modelos (estatísticas de Jarque-Bera altamente significativas), o que sugere que erros-padrão robustos a heterocedasticidade (HC1/HC3) dariam intervalos de confiança mais confiáveis.
- **Cortes de regime por data de anúncio de política**: os limites de regime seguem datas de mudança de política pública, não necessariamente o momento em que o mercado precificou essa mudança — pode haver defasagem não capturada.
- **Causalidade de Granger fora deste notebook**: este notebook identifica associação (via regressão) e quebra estrutural (via Chow), mas não testa formalmente causalidade de Granger nem estima um modelo VAR aqui. O teste de causalidade de Granger foi feito no notebook de backup exploratório (`backup_exploratório`) e ficou de fora deste documento principal por não ter apresentado resultados estatisticamente significativos.
