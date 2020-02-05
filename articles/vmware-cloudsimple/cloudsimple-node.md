---
title: Soluções VMware do Azure (AVS)-visão geral dos nós
description: Saiba mais sobre os nós e conceitos da AVS.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 08d8fc3e6f1f2f83cf3c4fee3fdafb0bd07e336c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024918"
---
# <a name="avs-nodes-overview"></a>Visão geral de nós da AVS

Os nós são os blocos de construção de uma nuvem privada de AVS. Um nó é:

* Um host de computação bare-metal dedicado em que um hipervisor VMware ESXi está instalado  
* Uma unidade de computação que você pode comprar ou reservar para criar nuvens privadas de AVS
* Disponível para compra ou reserva em uma região em que o serviço AVS está disponível

Você cria uma nuvem privada de AVS a partir dos nós comprados. Para criar uma nuvem privada de AVS, você precisa de um mínimo de três nós da mesma SKU. Para expandir uma nuvem privada de AVS, adicione outros nós. Você pode adicionar nós a um cluster existente ou criar um novo cluster adquirindo nós na portal do Azure e associando-os ao serviço AVS. Todos os nós adquiridos são visíveis no serviço AVS. 

## <a name="provisioned-nodes"></a>Nós provisionados

Os nós provisionados fornecem capacidade de pagamento conforme o uso. O provisionamento de nós ajuda você a dimensionar rapidamente seu cluster VMware sob demanda. Você pode adicionar nós conforme necessário ou excluir um nó provisionado para reduzir verticalmente o cluster do VMware. Os nós provisionados são cobrados mensalmente e cobrados na assinatura em que são provisionados.

* Se você pagar pela sua assinatura do Azure por cartão de crédito, o cartão será cobrado imediatamente.
* Se você for cobrado por fatura, os encargos serão exibidos na próxima fatura.

## <a name="vmware-solution-by-avs-nodes-sku"></a>Solução VMware por SKU de nós da AVS

Os seguintes tipos de nós estão disponíveis para provisionamento ou reserva.

| SKU           | CS28-nó                 | CS36-nó                 | CS36m-nó                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Região        | E.U.A. Leste, E.U.A. Oeste            | E.U.A. Leste, E.U.A. Oeste            | Oeste da Europa                 |
| CPU           | 2x 2,2 GHz, 28 núcleos (56 HT) | 2x 2,3 GHz, 36 núcleos (72 HT) | 2x 2,3 GHz, 36 núcleos (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Disco de cache    | 1,6-TB NVMe                 | 3,2-TB NVMe                 | 3,2-TB NVMe                 |
| Disco de capacidade | 5,625 TB brutos                | 11,25 TB brutos                | 15,36 TB brutos                |
| Tipo de armazenamento  | Todos os flash                   | Todos os flash                   | Todos os flash                   |

## <a name="limits"></a>Limites

Os limites de nó a seguir se aplicam a nuvens privadas de AVS.

| Grupos | Limite |
|----------|-------|
| Número mínimo de nós para criar uma nuvem privada de AVS | 3 |
| Número máximo de nós em um cluster em uma nuvem privada de AVS | 16 |
| Número máximo de nós em uma nuvem privada de AVS | 64 |
| Número mínimo de nós em um novo cluster | 3 |

## <a name="next-steps"></a>Próximos passos

* Saiba como [comprar nós](create-nodes.md)
* Saiba mais sobre [nuvens privadas da AVS](cloudsimple-private-cloud.md)
* Saiba como [provisionar nós](create-nodes.md)
* Saiba mais sobre [nuvens privadas](cloudsimple-private-cloud.md)
