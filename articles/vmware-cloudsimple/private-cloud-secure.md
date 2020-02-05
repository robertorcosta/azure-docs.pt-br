---
title: Soluções VMware do Azure (AVS) – nuvem privada de AVS seguro
description: Descreve como proteger a nuvem privada da AVS (soluções VMware do Azure)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b053f1493c2380153711176a4748ebf90b479a6c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020073"
---
# <a name="how-to-secure-your-avs-private-cloud-environment"></a>Como proteger seu ambiente de nuvem privada da AVS

Defina o RBAC (controle de acesso baseado em função) para o serviço AVS, o portal da AVS e a nuvem privada da AVS do Azure. Usuários, grupos e funções para acessar o vCenter da nuvem privada de AVS são especificados usando o SSO do VMware. 

## <a name="rbac-for-avs-service"></a>RBAC para serviço AVS

A criação do serviço AVS requer a função de **proprietário** ou **colaborador** na assinatura do Azure. Por padrão, todos os proprietários e colaboradores podem criar um serviço AVS e acessar o portal AVS para criar e gerenciar nuvens privadas de AVS. Somente um serviço AVS pode ser criado por região. Para restringir o acesso a administradores específicos, siga o procedimento abaixo.

1. Criar um serviço AVS em um novo **grupo de recursos** no portal do Azure
2. Especifique o RBAC para o grupo de recursos.
3. Comprar nós e usar o mesmo grupo de recursos que o serviço AVS

Somente os usuários que têm privilégios de **proprietário** ou **colaborador** no grupo de recursos verão o serviço AVS e iniciarão o portal AVS.

Para obter mais informações sobre o RBAC, consulte [o que é o RBAC (controle de acesso baseado em função) para recursos do Azure](../role-based-access-control/overview.md).

## <a name="rbac-for-avs-private-cloud-vcenter"></a>RBAC para vCenter de nuvem privada da AVS

Um usuário padrão `CloudOwner@AVS.local` é criado no domínio de SSO do vCenter quando uma nuvem privada de AVS é criada. O usuário CloudOwner tem privilégios para gerenciar o vCenter. As fontes de identidade adicionais são adicionadas ao SSO do vCenter para conceder acesso a diferentes usuários. Funções e grupos predefinidos são configurados no vCenter que pode ser usado para adicionar usuários adicionais.

### <a name="add-new-users-to-vcenter"></a>Adicionar novos usuários ao vCenter

1. [Escalonar privilégios](escalate-private-cloud-privileges.md) para **CloudOwner@AVS.local** usuário na nuvem privada da AVS.
2. Entrar no vCenter usando o **CloudOwner@AVS.local**
3. [Adicionar usuários de logon único do vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Adicione usuários a [grupos de logon único do vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Para obter mais informações sobre funções e grupos predefinidos, consulte o artigo [sobre o modelo de permissão da nuvem privada AVS do VMware vCenter](learn-private-cloud-permissions.md) .

### <a name="add-new-identity-sources"></a>Adicionar novas fontes de identidade

Você pode adicionar outros provedores de identidade para o domínio de SSO do vCenter da sua nuvem privada da AVS. Os provedores de identidade fornecem autenticação e grupos de SSO do vCenter fornecem autorização para usuários.

* [Use Active Directory como um provedor de identidade em uma](set-vcenter-identity.md) nuvem privada do AVS privado.
* [Usar o Azure ad como um provedor de identidade em uma](azure-ad.md) nuvem privada do AVS privado

1. [Escalonar privilégios](escalate-private-cloud-privileges.md) para **CloudOwner@AVS.local** usuário na nuvem privada da AVS.
2. Entrar no vCenter usando o **CloudOwner@AVS.local**
3. Adicione usuários do provedor de identidade para [grupos de logon único do vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-avs-private-cloud-environment"></a>Proteger a rede em seu ambiente de nuvem privada de AVS

A segurança de rede do ambiente de nuvem privada da AVS é controlada pela proteção do acesso à rede e controle do tráfego de rede entre os recursos.

### <a name="access-to-avs-private-cloud-resources"></a>Acesso a recursos de nuvem privada AVS

O acesso de recursos e vCenter de nuvem privada da AVS é por uma conexão de rede segura:

* **[Conexão do ExpressRoute](on-premises-connection.md)** . O ExpressRoute fornece uma conexão segura, de alta largura de banda e baixa latência do seu ambiente local. O uso da conexão permite que seus serviços, redes e usuários locais acessem seu vCenter de nuvem privada de AVS.
* **[Gateway de VPN site a site](vpn-gateway.md)** . A VPN site a site fornece acesso aos recursos de nuvem privada da AVS no local por meio de um túnel seguro. Você especifica quais redes locais podem enviar e receber o tráfego de rede para sua nuvem privada da AVS.
* **[Gateway de VPN ponto a site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)** . Use a conexão VPN ponto a site para acesso remoto rápido ao seu vCenter de nuvem privada de AVS.

### <a name="control-network-traffic-in-avs-private-cloud"></a>Controlar o tráfego de rede na nuvem privada de AVS

As tabelas e as regras de firewall controlam o tráfego de rede na nuvem privada da AVS. A tabela de firewall permite controlar o tráfego de rede entre uma rede de origem ou endereço IP e uma rede de destino ou endereço IP com base na combinação de regras definidas na tabela.

1. Crie uma [tabela de firewall](firewall.md#add-a-new-firewall-table).
2. [Adicione regras](firewall.md#create-a-firewall-rule) à tabela de firewall.
3. [Anexar uma tabela de firewall a uma VLAN/sub-rede] (firewall. MD # Attach-vlanssubnet.
