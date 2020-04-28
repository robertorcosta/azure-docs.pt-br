---
title: Lista de compatibilidade de federação do AD do Azure
description: Esta página contém provedores de identidade de terceiros que podem ser usados para implementar o logon único.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f5090101c486562e33de56402db348c6038c8a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "60244755"
---
# <a name="azure-ad-federation-compatibility-list"></a>Lista de compatibilidade de federação do AD do Azure
O Azure Active Directory fornece logon único e segurança aprimorada de acesso ao aplicativo para o Office 365 e outros serviços do Microsoft Online para implementações híbridas e apenas de nuvem, sem a necessidade de qualquer solução de terceiros. O Office 365, como a maioria dos serviços online da Microsoft, é integrado ao Azure Active Directory para autorização, autenticação e serviços de diretório. O Azure Active Directory também fornece logon único a milhares de aplicativos SaaS e aplicativos Web locais. Consulte a [galeria de aplicativos](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) do Azure Active Directory para aplicativos SaaS com suporte. 

## <a name="idp-validation"></a>Validação de IDP
Se sua organização usa uma solução de federação de terceiros, você pode configurar o logon único para os usuários do Active Directory local com os Serviços Online da Microsoft, como o Office 365, desde que a solução de federação de terceiros seja compatível com o Azure Active Directory.  Para dúvidas sobre a compatibilidade, entre em contato com seu provedor de identidade.  Se você quiser ver uma lista de provedores de identidade testados anteriormente quanto à compatibilidade com o Microsoft Azure Active Directory, pela Microsoft, clique em [aqui](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>A Microsoft não fornece mais testes de validação a provedores de identidade independentes para compatibilidade com o Azure Active Directory. Se você deseja testar a interoperabilidade do seu produto, consulte estas [diretrizes](https://www.microsoft.com/download/details.aspx?id=56843). 

## <a name="next-steps"></a>Próximas etapas

- [Integrar seus diretórios locais no Azure Active Directory](whatis-hybrid-identity.md)
- [Azure AD Connect e federação](how-to-connect-fed-whatis.md)
