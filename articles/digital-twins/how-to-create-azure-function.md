---
title: Configurar uma função do Azure para processar dados
titleSuffix: Azure Digital Twins
description: Veja como criar uma função do Azure que pode acessar e ser disparada por gêmeos digital.
author: baanders
ms.author: baanders
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 66f514f4c5d299ef11efda541f16f4ef2fe61aed
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930155"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>Conectar Azure Functions aplicativos para processamento de dados

Durante a visualização, a atualização do gêmeos digital com base em dados é tratada usando [**rotas de eventos**](concepts-route-events.md) por meio de recursos de computação, como [Azure Functions](../azure-functions/functions-overview.md). Uma função do Azure pode ser usada para atualizar uma atualização digital em resposta a:
* dados de telemetria do dispositivo provenientes do Hub IoT
* alteração de propriedade ou outros dados provenientes de outra troca digital no grafo de entrelaçamento

Este artigo orienta você pela criação de uma função do Azure para uso com o gêmeos digital do Azure. 

Aqui está uma visão geral das etapas que ela contém:

1. Criar um aplicativo Azure Functions no Visual Studio
2. Escrever uma função do Azure com um gatilho de [grade de eventos](../event-grid/overview.md)
3. Adicione o código de autenticação à função (para poder acessar o Azure digital gêmeos)
4. Publicar o aplicativo de funções no Azure
5. Configure o acesso de [segurança](concepts-security.md) . Para que o Azure function receba um token de acesso em tempo de execução para acessar o serviço, você precisará configurar Identidade de Serviço Gerenciada para o aplicativo de funções.

O restante deste artigo percorre as etapas de configuração do Azure Function, uma de cada vez.

## <a name="create-an-azure-functions-app-in-visual-studio"></a>Criar um aplicativo Azure Functions no Visual Studio

No Visual Studio 2019, selecione *arquivo > novo projeto*. Procure o modelo de *Azure Functions* , selecione-o e pressione "Avançar".

:::image type="content" source="media/how-to-create-azure-function/visual-studio-new-project.png" alt-text="Visual Studio: caixa de diálogo novo projeto":::

Especifique um nome para o aplicativo de funções e pressione "criar".

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-config.png" alt-text="Visual Studio: caixa de diálogo Configurar projeto":::

Selecione o *gatilho de grade de eventos* e pressione "criar".

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-trigger.png" alt-text="Visual Studio: caixa de diálogo gatilho do projeto de funções do Azure":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>Escrever uma função do Azure com um gatilho de grade de eventos

O código a seguir cria uma função curta do Azure que você pode usar para registrar eventos: 

```csharp
// Default URL for triggering Event Grid function in the local environment
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;

namespace FunctionSample
{
    public static class FooFunction    {
        [FunctionName("Foo")]
        public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Esta é sua função básica do Azure.

### <a name="run-and-debug-the-azure-function-app"></a>Executar e depurar o aplicativo de funções do Azure

Agora você pode compilar e executar a função. Embora o Azure Functions seja, em última análise, destinado a ser executado na nuvem, você também pode executar e depurar o Azure Functions localmente.

Para obter mais informações sobre isso, consulte [*depurar o gatilho de grade de eventos localmente*](../azure-functions/functions-debug-event-grid-trigger-local.md).

### <a name="add-the-azure-digital-twins-sdk-to-your-azure-function-app"></a>Adicionar o SDK do gêmeos digital do Azure ao seu aplicativo de funções do Azure

O aplicativo de funções interage com o gêmeos digital do Azure usando a [biblioteca de cliente do Microsoft Azure IOT digital para .net (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). Para usar o SDK, você precisará incluir os seguintes pacotes em seu projeto:
* `Azure.DigitalTwins.Core` (versão `1.0.0-preview.2` )
* `Azure.Identity`

Para que a configuração do pipeline do SDK do Azure seja configurada corretamente para Azure Functions, você também precisará de:
* `Azure.Net.Http`
* `Azure.Core`

Dependendo das ferramentas de sua escolha, você pode fazer isso com o Gerenciador de pacotes do Visual Studio ou a `dotnet` ferramenta de linha de comando. 

Adicione as seguintes instruções using ao seu Azure function.

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;
```

## <a name="add-authentication-code-to-the-azure-function"></a>Adicionar código de autenticação à função do Azure

Em seguida, adicione o código de autenticação que permitirá que a função acesse o Azure digital gêmeos.

Adicione variáveis à sua classe de função para estes valores: 
* A ID do aplicativo gêmeos digital do Azure
* A URL da instância de gêmeos digital do Azure. É uma boa prática ler a URL do serviço de uma variável de ambiente, em vez de codificá-la embutidamente na função.
* Uma variável estática para manter uma instância de HttpClient. O HttpClient é relativamente caro de criar e queremos evitar ter que fazer isso para cada invocação de função.

Além disso, adicione uma variável local dentro de sua função para manter a instância do cliente gêmeos digital do Azure para o projeto de função. *Não* torne essa uma variável estática dentro de sua classe.

Por fim, altere a assinatura da função para que seja assíncrona.

Após essas alterações, o código de função deve ser semelhante ao seguinte:

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Para que seu aplicativo de funções possa acessar o Azure digital gêmeos, ele precisa ter uma identidade gerenciada pelo sistema e ter permissões para acessar sua instância do gêmeos digital do Azure.

