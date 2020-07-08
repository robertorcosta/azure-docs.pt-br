---
title: Autenticação Multifator no Azure Active Directory B2C | Microsoft Docs
description: Como habilitar a Autenticação Multifator em aplicativos voltados ao consumidor protegidos pelo Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a683e34b1021754f5680092346d896ab7059aa1b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85389030"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Ativar a autenticação de vários fatores no Azure Active Directory B2C

O Azure Active Directory B2C (Azure AD B2C) integra-se diretamente com a [autenticação multifator do Azure](../active-directory/authentication/multi-factor-authentication.md) para que você possa adicionar uma segunda camada de segurança às experiências de inscrição e de entrada em seus aplicativos. Você habilita a autenticação de múltiplos fatores sem escrever uma única linha de código. Se você já criou fluxos de usuário de inscrição e de login, ainda poderá ativar a autenticação multi fator.

Esse recurso ajuda os aplicativos a lidarem com cenários como os seguintes:

- Você não precisa de autenticação de vários fatores para acessar um aplicativo, mas exige que ele acesse outro. Por exemplo, o cliente pode entrar em um aplicativo de seguro de automóvel com uma conta social ou local, mas deve verificar o número de telefone antes de acessar o aplicativo de seguro residencial registrado no mesmo diretório.
- Você não precisa de autenticação multi fator para acessar um aplicativo em geral, mas exige que ele acesse as partes confidenciais dentro dele. Por exemplo, o cliente pode entrar em um aplicativo bancário com uma conta social ou local e verificar o saldo da conta, mas deve verificar o número de telefone antes de tentar fazer uma transferência eletrônica.

## <a name="set-multi-factor-authentication"></a>Definir a autenticação multi fator

Quando você cria um fluxo de usuário, tem a opção de ativar a autenticação de vários fatores.

![Definir a autenticação multi fator](./media/custom-policy-multi-factor-authentication/add-policy.png)

Defina a **Autenticação de multifator** para **Habilitado**.

Você pode usar **Executar fluxo de usuário** na política para verificar a experiência. Confirme o seguinte cenário:

Uma conta de cliente é criada em seu locatário antes que a etapa de autenticação de vários fatores ocorra. Durante a etapa, o cliente é solicitado a fornecer um número de telefone e a verificar. Se a verificação for bem-sucedida, o número de telefone será anexado à conta para uso posterior. Mesmo se o cliente cancelar ou desistir, o cliente poderá ser solicitado a confirmar um número de telefone novamente durante o próximo login, com a autenticação multi fatores ativada.

## <a name="add-multi-factor-authentication"></a>Adicionar a autenticação multi fator

É possível habilitar a autenticação multi fator em um fluxo de usuário que você criou anteriormente.

Para habilitar a autenticação multi fator:

1. Abra o fluxo de usuário e, em seguida, selecione **Propriedades**.
2. Próximo a **Autenticação Multifator**, selecione **Habilitado**.
3. Clique em **Salvar** na parte superior da página.


