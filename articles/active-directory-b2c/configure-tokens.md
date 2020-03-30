---
title: Configurar tokens - Azure Active Directory B2C | Microsoft Docs
description: Saiba como configurar as configurações de vida útil e compatibilidade do token no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 160898f8535d3dad4811af016ebca779b7ef43a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189610"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Configure tokens no Azure Active Directory B2C

Neste artigo, você aprende como configurar a vida útil e a [compatibilidade de um token](tokens-overview.md) no Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Pré-requisitos

[Criar um fluxo de usuário](tutorial-create-user-flows.md) para permitir que os usuários se registrem e entrem no seu aplicativo.

## <a name="configure-token-lifetime"></a>Configurar a vida útil do token

Você pode configurar a vida útil do token em qualquer fluxo de usuário.

1. Faça login no [portal Azure](https://portal.azure.com).
2. Certifique-se de que está usando o diretório que contém seu inquilino Azure AD B2C. Selecione o filtro **de assinatura Diretório +** no menu superior e escolha o diretório que contém o inquilino Azure AD B2C.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **fluxos de usuário (políticas)**.
5. Abra o fluxo de usuário que você criou anteriormente.
6. Selecione **Propriedades**.
7. Em **Token,** ajuste as seguintes propriedades para atender às necessidades do seu aplicativo:

    ![Configurações de propriedade de token vitalno no portal Azure](./media/configure-tokens/token-lifetime.png)

8. Clique em **Salvar**.

## <a name="configure-token-compatibility"></a>Configurar compatibilidade de tokens

1. Selecione **fluxos de usuário (políticas)**.
2. Abra o fluxo de usuário que você criou anteriormente.
3. Selecione **Propriedades**.
4. Em **configurações de compatibilidade do Token,** ajuste as seguintes propriedades para atender às necessidades do seu aplicativo:

    ![Configurações de propriedade de compatibilidade de token no portal Azure](./media/configure-tokens/token-compatibility.png)

5. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [usar tokens de acesso](access-tokens.md).



