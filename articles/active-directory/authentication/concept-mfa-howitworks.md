---
title: Visão geral da autenticação multifatorial do Azure
description: Saiba como a Autenticação Multifatorial do Azure ajuda a proteger o acesso a dados e aplicativos, ao mesmo tempo em que atende à demanda do usuário por um simples processo de login.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c50232abd12c8c0390409bd7bf72833b4f153e02
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667357"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Como funciona: autenticação multifator do Azure

A autenticação multifator é um processo em que um usuário é solicitado durante o processo de conexão para obter uma forma adicional de identificação, como inserir um código no celular ou fornecer uma verificação de impressão digital.

Se você usar apenas uma senha para autenticar um usuário, isso deixará um vetor inseguro para ataque. Se a senha for fraca ou se tiver sido exposta em outro lugar, será realmente o usuário entrando com o nome de usuário e a senha ou um invasor? Quando você precisar de uma segunda forma de autenticação, a segurança será aprimorada, pois esse fator adicional não será algo fácil de ser obtido ou duplicado por um invasor.

![Imagem conceitual das diferentes formas de autenticação multifator](./media/concept-mfa-howitworks/methods.png)

A Autenticação Multifator do Azure funciona exigindo dois ou mais dos seguintes métodos de autenticação:

* Algo que você sabe, normalmente, uma senha.
* Algo que você tem, como um dispositivo confiável que não seja facilmente duplicado, como um telefone ou uma chave de hardware.
* Algo que você é: uma biometria, como uma impressão digital ou uma verificação facial.

Os usuários podem se registrar na redefinição de senha por autoatendimento e na Autenticação Multifator do Azure em uma só etapa para simplificar a experiência de integração. Os administradores podem definir quais formas de autenticação secundária podem ser usadas. A Autenticação Multifator do Azure também pode ser necessária quando os usuários executam uma redefinição de senha por autoatendimento para proteger ainda mais esse processo.

![Métodos de autenticação em uso na tela de login](media/concept-authentication-methods/overview-login.png)

A autenticação multifatorial do Azure ajuda a proteger o acesso a dados e aplicativos, mantendo a simplicidade para os usuários. Ele fornece segurança adicional exigindo uma segunda forma de autenticação e fornece autenticação forte por meio de uma variedade de [métodos de autenticação](concept-authentication-methods.md) fáceis de usar. Os usuários podem ou não ser desafiados para MFA com base em decisões de configuração tomadas por um administrador.

Seus aplicativos ou serviços não precisam fazer alterações para usar a Autenticação Multifatorial do Azure. Os pedidos de verificação fazem parte do evento de login Azure AD, que solicita e processa automaticamente o desafio do MFA quando necessário.

## <a name="available-verification-methods"></a>Métodos de verificação disponíveis

Quando um usuário entra em um aplicativo ou serviço e recebe um prompt MFA, ele pode escolher entre uma de suas formas registradas de verificação adicional. Um administrador pode exigir o registro desses métodos de verificação de autenticação multifatorial do Azure, ou o usuário pode acessar seu próprio [Meu Perfil](https://myprofile.microsoft.com) para editar ou adicionar métodos de verificação.

As seguintes formas adicionais de verificação podem ser usadas com a autenticação multifatorial do Azure:

* Aplicativo Microsoft Authenticator
* Token OATH de hardware
* sms
* Chamada de voz

## <a name="how-to-enable-and-use-azure-multi-factor-authentication"></a>Como ativar e usar a autenticação multifatorial do Azure

Os usuários e grupos podem ser habilitados para que a Autenticação Multifatorial do Azure seja solicitada para verificação adicional durante o evento de login. [Os padrões de segurança](../fundamentals/concept-fundamentals-security-defaults.md) estão disponíveis para todos os inquilinos do Azure AD para habilitar rapidamente o uso do aplicativo Microsoft Authenticator para todos os usuários.

Para controles mais granulares, as políticas [de Acesso Condicional](../conditional-access/overview.md) podem ser usadas para definir eventos ou aplicativos que requerem MFA. Essas políticas podem permitir eventos regulares de login quando o usuário está na rede corporativa ou em um dispositivo registrado, mas solicitam fatores adicionais de verificação quando remotos ou em um dispositivo pessoal.

![Diagrama de visão geral de como o Acesso Condicional funciona para proteger o processo de entrada](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre licenciamento, consulte [Recursos e licenças para Autenticação Multifatorial do Azure](concept-mfa-licensing.md).

Para ver o MFA em ação, habilite a Autenticação Multifatorial do Azure para um conjunto de usuários de teste no tutorial a seguir:

> [!div class="nextstepaction"]
> [Habilitar a Autenticação Multifator do Microsoft Azure](tutorial-mfa-applications.md)
