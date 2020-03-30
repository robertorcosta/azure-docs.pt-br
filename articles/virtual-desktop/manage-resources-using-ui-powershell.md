---
title: Implantar uma ferramenta de gerenciamento para o Windows Virtual Desktop usando o principal de serviço - Azure
description: Como implantar a ferramenta de gerenciamento para o Windows Virtual Desktop usando o PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0838edb03c4868548f3d09f14d71ec7016e670a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127802"
---
# <a name="deploy-a-management-tool-with-powershell"></a>Implantar uma ferramenta de gerenciamento com o PowerShell

Este artigo mostrará como implantar a ferramenta de gerenciamento usando o PowerShell.

## <a name="important-considerations"></a>Considerações importantes

A assinatura de cada inquilino do Azure Active Directory (Azure AD) precisa de sua própria implantação separada da ferramenta de gerenciamento. Esta ferramenta não suporta cenários Azure AD Business-to-Business (B2B). 

Esta ferramenta de gerenciamento é uma amostra. A Microsoft fornecerá atualizações de qualidade e de segurança importantes. [O código-fonte está disponível no GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Seja você um cliente ou parceiro, nós encorajamos você a personalizar a ferramenta para satisfazer as necessidades do seu negócio.

Os seguintes navegadores são compatíveis com a ferramenta de gerenciamento:

- Google Chrome 68 ou posterior
- Microsoft Edge 40.15063 ou posterior
- Mozilla Firefox 52.0 ou posterior
- Safari 10 ou posterior (somente macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>O que você precisa para implantar a ferramenta de gerenciamento

Antes de implantar a ferramenta de gerenciamento, você precisará que um usuário do Azure AD (Azure Active Directory) crie um registro de aplicativo e implante a interface do usuário de gerenciamento. Esse usuário precisa:

- Ter permissão para criar recursos em sua assinatura do Azure
- Ter permissão para criar um aplicativo do Azure AD. Siga estas etapas para verificar se o usuário tem as permissões necessárias, seguindo as instruções em [Permissões necessárias](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Para implantar e configurar com sucesso a ferramenta de gerenciamento, primeiro você precisa baixar os seguintes scripts PowerShell do [repo RDS-Templates GitHub e salvá-los](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts) na mesma pasta em sua máquina local.

  - createWvdMgmtUxAppRegistration.ps1
  - updateWvdMgmtUxApiUrl.ps1

Depois de implantar e configurar a ferramenta de gerenciamento, é recomendável solicitar que um usuário inicie a interface do usuário de gerenciamento para verificar se tudo está funcionando. O usuário que inicia a interface do usuário de gerenciamento deve ter uma atribuição de função que permita exibir ou editar o locatário da Área de Trabalho Virtual do Windows.

## <a name="set-up-powershell"></a>Configurar o PowerShell

Comece fazendo login nos módulos Az e Azure AD PowerShell. Veja como fazer login:

1. Abra o PowerShell como administrador e navegue até o diretório onde você salvou os scripts do PowerShell.
2. Faça login no Azure com uma conta que tenha permissões de proprietário ou contribuinte na assinatura do Azure que você planeja usar para criar a ferramenta de gerenciamento executando o seguinte cmdlet:

    ```powershell
    Login-AzAccount
    ```

3. Execute o cmdlet a seguir para fazer login no Azure AD com a mesma conta que você usou para o módulo Az PowerShell:

    ```powershell
    Connect-AzureAD
    ```

4. Depois disso, navegue até a pasta onde você salvou os dois scripts PowerShell do repo RDS-Templates GitHub.

Mantenha a janela PowerShell que você usou para assinar aberta para executar cmdlets adicionais do PowerShell enquanto estiver conectado.

## <a name="create-an-azure-active-directory-app-registration"></a>Crie um registro de aplicativo do Azure Active Directory

Execute os seguintes comandos para criar o registro do aplicativo com as permissões de API necessárias:

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$subscriptionId = Read-Host -Prompt "Enter the Azure subscription ID where you will be deploying the management tool."

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Agora que você completou o registro do aplicativo Azure AD, você pode implantar a ferramenta de gerenciamento.

## <a name="deploy-the-management-tool"></a>Implantar a ferramenta de gerenciamento

Execute os seguintes comandos powershell para implantar a ferramenta de gerenciamento e associá-la com o principal de serviço que você acabou de criar:
     
```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateParameters = @{}
$templateParameters.Add('isServicePrincipal', $true)
$templateParameters.Add('azureAdminUserPrincipalNameOrApplicationId', $ServicePrincipalCredentials.UserName)
$templateParameters.Add('azureAdminPassword', $servicePrincipalCredentials.Password)
$templateParameters.Add('applicationName', $appName)

Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -TemplateParameterObject $templateParameters `
    -Verbose
```

Depois de criar o aplicativo web, você deve adicionar um URI redirecionado ao aplicativo Azure AD para fazer login com sucesso nos usuários.

## <a name="set-the-redirect-uri"></a>Defina o URI de redirecionamento

Execute os seguintes comandos do PowerShell para recuperar a URL do aplicativo web e defina-a como uri de redirecionamento de autenticação (também chamado de URL de resposta):

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri  
```

Agora que você adicionou um URI de redirecionamento, você precisará atualizar a URL da API para que a ferramenta de gerenciamento possa interagir com o serviço de back-end da API.

## <a name="update-the-api-url-for-the-web-application"></a>Atualize a URL da API para o aplicativo web

Execute o seguinte script para atualizar a configuração de URL da API no front-end do aplicativo web:

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Agora que você configurou totalmente o aplicativo web da ferramenta de gerenciamento, é hora de verificar o aplicativo Azure AD e fornecer consentimento.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>Verifique o aplicativo Ad do Azure e forneça consentimento

Para verificar a configuração do aplicativo Azure AD e fornecer consentimento:

1. Abra seu navegador de internet e faça login no [portal Azure](https://portal.azure.com/) com sua conta administrativa.
2. Na barra de pesquisa no topo do portal Azure, procure **por inscrições de aplicativos** e selecione o item em **Serviços**.
3. Selecione **Todos os aplicativos** e pesquise o nome exclusivo do aplicativo que você forneceu para o script PowerShell em Criar um registro do aplicativo [Azure Active Directory](#create-an-azure-active-directory-app-registration).
4. No painel do lado esquerdo do navegador, selecione **Autenticação** e certifique-se de que o URI redirecionamento seja o mesmo que a URL do aplicativo web para a ferramenta de gerenciamento, como mostrado na imagem a seguir.
   
   [![A página de autenticação com](media/management-ui-redirect-uri-inline.png) o URI de redirecionamento ininserido](media/management-ui-redirect-uri-expanded.png#lightbox)

5. No painel esquerdo, selecione **permissões de API** para confirmar que as permissões foram adicionadas. Se você é um admin global, selecione o consentimento do anúncio **do Grant para `tenantname` ** o botão e siga as instruções de diálogo para fornecer consentimento de admin para sua organização.
    
    [![A página](media/management-ui-permissions-inline.png) de permissões da API](media/management-ui-permissions-expanded.png#lightbox)

Agora você pode começar a usar a ferramenta de gerenciamento.

## <a name="use-the-management-tool"></a>Usar a ferramenta de gerenciamento

Agora que você configurou a ferramenta de gerenciamento a qualquer momento, você pode lançá-la a qualquer hora, em qualquer lugar. Veja como lançar a ferramenta:

1. Abra a URL do aplicativo web em um navegador da Web. Se você não se lembrar da URL, você pode fazer login no Azure, encontrar o serviço de aplicativo que você implantou para a ferramenta de gerenciamento e, em seguida, selecionar a URL.
2. Entre usando suas credenciais da Área de Trabalho Virtual do Windows.
   
   > [!NOTE]
   > Se você não concedeu o consentimento do administrador durante a configuração da ferramenta de gerenciamento, cada usuário que assinar precisará fornecer seu próprio consentimento do usuário para usar a ferramenta.

3. Quando solicitado a escolher um grupo de inquilinos, selecione **Grupo de inquilinopadrão** na lista suspensa.
4. Quando você seleciona **Grupo de Locatários Padrão**, um menu deve ser exibido no lado esquerdo da janela. Nesse menu, encontre o nome do seu grupo de locatários e selecione-o.
   
   > [!NOTE]
   > Se tiver um grupo de locatários personalizado, digite o nome manualmente em vez de escolher na lista suspensa.

## <a name="report-issues"></a>Relatar problemas

Caso tenha problemas com a ferramenta de gerenciamento ou outras ferramentas da Área de Trabalho Virtual do Windows, siga as instruções em [Modelos do Azure Resource Manager para Serviços de Área de Trabalho Remota](https://github.com/Azure/RDS-Templates/blob/master/README.md) para relatá-los no GitHub.

## <a name="next-steps"></a>Próximas etapas

Agora que aprendeu como implantar a ferramenta de gerenciamento e conectar-se a ela, você pode aprender a usar a Integridade do Serviço do Azure para monitorar problemas de serviço e avisos de integridade. Para saber mais, confira o tutorial [Configurar alertas de serviço](./set-up-service-alerts.md).
