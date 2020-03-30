---
title: Acesse APIs locais com proxy de aplicativo Azure AD
description: O Proxy de aplicativos do Azure Active Directory permite que os aplicativos nativos acessem com segurança as APIs e a lógica de negócios que você hospeda no local ou em VMs na nuvem.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: ecd5d8bae22d67f8d9f5b99d5c94eecf54a4a1f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165999"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Acesso seguro a APIs locais com proxy de aplicativo Azure AD

Você pode ter APIs de lógica de negócios sendo realizadas no local ou hospedadas em máquinas virtuais na nuvem. Seus aplicativos nativos para Android, iOS, Mac ou Windows precisam interagir com os pontos finais da API para usar dados ou fornecer interação do usuário. O Azure AD Application Proxy e o [Azure Active Directory Authenticy Libraryes (ADAL)](/azure/active-directory/develop/active-directory-authentication-libraries) permitem que seus aplicativos nativos acessem com segurança suas APIs locais. O Azure Active Directory Application Proxy é uma solução mais rápida e segura do que abrir portas de firewall e controlar a autenticação e a autorização na camada do aplicativo. 

Este artigo orienta você a configurar uma solução Azure AD Application Proxy para hospedar um serviço de API web que os aplicativos nativos podem acessar. 

## <a name="overview"></a>Visão geral

O diagrama a seguir mostra uma maneira tradicional de publicar APIs no local. Esta abordagem requer a abertura dos portos de entrada 80 e 443.

