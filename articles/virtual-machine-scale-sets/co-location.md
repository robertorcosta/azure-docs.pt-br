---
title: Colocalizar conjuntos de dimensionamento de máquinas virtuais do Azure
description: Saiba como a colocalização de recursos do conjunto de dimensionamento de máquinas virtuais do Azure pode melhorar o desempenho.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/14/2019
ms.author: cynthn
ms.openlocfilehash: 9d6e4e15c552cc4467dd17221a1ba901fd8e4f58
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79250823"
---
# <a name="co-location"></a>Colocalização

Um dos maiores colaboradores da latência entre as VMs é simplesmente a distância.

## <a name="preview-proximity-placement-groups"></a>Visualização: grupos de posicionamento de proximidade 

[!INCLUDE [virtual-machines-common-ppg-overview](../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Próximas etapas

Crie um [grupo de posicionamento de proximidade](proximity-placement-groups.md) para seu conjunto de dimensionamento.

