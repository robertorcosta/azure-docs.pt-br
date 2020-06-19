---
title: Configurar tokens - Azure Active Directory B2C | Microsoft Docs
description: Saiba como configurar o tempo de vida do token e as definições de compatibilidade no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dca235374ca20ef8a17e685706f952127d5ece2a
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83869428"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Configurar tokens no Azure Active Directory B2C

Neste artigo, você aprenderá a configurar o [tempo de vida e a compatibilidade de um token](tokens-overview.md) no Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Pré-requisitos

[Criar um fluxo de usuário](tutorial-create-user-flows.md) para permitir que os usuários se registrem e entrem no seu aplicativo.

## <a name="configure-jwt-token-lifetime"></a>Configurar tempo de vida do token JWT

Você pode configurar o tempo de vida do token em qualquer fluxo de usuários.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C. Selecione o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário do Azure AD B2C.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **Fluxos de usuários (políticas)** .
5. Abra o fluxo de usuários criado anteriormente.
6. Selecione **Propriedades**.
7. Em **Tempo de vida do token**, ajuste as seguintes propriedades para atender às necessidades do seu aplicativo:

    ![Configurações de propriedade do tempo de vida do token no portal do Azure](./media/configure-tokens/token-lifetime.png)

8. Clique em **Save** (Salvar).

## <a name="configure-jwt-token-compatibility"></a>Configurar compatibilidade do token JWT

1. Selecione **Fluxos de usuários (políticas)** .
2. Abra o fluxo de usuários criado anteriormente.
3. Selecione **Propriedades**.
4. Em **Configurações da compatibilidade do token**, ajuste as seguintes propriedades para atender às necessidades do seu aplicativo:

    ![Configurações da propriedade de compatibilidade de token no portal do Azure](./media/configure-tokens/token-compatibility.png)

5. Clique em **Save** (Salvar).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [solicitar tokens de acesso](access-tokens.md).



