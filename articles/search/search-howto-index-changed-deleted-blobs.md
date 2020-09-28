---
title: BLOBs alterados e excluídos
titleSuffix: Azure Cognitive Search
description: Após um Build de índice de pesquisa inicial que importa do armazenamento de BLOBs do Azure, a indexação subsequente pode pegar apenas os blobs que são alterados ou excluídos. Este artigo explica os detalhes.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 210a4a352f5130bc67d70f47bdf6305c5a992282
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403764"
---
# <a name="how-to-set-up-change-and-deletion-detection-for-blobs-in-azure-cognitive-search-indexing"></a>Como configurar a detecção de alteração e exclusão para BLOBs na indexação de Pesquisa Cognitiva do Azure

Depois que um índice de pesquisa inicial é criado, talvez você queira configurar trabalhos de indexador subsequentes para pegar apenas os documentos que foram criados ou excluídos desde a execução inicial. Para o conteúdo de pesquisa originado do armazenamento de BLOBs do Azure, a detecção de alteração ocorre automaticamente quando você usa uma agenda para disparar a indexação. Por padrão, o serviço reindexa apenas os BLOBs alterados, conforme determinado pelo `LastModified` carimbo de data/hora do blob. Em contraste com outras fontes de dados com suporte dos indexadores de pesquisa, os BLOBs sempre têm um carimbo de data/hora, o que elimina a necessidade de configurar manualmente uma política de detecção de alteração.

Embora a detecção de alterações seja determinada, a detecção de exclusão não é. Se você quiser detectar documentos excluídos, use uma abordagem de "exclusão reversível". Se você excluir os blobs imediatamente, documentos correspondentes não serão removidos do índice de pesquisa.

Há duas maneiras de implementar a abordagem de exclusão reversível. Ambos são descritos abaixo.

## <a name="native-blob-soft-delete-preview"></a>Exclusão reversível de blob nativo (versão prévia)

> [!IMPORTANT]
> O suporte para exclusão reversível de blob nativo está em versão prévia. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2020-06-30-Preview](./search-api-preview.md) fornece esse recurso. No momento, não há suporte para Portal ou SDK do .NET.

> [!NOTE]
> Ao usar a política de exclusão reversível de blob nativo, as chaves de documento para os documentos em seu índice devem ser uma propriedade de BLOB ou metadados de BLOB.

Nesse método, você usará o recurso de [exclusão reversível de blob nativo](../storage/blobs/soft-delete-blob-overview.md) oferecido pelo armazenamento de BLOBs do Azure. Se a exclusão reversível de blob nativo estiver habilitada em sua conta de armazenamento, sua fonte de dados terá um conjunto de políticas de exclusão reversível nativa e o indexador encontrará um blob que foi transferido para um estado de exclusão reversível, o indexador removerá esse documento do índice. Não há suporte para a política de exclusão reversível de blob nativo ao indexar blobs de Azure Data Lake Storage Gen2.

Use as etapas a seguir:

1. Habilite [a exclusão reversível nativa para o armazenamento de BLOBs do Azure](../storage/blobs/soft-delete-blob-overview.md). É recomendável definir a política de retenção com um valor muito maior do que a agenda do intervalo do indexador. Dessa forma, se houver um problema ao executar o indexador ou se você tiver um grande número de documentos para indexar, haverá muito tempo para que o indexador eventualmente processe os BLOBs com exclusão reversível. Os indexadores do Azure Pesquisa Cognitiva excluirão apenas um documento do índice se ele processar o blob enquanto ele estiver em um estado de exclusão reversível.

1. Configure uma política de detecção de exclusão reversível de blob nativo na fonte de dados. Um exemplo é mostrado abaixo. Como esse recurso está em versão prévia, você deve usar a API REST de visualização.

1. Execute o indexador ou defina o indexador para ser executado em um agendamento. Quando o indexador executar e processar o blob, o documento será removido do índice.

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

### <a name="reindexing-un-deleted-blobs-using-native-soft-delete-policies"></a>Reindexando BLOBs não excluídos (usando políticas nativas de exclusão reversível)

Se você excluir um blob do armazenamento de BLOBs do Azure com a exclusão reversível nativa habilitada em sua conta de armazenamento, o blob passará para um estado de exclusão reversível, dando a você a opção de cancelar a exclusão desse blob dentro do período de retenção. Se você reverter uma exclusão depois que o indexador o tiver processado, o indexador nem sempre indexará o blob restaurado. Isso ocorre porque o indexador determina quais BLOBs serão indexados com base no `LastModified` carimbo de data/hora do blob. Quando um blob com exclusão reversível não é excluído, seu `LastModified` carimbo de data/hora não é atualizado, portanto, se o indexador já tiver processado BLOBs com `LastModified` carimbos de data/hora mais recentes, ele não reindexará o BLOB não excluído. 

Para garantir que um BLOB não excluído seja reindexado, será necessário atualizar o `LastModified` carimbo de data/hora do blob. Uma maneira de fazer isso é salvando novamente os metadados desse blob. Você não precisa alterar os metadados, mas resalvá-los atualizará o `LastModified` carimbo de data/hora do blob para que o indexador saiba que ele precisa reindexar esse BLOB.

## <a name="soft-delete-using-custom-metadata"></a>Exclusão reversível usando metadados personalizados

Nesse método, você usará os metadados de um blob para indicar quando um documento deve ser removido do índice de pesquisa. Esse método requer duas ações separadas, excluindo o documento de pesquisa do índice, seguido pela exclusão de blob no armazenamento do Azure.

Use as etapas a seguir:

1. Adicione um par chave-valor de metadados personalizados ao blob para indicar ao Azure Pesquisa Cognitiva que ele é excluído logicamente.

1. Configure uma política de detecção de coluna de exclusão reversível na fonte de dados. Um exemplo é mostrado abaixo.

1. Depois que o indexador tiver processado o blob e excluído o documento do índice, você poderá excluir o blob no armazenamento de BLOBs do Azure.

Por exemplo, a política a seguir considerará que um blob foi excluído se tiver uma propriedade de metadados `IsDeleted` com o valor `true`:

```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
```

### <a name="reindexing-un-deleted-blobs-using-custom-metadata"></a>Reindexando BLOBs não excluídos (usando metadados personalizados)

Depois que um indexador processar um blob excluído e remover o documento de pesquisa correspondente do índice, ele não revisitará esse BLOB se você restaurá-lo posteriormente se o `LastModified` carimbo de data/hora do blob for mais antigo do que a última execução do indexador.

Se você quiser reindexar esse documento, altere o `"softDeleteMarkerValue" : "false"` para esse BLOB e execute novamente o indexador.

## <a name="see-also"></a>Confira também

* [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
* [Como configurar um indexador de BLOB](search-howto-indexing-azure-blob-storage.md)
* [Visão geral da indexação de BLOB](search-blob-storage-integration.md)