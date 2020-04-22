---
title: Reidratar dados blob do nível de arquivo
description: Reidrate suas bolhas do armazenamento de arquivos para que você possa acessar os dados.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 82ea4ad23e3207f5641ade196f69595cd1e7b323
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684084"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Reidratar dados blob do nível de arquivo

Enquanto uma bolha está no nível de acesso ao arquivo, ela é considerada offline e não pode ser lida ou modificada. Os metadados blob permanecem on-line e disponíveis, permitindo que você liste a bolha e suas propriedades. A leitura e modificação de dados blob só está disponível com camadas on-line, como quente ou legal. Existem duas opções para recuperar e acessar dados armazenados na camada de acesso ao arquivo.

1. [Reidrate uma bolha arquivada para um nível on-line](#rehydrate-an-archived-blob-to-an-online-tier) - Reidrate uma bolha de arquivo para quente ou fria, alterando seu nível usando a operação [Set Blob Tier.](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)
2. [Copie uma bolha arquivada para um nível on-line](#copy-an-archived-blob-to-an-online-tier) - Crie uma nova cópia de uma bolha de arquivo usando a operação [Copy Blob.](https://docs.microsoft.com/rest/api/storageservices/copy-blob) Especifique um nome blob diferente e um nível de destino de quente ou legal.

 Para obter mais informações sobre níveis, consulte [o armazenamento Azure Blob: níveis de acesso quentes, legais e de arquivamento](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Reidrate uma bolha arquivada para um nível on-line

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Copiar um blob arquivado para uma camada online

Se você não quiser reidratar sua bolha de arquivo, você pode optar por fazer uma operação [Copy Blob.](https://docs.microsoft.com/rest/api/storageservices/copy-blob) Sua bolha original permanecerá não modificada no arquivo enquanto uma nova bolha é criada no nível quente ou legal on-line para você trabalhar. Na operação Copiar Blob, você também pode definir a propriedade *opcional x-ms-reidrat-priority* para Padrão ou Alto para especificar a prioridade na qual você deseja que sua cópia blob seja criada.

Copiar uma bolha do arquivo pode levar horas para ser concluída, dependendo da prioridade de reidratar selecionada. Nos bastidores, a operação **Copy Blob** lê sua bolha de origem de arquivo para criar uma nova bolha on-line no nível de destino selecionado. A nova bolha pode ser visível quando você lista blobs, mas os dados não estão disponíveis até que a leitura da bolha do arquivo de origem esteja completa e os dados sejam gravados para a nova bolha de destino on-line. A nova bolha é como uma cópia independente e qualquer modificação ou exclusão a ela não afeta a bolha do arquivo de origem.

Os blobs de arquivamento só podem ser copiados para níveis de destino on-line dentro da mesma conta de armazenamento. A cópia de uma bolha de arquivo para outra bolha de arquivo não é suportada. A tabela a seguir indica as capacidades do CopyBlob.

|                                           | **Fonte de camada quente**   | **Fonte de camada legal** | **Fonte de camada de arquivamento**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **Destino de nível quente**                  | Com suporte             | Com suporte            | Suportado dentro da mesma conta; pendente de reidratar               |
| **Destino de nível legal**                 | Com suporte             | Com suporte            | Suportado dentro da mesma conta; pendente de reidratar               |
| **Destino de nível de arquivamento**              | Com suporte             | Com suporte            | Sem suporte         |

## <a name="pricing-and-billing"></a>Preços e cobrança

Reidratação de bolhas fora do arquivo em níveis quentes ou frios são carregadas como operações de leitura e recuperação de dados. O uso de alta prioridade tem custos de operação e recuperação de dados mais elevados em comparação com a prioridade padrão. Reidratação de alta prioridade aparece como um item de linha separado em sua conta. Se uma solicitação de alta prioridade para retornar uma bolha de arquivo de alguns gigabytes leva mais de 5 horas, você não será cobrado a taxa de recuperação de alta prioridade. No entanto, as taxas de recuperação padrão ainda se aplicam à medida que a reidratação foi priorizada em relação a outras solicitações.

A cópia de bolhas de arquivo em camadas quentes ou frias é cobrada como operações de leitura e recuperação de dados. Uma operação de gravação é cobrada para a criação da nova cópia blob. As taxas de exclusão antecipadas não se aplicam quando você copia para uma bolha on-line porque a bolha de origem permanece não modificada no nível de arquivo. As taxas de recuperação de alta prioridade se aplicam se selecionadas.

Blobs no nível de arquivo devem ser armazenados por um mínimo de 180 dias. A exclusão ou a reidratação de bolhas arquivadas antes de 180 dias incorrerão em taxas de exclusão antecipadas.

> [!NOTE]
> Para obter mais informações sobre preços para bolhas de bloco e reidratação de dados, consulte [Preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). Para obter mais informações sobre as taxas de transferência de dados de saída, consulte [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quickstart-scenarios"></a>Cenários de início rápido

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>Reidrate uma bolha de arquivo para um nível on-line
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Entre no [portal do Azure](https://portal.azure.com).

1. No portal Azure, procure e selecione **Todos os Recursos**.

1. Selecione sua conta de armazenamento.

1. Selecione seu recipiente e selecione sua bolha.

1. Nas **propriedades Blob,** selecione **Alterar nível**.

1. Selecione a **camada de** acesso Hot ou **Cool.** 

1. Selecione uma prioridade de reidrateção de **padrão** ou **alto**.

1. Selecione **Salvar** na parte inferior.

![Alterar o](media/storage-tiers/blob-access-tier.png)
![nível da conta de armazenamento Verifique o status de reidratar](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
O script PowerShell a seguir pode ser usado para alterar o nível blob de uma bolha de arquivo. A `$rgName` variável deve ser inicializada com o nome do seu grupo de recursos. A `$accountName` variável deve ser inicializada com o nome da sua conta de armazenamento. A `$containerName` variável deve ser inicializada com o nome do recipiente. A `$blobName` variável deve ser inicializada com o seu nome blob. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", “Standard”)
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>Copie uma bolha de arquivo para uma nova bolha com um nível on-line
O script PowerShell a seguir pode ser usado para copiar uma bolha de arquivo para uma nova bolha dentro da mesma conta de armazenamento. A `$rgName` variável deve ser inicializada com o nome do seu grupo de recursos. A `$accountName` variável deve ser inicializada com o nome da sua conta de armazenamento. As `$srcContainerName` `$destContainerName` variáveis devem ser inicializadas com os nomes dos contêineres. As `$srcBlobName` `$destBlobName` variáveis devem ser inicializadas com seus nomes blob. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$srcContainerName = ""
$destContainerName = ""
$srcBlobName = ""
$destBlobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Copy source blob to a new destination blob with access tier hot using standard rehydrate priority
Start-AzStorageBlobCopy -SrcContainer $srcContainerName -SrcBlob $srcBlobName -DestContainer $destContainerName -DestBlob $destBlobName -StandardBlobTier Hot -RehydratePriority Standard -Context $ctx
```

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre os níveis de armazenamento blob](storage-blob-storage-tiers.md)
* [Verificar o preço de frequente, esporádica e de arquivo em contas de Armazenamento de Blobs e GPv2 por região](https://azure.microsoft.com/pricing/details/storage/)
* [Gerenciar o ciclo de vida de armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md)
* [Verificar os preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
