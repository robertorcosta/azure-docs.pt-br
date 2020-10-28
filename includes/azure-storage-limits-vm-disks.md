---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 10599b47d55a0b4ea1eb79cda3c31a2479efb7c0
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755594"
---
Você pode anexar um número de discos de dados a uma máquina virtual do Azure. Com base nas metas de escalabilidade e de desempenho dos discos de dados de uma VM, você pode determinar o número e o tipo de disco necessários para atender aos seus requisitos de desempenho e capacidade.

> [!IMPORTANT]
> Para obter o desempenho ideal, limite a quantidade de discos altamente utilizados anexados à máquina virtual para evitar possíveis limitações. Se todos os discos anexados não forem altamente utilizados ao mesmo tempo, a máquina virtual poderá dar suporte a um número maior de discos.

**Para discos gerenciados Azure:**

A tabela a seguir ilustra os limites padrão e máximo do número de recursos por região e assinatura. Os limites continuam os mesmos, independentemente dos discos criptografados com chaves de criptografia gerenciadas pela plataforma ou chaves gerenciadas pelo cliente. Não há nenhum limite para o número de discos gerenciados, instantâneos e imagens por grupo de recursos.  

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

