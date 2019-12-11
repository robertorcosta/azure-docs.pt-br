---
title: Como transferir dados adicionais entre o dispositivo e o serviço de provisionamento de dispositivos do Azure
description: Este documento descreve como transferir dados adicionais entre o serviço de provisionamento de dispositivos e dispositivos (DPS)
author: menchi
ms.author: menchi
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: e9482f7069616d61efb98f66590ce33cfe3cf350
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974846"
---
# <a name="how-to-transfer-additional-data-between-device-and-dps"></a>Como transferir dados adicionais entre o dispositivo e o DPS
Às vezes, o DPS precisa de mais dados dos dispositivos para provisioná-los de forma adequada no Hub IoT correto e esses dados precisam ser fornecidos pelo dispositivo. Vice-versa, o DPS pode retornar dados para o dispositivo para facilitar as lógicas do lado do cliente. 

## <a name="when-to-use-it"></a>Quando usá-lo
Esse recurso pode ser usado como um aprimoramento para a [alocação personalizada](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Por exemplo, você deseja alocar seus dispositivos com base no modelo de dispositivo sem intervenção humana. Nesse caso, você usará a [alocação personalizada](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Você pode configurar o dispositivo para relatar as informações do modelo como parte da [chamada do dispositivo de registro](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice). O DPS passará as informações do dispositivo para o webhook de alocação personalizada. E sua função pode decidir a qual Hub IoT este dispositivo será usado quando receber informações de modelo do dispositivo. Da mesma forma, se o webhook desejar retornar alguns dados para o dispositivo, ele transmitirá os dados de volta como uma cadeia de caracteres na resposta do webhook.  

## <a name="device-sends-data-to-dps"></a>O dispositivo envia dados para o DPS
Quando o dispositivo estiver enviando uma [chamada de dispositivo de registro](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) para o DPS, a chamada de registro poderá ser aprimorada para usar outros campos no corpo. O corpo é semelhante ao seguinte: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “interfaces”: “TODO: get how interfaces are reported by devices from PnP folks.”, 
       “data”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>O DPS retorna dados para o dispositivo
Se o webhook da diretiva de alocação personalizada quiser retornar alguns dados para o dispositivo, ele passará os dados de volta como uma cadeia de caracteres na resposta do webhook. A alteração está na seção returnData abaixo. 
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
        "returnData": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>Suporte a SDK
Esse recurso está disponível em SDKs do C# [cliente](https://docs.microsoft.com/azure/iot-dps/)C,, Java e node. js.  

## <a name="next-steps"></a>Próximos passos
* Desenvolver usando o [SDK do IoT do Azure]( https://github.com/Azure/azure-iot-sdks) para o Hub IoT do Azure e o Serviço de Provisionamento de Dispositivos Hub IoT do Azure
