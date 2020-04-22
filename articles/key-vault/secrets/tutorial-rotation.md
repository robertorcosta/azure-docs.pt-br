---
title: Tutorial de rotação de usuário único/senha única
description: Use este tutorial para aprender a automatizar a rotação de um segredo para recursos que usam autenticação por usuário único/senha única.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 70eb2449c5c54750831c30ff7d5c948173a38594
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81426345"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-single-usersingle-password-authentication"></a>Automatizar a rotação de um segredo para recursos que usam a autenticação por usuário único/senha única

A melhor maneira de se autenticar nos serviços do Azure é usando uma [identidade gerenciada](../general/managed-identity.md), mas há alguns cenários em que essa não é uma opção. Nesses casos, são usadas chaves de acesso ou segredos. Você deve realizar periodicamente a rotação de chaves de acesso ou segredos.

Este tutorial mostra como automatizar a rotação periódica de segredos para bancos de dados e serviços que usam a autenticação por usuário único/senha única. Especificamente, este tutorial realiza a rotação das senhas do SQL Server armazenadas no Azure Key Vault usando uma função disparada pela notificação da Grade de Eventos do Azure:

![Diagrama de solução de rotação](../media/rotate1.png)

1. Trinta dias antes da data de validade de um segredo, o Key Vault publica o evento de "expiração próxima" na Grade de Eventos.
1. A Grade de Eventos verifica as assinaturas de eventos e usa HTTP POST para chamar o ponto de extremidade do aplicativo de funções assinado para o evento.
1. O aplicativo de funções recebe as informações secretas, gera uma nova senha aleatória e cria uma versão para o segredo com a nova senha no Key Vault.
1. O aplicativo de funções atualiza o SQL Server com uma nova senha.

> [!NOTE]
> Pode haver um atraso entre as etapas 3 e 4. Durante esse tempo, o segredo no Key Vault não poderá se autenticar no SQL Server. Em caso de uma falha em qualquer uma das etapas, a Grade de Eventos realiza novas tentativas por duas horas.

## <a name="create-a-key-vault-and-sql-server-instance"></a>Criar um cofre de chaves e uma instância do SQL Server

A primeira etapa é criar um cofre de chaves e uma instância e um banco de dados do SQL Server e armazenar a senha do administrador do SQL Server no Key Vault.

