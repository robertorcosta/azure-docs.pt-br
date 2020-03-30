---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361551"
---
### <a name="identity-tier"></a>Nível de identidade 

A parceria Microsoft-Oracle permite configurar uma identidade unificada no Azure, OCI e no aplicativo Oracle. Para o conjunto de aplicativos JD Edwards EnterpriseOne ou PeopleSoft, uma instância do Oracle HTTP Server (OHS) é necessária para configurar o login único entre o Azure AD e o Oracle IDCS.

O OHS atua como um proxy reverso para o nível de aplicativo, o que significa que todas as solicitações para os aplicativos finais passam por ele. O Oracle Access Manager WebGate é um plugin de servidor web OHS que intercepta todas as solicitações que vão para o aplicativo final. Se um recurso que está sendo acessado estiver protegido (requer uma sessão autenticada), o WebGate iniciará o fluxo de autenticação OIDC com o Identity Cloud Service através do navegador do usuário. Para obter mais informações sobre os fluxos suportados pelo WebGate do OpenID Connect, consulte a [documentação](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html)do Oracle Access Manager .

Com essa configuração, um usuário já conectado ao Azure AD pode navegar até o aplicativo JD Edwards EnterpriseOne ou PeopleSoft sem fazer login novamente, através do Oracle Identity Cloud Service. Os clientes que implantarem essa solução ganham os benefícios do login único, incluindo um único conjunto de credenciais, uma melhor experiência de login, segurança melhorada e redução do custo do help-desk.

Para saber mais sobre como configurar o login único para JD Edwards EnterpriseOne ou PeopleSoft com a Azure AD, consulte o [whitepaper oracle](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)associado .