---
title: Pontos de extremidade de serviço de Rede Virtual - Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre como adicionar um ponto final de serviço microsoft.EventHub a uma rede virtual.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 11/26/2019
ms.author: shvija
ms.openlocfilehash: 6de51c23bd6358a6f54fe3baf9e9b256047d4ab5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064886"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Usar pontos de extremidade de serviço de Rede Virtual com Hubs de Eventos do Azure

A integração de Hubs de Eventos com [Pontos de extremidade de serviço de VNet (rede virtual)][vnet-sep] permite acesso seguro a recursos de mensagens de cargas de trabalho, como máquinas virtuais associadas a redes virtuais, com o caminho de tráfego de rede sendo protegido em ambas as extremidades.

Uma vez configurado para limitar-se a pelo menos um ponto final de serviço de sub-rede de rede virtual, o respectivo namespace Event Hubs não aceita mais tráfego de qualquer lugar, mas de sub-redes autorizadas em redes virtuais. Da perspectiva da rede virtual, a associação de um namespace de Hubs de Eventos a um ponto de extremidade de serviço configura um túnel de rede isolado da sub-rede da rede virtual para o serviço de sistema de mensagens. 

O resultado é um relacionamento privado e isolado entre as cargas de trabalho associadas à sub-rede e o respectivo namespace de Hubs de Eventos, apesar do endereço de rede observável do ponto de extremidade de serviço de mensagens estar em um intervalo de IP público. Há uma exceção para esse comportamento. Habilitar um ponto final de serviço, por padrão, permite que a `denyall` regra no firewall [IP](event-hubs-ip-filtering.md) associado à rede virtual. Você pode adicionar endereços IP específicos no firewall IP para habilitar o acesso ao ponto final público do Event Hub. 

> [!IMPORTANT]
> As redes virtuais têm suporte nas camadas **standard** e **dedicada** dos Hubs de Eventos. Não é suportado no nível **básico.**

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários avançados de segurança habilitados pela integração de VNet 

Soluções que requerem segurança apertada e compartimentada, e onde as subredes de rede virtuais fornecem a segmentação entre os serviços compartimentados, ainda precisam de caminhos de comunicação entre os serviços que residem nesses compartimentos.

Qualquer rota IP imediata entre os compartimentos, incluindo aqueles que transportam HTTPS sobre TCP/IP, acarreta o risco de exploração de vulnerabilidades a partir da camada de rede. Os serviços de mensagens fornecem caminhos de comunicação isolados, onde as mensagens são escritas em disco à medida que transitam entre as partes. Cargas de trabalho em duas redes virtuais distintas que estão associadas à mesma instância de Hubs de Eventos podem comunicar-se de maneira eficiente e confiável por meio de mensagens, enquanto a integridade de limite de isolamento da respectiva rede é preservada.
 
Isso significa que as soluções na nuvem de segurança confidencial não apenas obtêm acesso aos recursos de mensagens assíncronas confiáveis e escalonáveis líderes do mercado do Azure, mas agora podem usar o sistema de mensagens para criar caminhos de comunicação entre compartimentos de solução protegidos que são inerentemente mais seguros que o modo de comunicação ponto a ponto, incluindo HTTPS e outros protocolos de socket protegidos por TLS.

## <a name="bind-event-hubs-to-virtual-networks"></a>Vincular hubs de eventos a redes virtuais

As **regras da rede virtual** são o recurso de segurança do firewall que controla se o namespace de Hubs de Eventos do Azure aceita conexões de uma sub-rede de rede virtual específica.

Associar um namespace de Hubs de Eventos a uma rede virtual é um processo de duas etapas. Primeiro, você precisa criar um ponto final de **serviço de rede virtual** na sub-rede de uma rede virtual e habilitá-lo para o **Microsoft.EventHub,** conforme explicado no artigo de visão geral do [ponto final do serviço.][vnet-sep] Após adicionar o ponto de extremidade de serviço, você associa o namespace de Hubs de Eventos ao ponto de extremidade com uma **regra da rede virtual**.

A regra da rede virtual é uma associação do namespace de Hubs de Eventos com uma sub-rede de rede virtual. Embora a regra exista, todas as cargas de trabalho associadas à sub-rede recebem acesso ao namespace de Hubs de Eventos. O Event Hubs em si nunca estabelece conexões de saída, não precisa ter acesso e, portanto, nunca é concedido acesso à sua sub-rede, permitindo essa regra.

## <a name="use-azure-portal"></a>Usar o portal do Azure
Esta seção mostra como usar o portal Azure para adicionar um ponto final de serviço de rede virtual. Para limitar o acesso, você precisa integrar o ponto final do serviço de rede virtual para este namespace do Event Hubs.

1. Navegue até o **seu espaço de nome do Event Hubs** no portal [Azure](https://portal.azure.com).
2. No menu à esquerda, **selecione Opção de rede.** Se você selecionar a opção **Todas as redes,** o hub de eventos aceitará conexões a partir de qualquer endereço IP. Essa configuração é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0. 

    ![Firewall - Todas as opções de redes selecionadas](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Para restrct acessar redes específicas, selecione a opção **Redes Selecionadas** na parte superior da página.
2. Na seção **Rede Virtual** da página, selecione **+Adicionar rede virtual existente***. Selecione **+ Crie uma nova rede virtual** se quiser criar um novo VNet. 

    ![adicionar rede virtual existente](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)
3. Selecione a rede virtual na lista de redes virtuais e escolha a **sub-rede**. Você tem que ativar o ponto final do serviço antes de adicionar a rede virtual à lista. Se o ponto final do serviço não estiver ativado, o portal solicitará que você o habilite.
   
   ![selecionar sub-rede](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. Você deve ver a seguinte mensagem de sucesso após o ponto final do serviço para a sub-rede ser ativado para **Microsoft.EventHub**. Selecione **Adicionar** na parte inferior da página para adicionar a rede. 

    ![selecionar a sub-rede e habilitar o ponto de extremidade](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Se você não conseguir ativar o ponto final do serviço, você pode ignorar o ponto final do serviço de rede virtual ausente usando o modelo Gerenciador de recursos. Essa funcionalidade não está disponível no portal.
6. Selecione **Salvar** na barra de ferramentas para salvar as configurações. Aguarde alguns minutos para que a confirmação apareça nas notificações do portal.

    ![Salvar rede](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)


## <a name="use-resource-manager-template"></a>Usar modelo do Resource Manager

O modelo do Resource Manager a seguir permite incluir uma regra da rede virtual em um namespace de Hubs de Eventos existente.

Parâmetros de modelo:

* **namespaceName**: namespace de Hubs de Eventos.
* **vnetRuleName**: nome da regra da Rede Virtual a ser criada.
* **virtualNetworkingSubnetId**: caminho do Resource Manager totalmente qualificado para a sub-rede da rede virtual, por exemplo, `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` para a sub-rede padrão de uma rede virtual.

> [!NOTE]
> Embora não haja nenhuma regra de negação possível, o modelo do Azure Resource Manager tem a ação padrão definida como **"Allow"**, que não restringe as conexões.
> Ao criar as regras de rede virtual ou de firewalls, devemos alterar a ***"defaultAction"***
> 
> de
> ```json
> "defaultAction": "Allow"
> ```
> para
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.EventHub"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Para implantar o modelo, siga as instruções para o [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre redes virtuais, consulte os links a seguir:

- [Pontos finais de serviço de rede virtual do Azure][vnet-sep]
- [Filtragem IP de Hubs de Eventos do Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
