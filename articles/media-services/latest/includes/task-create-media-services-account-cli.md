---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: c902ea001d18504dabffc8431acafe33822792e1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88539090"
---
<!--Create a media services account -->

O comando da CLI do Azure a seguir cria uma nova conta dos Serviços de Mídia. Você pode substituir os seguintes valores: `amsaccount` `storageaccountforams` (deve corresponder ao valor que você forneceu para sua conta de armazenamento) e `amsResourceGroup` (deve corresponder ao valor que você deu para o grupo de recursos).  

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```