---
title: Políticas de ponto de extremidade de serviço de rede virtual do Azure | Microsoft Docs
description: Aprenda a filtrar o tráfego de Rede Virtual para recursos de serviço do Azure usando políticas de Ponto de Extremidade de Serviço
services: virtual-network
documentationcenter: na
author: RDhillon
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: 926da07ffaf0c61ca2a7fd02351ef3635ec4d73b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651280"
---
# <a name="virtual-network-service-endpoint-policies-for-azure-storage"></a>Políticas de ponto final de serviço de rede virtual para armazenamento azure

As políticas de ponto final de serviço da Virtual Network (VNet) permitem filtrar o tráfego de rede virtual para contas de armazenamento do Azure no ponto final do serviço e permitir a exfiltração de dados apenas para contas específicas do Azure Storage. As políticas de ponto final fornecem controle de acesso granular para tráfego de rede virtual ao Azure Storage ao se conectar em ponto final de serviço.

![Protegendo o tráfego de saída de rede virtual para contas de armazenamento do Azure](./media/virtual-network-service-endpoint-policies-overview/vnet-service-endpoint-policies-overview.png)

Este recurso está geralmente disponível para __o Azure Storage__ em __todas as regiões globais do Azure__.

## <a name="key-benefits"></a>Principais benefícios

Políticas de ponto de extremidade de serviço de rede virtual fornecem os seguintes benefícios:

