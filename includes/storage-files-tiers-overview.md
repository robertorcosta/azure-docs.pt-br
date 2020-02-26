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
ms.openlocfilehash: f4e5c59a5ce7c0d743a574309bb1b9276ce80bea
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597758"
---
Os arquivos do Azure oferecem duas camadas diferentes de armazenamento, Premium e Standard, para permitir que você personalize seus compartilhamentos para os requisitos de desempenho e preço do seu cenário:

- **Compartilhamentos de arquivos Premium**: os compartilhamentos de arquivos Premium são apoiados por SSDs (unidades de estado sólido) e implantados no tipo de **conta de armazenamento de armazenamento** de arquivo. Os compartilhamentos de arquivos Premium fornecem alto desempenho e baixa latência consistentes em milissegundos de dígito único para a maioria das operações de e/s, para cargas de trabalho com uso intensivo de e/s. Isso os torna adequados para uma ampla variedade de cargas de trabalho, como bancos de dados, Hospedagem de site e ambientes de desenvolvimento. Os compartilhamentos de arquivos Premium estão disponíveis em um modelo de cobrança provisionado. Para obter mais informações sobre o modelo de cobrança provisionado para compartilhamentos de arquivos premium, consulte [noções básicas sobre provisionamento de compartilhamentos de arquivos Premium](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares).
- **Compartilhamentos de arquivos padrão**: os compartilhamentos de arquivos padrão são apoiados por unidades de disco rígido (HDDs) e são implantados no tipo de **conta de armazenamento GPv2 (versão de uso geral 2)** . Os compartilhamentos de arquivos padrão fornecem um desempenho confiável para cargas de trabalho de e/s que são menos sensíveis à variabilidade de desempenho, como compartilhamentos de arquivos de uso geral e ambientes de desenvolvimento/teste. Os compartilhamentos de arquivos padrão estão disponíveis apenas em um modelo de cobrança pago conforme o uso.