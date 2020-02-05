---
title: Acessando as soluções do Azure VMware (AVS) no local
description: Acessando suas soluções do Azure VMware (AVS) de sua rede local por meio de um firewall
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 09b25dbdc8fc13c40ccd89b2cfd78611cedaac9d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024459"
---
# <a name="accessing-your-avs-private-cloud-environment-and-applications-from-on-premises"></a>Acessando seu ambiente de nuvem privada de AVS e aplicativos do local

Uma conexão pode ser configurada da rede local para a AVS usando o Azure ExpressRoute ou VPN site a site. Acesse seu vCenter de nuvem privada de AVS e quaisquer cargas de trabalho executadas na nuvem privada da AVS usando a conexão. Você pode controlar quais portas são abertas na conexão usando um firewall em sua rede local. Este artigo aborda alguns dos requisitos de porta de aplicativos típicos. Para qualquer outro aplicativo, consulte a documentação do aplicativo para obter os requisitos de porta.

## <a name="ports-required-for-accessing-vcenter"></a>Portas necessárias para acessar o vCenter

Para acessar seu Gerenciador de nuvem privada de sincronização automática do vCenter e NSX-T, as portas definidas na tabela a seguir devem ser abertas no firewall local. 

| Port       | Origem                           | Destino                      | Finalidade                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | Servidores DNS locais          | Servidores DNS da nuvem privada AVS        | Necessário para encaminhamento de pesquisa de DNS de *AZ. AVS.io* a AVS servidores DNS de nuvem privada da rede local.     |
| 53 (UDP)   | Servidores DNS da nuvem privada AVS        | Servidores DNS locais          | Necessário para o encaminhamento de DNS pesquisar nomes de domínio locais da AVS privado de nuvem privada para servidores DNS locais. |
| 80 (TCP)   | Rede local              | Rede de gerenciamento de nuvem privada AVS | Necessário para redirecionar a URL do vCenter de *http* para *https*.                                                         |
| 443 (TCP)  | Rede local              | Rede de gerenciamento de nuvem privada AVS | Necessário para acessar o vCenter e o NSX-T Manager da rede local.                                           |
| 8000 (TCP) | Rede local              | Rede de gerenciamento de nuvem privada AVS | Necessário para o vMotion de máquinas virtuais de nuvem privada local para AVS.                                          |
| 8000 (TCP) | Rede de gerenciamento de nuvem privada AVS | Rede local              | Necessário para o vMotion de máquinas virtuais de uma nuvem privada de AVS para local.                                          |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Portas necessárias para usar o Active Directory local como uma fonte de identidade

Para configurar o Active Directory local como uma fonte de identidade no vCenter de nuvem privada da AVS, as portas definidas na tabela devem ser abertas. Consulte [usar o Azure ad como um provedor de identidade para vCenter na nuvem privada da AVS AVS](https://docs.azure.cloudsimple.com/azure-ad/) para obter as etapas de configuração.

| Port         | Origem                           | Destino                                         | Finalidade                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | Servidores DNS da nuvem privada AVS        | Servidores DNS locais                             | Necessário para o encaminhamento do DNS Procure nomes de domínio do Active Directory local da AVS privado Cloud vCenter para servidores DNS locais.        |
| 389 (TCP/UDP) | Rede de gerenciamento de nuvem privada AVS | Controladores de domínio do Active Directory local     | Necessário para a comunicação LDAP do servidor vCenter privado de nuvem privada para controladores de domínio do Active Directory para autenticação de usuário.              |
| 636 (TCP)     | Rede de gerenciamento de nuvem privada AVS | Controladores de domínio do Active Directory local     | Necessário para comunicação LDAP (LDAPs) segura do servidor vCenter privado de nuvem privada para controladores de domínio do Active Directory para autenticação de usuário. |
| 3268 (TCP)    | Rede de gerenciamento de nuvem privada AVS | Servidores de catálogo global do Active Directory local | Necessário para a comunicação LDAP em implantações de controlador de vários domínios.                                                                      |
| 3269 (TCP)    | Rede de gerenciamento de nuvem privada AVS | Servidores de catálogo global do Active Directory local | Necessário para comunicação LDAPs em implantações de controlador de vários domínios.                                                                     |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>Portas comuns necessárias para acessar máquinas virtuais de carga de trabalho

As máquinas virtuais de carga de trabalho de acesso em execução na nuvem privada de AVS exigem que as portas sejam abertas no firewall local. A tabela abaixo mostra algumas das portas comuns necessárias e sua finalidade. Para qualquer requisito de porta específica do aplicativo, consulte a documentação do aplicativo.

| Port         | Origem                         | Destino                          | Finalidade                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Rede local            | Rede de carga de trabalho de nuvem privada AVS       | Proteja o acesso do Shell a máquinas virtuais do Linux em execução na sincronização privada da AVS.            |
| 3389 (TCP)    | Rede local            | Rede de carga de trabalho de nuvem privada AVS       | Área de trabalho remota para máquinas virtuais do Windows em execução na nuvem privada da AVS.               |
| 80 (TCP)      | Rede local            | Rede de carga de trabalho de nuvem privada AVS       | Acessar qualquer servidor Web implantado em máquinas virtuais em execução na nuvem privada da AVS.      |
| 443 (TCP)     | Rede local            | Rede de carga de trabalho de nuvem privada AVS       | Acesse qualquer servidor Web seguro implantado em máquinas virtuais em execução na nuvem privada da AVS. |
| 389 (TCP/UDP) | Rede de carga de trabalho de nuvem privada AVS | Rede do Active Directory local | Ingresse máquinas virtuais de carga de trabalho do Windows no domínio do Active Directory local.     |
| 53 (UDP)      | Rede de carga de trabalho de nuvem privada AVS | Rede local                  | Acesso do serviço DNS para máquinas virtuais de carga de trabalho para servidores DNS locais.       |

## <a name="next-steps"></a>Próximos passos

* [Criar e gerenciar VLANs e sub-redes](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
* [Conectar-se à rede local usando o Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Configurar VPN site a site do local](https://docs.azure.cloudsimple.com/vpn-gateway/)
