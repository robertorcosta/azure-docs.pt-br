---
title: Eventos de gatilho em fluxos de trabalho am
titleSuffix: Azure Machine Learning
description: Saiba como disparar aplicativos orientados a eventos, processos ou fluxos de trabalho de CI/CD com base em eventos de Azure Machine Learning para simplificar o ciclo de vida de am.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 03/11/2020
ms.openlocfilehash: 236cc46bb6f9e5ed95e4a49068ac41ae77a736f5
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982863"
---
# <a name="trigger-event-driven-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>Disparar aplicativos orientados a eventos, processos ou fluxos de trabalho de CI/CD com base em eventos de Azure Machine Learning (versão prévia)

Neste artigo, você aprende a configurar aplicativos orientados a eventos, processos ou fluxos de trabalho de CI/CD com base em eventos de Azure Machine Learning, como emails de notificação de falha ou execuções de pipeline de ML, quando determinadas condições são detectadas pela [grade de eventos do Azure](https://docs.microsoft.com/azure/event-grid/). 

O Azure Machine Learning gerencia todo o ciclo de vida do processo de aprendizado de máquina, incluindo treinamento de modelo, implantação de modelo e monitoramento. Você pode usar a grade de eventos para reagir a Azure Machine Learning eventos, como a conclusão de execuções de treinamento, o registro e a implantação de modelos e a detecção de descompasso de dados, usando arquiteturas modernas sem servidor. Você pode assinar e consumir eventos como o status de execução alterado, executar conclusão, registro de modelo, implantação de modelo e detecção de descompasso de dados em um espaço de trabalho.

Quando usar a grade de eventos para ações acionadas por evento:
* Enviar emails durante a execução e a conclusão da execução
* Usar uma função do Azure depois que um modelo é registrado
* Transmitindo eventos de Azure Machine Learning para vários pontos de extremidade
* Disparar um pipeline ML quando o descompasso for detectado

> [!NOTE] 
> Atualmente, os eventos runStatusChanged são disparados apenas quando o status de execução é **com falha**

## <a name="prerequisites"></a>Pré-requisitos
Para usar a grade de eventos, você precisa de acesso de colaborador ou proprietário ao espaço de trabalho de Azure Machine Learning para o qual você criará eventos.

## <a name="the-event-model--types"></a>O modelo de evento & tipos

A grade de eventos do Azure lê eventos de fontes, como Azure Machine Learning e outros serviços do Azure. Esses eventos são enviados para manipuladores de eventos, como hubs de eventos do Azure, Azure Functions, aplicativos lógicos e outros. O diagrama a seguir mostra como a grade de eventos conecta fontes e manipuladores, mas não é uma lista abrangente de integrações com suporte.

![Modelo funcional da Grade de Eventos do Azure](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Para obter mais informações sobre fontes de eventos e manipuladores de eventos, consulte [o que é a grade de eventos?](/azure/event-grid/overview).

### <a name="event-types-for-azure-machine-learning"></a>Tipos de evento para Azure Machine Learning

Azure Machine Learning fornece eventos nos vários pontos do ciclo de vida do Machine Learning: 

| Tipo de evento | Descrição |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Gerado quando uma execução do experimento do Machine Learning é concluída |
| `Microsoft.MachineLearningServices.ModelRegistered` | Gerado quando um modelo de aprendizado de máquina é registrado no espaço de trabalho |
| `Microsoft.MachineLearningServices.ModelDeployed` | Gerado quando uma implantação do serviço de inferência com um ou mais modelos é concluída |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Gerado quando um trabalho de detecção de descompasso de dados para dois conjuntos de dados é concluído |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Gerado quando um status de execução é alterado, atualmente só é gerado quando um status de execução é ' Failed ' |

### <a name="filter--subscribe-to-events"></a>Filtrar & assinar eventos

Esses eventos são publicados por meio da grade de eventos do Azure. Usando portal do Azure, PowerShell ou CLI do Azure, os clientes podem facilmente assinar eventos [especificando um ou mais tipos de evento e condições de filtragem](/azure/event-grid/event-filtering). 

Ao configurar seus eventos, você pode aplicar filtros para disparar apenas dados de evento específicos. No exemplo a seguir, para eventos de status de execução alterados, você pode filtrar por tipos de execução. O evento só dispara quando os critérios são atendidos. Consulte o [Azure Machine Learning esquema da grade de eventos](/azure/event-grid/event-schema-machine-learning) para saber mais sobre os dados de evento pelos quais você pode filtrar. 

As assinaturas para eventos de Azure Machine Learning são protegidas pelo RBAC (controle de acesso baseado em função). Somente o [colaborador ou o proprietário](how-to-assign-roles.md#default-roles) de um espaço de trabalho pode criar, atualizar e excluir assinaturas de evento.  Os filtros podem ser aplicados a assinaturas de evento durante a [criação](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) da assinatura de evento ou em um momento posterior. 


1. Vá para a portal do Azure, selecione uma nova assinatura ou uma existente. 

1. Selecione a guia filtros e role para baixo até filtros avançados. Para a **chave** e o **valor**, forneça os tipos de propriedade que você deseja filtrar. Aqui você pode ver que o evento só será disparado quando o tipo de execução for uma execução de pipeline ou etapa de pipeline.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="filtrar eventos":::


+ **Filtrar por tipo de evento:** Uma assinatura de evento pode especificar um ou mais tipos de evento Azure Machine Learning.

+ **Filtrar por assunto do evento:** A grade de eventos do Azure dá suporte a filtros de assunto com base em __começa com__ e __termina com__ as correspondências, para que os eventos com um assunto correspondente sejam entregues ao Assinante. Diferentes eventos de aprendizado de máquina têm um formato de assunto diferente.

  | Tipo de evento | Formato da entidade | Assunto de exemplo |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **Filtragem avançada**: a grade de eventos do Azure também dá suporte à filtragem avançada com base no esquema de evento publicado. Azure Machine Learning detalhes do esquema de eventos podem ser encontrados no [esquema de evento da grade de eventos do Azure para Azure Machine Learning](../event-grid/event-schema-machine-learning.md).  Alguns filtros avançados de exemplo que você pode executar incluem:

  Para `Microsoft.MachineLearningServices.ModelRegistered` evento, para filtrar o valor da marca do modelo:

  ```
  --advanced-filter data.ModelTags.key1 StringIn ('value1')
  ```

  Para saber mais sobre como aplicar filtros, consulte [filtrar eventos para a grade de eventos](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="consume-machine-learning-events"></a>Consumir Machine Learning eventos

Os aplicativos que manipulam Machine Learning eventos devem seguir algumas práticas recomendadas:

> [!div class="checklist"]
> * Como várias assinaturas podem ser configuradas para rotear eventos para o mesmo manipulador de eventos, é importante não pressupor que os eventos sejam de uma fonte específica, mas para verificar o tópico da mensagem a fim de garantir que ele venha do espaço de trabalho do Machine Learning que você espera.
> * Da mesma forma, verifique se o eventType é do tipo que você está preparado para processar, e não suponha que todos os eventos recebidos serão os tipos esperados.
> * Como as mensagens podem chegar fora de ordem e após algum atraso, use os campos de etag para entender se suas informações sobre objetos ainda estão atualizadas.  Além disso, use os campos do sequenciador para entender a ordem de eventos em qualquer objeto específico.
> * Ignore os campos que você não entende. Essa prática ajudará você a manter-se resiliente a novos recursos que possam ser adicionados no futuro.
> * As operações de Azure Machine Learning com falha ou canceladas não dispararão um evento. Por exemplo, se uma implantação de modelo falhar, o Microsoft. MachineLearningServices. ModelDeployed não será disparado. Considere esse modo de falha ao projetar seus aplicativos. Você sempre pode usar Azure Machine Learning SDK, CLI ou portal para verificar o status de uma operação e entender os motivos de falha detalhados.

A grade de eventos do Azure permite que os clientes criem manipuladores de mensagens desacopladas, que podem ser disparados por Azure Machine Learning eventos. Alguns exemplos notáveis de manipuladores de mensagens são:
* Funções do Azure
* Aplicativos Lógicos do Azure
* Hubs de eventos do Azure
* Pipeline de Azure Data Factory
* WebHooks genéricos, que podem ser hospedados na plataforma do Azure ou em outro lugar

## <a name="set-up-in-azure-portal"></a>Configurar no portal do Azure

1. Abra o [portal do Azure](https://portal.azure.com) e vá para seu espaço de trabalho do Azure Machine Learning.

1. Na barra à esquerda, selecione __eventos__ e, em seguida, selecione **assinaturas de evento**. 

    ![Select-Events-in-Workspace. png](./media/how-to-use-event-grid/select-event.png)

1. Selecione o tipo de evento a ser consumido. Por exemplo, a captura de tela a seguir selecionou o __modelo registrado__, __modelo implantado__, __execução concluída__e __descompasso de conjunto de banco__de e detectado:

    ![Adicionar tipo de evento](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Selecione o ponto de extremidade no qual publicar o evento. Na captura de tela a seguir, o __Hub de eventos__ é o ponto de extremidade selecionado:

    ![manipulador de eventos Select](./media/how-to-use-event-grid/select-event-handler.png)

Depois de confirmar sua seleção, clique em __criar__. Após a configuração, esses eventos serão enviados por push para o ponto de extremidade.


### <a name="set-up-with-the-cli"></a>Configurar com a CLI

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

## <a name="examples"></a>Exemplos

### <a name="example-send-email-alerts"></a>Exemplo: enviar alertas por email

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


### <a name="example-data-drift-triggers-retraining"></a>Exemplo: dados de desativação dos gatilhos de descompasso

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

### <a name="example-deploy-a-model-based-on-tags"></a>Exemplo: implantar um modelo com base em marcas

Um objeto de modelo de Azure Machine Learning contém parâmetros que você pode dinamizar implantações como nome do modelo, versão, marca e propriedade. O evento de registro de modelo pode disparar um ponto de extremidade e você pode usar uma função do Azure para implantar um modelo com base no valor desses parâmetros.

Para obter um exemplo, consulte [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) o repositório e siga as etapas no arquivo **Leiame** .

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a grade de eventos e dê uma tentativa de Azure Machine Learning eventos:

- [Sobre a Grade de Eventos](../event-grid/overview.md)

- [Esquema de evento para Azure Machine Learning](../event-grid/event-schema-machine-learning.md)

