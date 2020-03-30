---
title: Análises migratórias do Excel
titleSuffix: ML Studio (classic) - Azure
description: Uma comparação de modelos de regressão linear no Excel e no Azure Machine Learning Studio (clássico)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 85bae9bfc10460b51935c6eb1e14e3a3dd816a8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217808"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio-classic"></a>Migrar análises do Excel para o Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> *Kate Baroni* e *Ben Boatman* são arquitetos de soluções corporativas no Data Insights Center of Excellence da Microsoft. Neste artigo, eles descrevem sua experiência migrando um pacote de análise de regressão existente para uma solução baseada em nuvem usando o Azure Machine Learning Studio (clássico).

## <a name="goal"></a>Goal

Nosso projeto começou com dois objetivos: 

1. Use análises preditivas para melhorar a precisão das projeções mensais de receita da nossa organização 
2. Use o Azure Machine Learning Studio (clássico) para confirmar, otimizar, aumentar a velocidade e a escala de nossos resultados. 

Como muitas empresas, nossa organização passa por uma processo de previsão de receita mensal. Nossa pequena equipe de analistas de negócios foi incumbida de usar o Azure Machine Learning Studio (clássico) para apoiar o processo e melhorar a precisão das previsões. A equipe passou vários meses coletando dados de várias fontes e submetendo os atributos de dados à análise estatística, identificando os principais atributos relevantes à previsão de vendas de serviços. A etapa seguinte foi iniciar a criação de protótipos de modelos de regressão estatística com os dados no Excel. Em poucas semanas, tínhamos um modelo de regressão do Excel que superava os processos atuais de previsão de campo e finanças. Esse se tornou o resultado de previsão de linha de base. 

Em seguida, demos o próximo passo para mudar nossas análises preditivas para o Studio (clássico) para descobrir como o Studio (clássico) poderia melhorar o desempenho preditivo.

## <a name="achieving-predictive-performance-parity"></a>Obtendo a paridade de desempenho preditivo
Nossa prioridade era alcançar a paridade entre os modelos de regressão Studio (clássico) e Excel. Dado os mesmos dados, e a mesma divisão para dados de treinamento e teste, queríamos alcançar a paridade de desempenho preditiva entre excel e studio (clássico). Inicialmente, falhamos. O modelo Excel superou o modelo Studio (clássico). A falha foi devido à falta de compreensão da configuração da ferramenta base no Studio (clássico). Após uma sincronização com a equipe de produtos studio (clássico), ganhamos uma melhor compreensão da configuração base necessária para nossos conjuntos de dados, e alcançamos a paridade entre os dois modelos. 

### <a name="create-regression-model-in-excel"></a>Criar um modelo de regressão no Excel
Nossa Regressão do Excel usou o modelo de regressão linear padrão encontrado nas Ferramentas de Análise do Excel. 

Calculamos o *Erro de Média Absoluta %* e o usamos como a medida de desempenho para o modelo. Levamos três meses para chegar a um modelo de trabalho usando o Excel. Trouxemos grande parte do aprendizado para o experimento Studio (clássico), que acabou sendo benéfico na compreensão dos requisitos.

### <a name="create-comparable-experiment-in-studio-classic"></a>Criar experiência comparável no Studio (clássico)
Seguimos esses passos para criar nosso experimento no Studio (clássico): 

1. Carreguei o conjunto de dados como um arquivo csv para o Studio (clássico) (arquivo muito pequeno)
2. Criamos um novo experimento e usamos o módulo [Selecionar Colunas no Conjunto de Dados][select-columns] para selecionar os mesmos recursos de dados usados no Excel 
3. Usamos o módulo [Dividir Dados][split] (com o modo *Expressão Relativa*) para dividir os dados nos mesmos conjuntos de dados de treinamento usados no Excel 
4. Experimentamos o módulo de [Regressão Linear][linear-regression] (somente opções padrão), documentamos e comparamos os resultados com o nosso modelo de regressão do Excel

### <a name="review-initial-results"></a>Examinar os resultados iniciais
No início, o modelo Excel claramente superou o modelo Studio (clássico): 

