---
title: Componentes de VMware em nuvem privada
titleSuffix: Azure VMware Solution by CloudSimple
description: Descreve como os componentes vMware são instalados em nuvem privada
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9c9b80cd4d8a7a7ac5597d10bbb87095564bd461
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279501"
---
# <a name="private-cloud-vmware-components"></a>Componentes VMware de nuvem privada

Um Nuvem Privado é um ambiente isolado de VMware stack (hosts ESXi, vCenter, vSAN e NSX) gerenciado por um servidor vCenter em um domínio de gerenciamento.  O serviço CloudSimple permite que você implante o VMware nativamente na infra-estrutura de metal nu do Azure em locais do Azure.  As nuvens privadas são integradas com o resto da Nuvem Azul.  Uma nuvem privada é implantada com os seguintes componentes de pilha vMware:

* **VMware ESXi -** Hipervisor em azure dedos dedicados
* **VMware vCenter -** Aparelho para gerenciamento centralizado do ambiente privado vSphere em nuvem
* **VMware vSAN -** Solução de infra-estrutura hiperconvergente
* **Data Center VMware NSX -** Software de virtualização e segurança de rede  

## <a name="vmware-component-versions"></a>Versões de componentes VMware

Uma pilha De VMware privada em nuvem é implantada com a seguinte versão de software.

| Componente | Versão | Versão licenciada |
|-----------|---------|------------------|
| Esxi | 6.7U2 | Enterprise Plus |
| vCenter | 6.7U2 | Padrão vCenter |
| vSAN | 6.7 | Enterprise |
| NSX Data Center | 2.4.1 | Avançado |

## <a name="esxi"></a>Esxi

O VMware ESXi é instalado em nós CloudSimple provisionados quando você cria uma nuvem privada.  O ESXi fornece o hipervisor para a implantação de máquinas virtuais de carga de trabalho (VMs).  Os nodes fornecem infra-estrutura hiperconvergente (computação e armazenamento) em sua nuvem privada.  Os nós são uma parte do cluster vSphere na nuvem privada.  Cada nó tem quatro interfaces de redes físicas conectadas à rede underlay.  Duas interfaces de rede física são usadas para criar um **VSphere Distributed Switch (VDS)** no vCenter e duas são usadas para criar um **switch distribuído virtual gerenciado pelo NSX (N-VDS)**.  As interfaces de rede são configuradas no modo ativo ativo para alta disponibilidade.

Saiba mais sobre o VMware ESXi

## <a name="vcenter-server-appliance"></a>aparelho de servidor vCenter

O vCenter server appliance (VCSA) fornece as funções de autenticação, gerenciamento e orquestração da Solução VMware pelo CloudSimple. O VCSA com o PSC (Platform Services Controller, controlador de serviços de plataforma incorporado) é implantado quando você cria sua nuvem privada.  O VCSA é implantado no cluster vSphere criado quando você implanta sua nuvem privada.  Cada nuvem privada tem seu próprio VCSA.  A expansão de uma nuvem privada adiciona os nódulos ao VCSA na nuvem privada.

### <a name="vcenter-single-sign-on"></a>login único do vCenter

O Controlador de Serviços de Plataforma Embarcada no VCSA está associado a um **domínio de registro único do vCenter**.  O nome de domínio é **cloudsimple.local**.  Um usuário **CloudOwner@cloudsimple.com** padrão é criado para que você acesse o vCenter.  Você pode adicionar suas fontes de identidade de diretório ativo no local/Azure [para o vCenter](set-vcenter-identity.md).

## <a name="vsan-storage"></a>armazenamento vSAN

Nuvens privadas são criadas com armazenamento vSAN totalmente configurado, local para o cluster.  São necessários três nós mínimos do mesmo SKU para criar um cluster vSphere com datastore vSAN.  A eliminação da duplicação e a compactação estão habilitadas no datastore vSAN por padrão.  Dois grupos de disco são criados em cada nó do cluster vSphere. Cada grupo de disco contém um disco de cache e três discos de capacidade.

Uma política de armazenamento vSAN padrão é criada no cluster vSphere e aplicada ao armazenamento de dados vSAN.  Esta diretiva determina como os objetos de armazenamento vm são provisionados e alocados no armazenamento de dados para garantir o nível de serviço necessário.  A política de armazenamento define as **Falhas de Tolerar (FTT)** e o **método de tolerância**a falhas .  Você pode criar novas políticas de armazenamento e aplicá-las às VMs. Para manter o SLA, 25% da capacidade de reposição deve ser mantida no datastore vSAN.  

