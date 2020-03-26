---
title: incluir arquivo
description: incluir arquivo
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 09/18/2018
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: 458782011623721bb44771d42caa32130e23bbc9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "67172409"
---
[!INCLUDE [resource group intro text](resource-group.md)]

No Cloud Shell, crie um grupo de recursos com o comando [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create). O exemplo a seguir cria um grupo de recursos nomeado *myResourceGroup* no localização *Centro-Sul dos EUA*. Para ver todos os locais com suporte para o Serviço de Aplicativo no nível **Gratuito**, execute o comando [`az appservice list-locations --sku FREE`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Em geral, você cria seu grupo de recursos e os recursos em uma região próxima a você. 

Quando o comando for concluído, uma saída JSON mostra as propriedades do grupo de recursos.