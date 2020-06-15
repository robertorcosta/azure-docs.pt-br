---
title: Recursos de armazenamento de blobs no Azure Data Lake Storage Gen2 | Microsoft Docs
description: Saiba quais recursos de armazenamento de blobs você pode usar com Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 05/10/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 5be7c9800076cfaf361a3e752162e14d10f22655
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650750"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Recursos de armazenamento de blobs no Azure Data Lake Storage Gen2

Recursos do armazenamento de blobs, como [log de diagnóstico](../common/storage-analytics-logging.md),[camadas de acesso](storage-blob-storage-tiers.md) e [políticas de gerenciamento do ciclo de vida do armazenamento de blobs](storage-lifecycle-management-concepts.md) agora funcionam com contas que têm um namespace hierárquico. Portanto, você pode habilitar namespaces hierárquicos em suas contas de Armazenamento de Blobs sem perder o acesso a esses recursos.

Esta tabela lista os recursos de armazenamento de blobs que você pode usar com Azure Data Lake Storage Gen2. Os itens destas tabelas serão alterados ao longo do tempo, pois o suporte continuará a ser expandido. Para saber mais sobre problemas específicos associados ao status de visualização de um recurso, consulte o artigo [Problemas conhecidos](data-lake-storage-known-issues.md).

## <a name="supported-blob-storage-features"></a>Recursos do Armazenamento de Blobs compatíveis

> [!NOTE]
> O nível de suporte refere-se apenas ao suporte do recurso com o Data Lake Storage Gen2. 
>
> [Contas BlockBlobStorage de desempenho Premium](storage-blob-create-account-block-blob.md) para Data Lake Storage Gen2 estão em visualização pública no momento. Os níveis de suporte para esses tipos de contas aparecem na coluna **BlockBlobStorage (Premium)** .

|Recurso de Armazenamento de Blobs |Uso geral V2 |BlockBlobStorage (Premium) |Artigos relacionados |
|---------------|-------------------|---|
|Camada de acesso quente|Disponível para o público geral|Sem suporte|[Armazenamento de Blobs do Azure: camadas de acesso frequentes, esporádicas e de arquivo](storage-blob-storage-tiers.md)|
|Camada de acesso frio|Disponível para o público geral|Sem suporte|[Armazenamento de Blobs do Azure: camadas de acesso frequentes, esporádicas e de arquivo](storage-blob-storage-tiers.md)|
|Eventos|Disponível para o público geral|Visualização|[Reagindo a eventos de Armazenamento de Blobs](storage-blob-event-overview.md)|
|Métricas (clássico)|Disponível para o público geral|Sem suporte|[Métricas da análise de Armazenamento do Microsoft Azure (clássico)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Métricas no Azure Monitor|Disponível para o público geral|Visualização|[Métricas do Armazenamento do Microsoft Azure no Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Comandos de PowerShell do armazenamento de blobs|Disponível para o público geral|Visualização|[Início Rápido: Carregar, baixar e listar blobs com o PowerShell](storage-quickstart-blobs-powershell.md)|
|Comandos da CLI do Azure para armazenamento de blobs|Disponível para o público geral|Visualização|[Início Rápido: Criar, baixar e listar blobs com a CLI do Azure](storage-quickstart-blobs-cli.md)|
|APIs de armazenamento de Blob|Disponível para o público geral|Visualização|[Início Rápido: Biblioteca de clientes do Armazenamento de blobs v12 para .NET](storage-quickstart-blobs-dotnet.md)<br>[Início Rápido: Gerenciar blobs com o SDK do Java v12](storage-quickstart-blobs-java.md)<br>[Início Rápido: Gerenciar blobs com o SDK do Python v12](storage-quickstart-blobs-python.md)<br>[Início Rápido: Gerenciar blobs com o SDK do JavaScript v12 no Node.js](storage-quickstart-blobs-nodejs.md)|
|Logs de diagnóstico|Disponível para o público geral|Visualização <div role="complementary" aria-labelledby="diagnostic-logging"><sup>1</sup></div> |[Log da análise do Armazenamento do Azure](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Logon no Azure Monitor|Visualização |Ainda não compatível|[Monitorar o Armazenamento do Microsoft Azure](../common/monitor-storage.md)|
|Camada de acesso aos arquivos|Visualização|Sem suporte|[Armazenamento de Blobs do Azure: camadas de acesso frequentes, esporádicas e de arquivo](storage-blob-storage-tiers.md)|
|Políticas de gerenciamento do ciclo de vida|Visualização|Ainda não compatível|[Gerenciar o ciclo de vida de armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md)|
|Feed de alteração|Ainda não compatível|Ainda não compatível|[Suporte ao feed de alterações no armazenamento de blobs do Azure](storage-blob-change-feed.md)|
|Failover da conta|Ainda não compatível|Ainda não compatível|[Recuperação de desastre e failover de conta](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|ACL do contêiner de blob|Ainda não compatível|Ainda não compatível|[Definir ACL do contêiner](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Domínios personalizados|Ainda não compatível|Ainda não compatível|[Mapear um domínio personalizado para um ponto de extremidade do armazenamento de blobs do Azure](storage-custom-domain-name.md)|
|Armazenamento imutável|Ainda não compatível|Ainda não compatível|[Armazenar dados de blob comercialmente críticos com armazenamento imutável](storage-blob-immutable-storage.md)|
|Instantâneos|Ainda não compatível|Ainda não compatível|[Criar e gerenciar um instantâneo de blob no .NET](storage-blob-snapshots.md)|
|Exclusão reversível|Ainda não compatível|Ainda não compatível|[Exclusão reversível para blobs do Armazenamento do Azure ](storage-blob-soft-delete.md)|
|Sites estáticos|Ainda não compatível|Ainda não compatível|[Hospedagem de site estático no Armazenamento do Azure](storage-blob-static-website.md)|

<div id="diagnostic-logging"><sup>1</sup>Para contas de armazenamento de blobs de blocos Premium, logs de diagnóstico (clássico) não podem ser habilitados usando o portal do Azure. Habilite-os usando o PowerShell.</div>

## <a name="see-also"></a>Confira também

- [Problemas conhecidos com o Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Serviços do Azure compatíveis com o Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plataformas de software livre compatíveis com o Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Acesso de vários protocolos no Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)