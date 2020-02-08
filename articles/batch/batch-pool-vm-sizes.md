---
title: Escolher tamanhos de VM para pools - Azure Batch | Microsoft Docs
description: Como escolher entre os tamanhos de VM disponíveis para nós de computação em pools de Lote do Azure
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: c18190ec5e5d079d51630a976681717a78a46e00
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087055"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Escolher um tamanho de VM para nós de computação em um pool do Lote do Azure

Ao selecionar um tamanho de nó para um pool de Lote do Azure, você pode escolher dentre quase todos os tamanhos de VM disponíveis no Azure. O Azure oferece uma variedade de tamanhos para VMs do Linux e do Windows para diferentes cargas de trabalho.

Há algumas exceções e limitações para escolher um tamanho de VM:

* Não há suporte para alguns tamanhos de VM ou séries de VM no lote.
* Alguns tamanhos de VM são restritos e precisam ser especificamente habilitados antes de serem alocados.

## <a name="supported-vm-series-and-sizes"></a>Séries e tamanhos de VM com suporte

### <a name="pools-in-virtual-machine-configuration"></a>Pools na configuração de Máquina Virtual

Os pools do lote na configuração de máquina virtual dão suporte a quase todos os tamanhos de VM ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Consulte a tabela a seguir para saber mais sobre tamanhos e restrições com suporte.

| Série da VM  | Tamanhos com suporte |
|------------|---------|
| A Básico | Todos os tamanhos *, exceto* Basic_A0 (a0) |
| Um | Todos os tamanhos *, exceto* Standard_A0 |
| Av2 | Todos os tamanhos |
| B | Nenhum |
| DC | Nenhum |
| Dv2, DSv2 | Todos os tamanhos |
| Dv3, Dsv3 | Todos os tamanhos |
| Dav4, Dasv4 | Nenhum-ainda não disponível |
| Ev3, Esv3 | Todos os tamanhos, exceto para E64is_v3 e E64i_v3 |
| Eav4, Easv4 | Nenhum-ainda não disponível |
| F, FS | Todos os tamanhos |
| Fsv2 | Todos os tamanhos |
| G, GS | Todos os tamanhos |
| H | Todos os tamanhos |
| HB<sup>1</sup> | Todos os tamanhos |
| HBv2<sup>1</sup> | Todos os tamanhos |
| HC<sup>1</sup> | Todos os tamanhos |
| Ls | Todos os tamanhos |
| Lsv2 | Nenhum-ainda não disponível |
| M<sup>1</sup> | Todos os tamanhos, exceto para m64, M64m, m128, M128m |
| Mv2 | Nenhum-ainda não disponível |
| NC | Todos os tamanhos |
| NCv2<sup>1</sup> | Todos os tamanhos |
| NCv3<sup>1</sup> | Todos os tamanhos |
| ND<sup>1</sup> | Todos os tamanhos |
| NDv2<sup>1</sup> | Nenhum-ainda não disponível |
| NV | Todos os tamanhos |
| NVv3<sup>1</sup> | Todos os tamanhos |
| NVv4 | Nenhum |
| SAP HANA | Nenhum |

<sup>1</sup> esses tamanhos de VM podem ser alocados em pools do lote na configuração de máquina virtual, mas você deve criar uma nova conta do lote e solicitar um [aumento de cota](batch-quota-limit.md#increase-a-quota)específico. Essa limitação será removida quando a cota de vCPU por série de VM tiver suporte total para contas do lote.

### <a name="pools-in-cloud-service-configuration"></a>Pools na configuração de Serviço de Nuvem

Os pools do lote na configuração do serviço de nuvem dão suporte a todos os [tamanhos de VM para serviços de nuvem](../cloud-services/cloud-services-sizes-specs.md) **, exceto** para o seguinte:

| Série da VM  | Tamanhos sem suporte |
|------------|-------------------|
| Séria A   | Extra pequeno       |
| Série Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Considerações de tamanhos

* **Requisitos do aplicativo** - considere as características e os requisitos dos aplicativos que você vai executar nos nós. Os aspectos como se o aplicativo tem multithread e quanta memória ele consome podem ajudar a determinar o tamanho do nó mais adequado e econômico. Para [cargas de trabalho de MPI](batch-mpi.md) de instâncias múltiplas ou aplicativos CUDA, considere tamanhos de VM de [HPC](../virtual-machines/linux/sizes-hpc.md) especializado ou [habilitado para GPU](../virtual-machines/linux/sizes-gpu.md), respectivamente. (Confira [Usar instâncias compatíveis com RDMA ou habilitadas para GPU em pools do Lote](batch-pool-compute-intensive-sizes.md).)

* **Tarefas por nó** - Geralmente, você seleciona um tamanho de nó supondo que uma tarefa seja executada no nó por vez. No entanto, pode ser vantajoso ter várias tarefas (e, portanto, várias instâncias do aplicativo) [executadas em paralelo](batch-parallel-node-tasks.md) em nós de computação durante a execução do trabalho. Nesse caso, é comum escolher um tamanho multicore de nó para acomodar a demanda crescente de execução de tarefas paralelas.

* **Carregar níveis para tarefas diferentes** - Todos os nós em um pool têm o mesmo tamanho. Se você pretende executar aplicativos com diferentes requisitos de sistema e/ou níveis de carga, é recomendável usar pools separados.

* **Disponibilidade de região** – uma série ou tamanho de VM pode não estar disponível nas regiões em que você cria suas contas do lote. Para verificar se um tamanho está disponível, consulte [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/).

* **Cotas** - As [cotas principais](batch-quota-limit.md#resource-quotas) na sua conta do Lote podem limitar o número de nós de um determinado tamanho que você pode adicionar a um pool do Lote. Para solicitar um aumento de cota, consulte [este artigo](batch-quota-limit.md#increase-a-quota). 

* **Configuração do pool** - Em geral, você tem mais opções de tamanho de VM quando você cria um pool na configuração da Máquina Virtual, em comparação com a configuração do serviço de nuvem.

## <a name="next-steps"></a>Próximas etapas

* Para uma visão geral detalhada do Lote, confira [Desenvolver soluções de computação paralela em grande escala com o Lote](batch-api-basics.md).
* Para obter informações sobre tamanhos de VM de computação intensiva, consulte [Usar instâncias compatíveis com RDMA ou habilitadas para GPU em pools do Lote](batch-pool-compute-intensive-sizes.md).
