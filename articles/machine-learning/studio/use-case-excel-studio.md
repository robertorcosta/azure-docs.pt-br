---
title: Migrando a análise do Excel
titleSuffix: ML Studio (classic) - Azure
description: Uma comparação de modelos de regressão linear no Excel e no Azure Machine Learning Studio (clássico)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 5f4169753e653a1c6c82c997d37769d8548e76ff
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839436"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio-classic"></a>Migrar a análise do Excel para Azure Machine Learning Studio (clássico)

> *Kate Baroni* e *Ben Boatman* são arquitetos de soluções corporativas no Data Insights Center of Excellence da Microsoft. Neste artigo, eles descrevem sua experiência de migração de um pacote de análise de regressão existente para uma solução baseada em nuvem usando Azure Machine Learning Studio (clássico).

## <a name="goal"></a>Objetivo

Nosso projeto começou com dois objetivos: 

1. Usar a análise preditiva para melhorar a precisão das projeções de receita mensal de nossa organização 
2. Use a versão clássica do Azure Machine Learning Studio para confirmar, otimizar, aumentar a velocidade e dimensionar nossos resultados. 

Como muitas empresas, nossa organização passa por uma processo de previsão de receita mensal. Nossa pequena equipe de analistas de negócios foi tarefa com o uso da versão clássica do Azure Machine Learning Studio para dar suporte ao processo e melhorar a precisão da previsão. A equipe passou vários meses coletando dados de várias fontes e submetendo os atributos de dados à análise estatística, identificando os principais atributos relevantes à previsão de vendas de serviços. A etapa seguinte foi iniciar a criação de protótipos de modelos de regressão estatística com os dados no Excel. Em poucas semanas, tínhamos um modelo de regressão do Excel que superava os processos atuais de previsão de campo e finanças. Esse se tornou o resultado de previsão de linha de base. 

Seguimos a próxima etapa para mover nossa análise preditiva para a versão clássica do Studio para descobrir como a versão clássica do estúdio poderia melhorar o desempenho de previsão.

## <a name="achieving-predictive-performance-parity"></a>Obtendo a paridade de desempenho preditivo
Nossa primeira prioridade era obter a paridade entre a versão clássica do Studio e os modelos de regressão do Excel. Considerando os mesmos dados e a mesma divisão para dados de treinamento e teste, queríamos obter a paridade de desempenho preditiva entre o Excel e a versão clássica do Studio. Inicialmente, falhamos. O modelo do Excel supera o modelo de estúdio (clássico). A falha ocorreu devido à falta de compreensão da configuração da ferramenta base na versão clássica do Studio. Após uma sincronização com a versão clássica da equipe do produto Studio, obtivemos uma melhor compreensão da configuração básica necessária para nossos conjuntos de dados e alcançamos a paridade entre os dois modelos. 

### <a name="create-regression-model-in-excel"></a>Criar um modelo de regressão no Excel
Nossa Regressão do Excel usou o modelo de regressão linear padrão encontrado nas Ferramentas de Análise do Excel. 

Calculamos o *Erro de Média Absoluta %* e o usamos como a medida de desempenho para o modelo. Levamos três meses para chegar a um modelo de trabalho usando o Excel. Trouxemos muito do aprendizado para a versão clássica do experimento do estúdio, que, por fim, era benéfico na compreensão dos requisitos.

### <a name="create-comparable-experiment-in-studio-classic"></a>Criar experimento comparável no estúdio (clássico)
Seguimos estas etapas para criar nosso experimento na versão clássica do Studio: 

1. Carregou o conjunto de um arquivo CSV para a versão clássica do Studio (arquivo muito pequeno)
2. Criou um novo experimento e utilizou o módulo [selecionar colunas no conjunto][select-columns] de dados para selecionar os mesmos recursos usados no Excel 
3. Usou o módulo [dividir dados][split] (com o modo de *expressão relativa* ) para dividir os dados nos mesmos conjuntos de dado de treinamento que foram feitos no Excel 
4. Experimentamos o módulo de [regressão linear][linear-regression] (somente opções padrão), documentados e comparamos os resultados ao nosso modelo de regressão do Excel

### <a name="review-initial-results"></a>Examinar os resultados iniciais
A princípio, o modelo do Excel desempenhou claramente o modelo de estúdio (clássico): 

