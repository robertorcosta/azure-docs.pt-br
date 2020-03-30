---
title: Hospedagem de vários sites no Gateway de Aplicativo do Azure
description: Este artigo fornece uma visão geral do suporte a vários sites do Gateway de Aplicativo do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 03/11/2020
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 4d945a255dacd35c61c3c80574b7d46b56de4aab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257403"
---
# <a name="application-gateway-multiple-site-hosting"></a>Hospedagem de vários sites do Gateway de Aplicativo

A hospedagem de vários sites permite configurar mais de um aplicativo web na mesma porta de um gateway de aplicativo. Esse recurso permite que você configure a topologia mais eficiente para suas implantações adicionando até 100 sites a um Gateway de Aplicativo. Cada site pode ser direcionado para seu próprio pool de back-end. No exemplo a seguir, o `contoso.com` `fabrikam.com` gateway de aplicativo serve tráfego para e a partir de dois pools de servidores back-end chamados ContosoServerPool e FabrikamServerPool.

![imageURLroute](./media/multiple-site-overview/multisite.png)

> [!IMPORTANT]
> As regras são processadas na ordem em que estão listadas no portal para o V1 SKU. Para o v2 SKU, as correspondências exatas têm maior precedência. É altamente recomendável configurar primeiro os ouvintes de vários locais para configurar um ouvinte básico.  Isso irá garantir que o tráfego seja roteado para o back-end correto. Se um ouvinte básico for listado primeiro e corresponder a uma solicitação de entrada, ele é processado por esse ouvinte.

As solicitações de `http://contoso.com` são encaminhadas para ContosoServerPool, e as de `http://fabrikam.com` são encaminhadas para FabrikamServerPool.

Da mesma forma, você pode hospedar vários subdomínios do mesmo domínio pai na mesma implantação de gateway de aplicativo. Por exemplo, você `http://blog.contoso.com` `http://app.contoso.com` pode hospedar e em uma única implantação de gateway de aplicativo.

## <a name="host-headers-and-server-name-indication-sni"></a>Cabeçalhos de host e SNI (Indicação de Nome de Servidor)

Existem três mecanismos comuns para habilitar a hospedagem de vários sites na mesma infraestrutura.

1. Hospede vários aplicativos Web, cada um em um endereço IP exclusivo.
2. Use o nome de host para hospedar vários aplicativos Web no mesmo endereço IP.
3. Use portas diferentes para hospedar vários aplicativos Web no mesmo endereço IP.

Atualmente, o Application Gateway suporta um único endereço IP público onde ele ouve o tráfego. Assim, vários aplicativos, cada um com seu próprio endereço IP, não é suportado no momento. 

O Application Gateway suporta vários aplicativos que ouvem em diferentes portas, mas esse cenário exige que os aplicativos aceitem tráfego em portas não padronizadas. Esta muitas vezes não é uma configuração que você deseja.

O Gateway de Aplicativo depende de cabeçalhos de host HTTP 1.1 para hospedar mais de um site na mesma porta e endereço IP público. Os sites hospedados no gateway de aplicativo também podem suportar a descarga TLS com a extensão SNIS (Server Name Indication, indicação de nome do servidor). Esse cenário significa que o farm da Web de back-end e o navegador cliente devem dar suporte à extensão TLS e HTTP/1.1 conforme definido no RFC 6066.

## <a name="listener-configuration-element"></a>Elemento de configuração do ouvinte

Os elementos de configuração HTTPListener existentes são aprimorados para suportar os elementos de indicação de nome do host e do servidor. É usado pelo Application Gateway para direcionar o tráfego para o pool de backend apropriado. 

O exemplo de código a seguir é o trecho de um elemento HttpListeners de um arquivo de modelo:

```json
"httpListeners": [
    {
        "name": "appGatewayHttpsListener1",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
            },
            "Protocol": "Https",
            "SslCertificate": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
            },
            "HostName": "contoso.com",
            "RequireServerNameIndication": "true"
        }
    },
    {
        "name": "appGatewayHttpListener2",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
            },
            "Protocol": "Http",
            "HostName": "fabrikam.com",
            "RequireServerNameIndication": "false"
        }
    }
],
```

Você pode visitar [Modelo do Resource Manager usando a hospedagem de vários sites](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) para encontrar uma implantação baseada em modelo de ponta a ponta.

## <a name="routing-rule"></a>Regra de roteamento

Não há nenhuma mudança necessária na regra de roteamento. A regra de roteamento 'Básica' deve continuar a ser escolhido para ligar o ouvinte do site apropriado ao pool de endereços de back-end correspondente.

```json
"requestRoutingRules": [
{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

},
{
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}
]
```

## <a name="next-steps"></a>Próximas etapas

Depois de conhecer várias opções de hospedagem de site, vá para [criar um Application Gateway usando a hospedagem de vários sites](tutorial-multiple-sites-powershell.md) para criar um Application Gateway com capacidade de dar suporte a mais de um aplicativo Web.

