---
title: Gerenciar autenticação | Mapas do Microsoft Azure
description: Use o portal Azure para gerenciar a autenticação no Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: dfe73971f29ea362fdd0ddd654e705b622ab1866
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335540"
---
# <a name="manage-authentication-in-azure-maps"></a>Gerenciar autenticação no Azure Mapas

Depois de criar uma conta do Azure Maps, um ID do cliente e chaves são criados para suportar a autenticação do Azure Active Directory (Azure AD) e a autenticação de chaves compartilhadas.

## <a name="view-authentication-details"></a>Exibir detalhes de autenticação

Depois de criar uma conta do Azure Maps, as chaves primárias e secundárias são geradas. Recomendamos que você use uma chave primária como chave de assinatura quando [você usa autenticação de tecla compartilhada para chamar o Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). Você pode usar uma chave secundária em cenários como rolar mudanças de tecla. Para obter mais informações, consulte [Autenticação no Azure Maps](https://aka.ms/amauth).

Você pode ver seus detalhes de autenticação no portal Azure. Lá, em sua conta, no menu **Configurações,** selecione **Autenticação**.

![Detalhes de autenticação](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>Registre-se e configure um aplicativo AD do Azure

1. No portal Azure, na lista de serviços do Azure, selecione > **registros** > do Aplicativo de **Diretório Ativo do Azure****Novo registro**.  

    ![Registro do aplicativo](./media/how-to-manage-authentication/app-registration.png)

1. Se você já registrou seu aplicativo, continue para o próximo passo. Se você não registrou seu aplicativo, digite um **Nome,** escolha um **tipo de conta de suporte**e selecione **'Registrar 'Registro'.**  

    ![Detalhes de registro de aplicativos](./media/how-to-manage-authentication/app-create.png)

1. Para atribuir permissões de API delegadas ao Azure Maps, vá para o aplicativo. Em seguida, em **registros do aplicativo,** selecione **permissões** > de API**Adicione uma permissão**. Em **APIs que minha organização usa,** procure e selecione **Mapas Azure**.

    ![Adicionar permissões de API de aplicativos](./media/how-to-manage-authentication/app-permissions.png)

1. Selecione a caixa de seleção ao lado **do Access Azure Maps**e, em seguida, **selecione Adicionar permissões**.

    ![Selecione permissões de API de aplicativos](./media/how-to-manage-authentication/select-app-permissions.png)

1. Complete uma das seguintes etapas, dependendo do seu método de autenticação. 

    * Se o aplicativo usar autenticação de token de usuário com o `oauth2AllowImplicitFlow`Azure Maps Web SDK, então habilitar . Para habilitá-lo, na seção `oauth2AllowImplicitFlow` **Manifesto** do seu registro de aplicativo, definido como verdadeiro. 
    
       ![Manifesto de aplicativo](./media/how-to-manage-authentication/app-manifest.png)

    * Se o aplicativo usar autenticação de servidor ou aplicativo, então na página de registro do aplicativo, vá para **Certificados & segredos**. Em seguida, faça upload de um certificado de chave pública ou crie uma senha selecionando **novo segredo de cliente**. 
    
       ![Criar um segredo do cliente](./media/how-to-manage-authentication/app-keys.png)

        Se você criar uma senha, depois de selecionar **Adicionar,** copie a senha e armazene-a com segurança. Você usará esta senha para obter tokens do Azure AD.

       ![Adicione um segredo do cliente](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>Conceda controle de acesso baseado em papéis ao Azure Maps

Depois de associar uma conta do Azure Maps com seu inquilino Azure AD, você pode conceder o controle de acesso. Você concede *rbac (Role-based Access Control, controle de acesso baseado em função)* atribuindo um usuário, grupo ou aplicativo a uma ou mais funções de controle de acesso do Azure Maps. 

1. Vá para **sua conta do Azure Maps**. Selecione a**atribuição de função**Controle de acesso **(IAM).** > 

    ![Conceder RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. Na guia **''Atribuições de função',** em **Função**, selecione **Azure Maps Date Reader (Preview)**. Em **Atribuir acesso a**, selecione **Usuário, grupo ou entidade de serviço do Azure AD**. Selecione o usuário ou aplicativo. Em seguida, **selecione Salvar**.

    ![Adicionar atribuição de função](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Exibir funções disponíveis de RBAC do Azure Mapas

Para ver as funções RBAC disponíveis no Azure Maps, vá para **access control (IAM)**. Selecione **Funções**e procure por funções que começam com *o Azure Maps*. Essas funções do Azure Maps são as funções às as que você pode conceder acesso.

![Visualizar funções disponíveis](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Ver Mapas do Azure RBAC

O RBAC fornece controle de acesso granular.

Para visualizar usuários e aplicativos que receberam RBAC para o Azure Maps, vá para **Access Control (IAM)**. Lá, selecione **As atribuições de função**e, em seguida, filtre pelo **Azure Maps**.

![Veja usuários e aplicativos que receberam RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Solicitar tokens para o Azure Mapas

Depois de registrar seu aplicativo e associá-lo ao Azure Maps, você pode solicitar tokens de acesso.

Se o aplicativo usar a autenticação do token do usuário com o Azure Maps Web SDK, configure sua página HTML com o ID do cliente do Azure Maps e o ID do aplicativo Azure AD.

Se o aplicativo usar autenticação de servidor ou aplicativo, solicite um token do ponto `https://login.microsoftonline.com`final do token Azure AD . Em sua solicitação, use os seguintes detalhes: 

* ID de recurso Azure AD`https://atlas.microsoft.com/`
* ID do cliente do Azure Maps
* ID do aplicativo do Azure AD
* Senha ou certificado de registro do aplicativo Azure AD

| Ambiente azure   | Ponto final do token Azure AD | ID de recurso do Azure |
| --------------------|-------------------------|-------------------|
| Nuvem pública azure        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Nuvem do governo Azure   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

Para obter mais informações sobre a solicitação de tokens de acesso do Azure AD para usuários e diretores de serviços, consulte [cenários de autenticação para a Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [Azure AD e Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Encontre as métricas de uso da API para sua conta do Azure Maps:
> [!div class="nextstepaction"] 
> [Obter métricas de uso](how-to-view-api-usage.md)

Explorar amostras que mostram como integrar o Azure AD com o Azure Maps:

> [!div class="nextstepaction"]
> [Amostras de autenticação Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
