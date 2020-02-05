---
title: Soluções VMware do Azure (AVS)-componentes VMware de nuvem privada da AVS
description: Descreve como os componentes do VMware são instalados na nuvem privada de AVS
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3ea6c22c3957f72a0a416ce7ae42c62ff5a0791a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016775"
---
# <a name="avs-private-cloud-vmware-components"></a>Componentes do VMware na nuvem privada da AVS

Uma nuvem privada de AVS é um ambiente isolado de pilha VMware (hosts ESXi, vCenter, vSAN e NSX) gerenciado por um servidor vCenter em um domínio de gerenciamento. O serviço AVS permite implantar o VMware nativamente na infraestrutura bare-metal do Azure em locais do Azure. As nuvens privadas da AVS são integradas ao restante da nuvem do Azure. Uma nuvem privada de AVS é implantada com os seguintes componentes do VMware Stack:

* **VMware ESXi-** Hipervisor em nós dedicados do Azure
* **VMware vCenter –** Dispositivo para gerenciamento centralizado do ambiente de vSphere de nuvem privada de AVS
* **VSAN do VMware-** Solução de infraestrutura hiperconvergente
* **Data Center do VMware NSX-** Virtualização de rede e software de segurança  

## <a name="vmware-component-versions"></a>Versões de componentes do VMware

Uma pilha do VMware de nuvem privada de AVS é implantada com a versão de software a seguir.

| Componente | Versão | Versão licenciada |
|-----------|---------|------------------|
| ESXi | 6.7 U2 | Enterprise Plus |
| vCenter | 6.7 U2 | vCenter padrão |
| vSAN | 6.7 | Enterprise |
| Data Center do NSX | 2.4.1 | Avançado |

## <a name="esxi"></a>ESXi

O VMware ESXi é instalado em nós de AVS provisionados quando você cria uma nuvem privada de AVS. O ESXi fornece o hipervisor para implantar VMs (máquinas virtuais) de carga de trabalho. Os nós fornecem uma infraestrutura hiperconvergente (computação e armazenamento) em sua nuvem privada de AVS. Os nós fazem parte do cluster vSphere na nuvem privada da AVS. Cada nó tem quatro interfaces de redes físicas conectadas à rede Underlay. Duas interfaces de rede física são usadas para criar um **vSphere Distributed Switch (VDS)** no vCenter e duas são usadas para criar um **comutador virtual distribuído (N-VDS) gerenciado pelo NSX**. As interfaces de rede são configuradas no modo ativo-ativo para alta disponibilidade.

Saiba mais em VMware ESXi

## <a name="vcenter-server-appliance"></a>dispositivo vCenter Server

o dispositivo vCenter Server (VCSA) fornece autenticação, gerenciamento e funções de orquestração para soluções VMware (AVS). O VCSA com o PSC (controlador de serviços de plataforma) inserido é implantado quando você cria sua nuvem privada de AVS. O VCSA é implantado no cluster vSphere que é criado quando você implanta sua nuvem privada de AVS. Cada nuvem privada de AVS tem seu próprio VCSA. A expansão de uma nuvem privada de AVS adiciona os nós ao VCSA na nuvem privada da AVS.

### <a name="vcenter-single-sign-on"></a>logon único do vCenter

O controlador de serviços de plataforma inserido no VCSA está associado a um **domínio de logon único do vCenter**. O nome de domínio é **AVS. local**. Um **CloudOwner@AVS.com** de usuário padrão é criado para que você acesse o vCenter. Você pode adicionar suas fontes de identidade locais/do Azure Active Directory [para o vCenter](set-vcenter-identity.md).

## <a name="vsan-storage"></a>armazenamento vSAN

As nuvens privadas da AVS são criadas com o armazenamento totalmente configurado para todos os flash vSAN, local para o cluster. São necessários no mínimo três nós do mesmo SKU para criar um cluster vSphere com o vSAN datastore. A eliminação de duplicação e a compactação são habilitadas no repositório de armazenamento vSAN por padrão. Dois grupos de discos são criados em cada nó do cluster vSphere. Cada grupo de discos contém um disco de cache e três discos de capacidade.

Uma política de armazenamento padrão do vSAN é criada no cluster vSphere e aplicada ao repositório de armazenamento vSAN. Essa política determina como os objetos de armazenamento de VM são provisionados e alocados no repositório de armazenamento para garantir o nível de serviço necessário. A política de armazenamento define as **falhas a tolerar (FTT)** e o **método de tolerância a falhas**. Você pode criar novas políticas de armazenamento e aplicá-las às VMs. Para manter o SLA, a capacidade de 25% de reserva deve ser mantida no repositório de armazenamento vSAN. 

