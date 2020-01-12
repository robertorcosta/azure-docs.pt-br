---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/19/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 202bffb03a73acde67f0d4a03b31aa9a6fbf9824
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901625"
---
## <a name="deployment-considerations"></a>Considerações de implantação

* Para ver a disponibilidade das VMs da Série N, veja [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/).

* As VMs da Série N só podem ser implantadas no modelo de implantação do Resource Manager.

* As VMs da série N diferem no tipo de Armazenamento do Microsoft Azure que dão suporte aos discos. As VMs NC e NV dão suporte somente a discos de VM que executam backup em HDD (Armazenamento em Disco Standard). As NCv2, NCv3, ND, NDv2, e’ NVv2são compatíveis apenas com discos de VM que executam backup em SSD (Armazenamento em Disco Premium).

* Se você quiser implantar mais do que algumas VMs da Série N, considere uma assinatura pré-paga ou outras opções de compra. Se estiver usando uma [conta gratuita do Azure](https://azure.microsoft.com/free/), você poderá usar apenas um número limitado de núcleos de computação do Azure.

* Talvez seja necessário aumentar a cota de núcleos (por região) na sua assinatura do Azure, e aumentar a cota separada para núcleos NC, NCv2, NCv3, ND, NDv2, NV, ou NVv2. Para solicitar um aumento de cota, [abra uma solicitação de atendimento ao cliente online](../articles/azure-portal/supportability/how-to-create-azure-support-request.md) gratuitamente. Os limites padrão podem variar dependendo de sua categoria de assinatura.




