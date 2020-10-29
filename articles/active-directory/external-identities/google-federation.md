---
title: Adicionar o Google como provedor de identidade para B2B – Azure AD
description: Federar com o Google para permitir que os usuários convidados entrem em seus aplicativos do Azure AD com suas próprias contas do gmail.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff8912794169cf61f394a097248a8476b2e0c0f3
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926228"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Adicionar o Google como provedor de identidade para usuários convidados B2B

Ao configurar a Federação com o Google, você pode permitir que os usuários convidados entrem em seus aplicativos e recursos compartilhados com suas próprias contas do Gmail, sem precisar criar contas da Microsoft. 

> [!NOTE]
> A federação do Google foi criada especificamente para usuários do Gmail. Para federar com domínios do G Suite, use a [Federação direta](direct-federation.md).

## <a name="what-is-the-experience-for-the-google-user"></a>Qual é a experiência do usuário do Google?
Quando você envia um convite para usuários do Google Gmail, os usuários convidados devem acessar seus aplicativos ou recursos compartilhados usando um link que inclui o contexto do locatário. A experiência varia dependendo de a conexão ao Google já ter ocorrido ou não:
  - Os usuários convidados que não estiverem conectados ao Google serão solicitados a fazê-lo.
  - Os usuários convidados que já estiverem conectados ao Google serão solicitados a escolher a conta que desejam usar. Ele precisa escolher a conta que foi usada para convidá-lo.

Os usuários convidados que veem um erro de "cabeçalho muito longo" podem limpar seus cookies ou abrir uma janela particular ou Incognito e tentar entrar novamente.

