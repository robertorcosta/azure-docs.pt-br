---
title: Conecte-se ao servidor IBM MQ
description: Enviar e recuperar mensagens com um servidor Azure ou no local IBM MQ e Aplicativos Azure Logic
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: 6bfd626c1ce69029ee720d24b0b143e7b4c3dd56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650940"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Conecte-se a um servidor IBM MQ a partir de aplicativos azure logic

O conector IBM MQ envia e recupera mensagens armazenadas em um servidor IBM MQ no local ou no Azure. Esse conector inclui um cliente Microsoft MQ para se comunicar com um servidor MQ IBM remoto em uma rede TCP/IP. Este artigo fornece um guia inicial para usar o conector MQ. Você pode começar navegando uma única mensagem em uma fila e, em seguida, tentar outras ações.

O conector Ibm MQ inclui essas ações, mas não fornece gatilhos:

- Navegue por uma única mensagem sem excluir a mensagem do servidor IBM MQ
- Navegue por um lote de mensagens sem excluir as mensagens do servidor IBM MQ
- Receba uma única mensagem e exclua a mensagem do servidor IBM MQ
- Receba um lote de mensagens e exclua as mensagens do servidor IBM MQ
- Envie uma única mensagem para o servidor IBM MQ

## <a name="prerequisites"></a>Pré-requisitos

* Se você estiver usando um servidor MQ no local, [instale o gateway de dados local](../logic-apps/logic-apps-gateway-install.md) em um servidor dentro da sua rede. O servidor onde o gateway de dados no local está instalado também deve ter o .NET Framework 4.6 instalado para que o conector MQ funcione. Você também deve criar um recurso no Azure para o gateway de dados no local. Para obter mais informações, consulte [Configurar a conexão do gateway de dados](../logic-apps/logic-apps-gateway-connection.md).

  No entanto, se o seu servidor MQ estiver disponível publicamente ou disponível dentro do Azure, você não precisa usar o gateway de dados.

* Versões do IBM WebSphere MQ oficialmente com suporte:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

* O aplicativo lógico onde você deseja adicionar a ação MQ. Este aplicativo lógico deve usar o mesmo local que sua conexão de gateway de dados no local e já deve ter um gatilho que inicie seu fluxo de trabalho. 

  O conector MQ não tem nenhum gatilho, então você deve adicionar um gatilho ao seu aplicativo lógico primeiro. Por exemplo, você pode usar o gatilho Recorrência. Se você é novo em aplicativos lógicos, tente este [quickstart para criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="browse-a-single-message"></a>Procurar uma única mensagem

1. Em seu aplicativo lógico, o gatilho ou outra ação, escolha **Novo passo**. 

1. Na caixa de pesquisa, digite "mq", e selecione esta ação: **Procurar mensagem**

   ![Procurar mensagem](media/connectors-create-api-mq/Browse_message.png)

1. Se você não tiver uma conexão MQ existente, crie a conexão:  

   1. Na ação, selecione **Conectar via gateway de dados on-premises**.
   
   1. Digite as propriedades do servidor MQ.  

      Para **Servidor**, você pode digitar o nome do servidor MQ ou digitar o endereço IP seguido por dois-pontos e o número da porta.
    
   1. Abra a lista **de gateway,** que mostra quaisquer conexões de gateway configuradas anteriormente. Selecione seu gateway.
    
   1. Quando terminar, escolha **Criar**. 
   
      Sua conexão se parece com este exemplo:

      ![Propriedades da conexão](media/connectors-create-api-mq/Connection_Properties.png)

1. Configure as propriedades da ação:

   * **Fila**: Especifique uma fila diferente da conexão.

   * **MessageId,** **CorrelationId,** **GroupId**e outras propriedades: Procure uma mensagem com base nas diferentes propriedades de mensagem MQ

   * **IncludeInfo**: Especifique **True** para incluir informações adicionais de mensagem na saída. Ou, especifique **False** para não incluir informações adicionais de mensagem na saída.

   * **Tempo :** Digite um valor para determinar quanto tempo esperar para que uma mensagem chegue em uma fila vazia. Se nada for inserido, a primeira mensagem na fila será recuperada e não nenhum tempo será perdido esperando que uma mensagem apareça.

     ![Procurar propriedades de mensagem](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Salve** suas alterações e **execute** seu aplicativo lógico.

   ![Salvar e Executar](media/connectors-create-api-mq/Save_Run.png)

   Após o término da execução, as etapas da execução são mostradas e você pode rever a saída.

1. Para revisar os detalhes de cada etapa, escolha a marca verde. Para revisar mais informações sobre os dados de saída, escolha **Mostrar saídas brutas**.

   ![Procurar saída de mensagem](media/connectors-create-api-mq/Browse_message_output.png)  

   Aqui está uma amostra de saída bruta:

   ![Procurar saída bruta de mensagem](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Se você definir **IncludeInfo** como true, a seguinte saída será exibida:

   ![Procurar informações de inclusão da mensagem](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Procurar várias mensagens

A ação **Procurar mensagens** inclui uma opção **BatchSize** para indicar quantas mensagens devem ser retornadas da fila.  Se **BatchSize** não tem nenhuma entrada, todas as mensagens são retornadas. A saída retornada é uma matriz de mensagens.

1. Quando você adiciona a ação **Procurar mensagens,** a primeira conexão configurada anteriormente é selecionada por padrão. Para criar uma nova conexão, escolha **Alterar conexão**. Ou, selecione uma conexão diferente.

1. Depois que a execução do aplicativo lógico for concluída, aqui está uma saída de exemplo da ação **'Procurar mensagens':**

   ![Saída de Procurar mensagens](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Receba uma única mensagem

A ação **Receber mensagem** tem as mesmas entradas e saídas que a ação **Procurar mensagem**. Ao usar **Receber mensagem**, a mensagem é excluída da fila.

## <a name="receive-multiple-messages"></a>Receber várias mensagens

A ação **Receber mensagens** tem as mesmas entradas e saídas que a ação **Procurar mensagens**. Ao usar **Receber mensagens**, as mensagens são excluídas da fila.

Se não houver mensagens na fila ao fazer uma navegação ou um recebimento, a etapa falhará com esta saída:  

![Erro MQ Nenhuma Mensagem](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Enviar mensagem

Quando você adiciona a ação **Enviar mensagens,** a primeira conexão configurada anteriormente é selecionada por padrão. Para criar uma nova conexão, escolha **Alterar conexão**. Ou, selecione uma conexão diferente.

1. Selecione um tipo de mensagem válida: **Datagram,** **Reply**ou **Request**  

   ![Send Msg Props](media/connectors-create-api-mq/Send_Msg_Props.png)

1. Depois que o aplicativo lógico terminar de ser executado, aqui está uma saída de amostra da ação **Enviar mensagem:**

   ![Send Msg Output](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Referência de conector

Para obter mais detalhes técnicos sobre este conector, como gatilhos, ações e limites descritos pelo arquivo Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/mq/).

> [!NOTE]
> Para aplicativos lógicos em um [ambiente de serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão rotulada pelo conector ISE usa os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
