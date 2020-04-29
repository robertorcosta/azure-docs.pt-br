---
title: Conectar-se ao servidor do IBM MQ
description: Enviar e recuperar mensagens com um servidor e aplicativos lógicos do Azure no Azure ou no local
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 03/31/2020
tags: connectors
ms.openlocfilehash: 737c5b90b216156ca08346f4a64fd0b421ad6c19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80410232"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Conectar-se a um servidor do IBM MQ de aplicativos lógicos do Azure

O conector IBM MQ envia e recupera mensagens armazenadas em um servidor do IBM MQ localmente ou no Azure. Esse conector inclui um cliente Microsoft MQ para se comunicar com um servidor MQ IBM remoto em uma rede TCP/IP. Este artigo fornece um guia inicial para usar o conector do MQ. Você pode começar navegando por uma única mensagem em uma fila e, em seguida, tentar outras ações.

O conector IBM MQ inclui essas ações, mas não fornece gatilhos:

- Procure uma única mensagem sem excluir a mensagem do servidor IBM MQ.
- Procure um lote de mensagens sem excluir as mensagens do servidor IBM MQ.
- Receba uma única mensagem e exclua a mensagem do servidor IBM MQ.
- Receba um lote de mensagens e exclua as mensagens do servidor IBM MQ.
- Envie uma única mensagem para o servidor do IBM MQ.

Estas são as versões oficialmente com suporte do IBM WebSphere MQ:

  * MQ 7.5
  * MQ 8.0
  * MQ 9,0

## <a name="prerequisites"></a>Pré-requisitos

* Se você estiver usando um servidor MQ local, [Instale o gateway de dados local](../logic-apps/logic-apps-gateway-install.md) em um servidor dentro de sua rede. O servidor no qual o gateway de dados local está instalado também deve ter o .NET Framework 4,6 instalado para que o conector do MQ funcione.

  Depois de concluir a instalação do gateway, você também deve criar um recurso no Azure para o gateway de dados local. Para obter mais informações, consulte [Configurar a conexão do gateway de dados](../logic-apps/logic-apps-gateway-connection.md).

  Se o servidor do MQ estiver publicamente disponível ou disponível no Azure, você não precisará usar o gateway de dados.

