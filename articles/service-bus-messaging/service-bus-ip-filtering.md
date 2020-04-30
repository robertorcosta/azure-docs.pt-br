---
title: Configurar regras de firewall IP para o barramento de serviço do Azure
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
ms.openlocfilehash: 9601689bbce9566b52664058911e9c45647152d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82116811"
---
# <a name="configure-ip-firewall-rules-for-azure-service-bus"></a>Configurar regras de firewall IP para o barramento de serviço do Azure
Por padrão, os namespaces do barramento de serviço podem ser acessados pela Internet, desde que a solicitação venha com autenticação e autorização válidas. Com o firewall de IP, você pode restringir ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (roteamento entre domínios sem classificação)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Esse recurso é útil em cenários nos quais o barramento de serviço do Azure deve ser acessível somente de determinados sites conhecidos. As regras de firewall permitem que você configure regras para aceitar o tráfego originado de endereços IPv4 específicos. Por exemplo, se você usar o barramento de serviço com o [Azure Express Route][express-route], poderá criar uma **regra de firewall** para permitir o tráfego somente de endereços IP de infraestrutura local ou endereços de um gateway de NAT corporativo. 

> [!IMPORTANT]
> Só há suporte para firewalls e redes virtuais na camada **Premium** do barramento de serviço. Se a atualização para a camada **Premier** não for uma opção, recomendamos que você mantenha o token de assinatura de acesso compartilhado (SAS) seguro e compartilhe com apenas usuários autorizados. Para obter informações sobre a autenticação SAS, consulte [autenticação e autorização](service-bus-authentication-and-authorization.md#shared-access-signature).

## <a name="ip-firewall-rules"></a>Regras de firewall de IP
As regras de firewall IP são aplicadas no nível de namespace do barramento de serviço. Portanto, as regras se aplicam a todas as conexões de clientes que usam qualquer protocolo com suporte. Qualquer tentativa de conexão de um endereço IP que não corresponda a uma regra IP permitida no namespace do Barramento de Serviço será rejeitada como não autorizada. A resposta não menciona a regra IP. As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

>[!WARNING]
> Implementar as regras de Firewall pode impedir outros serviços do Azure de interagir com o Barramento de Serviço.
>
> Não há suporte para serviços confiáveis da Microsoft para quando a Filtragem de IP (regras de Firewall) é implementada e serão disponibilizados em breve.
>
> Cenários comuns do Azure que não funcionam com a Filtragem de IP (observe que a lista **NÃO** é exaustiva):
> - Integração com a Grade de Eventos do Azure
> - Rotas do Hub IoT do Azure
> - Device Explorer do Azure IoT
>
> Os seguintes serviços da Microsoft devem estar em uma rede virtual
> - Serviço de aplicativo do Azure
> - Funções do Azure

## <a name="use-azure-portal"></a>Usar o portal do Azure
Esta seção mostra como usar o portal do Azure para criar regras de firewall de IP para um namespace do barramento de serviço. 

1. Navegue até o **namespace do barramento de serviço** na [portal do Azure](https://portal.azure.com).
2. No menu à esquerda, selecione a opção **rede** . Por padrão, a opção **todas as redes** é selecionada. O namespace do barramento de serviço aceita conexões de qualquer endereço IP. Essa configuração padrão é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0. 

    ![Firewall – opção todas as redes selecionada](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Selecione a opção **redes selecionadas** na parte superior da página. Na seção **Firewall** , siga estas etapas:
    1. Selecione a opção **Adicionar seu endereço IP do cliente** para fornecer ao IP do cliente atual o acesso ao namespace. 
    2. Para **intervalo de endereços**, insira um endereço IPv4 específico ou um intervalo de endereços IPv4 na notação CIDR. 
    3. Especifique se deseja **permitir que os serviços confiáveis da Microsoft ignorem esse firewall**. 

        ![Firewall – opção todas as redes selecionada](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. Selecione **salvar** na barra de ferramentas para salvar as configurações. Aguarde alguns minutos para que a confirmação seja exibida nas notificações do Portal.

## <a name="use-resource-manager-template"></a>Usar modelo do Resource Manager
Esta seção tem um modelo de Azure Resource Manager de exemplo que cria uma rede virtual e uma regra de firewall.


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
