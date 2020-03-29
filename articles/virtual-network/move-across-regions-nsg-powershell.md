---
title: Mova o Grupo de Segurança de Rede (NSG) do Azure para outra região do Azure usando o Azure PowerShell
description: Use o modelo do Azure Resource Manager para mover o grupo de segurança da rede Azure de uma região do Azure para outra usando o Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 0cbd8f61cb1b4cb8eae6b30625fb3039ff75adde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641461"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>Mova o Grupo de Segurança de Rede (NSG) do Azure para outra região usando o Azure PowerShell

Existem vários cenários em que você gostaria de mover seus NSGs existentes de uma região para outra. Por exemplo, você pode querer criar um NSG com as mesmas regras de configuração e segurança para testes. Você também pode querer mover um NSG para outra região como parte do planejamento de recuperação de desastres.

Grupos de segurança do Azure não podem ser movidos de uma região para outra. No entanto, você pode usar um modelo do Azure Resource Manager para exportar as regras de configuração e segurança existentes de um NSG.  Em seguida, você pode encenar o recurso em outra região exportando o NSG para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantar o modelo para a nova região.  Para obter mais informações sobre gerenciador de recursos e modelos, consulte [Exportar grupos de recursos para modelos](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que o grupo de segurança da rede Azure esteja na região do Azure, da qual você deseja se mover.

- Grupos de segurança da rede Azure não podem ser movidos entre regiões.  Você terá que associar o novo NSG aos recursos na região alvo.

- Para exportar uma configuração DE NSG e implantar um modelo para criar um NSG em outra região, você precisará da função Contribuinte de rede ou superior.
   
- Identifique o layout de rede de origem e todos os recursos que você está usando atualmente. Este layout inclui, mas não se limita a balanceadores de carga, IPs públicos e redes virtuais.

- Verifique se sua assinatura do Azure permite criar NSGs na região de destino que é usada. Contate o suporte para habilitar a cota necessária.

- Certifique-se de que sua assinatura tenha recursos suficientes para suportar a adição de NSGs para este processo.  Consulte [Assinatura do Azure e limites de serviço, cotas e restrições](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Prepare-se e mova-se
As etapas a seguir mostram como preparar o grupo de segurança da rede para o movimento de configuração e regra de segurança usando um modelo de Gerenciador de recursos e mover as regras de configuração e segurança do NSG para a região de destino usando o Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Exportar o modelo e implantar a partir de um script

1. Faça login na sua assinatura do Azure com o comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e siga as instruções na tela:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Obtenha o ID de recurso do NSG que deseja mover para a região de destino e coloque-o em uma variável usando [get-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporte o NSG de origem para um arquivo .json no diretório onde você executa o comando [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. O arquivo baixado será nomeado após o grupo de recursos do que o recurso foi exportado.  Localize o arquivo que foi exportado a partir do comando chamado ** \<resource-group-name>.json** e abra-o em um editor de sua escolha:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Para editar o parâmetro do nome NSG, altere a **propriedade defaultValor** do nome NSG de origem para o nome do seu NSG alvo, certifique-se de que o nome está entre aspas:
    
    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
            "defaultValue": "<target-nsg-name>",
            "type": "String"
            }
        }

    ```


6. Para editar a região de destino para onde as regras de configuração e segurança do NSG serão movidas, altere a propriedade **de localização** em **recursos:**

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78", 
             }
            }
    ```
  
7. Para obter códigos de localização da região, você pode usar o cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) do Azure PowerShell executando o seguinte comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Você também pode alterar outros parâmetros no nome do ** \<grupo de recursos>.json,** se você escolher, e são opcionais dependendo dos seus requisitos:

    * **Regras de segurança** - Você pode editar quais regras são implantadas no NSG de destino adicionando ou removendo regras para a seção **securityRules** no ** \<arquivo>.json do grupo de recursos:**

        ```json
           "resources": [
                  {
                  "type": "Microsoft.Network/networkSecurityGroups",
                  "apiVersion": "2019-06-01",
                  "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
                  "location": "TARGET REGION",
                  "properties": {
                       "provisioningState": "Succeeded",
                       "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                  "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                             "provisioningState": "Succeeded",
                             "protocol": "TCP",
                             "sourcePortRange": "*",
                             "destinationPortRange": "3389",
                             "sourceAddressPrefix": "*",
                             "destinationAddressPrefix": "*",
                             "access": "Allow",
                             "priority": 300,
                             "direction": "Inbound",
                             "sourcePortRanges": [],
                             "destinationPortRanges": [],
                             "sourceAddressPrefixes": [],
                             "destinationAddressPrefixes": []
                            }
                        ]
            }  
            
        ```

        Para concluir a adição ou a remoção das regras no NSG de destino, você também deve editar os tipos de regras personalizadas no final do arquivo ** \<>.json do grupo** de recursos no formato do exemplo abaixo:

        ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
        ```

9. Salve o ** \<arquivo>.json do grupo de recursos.**

10. Crie um grupo de recursos na região de destino para que o NSG de destino seja implantado usando [o New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0):
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Implante o nome de grupo de recursos editado ** \<>.json** para o grupo de recursos criado na etapa anterior usando [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Para verificar se os recursos foram criados na região de destino, use [get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) e [Get-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Descartar 

Após a implantação, se você deseja recomeçar ou descartar o NSG no destino, exclua o grupo de recursos criado no destino e o NSG movido será excluído.  Para remover o grupo de recursos, use [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Limpar

Para cometer as alterações e concluir a movimentação do NSG, exclua o NSG de origem ou o grupo de recursos, use [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) ou [Remove-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworksecuritygroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu um grupo de segurança de rede do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres no Azure, consulte:


- [Mover recursos para um novo grupo de recursos ou assinatura](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Mover as VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
