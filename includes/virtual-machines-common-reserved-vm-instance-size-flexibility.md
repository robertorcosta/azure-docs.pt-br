---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02-18-2020
ms.openlocfilehash: aebe8f05bd1a3607da6d1153c87490891fc6b299
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471441"
---
Ao comprar uma VM Instance reservada, você pode optar por otimizar, por exemplo, flexibilidade de tamanho ou prioridade de capacidade. Para obter mais informações sobre como definir ou alterar a configuração otimizada para instâncias de VM reservadas, consulte [Alterar a configuração otimizar para instâncias vm reservadas](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).

Com uma instância de máquina virtual reservada que é otimizada para flexibilidade de tamanho por exemplo, a reserva que você compra pode aplicar-se aos tamanhos de máquinas virtuais (VMs) no mesmo grupo de flexibilidade de tamanho de instância. Por exemplo, se você comprar uma reserva para um tamanho VM que está listado na série DSv2, como Standard_DS5_v2, o desconto de reserva pode se aplicar aos outros quatro tamanhos que estão listados no mesmo grupo de flexibilidade de tamanho de instância:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Mas esse desconto de reserva não se aplica a tamanhos de VMs que estão listados em diferentes grupos de flexibilidade de tamanho de instância, como SKUs na Série DSv2 High Memory: Standard_DS11_v2, Standard_DS12_v2 e assim por diante.

Dentro do grupo de flexibilidade de tamanho de exemplo, o número de VMs que o desconto de reserva se aplica depende do tamanho da VM que você escolhe quando compra uma reserva. Isso também depende das VMs que você tem em execução. A coluna razão compara a pegada relativa para cada tamanho de VM nesse grupo de flexibilidade de tamanho de instância. Use o valor da proporção para calcular como o desconto de reserva se aplica às VMs você tem em execução.

## <a name="examples"></a>Exemplos

Os exemplos a seguir usam os tamanhos e proporções na tabela da série DSv2.

Você compra uma instância de VM reservada com o tamanho Standard_DS4_v2 em que a proporção ou o volume relativo em comparação comparada os outros tamanhos dessa série é 8.

- Cenário 1: executar oito VMs dimensionadas de acordo com Standard_DS1_v2 com uma proporção de 1. O desconto de reserva se aplica a todas essas oito VMs.
- Cenário 2: executar duas VMs dimensionadas de acordo com Standard_DS2_v2 com uma proporção de 2 cada. Além disso, executar uma VM dimensionada de acordo com Standard_DS3_v2 com uma proporção de 4. O volume total é de 2 + 2 + 4 = 8. Portanto, o desconto de reserva se aplica a todas essas três VMs.
- Cenário 3: executar uma Standard_DS5_v2 com uma proporção de 16. O desconto de reserva se aplica à metade do custo de computação da VM.

As seções a seguir mostram quais tamanhos estão no mesmo grupo de série de tamanho quando você compra uma instância de VM reservada otimizada para a flexibilidade de tamanho da instância.

## <a name="instance-size-flexibility-ratio-for-vms"></a>Taxa de flexibilidade de tamanho de instância para VMs 

CSV abaixo tem os grupos de flexibilidade de tamanho de ocorrência, ArmSkuName e as proporções.  

[Proporções de flexibilidade de tamanho de instância](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

Vamos manter a URL do arquivo e o esquema fixo para que você possa consumir este arquivo de forma programática. Os dados também estarão disponíveis através da API em breve.
