---
title: Passar um token de acesso por meio de um fluxo de usuário para seu aplicativo
titleSuffix: Azure AD B2C
description: Saiba como passar um token de acesso para provedores de identidade OAuth 2,0 como uma declaração em um fluxo de usuário no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0446dd9e316e145cf93c503ab9634d4eedbc299b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85387823"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Passar um token de acesso por meio de um fluxo de usuário para seu aplicativo no Azure Active Directory B2C

Um [fluxo de usuário](user-flow-overview.md) no Azure Active Directory B2C (Azure ad B2C) fornece aos usuários de seu aplicativo uma oportunidade de se inscrever ou entrar com um provedor de identidade. Quando a jornada é iniciada, o Azure AD B2C recebe um [token de acesso](tokens-overview.md) do provedor de identidade. O Azure Active Directory B2C usa esse token para recuperar informações sobre o usuário. Habilitar uma declaração no seu fluxo de usuário para passar o token por meio para os aplicativos que você se registrar no Azure AD B2C.

Atualmente, Azure AD B2C só dá suporte à passagem do token de acesso dos provedores de identidade [OAuth 2,0](authorization-code-flow.md) , que incluem o [Facebook](identity-provider-facebook.md) e o [Google](identity-provider-google.md). Para todos os outros provedores de identidade, a declaração é retornada em branco.

## <a name="prerequisites"></a>Pré-requisitos

* Seu aplicativo deve estar usando um [fluxo de usuário v2](user-flow-versions.md).
* Seu fluxo de usuário é configurado com um provedor de identidade do OAuth 2.0.

## <a name="enable-the-claim"></a>Habilitar a declaração

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C. Escolha o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **fluxos de usuário (políticas)** e, em seguida, selecione seu fluxo de usuário. Por exemplo, **B2C_1_signupsignin1**.
5. Selecione **Declarações do aplicativo**.
6. Habilite a declaração de **token de acesso do provedor de identidade** .

    ![Habilitar a declaração de token de acesso do provedor de identidade](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Clique em **Salvar** para salvar o fluxo de usuário.

## <a name="test-the-user-flow"></a>Testar o fluxo de usuário

Ao testar seus aplicativos no Azure AD B2C, pode ser útil ter o token do Azure AD B2C retornado ao `https://jwt.ms` para examinar as declarações.

1. Na página de visão geral do fluxo de usuário, selecione **Executar o fluxo de usuário**.
2. Para **Aplicativo**, selecione seu aplicativo que você registrou anteriormente. Para ver o token no exemplo a seguir, a **URL de resposta** deve mostrar `https://jwt.ms`.
3. Clique em **Executar o fluxo de usuário**, e em seguida, entre com suas credenciais de conta. Você deve ver o token de acesso do provedor de identidade na declaração **idp_access_token**.

    Você deverá ver algo semelhante ao texto a seguir:

    ![Token decodificado em jwt.ms com idp_access_token bloco realçado](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.PNG)

## <a name="next-steps"></a>Próximas etapas

Saiba mais na [visão geral de tokens de Azure ad B2C](tokens-overview.md).
