---
title: Executar tarefas de ciência de dados – Processo de Ciência de Dados da Equipe
description: Como um cientista de dados pode executar um projeto de ciência de dados de modo rastreável, com controle de versão e colaborativo.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: cbe822b75368a1ab72bcd7f73419770b291d2508
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321159"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Executar tarefas de ciência de dados: exploração, modelagem e implantação

Em geral, entre as tarefas de ciência de dados, temos: exploração, modelagem e implantação. Este artigo mostra como usar os utilitários **Exploração, Análise, Geração de Relatórios Interativas de Dados (IDEAR)** e **Modelagem e Geração de Relatórios Automatizadas (AMAR)** para completar várias tarefas comuns de ciência de dados como exploração interativa de dados, análise de dados, geração de relatórios e criação de modelo. As opções para implantar um modelo em um ambiente de produção podem incluir:

- [Azure Machine Learning](../index.yml)
- [SQL-Server com serviços de ML](/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> exploração 

Um cientista de dados pode realizar exploração e geração de relatórios de várias maneiras: usando bibliotecas e pacotes disponíveis para Python (matplotlib, por exemplo) ou com R (ggplot ou malha, por exemplo). Os cientistas de dados podem personalizar esse código para atender às necessidades de exploração de dados para cenários específicos. As necessidades de lidar com dados estruturados são diferentes daquelas de dados não estruturados, como texto ou imagens. 

Produtos como Azure Machine Learning também fornecem [preparação de dados avançada](../how-to-create-register-datasets.md) para Wrangling e exploração de dados, incluindo a criação de recursos. O usuário deve decidir quais ferramentas, bibliotecas e pacotes se ajustam melhor às suas necessidades. 

A entrega ao final dessa fase é um relatório de exploração de dados. O relatório deve fornecer uma exibição bastante abrangente dos dados a serem usados para modelagem e uma avaliação de se os dados são adequados para prosseguir para a etapa de modelagem. Os utilitários do Processo de ciência de dados de equipe (TDSP) discutidos nas seções a seguir para exploração, modelagem e geração de relatórios semiautomatizadas também oferecem exploração de dados e modelagem de relatórios padronizadas. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Exploração, análise e geração de relatórios interativas de dados usando o utilitário IDEAR

Esse utilitário com base em R Markdown ou Python com base em bloco de notas fornece uma ferramenta flexível e interativa para avaliar e explorar os conjuntos de dados. Os usuários podem gerar rapidamente relatórios do conjunto de dados com a quantidade mínima de código. Os usuários podem clicar nos botões para exportar os resultados de exploração na ferramenta interativa para um relatório final, que pode ser entregue aos clientes ou usado para tomar decisões sobre quais variáveis incluir na etapa de modelagem subsequente.

Neste momento, a ferramenta só funciona em estruturas de dados na memória. Um arquivo YAML é necessário para especificar os parâmetros do conjunto de dados a ser explorado. Para obter mais informações, consulte [IDEAR em utilitários de Ciência de Dados de TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> modelagem

Há vários kits de ferramentas e pacotes para treinar modelos em diversas linguagens. Os cientistas de dados devem ficar à vontade para usar aqueles com os quais estão acostumados, desde que as considerações de desempenho em relação à precisão e latência sejam atendidas para os casos de uso nos negócios e cenários de produção relevantes.

A próxima seção mostra como usar um utilitário TDSP com base em R para modelagem semiautomatizada. Esse utilitário AMAR pode ser usado para gerar modelos de linha de base, bem como os parâmetros que precisam ser ajustados para fornecer um melhor desempenho de modelo.
A seção de gerenciamento de modelo a seguir mostra como ter um sistema para registrar e gerenciar vários modelos.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Treinamento de modelo: modelagem e geração de relatórios usando o utilitário AMAR

O utilitário [Modelagem e Geração de Relatórios Automatizadas (AMAR)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) oferece uma ferramenta personalizável e semiautomatizada para executar a criação de modelo com varredura de parâmetro hyper e para comparar a exatidão desses modelos. 

O utilitário de criação de modelo é um arquivo R Markdown que pode ser executado para produzir saída HTML autossuficiente com um sumário para facilitar a navegação pelas diferentes seções. Três algoritmos são executados quando o arquivo Markdown é executado (knit): regressão regularizada usando o pacote glmnet, floresta aleatória usando o pacote randomForest e aumento de árvores usando o pacote xgboost. Cada um desses algoritmos produz um modelo treinado. Então, a precisão desses modelos é comparada e os gráficos de importância relacionados ao recurso são relatados. Atualmente, há dois utilitários: um para uma tarefa de classificação binária e um para uma tarefa de regressão. As principais diferenças entre eles é o modo como os parâmetros de controle e as métricas de precisão são especificados para essas tarefas de aprendizado. 

Um arquivo YAML é usado para especificar:

- a entrada de dados (uma fonte de SQL ou um arquivo de dados R) 
- qual parte dos dados é usada para treinamento e qual parte é usada para teste
- quais algoritmos executar 
- a escolha dos parâmetros de controle para otimização do modelo:
    - validação cruzada 
    - inicialização
    - formatos de validação cruzada
- os conjuntos de parâmetro hyper para cada algoritmo. 

O número de algoritmos, o número de formatos para otimização, os parâmetros e o número hyper e o número de conjuntos de parâmetros hyper para a varredura também podem ser modificados no arquivo Yaml para executar os modelos rapidamente. Por exemplo, eles podem ser executados com um número menor de formatos CV, um número menor de conjuntos de parâmetros. Se houver garantia, eles também podem ser executados de forma mais abrangente com um número maior de formatos CV ou um número maior de conjuntos de parâmetros.

Para obter mais informações, consulte [Modelagem automatizada e utilitário de relatório nos utilitários de Ciência de Dados de TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Gerenciamento de modelos
Depois de vários modelos terem sido compilados, geralmente é necessário ter um sistema para registrar e gerenciar os modelos. Geralmente, é necessária uma combinação de scripts ou APIs e um sistema de controle de versão ou de banco de dados de back-end. Algumas opções que podem ser levadas em consideração para essas tarefas de gerenciamento são:

1. [Azure Machine Learning – serviço de gerenciamento de modelos](../index.yml)
2. [ModelDB do MIT](http://modeldb.csail.mit.edu:3000/projects) 
3. [SQL-Server como um sistema de gerenciamento de modelos](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> implantação

A implantação de produção permite que um modelo execute uma função ativa em uma empresa. Previsões de um modelo implantado podem ser usadas para decisões de negócios.

### <a name="production-platforms"></a>Plataformas de produção
Há várias abordagens e plataformas para colocar modelos em produção. Veja algumas opções:


- [Implantação de modelo no Azure Machine Learning](../how-to-deploy-and-where.md)
- [Implantação de um modelo no SQL-server](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Antes da implantação, é preciso garantir que a latência da pontuação de modelo é baixa o suficiente para usar em produção.
>
>

Outros exemplos estão disponíveis em instruções passo a passo completas que demonstram todas as etapas do processo para **cenários específicos**. Eles estão listados e vinculados a descrições em miniatura no artigo [Instruções passo a passo de exemplo](walkthroughs.md). Eles ilustram como combinar a nuvem, as ferramentas locais e os serviços em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente.

> [!NOTE]
> Para implantações que usam o Azure Machine Learning Studio, consulte [Implantar um serviço Web do Machine Learning](../classic/deploy-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>Testes de A/B
Quando vários modelos estão em produção, pode ser útil executar [Testes de A/B](https://en.wikipedia.org/wiki/A/B_testing) para comparar o desempenho dos modelos. 

 
## <a name="next-steps"></a>Próximas etapas

[Acompanhar o progresso de projetos de ciência de dados](track-progress.md) mostra como um cientista de dados pode acompanhar o progresso de um projeto de ciência de dados.

A [Operação de modelo e CI/CD](ci-cd-flask.md) mostra como a CI/CD pode ser executada com modelos desenvolvidos.