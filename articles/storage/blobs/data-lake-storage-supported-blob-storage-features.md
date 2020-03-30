---
title: Recursos de armazenamento Blob disponíveis no Azure Data Lake Storage Gen2 | Microsoft Docs
description: Saiba quais recursos de armazenamento Blob você pode usar com o Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 215244204aa58cc2fdedc639d48e01b514759694
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196001"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Recursos de armazenamento Blob disponíveis no Azure Data Lake Storage Gen2

Os recursos de armazenamento blob, como [registro de diagnóstico,](../common/storage-analytics-logging.md) [níveis de acesso](storage-blob-storage-tiers.md)e políticas de gerenciamento do ciclo de vida do [Blob Storage,](storage-lifecycle-management-concepts.md) agora funcionam com contas que têm um namespace hierárquico. Portanto, você pode habilitar namespaces hierárquicos em suas contas de armazenamento Blob sem perder o acesso a esses recursos.

Esta tabela lista os recursos de armazenamento Blob que você pode usar com o Azure Data Lake Storage Gen2. Os itens que aparecem nessas tabelas mudarão ao longo do tempo à medida que o suporte continua a se expandir.

## <a name="supported-blob-storage-features"></a>Recursos de armazenamento Blob suportados

> [!NOTE]
> O nível de suporte refere-se apenas à forma como o recurso é suportado com data lake storage Gen2.

|Recurso de armazenamento blob |Nível de suporte |Artigos relacionados |
|---------------|-------------------|---|
|Camada de acesso quente|Disponível para o público geral|[Armazenamento de Blobs do Azure: camadas de acesso frequentes, esporádicas e de arquivo](storage-blob-storage-tiers.md)|
|Camada de acesso frio|Disponível para o público geral|[Armazenamento de Blobs do Azure: camadas de acesso frequentes, esporádicas e de arquivo](storage-blob-storage-tiers.md)|
|Eventos|Disponível para o público geral|[Reagir aos eventos de armazenamento de Blobs](storage-blob-event-overview.md)|
|Métricas (Clássico)|Disponível para o público geral|[Métricas de análise de armazenamento do Azure (Classic)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Métricas no Azure Monitor|Disponível para o público geral|[Métricas do Armazenamento do Microsoft Azure no Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Comandos PowerShell de armazenamento Blob|Disponível para o público geral|[Quickstart: Carregar, baixar e listar bolhas com o PowerShell](storage-quickstart-blobs-powershell.md)|
|Comandos CLI do Blob Storage Azure|Disponível para o público geral|[Quickstart: Crie, baixe e liste blobs com a Cli do Azure](storage-quickstart-blobs-cli.md)|
|APIs de armazenamento de Blob|Disponível para o público geral|[Quickstart: Biblioteca cliente de armazenamento Azure Blob v12 para .NET](storage-quickstart-blobs-dotnet.md)<br>[Quickstart: Gerenciar bolhas com Java v12 SDK](storage-quickstart-blobs-java.md)<br>[Quickstart: Gerenciar bolhas com Python v12 SDK](storage-quickstart-blobs-python.md)<br>[Quickstart: Gerenciar blobs com JavaScript v12 SDK em Node.js](storage-quickstart-blobs-nodejs.md)|
|Nível de acesso ao arquivamento|Visualização|[Armazenamento de Blobs do Azure: camadas de acesso frequentes, esporádicas e de arquivo](storage-blob-storage-tiers.md)|
|Políticas de gerenciamento do ciclo de vida|Visualização|[Gerenciar o ciclo de vida de armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md)|
|Logs de diagnóstico|Visualização|[Registro em log da Análise de Armazenamento do Azure](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Feed de alteração|Ainda não suportado|[Alterar o suporte ao feed no armazenamento Azure Blob](storage-blob-change-feed.md)|
|Failover da conta|Ainda não suportado|[Recuperação de desastres e failover de conta](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|ACL do recipiente Blob|Ainda não suportado|[Definir ACL do contêiner](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Domínios personalizados|Ainda não suportado|[Mapeie um domínio personalizado para um ponto final de armazenamento DoZure Blob](storage-custom-domain-name.md)|
|Armazenamento imutável|Ainda não suportado|[Armazene dados de bolha críticos para os negócios com armazenamento imutável](storage-blob-immutable-storage.md)|
|Instantâneos|Ainda não suportado|[Criar e gerenciar um instantâneo blob no .NET](storage-blob-snapshots.md)|
|Exclusão reversível|Ainda não suportado|[Exclusão reversível para blobs do Armazenamento do Azure ](storage-blob-soft-delete.md)|
|Sites estáticos|Ainda não suportado|[Hospedagem de site estático no Armazenamento do Microsoft Azure](storage-blob-static-website.md)|
|Login no Monitor Azure|Ainda não suportado|Ainda não está disponível|
|Bolhas de bloco premium|Ainda não suportado|[Criar uma conta BlockBlobStorage](storage-blob-create-account-block-blob.md)|

## <a name="see-also"></a>Confira também

- [Problemas conhecidos com o Azure Data Lake Storage Gen2 | Microsoft Docs](data-lake-storage-known-issues.md)
- [Serviços do Azure que suportam o Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plataformas de código aberto que suportam o Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Acesso de vários protocolos no Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)