![Captura de tela que mostra a página de entrada do Google.](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Limitações

O Teams dá suporte total aos usuários convidados do Google em todos os dispositivos. Os usuários do Google podem entrar no Teams de um ponto de extremidade comum, como `https://teams.microsoft.com`.

Os pontos de extremidade comuns de outros aplicativos podem não dar suporte a usuários do Google. Os usuários convidados do Google devem entrar usando um link que inclui suas informações de locatário. Veja a seguir exemplos:
  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://portal.azure.com/<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Se os usuários convidados do Google tentarem usar um link como `https://myapps.microsoft.com` ou `https://portal.azure.com` , eles receberão um erro.

Você também pode conceder aos usuários convidados do Google um link direto para um aplicativo ou recurso, desde que o link inclua suas informações de locatário. Por exemplo, `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`. 

## <a name="step-1-configure-a-google-developer-project"></a>Etapa 1: Configurar um projeto de desenvolvedor do Google
Primeiro, crie um novo projeto no console de desenvolvedores do Google para obter uma ID do cliente e um segredo do cliente que você pode adicionar posteriormente ao Azure Active Directory (Azure AD). 
1. Acesse as APIs do Google em https://console.developers.google.com e entre com sua conta do Google. É recomendável que você use uma conta do Google de equipe compartilhada.
2. Aceite os termos de serviço se você for solicitado a fazê-lo.
3. Criar um novo projeto: no painel, selecione **criar projeto** , dê um nome ao projeto (por exemplo, **Azure ad B2B** ) e, em seguida, selecione **criar** : 
   
   ![Captura de tela que mostra uma nova página de projeto.](media/google-federation/google-new-project.png)

4. Na página de **serviços & de APIs** , selecione **Exibir** em seu novo projeto.

5. Selecione **ir para APIs visão geral** no cartão de APIs. Selecione a **tela de consentimento do OAuth** .

6. Selecione **Firewall** e, em seguida, **Criar** . 

7. Na **tela de consentimento do OAuth** , insira um nome de **aplicativo** :

   ![Captura de tela que mostra o consentimento de OAuth do Google.](media/google-federation/google-oauth-consent-screen.png)

8. Role até a seção **domínios autorizados** e insira **microsoftonline.com** :

   ![Captura de tela que mostra a seção domínios autorizados.](media/google-federation/google-oauth-authorized-domains.PNG)

9. Selecione **Salvar** .

10. Selecione **Credenciais** . No menu **criar credenciais** , selecione **ID do cliente OAuth** :

    ![Captura de tela que mostra o menu criar credenciais do Google APIs.](media/google-federation/google-api-credentials.png)

11. Em **Tipo de aplicativo** , selecione **Aplicativo Web** . Dê ao aplicativo um nome adequado, como **B2B do Azure ad** . Em **URIs de redirecionamento autorizados** , insira os seguintes URIs:
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(onde `<tenant ID>` é sua ID de locatário)
   
    > [!NOTE]
    > Para localizar sua ID de locatário, vá para a [portal do Azure](https://portal.azure.com). Em **Azure Active Directory** , selecione **Propriedades** e copie a **ID do locatário** .

    ![Captura de tela que mostra a seção URIs de redirecionamento autorizados.](media/google-federation/google-create-oauth-client-id.png)

12. Selecione **Criar** . Copie a ID do cliente e o segredo do cliente. Você os usará quando adicionar o provedor de identidade no portal do Azure.

    ![Captura de tela que mostra a ID do cliente OAuth e o segredo do cliente.](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Etapa 2: Configurar a federação do Google no Microsoft Azure Active Directory 
Agora você vai definir a ID do cliente do Google e o segredo do cliente. Você pode usar o portal do Azure ou o PowerShell para fazer isso. Certifique-se de testar sua configuração do Google Federation convidando-se. Use um endereço do Gmail e tente resgatar o convite com sua conta do Google convidada. 

**Para configurar o Google Federation no portal do Azure** 
1. Vá para o [Portal do Azure](https://portal.azure.com). No painel esquerdo, selecione **Azure Active Directory** . 
2. Selecione **Identidades Externas** .
3. Selecione **todos os provedores de identidade** e, em seguida, selecione o botão **Google** .
4. Insira a ID do cliente e o segredo do cliente obtidos anteriormente. Selecione **salvar** : 

   ![Captura de tela que mostra a página Adicionar provedor de identidade do Google.](media/google-federation/google-identity-provider.png)

**Como configurar a federação do Google usando o PowerShell**
1. Instale a versão mais recente do módulo PowerShell for Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) do Azure AD.
2. Execute este comando: `Connect-AzureAD`
3. No prompt de entrada, entre com a conta de Administrador Global gerenciada.  
4. Execute o comando a seguir: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > Use a ID do cliente e o segredo do cliente do aplicativo criado em "etapa 1: configurar um projeto de desenvolvedor do Google". Para obter mais informações, consulte [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview). 
 
## <a name="how-do-i-remove-google-federation"></a>Como fazer a remoção da federação do Google?
É possível excluir sua configuração da federação do Google. Se você fizer isso, os usuários convidados do Google que já tiverem resgatado seu convite não poderão entrar. Mas você pode conceder a eles acesso a seus recursos novamente, excluindo-os do diretório e convidando-os novamente. 
 
**Para excluir o Google Federation no portal do Azure AD**
1. Vá para o [Portal do Azure](https://portal.azure.com). No painel esquerdo, selecione **Azure Active Directory** . 
2. Selecione **Identidades Externas** .
3. Selecione **Todos os provedores de identidade** .
4. Na linha do **Google** , selecione o botão de reticências ( **...** ) e, em seguida, selecione **excluir** . 
   
   ![Captura de tela que mostra o botão excluir do provedor de identidade social.](media/google-federation/google-social-identity-providers.png)

1. Selecione **Sim** para confirmar a exclusão. 

**Para excluir o Google Federation usando o PowerShell** 
1. Instale a versão mais recente do módulo PowerShell for Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) do Azure AD.
2. Execute `Connect-AzureAD`.  
4. No prompt de entrada, entre com a conta de Administrador Global gerenciada.  
5. Insira o seguinte comando:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Para obter mais informações, consulte [AzureADMSIdentityProvider remover](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview).
