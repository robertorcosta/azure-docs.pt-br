---
title: Acesse a solução Azure VMware por CloudSimple a partir do local
titleSuffix: Azure VMware Solution by CloudSimple
description: Acessando sua solução Azure VMware pelo CloudSimple a partir de sua rede local através de um firewall
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 539665c4756a7dc87078922421b45a88404f58f1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868135"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>Acessando seu ambiente cloudsimple private cloud e aplicativos a partir de locais

Uma conexão pode ser configurada a partir da rede local para o CloudSimple usando o Azure ExpressRoute ou a VPN site-to-site.  Acesse seu CloudSimple Private Cloud vCenter e quaisquer cargas de trabalho que você executar na Nuvem Privada usando a conexão.  Você pode controlar quais portas são abertas na conexão usando um firewall em sua rede local.  Este artigo discute alguns dos requisitos típicos de porta de aplicativos.  Para quaisquer outros aplicativos, consulte a documentação do aplicativo para os requisitos da porta.

## <a name="ports-required-for-accessing-vcenter"></a>Portas necessárias para acessar o vCenter

Para acessar o gerenciador private cloud vCenter e NSX-T, as portas definidas na tabela abaixo devem ser abertas no firewall local.  

| Porta       | Fonte                           | Destino                      | Finalidade                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | Servidores locais DNS          | Servidores DNS privados em nuvem        | Necessário para o encaminhamento de pesquisas dns de *az.cloudsimple.io* para servidores DNS privados em nuvem da rede local.       |
| 53 (UDP)   | Servidores DNS privados em nuvem        | Servidores locais DNS          | Necessário para o encaminhamento de DNS procure nomes de domínio no local, do Private Cloud vCenter para servidores DNS no local. |
| 80 (TCP)   | Rede local              | Rede privada de gerenciamento de nuvem | Necessário para redirecionar o URL do vCenter de *http* para *https*.                                                           |
| 443 (TCP)  | Rede local              | Rede privada de gerenciamento de nuvem | Necessário para acessar o gerenciador vCenter e NSX-T da rede local.                                             |
| 8000 (TCP) | Rede local              | Rede privada de gerenciamento de nuvem | Necessário para vMotion de máquinas virtuais de on-premises para Private Cloud.                                            |
| 8000 (TCP) | Rede privada de gerenciamento de nuvem | Rede local              | Necessário para vMotion de máquinas virtuais de Private Cloud para on-premises.                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Portas necessárias para usar diretório ativo no local como fonte de identidade

Para configurar o diretório ativo no local como uma fonte de identidade no Private Cloud vCenter, as portas definidas na tabela devem ser abertas.  Consulte [Use o Azure AD como um provedor de identidade para o vCenter no CloudSimple Private Cloud](https://docs.microsoft.com/azure/vmware-cloudsimple/azure-ad/) para obter etapas de configuração.

| Porta         | Fonte                           | Destino                                         | Finalidade                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | Servidores DNS privados em nuvem        | Servidores locais DNS                             | Necessário para o encaminhamento de DNS procure nomes de domínio de diretórios ativos no local, do Private Cloud vCenter para servidores DNS no local.          |
| 389 (TCP/UDP) | Rede privada de gerenciamento de nuvem | Controladores de domínio de diretório ativo no local     | Necessário para a comunicação LDAP do servidor Private Cloud vCenter para controladores ativos de domínio de diretório para autenticação do usuário.                |
| 636 (TCP)     | Rede privada de gerenciamento de nuvem | Controladores de domínio de diretório ativo no local     | Necessário para a comunicação LDAP (LDAPS) segura do servidor Private Cloud vCenter aos controladores ativos de domínio de diretório para autenticação do usuário. |
| 3268 (TCP)    | Rede privada de gerenciamento de nuvem | Servidores globais de catálogo ativo no local | Necessário para a comunicação LDAP em implantações de controladores de vários domínios.                                                                        |
| 3269 (TCP)    | Rede privada de gerenciamento de nuvem | Servidores globais de catálogo ativo no local | Necessário para a comunicação LDAPS em implantações de controladores de vários domínios.                                                                       |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>Portas comuns necessárias para acessar máquinas virtuais de carga de trabalho

O acesso às máquinas virtuais de carga de trabalho em execução no Private Cloud requer que as portas sejam abertas no firewall local.  A tabela abaixo mostra algumas das portas comuns necessárias e seu propósito.  Para quaisquer requisitos de porta específicos do aplicativo, consulte a documentação do aplicativo.

| Porta         | Fonte                         | Destino                          | Finalidade                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Rede local            | Rede de carga de trabalho privada cloud       | Acesso seguro shell a máquinas virtuais Linux em execução no Private Cloud.              |
| 3389 (TCP)    | Rede local            | Rede de carga de trabalho privada cloud       | Desktop remoto para máquinas virtuais windows em execução no Private Cloud.                 |
| 80 (TCP)      | Rede local            | Rede de carga de trabalho privada cloud       | Acesse quaisquer servidores web implantados em máquinas virtuais em execução no Private Cloud.        |
| 443 (TCP)     | Rede local            | Rede de carga de trabalho privada cloud       | Acesse quaisquer servidores web seguros implantados em máquinas virtuais em execução no Private Cloud. |
| 389 (TCP/UDP) | Rede de carga de trabalho privada cloud | Rede de diretórios ativos no local | Junte as máquinas virtuais de carga de trabalho do Windows ao domínio de diretório ativo no local.       |
| 53 (UDP)      | Rede de carga de trabalho privada cloud | Rede local                  | Acesso ao serviço DNS para máquinas virtuais de carga de trabalho para servidores DNS no local.         |

## <a name="next-steps"></a>Próximas etapas

* [Crie e gerencie VLANs e Subnets](https://docs.microsoft.com/azure/vmware-cloudsimple/create-vlan-subnet/)
* [Conecte-se à rede local usando o Azure ExpressRoute](https://docs.microsoft.com/azure/vmware-cloudsimple/on-premises-connection/)
* [Configurar VPN site-to-site a partir de locais](https://docs.microsoft.com/azure/vmware-cloudsimple/vpn-gateway/)
