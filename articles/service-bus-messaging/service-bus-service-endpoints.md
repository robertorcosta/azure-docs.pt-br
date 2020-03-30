---
title: Configure pontos finais de serviço de rede virtual para ônibus de serviço do Azure
description: Este artigo fornece informações sobre como adicionar um ponto final de serviço Microsoft.ServiceBus a uma rede virtual.
services: service-bus
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 9dbf65522d5c85e1054ed3f1f6ca9f86180e7f7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454974"
---
# <a name="configure-virtual-network-service-endpoints-for-azure-service-bus"></a>Configure pontos finais de serviço de rede virtual para ônibus de serviço do Azure

A integração dos pontos finais de serviço service bus com [virtual network (VNet)][vnet-sep] permite acesso seguro aos recursos de mensagens a partir de cargas de trabalho como máquinas virtuais vinculadas a redes virtuais, com o caminho de tráfego de rede sendo protegido em ambas as extremidades.

Após ser configurado para associar-se a pelo menos um ponto de extremidade de serviço de sub-rede da rede virtual, o respectivo namespace de Barramento de Serviço não aceitará mais tráfego de nenhum lugar, exceto das redes virtuais autorizadas. Da perspectiva da rede virtual, a associação de um namespace de Barramento de Serviço a um ponto de extremidade de serviço configura um túnel de rede isolado da sub-rede da rede virtual para o serviço de sistema de mensagens.

O resultado é um relacionamento privado e isolado entre as cargas de trabalho associadas à sub-rede e o respectivo namespace de Barramento de Serviço, apesar do endereço de rede observável do ponto de extremidade de serviço de mensagens estar em um intervalo de IP público.

> [!IMPORTANT]
> As redes virtuais têm suporte apenas em namespaces do Barramento de Serviço [camada Premium](service-bus-premium-messaging.md).
> 
> Ao usar os pontos finais de serviço da VNet com o Service Bus, você não deve habilitar esses pontos finais em aplicativos que misturam os namespaces de barramento de serviço padrão e premium. Porque o nível Standard não suporta VNets. O ponto final é restrito apenas aos namespaces de nível Premium.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários avançados de segurança habilitados pela integração de VNet 

Soluções que exigem segurança compartimentada e restrita, e onde as sub-redes da rede virtual fornecem a segmentação entre os serviços compartimentados, geralmente ainda precisam de caminhos de comunicação entre os serviços que residem nesses compartimentos.

Qualquer rota IP imediata entre os compartimentos, incluindo aqueles que transportam HTTPS sobre TCP/IP, acarreta o risco de exploração de vulnerabilidades a partir da camada de rede. Os serviços de mensagens fornecem caminhos de comunicação completamente isolados, onde as mensagens são gravadas no disco na medida em que fazem a transição entre as partes. Cargas de trabalho em duas redes virtuais distintas que estão associadas à mesma instância de Barramento de Serviço podem comunicar-se de maneira eficiente e confiável por meio de mensagens, enquanto a integridade de limite de isolamento da respectiva rede é preservada.
 
Isso significa que as soluções na nuvem de segurança confidencial não apenas obtêm acesso aos recursos de mensagens assíncronas confiáveis e escalonáveis líderes do mercado do Azure, mas agora podem usar o sistema de mensagens para criar caminhos de comunicação entre compartimentos de solução protegidos que são inerentemente mais seguros que o modo de comunicação ponto a ponto, incluindo HTTPS e outros protocolos de socket protegidos por TLS.

## <a name="binding-service-bus-to-virtual-networks"></a>Associar Barramento de Serviço a Redes Virtuais

As *regras da rede virtual* são o recurso de segurança do firewall que controla se o servidor de Barramento de Serviço do Azure aceita conexões de uma sub-rede de rede virtual específica.

Associar um namespace de Barramento de Serviço a uma rede virtual é um processo de duas etapas. Primeiro, você precisa criar um **ponto final de serviço de rede virtual** em uma sub-rede de rede virtual e habilitá-lo para **microsoft.ServiceBus,** conforme explicado na visão geral do [ponto final do serviço][vnet-sep]. Após adicionar o ponto de extremidade de serviço, você associa o namespace de Barramento de Serviço ao ponto de extremidade com uma **regra da rede virtual**.

A regra da rede virtual é uma associação do namespace de Barramento de Serviço com uma sub-rede de rede virtual. Embora a regra exista, todas as cargas de trabalho associadas à sub-rede recebem acesso ao namespace de Barramento de Serviço. Os próprios Barramentos de Serviço nunca estabelecem conexões de saída, não precisam obter acesso e, portanto, nunca recebem acesso à sub-rede habilitando essa regra.

## <a name="use-azure-portal"></a>Usar o portal do Azure
Esta seção mostra como usar o portal Azure para adicionar um ponto final de serviço de rede virtual. Para limitar o acesso, você precisa integrar o ponto final do serviço de rede virtual para este namespace do Event Hubs.

1. Navegue até o espaço de nome do **Seu Ônibus de Serviço** no portal [Azure](https://portal.azure.com).
2. No menu à esquerda, **selecione Opção de rede.** Por padrão, a opção **Todas as redes** é selecionada. Seu namespace aceita conexões de qualquer endereço IP. Essa configuração padrão é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0. 

    ![Firewall - Todas as opções de redes selecionadas](./media/service-endpoints/firewall-all-networks-selected.png)
1. Selecione a opção **Redes selecionadas** no topo da página.
2. Na seção **Rede Virtual** da página, selecione **+Adicionar rede virtual existente**. 

    ![adicionar rede virtual existente](./media/service-endpoints/add-vnet-menu.png)
3. Selecione a rede virtual na lista de redes virtuais e escolha a **sub-rede**. Você tem que ativar o ponto final do serviço antes de adicionar a rede virtual à lista. Se o ponto final do serviço não estiver ativado, o portal solicitará que você o habilite.
   
   ![selecionar sub-rede](./media/service-endpoints/select-subnet.png)

4. Você deve ver a seguinte mensagem de sucesso após o ponto final do serviço para a sub-rede ser ativado para **Microsoft.ServiceBus**. Selecione **Adicionar** na parte inferior da página para adicionar a rede. 

    ![selecionar a sub-rede e habilitar o ponto de extremidade](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Se você não conseguir ativar o ponto final do serviço, você pode ignorar o ponto final do serviço de rede virtual ausente usando o modelo Gerenciador de recursos. Essa funcionalidade não está disponível no portal.
6. Selecione **Salvar** na barra de ferramentas para salvar as configurações. Aguarde alguns minutos para que a confirmação apareça nas notificações do portal. O botão **Salvar** deve ser desativado. 

    ![Salvar rede](./media/service-endpoints/save-vnet.png)

## <a name="use-resource-manager-template"></a>Usar modelo do Resource Manager
O modelo do Resource Manager a seguir permite incluir uma regra da rede virtual em um namespace de Barramento de Serviço existente.

Parâmetros de modelo:

* **namespaceName**: namespace de Barramento de serviço.
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

Modelo:

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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
                    "service": "Microsoft.ServiceBus"
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
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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
- [Filtragem IP do Barramento de Serviço do Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
