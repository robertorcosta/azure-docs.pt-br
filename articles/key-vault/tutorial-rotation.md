---
title: Tutorial de rotação de usuário único/senha
description: Use este tutorial para automatizar a rotação de usuário único/senha
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 890932f7c0e46a2c9c0b6e1cf1461e4d7d25b409
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79223353"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-single-userpassword-authentication"></a>Automatizar a rotação de um segredo para recursos com autenticação de usuário único/senha

Embora a melhor maneira de se autenticar nos serviços do Azure seja usando uma [identidade gerenciada](managed-identity.md), há alguns cenários em que essa não é uma opção. Nesses casos, são usadas chaves de acesso ou segredos. A rotação de chaves de acesso ou segredos deve ser realizada periodicamente.

Este tutorial demonstra como automatizar a rotação periódica de segredos para bancos de dados e serviços com autenticação por usuário único/senha. Especificamente, esse cenário realiza a rotação das senhas do SQL Server armazenadas no cofre de chaves usando uma função disparada pela notificação da Grade de Eventos:

![Diagrama de rotação](./media/rotate1.png)

1. Trinta dias antes da data de validade de um segredo, o Key Vault publica o evento de "expiração próxima" na Grade de Eventos.
1. A Grade de Eventos verifica as assinaturas de evento e, usando http post, chama o ponto de extremidade do Aplicativo de Funções assinado para esse evento.
1. O Aplicativo de Funções recebe as informações secretas, gera uma nova senha aleatória e cria uma nova versão para o segredo com uma nova senha no Key Vault.
1. O Aplicativo de Funções atualiza o SQL com uma nova senha.

> [!NOTE]
> Poderá haver um atraso entre as etapas 3 e 4 e, durante esse tempo, o segredo no Key Vault não será válido para autenticação no SQL. Em caso de falha em qualquer uma das etapas, a Grade de Eventos realiza novas tentativas por duas horas.

## <a name="setup"></a>Instalação

## <a name="create-a-key-vault-and-sql-server"></a>Criar um cofre de chaves e um SQL Server

Antes de começar, devemos criar um Key Vault, criar um SQL Server e um banco de dados e armazenar a senha do administrador do SQL Server no Key Vault.

