---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: cURL
ms.openlocfilehash: ca7f0dab2302386a9cb7d21ebb3224d24afe5e19
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608711"
---
<!--Create a media services asset cURL-->

O comando de ondulação do Azure a seguir cria um novo ativo de serviços de mídia. Substitua os valores `subscriptionID` , `resourceGroup` e `amsAccountName` pelos valores com os quais você está trabalhando no momento. Dê um nome ao seu ativo Configurando `assetName` aqui.

```cURL
curl -X PUT 'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' -H 'Accept: application/json' -H 'Content-Type: application/json' -d '{"properties": {"description": "",}}'
```
