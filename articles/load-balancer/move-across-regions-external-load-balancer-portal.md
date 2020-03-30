---
title: Mova um balanceador de carga externa do Azure para outra região do Azure usando o portal Azure
description: Use um modelo de Gerenciador de Recursos do Azure para mover um balanceador de carga externo de uma região do Azure para outra usando o portal Azure.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 5cd5ce2635ce05c4d5962f12ddc3945342897ecd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638486"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>Mova um balanceador de carga externo para outra região usando o portal Azure

Existem vários cenários em que você gostaria de mover um balanceador de carga externa de uma região para outra. Por exemplo, você pode querer criar outro balanceador de carga externo com a mesma configuração para testes. Você também pode querer mover um balanceador de carga externa para outra região como parte do planejamento de recuperação de desastres.

Em um sentido literal, você não pode mover um balanceador de carga externa Do Zure de uma região para outra. Mas você pode usar um modelo do Azure Resource Manager para exportar a configuração existente e o endereço IP público de um balanceador de carga externo. Em seguida, você pode encenar o recurso em outra região exportando o balanceador de carga e o IP público para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantando o modelo para a nova região. Para obter mais informações sobre gerenciador de recursos e modelos, consulte [Exportar grupos de recursos para modelos](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que o balanceador de carga externo do Azure esteja na região do Azure, da qual você deseja se mover.

- Balanceadores de carga externos do Azure não podem ser movidos entre regiões. Você terá que associar o novo balanceador de carga aos recursos da região alvo.

- Para exportar uma configuração de balanceador de carga externa e implantar um modelo para criar um balanceador de carga externo em outra região, você precisará ser designado para a função contribuinte de rede ou superior.

- Identifique o layout de rede de origem e todos os recursos que você está usando atualmente. Esse layout inclui, mas não se limita a balanceadores de carga, grupos de segurança de rede, IPs públicos e redes virtuais.

- Verifique se sua assinatura do Azure permite criar balanceadores de carga externos na região de destino. Contate o suporte para habilitar a cota necessária.

- Certifique-se de que sua assinatura tenha recursos suficientes para suportar a adição dos balanceadores de carga. Consulte [Assinatura do Azure e limites de serviço, cotas e restrições](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).

## <a name="prepare-and-move"></a>Prepare-se e mova-se
Os procedimentos a seguir mostram como preparar o balanceador de carga externa para a mudança usando um modelo de Gerenciador de recursos e mover a configuração do balanceador de carga externa para a região alvo usando o portal Azure. Primeiro deve exportar a configuração IP pública do balanceador de carga externo.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>Exportar o modelo ip público e implantar o IP público a partir do portal

1. Faça login no [portal Azure](https://portal.azure.com) e selecione **grupos de recursos**.
2. Localize o grupo de recursos que contém o IP público de origem e selecione-o.
3. Selecione**Modelo de exportação de** **configurações** > .
4. Selecione **Implantar** em **modelo Exportar**.
5. Selecione **TEMPLATE** > **Editar parâmetros** para abrir o arquivo parameters.json no editor on-line.
8. Para editar o parâmetro do nome IP público, altere a propriedade de **valor** **parâmetros** do nome IP público de origem para o nome do seu IP público alvo. Feche o nome entre aspas.

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```

    Selecione **Salvar** no editor.

9.  Selecione **MODELO** > **Editar modelo** para abrir o arquivo template.json no editor on-line.

10. Para editar a região de destino para a qual o IP público será movido, altere a propriedade de **localização** em **recursos**:

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
  
    Para obter códigos de localização da região, consulte [os locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/). O código para uma região é o nome da região sem espaços. Por exemplo, o código para o Central US é **central.**
    
12. Você também pode alterar outros parâmetros no modelo se quiser ou precisar, dependendo dos seus requisitos:

    * **SKU**. Você pode alterar o SKU do IP público na configuração de padrão para básico ou de básico para padrão alterando a propriedade **de nome** sob **sku** no arquivo template.json:

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

        Para obter informações sobre as diferenças entre IPs públicos SKU básicos e padrão, consulte [Criar, alterar ou excluir um endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

    * **Método público de alocação de IP** e **tempo de tempo inocioso**. Você pode alterar o método público de alocação de IP alterando a propriedade **publicIPAllocationMethod** de **Dinâmica** para **Estática** ou de **Estática** para **Dinâmica**. Você pode alterar o tempo de espera ocioso alterando a propriedade **idleTimeoutInMinutes** para o valor desejado. O padrão é **4**.

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

        ```

        Para obter informações sobre os métodos de alocação e os valores de tempo de tempo inocioso, consulte [Criar, alterar ou excluir um endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

 
13. Selecione **Salvar** no editor on-line.

14. Selecione**a assinatura** **BASICS** > para escolher a assinatura onde o IP público-alvo será implantado.

15. Selecione o**grupo de recursos** **BASICS** > para escolher o grupo de recursos onde o IP público-alvo será implantado. Você pode selecionar **Criar novo** para criar um novo grupo de recursos para o IP público-alvo. Certifique-se de que o nome não é o mesmo que o grupo de recursos de origem do IP público de origem existente.

16. Verifique se o**local** do **BASICS** > está definido para o local de destino onde deseja que o IP público seja implantado.

17. Em **CONFIGURAÇÕES,** verifique se o nome corresponde ao nome que você inseriu anteriormente no editor de parâmetros.

18. Selecione a caixa de seleção **TERMOS E CONDIÇÕES.**

19. Selecione **Comprar** para implantar o IP público-alvo.

20. Se você tiver outro IP público que está sendo usado para o NAT de saída para o balanceador de carga que está sendo movido, repita as etapas anteriores para exportar e implantar o segundo IP público de saída para a região alvo.

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>Exporte o modelo de balanceador de carga externa e implante o balanceador de carga do portal Azure

1. Faça login no [portal Azure](https://portal.azure.com) e selecione **grupos de recursos**.
2. Localize o grupo de recursos que contém o balanceador de carga externo de origem e selecione-o.
3. Selecione**Modelo de exportação de** **configurações** > .
4. Selecione **Implantar** em **modelo Exportar**.
5. Selecione **TEMPLATE** > **Editar parâmetros** para abrir o arquivo parameters.json no editor on-line.

5. Para editar o parâmetro do nome do balanceador de carga externa, altere a propriedade de **valor** do nome do balanceador de carga externa de origem para o nome do balanceador de carga externo alvo. Feche o nome entre aspas.

    ```json
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
          "loadBalancers_myLoadbalancer_ext_name": {
          "value": "<target-external-lb-name>"
    },
          "publicIPAddresses_myPubIP_in_externalid": {
          "value": "<target-publicIP-resource-ID>"
    },

    ```

6.  Para editar o valor do IP público de destino que você moveu nas etapas anteriores, você deve primeiro obter o ID de recurso e, em seguida, colá-lo no arquivo parameters.json. Para obter o ID:

    1. Em outra guia ou janela do navegador, faça login no [portal DoZure](https://portal.azure.com) e selecione **grupos de recursos**.
    2. Localize o grupo de recursos de destino que contém o IP público que você moveu nas etapas anteriores. Selecione-o.
    3. Selecione **Propriedades de configurações** > **Properties**.
    4. Na lâmina à direita, destaque o **ID de recurso** e copie-o para a área de transferência. Alternativamente, você pode selecionar **cópia para área de transferência** à direita do caminho de **ID de recursos.**
    5. Cole o ID de recurso na propriedade **de valor** no editor **Editar parâmetros** que está aberto na outra janela ou guia do navegador:

        ```json
           ```json
           "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
           "contentVersion": "1.0.0.0",
           "parameters": {
              "loadBalancers_myLoadbalancer_ext_name": {
              "value": "<target-external-lb-name>"
        },
              "publicIPAddresses_myPubIP_in_externalid": {
              "value": "<target-publicIP-resource-ID>"
        },

        ```
    6. Selecione **Salvar** no editor on-line.


7.  Se você configurou as regras de saída do NAT e do desaída para o balanceador de carga, você verá uma terceira entrada neste arquivo para o ID externo do IP público de saída. Repita as etapas anteriores na **região alvo** para obter o ID para o IP público de saída. Cole esse ID no arquivo parameters.json:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "value": "<target-external-lb-name>",

            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "value": "<target-publicIP-resource-ID>",

            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",

            }
        },
    ```

8.  Selecione **MODELO** > **Editar modelo** para abrir o arquivo template.json no editor on-line.
9.  Para editar a região de destino para a qual a configuração do balanceador de carga externa será movida, altere a propriedade **de localização** em **recursos** no arquivo template.json:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

10. Para obter códigos de localização da região, consulte [os locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/). O código para uma região é o nome da região sem espaços. Por exemplo, o código para o Central US é **central.**

11. Você também pode alterar outros parâmetros no modelo se quiser ou precisar, dependendo dos seus requisitos:

    * **SKU**. Você pode alterar o SKU do balanceador de carga externa na configuração de padrão para básico ou de básico para padrão alterando a propriedade **de nome** **sku** no arquivo template.json:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Para obter informações sobre as diferenças entre balanceadores de carga SKU básicos e padrão, consulte [a visão geral do Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

    * **Regras de balanceamento de carga**. Você pode adicionar ou remover regras de balanceamento de carga na configuração adicionando ou removendo entradas na seção **loadBalanceingRules** do arquivo template.json:

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
       Para obter informações sobre as regras de balanceamento de carga, consulte [O que é o Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

    * **Sondas**. Você pode adicionar ou remover um teste para o balanceador de carga na configuração adicionando ou removendo **entradas** na seção de testes do arquivo template.json:

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
       Para obter mais informações, consulte [testes de saúde load balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

    * **Regras nat de entrada.** Você pode adicionar ou remover as regras NAT de entrada para o balanceador de carga adicionando ou removendo entradas na seção **de entradaNatRules** do arquivo template.json:

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
        Para completar a adição ou remoção de uma regra NAT de entrada, a regra deve estar presente ou removida como uma propriedade de **tipo** no final do arquivo template.json:

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
        Para obter informações sobre as regras de NAT de entrada, consulte [O que é o Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

    * **Regras de saída.** Você pode adicionar ou remover regras de saída na configuração editando a propriedade **outboundRules** no arquivo template.json:

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         Para obter mais informações, consulte [as regras de saída do Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).

12. Selecione **Salvar** no editor on-line.

13. Selecione**a assinatura** **BASICS** > para escolher a assinatura onde o balanceador de carga externo de destino será implantado.

15. Selecione o**grupo de recursos** **BASICS** > para escolher o grupo de recursos onde o balanceador de carga de destino será implantado. Você pode selecionar **Criar novo** para criar um novo grupo de recursos para o balanceador de carga externa alvo. Ou você pode escolher o grupo de recursos existente que você criou anteriormente para o IP público. Certifique-se de que o nome não é o mesmo que o grupo de recursos de origem do balanceador de carga externa de origem existente.

16. Verifique se o**local** do **BASICS** > está definido para o local de destino onde deseja que o balanceador de carga externo seja implantado.

17. Em **CONFIGURAÇÕES,** verifique se o nome corresponde ao nome inserido anteriormente no editor de parâmetros. Verifique se os IDs de recursos estão preenchidos para quaisquer IPs públicos na configuração.

18. Selecione a caixa de seleção **TERMOS E CONDIÇÕES.**

19. Selecione **Comprar** para implantar o IP público-alvo.

## <a name="discard"></a>Descartar

Se você quiser descartar o IP público de destino e o balanceador de carga externo, exclua o grupo de recursos que os contém. Para isso, selecione o grupo de recursos do painel no portal e **selecione Excluir** na parte superior da página de visão geral.

## <a name="clean-up"></a>Limpar

Para cometer as alterações e concluir a movimentação do IP público e do balanceador de carga externo, exclua o IP público de origem e o balanceador de carga externo ou grupo de recursos. Para isso, selecione esse grupo de recursos do seu painel no portal e **selecione Excluir** no topo de cada página.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu um balanceador de carga externa do Azure de uma região para outra e limpou os recursos de origem. Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres no Azure, consulte:


- [Mover recursos para um novo grupo de recursos ou assinatura](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Mover as VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
