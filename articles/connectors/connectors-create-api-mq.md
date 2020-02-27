---
title: Conectar-se ao servidor do IBM MQ
description: Enviar e recuperar mensagens com um servidor e aplicativos lógicos do Azure no Azure ou no local
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: 6bfd626c1ce69029ee720d24b0b143e7b4c3dd56
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650940"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Conectar-se a um servidor do IBM MQ de aplicativos lógicos do Azure

O conector IBM MQ envia e recupera mensagens armazenadas em um servidor do IBM MQ localmente ou no Azure. Esse conector inclui um cliente Microsoft MQ para se comunicar com um servidor MQ IBM remoto em uma rede TCP/IP. Este artigo fornece um guia inicial para usar o conector do MQ. Você pode começar navegando por uma única mensagem em uma fila e, em seguida, tentar outras ações.

O conector IBM MQ inclui essas ações, mas não fornece gatilhos:

- Procurar uma única mensagem sem excluir a mensagem do servidor do IBM MQ
- Procurar um lote de mensagens sem excluir as mensagens do servidor do IBM MQ
- Receber uma única mensagem e excluir a mensagem do servidor do IBM MQ
- Receber um lote de mensagens e excluir as mensagens do servidor do IBM MQ
- Enviar uma única mensagem para o servidor do IBM MQ

## <a name="prerequisites"></a>Prerequisites

* Se você estiver usando um servidor MQ local, [Instale o gateway de dados local](../logic-apps/logic-apps-gateway-install.md) em um servidor dentro de sua rede. O servidor no qual o gateway de dados local está instalado também deve ter o .NET Framework 4,6 instalado para que o conector do MQ funcione. Você também deve criar um recurso no Azure para o gateway de dados local. Para obter mais informações, consulte [Configurar a conexão do gateway de dados](../logic-apps/logic-apps-gateway-connection.md).

  No entanto, se o servidor do MQ estiver publicamente disponível ou disponível no Azure, você não precisará usar o gateway de dados.

* Versões do IBM WebSphere MQ oficialmente com suporte:

  * MQ 7.5
  * MQ 8.0
  * MQ 9,0

* O aplicativo lógico no qual você deseja adicionar a ação do MQ. Esse aplicativo lógico deve usar o mesmo local que a sua conexão de gateway de dados local e já deve ter um gatilho que inicie o fluxo de trabalho. 

  O conector do MQ não tem nenhum gatilho, portanto, você deve adicionar um gatilho ao seu aplicativo lógico primeiro. Por exemplo, você pode usar o gatilho de recorrência. Se você for novo em aplicativos lógicos, experimente este guia [de início rápido para criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="browse-a-single-message"></a>Procurar uma única mensagem

1. No aplicativo lógico, no gatilho ou outra ação, escolha **nova etapa**. 

1. Na caixa de pesquisa, digite "MQ" e selecione esta ação: **procurar mensagem**

   ![Procurar mensagem](media/connectors-create-api-mq/Browse_message.png)

1. Se você não tiver uma conexão MQ existente, crie a conexão:  

   1. Na ação, selecione **conectar por meio do gateway de dados local**.
   
   1. Insira as propriedades do servidor MQ.  

      Para **Servidor**, você pode digitar o nome do servidor MQ ou digitar o endereço IP seguido por dois-pontos e o número da porta.
    
   1. Abra a lista **Gateway** , que mostra todas as conexões de gateway configuradas anteriormente. Selecione seu gateway.
    
   1. Quando terminar, escolha **Criar**. 
   
      Sua conexão é semelhante a este exemplo:

      ![Propriedades da conexão](media/connectors-create-api-mq/Connection_Properties.png)

1. Configure as propriedades da ação:

   * **Fila**: especifique uma fila diferente da conexão.

   * **MessageId**, **CorrelationId**, **GroupId**e outras propriedades: procurar uma mensagem com base nas diferentes propriedades de mensagem do MQ

   * **IncludeInfo**: especifique **true** para incluir informações de mensagem adicionais na saída. Ou especifique **false** para não incluir informações de mensagem adicionais na saída.

   * **Tempo limite**: Insira um valor para determinar por quanto tempo esperar que uma mensagem chegue em uma fila vazia. Se nada for inserido, a primeira mensagem na fila será recuperada e não nenhum tempo será perdido esperando que uma mensagem apareça.

     ![Procurar propriedades da mensagem](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Salve** suas alterações e, em seguida, **Execute** seu aplicativo lógico.

   ![Salvar e executar](media/connectors-create-api-mq/Save_Run.png)

   Após a conclusão da execução, as etapas da execução são mostradas e você pode examinar a saída.

1. Para examinar os detalhes de cada etapa, escolha a marca de seleção verde. Para examinar mais informações sobre os dados de saída, escolha **Mostrar saídas brutas**.

   ![Procurar saída da mensagem](media/connectors-create-api-mq/Browse_message_output.png)  

   Aqui está um exemplo de saída bruta:

   ![Procurar saída bruta de mensagem](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Se você definir **IncludeInfo** como true, a seguinte saída será exibida:

   ![Procurar mensagem incluir informações](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Procurar várias mensagens

A ação **Procurar mensagens** inclui uma opção **BatchSize** para indicar quantas mensagens devem ser retornadas da fila.  Se **BatchSize** não tem nenhuma entrada, todas as mensagens são retornadas. A saída retornada é uma matriz de mensagens.

1. Quando você adiciona a ação **Procurar mensagens** , a primeira conexão configurada anteriormente é selecionada por padrão. Para criar uma nova conexão, escolha **alterar conexão**. Ou então, selecione uma conexão diferente.

1. Depois que a execução do aplicativo lógico for concluída, aqui está alguns exemplos de saída da ação **Procurar mensagens** :

   ![Saída de Procurar mensagens](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Receber mensagem única

A ação **Receber mensagem** tem as mesmas entradas e saídas que a ação **Procurar mensagem**. Ao usar **Receber mensagem**, a mensagem é excluída da fila.

## <a name="receive-multiple-messages"></a>Receber várias mensagens

A ação **Receber mensagens** tem as mesmas entradas e saídas que a ação **Procurar mensagens**. Ao usar **Receber mensagens**, as mensagens são excluídas da fila.

Se não houver nenhuma mensagem na fila ao fazer uma procura ou um recebimento, a etapa falhará com esta saída:  

![Erro MQ Nenhuma Mensagem](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Enviar mensagem

Quando você adiciona a ação **enviar mensagens** , a primeira conexão configurada anteriormente é selecionada por padrão. Para criar uma nova conexão, escolha **alterar conexão**. Ou então, selecione uma conexão diferente.

1. Selecione um tipo de mensagem válido: **datagrama**, **resposta**ou **solicitação**  

   ![Enviar props de mensagens](media/connectors-create-api-mq/Send_Msg_Props.png)

1. Depois que o aplicativo lógico terminar a execução, aqui está alguns exemplos de saída da ação **Enviar mensagem** :

   ![Send Msg Output](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Referência de conector

Para obter mais detalhes técnicos sobre esse conector, como gatilhos, ações e limites, conforme descrito pelo arquivo Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/mq/).

> [!NOTE]
> Para aplicativos lógicos em um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), a versão rotulada do ISE do conector usa os [limites de mensagem do ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) em vez disso.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
