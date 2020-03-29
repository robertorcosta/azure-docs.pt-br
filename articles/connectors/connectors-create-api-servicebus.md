---
title: Trocar mensagens com o Ônibus de Serviço Azure
description: Crie tarefas e fluxos de trabalho automatizados que enviam e recebem mensagens usando o Azure Service Bus em Aplicativos de Lógica Do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/19/2019
tags: connectors
ms.openlocfilehash: 1b38b8508dbe17d42bf191149410f5db638cf834
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261612"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Troque mensagens na nuvem usando aplicativos azure logic e ônibus de serviço do Azure

Com [o Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o conector [Azure Service Bus,](../service-bus-messaging/service-bus-messaging-overview.md) você pode criar tarefas e fluxos de trabalho automatizados que transferem dados, como ordens de venda e compra, diários e movimentos de inventário em aplicativos para sua organização. Além de monitorar, enviar e gerenciar mensagens, o conector também realiza ações com filas, sessões, tópicos, assinaturas e assim por diante; por exemplo:

* Monitorar quando mensagens chegam (preenchimento automático) ou são recebidas (peek-lock) em filas, tópicos e assinaturas de tópicos.
* Enviar mensagens.
* Criar e excluir assinaturas de tópicos.
* Gerenciar mensagens em filas e assinaturas de tópicos; por exemplo, get, get deferred, complete, defer, abandon e dead-letter.
* Renovar bloqueios em mensagens e sessões nas filas e assinaturas de tópicos.
* Fechar sessões em filas e tópicos.

Você pode usar gatilhos para obter respostas do Barramento de Serviço e disponibilizar a saída para outras ações em seus aplicativos lógicos. Você também pode fazer com que outras ações usem a saída das ações do Barramento de Serviço. Se você é novo em Service Bus and Logic Apps, [analise o que é o Ônibus de Serviço do Azure?](../service-bus-messaging/service-bus-messaging-overview.md) [What is Azure Logic Apps](../logic-apps/logic-apps-overview.md)

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um namespace de Barramento de Serviço e a entidade de mensagens, como uma fila. Esses itens e seu aplicativo lógico precisam usar a mesma assinatura do Azure. Se você não tiver esses itens, aprenda a [criar seu namespace do Barramento de Serviço e uma fila](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico onde você usa o namespace do Service Bus e a entidade de mensagens. Seu aplicativo lógico e o ônibus de serviço precisam usar a mesma assinatura do Azure. Para iniciar seu fluxo de trabalho com um gatilho do Service Bus, [crie um aplicativo de lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação de Barra de Serviço em seu fluxo de trabalho, inicie seu aplicativo lógico com outro gatilho, por exemplo, o [gatilho Recorrência](../connectors/connectors-native-recurrence.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Verificar permissões

Confirme se seu aplicativo lógico tem permissões para acessar o namespace do Barramento de Serviço.

1. Faça login no [portal Azure](https://portal.azure.com).

1. Acesse o *namespace* do seu Barramento de Serviço. Na página do namespace, em **Configurações**, selecione **Políticas de acesso compartilhado**. Em **Reclamações,** verifique se você tem permissões **de gerenciamento** para esse namespace.

   ![Gerenciar permissões para o espaço de nome do Service Bus](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Obtenha a cadeia de conexão para o namespace do Barramento de Serviço. Você precisa desta string quando você fornece as informações de conexão em seu aplicativo lógico.

   1. No painel **políticas de acesso compartilhado,** selecione **RootManageSharedAccessKey**.
   
   1. Ao lado da seqüência de conexão primária, selecione o botão copiar. Salve a cadeia de conexão para uso posterior.

      ![Copiar a cadeia de conexão do namespace do Barramento de Serviço](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Para confirmar se sua cadeia de conexão está associada ao namespace do Barramento de Serviços ou a uma entidade de sistema de mensagens, como uma fila, pesquise a cadeia de conexão para o parâmetro `EntityPath` . Se encontrar esse parâmetro, a cadeia de conexão servirá para uma entidade específica e não será a cadeia correta a ser usada com seu aplicativo lógico.

## <a name="add-service-bus-trigger"></a>Adicionar gatilho de ônibus de serviço

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Faça login no [portal Azure](https://portal.azure.com)e abra seu aplicativo de lógica em branco no Logic App Designer.

1. Na caixa de pesquisa, digite "barramento de serviço azul" como seu filtro. Na lista de gatilhos, selecione o gatilho que deseja.

   Por exemplo, para acionar seu aplicativo lógico quando um novo item é enviado para uma fila de Barramento de Serviço, selecione o **Quando uma mensagem é recebida em uma fila (auto-completar)** gatilho.

   ![Selecionar um gatilho do Barramento de Serviço](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   Todos os gatilhos do Service Bus são gatilhos *de votação de longo* prazo. Esta descrição significa que quando o gatilho é acionado, o gatilho processa todas as mensagens e, em seguida, espera 30 segundos para que mais mensagens apareçam na fila ou na assinatura do tópico. Se nenhuma mensagem aparecer em 30 segundos, a execução do gatilho será ignorada. Caso contrário, o gatilho continuará lendo as mensagens até que a fila ou a assinatura do tópico esteja vazia. A próxima sondagem de gatilho é baseada no intervalo de recorrência especificado nas propriedades do gatilho.

   Alguns gatilhos, como o **Quando uma ou mais mensagens chegam em uma fila (auto-completação),** podem retornar uma ou mais mensagens. Quando esses gatilhos disparam, eles retornam entre uma e o número de mensagens especificadas pela propriedade **de contagem máxima** de mensagens do gatilho.

1. Se o seu gatilho estiver se conectando ao seu espaço de nome do Busde de Serviço pela primeira vez, siga estas etapas quando o Logic App Designer solicitar informações sobre a conexão.

   1. Forneça um nome para sua conexão e selecione o namespace do seu Barramento de Serviço.

      ![Criar uma conexão do Barramento de Serviço, parte 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      Para inserir manualmente a seqüência de conexões, selecione **Inserir manualmente as informações de conexão**. Caso não tenha uma cadeia de conexão, aprenda [como localizar sua cadeia de conexão](#permissions-connection-string).

   1. Selecione sua política de barramento de serviço e selecione **Criar**.

      ![Criar uma conexão do Barramento de Serviço, parte 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. Selecione a entidade de mensagens desejada, como uma fila ou tópico. Para este exemplo, selecione a fila do Ônibus de Serviço.
   
      ![Selecionar fila do Barramento de Serviço](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. Forneça as informações necessárias para o seu gatilho selecionado. Para adicionar outras propriedades disponíveis à ação, abra a lista **Adicionar novos parâmetros** e selecione as propriedades desejadas.

   Para o gatilho deste exemplo, selecione o intervalo de votação e a freqüência para verificar a fila.

   ![Configurar intervalo de sondagem](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   Para obter mais informações sobre gatilhos e propriedades disponíveis, consulte a página de [referência](/connectors/servicebus/)do conector .

1. Continue construindo seu aplicativo lógico adicionando as ações que você deseja.

   Por exemplo, pode adicionar uma ação que envia um email quando uma nova mensagem chega. Quando o gatilho verifica sua fila e localiza uma nova mensagem, seu aplicativo lógico executa as ações selecionadas para a mensagem encontrada.

## <a name="add-service-bus-action"></a>Adicionar ação de Ônibus de Serviço

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Faça login no [portal Azure](https://portal.azure.com)e abra seu aplicativo lógico no Logic App Designer.

1. a etapa onde você deseja adicionar uma ação, selecione **Novo passo**.

   Ou, para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre esses passos. Selecione o**+** sinal de adição () que aparece e **selecione Adicionar uma ação**.

1. Em **Escolha uma ação**, na caixa de pesquisa, digite "barramento de serviço azul" como seu filtro. Na lista de ações, selecione a ação desejada. 

   Para este exemplo, selecione a ação **Enviar mensagem.**

   ![Selecionar ação de Barramento de Serviço](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Se sua ação estiver se conectando ao seu espaço de nome do Busde de Serviço pela primeira vez, siga estas etapas quando o Logic App Designer solicitar informações sobre a conexão.

   1. Forneça um nome para sua conexão e selecione o namespace do seu Barramento de Serviço.

      ![Criar uma conexão do Barramento de Serviço, parte 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      Para inserir manualmente a seqüência de conexões, selecione **Inserir manualmente as informações de conexão**. Caso não tenha uma cadeia de conexão, aprenda [como localizar sua cadeia de conexão](#permissions-connection-string).

   1. Selecione sua política de barramento de serviço e selecione **Criar**.

      ![Criar uma conexão do Barramento de Serviço, parte 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. Selecione a entidade de mensagens desejada, como uma fila ou tópico. Para este exemplo, selecione a fila do Ônibus de Serviço.

      ![Selecionar fila do Barramento de Serviço](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. Forneça os detalhes necessários para a ação selecionada. Para adicionar outras propriedades disponíveis à ação, abra a lista **Adicionar novos parâmetros** e selecione as propriedades desejadas.

   Por exemplo, selecione as propriedades **Conteúdo** e **Tipo de Conteúdo** para que você as adicione à ação. Em seguida, especifique o conteúdo da mensagem que deseja enviar.

   ![Fornecer o conteúdo e os detalhes da mensagem](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   Para obter mais informações sobre as ações disponíveis e suas propriedades, consulte a página de [referência](/connectors/servicebus/)do conector .

1. Continue construindo seu aplicativo lógico adicionando quaisquer outras ações que você quiser.

   Por exemplo, você pode adicionar uma ação que envia e-mail para confirmar se sua mensagem foi enviada.

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

## <a name="connector-reference"></a>Referência de conector

O conector Service Bus pode economizar até 1.500 sessões únicas por vez de um barramento de serviço para o cache do conector. Se a contagem de sessões exceder esse limite, as sessões antigas serão removidas do cache. Para obter mais informações, consulte [Sessões de mensagem](../service-bus-messaging/message-sessions.md).

Para obter outros detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição OpenAPI (anteriormente Swagger) do conector, revise a página de [referência](/connectors/servicebus/)do conector . Para saber mais sobre o Azure Service Bus Messaging, veja [o que é ônibus de serviço do Azure](../service-bus-messaging/service-bus-messaging-overview.md)?

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
