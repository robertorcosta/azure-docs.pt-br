---
title: Adicionar funções de aplicativo e obtê-las de um token | Azure
titleSuffix: Microsoft identity platform
description: Saiba como adicionar funções de aplicativo a um aplicativo registrado no Azure Active Directory, atribuir usuários e grupos a essas funções e recebê-las na declaração ' Roles ' no token.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2020
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 02fe3c1ebc893dea259abcda3ea1d13ab96d68d5
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706006"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Como fazer: adicionar funções de aplicativo em seu aplicativo e recebê-las no token

O RBAC (controle de acesso baseado em função) é um mecanismo comum usado para impor a autorização em aplicativos. Usando o RBAC, o administrador concede permissões a funções, e não a usuários ou grupos individuais. O administrador pode, então, atribuir funções a diferentes usuários e grupos para controlar quem tem acesso a qual conteúdo e funcionalidade.

Usando o RBAC com funções de aplicativo e declarações de função, os desenvolvedores podem impor com segurança a autorização em seus aplicativos com menos esforço em sua parte.

Outra abordagem é usar grupos e declarações de grupo do Azure AD, conforme mostrado no exemplo de código [webapp-GroupClaims-dotnet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet) no github. Grupos e funções de aplicativo do Azure AD não são mutuamente exclusivos; Eles podem ser usados em conjunto para fornecer um controle de acesso ainda mais refinado.

## <a name="declare-roles-for-an-application"></a>Declarar funções para um aplicativo

As funções de aplicativo são definidas no [portal do Azure](https://portal.azure.com).  Quando um usuário entra no aplicativo, o Azure AD emite uma declaração `roles` para cada função que o usuário recebeu, tanto para o usuário individualmente quanto no caso de funções provenientes de sua associação a um grupo.  A atribuição de usuários e grupos a funções pode ser feita por meio da interface do usuário do portal ou de maneira programática usando o [Microsoft Graph](/graph/azuread-identity-access-management-concept-overview).

Para criar uma função de aplicativo:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o **diretório +** filtro de assinatura no menu superior e escolha o locatário Azure Active Directory que contém o registro do aplicativo ao qual você deseja adicionar uma função de aplicativo.
1. Pesquise **Azure Active Directory** e selecione-o.
1. Em **gerenciar**, selecione **registros de aplicativo**e, em seguida, selecione o aplicativo no qual você deseja definir funções de aplicativo.
1. Selecionar **funções de aplicativo | Visualize**e, em seguida, selecione **criar função de aplicativo**.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Um painel de funções de aplicativo do registro de aplicativo no portal do Azure":::
1. No painel **criar função de aplicativo** , insira as configurações para a função. A tabela a seguir descreve cada configuração e seus parâmetros.

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="Um painel de contexto criar funções de aplicativo do registro de aplicativo no portal do Azure":::

    | Campo | Descrição | Exemplo |
    |-------|-------------|---------|
    | **Nome de exibição** | Nome de exibição para a função de aplicativo que aparece no consentimento do administrador e experiências de atribuição de aplicativo. Esse valor pode conter espaços.  | `Survey Writer` |
    | **Tipos de membro permitidos** | Especifica se essa função de aplicativo pode ser atribuída a usuários, aplicativos ou ambos. | `Users/Groups` |
    | **Valor** | Especifica o valor da declaração de funções que o aplicativo deve esperar no token. O valor deve corresponder exatamente à cadeia de caracteres referenciada no código do aplicativo. O valor não pode conter espaços. | `Survey.Create` |
    | **Descrição** | Uma descrição mais detalhada da função de aplicativo exibida durante as experiências de atribuição e consentimento do aplicativo de administração. | `Writers can create surveys.` |
    | **Deseja habilitar esta função de aplicativo?** | Se a função de aplicativo está habilitada. Para excluir uma função de aplicativo, desmarque essa caixa de seleção e aplique a alteração antes de tentar a operação de exclusão. | *Verificado* |

1. Selecione **Aplicar** para salvar as alterações.

> [!NOTE]
> O número de funções que você adiciona conta em relação aos limites definidos para um manifesto do aplicativo. Para obter informações sobre esses limites, consulte a seção de  [limites de manifesto](./reference-app-manifest.md#manifest-limits) de [Azure Active Directory referência de manifesto de aplicativo](reference-app-manifest.md).

## <a name="assign-users-and-groups-to-roles"></a>Atribuir usuários e grupos a funções

Depois de adicionar funções de aplicativo em seu aplicativo, você pode atribuir usuários e grupos às funções.

1. Em **Azure Active Directory** na portal do Azure, selecione **aplicativos empresariais** no menu de navegação à esquerda.
1. Selecione **Todos os aplicativos** para exibir uma lista com todos os seus aplicativos.

     Se o seu aplicativo não aparecer na lista, use os filtros na parte superior da lista **todos os aplicativos** para restringir a lista ou role para baixo na lista para localizar seu aplicativo.

1. Selecione o aplicativo no qual deseja atribuir funções a usuários ou grupos de segurança.
1. Em **Gerenciar**, selecione **Usuários e grupos**.
1. Selecione **Adicionar usuário** para abrir o painel **Adicionar atribuição** .
1. Clique no seletor **Usuários e grupos** do painel **Adicionar Atribuição**.

     Uma lista de usuários e grupos de segurança é exibida. Você pode pesquisar um determinado usuário ou grupo, bem como selecionar vários usuários e grupos que aparecem na lista.

1. Depois de selecionar usuários e grupos, selecione o botão **selecionar** para continuar.
1. Selecione **selecionar função** no painel **Adicionar atribuição** . Todas as funções que você definiu para o aplicativo são exibidas.
1. Escolha uma função e selecione o botão **selecionar** .
1. Selecione o botão **atribuir** para concluir a atribuição de usuários e grupos ao aplicativo.
1. Confirme se os usuários e grupos que você adicionou são exibidos na lista **usuários e grupos** .

## <a name="receive-roles-in-tokens"></a>Receber funções em tokens

Quando os usuários atribuídos às várias funções de aplicativo entrarem no aplicativo, seus tokens terão suas funções atribuídas na `roles` declaração.

## <a name="web-api-application-permissions"></a>Permissões de aplicativo da API Web

Você pode definir funções de aplicativo direcionadas a **usuários/grupos**, **aplicativos**ou **ambos**. Quando você seleciona **aplicativos** ou **ambos**, a função de aplicativo é exibida como uma permissão de aplicativo nas **permissões de API**de um aplicativo cliente.

Para selecionar a permissão de aplicativo em um registro de aplicativo cliente depois de definir uma função que se destina a **aplicativos** ou **ambos**:

1. Em **Azure Active Directory** no portal do Azure, selecione **registros de aplicativo**e, em seguida, selecione o registro do aplicativo cliente.
1. Em **Gerenciar**, selecione **Permissões de API**.
1. Selecione **Adicionar uma permissão**  >  **minhas APIs**.
1. Selecione o aplicativo ao qual você adicionou a função de aplicativo e, em seguida, selecione **permissões de aplicativo**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as funções de aplicativo com os seguintes recursos:

- Exemplo de código: [Adicionar autorização usando funções de aplicativo & declarações de funções para um aplicativo web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles) (github)
- Vídeo: [implementar a autorização em seus aplicativos com a plataforma de identidade da Microsoft ](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15)
- [Manifesto de aplicativo do Azure Active Directory](./reference-app-manifest.md)
- [Tokens de acesso do AD do Azure](access-tokens.md)
- [Azure AD `id_tokens`](id-tokens.md)
