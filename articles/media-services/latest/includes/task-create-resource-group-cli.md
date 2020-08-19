---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: d567a4f7d9b9429887d6396cb2b03dfc34c6ac93
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88602351"
---
<!-- Create a resource group -->

Use o comando a seguir para criar um grupo de recursos. Selecione a região geográfica que será usada para armazenar os registros de mídia e de metadados para sua conta de serviços de mídia. Essa região será usada para processar e transmitir a mídia.

```azurecli
az group create --name amsResourceGroup --location westus2
```
