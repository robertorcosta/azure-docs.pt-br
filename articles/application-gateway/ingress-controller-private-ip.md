---
title: Use o endereço IP privado para roteamento interno para um ponto final de ingestão
description: Este artigo fornece informações sobre como usar IPs privados para roteamento interno e, assim, expor o ponto final de Ingress dentro de um cluster para o resto do VNet.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 570f28ce559ff1c1180ffaacb781b9120b1890a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795485"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>Use IP privado para roteamento interno para um ponto final da Ingress 

Esse recurso permite expor o ponto final `Virtual Network` de ingestão dentro do uso de um IP privado.

## <a name="pre-requisites"></a>Pré-requisitos  
Gateway de aplicativo com uma [configuração DE Privada](https://docs.microsoft.com/azure/application-gateway/configure-application-gateway-with-private-frontend-ip)

Existem duas maneiras de configurar o controlador para usar ip privado para entrada,

## <a name="assign-to-a-particular-ingress"></a>Atribuir a uma inserção específica
Para expor uma ingestão específica sobre IP privado, use anotação [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) em Ingress.

### <a name="usage"></a>Uso
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

Para gateways de aplicativos sem um IP privado, ingressas anotadas serão `appgw.ingress.kubernetes.io/use-private-ip: "true"` ignoradas. Isso será indicado no evento de entrada e no registro de pods AGIC.

* Erro como indicado no Evento de Ingress

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* Erro como indicado nos registros AGIC

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>Atribuir globalmente
No caso, o requisito é restringir todas as Ingresses `appgw.usePrivateIP: true` `helm` a serem expostas sobre IP privado, use em config.

### <a name="usage"></a>Uso
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

Isso fará com que o controlador de entrada filtre as configurações do endereço IP para um IP privado ao configurar os ouvintes frontend no Gateway de aplicativo.
AGIC entrará em `usePrivateIP: true` pânico e falhará se e nenhum IP privado for atribuído.

> [!NOTE]
> O Application Gateway v2 SKU requer um IP público. Se você precisar que o Application [`Network Security Group`](https://docs.microsoft.com/azure/virtual-network/security-overview) Gateway seja privado, anexe um na sub-rede do Gateway de aplicativo para restringir o tráfego.
