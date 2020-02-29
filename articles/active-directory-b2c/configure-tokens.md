---
title: Configurar tokens-Azure Active Directory B2C | Microsoft Docs
description: Saiba como configurar a vida útil do token e as configurações de compatibilidade no Azure Active Directory B2C.
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189610"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Configurar tokens no Azure Active Directory B2C

Neste artigo, você aprenderá a configurar o [tempo de vida e a compatibilidade de um token](tokens-overview.md) no Azure Active Directory B2C (Azure ad B2C).

## <a name="prerequisites"></a>Prerequisites

[Criar um fluxo de usuário](tutorial-create-user-flows.md) para permitir que os usuários se registrem e entrem no seu aplicativo.

## <a name="configure-token-lifetime"></a>Configurar tempo de vida do token

Você pode configurar o tempo de vida do token em qualquer fluxo de usuário.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C. Selecione o **diretório +** filtro de assinatura no menu superior e escolha o diretório que contém seu locatário de Azure ad B2C.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **fluxos de usuário (políticas)** .
5. Abra o fluxo de usuário que você criou anteriormente.
6. Selecione **Propriedades**.
7. Em **tempo de vida do token**, ajuste as propriedades a seguir para atender às necessidades do seu aplicativo:

    ![Configurações de propriedade de tempo de vida do token no portal do Azure](./media/configure-tokens/token-lifetime.png)

8. Clique em **Save** (Salvar).

## <a name="configure-token-compatibility"></a>Configurar a compatibilidade de token

1. Selecione **fluxos de usuário (políticas)** .
2. Abra o fluxo de usuário que você criou anteriormente.
3. Selecione **Propriedades**.
4. Em **configurações de compatibilidade de token**, ajuste as propriedades a seguir para atender às necessidades do seu aplicativo:

    ![Configurações de propriedade de compatibilidade de token no portal do Azure](./media/configure-tokens/token-compatibility.png)

5. Clique em **Save** (Salvar).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [usar tokens de acesso](access-tokens.md).



