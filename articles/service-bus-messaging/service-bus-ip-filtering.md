---
title: Regras de Firewall de Barramento de Serviço do Azure | Microsoft Docs
description: Como usar Regras de Firewall para permitir conexões de endereços IP específicos para o Barramento de Serviço do Azure.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 59afdb0e273511f3d8255a9c859b86f93e0b7269
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462416"
---
# <a name="azure-service-bus---use-firewall-rules"></a>Barramento de serviço do Azure-usar regras de firewall

Para cenários em que os Barramento de Serviço do Azure são acessíveis apenas de determinados sites bem conhecidos, as regras de Firewall permitem que você configure regras para aceitar tráfego originado de endereços IPv4 específicos. Por exemplo, esses endereços podem ser aqueles de um gateway NAT corporativo.

## <a name="when-to-use"></a>Quando usar

Se você estiver buscando instalar o Barramento de Serviço que deve receber tráfego apenas de um intervalo específico de endereços IP e rejeitar todo o resto, você poderá aproveitar um *Firewall* para bloquear os pontos de extremidade do Barramento de Serviço de outros endereços IP. Por exemplo, você está usando o barramento de serviço com o [Azure Express Route][express-route] para criar conexões privadas com sua infraestrutura local. 

## <a name="how-filter-rules-are-applied"></a>Como são aplicadas as regras de filtro

As regras de filtro IP são aplicadas no nível de namespace do Barramento de Serviço. Portanto, as regras se aplicam a todas as conexões de clientes que usam qualquer protocolo com suporte.

Qualquer tentativa de conexão de um endereço IP que não corresponda a uma regra IP permitida no namespace do Barramento de Serviço será rejeitada como não autorizada. A resposta não menciona a regra IP.

## <a name="default-setting"></a>Configuração padrão

Por padrão, a grade **Filtro IP** o portal do Barramento de Serviço é vazia. Essa configuração padrão significa que o namespace aceita conexões com qualquer endereço IP. Essa configuração padrão é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Avaliação da regra de filtro IP

As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

>[!WARNING]
> Implementar as regras de Firewall pode impedir outros serviços do Azure de interagir com o Barramento de Serviço.
>
> Não há suporte para serviços confiáveis da Microsoft para quando a Filtragem de IP (regras de Firewall) é implementada e serão disponibilizados em breve.
>
> Cenários comuns do Azure que não funcionam com a Filtragem de IP (observe que a lista **NÃO** é exaustiva):
> - Azure Stream Analytics
> - Integração com a Grade de Eventos do Azure
> - Rotas do Hub IoT do Azure
> - Device Explorer do Azure IoT
>
> Os serviços da Microsoft abaixo devem estar em uma rede virtual
> - Serviço de Aplicativos do Azure
> - Funções do Azure

### <a name="creating-a-virtual-network-and-firewall-rule-with-azure-resource-manager-templates"></a>Criar uma regra de rede virtual e firewall com modelos do Azure Resource Manager

> [!IMPORTANT]
> Só há suporte para firewalls e redes virtuais na camada **Premium** do barramento de serviço.

O modelo do Resource Manager a seguir permite incluir uma regra da rede virtual em um namespace de Barramento de Serviço existente.

Parâmetros de modelo:

- A **ipMask** é um endereço IPv4 único ou um bloco de endereços IP na notação CIDR. Por exemplo, na notação CIDR 70.37.104.0/24, representa os 256 endereços IPv4 de 70.37.104.0 a 70.37.104.255, em que 24 indica o número de bits de prefixo significativos para o intervalo.

> [!NOTE]
> Embora não haja nenhuma regra de negação possível, o modelo do Azure Resource Manager tem a ação padrão definida como **"Allow"** , que não restringe as conexões.
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
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Para implantar o modelo, siga as instruções para [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Próximos passos

Para restringir o acesso a Barramento de Serviço para redes virtuais do Azure, consulte o link a seguir:

- [Pontos de extremidade de serviço de rede virtual para o barramento de serviço][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
