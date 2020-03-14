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
ms.openlocfilehash: 1f094cd78e6708fec4bda1e5510379b11df14dcd
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299138"
---
Instantâneos incrementais são backups pontuais para discos gerenciados que, quando obtidos, consistem apenas em todas as alterações desde o último instantâneo. Quando você tenta baixar ou, de outra forma, usa um instantâneo incremental, o VHD completo é usado. Essa nova funcionalidade para instantâneos de disco gerenciado pode permitir que eles sejam mais econômicos, já que, a menos que você escolha, você não precisa armazenar todo o disco com cada instantâneo individual. Assim como instantâneos regulares, instantâneos incrementais podem ser usados para criar um disco gerenciado completo ou para criar um instantâneo normal.

Há algumas diferenças entre um instantâneo incremental e um instantâneo normal. Os instantâneos incrementais sempre usarão o armazenamento de HDDs padrão, independentemente do tipo de armazenamento do disco, enquanto os instantâneos regulares podem usar o SSDs Premium. Se você estiver usando instantâneos regulares no armazenamento Premium para escalar verticalmente as implantações de VM, recomendamos o uso de imagens personalizadas no armazenamento Standard na [Galeria de imagens compartilhadas](../articles/virtual-machines/linux/shared-image-galleries.md). Ele o ajudará a alcançar uma escala mais maciça com menor custo. Além disso, os instantâneos incrementais podem oferecer melhor confiabilidade com ZRS ( [armazenamento com redundância de zona](../articles/storage/common/storage-redundancy-zrs.md) ). Se ZRS estiver disponível na região selecionada, um instantâneo incremental usará o ZRS automaticamente. Se ZRS não estiver disponível na região, o instantâneo usará como padrão o [armazenamento com redundância local](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Você pode substituir esse comportamento e selecionar um manualmente, mas não é recomendável.

Os instantâneos incrementais também oferecem um recurso diferencial, disponível apenas para discos gerenciados. Eles permitem que você obtenha as alterações entre dois instantâneos incrementais dos mesmos discos gerenciados, até o nível de bloco. Você pode usar essa capacidade para reduzir o volume de dados ao copiar instantâneos entre regiões.
