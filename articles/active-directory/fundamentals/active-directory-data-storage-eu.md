---
title: Armazenamento de dados de identidade para clientes europeus - Azure AD
description: Saiba mais sobre onde o Azure Active Directory armazena dados relacionados à identidade para os seus clientes europeus.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7360b11f41cc08c2beb3ffa227e1658881798502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422994"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Armazenamento de dados de identidade para os clientes europeus no Azure Active Directory
Os dados de identidade são armazenados pelo Azure AD em uma localização geográfica com base no endereço fornecido pela sua organização ao se inscrever em um serviço Microsoft Online, como o Office 365 e o Azure. Para obter informações sobre onde seus dados de identidade estão armazenados, você pode usar a seção [Onde estão seus dados localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

Para clientes que forneceram um endereço na Europa, o Azure AD mantém a maioria dos dados de identidade dentro de data centers europeus. Este documento fornece informações sobre quaisquer dados armazenados fora da Europa pelos serviços Azure AD.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Autenticação multifatorial do Microsoft Azure (MFA)
    
- Toda autenticação de dois fatores usando chamadas telefônicas ou SMS são originárias de data centers dos EUA e também são roteadas por provedores globais.
- As notificações push usando o aplicativo Microsoft Authenticator são originárias de data centers nos EUA. Além disso, serviços específicos de fornecedores de dispositivos também podem entrar em jogo e esses serviços talvez fora da Europa.
- Códigos OATH sempre são validados nos EUA. 

Para obter mais informações sobre quais informações do usuário são coletadas pelo Azure Multi-Factor Authentication Server (MFA Server) e pelo Azure MFA baseado na nuvem, consulte a coleta de dados do usuário do [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection).

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Os dados de configuração da diretiva Azure AD B2C e os contêineres-chave são armazenados em data centers nos EUA. Estes não contêm nenhum dado pessoal do usuário. Para obter mais informações sobre as configurações de política, consulte o artigo [Azure Active Directory B2C: Políticas internas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
O Azure AD B2B armazena convites com link de resgate e redirecionam informações de URL em data centers nos EUA. Além disso, o endereço de e-mail dos usuários que não assinam o recebimento de convites B2B também são armazenados em data centers nos EUA.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

O Microsoft Azure AD DS armazena dados de usuário no mesmo que a Rede Virtual do Microsoft Azure selecionada pelo cliente. Portanto, se a rede estiver fora da Europa, os dados são replicados e armazenados fora da Europa.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Federação no Microsoft Exchange Server 2013
    
- Identificador de aplicativo (AppID) - Um número único gerado pelo sistema de autenticação do Azure Active Directory para identificar organizações do Exchange.
- Lista de domínios federados aprovados para aplicação
- Assinatura de token do aplicativo Chave Pública 

Para obter mais informações sobre a federação no servidor Microsoft Exchange, consulte o artigo [Federação: Exchange 2013 Help.](https://docs.microsoft.com/exchange/federation-exchange-2013-help)


## <a name="other-considerations"></a>Outras considerações

Serviços e aplicativos que se integram ao Azure AD têm acesso a dados de identidade. Avalie cada serviço e aplicativo que você usa para determinar como os dados de identidade são processados por esse serviço e aplicativo específicos e se eles atendem aos requisitos de armazenamento de dados da sua empresa.

Para obter mais informações sobre a residência de dados de serviços da Microsoft, consulte o [Onde seus dados estão localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) seção do Microsoft Trust Center.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre qualquer um dos recursos e funcionalidades descritos acima, consulte esses artigos:
- [O que é a Autenticação Multifator?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Redefinição da senha de autoatendimento do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [O que é o Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [O que é a colaboração Azure AD B2B?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
