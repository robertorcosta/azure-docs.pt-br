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
ms.openlocfilehash: cace61739e13514bb4aba1169c51ea670a167c9d
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88944370"
---
Os Arquivos do Azure oferecem quatro camadas diferentes de armazenamento: premium, transação otimizada, quente e fria para permitir que você personalize seus compartilhamentos conforme os requisitos de desempenho e preço de seu cenário:

- **Premium**: Os compartilhamentos de arquivos Premium são apoiados por SSDs (unidades de estado sólido) e implantados no tipo de **conta de armazenamento FileStorage**. Os compartilhamentos de arquivos Premium fornecem alto desempenho consistente e baixa latência, em milissegundos de dígito único para a maioria das operações de E/S e para as cargas de E/S mais intensivas. Isso os torna adequados para uma ampla variedade de cargas de trabalho, como bancos de dados, hospedagem de websites e ambientes de desenvolvimento. 
- **Transação otimizada**: Compartilhamentos de arquivos com transação otimizada permitem cargas de trabalho pesadas de transação que não precisam da latência oferecida por compartilhamentos de arquivos Premium. Os compartilhamentos de arquivos com transação otimizada são oferecidos no hardware de armazenamento padrão apoiado por HDs (discos rígidos) e implantados no tipo de **conta de armazenamento do GPv2 (uso geral versão 2)** . Essa camada de armazenamento tem sido, historicamente, chamada de "padrão". No entanto, isso se refere ao tipo de mídia de armazenamento, não à camada em si (as camadas quente e fria são também "padrão", pois elas estão no hardware de armazenamento padrão).
- **Frequente**: Os compartilhamentos de arquivos quentes oferecem armazenamento otimizado para cenários de compartilhamento de arquivos de uso geral, como compartilhamentos de equipe e Sincronização de Arquivos do Azure. Os compartilhamentos de arquivos quentes são oferecidos no hardware de armazenamento padrão apoiado por HDs e implantados no tipo de **conta de armazenamento do GPv2 (uso geral versão 2)** .
- **Esporádico**: Os compartilhamentos de arquivos frios oferecem armazenamento econômico otimizado para cenários de armazenamento de arquivos online. A Sincronização de Arquivos do Azure também pode ser uma boa opção para cargas de trabalho de rotatividade mais baixa. Os compartilhamentos de arquivos frios são oferecidos no hardware de armazenamento padrão apoiado por HDs e implantados no tipo de **conta de armazenamento do GPv2 (uso geral versão 2)** .

Os compartilhamentos de arquivos Premium estão disponíveis em um modelo de cobrança provisionado. Para obter mais informações sobre o modelo de cobrança provisionado para compartilhamentos de arquivos premium, confira [Noções básicas sobre provisionamento de compartilhamentos de arquivos premium](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares). Compartilhamentos de arquivos padrão, incluindo compartilhamentos de arquivos com transação otimizada, quentes e frios, estão disponíveis em um modelo pago conforme o uso.

Os compartilhamentos de arquivos quentes e frios estão disponíveis no seguinte subconjunto de regiões públicas (os compartilhamentos de arquivos com transação otimizada estão disponíveis em todas as regiões do Azure):

- Austrália Central
- Austrália Central 2
- Leste da Austrália
- Sudeste da Austrália
- Leste do Canadá
- Canadá Central
- França Central
- Sul da França
- Norte da Alemanha (público)
- Centro-Oeste da Alemanha (público)
- Centro da Índia
- Sul da Índia
- Oeste da Índia
- Leste do Japão
- Oeste do Japão
- Coreia Central
- Sul da Coreia
- Leste da Noruega
- Oeste da Noruega
- Norte da África do Sul
- Oeste da África do Sul
- Norte da Suíça
- Oeste da Suíça
- EAU Central
- Norte dos EAU
- Sul do Reino Unido
- Oeste do Reino Unido

Para implantar um compartilhamento de arquivo quente ou frio, confira [Criar um compartilhamento de arquivo quente ou frio](../articles/storage/files/storage-how-to-create-file-share.md#create-a-hot-or-cool-file-share). 