---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 73c2b742ede21a4e86d717d994f8ebc4f16389c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77157204"
---
As opções de redundância para uma conta de armazenamento incluem:

* LRS (armazenamento com redundância local): uma estratégia de redundância simples e de baixo custo. Os dados são copiados de forma síncrona três vezes dentro da região primária.
* ZRS (armazenamento com redundância de zona): redundância para cenários que exigem alta disponibilidade. Os dados são copiados de forma síncrona em três zonas de disponibilidade do Azure na região primária.
* Armazenamento com redundância geográfica (GRS): redundância entre regiões para proteger contra interrupções regionais. Os dados são copiados de forma síncrona três vezes na região primária e, em seguida, copiados assincronamente para a região secundária. Para acesso de leitura aos dados na região secundária, habilite o armazenamento com redundância geográfica com acesso de leitura (RA-GRS).
* Armazenamento com redundância de zona geográfica (GZRS) (visualização): redundância para cenários que exigem alta disponibilidade e durabilidade máxima. Os dados são copiados de forma síncrona em três zonas de disponibilidade do Azure na região primária e, em seguida, copiados assincronamente para a região secundária. Para acesso de leitura aos dados na região secundária, habilite o armazenamento com redundância de zona geográfica com acesso de leitura (RA-GZRS).

Para obter mais informações sobre as opções de redundância no armazenamento do Azure, consulte [redundância de armazenamento do Azure](../articles/storage/common/storage-redundancy.md).
