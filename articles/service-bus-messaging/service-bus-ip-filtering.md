---
title: Configure regras de firewall IP para ônibus de serviço do Azure
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
ms.openlocfilehash: 24591c20ed707d9541eece0698ecd6e6b5ddee35
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878180"
---
# <a name="configure-ip-firewall-rules-for-azure-service-bus"></a>Configure regras de firewall IP para ônibus de serviço do Azure
Por padrão, os namespaces do Service Bus são acessíveis a partir da internet, desde que a solicitação venha com autenticação e autorização válidas. Com firewall IP, você pode restringi-lo ainda mais a apenas um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Esse recurso é útil em cenários nos quais o Azure Service Bus só deve ser acessível a partir de certos sites conhecidos. As regras do firewall permitem configurar regras para aceitar tráfego originário de endereços IPv4 específicos. Por exemplo, se você usar o Service Bus com [o Azure Express Route,][express-route]você poderá criar uma regra de **firewall** para permitir o tráfego apenas de seus endereços IP de infra-estrutura no local ou endereços de um gateway NAT corporativo. 

> [!IMPORTANT]
> Firewalls e Redes Virtuais são suportados apenas no nível **premium** do Service Bus. Se a atualização para o nível **premier** não for uma opção, recomendamos que você mantenha o token SAS (SAS) de Assinatura de Acesso Compartilhado seguro e compartilhe apenas com usuários autorizados. Para obter informações sobre a autenticação do SAS, consulte [Autenticação e autorização](service-bus-authentication-and-authorization.md#shared-access-signature).

## <a name="ip-firewall-rules"></a>Regras de firewall de IP
As regras de firewall IP são aplicadas no nível de namespace do Service Bus. Portanto, as regras se aplicam a todas as conexões de clientes que usam qualquer protocolo com suporte. Qualquer tentativa de conexão de um endereço IP que não corresponda a uma regra IP permitida no namespace do Barramento de Serviço será rejeitada como não autorizada. A resposta não menciona a regra IP. As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

## <a name="use-azure-portal"></a>Usar o portal do Azure
Esta seção mostra como usar o portal Azure para criar regras de firewall IP para um espaço de nome do Service Bus. 

1. Navegue até o espaço de nome do **Seu Ônibus de Serviço** no portal [Azure](https://portal.azure.com).
2. No menu à esquerda, **selecione Opção de rede.** Por padrão, a opção **Todas as redes** é selecionada. O namespace do bus de serviço aceita conexões de qualquer endereço IP. Essa configuração padrão é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0. 

    ![Firewall - Todas as opções de redes selecionadas](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Selecione a opção **Redes selecionadas** no topo da página. Na seção **Firewall,** siga estas etapas:
    1. Selecione Adicionar a opção **de endereço IP do cliente** para dar ao IP do cliente atual o acesso ao namespace. 
    2. Para **o intervalo de endereços,** digite um endereço IPv4 específico ou uma série de endereços IPv4 na notação CIDR. 
    3. Especifique se deseja **permitir que serviços confiáveis da Microsoft contornem esse firewall**. 

        ![Firewall - Todas as opções de redes selecionadas](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. Selecione **Salvar** na barra de ferramentas para salvar as configurações. Aguarde alguns minutos para que a confirmação apareça nas notificações do portal.

## <a name="use-resource-manager-template"></a>Usar modelo do Resource Manager
Esta seção tem um modelo de gerenciador de recursos do Azure de exemplo que cria uma rede virtual e uma regra de firewall.


O modelo do Resource Manager a seguir permite incluir uma regra da rede virtual em um namespace de Barramento de Serviço existente.

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

Para restringir o acesso a Barramento de Serviço para redes virtuais do Azure, consulte o link a seguir:

- [Pontos de extremidade de serviço de Rede Virtual para Barramento de Serviço][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
