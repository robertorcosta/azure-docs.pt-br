---
title: Chamar, disparar ou aninhar aplicativos lógicos
description: Configure pontos finais HTTP para fluxos de trabalho de aplicativos de lógica de chamada, gatilho ou ninho em aplicativos de lógica do Azure
services: logic-apps
ms.workload: integration
ms.reviewer: klam, jehollan, logicappspm
ms.topic: article
ms.date: 11/04/2019
ms.openlocfilehash: d5b5a69c7927d07c0ae6b3b56ec97b6551e5d46b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191342"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-http-endpoints-in-azure-logic-apps"></a>Aplicativos de lógica de chamada, gatilho ou ninho usando pontos finais HTTP em Aplicativos de Lógica do Azure

Para tornar seu aplicativo lógico callable através de uma URL para que seu aplicativo lógico possa receber solicitações recebidas de outros serviços, você pode expor nativamente um ponto final HTTP síncrono como um gatilho nesse aplicativo lógico. Quando você configura esse recurso, você também pode aninhar seu aplicativo lógico dentro de outros aplicativos lógicos, o que permite criar um padrão de pontos finais callable.

Para configurar um ponto final HTTP, você pode usar qualquer um desses tipos de gatilho, que permitem que os aplicativos lógicos recebam solicitações recebidas:

* [Solicitação](../connectors/connectors-native-reqres.md)
* [Webhook HTTP](../connectors/connectors-native-webhook.md)
* Acionados de conector gerenciado que possuem o [tipo ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) e podem receber solicitações HTTP recebidas

> [!NOTE]
> Esses exemplos usam o gatilho Solicitação, mas você pode usar qualquer gatilho baseado em solicitação HTTP que esteja na lista anterior. Todos os princípios se aplicam de forma idêntica a esses outros tipos de gatilho.