|  | Excel | Estúdio (clássico) |
| --- |:---:|:---:|
| Desempenho | | |
| <ul style="list-style-type: none;"><li>Quadrado R Ajustado</li></ul> |0,96 |N/D |
| <ul style="list-style-type: none;"><li>Coeficiente de <br />Determinação</li></ul> |N/D |0,78<br />(baixa precisão) |
| Erro Absoluto Médio |US$ 9,5 milhões |US$ 19,4 milhões |
| Erro Absoluto Médio (%) |6,03% |12,2% |

Quando apresentamos nosso processo e os resultados aos desenvolvedores e cientistas de dados à equipe do Machine Learning, eles forneceram rapidamente algumas dicas úteis. 

* Quando você usa o módulo [regressão linear][linear-regression] na versão clássica do Studio, dois métodos são fornecidos:
  * Gradiente Online Descendente: talvez seja mais adequado para problemas de maior escala
  * Mínimos Quadrados Comuns: esse é o método em que muitas pessoas pensam quando ouvem falar em regressão linear. Para conjuntos de dados pequenos, Mínimos Quadrados Comuns pode ser uma opção melhor.
* Considere a possibilidade de ajustar o parâmetro L2 de Regularização de Peso para melhorar o desempenho. Ele é definido como 0,001 por padrão e, para nosso pequeno conjunto de dados, o definimos como 0,005 para melhorar o desempenho. 

### <a name="mystery-solved"></a>Mistério resolvido!
Quando aplicamos as recomendações, atingimos o mesmo desempenho de linha de base na versão clássica do Studio como com o Excel: 

|  | Excel | Studio (clássico) (inicial) | Estúdio (clássico) w/mínimo de quadrados |
| --- |:---:|:---:|:---:|
| Valor rotulado |Dados reais (numéricos) |mesmo |mesmo |
| Aprendiz |Excel -> Dados da Análise -> Regressão |Regressão Linear. |Regressão Linear |
| Opções de aprendiz |N/D |Padrões |quadrados mínimos comuns<br />L2 = 0,005 |
| Conjunto de dados |26 linhas, 3 recursos, 1 rótulo. Todos numéricos. |mesmo |mesmo |
| Divisão: treinamento |Excel treinado nas primeiras 18 linhas e testado nas últimas 8 linhas. |mesmo |mesmo |
| Divisão: teste |Fórmula de regressão do Excel aplicada às últimas 8 linhas |mesmo |mesmo |
| **Desempenho** | | | |
| Quadrado R Ajustado |0,96 |N/D | |
| Coeficiente de Determinação |N/D |0,78 |0,952049 |
| Erro Absoluto Médio |US$ 9,5 milhões |US$ 19,4 milhões |US$ 9,5 milhões |
| Erro Absoluto Médio (%) |<span style="background-color: 00FF00;"> 6,03%</span> |12,2% |<span style="background-color: 00FF00;"> 6,03%</span> |

Além disso, os coeficientes do Excel saíram-se bem em comparação com os pesos de recurso no modelo de treinamento do Azure:

|  | Coeficientes do Excel | Pesos de recursos do Azure |
| --- |:---:|:---:|
| Interceptação/desvio |19470209,88 |19328500 |
| Recurso A |0,832653063 |0,834156 |
| Recurso B |11071967,08 |11007300 |
| Recurso C |25383318,09 |25140800 |

## <a name="next-steps"></a>Próximas etapas
Queríamos usar o serviço Web do Machine Learning no Excel. Nossos analistas de negócios usam o Excel, e precisávamos de um modo para chamar o serviço Web do Machine Learning com uma linha de dados do Excel e fazer com que ele retornasse ao Excel o valor previsto. 

Também queríamos otimizar nosso modelo, usando as opções e os algoritmos disponíveis na versão clássica do Studio.

### <a name="integration-with-excel"></a>Integração com o Excel
A solução foi operacionalizar nosso modelo de regressão do Machine Learning criando um serviço Web com base no modelo treinado. Em alguns minutos, o serviço Web foi criado e pudemos chamá-lo diretamente no Excel para retornar um valor de receita prevista. 

A seção *Painel de Serviços Web* inclui uma pasta de trabalho do Excel que pode ser baixada. A pasta de trabalho vem pré-formatada com a API do serviço Web e informações de esquema inseridas. Quando você clica em *Baixar Pasta de Trabalho do Excel*, ela é aberta e você pode salvá-la em seu computador local. 

