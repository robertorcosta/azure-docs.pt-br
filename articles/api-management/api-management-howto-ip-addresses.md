---
title: Endereços IP do serviço de gerenciamento de API do Azure | Microsoft Docs
description: Saiba como recuperar os endereços IP de um serviço de gerenciamento de API do Azure e quando eles mudam.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 45501fee9ae6ff47643a1ed197a07c4ba598e981
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047738"
---
# <a name="ip-addresses-of-azure-api-management"></a>Endereços IP do Gerenciamento de API do Azure

Neste artigo descrevemos como recuperar os endereços IP do serviço de gerenciamento de API do Azure. Os endereços IP podem ser públicos ou privados se o serviço estiver em uma rede virtual.

Você pode usar endereços IP para criar regras de firewall, filtrar o tráfego de entrada para os serviços de back-end ou restringir o tráfego de saída.

## <a name="ip-addresses-of-api-management-service"></a>Endereços IP do serviço de Gerenciamento de API

Todas as instâncias de serviço de Gerenciamento de API no nível Desenvolvedor, Básico, Padrão ou Premium possuem endereços IP públicos, que são exclusivos apenas para essa instância de serviço (eles não são compartilhados com outros recursos). 

Você pode recuperar os endereços IP do painel de visão geral do seu recurso no portal Azure.

![Endereço IP de gerenciamento de API](media/api-management-howto-ip-addresses/public-ip.png)

Você também pode buscá-los programáticamente com a seguinte chamada de API:

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

Os endereços IP públicos serão parte da resposta:

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

Em [implantações multi-regionais,](api-management-howto-deploy-multi-region.md)cada implantação regional tem um endereço IP público.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>Endereços IP do serviço de Gerenciamento de API no VNet

Se o seu serviço de Gerenciamento de API estiver dentro de uma rede virtual, ele terá dois tipos de endereços IP - público e privado.

Os endereços IP públicos são `3443` usados para comunicação interna na porta - para gerenciar a configuração (por exemplo, através do Azure Resource Manager). Na configuração externa do VNet, eles também são usados para tráfego de API em tempo de execução. Quando uma solicitação é enviada do Gerenciamento de API para um backend voltado para o público (voltado para a Internet), um endereço IP público será visível como a origem da solicitação.

Os endereços VIP virtuais privados , disponíveis **apenas** no [modo VNet interno,](api-management-using-with-internal-vnet.md)são usados para se conectar de dentro da rede a pontos finais de Gerenciamento de API - gateways, portal de desenvolvedores e plano de gerenciamento para acesso direto à API. Você pode usá-los para configurar registros DNS dentro da rede.

Você verá endereços de ambos os tipos no portal Azure e na resposta da chamada da API:

![Gerenciamento de API no endereço IP do VNet](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

O Gerenciamento de API usa um endereço IP público para conexões fora do VNet e um endereço IP privado para conexões dentro do VNet.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>Endereços IP do serviço de gerenciamento de API de nível de consumo

Se o serviço de gerenciamento de API for um serviço de nível de consumo, ele não terá um endereço IP dedicado. O serviço de nível de consumo é executado em uma infra-estrutura compartilhada e sem um endereço IP determinista. 

Para fins de restrição de tráfego, você pode usar a gama de endereços IP dos data centers do Azure. Consulte [o artigo de documentação funções do Azure](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses) para obter etapas precisas.

## <a name="changes-to-the-ip-addresses"></a>Alterações nos endereços IP

Nos níveis De Desenvolvedor, Básico, Padrão e Premium de Gerenciamento de API, os endereços IP públicos (VIP) são estáticos durante a vida útil de um serviço, com as seguintes exceções:

* O serviço é excluído e recriado.
* A assinatura do serviço é [suspensa](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) ou [avisada](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (por exemplo, por não pagamento) e, depois, reintegrada.
* A Rede Virtual Do Azure é adicionada ou removida do serviço.
* O serviço de gerenciamento de API é alternado entre o modo de implantação do VNet externo e interno.

Em [implantações multi-regionais,](api-management-howto-deploy-multi-region.md)o endereço IP regional muda se uma região for desocupada e, em seguida, reintegrada.
