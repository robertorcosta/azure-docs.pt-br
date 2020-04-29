---
title: Criar fluxos de trabalho de aprendizado de máquina controlados por eventos
titleSuffix: Azure Machine Learning
description: Saiba como usar a grade de eventos com Azure Machine Learning para habilitar soluções controladas por eventos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 03/11/2020
ms.openlocfilehash: 2a1440dcda27a487c89be4ac63e624a2bb6b393a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82111871"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>Criar fluxos de trabalho de aprendizado de máquina controlados por evento (versão prévia)

A [grade de eventos do Azure](https://docs.microsoft.com/azure/event-grid/) dá suporte a eventos Azure Machine Learning. Você pode assinar e consumir eventos como o status de execução alterado, executar conclusão, registro de modelo, implantação de modelo e detecção de descompasso de dados em um espaço de trabalho.

Para obter mais informações sobre os tipos de evento, consulte [Azure Machine Learning integração com a grade de eventos](concept-event-grid-integration.md) e o [Azure Machine Learning esquema da grade de eventos](/azure/event-grid/event-schema-machine-learning).

Use a grade de eventos para habilitar cenários comuns, como:

* Enviar emails durante a execução e a conclusão da execução
* Usar uma função do Azure depois que um modelo é registrado
* Transmitindo eventos de Azure Machine Learning para vários pontos de extremidade
* Disparar um pipeline ML quando o descompasso for detectado

> [!NOTE] 
> Atualmente, os eventos runStatusChanged são disparados apenas quando o status de execução é **com falha**
>

## <a name="prerequisites"></a>Pré-requisitos
* Acesso de colaborador ou proprietário ao espaço de trabalho Azure Machine Learning você criará eventos para o.

### <a name="configure-eventgrid-using-the-azure-portal"></a>Configurar o EventGrid usando o portal do Azure

1. Abra o [portal do Azure](https://portal.azure.com) e vá para seu espaço de trabalho do Azure Machine Learning.

1. Na barra à esquerda, selecione __eventos__ e, em seguida, selecione **assinaturas de evento**. 

    ![Select-Events-in-Workspace. png](./media/how-to-use-event-grid/select-event.png)

1. Selecione o tipo de evento a ser consumido. Por exemplo, a captura de tela a seguir selecionou o __modelo registrado__, __modelo implantado__, __execução concluída__e __descompasso de conjunto de banco__de e detectado:

    ![Adicionar tipo de evento](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Selecione o ponto de extremidade no qual publicar o evento. Na captura de tela a seguir, o __Hub de eventos__ é o ponto de extremidade selecionado:

    ![manipulador de eventos Select](./media/how-to-use-event-grid/select-event-handler.png)

Depois de confirmar sua seleção, clique em __criar__. Após a configuração, esses eventos serão enviados por push para o ponto de extremidade.


### <a name="configure-eventgrid-using-the-cli"></a>Configurar o EventGrid usando a CLI

Você pode instalar a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)mais recente ou usar o Azure cloud Shell fornecido como parte de sua assinatura do Azure.

Para instalar a extensão da grade de eventos, use o seguinte comando da CLI:

```azurecli-interactive
az add extension --name eventgrid
```

O exemplo a seguir demonstra como selecionar uma assinatura do Azure e cria e uma nova assinatura de evento para Azure Machine Learning:

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace.
az eventgrid event-subscription create \
  --name {eventGridFilterName} \
  --source-resource-id "/subscriptions/{subId}/resourceGroups/{rgName}/ \providers/Microsoft.MachineLearningServices/workspaces/{wsName}" \
  --endpoint {event handler endpoint} \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="filter-events"></a>Filtrar eventos

Ao configurar seus eventos, você pode aplicar filtros para disparar apenas dados de evento específicos. No exemplo a seguir, para eventos de status de execução alterados, você pode filtrar por tipos de execução. O evento só dispara quando os critérios são atendidos. Consulte o [Azure Machine Learning esquema da grade de eventos](/azure/event-grid/event-schema-machine-learning) para saber mais sobre os dados de evento pelos quais você pode filtrar. 

1. Vá para a portal do Azure, selecione uma nova assinatura ou uma existente. 

1. Selecione a guia filtros e role para baixo até filtros avançados. Para a **chave** e o **valor**, forneça os tipos de propriedade que você deseja filtrar. Aqui você pode ver que o evento só será disparado quando o tipo de execução for uma execução de pipeline ou etapa de pipeline.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="filtrar eventos":::

## <a name="sample-send-email-alerts"></a>Exemplo: enviar alertas por email

Use os [aplicativos lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/) para configurar emails para todos os seus eventos. Personalize com condições e especifique os destinatários para permitir a colaboração e o reconhecimento entre equipes trabalhando juntas.

1. Na portal do Azure, vá para o espaço de trabalho do Azure Machine Learning e selecione a guia eventos na barra à esquerda. Aqui, selecione __aplicativos lógicos__. 

    ![Select-Logic-AP](./media/how-to-use-event-grid/select-logic-ap.png)

1. Entre na interface do usuário do aplicativo lógico e selecione Machine Learning serviço como o tipo de tópico. 

    ![Select – tópico-Type](./media/how-to-use-event-grid/select-topic-type.png)

1. Selecione para quais eventos serão notificados. Por exemplo, a captura de tela a seguir __RunCompleted__.

    ![Select-Event-runcomplete](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Você pode usar o método de filtragem na seção acima ou adicionar filtros para disparar apenas o aplicativo lógico em um subconjunto de tipos de evento. Na captura de tela a seguir, um __filtro de prefixo__ de __/datadriftID/runs/__ é usado.

    ![filtrar-eventos](./media/how-to-use-event-grid/filtering-events.png)

1. Em seguida, adicione uma etapa para consumir esse evento e pesquise por email. Há várias contas de email diferentes que você pode usar para receber eventos. Você também pode configurar condições em quando enviar um alerta por email.

    ![Select-email-ação](./media/how-to-use-event-grid/select-email-action.png)

1. Selecione __enviar um email__ e preencha os parâmetros. No assunto, você pode incluir o __tipo de evento__ e o __tópico__ para ajudar a filtrar eventos. Você também pode incluir um link para a página do espaço de trabalho para execuções no corpo da mensagem. 

    ![Configurar-email-corpo](./media/how-to-use-event-grid/configure-email-body.png)

1. Para salvar essa ação, selecione **salvar como** no canto esquerdo da página. Na barra à direita que aparece, confirme a criação desta ação.

    ![Confirm-lógica-app-Create](./media/how-to-use-event-grid/confirm-logic-app-create.png)


## <a name="sample-trigger-retraining-when-data-drift-occurs"></a>Exemplo: disparar o retreinamento quando ocorrer descompasso de dados

Os modelos ficam obsoletos ao longo do tempo e não permanecem úteis no contexto em que ele está sendo executado. Uma maneira de saber se é hora de treinar novamente o modelo é detectar descompasso de dados. 

Este exemplo mostra como usar a grade de eventos com um aplicativo lógico do Azure para disparar o novo treinamento. O exemplo dispara um pipeline de Azure Data Factory quando o descompasso de dados ocorre entre o treinamento de um modelo e o fornecimento de conjuntos.

Antes de começar, execute as seguintes ações:

* Configurar um monitor de DataSet para [detectar descompasso de dados]( https://aka.ms/datadrift) em um espaço de trabalho
* Crie um [pipeline de Azure data Factory](https://docs.microsoft.com/azure/data-factory/)publicado.

Neste exemplo, um pipeline de Data Factory simples é usado para copiar arquivos em um repositório de BLOB e executar um pipeline de Machine Learning publicado. Para obter mais informações sobre esse cenário, consulte como configurar uma [etapa de Machine Learning no Azure data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![ADF-mlpipeline-Stage](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Comece com a criação do aplicativo lógico. Vá para a [portal do Azure](https://portal.azure.com), procure por aplicativos lógicos e selecione criar.

    ![Pesquisar-lógica-aplicativo](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Preencha as informações solicitadas. Para simplificar a experiência, use a mesma assinatura e grupo de recursos que o pipeline de Azure Data Factory e o espaço de trabalho do Azure Machine Learning.

    ![set-up-Logic-App-for-ADF](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Depois de criar o aplicativo lógico, selecione __quando ocorrer um evento de recurso de grade de eventos__. 

    ![seletor de evento-grade-de-gatilho](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Faça logon e preencha os detalhes do evento. Defina o __nome do recurso__ como o nome do espaço de trabalho. Defina o __tipo de evento__ como __DatasetDriftDetected__.

    ![logon-e-Add-Event](./media/how-to-use-event-grid/login-and-add-event.png)

1. Adicione uma nova etapa e procure __Azure data Factory__. Selecione __criar uma execução de pipeline__. 

    ![criar-adfpipeline-executar](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Faça logon e especifique o pipeline de Azure Data Factory publicado a ser executado.

    ![especificar-ADF-pipeline](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Salve e crie o aplicativo lógico usando o botão **salvar** na parte superior esquerda da página. Para exibir seu aplicativo, acesse seu espaço de trabalho no [portal do Azure](https://portal.azure.com) e clique em **eventos**.

    ![show-Logic-app-webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Agora, o pipeline data factory é disparado quando ocorre descompasso. Exiba detalhes sobre a execução de descompasso de dados e o pipeline de Machine Learning no [novo portal de espaço de trabalho](https://ml.azure.com). 

![exibir-no-espaço de trabalho](./media/how-to-use-event-grid/view-in-workspace.png)

## <a name="sample-deploy-a-model-based-on-tags"></a>Exemplo: implantar um modelo com base em marcas

Um objeto de modelo de Azure Machine Learning contém parâmetros que você pode dinamizar implantações como nome do modelo, versão, marca e propriedade. O evento de registro de modelo pode disparar um ponto de extremidade e você pode usar uma função do Azure para implantar um modelo com base no valor desses parâmetros.

Para obter um exemplo, consulte [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) o repositório e siga as etapas no arquivo **Leiame** .

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os eventos disponíveis, consulte o [esquema de evento Azure Machine Learning](/azure/event-grid/event-schema-machine-learning)
