---
title: Visão geral do controle de manutenção para máquinas virtuais do Azure usando o portal do Azure
description: Saiba como controlar quando a manutenção é aplicada às suas VMs do Azure usando o controle de manutenção.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 4c5e30d0607db2d529ae41ebab6dc82e925ff2a8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82139193"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>Gerenciando atualizações de plataforma com o controle de manutenção 

Gerenciar atualizações de plataforma, que não exigem uma reinicialização, usando o controle de manutenção. O Azure frequentemente atualiza sua infraestrutura para melhorar a confiabilidade, desempenho, segurança ou lançamento de novos recursos. A maioria das atualizações é transparente para os usuários. Algumas cargas de trabalho confidenciais, como jogos, streaming de mídia e transações financeiras, não podem tolerar até poucos segundos de uma VM congelando ou desconectando para manutenção. O controle de manutenção oferece a opção de aguardar atualizações de plataforma e aplicá-las em uma janela sem interrupção de 35 dias. 

O controle de manutenção permite que você decida quando aplicar atualizações às VMs isoladas e aos hosts dedicados do Azure.

Com o controle de manutenção, você pode:
- Atualizações em lote em um pacote de atualização.
- Aguarde até 35 dias para aplicar atualizações. 
- Automatize as atualizações de plataforma para sua janela de manutenção usando Azure Functions.
- As configurações de manutenção funcionam em assinaturas e grupos de recursos. 

## <a name="limitations"></a>Limitações

- As VMs devem estar em um [host dedicado](./linux/dedicated-hosts.md)ou ser criadas usando um [tamanho de VM isolado](./linux/isolation.md).
- Após 35 dias, uma atualização será aplicada automaticamente.
- O usuário deve ter acesso de **colaborador de recurso** .

## <a name="management-options"></a>Opções de gerenciamento

Você pode criar e gerenciar configurações de manutenção usando qualquer uma das seguintes opções:

- [CLI do Azure](maintenance-control-cli.md)
- [PowerShell do Azure](maintenance-control-powershell.md)
- [Azure portal](maintenance-control-portal.md)

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte [manutenção e atualizações](maintenance-and-updates.md).