Este tutorial usa um modelo do Azure Resource Manager existente para criar componentes. Você pode encontrar o código aqui: [Exemplo de modelo básico de rotação secreta](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Selecione o link de implantação de modelo do Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Em **Grupo de recursos**, selecione **Criar**. Dê ao grupo o nome **simplerotation**.
1. Selecione **Comprar**.

    ![Criar um grupo de recursos](../media/rotate2.png)

Agora você terá um cofre de chaves, uma instância do SQL Server e um Banco de Dados SQL. Você pode verificar essa configuração na CLI do Azure executando o seguinte comando:

```azurecli
az resource list -o table
```

O resultado terá uma aparência semelhante à da seguinte saída:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation      eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation      eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

Em seguida, crie um aplicativo de funções com uma identidade gerenciada pelo sistema, além dos outros componentes necessários.

O aplicativo de funções requer estes componentes:
- Um plano do Serviço de Aplicativo do Azure
- Uma conta de armazenamento
- Uma política de acesso para acessar segredos no Key Vault usando a identidade gerenciada do aplicativo de funções

1. Selecione o link de implantação de modelo do Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Na lista **Grupo de recursos**, selecione **simplerotation**.
1. Selecione **Comprar**.

   ![Selecionar Comprar](../media/rotate3.png)

Depois de concluir as etapas anteriores, você terá uma conta de armazenamento, um farm de servidores e um aplicativo de funções. Você pode verificar essa configuração na CLI do Azure executando o seguinte comando:

```azurecli
az resource list -o table
```

O resultado terá uma aparência semelhante à da seguinte saída:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation       eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation       eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation       eastus      Microsoft.Sql/servers/databases
simplerotationstrg         simplerotation       eastus      Microsoft.Storage/storageAccounts
simplerotation-plan        simplerotation       eastus      Microsoft.Web/serverFarms
simplerotation-fn          simplerotation       eastus      Microsoft.Web/sites
```

Para obter informações sobre como criar um aplicativo de funções e usar a identidade gerenciada para acessar o Key Vault, confira [Criar um aplicativo de funções no portal do Azure](../../azure-functions/functions-create-function-app-portal.md) e [Fornecer autenticação de Key Vault com uma identidade gerenciada](../general/managed-identity.md).

### <a name="rotation-function"></a>Função de rotação
A função usa um evento para disparar a rotação de um segredo atualizando o Key Vault e o Banco de Dados SQL.

#### <a name="function-trigger-event"></a>Evento de gatilho de função

Essa função lê dados de eventos e executa a lógica de rotação:

```csharp
public static class SimpleRotationEventHandler
{
    [FunctionName("SimpleRotation")]
       public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
       {
            log.LogInformation("C# Event trigger function processed a request.");
            var secretName = eventGridEvent.Subject;
            var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
            var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
            log.LogInformation($"Key Vault Name: {keyVaultName}");
            log.LogInformation($"Secret Name: {secretName}");
            log.LogInformation($"Secret Version: {secretVersion}");

            SeretRotator.RotateSecret(log, secretName, secretVersion, keyVaultName);
        }
}
```

#### <a name="secret-rotation-logic"></a>Lógica de rotação do segredo
Esse método de rotação lê as informações do banco de dados do segredo, cria uma versão do segredo e atualiza o banco de dados com um novo segredo:

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve current secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve secret info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //Create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check DB connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update DB password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
É possível encontrar o código de exemplo [no GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function).

#### <a name="function-deployment"></a>Implantação da função

1. Baixe o arquivo zip do aplicativo de funções do [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip).

1. Carregue o arquivo simplerotationsample-fn.zip para o Azure Cloud Shell.

   ![Carregar o arquivo](../media/rotate4.png)
1. Use este comando da CLI do Azure para implantar o arquivo zip no aplicativo de funções:

   ```azurecli
   az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
   ```

Depois que a função for implantada, você deverá ver duas funções em simplerotation-fn:

![Funções SimpleRotation e SimpleRotationHttpTest](../media/rotate5.png)

## <a name="add-an-event-subscription-for-the-secretnearexpiry-event"></a>Adicionar uma assinatura de evento para o evento SecretNearExpiry

Copie a chave `eventgrid_extension` do aplicativo de funções:

   ![Selecionar as configurações do aplicativo de funções](../media/rotate6.png)

   ![Chave eventgrid_extension](../media/rotate7.png)

Use a chave `eventgrid_extension` copiada e a ID da assinatura no seguinte comando para criar uma assinatura da Grade de Eventos para eventos `SecretNearExpiry`:

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

## <a name="add-the-secret-to-key-vault"></a>Adicionar o segredo ao Key Vault
Defina sua política de acesso para conceder as permissões para *gerenciar segredos* a usuários:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Crie um segredo com tags que contenham a fonte de dados do Banco de Dados SQL e a ID de usuário. Inclua uma data de validade definida para amanhã.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

A criação de um segredo com uma data de validade curta publicará imediatamente um evento `SecretNearExpiry`, que, por sua vez, disparará a função para realizar a rotação do segredo.

## <a name="test-and-verify"></a>Testar e verificar
Após alguns minutos, a rotação do segredo `sqluser` deverá ser realizada automaticamente.

Para verificar se a rotação do segredo foi realizada, acesse **Key Vault** > **Segredos**:

![Acessar Segredos](../media/rotate8.png)

Abra o segredo **sqluser** e veja as versões originais e com rotação:

![Abrir o segredo sqluser](../media/rotate9.png)

### <a name="create-a-web-app"></a>Criar um aplicativo Web

Para verificar as credenciais do SQL, crie um aplicativo Web. Esse aplicativo Web obterá o segredo do Key Vault, extrairá as informações e as credenciais do Banco de Dados SQL do segredo e testará a conexão com o SQL Server.

O aplicativo Web requer estes componentes:
- Um aplicativo Web com identidade gerenciada pelo sistema
- Uma política de acesso para acessar segredos no Key Vault usando a identidade gerenciada do aplicativo Web

1. Selecione o link de implantação de modelo do Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Selecione o grupo de recursos **simplerotation**.
1. Selecione **Comprar**.

### <a name="deploy-the-web-app"></a>Implantar o aplicativo Web

É possível encontrar o código-fonte do aplicativo Web no [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp).

Para implantar o aplicativo Web, conclua estas etapas:

1. Baixe o arquivo zip do aplicativo de funções do [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip).
1. Carregue o arquivo simplerotationsample-app.zip para o Azure Cloud Shell.
1. Use este comando da CLI do Azure para implantar o arquivo zip no aplicativo de funções:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

### <a name="open-the-web-app"></a>Abra o aplicativo Web

Acesse o aplicativo implantado e selecione a URL:
 
![Selecione a URL](../media/rotate10.png)

Você deve ver o Valor Secreto Gerado com um valor true Conectado do Banco de Dados.

## <a name="learn-more"></a>Saiba mais

- Visão geral: [Monitoramento do Key Vault com a Grade de Eventos do Azure (versão prévia)](../general/event-grid-overview.md)
- Como fazer: [Receber emails quando o status do cofre de chaves secreto é alterado](../general/event-grid-logicapps.md)
- [Esquema de eventos da Grade de Eventos do Azure para o Azure Key Vault (versão prévia)](../../event-grid/event-schema-key-vault.md)