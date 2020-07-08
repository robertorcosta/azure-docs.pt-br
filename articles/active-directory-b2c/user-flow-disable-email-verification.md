---
title: Desabilitar a verificação de email durante a inscrição do cliente
titleSuffix: Azure AD B2C
description: Saiba como desabilitar a verificação de email durante a inscrição do cliente no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 10613bd2d6219272248f882e45ae1c33d2cc9d61
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85384202"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Desabilitar a verificação de email durante a inscrição do cliente no Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

Siga estas etapas para desabilitar a verificação de email:

1. Entre no [Portal do Azure](https://portal.azure.com)
1. Use o filtro **diretório + assinatura** no menu superior para selecionar o diretório que contém seu locatário Azure ad B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Escolha **Fluxos de usuário**.
1. Selecione o fluxo de usuário para o qual você deseja desabilitar a verificação de email. Por exemplo, *B2C_1_signinsignup*.
1. Selecione **layouts de página**.
1. Selecione **a página de inscrição da conta local**.
1. Em **atributos de usuário**, selecione **endereço de email**.
1. Na lista suspensa **requer verificação** , selecione **não**.
1. Selecione **Salvar**. A verificação de email agora está desabilitada para este fluxo de usuário.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Personalizar a interface do usuário no Azure Active Directory B2C](customize-ui-overview.md)

