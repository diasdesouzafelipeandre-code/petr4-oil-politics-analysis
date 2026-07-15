# PBR vs WTI: Sensibilidade da Petrobras ao Petróleo sob Diferentes Regimes de Política de Preços

Análise estatística da relação entre o preço das ações da Petrobras (PBR) e o preço do petróleo WTI entre 2000 e 2026, testando se essa relação muda conforme a política de precificação de combustíveis adotada pela empresa ao longo do tempo.

## Objetivo

A Petrobras já operou sob diferentes regras de precificação de combustíveis — de um modelo mais intervencionista a uma política de paridade com o mercado internacional (PPI) e, mais recentemente, um novo modelo a partir de 2023. A hipótese testada aqui é simples: **a sensibilidade do preço da ação da Petrobras às variações do petróleo WTI muda dependendo do regime de política vigente?**

# Datasets Utilizados

## 1. Petrobras Stock Data (2000–2026)

- Fonte: Kaggle
- Autor: alehcleal
- Link: https://www.kaggle.com/datasets/alehcleal/petrobras-stock-data-2000-2026

Descrição:
Contém dados históricos diários das ações da Petrobras.

Licença:
CC BY-SA 4.0

---

## 2. WTI Crude Oil

- Fonte: Kaggle
- Autor: Muhammad Ibrahim Qasmi
- Link: https://www.kaggle.com/datasets/ibrahimqasimi/global-commodity-prices-2000-2026

Descrição:
Contém dados históricos diários das commodity.

Licença:
Apache 2.0

Os dois conjuntos são unidos por data (`inner join`), resultando em uma série com apenas os dias em que ambos os ativos possuem cotação registrada.

## Regimes analisados

| Regime | Período | Descrição |
|---|---|---|
| 0 | até 09/2016 | Período intervencionista |
| 1 | 10/2016 a 12/2022 | Política de Paridade de Importação (PPI) |
| 2 | a partir de 01/2023 | Nova política de preços |

## O que a análise faz

1. Normaliza as séries (base 100 e min-max) para comparação visual.
2. Calcula os retornos diários (variação percentual) de PBR e WTI.
3. Testa a estacionariedade das séries (teste ADF), em nível e em retornos.
4. Testa cointegração entre PBR e WTI (teste de Johansen), para a amostra completa e para cada regime separadamente.
5. Estima a sensibilidade de PBR ao WTI via regressão linear (OLS) dos retornos, por regime.
6. Testa formalmente se essa sensibilidade difere entre regimes (teste de Chow).

Detalhes de cada etapa, incluindo justificativas metodológicas, estão em [METODOLOGIA.md](METODOLOGIA.md).

## Principais resultados

- PBR é significativamente sensível ao WTI em todos os regimes, mas **a magnitude dessa sensibilidade varia bastante**: mais alta no regime intervencionista (coef. ≈ 0,51) e no regime mais recente (coef. ≈ 0,41), e visivelmente mais baixa durante a vigência da PPI (coef. ≈ 0,06).
- O teste de Chow rejeita fortemente a hipótese de que o coeficiente de sensibilidade é igual entre o regime 0 e o regime 1 (F ≈ 217,7; p ≈ 1,1×10⁻¹⁶), confirmando uma quebra estrutural na relação.
- Não foi encontrada cointegração entre PBR e WTI em nível (nem na amostra completa, nem em nenhum regime individualmente) pelo teste de Johansen — a relação identificada é de curto prazo, via retornos, não uma relação de equilíbrio de longo prazo entre os níveis de preço.

## Como reproduzir

Requisitos: `pandas`, `numpy`, `matplotlib`, `statsmodels`, `scipy`, `yfinance`.

1. Coloque os dois arquivos CSV de dados na mesma pasta do notebook.
2. Execute `paper_analysis.ipynb` célula a célula, na ordem.

## Limitações

- A regressão OLS usada para estimar a sensibilidade por regime é simples (um único regressor) e não controla por outros fatores que também afetam PBR (câmbio, taxa de juros, risco-país, notícias específicas da empresa).
- Os erros-padrão da OLS reportados são não-robustos; dado o excesso de curtose observado nos resíduos, resultados com erros-padrão robustos a heterocedasticidade (HC) tenderiam a ser mais confiáveis.
- O corte de datas dos regimes segue marcos de política pública amplamente divulgados, mas não captura eventuais defasagens de mercado na precificação da mudança de regime.
