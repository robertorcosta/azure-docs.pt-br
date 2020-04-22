---
title: Restringir o aplicativo Azure AD a um conjunto de usuários | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a restringir o acesso a aplicativos registrados no Azure AD a um conjunto selecionado de usuários.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8bdc7e6e3795719128a8ecfb1e8bc97c1a9a08c7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759037"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Como: Restringir seu aplicativo Azure AD a um conjunto de usuários em um inquilino AD do Azure

Aplicativos registrados em um locatário do Azure Active Directory (Azure AD) estão, por padrão, disponíveis para todos os usuários do locatário que se autenticar com êxito.

Da mesma forma, no caso de um aplicativo [multilocatário](howto-convert-app-to-be-multi-tenant.md), todos os usuários no locatário do Azure AD em que esse aplicativo é provisionado conseguirão acessá-lo após a autenticação bem-sucedida em seu respectivo locatário.

Os desenvolvedores e administradores de locatários geralmente têm requisitos, como aplicativos que devem ser restritos a um determinado conjunto de usuários. Os desenvolvedores podem fazer o mesmo usando padrões de autorização conhecidos, como RBAC (Controle de Acesso Baseado em Função), mas essa abordagem exige uma quantidade significativa de trabalho por parte do desenvolvedor.

Administradores e desenvolvedores inquilinos podem restringir um aplicativo a um conjunto específico de usuários ou grupos de segurança no inquilino usando este recurso incorporado do Azure AD também.

## <a name="supported-app-configurations"></a>Configurações de aplicativo com suporte

A opção de restringir um aplicativo a um conjunto específico de usuários ou grupos de segurança em um locatário funciona com os seguintes tipos de aplicativos:

- Aplicativos configurados para logon único federado com autenticação baseada em SAML
- Aplicativos de proxy de aplicativo que usam a pré-autenticação do Azure AD
- Aplicativos criados diretamente na plataforma de aplicativos do Azure AD que usam a autenticação OAuth 2.0/OpenID Connect depois que um usuário ou administrador autoriza o aplicativo.

     > [!NOTE]
     > Esse recurso está disponível para aplicativos Web/API Web e aplicativos corporativos apenas. Aplicativos que são registrados como [nativos](quickstart-v1-integrate-apps-with-azure-ad.md) não podem ficar restritos a um conjunto de usuários ou grupos de segurança no locatário.

## <a name="update-the-app-to-enable-user-assignment"></a>Atualizar o aplicativo para habilitar a atribuição de usuários

Existem duas maneiras de criar um aplicativo com atribuição de usuário habilitada. Um requer a função **de Administrador Global,** o segundo não.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Aplicativos corporativos (requer a função administrador global)

1. Acesse o [**portal Azure**](https://portal.azure.com/) e faça login como **administrador global.**
1. Na barra superior, selecione a conta conectada. 
1. Em **Diretório**, escolha o locatário do Azure AD onde o aplicativo está registrado.
1. Escolha **Azure Active Directory** no menu de navegação à esquerda. Se o Azure Active Directory não estiver disponível no painel de navegação, siga estas etapas:

    1. Na parte superior do menu principal à esquerda, marque **Todos os serviços**.
    1. Digite **o Diretório Ativo do Azure** na caixa de pesquisa do filtro e selecione o item do Diretório Ativo do **Azure** no resultado.

1. No painel diretório ativo do **Azure,** selecione **Aplicativos Corporativos** no menu de navegação à mão esquerda **do Azure Active Directory.**
1. Selecione **Todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

     Se não vir o aplicativo desejado na lista, use os diferentes filtros na parte superior da lista **Todos os aplicativos** para restringir a lista ou percorra a lista para localizar o aplicativo.

1. Na lista, escolha o aplicativo ao qual deseja atribuir um usuário ou grupo de segurança.
1. Na página **Visão geral** do aplicativo, selecione **Propriedades** no menu de navegação à esquerda do aplicativo.
1. Localize a configuração **Atribuição de usuário obrigatória?** e defina-a como **Sim**. Quando esta opção for definida como **Sim,** os usuários do inquilino devem primeiro ser atribuídos a este aplicativo ou não poderão fazer login neste aplicativo.
1. Escolha **Salvar** para salvar essa alteração da configuração.

### <a name="app-registration"></a>Registro do aplicativo

1. Vá para o [**portal Azure.**](https://portal.azure.com/)
1. Na barra superior, selecione a conta conectada. 
1. Em **Diretório**, escolha o locatário do Azure AD onde o aplicativo está registrado.
1. Escolha **Azure Active Directory** no menu de navegação à esquerda.
1. No painel diretório ativo do **Azure,** selecione Registros de **aplicativos** no menu de navegação à esquerda do Diretório Ativo do **Azure.**
1. Crie ou selecione o aplicativo que deseja gerenciar. Você precisa ser **proprietário** deste registro de aplicativo.
1. Na página **Visão Geral** do aplicativo, siga o aplicativo Gerenciado no link de **diretório local** sob o essencial na parte superior da página. Isso o levará ao _aplicativo corporativo gerenciado_ do seu registro de aplicativo.
1. A partir da lâmina de navegação à esquerda, selecione **Propriedades**.
1. Localize a configuração **Atribuição de usuário obrigatória?** e defina-a como **Sim**. Quando esta opção for definida como **Sim,** os usuários do inquilino devem primeiro ser atribuídos a este aplicativo ou não poderão fazer login neste aplicativo.
1. Escolha **Salvar** para salvar essa alteração da configuração.

## <a name="assign-users-and-groups-to-the-app"></a>Atribuir usuários e grupos ao aplicativo

Após configurar seu aplicativo para habilitar a atribuição de usuários, prossiga e atribua usuários e grupos ao aplicativo.

1. Escolha o painel **Usuários e grupos** no menu de navegação esquerdo do aplicativo.
1. Na parte superior da lista **Usuários e grupos**, escolha o botão **Adicionar usuário** para abrir o painel **Adicionar Atribuição**.
1. Clique no seletor **Usuários** do painel **Adicionar Atribuição**. 

     Uma lista de usuários e grupos de segurança será exibida em conjunto com uma caixa de texto para pesquisar e localizar um determinado usuário ou grupo. Esta tela permite que você escolha vários usuários e grupos de uma só vez.

1. Após terminar de escolher os usuários e grupos, pressione o botão **Selecionar** na parte inferior para passar para a próxima parte.
1. (Opcional) Se você tiver definido as funções do Aplicativo em seu aplicativo, você pode usar a opção **Selecionar função** para atribuir os usuários e grupos selecionados a uma das funções do aplicativo. 
1. Pressione o botão **Atribuir** na parte inferior para concluir as atribuições de usuários e grupos ao aplicativo. 
1. Confirme que os usuários e grupos que você adicionou estão aparecendo na lista atualizada de **Usuários e grupos**.

## <a name="more-information"></a>Mais informações

- [Como: Adicionar funções de aplicativo em seu aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
- [Adicionar autorização usando funções de aplicativo & as funções reivindicaa a um aplicativo web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Usando grupos de segurança e funções de aplicativo em seus aplicativos (vídeo)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory, agora com declarações de grupo e funções de aplicativo](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Manifesto do aplicativo do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)