|  | Excel | Studio (clássico) |
| --- |:---:|:---:|
| Desempenho | | |
| <ul style="list-style-type: none;"><li>Quadrado R Ajustado</li></ul> |0,96 |N/D |
| <ul style="list-style-type: none;"><li>Coeficiente de <br />Determinação</li></ul> |N/D |0,78<br />(baixa precisão) |
| Erro de Média Absoluta |US$ 9,5 milhões |US$ 19,4 milhões |
| Erro Absoluto Médio (%) |6,03% |12,2% |

Quando apresentamos nosso processo e os resultados aos desenvolvedores e cientistas de dados à equipe do Machine Learning, eles forneceram rapidamente algumas dicas úteis. 

* Quando você usa o módulo [de regressão linear][linear-regression] no Studio (clássico), dois métodos são fornecidos:
  * Gradiente Online Descendente: talvez seja mais adequado para problemas de maior escala
  * Mínimos Quadrados Comuns: esse é o método em que muitas pessoas pensam quando ouvem falar em regressão linear. Para conjuntos de dados pequenos, Mínimos Quadrados Comuns pode ser uma opção melhor.
* Considere a possibilidade de ajustar o parâmetro L2 de Regularização de Peso para melhorar o desempenho. Ele é definido como 0,001 por padrão e, para nosso pequeno conjunto de dados, o definimos como 0,005 para melhorar o desempenho. 

### <a name="mystery-solved"></a>Mistério resolvido!
Quando aplicamos as recomendações, obtivemos o mesmo desempenho de base no Studio (clássico) como com o Excel: 

|  | Excel | Estúdio (clássico) (Inicial) | Estúdio (clássico) c/ Praças Mínimas |
| --- |:---:|:---:|:---:|
| Valor rotulado |Dados reais (numéricos) |mesmo |mesmo |
| Aprendiz |Excel -> Dados da Análise -> Regressão |Regressão Linear. |Regressão Linear |
| Opções de aprendiz |N/D |Padrões |quadrados mínimos comuns<br />L2 = 0,005 |
| Conjunto de Dados |26 linhas, 3 recursos, 1 rótulo. Todos numéricos. |mesmo |mesmo |
| Divisão: treinamento |Excel treinado nas primeiras 18 linhas e testado nas últimas 8 linhas. |mesmo |mesmo |
| Divisão: teste |Fórmula de regressão do Excel aplicada às últimas 8 linhas |mesmo |mesmo |
| **Desempenho** | | | |
| Quadrado R Ajustado |0,96 |N/D | |
| Coeficiente de Determinação |N/D |0,78 |0,952049 |
| Erro de Média Absoluta |US$ 9,5 milhões |US$ 19,4 milhões |US$ 9,5 milhões |
| Erro Absoluto Médio (%) |<span style="background-color: 00FF00;">6.03%</span> |12,2% |<span style="background-color: 00FF00;">6.03%</span> |

Além disso, os coeficientes do Excel saíram-se bem em comparação com os pesos de recurso no modelo de treinamento do Azure:

|  | Coeficientes do Excel | Pesos de recursos do Azure |
| --- |:---:|:---:|
| Interceptação/desvio |19470209,88 |19328500 |
| Recurso A |0,832653063 |0,834156 |
| Recurso B |11071967,08 |11007300 |
| Recurso C |25383318,09 |25140800 |

## <a name="next-steps"></a>Próximas etapas
Queríamos usar o serviço Web do Machine Learning no Excel. Nossos analistas de negócios usam o Excel, e precisávamos de um modo para chamar o serviço Web do Machine Learning com uma linha de dados do Excel e fazer com que ele retornasse ao Excel o valor previsto. 

Também queríamos otimizar nosso modelo, utilizando as opções e algoritmos disponíveis no Studio (clássico).

### <a name="integration-with-excel"></a>Integração com o Excel
A solução foi operacionalizar nosso modelo de regressão do Machine Learning criando um serviço Web com base no modelo treinado. Em alguns minutos, o serviço Web foi criado e pudemos chamá-lo diretamente no Excel para retornar um valor de receita prevista. 

A seção *Painel de Serviços Web* inclui uma pasta de trabalho do Excel que pode ser baixada. A pasta de trabalho vem pré-formatada com a API do serviço Web e informações de esquema inseridas. Quando você clica em *Baixar Pasta de Trabalho do Excel*, ela é aberta e você pode salvá-la em seu computador local. 

