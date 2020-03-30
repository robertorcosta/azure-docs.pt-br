---
title: Manutenção e atualizações
description: Visão geral da manutenção e atualizações de máquinas virtuais em execução no Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/18/2019
ms.author: shants
ms.openlocfilehash: eaf7616b3bd69828829342b4dca9247c009d3475
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250225"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Manutenção para máquinas virtuais no Azure

O Azure atualiza periodicamente sua plataforma para melhorar a confiabilidade, o desempenho e a segurança da infra-estrutura de host para máquinas virtuais. A finalidade dessas atualizações vai desde a aplicação de patch de componentes de software no ambiente de hospedagem até a atualização de componentes de rede ou o encerramento de hardware. 

As atualizações raramente afetam as VMs hospedadas. Quando as atualizações têm um efeito, o Azure escolhe o método menos impactante para atualizações:

- Se a atualização não exigir uma reinicialização, a VM será pausada enquanto o host estiver atualizado ou a VM for migrada ao vivo para um host já atualizado. 
- Se a manutenção exigir uma reinicialização, você será notificado da manutenção planejada. O Azure também fornece uma janela de tempo na qual você mesmo pode iniciar a manutenção, em um momento que funciona para você. A janela de auto-manutenção é tipicamente de 30 dias, a menos que a manutenção seja urgente. O Azure está investindo em tecnologias para reduzir o número de casos em que a manutenção planejada da plataforma exige que as VMs sejam reiniciadas. Para obter instruções sobre como gerenciar a manutenção planejada, consulte Manipulação de notificações de manutenção planejadas usando o Azure [CLI,](maintenance-notifications-cli.md) [PowerShell](maintenance-notifications-powershell.md) ou [portal](maintenance-notifications-portal.md).

Esta página descreve como o Microsoft Azure executa os dois tipos de manutenção. Para obter mais informações sobre eventos não planejados (paralisações), consulte [Gerenciar a disponibilidade de VMs para Windows](./windows/manage-availability.md) ou o artigo correspondente para [Linux](./linux/manage-availability.md).

Dentro de uma VM, você pode receber notificações sobre a próxima manutenção [usando Eventos Agendados para Windows](./windows/scheduled-events.md) ou para [Linux](./linux/scheduled-events.md).



## <a name="maintenance-that-doesnt-require-a-reboot"></a>Manutenção que não requer uma reinicialização

A maioria das atualizações da plataforma não afeta as VMs dos clientes. Quando uma atualização sem impacto não é possível, o Azure escolhe o mecanismo de atualização menos impactante para as VMs do cliente. 

A maior parte da manutenção sem impacto zero pausa o VM por menos de 10 segundos. Em certos casos, o Azure usa mecanismos de manutenção de preservação da memória. Esses mecanismos pausam a VM por até 30 segundos e preservam a memória na RAM. O VM é então retomado, e seu relógio é sincronizado automaticamente. 

A manutenção de preservação de memória funciona para mais de 90% das VMs do Azure. Não funciona para as séries G, M, N e H. O Azure usa cada vez mais tecnologias de migração ao vivo e melhora os mecanismos de manutenção de preservação da memória para reduzir as durações de pausa.  

Essas operações de manutenção que não requerem uma reinicialização são aplicadas um domínio de falha por vez. Eles param se receberem algum sinal de alerta de saúde. 

Esses tipos de atualizações podem afetar alguns aplicativos. Quando a VM é migrada ao vivo para um host diferente, algumas cargas de trabalho sensíveis podem mostrar uma leve degradação de desempenho nos poucos minutos que antecedem a pausa da VM. Para se preparar para a manutenção da VM e reduzir o impacto durante a manutenção do Azure, tente [usar eventos agendados para Windows](./windows/scheduled-events.md) ou [Linux](./linux/scheduled-events.md) para tais aplicativos. 

Há também um recurso, controle de manutenção, em visualização pública que pode ajudar a gerenciar a manutenção que não requer uma reinicialização. Você deve estar usando [hosts dedicados do Azure](./linux/dedicated-hosts.md) ou uma [VM isolada](../security/fundamentals/isolation-choices.md). O controle de manutenção lhe dá a opção de pular as atualizações da plataforma e aplicar as atualizações na sua escolha de tempo dentro de uma janela de rolamento de 35 dias. Para obter mais informações, consulte [Atualizações de controle com controle de manutenção e o Azure CLI](maintenance-control-cli.md).


### <a name="live-migration"></a>Migração ao vivo

Migração ao vivo é uma operação que não requer uma reinicialização e que preserva a memória para a VM. Causa uma pausa ou congelamento, normalmente não dura mais do que 5 segundos. Com exceção das séries G, M, N e H, todas as VMs de infra-estrutura como serviço (IaaS) são elegíveis para migração ao vivo. As VMs elegíveis representam mais de 90% das VMs IaaS que são implantadas na frota do Azure. 

A plataforma Azure inicia a migração ao vivo nos seguintes cenários:
- Manutenção planejada
- Falha de hardware
- Otimizações de alocação

Alguns cenários de manutenção planejada usam migração ao vivo, e você pode usar eventos agendados para saber com antecedência quando as operações de migração ao vivo começarão.

