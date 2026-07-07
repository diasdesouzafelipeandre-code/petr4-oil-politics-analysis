# Metodologia e Conclusões

## Objetivo

Esta análise tem como objetivo observar a correlação entre o preço do petróleo cru e as ações PETR4 da Petrobras.

## Hipótese

A relação entre PETR4 e o preço do petróleo parece ser conjunta, e as diferenças entre essas séries podem indicar instabilidade política interna.

## Metodologia

- Utilização de dois conjuntos de dados distintos para comparar séries históricas.
- Avaliação da causalidade entre os preços.
- Identificação de outliers na diferença entre as séries para encontrar eventos políticos internos relevantes.

## Processo

1. Limpeza dos dados.
2. Alinhamento e verificação da integridade das séries.
3. Identificação dos pontos de máximo e mínimo em ambas as séries.
4. Criação de um índice de variação com base na abertura e no fechamento.
5. Comparação das variações normalizadas de cada série para evidenciar diferenças relativas.
6. Teste de causalidade utilizando o **Granger causality test**.
7. Identificação das maiores variações e busca por notícias relacionadas ao período analisado.

## Resultados

- Os gráficos de variação do preço do petróleo e de PETR4 são, em geral, semelhantes, com algumas diferenças perceptíveis.
- Um ponto notável foi o preço negativo do petróleo em **20/04/2020**, durante a pandemia.
- Para comparar as séries de forma consistente, os valores foram normalizados pelo máximo de cada uma, analisando a diferença entre elas sem usar valor absoluto — preservando os sinais negativos gerados na pandemia.
- O teste de causalidade mostrou uma relação próxima entre as séries, com **p-valor = 0,0053**, sugerindo uma conexão direta.

## Diferenças com maior relevância política

| Data | Evento | Fonte |
|---|---|---|
| 17/02/2022 | Alta dos combustíveis; discussão sobre "abrasileirar" o preço da gasolina | [G1](https://g1.globo.com/politica/eleicoes/2022/noticia/2022/02/17/em-meio-a-alta-dos-combustiveis-lula-defende-abrasileirar-o-preco-da-gasolina.ghtml) |
| 05/07/2013 | Ações da Petrobras atingem menor cotação em quase oito anos | [Valor](https://valor.globo.com/google/amp/empresas/noticia/2013/07/05/acoes-da-petrobras-atingem-menor-cotacao-em-quase-oito-anos.ghtml) |
| 05/07/2013 | Queda geral da bolsa em meio a rumores de corte de imposto | [UOL](https://economia.uol.com.br/cotacoes/noticias/redacao/2013/07/05/bovespa-opera-em-queda-e-dolar-sobe-nesta-sexta-feira-acompanhe.htm) |
| 14/02/2014 | Pedido de apuração de denúncia de suborno envolvendo funcionários da Petrobras | [UOL](https://economia.uol.com.br/noticias/efe/2014/02/14/psdb-pede-apuracao-de-denuncia-de-suborno-a-funcionarios-da-petrobras.htm) |
| 14/02/2014 | Investigação de suborno envolvendo a Petrobras e a SBM Offshore (Holanda) | [Auditar](https://auditar.org.br/2014/02/14/) |

## Conclusão

A análise inicial demonstra, com um alto índice de causalidade, que as ações da PETR4 tendem a seguir as variações do preço do petróleo, e que as principais diferenças observadas podem estar relacionadas a acontecimentos marcantes de natureza política. A tese ainda pode ser aprofundada com mais dados e refinamento estatístico.