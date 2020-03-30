---
title: Lidar com erros e exceções em fluxos de trabalho
description: Saiba como lidar com erros e exceções que acontecem em tarefas e fluxos de trabalho automatizados criados usando aplicativos de lógica do Azure
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.date: 01/11/2020
ms.topic: article
ms.openlocfilehash: 73b116117530e5a2103b604efbf757d691006508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284025"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Tratar erros e exceções em Aplicativos Lógicos do Azure

A maneira como qualquer arquitetura de integração lida adequadamente com o tempo de inatividade ou problemas causados por sistemas dependentes pode representar um desafio. Para ajudá-lo a criar integrações robustas e resilientes que lidam com problemas e falhas, o Logic Apps oferece uma experiência de primeira classe para lidar com erros e exceções.

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Políticas de repetição

Para a exceção mais básica e o manuseio de erros, você pode usar uma *política de repetição* em qualquer ação ou gatilho onde suportado, por exemplo, ver [ação HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger). Uma política de repetição especifica se e como a ação ou o acionador tenta novamente uma solicitação quando a solicitação original expira ou falha, que é qualquer solicitação que resulte em uma resposta 408, 429 ou 5xx. Se nenhuma outra política de repetição for usada, a política padrão será usada.

Aqui estão os tipos de política de repetição:

| Type | Descrição |
|------|-------------|
| **Padrão** | Essa política envia até quatro novas tentativas em intervalos *exponencialmente crescentes*, que são dimensionados em 7,5 segundos, mas são limitados entre 5 e 45 segundos. |
| **Intervalo exponencial**  | Essa política aguarda um intervalo aleatório selecionado de um intervalo em crescimento exponencial antes de enviar a próxima solicitação. |
| **Intervalo fixo**  | Essa política aguarda o intervalo especificado antes de enviar a próxima solicitação. |
| **Nenhum**  | Não reenvie o pedido. |
|||

