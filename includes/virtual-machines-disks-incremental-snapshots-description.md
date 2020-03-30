---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4d2c0a02a48c0e04b501f136f66c28b3f532e2a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79485972"
---
Instantâneos incrementais são backups pontuais para discos gerenciados que, quando tomados, consistem apenas em todas as alterações desde o último snapshot. Quando você tenta baixar ou usar um instantâneo incremental, o VHD completo é usado. Esse novo recurso para snapshots de disco gerenciados potencialmente permite que eles sejam mais econômicos, uma vez que, a menos que você escolha, você não precisa armazenar todo o disco com cada instantâneo individual. Assim como instantâneos regulares, instantâneos incrementais podem ser usados para criar um disco gerenciado completo ou para fazer um instantâneo regular.

Há algumas diferenças entre um instantâneo incremental e um instantâneo regular. Os instantâneos incrementais sempre usarão o armazenamento hddpadrão, independentemente do tipo de armazenamento do disco, enquanto os snapshots regulares podem usar SSDs premium. Se você estiver usando instantâneos regulares no Armazenamento Premium para dimensionar as implantações de VM, recomendamos que você use imagens personalizadas no armazenamento padrão na [Galeria de Imagens Compartilhadas](../articles/virtual-machines/linux/shared-image-galleries.md). Ele vai ajudá-lo a alcançar uma escala mais massiva com menor custo. Além disso, instantâneos incrementais potencialmente oferecem melhor confiabilidade com [o ZRS (Zone-redundante).](../articles/storage/common/storage-redundancy-zrs.md) Se o ZRS estiver disponível na região selecionada, um instantâneo incremental usará o ZRS automaticamente. Se o ZRS não estiver disponível na região, o snapshot será padrão para [o armazenamento localmente redundante](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Você pode substituir esse comportamento e selecionar um manualmente, mas, não recomendamos isso.

Instantâneos incrementais também oferecem um recurso diferencial, disponível apenas para discos gerenciados. Eles permitem que você obtenha as alterações entre dois instantâneos incrementais dos mesmos discos gerenciados, até o nível de bloco. Você pode usar esse recurso para reduzir sua pegada de dados ao copiar instantâneos em regiões.  Por exemplo, você pode baixar o primeiro instantâneo incremental como uma bolha base em outra região. Para os instantâneos incrementais subseqüentes, você pode copiar apenas as alterações desde o último instantâneo para a bolha base. Depois de copiar as alterações, você pode tirar instantâneos na bolha base que representam o backup do seu ponto no tempo do disco em outra região. Você pode restaurar seu disco a partir da bolha base ou de um instantâneo na bolha base em outra região.

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="Diagrama representando instantâneos incrementais copiados entre regiões. Os snapshots fazem várias chamadas de API até eventualmente formar blobs de página por cada instantâneo.":::