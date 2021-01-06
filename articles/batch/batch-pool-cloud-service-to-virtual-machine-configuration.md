---
title: Migrar a configuração do pool do lote dos serviços de nuvem para máquinas virtuais
description: Saiba como atualizar sua configuração de pool para a configuração mais recente e recomendada
ms.topic: how-to
ms.date: 1/4/2021
ms.openlocfilehash: 52e1762dc8e81b3eb7e1bce388d91dfd2c76191a
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937551"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machines"></a>Migrar a configuração do pool do lote dos serviços de nuvem para máquinas virtuais

Os pools do lote podem ser criados usando [cloudServiceConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) ou [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration). ' virtualMachineConfiguration ' é a configuração recomendada, pois oferece suporte a todos os recursos do lote. os pools ' cloudServiceConfiguration ' não dão suporte a todos os recursos e nenhum recurso novo está planejado.

Se você usar pools ' cloudServiceConfiguration ', é altamente recomendável que você mova para usar os pools ' virtualMachineConfiguration '. Este artigo descreve como migrar para a configuração ' virtualMachineConfiguration ' recomendada.

## <a name="new-pools-are-required"></a>Novos pools são necessários

Os pools ativos existentes não podem ser atualizados de ' cloudServiceConfiguration ' para ' virtualMachineConfiguration ', novos pools devem ser criados. A criação de pools usando ' virtualMachineConfiguration ' tem suporte em todas as APIs do lote, ferramentas de linha de comando, portal do Azure e a interface do usuário do Batch Explorer.

Os tutoriais do [.net](tutorial-parallel-dotnet.md) e do [Python](tutorial-parallel-python.md) fornecem exemplos de criação de pool usando ' virtualMachineConfiguration '.

## <a name="pool-configuration-differences"></a>Diferenças de configuração de pool

O seguinte deve ser considerado ao atualizar a configuração do pool:

- OS nós de pool ' cloudServiceConfiguration ' são sempre o sistema operacional Windows, os pools ' virtualMachineConfiguration ' podem ser sistemas operacionais Linux ou Windows.
- Em comparação com os pools ' cloudServiceConfiguration ', os pools ' virtualMachineConfiguration ' têm um conjunto mais rico de recursos, como suporte a contêiner, discos de dados e criptografia de disco.
- OS nós de pool ' virtualMachineConfiguration ' utilizam discos do sistema operacional gerenciados. O [tipo de disco gerenciado](../virtual-machines/disks-types.md) que é usado para cada nó depende do tamanho da VM escolhido para o pool. Se um ' tamanho da VM for especificado para o pool, por exemplo, ' Standard_D2s_v3 ', será usado um SSD Premium. Se um tamanho de VM ' não s' for especificado, por exemplo, ' Standard_D2_v3 ', será usado um HDD padrão.

   > [!IMPORTANT]
   > Assim como ocorre com máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais, o disco gerenciado do sistema operacional usado para cada nó provoca um custo, que é adicional ao custo das VMs. Não há custo de disco do sistema operacional para os nós ' cloudServiceConfiguration ', pois o disco do sistema operacional é criado no SSD local dos nós.

- Os tempos de exclusão e de inicialização do pool e do nó podem diferir ligeiramente entre os pools ' cloudServiceConfiguration ' e os pools ' virtualMachineConfiguration '.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [as configurações de pool](nodes-and-pools.md#configurations).
- Saiba mais sobre [as práticas recomendadas de pool](best-practices.md#pools).
- Referência da API REST para [adição de pool](https://docs.microsoft.com/rest/api/batchservice/pool/add) e [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration).
