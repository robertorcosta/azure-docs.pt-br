---
title: Dimensione uma instância do Serviço DeR SignalR do Azure
description: Aprenda a dimensionar uma instância de Serviço SignalR do Azure para adicionar ou reduzir a capacidade, através do portal Azure ou do Azure CLI.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: zhshang
ms.openlocfilehash: c8d74342e624b837c7ee803a2bcdcc12a3fb814b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75659280"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>Como dimensionar uma instância de serviço do SignalR do Azure?
Este artigo mostra como dimensionar sua instância do Azure SignalR Service. Há dois cenários para escalar, escalar e escalar.

* [Scale up](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Obtenha mais unidades, conexões, mensagens e muito mais. Você aumentar á escala alterando o nível de preços de Free para Standard.
* [Escala :](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)Aumente o número de unidades SignalR. Você pode escalar até 100 unidades.

As configurações de escala levam alguns minutos para serem aplicadas. Em casos raros, pode levar cerca de 30 minutos para ser aplicado. Eles não exigem que você altere seu código ou reimplante seu aplicativo de servidor.

Para obter informações sobre os preços e capacidades do Serviço SignalR individual, consulte [Detalhes de preços do serviço Azure SignalR](https://azure.microsoft.com/pricing/details/signalr-service/).  

> [!NOTE]
> Mudando o SignalR Service de **nível Gratuito** para o nível **Padrão** ou vice-versa, o IP do serviço público será alterado e geralmente leva de 30 a 60 minutos para propagar a alteração para servidores DNS em toda a internet. Seu serviço pode ser inalcançável antes que o DNS seja atualizado. Geralmente não é recomendável mudar seu nível de preços com muita frequência.


## <a name="scale-on-azure-portal"></a>Escala no portal Azure

1. No seu navegador, abra o [portal Azure](https://portal.azure.com).

2. Na página SignalR Service, no menu esquerdo, selecione **Escala**.
   
3. Escolha seu nível de preços e clique em **Selecionar**. Defina a contagem de unidades para **Nível Padrão.**
   
    ![Escala no Portal](./media/signalr-howto-scale/signalr-howto-scale.png)

4. Clique em **Salvar**.

## <a name="scale-using-azure-cli"></a>Dimensionar usando a CLI do Azure

Esse script cria um novo recurso signalr service do **Free** Tier e um novo grupo de recursos e o dimensiona para **o Nível Padrão.** 

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate a unique service and group name with the suffix
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Free_F1 \
  --service-mode Default

# Scale up to Standard Tier, and scale out to 50 units
az signalr update \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 50
```

Anote o nome real gerado para o novo grupo de recursos. Você usará esse nome de grupo de recursos quando quiser excluir todos os recursos do grupo.

[!INCLUDE [cli-script-clean-up](../../includes/cli-script-clean-up.md)]

## <a name="compare-pricing-tiers"></a>Comparar tipos de preço

Para obter informações detalhadas, como mensagens incluídas e conexões para cada nível de preço, consulte [Detalhes de preços do serviço SignalR](https://azure.microsoft.com/pricing/details/signalr-service/).

Para obter uma tabela de limites de serviço, cotas e restrições em cada camada, consulte [os limites do SignalR Service](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-signalr-service-limits).

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu sobre como dimensionar uma única instância de Serviço SignalR.

Vários pontos finais também são suportados para cenários de escala, fragmentação e região cruzada.

> [!div class="nextstepaction"]
> [serviço signalR de escala com várias instâncias](./signalr-howto-scale-multi-instances.md)
