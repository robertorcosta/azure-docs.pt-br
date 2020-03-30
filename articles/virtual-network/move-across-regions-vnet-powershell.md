---
title: Mova uma rede virtual do Azure para outra região do Azure usando o Azure PowerShell
description: Mova uma rede virtual Do Azure de uma região do Azure para outra usando um modelo de Gerenciador de Recursos e o Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: dc316e5bbb88359ff8b1e8a4fc35a56541a577f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646703"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Mova uma rede virtual do Azure para outra região usando o Azure PowerShell

Existem vários cenários para mover uma rede virtual Azure existente de uma região para outra. Por exemplo, você pode querer criar uma rede virtual com a mesma configuração para testes e disponibilidade que sua rede virtual existente. Ou você pode querer mover uma rede virtual de produção para outra região como parte do seu planejamento de recuperação de desastres.

Você pode usar um modelo do Azure Resource Manager para concluir a mudança da rede virtual para outra região. Você faz isso exportando a rede virtual para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantando o modelo para a nova região. Para obter mais informações sobre modelos do Gerenciador de recursos, consulte [Exportar grupos de recursos para modelos](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que sua rede virtual está na região do Azure da qual você deseja se mudar.

- Para exportar uma rede virtual e implantar um modelo para criar uma rede virtual em outra região, você precisa ter a função contribuinte de rede ou superior.

- Os peerings de rede virtuais não serão recriados e falharão se ainda estiverem presentes no modelo. Antes de exportar o modelo, você tem que remover quaisquer pares de rede virtuais. Em seguida, você pode restabelecendo-os após a movimentação da rede virtual.
    
- Identifique o layout de rede de origem e todos os recursos que você está usando atualmente. Esse layout inclui, mas não se limita a balanceadores de carga, grupos de segurança de rede (NSGs) e IPs públicos.

- Verifique se sua assinatura do Azure permite criar redes virtuais na região de destino. Para habilitar a cota necessária, entre em contato com o suporte.

- Certifique-se de que sua assinatura tenha recursos suficientes para suportar a adição de redes virtuais para este processo. Para saber mais, confira [Assinatura e limites de serviço, cotas e restrições do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Preparem-se para a mudança.
Nesta seção, você prepara a rede virtual para a mudança usando um modelo de Gerenciador de recursos. Em seguida, você move a rede virtual para a região de destino usando comandos Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para exportar a rede virtual e implantar a rede virtual de destino usando o PowerShell, faça o seguinte:

1. Faça login na sua assinatura do Azure com o comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e siga as instruções na tela:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Obtenha o ID de recurso da rede virtual que você deseja mover para a região de destino e, em seguida, coloque-o em uma variável usando [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. Exportar a rede virtual de origem para um arquivo .json no diretório onde você executa o comando [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. O arquivo baixado tem o mesmo nome do grupo de recursos do que o recurso foi exportado. Localize o * \<arquivo>.json, nome do grupo de recursos,* que você exportou com o comando e, em seguida, abra-o em seu editor:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. Para editar o parâmetro do nome da rede virtual, altere a propriedade **defaultValue** do nome da rede virtual de origem para o nome da sua rede virtual de destino. Certifique-se de colocar o nome entre aspas.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. Para editar a região de destino para onde a rede virtual será movida, altere a propriedade **de localização** em recursos:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```
  
1. Para obter códigos de localização da região, você pode usar o cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) do Azure PowerShell executando o seguinte comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. (Opcional) Você também pode alterar outros parâmetros no * \<arquivo>.json* do grupo de recursos, dependendo dos seus requisitos:

    * **Espaço de endereço**: Antes de salvar o arquivo, você pode alterar o espaço de endereço da rede virtual modificando a seção **recursos** > **addressSpace** e alterando a propriedade **AddressPrefixes:**

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },
        ```

    * **Sub-rede**: Você pode alterar ou adicionar ao nome da sub-rede e ao espaço do endereço da sub-rede alterando a seção **de sub-redes** do arquivo. Você pode alterar o nome da sub-rede alterando a propriedade **de nome.** E você pode alterar o espaço de endereço da sub-rede alterando a propriedade **addressPrefix:**

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

        Para alterar o prefixo de endereço, edite o arquivo em dois lugares: no código na seção anterior e na seção de **tipo** do seguinte código. Alterar a propriedade **addressPrefix** no código a seguir para corresponder à propriedade **addressPrefix** no código na seção anterior.

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

1. Salve o * \<arquivo>.json do grupo de recursos.*

1. Crie um grupo de recursos na região de destino para que a rede virtual de destino seja implantada usando [o New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0):
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. Implante o * \<> nome de grupo de recursos.json* no grupo de recursos que você criou na etapa anterior usando [o New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. Para verificar se os recursos foram criados na região de destino, use [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) e [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>Exclua a rede virtual ou o grupo de recursos 

Depois de implantar a rede virtual, para recomeçar ou descartar a rede virtual na região de destino, exclua o grupo de recursos que você criou na região de destino e a rede virtual movida será excluída. 

Para remover o grupo de recursos, use [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>Limpar

Para comprometer suas alterações e concluir o movimento da rede virtual, faça qualquer uma das seguintes:

* Exclua o grupo de recursos usando [remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* Exclua a rede virtual de origem usando [remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0):  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu uma rede virtual de uma região para outra usando o PowerShell e, em seguida, limpou os recursos de origem desnecessários. Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres no Azure, consulte:

- [Mover recursos para um novo grupo de recursos ou assinatura](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Mova máquinas virtuais do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