### <a name="default-vsan-storage-policy"></a>Política de armazenamento padrão do vSAN

A tabela abaixo mostra os parâmetros padrão da política de armazenamento do vSAN.

| Número de nós no cluster vSphere | FTT | Método de tolerância a falhas |
|------------------------------------|-----|--------------------------|
| 3 e 4 nós | 1 | RAID 1 (espelhamento) – cria 2 cópias |
| 5 a 16 nós | 2 | RAID 1 (espelhamento) – cria 3 cópias |

## <a name="nsx-data-center"></a>Data Center do NSX

O NSX Data Center fornece recursos de virtualização de rede, micro segmentação e segurança de rede em sua nuvem privada de AVS. Você pode configurar todos os serviços com suporte no data center do NSX em sua nuvem privada da AVS por meio do NSX. Quando você cria uma nuvem privada de AVS, os seguintes componentes do NSX são instalados e configurados.

* Gerenciador de NSXT
* Zonas de transporte
* Perfil de uplink do host e do Edge
* Comutador lógico para transporte de borda, Ext1 e ext2
* Pool de IPS para nó de transporte ESXi
* Pool de IPS para o nó de transporte de borda
* Nós de borda
* Regra de antiafinidade do DRS para VMs de controlador e borda
* Roteador da camada 0
* Habilitar o BGP no roteador tier0

## <a name="vsphere-cluster"></a>cluster vSphere

Os hosts ESXi são configurados como um cluster para garantir a alta disponibilidade da nuvem privada de AVS. Quando você cria uma nuvem privada de AVS, os componentes de gerenciamento do vSphere são implantados no primeiro cluster. Um pool de recursos é criado para componentes de gerenciamento e todas as VMs de gerenciamento são implantadas nesse pool de recursos. O primeiro cluster não pode ser excluído para reduzir a nuvem privada de AVS. o cluster vSphere fornece alta disponibilidade para VMs usando o **VSPHERE ha**. As falhas a serem toleradas são baseadas no número de nós disponíveis no cluster. Você pode usar a fórmula ```Number of nodes = 2N+1``` em que ```N``` é o número de falhas a tolerar.

### <a name="vsphere-cluster-limits"></a>limites de cluster vSphere

| Grupos | Limite |
|----------|-------|
| Número mínimo de nós para criar uma nuvem privada de AVS (primeiro cluster vSphere) | 3 |
| Número máximo de nós em um cluster vSphere em uma nuvem privada de AVS | 16 |
| Número máximo de nós em uma nuvem privada de AVS | 64 |
| Número máximo de clusters vSphere em uma nuvem privada de AVS | 21 |
| Número mínimo de nós em um novo cluster vSphere | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Manutenção da infraestrutura do VMware

Ocasionalmente, é necessário fazer alterações na configuração da infra-estrutura do VMware. Atualmente, esses intervalos podem ocorrer a cada 1-2 meses, mas a frequência é esperada para recusar ao longo do tempo. Esse tipo de manutenção geralmente pode ser feito sem interromper o consumo normal dos serviços de AVS. Durante um intervalo de manutenção do VMware, os seguintes serviços continuam a funcionar sem nenhum impacto:

* Plano e aplicativos de gerenciamento do VMware
* acesso do vCenter
* Todas as redes e armazenamento
* Todo o tráfego do Azure

## <a name="updates-and-upgrades"></a>Atualizações e upgrades

A AVS é responsável pelo gerenciamento do ciclo de vida do software VMware (ESXi, vCenter, PSC e NSX) na nuvem privada da AVS.

As atualizações de software incluem:

* **Patches**. Patches de segurança ou correções de bugs liberadas pelo VMware.
* **Atualizações**. Alteração de versão secundária de um componente de pilha do VMware.
* **Atualizações**. Alteração de versão principal de um componente do VMware Stack.

A AVS testa um patch de segurança crítico assim que ele se torna disponível no VMware. Por SLA, a AVS acumula o patch de segurança para ambientes de nuvem privada da AVS em uma semana.

A AVS fornece atualizações de manutenção trimestral para componentes de software VMware. Quando uma nova versão principal do software VMware estiver disponível, o AVS funcionará com os clientes para coordenar uma janela de manutenção adequada para atualização. 

## <a name="next-steps"></a>Próximos passos

* [Manutenção e atualizações de AVS](cloudsimple-maintenance-updates.md)
