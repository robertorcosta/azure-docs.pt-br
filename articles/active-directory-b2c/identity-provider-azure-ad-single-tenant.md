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
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 5b21fcd2d3ec5560b01352b112e9ed1bb2404766
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678055"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Configurar assinatura para uma organização do Active Directory do Azure específica no Azure Active Directory B2C

Para usar um Azure Active Directory (Azure AD) como um [provedor de identidade](authorization-code-flow.md) no Azure AD B2C, é preciso criar um aplicativo que o represente. Este artigo mostra como habilitar a entrada de usuários a partir de uma organização específica do Azure AD usando um fluxo de usuário no Azure AD B2C.

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurar o Azure AD como um provedor de identidade

1. Certifique-se de que está usando o diretório que contém o inquilino Azure AD B2C. Selecione o filtro **de assinatura Diretório +** no menu superior e escolha o diretório que contém o inquilino Azure AD B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Selecione **provedores de identidade**e selecione **o novo provedor OpenID Connect**.
1. Digite um **nome**. Por exemplo, insira *Contoso Azure AD*.
1. Para **url Metadata,** digite `{tenant}` a seguinte URL substituindo com o nome de domínio do seu inquilino Azure AD:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Por exemplo, `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.

1. Para **ID do cliente,** digite o ID do aplicativo que você gravou anteriormente.
1. Para **o segredo do Cliente,** digite o segredo do cliente que você gravou anteriormente.
1. Para o **Escopo,** digite o `openid profile`.
1. Deixe os valores padrão para o **tipo resposta**e **o modo resposta**.
1. (Opcional) Para a dica `contoso.com` **Domínio,** digite . Para obter mais informações, confira [Configurar entrada direta usando o Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Em **Mapeamento de reivindicações do provedor de identidade,** selecione as seguintes reclamações:

    * **ID do usuário**: *oid*
    * **Nome de exibição:** *nome*
    * **Nome dado:** *given_name*
    * **Sobrenome**: *family_name*
    * **E-mail**: *unique_name*

1. Clique em **Salvar**.
