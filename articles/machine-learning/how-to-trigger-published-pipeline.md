---
title: Acione a execução de um pipeline ML a partir de um aplicativo lógico
titleSuffix: Azure Machine Learning
description: Aprenda a ativar a execução de um pipeline ML usando aplicativos azure logic.
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 6bb976b8b310fb3eb4d0247a8d745599f688d7b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122851"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>Acione uma execução de um pipeline de Aprendizado de Máquina a partir de um aplicativo lógico

Acione a execução do seu Azure Machine Learning Pipeline quando novos dados aparecerem. Por exemplo, você pode querer acionar o pipeline para treinar um novo modelo quando novos dados aparecerem na conta de armazenamento blob. Configure o gatilho com [o Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho de aprendizado de máquina do Azure](how-to-manage-workspace.md).

* O ponto final REST para um pipeline de Machine Learning publicado. [Crie e publique seu pipeline](how-to-create-your-first-pipeline.md). Em seguida, encontre o ponto final REST do seu PublishedPipeline usando o ID do pipeline:
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* [Armazenamento de blob do Azure](../storage/blobs/storage-blobs-overview.md) para armazenar seus dados.
* [Um armazenamento de dados](how-to-access-data.md) em seu espaço de trabalho que contém os detalhes da sua conta de armazenamento blob.

## <a name="create-a-logic-app"></a>Criar um aplicativo lógico

Agora crie [uma instância do Azure Logic App.](../logic-apps/logic-apps-overview.md) Se desejar, [use um ambiente de serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) e [configure uma chave gerenciada pelo cliente](../logic-apps/customer-managed-keys-integration-service-environment.md) para uso pelo seu App Logic.

Uma vez que o aplicativo logic tenha sido provisionado, use essas etapas para configurar um gatilho para o seu pipeline:

1. [Crie uma identidade gerenciada atribuída ao sistema](../logic-apps/create-managed-service-identity.md) para dar ao aplicativo acesso ao seu Espaço de Trabalho de Aprendizagem de Máquina do Azure.

1. Navegue até a exibição Logic App Designer e selecione o modelo do Aplicativo de lógica em branco. 
    > [!div class="mx-imgBorder"]
    > ![Modelo em branco](media/how-to-trigger-published-pipeline/blank-template.png)

1. No Designer, procure por **blob**. Selecione o **gatilho Quando uma bolha é adicionada ou modificada (somente propriedades)** e adicione este gatilho ao seu Aplicativo Lógico.
    > [!div class="mx-imgBorder"]
    > ![Adicionar gatilho](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Preencha as informações de conexão da conta de armazenamento Blob que você deseja monitorar para adições ou modificações de blob. Selecione o Contêiner para monitorar. 
 
    Escolha o **Intervalo** e **a Frequência** para verificar as atualizações que funcionam para você.  

    > [!NOTE]
    > Este gatilho monitorará o contêiner selecionado, mas não monitorará subpastas.

1. Adicione uma ação HTTP que será executada quando uma bolha nova ou modificada for detectada. Selecione **+ Novo Passo**, em seguida, procure e selecione a ação HTTP.

  > [!div class="mx-imgBorder"]
  > ![Procure por ação HTTP](media/how-to-trigger-published-pipeline/search-http.png)

  Use as seguintes configurações para configurar sua ação:

  | Configuração | Valor | 
  |---|---|
  | Ação HTTP | POST |
  | URI |o ponto final para o pipeline publicado que você encontrou como um [pré-requisito](#prerequisites) |
  | Modo de autenticação | Identidade Gerenciada |

1. Configure seu cronograma para definir o valor de qualquer [PipelineDo DataPathparâmetros](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) que você possa ter:

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

    Use `DataStoreName` o que você adicionou ao seu espaço de trabalho como [pré-requisito](#prerequisites).
     
    > [!div class="mx-imgBorder"]
    > ![Configurações HTTP](media/how-to-trigger-published-pipeline/http-settings.png)

1. Selecione **Salvar** e sua agenda está pronta.