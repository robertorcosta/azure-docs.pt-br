---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 195caa6f8d7c00c741741fbf80a77bd7fe5579b0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84464925"
---
O comando da CLI a seguir negará todo o tráfego para o ponto de extremidade público da conta de armazenamento. Observe que esse comando tem o parâmetro `-bypass` definido como `AzureServices`. Isso permitirá que serviços próprios confiáveis, como a Sincronização de Arquivos do Azure, acessem a conta de armazenamento por meio do ponto de extremidade público.

```bash
# This assumes $storageAccountResourceGroupName and $storageAccountName 
# are still defined from the beginning of this guide.
az storage account update \
    --resource-group $storageAccountResourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" \
    --output none
```