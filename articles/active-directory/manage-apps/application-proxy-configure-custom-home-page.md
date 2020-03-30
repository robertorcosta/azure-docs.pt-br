---
title: Página inicial personalizada para aplicativos publicados - Azure AD Application Proxy
description: Abora as noções básicas sobre os conectores do Proxy de Aplicativo Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1621b273f617955a374ed46d9c215ba99e5b2913
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275607"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Definir uma home page personalizada para aplicativos publicados usando o Proxy de Aplicativo Azure AD

Este artigo discute como configurar um aplicativo para direcionar um usuário para uma página inicial personalizada. Quando você publica um aplicativo com o Proxy do aplicativo, você define uma URL interna, mas às vezes essa não é a página que um usuário deve ver primeiro. Defina uma página inicial personalizada para que o usuário obtenha a página certa quando acessar o aplicativo. Um usuário verá a página inicial personalizada que você definiu, independentemente de acessar o aplicativo do Painel de Acesso ao Diretório Ativo do Azure ou do lançador de aplicativos do Office 365.

Quando um usuário inicia o aplicativo, ele é direcionado por padrão para a URL de domínio raiz para o aplicativo publicado. A página de aterrissagem é normalmente definida como a URL da home page. Use o módulo Azure AD PowerShell para definir uma URL de página inicial personalizada quando quiser que um usuário do aplicativo aterrisse em uma página específica dentro do aplicativo.

Aqui está um cenário que explica por que sua empresa definiria uma home page personalizada:

- Dentro de sua rede corporativa, um usuário vai `https://ExpenseApp/login/login.aspx` fazer login e acessar seu aplicativo.
- Como você tem outros ativos (como imagens) que o Proxy do aplicativo precisa acessar `https://ExpenseApp` no nível superior da estrutura da pasta, você publica o aplicativo com como URL interna.
- A URL externa `https://ExpenseApp-contoso.msappproxy.net`padrão é , que não leva um usuário externo para a página de login.
- Em vez `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` disso, você deseja definir como url da página inicial, para que um usuário externo veja a página de login primeiro.

