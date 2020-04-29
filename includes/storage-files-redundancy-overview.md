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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77597755"
---
Para proteger os dados em seus compartilhamentos de arquivos do Azure contra perda ou corrupção de dados, todos os compartilhamentos de arquivos do Azure armazenam várias cópias de cada arquivo à medida que são gravadas. Dependendo dos requisitos de sua carga de trabalho, você pode selecionar graus adicionais de redundância. Atualmente, os arquivos do Azure dão suporte às seguintes opções de redundância de dados:

- **Localmente redundante**: o armazenamento com redundância local, geralmente conhecido como lRS, significa que cada arquivo é armazenado três vezes em um cluster de armazenamento do Azure. Isso protege contra perda de dados devido a falhas de hardware, como uma unidade de disco defeituosa.
- Com **redundância de zona**: o armazenamento com redundância de zona, geralmente referido como ZRS, significa que cada arquivo é armazenado três vezes em três clusters de armazenamento do Azure distintos. Os três diferentes clusters de armazenamento do Azure armazenam o arquivo três vezes, assim como com o armazenamento com redundância local, e são fisicamente isolados em diferentes *zonas de disponibilidade*do Azure. As zonas de disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Uma gravação no armazenamento não é aceita até que seja gravada nos clusters de armazenamento em todas as três zonas de disponibilidade. 
- **Com redundância geográfica**: o armazenamento com redundância geográfica, geralmente conhecido como grs, é como um armazenamento com redundância local, no qual um arquivo é armazenado três vezes em um cluster de armazenamento do Azure na região primária. Em seguida, todas as gravações são replicadas de forma assíncrona para uma região secundária definida pela Microsoft. O armazenamento com redundância geográfica fornece seis cópias de seus dados espalhados entre duas regiões do Azure. No caso de um grande desastre, como a perda permanente de uma região do Azure devido a um desastre natural ou a outro evento semelhante, a Microsoft executará um failover para que o secundário em vigor se torne o primário, atendendo a todas as operações. Como a replicação entre as regiões primária e secundária é assíncrona, no caso de um desastre principal, os dados ainda não replicados para a região secundária serão perdidos. Você também pode executar um failover manual de uma conta de armazenamento com redundância geográfica.
- **Com redundância de zona geográfica**: o armazenamento com redundância de zona geográfica, geralmente conhecido como GZRS, é como o armazenamento com redundância de zona, no qual um arquivo é armazenado nove vezes em três clusters de armazenamento distintos na região primária. Em seguida, todas as gravações são replicadas de forma assíncrona para uma região secundária definida pela Microsoft. O processo de failover para armazenamento com redundância de zona geográfica funciona da mesma forma que o armazenamento com redundância geográfica.

Os compartilhamentos de arquivos padrão do Azure dão suporte a todos os quatro tipos de redundância, enquanto os compartilhamentos de arquivos premium do Azure só oferecem suporte a armazenamento com redundância local

As contas de armazenamento de uso geral versão 2 (GPv2) fornecem duas opções adicionais de redundância que não têm suporte nos arquivos do Azure: Leia armazenamento com redundância geográfica acessível, geralmente conhecido como RA-GRS e leia armazenamento com redundância de zona geográfica acessível, geralmente conhecido como RA-GZRS. Você pode provisionar compartilhamentos de arquivos do Azure em contas de armazenamento com essas opções definidas, no entanto, os arquivos do Azure não dão suporte à leitura da região secundária. Os compartilhamentos de arquivos do Azure implantados em contas de armazenamento com redundância de zona geográfica ou geográfica acessível serão cobrados como armazenamento com redundância geográfica ou com redundância de zona geográfica, respectivamente.