---
title: Executar previsões em lote usando o designer do Azure Machine Learning (versão prévia)
titleSuffix: Azure Machine Learning
description: Saiba como treinar um modelo e configurar um pipeline de previsão em lote usando o designer. Implante o pipeline como um serviço Web com parâmetro, que pode ser disparado de qualquer biblioteca HTTP.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 11/04/2019
ms.custom: Ignite2019
ms.openlocfilehash: c496e57ea5f4b5b7fcda4f9c43c511488ef3c246
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73509430"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer"></a>Executar previsões em lote usando o designer do Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Nestas instruções, você aprenderá a usar o designer para treinar um modelo e configurar um pipeline de previsão em lote e um serviço Web. A previsão em lote possibilita pontuação contínua e sob demanda de modelos treinados em grandes conjuntos de dados, opcionalmente configurados como um serviço Web que pode ser disparado de qualquer biblioteca HTTP. 

Para configurar serviços de pontuação de lote usando o SDK, confira as [instruções](how-to-run-batch-predictions.md) complementares.

Nestas instruções, você aprenderá as seguintes tarefas:

> [!div class="checklist"]
> * Criar um experimento de ML básico em um pipeline
> * Criar um pipeline de inferência em lote com parâmetros
> * Gerenciar e executar pipelines manualmente ou de um ponto de extremidade REST

## <a name="prerequisites"></a>Pré-requisitos

1. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do serviço do Azure Machine Learning](https://aka.ms/AMLFree).

1. Crie um [workspace](tutorial-1st-experiment-sdk-setup.md).

1. Entre no [Estúdio do Azure Machine Learning](https://ml.azure.com/).

Estas instruções pressupõem um conhecimento básico da criação de um pipeline simples no designer. Para obter uma introdução guiada ao designer, conclua o [tutorial](tutorial-designer-automobile-price-train-score.md). 

## <a name="create-a-pipeline"></a>Criar um pipeline

Para criar um pipeline de inferência em lote, primeiro você precisa de um experimento de aprendizado de máquina. Para criar um, navegue até a guia **Designer** em seu workspace e crie um pipeline selecionando a opção **Módulos predefinidos fáceis de usar**.

![Página inicial do designer](media/how-to-run-batch-predictions-ui/ui-batch-scoring-1.png)

Veja a seguir um modelo de machine learning simples para fins de demonstração. Os dados são um conjunto de dados registrado criado com base nos dados de diabetes do Azure Open Datasets. Consulte a [seção de instruções](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets) para registrar Conjuntos de dados no Azure Open Datasets. Os dados são divididos em conjuntos de treinamento e validação, e uma árvore de decisão aumentada é treinada e pontuada. O pipeline deve ser executado pelo menos uma vez para poder criar um pipeline de inferência. Clique no botão **Executar** para executar o pipeline.

![Criar um experimento simples](media/how-to-run-batch-predictions-ui/ui-batch-scoring-2.png)

## <a name="create-a-batch-inference-pipeline"></a>Criar um pipeline de inferência em lote

Agora que o pipeline foi executado, há uma nova opção disponível ao lado de **Executar** e **Publicar** chamada **Criar pipeline de inferência**. Clique na lista suspensa e selecione **Pipeline de inferência em lote**.

![Criar pipeline de inferência em lote](media/how-to-run-batch-predictions-ui/ui-batch-scoring-5.png)

O resultado é um pipeline de inferência em lote padrão. Isso inclui um nó para a configuração do experimento de pipeline original, um nó para dados brutos para pontuação e um nó para pontuar os dados brutos em relação ao seu pipeline original.

![Pipeline de inferência em lote padrão](media/how-to-run-batch-predictions-ui/ui-batch-scoring-6.png)

Você pode adicionar outros nós para alterar o comportamento do processo de inferência em lote. Neste exemplo, você adiciona um nó para realizar amostragem aleatoriamente com base nos dados de entrada antes da pontuação. Crie uma nó de **Partição e Exemplo** e coloque-o entre os dados brutos e nós de pontuação. Em seguida, clique no nó **Partição e Exemplo** para obter acesso às configurações e aos parâmetros.

![Novo nó](media/how-to-run-batch-predictions-ui/ui-batch-scoring-7.png)

O parâmetro *Taxa de amostragem* controla qual o percentual do conjunto de dados original do qual extrair um exemplo aleatório. Esse é um parâmetro que será útil para ser ajustado com frequência. Portanto, habilite-o como um parâmetro de pipeline. Os parâmetros de pipeline podem ser alterados em tempo de execução e especificados em um objeto de carga ao executar novamente o pipeline de um ponto de extremidade REST. 

Para habilitar esse campo como um parâmetro de pipeline, clique nas reticências em cima do campo e em **Adicionar ao parâmetro de pipeline**. 

![Configuração de exemplo](media/how-to-run-batch-predictions-ui/ui-batch-scoring-8.png)

Em seguida, dê ao parâmetro um nome e um valor padrão. O nome será usado para identificar o parâmetro e especificá-lo em uma chamada REST.

![Parâmetro do pipeline](media/how-to-run-batch-predictions-ui/ui-batch-scoring-9.png)

## <a name="deploy-batch-inferencing-pipeline"></a>Implantar o pipeline de inferência em lote

Agora você já pode implantar o pipeline. Clique no botão **Implantar**, que abre a interface para configurar um ponto de extremidade. Clique na lista suspensa e selecione **Novo PipelineEndpoint**.

![Implantação do pipeline](media/how-to-run-batch-predictions-ui/ui-batch-scoring-10.png)

Dê ao ponto de extremidade um nome e uma descrição opcional. Próximo à parte inferior, você verá o parâmetro `sample-rate` configurado com um valor padrão de 0,8. Quando estiver pronto, clique em **Implantar**.

![Configurar ponto de extremidade](media/how-to-run-batch-predictions-ui/ui-batch-scoring-11.png)

## <a name="manage-endpoints"></a>Gerenciar pontos de extremidade 

Após a conclusão da implantação, acesse a guia **Pontos de extremidade** e clique no nome do ponto que você acabou de criar.

![Link do ponto de extremidade](media/how-to-run-batch-predictions-ui/ui-batch-scoring-12.png)

Esta tela mostra todos os pipelines publicados no ponto de extremidade específico. Clique em seu pipeline de inferência.

![Pipeline de inferência](media/how-to-run-batch-predictions-ui/ui-batch-scoring-13.png)

A página de detalhes do pipeline mostra o histórico de execução detalhado e as informações da cadeia de conexão para seu pipeline. Clique no botão **Executar** para criar uma execução manual do pipeline.

![Detalhes do pipeline](media/how-to-run-batch-predictions-ui/ui-batch-scoring-14.png)

Na configuração da execução, você pode fornecer uma descrição da execução e alterar o valor dos parâmetros de pipeline. Desta vez, execute novamente o pipeline inferência com uma taxa de amostragem de 0,9. Clique em **Executar** para executar o pipeline.

![Execução do pipeline](media/how-to-run-batch-predictions-ui/ui-batch-scoring-15.png)

A guia **Consumir** contém o ponto de extremidade REST para executar novamente seu pipeline. Para realizar uma chamada REST, você precisará de um cabeçalho de autenticação do tipo portador do OAuth 2.0. Confira a [seção do tutorial](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) a seguir para obter mais detalhes sobre como configurar a autenticação para seu workspace e realizar uma chamada REST.

## <a name="next-steps"></a>Próximas etapas

Siga o [tutorial](tutorial-designer-automobile-price-train-score.md) do Designer para treinar e implantar um modelo de regressão.