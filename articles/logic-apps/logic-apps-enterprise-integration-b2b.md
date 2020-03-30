---
title: Trocar mensagens para cenários de integração empresarial B2B
description: Receba e envie mensagens B2B entre parceiros comerciais em Aplicativos Azure Logic usando o Pacote de Integração Corporativa
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 01b2bd464db51e255930fe83a3f4321687322275
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77151052"
---
# <a name="receive-and-send-b2b-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Receba e envie mensagens B2B usando aplicativos azure logic e pacote de integração corporativa

Quando você tem uma conta de integração que define parceiros comerciais e acordos, você pode criar um fluxo de trabalho automatizado de negócios para negócios (B2B) que troca mensagens entre parceiros comerciais usando [o Azure Logic Apps](../logic-apps/logic-apps-overview.md) com o Enterprise Integration [Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). O Azure Logic Apps funciona com conectores que suportam protocolos padrão do setor AS2, X12, EDIFACT e RosettaNet. Você também pode combinar esses conectores com outros [conectores disponíveis em Logic Apps,](../connectors/apis-list.md)por exemplo, Salesforce e Office 365 Outlook.

Este artigo mostra como criar um aplicativo lógico que recebe uma solicitação HTTP usando um gatilho de solicitação, decodifica o conteúdo da mensagem usando as ações AS2 e X12 e, em seguida, retorna uma resposta usando a ação Resposta.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você ainda não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um aplicativo de lógica em branco para que você possa criar o fluxo de trabalho B2B usando o gatilho [Solicitar](../connectors/connectors-native-reqres.md) que é seguido por essas ações:

  * [Decodificar AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [Condição](../logic-apps/logic-apps-control-flow-conditional-statement.md), que envia uma [resposta](../connectors/connectors-native-reqres.md) com base em se a ação AS2 Decode é bem sucedida ou não

  * [Decodificar mensagem X12](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  Se você é novo em aplicativos lógicos, [revise o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md) e [quickstart: crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-accounts.md) que está associada à sua assinatura do Azure e vinculada ao seu aplicativo lógico. Tanto seu aplicativo lógico quanto sua conta de integração devem existir no mesmo local ou região do Azure.

* Pelo menos dois [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) que você já definiu em sua conta de integração, juntamente com [os acordos AS2 e X12](logic-apps-enterprise-integration-agreements.md) para esses parceiros.

## <a name="add-request-trigger"></a>Adicionar gatilho de solicitação

Este exemplo usa o Logic App Designer no portal Azure, mas você pode seguir passos semelhantes para o Logic App Designer no Visual Studio.

1. No [portal Azure,](https://portal.azure.com)abra seu aplicativo de lógica em branco no Logic App Designer.

1. Na caixa de `when a http request`pesquisa, digite e selecione **Quando uma solicitação HTTP for recebida** para usar como gatilho.

   ![Selecione O gatilho solicitar para iniciar o fluxo de trabalho do aplicativo lógico](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. Deixe a **caixa De solicitar esquema JSON** vazia porque a mensagem X12 é um arquivo plano.

   ![Deixar o "Request body JSON Schema" vazio](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. Quando terminar, na barra de ferramentas do designer, selecione **Salvar**.

   Esta etapa gera a **URL HTTP POST** para usar para enviar a solicitação que aciona o aplicativo lógico. Para copiar essa URL, selecione o ícone de cópia ao lado da URL.

   ![URL gerado para o gatilho solicitação para receber chamadas](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-as2-decode-action"></a>Adicionar ação de decodificação AS2

Agora adicione as ações B2B que você deseja usar. Este exemplo usa ações AS2 e X12.

1. No gatilho, selecione **Nova etapa**. Para ocultar os detalhes do gatilho, clique na barra de título do gatilho.

   ![Adicione mais um passo ao seu fluxo de trabalho de aplicativo lógico](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. Em **Escolha uma ação**, na `as2 decode`caixa de pesquisa, digite e selecione **AS2 Decode (v2)**.

   ![Encontre e selecione "Decodificação AS2 (v2)"](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. Para que a **mensagem decodifique a** propriedade, digite a entrada `body` que deseja que a ação AS2 decodifique, que é o conteúdo recebido pelo gatilho de solicitação HTTP. Você tem várias maneiras de especificar esse conteúdo como entrada, seja a partir da lista de conteúdo dinâmico ou como uma expressão:

   * Para selecionar a partir de uma lista que mostra as saídas de gatilho disponíveis, clique em dentro da **caixa Mensagem para decodificar.** Depois que a lista de conteúdo dinâmico for exibida, em **Quando uma solicitação HTTP for recebida,** selecione O valor da propriedade **Corpo,** por exemplo:

     ![Selecione o valor "Corpo" do gatilho](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * Para inserir uma expressão que faz `body` referência à saída do gatilho, clique em dentro da **mensagem para decodificar a** caixa. Depois que a lista de conteúdo dinâmico aparecer, selecione **'Expressão '''''''''''''''** No editor de expressão, digite a expressão aqui e selecione **OK**:

     `triggerOutputs()['body']`

     Ou, na **caixa Mensagem para decodificar,** digite diretamente esta expressão:

     `@triggerBody()`

     A expressão se resolve para o **token Body.**

     ![Saída do corpo resolvida do gatilho](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. Para a propriedade **Cabeçalhos de mensagem,** digite quaisquer cabeçalhos necessários para a ação AS2, que são descritos pelo `headers` conteúdo recebido pelo gatilho de solicitação HTTP.

   Para inserir uma expressão que faz `headers` referência à saída do gatilho, clique na caixa **cabeçalhos da mensagem.** Depois que a lista de conteúdo dinâmico aparecer, selecione **'Expressão '''''''''''''''** No editor de expressão, digite a expressão aqui e selecione **OK**:

   `triggerOutputs()['Headers']`

   Para que essa expressão seja resolvida como este token, alterne entre o designer e a exibição de código, por exemplo:

   ![Saída de cabeçalhos resolvidos do gatilho](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>Adicionar ação de resposta para notificação de recebimento de mensagens

Para notificar o parceiro de negociação de que a mensagem foi recebida, você pode retornar uma resposta que contém uma Notificação de Descarte de Mensagem AS2 (MDN) usando a ação **Resposta.** Adicionando essa ação imediatamente após a ação **AS2 Decode,** caso essa ação falhe, o aplicativo lógico não continua sendo processado.

1. Na ação **AS2 Decode,** selecione **Nova etapa**.

1. Em **Escolha uma ação**, na caixa de pesquisa, **selecione Incorporada**. Na caixa de pesquisa, insira `condition`. Na lista **Ações,** selecione **Condição**.

   ![Adicione a ação "Condição"](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   Agora, a forma da condição aparece, incluindo os caminhos para saber se a condição é atendida ou não.

   ![Forma de condição com caminhos de decisão](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. Agora especifique a condição para avaliar. Na caixa Escolher uma caixa **de valor,** digite esta expressão:

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   Na caixa do meio, certifique-se `is equal to`de que a operação de comparação está definida como . Na caixa do lado direito, `Expected`digite o valor . Para que a expressão seja resolvida como este token, alterne entre o designer e a exibição de código.

   ![Forma de condição com caminhos de decisão](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. Agora especifique as respostas para retornar se a ação **AS2 Decode** for bem sucedida ou não.

   1. Para o caso em que a ação **AS2 Decode** for bem sucedida, na forma **Se for verdade,** **selecione Adicionar uma ação**. Em **Escolha uma ação**, na `response`caixa de pesquisa, digite e selecione **Resposta**.

      ![Encontre e selecione a ação "Resposta"](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. Para acessar o MDN AS2 a partir da saída da ação **AS2 Decode,** especifique estas expressões:

      * Na propriedade **Cabeçalhos** da ação **Resposta,** digite esta expressão:

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * Na propriedade **Corpo** da ação **Resposta,** digite esta expressão:

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. Para que as expressões se resolvam como tokens, alterne entre o designer e a exibição de código:

      ![Expressão resolvida para acessar o AS2 MDN](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. Para o caso em que a ação **AS2 Decode** falhar, na forma **If false,** **selecione Adicionar uma ação**. Em **Escolha uma ação**, na `response`caixa de pesquisa, digite e selecione **Resposta**. Configure a **ação Resposta** para retornar o status e o erro que você deseja.

1. Salve seu aplicativo lógico.

## <a name="add-decode-x12-message-action"></a>Adicionar ação de mensagem Decode X12

1. Agora adicione a ação **de mensagem Decode X12.** Na ação **Resposta,** **selecione Adicionar uma ação**.

1. Em **Escolha uma ação**, na `x12 decode`caixa de pesquisa, digite e selecione **Decode X12 message**.

   ![Encontre e selecione a ação "Decode X12"](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. Se a ação X12 solicitar informações de conexão, forneça o nome da conexão, selecione a conta de integração que deseja usar e selecione **Criar**.

   ![Crie conexão X12 para conta de integração](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. Agora especifique a entrada para a ação X12. Este exemplo usa a saída da ação AS2, que é o conteúdo da mensagem, mas observe que este conteúdo está no formato de objeto JSON e é codificado base64. Então, você tem que converter este conteúdo em uma seqüência.

   Na **mensagem de arquivo X12 Flat para decodificar a** caixa, digite esta expressão para converter a saída AS2:

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    Para que a expressão seja resolvida como este token, alterne entre o designer e a exibição de código.

    ![Converter conteúdo codificado base64 em uma string](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. Salve seu aplicativo lógico.

   Se você precisar de etapas adicionais para este aplicativo lógico, por exemplo, para decodificar o conteúdo da mensagem e produzir esse conteúdo no formato de objeto JSON, continue construindo seu aplicativo lógico.

Agora você terminou de configurar seu aplicativo de lógica B2B. Em um aplicativo do mundo real, você pode querer armazenar os dados X12 decodificados em um aplicativo ou armazenamento de dados (LOB) de linha de negócios. Por exemplo, veja estes artigos:

* [Conectar aos sistemas SAP a partir do Aplicativos Lógicos do Azure](../logic-apps/logic-apps-using-sap-connector.md)
* [Monitore, crie e gerencie arquivos SFTP usando SSH e os Aplicativos Lógicos do Azure](../connectors/connectors-sftp-ssh.md)

Para conectar seus próprios aplicativos LOB e usar essas APIs em seu aplicativo lógico, você pode adicionar mais ações ou [escrever APIs personalizadas.](../logic-apps/logic-apps-create-api-app.md)

## <a name="next-steps"></a>Próximas etapas

* [Receba e responda às chamadas HTTPS recebidas](../connectors/connectors-native-reqres.md)
* [Troque mensagens AS2 para integração corporativa B2B](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [Troque mensagens X12 para integração corporativa B2B](../logic-apps/logic-apps-enterprise-integration-x12.md)
* Saiba mais sobre o [Pacote de Integração Empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md)