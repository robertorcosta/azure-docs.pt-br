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
ms.openlocfilehash: 83c391c0d92f8d4a0ed4b44bc3a90273db51b412
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81539410"
---
Instantâneos incrementais são backups pontuais para discos gerenciados que, quando tomados, consistem apenas em todas as alterações desde o último snapshot. Quando você tenta baixar ou usar um instantâneo incremental, o VHD completo é usado. Esse novo recurso para snapshots de disco gerenciados potencialmente permite que eles sejam mais econômicos, uma vez que, a menos que você escolha, você não precisa armazenar todo o disco com cada instantâneo individual. Assim como instantâneos regulares, instantâneos incrementais podem ser usados para criar um disco gerenciado completo ou para fazer um instantâneo regular.

Há algumas diferenças entre um instantâneo incremental e um instantâneo regular. Os instantâneos incrementais sempre usarão o armazenamento hddpadrão, independentemente do tipo de armazenamento do disco, enquanto os snapshots regulares podem usar SSDs premium. Se você estiver usando instantâneos regulares no Armazenamento Premium para dimensionar as implantações de VM, recomendamos que você use imagens personalizadas no armazenamento padrão na [Galeria de Imagens Compartilhadas](../articles/virtual-machines/linux/shared-image-galleries.md). Ele vai ajudá-lo a alcançar uma escala mais massiva com menor custo. Além disso, instantâneos incrementais potencialmente oferecem melhor confiabilidade com [o ZRS (Zone-redundante).](../articles/storage/common/storage-redundancy-zrs.md) Se o ZRS estiver disponível na região selecionada, um instantâneo incremental usará o ZRS automaticamente. Se o ZRS não estiver disponível na região, o snapshot será padrão para [o armazenamento localmente redundante](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Você pode substituir esse comportamento e selecionar um manualmente, mas, não recomendamos isso.

Instantâneos incrementais também oferecem um recurso diferencial, disponível apenas para discos gerenciados. Eles permitem que você obtenha as alterações entre dois instantâneos incrementais dos mesmos discos gerenciados, até o nível de bloco. Você pode usar esse recurso para reduzir sua pegada de dados ao copiar instantâneos em regiões.  Por exemplo, você pode baixar o primeiro instantâneo incremental como uma bolha base em outra região. Para os instantâneos incrementais subseqüentes, você pode copiar apenas as alterações desde o último instantâneo para a bolha base. Depois de copiar as alterações, você pode tirar instantâneos na bolha base que representam o backup do seu ponto no tempo do disco em outra região. Você pode restaurar seu disco a partir da bolha base ou de um instantâneo na bolha base em outra região.

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="Diagrama representando instantâneos incrementais copiados entre regiões. Os snapshots fazem várias chamadas de API até eventualmente formar blobs de página por cada instantâneo.":::

Você poderá ver o tamanho utilizado dos instantâneos examinando o [relatório de uso do Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). Por exemplo, se o tamanho dos dados de um snapshot usado for 10 GiB, o relatório de uso **diário** mostrará 10 GiB/(31 dias) = 0,3226 como a quantidade consumida.
