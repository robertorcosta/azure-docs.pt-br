---
title: Mova o Balancer de Carga interna do Azure para outra região do Azure usando o portal Azure
description: Use o modelo do Azure Resource Manager para mover o Balancer de carga interno do Azure de uma região do Azure para outra usando o portal Azure
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: f23923b9d847ef393ebd609eb5fbba530b1a07d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638798"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Mova o Balancer de Carga interna do Azure para outra região usando o portal Azure

Existem vários cenários em que você gostaria de mover seu balanceador de carga interna existente de uma região para outra. Por exemplo, você pode querer criar um balanceador de carga interno com a mesma configuração para testes. Você também pode querer mover um balanceador de carga interna para outra região como parte do planejamento de recuperação de desastres.

Os balanceadores de carga interna do Azure não podem ser movidos de uma região para outra. No entanto, você pode usar um modelo do Azure Resource Manager para exportar a configuração existente e a rede virtual de um balanceador de carga interno.  Em seguida, você pode encenar o recurso em outra região exportando o balanceador de carga e a rede virtual para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantar os modelos para a nova região.  Para obter mais informações sobre gerenciador de recursos e modelos, consulte [Quickstart: Crie e implante modelos do Azure Resource Manager usando o portal Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que o balanceador de carga interna do Azure esteja na região do Azure, da qual você deseja se mover.

- Os balanceadores de carga interna do Azure não podem ser movidos entre regiões.  Você terá que associar o novo balanceador de carga aos recursos da região alvo.

- Para exportar uma configuração de balanceador de carga interna e implantar um modelo para criar um balanceador de carga interno em outra região, você precisará da função Contribuinte de rede ou superior.

- Identifique o layout de rede de origem e todos os recursos que você está usando atualmente. Esse layout inclui, mas não se limita a balanceadores de carga, grupos de segurança de rede, máquinas virtuais e redes virtuais.

- Verifique se sua assinatura do Azure permite criar balanceadores de carga internos na região de destino que é usada. Contate o suporte para habilitar a cota necessária.

- Certifique-se de que sua assinatura tenha recursos suficientes para suportar a adição de balanceadores de carga para este processo.  Veja os limites de assinatura e serviço do [Azure, cotas e restrições](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Prepare-se e mova-se
As etapas a seguir mostram como preparar o balanceador de carga interna para a movimentação usando um modelo de Gerenciador de recursos e mover a configuração do balanceador de carga interna para a região alvo usando o portal Azure.  Como parte desse processo, a configuração de rede virtual do balanceador de carga interna deve ser incluída e deve ser feita primeiro antes de mover o balanceador de carga interna.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>Exportar o modelo de rede virtual e implantar no portal Azure

1. Faça login no [portal Azure](https://portal.azure.com) > **Grupos de Recursos**.
2. Localize o Grupo de recursos que contém a rede virtual de origem e clique nela.
3. Selecione >**modelo de exportação** **de configurações** > .
4. Escolha **Implantar** na lâmina **do modelo Exportar.**
5. Clique **EM EDITAR** > **parâmetros** para abrir o arquivo **parameters.json** no editor on-line.
6. Para editar o parâmetro do nome da rede virtual, altere a propriedade **de valor** **parâmetros:**

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```
7. Altere o valor do nome da rede virtual de origem no editor para um nome de sua escolha para o VNET de destino. Certifique-se de envolver o nome entre aspas.

8. Clique em **Salvar** no editor.

9. Clique **EM EDITAR** > **modelo** para abrir o arquivo **template.json** no editor on-line.

10. Para editar a região de destino para onde o VNET será movido, altere a propriedade **de localização** em recursos:

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

11. Para obter códigos de localização da região, consulte [Azure Localizações](https://azure.microsoft.com/global-infrastructure/locations/).  O código para uma região é o nome da região sem espaços, **central dos EUA.** = **centralus**

12. Você também pode alterar outros parâmetros no arquivo **template.json** se você escolher, e são opcionais dependendo de seus requisitos:

    * **Espaço de endereço** - O espaço de endereço do VNET pode ser alterado antes de salvar modificando a seção **recursos** > **addressSpace** e alterando a propriedade **addressPrefixes** no arquivo **template.json:**

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

    * **Sub-rede** - O nome da sub-rede e o espaço do endereço da sub-rede podem ser alterados ou adicionados modificando a seção **de sub-redes** do arquivo **template.json.** O nome da sub-rede pode ser alterado alterando a propriedade do **nome.** O espaço de endereço da sub-rede pode ser alterado alterando a propriedade **addressPrefix** no arquivo **template.json:**

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

         No arquivo **template.json,** para alterar o prefixo de endereço, ele deve ser editado em dois lugares, a seção listada acima e a seção **de tipo** listada abaixo.  Alterar a **propriedade addressPrefix** para corresponder à acima:

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

13. Clique em **Salvar** no editor on-line.

14. Clique **em Assinatura BÁSICA** > **Para** escolher a assinatura onde o VNET de destino será implantado.

15. Clique no**grupo de recursos** **BASICS** > para escolher o grupo de recursos onde o VNET de destino será implantado.  Você pode clicar em **Criar novo** para criar um novo grupo de recursos para o VNET de destino.  Certifique-se de que o nome não é o mesmo que o grupo de recursos de origem do VNET existente.

16. Verifique o local do > **BASICS** está definido para o local de destino onde deseja que o VNET seja implantado. **BASICS**

17. Verifique em **CONFIGURAÇÕES** se o nome corresponde ao nome que você inseriu no editor de parâmetros acima.

18. Verifique a caixa em **TERMOS E CONDIÇÕES**.

19. Clique no botão **Comprar** para implantar a rede virtual de destino.

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportar o modelo de balanceador de carga interna e implantar a partir do Azure PowerShell

1. Faça login no [portal Azure](https://portal.azure.com) > **Grupos de Recursos**.
2. Localize o Grupo de recursos que contém o balanceador de carga interna de origem e clique nele.
3. Selecione >**modelo de exportação** **de configurações** > .
4. Escolha **Implantar** na lâmina **do modelo Exportar.**
5. Clique **EM EDITAR** > **parâmetros** para abrir o arquivo **parameters.json** no editor on-line.

6. Para editar o parâmetro do nome do balanceador de carga interna, altere a **propriedade defaultValor** do nome do balanceador de carga interna de origem para o nome do balanceador de carga interno de destino, certifique-se de que o nome está entre aspas:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

6. Para editar o valor da rede virtual de destino que foi movida acima, você deve primeiro obter o ID de recurso e, em seguida, copiá-lo e colá-lo no arquivo **parameters.json.** Para obter o ID:

    1. Faça login no [portal Azure](https://portal.azure.com) > **Grupos de recursos** em outra guia ou janela do navegador.
    2. Localize o grupo de recursos de destino que contém a rede virtual movida a partir das etapas acima e clique nela.
    3. Selecione > **propriedades de configurações** > **.**
    4. Na lâmina à direita, destaque o **ID de recurso** e copie-o para a área de transferência.  Alternativamente, você pode clicar no botão **copiar para a área de transferência** à direita do caminho de **ID de recurso.**
    5. Cole o ID de recurso na propriedade **defaultValue** no editor **Editar parâmetros** aberto na outra janela ou guia do navegador:

        ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
        ```
    6. Clique em **Salvar** no editor on-line.

7. Clique **EM EDITAR** > **modelo** para abrir o arquivo **template.json** no editor on-line.
8. Para editar a região de destino para onde a configuração do balanceador de carga interna será movida, altere a propriedade **de localização** em **recursos** no arquivo **template.json:**

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

9.  Para obter códigos de localização da região, consulte [Azure Localizações](https://azure.microsoft.com/global-infrastructure/locations/).  O código para uma região é o nome da região sem espaços, **central dos EUA.** = **centralus**

10. Você também pode alterar outros parâmetros no modelo se você escolher, e são opcionais dependendo de seus requisitos:

    * **Sku** - Você pode alterar o sku do balanceador de carga interna na configuração de padrão para básico ou básico para padrão alterando a propriedade **sku** > **name** no arquivo **template.json:**

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Para obter mais informações sobre as diferenças entre balanceadores básicos e padrão de carga sku, consulte a visão geral do [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **Regras de balanceamento de carga** - Você pode adicionar ou remover regras de balanceamento de carga na configuração adicionando ou removendo entradas na seção **loadBalanceingRules** do arquivo **template.json:**

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Para obter mais informações sobre as regras de balanceamento de carga, consulte [O que é o Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Testes** - Você pode adicionar ou remover um teste para o balanceador de carga na configuração adicionando ou removendo **entradas** na seção de testes do arquivo **template.json:**

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Para obter mais informações sobre os testes de saúde do Azure Load Balancer, consulte [testes de saúde load balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **Regras naT de entrada** - Você pode adicionar ou remover as regras NAT de entrada para o balanceador de carga adicionando ou removendo entradas na seção **de inboundNatRules** do arquivo **template.json:**

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Para completar a adição ou remoção de uma regra NAT de entrada, a regra deve estar presente ou removida como uma propriedade de **tipo** no final do arquivo **template.json:**

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Para obter mais informações sobre as regras de NAT de entrada, consulte [O que é o Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

12. Clique em **Salvar** no editor on-line.

13. Clique **em ASSINATURA BASICS** > **Subscription** para escolher a assinatura onde o balanceador de carga interna de destino será implantado.

15. Clique no**grupo de recursos** **BASICS** > para escolher o grupo de recursos onde o balanceador de carga de destino será implantado.  Você pode clicar em **Criar um novo** grupo de recursos para o balanceador de carga interna de destino ou escolher o grupo de recursos existente que foi criado acima para a rede virtual.  Certifique-se de que o nome não é o mesmo que o grupo de recursos de origem do balanceador de carga interna de origem existente.

16. Verifique **basics** > **A localização** está definida no local de destino onde deseja que o balanceador de carga interno seja implantado.

17. Verifique em **CONFIGURAÇÕES** se o nome corresponde ao nome que você inseriu no editor de parâmetros acima.  Verifique se os IDs de recursos estão preenchidos para quaisquer redes virtuais na configuração.

18. Verifique a caixa em **TERMOS E CONDIÇÕES**.

19. Clique no botão **Comprar** para implantar a rede virtual de destino.

## <a name="discard"></a>Descartar

Se você deseja descartar a rede virtual de destino e o balanceador de carga interna, exclua o grupo de recursos que contém a rede virtual de destino e o balanceador de carga interna.  Para isso, selecione o grupo de recursos do painel no portal e **selecione Excluir** na parte superior da página de visão geral.

## <a name="clean-up"></a>Limpar

Para cometer as alterações e concluir a movimentação da rede virtual e do balanceador de carga interna, exclua a rede virtual de origem e o balanceador de carga interna ou grupo de recursos. Para isso, selecione a rede virtual e o balanceador de carga interna ou grupo de recursos do seu painel no portal e **selecione Excluir** no topo de cada página.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu um balanceador de carga interna do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres no Azure, consulte:


- [Mover recursos para um novo grupo de recursos ou assinatura](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Mover as VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