> [!NOTE]
> Quando você conceder aos usuários acesso a aplicativos publicados, os aplicativos são exibidos no [painel de acesso do Azure AD](../user-help/my-apps-portal-end-user-access.md) e [lançador de aplicativo do Office 365](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Antes de começar

Antes de definir a URL da página inicial, tenha em mente os seguintes requisitos:

- O caminho especificado deve ser um caminho de subdomínio da URL de domínio raiz.

  Por exemplo, se a URL `https://apps.contoso.com/app1/`de domínio raiz for, a `https://apps.contoso.com/app1/`URL da página inicial com a que você configura deve começar com .

- Se você fizer uma alteração no aplicativo publicado, essa alteração poderá redefinir o valor da URL da home page. Quando você decidir atualizar o aplicativo no futuro, você deverá verificar novamente e, se necessário, atualizar a URL da home page.

Você pode definir a URL da página inicial através do portal Azure ou usando o PowerShell.

## <a name="change-the-home-page-in-the-azure-portal"></a>Altere a home page no portal do Azure

Para alterar a URL da página inicial do seu aplicativo através do portal Azure AD, siga estas etapas:

1. Faça login no [portal Azure](https://portal.azure.com/) como administrador.
1. Selecione **o Azure Active Directory**e, em seguida, **registros de aplicativos**. A lista de aplicativos registrados é exibida.
1. Escolha seu aplicativo na lista. Uma página mostrando os detalhes do aplicativo registrado é exibida.
1. Em **Gerenciar,** selecione **Branding**.
1. Atualize a URL da **página inicial** com seu novo caminho.

   ![Página de marca para um aplicativo registrado mostrando o campo URL da página inicial](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Selecione **Salvar**.

## <a name="change-the-home-page-with-powershell"></a>Alterar a home page com o PowerShell

Para configurar a página inicial de um aplicativo usando o PowerShell, você precisa:

1. Instale o módulo do PowerShell do Azure AD.
1. Encontre o valor ObjectId do aplicativo.
1. Atualize a URL da página inicial do aplicativo usando comandos PowerShell.

### <a name="install-the-azure-ad-powershell-module"></a>Instalar o módulo do Powershell do Azure AD

Antes de definir uma URL personalizada de página inicial usando o PowerShell, instale o módulo PowerShell do Azure AD.Baixe esse pacote da [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), que usa o ponto de extremidade de API do Graph.

Para instalar o pacote, siga estas etapas:

1. Abra uma janela padrão do PowerShell e, em seguida, execute o seguinte comando:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Se você estiver executando o comando como um não administrador, use a opção `-scope currentuser`.

1. Durante a instalação, selecione **Y** para instalar dois pacotes de Nuget.org. Ambos os pacotes são necessários.

### <a name="find-the-objectid-of-the-app"></a>Encontre o ObjectId do aplicativo

Você recebe o ObjectId do aplicativo procurando pelo aplicativo pelo seu nome de exibição ou página inicial.

1. Na janela do PowerShell, importe o módulo do Azure AD.

   ```powershell
   Import-Module AzureAD
   ```

1. Entre no módulo Azure AD como o administrador de locatários.

   ```powershell
   Connect-AzureAD
   ```

1. Encontre o aplicativo. Este exemplo usa o PowerShell para encontrar o ObjectId `SharePoint`pesquisando pelo aplicativo com um nome de exibição de .

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Você deve obter um resultado semelhante ao mostrado aqui. Copie o Guid ObjectId para usar na próxima seção.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Alternativamente, você pode apenas puxar a lista de todos os aplicativos, pesquisar na lista para o aplicativo com um nome de exibição específico ou página inicial e copiar o ObjectId do aplicativo assim que o aplicativo for encontrado.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Atualizar a URL da home page

Crie a URL da página inicial e atualize seu aplicativo com esse valor. Continue usando a mesma janela PowerShell, ou se estiver usando uma nova janela PowerShell, `Connect-AzureAD`faça login no módulo Ad do Azure novamente usando . Depois, siga estas etapas:

1. Crie uma variável para manter o valor ObjectId copiado na seção anterior. (Substitua o valor ObjectId usado neste exemplo do SharePoint pelo valor ObjectId do seu aplicativo.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Confirme se você tem o aplicativo correto executando o seguinte comando. A saída deve ser idêntica à saída que você viu na seção anterior[(Encontre o ObjectId do aplicativo](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. Crie um objeto de aplicativo em branco para armazenar as alterações que você deseja fazer.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. Defina a URL da home page como o valor desejado. O valor deve ser um caminho de subdomínio do aplicativo publicado. Por exemplo, se você alterar o URL da página inicial de `https://sharepoint-iddemo.msappproxy.net/` para `https://sharepoint-iddemo.msappproxy.net/hybrid/`, os usuários do aplicativo vão diretamente para a página inicial personalizada.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. Faça a atualização da página inicial.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. Para confirmar que a mudança foi bem sucedida, execute o seguinte comando a partir do passo 2 novamente.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   Para nosso exemplo, a saída deve agora aparecer da seguinte forma:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Reinicie o aplicativo para confirmar se a página inicial aparece como a primeira tela, como esperado.

> [!NOTE]
> Todas as alterações que você fizer no aplicativo poderão redefinir a URL da home page. Se a URL da página inicial for redefinida, repita as etapas nesta seção para defini-la novamente.

## <a name="next-steps"></a>Próximas etapas

- [Habilitar acesso remoto ao SharePoint com o Proxy de Aplicativo do Azure AD](application-proxy-integrate-with-sharepoint-server.md)
- [Tutorial: Adicione um aplicativo local para acesso remoto através do Proxy de aplicativos no Diretório Ativo do Azure](application-proxy-add-on-premises-application.md)