![Acesso a API tradicional](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

O diagrama a seguir mostra como você pode usar o Azure AD Application Proxy para publicar APIs com segurança sem abrir nenhuma porta de entrada:

![Acesso à API proxy do aplicativo Azure AD](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

O Proxy de aplicativo Azure AD forma a espinha dorsal da solução, funcionando como um ponto final público para acesso à API e fornecendo autenticação e autorização. Você pode acessar suas APIs a partir de uma vasta gama de plataformas usando as bibliotecas [ADAL.](/azure/active-directory/develop/active-directory-authentication-libraries) 

Uma vez que a autenticação e a autorização do Proxy do aplicativo Azure AD são construídas em cima do Azure AD, você pode usar o Azure AD Conditional Access para garantir que apenas dispositivos confiáveis possam acessar APIs publicadas através do Proxy do Aplicativo. Use a Azure AD Join ou Azure AD Hybrid Joined para desktops e Intune Managed para dispositivos. Você também pode aproveitar os recursos do Azure Active Directory Premium, como a Autenticação Multifatorial do Azure, e a segurança apoiada por aprendizado de máquina do [Azure Identity Protection](/azure/active-directory/active-directory-identityprotection).

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este passo a passo, você precisa:

- Acesso ao administrador de um diretório do Azure, com uma conta que pode criar e registrar aplicativos
- A API web de exemplo e aplicativos clientes nativos de[https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>Publique a API através do Proxy de Aplicativos

Para publicar uma API fora da sua intranet através do Proxy de aplicativo, você segue o mesmo padrão de publicação de aplicativos web. Para obter mais informações, consulte [Tutorial: Adicione um aplicativo local para acesso remoto através do Proxy de aplicativo no Azure Active Directory](application-proxy-add-on-premises-application.md).

Para publicar a API web SecretAPI através do Proxy de aplicativo:

1. Construa e publique o projeto SecretAPI de exemplo como um aplicativo web ASP.NET em seu computador ou intranet local. Certifique-se de que você pode acessar o aplicativo web localmente. 
   
1. No [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**. Em seguida, selecione **aplicativos Corporativos**.
   
1. Na parte superior dos **aplicativos Enterprise - Página de todos os aplicativos,** selecione Novo **aplicativo**.
   
1. Na **página Adicionar uma página de aplicativo,** selecione **aplicativos on-premises**. A página Adicionar sua própria página **de aplicativo no local** é exibida.
   
1. Se você não tiver um conector proxy de aplicativo instalado, você será solicitado a instalá-lo. Selecione **Baixar conector proxy de aplicativo** para baixar e instalar o conector. 
   
1. Depois de instalar o Conector proxy do aplicativo, na página **do aplicativo Adicionar no local:**
   
   1. Ao lado **de Nome,** insira *SecretAPI*.
      
   1. Ao lado **da Url Interna,** digite a URL que você usa para acessar a API de dentro de sua intranet.
      
   1. Certifique-se de que **a pré-autenticação** está definida **como Azure Active Directory**. 
      
   1. Selecione **Adicionar** na parte superior da página e aguarde a criação do aplicativo.
   
   ![Adicionar aplicativo de API](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. Nos **aplicativos Enterprise - Página de todos os aplicativos,** selecione o aplicativo **SecretAPI.** 
   
1. Na página **SecretAPI - Visão geral,** selecione **Propriedades** na navegação à esquerda.
   
1. Você não quer que as APIs estejam disponíveis para usuários finais no painel **MyApps,** então defina **'Visível para os usuários'** como **Não** na parte inferior da página **Propriedades** e selecione **Salvar**.
   
   ![Não é visível para os usuários](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
Você publicou sua API web através do Proxy de aplicativo Azure AD. Agora, adicione usuários que podem acessar o aplicativo. 

1. Na página **SecretAPI - Visão geral,** selecione **Usuários e grupos** na navegação à esquerda.
   
1. Na página **Usuários e grupos,** selecione **Adicionar usuário**.  
   
1. Na página **Adicionar atribuição,** selecione **Usuários e grupos**. 
   
1. Na página **Usuários e grupos,** procure e selecione usuários que podem acessar o aplicativo, incluindo pelo menos você mesmo. Depois de selecionar todos os usuários, selecione **Selecionar**. 
   
   ![Selecione e designe o usuário](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. De volta à página **Adicionar atribuição,** selecione **Atribuir**. 

> [!NOTE]
> ApIs que usam autenticação integrada do Windows podem exigir [etapas adicionais](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd).

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Registre o aplicativo nativo e conceda acesso à API

Aplicativos nativos são programas desenvolvidos para usar em uma determinada plataforma ou dispositivo. Antes que seu aplicativo nativo possa se conectar e acessar uma API, você deve registrá-la no Azure AD. As etapas a seguir mostram como registrar um aplicativo nativo e dar-lhe acesso à API web que você publicou através do Proxy do aplicativo.

Para registrar o aplicativo nativo AppProxyNativeAppSample:

1. Na página **Visão Geral** do Diretório Ativo do Azure, selecione inscrições de **aplicativos**e, no topo do painel de inscrições do **App,** selecione **Novo registro**.
   
1. Na **página Registrar um aplicativo:**
   
   1. Em **Nome,** *insira AppProxyNativeAppSample*. 
      
   1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**. 
      
   1. Em **Url redirecionar,** baixe e selecione **Cliente público (mobile & desktop)** e digite *https:\//appproxynativeapp*. 
      
   1. Selecione **Registrar**e aguarde que o aplicativo seja registrado com sucesso. 
      
      ![Novo registro de aplicativo](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
Você já registrou o aplicativo AppProxyNativeAppSample no Azure Active Directory. Para dar acesso ao seu aplicativo nativo à API web SecretAPI:

1. Na página Azure Active Directory **'Registros** > de**aplicativos' (Registros** de aplicativos de visão geral do diretório ativo) selecione o aplicativo **AppProxyNativeAppSample.** 
   
1. Na página **AppProxyNativeAppSample,** selecione **permissões de API** na navegação à esquerda. 
   
1. Na página de permissões da **API,** **selecione Adicionar uma permissão**.
   
1. Na primeira página **de permissões de API de solicitação,** selecione as **APIs que minha organização usa** e, em seguida, procure e selecione **SecretAPI**. 
   
1. Na próxima página **de permissões de API de solicitação,** selecione a caixa de seleção ao lado **de user_impersonation**e, em seguida, **selecione Adicionar permissões**. 
   
    ![Selecionar uma API](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. De volta à página de permissões da **API,** você pode selecionar **o consentimento de admin grant para Contoso para** evitar que outros usuários tenham que consentir individualmente com o aplicativo. 

## <a name="configure-the-native-app-code"></a>Configure o código do aplicativo nativo

O último passo é configurar o aplicativo nativo. O trecho a seguir do arquivo *Form1.cs* no aplicativo de amostra NativeClient faz com que a biblioteca ADAL adquira o token para solicitar a chamada da API e anexe-o como portador ao cabeçalho do aplicativo. 
   
   ```csharp
       AuthenticationResult result = null;
       HttpClient httpClient = new HttpClient();
       authContext = new AuthenticationContext(authority);
       result = await authContext.AcquireTokenAsync(todoListResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
       
       // Append the token as bearer in the request header.
       httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
       
       // Call the API.
       HttpResponseMessage response = await httpClient.GetAsync(todoListBaseAddress + "/api/values/4");
   
       // MessageBox.Show(response.RequestMessage.ToString());
       string s = await response.Content.ReadAsStringAsync();
       MessageBox.Show(s);
   ```
   
Para configurar o aplicativo nativo para se conectar ao Azure Active Directory e chamar o Proxy do aplicativo aPI, atualize os valores do espaço reservado no arquivo *App.config* do aplicativo de amostra NativeClient com valores do Azure AD: 

- Cole o **ID do Diretório (inquilino)** no `<add key="ida:Tenant" value="" />` campo. Você pode encontrar e copiar este valor (um GUID) na página **Visão Geral** de qualquer um de seus aplicativos. 
  
- Cole o ID do Aplicativo AppProxyNativeAppSample `<add key="ida:ClientId" value="" />` **(cliente)** no campo. Você pode encontrar e copiar esse valor (um GUID) na página Visão **Geral** do AppProxyNativeAppSample.
  
- Cole o APPProxyNativeAppSample **Redirecionar** `<add key="ida:RedirectUri" value="" />` uri no campo. Você pode encontrar e copiar esse valor (um URI) na página **AppProxyNativeAppSampleAuthentication.** 
  
- Cole o **ID de aplicação** SecretAPI URI no `<add key="todo:TodoListResourceId" value="" />` campo. Você pode encontrar e copiar este valor (um URI) da página SecretAPI **Expor uma API.**
  
- Cole a URL da **página** inicial `<add key="todo:TodoListBaseAddress" value="" />` secretapi no campo. Você pode encontrar e copiar este valor (uma URL) da página SecretAPI **Branding.**

Depois de configurar os parâmetros, construa e execute o aplicativo nativo. Quando você seleciona o botão **Fazer login,** o aplicativo permite que você faça login e, em seguida, exibe uma tela de sucesso para confirmar que ele está conectado com sucesso à SecretAPI.

![Sucesso](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Adicione um aplicativo local para acesso remoto através do Proxy de aplicativos no Diretório Ativo do Azure](application-proxy-add-on-premises-application.md)
- [Quickstart: Configure um aplicativo cliente para acessar APIs da Web](../develop/quickstart-configure-app-access-web-apis.md)
- [Como permitir que aplicativos clientes nativos interajam com aplicativos proxy](application-proxy-configure-native-client-application.md)
