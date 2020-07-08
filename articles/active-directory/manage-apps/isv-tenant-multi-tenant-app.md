---
title: Criar um locatário do Azure para um aplicativo multilocatário
description: Diretrizes para fornecedores independentes de software na integração com o Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a0b63c130d7d1e72bd3320e40213ae3cb1069a6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763237"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Criar um locatário do Azure para um aplicativo multilocatário  

Para fornecer acesso ao aplicativo multilocatário, você deve criar um locatário Azure Active Directory para registrar o aplicativo e habilitar a Federação das identidades do cliente. Consulte [escolhendo o protocolo de Federação correto para seu aplicativo multilocatário](isv-choose-multi-tenant-federation.md). Esse locatário permitirá que você teste seu aplicativo e a Federação em um ambiente semelhante aos seus ambientes de clientes do Azure AD.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Custos de Hospedagem de um aplicativo multilocatário

Azure Active Directory está disponível em várias edições. [Consulte a comparação detalhada de recursos](https://azure.microsoft.com/pricing/details/active-directory/).

Você pode criar sua assinatura do Azure e o Azure Active Directory gratuitamente e usar recursos básicos.

## <a name="create-your-tenant"></a>Criar seu locatário

1. Crie seu locatário. Consulte [configurar um ambiente de desenvolvimento](../develop/quickstart-create-new-tenant.md).

2. Habilitar e testar o acesso de logon único ao seu aplicativo,

   a. **Para aplicativos OIDC ou Oath**, [Registre seu aplicativo](../develop/quickstart-register-app.md) como um aplicativo multilocatário. Selecione a opção contas em qualquer diretório organizacional e contas pessoais da Microsoft em tipos de conta com suporte

   b. **Para aplicativos baseados em SAML e WS-baseded**, você [configura aplicativos de logon único baseados em SAML](configure-single-sign-on-non-gallery-applications.md) usando um modelo SAML genérico no Azure AD.

Você também pode [converter um aplicativo de locatário único para multilocatário,](../develop/howto-convert-app-to-be-multi-tenant.md) se necessário.

## <a name="next-steps"></a>Próximas etapas

[Integrar o SSO em seu aplicativo](isv-sso-content.md)
