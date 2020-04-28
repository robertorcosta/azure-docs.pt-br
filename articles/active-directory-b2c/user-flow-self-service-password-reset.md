---
title: Redefinição de senha de autoatendimento no Azure Active Directory B2C | Microsoft Docs
description: Demonstra como configurar a redefinição de senha de autoatendimento para seus clientes no Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d6dad52c8a3e63c64bb8e0e0030e8c50b5bab42c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78183101"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Configure a redefinição de senha de autoatendimento para seus clientes

Com o recurso de redefinição de senha de autoatendimento, seus clientes que tenham se registrado com contas locais poderão redefinir as respectivas senhas por conta própria. Isso reduz significativamente a sobrecarga da equipe de suporte, principalmente se milhões de clientes usarem seu aplicativo regularmente. Atualmente, usar um endereço de email verificado é o único método de recuperação com suporte.

> [!NOTE]
> Este artigo aplica-se à redefinição de senha por autoatendimento usada no contexto do fluxo de usuário de **Entrada** V1 que usa **Entrada na Conta Local** como o provedor de identidade. Se você precisar de fluxos de usuários de redefinição de senha totalmente personalizáveis invocados do seu aplicativo, consulte [este artigo](user-flow-overview.md).
>
>

Por padrão, o diretório não tem a redefinição de senha de autoatendimento ativada. Use as etapas a seguir para ativá-la:

1. Entre no [portal do Azure](https://portal.azure.com/) como o Administrador da Assinatura. Essa é a mesma conta corporativa, de estudante ou da Microsoft que você usou para criar o diretório.
2. Abra o **Azure Active Directory** (na barra de navegação à esquerda).
3. Role para baixo na folha opções e selecione **redefinição de senha**.
4. Configure a **Redefinição de senha de autoatendimento habilitada** como **Tudo**.
5. Clique em **Salvar** na parte superior da página. Pronto!

Para testar, use o recurso "Executar agora" em qualquer fluxo de usuário de entrada que tenha contas locais como um provedor de identidade. Na página de entrada da conta local (na qual você insere um endereço de email e a senha ou um nome de usuário e a senha), clique em **Não consegue acessar sua conta?** para verificar a experiência do cliente.

> [!NOTE]
> As páginas do autoatendimento de redefinição de senha podem ser personalizadas usando o [recurso de identidade visual da empresa](../active-directory/fundamentals/customize-branding.md).
>
>

