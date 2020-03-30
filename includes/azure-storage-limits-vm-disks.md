---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: c3028ed7629c41eece354dd2554ede9249bac4f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334965"
---
Você pode anexar uma série de discos de dados a uma máquina virtual do Azure. Com base nas metas de escalabilidade e desempenho dos discos de dados de uma VM, você pode determinar o número e o tipo de disco que você precisa para atender aos seus requisitos de desempenho e capacidade.

> [!IMPORTANT]
> Para obter o desempenho ideal, limite a quantidade de discos altamente utilizados anexados à máquina virtual para evitar possíveis limitações. Se todos os discos conectados não forem altamente utilizados ao mesmo tempo, a máquina virtual pode suportar um número maior de discos.

**Para discos gerenciados do Azure:**

A tabela a seguir ilustra os limites padrão e máximo do número de recursos por região por assinatura. Não há limite para o número de Discos Gerenciados, instantâneos e imagens por grupo de recursos.  

> | Recurso | Limite |
> | --- | --- |
> | Standard Managed Disks | 50.000 |
> | Discos gerenciados padrão de SSD | 50.000 |
> | Discos gerenciados premium | 50.000 |
> | Standard_LRS instantâneos | 50.000 |
> | instantâneos de Standard_ZRS | 50.000 |
> | Imagem gerenciada | 50.000 |

* **Para contas de armazenamento padrão:** Uma conta de armazenamento Standard tem uma taxa de solicitação total máxima de 20.000 IOPS. O IOPS total em todos os seus discos de máquina virtual em uma conta de armazenamento Padrão não deve exceder esse limite.
  
    Você pode calcular aproximadamente o número de discos altamente utilizados suportados por uma única conta de armazenamento Padrão com base no limite da taxa de solicitação. Por exemplo, para uma VM de nível básico, o número máximo de discos altamente utilizados é de cerca de 66, que é de 20.000/300 IOPS por disco. O número máximo de discos altamente utilizados para um VM de nível Padrão é de cerca de 40, que é de 20.000/500 IOPS por disco. 

* **Para contas de armazenamento Premium:** Uma conta de armazenamento Premium tem uma taxa de rendimento total máxima de 50 Gbps. A taxa de transferência total de todos os discos da VM não deve exceder esse limite.

