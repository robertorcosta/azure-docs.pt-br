---
title: Configurar a entrada para uma organização do Azure AD
titleSuffix: Azure AD B2C
description: Configurar assinatura para uma organização do Active Directory do Azure específica no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: ce5f373576f13a4b1bdb88b5ffb7869a2d7865cd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388350"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Configurar assinatura para uma organização do Active Directory do Azure específica no Azure Active Directory B2C

Para usar um Azure Active Directory (Azure AD) como um [provedor de identidade](authorization-code-flow.md) no Azure AD B2C, é preciso criar um aplicativo que o represente. Este artigo mostra como habilitar a entrada de usuários a partir de uma organização específica do Azure AD usando um fluxo de usuário no Azure AD B2C.

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurar o Azure AD como um provedor de identidade

1. Verifique se você está usando o diretório que contém Azure AD B2C locatário. Selecione o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário do Azure AD B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Selecione **provedores de identidade**e, em seguida, selecione **novo provedor do OpenID Connect**.
1. Insira um **Nome**. Por exemplo, insira *Contoso Azure AD*.
1. Para a **URL de metadados**, insira a seguinte URL substituindo `{tenant}` pelo nome de domínio do seu locatário do Azure AD:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Por exemplo, `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.

1. Para **ID do cliente**, insira a ID do aplicativo que você registrou anteriormente.
1. Para **segredo do cliente**, insira o segredo do cliente que você registrou anteriormente.
1. Para o **escopo**, insira o `openid profile` .
1. Deixe os valores padrão para **tipo de resposta**e **modo de resposta**.
1. Adicional Para a **dica de domínio**, digite `contoso.com` . Para obter mais informações, confira [Configurar entrada direta usando o Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Em **mapeamento de declarações do provedor de identidade**, selecione as seguintes declarações:

    * **ID de usuário**: *OID*
    * **Nome de exibição**: *nome*
    * **Nome fornecido**: *given_name*
    * **Sobrenome**: *family_name*
    * **Email**: *unique_name*

1. Clique em **Salvar**.
