---
title: Crie um inquilino do Azure para um aplicativo de vários inquilinos
description: Orientação para fornecedores independentes de software sobre a integração com o Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 637adba89445e6974e83486f0641576225ccd268
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70812620"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Crie um inquilino do Azure para um aplicativo de vários inquilinos  

Para fornecer acesso ao seu aplicativo multi-inquilino, você deve criar um inquilino do Azure Active Directory para registrar o aplicativo e habilitar a federação das identidades de seus clientes. Consulte [Escolher o protocolo de federação certo para o seu aplicativo multi-inquilino](isv-choose-multi-tenant-federation.md). Este inquilino permitirá que você teste sua aplicação e a federação em um ambiente semelhante aos ambientes Azure AD de seus clientes.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Custos de hospedagem de um aplicativo multilocatário

O Azure Active Directory está disponível em várias edições. [Veja a comparação detalhada de recursos](https://azure.microsoft.com/pricing/details/active-directory/).

Você pode criar sua assinatura do Azure e diretório ativo do Azure gratuitamente e usar recursos básicos.

## <a name="create-your-tenant"></a>Crie seu inquilino

1. Crie seu inquilino. Consulte [Configurar um ambiente de dev](../develop/quickstart-create-new-tenant.md).

2. Habilite e teste o acesso único de logon ao seu aplicativo,

   a. **Para aplicações OIDC ou Oath,** [registre sua aplicação](../develop/quickstart-register-app.md) como um aplicativo multi-inquilino. Selecione as Contas em qualquer diretório organizacional e opção de contas microsoft pessoais nos tipos de conta suportada

   b. **Para aplicativos baseados em SAML e WS-Fed,** você configura aplicativos [de login único baseados em SAML](configure-single-sign-on-non-gallery-applications.md) usando um modelo SAML genérico no Azure AD.

Você também pode [converter um aplicativo de inquilino único para multi-inquilino,](../develop/howto-convert-app-to-be-multi-tenant.md) se necessário.

## <a name="next-steps"></a>Próximas etapas

[Integre o SSO em sua aplicação](isv-sso-content.md)
