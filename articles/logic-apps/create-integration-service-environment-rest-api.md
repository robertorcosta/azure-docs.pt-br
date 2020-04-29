---
title: Criar ambientes de serviço de integração (ISEs) com a API REST de aplicativos lógicos
description: Criar um ISE (ambiente do serviço de integração) usando a API REST dos aplicativos lógicos para que você possa acessar redes virtuais do Azure (VNETs) de aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 0670331d2338b4b6419ffbff1452b5fbac91029f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80478836"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Criar um ambiente do serviço de integração (ISE) usando a API REST de aplicativos lógicos

Este artigo mostra como criar um [ISE ( *ambiente do serviço de integração* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) por meio da API REST dos aplicativos lógicos para cenários em que seus aplicativos lógicos e contas de integração precisam de acesso a uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md). Um ISE é um ambiente isolado que usa armazenamento dedicado e outros recursos que são mantidos separados do serviço de aplicativos lógicos multilocatários "globais". Essa separação também reduz os impactos que outros locatários do Azure podem ter no desempenho de seus aplicativos. Um ISE também fornece seus próprios endereços IP estáticos. Esses endereços IP são separados dos endereços IP estáticos que são compartilhados pelos aplicativos lógicos no serviço público e de vários locatários.

Você também pode criar um ISE usando o [modelo de Azure Resource Manager início rápido de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) ou usando o [portal do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

> [!IMPORTANT]
> Os aplicativos lógicos, os gatilhos internos, as ações internas e os conectores executados no ISE usam um plano de preços diferente do plano de preços baseado em consumo. Para saber como o preço e a cobrança funcionam para o ISEs, consulte o [modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para obter taxas de preços, consulte [preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Pré-requisitos

* Os mesmos [pré-requisitos](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) e [requisitos para habilitar o acesso ao ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) como quando você cria um ISE no portal do Azure

* Uma ferramenta que você pode usar para criar seu ISE chamando a API REST de aplicativos lógicos com uma solicitação HTTPS PUT. Por exemplo, você pode usar o [postmaster](https://www.getpostman.com/downloads/)ou pode criar um aplicativo lógico que executa essa tarefa.

## <a name="send-the-request"></a>Enviar a solicitação

Para criar seu ISE chamando a API REST dos aplicativos lógicos, faça esta solicitação HTTPS PUT:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> A versão 2019-05-01 da API REST dos aplicativos lógicos requer que você faça sua própria solicitação HTTP PUT para conectores do ISE.

A implantação geralmente leva dentro de duas horas para ser concluída. Ocasionalmente, a implantação pode levar até quatro horas. Para verificar o status da implantação, na [portal do Azure](https://portal.azure.com), na barra de ferramentas do Azure, selecione o ícone notificações, que abre o painel notificações.

> [!NOTE]
> Se a implantação falhar ou se você excluir o ISE, o Azure poderá levar até uma hora para liberar as sub-redes. Esse atraso significa que você pode precisar esperar antes de reutilizar essas sub-redes em outro ISE.
>
> Se você excluir sua rede virtual, o Azure geralmente levará até duas horas antes de lançar suas sub-redes, mas essa operação poderá levar mais tempo. 
> Ao excluir redes virtuais, certifique-se de que nenhum recurso ainda esteja conectado. 
> Consulte [excluir rede virtual](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Cabeçalho da solicitação

No cabeçalho da solicitação, inclua estas propriedades:

* `Content-type`: Defina esse valor de propriedade `application/json`como.

* `Authorization`: Defina esse valor de propriedade para o token de portador para o cliente que tem acesso à assinatura do Azure ou ao grupo de recursos que você deseja usar.

### <a name="request-body-syntax"></a>Sintaxe de corpo da solicitação

Aqui está a sintaxe do corpo da solicitação, que descreve as propriedades a serem usadas ao criar o ISE:

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

### <a name="request-body-example"></a>Exemplo de corpo de solicitação

Este exemplo de corpo de solicitação mostra os valores de exemplo:

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

* [Adicionar recursos a ambientes do serviço de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gerenciar ambientes de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

