# Análise da relação entre PETR4 e o preço do petróleo

Este projeto reúne uma análise exploratória e estatística sobre a relação entre o preço do petróleo cru e as ações da Petrobras, representadas pela PETR4. A proposta é investigar se divergências entre essas séries podem estar associadas a eventos políticos e econômicos relevantes no Brasil.

## Objetivo

Avaliar se a variação do preço do petróleo acompanha os movimentos das ações PETR4 e verificar se as diferenças observadas entre as séries podem indicar instabilidade política ou outros acontecimentos marcantes.

## Contexto da análise

A análise parte da hipótese de que PETR4 e o preço do petróleo apresentam comportamentos semelhantes em grande parte do período estudado, mas que alguns momentos de divergência podem refletir eventos específicos, como crises econômicas, choques de mercado e temas de natureza política.

## Metodologia

A análise foi conduzida em etapas que incluem:

1. Carregamento dos dados de commodities e das ações PETR4.
2. Limpeza e alinhamento das séries temporais.
3. Identificação de pontos de máximo e mínimo.
4. Criação de um índice de variação com base em abertura e fechamento.
5. Normalização das séries para comparação relativa.
6. Identificação de outliers e diferenças relevantes entre os movimentos.
7. Aplicação do teste de causalidade de Granger para avaliar a relação entre as séries.
8. Comparação dos principais pontos de divergência com notícias e eventos do período.

## Dados utilizados

- [DATA_SET/Global_Commodity_Prices_2000_2026.csv](DATA_SET/Global_Commodity_Prices_2000_2026.csv)
- [DATA_SET/Petrobras Historical Stock Data (2000-2026).csv](DATA_SET/Petrobras%20Historical%20Stock%20Data%20(2000-2026).csv)

## Estrutura do projeto

- [Comparação.ipynb](Comparação.ipynb): notebook principal com a análise e os gráficos.
- [Tese.txt](Tese.txt): resumo da tese, metodologia e principais conclusões.
- [Images](Images): pasta com as visualizações geradas.
- [LICENSE](LICENSE): licença do projeto.

## Resultados principais

- As séries apresentam comportamento semelhante em termos gerais.
- Foram observadas divergências importantes em períodos específicos.
- O período da pandemia, especialmente em 2020, foi um marco extremo, com queda expressiva no preço do petróleo.
- A análise também indicou uma relação de causalidade relevante entre as séries, com o teste de Granger sugerindo conexão estatística importante.
- Momentos de divergência foram associados a eventos como:
  - alta de combustíveis e discussões sobre preços da gasolina em 2022;
  - queda intensa das ações da Petrobras em 2013;
  - denúncias e investigações envolvendo a empresa em 2014.

## Visualizações

### Variação do preço do petróleo

![Variação do preço do petróleo](Images/OIL%20variação.png)

### Variação das ações PETR4

![Variação das ações PETR4](Images/Petr4%20variação.png)

### Diferença entre as séries

![Diferença entre as séries](Images/Diferença%20variação.png)

## Como executar

1. Leia primeiro o arquivo [Tese.txt](Tese.txt) para compreender a hipótese e o contexto da análise.
2. Abra o notebook [Comparação.ipynb](Comparação.ipynb) em um ambiente Jupyter ou no VS Code.
3. Certifique-se de que os arquivos da pasta [DATA_SET](DATA_SET) estejam presentes.
4. Execute as células do notebook para reproduzir a análise e gerar as visualizações.

## Observações

A análise é uma primeira fase de um estudo mais amplo e pode ser aprimorada com tratamento adicional de dados, testes estatísticos mais robustos e inclusão de novas variáveis econômicas e políticas.