* O aplicativo lógico no qual você deseja adicionar a ação do MQ. Esse aplicativo lógico deve usar o mesmo local que a sua conexão de gateway de dados local e já deve ter um gatilho que inicie o fluxo de trabalho.

  O conector do MQ não tem nenhum gatilho, portanto, você deve adicionar um gatilho ao seu aplicativo lógico primeiro. Por exemplo, você pode usar o gatilho de recorrência. Se você for novo em aplicativos lógicos, experimente este guia [de início rápido para criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>Criar conexão do MQ

Se você ainda não tiver uma conexão do MQ ao adicionar uma ação do MQ, você será solicitado a criar a conexão, por exemplo:

![Fornecer informações de conexão](media/connectors-create-api-mq/connection-properties.png)

1. Se você estiver se conectando a um servidor MQ local, selecione **conectar por meio do gateway de dados local**.

1. Forneça as informações de conexão para o servidor MQ.

   * Para **Servidor**, você pode digitar o nome do servidor MQ ou digitar o endereço IP seguido por dois-pontos e o número da porta.

   * Para usar protocolo SSL (SSL), selecione **habilitar SSL?**.

     O conector do MQ atualmente dá suporte apenas à autenticação do servidor, não à autenticação do cliente. Para obter mais informações, consulte [problemas de conexão e autenticação](#connection-problems).

1. Na seção **Gateway** , siga estas etapas:

   1. Na lista **assinatura** , selecione a assinatura do Azure associada ao recurso de gateway do Azure.

   1. Na lista **Gateway de conexão** , selecione o recurso de gateway do Azure que você deseja usar.

1. Quando terminar, selecione **Criar**.

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>Problemas de conexão e autenticação

Quando seu aplicativo lógico tentar se conectar ao servidor MQ local, você poderá receber esse erro:

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* Se você estiver usando o conector do MQ diretamente no Azure, o servidor MQ precisará usar um certificado emitido por uma autoridade de [certificação](https://www.ssl.com/faqs/what-is-a-certificate-authority/)confiável.

* Se você estiver usando o gateway de dados local, tente usar um certificado emitido por uma [autoridade de certificação](https://www.ssl.com/faqs/what-is-a-certificate-authority/) confiável, quando possível. No entanto, se essa opção não for possível, você poderá usar um certificado autoassinado, que não é emitido por uma [autoridade de certificação](https://www.ssl.com/faqs/what-is-a-certificate-authority/) confiável e é considerado menos seguro.

  Para instalar o certificado autoassinado do servidor, você pode usar a ferramenta do **Gerenciador de certificação do Windows** (Certmgr. msc). Para esse cenário, no computador local em que o serviço do gateway de dados local está em execução, você precisa instalar o certificado no repositório de certificados do **computador local** no nível das **autoridades de certificação raiz confiáveis** .

  1. No computador em que o serviço do gateway de dados local está em execução, abra o menu Iniciar, localize e selecione **gerenciar certificados de usuário**.

  1. Após a abertura da ferramenta Gerenciador de certificação do Windows, vá para a pasta **certificados-autoridades de** >  **certificação raiz confiáveis** do computador local e instale o certificado.

     > [!IMPORTANT]
     > Certifique-se de instalar o certificado no repositório **certificados-autoridades de** > **certificação raiz confiáveis** do computador local.

* O servidor MQ requer que você defina a especificação de codificação que deseja usar para conexões SSL. No entanto, o SsLStream no .NET não permite que você especifique a ordem das especificações de codificação. Para contornar essa limitação, você pode alterar a configuração do servidor do MQ para corresponder à primeira especificação de codificação no conjunto que o conector envia na negociação SSL.

  Quando você tenta a conexão, o servidor MQ registra uma mensagem de evento que indica que a conexão falhou porque a outra extremidade usou a especificação de codificação incorreta. A mensagem de evento contém a especificação de codificação que aparece primeiro na lista. Atualize a especificação de codificação na configuração de canal para corresponder à especificação de codificação na mensagem de evento.

## <a name="browse-single-message"></a>Procurar uma única mensagem

1. No aplicativo lógico, no gatilho ou outra ação, selecione **nova etapa**.

1. Na caixa de pesquisa, insira `mq`e selecione a ação **procurar mensagem** .

   ![Selecione a ação "procurar mensagem"](media/connectors-create-api-mq/browse-message.png)

1. Se você ainda não criou uma conexão do MQ, você será solicitado a [criar essa conexão](#create-connection).

1. Depois de criar a conexão, configure as propriedades da ação da **mensagem de procura** :

   | Propriedade | Descrição |
   |----------|-------------|
   | **Espera** | Se for diferente da fila especificada na conexão, especifique essa fila. |
   | **MessageId**, **CorrelationId**, **GroupId**e outras propriedades | Procurar uma mensagem com base nas diferentes propriedades de mensagem do MQ |
   | **IncludeInfo** | Para incluir informações adicionais da mensagem na saída, selecione **true**. Para omitir informações de mensagem adicionais na saída, selecione **false**. |
   | **Tempo Limite** | Insira um valor para determinar por quanto tempo aguardar a chegada de uma mensagem em uma fila vazia. Se nada for inserido, a primeira mensagem na fila será recuperada e não nenhum tempo será perdido esperando que uma mensagem apareça. |
   |||

   Por exemplo:

   ![Propriedades da ação "procurar mensagem"](media/connectors-create-api-mq/browse-message-properties.png)

1. Quando terminar, na barra de ferramentas do designer, selecione **salvar**. Para testar seu aplicativo, selecione **executar**.

   Após a conclusão da execução, o designer mostra as etapas do fluxo de trabalho e seu status para que você possa examinar a saída.

1. Para exibir os detalhes sobre cada etapa, clique na barra de título da etapa. Para examinar mais informações sobre a saída de uma etapa, selecione **Mostrar saídas brutas**.

   ![Procurar saída de mensagem](media/connectors-create-api-mq/browse-message-output.png)

   Aqui está um exemplo de saída bruta:

   ![Procurar saída bruta de mensagem](media/connectors-create-api-mq/browse-message-raw-output.png)

1. Se você definir **IncludeInfo** como **true**, a saída adicional será mostrada:

   ![Procurar informações de inclusão da mensagem](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>Procurar várias mensagens

A ação **Procurar mensagens** inclui uma opção **BatchSize** para indicar quantas mensagens retornar da fila. Se **BatchSize** não tiver nenhum valor, todas as mensagens serão retornadas. A saída retornada é uma matriz de mensagens.

1. Siga as etapas anteriores, mas adicione a ação **Procurar mensagens** em vez disso.

1. Se você ainda não criou uma conexão do MQ, você será solicitado a [criar essa conexão](#create-connection). Caso contrário, por padrão, a primeira conexão configurada anteriormente será usada. Para criar uma nova conexão, selecione **alterar conexão**. Ou então, selecione uma conexão diferente.

1. Forneça as informações para a ação.

1. Salve e execute o aplicativo lógico.

   Depois que o aplicativo lógico concluir a execução, aqui está alguns exemplos de saída da ação **Procurar mensagens** :

   ![Saída de "procurar mensagens" de exemplo](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>Receber mensagem única

A ação **Receber mensagem** tem as mesmas entradas e saídas que a ação **Procurar mensagem**. Quando você usa **receber mensagem**, a mensagem é excluída da fila.

## <a name="receive-multiple-messages"></a>Receber várias mensagens

A ação **Receber mensagens** tem as mesmas entradas e saídas que a ação **Procurar mensagens**. Quando você usa **receber mensagens**, as mensagens são excluídas da fila.

> [!NOTE]
> Ao executar uma ação de navegação ou de recebimento em uma fila que não tem nenhuma mensagem, a ação falha com esta saída:
>
> ![Erro do MQ "sem mensagem"](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>Enviar mensagem

1. Siga as etapas anteriores, mas adicione a ação **Enviar mensagem** em vez disso.

1. Se você ainda não criou uma conexão do MQ, você será solicitado a [criar essa conexão](#create-connection). Caso contrário, por padrão, a primeira conexão configurada anteriormente será usada. Para criar uma nova conexão, selecione **alterar conexão**. Ou então, selecione uma conexão diferente.

1. Forneça as informações para a ação. Para **MessageType**, selecione um tipo de mensagem válido: **datagrama**, **resposta**ou **solicitação**

   ![Propriedades de "ação enviar mensagem"](media/connectors-create-api-mq/send-message-properties.png)

1. Salve e execute o aplicativo lógico.

   Depois que o aplicativo lógico terminar a execução, aqui está alguns exemplos de saída da ação **Enviar mensagem** :

   ![Saída de "Enviar mensagem" de exemplo](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre ações e limites, que são descritos pela descrição do Swagger do conector, examine a [página de referência](/connectors/mq/)do conector.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
