---
title: Recuperar metadados do balanceador de carga usando o serviço de metadados de instância do Azure (IMDS)
titleSuffix: Azure Load Balancer
description: Comece a aprender como recuperar metadados do balanceador de carga usando o serviço de metadados de instância do Azure.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: 35e32627eaf26eb6f73ca382f119eab61ebd8711
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417007"
---
# <a name="retrieve-load-balancer-metadata-using-the-azure-instance-metadata-service-imds"></a>Recuperar metadados do balanceador de carga usando o serviço de metadados de instância do Azure (IMDS)

## <a name="prerequisites"></a>Pré-requisitos

* Use a [versão mais recente da API](/virtual-machines/windows/instance-metadata-service?tabs=windows#supported-api-versions) para sua solicitação.

## <a name="sample-request-and-response"></a>Exemplo de solicitação e resposta
> [!IMPORTANT]
> Este exemplo ignora proxies. Você **deve** ignorar proxies ao consultar IMDS. Para obter mais informações, consulte [proxies](/virtual-machines/windows/instance-metadata-service?tabs=windows#proxies).
### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01" | ConvertTo-Json
```

### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H "Metadata:true" --noproxy "*" "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01"
```

---
### <a name="sample-response"></a>Resposta de exemplo

```json
{
   "loadbalancer": {
    "publicIpAddresses":[
      {
         "frontendIpAddress":"51.0.0.1",
         "privateIpAddress":"10.1.0.4"
      }
   ],
   "inboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frontendIpAddress":"2603:10e1:100:2::1:1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
   ],
   "outboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frotendIpAddress":"2603:10e1:100:2::1:1",
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
    ]
   }
}

```

## <a name="next-steps"></a>Próximas etapas
[Códigos de erro comuns e etapas de solução de problemas](troubleshoot-load-balancer-imds.md)

Saiba mais sobre o [serviço de metadados de instância do Azure](/virtual-machines/windows/instance-metadata-service)

[Recuperar todos os metadados de uma instância](/virtual-machines/windows/instance-metadata-service?tabs=windows#access-azure-instance-metadata-service)

[Implantar um balanceador de carga padrão](quickstart-load-balancer-standard-public-portal.md)

