---
title: Solução Azure VMware por CloudSimple - Migrar VMs de carga de trabalho para nuvem privada
description: Descreve como migrar máquinas virtuais do vCenter local para o CloudSimple Private Cloud vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 87b8a112a319519dbde977ee30136a884137212d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019988"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-private-cloud-vcenter-environment"></a>Migre as VMs de carga de trabalho do vCenter local para o ambiente private cloud vCenter

Para migrar vMs de um datacenter local para sua CloudSimple Private Cloud, várias opções estão disponíveis.  O Private Cloud fornece acesso nativo ao VMware vCenter, e ferramentas suportadas pelo VMware podem ser usadas para migração de carga de trabalho. Este artigo descreve algumas das opções de migração do vCenter.

## <a name="prerequisites"></a>Pré-requisitos

A migração de VMs e dados do seu datacenter local requer conectividade de rede do datacenter para o ambiente Private Cloud.  Use qualquer um dos seguintes métodos para estabelecer a conectividade de rede:

* [Conexão VPN site-a-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) entre seu ambiente local e sua Nuvem Privada.
* Conexão ExpressRoute Global Reach entre o circuito ExpressRoute no local e um circuito CloudSimple ExpressRoute.

O caminho de rede do ambiente vCenter no local para a nuvem privada deve estar disponível para migração de VMs usando vMotion.  A rede vMotion em seu vCenter no local deve ter habilidades de roteamento.  Verifique se seu firewall permite todo o tráfego vMotion entre seu vCenter no local e o vCenter privado da Nuvem. (Na Nuvem Privada, o roteamento na rede vMotion é configurado por padrão.)

## <a name="migrate-isos-and-templates"></a>Migrar ISOs e modelos

Para criar novas máquinas virtuais em sua Nuvem Privada, use modelos ISOs e VM.  Para carregar os ISOs e modelos para o seu vCenter privado da Nuvem e disponibilizá-los, use o seguinte método.

1. Faça upload do ISO para o VCenter private cloud do vCenter UI.
2. [Publique uma biblioteca de conteúdo](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) no seu vCenter private cloud:

    1. Publique sua biblioteca de conteúdo no local.
    2. Crie uma nova biblioteca de conteúdo no VCenter private cloud.
    3. Inscreva-se na biblioteca de conteúdo publicada no local.
    4. Sincronize a biblioteca de conteúdo para acesso a conteúdos subscritos.

## <a name="migrate-vms-using-powercli"></a>Migrar VMs usando PowerCLI

Para migrar vMs do vCenter local para o vCenter de Nuvem Privada, use o VMware PowerCLI ou o Utilitário de Migração de Carga de Trabalho cross vCenter disponível no VMware Labs.  O script de exemplo a seguir mostra os comandos de migração PowerCLI.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> Para usar os nomes do servidor vCenter de destino e hosts ESXi, configure o encaminhamento de DNS do local para a nuvem privada.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>Migrar VMs usando A VPN DE CAMADA 2 do NSX

Essa opção permite a migração ao vivo de cargas de trabalho do seu ambiente VMware no local para a Nuvem Privada no Azure.  Com esta rede de camada 2 esticada, a sub-rede do local estará disponível na Nuvem Privada.  Após a migração, a nova atribuição de endereço IP não é necessária para as VMs.

[Migrar cargas de trabalho usando redes esticadas de Camada 2](migration-layer-2-vpn.md) descreve como usar uma VPN de Camada 2 para esticar uma rede de camada 2 do seu ambiente local para a nuvem privada.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>Migrar VMs usando ferramentas de backup e recuperação de desastres

A migração de VMs para nuvem privada pode ser feita usando ferramentas de backup/restauração e ferramentas de recuperação de desastres.  Use a Nuvem Privada como um alvo para restaurar backups criados usando uma ferramenta de terceiros.  O Private Cloud também pode ser usado como um alvo para a recuperação de desastres usando o VMware SRM ou uma ferramenta de terceiros.

Para obter mais informações usando essas ferramentas, consulte os seguintes tópicos:

* [Fazer backup de máquinas virtuais de carga de trabalho em CloudSimple Private Cloud usando Veeam B&R](backup-workloads-veeam.md)
* [Configure o CloudSimple Private Cloud como um local de recuperação de desastres para cargas de trabalho vMware no local](disaster-recovery-zerto.md)
