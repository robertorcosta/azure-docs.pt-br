---
title: Como usar uma identidade gerenciada atribuída ao sistema para acessar dados do Azure Cosmos DB
description: Saiba como configurar uma identidade gerenciada atribuída ao sistema Azure AD para acessar chaves do Azure Cosmos DB. msi, identidade de serviço gerenciado, aad, diretório ativo azul, identidade
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 37e5cb817db2c54a07ab04c4dcc31b1976fdf03d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450047"
---
# <a name="how-to-use-a-system-assigned-managed-identity-to-access-azure-cosmos-db-data"></a>Como usar uma identidade gerenciada atribuída ao sistema para acessar dados do Azure Cosmos DB

Neste artigo você irá configurar uma solução **agnóstica de rotação robusta e chave** para acessar as chaves do Azure Cosmos DB aproveitando [identidades gerenciadas](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). O exemplo neste artigo usa uma função Azure. No entanto, você pode obter essa solução usando qualquer serviço que suporte identidades gerenciadas. 

Você aprenderá como criar uma função Azure que pode acessar o Azure Cosmos DB sem precisar copiar nenhuma tecla DB do Azure Cosmos. A função acordará a cada minuto e registrará a temperatura atual de um aquário. Para aprender como configurar um temporizador acionado função Azure ver a [Criar uma função no Azure que é acionada por um artigo do temporizador.](../azure-functions/functions-create-scheduled-function.md)

Para simplificar o cenário, a limpeza de documentos de temperatura mais antigos é tratada por uma configuração já configurada [tempo para viver.](./time-to-live.md) 

## <a name="assign-a-system-assigned-managed-identity-to-an-azure-function"></a>Atribuir uma identidade gerenciada atribuída ao sistema a uma função Azure

Nesta etapa, você atribuirá uma identidade gerenciada atribuída ao sistema à sua função Azure.

1. No [portal Azure,](https://portal.azure.com/)abra o painel **função Azure** e navegue até o seu aplicativo de função. 

1. Abrir a **plataforma apresenta a** > guia**Identidade:** 

   ![Guia de Identidade](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. Na guia **Identidade,** gire **o** status de Identidade do **sistema.** Certifique-se de selecionar **Salvar**e confirmar se deseja ativar a identidade do sistema. No final, o painel identidade do **sistema** deve olhar o seguinte:  

   ![Identidade do sistema ligada](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-the-managed-identity-access-to-your-azure-cosmos-account"></a>Conceda o acesso de identidade gerenciado à sua conta do Azure Cosmos

Nesta etapa, você atribuirá uma função à identidade gerenciada atribuída ao sistema da Função Azure. O Azure Cosmos DB tem várias funções incorporadas que você pode atribuir à identidade gerenciada. Para esta solução, você usará as duas funções a seguir:

|Função interna  |Descrição  |
|---------|---------|
|[Colaborador de Conta do DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Pode gerenciar contas do Azure Cosmos DB. Permite a recuperação de chaves de leitura/gravação. |
|[Leitor de contas Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Pode ler dados de contas do Azure Cosmos DB. Permite a recuperação de chaves de leitura. |

> [!IMPORTANT]
> O suporte rBAC no Azure Cosmos DB é aplicável apenas para controlar operações de aviões. As operações de plano de dados são seguras usando chaves mestres ou tokens de recursos. Para saber mais, consulte o [artigo Do Secure access to data.](secure-access-to-data.md)

> [!TIP] 
> Ao atribuir funções, apenas atribua o acesso necessário. Se o seu serviço exigir apenas dados de leitura, então atribua a identidade gerenciada à função **Cosmos DB Account Reader.** Para obter mais informações sobre a importância do acesso ao menor privilégio, consulte a menor exposição do artigo [de contas privilegiadas.](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)

Para o seu cenário, você lerá a temperatura e, em seguida, escreverá de volta esses dados para um contêiner no Azure Cosmos DB. Como você tem que escrever os dados, você usará a função **Contribuinte de Conta documentDB.** 

1. Entre no portal do Azure e navegue até a sua conta Microsoft Azure Cosmos DB. Abra o **painel de gerenciamento de acesso (IAM)** e, em seguida, a guia **Atribuições de função:**

   ![IAM Pane](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. Selecione o botão **+ Adicionar** e, em seguida, adicione **a atribuição de função**.

1. O **painel Adicionar atribuição de** função é aberto à direita:

   ![Adicionar função](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **Função** - Selecionar **contribuinte de conta do DocumentDB**
   * **Atribuir acesso a** - Na subseção **de identidade gerenciada atribuída pelo Sistema** Select, selecione Function **App**.
   * **Selecione** - O painel será preenchido com todos os aplicativos de função, em sua assinatura, que possuem uma **Identidade gerenciada do sistema**. No nosso caso, seleciono o aplicativo de função **SummaryService:** 

      ![Selecionar atribuição](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. Depois que a identidade do aplicativo de função for selecionada, clique **em Salvar**.

## <a name="programmatically-access-the-azure-cosmos-db-keys-from-the-azure-function"></a>Acesse programáticamente as teclas Azure Cosmos DB da função Azure

Agora temos um aplicativo de função que tem uma identidade gerenciada atribuída ao sistema. Essa identidade é dada a função **de Contribuinte de Conta documentDB** nas permissões do Azure Cosmos DB. O código do aplicativo de função a seguir obterá as teclas Azure Cosmos DB, criará um objeto CosmosClient, obterá a temperatura e salvará isso no Cosmos DB.

Esta amostra usa a [API List Keys](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys) para acessar as chaves da conta do Azure Cosmos DB.

> [!IMPORTANT] 
> Se você quiser atribuir a função [ **Cosmos DB Account Reader,** ](#grant-the-managed-identity-access-to-your-azure-cosmos-account) você precisará usar a [api ''Chaves de lista'](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys)somente leitura . Isso só preencherá as teclas somente de leitura.

A API Chaves `DatabaseAccountListKeysResult` de lista retorna o objeto. Este tipo não é definido nas bibliotecas C#. O código a seguir mostra a implementação desta classe:  

```csharp 
namespace SummarizationService 
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

Você usará a biblioteca [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) para obter o token de identidade gerenciado atribuído pelo sistema. Para saber outras maneiras de obter `Microsoft.Azure.Service.AppAuthentication` o token e mais informações sobre a biblioteca, consulte o artigo [De Autenticação serviço para serviço.](../key-vault/general/service-to-service-authentication.md)

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
    public static class TemperatureMonitor
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

        [FunctionName("TemperatureMonitor")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // In order to get the Service Managed token we need to authenticate to the Azure Resource Manager.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // To get the Azure Cosmos DB keys setup the List Keys API:
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the Result back as a DatabaseAccountListKeysResult.
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
            // fake the temperature sensor for this demo
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

Agora você está pronto para [implantar sua função Azure](../azure-functions/functions-create-first-function-vs-code.md).

## <a name="next-steps"></a>Próximas etapas

* [Autenticação baseada em certificados com O Zure Cosmos DB e Active Directory](certificate-based-authentication.md)
* [Proteger chaves do Azure Cosmos usando o Azure Key Vault](access-secrets-from-keyvault.md)
* [Linha de base de segurança para Azure Cosmos DB](security-baseline.md)
