---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: dotnet
ms.openlocfilehash: 820353ffbb5e23e27c3039ad27a8f6507b9d2167
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608710"
---
<!--Create a media services asset REST-->

O comando do Azure .NET a seguir cria um novo ativo de serviços de mídia. Substitua os valores `subscriptionID` , `resourceGroup` e `amsAccountName` pelos valores com os quais você está trabalhando no momento. Dê um nome ao seu ativo Configurando `assetName` aqui.

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```
