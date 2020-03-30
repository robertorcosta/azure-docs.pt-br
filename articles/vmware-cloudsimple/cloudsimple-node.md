---
title: Solução Azure VMware por CloudSimple - Visão geral dos nodes
description: Saiba mais sobre os nódulos e conceitos do CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65afe26a98a53b00b72a1ea2b49799db2049b727
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024918"
---
# <a name="cloudsimple-nodes-overview"></a>Visão geral dos nódulos CloudSimple

Nós são os blocos de construção de uma nuvem privada. Um nó é:

* Um host dedicado de computação em metal nu onde um hipervisor VMware ESXi é instalado  
* Uma unidade de computação que você pode prover ou reservar para criar Nuvens Privadas
* Disponível para provisão ou reserva em uma região onde o serviço CloudSimple está disponível

Você cria uma Nuvem Privada a partir dos nós provisionados. Para criar uma Nuvem Privada, você precisa de um mínimo de três nós do mesmo SKU. Para expandir uma Nuvem Privada, adicione outros nodos.  Você pode adicionar nós a um cluster existente ou criar um novo cluster provisionando nomes no portal Azure e associando-os ao serviço CloudSimple.  Todos os nós provisionados são visíveis o serviço CloudSimple.  

## <a name="provisioned-nodes"></a>Nódulos provisionados

Os nódulos provisionados fornecem capacidade de pagamento à medida que você vai. Os nós de provisionamento ajudam você a dimensionar rapidamente seu cluster VMware demanda. Você pode adicionar nós conforme necessário ou excluir um nó provisionado para reduzir o cluster VMware. Os nós provisionados são cobrados mensalmente e cobrados na assinatura onde são provisionados.

* Se você pagar sua assinatura do Azure com cartão de crédito, o cartão é cobrado imediatamente.
* Se você for cobrado por fatura, os encargos aparecem na sua próxima fatura.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>Solução VMware por nódulos CloudSimple SKU

Os seguintes tipos de nós estão disponíveis para provisionamento ou reserva.

| SKU           | CS28 - Nó                 | CS36 - Nó                 | CS36m - Nó                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Região        | Leste dos EUA, Oeste dos EUA            | Leste dos EUA, Oeste dos EUA            | Europa Ocidental                 |
| CPU           | 2x2,2 GHz, 28 Núcleos (56 HT) | 2x2,3 GHz, 36 Núcleos (72 HT) | 2x2,3 GHz, 36 Núcleos (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Disco de cache    | NVMe de 1,6 TB                 | NVMe de 3,2 TB                 | NVMe de 3,2 TB                 |
| Disco de capacidade | 5.625 TB Bruto                | 11,25 TB Bruto                | 15,36 TB Bruto                |
| Tipo de armazenamento  | Todos os Flash                   | Todos os Flash                   | Todos os Flash                   |

## <a name="limits"></a>limites

Os seguintes limites de nó se aplicam a Nuvens Privadas.

| Recurso | Limite |
|----------|-------|
| Número mínimo de nós para criar uma Nuvem Privada | 3 |
| Número máximo de nódulos em um cluster em uma nuvem privada | 16 |
| Número máximo de nódulos em uma nuvem privada | 64 |
| Número mínimo de nódulos em um novo cluster | 3 |

## <a name="next-steps"></a>Próximas etapas

* Saiba como [prover os nódulos](create-nodes.md)
* Conheça as [Nuvens Privadas](cloudsimple-private-cloud.md)
