---
title: arquivo de inclusão
description: arquivo de inclusão
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: da27f818bf368108568287f1ed1bbdae4c3902d4
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72560224"
---
## <a name="create-a-media-services-account"></a>Criar uma conta de Serviços de Mídia

Primeiro, você precisa criar uma conta dos serviços de mídia. Esta seção mostra o que você precisa para a criação da conta usando o CLI do Azure.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos usando o comando a seguir. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos, como contas dos serviços de mídia do Azure, e as contas de armazenamento associadas são implantados e gerenciados.

Você pode substituir `amsResourceGroup` pelo valor.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Crie uma conta de armazenamento

Ao criar uma conta de serviços de mídia, você precisa fornecer o nome de um recurso de conta de armazenamento do Azure. A conta de armazenamento especificada é anexada à sua conta de serviços de mídia. Para obter mais informações sobre como as contas de armazenamento são usadas nos serviços de mídia, consulte [contas de armazenamento](../articles/media-services/latest/storage-account-concept.md).

Você deve ter uma conta de armazenamento **principal** e pode ter qualquer número de contas de armazenamento **secundárias** associadas à sua conta de serviços de mídia. Os serviços de mídia oferecem suporte a contas de **uso geral v2** (GPv2) ou GPv1 ( **uso geral v1** ). Contas somente BLOB não são permitidas como **primárias**. Se você quiser saber mais sobre contas de armazenamento, consulte [Opções de conta de armazenamento do Azure](../articles/storage/common/storage-account-options.md). 

Neste exemplo, criamos uma conta Uso Geral v2, Standard LRS. Se você quiser experimentar as contas de armazenamento, use `--sku Standard_LRS`. No entanto, ao escolher uma SKU para produção, você deve considerar, `--sku Standard_RAGRS`, que fornece replicação geográfica para continuidade dos negócios. Para obter mais informações, consulte [contas de armazenamento](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
O comando a seguir cria uma conta de armazenamento que será associada à conta dos serviços de mídia. No script abaixo, você pode substituir `storageaccountforams` pelo valor. `amsResourceGroup` deve corresponder ao valor que você deu para o grupo de recursos na etapa anterior. O nome da conta de armazenamento deve ter comprimento menor que 24.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Criar uma conta de Serviços de Mídia

O comando a seguir CLI do Azure cria uma nova conta dos serviços de mídia. Você pode substituir os seguintes valores: `amsaccount` `storageaccountforams` (deve corresponder ao valor que você forneceu para sua conta de armazenamento) e `amsResourceGroup` (deve corresponder ao valor que você deu para o grupo de recursos).

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```
