---
title: Configurar o comportamento da sessão-Azure Active Directory B2C | Microsoft Docs
description: Saiba como configurar o comportamento da sessão no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f2d6d00ea06bb362d82b5fbdff658b729eed17cd
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258977"
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

8. Clique em **Save** (Salvar).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [sessão do Azure AD B2C](session-overview.md).