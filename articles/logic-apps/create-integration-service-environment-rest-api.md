---
title: Crie ambientes de serviço de integração (ISEs) com a API Logic Apps REST
description: Crie um ambiente de serviço de integração (ISE) usando a API Logic Apps REST para que você possa acessar redes virtuais (VNETs) do Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 2c6e35b1e7d160064998004f87c5b14d0eaeac5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127665"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Crie um ambiente de serviço de integração (ISE) usando a API Logic Apps REST

Este artigo mostra como criar um [ *ambiente de serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) através da API Logic Apps REST para cenários onde seus aplicativos lógicos e contas de integração precisam de acesso a uma [rede virtual Do Zure.](../virtual-network/virtual-networks-overview.md) Um ISE é um ambiente isolado que usa armazenamento dedicado e outros recursos que são mantidos separados do serviço "global" de aplicativos lógicos multilocatários. Essa separação também reduz os impactos que outros locatários do Azure podem ter no desempenho de seus aplicativos. Um ISE também fornece seus próprios endereços IP estáticos. Esses endereços IP são separados dos endereços IP estáticos que são compartilhados pelos aplicativos lógicos no serviço público e multilocatário.

Para criar uma ISE usando o portal Azure, consulte [Conectar-se às redes virtuais do Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

> [!IMPORTANT]
> Aplicativos de lógica, gatilhos incorporados, ações incorporadas e conectores que são executados em seu ISE usam um plano de preços diferente do plano de preços baseado no consumo. Para saber como funcionam os preços e o faturamento dos ISEs, consulte o [modelo de preços da Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para obter preços, consulte [preços da Logic Apps](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Pré-requisitos

* Os [mesmos pré-requisitos](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) e [requisitos para habilitar o acesso ao seu ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) como quando você cria um ISE no portal Azure

* Uma ferramenta que você pode usar para criar seu ISE chamando a API Logic Apps REST com uma solicitação HTTPS PUT. Por exemplo, você pode usar [o Carteiro](https://www.getpostman.com/downloads/), ou você pode construir um aplicativo lógico que executa essa tarefa.

## <a name="send-the-request"></a>Enviar a solicitação

Para criar seu ISE chamando a API de logic apps REST, faça esta solicitação HTTPS PUT:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> A versão da API REST 2019-05-01 do Logic Apps requer que você faça sua própria solicitação HTTP PUT para conectores ISE.

A implantação geralmente leva duas horas para ser concluída. Ocasionalmente, a implantação pode levar até quatro horas. Para verificar o status de implantação, no [portal Azure,](https://portal.azure.com)na barra de ferramentas do Azure, selecione o ícone notificações, que abre o painel de notificações.

> [!NOTE]
> Se a implantação falhar ou se você excluir o ISE, o Azure poderá levar até uma hora para liberar as sub-redes. Este atraso significa que você pode ter que esperar antes de reutilizar essas sub-redes em outro ISE.
>
> Se você excluir sua rede virtual, o Azure geralmente leva até duas horas antes de liberar suas sub-redes, mas essa operação pode levar mais tempo. 
> Ao excluir redes virtuais, certifique-se de que nenhum recurso ainda esteja conectado. 
> Consulte [Excluir rede virtual](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Cabeçalho da solicitação

No cabeçalho de solicitação, inclua estas propriedades:

* `Content-type`: Definir este `application/json`valor de propriedade para .

* `Authorization`: Defina esse valor de propriedade para o token do portador para o cliente que tiver acesso à assinatura do Azure ou ao grupo de recursos que você deseja usar.

### <a name="request-body-syntax"></a>Sintaxe de corpo da solicitação

Aqui está a sintaxe do corpo de solicitação, que descreve as propriedades a serem usados quando você cria seu ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      }
   }
}
```

### <a name="request-body-example"></a>Solicitar exemplo de corpo

Este corpo de solicitação de exemplo mostra os valores da amostra:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      }
   }
}
```

## <a name="next-steps"></a>Próximas etapas

* [Adicionar recursos aos ambientes de serviços de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gerenciar ambientes de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

