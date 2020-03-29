---
title: Acesso multiprotocolo no Azure Data Lake Storage | Microsoft Docs
description: Use APIs blob e aplicativos que usam APIs blob com Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e3997fc215637175165402a926bffc6ac8d02771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914851"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Acesso de vários protocolos no Azure Data Lake Storage

As APIs blob agora trabalham com contas que têm um namespace hierárquico. Isso desbloqueia o ecossistema de ferramentas, aplicativos e serviços, bem como vários recursos de armazenamento Blob para contas que têm um namespace hierárquico.

Até recentemente, você poderia ter tido que manter soluções de armazenamento separadas para armazenamento de objetos e armazenamento de análise. Isso porque o Azure Data Lake Storage Gen2 tinha suporte limitado ao ecossistema. Também tinha acesso limitado a recursos de serviço Blob, como registro de diagnóstico. Uma solução de armazenamento fragmentado é difícil de manter porque você tem que mover dados entre contas para realizar vários cenários. Você não precisa mais fazer isso.

Com acesso multiprotocolo no Data Lake Storage, você pode trabalhar com seus dados usando o ecossistema de ferramentas, aplicativos e serviços. Isso também inclui ferramentas e aplicativos de terceiros. Você pode apontá-los para contas que têm um namespace hierárquico sem ter que modificá-los. Esses aplicativos funcionam *como são* mesmo se eles chamam de APIs blob, porque as APIs blob agora podem operar em dados em contas que têm um namespace hierárquico.

Os recursos de armazenamento blob, como [registro de diagnóstico,](../common/storage-analytics-logging.md) [níveis de acesso](storage-blob-storage-tiers.md)e políticas de gerenciamento do ciclo de vida do armazenamento [Blob,](storage-lifecycle-management-concepts.md) agora funcionam com contas que têm um namespace hierárquico. Portanto, você pode habilitar espaços de nomes hierárquicos em suas contas de armazenamento blob sem perder o acesso a esses recursos importantes. 

> [!NOTE]
> O acesso multiprotocolo no Data Lake Storage está geralmente disponível e está disponível em todas as regiões. Alguns serviços do Azure ou recursos de armazenamento Blob habilitados pelo acesso multiprotocolo permanecem em visualização.  Esses artigos resumem o suporte atual aos recursos de armazenamento Blob e integrações de serviços Do Azure. 
>
> [Recursos de armazenamento blob disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
>
>[Serviços do Azure que suportam o Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Como funciona o acesso multiprotocolo no armazenamento de data lake

APIs blob e APIs de armazenamento de data lake Gen2 podem operar com os mesmos dados em contas de armazenamento que têm um namespace hierárquico. Data Lake Storage Gen2 rotas APIs blob através do namespace hierárquico para que você possa obter os benefícios das operações de diretório de primeira classe e listas de controle de acesso compatíveis com POSIX (ACLs). 

![Acesso multiprotocolo no conceito de Armazenamento de Data Lake](./media/data-lake-storage-interop/interop-concept.png) 

As ferramentas e aplicativos existentes que usam a API Blob ganham esses benefícios automaticamente. Os desenvolvedores não terão que modificá-los. Data Lake Storage Gen2 aplica consistentemente ACLs de diretório e nível de arquivo, independentemente do protocolo que as ferramentas e aplicativos usam para acessar os dados. 

## <a name="see-also"></a>Confira também

- [Recursos de armazenamento blob disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Serviços do Azure que suportam o Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plataformas de código aberto que suportam o Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Problemas conhecidos com o Azure Data Lake Storage Gen2 | Microsoft Docs](data-lake-storage-known-issues.md)




