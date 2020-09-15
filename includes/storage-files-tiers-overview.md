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
ms.openlocfilehash: a2493ce764f2aed2aca1d555f98b738185159f7a
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064841"
---
Os Arquivos do Azure oferecem quatro camadas diferentes de armazenamento: premium, transação otimizada, quente e fria para permitir que você personalize seus compartilhamentos conforme os requisitos de desempenho e preço de seu cenário:

- **Premium**: Os compartilhamentos de arquivos Premium são apoiados por SSDs (unidades de estado sólido) e implantados no tipo de **conta de armazenamento FileStorage**. Os compartilhamentos de arquivos Premium fornecem alto desempenho consistente e baixa latência, em milissegundos de dígito único para a maioria das operações de E/S e para as cargas de E/S mais intensivas. Compartilhamentos de arquivo Premium são adequados para uma ampla variedade de cargas de trabalho, como bancos de dados, hospedagem de websites e ambientes de desenvolvimento. 
- **Transação otimizada**: Compartilhamentos de arquivos com transação otimizada permitem cargas de trabalho pesadas de transação que não precisam da latência oferecida por compartilhamentos de arquivos Premium. Os compartilhamentos de arquivos com transação otimizada são oferecidos no hardware de armazenamento padrão apoiado por HDs (discos rígidos) e implantados no tipo de **conta de armazenamento do GPv2 (uso geral versão 2)** . A transação otimizada tem sido, historicamente, chamada de "padrão". No entanto, isso se refere ao tipo de mídia de armazenamento, não à camada em si (as camadas quente e fria também são "padrão", pois estão no hardware de armazenamento padrão).
- **Frequente**: Os compartilhamentos de arquivos quentes oferecem armazenamento otimizado para cenários de compartilhamento de arquivos de uso geral, como compartilhamentos de equipe e Sincronização de Arquivos do Azure. Os compartilhamentos de arquivos quentes são oferecidos no hardware de armazenamento padrão apoiado por HDs e implantados no tipo de **conta de armazenamento do GPv2 (uso geral versão 2)** .
- **Esporádico**: Os compartilhamentos de arquivos frios oferecem armazenamento econômico otimizado para cenários de armazenamento de arquivos online. A Sincronização de Arquivos do Azure também pode ser uma boa opção para cargas de trabalho de rotatividade mais baixa. Os compartilhamentos de arquivos frios são oferecidos no hardware de armazenamento padrão apoiado por HDs e implantados no tipo de **conta de armazenamento do GPv2 (uso geral versão 2)** .

Os compartilhamentos de arquivos Premium estão disponíveis em um modelo de cobrança provisionado. Para obter mais informações sobre o modelo de cobrança provisionado para compartilhamentos de arquivos premium, confira [Noções básicas sobre provisionamento de compartilhamentos de arquivos premium](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares). Compartilhamentos de arquivo padrão, incluindo compartilhamentos de arquivos com transação otimizada, quentes e frios, estão disponíveis em um modelo de cobrança pago conforme o uso.

Compartilhamentos de arquivo quentes e frios estão disponíveis em todas as regiões públicas do Azure. Os compartilhamentos de arquivo com transação otimizada estão disponíveis em todas as regiões do Azure, incluindo regiões de nuvem nacional.

Para implantar um compartilhamento de arquivo quente ou frio, confira [Criar um compartilhamento de arquivo quente ou frio](../articles/storage/files/storage-how-to-create-file-share.md#create-a-hot-or-cool-file-share). 