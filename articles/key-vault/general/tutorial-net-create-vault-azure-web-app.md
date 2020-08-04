---
title: Tutorial – Usar o Azure Key Vault com um aplicativo Web do Azure no .NET | Microsoft Docs
description: Neste tutorial, você vai configurar um aplicativo do ASP.NET Core para ler um segredo no cofre de chaves.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.openlocfilehash: 4e236be298f92506e40a7f5197b2abeb065e7eed
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87013259"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-with-net"></a>Tutorial: Usar uma identidade gerenciada para conectar o Key Vault a um Aplicativo Web do Azure com o .NET

O Azure Key Vault fornece uma maneira de armazenar as credenciais e outros segredos com segurança, mas seu código precisa ser autenticado no Key Vault para recuperá-los. A [Visão geral das identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md) ajuda a solucionar esse problema fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter que todas as credenciais no seu código.

Este tutorial usa uma identidade gerenciada para autenticar um Aplicativo Web do Azure com um Azure Key Vault. Embora as etapas usem a [biblioteca de clientes do Azure Key Vault v4 para .NET](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) e a [CLI do Azure](/cli/azure/get-started-with-azure-cli), os mesmos princípios básicos se aplicam ao usar a linguagem de desenvolvimento de sua escolha, Azure PowerShell e/ou a portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* O [SDK do .NET Core 3.1 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Crie um grupo de recursos para alojar o cofre de chaves e seu aplicativo Web com o comando [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create):

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>Configurar seu cofre de chaves

Agora, você criará um cofre de chaves e inserirá um segredo nele para uso posterior neste tutorial.

Para criar um cofre de chaves, use o comando [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create):

> [!Important]
> Cada cofre de chaves deve ter um nome exclusivo. Substitua <your-keyvault-name> pelo nome do seu cofre de chaves nos exemplos a seguir.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

Anote o `vaultUri` retornado, que estará no formato "https://&lt;nome-do-seu-key-vault&gt;.vault.azure.net/". Ele será usado na etapa [Atualizar o código](#update-the-code).

Agora você pode inserir um segredo em seu cofre de chaves com o comando [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set). Defina o nome do seu segredo como "MySecret" e o valor como "Sucesso!".

```azurecli-interactive
az keyvault secret set --vault-name "<your-keyvault-name>" --name "MySecret" --value "Success!"
```

## <a name="create-a-net-web-app"></a>Criar um aplicativo Web do .NET

### <a name="create-a-local-app"></a>Criar um aplicativo local

Em uma janela de terminal no computador, crie um diretório chamado `akvwebapp` e altere o diretório atual para ele.

```bash
mkdir akvwebapp
cd akvwebapp
```

Agora, crie um aplicativo .NET Core com o comando [dotnet new web](/dotnet/core/tools/dotnet-new):

```bash
dotnet new web
```

Execute o aplicativo no local para ver como ele deve ficar quando o implantar no Azure. 

```bash
dotnet run
```

Abra um navegador da Web e navegue até o aplicativo em `http://localhost:5000`.

Você verá a mensagem **Olá, Mundo** no aplicativo de exemplo exibido na página.

### <a name="initialize-the-git-repository"></a>Inicializar o repositório git

Na janela do terminal, pressione **Ctrl+C** para sair do servidor Web.  Inicialize um repositório Git para o projeto .NET Core.

```bash
git init
git add .
git commit -m "first commit"
```

### <a name="configure-a-deployment-user"></a>Configurar um usuário de implantação

O FTP e o Git local podem implantar em um aplicativo Web do Azure usando um *usuário de implantação*. Após configurar o usuário de implantação, use-o em todas as implantações do Azure. O nome de usuário e a senha da implantação no nível da conta são diferentes das credenciais de assinatura do Azure. 

Para configurar o usuário de implantação, execute o comando [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set). Escolha um nome de usuário e senha que sigam estas diretrizes: 

- O nome de usuário deve ser exclusivo no Azure. Para envios por push do Git local, não deve conter o símbolo "\@". 
- A senha deve ter pelo menos oito caracteres, com dois destes três elementos: letras, números, símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

A saída JSON mostra a senha como `null`. Se receber um erro `'Conflict'. Details: 409`, altere o nome de usuário. Se receber um erro `'Bad Request'. Details: 400`, use uma senha mais forte. 

Registre seu nome de usuário e senha para usá-los na implantação de aplicativos Web.

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

Criar um plano do Serviço de Aplicativo com o comando [az appservice plan create](/cli/azure/appservice/plan?view=azure-cli-latest) da CLI do Azure. O seguinte exemplo cria um plano do Serviço de Aplicativo denominado `myAppServicePlan` usando o tipo de preço **Gratuita**:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Quando o Plano do Serviço de Aplicativo for criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>


### <a name="create-a-remote-web-app"></a>Criar um aplicativo Web remoto

Crie um [Aplicativo Web do Azure](../../app-service/containers/app-service-linux-intro.md) no plano do serviço de aplicativo `myAppServicePlan`. 

> [!Important]
> Semelhante ao Key Vault, um aplicativo Web do Azure deve ter um nome exclusivo. Substitua \<your-webapp-name\> pelo nome do aplicativo Web como nos exemplos a seguir.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

Quando o aplicativo Web for criado, a CLI do Azure mostrará um resultado semelhante ao seguinte exemplo:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>


A URL do Git remoto é mostrada na propriedade `deploymentLocalGitUrl` com o formato `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git`. Salve essa URL, pois você precisará dela mais tarde.

Navegue para o aplicativo recém-criado. Substitua _&lt;your-webapp-name >_ pelo nome do aplicativo.

```bash
https://<your-webapp-name>.azurewebsites.net
```

Você verá a página da Web padrão para um aplicativo Web do Azure recém-criado.

### <a name="deploy-your-local-app"></a>Implantar seu aplicativo local

De volta à janela do terminal local, adicione um repositório remoto do Azure ao repositório Git local, substituindo *\<deploymentLocalGitUrl-from-create-step>* pela URL do repositório remoto Git que você salvou na etapa [Criar um aplicativo Web remoto](#create-a-remote-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Envie por push para o Azure remoto para implantar seu aplicativo com o comando a seguir. Quando o Git Credential Manager solicitar credenciais, use as credenciais criadas na etapa [Configurar um usuário de implantação](#configure-a-deployment-user).

```bash
git push azure master
```

Esse comando pode demorar um pouco para ser executado. Na execução, ele exibe informações semelhantes ao seguinte exemplo:
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

Navegue (ou atualize) até o aplicativo implantado usando o navegador da Web.

```bash
http://<your-webapp-name>.azurewebsites.net
```

Você verá a mensagem "Olá, Mundo!" que você viu anteriormente ao visitar `http://localhost:5000`.

## <a name="create-and-assign-a-managed-identity"></a>Criar e atribuir uma identidade gerenciada

Na CLI do Azure, para criar a identidade para esse aplicativo, execute o comando [az webapp-identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign):

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

A operação retornará este snippet de JSON:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Para dar ao seu aplicativo Web permissão para as operações **get** e **list** no cofre de chaves, passe a principalID para o comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) da CLI do Azure:

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```


## <a name="modify-the-app-to-access-your-key-vault"></a>Modificar o aplicativo para acessar o cofre de chaves

### <a name="install-the-packages"></a>Instalar os pacotes

Na janela do terminal, instale a biblioteca de cliente do Azure Key Vault para pacotes .NET:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

### <a name="update-the-code"></a>Atualizar o código

Localize e abra o arquivo Startup.cs em seu projeto do akvwebapp. 

Adicione estas duas linhas ao cabeçalho:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Adicione essas linhas antes da chamada de `app.UseEndpoints`, atualizando o URI para refletir a `vaultUri` do cofre de chaves. O código abaixo está usando ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) para autenticação no cofre de chaves, que está usando o token da identidade gerenciada do aplicativo para autenticar. Ele também está usando retirada exponencial para novas tentativas, caso o cofre de chaves esteja sendo limitado.

```csharp
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("mySecret");

string secretValue = secret.Value;
```

Atualize a linha `await context.Response.WriteAsync("Hello World!");` para leitura:

```csharp
await context.Response.WriteAsync(secretValue);
```

Salve suas alterações antes de prosseguir para a próxima etapa.

### <a name="redeploy-your-web-app"></a>Reimplantar seu aplicativo Web

Depois de atualizar seu código, você pode reimplantá-lo no Azure com os seguintes comandos do git:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure master
```

## <a name="visit-your-completed-web-app"></a>Visite seu aplicativo Web concluído

```bash
http://<your-webapp-name>.azurewebsites.net
```

Em que, antes de ver **Olá, Mundo**, agora você deve ver o valor do seu segredo exibido: **Êxito!**

## <a name="next-steps"></a>Próximas etapas

- Aprenda sobre [identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md)
- Saiba mais sobre [identidades gerenciadas para o Serviço de Aplicativo](../../app-service/overview-managed-identity.md?tabs=dotnet)
- Confira a [referência da API da biblioteca de clientes do Azure Key Vault para .NET](/dotnet/api/overview/azure/key-vault?view=azure-dotnet)
- Confira o [código-fonte da biblioteca de clientes do Azure Key Vault para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)
- Confira o [pacote do NuGet da biblioteca de clientes do Azure Key Vault para .NET v4](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)


