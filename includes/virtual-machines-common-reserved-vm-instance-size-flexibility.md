---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02-18-2020
ms.openlocfilehash: aebe8f05bd1a3607da6d1153c87490891fc6b299
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77471441"
---
Ao comprar uma instância de VM reservada, você pode optar por otimizar a flexibilidade de tamanho da instância ou a prioridade da capacidade. Para obter mais informações sobre como configurar ou alterar a configuração de otimização para instâncias de VM reservadas, consulte [alterar a configuração de otimização para instâncias de VM reservadas](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).

Com uma instância de máquina virtual reservada que é otimizada para flexibilidade de tamanho de instância, a reserva que você compra pode se aplicar a tamanhos de máquinas virtuais (VMs) no mesmo grupo de flexibilidade de tamanho de instância. Por exemplo, se você comprar uma reserva para um tamanho de VM listado na série DSv2, como Standard_DS5_v2, o desconto de reserva poderá ser aplicado aos outros quatro tamanhos listados no mesmo grupo de flexibilidade de tamanho de instância:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Mas esse desconto de reserva não se aplica a tamanhos de VMs que estão listados em diferentes grupos de flexibilidade de tamanho de instância, como SKUs na série DSv2 de memória alta: Standard_DS11_v2, Standard_DS12_v2 e assim por diante.

Dentro do grupo de flexibilidade do tamanho da instância, o número de VMs a que o desconto de reserva se aplica depende do tamanho da VM que você escolher ao comprar uma reserva. Isso também depende das VMs que você tem em execução. A coluna ratio compara a superfície relativa para cada tamanho de VM nesse grupo de flexibilidade de tamanho de instância. Use o valor da proporção para calcular como o desconto de reserva se aplica às VMs você tem em execução.

## <a name="examples"></a>Exemplos

Os exemplos a seguir usam os tamanhos e proporções na tabela da série DSv2.

Você compra uma instância de VM reservada com o tamanho Standard_DS4_v2 em que a proporção ou o volume relativo em comparação comparada os outros tamanhos dessa série é 8.

- Cenário 1: executar oito VMs dimensionadas de acordo com Standard_DS1_v2 com uma proporção de 1. O desconto de reserva se aplica a todas essas oito VMs.
- Cenário 2: executar duas VMs dimensionadas de acordo com Standard_DS2_v2 com uma proporção de 2 cada. Além disso, executar uma VM dimensionada de acordo com Standard_DS3_v2 com uma proporção de 4. O volume total é de 2 + 2 + 4 = 8. Portanto, o desconto de reserva se aplica a todas essas três VMs.
- Cenário 3: executar uma Standard_DS5_v2 com uma proporção de 16. O desconto de reserva se aplica à metade do custo de computação da VM.

As seções a seguir mostram quais tamanhos estão no mesmo grupo de série de tamanho quando você compra uma instância de VM reservada otimizada para a flexibilidade de tamanho da instância.

## <a name="instance-size-flexibility-ratio-for-vms"></a>Taxa de flexibilidade de tamanho de instância para VMs 

O CSV abaixo tem os grupos de flexibilidade de tamanho de instância, ArmSkuName e as proporções.  

[Taxas de flexibilidade de tamanho de instância](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

Manteremos a URL do arquivo e o esquema fixo para que você possa consumir esse arquivo programaticamente. Os dados também estarão disponíveis por meio da API em breve.
