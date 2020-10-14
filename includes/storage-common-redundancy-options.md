---
title: arquivo de inclusão
description: arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 73c2b742ede21a4e86d717d994f8ebc4f16389c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77157204"
---
As opções de redundância para uma conta de armazenamento incluem:

* LRS (armazenamento com redundância local): Uma estratégia de redundância simples e de baixo custo. Os dados são copiados de maneira síncrona três vezes dentro da região primária.
* Armazenamento com redundância de zona (ZRS): Redundância para cenários que exigem alta disponibilidade. Os dados são copiados de maneira síncrona em três zonas de disponibilidade do Azure na região primária.
* GRS (armazenamento com redundância geográfica): Redundância entre regiões para fornecer proteção contra interrupções regionais. Os dados são copiados de maneira síncrona três vezes na região primária e copiados de maneira assíncrona para a região secundária. Para obter acesso de leitura aos dados na região secundária, habilite RA-GRS (armazenamento com redundância geográfica com acesso de leitura).
* GZRS (armazenamento com redundância de zona geográfica) (versão prévia): Redundância para cenários que exigem alta disponibilidade e durabilidade máxima. Os dados são copiados de maneira síncrona em três zonas de disponibilidade do Azure na região primária e copiados de maneira assíncrona para a região secundária. Para ter acesso de leitura aos dados na região secundária, habilite o RA-GZRS (armazenamento com redundância de zona geográfica com acesso de leitura).

Para obter mais informações sobre opções de redundância no Armazenamento do Azure, confira [Redundância do Armazenamento do Azure](../articles/storage/common/storage-redundancy.md).
