---
title: incluir arquivo
description: arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 65729934ea7c4037d6857aec10b14cdddd616368
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97805628"
---
As opções de redundância para uma conta de armazenamento incluem:

* LRS (armazenamento com redundância local): Uma estratégia de redundância simples e de baixo custo. Os dados serão copiados três vezes em uma localização física única de maneira síncrona na região primária.
* Armazenamento com redundância de zona (ZRS): Redundância para cenários que exigem alta disponibilidade. Os dados são copiados de maneira síncrona em três zonas de disponibilidade do Azure na região primária.
* GRS (armazenamento com redundância geográfica): Redundância entre regiões para fornecer proteção contra interrupções regionais. Os dados são copiados de maneira síncrona três vezes na região primária e copiados de maneira assíncrona para a região secundária. Para obter acesso de leitura aos dados na região secundária, habilite RA-GRS (armazenamento com redundância geográfica com acesso de leitura).
* GZRS (armazenamento com redundância de zona geográfica) (versão prévia): Redundância para cenários que exigem alta disponibilidade e durabilidade máxima. Os dados são copiados de maneira síncrona em três zonas de disponibilidade do Azure na região primária e copiados de maneira assíncrona para a região secundária. Para ter acesso de leitura aos dados na região secundária, habilite o RA-GZRS (armazenamento com redundância de zona geográfica com acesso de leitura).

Para obter mais informações sobre opções de redundância no Armazenamento do Azure, confira [Redundância do Armazenamento do Azure](../articles/storage/common/storage-redundancy.md).
