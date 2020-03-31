---
title: Crie fluxos de trabalho de aprendizado de máquina orientados a eventos
titleSuffix: Azure Machine Learning
description: Aprenda a usar a grade de eventos com o Azure Machine Learning para habilitar soluções orientadas a eventos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 03/11/2020
ms.openlocfilehash: fe6125682f669e453100488b7e0afc4c49409588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129694"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>Criar fluxos de trabalho de aprendizado de máquina orientados a eventos (Preview)

[A Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) suporta eventos de Machine Learning do Azure. Você pode assinar e consumir eventos como o status de execução alterado, a conclusão da execução, o registro do modelo, a implantação do modelo e a detecção de deriva de dados dentro de um espaço de trabalho.

Para obter mais informações sobre os tipos de eventos, consulte [a integração do Azure Machine Learning com](concept-event-grid-integration.md) a Event Grid e o esquema de grade de eventos do [Azure Machine Learning](/azure/event-grid/event-schema-machine-learning).

Use a Event Grid para habilitar cenários comuns como:

* Envie e-mails em falha de execução e execute a conclusão
* Use uma função azure depois que um modelo é registrado
* Streaming de eventos do Azure Machine Learning para vários pontos finais
* Acione um gasoduto ML quando a deriva for detectada

> [!NOTE] 
> Atualmente, os eventos runStatusChanged só disparam quando o status de execução é **falhado**
>

## <a name="prerequisites"></a>Pré-requisitos
* O contribuinte ou o proprietário acesso ao espaço de trabalho Azure Machine Learning para o que você criará.

### <a name="configure-eventgrid-using-the-azure-portal"></a>Configure eventgrid usando o portal Azure

