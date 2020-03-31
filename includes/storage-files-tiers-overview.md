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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597758"
---
O Azure Files oferece dois níveis diferentes de armazenamento, premium e padrão, para permitir que você adapte suas ações aos requisitos de desempenho e preço do seu cenário:

- **Compartilhamentos de arquivos premium**: As partes de arquivos premium são apoiadas por drives de estado sólido (SSDs) e são implantadas no tipo **de conta de armazenamento de arquivos.** Os compartilhamentos de arquivos premium fornecem alto desempenho consistente e baixa latência, dentro de milissegundos de um dígito para a maioria das operações de IO, para cargas de trabalho intensivas em IO. Isso os torna adequados para uma grande variedade de cargas de trabalho, como bancos de dados, hospedagem de sites e ambientes de desenvolvimento. Os compartilhamentos de arquivos Premium estão disponíveis em um modelo de cobrança provisionado. Para obter mais informações sobre o modelo de faturamento provisionado para ações de arquivos premium, consulte [Compreensão de provisionamento para ações de arquivos premium](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares).
- **Compartilhamentos de arquivos padrão**: Os compartilhamentos de arquivos padrão são apoiados por unidades de disco rígido (HDDs) e são implantados no tipo de **conta de armazenamento da versão 2 (GPv2).** Os compartilhamentos de arquivos padrão fornecem desempenho confiável para cargas de trabalho de IO menos sensíveis à variabilidade de desempenho, como compartilhamentos de arquivos de uso geral e ambientes de dev/teste. Os compartilhamentos de arquivos padrão estão disponíveis apenas em um modelo de cobrança pago conforme o uso.