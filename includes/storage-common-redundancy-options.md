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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77157204"
---
As opções de redundância para uma conta de armazenamento incluem:

* Armazenamento localmente redundante (LRS): uma estratégia de redundância simples e de baixo custo. Os dados são copiados sincronizadamente três vezes dentro da região primária.
* Armazenamento redundante de zona (ZRS): Redundância para cenários que requerem alta disponibilidade. Os dados são copiados sincronizadamente em três zonas de disponibilidade do Azure na região primária.
* Armazenamento geo-redundante (GRS): Redundância transfronteiriça para proteger contra paralisações regionais. Os dados são copiados sincronizadamente três vezes na região primária e copiados assíncronamente para a região secundária. Para ler o acesso aos dados na região secundária, habilite o armazenamento geo-redundante de acesso de leitura (RA-GRS).
* GZRS (visualização): Redundância para cenários que requerem alta disponibilidade e durabilidade máxima. Os dados são copiados sincronizadamente em três zonas de disponibilidade do Azure na região primária e copiados de forma assíncrona para a região secundária. Para ler o acesso aos dados na região secundária, habilite o armazenamento geo-zona-redundante de acesso de leitura (RA-GZRS).

Para obter mais informações sobre opções de redundância no Azure Storage, consulte [a redundância de armazenamento do Azure](../articles/storage/common/storage-redundancy.md).
