---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: 8754504655cdd08c9bf9f89311cb6c5d1057f0e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262338"
---
## <a name="authenticate-with-azure-active-directory"></a>Autenticar com o Azure Active Directory

> [!IMPORTANT]
> 1. Atualmente, **apenas** a API de visão computacional, API face, API de análise de texto, leitor imersivo, reconhecimento de formulário, detector de anomalias e todos os serviços bing, exceto bing custom search de suporte de autenticação usando AaD (AAD Active Directory).
> 2. A autenticação AAD precisa ser sempre usada em conjunto com o nome personalizado de subdomínio do seu recurso Azure. [Os pontos finais regionais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#is-there-a-list-of-regional-endpoints) não suportam a autenticação AAD.

Nas seções anteriores, mostramos como autenticar contra os Serviços Cognitivos do Azure usando uma chave de assinatura de serviço único ou multi-serviço. Embora essas chaves forneçam um caminho rápido e fácil para iniciar o desenvolvimento, elas ficam aquém em cenários mais complexos que exigem controles de acesso baseados em função. Vamos dar uma olhada no que é necessário para autenticar usando o Azure Active Directory (AAD).

Nas seções a seguir, você usará o ambiente Azure Cloud Shell ou o Azure CLI para criar um subdomínio, atribuir funções e obter um token portador para chamar os Serviços Cognitivos do Azure. Se você ficar preso, os links são fornecidos em cada seção com todas as opções disponíveis para cada comando no Azure Cloud Shell/Azure CLI.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Crie um recurso com um subdomínio personalizado

O primeiro passo é criar um subdomínio personalizado. Se você quiser usar um recurso de Serviços Cognitivos existente que não tenha nome de subdomínio personalizado, siga as instruções em [Subdomínios personalizados de serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#how-does-this-impact-existing-resources) para habilitar subdomínio personalizado para o seu recurso.

1. Comece abrindo a Azure Cloud Shell. Em [seguida, selecione uma assinatura](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext?view=azps-3.3.0):

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. Em seguida, [crie um recurso de Serviços Cognitivos](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) com um subdomínio personalizado. O nome de subdomínio precisa ser globalmente único e não pode incluir caracteres especiais, como: ".", "!", "!", ".

   ```powershell-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. Se for bem-sucedido, o **Ponto Final** deve mostrar o nome do subdomínio exclusivo para o seu recurso.


### <a name="assign-a-role-to-a-service-principal"></a>Atribuir uma função a uma entidade de serviço

Agora que você tem um subdomínio personalizado associado ao seu recurso, você precisará atribuir uma função a um diretor de serviço.

> [!NOTE]
> Tenha em mente que as atribuições de função AAD podem levar até cinco minutos para se propagar.

1. Primeiro, vamos registrar um [aplicativo AAD](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   Você vai precisar do **ApplicationId** na próxima etapa.

2. Em seguida, você precisa [criar um diretor de serviço](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) para o aplicativo AAD.

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Se você registrar um aplicativo no portal Azure, esta etapa será concluída para você.

3. O último passo é [atribuir a função "Usuário de Serviços Cognitivos"](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) ao principal de serviço (escopo do recurso). Ao atribuir um papel, você está concedendo acesso principal ao principal serviço a este recurso. Você pode conceder ao mesmo serviço acesso principal a vários recursos em sua assinatura.
   >[!NOTE]
   > O ObjectId do principal de serviço é usado, não o ObjectId para o aplicativo.
   > O ACCOUNT_ID será o ID de recurso do Azure da conta de Serviços Cognitivos que você criou. Você pode encontrar o ID de recurso do Azure a partir de "propriedades" do recurso no portal Azure.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Solicitação de exemplo

Nesta amostra, uma senha é usada para autenticar o diretor do serviço. O token fornecido é então usado para chamar a API de visão de computador.

1. Obter o seu **TenantId**:
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Obter um token:
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. Ligue para a API de visão computacional:
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

Alternativamente, o diretor de serviço pode ser autenticado com um certificado. Além do diretor de serviço, o diretor do usuário também é suportado por ter permissões delegadas através de outro aplicativo AAD. Neste caso, em vez de senhas ou certificados, os usuários seriam solicitados para autenticação de dois fatores ao adquirir o token.
