---
title: Mova o IP público do Azure para outra região do Azure usando o Azure PowerShell
description: Use o modelo do Azure Resource Manager para mover o IP público do Azure de uma região do Azure para outra usando o Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: c55b6011381d385fed7c7b8175ff02ec9be66fdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641493"
---
# <a name="move-azure-public-ip-to-another-region-using-azure-powershell"></a>Mova o IP público do Azure para outra região usando o Azure PowerShell

Existem vários cenários em que você gostaria de mover seus IPs Públicos Azure existentes de uma região para outra. Por exemplo, você pode querer criar um IP público com a mesma configuração e sku para testes. Você também pode querer mover um IP público para outra região como parte do planejamento de recuperação de desastres.

Os IPs Públicos do Azure são específicos da região e não podem ser movidos de uma região para outra. No entanto, você pode usar um modelo do Azure Resource Manager para exportar a configuração existente de um IP público.  Em seguida, você pode encenar o recurso em outra região exportando o IP público para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantar o modelo para a nova região.  Para obter mais informações sobre gerenciador de recursos e modelos, consulte [Grupos de recursos de exportação para modelos](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que o IP público do Azure está na região do Azure de onde você deseja se mudar.

- Os IPs Públicos do Azure não podem ser movidos entre regiões.  Você terá que associar o novo ip público aos recursos na região alvo.

- Para exportar uma configuração de IP pública e implantar um modelo para criar um IP público em outra região, você precisará da função Contribuinte de rede ou superior.
   
- Identifique o layout de rede de origem e todos os recursos que você está usando atualmente. Esse layout inclui, mas não se limita a balanceadores de carga, grupos de segurança de rede (NSGs) e redes virtuais.

- Verifique se sua assinatura do Azure permite que você crie IPs públicos na região de destino que é usada. Contate o suporte para habilitar a cota necessária.

- Certifique-se de que sua assinatura tenha recursos suficientes para suportar a adição de IPs públicos para este processo.  Consulte [Assinatura do Azure e limites de serviço, cotas e restrições](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Prepare-se e mova-se
As etapas a seguir mostram como preparar o IP público para o movimento de configuração usando um modelo de Gerenciador de recursos e mover a configuração pública de IP para a região de destino usando o Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Exportar o modelo e implantar a partir de um script

1. Faça login na sua assinatura do Azure com o comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e siga as instruções na tela:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Obtenha o ID de recurso do IP público que deseja mover para a região de destino e coloque-o em uma variável usando [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporte a rede virtual de origem para um arquivo .json no diretório onde você executa o comando [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. O arquivo baixado será nomeado após o grupo de recursos do que o recurso foi exportado.  Localize o arquivo que foi exportado a partir do comando chamado ** \<resource-group-name>.json** e abra-o em um editor de sua escolha:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Para editar o parâmetro do nome IP público, altere a **propriedade defaultValor** do nome IP público de origem para o nome do seu IP público alvo, certifique-se de que o nome está entre aspas:
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. Para editar a região de destino para onde o IP público será movido, altere a propriedade de **localização** em recursos:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. Para obter códigos de localização da região, você pode usar o cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) do Azure PowerShell executando o seguinte comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Você também pode alterar outros parâmetros no modelo se você escolher, e são opcionais dependendo de seus requisitos:

    * **Sku** - Você pode alterar o sku do IP público na configuração de padrão para básico ou básico para padrão alterando a propriedade **sku** > **name** no arquivo ** \<>.json** do grupo de recursos:

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         Para obter mais informações sobre as diferenças entre ips públicos sku básicos e padrão, consulte [Criar, alterar ou excluir um endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

    * **Método de alocação pública de IP** e **tempo limite de ociosidade** - Você pode alterar ambas as opções no modelo alterando a propriedade **publicIPAllocationMethod** de **Dinâmica** para **Estática** ou **Estática** para **Dinâmica**. O tempo de espera ocioso pode ser alterado alterando a propriedade **idleTimeoutInMinutes** para a quantidade desejada.  O padrão é **4**:

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        Para obter mais informações sobre os métodos de alocação e os valores de tempo de tempo inocioso, consulte [Criar, alterar ou excluir um endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


9. Salve o ** \<arquivo>.json do grupo de recursos.**

10. Crie um grupo de recursos na região de destino para que o IP público-alvo seja implantado usando [o New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0).
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Implante o nome de grupo de recursos editado ** \<>.json** para o grupo de recursos criado na etapa anterior usando [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Para verificar se os recursos foram criados na região de destino, use [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) e [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```
## <a name="discard"></a>Descartar 

Após a implantação, se você deseja recomeçar ou descartar o ip público no destino, exclua o grupo de recursos criado no destino e o IP público movido será excluído.  Para remover o grupo de recursos, use [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Limpar

Para cometer as alterações e concluir a movimentação da rede virtual, exclua a rede virtual de origem ou o grupo de recursos, use [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) ou [Remove-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu um IP público do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres no Azure, consulte:


- [Mover recursos para um novo grupo de recursos ou assinatura](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Mover as VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
