---
title: 'Modelo azure ExpressRoute: Crie um circuito ExpressRoute'
description: Crie, provisão, exclua e deprovisione um circuito ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 11/13/2019
ms.author: charwen
ms.openlocfilehash: ac2fb2f884feb0fbe6fbb1731ebe7c8bdad5114b
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616524"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Crie um circuito ExpressRoute usando o modelo do Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Modelo do Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Vídeo – Portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Aprenda a criar um circuito ExpressRoute implantando um modelo do Azure Resource Manager usando o Azure PowerShell. Para obter mais informações sobre como desenvolver modelos do Resource Manager, confira [documentação do Resource Manager](/azure/azure-resource-manager/) e a [referência de modelo](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Antes de começar

* Examine os [pré-requisitos](expressroute-prerequisites.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
* Verifique se você tem permissões para criar novos recursos de rede. Se você não tiver as permissões corretas, entre em contato com o administrador da conta.
* Você pode [exibir um vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) antes de começar para entender melhor as etapas.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Criar e provisionar um circuito do ExpressRoute

[Os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/) têm uma boa coleção de modelos de Gerenciador de Recursos. Você usa um dos [modelos existentes](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) para criar um circuito ExpressRoute.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Para ver mais modelos relacionados, selecione [aqui](https://azure.microsoft.com/resources/templates/?term=expressroute).

Para criar um Circuito ExpressRoute, implantando um modelo:

1. Selecione **Testar** no seguinte bloco de código e, em seguida, siga as instruções para entrar no Azure Cloud Shell.

    ```azurepowershell-interactive
    $circuitName = Read-Host -Prompt "Enter a circuit name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${circuitName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-circuit-create/azuredeploy.json"

    $serviceProviderName = "Equinix"
    $peeringLocation = "Silicon Valley"
    $bandwidthInMbps = 500
    $sku_tier = "Premium"
    $sku_family = "MeteredData"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -circuitName $circuitName -serviceProviderName $serviceProviderName -peeringLocation $peeringLocation -bandwidthInMbps $bandwidthInMbps -sku_tier $sku_tier -sku_family $sku_family

    Write-Host "Press [ENTER] to continue ..."
    ```

   * **O nível SKU** determina se um circuito ExpressRoute é [Local,](expressroute-faqs.md#expressroute-local)Standard ou [Premium.](expressroute-faqs.md#expressroute-premium) Você pode especificar *Local*, *Padrão ou *Premium*.
   * **A família SKU** determina o tipo de faturamento. Você pode especificar *dados medidos* para um plano de dados medido e *dados ilimitados* para um plano de dados ilimitado. É possível alterar o tipo de cobrança de *Metereddata* para *Unlimiteddata*, mas não é possível alterar o tipo de *Unlimiteddata* para *Metereddata*. Um circuito *local* é apenas *ilimitado.*
   * **Local de Emparelhamento** é o local físico em que você está realizando o emparelhamento com a Microsoft.

     > [!IMPORTANT]
     > O Local de Emparelhamento indica o [local físico](expressroute-locations.md) em que você está realizando o emparelhamento com a Microsoft. Isso **não** tem vínculo à propriedade "Local", que se refere à posição geográfica na qual o Provedor de Recursos de Rede do Azure está localizado. Embora eles não estejam relacionados, é uma boa prática escolher um provedor de recursos de rede geograficamente próximo do Local de Emparelhamento do circuito.

    O nome do grupo de recursos é o nome do namespace do ponto de ônibus de serviço com **rg** anexado.

2. Selecione **Copiar** para copiar o script do PowerShell.
3. Clique com o botão direito do mouse no console do Shell e selecione **Colar**.

Pode demorar alguns minutos para criar um hub de eventos.

O Azure PowerShell é usado para implantar o modelo neste tutorial. Para outros métodos de implantação de modelos, consulte:

* [Usando o portal Azure.](../azure-resource-manager/templates/deploy-portal.md)
* [Usando o Azure CLI](../azure-resource-manager/templates/deploy-cli.md).
* [Usando a API REST](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Desprovisionamento e exclusão de um circuito do ExpressRoute

Você pode excluir o circuito ExpressRoute selecionando o ícone **de exclusão.** Observe as seguintes informações:

* Você deve desvincular todas as redes virtuais do circuito do ExpressRoute. Se essa operação falhar, verifique se há redes virtuais vinculadas ao circuito.
* Se o provedor de serviços de circuito ExpressRoute for **provisionamento** ou **provisionado,** você deverá trabalhar com seu provedor de serviços para desprovisionar o circuito ao seu lado. Continuaremos a reservar recursos e a cobrar de você até que o provedor de serviços complete o desprovisionamento do circuito e nos notifique.
* Se o provedor de serviço tiver desprovisionado o circuito (o estado de provisionamento do provedor de serviços está definido como **Não provisionado),** você pode excluir o circuito. Isso interrompe a cobrança pelo circuito.

Você pode excluir o circuito ExpressRoute executando o seguinte comando PowerShell:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Próximas etapas

Depois de criar o circuito, continue para as próximas etapas:

* [Criar e modificar o roteamento do circuito do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Vincular a rede virtual ao circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