Use o comando a seguir para criar a identidade gerenciada pelo sistema. Anote o campo *principalId* na saída.

```azurecli
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Use o valor *PrincipalId* no comando a seguir para atribuir a identidade do aplicativo de funções à função de *proprietário do gêmeos digital do Azure (versão prévia)* para sua instância do gêmeos digital do Azure. Isso dará à permissão do aplicativo de funções na instância para executar atividades de plano de dados.

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```

Para obter mais informações sobre identidade gerenciada, consulte [*como usar identidades gerenciadas para serviço de aplicativo e Azure Functions*](../app-service/overview-managed-identity.md).

Por fim, você pode tornar a URL da sua instância de gêmeos digital do Azure acessível para sua função definindo uma variável de ambiente. Para obter mais informações sobre isso, consulte [*variáveis de ambiente*](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables).

> [!TIP]
> A URL da instância do gêmeos digital do Azure é feita adicionando *https://* ao início do *nome de host*da instância do Azure digital gêmeos. Para ver o nome do host, juntamente com todas as propriedades de sua instância, você pode executar `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-URL>"
```

## <a name="publish-the-azure-function-app"></a>Publicar o aplicativo de funções do Azure

Para publicar o aplicativo de funções no Azure, selecione o projeto de função (não a solução) em Gerenciador de Soluções e escolha *publicar ()*.

A seguinte guia será exibida:

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-1.png" alt-text="Visual Studio: caixa de diálogo Publicar função, página 1":::

Selecione ou crie um plano do serviço de aplicativo para usar com Azure Functions. Se não tiver certeza, comece a usar o plano de consumo padrão.

> [!IMPORTANT] 
> A publicação de uma função do Azure incorrerá em encargos adicionais em sua assinatura, independentemente do Azure digital gêmeos.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-2.png" alt-text="Visual Studio: caixa de diálogo Publicar função, página 2":::

Na página a seguir, insira o nome desejado para o novo aplicativo de funções, um grupo de recursos e outros detalhes.

## <a name="set-up-security-access-for-the-azure-function-app"></a>Configurar o acesso de segurança para o aplicativo de funções do Azure

O esqueleto do Azure function dos exemplos anteriores requer que um token de portador seja passado para ele, a fim de ser capaz de autenticar com o gêmeos digital do Azure. Para garantir que esse token de portador seja passado, você precisará configurar [identidade de serviço gerenciada (MSI)](../active-directory/managed-identities-azure-resources/overview.md) para o aplicativo de funções. Isso só precisa ser feito uma vez para cada aplicativo de funções.

Para configurar isso, vá para a [portal do Azure](https://portal.azure.com/) e navegue até seu aplicativo de funções.

Na guia *recursos da plataforma* , selecione *identidade*:

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-1.png" alt-text="Portal do Azure: selecionando a identidade para uma função do Azure":::

Na página identidade, defina o *status* alternar para *ativado*. 

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-2.png" alt-text="Portal do Azure: Ativando o status de identidade":::

Observe também a **ID de objeto** mostrada nesta página, pois ela será usada na próxima seção.

### <a name="assign-access-roles"></a>Atribuir funções de acesso

Como o Azure digital gêmeos usa o controle de acesso baseado em função para gerenciar o acesso (consulte [*conceitos: segurança para soluções de gêmeos digitais do Azure*](concepts-security.md) para obter mais informações sobre isso), você também precisa adicionar uma função para cada aplicativo de funções que deseja permitir para acessar o gêmeos digital do Azure.

Para atribuir uma função, você precisa da **ID de recurso** da instância do gêmeos digital do Azure que você criou. Se você não o registrou antes de criar sua instância, poderá recuperá-la usando este comando:

```bash
az dt show --name <your-instance-name> -g <your-resource-group-name>
```
A ID do recurso fará parte da saída, como uma cadeia de caracteres longa chamada "ID", que começa com as letras "/subscriptions/...".

Use a ID do recurso junto com a ID de objeto da função do Azure de antes no comando a seguir:

```azurecli
az role assignment create --role "Azure Digital Twins Owner (Preview)" --assignee <object-ID> --scope <resource-ID>
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você seguiu as etapas para configurar uma função do Azure para uso com o gêmeos digital do Azure. Em seguida, você pode assinar sua função do Azure para a grade de eventos, para escutar em um ponto de extremidade. Esse ponto de extremidade pode ser:
* Um ponto de extremidade de grade de eventos anexado ao Azure digital gêmeos para processar mensagens provenientes do Azure digital gêmeos em si (como mensagens de alteração de propriedade, mensagens de telemetria geradas por [gêmeos digital](concepts-twins-graph.md) no grafo de entrelaçamento ou mensagens de ciclo de vida)
* Os tópicos do sistema IoT usados pelo Hub IoT para enviar telemetria e outros eventos de dispositivo
* Um ponto de extremidade de grade de eventos que recebe mensagens de outros serviços

Em seguida, consulte como criar em sua função básica do Azure para ingerir dados do Hub IoT no gêmeos digital do Azure:
* [*Como: telemetria de ingestão do Hub IoT*](how-to-ingest-iot-hub-data.md)