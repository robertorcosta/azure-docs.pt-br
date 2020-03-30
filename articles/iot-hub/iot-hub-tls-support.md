---
title: Suporte a Azure IoT Hub TLS
description: Práticas recomendadas no uso de conexões TLS seguras para dispositivos e serviços que se comunicam com o IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: 7ab3b48d22f116a707f68cbf6284928c7d2557e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409484"
---
# <a name="tls-support-in-iot-hub"></a>Suporte tls no IoT Hub

O IoT Hub usa o TLS (Transport Layer Security, segurança da camada de transporte) para proteger conexões de dispositivos e serviços IoT. Atualmente, três versões do protocolo TLS são suportadas, ou seja, as versões 1.0, 1.1 e 1.2.

TLS 1.0 e 1.1 são considerados legados e estão planejados para depreciação. Para obter mais informações, consulte [Depreciativo TLS 1.0 e 1.1 para IoT Hub](iot-hub-tls-deprecating-1-0-and-1-1.md). É fortemente recomendável que você use o TLS 1.2 como a versão TLS preferida ao se conectar ao IoT Hub.

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>Restringir conexões ao TLS 1.2 no recurso IoT Hub

Para maior segurança, é aconselhável configurar seus Hubs IoT para *permitir apenas* conexões de clientes que usam a versão 1.2 do TLS e para reforçar o uso de [cifras recomendadas.](#recommended-ciphers)

Para isso, provisione um novo Hub de IoT em `minTlsVersion` qualquer `1.2` uma das [regiões suportadas](#supported-regions) e defina a propriedade na especificação de recurso do hub de ioT do seu modelo de Azure Resource Manager:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

O recurso Criado do IoT Hub usando essa configuração recusará clientes de dispositivos e serviços que tentam se conectar usando as versões TLS 1.0 e 1.1. Da mesma forma, o aperto de mão TLS será recusado se a mensagem HELLO do cliente não listar nenhuma das [cifras recomendadas](#recommended-ciphers).

> [!NOTE]
> A `minTlsVersion` propriedade é somente leitura e não pode ser alterada quando seu recurso do IoT Hub é criado. Portanto, é essencial que você teste e valide corretamente que *todos os* seus dispositivos e serviços de IoT são compatíveis com o TLS 1.2 e as [cifras recomendadas](#recommended-ciphers) com antecedência.

### <a name="supported-regions"></a>Regiões com suporte

Hubs ioT que requerem o uso do TLS 1.2 podem ser criados nas seguintes regiões:

* Leste dos EUA
* Centro-Sul dos Estados Unidos
* Oeste dos EUA 2
* Governo dos EUA do Arizona
* Gov. dos EUA – Virgínia

> [!NOTE]
> Após failovers, `minTlsVersion` a propriedade do seu IoT Hub permanecerá eficaz na região geo-emparelhada após o failover.

### <a name="recommended-ciphers"></a>Cifras recomendadas

Os hubs de IoT configurados para aceitar apenas o TLS 1.2 também reforçarão o uso das seguintes cifras recomendadas:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

### <a name="use-tls-12-in-your-iot-hub-sdks"></a>Use O TLS 1.2 em seus SDKs ioT hub

Use os links abaixo para configurar o TLS 1.2 e permitir cifras em SDKs cliente sdks cliente IoT Hub.

| Idioma | Versões que suportam TLS 1.2 | Documentação |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 ou mais novo            | [Link](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Versão 2.0.0 ou mais recente             | [Link](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Versão 1.21.4 ou mais recente            | [Link](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Versão 1.19.0 ou mais recente            | [Link](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Versão 1.12.2 ou mais recente            | [Link](https://aka.ms/Tls_Node_SDK_IoT) |


### <a name="use-tls-12-in-your-iot-edge-setup"></a>Use o TLS 1.2 na configuração do IoT Edge

Os dispositivos IoT Edge podem ser configurados para usar o TLS 1.2 ao se comunicar com o IoT Hub. Para isso, use a [página de documentação ioT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).