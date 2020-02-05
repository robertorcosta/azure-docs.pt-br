---
title: Gerenciar autenticação | Mapas do Microsoft Azure
description: Você pode usar o portal do Azure para gerenciar a autenticação no Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: f856aebe5e3acaca142e460d18ec8c6498b18787
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989153"
---
# <a name="manage-authentication-in-azure-maps"></a>Gerenciar autenticação no Azure Mapas

Depois de criar uma conta do Azure Maps, uma ID do cliente e as chaves são criadas para dar suporte ao Azure Active Directory (Azure AD) e à autenticação de chave compartilhada.

## <a name="view-authentication-details"></a>Exibir detalhes de autenticação

Após a criação da conta do Azure Maps, as chaves primária e secundária são geradas. É recomendável usar a chave primária como chave de assinatura ao chamar o Azure Maps usando a [autenticação de chave compartilhada](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). A chave secundária pode ser usada em cenários como alterações de chave sem interrupção. Para saber mais, confira [autenticação com o Azure Maps](https://aka.ms/amauth).

Você pode exibir os detalhes de autenticação no portal do Azure. Vá para sua conta e selecione **autenticação** no menu **configurações** .

![Detalhes de autenticação](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="configure-azure-ad-app-registration"></a>Configurar o registro do Aplicativo Azure AD

1. Selecione **Azure Active Directory** na lista de serviços do Azure no portal do Azure.  Selecione **registros de aplicativo** e clique em **novo registro**.  Avançar. Insira **nome**, escolha **tipo de conta de suporte**e clique em **registrar**.  Se você já tiver um aplicativo registrado, vá para a etapa 2. 

    ![Registro do aplicativo](./media/how-to-manage-authentication/app-registration.png)

    ![Detalhes de registro do aplicativo](./media/how-to-manage-authentication/app-create.png)

2. Para atribuir permissões de API delegadas ao Azure Maps, vá para o aplicativo em **registros de aplicativo**. Em seguida, selecione **permissões de API**e, em seguida, selecione **Adicionar uma permissão**. Pesquise e selecione **mapas do Azure** em **APIs que minha organização usa**.

    ![Permissões de API de aplicativo](./media/how-to-manage-authentication/app-permissions.png)

3. Marque **acessar Azure Maps** e clique em **adicionar permissões**.

    ![Selecionar permissões de API de aplicativo](./media/how-to-manage-authentication/select-app-permissions.png)

4. Conclua a etapa a ou b, dependendo do seu método de autenticação. 

    1. Se seu aplicativo usar a autenticação de token de usuário com o SDK da Web do Azure Maps, habilite `oauth2AllowImplicitFlow`. Para habilitar `oauth2AllowImplicitFlow`, defina-a como true na seção de manifesto do registro do aplicativo. 
    
       ![Manifesto de aplicativo](./media/how-to-manage-authentication/app-manifest.png)

    2. Se seu aplicativo usar a autenticação de servidor/aplicativo, vá para a folha **certificados & segredos** na página de registro do aplicativo e crie uma senha clicando em **novo segredo do cliente** ou carregar um certificado de chave pública para o registro do aplicativo. Se você criar uma senha, depois de clicar em **Adicionar**, copie a senha para mais tarde e armazene-a com segurança. Você usará essa senha para adquirir tokens do Azure AD.

       ![Chaves do aplicativo](./media/how-to-manage-authentication/app-keys.png)

       ![Adicionar chave](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-rbac-to-azure-maps"></a>Conceder RBAC (controle de acesso baseado em função) para mapas do Azure

Depois de associar uma conta do Azure Maps ao seu locatário do Azure AD, você pode conceder controle de acesso. Você concede o controle de acesso atribuindo um usuário, grupo ou aplicativo a uma ou mais funções de controle de acesso do Azure Maps.

1. Vá para sua **conta do Azure Maps**. Selecione **controle de acesso (iam)** e, em seguida, selecione **atribuição de função**.

    ![Conceder RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Na janela **atribuição de função** , em **função**, selecione **leitor de data do Azure Maps (versão prévia)** . Em **atribuir acesso para** selecionar o **usuário, grupo ou entidade de serviço do Azure ad**. Selecione o usuário ou aplicativo. Clique em **Salvar**.

    ![Adicionar atribuição de função](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Exibir funções disponíveis de RBAC do Azure Mapas

Para exibir funções RBAC (controle de acesso baseado em função) que estão disponíveis para mapas do Azure, vá para **controle de acesso (iam)** , selecione **funções**e, em seguida, pesquise funções começando com o **Azure Maps**. Essas funções são as funções às quais você pode conceder acesso.

![Visualizar funções disponíveis](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Exibir RBAC do Azure Maps

O RBAC fornece controle de acesso granular.

Para exibir usuários e aplicativos que receberam o RBAC para mapas do Azure, vá para **controle de acesso (iam)** , selecione **atribuições de função**e filtre por **mapas do Azure**.

![Exibir usuários e aplicativos com o RBAC concedido](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Solicitar tokens para o Azure Mapas

Depois de registrar seu aplicativo e associo-lo ao Azure Maps, você pode solicitar tokens de acesso.

* Se seu aplicativo usar a autenticação de token de usuário com o SDK da Web do Azure Maps, você precisará configurar sua página HTML com a ID do cliente do Azure Maps e a ID do aplicativo do Azure AD.

* Se seu aplicativo usar a autenticação de servidor/aplicativo, você precisará solicitar um token do ponto de extremidade de token do Azure AD `https://login.microsoftonline.com` com a ID de recurso do Azure AD `https://atlas.microsoft.com/`, a ID do cliente do Azure Maps, a ID do aplicativo do Azure AD e a senha ou o certificado de registro do aplicativo do Azure AD.

| Azure Environment   | Ponto de extremidade de token do Azure AD | ID do recurso do Azure |
| --------------------|-------------------------|-------------------|
| Público do Azure        | https://login.microsoftonline.com | https://atlas.microsoft.com/ |
| Azure Governamental    | https://login.microsoftonline.us  | https://atlas.microsoft.com/ | 

Para obter mais informações sobre como solicitar tokens de acesso do Azure AD, para usuários e entidades de serviço, consulte [cenários de autenticação do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Próximos passos

Para saber mais sobre a autenticação do Azure AD e o SDK da Web do Azure Mapas, consulte [Azure AD e o SDK da Web do Azure Mapas](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Saiba como ver as métricas de uso da API para sua conta do Azure Mapas:
> [!div class="nextstepaction"] 
> [ Exibir métricas de uso](how-to-view-api-usage.md)

Para obter uma lista de exemplos que mostram como integrar o Azure Active Directory (AAD) com o Azure Maps, consulte:

> [!div class="nextstepaction"]
> [Exemplos de autenticação do Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
