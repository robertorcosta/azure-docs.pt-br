---
title: Entender os componentes em modelos de Plug and Play IoT | Microsoft Docs
description: Entenda a diferença entre os modelos de IoT Plug and Play DTDL que usam componentes e modelos que não usam componentes.
author: ericmitt
ms.author: ericmitt
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4c41edc477460e6d239688aafe6d7219bed36cd4
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352118"
---
# <a name="iot-plug-and-play-components-in-models"></a>Componentes de Plug and Play IoT em modelos

Nas convenções de Plug and Play IoT, um dispositivo será um dispositivo IoT Plug and Play se ele apresentar sua ID de modelo de DTDL (linguagem de definição de gêmeos digital) quando se conectar a um hub IoT.

O trecho a seguir mostra algumas IDs de modelo de exemplo:

```json
 "@id": "dtmi:com:example:TemperatureController;1"
 "@id": "dtmi:com:example:Thermostat;1",
```

## <a name="no-components"></a>Nenhum componente

Um modelo simples não usa componentes incorporados ou em cascata. Ele inclui informações de cabeçalho e uma seção de conteúdo para definir telemetria, propriedades e comandos.

O exemplo a seguir mostra parte de um modelo simples que não usa componentes:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

Embora o modelo não defina explicitamente um componente, ele se comporta como se houver um único componente com todas as definições de telemetria, propriedade e comando.

A captura de tela a seguir mostra como o modelo é exibido na ferramenta do Azure IoT Explorer:

:::image type="content" source="media/concepts-components/default-component.png" alt-text="Componente padrão no Azure IoT Explorer":::

A ID do modelo é armazenada em uma propriedade mydo dispositivo, como mostra a captura de tela a seguir:

:::image type="content" source="media/concepts-components/twin-model-id.png" alt-text="ID do modelo na propriedade de propriedades digitais":::

Um modelo DTDL sem componentes é uma simplificação útil para um dispositivo com um único conjunto de telemetria, propriedades e comandos. Um modelo que não usa componentes facilita a migração de um dispositivo existente para um dispositivo IoT Plug and Play-você cria um modelo DTDL que descreve o dispositivo real sem a necessidade de definir nenhum componente.

## <a name="multiple-components"></a>Vários componentes

Os componentes permitem criar uma interface de modelo como um assembly de outras interfaces.

Por exemplo, a interface [termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) é definida como um modelo. Você pode incorporar essa interface como um ou mais componentes quando definir o modelo de [controlador de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json). No exemplo a seguir, esses componentes são chamados `thermostat1` e `thermostat2` .

Para um modelo DTDL com vários componentes, há duas ou mais seções de componente. Cada seção foi `@type` definida como `Component` e se refere explicitamente a um esquema, conforme mostrado no trecho a seguir:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
... 
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
...
```

Esse modelo tem três componentes definidos na seção conteúdo-dois `Thermostat` componentes e um `DeviceInformation` componente. Também há um componente raiz padrão.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre componentes de modelo, aqui estão alguns recursos adicionais:

- [DTDL (digital gêmeos Definition Language) V2](https://github.com/Azure/opendigitaltwins-dtdl)
- [Repositórios de modelo](./concepts-model-repository.md)