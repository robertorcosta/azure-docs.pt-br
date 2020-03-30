---
title: Desativar políticas de rede para pontos finais privados no Azure
description: Saiba como desativar as políticas de rede para pontos finais privados.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: b5ab62e7ab57d32a11a45713519633034deb6a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453012"
---
# <a name="disable-network-policies-for-private-endpoints"></a>Desativar políticas de rede para pontos finais privados

As políticas de rede, como o NSG( Network Security Groups, grupos de segurança de rede) não são suportadas para pontos finais privados. Para implantar um Ponto Final Privado em uma determinada sub-rede, é necessária uma configuração de desativação explícita nessa sub-rede. Esta configuração só é aplicável para o Ponto Final Privado. Para outros recursos na sub-rede, o acesso é controlado com base na definição de regras de segurança do Network Security Groups (NSG). 
 
Ao usar o portal para criar um ponto final privado, essa configuração é automaticamente desativada como parte do processo de criação. A implantação usando outros clientes requer um passo adicional para alterar essa configuração. Você pode desativar a configuração usando o shell de nuvem do portal Azure ou instalações locais do Azure PowerShell, Azure CLI ou usar modelos do Azure Resource Manager.  
 
Os exemplos a seguir descrevem `PrivateEndpointNetworkPolicies` como desativar uma rede virtual chamada *myVirtualNetwork* com uma sub-rede *padrão* hospedada em um grupo de recursos chamado *myResourceGroup*.

## <a name="using-azure-powershell"></a>Usando o PowerShell do Azure
Esta seção descreve como desativar políticas de ponto final privado de sub-rede usando o Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Usando a CLI do Azure
Esta seção descreve como desativar políticas de ponto final privado de sub-rede usando o Azure CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
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
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [o ponto final privado do Azure](private-endpoint-overview.md)
 
