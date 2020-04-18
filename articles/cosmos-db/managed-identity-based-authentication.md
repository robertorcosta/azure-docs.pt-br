---
title: Como usar uma identidade gerenciada atribuída ao sistema para acessar dados do Azure Cosmos DB
description: Saiba como configurar uma identidade gerenciada atribuída ao sistema Azure Active Directory (Azure AD) para acessar chaves do Azure Cosmos DB.
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 8136ad7a1fe29bc3394e959c10aafc52988c0a23
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641193"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Use identidades gerenciadas atribuídas ao sistema para acessar dados do Azure Cosmos DB

Neste artigo, você criará uma solução *agnóstica de rotação robusta e chave* para acessar as chaves do Azure Cosmos DB usando [identidades gerenciadas](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). O exemplo neste artigo usa funções do Azure, mas você pode usar qualquer serviço que suporte identidades gerenciadas. 

Você aprenderá como criar um aplicativo de função que possa acessar os dados do Azure Cosmos DB sem precisar copiar nenhuma tecla DB do Azure Cosmos. O aplicativo de função acordará a cada minuto e registrará a temperatura atual de um aquário. Para saber como configurar um aplicativo de função acionado pelo temporizador, consulte o [Criar uma função no Azure que é acionada por um artigo do temporizador.](../azure-functions/functions-create-scheduled-function.md)

Para simplificar o cenário, uma configuração [Time To Live](./time-to-live.md) já está configurada para limpar documentos de temperatura mais antigos. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Atribuir uma identidade gerenciada atribuída ao sistema a um aplicativo de função

Nesta etapa, você atribuirá uma identidade gerenciada atribuída ao sistema ao seu aplicativo de função.

1. No [portal Azure,](https://portal.azure.com/)abra o painel **função Azure** e vá para o seu aplicativo de função. 

1. Abrir a **plataforma apresenta a** > guia**Identidade:** 

   ![Captura de tela mostrando recursos da plataforma e opções de identidade para o aplicativo de função.](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. Na guia **Identidade,** **gire O** **status** da identidade do sistema e selecione **Salvar**. O painel **identidade** deve olhar da seguinte forma:  

   ![Captura de tela mostrando o status da identidade do sistema definido como On.](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-access-to-your-azure-cosmos-account"></a>Conceda acesso à sua conta do Azure Cosmos

Nesta etapa, você atribuirá uma função à identidade gerenciada atribuída ao sistema do aplicativo. O Azure Cosmos DB tem várias funções incorporadas que você pode atribuir à identidade gerenciada. Para esta solução, você usará as duas seguintes funções:

|Função interna  |Descrição  |
|---------|---------|
|[Colaborador de Conta do DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Pode gerenciar contas do Azure Cosmos DB. Permite a recuperação de chaves de leitura/gravação. |
|[Leitor de contas Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Pode ler dados de contas do Azure Cosmos DB. Permite a recuperação de chaves de leitura. |

> [!IMPORTANT]
> O suporte ao controle de acesso baseado em função no Azure Cosmos DB aplica-se apenas às operações de plano de controle. As operações de plano de dados são protegidas através de chaves mestras ou tokens de recursos. Para saber mais, consulte o [artigo Do Secure access to data.](secure-access-to-data.md)

> [!TIP] 
> Quando você atribuir funções, atribua apenas o acesso necessário. Se o seu serviço exigir apenas a leitura de dados, atribua a função **Cosmos DB Account Reader** à identidade gerenciada. Para obter mais informações sobre a importância do acesso menos privilegiado, consulte o artigo [De menor exposição de contas privilegiadas.](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)

Neste cenário, o aplicativo de função lerá a temperatura do aquário e, em seguida, escreverá de volta esses dados para um contêiner no Azure Cosmos DB. Como o aplicativo de função deve gravar os dados, você precisará atribuir a função **Contribuinte de Conta documentDB.** 

1. Faça login no portal Azure e vá para sua conta azure Cosmos DB. Abra o painel **de controle de acesso (IAM)** e, em seguida, a guia **Atribuições de função:**

   ![Captura de tela mostrando o painel de controle de acesso e a guia Atribuições de Função.](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. Selecione **+ Adicione** > **atribuição de função adicionar**.

1. O **painel 'Adicionar atribuição'** é aberto à direita:

   ![Captura de tela mostrando o painel de atribuição adicionar função.](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **Função**: Selecionar **contribuinte de conta documentdb**
   * **Atribuir acesso a:** Na subseção **de identidade gerenciada atribuída pelo sistema Select,** selecione **Function App**.
   * **Selecione**: O painel será preenchido com todos os aplicativos de função em sua assinatura que possuem uma **identidade gerenciada do sistema**. Neste caso, selecione o aplicativo de função **FishTankTemperatureService:** 

      ![Captura de tela mostrando o painel de atribuição Adicionar atribuição preenchido com exemplos.](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. Depois de selecionar seu aplicativo de função, selecione **Salvar**.

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Acesse programáticamente as chaves azure Cosmos DB

Agora temos um aplicativo de função que tem uma identidade gerenciada atribuída ao sistema com a função **Contribuinte de Conta DocumentDB** nas permissões do Azure Cosmos DB. O código do aplicativo de função a seguir obterá as teclas Azure Cosmos DB, criará um objeto CosmosClient, obterá a temperatura do aquário e, em seguida, salvará isso no Azure Cosmos DB.

Esta amostra usa a [API List Keys](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys) para acessar as chaves da conta do Azure Cosmos DB.

> [!IMPORTANT] 
> Se você quiser atribuir a função [Cosmos DB Account Reader,](#grant-access-to-your-azure-cosmos-account) você precisará usar a [API 'Somente chaves de leitura de lista'.](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys) Isso preencherá apenas as chaves somente de leitura.

A API Chaves `DatabaseAccountListKeysResult` de lista retorna o objeto. Este tipo não é definido nas bibliotecas C#. O código a seguir mostra a implementação desta classe:  

```csharp 
namespace Monitor 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

O exemplo também usa um documento simples chamado "TemperatureRecord", que é definido da seguinte forma:

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

Você usará a biblioteca [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) para obter o token de identidade gerenciado atribuído pelo sistema. Para aprender outras maneiras de obter o `Microsoft.Azure.Service.AppAuthentication` token e obter mais informações sobre a biblioteca, consulte o artigo [de autenticação serviço-serviço.](../key-vault/general/service-to-service-authentication.md)


```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class FishTankTemperatureService
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("FishTankTemperatureService")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // Authenticate to the Azure Resource Manager to get the Service Managed token.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // Setup the List Keys API to get the Azure Cosmos DB keys.
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // Setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // Fake the temperature sensor for this demo.
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

Agora você está pronto para [implantar seu aplicativo de função](../azure-functions/functions-create-first-function-vs-code.md).

## <a name="next-steps"></a>Próximas etapas

* [Autenticação baseada em certificados com o Azure Cosmos DB e o Azure Active Directory](certificate-based-authentication.md)
* [Proteja as chaves db do Azure Cosmos usando o Cofre chave Do Azure](access-secrets-from-keyvault.md)
* [Linha de base de segurança para Azure Cosmos DB](security-baseline.md)
