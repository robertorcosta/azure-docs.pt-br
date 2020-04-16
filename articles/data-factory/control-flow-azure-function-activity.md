---
title: Atividade de função do Azure na fábrica de dados do Azure
description: Saiba como usar a atividade de função do Azure para executar uma função do Azure em um pipeline do Data Factory
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: ee2e59e794cf34a8fd5043a56867a81c2537f1ae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415319"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Atividade de função do Azure no Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Saiba como usar a atividade do [Azure Functions](../azure-functions/functions-overview.md) para executar uma função do Azure em um pipeline do Data Factory. Para executar uma função do Azure, você precisará criar uma conexão de serviço vinculado e uma atividade que especifica a função do Azure que você planeja executar.

Para ver uma introdução de oito minutos e uma demonstração desse recurso, assista ao seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Serviço de função vinculado do Azure

O tipo de retorno da função do Azure deve ser um `JObject` válido. (Tenha em mente que [jArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *não* é um `JObject`.) Qualquer tipo de `JObject` retorno que não falhe e aumente o conteúdo de resposta do erro do usuário *não é um JObject válido*.

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| type   | A propriedade do tipo deve ser definida **como: AzureFunction** | sim |
| url do aplicativo de função | URL para o Aplicativo de Funções do Azure. O formato é `https://<accountname>.azurewebsites.net`. Essa URL é o valor na seção **URL** ao exibir o Aplicativo de funções no portal do Azure  | sim |
| teclas de função | Tecla de acesso para o Azure Function. Clique na seção **Gerenciar** seção para a função respectiva e copie-a na **Tecla de Função** ou na **Tecla Host**. Saiba mais aqui: [Azure Funções HTTP gatilhos e vinculações](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | sim |
|   |   |   |

## <a name="azure-function-activity"></a>Atividade do Azure Function

| **Propriedade**  | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| name  | Nome da atividade no pipeline  | String | sim |
| type  | O tipo de atividade é 'AzureFunctionActivity' | String | sim |
| serviço vinculado | O serviço de função do Azure vinculado para o Aplicativo de funções correspondente do Azure  | Referência de serviço vinculado | sim |
| nome da função  | Nome da função no Aplicativo de funções do Azure que essa atividade chama | String | sim |
| method  | Método da API REST para a chamada de função | Tipos suportados por cordas: "GET", "POST", "PUT"   | sim |
| cabeçalho  | Cabeçalhos que são enviados para a solicitação. Por exemplo, para definir o idioma e o tipo em uma solicitação: "cabeçalhos": { "Accept-Language": "en-us", "Content-Type": "application/json" } | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) | Não |
| body  | corpo que é enviado junto com a solicitação para o método de api de função  | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) ou objeto.   | Necessário para os métodos PUT/POST |
|   |   |   | |

Consulte o esquema da carga útil da solicitação na seção [De solicitação](control-flow-web-activity.md#request-payload-schema) de esquema de carga útil.

## <a name="routing-and-queries"></a>Roteamento e consultas

A atividade do Azure Function dá suporte a **roteamento**. Por exemplo, se a função Azure tiver o ponto final, `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`então o `functionName` uso na atividade de função do Azure é `<functionName>/<value>`. Você pode parametrizar esta função `functionName` para fornecer o desejado em tempo de execução.

A atividade do Azure Function também dá suporte a **consultas**.  Uma consulta deve ser incluída como `functionName`parte do . Por exemplo, quando o `HttpTriggerCSharp` nome da função é e `name=hello`a consulta que `functionName` você deseja incluir é, então você pode construir o na Atividade de função Azure como `HttpTriggerCSharp?name=hello`. Esta função pode ser parametrizada para que o valor possa ser determinado em tempo de execução.

## <a name="timeout-and-long-running-functions"></a>Tempo máximo e funções de longo prazo

O Azure funciona após 230 segundos, independentemente da `functionTimeout` configuração configurada nas configurações. Para obter mais informações, consulte [este artigo](../azure-functions/functions-versions.md#timeout). Para contornar esse comportamento, siga um padrão assíncrono ou use Funções Duráveis. O benefício das Funções Duráveis é que elas oferecem seu próprio mecanismo de rastreamento de estado, para que você não tenha que implementar o seu próprio.

Saiba mais sobre funções duráveis [neste artigo](../azure-functions/durable/durable-functions-overview.md). Você pode configurar uma atividade de função azure para chamar a função durável, que retornará uma resposta com um URI diferente, como [este exemplo](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery). Como `statusQueryGetUri` retorna o STATUS HTTP 202 enquanto a função está em execução, você pode sondar o status da função usando uma Atividade da Web. Basta configurar uma Atividade `url` web com `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`o campo definido para . Quando a função durável for concluída, a saída da função será a saída da Atividade da Web.


## <a name="sample"></a>Amostra

Você pode encontrar uma amostra de uma fábrica de dados que usa uma função Azure para extrair o conteúdo de um arquivo de piche [aqui](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as atividades no Data Factory no [Pipelines e atividades no Azure Data Factory](concepts-pipelines-activities.md).
