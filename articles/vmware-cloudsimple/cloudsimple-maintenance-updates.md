---
title: Soluções VMware do Azure (AVS)-manutenção e atualizações da AVS
description: Descreve o processo de serviço AVS para manutenção agendada e atualizações
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bf5937183fc20579ecd21aca8543a0a78d4b9ff3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025020"
---
# <a name="avs-maintenance-and-updates"></a>Manutenção e atualizações de AVS

O ambiente de nuvem privada AVS foi projetado para não ter nenhum ponto único de falha.

* Os clusters ESXi são configurados com HA (alta disponibilidade) de vSphere. Os clusters são dimensionados para ter pelo menos um nó sobressalente para resiliência.
* O armazenamento primário redundante é fornecido pelo vSAN, que requer pelo menos três nós para fornecer proteção contra uma única falha. a vSAN pode ser configurada para fornecer maior resiliência para clusters maiores.
* as VMs do vCenter, PSC e NSX Manager são configuradas com o armazenamento RAID-10 para proteger contra falhas de armazenamento. As VMs são protegidas contra falhas de nó/rede por vSphere HA.
* Os hosts ESXi têm ventiladores e NICs redundantes.
* Os comutadores TOR e lombada são configurados em pares de alta disponibilidade para fornecer resiliência.

A AVS monitora continuamente as seguintes VMs quanto ao tempo de atividade e disponibilidade e fornece SLAs de disponibilidade:

* Hosts ESXi
* vCenter
* PSC
* NSX Manager

A AVS também monitora o seguinte continuamente em busca de falhas:

* Discos rígidos
* Portas NIC físicas
* Servidores
* Fãs
* Avançado
* Opções
* Portas do comutador

Se um disco ou nó falhar, um novo nó será adicionado automaticamente ao cluster do VMware afetado para trazê-lo de volta à integridade imediatamente.

A AVS faz backup, mantém e atualiza esses elementos do VMware nas nuvens privadas da AVS:

* ESXi
* Serviços da plataforma vCenter
* Controller
* vSAN
* NSX

## <a name="back-up-and-restore"></a>Backup e restauração

O backup da AVS inclui:

* Backups incrementais noturnos de regras do vCenter, PSC e DVS.
* APIs nativas do vCenter para fazer backup de componentes na camada de aplicativo.
* Backup automático antes da atualização ou atualização do software de gerenciamento do VMware.
* a criptografia de dados do vCenter na origem antes que os dados sejam transferidos por um canal criptografado do TLS 1.2 para o Azure. Os dados são armazenados em um blob do Azure onde são replicados entre regiões.

Você pode solicitar uma restauração abrindo um [solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Manutenção

A AVS faz vários tipos de manutenção planejada.

### <a name="backendinternal-maintenance"></a>Back-end/manutenção interna

Essa manutenção normalmente envolve a reconfiguração de ativos físicos ou a instalação de patches de software. Ele não afeta o consumo normal dos ativos que estão sendo atendidos. Com NICs redundantes indo para cada rack físico, o tráfego de rede normal e as operações de nuvem privada de AVS não são afetados. Você poderá notar um impacto no desempenho somente se sua organização espera usar a largura de banda totalmente redundante durante o intervalo de manutenção.

### <a name="avs-portal-maintenance"></a>Manutenção do portal de sincronização automática

Um tempo de inatividade de serviço limitado é necessário quando a infraestrutura ou o plano de controle da AVS é atualizado. Atualmente, os intervalos de manutenção podem ser tão frequentes quanto uma vez por mês. Espera-se que a frequência seja recusada ao longo do tempo. A AVS fornece notificação para manutenção do portal e mantém o intervalo o mais curto possível. Durante um intervalo de manutenção do portal, os seguintes serviços continuam a funcionar sem nenhum impacto:

* Plano e aplicativos de gerenciamento do VMware
* acesso do vCenter
* Todas as redes e armazenamento
* Todo o tráfego do Azure

### <a name="vmware-infrastructure-maintenance"></a>Manutenção da infraestrutura do VMware

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

[Fazer backup de VMs de carga de trabalho usando Veeam](backup-workloads-veeam.md)
