---
title: Azure AD Connect na Microsoft Cloud Alemanha
description: O Azure AD Connect integrará seus diretórios locais com o Azure Active Directory. Isso permite que você forneça uma identidade comum para os aplicativos do Office 365, Azure e SaaS integrados ao AD do Azure.
keywords: introdução ao Azure AD Connect, visão geral do Azure AD Connect, o que é o Azure AD Connect, instalar o active directory, Alemanha, Floresta Negra
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2bcb0caf-5d97-46cb-8c32-bda66cc22dad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62f0d82a543c0ae4e629eda3bca18b0a06322f2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "60381223"
---
# <a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Azure AD Connect na Microsoft Cloud Alemanha - Visualização Pública
## <a name="introduction"></a>Introdução
O Azure AD Connect fornece sincronização entre o Active Directory local e o Azure Active Directory.
Atualmente, muitos dos cenários em [Microsoft Cloud Alemanha](https://azure.microsoft.com/global-infrastructure/germany/
) devem ser realizados pela operadora. Ao usar o Microsoft Cloud Alemanha, saiba das seguintes informações:

* As URLs a seguir devem ser abertas em um servidor proxy para que a sincronização tenha êxito:
  
  * *.microsoftonline.de
  * *.windows.net
  * * Listas de Revogação de Certificados
* Quando você entrar em seu diretório do Azure AD, deverá usar uma conta com o domínio onmicrosoft.de.

 
## <a name="download"></a>Baixar
Você pode baixar o Azure AD Connect da folha do Azure AD Connect no portal.  Use as instruções abaixo para localizar a folha do Azure AD Connect.

### <a name="the-azure-ad-connect-blade"></a>A folha do Azure AD Connect
Após entrar no portal do Azure:

1. Vá para Procurar
2. Selecione Azure Active Directory
3. Em seguida, selecione Azure AD Connect

Você verá esses detalhes:

![Folha do Azure AD Connect](./media/reference-connect-germany/germany1.png)

A tabela a seguir descreve os recursos mostrados na folha.

| Title | Descrição |
| --- | --- |
| STATUS DA SINCRONIZAÇÃO |Informa se a sincronização está habilitada ou desabilitada. |
| ÚLTIMA SINCRONIZAÇÃO |A última vez que uma sincronização bem-sucedida foi concluída. |
| DOMÍNIOS FEDERADOS |Mostra o número de domínios federados configurado no momento. |

## <a name="installation"></a>Instalação
Para instalar o Azure AD Connect, você pode usar a documentação [aqui](how-to-connect-install-roadmap.md).

## <a name="advanced-features-and-additional-information"></a>Recursos avançados e informações adicionais
Para obter mais informações e orientação sobre as configurações personalizadas ou avançadas, acesse [Integrar suas identidades locais ao Azure Active Directory](whatis-hybrid-identity.md). Esta página fornece informações e links para orientações adicionais.

