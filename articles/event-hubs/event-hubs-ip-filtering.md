---
title: Regras de firewall dos Hubs de Eventos do Azure| Microsoft Docs
description: Use as Regras de firewall para permitir conexões de endereços IP específicos com os Hubs de Eventos do Azure.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: e0116c1cfe61b49f2d5aff46fab9cadc0e423ecc
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310168"
---
# <a name="configure-ip-firewall-rules-for-an-azure-event-hubs-namespace"></a>Configure regras de firewall IP para um namespace do Azure Event Hubs
Por padrão, os namespaces do Event Hubs são acessíveis a partir da internet, desde que a solicitação venha com autenticação e autorização válidas. Com firewall IP, você pode restringi-lo ainda mais a apenas um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Esse recurso é útil em cenários nos quais o Azure Event Hubs só deve ser acessível a partir de certos sites conhecidos. As regras do firewall permitem configurar regras para aceitar tráfego originário de endereços IPv4 específicos. Por exemplo, se você usar hubs de eventos com [o Azure Express Route,][express-route]você poderá criar uma regra de **firewall** para permitir o tráfego apenas de seus endereços IP de infra-estrutura no local. 

>[!WARNING]
> A habilitação da filtragem ip pode impedir que outros serviços do Azure interajam com hubs de eventos.
>
> Não há suporte para serviços confiáveis da Microsoft quando as Redes Virtuais são implementadas.
>
> Cenários comuns do Azure que não funcionam com Redes Virtuais (observe que a lista **NÃO** é exaustiva):
> - Stream Analytics do Azure
> - Integração com a Grade de Eventos do Azure
> - Rotas do Hub IoT do Azure
> - Device Explorer do Azure IoT
>
> Os seguintes serviços da Microsoft são necessários para estar em uma rede virtual
> - Aplicativos Web do Azure
> - Funções do Azure


## <a name="ip-firewall-rules"></a>Regras de firewall de IP
As regras de firewall IP são aplicadas no nível de namespace do Event Hubs. Portanto, as regras se aplicam a todas as conexões de clientes que usam qualquer protocolo com suporte. Qualquer tentativa de conexão de um endereço IP que não corresponda a uma regra IP permitida no namespace dos Hubs de Eventos será rejeitada como não autorizada. A resposta não menciona a regra IP. As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

## <a name="use-azure-portal"></a>Usar o portal do Azure
Esta seção mostra como usar o portal Azure para criar regras de firewall IP para um namespace do Event Hubs. 

1. Navegue até o **seu espaço de nome do Event Hubs** no portal [Azure](https://portal.azure.com).
2. No menu à esquerda, **selecione Opção de rede.** Se você selecionar a opção **Todas as redes,** o hub de eventos aceitará conexões a partir de qualquer endereço IP. Essa configuração é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0. 

    ![Firewall - Todas as opções de redes selecionadas](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Para restringir o acesso a redes específicas e endereços IP, selecione a opção **Redes Selecionadas.** Na seção **Firewall,** siga estas etapas:
    1. Selecione Adicionar a opção **de endereço IP do cliente** para dar ao IP do cliente atual o acesso ao namespace. 
    2. Para **o intervalo de endereços,** digite um endereço IPv4 específico ou uma série de endereços IPv4 na notação CIDR. 
    3. Especifique se deseja **permitir que serviços confiáveis da Microsoft contornem esse firewall**. 

        ![Firewall - Todas as opções de redes selecionadas](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. Selecione **Salvar** na barra de ferramentas para salvar as configurações. Aguarde alguns minutos para que a confirmação apareça nas notificações do portal.


## <a name="use-resource-manager-template"></a>Usar modelo do Resource Manager

> [!IMPORTANT]
> As regras de firewall têm suporte nas camadas **Standard** e **Dedicada** dos Hubs de Eventos. Não tem suporte na camada básica.

O modelo do Resource Manager a seguir permite adicionar uma regra de filtro IP a um namespace de Hubs de Eventos existente.

Parâmetros de modelo:

- A **ipMask** é um endereço IPv4 único ou um bloco de endereços IP na notação CIDR. Por exemplo, na notação CIDR 70.37.104.0/24, representa os 256 endereços IPv4 de 70.37.104.0 a 70.37.104.255, em que 24 indica o número de bits de prefixo significativos para o intervalo.

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
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
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
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
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

Para restringir o acesso a Hubs de Eventos para redes virtuais do Azure, consulte o link a seguir:

- [Pontos de extremidade de serviço de rede virtual para Hubs de Eventos][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
