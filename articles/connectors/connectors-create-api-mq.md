---
title: Conecte-se ao servidor IBM MQ
description: Enviar e recuperar mensagens com um servidor Azure ou no local IBM MQ e Aplicativos Azure Logic
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 03/31/2020
tags: connectors
ms.openlocfilehash: 737c5b90b216156ca08346f4a64fd0b421ad6c19
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410232"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Conecte-se a um servidor IBM MQ a partir de aplicativos azure logic

O conector IBM MQ envia e recupera mensagens armazenadas em um servidor IBM MQ no local ou no Azure. Esse conector inclui um cliente Microsoft MQ para se comunicar com um servidor MQ IBM remoto em uma rede TCP/IP. Este artigo fornece um guia inicial para usar o conector MQ. Você pode começar navegando uma única mensagem em uma fila e, em seguida, tentar outras ações.

O conector Ibm MQ inclui essas ações, mas não fornece gatilhos:

- Navegue por uma única mensagem sem excluir a mensagem do servidor IBM MQ.
- Navegue por um lote de mensagens sem excluir as mensagens do servidor IBM MQ.
- Receba uma única mensagem e exclua a mensagem do servidor IBM MQ.
- Receba um lote de mensagens e exclua as mensagens do servidor IBM MQ.
- Envie uma única mensagem para o servidor IBM MQ.

Aqui estão as versões do IBM WebSphere MQ com suporte oficial:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

## <a name="prerequisites"></a>Pré-requisitos

* Se você estiver usando um servidor MQ no local, [instale o gateway de dados local](../logic-apps/logic-apps-gateway-install.md) em um servidor dentro da sua rede. O servidor onde o gateway de dados no local está instalado também deve ter o .NET Framework 4.6 instalado para que o conector MQ funcione.

  Depois de terminar de instalar o gateway, você também deve criar um recurso no Azure para o gateway de dados no local. Para obter mais informações, consulte [Configurar a conexão do gateway de dados](../logic-apps/logic-apps-gateway-connection.md).

  Se o seu servidor MQ estiver disponível publicamente ou disponível dentro do Azure, você não precisa usar o gateway de dados.

