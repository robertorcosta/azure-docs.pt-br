---
title: Configurar o comportamento da sessão-Azure Active Directory B2C | Microsoft Docs
description: Saiba como configurar o comportamento da sessão no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 091704fabb7b50a0c83625c6ae46d9a807f01ffc
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961027"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Configurar o comportamento da sessão no Azure Active Directory B2C

O gerenciamento de [sessão de SSO (logon único)](session-overview.md) no Azure Active Directory B2C (Azure ad B2C) permite que um administrador controle a interação com um usuário depois que o usuário já tiver se autenticado. Por exemplo, o administrador pode controlar se a seleção de provedores de identidade é exibida ou se os detalhes da conta precisam ser inseridos novamente. Este artigo descreve como definir as configurações de SSO para o Azure AD B2C.

## <a name="session-behavior-properties"></a>Propriedades de comportamento da sessão

Você pode usar as seguintes propriedades para gerenciar sessões de aplicativos da Web:

- **Duração da sessão do aplicativo Web (minutos)** - A vida útil do cookie de sessão do Azure AD B2C armazenado no navegador do usuário após a autenticação bem-sucedida.
    - Padrão = 1440 minutos.
    - Mínimo (inclusive) = 15 minutos.
    - Máximo (inclusive) = 1440 minutos.
- **Tempo limite da sessão do aplicativo Web** -o [tipo de expiração da sessão](session-overview.md#session-expiry-type), *sem interrupção*ou *absoluto*. 
- **Configuração de logon único** – o [escopo da sessão](session-overview.md#session-scope) do comportamento de logon único (SSO) em vários aplicativos e fluxos de usuário em seu locatário Azure ad B2C.


## <a name="configure-the-properties"></a>Configurar as propriedades

1. Entre no [portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém o locatário Azure ad B2C.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Escolha **Fluxos de usuário**.
5. Abra o fluxo de usuários criado anteriormente.
6. Selecione **Propriedades**.
7. Configure a **vida útil da sessão do aplicativo Web (minutos)**, o **tempo limite da sessão do aplicativo Web**, **a configuração de logon único**e **exija o token de ID em solicitações de logout** , conforme necessário.

    ![Configurações de propriedade de comportamento de sessão no portal do Azure](./media/session-behavior/session-behavior.png)

8. Clique em **Salvar**.

## <a name="configure-sign-out-behavior"></a>Configurar comportamento de saída

### <a name="secure-your-logout-redirect"></a>Proteger seu redirecionamento de logout

Após o logout, o usuário é redirecionado para o URI especificado no `post_logout_redirect_uri` parâmetro, independentemente das URLs de resposta que foram especificadas para o aplicativo. No entanto, se um valor válido `id_token_hint` for passado e o **token de ID necessário em solicitações de logout** estiver ativado, Azure ad B2C verificará se o valor de `post_logout_redirect_uri` corresponde a um dos URIs de redirecionamento configurados do aplicativo antes de executar o redirecionamento. Se nenhuma URL de resposta correspondente tiver sido configurada para o aplicativo, uma mensagem de erro será exibida e o usuário não será redirecionado. Para exigir um token de ID em solicitações de logout:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém o locatário Azure ad B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Escolha **Fluxos de usuário**.
1. Abra o fluxo de usuários criado anteriormente.
1. Selecione **Propriedades**.
1. Habilite o **token de ID de solicitação em solicitações de logout**.
1. Volte para  **Azure ad B2C**.
1. Selecione **registros de aplicativo**e, em seguida, selecione seu aplicativo.
1. Selecione **Autenticação**.
1. Na caixa de texto **URL de logout** , digite o URI de redirecionamento de logout de postagem e, em seguida, selecione **salvar**.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [sessão do Azure AD B2C](session-overview.md).