- __Segurança aprimorada para o tráfego da rede virtual para o armazenamento do Azure__

  [As tags de serviço do Azure para grupos de segurança](https://aka.ms/servicetags) de rede permitem restringir o tráfego de saída de rede virtual a regiões específicas do Armazenamento Azure. No entanto, isso permite que o tráfego para qualquer conta dentro da região selecionada do Azure Storage.
  
  As políticas de ponto final permitem que você especifique as contas de armazenamento do Azure que são permitidas acesso de saída de rede virtual e restringe o acesso a todas as outras contas de armazenamento. Isso dá muito mais controle de segurança granular para proteger a exfiltração de dados de sua rede virtual.

- __Políticas escalonáveis altamente disponíveis para filtrar o tráfego de serviço do Azure__

   Políticas de ponto de extremidade fornecem uma solução altamente disponível e escalonável horizontalmente para filtrar o tráfego de serviço do Azure de redes virtuais em pontos de extremidade de serviço. Nenhuma sobrecarga adicional é necessária para manter os dispositivos de rede central para esse tráfego em suas redes virtuais.

## <a name="json-object-for-service-endpoint-policies"></a>Objeto JSON para políticas de ponto final de serviço
Vamos dar uma olhada rápida no objeto Service Endpoint Policy.

```json
"serviceEndpointPolicyDefinitions": [
    {
            "description": null,
            "name": "MySEP-Definition",
            "resourceGroup": "MySEPDeployment",
            "service": "Microsoft.Storage",
            "serviceResources": [ 
                    "/subscriptions/subscriptionID/resourceGroups/MySEPDeployment/providers/Microsoft.Storage/storageAccounts/mystgacc"
            ],
            "type": "Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions"
    }
]
```

## <a name="configuration"></a>Configuração

-   Você pode configurar as políticas de ponto final para restringir o tráfego de rede virtual a contas específicas do Azure Storage.
-   A política de ponto de extremidade é configurada em uma sub-rede em uma rede virtual. Os pontos finais de serviço para armazenamento Azure devem ser habilitados na sub-rede para aplicar a diretiva.
-   A política de ponto final permite adicionar contas específicas do Azure Storage para permitir a lista, usando o formato resourceID. Você pode restringir o acesso a
    - todas as contas de armazenamento em uma assinatura<br>
      `E.g. /subscriptions/subscriptionId`

    - todas as contas de armazenamento em um grupo de recursos<br>
      `E.g. subscriptions/subscriptionId/resourceGroups/resourceGroupName`
     
    - uma conta de armazenamento individual, listando o recurso Azure Resource Manager correspondente. Isso abrange o tráfego para blobs, tabelas, filas, arquivos e armazenamento do Azure Data Lake Storage Gen2. <br>
    `E.g. /subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/storageAccountName`
-   Por padrão, se nenhuma política for anexada a uma sub-rede com pontos finais, você poderá acessar todas as contas de armazenamento no serviço. Depois que uma política é configurada na sub-rede, somente os recursos especificados na política poderão ser acessados de instâncias de computação naquela sub-rede. O acesso a todas as outras contas de armazenamento será negado.
-   Ao aplicar as políticas de ponto final de serviço em uma sub-rede, o escopo Azure Storage *Service Endpoint* é atualizado de regional para **global**. Isso significa que todo o tráfego para o Azure Storage é protegido sobre o ponto final do serviço depois disso. As políticas de ponto final do Serviço também são aplicáveis globalmente, de modo que quaisquer contas de armazenamento, que não são explicitamente permitidas, terão acesso negado. 
-   Você pode aplicar várias políticas a uma sub-rede. Quando várias políticas estiverem associadas à sub-rede, será permitido o tráfego de rede virtual para recursos especificados em qualquer uma dessas políticas. O acesso a todos os outros recursos de serviço não especificado em nenhuma das políticas será negado.

    > [!NOTE]  
    > As políticas de ponto final de **serviço são políticas permitidas,** portanto, além dos recursos especificados, todos os outros recursos são restritos. Certifique-se de que todas as dependências de recursos de serviço para seus aplicativos sejam identificadas e listadas na diretiva.

- Somente contas de armazenamento usando o Modelo de Recurso do Azure podem ser especificadas na política de ponto de extremidade. Suas contas clássicas do Azure Storage não suportarão políticas de ponto final do serviço do Azure.
- Acesso de RA-GRS secundário será permitido automaticamente se a conta primária estiver listada.
- Contas de armazenamento podem estar em uma assinatura ou Locatário do Azure Active Directory igual ou diferente daquele da rede virtual.

## <a name="scenarios"></a>Cenários

- **Redes virtuais emparelhadas, conectadas ou múltiplas**: para filtrar o tráfego em redes virtuais emparelhadas, as políticas de ponto de extremidade devem ser aplicadas individualmente a elas.
- **Filtrando o tráfego da Internet com os Aparelhos de Rede ou o Firewall Azure**: Filtre o tráfego de serviços do Azure com políticas, sobre pontos finais de serviço e filtre o resto do tráfego da Internet ou do Azure através de aparelhos ou firewall do Azure.
- **Filtrando o tráfego nos serviços do Azure implantados em redes virtuais**: Neste momento, as políticas de ponto final de serviço do Azure não são suportadas para quaisquer serviços Gerenciados do Azure que sejam implantados em sua rede virtual. 
- **Filtragem de tráfego para serviços do Azure do local**: políticas de ponto de extremidade de serviço se aplicam somente ao tráfego de sub-redes associadas a políticas. Para permitir o acesso a recursos de Serviço do Azure específicos do local, o tráfego deve ser filtrado usando firewalls ou dispositivos virtuais de rede ou.

## <a name="logging-and-troubleshooting"></a>Registro em log e solução de problemas
Nenhum registro em log centralizado está disponível para políticas de ponto de extremidade de serviço. Para logs de diagnóstico de serviço, veja [Registro em log de pontos de extremidade de serviço](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting).

### <a name="troubleshooting-scenarios"></a>Cenários de solução de problemas
- Acesso negado a contas de armazenamento que estavam trabalhando em pré-visualização (não em região geoemparelhada)
  - Com a atualização do Azure Storage para usar as Marcas de Serviço Globais, o escopo das políticas de ponto final de serviço e, portanto, de ponto final de serviço, agora é global. Assim, qualquer tráfego para o Azure Storage é criptografado sobre pontos finais de serviço e apenas contas de armazenamento que estão explicitamente listadas na diretiva têm acesso permitido.
  - Permitir explicitamente listar todas as contas de armazenamento necessárias para restaurar o acesso.  
  - Contate o suporte do Azure.
- O acesso é negado para contas listadas nas políticas de ponto de extremidade
  - Grupos de segurança de rede ou a filtragem de firewall podem estar bloqueando o acesso
  - Se remover/reaplicar a política resultar em perda de conectividade:
    - Valide se o serviço Azure está configurado para permitir o acesso da rede virtual sobre pontos finais ou se a diretiva padrão para o recurso está definida como *Permitir tudo*.
    - Valide que o diagnóstico de serviço mostra o tráfego nos pontos de extremidade.
    - Verifique se os logs de fluxo do grupo de segurança de rede mostram o acesso e se os logs de armazenamento mostram o acesso, conforme esperado, pelos pontos de extremidade de serviço.
    - Contate o suporte do Azure.
- O acesso é negado para contas não listadas nas políticas de ponto de extremidade de serviço
  - Valide se o Azure Storage está configurado para permitir o acesso da rede virtual sobre os pontos finais ou se a política padrão do recurso está definida como *Permitir tudo*.
  - Certifique-se de que as contas não são **contas de armazenamento clássicas** com políticas de ponto final de serviço na sub-rede.
- Um serviço azure gerenciado parou de funcionar depois de aplicar uma política de ponto final de serviço sobre a sub-rede
  - Os serviços gerenciados não são suportados com políticas de ponto final de serviço no momento. *Assista a este espaço para atualizações.*

## <a name="provisioning"></a>Provisionamento

Políticas de ponto de extremidade de serviço podem ser configuradas em sub-redes por um usuário com acesso de gravação a uma rede virtual. Saiba mais sobre [funções internas](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure e como atribuir permissões específicas a [funções personalizadas](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Redes virtuais e contas de armazenamento Azure podem estar nas mesmas assinaturas ou diferentes, ou inquilinos do Azure Active Directory.

## <a name="limitations"></a>Limitações

- Você só pode implantar políticas de ponto de extremidade de serviço em redes virtuais implantadas por meio do modelo de implantação do Azure Resource Manager.
- Redes virtuais devem estar na mesma região que a política de ponto de extremidade de serviço.
- Só será possível aplicar política de ponto de extremidade de serviço em uma sub-rede se os pontos de extremidade de serviço estiverem configurados para os serviços do Azure listados na política.
- Você não pode usar políticas de ponto de extremidade de serviço para o tráfego da sua rede local para serviços do Azure.
- Os serviços gerenciados do Azure não suportam atualmente as políticas de Endpoint. Isso inclui serviços gerenciados implantados nas sub-redes compartilhadas (por *exemplo, Azure HDInsight, Azure Batch, Azure ADDS, Azure APplication Gateway, Azure VPN gateway, Azure Firewall*) ou nas sub-redes dedicadas (por *exemplo, Azure App Service Environment, Azure Redis Cache, Azure API Management, Azure SQL MI, serviços gerenciados clássicos).*

 > [!WARNING]
 > Serviços do Azure implantados em sua rede virtual, como Azure HDInsight, acessam outros serviços do Azure, como Armazenamento do Azure, para requisitos de infraestrutura. Restringir a política de ponto de extremidade a recursos específicos pode interromper o acesso a esses recursos de infraestrutura para serviços do Azure implantados em sua rede virtual.

- Não há suporte para contas de armazenamento clássicas em políticas de ponto de extremidade. As políticas negarão o acesso a todas as contas de armazenamento clássicas por padrão. Se seu aplicativo precisar acessar o Azure Resource Manager e contas de armazenamento clássico, políticas de ponto de extremidade não deverão ser usadas para esse tráfego.

## <a name="pricing-and-limits"></a>Preços e limites

Não há custo adicional para usar políticas de ponto de extremidade de serviço. O modelo de preço atual para serviços do Azure (como o Armazenamento do Azure) se aplica é hoje, em pontos de extremidade de serviço.

Os limites a seguir são impostos em políticas de ponto de extremidade de serviço: 

 |Recurso | Limite padrão |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpintPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>Próximas etapas

- Saiba como [configurar políticas de ponto de extremidade de serviço de rede virtual](virtual-network-service-endpoint-policies-portal.md)
- Saiba mais sobre [os endpoints de serviços de rede virtual](virtual-network-service-endpoints-overview.md)
