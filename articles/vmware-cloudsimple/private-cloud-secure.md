---
title: Azure VMware Solutions by CloudSimple - Secure Private Cloud
description: Descreve como proteger as soluções Azure VMware pela CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4541874a9e8fc4111e5c65d02f07535c4d14f9f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565971"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>Como proteger seu ambiente de Nuvem Privada

Defina o Controle de Acesso Baseado em Função (RBAC) para o CloudSimple Service, o portal CloudSimple e o Private Cloud do Azure.  Usuários, grupos e funções para acessar o vCenter of Private Cloud são especificados usando o VMware SSO.  

## <a name="rbac-for-cloudsimple-service"></a>RBAC para serviço CloudSimple

A criação do serviço CloudSimple requer **função proprietário** ou **contribuinte** na assinatura do Azure.  Por padrão, todos os proprietários e colaboradores podem criar um serviço CloudSimple e acessar o portal CloudSimple para criar e gerenciar nuvens privadas.  Apenas um serviço CloudSimple pode ser criado por região.  Para restringir o acesso a administradores específicos, siga o procedimento abaixo.

1. Crie um Serviço CloudSimple em um novo **grupo de recursos** no portal Azure
2. Especifique o RBAC para o grupo de recursos.
3. Compre á6hs e use o mesmo grupo de recursos que o serviço CloudSimple

Somente os usuários que possuem privilégios **de Proprietário** ou **Contribuinte** no grupo de recursos verão o serviço CloudSimple e iniciarão o portal CloudSimple.

Para obter mais informações sobre o RBAC, consulte [O que é o RBAC (Role-Based Access Control, controle de acesso baseado em função) para recursos do Azure](../role-based-access-control/overview.md).

## <a name="rbac-for-private-cloud-vcenter"></a>RBAC para VCenter de Nuvem Privada

Um usuário `CloudOwner@cloudsimple.local` padrão é criado no domínio vCenter SSO quando uma Nuvem Privada é criada.  O usuário cloudowner tem privilégios para gerenciar o vCenter. Fontes de identidade adicionais são adicionadas ao vCenter SSO para dar acesso a diferentes usuários.  Funções e grupos pré-definidos são configurados no vCenter que podem ser usados para adicionar usuários adicionais.

### <a name="add-new-users-to-vcenter"></a>Adicione novos usuários ao vCenter

1. [Aumente os privilégios](escalate-private-cloud-privileges.md) para o usuário **\@cloudOwner cloudsimple.local** na Nuvem Privada.
2. Faça login no vCenter usando **cloudOwner\@cloudsimple.local**
3. [Adicionar usuários de login único do vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Adicione os usuários aos [grupos de login único do vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Para obter mais informações sobre funções e grupos pré-definidos, consulte [o modelo de permissão CloudSimple Private Cloud do artigo vMware vCenter.](learn-private-cloud-permissions.md)

### <a name="add-new-identity-sources"></a>Adicionar novas fontes de identidade

Você pode adicionar provedores de identidade adicionais para o domínio vCenter SSO da sua Nuvem Privada.  Os provedores de identidade fornecem autenticação e os grupos VCenter SSO fornecem autorização para os usuários.

* [Use o Active Directory como provedor de identidade](set-vcenter-identity.md) no Private Cloud vCenter.
* [Use o Azure AD como provedor de identidade](azure-ad.md) no Private Cloud vCenter

1. [Aumente os privilégios](escalate-private-cloud-privileges.md) para o usuário **\@cloudOwner cloudsimple.local** na Nuvem Privada.
2. Faça login no vCenter usando **cloudOwner\@cloudsimple.local**
3. Adicione os usuários do provedor de identidade aos [grupos de login único do vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-private-cloud-environment"></a>Rede segura em seu ambiente de Nuvem Privada

A segurança de rede do ambiente Private Cloud é controlada por garantir o acesso à rede e controlar o tráfego de rede entre os recursos.

### <a name="access-to-private-cloud-resources"></a>Acesso aos recursos da Nuvem Privada

O vCenter privado da Cloud e o acesso aos recursos são por meio de uma conexão de rede segura:

* **[Conexão ExpressRoute](on-premises-connection.md)**. O ExpressRoute fornece uma conexão segura, de alta largura de banda e baixa latência do seu ambiente local.  O uso da conexão permite que seus serviços, redes e usuários no local acessem seu vCenter privado da Nuvem.
* **[Gateway VPN site-to-site](vpn-gateway.md)**. Vpn site-to-site dá acesso aos recursos da Nuvem Privada a partir do local através de um túnel seguro.  Você especifica quais redes locais podem enviar e receber tráfego de rede para sua Nuvem Privada.
* **[Gateway VPN ponto a ponto](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)**. Use a conexão VPN ponto a ponto para acesso remoto rápido ao seu vCenter privado da Nuvem.

### <a name="control-network-traffic-in-private-cloud"></a>Controle o tráfego da rede na Nuvem Privada

Tabelas de firewall e regras controlam o tráfego de rede na Nuvem Privada.  A tabela de firewall permite controlar o tráfego de rede entre uma rede de origem ou endereço IP e uma rede de destino ou endereço IP com base na combinação de regras definidas na tabela.

1. Crie uma [tabela de firewall](firewall.md#add-a-new-firewall-table).
2. [Adicione regras](firewall.md#create-a-firewall-rule) à tabela de firewall.
3. [Conecte uma tabela de firewall a uma VLAN/sub-rede](firewall.md#attach-vlans-subnet).