Para obter informações sobre novos limites de política, consulte [Limites e configuração de aplicativos lógicos](../logic-apps/logic-apps-limits-and-config.md#request-limits).

### <a name="change-retry-policy"></a>Política de repetição de alteração

Para selecionar uma política de repetição diferente, siga estas etapas:

1. Abra seu aplicativo lógico no Logic App Designer.

1. Abra o **configurações** para um gatilho ou ação.

1. Se a ação ou o gatilho suportar novas políticas, em **Repetir política**, selecione o tipo desejado.

Ou você pode especificar manualmente a política de repetição na seção `inputs` para uma ação ou um gatilho que suporte políticas de repetição. Se você não especificar uma política de repetição, a ação usa a política padrão.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": {
      "<action-specific-inputs>",
      "retryPolicy": {
         "type": "<retry-policy-type>",
         "interval": "<retry-interval>",
         "count": <retry-attempts>,
         "minimumInterval": "<minimum-interval>",
         "maximumInterval": "<maximum-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Obrigatório*

| Valor | Type | Descrição |
|-------|------|-------------|
| <*tipo de política de repetição*> | String | O tipo de política de repetição que você deseja usar: `default`, `none`, `fixed`, ou `exponential` |
| <*retritry-interval*> | String | O intervalo de repetição em que o valor deve usar [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). O intervalo mínimo de padrão é `PT5S` e o intervalo máximo é `PT1D`. Ao usar a política de intervalo exponencial, você pode especificar valores mínimos e máximos diferentes. |
| <*tentativas de repetição*> | Integer | O número de tentativas de repetição, que deve estar entre 1 e 90 |
||||

*Opcional*

| Valor | Type | Descrição |
|-------|------|-------------|
| <*intervalo mínimo*> | String | Para a política de intervalo exponencial, o menor intervalo para o intervalo selecionado aleatoriamente no formato [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
| <*intervalo máximo*> | String | Para a política de intervalo exponencial, o maior intervalo para o intervalo selecionado aleatoriamente no formato [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
||||

Aqui estão mais informações sobre os diferentes tipos de políticas.

<a name="default-retry"></a>

### <a name="default"></a>Padrão

Se você não especificar uma política de repetição, a ação usará a política padrão, que é na verdade uma política de [intervalo exponencial](#exponential-interval) que envia até quatro novas tentativas em intervalos de aumento exponencial que são dimensionados por 7,5 segundos. O intervalo é limitado entre 5 e 45 segundos.

Embora não seja explicitamente definido em sua ação ou acionador, aqui está como a política padrão se comporta em uma ação HTTP de exemplo:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://myAPIendpoint/api/action",
      "retryPolicy" : {
         "type": "exponential",
         "interval": "PT7S",
         "count": 4,
         "minimumInterval": "PT5S",
         "maximumInterval": "PT1H"
      }
   },
   "runAfter": {}
}
```

### <a name="none"></a>Nenhum

Para especificar que a ação ou gatilho não tentará repetir solicitações com falha, defina o <*tipo de política de repetição*> para `none`.

### <a name="fixed-interval"></a>Intervalo fixo

Para especificar que a ação ou o acionador aguarda o intervalo especificado antes de enviar a próxima solicitação, defina <*retry-policy-type*> como `fixed`.

*Exemplo*

Essa política de repetição tenta receber as últimas notícias mais duas vezes após a primeira solicitação com falha, com um atraso de 30 segundos entre cada tentativa:

```json
"Get_latest_news": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest",
      "retryPolicy": {
         "type": "fixed",
         "interval": "PT30S",
         "count": 2
      }
   }
}
```

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Intervalo exponencial

Para especificar que a ação ou o acionador aguarda um intervalo aleatório antes de enviar a próxima solicitação, defina o <*retry-policy-type*> como `exponential`. O intervalo aleatório é selecionado de um intervalo crescente exponencial. Opcionalmente, você também pode substituir os intervalos mínimo e máximo padrão, especificando seus próprios intervalos mínimo e máximo.

**Intervalos de variável aleatória**

Esta tabela mostra como o Logic Apps gera uma variável aleatória uniforme no intervalo especificado para cada nova tentativa até e incluindo o número de novas tentativas:

| Número de Repetições | Intervalo mínimo | Intervalo máximo |
|--------------|------------------|------------------|
| 1 | max(0, <> *de intervalo mínimo)* | minuto (intervalo, <> *de intervalo máximo)* |
| 2 | max (intervalo, <*> de intervalo mínimo)* | min(intervalo de 2 * <*intervalo máximo*>) |
| 3 | Max (2 * intervalo, <*intervalo mínimo*>) | min (intervalo de 4 * <*intervalo máximo*>) |
| 4 | max(intervalo de 4 *, <> *de intervalo mínimo)* | min (intervalo de 8 * <intervalo *máximo*>) |
| .... | .... | .... |
||||

<a name="control-run-after-behavior"></a>

## <a name="catch-and-handle-failures-by-changing-run-after-behavior"></a>Pegar e lidar com falhas alterando o comportamento de "correr atrás"

Quando você adiciona ações no Logic App Designer, você declara implicitamente a ordem de uso para executar essas ações. Depois que uma ação termina de ser executado, `Succeeded` `Failed`essa `Skipped`ação `TimedOut`é marcada com um status como, ou . Em cada definição `runAfter` de ação, a propriedade especifica a ação predecessora que deve primeiro terminar e os status permitidos para esse antecessor antes que a ação do sucessor possa ser executada. Por padrão, uma ação que você adiciona no designer `Succeeded` é executada somente depois que o antecessor completa com status.

Quando uma ação lança um erro ou exceção `Failed`não manuseado, a ação é marcada e qualquer ação sucessora é marcada `Skipped`. Se esse comportamento acontecer para uma ação que tenha ramificações paralelas, o mecanismo Logic Apps segue os outros ramos para determinar seus status de conclusão. Por exemplo, se um `Skipped` ramo termina com uma ação, o status de conclusão desse ramo é baseado no status predecessor dessa ação. Depois que a execução do aplicativo lógico é concluída, o mecanismo determina todo o status da execução avaliando todos os status do ramo. Se qualquer ramo terminar em falha, `Failed`toda a execução do aplicativo lógico será marcada .

![Exemplos que mostram como os status de execução são avaliados](./media/logic-apps-exception-handling/status-evaluation-for-parallel-branches.png)

Para garantir que uma ação ainda possa ser executada apesar do status de seu antecessor, [personalize o comportamento de "correr atrás" de uma ação](#customize-run-after) para lidar com os status mal sucedidos do antecessor.

<a name="customize-run-after"></a>

### <a name="customize-run-after-behavior"></a>Personalize o comportamento de "executar depois"

Você pode personalizar o comportamento de "correr atrás" de uma ação para `Succeeded`que `Failed` `Skipped`a `TimedOut`ação seja executada quando o status do antecessor for ou , ou qualquer um desses status. Por exemplo, para enviar um `Add_a_row_into_a_table` e-mail `Failed`após a `Succeeded`ação do predecessor do Excel Online ser marcada, em vez de alterar o comportamento "executar depois" seguindo qualquer etapa:

* Na exibição de design, selecione o botão elipses (**...**) e, em seguida, **selecione Configurar executar depois**.

  ![Configure o comportamento de "correr atrás" para uma ação](./media/logic-apps-exception-handling/configure-run-after-property-setting.png)

  A forma de ação mostra o status padrão necessário para a ação antecessora, que é **Adicionar uma linha em uma tabela** neste exemplo:

  ![Comportamento padrão de "executar depois" para uma ação](./media/logic-apps-exception-handling/change-run-after-property-status.png)

  Altere o comportamento "executar depois" para o status que você deseja, que é **falho** neste exemplo:

  ![Alterar o comportamento de "correr atrás" para "falhou"](./media/logic-apps-exception-handling/run-after-property-status-set-to-failed.png)

  Para especificar que a ação é `Failed`executada `Skipped` `TimedOut`se a ação predecessora está marcada como , ou, selecione os outros status:

  ![Alterar o comportamento de "correr atrás" para ter qualquer outro status](./media/logic-apps-exception-handling/run-after-property-multiple-statuses.png)

* Na exibição de código, na definição JSON da ação, edite a `runAfter` propriedade, que segue esta sintaxe:

  ```json
  "<action-name>": {
     "inputs": {
        "<action-specific-inputs>"
     },
     "runAfter": {
        "<preceding-action>": [
           "Succeeded"
        ]
     },
     "type": "<action-type>"
  }
  ```

  Para este exemplo, `runAfter` altere `Failed`a propriedade de: `Succeeded`

  ```json
  "Send_an_email_(V2)": {
     "inputs": {
        "body": {
           "Body": "<p>Failed to&nbsp;add row to &nbsp;@{body('Add_a_row_into_a_table')?['Terms']}</p>",,
           "Subject": "Add row to table failed: @{body('Add_a_row_into_a_table')?['Terms']}",
           "To": "Sophia.Owen@fabrikam.com"
        },
        "host": {
           "connection": {
              "name": "@parameters('$connections')['office365']['connectionId']"
           }
        },
        "method": "post",
        "path": "/v2/Mail"
     },
     "runAfter": {
        "Add_a_row_into_a_table": [
           "Failed"
        ]
     },
     "type": "ApiConnection"
  }
  ```

  Para especificar que a ação é `Failed`executada `Skipped` `TimedOut`se a ação predecessora está marcada como , ou , adicionar os outros status:

  ```json
  "runAfter": {
     "Add_a_row_into_a_table": [
        "Failed", "Skipped", "TimedOut"
     ]
  },
  ```

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Avaliar ações com escopos e seus resultados

Semelhante a executar etapas `runAfter` após ações individuais com a propriedade, você pode agrupar ações dentro de um [escopo](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Você pode usar escopos quando quiser agrupar ações logicamente, avaliar o status agregado do escopo e executar ações com base no status. Depois que todas as ações em um escopo concluem a execução, o próprio escopo também obtém seu próprio status.

Para verificar o status de um escopo, você pode usar os mesmos critérios que `Succeeded` `Failed`você usa para verificar o status de execução de um aplicativo lógico, como , e assim por diante.

Por padrão, quando todas as ações do escopo são `Succeeded`bem sucedidas, o status do escopo é marcado . Se a ação final em `Failed` `Aborted`um escopo resultar como `Failed`ou , o status do escopo será marcado .

Para capturar exceções `Failed` em um escopo e executar ações `runAfter` que lidam `Failed` com esses erros, você pode usar a propriedade para esse escopo. Dessa forma, se *qualquer* ação no escopo `runAfter` falhar, e você usar a propriedade para esse escopo, você pode criar uma única ação para capturar falhas.

Para limites nos escopos, consulte [Limites e configurações](../logic-apps/logic-apps-limits-and-config.md).

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>Obter o contexto e os resultados de falhas

Embora seja útil detectar falhas de um escopo, convém ter o contexto para ajudá-lo a entender exatamente quais ações falharam, além de quais erros ou códigos de status foram retornados.

A [`result()`](../logic-apps/workflow-definition-language-functions-reference.md#result) função fornece contexto sobre os resultados de todas as ações em um escopo. A `result()` função aceita um único parâmetro, que é o nome do escopo, e retorna uma matriz que contém todos os resultados de ação de dentro desse escopo. Esses objetos de ação incluem `actions()` os mesmos atributos do objeto, como o tempo de início da ação, tempo de término, status, entradas, IDs de correlação e saídas. Para enviar contexto para quaisquer ações que falharam `@result()` dentro de `runAfter` um escopo, você pode facilmente emparelhar uma expressão com a propriedade.

Para executar uma ação para cada ação `Failed` em um escopo que tenha um resultado e filtrar `@result()` a matriz de resultados até as ações com falha, você pode emparelhar uma expressão com uma ação [**Filter Array**](logic-apps-perform-data-operations.md#filter-array-action) e um [**Para cada**](../logic-apps/logic-apps-control-flow-loops.md) loop. Você pode pegar a matriz de resultados filtrada `For_each` e executar uma ação para cada falha usando o loop.

Aqui está um exemplo, seguido por uma explicação detalhada, que envia uma solicitação HTTP POST com o corpo da resposta para quaisquer ações que falharam no escopo "My_Scope":

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": "@result('My_Scope')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "My_Scope": [
         "Failed"
      ]
    }
},
"For_each": {
   "type": "foreach",
   "actions": {
      "Log_exception": {
         "type": "Http",
         "inputs": {
            "method": "POST",
            "body": "@item()['outputs']['body']",
            "headers": {
               "x-failed-action-name": "@item()['name']",
               "x-failed-tracking-id": "@item()['clientTrackingId']"
            },
            "uri": "http://requestb.in/"
         },
         "runAfter": {}
      }
   },
   "foreach": "@body('Filter_array')",
   "runAfter": {
      "Filter_array": [
         "Succeeded"
      ]
   }
}
```

Aqui, está um passo a passo detalhado que descreve o que acontece nesse exemplo:

1. Para obter o resultado de todas as ações dentro de "My_Scope", a ação **Filter Array** usa essa expressão de filtro: `@result('My_Scope')`

1. A condição para **Filter Array** é qualquer `@result()` item que tenha um status igual a `Failed`. Essa condição filtra a matriz que tem todos os resultados de ação "My_Scope" para uma matriz com apenas os resultados de ação com falha.

1. Execute `For_each` uma ação de loop nas saídas de *matriz filtradas.* Esta etapa executa uma ação para cada resultado de ação com falha que foi filtrado anteriormente.

   Se uma única ação no escopo falhar, as ações no loop serão `For_each` executadas apenas uma vez. Várias ações com falha causam uma ação por falha.

1. Envie um POST `For_each` HTTP no corpo de `@item()['outputs']['body']` resposta do item, que é a expressão.

   A forma do item `@result()` é a mesma que a forma `@actions()` e pode ser analisada da mesma maneira.

1. Inclua dois cabeçalhos personalizados com o nome da ação com falha (`@item()['name']`) e o ID de acompanhamento do cliente de execução com falha (`@item()['clientTrackingId']`).

Para referência, aqui está um exemplo de um único item `@result()`, mostrando as propriedades `name`, `body` e `clientTrackingId` que são analisadas no exemplo anterior. Fora `For_each` de `@result()` uma ação, retorna uma matriz desses objetos.

```json
{
   "name": "Example_Action_That_Failed",
   "inputs": {
      "uri": "https://myfailedaction.azurewebsites.net",
      "method": "POST"
   },
   "outputs": {
      "statusCode": 404,
      "headers": {
         "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
         "Server": "Microsoft-IIS/8.0",
         "X-Powered-By": "ASP.NET",
         "Content-Length": "68",
         "Content-Type": "application/json"
      },
      "body": {
         "code": "ResourceNotFound",
         "message": "/docs/folder-name/resource-name does not exist"
      }
   },
   "startTime": "2016-08-11T03:18:19.7755341Z",
   "endTime": "2016-08-11T03:18:20.2598835Z",
   "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
   "clientTrackingId": "08587307213861835591296330354",
   "code": "NotFound",
   "status": "Failed"
}
```

Para executar diferentes padrões de tratamento de exceções, você pode usar as expressões descritas anteriormente neste artigo. Você pode optar por executar uma única ação de manipulação de exceção fora `For_each` do escopo que aceita toda a matriz filtrada de falhas e remover a ação. Você também pode incluir outras propriedades úteis da `\@result()` resposta como descrito anteriormente.

## <a name="set-up-azure-monitor-logs"></a>Configurar os logs do Azure Monitor

Os padrões anteriores são uma ótima maneira de identificar erros e exceções dentro de uma execução, mas você também pode identificar e responder a erros independentemente da execução em si. [O Azure Monitor](../azure-monitor/overview.md) fornece uma maneira simples de enviar todos os eventos do fluxo de trabalho, incluindo todos os status de execução e ação, para um [espaço de trabalho log analytics,](../azure-monitor/platform/data-platform-logs.md) [conta de armazenamento Azure](../storage/blobs/storage-blobs-overview.md)ou [Hubs de Eventos Azure.](../event-hubs/event-hubs-about.md)

Para avaliar o status de execução, você pode monitorar os logs e as métricas ou publicá-los em qualquer ferramenta de monitoramento que preferir. Uma opção possível é transmitir todos os eventos através dos Hubs de Eventos para o [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). No Stream Analytics é possível gravar consultas dinâmicas com base em quaisquer anomalias, médias ou falhas dos logs de diagnóstico. Você pode usar o Stream Analytics para enviar informações a outras fontes de dados, como filas, tópicos, SQL, Azure Cosmos DB ou Power BI.

## <a name="next-steps"></a>Próximas etapas

* [Veja como um cliente compila o tratamento de erros com Aplicativos Lógicos do Azure](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Encontre mais exemplos e cenários de Logic Apps](../logic-apps/logic-apps-examples-and-scenarios.md)