A migração ao vivo também pode ser usada para mover VMs quando algoritmos do Azure Machine Learning prevêem uma falha de hardware iminente ou quando você deseja otimizar alocações de VM. Para obter mais informações sobre modelagem preditiva que detecta instâncias de hardware degradado, consulte [Melhorar a resiliência do Azure VM com aprendizado de máquina preditivo e migração ao vivo](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). As notificações de migração ao vivo aparecem no portal Azure nos registros de Saúde do Monitor e do Serviço, bem como em Eventos Agendados, se você usar esses serviços.

## <a name="maintenance-that-requires-a-reboot"></a>Manutenção que requer uma reinicialização

No caso raro em que as VMs precisam ser reiniciadas para manutenção planejada, você será notificado com antecedência. A manutenção planejada tem duas fases: a fase de autoatendimento e uma fase de manutenção programada.

Durante a *fase de autoatendimento,* que normalmente dura quatro semanas, você inicia a manutenção em suas VMs. Como parte do autoatendimento, você pode consultar cada VM para ver seu status e o resultado de sua última solicitação de manutenção.

Quando você inicia a manutenção de autoatendimento, sua VM é reimplantada em um nó já atualizado. Como a VM é reinicializada, o disco temporário é perdido e endereços IP dinâmicos associados à interface de rede virtual são atualizados.

Se um erro surgir durante a manutenção do autoatendimento, a operação pára, a VM não é atualizada e você tem a opção de tentar novamente a manutenção do autoatendimento. 

Quando a fase de autoatendimento termina, *inicia-se* a fase de manutenção programada. Durante esta fase, você ainda pode consultar a fase de manutenção, mas você não pode iniciar a manutenção sozinho.

Para obter mais informações sobre o gerenciamento de manutenção que requer uma reinicialização, consulte **Manipulação de notificações de manutenção planejadas** usando o Azure [CLI,](maintenance-notifications-cli.md) [PowerShell](maintenance-notifications-powershell.md) ou [portal](maintenance-notifications-portal.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Considerações de disponibilidade durante a manutenção programada 

Se você decidir esperar até a fase de manutenção programada, existem algumas coisas que você deve considerar para manter a maior disponibilidade de suas VMs. 

#### <a name="paired-regions"></a>Regiões emparelhadas

Cada região do Azure é emparelhada com outra região dentro da mesma vizinhança geográfica. Juntos, eles fazem um par de regiões. Durante a fase de manutenção programada, o Azure atualiza apenas as VMs em uma única região de um par de regiões. Por exemplo, enquanto atualiza o VM no North Central DOS EUA, o Azure não atualiza nenhuma VM no Centro-Sul dos EUA ao mesmo tempo. No entanto, outras regiões, como o Norte da Europa, podem estar em manutenção simultaneamente com Leste dos EUA. Noções básicas sobre como funcionam os pares de região podem ajudá-lo a melhor distribuir suas VMs entre regiões. Para obter mais informações, consulte [pares de regiões do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Conjuntos de disponibilidade e conjuntos de dimensionamento

Ao implantar uma carga de trabalho em VMs do Azure, você pode criar as VMs dentro de um *conjunto de disponibilidade* para fornecer alta disponibilidade ao seu aplicativo. Usando conjuntos de disponibilidade, você pode garantir que durante uma paralisação ou eventos de manutenção que requerem uma reinicialização, pelo menos uma VM esteja disponível.

Em um conjunto de disponibilidade, VMs individuais são distribuídas em até 20 domínios de atualização. Durante a manutenção programada, apenas um domínio de atualização é atualizado a qualquer momento. Os domínios de atualização não são necessariamente atualizados sequencialmente. 

Os *conjuntos* de escala de máquinas virtuais são um recurso de computação do Azure que você pode usar para implantar e gerenciar um conjunto de VMs idênticas como um único recurso. O conjunto de escalas é implantado automaticamente em UDs, como VMs em um conjunto de disponibilidade. Como acontece com os conjuntos de disponibilidade, quando você usa conjuntos de escala, apenas um UD é atualizado a qualquer momento durante a manutenção programada.

Para obter mais informações sobre como configurar suas VMs para alta disponibilidade, consulte [Gerenciar a disponibilidade de suas VMs para Windows](./windows/manage-availability.md) ou o artigo correspondente para [Linux](./linux/manage-availability.md).

#### <a name="availability-zones"></a>Zonas de disponibilidade

As zonas de disponibilidade são locais físicos exclusivos dentro de uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. 

Uma zona de disponibilidade é uma combinação de um domínio de falha e um domínio de atualização. Se você criar três ou mais VMs em três zonas em uma região do Azure, suas VMs serão efetivamente distribuídas em três domínios de falha e três domínios de atualização. A plataforma do Azure reconhece essa distribuição nos domínios de atualização para garantir que as VMs em diferentes zonas não sejam atualizadas ao mesmo tempo.

Cada atualização de infra-estrutura é atualizada zona por região, dentro de uma única região. Mas, você pode ter a implantação acontecendo na Zona 1, e implantação diferente acontecendo na Zona 2, ao mesmo tempo. As implantações não são todas serializadas. Mas, uma única implantação só rola uma zona de cada vez para reduzir o risco.

## <a name="next-steps"></a>Próximas etapas 

Você pode usar o [Azure CLI,](maintenance-notifications-cli.md) [Azure PowerShell](maintenance-notifications-powershell.md) ou o [portal](maintenance-notifications-portal.md) para gerenciar a manutenção planejada. 