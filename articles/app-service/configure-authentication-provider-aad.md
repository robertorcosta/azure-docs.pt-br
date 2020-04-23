---
title: Configurar a autenticação do Azure AD
description: Saiba como configurar a autenticação do Azure Active Directory como um provedor de identidade para o aplicativo App Service ou Azure Functions.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 04/14/2020
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: f625f5df4f33c6516bd5c50f97c52404d76757a0
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024448"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Configure seu aplicativo App Service ou Azure Functions para usar o login do Azure AD

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este artigo mostra como configurar funções do Azure App Service ou Do Azure para usar o Azure Active Directory (Azure AD) como provedor de autenticação.

> [!NOTE]
> O fluxo de configurações expressas configura um registro de aplicativo AAD V1. Se desejar usar [o Azure Active Directory v2.0](../active-directory/develop/v2-overview.md) (incluindo [o MSAL),](../active-directory/develop/msal-overview.md)siga as [instruções avançadas de configuração](#advanced).

Siga essas práticas recomendadas ao configurar seu aplicativo e autenticação:

- Dê a cada aplicativo app Service suas próprias permissões e consentimento.
- Configure cada aplicativo do App Service com seu próprio registro.
- Evite o compartilhamento de permissões entre ambientes usando registros de aplicativos separados para slots de implantação separados. Ao testar um novo código, essa prática pode ajudar a evitar que problemas afetem o aplicativo de produção.

## <a name="configure-with-express-settings"></a><a name="express"> </a>Configurar com configurações expressas

> [!NOTE]
> A opção **Express** não está disponível para nuvens do governo. 

1. No [portal Azure,]procure e selecione **Serviços de Aplicativos**e selecione seu aplicativo.
2. Na navegação à esquerda, selecione **Autenticação /Autorização** > **On**.
3. Selecione O Express o Diretório >  **Ativo do Azure****.**

   Se você quiser escolher um registro de aplicativo existente em vez disso:

   1. Escolha **Selecionar o aplicativo AD existente**e, em seguida, clique no aplicativo **Azure AD**.
   2. Escolha um registro de aplicativo existente e clique em **OK**.

3. Selecione **OK** para registrar o aplicativo do Serviço de Aplicativo no Azure Active Directory. Um novo registro de aplicativo é criado.
   
    ![Configurações expressas no Diretório Ativo do Azure](./media/configure-authentication-provider-aad/express-settings.png)
   
4. (Opcional) Por padrão, o App Service fornece autenticação, mas não restringe o acesso autorizado ao conteúdo do seu site e APIs. Você deve autorizar os usuários no código do aplicativo. Para restringir o acesso ao aplicativo apenas aos usuários autenticados pelo Azure Active Directory, defina ação a ser tomada quando a **solicitação não for autenticada** para **fazer login com o Azure Active Directory**. Quando você define essa funcionalidade, seu aplicativo requer que todas as solicitações sejam autenticadas. Ele também redireciona todos os não autenticados para o Azure Active Directory para autenticação.

    > [!CAUTION]
    > Restringir o acesso desta forma se aplica a todas as chamadas para o seu aplicativo, o que pode não ser desejável para aplicativos que têm uma página inicial disponível publicamente, como em muitos aplicativos de página única. Para tais aplicativos, **permitir solicitações anônimas (nenhuma ação)** pode ser preferível, com o aplicativo iniciando manualmente o próprio login. Para obter mais informações, consulte [Fluxo de Autenticação](overview-authentication-authorization.md#authentication-flow).
5. Clique em **Salvar**.

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>Configurar com configurações avançadas

Você pode configurar as configurações do aplicativo manualmente se quiser usar um registro de aplicativo de um inquilino Azure AD diferente. Para completar esta configuração personalizada:

1. Crie um registro no Azure AD.
2. Forneça alguns dos detalhes de registro ao App Service.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>Crie um registro de aplicativo no Azure AD para o seu aplicativo App Service

Você precisará das seguintes informações ao configurar seu aplicativo app Service:

- ID do Cliente
- ID do locatário
- Segredo do cliente (opcional)
- Uri de id de aplicação

Execute as seguintes etapas:

1. Faça login no [portal Azure,]procure e selecione **Serviços de Aplicativos**e selecione seu aplicativo. Observe a **URL**do seu aplicativo. Você o usará para configurar o registro do aplicativo Azure Active Directory.
1. Selecione**registros** >  **do aplicativo azure active directory** > **Novo registro**.
1. Na **página Registrar um aplicativo,** digite um **Nome** para o seu registro de aplicativo.
1. Em **Redirecionar URI,** selecione **Web** e digite `<app-url>/.auth/login/aad/callback`. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. 
1. Selecione **Criar**.
1. Depois que o registro do aplicativo for criado, copie o **ID do aplicativo (cliente)** e o ID do **Diretório (inquilino)** para posterior.
1. Selecione **Autenticação**. Em **subvenção implícita,** habilite **os tokens de ID** para permitir que o OpenID Connect conecte logins de usuário do App Service.
1. (Opcional) Selecione **Marca**. Na **URL da página inicial,** digite a URL do aplicativo App Service e selecione **Salvar**.
1. Selecione **Expor um conjunto de API** > **Set**. Para um aplicativo de inquilino único, cole na URL do seu aplicativo App Service e selecione **Salvar** e para o aplicativo multi-inquilino, cole na URL que é baseada em um dos domínios verificados pelo inquilino e, em seguida, selecione **Salvar**.

   > [!NOTE]
   > Este valor é o **ID de aplicativo uri** do registro do aplicativo. Se o seu aplicativo web requer acesso a uma API na nuvem, você precisa do **Recurso ID URI** do aplicativo web ao configurar o recurso de Serviço de Aplicativo em nuvem. Você pode usar isso, por exemplo, se quiser que o serviço de nuvem conceda explicitamente acesso ao aplicativo web.

1. Selecione **Adicionar um escopo**.
   1. Em **nome do escopo,** digite *user_impersonation*.
   1. Nas caixas de texto, digite o nome e a descrição do escopo de consentimento que você deseja que os usuários vejam na página de consentimento. Por exemplo, digite *Access my app*. 
   1. Selecione **Adicionar escopo**.
1. (Opcional) Para criar um segredo de cliente, selecione **Certificados & segredos** > **Novo segredo do** > cliente**Adicionar**. Copie o valor secreto do cliente mostrado na página. Não será mostrado novamente.
1. (Opcional) Para adicionar vários **URLs de resposta,** selecione **Autenticação**.

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>Habilite o Azure Active Directory em seu aplicativo app Service

1. No [portal Azure,]procure e selecione **Serviços de Aplicativos**e selecione seu aplicativo. 
1. No painel esquerdo, em **Configurações,** selecione **Autenticação /Autorização** > **Em**.
1. (Opcional) Por padrão, a autenticação do App Service permite acesso não autenticado ao seu aplicativo. Para impor a autenticação do usuário, defina Ação a ser tomada quando a **solicitação não for autenticada** para **fazer login com o Azure Active Directory**.
1. Em **Provedores de Autenticação,** selecione **O Diretório Ativo do Azure**.
1. No **modo Gerenciamento,** selecione **Avançado** e configure a autenticação do Serviço de Aplicativos de acordo com a tabela a seguir:

    |Campo|Descrição|
    |-|-|
    |ID do Cliente| Use o **ID do aplicativo (cliente)** do registro do aplicativo. |
    |Url do emissor| Use `<authentication-endpoint>/<tenant-id>/v2.0`, e substitua * \<o ponto final de autenticação>* com o [ponto final de autenticação para o seu ambiente de nuvem](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) (por exemplo, "https://login.microsoft.com" para o Global Azure), substituindo * \<também o>id de inquilino si locadem* pelo ID do Diretório **(inquilino)** no qual o registro do aplicativo foi criado. Esse valor é usado para redirecionar os usuários para o inquilino Azure AD correto, bem como para baixar os metadados apropriados para determinar as chaves de assinatura de token apropriadas e o valor de reclamação do emissor do token, por exemplo. A `/v2.0` seção pode ser omitida para aplicações usando AAD v1. |
    |Client Secret (Opcional)| Use o segredo do cliente que você gerou no registro do aplicativo.|
    |Audiências de token permitidas| Se este é um aplicativo de nuvem ou servidor e você deseja permitir tokens de autenticação a partir de um aplicativo web, adicione o **ID** de aplicativo URI do aplicativo web aqui. O **ID do Cliente** configurado é *sempre* considerado implicitamente como um público permitido. |

2. Selecione **OK** e, em seguida, **Salvar**.

Agora você está pronto para usar o Azure Active Directory para autenticação em seu aplicativo App Service.

## <a name="configure-a-native-client-application"></a>Configurar um aplicativo de cliente nativo

Você pode registrar clientes nativos para permitir a autenticação das API da Web hospedadas em seu aplicativo usando uma biblioteca de clientes, como a **Biblioteca de Autenticação de Diretório Ativo**.

1. No [portal Azure,]selecione**inscrições** > do Active **Directory** > App**Novo registro**.
1. Na **página Registrar um aplicativo,** digite um **Nome** para o seu registro de aplicativo.
1. Em **Redirecionar URI,** selecione **Cliente público (celular & desktop)** e digite a URL `<app-url>/.auth/login/aad/callback`. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > Para um aplicativo da Microsoft Store, use o SID do [pacote](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) como URI.
1. Selecione **Criar**.
1. Após a criação do registro do aplicativo, copie o valor do ID do **aplicativo (cliente).**
1. Selecione **permissões** > de API**Adicione uma permissão** > **Minhas APIs**.
1. Selecione o registro do aplicativo que você criou anteriormente para o seu aplicativo App Service. Se você não ver o registro do aplicativo, certifique-se de que você adicionou o **escopo user_impersonation** em [Criar um registro de aplicativo no Azure AD para o seu aplicativo App Service](#register).
1. Selecione **user_impersonation**e selecione **Adicionar permissões**.

Agora você configurou um aplicativo cliente nativo que pode acessar seu aplicativo App Service em nome de um usuário.

## <a name="next-steps"></a><a name="related-content"> </a>Próximas etapas

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
