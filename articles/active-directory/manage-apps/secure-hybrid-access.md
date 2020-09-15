---
title: Acesso híbrido seguro do Azure AD | Microsoft Docs
description: Este artigo descreve soluções de parceiros para integrar seus aplicativos herdados locais, na nuvem pública ou na nuvem privada ao Azure AD. Proteja seus aplicativos herdados, conectando controladores ou redes de entrega de aplicativos ao Azure AD.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 9/10/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d64f7423d537958b6d3c388cb12f23bd2e30e36
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90087063"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Acesso híbrido seguro: proteger aplicativos herdados com o Azure Active Directory

Agora você pode proteger seus aplicativos de autenticação herdados locais e na nuvem conectando-os ao Azure Active Directory (AD) com:

- [Proxy de Aplicativo do AD do Azure](#secure-hybrid-access-sha-through-azure-ad-application-proxy)

- [Seus controladores e redes de entrega de aplicativos existentes](#sha-through-networking-and-delivery-controllers)

- [Aplicativos de rede virtual privada (VPN) e de perímetro definido pelo software (SDP)](#sha-through-vpn-and-sdp-applications)

Você pode preencher a lacuna e fortalecer sua postura de segurança em todos os aplicativos com recursos do Azure AD, como o [acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) do Azure AD e o Azure ad [Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection).

## <a name="secure-hybrid-access-sha-through-azure-ad-application-proxy"></a>Acesso híbrido seguro (SHA) por meio do Azure Proxy de Aplicativo do AD
  
Usando o [proxy de aplicativo](https://aka.ms/whyappproxy) , você pode fornecer [acesso remoto seguro](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) aos seus aplicativos Web locais. Os usuários não precisam usar uma VPN. Os usuários se beneficiam com a conexão fácil com seus aplicativos de qualquer dispositivo após um [logon único](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-sso). O proxy de aplicativo fornece acesso remoto como um serviço e permite que você [publique facilmente seus aplicativos locais](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application) para usuários fora da rede corporativa. Ele ajuda a dimensionar o gerenciamento de acesso à nuvem sem exigir que você modifique seus aplicativos locais. [Planeje uma implantação de proxy de aplicativo do AD do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-deployment-plan) como uma próxima etapa.

## <a name="azure-ad-partner-integrations"></a>Integrações de parceiros do Azure AD

### <a name="sha-through-networking-and-delivery-controllers"></a>SHA por meio de controladores de rede e entrega

Além do [proxy de aplicativo do AD do Azure](https://aka.ms/whyappproxy), para permitir que você use a [estrutura de confiança zero](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/), a Microsoft é parceira com provedores de terceiros. Você pode usar seus controladores de rede e de entrega existentes e proteger facilmente os aplicativos herdados que são essenciais para seus processos de negócios, mas que não poderiam ser protegidos antes do Azure AD. É provável que você já tenha tudo o que precisa para começar a proteger esses aplicativos.

![Imagem mostra acesso híbrido seguro com parceiros de rede e proxy de aplicativo](./media/secure-hybrid-access/secure-hybrid-access.png)

Os fornecedores de rede a seguir oferecem soluções predefinidas e diretrizes detalhadas para a integração com o Azure AD.

- [EAA (Akamai Enterprise Application Access)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)

- [ADC (Application Delivery Controller) Citrix](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)

- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)

- [Kemp](https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial)

### <a name="sha-through-vpn-and-sdp-applications"></a>SHA por meio de aplicativos VPN e SDP

Usando soluções VPN e SDP, você pode fornecer acesso seguro à sua rede corporativa a partir de qualquer dispositivo, a qualquer momento, em qualquer local, ao mesmo tempo em que protege os dados da sua organização. Com o Azure AD como um IDP (provedor de identidade), você pode usar métodos de autenticação e autorização modernos, como o [logon único](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) do Azure AD e a [autenticação multifator](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) para proteger seus aplicativos herdados locais.  

![Imagem mostra acesso híbrido seguro com parceiros VPN e proxy de aplicativo ](./media/secure-hybrid-access/app-proxy-vpn.png)

Os seguintes fornecedores de VPN e SDP oferecem soluções predefinidas e diretrizes detalhadas para a integração com o Azure AD.

• [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

• [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

• [F5 Big-IP APM](https://aka.ms/f5-hybridaccessguide)

• O [Palo Alto Networks global Protect](https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial)

• [ZPA (Zscaler Private Access)](https://aka.ms/zscaler-hybridaccessguide)
