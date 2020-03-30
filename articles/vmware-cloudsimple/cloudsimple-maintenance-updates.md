---
title: Manutenção e atualizações do CloudSimple
titleSuffix: Azure VMware Solution by CloudSimple
description: Descreve o processo de serviço CloudSimple para manutenção e atualizações programadas
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 826fae1123b355a4143118b53ba649f0939acaf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025020"
---
# <a name="cloudsimple-maintenance-and-updates"></a>Manutenção e atualizações do CloudSimple

O ambiente Private Cloud foi projetado para não ter um único ponto de falha.

* Os clusters ESXi são configurados com o ha (VSphere High Availability, alta disponibilidade) do vSphere. Os clusters são dimensionados para ter pelo menos um nó sobressalente para resiliência.
* O armazenamento primário redundante é fornecido pela vSAN, que requer pelo menos três nós para fornecer proteção contra uma única falha. o vSAN pode ser configurado para fornecer maior resiliência para clusters maiores.
* As VMs vCenter, PSC e NSX Manager são configuradas com armazenamento RAID-10 para proteger contra falhas de armazenamento. As VMs são protegidas contra falhas de nó/rede pelo vSphere HA.
* Os hosts ESXi têm ventiladores redundantes e NICs.
* Os switches TOR e coluna são configurados em pares ha para fornecer resiliência.

O CloudSimple monitora continuamente as seguintes VMs para tempo de atividade e disponibilidade e fornece SLAs de disponibilidade:

* Hosts ESXi
* vCenter
* Psc
* Gerente NSX

O CloudSimple também monitora continuamente as seguintes falhas:

* Discos rígidos
* Portas NIC físicas
* Servidores
* Fãs
* Potência
* Opções
* Portas de switch

Se um disco ou nó falhar, um novo nó será automaticamente adicionado ao cluster VMware afetado para trazê-lo de volta à saúde imediatamente.

O CloudSimple apoia, mantém e atualiza esses elementos do VMware nas Nuvens Privadas:

* Esxi
* Serviços de plataforma vCenter
* Controller
* vSAN
* Nsx

## <a name="back-up-and-restore"></a>Fazer backup e restaurar

O backup do CloudSimple inclui:

* Backups incrementais noturnos das regras do vCenter, PSC e DVS.
* APIs nativas do vCenter para fazer backup de componentes na camada de aplicativo.
* Backup automático antes de atualizar ou atualizar o software de gerenciamento VMware.
* criptografia de dados do vCenter na fonte antes que os dados sejam transferidos por um canal criptografado TLS1.2 para o Azure. Os dados são armazenados em uma bolha do Azure onde são replicados em todas as regiões.

Você pode solicitar uma restauração abrindo uma [solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Manutenção 

CloudSimple faz vários tipos de manutenção planejada.

### <a name="backendinternal-maintenance"></a>Backend/manutenção interna

Essa manutenção normalmente envolve reconfigurar ativos físicos ou instalar patches de software. Não afeta o consumo normal dos ativos que estão sendo atendidos. Com NICs redundantes indo para cada rack físico, o tráfego de rede normal e as operações de Nuvem Privada não são afetados. Você pode notar um impacto de desempenho apenas se sua organização espera usar toda a largura de banda redundante durante o intervalo de manutenção.

### <a name="cloudsimple-portal-maintenance"></a>Manutenção do portal CloudSimple

Algum tempo de inatividade de serviço limitado é necessário quando o plano de controle ou infra-estrutura do CloudSimple é atualizado. Atualmente, os intervalos de manutenção podem ser tão frequentes quanto uma vez por mês. Espera-se que a frequência diminua com o tempo. O CloudSimple fornece notificação para manutenção do portal e mantém o intervalo o mais curto possível. Durante um intervalo de manutenção do portal, os seguintes serviços continuam funcionando sem qualquer impacto:

* Plano de gerenciamento vMware e aplicativos
* acesso ao vCenter
* Toda a rede e armazenamento
* Todo o tráfego do Azure

### <a name="vmware-infrastructure-maintenance"></a>Manutenção da infra-estrutura VMware

Ocasionalmente, é necessário fazer alterações na configuração da infra-estrutura VMware.  Atualmente, esses intervalos podem ocorrer a cada 1-2 meses, mas espera-se que a frequência diminua ao longo do tempo. Esse tipo de manutenção geralmente pode ser feita sem interromper o consumo normal dos serviços CloudSimple. Durante um intervalo de manutenção do VMware, os seguintes serviços continuam funcionando sem qualquer impacto:

* Plano de gerenciamento vMware e aplicativos
* acesso ao vCenter
* Toda a rede e armazenamento
* Todo o tráfego do Azure

## <a name="updates-and-upgrades"></a>Atualizações e atualizações

O CloudSimple é responsável pelo gerenciamento do ciclo de vida do software VMware (ESXi, vCenter, PSC e NSX) na Nuvem Privada.

As atualizações de software incluem:

* **Patches**. Patches de segurança ou correções de bugs liberados pela VMware.
* **Atualizações**. Mudança de versão menor de um componente de pilha VMware.
* **Upgrades**. Mudança de versão principal de um componente de pilha VMware.

O CloudSimple testa um patch de segurança crítico assim que ele se torna disponível no VMware. Por SLA, o CloudSimple lança o patch de segurança para ambientes de Nuvem Privada dentro de uma semana.

O CloudSimple fornece atualizações trimestrais de manutenção para componentes de software VMware. Quando uma nova versão principal do software VMware está disponível, o CloudSimple trabalha com os clientes para coordenar uma janela de manutenção adequada para atualização.

## <a name="next-steps"></a>Próximas etapas

[Fazer backup de VMs de carga de trabalho usando veeam](backup-workloads-veeam.md)