![Baixar a pasta de trabalho do Excel no painel de serviços Web](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

Com a pasta de trabalho aberta, copie os parâmetros predefinidos para a seção azul Parâmetro, conforme mostrado abaixo. Depois que os parâmetros forem inseridos, o Excel chamará o serviço Web do Machine Learning, e os rótulos pontuados previstos serão exibidos na seção verde Valores Previstos. A pasta de trabalho continuará a criar previsões para parâmetros com base em seu modelo treinado para todos os itens de linha inseridos em Parâmetros. Para obter mais informações sobre como usar esse recurso, confira [Utilizando um serviço Web do Azure Machine Learning no Excel](consuming-from-excel.md). 

![Pasta de trabalho de modelo do Excel conectando ao serviço Web implantado](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>Otimização e experimentos adicionais
Agora que tínhamos uma linha de base com nosso modelo do Excel, otimizamos nosso Modelo de Regressão Linear do Machine Learning. Usamos a seleção de [recursos baseada em filtro][filter-based-feature-selection] de módulo para melhorar nossa seleção de elementos de dados iniciais e isso nos ajudou a alcançar uma melhoria de desempenho de 4,6% de erro absoluto médio. Para futuros projetos, usaremos esse recurso, que pode economizar semanas na iteração por meio de atributos de dados para encontrar o conjunto certo de recursos a serem usados para modelagem. 

Em seguida, planejamos incluir algoritmos adicionais, como [Bayesiana][bayesian-linear-regression] ou [árvores de decisão aumentadas][boosted-decision-tree-regression] em nosso experimento para comparar o desempenho. 

Se você quiser experimentar a regressão, um bom conjunto de dados para tentar é o conjunto de dados de exemplo de Regressão de Eficiência de Energia, que tem muitos atributos numéricos. O conjunto de valores é fornecido como parte dos conjuntos de exemplos de exemplo na versão clássica do Studio. Você pode usar diversos módulos de aprendizado para prever a Carga de Aquecimento ou a Carga de Resfriamento. O gráfico abaixo é uma comparação de desempenho de diferentes aprendizados de regressão em relação ao conjunto de dados de Eficiência Energética, prevendo a Carga de Resfriamento de variável de destino: 

| Modelo | Erro Absoluto Médio | Erro Quadrado Médio de Raiz | Erro Absoluto Relativo | Erro Quadrado Relativo | Coeficiente de Determinação |
| --- | --- | --- | --- | --- | --- |
| Árvore de Decisão Aumentada |0,930113 |1,4239 |0,106647 |0,021662 |0,978338 |
| Regressão Linear (Gradiente Descendente) |2,035693 |2,98006 |0,233414 |0,094881 |0,905119 |
| Regressão de Rede Neural |1,548195 |2,114617 |0,177517 |0,047774 |0,952226 |
| Regressão Linear (Quadrados Mínimos Comuns) |1,428273 |1,984461 |0,163767 |0,042074 |0,957926 |

## <a name="key-takeaways"></a>Principais observações
Aprendemos muito por meio da execução da regressão do Excel e da versão clássica dos experimentos do estúdio em paralelo. Criar o modelo de linha de base no Excel e compará-lo a modelos usando Machine Learning [regressão linear][linear-regression] nos ajudou a aprender o Studio (clássico) e descobrimos oportunidades para melhorar a seleção de dados e o desempenho do modelo. 

Também descobrimos que é aconselhável usar a seleção de [recursos baseada em filtro][filter-based-feature-selection] para acelerar projetos de previsão futuros. Ao aplicar a seleção de recursos aos seus dados, você pode criar um modelo aprimorado na versão clássica do Studio com melhor desempenho geral. 

A capacidade de transferir a previsão analítica preditiva da versão clássica do Studio para o Excel sistematicamente permite um aumento significativo na capacidade de fornecer resultados com êxito a um grande público de usuários de negócios. 

## <a name="resources"></a>Recursos
Estes são alguns recursos que ajudam a trabalhar com a regressão: 

* Regressão no Excel. Se você nunca tentou regressão no Excel, este tutorial torna mais fácil: [https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)
* Regressão versus previsão. Tyler Chessman escreveu um artigo no blog explicando como realizar a previsão de série temporal no Excel, que contém uma boa descrição para iniciantes sobre a regressão linear. [https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts](https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts) 
* Regressão linear de quadrados mínimos simples: falhas, problemas e armadilhas. Para uma introdução e uma discussão sobre Regressão: [https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ ](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

