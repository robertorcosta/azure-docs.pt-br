---
title: Implantar conjuntos de escala de máquinas virtuais com IPv6 no Azure
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implantar conjuntos de escala de máquinas virtuais com IPv6 em uma rede virtual Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: kumud
ms.openlocfilehash: b90910614bcd86a54198b1a0961a3378427ea87e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73164985"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure-preview"></a>Implantar conjuntos de escala de máquinavirtual com IPv6 no Azure (Preview)

Este artigo mostra como implantar um conjunto de escala de máquina virtual (IPv4 + IPv6) com um balanceador de carga externa de pilha dupla em uma rede virtual Azure. O processo para criar um conjunto de escala de máquina virtual capaz de IPv6 é quase idêntico ao processo de criação de VMs individuais descritos [aqui](ipv6-configure-standard-load-balancer-template-json.md). Você começará com as etapas que são semelhantes às descritas para VMs individuais:
1.  Crie IPv4 e IPv6 Public IPs.
2.  Crie um balanceador de carga de pilha dupla.  
3.  Crie regras do grupo de segurança de rede (NSG).  

O único passo que é diferente das VMs individuais é criar a configuração de interface de rede (NIC) que usa o recurso de conjunto de escala de máquina virtual: networkProfile/networkInterfaceConfigurations. A estrutura JSON é semelhante à do objeto Microsoft.Network/networkInterfaces usado para VMs individuais com a adição de definir a CONFIGURAÇÃO NIC e a IpConfiguration iPv4 como a interface principal usando o **"principal":** atributo verdadeiro como visto no exemplo a seguir:

```json
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[variables('nicName')]",
                "properties": {
                  "primary": true,
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups','VmssNsg')]"
          },                  
                  "ipConfigurations": [
                    {
                      "name": "[variables('ipConfigName')]",
                      "properties": {
                        "primary": true,
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv4",                       
                        "publicipaddressconfiguration": {
                          "name": "pub1",
                          "properties": {
                            "idleTimeoutInMinutes": 15
                          }
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer', 'bePool'))]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', 'loadBalancer', 'natPool')]"
                          }
                        ]
                      }
                    },
                    {
                      "name": "[variables('ipConfigNameV6')]",
                      "properties": {
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets','MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv6",
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer','bePoolv6')]"
                          }
                        ],                        
                      }
                    }
                  ]
                }
              }
            ]
          }

```


## <a name="sample-virtual-machine-scale-set-template-json"></a>Modelo de conjunto de escala de máquina virtual de amostra JSON

Para implantar um conjunto de escala de máquina virtual (IPv4 + IPv6) com balanceador de carga externo de pilha dupla e modelo de exemplo de exibição de rede virtual [aqui](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/).
## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o suporte ao IPv6 nas redes virtuais do Azure, veja [o que é IPv6 para Azure Virtual Network?](ipv6-overview.md).
