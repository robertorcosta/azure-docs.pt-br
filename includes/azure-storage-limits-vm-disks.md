---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: e89676505bc211d01a4327f8816a048218e579fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91829063"
---
Você pode anexar um número de discos de dados a uma máquina virtual do Azure. Com base nas metas de escalabilidade e de desempenho dos discos de dados de uma VM, você pode determinar o número e o tipo de disco necessários para atender aos seus requisitos de desempenho e capacidade.

> [!IMPORTANT]
> Para obter o desempenho ideal, limite a quantidade de discos altamente utilizados anexados à máquina virtual para evitar possíveis limitações. Se todos os discos anexados não forem altamente utilizados ao mesmo tempo, a máquina virtual poderá dar suporte a um número maior de discos.

**Para discos gerenciados Azure:**

A tabela a seguir ilustra os limites padrão e máximo do número de recursos por região e assinatura. Não há nenhum limite para o número de discos gerenciados, instantâneos e imagens por grupo de recursos.  

> | Recurso | Limite |
> | --- | --- |
> | Standard Managed Disks | 50.000 |
> | Discos gerenciados SSD Standard | 50.000 |
> | Discos gerenciados Premium | 50.000 |
> | Instantâneos de Standard_LRS | 50.000 |
> | Instantâneos de Standard_ZRS | 50.000 |
> | Imagem gerenciada | 50.000 |

**Para contas de armazenamento Standard:** uma conta de armazenamento Standard tem uma taxa de solicitação total máxima de 20.000 IOPS. A IOPS total de todos os discos da máquina virtual de uma conta de armazenamento Standard não deve exceder esse limite.
  
    You can roughly calculate the number of highly utilized disks supported by a single Standard storage account based on the request rate limit. For example, for a Basic tier VM, the maximum number of highly utilized disks is about 66, which is 20,000/300 IOPS per disk. The maximum number of highly utilized disks for a Standard tier VM is about 40, which is 20,000/500 IOPS per disk. 

**Para contas de armazenamento Premium:** uma conta de armazenamento Premium tem uma taxa de transferência total máxima de 50 Gbps. A taxa de transferência total de todos os discos da VM não deve exceder esse limite.

