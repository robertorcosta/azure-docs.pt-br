---
title: Azure Active Directory entrada sem senha (versão prévia)
description: Saiba mais sobre as opções de entrada sem senha para Azure Active Directory usando as chaves de segurança FIDO2 ou o aplicativo Microsoft Authenticator
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba579d6da8c759a4653b729f1a471efdedc2baa7
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505759"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Opções de autenticação com senha para Azure Active Directory

A autenticação multifator (MFA) é uma ótima maneira de proteger sua organização, mas os usuários geralmente ficam frustrados com a camada de segurança adicional sobre a existência de lembrar suas senhas. Os métodos de autenticação sem senha são mais convenientes porque a senha é removida e substituída por algo que você tem, além de algo que você conhece ou algo que você sabe.

|   | Algo que você tem | Algo que você está ou conhece |
| --- | --- | --- |
| Sem senha | Dispositivo, telefone ou chave de segurança do Windows 10 | Biometria ou PIN |

Cada organização tem necessidades diferentes quando se trata de autenticação. A Microsoft oferece as três seguintes opções de autenticação com senha:

- Windows Hello for Business
- Aplicativo Microsoft Authenticator
- Chaves de segurança do FIDO2

![Autenticação: segurança versus conveniência](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello for Business

O Windows Hello para empresas é ideal para os operadores de informações que têm seu próprio computador Windows designado. A biométrica e o PIN estão diretamente ligados ao computador do usuário, o que impede o acesso de qualquer pessoa que não seja o proprietário. Com a integração de PKI (infraestrutura de chave pública) e suporte interno para SSO (logon único), o Windows Hello para empresas fornece um método conveniente para acessar diretamente os recursos corporativos locais e na nuvem.

O [Guia de planejamento](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) do Windows Hello para empresas pode ser usado para ajudá-lo a tomar decisões sobre o tipo de implantação do Windows Hello para empresas e as opções que você precisará considerar.

## <a name="microsoft-authenticator-app"></a>Aplicativo Microsoft Authenticator

Permitir que o telefone do funcionário se torne um método de autenticação com senha. Talvez você já esteja usando o aplicativo Microsoft Authenticator como uma opção de autenticação multifator conveniente, além de uma senha. Você também pode usar o aplicativo autenticador como uma opção com senha.

![Entrar no Microsoft Edge com o aplicativo Microsoft Authenticator](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

O aplicativo autenticador transforma qualquer telefone iOS ou Android em uma credencial forte e com senha. Os usuários podem entrar em qualquer plataforma ou navegador, obtendo uma notificação para seu telefone, correspondendo a um número exibido na tela para aquele em seu telefone e, em seguida, usando sua biométrica (toque ou face) ou PIN para confirmar.

## <a name="fido2-security-keys"></a>Chaves de segurança do FIDO2

As chaves de segurança FIDO2 são um método de autenticação de senha com base em padrões não Phish que pode ser fornecido em qualquer fator forma. A FIDO (Fast Identity online) é um padrão aberto para autenticação com senha. O FIDO permite que usuários e organizações aproveitem o padrão para entrar em seus recursos sem nome de usuário ou senha usando uma chave de segurança externa ou uma chave de plataforma incorporada a um dispositivo.

Para a visualização pública, os funcionários podem usar as chaves de segurança para entrar em seus dispositivos Windows 10 do Azure AD ou híbridos ingressados no Azure AD e obter logon único em seus recursos locais e de nuvem. Os usuários também podem entrar em navegadores com suporte. As chaves de segurança do FIDO2 são uma ótima opção para empresas que são muito sensíveis à segurança ou que têm cenários ou funcionários que não estão dispostos ou podem usar seus telefones como um segundo fator.

![Entrar no Microsoft Edge com uma chave de segurança](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Embora existam muitas chaves que são FIDO2 certificadas pela Aliança de FIDO, a Microsoft exige que algumas extensões opcionais da especificação CTAP (FIDO2 Client-to-Authenticator Protocol) sejam implementadas pelo fornecedor para garantir a segurança máxima e a melhor ocorrer.

Uma chave de segurança **deve** implementar os seguintes recursos e extensões do protocolo FIDO2 CTAP para ser compatível com a Microsoft:

| # | Confiança de recurso/extensão | Por que esse recurso ou extensão é necessário? |
| --- | --- | --- |
| 1 | Chave residente | Esse recurso permite que a chave de segurança seja portátil, em que sua credencial é armazenada na chave de segurança. |
| 2 | PIN do cliente | Esse recurso permite que você proteja suas credenciais com um segundo fator e se aplica a chaves de segurança que não têm uma interface do usuário. |
| 3 | HMAC-segredo | Essa extensão garante que você possa entrar em seu dispositivo quando ele estiver offline ou no modo avião. |
| 4 | Várias contas por RP | Esse recurso garante que você possa usar a mesma chave de segurança em vários serviços, como conta da Microsoft e Azure Active Directory. |

Os provedores a seguir oferecem chaves de segurança FIDO2 de fatores forma diferentes que são conhecidos como compatíveis com a experiência sem senha. Incentivamos você a avaliar as propriedades de segurança dessas chaves contatando o fornecedor, bem como a FIDO Alliance.

| Provedor | Contato |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |

> [!NOTE]
> Se você comprar e planejar usar chaves de segurança baseadas em NFC, precisará de um leitor de NFC com suporte para a chave de segurança. O leitor NFC não é um requisito ou limitação do Azure. Consulte o fornecedor da sua chave de segurança baseada em NFC para obter uma lista de leitores NFC com suporte.

Se você for um fornecedor e quiser obter seu dispositivo na lista de dispositivos com suporte, entre em contato com [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com).

## <a name="what-scenarios-work-with-the-preview"></a>Quais cenários funcionam com a versão prévia?

- Os administradores podem habilitar métodos de autenticação com senha para seus locatários
- Os administradores podem direcionar todos os usuários ou Selecionar usuários/grupos dentro de seu locatário para cada método
- Os usuários finais podem registrar e gerenciar esses métodos de autenticação com senha em seu portal de conta
- Os usuários finais podem entrar com esses métodos de autenticação sem senha
   - Microsoft Authenticator aplicativo: funciona em cenários em que a autenticação do Azure AD é usada, inclusive em todos os navegadores, durante a instalação do OOBE (Windows 10) e com aplicativos móveis integrados em qualquer sistema operacional.
   - Chaves de segurança: trabalhe na tela de bloqueio para o Windows 10 e a Web em navegadores com suporte, como o Microsoft Edge.

## <a name="next-steps"></a>Próximas etapas

[Habilitar as opções de passwordlesss de chave de segurança do FIDO2 em sua organização](howto-authentication-passwordless-security-key.md)

[Habilitar opções com senha baseada em telefone em sua organização](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Links externos

[FIDO Alliance](https://fidoalliance.org/)

[Especificação CTAP FIDO2](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
