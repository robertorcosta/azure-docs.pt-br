---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/03/2020
ms.author: alkohli
ms.openlocfilehash: 7ce49873b4e59bcf474deaea4420f56a72c9c589
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89082950"
---
O tamanho da VM determina a quantidade de recursos de computação, como memória, CPU e GPU que estão disponíveis para a VM. As máquinas virtuais devem ser criadas usando um tamanho de VM adequado para a carga de trabalho. Embora todos os computadores sejam executados no mesmo hardware, os tamanhos de máquina têm limites diferentes para o acesso ao disco, o que pode ajudá-lo a gerenciar o acesso geral ao disco em suas VMs. Se uma carga de trabalho aumentar, uma máquina virtual existente também poderá ser redimensionada.

As seguintes VMs da série Dv2 padrão têm suporte para a criação no dispositivo Azure Stack Edge.

### <a name="dv2-series"></a>Série Dv2
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Taxa de transferência máxima do disco do so (IOPS) | Taxa de transferência máxima de armazenamento temporário (IOPS) | Máximo de discos de dados/taxa de transferência (IOPS) | Máximo de NICs |
|-------------------|----|----|-----|----|------|------------|---------|
|**Standard_D1_v2** |1   |3,5 |50   |500 |3000  |4 / 4 x 500   |2 |
|**Standard_D2_v2** |2   |7   |100  |500 |6000  |8 / 8 x 500   |2 |
|**Standard_D3_v2** |4   |14  |200  |500 |12000 |16 / 16 x 500 |4 |
|**Standard_D4_v2** |8   |28  |400  |500 |24.000 |32 / 32 x 500 |8 |
|**Standard_D5_v2** |16  |56  |800  |500 |48000 |64 / 64x500 |8 |

### <a name="dsv2-series"></a>Série DSv2
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Taxa de transferência máxima do disco do so (IOPS) | Taxa de transferência máxima de armazenamento temporário (IOPS) | Máximo de discos de dados/taxa de transferência (IOPS) | Máximo de NICs |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standard_DS1_v2** |1   |3,5 |7   |1000 |4000  |4/4x2300   |2 |
|**Standard_DS2_v2** |2   |7   |14  |1000 |8000  |8/8x2300   |2 |
|**Standard_DS3_v2** |4   |14  |28  |1000 |16000 |16/16x2300 |4 |
|**Standard_DS4_v2** |8   |28  |56  |1000 |32000 |32/32x2300 |8 |
|**Standard_DS5_v2** |16  |56  |112 |1000 |64000 |64/64x2300 |8 |

### <a name="dv2-series"></a>Série Dv2
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Taxa de transferência máxima do disco do so (IOPS) | Taxa de transferência máxima de armazenamento temporário (IOPS) | Máximo de discos de dados/taxa de transferência (IOPS) | Máximo de NICs |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Standard_D11_v2** |2   |14  |100  |500 |6000   |8 / 8 x 500    |2 |
|**Standard_D12_v2** |4   |28  |200  |500 |12000  |16 / 16 x 500  |4 |
|**Standard_D13_v2** |8   |56  |400  |500 |24.000  |32 / 32 x 500  |8 |
|**Standard_D14_v2** |16  |112 |800  |500 |48000  |64 / 64x500  |8 |


### <a name="dsv2-series"></a>Série DSv2
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Taxa de transferência máxima do disco do so (IOPS) | Taxa de transferência máxima de armazenamento temporário (IOPS) | Máximo de discos de dados/taxa de transferência (IOPS) | Máximo de NICs |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_DS11_v2** |2   |14  |28   |1000 |8000   |4/4x2300    |2 |
|**Standard_DS12_v2** |4   |28  |56   |1000 |16000  |8/8x2300    |4 |
|**Standard_DS13_v2** |8   |56  |112  |1000 |32000  |16/16x2300  |8 |
|**Standard_DS14_v2** |16  |112 |224  |1000 |64000  |32/32x2300  |8 |

Para obter mais informações, acesse a [série Dv2 em uso geral tamanhos de VM](../articles/virtual-machines/dv2-dsv2-series.md#dv2-series).
