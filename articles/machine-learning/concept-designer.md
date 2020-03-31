---
title: Construir modelos ML com designer
titleSuffix: Azure Machine Learning
description: Conheça os termos, conceitos e fluxo de trabalho que compõem o designer do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 11/12/2019
ms.openlocfilehash: 78a6e7fa8d030185f537136a3a2124d8bc59d808
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037619"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>O que é o designer do Azure Machine Learning (versão prévia)? 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

O designer de Machine Learning do Azure permite conectar visualmente [conjuntos de dados](#datasets) e [módulos](#module) em uma tela interativa para criar modelos de aprendizado de máquina. Para saber como começar com o designer, consulte [Tutorial: Prever o preço do automóvel com o designer](tutorial-designer-automobile-price-train-score.md)

![Exemplo do Azure Machine Learning Designer](./media/concept-designer/designer-drag-and-drop.gif)

O designer usa seu [espaço de trabalho](concept-workspace.md) de Machine Learning do Azure para organizar recursos compartilhados, como:

+ [Pipelines](#pipeline)
+ [Conjuntos de dados](#datasets)
+ [Recursos de computação](#compute)
+ [Modelos registrados](concept-azure-machine-learning-architecture.md#models)
+ [Gasodutos publicados](#publish)
+ [Pontos finais em tempo real](#deploy)

## <a name="model-training-and-deployment"></a>Treinamento e implantação de modelos

O designer lhe dá uma tela visual para construir, testar e implantar modelos de aprendizado de máquina. Com o designer você pode:

+ Arraste e solte [conjuntos](#datasets) de dados e [módulos](#module) na tela.
+ Conecte os módulos para criar um [rascunho de pipeline](#pipeline-draft).
+ Envie uma [execução de pipeline](#pipeline-run) usando os recursos de computação em seu espaço de trabalho azure Machine Learning.
+ Converta seus **pipelines de treinamento** **em oleodutos de inferência.**
+ [Publique](#publish) seus pipelines em um **ponto final do pipeline** REST para enviar novas executações de pipeline com diferentes parâmetros e conjuntos de dados.
    + Publique um **pipeline de treinamento** para reutilizar um único pipeline para treinar vários modelos enquanto altera parâmetros e conjuntos de dados.
    + Publique um **pipeline de inferência em lote** para fazer previsões sobre novos dados usando um modelo previamente treinado.
+ [Implante](#deploy) um **pipeline de inferência em tempo real** para um ponto final em tempo real para fazer previsões sobre novos dados em tempo real.

![Diagrama de fluxo de trabalho para treinamento, inferência em lote e inferência em tempo real no designer](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>Pipeline

Um [pipeline](concept-azure-machine-learning-architecture.md#ml-pipelines) consiste em conjuntos de dados e módulos analíticos, que você conecta juntos. Os gasodutos têm muitos usos: você pode fazer um gasoduto que treina um único modelo, ou um que treina vários modelos. Você pode criar um pipeline que faça previsões em tempo real ou em lote, ou fazer um pipeline que só limpa dados. Os pipelines permitem que você reutilize seu trabalho e organize seus projetos.

### <a name="pipeline-draft"></a>Rascunho de gasoduto

Ao editar um pipeline no designer, seu progresso é salvo como um **rascunho de pipeline**. Você pode editar um rascunho de pipeline a qualquer momento adicionando ou removendo módulos, configurando alvos de computação, criando parâmetros e assim por diante.

Um gasoduto válido tem essas características:

* Os conjuntos de dados só podem se conectar a módulos.
* Os módulos só podem se conectar a conjuntos de dados ou a outros módulos.
* Todas as portas de entrada para módulos devem ter alguma conexão com o fluxo de dados.
* Todos os parâmetros necessários para cada módulo devem ser definidos.

Quando você estiver pronto para executar o seu rascunho de pipeline, você envia uma corrida de pipeline.

### <a name="pipeline-run"></a>Execução do pipeline

Cada vez que você executa um pipeline, a configuração do pipeline e seus resultados são armazenados em seu espaço de trabalho como uma **execução de pipeline**. Você pode voltar a qualquer execução de pipeline para inspecioná-lo para fins de solução de problemas ou auditoria. **Clone** uma execução de pipeline para criar um novo rascunho de pipeline para você editar.

As corridas de pipeline são agrupadas em [experimentos](concept-azure-machine-learning-architecture.md#experiments) para organizar a história da execução. Você pode definir o experimento para cada execução de oleoduto. 

## <a name="datasets"></a>Conjunto de dados

Um conjunto de dados de aprendizado de máquina facilita o acesso e o trabalho com seus dados. Vários conjuntos de dados de amostra são incluídos no designer para você experimentar. Você pode [registrar](how-to-create-register-datasets.md) mais conjuntos de dados conforme precisar deles.

## <a name="module"></a>Módulo

Um módulo é um algoritmo que você pode executar em seus dados. O designer possui uma série de módulos que vão desde funções de ingestão de dados até processos de treinamento, pontuação e validação.

Um módulo pode ter um conjunto de parâmetros que você pode usar para configurar os algoritmos internos do módulo. Ao selecionar um módulo nas telas, os parâmetros do módulo são exibidos no painel Propriedades à direita das telas. Você pode modificar os parâmetros nesse painel para ajustar seu modelo. Você pode definir os recursos de computação para módulos individuais no designer. 

![Propriedades do módulo](./media/concept-designer/properties.png)

Para obter alguma ajuda para navegar pela biblioteca de algoritmos de aprendizado de máquina disponíveis, consulte [Algorithm & visão geral de referência do módulo](algorithm-module-reference/module-reference.md)

## <a name="compute-resources"></a><a name="compute"></a>Recursos de computação

Use recursos de computação do seu espaço de trabalho para executar seu pipeline e hospede seus modelos implantados como pontos finais em tempo real ou pontos finais do pipeline (para inferência em lote). Os destinos de computação com suporte são:

| Destino de computação | Treinamento | Implantação |
| ---- |:----:|:----:|
| Computação do Azure Machine Learning | ✓ | |
| Serviço de Kubernetes do Azure | | ✓ |

Os alvos de computação são anexados ao seu [espaço de trabalho azure Machine Learning](concept-workspace.md). Você gerencia seus alvos de computação em seu espaço de trabalho no [Azure Machine Learning Studio (clássico)](https://ml.azure.com).

## <a name="deploy"></a>Implantar

Para realizar a inferência em tempo real, você deve implantar um pipeline como um **ponto final em tempo real**. O ponto final em tempo real cria uma interface entre um aplicativo externo e seu modelo de pontuação. Uma chamada para um ponto final em tempo real retorna os resultados da previsão para o aplicativo em tempo real. Para fazer uma chamada para um ponto final em tempo real, você passa a tecla API criada quando você implantou o ponto final. O ponto final é baseado no REST, uma escolha de arquitetura popular para projetos de programação web.

Os pontos finais em tempo real devem ser implantados em um cluster do Azure Kubernetes Service.

Para saber como implantar seu modelo, consulte [Tutorial: Implante um modelo de aprendizado de máquina com o designer](tutorial-designer-automobile-price-deploy.md).

## <a name="publish"></a>Publicar

Você também pode publicar um pipeline para um **ponto final do pipeline**. Semelhante a um ponto final em tempo real, um ponto final do pipeline permite enviar novas executações de pipeline a partir de aplicativos externos usando chamadas REST. No entanto, você não pode enviar ou receber dados em tempo real usando um ponto final do pipeline.

Os gasodutos publicados são flexíveis, podem ser usados para treinar ou retreinar modelos, [realizar inferência em lote,](how-to-run-batch-predictions-designer.md)processar novos dados e muito mais. Você pode publicar vários pipelines em um único ponto final do pipeline e especificar qual versão de pipeline a ser executada.

Um pipeline publicado é executado nos recursos de computação que você define no rascunho do pipeline para cada módulo.

O designer cria o mesmo objeto [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) que o SDK.


## <a name="moving-from-the-visual-interface-to-the-designer"></a>Movendo-se da interface visual para o designer

A interface visual (visualização) foi atualizada e agora é a desenhista de Machine Learning (visualização). O designer foi rearquitetado para usar um backend baseado em pipeline que se integra totalmente com as outras características do Azure Machine Learning. 

Como resultado dessas atualizações, alguns conceitos e termos para a interface visual foram alterados ou renomeados. Veja a tabela abaixo para as mudanças conceituais mais importantes. 

| Conceito no designer | Anteriormente na interface visual |
| ---- |:----:|
| Rascunho de gasoduto | Experimento |
| Ponto final em tempo real | Serviço Web |

### <a name="migrating-to-the-designer"></a>Migrando para o designer

Você pode converter experimentos de interface visual e serviços web existentes em pipelines e pontos finais em tempo real no designer. Use as seguintes etapas para migrar seus ativos de interface visual:

[!INCLUDE [migrate from the visual interface](../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>Próximas etapas

* Aprenda o básico de análise preditiva e aprendizado de máquina com [Tutorial: Preveja o preço do automóvel com o designer](tutorial-designer-automobile-price-train-score.md)
* Aprenda a modificar [amostras de designers](samples-designer.md) existentes para adaptá-las às suas necessidades.

