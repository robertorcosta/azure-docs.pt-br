---
title: Adicionar e executar trechos de código usando código inline
description: Aprenda a criar e executar trechos de código usando ações de código inline para tarefas e fluxos de trabalho automatizados que você cria com aplicativos de lógica do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: f7a134fd026b42d1666b8310b3fb0c10642c7bb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453505"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Adicionar e executar trechos de código usando código inline em Aplicativos azure Logic

Quando você quiser executar um pedaço de código dentro do seu aplicativo lógico, você pode adicionar a ação **Inline Code** incorporada como um passo no fluxo de trabalho do seu aplicativo lógico. Esta ação funciona melhor quando você deseja executar código que se encaixa neste cenário:

* É executado em JavaScript. Mais línguas em breve.
* Termina de correr em cinco segundos ou menos.
* Lida com dados de até 50 MB de tamanho.
* Não requer trabalhar com as [ **ações variáveis** ](../logic-apps/logic-apps-create-variables-store-values.md), que ainda não são suportadas.
* Usa node.js versão 8.11.1. Para obter mais informações, consulte [Os objetos incorporados Padrão](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects). 

  > [!NOTE]
  > A `require()` função não é suportada pela ação **Código Inline** para executar JavaScript.

Esta ação executa o trecho de código e retorna a saída desse trecho como um token chamado **Resultado**, que você pode usar em ações subseqüentes em seu aplicativo lógico. Para outros cenários onde você deseja criar uma função para o seu código, tente [criar e chamar uma função Azure](../logic-apps/logic-apps-azure-functions.md) em seu aplicativo lógico.

Neste artigo, o aplicativo de lógica de exemplo é acionado quando um novo e-mail chega em uma conta do Office 365 Outlook. O trecho de código extrai e retorna quaisquer endereços de e-mail que apareçam no corpo de e-mail.

