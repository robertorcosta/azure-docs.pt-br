---
title: Como transferir uma carga útil entre o dispositivo e o Serviço de Provisionamento de Dispositivos Azure
description: Este documento descreve como transferir uma carga útil entre o dispositivo e o DPS (Device Provisioning Service, serviço de provisionamento de dispositivos)
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: de6bb3fe0879a69467283e93a04a355876a02cba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246680"
---
# <a name="how-to-transfer-a-payload-between-device-and-dps"></a>Como transferir uma carga útil entre dispositivo e DPS
Às vezes, o DPS precisa de mais dados dos dispositivos para provisioná-los adequadamente para o IoT Hub certo, e esses dados precisam ser fornecidos pelo dispositivo. Vice-versa, o DPS pode retornar dados para o dispositivo para facilitar as lógicas do lado do cliente. 

## <a name="when-to-use-it"></a>Quando usar
Esse recurso pode ser usado como um aprimoramento para [alocação personalizada](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Por exemplo, você deseja alocar seus dispositivos com base no modelo do dispositivo sem intervenção humana. Neste caso, você usará [alocação personalizada](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Você pode configurar o dispositivo para relatar as informações do modelo como parte da chamada do [dispositivo de registro](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice). O DPS passará a carga útil do dispositivo para o webhook de alocação personalizada. E sua função pode decidir para qual IoT Hub este dispositivo irá quando receber informações do modelo do dispositivo. Da mesma forma, se o webhook desejar retornar alguns dados para o dispositivo, ele passará os dados de volta como uma seqüência na resposta do webhook.  

## <a name="device-sends-data-payload-to-dps"></a>Dispositivo envia carga de dados para DPS
Quando seu dispositivo está enviando uma [chamada de dispositivo de registro](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) para DPS, a chamada de registro pode ser aprimorada para tomar outros campos no corpo. O corpo parece o seguinte: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS retorna dados para o dispositivo
Se o webhook da política de alocação personalizada desejar retornar alguns dados ao dispositivo, ele repassará os dados como uma seqüência na resposta do webhook. A mudança está na seção de carga abaixo. 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>Suporte a SDK
Este recurso está disponível em [SDKs cliente](https://docs.microsoft.com/azure/iot-dps/)C, C#, JAVA e Node.js .  

## <a name="next-steps"></a>Próximas etapas
* Desenvolver usando o [SDK do IoT do Azure]( https://github.com/Azure/azure-iot-sdks) para o Hub IoT do Azure e o Serviço de Provisionamento de Dispositivos Hub IoT do Azure
