---
title: Verificar a propriedade hora da última sincronização de uma conta de armazenamento
titleSuffix: Azure Storage
description: Saiba como verificar a propriedade **hora da última sincronização** de uma conta de armazenamento replicada geograficamente. A propriedade **hora da última sincronização** indica a última vez em que todas as gravações da região primária foram gravadas com êxito na região secundária.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/16/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 3a406ce6db060b9ff5be7bcadecb6c7ff7e65a1f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77165482"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>Verificar a propriedade hora da última sincronização de uma conta de armazenamento

Ao configurar uma conta de armazenamento, você pode especificar que seus dados sejam copiados para uma região secundária que seja de centenas de milhas da região primária. A replicação geográfica oferece durabilidade para seus dados em caso de uma interrupção significativa na região primária, como um desastre natural. Se você também habilitar o acesso de leitura para a região secundária, seus dados permanecerão disponíveis para operações de leitura se a região primária ficar indisponível. Você pode projetar seu aplicativo para alternar diretamente para a leitura da região secundária se a região primária não estiver respondendo.

O GRS (armazenamento com redundância geográfica) e o GZRS (armazenamento com redundância de zona geográfica) (visualização) replicam seus dados de forma assíncrona para uma região secundária. Para acesso de leitura à região secundária, habilite o armazenamento com redundância geográfica com acesso de leitura (RA-GRS) ou o armazenamento com redundância de acesso de leitura (RA-GZRS). Para obter mais informações sobre as várias opções de redundância oferecidas pelo armazenamento do Azure, consulte [redundância de armazenamento do Azure](storage-redundancy.md).

Este artigo descreve como verificar a propriedade **hora da última sincronização** de sua conta de armazenamento para que você possa avaliar qualquer discrepância entre as regiões primárias e secundárias.

## <a name="about-the-last-sync-time-property"></a>Sobre a propriedade hora da última sincronização

Como a replicação geográfica é assíncrona, é possível que os dados gravados na região primária ainda não tenham sido gravados na região secundária no momento em que ocorrer uma interrupção. A propriedade **hora da última sincronização** indica a última vez que os dados da região primária foram gravados com êxito na região secundária. Todas as gravações feitas na região primária antes da hora da última sincronização estão disponíveis para serem lidas a partir do local secundário. As gravações feitas na região primária após a última propriedade de hora de sincronização podem ou não estar disponíveis para leituras ainda.

A propriedade **hora da última sincronização** é um valor de data/hora GMT.

## <a name="get-the-last-sync-time-property"></a>Obter a propriedade hora da última sincronização

Você pode usar o PowerShell ou CLI do Azure para recuperar o valor da **última** propriedade de hora de sincronização.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para obter a hora da última sincronização para a conta de armazenamento com o PowerShell, instale um módulo de visualização do armazenamento do Azure que dá suporte à obtenção de estatísticas de replicação geográfica. Por exemplo:

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
```

Em seguida, verifique a propriedade **GeoReplicationStats. LastSyncTime** da conta de armazenamento. Lembre-se de substituir os valores de espaço reservado pelos seus próprios valores:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para obter a hora da última sincronização para a conta de armazenamento com CLI do Azure, verifique a propriedade **geoReplicationStats. lastSyncTime** da conta de armazenamento. Use o `--expand` parâmetro para retornar valores para as propriedades aninhadas em **geoReplicationStats**. Lembre-se de substituir os valores de espaço reservado pelos seus próprios valores:

```azurecli-interactive
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

---

## <a name="see-also"></a>Confira também

- [Redundância de armazenamento do Azure](storage-redundancy.md)
- [Alterar a opção de redundância para uma conta de armazenamento](redundancy-migration.md)
- [Criando aplicativos altamente disponíveis usando o armazenamento com redundância geográfica com acesso de leitura](storage-designing-ha-apps-with-ragrs.md)