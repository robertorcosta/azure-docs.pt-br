---
title: Disparar a execução de um pipeline de ML de um aplicativo lógico
titleSuffix: Azure Machine Learning
description: Saiba como disparar a execução de um pipeline de ML usando os aplicativos lógicos do Azure.
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 6bb976b8b310fb3eb4d0247a8d745599f688d7b5
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122851"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>Disparar uma execução de um pipeline de Machine Learning de um aplicativo lógico

Dispare a execução do seu pipeline de Azure Machine Learning quando novos dados forem exibidos. Por exemplo, talvez você queira disparar o pipeline para treinar um novo modelo quando novos dados aparecerem na conta de armazenamento de BLOBs. Configure o gatilho com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Prerequisites

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
    > ![modelo em branco](media/how-to-trigger-published-pipeline/blank-template.png)

1. No designer, pesquise por **blob**. Selecione o gatilho **quando um blob é adicionado ou modificado (somente Propriedades)** e adicione esse gatilho ao seu aplicativo lógico.
    > [!div class="mx-imgBorder"]
    > ![adicionar gatilho](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Preencha as informações de conexão para a conta de armazenamento de BLOBs que você deseja monitorar para adições ou modificações de BLOB. Selecione o contêiner a ser monitorado. 
 
    Escolha o **intervalo** e a **frequência** para sondar as atualizações que funcionam para você.  

    > [!NOTE]
    > Esse gatilho irá monitorar o contêiner selecionado, mas não monitorará subpastas.

1. Adicione uma ação HTTP que será executada quando um blob novo ou modificado for detectado. Selecione **+ nova etapa**, procure e selecione a ação http.

  > [!div class="mx-imgBorder"]
  > ![pesquisa de ação HTTP](media/how-to-trigger-published-pipeline/search-http.png)

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

    Use o `DataStoreName` adicionado ao seu espaço de trabalho como um [pré-requisito](#prerequisites).
     
    > [!div class="mx-imgBorder"]
    > ![Configurações HTTP](media/how-to-trigger-published-pipeline/http-settings.png)

1. Selecione **salvar** e sua agenda agora está pronta.