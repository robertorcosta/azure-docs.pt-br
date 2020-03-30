---
title: Co-localizar conjuntos de escala de máquinas virtuais do Azure
description: Saiba como a co-localização dos recursos de conjunto de escalade máquinavirtual do Azure pode melhorar o desempenho.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/14/2019
ms.author: cynthn
ms.openlocfilehash: 9d6e4e15c552cc4467dd17221a1ba901fd8e4f58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250823"
---
# <a name="co-location"></a>Colocalização

Um dos maiores contribuintes para a latência entre VMs é simplesmente a distância.

## <a name="preview-proximity-placement-groups"></a>Visualização: Grupos de colocação de proximidade 

[!INCLUDE [virtual-machines-common-ppg-overview](../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Próximas etapas

Crie um [grupo de colocação de proximidade](proximity-placement-groups.md) para o seu conjunto de escalas.

