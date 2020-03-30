---
title: Armazenamento de dados de identidade para clientes australianos - Azure AD
description: Saiba onde o Azure Active Directory armazena dados relacionados à identidade para seus clientes australianos.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0dc659f3d580646c1e86e5e97940268311e5546f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460527"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Armazenamento de dados de identidade para clientes australianos e neozelandeses no Azure Active Directory

Os dados de identidade são armazenados pelo Azure AD em uma localização geográfica com base no endereço fornecido pela sua organização ao se inscrever em um serviço Microsoft Online, como o Office 365 e o Azure. Para obter informações sobre onde os dados do cliente de identidade estão armazenados, você pode usar a seção [Onde estão seus dados localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

> [!NOTE]
> Serviços e aplicativos que se integram ao Azure AD têm acesso aos Dados do Cliente de Identidade. Avalie cada serviço e aplicativo que você usa para determinar como os Dados do Cliente de Identidade são processados por esse serviço e aplicativo específicos e se eles atendem aos requisitos de armazenamento de dados da sua empresa. Para obter mais informações sobre a residência de dados de serviços da Microsoft, consulte o Onde seus dados estão localizados? seção do Microsoft Trust Center.

Para clientes que forneceram um endereço na Austrália e Nova Zelândia e usam a edição gratuita do Azure AD, o Azure AD mantém os dados do PII em repouso dentro dos data centers australianos. 

Todos os outros serviços premium do Azure AD armazenam dados de clientes em data centers globais. Para localizar o data center de um serviço, consulte [O Azure Active Directory – Onde estão seus dados localizados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Autenticação multifatorial do Microsoft Azure (MFA)

O serviço Dememina no Azure AD armazena dados de clientes de identidade em data centers globais em repouso. Para saber mais sobre as informações do usuário coletadas e armazenadas pelo Azure MFA e pelo Azure MFA Server, consulte a coleta de dados do usuário do [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency). Se os clientes usarem MFA, seus dados serão armazenados fora dos data centers da Austrália em repouso. 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre qualquer um dos recursos e funcionalidades descritos acima, consulte esses artigos:
- [O que é a Autenticação Multifator?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
