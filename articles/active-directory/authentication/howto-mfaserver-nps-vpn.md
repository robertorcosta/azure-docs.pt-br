---
title: Servidor Azure MFA e VPNs de terceiros - Azure Active Directory
description: Guias de configuração passo a passo para que o Azure MFA Server se integre com Cisco, Citrix e Juniper.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01decb99a9eb24ae60250f83f1f961b4c1690bc0
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652856"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Cenários avançados com o Azure MFA Server e soluções VPN de terceiros

O Azure Multi-Factor Authentication Server (Azure MFA Server) pode ser usado para se conectar perfeitamente com várias soluções VPN de terceiros. Este artigo se&reg; concentra no aparelho Cisco ASA VPN, no citrix NetScaler SSL VPN e no aparelho Juniper Networks Secure Access/Pulse Secure Connect Secure SSL VPN. Criamos guias de configuração para abordar esses três dispositivos comuns. O Azure MFA Server também pode se integrar com a maioria dos outros sistemas que usam RADIUS, LDAP, IIS ou autenticação baseada em sinistros para AD FS. Você pode encontrar mais detalhes nas [configurações do Azure MFA Server](howto-mfaserver-deploy.md#next-steps).

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferecerá mais o MFA Server para novas implantações. Novos clientes que gostariam de exigir autenticação multifatorial de seus usuários devem usar a Autenticação Multifatorial baseada na nuvem. Os clientes existentes que ativaram o MFA Server antes de 1º de julho poderão baixar a versão mais recente, atualizações futuras e gerar credenciais de ativação como de costume.

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Cisco ASA VPN appliance e Azure MFA Server
O Azure MFA Server&reg; integra-se ao seu aparelho Cisco&reg; ASA VPN para fornecer segurança adicional para logins Cisco AnyConnect VPN e acesso ao portal.  Use o protocolo LDAP ou RADIUS.  Selecione um dos procedimentos a seguir para baixar os guias passo a passo de configuração detalhados.

| Guia de configuração | Descrição |
| --- | --- |
| [Cisco ASA com VPN Anyconnect e configuração do Azure MFA para LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integrar o dispositivo VPN Cisco ASA ao Azure MFA usando LDAP |
| [Configuração do Cisco ASA com VPN Anyconnect e do Azure MFA para RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integrar o dispositivo VPN Cisco ASA ao Azure MFA usando RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix NetScaler SSL VPN e Azure MFA Server
O Azure MFA Server integra-se ao seu aparelho Citrix NetScaler SSL VPN para fornecer segurança adicional para logins Citrix NetScaler SSL VPN e acesso ao portal.  Use o protocolo LDAP ou RADIUS.  Selecione um dos procedimentos a seguir para baixar os guias passo a passo de configuração detalhados.

| Guia de configuração | Descrição |
| --- | --- |
| [Configuração da VPN do Citrix NetScaler SSL e do Azure MFA para LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integrar a VPN Citrix NetScaler SSL ao dispositivo Azure MFA usando LDAP |
| [Configuração da VPN Citrix NetScaler SSL e do Azure MFA para RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integrar o dispositivo VPN Citrix NetScaler SSL ao Azure MFA usando RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>Juniper/Pulse Secure SSL VPN appliance e Azure MFA Server
O Azure MFA Server integra-se ao seu aparelho Juniper/Pulse Secure SSL VPN para fornecer segurança adicional para logins Juniper/Pulse Secure SSL VPN e acesso ao portal.  Use o protocolo LDAP ou RADIUS.  Selecione um dos procedimentos a seguir para baixar os guias passo a passo de configuração detalhados.

| Guia de configuração | Descrição |
| --- | --- |
| [Configuração da VPN do Juniper/Pulse Secure SSL e do Azure MFA para LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integrar o dispositivo VPN Juniper/Pulse Secure SSL ao Azure MFA usando LDAP |
| [Configuração da VPN do Juniper/Pulse Secure SSL e do Azure MFA para RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integrar o dispositivo VPN Juniper/Pulse Secure SSL ao Azure MFA usando RADIUS |

## <a name="next-steps"></a>Próximas etapas

- [Aumentar sua infraestrutura de autenticação atual com a extensão NPS para a Autenticação Multifator do Azure](howto-mfa-nps-extension.md)

- [Configure as configurações de autenticação multifatorial do Azure](howto-mfa-mfasettings.md)
