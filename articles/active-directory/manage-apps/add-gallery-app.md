---
title: Adicionar um aplicativo da galeria – Azure Active Directory | Microsoft Docs
description: Saiba como adicionar um aplicativo da galeria do Azure AD aos seus aplicativos empresariais do Azure.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f2002414836dcb77a09f633f30ec0fcbb7981de
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2020
ms.locfileid: "84760517"
---
# <a name="add-a-gallery-app-to-your-azure-ad-organization"></a>Adicionar um aplicativo da galeria à sua organização do Azure AD

O Azure AD (Azure Active Directory) tem uma galeria que contém milhares de aplicativos pré-integrados que são habilitados com o logon único empresarial. Este artigo descreve as etapas gerais para adição de um aplicativo da galeria à sua organização do Azure AD.

> [!IMPORTANT]
> Primeiro, verifique para seu aplicativo na [Lista de tutoriais sobre como integrar aplicativos SaaS ao Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/). Provavelmente, você encontrará diretrizes passo a passo para adicionar e configurar o aplicativo da galeria que deseja adicionar.

## <a name="add-a-gallery-application"></a>Adicionar um aplicativo da galeria

1. Entre no [portal do Azure](https://portal.azure.com) como administrador global do locatário do Azure AD, administrador do aplicativo de nuvem ou administrador do aplicativo.

1. No [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**.

1. No painel do **Azure Active Directory**, selecione **Aplicativos empresariais**.

    ![Abrir aplicativos empresariais](media/add-gallery-app/open-enterprise-apps.png)


3. Para adicionar um aplicativo da galeria ao seu locatário, selecione **Novo aplicativo**.

    ![Selecione Novo aplicativo para adicionar um aplicativo da galeria ao seu locatário](media/add-gallery-app/new-application.png)

 4. Mude para a nova experiência de versão prévia da galeria: Na faixa na parte superior da **página Adicionar um aplicativo**, selecione o link que diz **Clique aqui para experimentar a galeria de aplicativos nova e aprimorada**.

5. O painel **Procurar na Galeria do Azure AD** é aberto e exibe blocos de aplicativos para plataformas de nuvem, aplicativos locais e aplicativos em destaque. Observe que os aplicativos listados na seção **Aplicativos em destaque** têm ícones que indicam se eles dão suporte ao SSO (logon único) federado e ao provisionamento.

    ![Pesquisar por um aplicativo por nome ou categoria](media/add-gallery-app/browse-gallery.png)

6. Procure na galeria pelo aplicativo que você deseja adicionar ou pesquise-o inserindo seu nome na caixa de pesquisa. Em seguida, selecione o aplicativo entre os resultados. (Opcional) No formulário, você pode editar o nome do aplicativo para atender às necessidades de sua organização.

    ![Mostra como adicionar um aplicativo da galeria](media/add-gallery-app/create-application.png)

7. Selecione **Criar**. Uma página de introdução é exibida com as opções para configurar o aplicativo para a organização.

## <a name="configure-user-sign-in-properties"></a>Configurar propriedades de logon do usuário

1. Selecione **Propriedades** para abrir o painel de propriedades para edição.

    ![Editar painel de propriedades](media/add-gallery-app/edit-properties.png)

1. Defina as opções a seguir para determinar como os usuários atribuídos ou não atribuídos ao aplicativo podem entrar no aplicativo e se um usuário pode ver o aplicativo no Painel de Acesso.

    - **Habilitado para que os usuários entrem** determina se os usuários atribuídos ao aplicativo podem entrar nele.
    - A **atribuição de usuário obrigatória** determina se os usuários não atribuídos ao aplicativo podem entrar nele.
    - **Visível para usuário** determina se os usuários atribuídos a um aplicativo podem vê-lo no painel de acesso e no inicializador do O365.

      Comportamento de usuários **atribuídos**:

       | Configurações de propriedade do aplicativo | | | Experiência do usuário atribuído | |
       |---|---|---|---|---|
       | Habilitado para os usuários entrarem? | Atribuição de usuário obrigatória? | Visível para os usuários? | Os usuários atribuídos podem entrar? | Os usuários atribuídos podem ver o aplicativo?* |
       | sim | sim | sim | sim | sim  |
       | sim | sim | não  | sim | não   |
       | sim | não  | sim | sim | sim  |
       | sim | não  | não  | sim | não   |
       | não  | sim | sim | não  | não   |
       | não  | sim | não  | não  | não   |
       | não  | não  | sim | não  | não   |
       | não  | não  | não  | não  | não   |

      Comportamento de usuários **não atribuídos**:

       | Configurações de propriedade do aplicativo | | | Experiência de usuário não atribuído | |
       |---|---|---|---|---|
       | Habilitado para os usuários entrarem? | Atribuição de usuário obrigatória? | Visível para os usuários? | Os usuários não atribuídos podem entrar? | Os usuários não atribuídos podem ver o aplicativo?* |
       | sim | sim | sim | não  | não   |
       | sim | sim | não  | não  | não   |
       | sim | não  | sim | sim | não   |
       | sim | não  | não  | sim | não   |
       | não  | sim | sim | não  | não   |
       | não  | sim | não  | não  | não   |
       | não  | não  | sim | não  | não   |
       | não  | não  | não  | não  | não   |

     *O usuário pode ver o aplicativo no painel de acesso e no iniciador de aplicativos do Office 365?

1. Para usar um logotipo personalizado, crie um logotipo de 215 x 215 pixels e salve-o no formato PNG. Em seguida, procure o logotipo e carregue-o.

    ![Alterar o logotipo](media/add-gallery-app/change-logo.png)

1. Quando terminar, selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas

Agora que você adicionou o aplicativo à sua organização do Azure AD, [escolha um método de logon único](what-is-single-sign-on.md#choosing-a-single-sign-on-method) que deseja usar e veja o artigo apropriado abaixo:

- [Configurar o logon único baseado em SAML](configure-single-sign-on-non-gallery-applications.md)
- [Configurar o logon único com senha](configure-password-single-sign-on-non-gallery-applications.md)
- [Configurar o logon vinculado](configure-linked-sign-on.md)

