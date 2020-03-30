---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 87457bb103f49be4ca3e7bf9f463c5bf63f3a119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597755"
---
Para proteger os dados em seus compartilhamentos de arquivos do Azure contra perda de dados ou corrupção, todos os compartilhamentos de arquivos do Azure armazenam várias cópias de cada arquivo à medida que são gravados. Dependendo dos requisitos de sua carga de trabalho, você pode selecionar graus adicionais de redundância. Atualmente, o Azure Files suporta as seguintes opções de redundância de dados:

- **Localmente redundante**: O armazenamento localmente redundante, muitas vezes referido como LRS, significa que cada arquivo é armazenado três vezes dentro de um cluster de armazenamento Azure. Isso protege contra a perda de dados devido a falhas de hardware, como uma unidade de disco ruim.
- **Zona redundante**: O armazenamento redundante da zona, muitas vezes referido como ZRS, significa que cada arquivo é armazenado três vezes em três clusters de armazenamento Azure distintos. Os três clusters de armazenamento Azure distintos armazenam o arquivo três vezes, assim como com o armazenamento localmente redundante, e são fisicamente isolados em diferentes zonas de *disponibilidade*do Azure . As zonas de disponibilidade são locais físicos exclusivos dentro de uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Uma gravação para armazenamento não é aceita até que seja escrita nos clusters de armazenamento nas três zonas de disponibilidade. 
- **Geo-redundante**: O armazenamento geo-redundante, muitas vezes referido como GRS, é como o armazenamento localmente redundante, na medida em que um arquivo é armazenado três vezes dentro de um cluster de armazenamento Azure na região principal. Todas as gravações são então replicadas assíncronamente para uma região secundária definida pela Microsoft. O armazenamento geo-redundante fornece 6 cópias de seus dados espalhados entre duas regiões do Azure. No caso de um grande desastre, como a perda permanente de uma região do Azure devido a um desastre natural ou outro evento semelhante, a Microsoft realizará um failover para que o secundário em vigor se torne o principal, servindo todas as operações. Uma vez que a replicação entre as regiões primária e secundária são assíncronas, em caso de um grande desastre, os dados ainda não replicados para a região secundária serão perdidos. Você também pode executar um failover manual de uma conta de armazenamento geo-redundante.
- **Geo-zone redundante**: O armazenamento redundante da zona geográfica, muitas vezes referido como GZRS, é como o armazenamento redundante da zona, na medida em que um arquivo é armazenado nove vezes em 3 clusters de armazenamento distintos na região primária. Todas as gravações são então replicadas assíncronamente para uma região secundária definida pela Microsoft. O processo de failover para armazenamento redundante em geo-zona funciona da mesma forma que para o armazenamento geo-redundante.

Os compartilhamentos de arquivos Padrão Azure suportam todos os quatro tipos de redundância, enquanto os compartilhamentos de arquivos Premium Azure só suportam armazenamento localmente redundante e redundante de zona.

As contas de armazenamento da versão 2 (GPv2) fornecem duas opções adicionais de redundância que não são suportadas pelos Arquivos Azure: leia o armazenamento geo-redundante acessível, muitas vezes referido como RA-GRS, e leia o armazenamento acessível de geozona-redundante, muitas vezes referido como RA-GZRS. Você pode provisionar os compartilhamentos de arquivos do Azure em contas de armazenamento com essas opções definidas, no entanto, o Azure Files não suporta leitura da região secundária. Os compartilhamentos de arquivos do Azure implantados em contas de armazenamento redundantes de geozona acessíveis à leitura serão cobrados como armazenamento geo-redundante ou geo-zona-redundante, respectivamente.