Se você é novo em aplicativos lógicos, veja [o que é a Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [Quickstart: Crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O aplicativo lógico onde você deseja configurar o ponto final HTTP como o gatilho. Você pode começar com um aplicativo de lógica em branco ou com um aplicativo lógico existente onde deseja substituir o gatilho atual. Este exemplo começa com um aplicativo de lógica em branco.

## <a name="create-a-callable-endpoint"></a>Crie um ponto final callable

1. Faça login no [portal Azure](https://portal.azure.com). Crie e abra um aplicativo de lógica em branco no Logic App Designer.

   Este exemplo usa o gatilho Solicitar, mas você pode usar qualquer gatilho que possa receber solicitações HTTP recebidas. Todos os princípios se aplicam a esses gatilhos. Para obter mais informações sobre o gatilho de solicitação, consulte [Receber e responder às chamadas HTTPS recebidas usando o Azure Logic Apps](../connectors/connectors-native-reqres.md).

1. Na caixa de pesquisa, **selecione Built-in**. Na caixa de pesquisa, insira `request` como o filtro. Na lista de gatilhos, **selecione Quando uma solicitação HTTP for recebida**.

   ![Encontre e selecione o gatilho Solicitar](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. Opcionalmente, na caixa **Esquema JSON** do Corpo de Solicitação, você pode inserir um esquema JSON que descreve a carga ou os dados que você espera que o gatilho receba.

   O designer usa este esquema para gerar tokens que representam saídas de gatilho. Você pode, então, facilmente referenciar essas saídas através do fluxo de trabalho do seu aplicativo lógico. Saiba mais sobre [tokens gerados a partir de esquemas JSON](#generated-tokens).

   Para este exemplo, digite este esquema:

   ```json
      {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "streetNumber": {
                  "type": "string"
               },
               "streetName": {
                  "type": "string"
               },
               "town": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "string"
               }
            }
         }
      }
   }
    ```

   ![Fornecer esquema JSON para a ação solicitação](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   Ou, você pode gerar um esquema JSON fornecendo uma carga de amostra:

   1. No gatilho **Solicitar,** selecione **Usar carga útil da amostra para gerar esquema**.

   1. Na **caixa De entrar ou colar uma caixa de carga JSON amostra,** digite sua carga de amostra, por exemplo:

      ```json
      {
         "address": {
            "streetNumber": "00000",
            "streetName": "AnyStreet",
            "town": "AnyTown",
            "postalCode": "11111-1111"
        }
      }
      ```

   1. Quando estiver pronto, selecione **Pronto**.

      A **caixa Esquema JSON Do Corpo de Solicitação** agora mostra o esquema gerado.

1. Salve seu aplicativo lógico.

   O **POST HTTP para esta** caixa de URL agora mostra a URL de retorno de chamada gerada que outros serviços podem usar para ligar e acionar seu aplicativo lógico. Esta URL inclui uma chave SAS (SAS) de assinatura de acesso compartilhada, usada para autenticação, nos parâmetros de consulta, por exemplo:

   ![URL de retorno de chamada gerada para ponto de extremidade](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

   Você também pode obter a URL http endpoint do painel **Visão Geral** do seu aplicativo lógico.

   1. No menu do aplicativo lógico, selecione **Visão geral**.

   1. Na seção **Resumo,** selecione **'Ver histórico de gatilhos ''''''''''''''''**

      ![Obter a URL de ponto de extremidade HTTP no portal do Azure](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

   1. Em **Url de retorno de chamada [POST]**, copie a URL:

      ![Copiar URL de ponto final HTTP do portal Azure](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url.png)

      Ou você pode obter a URL fazendo esta chamada:

      ```http
      POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01
      ```

<a name="set-method"></a>

## <a name="set-expected-http-method"></a>Definir o método HTTP esperado

Por padrão, o gatilho Solicitação aguarda uma solicitação HTTP POST. No entanto, você pode especificar um método diferente para esperar, mas apenas um método.

1. No gatilho 'Solicitar', abra a lista **Adicionar novo parâmetro** e selecione **Método**, que adiciona essa propriedade ao gatilho.

   ![Adicionar propriedade "Método" para acionar](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. Na lista **Método,** selecione outro método que o gatilho espera em vez disso. Ou, você pode especificar um método personalizado.

   Por exemplo, selecione o método **GET** para que você possa testar a URL do seu ponto final HTTP mais tarde.

   ![Selecione o método HTTP para usar no gatilho](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

## <a name="accept-parameters-in-endpoint-url"></a>Aceitar parâmetros na URL de ponto final

Quando quiser que sua URL de ponto final aceite parâmetros, especifique o caminho relativo no gatilho. Você também precisa [definir](#set-method) explicitamente o método que sua solicitação HTTP espera.

1. No gatilho 'Solicitar', abra a lista **Adicionar novo parâmetro** e selecione **'Caminho relativo'** que adiciona essa propriedade ao gatilho.

   ![Adicionar a propriedade "Caminho relativo" para acionar](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. Na propriedade **Relative path,** especifique o caminho relativo para o parâmetro no seu esquema `address/{postalCode}`JSON que você deseja que sua URL aceite, por exemplo, .

   ![Especifique o caminho relativo para o parâmetro](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. Para usar o parâmetro, encontre e adicione uma ação **de resposta** ao seu aplicativo lógico.

   1. No gatilho 'Solicitar', selecione **Nova etapa** > **Adicione uma ação**.

   1. Em **Escolher uma ação**, na caixa de pesquisa, insira `response` como o filtro.

   1. Na lista de ações, selecione a **ação Resposta.**

1. Na propriedade **Corpo** da ação resposta, inclua o token que representa o parâmetro especificado no caminho relativo do seu gatilho.

   Por exemplo, suponha que você `Postal Code: {postalCode}`queira que a ação resposta retorne .

   Na propriedade **body,** entre `Postal Code: ` com um espaço de trilha. Na lista de conteúdo dinâmico que aparece, selecione o **token postalCode.**

   ![Adicione o parâmetro especificado ao corpo de resposta](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

   A propriedade **Body** agora inclui o parâmetro selecionado:

   ![Exemplo de corpo de resposta com parâmetro](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Salve seu aplicativo lógico.

    A URL de ponto de extremidade HTTP agora inclui o caminho relativo, por exemplo:

    ```http
    https://prod-25.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}
    ```

1. Para testar o ponto de extremidade HTTP, copie e cole a URL atualizada em outra janela do navegador, mas substitua `{postalCode}` por `123456` e pressione Enter.

   Seu navegador mostra este texto:`Postal Code: 123456`

## <a name="call-logic-app-through-http-endpoint"></a>Aplicativo de lógica de chamada através do ponto final HTTP

Depois de criar o ponto final HTTP, você pode `POST` acionar o aplicativo lógico enviando uma solicitação HTTP para a URL completa do ponto final. Os aplicativos lógicos têm suporte interno para pontos de extremidade de acesso direto.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Tokens gerados a partir de esquemas

Quando você fornece um esquema JSON no gatilho Solicitação, o Logic App Designer gera tokens para as propriedades nesse esquema. Assim, você pode usar esses tokens para transmitir dados por meio do fluxo de trabalho do aplicativo lógico.

Por exemplo, se você adicionar `"suite"`mais propriedades, como, no seu esquema JSON, tokens para essas propriedades estão disponíveis para você usar nas etapas posteriores do seu aplicativo lógico. Veja a seguir o esquema JSON completo:

```json
   {
   "type": "object",
   "properties": {
      "address": {
         "type": "object",
         "properties": {
            "streetNumber": {
               "type": "string"
            },
            "streetName": {
               "type": "string"
            },
            "suite": {
               "type": "string"
            },
            "town": {
               "type": "string"
            },
            "postalCode": {
               "type": "string"
            }
         }
      }
   }
}
```

## <a name="create-nested-logic-apps"></a>Criar aplicativos lógicos aninhados

Você pode aninhar os fluxos de trabalho no aplicativo lógico adicionando outros aplicativos lógicos que podem receber solicitações. Para incluir esses aplicativos lógicos, siga estas etapas:

1. a etapa em que você deseja chamar outro aplicativo lógico, selecione **Novo passo** > **Adicionar uma ação**.

1. Em **Escolha uma ação**, selecione **Interno**. Na caixa de pesquisa, insira `logic apps` como o filtro. Na lista de ações, **selecione Escolha um fluxo de trabalho de aplicativos lógicos**.

   ![Aplicativo de lógica nest dentro do aplicativo lógico atual](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   O designer mostra os aplicativos lógicos elegíveis para você selecionar.

1. Selecione o aplicativo lógico para ligar do seu aplicativo lógico atual.

   ![Selecione o aplicativo lógico para chamar do aplicativo lógico atual](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Fazer referência ao conteúdo de uma solicitação de entrada

Se o tipo de conteúdo `application/json`da solicitação de entrada for, você pode referenciar as propriedades na solicitação recebida. Caso contrário, este conteúdo é tratado como uma única unidade binária que você pode passar para outras APIs. Para referenciar esse conteúdo dentro do fluxo de trabalho do seu aplicativo lógico, você precisa primeiro converter esse conteúdo.

Por exemplo, se você estiver `application/xml` passando conteúdo que tenha tipo, você pode usar a [ `@xpath()` expressão](../logic-apps/workflow-definition-language-functions-reference.md#xpath) para executar uma extração XPath ou usar a [ `@json()` expressão](../logic-apps/workflow-definition-language-functions-reference.md#json) para converter XML para JSON. Saiba mais sobre como trabalhar com tipos de [conteúdo](../logic-apps/logic-apps-content-type.md)suportados.

Para obter a saída de uma solicitação [ `@triggerOutputs` ](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs)recebida, você pode usar a expressão . Por exemplo, suponha que você tenha uma saída que se pareça com este exemplo:

```json
{
   "headers": {
      "content-type" : "application/json"
   },
   "body": {
      "myProperty" : "property value"
   }
}
```

Para acessar especificamente a `body` propriedade, você pode usar a [ `@triggerBody()` expressão](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) como um atalho.

## <a name="respond-to-requests"></a>Responder às solicitações

Às vezes, você quer responder a certas solicitações que acionam seu aplicativo lógico retornando o conteúdo para o chamador. Para construir o código de status, cabeçalho e corpo para sua resposta, use a ação Resposta. Essa ação pode aparecer em qualquer lugar no aplicativo lógico, não apenas no fim do fluxo de trabalho. Se o seu aplicativo lógico não incluir uma ação de resposta, o ponto final HTTP responderá *imediatamente* com o status **202 Accepted.**

Para que o chamador original obtenha a resposta com sucesso, todas as etapas necessárias para a resposta devem ser concluídas dentro do [limite de tempo limite de solicitação,](./logic-apps-limits-and-config.md) a menos que o aplicativo lógico acionado seja chamado como um aplicativo de lógica aninhada. Se nenhuma resposta for retornada dentro desse limite, a solicitação recebida é retirada e recebe a resposta de tempo limite do **Cliente 408.**

Para aplicativos lógicos aninhados, o aplicativo de lógica pai continua esperando por uma resposta até que todas as etapas sejam concluídas, independentemente de quanto tempo seja necessário.

### <a name="construct-the-response"></a>Construir a resposta

No corpo de resposta, você pode incluir vários cabeçalhos e qualquer tipo de conteúdo. Por exemplo, o cabeçalho desta resposta especifica que `application/json` o tipo de conteúdo `town` `postalCode` da resposta é e que o corpo contém valores para as propriedades e propriedades, com base no esquema JSON descrito anteriormente neste tópico para o gatilho Solicitação.

![Fornecer conteúdo de resposta para ação HTTP Response](./media/logic-apps-http-endpoint/content-for-response-action.png)

As respostas têm estas propriedades:

| Propriedade (Exibição) | Property (JSON) | Descrição |
|--------------------|-----------------|-------------|
| **Código de status** | `statusCode` | O código de status HTTP a ser usado na resposta para a solicitação recebida. Este código pode ser qualquer código de status válido que comece com 2xx, 4xx ou 5xx. No entanto, não há permissão para códigos de status 3xx. |
| **Cabeçalhos** | `headers` | Um ou mais cabeçalhos para incluir na resposta |
| **Corpo** | `body` | Um objeto corporal que pode ser uma string, um objeto JSON ou até mesmo conteúdo binário referenciado de uma etapa anterior |
||||

Para visualizar a definição JSON para a ação Response e a definição completa de JSON do seu aplicativo lógico, na barra de ferramentas logic app designer, selecione **Exibição de código**.

``` json
"Response": {
   "type": "Response",
   "kind": "http",
   "inputs": {
      "body": {
         "postalCode": "@triggerBody()?['address']?['postalCode']",
         "town": "@triggerBody()?['address']?['town']"
      },
      "headers": {
         "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {}
}
```

## <a name="q--a"></a>Perguntas e respostas

#### <a name="q-what-about-url-security"></a>P: O que dizer sobre a segurança de URL?

**A**: O Azure gera com segurança URLs de retorno de chamada de aplicativo de lógica usando [sas (SAS) (SAS).](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature) Essa assinatura passa como um parâmetro de consulta e deve ser validada antes que seu aplicativo lógico possa ser executado. O Azure gera a assinatura usando uma combinação exclusiva de uma chave secreta por aplicativo lógico, o nome do gatilho e a operação que é executada. Portanto, a menos que alguém tenha acesso à chave secreta do aplicativo lógico, não é possível gerar uma assinatura válida.

> [!IMPORTANT]
> Para produção e sistemas de segurança mais elevados, aconselhamos fortemente contra chamar seu aplicativo lógico diretamente do navegador por essas razões:
>
> * A chave de acesso compartilhado é exibida na URL.
> * Você não pode gerenciar políticas de conteúdo de segurança devido a domínios compartilhados entre os clientes do Azure Logic Apps.

#### <a name="q-can-i-configure-http-endpoints-further"></a>P: Posso configurar pontos de extremidade HTTP mais tarde?

**A:** Sim, os pontos finais HTTP suportam configurações mais avançadas através do [Gerenciamento de API do Azure](../api-management/api-management-key-concepts.md). Esse serviço também oferece a capacidade de gerenciar todas as suas APIs de modo consistente, incluindo aplicativos lógicos, configurar os nomes de domínio personalizados, usar mais métodos de autenticação e mais, por exemplo:

* [Alterar o método de solicitação](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [Alterar os segmentos de URL da solicitação](../api-management/api-management-transformation-policies.md#RewriteURL)
* Configure seus domínios de gerenciamento de API no [portal Azure](https://portal.azure.com/)
* Configurar a política para verificar a autenticação Básica

## <a name="next-steps"></a>Próximas etapas

* [Receba e responda às chamadas HTTPS recebidas usando aplicativos azure logic](../connectors/connectors-native-reqres.md)