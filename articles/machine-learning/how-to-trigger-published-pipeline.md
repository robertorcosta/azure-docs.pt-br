---
title: Disparar o pipeline ML para novos dados
titleSuffix: Azure Machine Learning
description: Saiba como disparar a execução de um pipeline de Azure Machine Learning usando o aplicativo lógico do Azure para responder a novos dados.
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.date: 02/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4
ms.openlocfilehash: 20d44fd3150f9da31e9c242017e597d4f46e4d5d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963922"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>Disparar uma execução de um pipeline de Machine Learning de um aplicativo lógico

Dispare a execução do seu pipeline de Azure Machine Learning quando novos dados forem exibidos. Por exemplo, talvez você queira disparar o pipeline para treinar um novo modelo quando novos dados aparecerem na conta de armazenamento de BLOBs. Configure o gatilho com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [criar um Azure Machine Learning espaço de trabalho](how-to-manage-workspace.md).

* O ponto de extremidade REST para um pipeline de Machine Learning publicado. [Crie e publique seu pipeline](how-to-create-your-first-pipeline.md). Em seguida, localize o ponto de extremidade REST de seu PublishedPipeline usando a ID do pipeline:
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* [Armazenamento de BLOBs do Azure](../storage/blobs/storage-blobs-overview.md) para armazenar seus dados.
* [Um repositório de armazenamento](how-to-access-data.md) em seu espaço de trabalho que contém os detalhes da sua conta de armazenamento de BLOBs.

## <a name="create-a-logic-app"></a>Criar um aplicativo lógico

Agora, crie uma instância [do aplicativo lógico do Azure](../logic-apps/logic-apps-overview.md) . Se desejar, [use um ISE (ambiente do serviço de integração)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) e [Configure uma chave gerenciada pelo cliente](../logic-apps/customer-managed-keys-integration-service-environment.md) para ser usada pelo seu aplicativo lógico.

Depois que seu aplicativo lógico tiver sido provisionado, use estas etapas para configurar um gatilho para seu pipeline:

1. [Crie uma identidade gerenciada atribuída pelo sistema](../logic-apps/create-managed-service-identity.md) para conceder ao aplicativo acesso ao seu Workspace do Azure Machine Learning.

1. Navegue até o modo de exibição designer de aplicativo lógico e selecione o modelo aplicativo lógico em branco. 
    > [!div class="mx-imgBorder"]
    > ![Modelo em branco](media/how-to-trigger-published-pipeline/blank-template.png)

1. No designer, pesquise por **blob**. Selecione o gatilho **quando um blob é adicionado ou modificado (somente Propriedades)** e adicione esse gatilho ao seu aplicativo lógico.
    > [!div class="mx-imgBorder"]
    > ![Adicionar gatilho](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Preencha as informações de conexão para a conta de armazenamento de BLOBs que você deseja monitorar para adições ou modificações de BLOB. Selecione o contêiner a ser monitorado. 
 
    Escolha o **intervalo** e a **frequência** para sondar as atualizações que funcionam para você.  

    > [!NOTE]
    > Esse gatilho irá monitorar o contêiner selecionado, mas não monitorará subpastas.

1. Adicione uma ação HTTP que será executada quando um blob novo ou modificado for detectado. Selecione **+ nova etapa**, procure e selecione a ação http.

  > [!div class="mx-imgBorder"]
  > ![Pesquisar ação HTTP](media/how-to-trigger-published-pipeline/search-http.png)

  Use as seguintes configurações para configurar sua ação:

  | Configuração | Valor | 
  |---|---|
  | Ação HTTP | POST |
  | URI |o ponto de extremidade para o pipeline publicado que você encontrou como um [pré-requisito](#prerequisites) |
  | Modo de autenticação | Identidade Gerenciada |

1. Configure sua agenda para definir o valor de qualquer [pipelineparameters de DataPath](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) que você possa ter:

    ```json
    "DataPathAssignments": { 
         "input_datapath": { 
                            "DataStoreName": "<datastore-name>", 
                            "RelativePath": "@triggerBody()?['Name']" 
    } 
    }, 
    "ExperimentName": "MyRestPipeline", 
    "ParameterAssignments": { 
    "input_string": "sample_string3" 
    },
    ```

    Use o `DataStoreName` que você adicionou ao seu espaço de trabalho como um [pré-requisito](#prerequisites).
     
    > [!div class="mx-imgBorder"]
    > ![Configurações HTTP](media/how-to-trigger-published-pipeline/http-settings.png)

1. Selecione **salvar** e sua agenda agora está pronta.

> [!IMPORTANT]
> Se você estiver usando o controle de acesso baseado em função do Azure (RBAC do Azure) para gerenciar o acesso ao seu pipeline, [defina as permissões para seu cenário de pipeline (treinamento ou pontuação)](how-to-assign-roles.md#common-scenarios).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte:

> [!div class="nextstepaction"]
> [Usar o Azure Machine Learning Pipelines para pontuação do lote](tutorial-pipeline-batch-scoring-classification.md)

* Saiba mais sobre [pipelines](concept-ml-pipelines.md)
* Saiba mais sobre como [explorar Azure Machine Learning com Jupyter](samples-notebooks.md)

