---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7d0286b63703c165dda6cd12bb625fc64272aac1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376905"
---
Os Arquivos do Azure oferecem quatro camadas diferentes de armazenamento: premium, transação otimizada, quente e fria para permitir que você personalize seus compartilhamentos conforme os requisitos de desempenho e preço de seu cenário:

- **Premium**: Os compartilhamentos de arquivos Premium são apoiados por SSDs (unidades de estado sólido) e implantados no tipo de **conta de armazenamento FileStorage**. Os compartilhamentos de arquivos Premium fornecem alto desempenho consistente e baixa latência, em milissegundos de dígito único para a maioria das operações de E/S e para as cargas de E/S mais intensivas. Compartilhamentos de arquivo Premium são adequados para uma ampla variedade de cargas de trabalho, como bancos de dados, hospedagem de websites e ambientes de desenvolvimento. Os compartilhamentos de arquivo Premium podem ser usados com protocolos SMB e NFS (Network File System).
- **Transação otimizada**: Compartilhamentos de arquivos com transação otimizada permitem cargas de trabalho pesadas de transação que não precisam da latência oferecida por compartilhamentos de arquivos Premium. Os compartilhamentos de arquivos com transação otimizada são oferecidos no hardware de armazenamento padrão apoiado por HDs (discos rígidos) e implantados no tipo de **conta de armazenamento do GPv2 (uso geral versão 2)** . A transação otimizada tem sido, historicamente, chamada de "padrão". No entanto, isso se refere ao tipo de mídia de armazenamento, não à camada em si (as camadas quente e fria também são "padrão", pois estão no hardware de armazenamento padrão).
- **Frequente**: Os compartilhamentos de arquivos quentes oferecem armazenamento otimizado para cenários de compartilhamento de arquivos de uso geral, como compartilhamentos de equipe e Sincronização de Arquivos do Azure. Os compartilhamentos de arquivos quentes são oferecidos no hardware de armazenamento padrão apoiado por HDs e implantados no tipo de **conta de armazenamento do GPv2 (uso geral versão 2)** .
- **Esporádico**: Os compartilhamentos de arquivos frios oferecem armazenamento econômico otimizado para cenários de armazenamento de arquivos online. A Sincronização de Arquivos do Azure também pode ser uma boa opção para cargas de trabalho de rotatividade mais baixa. Os compartilhamentos de arquivos frios são oferecidos no hardware de armazenamento padrão apoiado por HDs e implantados no tipo de **conta de armazenamento do GPv2 (uso geral versão 2)** .

Os compartilhamentos de arquivos Premium estão disponíveis em um modelo de cobrança provisionado. Para obter mais informações sobre o modelo de cobrança provisionado para compartilhamentos de arquivos premium, confira [Noções básicas sobre provisionamento de compartilhamentos de arquivos premium](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares). Compartilhamentos de arquivo padrão, incluindo compartilhamentos de arquivos com transação otimizada, quentes e frios, estão disponíveis em um modelo de cobrança pago conforme o uso.

Os compartilhamentos de arquivo quentes e frios estão disponíveis em todas as regiões públicas do Azure e do Azure Government. Os compartilhamentos de arquivo com transação otimizada estão disponíveis em todas as regiões do Azure, incluindo Azure China e Azure Alemanha.

> [!Important]  
> Você pode mover compartilhamentos de arquivo entre camadas dentro de tipos de conta de armazenamento GPv2 (transação otimizada, quente e fria). Movimentações de compartilhamento entre camadas incorrem transações: mover de uma camada mais quente para uma camada mais fria incorrerá na cobrança da transação de gravação da camada mais fria para cada arquivo no compartilhamento, enquanto uma movimentação de uma camada mais fria para uma camada mais quente incorrerá na cobrança da transação de leitura da camada fria para cada arquivo no compartilhamento.