---
title: Configure o login para uma organização AD do Azure
titleSuffix: Azure AD B2C
description: Configurar assinatura para uma organização do Active Directory do Azure específica no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 35fc4e1d64fa7df392fa878db14c0464da7dccf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188300"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Configurar assinatura para uma organização do Active Directory do Azure específica no Azure Active Directory B2C

Para usar um Azure Active Directory (Azure AD) como um [provedor de identidade](authorization-code-flow.md) no Azure AD B2C, é preciso criar um aplicativo que o represente. Este artigo mostra como habilitar a entrada de usuários a partir de uma organização específica do Azure AD usando um fluxo de usuário no Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Criar um aplicativo Azure AD

Para habilitar a entrada para usuários de uma organização específica do Azure AD, você precisa registrar um aplicativo no locatário organizacional do Azure AD, que não é o mesmo que o seu locatário do Azure Active Directory B2C.

1. Faça login no [portal Azure](https://portal.azure.com).
2. Certifique-se de que está usando o diretório que contém seu inquilino Azure AD. Selecione o filtro **de assinatura Diretório +** no menu superior e escolha o diretório que contém o inquilino Azure AD. Este não é o mesmo inquilino do seu inquilino Azure AD B2C.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
4. Selecione **Novo registro**.
5. Insira um nome para seu aplicativo. Por exemplo, `Azure AD B2C App`.
6. Aceite a seleção de **Contas neste diretório organizacional apenas** para este aplicativo.
7. Para o **URI redirecionar**, aceite o valor da **Web**e digite a URL a seguir em todas as letras minúsculas, onde `your-B2C-tenant-name` é substituído pelo nome do seu inquilino Azure AD B2C. Por exemplo `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Agora todas as URLs devem estar usando [b2clogin.com](b2clogin.md).

8. Clique em **Registrar**. Copie o **ID do aplicativo (cliente)** a ser usado posteriormente.
9. Selecione **Certificados & segredos** no menu do aplicativo e, em seguida, selecione **Novo segredo do cliente**.
10. Digite um nome para o segredo do cliente. Por exemplo, `Azure AD B2C App Secret`.
11. Selecione o período de expiração. Para esta aplicação, aceite a seleção de **Em 1 ano**.
12. Selecione **Adicionar** e copie o valor do novo segredo do cliente exibido para ser usado posteriormente.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurar o Azure AD como um provedor de identidade

1. Certifique-se de que está usando o diretório que contém o inquilino Azure AD B2C. Selecione o filtro **de assinatura Diretório +** no menu superior e escolha o diretório que contém o inquilino Azure AD B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Selecione **provedores de identidade**e selecione **o novo provedor OpenID Connect**.
1. Digite um **nome**. Por exemplo, insira *Contoso Azure AD*.
1. Para **url Metadata,** digite `your-AD-tenant-domain` a seguinte URL substituindo com o nome de domínio do seu inquilino Azure AD:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Por exemplo, `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

    **Não** use o ponto final de metadados Azure AD v2.0, por exemplo `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`. Fazer isso resulta em `AADB2C: A claim with id 'UserId' was not found, which is required by ClaimsTransformation 'CreateAlternativeSecurityId' with id 'CreateAlternativeSecurityId' in policy 'B2C_1_SignUpOrIn' of tenant 'contoso.onmicrosoft.com'` um erro semelhante ao tentar fazer login.

1. Para **ID do cliente,** digite o ID do aplicativo que você gravou anteriormente.
1. Para **o segredo do Cliente,** digite o segredo do cliente que você gravou anteriormente.
1. Deixe os valores padrão para **escopo,** **tipo de resposta**e modo de **resposta**.
1. (Opcional) Digite um valor para **Domain_hint**. Por exemplo, *ContosoAD*. Esse é o valor a ser usado ao fazer referência a esse provedor de identidade usando *domain_hint* na solicitação.
1. Em **Mapeamento de reivindicações do provedor de identidade,** digite os seguintes valores de mapeamento de sinistros:

    * **ID do usuário**: *oid*
    * **Nome de exibição:** *nome*
    * **Nome dado:** *given_name*
    * **Sobrenome**: *family_name*
    * **E-mail**: *unique_name*

1. Selecione **Salvar**.
