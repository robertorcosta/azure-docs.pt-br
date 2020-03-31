---
title: Desativar a verificação de e-mail durante a inscrição do cliente
titleSuffix: Azure AD B2C
description: Saiba como desativar a verificação de e-mail durante a inscrição do cliente no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 369b4e13baa344a71a51b358ef810d1a66b4b6ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126728"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Desativar a verificação de e-mail durante a inscrição do cliente no Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

Siga estas etapas para desativar a verificação de e-mail:

1. Entre no [portal do Azure](https://portal.azure.com)
1. Use o filtro **de assinatura Directory +** no menu superior para selecionar o diretório que contém o inquilino Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Escolha **Fluxos de usuário**.
1. Selecione o fluxo de usuário para o qual deseja desativar a verificação de e-mail. Por exemplo, *B2C_1_signinsignup.*
1. Selecione **layouts de página**.
1. Selecione **Página de inscrição de conta local**.
1. Em **atributos do usuário,** selecione **Endereço de e-mail**.
1. Na parada DE **VERIFICAÇÃO** NECESSÁRIA, selecione **Não**.
1. Selecione **Salvar**. A verificação de e-mail está agora desativada para esse fluxo de usuário.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [personalizar a interface do usuário no Azure Active Directory B2C](customize-ui-overview.md)

