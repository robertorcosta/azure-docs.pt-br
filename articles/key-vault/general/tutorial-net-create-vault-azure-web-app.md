---
title: Tutorial – Usar o Azure Key Vault com um aplicativo Web do Azure no .NET | Microsoft Docs
description: Neste tutorial, você configurará um aplicativo Web do Azure em um aplicativo do ASP.NET Core para ler um segredo no cofre de chaves.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 4ed999e282aa9bcd80b000f3db2ecf9a8386a489
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95537944"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-with-net"></a>Tutorial: Usar uma identidade gerenciada para conectar o Key Vault a um Aplicativo Web do Azure com o .NET

O [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview) fornece um modo de armazenar as credenciais e outros segredos com segurança, mas o código precisa ser autenticado no Key Vault para recuperá-los. A [Visão geral das identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md) ajuda a solucionar esse problema fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter que todas as credenciais no seu código.

Este tutorial usa uma identidade gerenciada para autenticar um Aplicativo Web do Azure com um Azure Key Vault. Embora as etapas usem a [biblioteca de clientes do Azure Key Vault v4 para .NET](/dotnet/api/overview/azure/key-vault) e a [CLI do Azure](/cli/azure/get-started-with-azure-cli), os mesmos princípios básicos se aplicam ao usar a linguagem de desenvolvimento de sua escolha, Azure PowerShell e/ou a portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* O [SDK do .NET Core 3.1 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Instalar o Git](https://www.git-scm.com/downloads).
* [CLI do Azure](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/)
* [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview). Crie um cofre de chaves usando o [portal do Azure](quick-create-portal.md), a [CLI do Azure](quick-create-cli.md) ou o [Azure PowerShell](quick-create-powershell.md).
* [Segredo](https://docs.microsoft.com/azure/key-vault/secrets/about-secrets) do Key Vault. Crie um segredo usando o [portal do Azure](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal), o [PowerShell](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-powershell) ou a [CLI do Azure](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli)

## <a name="create-a-net-core-app-and-deploy-it-to-azure"></a>Criar um aplicativo .NET Core e implantá-lo no Azure
Nesta etapa, você configura o projeto .NET Core local.

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

## <a name="deploy-app-to-azure"></a>Implantar o aplicativo no Azure

Nesta etapa, você implantará seu aplicativo .NET Core no Serviço de Aplicativo usando o Git local. Para obter mais informações sobre como criar e implantar aplicativos, confira [Criar um aplicativo Web ASP.NET Core no Azure](https://docs.microsoft.com/azure/app-service/quickstart-dotnetcore)

### <a name="configure-local-git-deployment"></a>Configurar a implantação do git local

Na janela do terminal, pressione **Ctrl+C** para sair do servidor Web.  Inicialize um repositório Git para o projeto .NET Core.

```bash
git init
git add .
git commit -m "first commit"
```

O FTP e o Git local podem implantar em um aplicativo Web do Azure usando um *usuário de implantação*. Após configurar o usuário de implantação, use-o em todas as implantações do Azure. O nome de usuário e a senha da implantação no nível da conta são diferentes das credenciais de assinatura do Azure. 

Para configurar o usuário de implantação, execute o comando [az webapp deployment user set](/cli/azure/webapp/deployment/user?#az-webapp-deployment-user-set). Escolha um nome de usuário e senha que sigam estas diretrizes: 

- O nome de usuário deve ser exclusivo no Azure. Para envios por push do Git local, não deve conter o símbolo "\@". 
- A senha deve ter pelo menos oito caracteres, com dois destes três elementos: letras, números, símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

A saída JSON mostra a senha como `null`. Se receber um erro `'Conflict'. Details: 409`, altere o nome de usuário. Se receber um erro `'Bad Request'. Details: 400`, use uma senha mais forte. 

Registre seu nome de usuário e senha para usá-los na implantação de aplicativos Web.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Crie um grupo de recursos para alojar o cofre de chaves e seu aplicativo Web com o comando [az group create](/cli/azure/group?#az-group-create):

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

Crie um [Plano do Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) com o comando [az appservice plan create](/cli/azure/appservice/plan) da CLI do Azure. O seguinte exemplo cria um plano do Serviço de Aplicativo denominado `myAppServicePlan` usando o tipo de preço **Gratuita**:

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

Para obter mais informações sobre o gerenciamento do Plano do Serviço de Aplicativo, confira [Gerenciar um Plano do Serviço de Aplicativo no Azure](https://docs.microsoft.com/azure/app-service/app-service-plan-manage)

### <a name="create-a-web-app"></a>Criar um aplicativo Web

Crie um [Aplicativo Web do Azure](../../app-service/overview.md) no plano do serviço de aplicativo `myAppServicePlan`. 

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

De volta à janela do terminal local, adicione um repositório remoto do Azure ao repositório Git local, substituindo *\<deploymentLocalGitUrl-from-create-step>* pela URL do repositório remoto Git que você salvou na etapa [Criar um aplicativo Web](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Envie por push para o Azure remoto para implantar seu aplicativo com o comando a seguir. Quando o Gerenciador de Credenciais do Git solicitar as credenciais, use as credenciais criadas na etapa [Configurar uma implantação do Git local](#configure-local-git-deployment).

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
 
## <a name="configure-web-app-to-connect-to-key-vault"></a>Configurar o aplicativo Web para se conectar ao Key Vault

Nesta seção, você vai configurar o acesso à Web para o cofre de chaves e atualizar o código do aplicativo para recuperar o segredo do cofre de chaves.

### <a name="create-and-assign-a-managed-identity"></a>Criar e atribuir uma identidade gerenciada

Neste tutorial, usaremos a [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md) do aplicativo para se autenticar no cofre de chaves, que gerencia automaticamente as credenciais do aplicativo.

Na CLI do Azure, para criar a identidade para esse aplicativo, execute o comando [az webapp-identity assign](/cli/azure/webapp/identity?#az-webapp-identity-assign):

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

Para dar ao seu aplicativo Web permissão para as operações **get** e **list** no cofre de chaves, passe a principalID para o comando [az keyvault set-policy](/cli/azure/keyvault?#az-keyvault-set-policy) da CLI do Azure:

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```

Atribua também políticas de acesso usando o [portal do Azure](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal) ou o [PowerShell](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-powershell).

### <a name="modify-the-app-to-access-your-key-vault"></a>Modificar o aplicativo para acessar o cofre de chaves

#### <a name="install-the-packages"></a>Instalar os pacotes

Na janela do terminal, instale a biblioteca de cliente do Azure Key Vault para pacotes .NET:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

#### <a name="update-the-code"></a>Atualizar o código

Localize e abra o arquivo Startup.cs em seu projeto do akvwebapp. 

Adicione estas duas linhas ao cabeçalho:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Adicione essas linhas antes da chamada de `app.UseEndpoints`, atualizando o URI para refletir a `vaultUri` do cofre de chaves. O código abaixo está usando ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential) para autenticação no cofre de chaves, que está usando o token da identidade gerenciada do aplicativo para autenticar. Para obter mais informações sobre como se autenticar no cofre de chaves, confira [Guia do Desenvolvedor](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code). Ele também está usando retirada exponencial para novas tentativas, caso o cofre de chaves esteja sendo limitado. Para obter mais informações sobre os limites de transações do cofre de chaves, leia [Diretrizes de limitação do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview-throttling)

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

KeyVaultSecret secret = client.GetSecret("<mySecret>");

string secretValue = secret.Value;
```

Atualize a linha `await context.Response.WriteAsync("Hello World!");` para leitura:

```csharp
await context.Response.WriteAsync(secretValue);
```

Salve suas alterações antes de prosseguir para a próxima etapa.

#### <a name="redeploy-your-web-app"></a>Reimplantar seu aplicativo Web

Depois de atualizar seu código, você pode reimplantá-lo no Azure com os seguintes comandos do git:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure master
```

### <a name="visit-your-completed-web-app"></a>Visite seu aplicativo Web concluído

```bash
http://<your-webapp-name>.azurewebsites.net
```

Em que, antes de ver **Olá, Mundo**, agora você deve ver o valor do seu segredo exibido: **Êxito!**

## <a name="next-steps"></a>Próximas etapas

- [Usar o Azure Key Vault com os aplicativos implantados na máquina virtual no .NET](https://docs.microsoft.com/azure/key-vault/general/tutorial-net-virtual-machine)
- Aprenda sobre [identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md)
- Saiba mais sobre [identidades gerenciadas para o Serviço de Aplicativo](../../app-service/overview-managed-identity.md?tabs=dotnet)
- [Guia do desenvolvedor](https://docs.microsoft.com/azure/key-vault/general/developers-guide)
- [Proteger o acesso a um cofre de chaves](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault)