![Visão geral do exemplo](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O aplicativo lógico onde você deseja adicionar seu trecho de código, incluindo um gatilho. Se você não tem um aplicativo lógico, consulte [Quickstart: Crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   O aplicativo lógico de exemplo neste tópico usa este gatilho do Office 365 Outlook: **Quando um novo e-mail chega**

* Uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) vinculada ao seu aplicativo lógico

  > [!NOTE]
  > Certifique-se de usar uma conta de integração apropriada para o seu caso de uso. Por exemplo, as contas de integração [de nível livre](../logic-apps/logic-apps-pricing.md#integration-accounts) são destinadas apenas para cenários exploratórios e cargas de trabalho, não cenários de produção, são limitadas em uso e throughput e não são suportadas por um contrato de nível de serviço (SLA). Outros níveis incorrem em custos, mas incluem suporte a SLA, oferecem mais throughput e têm limites mais altos. Saiba mais sobre [os níveis de](../logic-apps/logic-apps-pricing.md#integration-accounts)conta de integração, [preços](https://azure.microsoft.com/pricing/details/logic-apps/)e [limites.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

## <a name="add-inline-code"></a>Adicionar código inline

1. Se você ainda não tem, no [portal Azure,](https://portal.azure.com)abra seu aplicativo lógico no Logic App Designer.

1. No designer, adicione a ação **Código Inline** no local que você deseja no fluxo de trabalho do seu aplicativo lógico.

   * Para adicionar a ação no final do seu fluxo de trabalho, escolha **Nova etapa**.

   * Para adicionar a ação entre as etapas existentes, mova o ponteiro do mouse sobre a seta que conecta essas etapas. Escolha o sinal**+** de mais () e **selecione Adicionar uma ação**.

   Este exemplo adiciona a ação **Código inline** o gatilho do Office 365 Outlook.

   ![Adicionar novo passo](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. Em **Escolha uma ação**, na caixa de pesquisa, digite "código inline" como seu filtro. Na lista de ações, selecione esta ação: **Execute JavaScript Code**

   ![Selecione "Executar código JavaScript"](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   A ação aparece no designer e contém algum código de exemplo padrão, incluindo uma declaração de retorno.

   ![Ação de código inline com código de amostra padrão](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. Na **caixa Código,** exclua o código de exemplo e digite o código que deseja executar. Escreva código que você colocaria dentro de um método, mas sem definir a assinatura do método. 

   Quando você digita uma palavra-chave reconhecida, a lista de preenchimento automático é exibida para que você possa selecionar entre palavras-chave disponíveis, por exemplo:

   ![Lista de preenchimento automático de palavras-chave](./media/logic-apps-add-run-inline-code/auto-complete.png)

   Este exemplo de trecho de código cria primeiro uma variável que armazena uma *expressão regular,* que especifica um padrão para combinar no texto de entrada. O código então cria uma variável que armazena os dados do corpo de e-mail do gatilho.

   ![Criar variáveis](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Para tornar os resultados do gatilho e ações anteriores mais fáceis de fazer referência, a lista de conteúdo dinâmico aparece enquanto seu cursor está dentro da caixa **Código.** Para este exemplo, a lista mostra os resultados disponíveis do gatilho, incluindo o token **Body,** que agora você pode selecionar.

   Depois de selecionar o token **Corpo,** a ação de `workflowContext` código inline resolve o `Body` token para um objeto que faz referência ao valor da propriedade do e-mail:

   ![Selecione o resultado](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   Na caixa **Código,** seu trecho pode usar `workflowContext` o objeto somente leitura como entrada. Este objeto possui subpropriedades que dão ao seu código acesso aos resultados do gatilho e ações anteriores em seu fluxo de trabalho.
   Para obter mais informações, consulte esta seção mais tarde neste tópico: [O gatilho de referência e os resultados de ação em seu código](#workflowcontext).

   > [!NOTE]
   >
   > Se o seu trecho de código fizer referência aos nomes de ação que usam o operador ponto (.), você deve adicionar esses nomes de ação ao parâmetro [ **Ações** ](#add-parameters). Essas referências também devem envolver os nomes de ação com suportes quadrados ([]) e aspas, por exemplo:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   A ação de código inline `return` não requer uma `return` declaração, mas os resultados de uma declaração estão disponíveis para referência em ações posteriores através do **token Resultado.** 
   Por exemplo, o trecho de código retorna `match()` o resultado chamando a função, que encontra correspondências no corpo de e-mail contra a expressão regular. A ação **Compose** usa o **token Resultado** para referenciar os resultados da ação de código inline e cria um único resultado.

   ![Aplicativo lógico concluído](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. Quando terminar, salve o aplicativo lógico.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Gatilho de referência e resultados de ação em seu código

O `workflowContext` objeto possui essa estrutura, `actions` `trigger`que `workflow` inclui as subpropriedades e subpropriedades:

```json
{
   "workflowContext": {
      "actions": {
         "<action-name-1>": @actions('<action-name-1>'),
         "<action-name-2>": @actions('<action-name-2>')
      },
      "trigger": {
         @trigger()
      },
      "workflow": {
         @workflow()
      }
   }
}
```

Esta tabela contém mais informações sobre essas subpropriedades:

| Propriedade | Type | Descrição |
|----------|------|-------|
| `actions` | Coleção de objetos | Resulta de objetos de ações que são executadas antes do seu trecho de código ser executado. Cada objeto tem um par *de valor-chave* onde a chave é o nome de uma `@actions('<action-name>')`ação, e o valor é equivalente a chamar a função de ações [com](../logic-apps/workflow-definition-language-functions-reference.md#actions) . O nome da ação usa o mesmo nome de ação usado na definição de fluxo de trabalho subjacente, que substitui espaços (" "" " no nome da ação por sublinhados (_). Esse objeto fornece acesso aos valores da propriedade de ação a partir da instância atual do fluxo de trabalho. |
| `trigger` | Objeto | Objeto de resultado do gatilho e equivalente a chamar a [função de gatilho()](../logic-apps/workflow-definition-language-functions-reference.md#trigger) Esse objeto fornece acesso aos valores de propriedade de gatilho a partir da instância atual do fluxo de trabalho. |
| `workflow` | Objeto | O objeto de fluxo de trabalho e equivalente à chamada [função fluxo de trabalho ().](../logic-apps/workflow-definition-language-functions-reference.md#workflow) Esse objeto fornece acesso aos valores de propriedade do fluxo de trabalho, como o nome do fluxo de trabalho, o ID de execução e assim por diante, a partir da instância atual do fluxo de trabalho. |
|||

No exemplo deste tópico, `workflowContext` o objeto tem essas propriedades que seu código pode acessar:

```json
{
   "workflowContext": {
      "trigger": {
         "name": "When_a_new_email_arrives",
         "inputs": {
            "host": {
               "connection": {
                  "name": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/office365"
               }
            },
            "method": "get",
            "path": "/Mail/OnNewEmail",
            "queries": {
               "includeAttachments": "False"
            }
         },
         "outputs": {
            "headers": {
               "Pragma": "no-cache",
               "Content-Type": "application/json; charset=utf-8",
               "Expires": "-1",
               "Content-Length": "962095"
            },
            "body": {
               "Id": "AAMkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgBGAAAAAABmZwxUQtCGTqSPpjjMQeD",
               "DateTimeReceived": "2019-03-28T19:42:16+00:00",
               "HasAttachment": false,
               "Subject": "Hello World",
               "BodyPreview": "Hello World",
               "Importance": 1,
               "ConversationId": "AAQkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgAQ",
               "IsRead": false,
               "IsHtml": true,
               "Body": "Hello World",
               "From": "<sender>@<domain>.com",
               "To": "<recipient-2>@<domain>.com;<recipient-2>@<domain>.com",
               "Cc": null,
               "Bcc": null,
               "Attachments": []
            }
         },
         "startTime": "2019-05-03T14:30:45.971564Z",
         "endTime": "2019-05-03T14:30:50.1746874Z",
         "scheduledTime": "2019-05-03T14:30:45.8778117Z",
         "trackingId": "1cd5ffbd-f989-4df5-a96a-6e9ce31d03c5",
         "clientTrackingId": "08586447130394969981639729333CU06",
         "originHistoryName": "08586447130394969981639729333CU06",
         "code": "OK",
         "status": "Succeeded"
      },
      "workflow": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>",
         "name": "<logic-app-workflow-name>",
         "type": "Microsoft.Logic/workflows",
         "location": "<Azure-region>",
         "run": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>/runs/08586453954668694173655267965CU00",
            "name": "08586453954668694173655267965CU00",
            "type": "Microsoft.Logic/workflows/runs"
         }
      }
   }
}
```

<a name="add-parameters"></a>

## <a name="add-parameters"></a>Adicionar parâmetros

Em alguns casos, você pode ter que exigir explicitamente que a ação **Código Inline** inclua resultados do gatilho ou ações específicas que seu código faz referência como dependências adicionando os parâmetros **Gatilho** ou **Ações.** Esta opção é útil para cenários onde os resultados referenciados não são encontrados em tempo de execução.

> [!TIP]
> Se você planeja reutilizar seu código, adicione referências às propriedades usando a caixa **Código** para que seu código inclua as referências de token resolvidas, em vez de adicionar o gatilho ou as ações como dependências explícitas.

Por exemplo, suponha que você tenha um código que faça referência ao resultado da **Opção Selecionada** da ação **de e-mail** de aprovação do Enviar para o conector Do Office 365 Outlook. No momento da criação, o mecanismo Logic Apps analisa seu código para determinar se você fez referência a algum resultado de gatilho ou ação e inclui esses resultados automaticamente. No tempo de execução, se você tiver um erro de que o `workflowContext` gatilho ou o resultado da ação referenciado não estiver disponível no objeto especificado, você pode adicionar esse gatilho ou ação como uma dependência explícita. Neste exemplo, você adiciona o parâmetro **Ações** e especifica que a ação **Código inline** inclui explicitamente o resultado da ação **de e-mail de aprovação enviar.**

Para adicionar esses parâmetros, abra a lista **Adicionar novos parâmetros** e selecione os parâmetros desejados:

   ![Adicionar parâmetros](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Parâmetro | Descrição |
   |-----------|-------------|
   | **Ações** | Inclua resultados de ações anteriores. Consulte [Incluir resultados de ação](#action-results). |
   | **Gatilho** | Inclua os resultados do gatilho. Veja [Incluir resultados do gatilho](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Inclua resultados de gatilho

Se você selecionar **Gatilhos,** você será solicitado a incluir os resultados do gatilho.

* Na lista **Trigger,** selecione **Sim**.

<a name="action-results"></a>

### <a name="include-action-results"></a>Inclua resultados de ação

Se você selecionar **Ações,** você será solicitado para as ações que deseja adicionar. No entanto, antes de começar a adicionar ações, você precisa da versão do nome de ação que aparece na definição de fluxo de trabalho subjacente do aplicativo lógico.

* Esse recurso não suporta variáveis, loops e índices de iteração.

* Nomes na definição do fluxo de trabalho do seu aplicativo lógico usam um sublinhado (_), não um espaço.

* Para nomes de ação que usam o operador de dot (.), incluem esses operadores, por exemplo:

  `My.Action.Name`

1. Na barra de ferramentas do designer, escolha `actions` **'Exibir'** e pesquise dentro do atributo para o nome da ação.

   Por exemplo, `Send_approval_email_` é o nome JSON para a ação **enviar e-mail de aprovação.**

   ![Encontre o nome da ação no JSON](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Para retornar à exibição do designer, na barra de ferramentas de exibição de código, escolha **Designer**.

1. Para adicionar a primeira ação, no **Item ações - 1** caixa, digite o nome JSON da ação.

   ![Insira a primeira ação](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Para adicionar outra ação, escolha **Adicionar novo item**.

## <a name="reference"></a>Referência

Para obter mais informações sobre a estrutura e a sintaxe da ação **Execute JavaScript Code** na definição de fluxo de trabalho subjacente do seu aplicativo lógico usando a Linguagem de Definição de Fluxo de Trabalho, consulte a [seção](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)de referência desta ação .

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [conectores para aplicativos azure logic](../connectors/apis-list.md)
