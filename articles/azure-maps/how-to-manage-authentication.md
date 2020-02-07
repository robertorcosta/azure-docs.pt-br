---
title: Gerenciar autenticação | Mapas do Microsoft Azure
description: Use o portal do Azure para gerenciar a autenticação no Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 30eb637e9117818758ed4ab0e3adef9db29cc698
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057304"
---
# <a name="manage-authentication-in-azure-maps"></a>Gerenciar autenticação no Azure Mapas

Depois de criar uma conta do Azure Maps, uma ID do cliente e as chaves são criadas para dar suporte à autenticação do Azure Active Directory (Azure AD) e à autenticação de chave compartilhada.

## <a name="view-authentication-details"></a>Exibir detalhes de autenticação

Depois de criar uma conta do Azure Maps, as chaves primária e secundária são geradas. Recomendamos que você use uma chave primária como uma chave de assinatura ao [usar a autenticação de chave compartilhada para chamar o Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). Você pode usar uma chave secundária em cenários como alterações de chave sem interrupção. Para obter mais informações, consulte [autenticação no Azure Maps](https://aka.ms/amauth).

Você pode exibir os detalhes de autenticação no portal do Azure. Lá, em sua conta, no menu **configurações** , selecione **autenticação**.

![Detalhes de autenticação](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>Registrar e configurar um aplicativo do Azure AD

1. Na portal do Azure, na lista de serviços do Azure, selecione **Azure Active Directory** > **registros de aplicativo** > **novo registro**.  

    ![Registro do aplicativo](./media/how-to-manage-authentication/app-registration.png)

1. Se você já registrou seu aplicativo, continue na próxima etapa. Se você ainda não registrou seu aplicativo, insira um **nome**, escolha um **tipo de conta de suporte**e, em seguida, selecione **registrar**.  

    ![Detalhes de registro do aplicativo](./media/how-to-manage-authentication/app-create.png)

1. Para atribuir permissões de API delegadas para mapas do Azure, vá para o aplicativo. Em **registros de aplicativo**, selecione **permissões de API** > **Adicionar uma permissão**. Em **APIs que minha organização usa**, pesquise e selecione **mapas do Azure**.

    ![Adicionar permissões de API de aplicativo](./media/how-to-manage-authentication/app-permissions.png)

1. Marque a caixa de seleção ao lado de **acessar mapas do Azure**e, em seguida, selecione **adicionar permissões**.

    ![Selecionar permissões de API de aplicativo](./media/how-to-manage-authentication/select-app-permissions.png)

1. Conclua uma das etapas a seguir, dependendo do seu método de autenticação. 

    * Se seu aplicativo usar a autenticação de token de usuário com o SDK da Web do Azure Maps, habilite `oauth2AllowImplicitFlow`. Para habilitá-lo, na seção **manifesto** do registro do aplicativo, defina `oauth2AllowImplicitFlow` como true. 
    
       ![Manifesto de aplicativo](./media/how-to-manage-authentication/app-manifest.png)

    * Se seu aplicativo usar a autenticação de servidor ou aplicativo, em sua página de registro do aplicativo, acesse **certificados & segredos**. Em seguida, carregue um certificado de chave pública ou crie uma senha selecionando **novo segredo do cliente**. 
    
       ![Criar um segredo do cliente](./media/how-to-manage-authentication/app-keys.png)

        Se você criar uma senha, depois de selecionar **Adicionar**, copie a senha e armazene-a com segurança. Você usará essa senha para obter tokens do Azure AD.

       ![Adicionar um segredo do cliente](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>Conceder controle de acesso baseado em função para mapas do Azure

Depois de associar uma conta do Azure Maps ao seu locatário do Azure AD, você pode conceder controle de acesso. Conceda o RBAC ( *controle de acesso baseado em função* ) atribuindo um usuário, grupo ou aplicativo a uma ou mais funções de controle de acesso do Azure Maps. 

1. Vá para sua **conta do Azure Maps**. Selecione **iam (controle de acesso)**  > **atribuição de função**.

    ![Conceder RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. Na guia **atribuições de função** , em **função**, selecione **leitor de data do Azure Maps (versão prévia)** . Em **Atribuir acesso a**, selecione **Usuário, grupo ou entidade de serviço do Azure AD**. Selecione o usuário ou aplicativo. Em seguida, selecione **Salvar**.

    ![Adicionar atribuição de função](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Exibir funções disponíveis de RBAC do Azure Mapas

Para exibir as funções RBAC que estão disponíveis para mapas do Azure, vá para **controle de acesso (iam)** . Selecione **funções**e, em seguida, procure funções que começam com o *Azure Maps*. Essas funções do Azure Maps são as funções às quais você pode conceder acesso.

![Visualizar funções disponíveis](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Exibir RBAC do Azure Maps

O RBAC fornece controle de acesso granular.

Para exibir usuários e aplicativos que receberam o RBAC para mapas do Azure, vá para **controle de acesso (iam)** . Lá, selecione **atribuições de função**e, em seguida, filtre por **mapas do Azure**.

![Exibir usuários e aplicativos que receberam o RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Solicitar tokens para o Azure Mapas

Depois de registrar seu aplicativo e associá-lo ao Azure Maps, você pode solicitar tokens de acesso.

Se seu aplicativo usar a autenticação de token de usuário com o SDK da Web do Azure Maps, configure sua página HTML com a ID do cliente do Azure Maps e a ID do aplicativo do Azure AD.

Se seu aplicativo usar a autenticação de servidor ou aplicativo, solicite um token do ponto de extremidade de token do Azure AD `https://login.microsoftonline.com`. Em sua solicitação, use os seguintes detalhes: 

* ID de recurso do Azure AD `https://atlas.microsoft.com/`
* ID do cliente do Azure Maps
* ID do aplicativo do Azure AD
* Senha ou certificado de registro do aplicativo do Azure AD

| Ambiente do Azure   | Ponto de extremidade de token do Azure AD | ID de recurso do Azure |
| --------------------|-------------------------|-------------------|
| Nuvem pública do Azure        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Nuvem do Azure governamental   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

Para obter mais informações sobre como solicitar tokens de acesso do Azure AD para usuários e entidades de serviço, consulte [cenários de autenticação do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [Azure AD e SDK da Web do Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Localize as métricas de uso da API para sua conta do Azure Maps:
> [!div class="nextstepaction"] 
> [ Exibir métricas de uso](how-to-view-api-usage.md)

Explore os exemplos que mostram como integrar o Azure AD ao Azure Maps:

> [!div class="nextstepaction"]
> [Exemplos de autenticação do Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