Este tutorial usa um modelo do Azure Resource Manager criado previamente para criar componentes. Você pode encontrar o código inteiro aqui: [Exemplo de modelo básico de rotação secreta](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Clique no link de implantação de modelo do Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Para "Grupo de Recursos", selecione "Criar" e dê a ele o nome "simplerotation".
1. Selecione "Comprar".

    ![Criar novo grupo de recursos](./media/rotate2.png)

Depois de concluir essas etapas, você terá um cofre de chaves, um SQL Server e um banco de dados SQL. Você pode verificar isso em um terminal da CLI do Azure executando o seguinte:

```azurecli
az resource list -o table
```

Os resultados serão algo parecido com este exemplo:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
```

## <a name="create-function-app"></a>Criar aplicativo de funções

Crie um aplicativo de funções com uma identidade gerenciada pelo sistema, bem como os componentes adicionais necessários: 

O aplicativo de funções requer os componentes e as configurações abaixo:
- Plano do Serviço de Aplicativo
- Conta de Armazenamento
- Política de acesso para acessar segredos no Key Vault usando Aplicativo de Funções de Identidade Gerenciada

1. Clique no link de implantação de modelo do Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Para "Grupo de recursos", selecione "simplerotation".
1. Selecione "Comprar".

   ![Tela de compra](./media/rotate3.png)

Depois de concluir as etapas acima, você terá uma conta de armazenamento, um farm de servidores e um aplicativo de funções.  Você pode verificar isso em um terminal da CLI do Azure executando o seguinte:

```azurecli
az resource list -o table
```

Os resultados serão algo parecido com este exemplo:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
simplerotationstrg            simplerotation             eastus      Microsoft.Storage/storageAccounts
simplerotation-plan           simplerotation             eastus      Microsoft.Web/serverFarms
simplerotation-fn             simplerotation             eastus      Microsoft.Web/sites
```

Para obter informações sobre como criar um aplicativo de funções e usar identidade gerenciada para acessar o Key Vault, confira [Criar um aplicativo de funções no portal do Azure](../azure-functions/functions-create-function-app-portal.md) e [Fornecer autenticação de Key Vault com uma identidade gerenciada](managed-identity.md)

### <a name="rotation-function-and-deployment"></a>Função de rotação e implantação
A função está usando evento como gatilho e executa a rotação de um segredo atualizando o Key Vault e o banco de dados SQL.

#### <a name="function-event-trigger-handler"></a>Manipulador do gatilho de evento da função

A função abaixo lê os dados de evento e executa a lógica de rotação

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
Esse método de rotação lê as informações do banco de dados do segredo, cria uma outra versão do segredo e atualiza o banco de dados com um novo segredo.

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve Current Secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve Secret Info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check db connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update db password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
Você pode encontrar o código inteiro aqui: https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function

#### <a name="function-deployment"></a>Implantação da função

1. Baixar arquivo zip do aplicativo de funções: https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip

1. Carregue o arquivo simplerotationsample-fn.zip para o Azure Cloud Shell.
 
1. Use o comando da CLI abaixo para implantar o arquivo zip no aplicativo de funções:

```azurecli
az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
```
![Tela de compra](./media/rotate4.png)

Após a implantação, você deverá ver duas funções em simplerotation-fn:

![Azure Cloud Shell](./media/rotate5.png)

### <a name="add-event-subscription-for-secretnearexpiry-event"></a>Adicionar a assinatura de evento para o evento "SecretNearExpiry"

Copie a chave eventgrid_extension do aplicativo de funções.

![Azure Cloud Shell](./media/rotate6.png)

![Testar e verificar](./media/rotate7.png)

Use a chave de extensão eventgrid copiada e a sua ID de assinatura no comando abaixo para criar uma assinatura de Grade de Eventos para eventos SecretNearExpiry.

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

### <a name="add-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault
Defina sua política de acesso para dar a permissão "gerenciar segredos" a usuários.

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Agora, crie um segredo com marcas que contenham a ID de usuário e a fonte de dados do banco de dados SQL, com a data de validade definida para amanhã.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

A criação de um segredo com uma data de vencimento curta publicará imediatamente um evento SecretNearExpiry, que, por sua vez, vai disparar a função para realizar a rotação do segredo.

### <a name="test-and-verify"></a>Testar e verificar
Após alguns minutos, a rotação do segredo sqluser deverá ser realizada automaticamente.

Para realizar a verificação de rotação do segredo, vá para Key Vault > Segredos

![Testar e verificar](./media/rotate8.png)

Abra o segredo "sqluser" e veja a versão original e a obtida após a rotação

![Testar e verificar](./media/rotate9.png)

## <a name="create-web-app"></a>Criar um aplicativo Web

Para verificar as credenciais do SQL, crie um aplicativo Web. Esse aplicativo Web obterá o segredo do cofre de chaves, extrairá as informações e as credenciais do banco de dados SQL do segredo e testará a conexão com o SQL.

O aplicativo Web requer os componentes e as configurações abaixo:
- Aplicativo Web com identidade gerenciada pelo sistema
- Política de acesso para acessar segredos no Key Vault usando aplicativo Web de identidade gerenciada

1. Clique no link de implantação de modelo do Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Selecione o grupo de recursos **simplerotation**
1. Clique em Comprar

### <a name="deploy-web-app"></a>Implantar aplicativo Web

Você pode encontrar o código-fonte do aplicativo Web em https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp. Para implantar o aplicativo Web, faça o seguinte:

1. Baixar o arquivo zip do aplicativo de funções de https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip
1. Carregar o arquivo `simplerotationsample-app.zip` para o Azure Cloud Shell.
1. Use este comando da CLI do Azure para implantar o arquivo zip no aplicativo de funções:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

#### <a name="open-web-application"></a>Abrir o aplicativo Web

Acesse o aplicativo implantado e clique em "URL":
 
![Testar e verificar](./media/rotate10.png)

O valor do segredo gerado deve ser mostrado com a opção Banco de Dados Conectado definida como true.

## <a name="learn-more"></a>Saiba mais:

- Visão geral: [Monitoramento do Key Vault com a Grade de Eventos do Azure (versão prévia)](event-grid-overview.md)
- Como fazer: [Receber emails quando o status do cofre de chaves secreto é alterado](event-grid-logicapps.md)
- [Esquema de eventos da Grade de Eventos do Azure para o Azure Key Vault (versão prévia)](../event-grid/event-schema-key-vault.md)
