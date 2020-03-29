---
title: Safelist os URLs do portal Azure em seu firewall ou servidor proxy
description: Adicione esses URLs ao bypass do servidor proxy para se comunicar com o portal Azure e seus serviços
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5bba7296a05cfbb72698a991ece1ef298689bd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900665"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Safelist os URLs do portal Azure em seu firewall ou servidor proxy

Você pode configurar dispositivos de segurança no local para contornar as restrições de segurança para os URLs do portal Azure. Essa configuração pode melhorar o desempenho e a conectividade entre sua rede local ou ampla e a nuvem Azure.

Os administradores de rede geralmente implantam servidores proxy, firewalls ou outros dispositivos. Esses dispositivos ajudam a proteger e dar controle sobre como os usuários acessam a internet. Regras projetadas para proteger os usuários podem, por vezes, bloquear ou retardar o tráfego legítimo da Internet relacionado aos negócios. Este tráfego inclui comunicações entre você e o Azure. Para otimizar a conectividade entre sua rede e o portal Azure e seus serviços, recomendamos que você adicione URLs do portal Azure à sua lista de segurança.

## <a name="azure-portal-urls-for-proxy-bypass"></a>URLs do portal Azure para bypass proxy

Os pontos finais da URL para safelist para o portal Azure são específicos para a nuvem Dozure onde sua organização está implantada. Para permitir que o tráfego de rede para esses pontos finais contorne as restrições, selecione sua nuvem. Em seguida, adicione a lista de URLs ao seu servidor proxy ou firewall.

#### <a name="public-cloud"></a>[Nuvem Pública](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azureafd.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloud"></a>[Nuvem do governo dos EUA](#tab/us-government-cloud)

```
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[Nuvem do governo da China](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> O tráfego para esses pontos finais usa portas TCP padrão para HTTP (80) e HTTPS (443).
>
>
## <a name="next-steps"></a>Próximas etapas

Precisa de segurança de endereços IP? Baixe a lista das faixas IP do data center do Microsoft Azure para sua nuvem:

* [Worldwide](https://www.microsoft.com/download/details.aspx?id=56519)
* [Governo dos EUA](https://www.microsoft.com/download/details.aspx?id=57063)
* [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064)
* [China](https://www.microsoft.com/download/details.aspx?id=57062)

Outros serviços da Microsoft usam URLs adicionais e endereços IP para conectividade. Para otimizar a conectividade de rede para os serviços Microsoft 365, consulte [Configurar sua rede para o Office 365](/office365/enterprise/set-up-network-for-office-365).
