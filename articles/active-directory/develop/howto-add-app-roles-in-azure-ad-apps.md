---
title: Adicionar funções de aplicativo e obtê-las de um token | Azure
titleSuffix: Microsoft identity platform
description: Saiba como adicionar funções de aplicativo em um aplicativo registrado no Azure Active Directory, atribuir usuários e grupos a essas funções e recebê-las na declaração `roles` no token.
services: active-directory
documentationcenter: ''
author: kkrishna
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 805f984ebdb5e95a9f3a46fab010cecf5c811547
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424489"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Como adicionar funções de aplicativo em seu aplicativo e recebê-las no token

O RBAC (controle de acesso baseado em função) é um mecanismo comum usado para impor a autorização em aplicativos. Usando o RBAC, o administrador concede permissões a funções, e não a usuários ou grupos individuais. O administrador pode, então, atribuir funções a diferentes usuários e grupos para controlar quem tem acesso a qual conteúdo e funcionalidade.

Usando o RBAC com Declarações de função e Funções de aplicativo, os desenvolvedores podem impor, de forma segura e com pouco esforço, a autorização em seus aplicativos.

Outra abordagem é usar os Grupos do Azure AD e as Declarações de grupo, conforme mostrado em [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet). As Funções de aplicativo e os Grupos do Azure AD não são mutuamente excludentes e podem ser usados em conjunto para fornecer um controle de acesso ainda mais refinado.

## <a name="declare-roles-for-an-application"></a>Declarar funções para um aplicativo

Essas funções de aplicativo são definidas no [portal do Azure](https://portal.azure.com), no manifesto de registro do aplicativo.  Quando um usuário entra no aplicativo, o Azure AD emite uma declaração `roles` para cada função que o usuário recebeu, tanto para o usuário individualmente quanto no caso de funções provenientes de sua associação a um grupo.  A atribuição de usuários e grupos a funções pode ser feita por meio da interface do usuário do portal ou de maneira programática usando o [Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview).

### <a name="declare-app-roles-using-azure-portal"></a>Declarar funções de aplicativo usando o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com).
1. Na barra superior, selecione sua conta e, em seguida, **Mudar diretório**.
1. Quando o painel **Diretório + assinatura** abrir, escolha o locatário do Active Directory em que deseja registrar o aplicativo, na lista **Favoritos** ou **Todos os Diretórios**.
1. Selecione **Todos os serviços** no navegador esquerdo e escolha **Azure Active Directory**.
1. No painel **Azure Active Directory**, selecione **Registros de aplicativo** para exibir uma lista com todos os seus aplicativos.
1. Selecione o aplicativo no qual deseja definir funções. Em seguida, selecione **manifesto**.
1. Edite o manifesto do aplicativo localizando a configuração `appRoles` e adicionando todas as suas Funções de aplicativo.

     > [!NOTE]
     > Cada definição de função de aplicativo neste manifesto deve ter um GUID válido diferente para a propriedade `id`. 
     > 
     > A propriedade `value` de cada definição de função de aplicativo deve corresponder exatamente às cadeias de caracteres usadas no código no aplicativo. A propriedade `value` não pode conter espaços. Se tiver, você receberá um erro ao salvar o manifesto.
     
1. Salve o manifesto.

### <a name="examples"></a>Exemplos

O exemplo a seguir mostra o `appRoles` que você pode atribuir a `users`.

> [!NOTE]
>O `id` precisa ser um GUID exclusivo.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-98f8-45fd-aa4a-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

> [!NOTE]
>O `displayName` não pode conter espaços.

Você pode definir funções de aplicativo para o `users` de destino, para `applications` ou ambos. Quando disponíveis para `applications`, as funções de aplicativo aparecem como permissões de aplicativo na folha **Permissões Necessárias**. O exemplo a seguir mostra uma função de aplicativo destinada a um `Application`.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-859a-4941-89c9-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

O número de funções definidas afeta os limites que o manifesto do aplicativo tem. Eles foram discutidos em detalhes na página [limites do manifesto](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-limits) .

### <a name="assign-users-and-groups-to-roles"></a>Atribuir usuários e grupos a funções

Após adicionar funções de aplicativo em seu aplicativo, você pode atribuir usuários e grupos a essas funções.

1. No painel **Azure Active Directory**, selecione **Aplicativos empresariais** no menu de navegação esquerdo do **Azure Active Directory**.
1. Selecione **Todos os aplicativos** para exibir uma lista com todos os seus aplicativos.

     Se não vir o aplicativo desejado na lista, use os diferentes filtros na parte superior da lista **Todos os aplicativos** para restringir a lista ou percorra a lista para localizar o aplicativo.

1. Selecione o aplicativo no qual deseja atribuir funções a usuários ou grupos de segurança.
1. Escolha o painel **Usuários e grupos** no menu de navegação esquerdo do aplicativo.
1. Na parte superior da lista **Usuários e grupos**, escolha o botão **Adicionar usuário** para abrir o painel **Adicionar Atribuição**.
1. Clique no seletor **Usuários e grupos** do painel **Adicionar Atribuição**.

     Uma lista de usuários e grupos de segurança será exibida em conjunto com uma caixa de texto para pesquisar e localizar um determinado usuário ou grupo. Esta tela permite que você escolha vários usuários e grupos de uma só vez.

1. Após terminar de escolher os usuários e grupos, pressione o botão **Selecionar** na parte inferior para passar para a próxima parte.
1. Escolha o seletor **Selecionar Função** no painel **Adicionar atribuição**. Todas as funções declaradas anteriormente no manifesto do aplicativo serão exibidas.
1. Escolha uma função e pressione o botão **Selecionar**.
1. Pressione o botão **Atribuir** na parte inferior para concluir as atribuições de usuários e grupos ao aplicativo.
1. Confirme que os usuários e grupos que você adicionou estão aparecendo na lista atualizada de **Usuários e grupos**.

## <a name="more-information"></a>Mais informações

- [Autorização em um aplicativo Web usando declarações de função &amp; nas funções de aplicativo do Azure AD (exemplo)](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims)
- [Usando grupos de segurança e funções de aplicativo em seus aplicativos (vídeo)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory, agora com declarações de grupo e funções de aplicativo](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Manifesto de aplicativo do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [Tokens de acesso do AAD](access-tokens.md)
- [AAD `id_tokens`](id-tokens.md)
