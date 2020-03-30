---
title: 'Desativar políticas de rede para o endereço IP de origem do serviço Azure Private Link '
description: Saiba como desativar as políticas de rede do Link privado do Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 4c6bd64d141341e0b7fa5641e04320a95d7951bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453003"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Desativar políticas de rede para IP de fonte de serviço do Private Link

Para escolher um endereço IP de origem para o serviço `privateLinkServiceNetworkPolicies` Private Link, é necessária uma configuração de desativação explícita na sub-rede. Essa configuração só é aplicável para o endereço IP privado específico que você escolheu como o IP de origem do serviço Private Link. Para outros recursos na sub-rede, o acesso é controlado com base na definição de regras de segurança do Network Security Groups (NSG). 
 
Ao usar qualquer cliente Azure (PowerShell, CLI ou templates), é necessário um passo adicional para alterar essa propriedade. Você pode desativar a diretiva usando o shell de nuvem do portal Azure ou instalações locais do Azure PowerShell, Azure CLI ou usar modelos do Azure Resource Manager.  
 
Siga as etapas abaixo para desativar as políticas de rede de serviços de link privados para uma rede virtual chamada *myVirtualNetwork* com uma sub-rede *padrão* hospedada em um grupo de recursos chamado *myResourceGroup*. 

## <a name="using-azure-powershell"></a>Usando o PowerShell do Azure
Esta seção descreve como desativar políticas de ponto final privado de sub-rede usando o Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Usando a CLI do Azure
Esta seção descreve como desativar políticas de ponto final privado de sub-rede usando o Azure CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
```
## <a name="using-a-template"></a>Criação de um modelo
Esta seção descreve como desativar políticas de ponto final privado de sub-rede usando o Azure Resource Manager Template.
```json
{ 
    "name": "myVirtualNetwork", 
    "type": "Microsoft.Network/virtualNetworks", 
    "apiVersion": "2019-04-01", 
    "location": "WestUS", 
    "properties": { 
        "addressSpace": { 
            "addressPrefixes": [ 
                "10.0.0.0/16" 
             ] 
        }, 
        "subnets": [ 
               { 
                 "name": "default", 
                 "properties": { 
                        "addressPrefix": "10.0.0.0/24", 
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [o Azure Private Endpoint](private-endpoint-overview.md)
 