* O aplicativo lógico onde você deseja adicionar a ação MQ. Este aplicativo lógico deve usar o mesmo local que sua conexão de gateway de dados no local e já deve ter um gatilho que inicie seu fluxo de trabalho.

  O conector MQ não tem nenhum gatilho, então você deve adicionar um gatilho ao seu aplicativo lógico primeiro. Por exemplo, você pode usar o gatilho Recorrência. Se você é novo em aplicativos lógicos, tente este [quickstart para criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>Criar conexão MQ

Se você ainda não tiver uma conexão MQ quando adicionar uma ação MQ, você será solicitado a criar a conexão, por exemplo:

![Fornecer informações de conexão](media/connectors-create-api-mq/connection-properties.png)

1. Se você estiver se conectando a um servidor MQ no local, selecione **Conectar via gateway de dados local**.

1. Forneça as informações de conexão para o seu servidor MQ.

   * Para **Servidor**, você pode digitar o nome do servidor MQ ou digitar o endereço IP seguido por dois-pontos e o número da porta.

   * Para usar o SSL Secure Sockets Layer (Secure Sockets Layer, camada de soquetes seguros), **selecione Ativar SSL?**.

     O conector MQ atualmente suporta apenas autenticação de servidor, não autenticação de cliente. Para obter mais informações, consulte [problemas de conexão e autenticação](#connection-problems).

1. Na seção **gateway,** siga estas etapas:

   1. Na lista **de assinaturas,** selecione a assinatura do Azure associada ao recurso gateway do Azure.

   1. Na lista **do Gateway de conexão,** selecione o recurso gateway do Azure que deseja usar.

1. Quando terminar, selecione **Criar**.

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>Problemas de conexão e autenticação

Quando seu aplicativo lógico tenta se conectar ao seu servidor MQ no local, você pode ter esse erro:

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* Se você estiver usando o conector MQ diretamente no Azure, o servidor MQ precisa usar um certificado emitido por uma autoridade de [certificado](https://www.ssl.com/faqs/what-is-a-certificate-authority/)confiável .

* Se você estiver usando o gateway de dados no local, tente usar um certificado emitido por uma autoridade de [certificado](https://www.ssl.com/faqs/what-is-a-certificate-authority/) confiável quando possível. No entanto, se essa opção não for possível, você pode usar um certificado auto-assinado, que não é emitido por uma autoridade de [certificado](https://www.ssl.com/faqs/what-is-a-certificate-authority/) confiável e é considerado menos seguro.

  Para instalar o certificado auto-assinado do servidor, você pode usar a ferramenta **Gerenciador de Certificação do Windows** (certmgr.msc). Para este cenário, em seu computador local onde o serviço de gateway de dados no local está sendo executado, você precisa instalar o certificado em sua loja de certificados **de computador local** no nível de **Autoridades de Certificação raiz confiável.**

  1. No computador onde o serviço de gateway de dados no local está sendo executado, abra o menu iniciar, encontre e **selecione Gerenciar certificados de usuário**.

  1. Depois que a ferramenta Gerenciador de Certificação do Windows for aberta, vá até a pasta **Certificates - Local Computer** >  **Trusted Certification Authorities** e instale o certificado.

     > [!IMPORTANT]
     > Certifique-se de instalar o certificado na loja **Certificados - Autoridades locais** > **de certificação raiz confiável do** computador.

* O servidor MQ requer que você defina a especificação de cifra que deseja usar para conexões SSL. No entanto, o SsLStream em .NET não permite que você especifique o pedido para especificações de cifras. Para contornar essa limitação, você pode alterar a configuração do servidor MQ para corresponder à primeira especificação de cifra no conjunto que o conector envia na negociação SSL.

  Quando você tenta a conexão, o servidor MQ registra uma mensagem de evento que indica que a conexão falhou porque a outra extremidade usou a especificação de cifra incorreta. A mensagem de evento contém a especificação de cifra que aparece primeiro na lista. Atualize a especificação de cifra na configuração do canal para corresponder à especificação de cifra na mensagem de evento.

## <a name="browse-single-message"></a>Navegue por uma única mensagem

1. Em seu aplicativo lógico, sob o gatilho ou outra ação, selecione **Novo passo**.

1. Na caixa de `mq`pesquisa, digite e selecione a ação **Procurar mensagem.**

   ![Selecione a ação "Procurar mensagem"](media/connectors-create-api-mq/browse-message.png)

1. Se você ainda não criou uma conexão MQ, você é solicitado a [criar essa conexão](#create-connection).

1. Depois de criar a conexão, configure as propriedades da ação de **mensagem Browse:**

   | Propriedade | Descrição |
   |----------|-------------|
   | **Fila** | Se diferente da fila especificada na conexão, especifique essa fila. |
   | **MessageId,** **CorrelationId,** **GroupId**e outras propriedades | Procure por uma mensagem baseada nas diferentes propriedades de mensagem MQ |
   | **IncluirInformações** | Para incluir informações adicionais de mensagem na saída, selecione **true**. Para omitir informações adicionais de mensagem na saída, selecione **falso**. |
   | **Timeout** | Digite um valor para determinar quanto tempo esperar para que uma mensagem chegue em uma fila vazia. Se nada for inserido, a primeira mensagem na fila será recuperada e não nenhum tempo será perdido esperando que uma mensagem apareça. |
   |||

   Por exemplo: 

   ![Propriedades para ação "Procurar mensagem"](media/connectors-create-api-mq/browse-message-properties.png)

1. Quando terminar, na barra de ferramentas do designer, selecione **Salvar**. Para testar seu aplicativo, selecione **Executar**.

   Após o término da execução, o designer mostra as etapas do fluxo de trabalho e seu status para que você possa rever a saída.

1. Para ver os detalhes sobre cada etapa, clique na barra de títuloda etapa. Para revisar mais informações sobre a saída de uma etapa, **selecione Mostrar saídas brutas**.

   ![Procurar saída de mensagem](media/connectors-create-api-mq/browse-message-output.png)

   Aqui está uma amostra de saída bruta:

   ![Procurar saída bruta de mensagem](media/connectors-create-api-mq/browse-message-raw-output.png)

1. Se você definir **IncludeInfo** como **true,** a saída adicional será exibida:

   ![Procurar informações de inclusão da mensagem](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>Procurar várias mensagens

A ação **Procurar mensagens** inclui uma opção **BatchSize** para indicar quantas mensagens retornar da fila. Se **BatchSize** não tiver valor, todas as mensagens serão devolvidas. A saída retornada é uma matriz de mensagens.

1. Siga as etapas anteriores, mas adicione a ação **Procurar mensagens** em vez disso.

1. Se você ainda não criou uma conexão MQ, você é solicitado a [criar essa conexão](#create-connection). Caso contrário, por padrão, a primeira conexão previamente configurada é usada. Para criar uma nova conexão, selecione **Alterar conexão**. Ou, selecione uma conexão diferente.

1. Forneça as informações para a ação.

1. Salve e execute o aplicativo lógico.

   Depois que o aplicativo lógico terminar de ser executado, aqui está uma saída de exemplo da ação **'Procurar mensagens':**

   ![Amostra da saída "Procurar mensagens"](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>Receba uma única mensagem

A ação **Receber mensagem** tem as mesmas entradas e saídas que a ação **Procurar mensagem**. Quando você usa **Enviar mensagem,** a mensagem é excluída da fila.

## <a name="receive-multiple-messages"></a>Receber várias mensagens

A ação **Receber mensagens** tem as mesmas entradas e saídas que a ação **Procurar mensagens**. Quando você **usa Receber mensagens,** as mensagens são excluídas da fila.

> [!NOTE]
> Ao executar uma navegação ou uma ação de recebimento em uma fila que não tenha mensagens, a ação falha com essa saída:
>
> ![Erro de MQ "sem mensagem"](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>Enviar mensagem

1. Siga as etapas anteriores, mas adicione a ação **Enviar mensagem** em vez disso.

1. Se você ainda não criou uma conexão MQ, você é solicitado a [criar essa conexão](#create-connection). Caso contrário, por padrão, a primeira conexão previamente configurada é usada. Para criar uma nova conexão, selecione **Alterar conexão**. Ou, selecione uma conexão diferente.

1. Forneça as informações para a ação. Para **MessageType,** selecione um tipo de mensagem válida: **Datagram,** **Reply**ou **Request**

   ![Propriedades para "Enviar ação de mensagem"](media/connectors-create-api-mq/send-message-properties.png)

1. Salve e execute o aplicativo lógico.

   Depois que o aplicativo lógico terminar de ser executado, aqui está uma saída de amostra da ação **Enviar mensagem:**

   ![Amostra de saída "Enviar mensagem"](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre ações e limites, descritos pela descrição swagger do conector, revise a página de [referência](/connectors/mq/)do conector .

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
