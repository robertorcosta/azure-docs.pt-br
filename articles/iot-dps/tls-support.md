---
title: Suporte TLS do DPS (serviço de provisionamento de dispositivos) do Azure IoT
description: Práticas recomendadas no uso de conexões TLS seguras para dispositivos e serviços se comunicando com o serviço de provisionamento de dispositivos IoT (DPS)
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: wesmc
ms.openlocfilehash: 285832d80d37c8553ffc8e37c6f6eab5d7f6d943
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984844"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>Suporte a TLS no serviço de provisionamento de dispositivos no Hub IoT do Azure (DPS)

O DPS usa TLS (Transport Layer Security) para proteger conexões de dispositivos IoT. Atualmente, há suporte para três versões do protocolo TLS, principalmente as versões 1,0, 1,1 e 1,2.

O TLS 1,0 e o 1,1 são considerados herdados e são planejados para substituição. Para obter mais informações, consulte [preterindo TLS 1,0 e 1,1 para o Hub IOT](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md). É altamente recomendável que você use o TLS 1,2 como a versão do TLS preferencial ao se conectar ao DPS.

## <a name="restrict-connections-to-tls-12"></a>Restringir conexões ao TLS 1,2

Para maior segurança, é aconselhável configurar suas instâncias de DPS para permitir *somente* conexões de cliente de dispositivo que usam TLS versão 1,2 e para impor o uso de [codificações recomendadas](#recommended-ciphers).

Para fazer isso, provisione um novo recurso de DPS em qualquer uma das [regiões com suporte](#supported-regions) e defina a `minTlsVersion` Propriedade como `1.2` na especificação de recurso do DPS do modelo de Azure Resource Manager. O JSON de modelo de exemplo a `minTlsVersion` seguir especifica a propriedade para uma nova instância de DPS.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/ProvisioningServices",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-DPS-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "S1",
                "capacity": 1
            },
        }     
    ]
}
```

Você pode implantar o modelo com o comando CLI do Azure a seguir. 

```azurecli
az deployment group create -g <your resource group name> --template-file template.json
```

Para obter mais informações sobre como criar recursos de DPS com modelos do Resource Manager, consulte, [Configurar o DPS com um modelo de Azure Resource Manager](quick-setup-auto-provision-rm.md).

O recurso de DPS criado usando essa configuração recusará dispositivos que tentam se conectar usando as versões 1,0 e 1,1 do TLS. Da mesma forma, o handshake TLS será recusado se a mensagem de SAUDAção do cliente do dispositivo não listar nenhuma das [codificações recomendadas](#recommended-ciphers).

> [!NOTE]
> A `minTlsVersion` propriedade é somente leitura e não pode ser alterada depois que o recurso de DPS é criado. Portanto, é essencial que você teste e valide corretamente se *todos os* seus dispositivos IOT são compatíveis com o TLS 1,2 e as [codificações recomendadas](#recommended-ciphers) com antecedência.

## <a name="supported-regions"></a>Regiões com suporte

Instâncias de DPS do IoT que exigem o uso de TLS 1,2 podem ser criadas nas seguintes regiões:

* Governo dos EUA do Arizona
* Gov. dos EUA – Virgínia

> [!NOTE]
> Após os failovers, `minTlsVersion` a propriedade de seu DPS permanecerá efetiva no pós-failover da região emparelhada geograficamente.

## <a name="recommended-ciphers"></a>Codificações recomendadas

As instâncias de DPS configuradas para aceitar somente o TLS 1,2 também impedirão o uso das seguintes codificações recomendadas:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

## <a name="use-tls-12-in-the-iot-sdks"></a>Usar o TLS 1,2 nos SDKs de IoT

Use os links abaixo para configurar o TLS 1,2 e as codificações permitidas nos SDKs do cliente IoT do Azure.

| Linguagem | Versões com suporte a TLS 1,2 | Documentação |
|----------|------------------------------------|---------------|
| C        | Marca 2019-12-11 ou mais recente            | [Link](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Versão 2.0.0 ou mais recente             | [Link](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Versão 1.21.4 ou mais recente            | [Link](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Versão 1.19.0 ou mais recente            | [Link](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Versão 1.12.2 ou mais recente            | [Link](https://aka.ms/Tls_Node_SDK_IoT) |


## <a name="use-tls-12-with-iot-edge"></a>Usar o TLS 1,2 com IoT Edge

IoT Edge dispositivos podem ser configurados para usar o TLS 1,2 ao se comunicar com o Hub IoT e o DPS. Para essa finalidade, use a [página de documentação IOT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).