1. Abra o [portal Azure](https://portal.azure.com) e vá para o seu espaço de trabalho de Machine Learning do Azure.

1. Na barra esquerda, selecione __Eventos__ e selecione **Assinaturas de Eventos**. 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Selecione o tipo de evento para consumir. Por exemplo, a captura de tela a seguir __selecionou Modelo registrado,__ __Modelo implantado,__ __Execução concluída__e __deriva de conjunto de dados detectado:__

    ![adicionar-tipo de evento](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Selecione o ponto final para publicar o evento para. Na captura de tela a seguir, o __hub Event__ é o ponto final selecionado:

    ![manipulador de eventos selecionado](./media/how-to-use-event-grid/select-event-handler.png)

Depois de confirmar sua seleção, clique __em Criar__. Após a configuração, esses eventos serão empurrados para o seu ponto final.


### <a name="configure-eventgrid-using-the-cli"></a>Configure eventgrid usando a CLI

Você pode instalar o [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)mais recente ou usar o Azure Cloud Shell fornecido como parte de sua assinatura do Azure.

Para instalar a extensão Event Grid, use o seguinte comando da CLI:

```azurecli-interactive
az add extension --name eventgrid
```

O exemplo a seguir demonstra como selecionar uma assinatura do Azure e cria uma nova assinatura de evento para o Azure Machine Learning:

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

Ao configurar seus eventos, você pode aplicar filtros para somente ativar em dados específicos de eventos. No exemplo abaixo, para eventos de status de execução alterados, você pode filtrar por tipos de execução. O evento só é acionado quando os critérios são atendidos. Consulte o esquema da grade de [eventos do Azure Machine Learning](/azure/event-grid/event-schema-machine-learning) para saber mais sobre os dados do evento que você pode filtrar. 

1. Vá para o portal Azure, selecione uma nova assinatura ou uma já existente. 

1. Selecione a guia filtros e role até filtros avançados. Na **Chave** e **Valor** forneça os tipos de propriedade que você deseja filtrar. Aqui você pode ver que o evento só será acionado quando o tipo de execução for uma execução de pipeline ou uma etapa de pipeline.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="eventos de filtro":::

## <a name="sample-scenarios"></a>Cenários de exemplo

### <a name="use-a-logic-app-to-send-email-alerts"></a>Use um aplicativo lógico para enviar alertas de e-mail

Aproveite [os aplicativos azure logic para](https://docs.microsoft.com/azure/logic-apps/) configurar e-mails para todos os seus eventos. Personalize com condições e especifique os destinatários para permitir a colaboração e a conscientização entre as equipes que trabalham em conjunto.

1. No portal Azure, vá ao seu espaço de trabalho azure Machine Learning e selecione a guia de eventos na barra esquerda. A partir daqui, selecione __aplicativos Logic__. 

    ![select-logic-ap](./media/how-to-use-event-grid/select-logic-ap.png)

1. Entre na Logic App UI e selecione o serviço de Machine Learning como o tipo de tópico. 

    ![selecionar-tópico-tipo](./media/how-to-use-event-grid/select-topic-type.png)

1. Selecione para quais eventos serão notificados. Por exemplo, a seguinte captura de tela __RunCompleted__.

    ![select-event-runcomplete](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Você pode usar o método de filtragem na seção acima ou adicionar filtros para apenas acionar o aplicativo lógico em um subconjunto de tipos de eventos. Na captura de tela a seguir, um __filtro de prefixo__ de __/datadriftID/runs/__ é usado.

    ![eventos de filtro](./media/how-to-use-event-grid/filtering-events.png)

1. Em seguida, adicione um passo para consumir este evento e procure por e-mail. Existem várias contas de e-mail diferentes que você pode usar para receber eventos. Você também pode configurar condições sobre quando enviar um alerta de e-mail.

    ![seleção-e-mail-ação](./media/how-to-use-event-grid/select-email-action.png)

1. Selecione __Enviar um e-mail__ e preencher os parâmetros. No assunto, você pode incluir o __Tipo de Evento__ e O __Tópico__ para ajudar a filtrar eventos. Você também pode incluir um link para a página do espaço de trabalho para ser executado no corpo da mensagem. 

    ![configurar-e-mail-corpo](./media/how-to-use-event-grid/configure-email-body.png)

1. Para salvar esta ação, **selecione Salvar como** no canto esquerdo da página. Da barra direita que aparece, confirme a criação desta ação.

    ![confirmar-lógica-aplicativo-criar](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>Use um App Logic para ativar fluxos de trabalho de retreinamento quando ocorre deriva de dados

Os modelos ficam obsoletos ao longo do tempo, e não permanecem úteis no contexto em que está sendo executado. Uma maneira de dizer se é hora de retreinar o modelo é detectar a deriva de dados. 

Este exemplo mostra como usar a grade de eventos com um aplicativo Azure Logic para ativar o retreinamento. O exemplo desencadeia um pipeline da Fábrica de Dados Do Azure quando ocorre deriva de dados entre o treinamento de um modelo e o serviço de conjuntos de dados.

Antes de começar, execute as seguintes ações:

* Configure um monitor de conjunto de dados para [detectar a deriva de dados]( https://aka.ms/datadrift) em um espaço de trabalho
* Crie um pipeline publicado [da Fábrica de Dados Azure](https://docs.microsoft.com/azure/data-factory/).

Neste exemplo, um simples pipeline data factory é usado para copiar arquivos em uma loja de blob e executar um pipeline de Machine Learning publicado. Para obter mais informações sobre este cenário, veja como configurar uma [etapa de Machine Learning na Fábrica de Dados do Azure](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![adf-mlpipeline-stage](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Comece criando o aplicativo lógico. Vá para o [portal Azure,](https://portal.azure.com)procure por Aplicativos lógicos e selecione criar.

    ![aplicativo de pesquisa-lógica](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Preencha as informações solicitadas. Para simplificar a experiência, use o mesmo grupo de assinatura e recursos que o azure Data Factory Pipeline e o Azure Machine Learning.

    ![set-up-logic-app-for-adf](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Depois de criar o aplicativo lógico, __selecione Quando ocorrer um evento de recurso da Event Grid__. 

    ![selecionar-evento-grade-gatilho](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Faça login e preencha os detalhes do evento. Defina o __nome do recurso__ para o nome do espaço de trabalho. Defina o __tipo de evento__ como Conjunto de __dadosDriftDetected__.

    ![login e adicionar-evento](./media/how-to-use-event-grid/login-and-add-event.png)

1. Adicione um novo passo e procure __a Fábrica de Dados Do Azure__. Selecione __Criar uma execução de pipeline__. 

    ![create-adfpipeline-run](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Faça login e especifique o pipeline publicado da Fábrica de Dados do Azure para ser executado.

    ![especificar-adf-pipeline](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Salve e crie o aplicativo lógico usando o botão **salvar** no canto superior esquerdo da página. Para ver seu aplicativo, vá para o seu espaço de trabalho no [portal Azure](https://portal.azure.com) e clique em **Eventos**.

    ![show-logic-app-webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Agora, o pipeline de fábrica de dados é acionado quando ocorre deriva. Veja detalhes sobre sua corrida de deriva de dados e pipeline de aprendizado de máquina no [novo portal de espaço de trabalho](https://ml.azure.com). 

![espaço de exibição no trabalho](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>Use funções do Azure para implantar um modelo baseado em tags

Um objeto modelo azure Machine Learning contém parâmetros em que você pode pivotar implantações, como nome do modelo, versão, tag e propriedade. O evento de registro do modelo pode ativar um ponto final e você pode usar uma função Azure para implantar um modelo com base no valor desses parâmetros.

Por exemplo, consulte [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) o repositório e siga as etapas do arquivo **readme.**

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os eventos disponíveis, consulte o esquema de [eventos do Azure Machine Learning](/azure/event-grid/event-schema-machine-learning)