![Baixe a pasta de trabalho do Excel no Painel de Serviços Web](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

Com a pasta de trabalho aberta, copie os parâmetros predefinidos para a seção azul Parâmetro, conforme mostrado abaixo. Depois que os parâmetros forem inseridos, o Excel chamará o serviço Web do Machine Learning, e os rótulos pontuados previstos serão exibidos na seção verde Valores Previstos. A pasta de trabalho continuará a criar previsões para parâmetros com base em seu modelo treinado para todos os itens de linha inseridos em Parâmetros. Para obter mais informações sobre como usar esse recurso, confira [Utilizando um serviço Web do Azure Machine Learning no Excel](consuming-from-excel.md). 

![Template Excel manual de trabalho conectando-se ao serviço web implantado](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>Otimização e experimentos adicionais
Agora que tínhamos uma linha de base com nosso modelo do Excel, otimizamos nosso Modelo de Regressão Linear do Machine Learning. Usamos o módulo [Seleção de Recursos com Base em Filtro][filter-based-feature-selection] para melhorar nossa seleção de elementos de dados iniciais e isso nos ajudou a obter uma melhoria de desempenho de 4,6% de Erro Absoluto Médio. Para projetos futuros, usaremos esse recurso que pode nos poupar semanas na iteração através de atributos de dados para encontrar o conjunto certo de recursos para usar para modelagem. 

Em seguida, planejamos incluir algoritmos adicionais, como [Bayesiano][bayesian-linear-regression] ou [Árvores de Decisão Aumentadas][boosted-decision-tree-regression] em nosso experimento para comparar o desempenho. 

Se você quiser experimentar a regressão, um bom conjunto de dados para tentar é o conjunto de dados de exemplo de Regressão de Eficiência de Energia, que tem muitos atributos numéricos. O conjunto de dados é fornecido como parte dos conjuntos de dados da amostra no Studio (clássico). Você pode usar diversos módulos de aprendizado para prever a Carga de Aquecimento ou a Carga de Resfriamento. O gráfico abaixo é uma comparação de desempenho de diferentes aprendizados de regressão em relação ao conjunto de dados de Eficiência Energética, prevendo a Carga de Resfriamento de variável de destino: 

| Modelo | Erro de Média Absoluta | Erro Quadrado Médio de Raiz | Erro Absoluto Relativo | Erro Quadrado Relativo | Coeficiente de Determinação |
| --- | --- | --- | --- | --- | --- |
| Árvore de Decisão Aumentada |0,930113 |1,4239 |0,106647 |0,021662 |0,978338 |
| Regressão Linear (Gradiente Descendente) |2,035693 |2,98006 |0,233414 |0,094881 |0,905119 |
| Regressão de Rede Neural |1,548195 |2,114617 |0,177517 |0,047774 |0,952226 |
| Regressão Linear (Quadrados Mínimos Comuns) |1,428273 |1,984461 |0,163767 |0,042074 |0,957926 |

## <a name="key-takeaways"></a>Principais observações
Aprendemos muito executando experimentos de regressão excel e Studio (clássico) em paralelo. Criar o modelo de linha de base no Excel e compará-lo com modelos usando [a Regressão Linear][linear-regression] de Machine Learning nos ajudou a aprender o Studio (clássico), e descobrimos oportunidades para melhorar a seleção de dados e o desempenho do modelo. 

Também descobrimos que é aconselhável usar a [Seleção de Recursos com Base em Filtro][filter-based-feature-selection] para acelerar projetos de previsão futuros. Ao aplicar a seleção de recursos aos seus dados, você pode criar um modelo melhorado no Studio (clássico) com melhor desempenho geral. 

A capacidade de transferir a previsão analítica preditiva do Studio (clássico) para o Excel sistematicamente permite um aumento significativo na capacidade de fornecer resultados com sucesso para um público de usuários de negócios amplo. 

## <a name="resources"></a>Recursos
Estes são alguns recursos que ajudam a trabalhar com a regressão: 

* Regressão no Excel. Se você nunca tentou a regressão no Excel, este tutorial facilita:[https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)
* Regressão versus previsão. Tyler Chessman escreveu um artigo no blog explicando como fazer a previsão de séries tempontos no Excel, que contém uma boa descrição iniciante de regressão linear. [https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts](https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts) 
* Regressão linear de quadrados mínimos simples: falhas, problemas e armadilhas. Para uma introdução e uma discussão sobre Regressão: [https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ ](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

