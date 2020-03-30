---
title: Verifique a última propriedade Sync Time para obter uma conta de armazenamento
titleSuffix: Azure Storage
description: Saiba como verificar a **propriedade Last Sync Time** para obter uma conta de armazenamento replicada geo-replicada. A última propriedade **Sync Time** indica a última vez em que todas as gravações da região primária foram escritas com sucesso para a região secundária.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/16/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 3a406ce6db060b9ff5be7bcadecb6c7ff7e65a1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165482"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>Verifique a última propriedade Sync Time para obter uma conta de armazenamento

Quando você configura uma conta de armazenamento, você pode especificar que seus dados são copiados para uma região secundária que está a centenas de quilômetros da região primária. A replicação geotécnica oferece durabilidade para seus dados no caso de uma paralisação significativa na região primária, como um desastre natural. Se você habilitar adicionalmente o acesso à leitura para a região secundária, seus dados permanecem disponíveis para operações de leitura se a região primária ficar indisponível. Você pode projetar seu aplicativo para alternar perfeitamente para leitura da região secundária se a região primária não estiver respondendo.

O grs (GrS) e o gzrs (visualização) de armazenamento geo-redundante (visualização) replicam seus dados de forma assíncrona para uma região secundária. Para acesso à leitura da região secundária, habilite o armazenamento geo-redundante de acesso de leitura (RA-GRS) ou o armazenamento geo-redundante de acesso de leitura (RA-GZRS). Para obter mais informações sobre as várias opções de redundância oferecidas pelo Azure Storage, consulte [a redundância de armazenamento do Azure](storage-redundancy.md).

Este artigo descreve como verificar a **propriedade Last Sync Time** para sua conta de armazenamento para que você possa avaliar qualquer discrepância entre as regiões primária e secundária.

## <a name="about-the-last-sync-time-property"></a>Sobre a última propriedade Sync Time

Como a replicação geográfica é assíncrona, é possível que os dados escritos para a região primária ainda não tenha sido escrito para a região secundária no momento em que ocorre uma paralisação. A última propriedade **Sync Time** indica a última vez que os dados da região primária foram gravados com sucesso na região secundária. Todas as gravações feitas na região primária antes do último tempo de sincronização estão disponíveis para serem lidas a partir do local secundário. As gravações feitas na região primária após a última propriedade de tempo de sincronização podem ou não estar disponíveis para leituras ainda.

A última propriedade **Sync Time** é um valor de data/hora GMT.

## <a name="get-the-last-sync-time-property"></a>Obtenha a última propriedade Sync Time

Você pode usar o PowerShell ou o Azure CLI para recuperar o valor da propriedade **Last Sync Time.**

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Para obter o último tempo de sincronização para a conta de armazenamento com o PowerShell, instale um módulo de visualização do Azure Storage que suporta obter estatísticas de georeplicação. Por exemplo:

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
```

Em seguida, verifique a propriedade **GeoReplicationStats.LastSyncTime** da conta de armazenamento. Lembre-se de substituir os valores do espaço reservado por seus próprios valores:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para obter o último tempo de sincronização para a conta de armazenamento com o Azure CLI, verifique a propriedade **geoReplicationStats.lastSyncTime** da conta de armazenamento. Use `--expand` o parâmetro para retornar valores para as propriedades aninhadas em **geoReplicationStats**. Lembre-se de substituir os valores do espaço reservado por seus próprios valores:

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

- [Redundância de armazenamento azure](storage-redundancy.md)
- [Alterar a opção de redundância para uma conta de armazenamento](redundancy-migration.md)
- [Projetando aplicativos altamente disponíveis usando armazenamento geo-redundante de acesso de leitura](storage-designing-ha-apps-with-ragrs.md)