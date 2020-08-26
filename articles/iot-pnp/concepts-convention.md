---
title: Convenções de Plug and Play IoT | Microsoft Docs
description: A descrição das convenções que o IoT Plug and Play espera que os dispositivos usem quando enviam telemetria e propriedades e manipulam comandos e atualizações de propriedade.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ef29be53e776c4c185ac8430b3340c53ca85d855
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88856050"
---
# <a name="iot-plug-and-play-conventions"></a>Convenções do IoT Plug and Play

Os dispositivos de visualização de IoT Plug and Play devem seguir um conjunto de convenções quando trocam mensagens com um hub IoT. Os dispositivos de visualização do IoT Plug and Play usam o protocolo MQTT para se comunicar com o Hub IoT.

Você descreve a telemetria, as propriedades e os comandos que um dispositivo de Plug and Play IoT implementa com um _modelo_ [DTDL (digital gêmeos Definition Language) V2](https://github.com/Azure/opendigitaltwins-dtdl) . Há dois tipos de modelo referenciados neste artigo:

- **Nenhum componente** -um modelo sem componentes. O modelo declara telemetria, propriedades e comandos como propriedades de nível superior na seção de conteúdo da interface principal.
- **Vários componentes** – um modelo composto por duas ou mais interfaces. Uma interface principal com telemetria, propriedades e comandos. Uma ou mais interfaces declaradas como componentes com telemetria, propriedades e comandos adicionais.

Para obter mais informações, consulte [componentes de IoT plug and Play em modelos](concepts-components.md).

## <a name="identify-the-model"></a>Identificar o modelo

Para anunciar o modelo que ele implementa, um dispositivo IoT Plug and Play inclui a ID do modelo no pacote de conexão MQTT adicionando `model-id` ao `USERNAME` campo.

Para identificar o modelo que um dispositivo implementa, um serviço pode obter a ID do modelo de:

- O campo de dispositivo `modelId` .
- O campo de `$metadata.$model` área digital.
- Uma notificação de alteração de troca digital.

## <a name="telemetry"></a>Telemetria

A telemetria enviada de um dispositivo sem componente não requer metadados extras. O sistema adiciona a `dt-dataschema` propriedade.

A telemetria enviada de um dispositivo de vários componentes deve ser adicionada `$.sub` como uma propriedade de mensagem. O sistema adiciona as `dt-subject` `dt-dataschema` Propriedades e.

## <a name="read-only-properties"></a>Propriedades somente leitura

### <a name="sample-no-component-read-only-property"></a>Exemplo sem propriedade somente leitura do componente

Um dispositivo pode enviar qualquer JSON válido que siga as regras de DTDL v2.

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:example: Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "temperature",
          "schema": "double"
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Carga de exemplo**

      ```json
      "reported" :
      {
        "temperature" : 21.3
      }
      ```
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-read-only-property"></a>Propriedade somente leitura de vários componentes de exemplo

O dispositivo deve adicionar o `{"__t": "c"}` marcador para indicar que o elemento refere-se a um componente.

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:TemperatureController;1",
      "@type": "Interface",
      "displayName": "Temperature Controller",
      "contents": [
        {
          "@type" : "Component",
          "schema": "dtmi:com:example:Thermostat;1",
          "name": "thermostat1"
        }
      ]

      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "temperature",
          "schema": "double"
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Propriedade relatada**

      ```json
      "reported": {
        "thermostat1": {
          "__t": "c",
          "temperature": 21.3
        }
      }
      ```
   :::column-end:::
:::row-end:::

## <a name="writable-properties"></a>Propriedades graváveis

O dispositivo deve confirmar que recebeu a propriedade enviando uma propriedade relatada. A propriedade relatada deve incluir:

- `value` -o valor real da propriedade (normalmente o valor recebido, mas o dispositivo pode decidir relatar um valor diferente).
- `ac` -um código de confirmação que usa um código de status HTTP.
- `av` -uma versão de confirmação que se refere ao `$version` da propriedade desejada.
- `ad` -uma descrição de confirmação opcional.

### <a name="sample-no-component-writable-property"></a>Exemplo de propriedade não gravável de componente

Um dispositivo pode enviar qualquer JSON válido que siga as regras DTDL v2:

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:example: Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "targetTemperature",
          "schema": "double",
          "writable": true
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Propriedade desejada**

      ```json
      "desired" :
      {
        "targetTemperature" : 21.3
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **Propriedade relatada**

      ```json
      "reported": {
        "targetTemperature": {
          "value": 21.3,
          "ac": 200,
          "av": 3,
          "ad": "complete"
       }
     }
      ```
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-writable-property"></a>Propriedade gravável de vários componentes de exemplo

O dispositivo deve adicionar o `{"__t": "c"}` marcador para indicar que o elemento refere-se a um componente.

O marcador é enviado somente para atualizações em nível de componente; portanto, os dispositivos não deve verificam esse sinalizador.

O dispositivo deve confirmar que recebeu a propriedade enviando uma propriedade relatada:

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:TemperatureController;1",
      "@type": "Interface",
      "displayName": "Temperature Controller",
      "contents": [
        {
          "@type" : "Component",
          "schema": "dtmi:com:example:Thermostat;1",
          "name": "thermostat1"
        }
      ]

      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "targetTemperature",
          "schema": "double",
          "writable": true
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Propriedade desejada**

      ```json
      "desired": {
        "thermostat1": {
          "__t": "c",
          "targetTemperature": 21.3
        }
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **Propriedade relatada**

      ```json
      "reported": {
        "thermostat1": {
          "__t": "c",
          "targetTemperature": {
            "value": 23,
            "ac": 200,
            "av": 3,
            "ad": "complete"
          }
        }
      }
      ```
   :::column-end:::
:::row-end:::

## <a name="commands"></a>Comandos

Nenhuma interface de componente usa o nome de comando sem um prefixo.

Em um dispositivo, várias interfaces de componente usam nomes de comando com o seguinte formato: `componentName*commandName` .

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre as convenções de Plug and Play IoT, aqui estão alguns recursos adicionais:

- [DTDL (Linguagem de Definição de Gêmeos Digitais)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK do Dispositivo C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [API REST de IoT](https://docs.microsoft.com/rest/api/iothub/device)
- [Componentes do modelo](./concepts-components.md)
