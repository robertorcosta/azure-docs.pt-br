---
title: Adicionar um aplicativo não-galeria - plataforma de identidade Microsoft | Microsoft Docs
description: Adicione um aplicativo que não seja da galeria ao seu inquilino Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd5a5f100dbe09c3b82f58183a118ee3bf455f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063604"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Adicione um aplicativo não listado (não-galeria) à sua organização Azure AD

Além das opções na [galeria de aplicativos Azure AD,](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)você tem a opção de adicionar um **aplicativo que não é galeria.** Você pode adicionar qualquer aplicativo que já existe em sua organização ou qualquer aplicativo de terceiros de um fornecedor que ainda não faz parte da galeria AD do Azure. Dependendo do contrato [de licença,](https://azure.microsoft.com/pricing/details/active-directory/)os seguintes recursos estão disponíveis:

- Integração de autoatendimento de qualquer aplicativo que suporte provedores de identidade [SAML (Security Assertion Markup Language,](https://wikipedia.org/wiki/SAML_2.0) linguagem de marcação de afirmação de segurança) (iniciação por SP ou idP)
- Integração de autoatendimento de qualquer aplicativo Web que tenha uma página de entrada baseada em HTML usando o [SSO baseado em senha](what-is-single-sign-on.md#password-based-sso)
- Conexão de autoatendimento de aplicativos que usam o [protocolo SCIM (System for Cross-Domain Identity Management, gerenciamento de identidade entre domínios) para provisionamento de usuários](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Capacidade de adicionar links aos aplicativos no [inicializador de aplicativos do Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) ou no [Painel de acesso do Azure AD](what-is-single-sign-on.md#linked-sign-on)

Este artigo descreve como adicionar um aplicativo não-galeria aos **Aplicativos Corporativos** no portal Azure sem escrever código. Se, em vez disso, você estiver procurando orientação do desenvolvedor sobre como integrar aplicativos personalizados com o Azure AD, consulte [Cenários de autenticação para a Azure AD](../develop/authentication-scenarios.md). Quando você desenvolve um aplicativo que usa um protocolo moderno como [o OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) para autenticar usuários, você pode registrá-lo na plataforma de identidade da Microsoft usando a experiência [de registros](../develop/quickstart-register-app.md) do App no portal Azure.

## <a name="add-a-non-gallery-application"></a>Adicionar um aplicativo inexistente na galeria

1. Faça login no [portal do Azure Active Directory](https://aad.portal.azure.com/) usando sua conta de administrador da plataforma de identidade Microsoft.

2. Selecione **aplicativos corporativos** > **Novo aplicativo**.

3. (Opcional, mas recomendado) Na caixa de pesquisa **procurar a galeria azure,** digite o nome de exibição do aplicativo. 

4. Selecione **Criar seu próprio aplicativo**. A **página Criar sua própria página de aplicativo** é exibida.

   ![Adicionar aplicativo](media/add-non-gallery-app/create-your-own-application.png)

5. Comece a digitar o nome de exibição para o seu novo aplicativo. Se houver aplicativos de galeria com nomes semelhantes, eles aparecerão em uma lista de resultados de pesquisa.

   > [!NOTE]
   > Recomendamos usar a versão de galeria do seu aplicativo sempre que possível. Se o aplicativo que você deseja adicionar aparecer nos resultados da pesquisa, selecione o aplicativo e pule o resto deste procedimento.

6. Em Que você está procurando fazer **Integrate any other application you don't find in the gallery**com a **sua aplicação?** Esta opção é normalmente usada para aplicações SAML e WS-Fed.

   > [!NOTE]
   > As outras duas opções são utilizadas nos seguintes cenários:
   >* **Configure o Proxy do aplicativo para acesso remoto seguro a um aplicativo local,** que abre a página de configuração para proxy e conectores de aplicativos Azure AD.
   >* **Registre um aplicativo em que você está trabalhando para se integrar com o Azure AD** abre a página de registros do **App.** Esta opção é normalmente usada para aplicativos OpenID Connect.

7. Selecione **Criar**. A página **Visão Geral** do aplicativo será aberta.

## <a name="configure-user-sign-in-properties"></a>Configurar propriedades de logon do usuário

1. Selecione **Propriedades** para abrir o painel de propriedades para edição.

    ![Editar painel de propriedades](media/add-non-gallery-app/edit-properties.png)

2. Defina as opções a seguir para determinar como os usuários atribuídos ou não atribuídos ao aplicativo podem entrar no aplicativo e se um usuário pode ver o aplicativo no Painel de Acesso.

    - **Habilitado para que os usuários entrem** determina se os usuários atribuídos ao aplicativo podem entrar nele.
    - **A atribuição de usuário necessária** determina se os usuários que não são atribuídos ao aplicativo podem fazer login.
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

3. Para usar um logotipo personalizado, crie um logotipo de 215 x 215 pixels e salve-o no formato PNG. Em seguida, procure o logotipo e carregue-o.

    ![Alterar o logotipo](media/add-non-gallery-app/change-logo.png)

4. Quando terminar, selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas

Agora que você adicionou o aplicativo à sua organização do Azure AD, [escolha um método de logon único](what-is-single-sign-on.md#choosing-a-single-sign-on-method) que deseja usar e veja o artigo apropriado abaixo:

- [Configurar o logon único baseado em SAML](configure-single-sign-on-non-gallery-applications.md)
- [Configurar o logon único com senha](configure-password-single-sign-on-non-gallery-applications.md)
- [Configurar o logon vinculado](configure-linked-sign-on.md)
