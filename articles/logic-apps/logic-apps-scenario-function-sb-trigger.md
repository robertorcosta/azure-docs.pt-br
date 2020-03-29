---
title: Chamar aplicativos lógicos com o Azure Functions
description: Crie funções do Azure que chamam ou acionam aplicativos lógicos ouvindo o Ônibus de Serviço do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jehollan, klam, logicappspm
ms.topic: article
ms.date: 11/08/2019
ms.openlocfilehash: afd2735bae2a79ad942c347219019ef200b61070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428717"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Ligue ou acione aplicativos lógicos usando funções do Azure e ônibus de serviço do Azure

Você pode usar [funções do Azure](../azure-functions/functions-overview.md) para ativar um aplicativo lógico quando precisar implantar um ouvinte ou tarefa de longa duração. Por exemplo, você pode criar uma função Azure que escuta uma fila [de ônibus de serviço do Azure](../service-bus-messaging/service-bus-messaging-overview.md) e imediatamente dispara um aplicativo lógico como um gatilho de pressão.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um espaço de nome do Ônibus de Serviço Azure. Se você não tiver um namespace, [crie seu namespace primeiro](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Um aplicativo de função Azure, que é um contêiner para funções do Azure. Se você não tiver um aplicativo de função, [crie seu aplicativo de função primeiro](../azure-functions/functions-create-first-azure-function.md)e certifique-se de selecionar .NET como a pilha de tempo de execução.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Criar aplicativo lógico

Para este cenário, você tem uma função executando cada aplicativo lógico que você deseja acionar. Primeiro, crie um aplicativo lógico que comece com um gatilho de solicitação HTTP. A função chama esse ponto de extremidade sempre que uma mensagem da fila é recebida.

1. Entre no [portal do Azure](https://portal.azure.com) e crie um aplicativo lógico em branco.

   Se ainda não estiver familiarizado com aplicativos lógicos, veja o [Guia de Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Na caixa de pesquisa, insira `http request`. Na lista de gatilhos, selecione **o gatilho Quando uma solicitação HTTP for recebida.**

   ![Selecionar gatilho](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Com o gatilho Solicitar, você pode inserir opcionalmente um esquema JSON para usar com a mensagem de fila. Os esquemas JSON ajudam o Logic App Designer a entender a estrutura dos dados de entrada e tornar as saídas mais fáceis para você usar em seu fluxo de trabalho.

1. Para especificar um esquema, insira o esquema na caixa **Esquema JSON do Corpo da Solicitação**, por exemplo:

   ![Especificar o esquema JSON](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Se você não tem um esquema, mas tem um conteúdo de exemplo no formato JSON, você pode gerar um esquema com base nesse conteúdo.

   1. No gatilho de Solicitação, selecione **Usar o conteúdo de amostra para gerar o esquema**.

   1. Em **Enter ou cole uma amostra de carga JSON,** digite sua carga de amostra e, em seguida, selecione **Feito**.

      ![Inserir o conteúdo de exemplo](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Este conteúdo de exemplo gera o seguinte esquema que aparece no gatilho:

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. Adicione quaisquer outras ações que você deseja executar depois de receber a mensagem de fila.

   Por exemplo, você pode enviar um email com o conector do Office 365 Outlook.

1. Salve seu aplicativo lógico, o qual gera a URL de retorno de chamada para o gatilho neste aplicativo lógico. Mais tarde, você usa esta URL de retorno de chamada no código para o gatilho azure Service Bus Queue.

   A URL de retorno de chamada aparece na propriedade **HTTP POST URL.**

   ![URL de retorno de chamada gerada para o gatilho](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Criar função do Azure

Em seguida, crie a função que atuará como o gatilho e escutará a fila.

1. No portal do Azure, abra e expanda seu aplicativo de funções, se ainda não estiver aberto. 

1. No nome do aplicativo de funções, expanda **Funções**. No painel **Funções,** selecione **Nova função**.

   ![Expanda "Funções" e selecione "Nova função"](./media/logic-apps-scenario-function-sb-trigger/add-new-function-to-function-app.png)

1. Selecione este modelo com base em se você criou um novo aplicativo de função onde você selecionou .NET como a pilha de tempo de execução ou você está usando um aplicativo de função existente.

   * Para novos aplicativos de função, selecione este modelo: **Service Bus Queue trigger**

     ![Selecione o modelo para o novo aplicativo de função](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Para um aplicativo de função existente, selecione este modelo: **Service Bus Queue trigger - C#**

     ![Selecione o modelo para o aplicativo de função existente](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. No painel de gatilho do **Azure Service Bus Queue,** forneça um nome para o seu gatilho e configure a `OnMessageReceive()` **conexão Service Bus** para a fila, que usa o ouvinte SDK do Ônibus de Serviço Azure e selecione **Criar**.

1. Escreva uma função básica para chamar o ponto final do aplicativo lógico criado anteriormente usando a mensagem de fila como um gatilho. Antes de escrever sua função, revise estas considerações:

   * Este exemplo usa o tipo de conteúdo de mensagem `application/json`, mas você pode alterar isso conforme o necessário.
   
   * Devido a possíveis funções simultâneas em execução, volumes altos ou cargas pesadas, `using` evite instanciar a [classe HTTPClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) com a declaração e criar diretamente instâncias HTTPClient por solicitação. Para obter mais informações, consulte [Use HttpClientFactory para implementar solicitações HTTP resilientes](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#issues-with-the-original-httpclient-class-available-in-net-core).
   
   * Se possível, reutilize a instância dos clientes HTTP. Para obter mais informações, consulte [Gerenciar conexões em Funções Azure](../azure-functions/manage-connections.md).

   Este exemplo [ `Task.Run` ](https://docs.microsoft.com/dotnet/api/system.threading.tasks.task.run) usa o método no modo [assíncrono.](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/async) Para obter mais informações, consulte [programação assíncrona com assincronização e aguarde](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/).

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Can also fetch from App Settings or environment variable
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible: https://docs.microsoft.com/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();

   public static async Task Run(string myQueueItem, TraceWriter log) 
   {
      log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
      var response = await httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")); 
   }
   ```

1. Para testar a função, adicione uma mensagem da fila usando uma ferramenta como o [Gerenciador do Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer).

   O aplicativo lógico dispara imediatamente após a função receber a mensagem.

## <a name="next-steps"></a>Próximas etapas

* [Fluxos de trabalho de chamada, gatilho ou ninho usando pontos finais HTTP](../logic-apps/logic-apps-http-endpoint.md)