### <a name="default-vsan-storage-policy"></a>Política de armazenamento vSAN padrão

A tabela abaixo mostra os parâmetros de diretiva de armazenamento vSAN padrão.

| Número de nódulos no cluster vSphere | FTT | Método de tolerância a falhas |
|------------------------------------|-----|--------------------------|
| 3 e 4 nodos | 1 | RAID 1 (espelhamento) - cria 2 cópias |
| 5 a 16 nodos | 2 | RAID 1 (espelhamento) - cria 3 cópias |

## <a name="nsx-data-center"></a>NSX Data Center

O NSX Data Center oferece recursos de virtualização de rede, micro segmentação e segurança de rede em sua nuvem privada.  Você pode configurar todos os serviços suportados pelo NSX Data Center em sua nuvem privada através do NSX.  Quando você cria uma nuvem privada, os seguintes componentes DO NSX são instalados e configurados.

* Gerente nsxt
* Zonas de transporte
* Perfil de uplink de host e borda
* Interruptor lógico para transporte de borda, ext1 e ext2
* Pool IP para nó de transporte ESXi
* Piscina IP para nó de transporte de borda
* Nódulos de borda
* Regra anti-afinidade drs para controlador e VMs edge
* Roteador de nível 0
* Habilite o BGP no roteador tier0

## <a name="vsphere-cluster"></a>cluster vSphere

Os hosts ESXi são configurados como um cluster para garantir alta disponibilidade da nuvem privada.  Quando você cria uma nuvem privada, os componentes de gerenciamento do vSphere são implantados no primeiro cluster.  Um pool de recursos é criado para componentes de gerenciamento e todas as VMs de gerenciamento são implantadas neste pool de recursos. O primeiro cluster não pode ser excluído para encolher a nuvem privada.  O cluster vSphere fornece alta disponibilidade para VMs usando **o vSphere HA**.  As falhas de tolerar baseiam-se no número de nós disponíveis no cluster.  Você pode usar ```Number of nodes = 2N+1``` ```N``` a fórmula onde está o número de falhas a tolerar.

### <a name="vsphere-cluster-limits"></a>limites de cluster vSphere

| Recurso | Limite |
|----------|-------|
| Número mínimo de nódulos para criar uma nuvem privada (primeiro cluster vSphere) | 3 |
| Número máximo de nódulos em um cluster vSphere em uma nuvem privada | 16 |
| Número máximo de nódulos em uma nuvem privada | 64 |
| Número máximo de clusters vSphere em uma nuvem privada | 21 |
| Número mínimo de nós em um novo cluster vSphere | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Manutenção da infra-estrutura VMware

Ocasionalmente, é necessário fazer alterações na configuração da infra-estrutura VMware. Atualmente, esses intervalos podem ocorrer a cada 1-2 meses, mas espera-se que a frequência diminua ao longo do tempo. Esse tipo de manutenção geralmente pode ser feita sem interromper o consumo normal dos serviços CloudSimple. Durante um intervalo de manutenção do VMware, os seguintes serviços continuam funcionando sem qualquer impacto:

* Plano de gerenciamento vMware e aplicativos
* acesso ao vCenter
* Toda a rede e armazenamento
* Todo o tráfego do Azure

## <a name="updates-and-upgrades"></a>Atualizações e atualizações

O CloudSimple é responsável pelo gerenciamento do ciclo de vida do software VMware (ESXi, vCenter, PSC e NSX) na nuvem privada.

As atualizações de software incluem:

* **Patches**. Patches de segurança ou correções de bugs liberados pela VMware.
* **Atualizações**. Mudança de versão menor de um componente de pilha VMware.
* **Upgrades**. Mudança de versão principal de um componente de pilha VMware.

O CloudSimple testa um patch de segurança crítico assim que ele se torna disponível no VMware. Por SLA, o CloudSimple lança o patch de segurança para ambientes privados em nuvem dentro de uma semana.

O CloudSimple fornece atualizações trimestrais de manutenção para componentes de software VMware. Quando uma nova versão principal do software VMware está disponível, o CloudSimple trabalha com os clientes para coordenar uma janela de manutenção adequada para atualização.  

## <a name="next-steps"></a>Próximas etapas

* [Manutenção e atualizações do CloudSimple](cloudsimple-maintenance-updates.md)
