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
ms.openlocfilehash: cccd2df334828c0b8103e4da2ffcd8549673b69c
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696989"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users"></a>Como restringir seu aplicativo do Azure AD a um conjunto de usuários

Aplicativos registrados em um locatário do Azure Active Directory (Azure AD) estão, por padrão, disponíveis para todos os usuários do locatário que se autenticar com êxito.

Da mesma forma, no caso de um aplicativo [multilocatário](howto-convert-app-to-be-multi-tenant.md), todos os usuários no locatário do Azure AD em que esse aplicativo é provisionado conseguirão acessá-lo após a autenticação bem-sucedida em seu respectivo locatário.

Os desenvolvedores e administradores de locatários geralmente têm requisitos, como aplicativos que devem ser restritos a um determinado conjunto de usuários. Os desenvolvedores podem fazer o mesmo usando padrões de autorização conhecidos, como RBAC (Controle de Acesso Baseado em Função), mas essa abordagem exige uma quantidade significativa de trabalho por parte do desenvolvedor.

O Azure AD permite que os desenvolvedores e administradores de locatários restrinjam um aplicativo a um conjunto específico de usuários ou grupos de segurança no locatário.

## <a name="supported-app-configurations"></a>Configurações de aplicativo com suporte

A opção de restringir um aplicativo a um conjunto específico de usuários ou grupos de segurança em um locatário funciona com os seguintes tipos de aplicativos:

- Aplicativos configurados para logon único federado com autenticação baseada em SAML
- Aplicativos de proxy de aplicativo que usam a pré-autenticação do Azure AD
- Aplicativos criados diretamente na plataforma de aplicativos do Azure AD que usam a autenticação OAuth 2.0/OpenID Connect depois que um usuário ou administrador autoriza o aplicativo.

     > [!NOTE]
     > Esse recurso está disponível para aplicativos Web/API Web e aplicativos corporativos apenas. Aplicativos que são registrados como [nativos](quickstart-v1-integrate-apps-with-azure-ad.md) não podem ficar restritos a um conjunto de usuários ou grupos de segurança no locatário.

## <a name="update-the-app-to-enable-user-assignment"></a>Atualizar o aplicativo para habilitar a atribuição de usuários

Há duas maneiras de criar um aplicativo com atribuição de usuário habilitada. Uma delas requer a função de **administrador global** , a segunda não.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Aplicativos empresariais (requer a função de administrador global)

1. Vá para a [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global**.
1. Na barra superior, selecione a conta conectada. 
1. Em **Diretório**, escolha o locatário do Azure AD onde o aplicativo está registrado.
1. Escolha **Azure Active Directory** no menu de navegação à esquerda. Se Azure Active Directory não estiver disponível no painel de navegação, siga estas etapas:

    1. Na parte superior do menu principal à esquerda, marque **Todos os serviços**.
    1. Digite **Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione o item de **Azure Active Directory** do resultado.

1. No painel **Azure Active Directory**, escolha **Aplicativos empresariais** no menu de navegação esquerdo do **Azure Active Directory**.
1. Marque **Todos os aplicativos** para exibir uma lista com todos os seus aplicativos.

     Se não vir o aplicativo desejado na lista, use os diferentes filtros na parte superior da lista **Todos os aplicativos** para restringir a lista ou percorra a lista para localizar o aplicativo.

1. Na lista, escolha o aplicativo ao qual deseja atribuir um usuário ou grupo de segurança.
1. Na página **visão geral** do aplicativo, selecione **Propriedades** no menu de navegação esquerdo do aplicativo.
1. Localize a configuração **Atribuição de usuário obrigatória?** e defina-a como **Sim**. Quando essa opção é definida como **Sim**, os usuários devem primeiro ser atribuídos a esse aplicativo antes de poderem acessá-lo.
1. Escolha **Salvar** para salvar essa alteração da configuração.

### <a name="app-registration"></a>Registro do aplicativo

1. Vá para a [**portal do Azure**](https://portal.azure.com/).
1. Na barra superior, selecione a conta conectada. 
1. Em **Diretório**, escolha o locatário do Azure AD onde o aplicativo está registrado.
1. Escolha **Azure Active Directory** no menu de navegação à esquerda.
1. No painel de **Azure Active Directory** , selecione **registros de aplicativo** no menu de navegação do **Azure Active Directory** esquerdo.
1. Crie ou selecione o aplicativo que você deseja gerenciar. Você precisa ser **proprietário** deste registro de aplicativo.
1. Na página **visão geral** do aplicativo, siga o link **aplicativo gerenciado no diretório local** sob o Essentials na parte superior da página. Isso levará você para o _aplicativo empresarial gerenciado_ do registro do aplicativo.
1. Na folha de navegação à esquerda, selecione **Propriedades**.
1. Localize a configuração **Atribuição de usuário obrigatória?** e defina-a como **Sim**. Quando essa opção é definida como **Sim**, os usuários devem primeiro ser atribuídos a esse aplicativo antes de poderem acessá-lo.
1. Escolha **Salvar** para salvar essa alteração da configuração.

## <a name="assign-users-and-groups-to-the-app"></a>Atribuir usuários e grupos ao aplicativo

Após configurar seu aplicativo para habilitar a atribuição de usuários, prossiga e atribua usuários e grupos ao aplicativo.

1. Escolha o painel **Usuários e grupos** no menu de navegação esquerdo do aplicativo.
1. Na parte superior da lista **Usuários e grupos**, escolha o botão **Adicionar usuário** para abrir o painel **Adicionar Atribuição**.
1. Clique no seletor **Usuários** do painel **Adicionar Atribuição**. 

     Uma lista de usuários e grupos de segurança será exibida em conjunto com uma caixa de texto para pesquisar e localizar um determinado usuário ou grupo. Esta tela permite que você escolha vários usuários e grupos de uma só vez.

1. Após terminar de escolher os usuários e grupos, pressione o botão **Selecionar** na parte inferior para passar para a próxima parte.
1. Pressione o botão **Atribuir** na parte inferior para concluir as atribuições de usuários e grupos ao aplicativo. 
1. Confirme que os usuários e grupos que você adicionou estão aparecendo na lista atualizada de **Usuários e grupos**.

