---
title: Quais são as novidades? Atualização da versão prévia do IoT Plug and Play | Microsoft Docs
description: Saiba o que há de novo na liberação de Atualização da versão prévia do IoT Plug and Play.
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.openlocfilehash: 60ad7f5df4f13d626d7a2c24990c7f48db28d01d
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475219"
---
# <a name="iot-plug-and-play-preview-refresh"></a>Atualização da versão prévia do IoT Plug and Play

Este artigo descreve as principais alterações nos SDKS, bibliotecas, ferramentas e serviços na liberação da atualização da versão prévia do IoT Plug and Play em julho de 2020. A liberação da versão prévia do IoT Plug and Play anterior foi em agosto de 2019.

## <a name="digital-twins-definition-language-dtdl"></a>DTDL (Linguagem de Definição de Gêmeos Digitais)

Esse lançamento dá suporte para o [DTDL v2](https://github.com/Azure/opendigitaltwins-dtdl) e substitui o [DTDL v1](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL/v1-preview).

A lista a seguir mostra as principais diferenças entre o DTDL v1 e o DTDL v2. No DTDL v2:

- As IDs de modelo têm o prefixo `dtmi` em vez de `urn`. DTMI (identificadores de modelo de gêmeo digital) substituem as IDs de gêmeo digital prefixadas `urn` usadas no DTDL v1. A versão agora é precedida por um `;`. Por exemplo, em vez de usar `urn:CompanyName:Model:1`, agora você usa `dtmi:CompanyName:Model;1`.
- Defina `@context` como `dtmi:dtdl:context;2` em vez de `http://azureiot.com/v1/contexts/IoTModel.json`.
- Use o tipo **Interface** em vez dos tipos **CapabilityModel** para modelar um dispositivo.
- Os **Componentes** substituem instâncias de **Interface**. Você pode definir **Relações** e **Componentes** como parte do conteúdo de uma **Interface**.
- Uma **Interface** pode herdar de outra **Interface**.
- Você pode estender o DTDL usando os _tipos semânticos extensíveis_. Esse sistema de tipo extensível oferece maior flexibilidade do que os tipos semânticos embutidos em código, como temperatura e umidade no DTDL v1.
- A propriedade **displayUnit** foi removida.
- Você não pode usar sublinhados à esquerda ou à direita em um nome. Sublinhados à esquerda em um nome são reservados para uso do sistema.

Para trabalhar com o DTDL v1, você precisa usar a versão anterior do SDK e o Azure IoT Explorer 0.10.x. Para trabalhar com o DTDL v2, você precisa da versão mais recente do SDK e do Azure IoT Explorer 0.11.x.

## <a name="no-component-and-multiple-component-implementations"></a>Implementações sem componente e com vários componentes

Dispositivos simples que usam pouca telemetria, comandos ou propriedades podem ser descritos em uma interface sem usar componentes. Qualquer dispositivo existente pode se tornar um IoT Plug and Play anunciando a **ID do Modelo** sem nenhuma alteração na implementação do dispositivo existente.

Dispositivos mais complexos podem agrupar telemetria, comandos e propriedades em diferentes interfaces para gerenciar a complexidade e habilitar a reutilização entre dispositivos. Esses dispositivos precisam ser atualizados para seguir um conjunto de regras simples definidas em [convenções de mensagem de versão prévia do IoT Plug and Play](concepts-convention.md).

## <a name="registration-and-discovery"></a>Registro e descoberta

Neste lançamento, os dispositivos anunciam a **ID de Modelo** com o Hub IoT em cada conexão. O Hub IoT armazena em cache a **ID do Modelo** habilitando soluções de back-end para recuperar a **ID do Modelo** usando a propriedade `modelId` do dispositivo gêmeo. A **ID do Modelo** também pode ser recuperada de `$metadata.$model` no gêmeo digital.

## <a name="microsoft-defined-interfaces"></a>Interfaces definidas pela Microsoft

As seguintes interfaces definidas pela Microsoft são preteridas e não foram publicadas no novo repositório de modelos:

- **urn:azureiot:ModelDiscovery:DigitalTwin:1**
- **urn:azureiot:ModelDiscovery:ModelInformation:1**

A seguinte interface foi publicada no novo repositório de modelos: `dtmi:azure:DeviceManagement:DeviceInformation;1`, disponível na URL [https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation;1?api-version=2020-05-01-preview](https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation;1?api-version=2020-05-01-preview).

## <a name="digitaltwinchangeevents"></a>DigitalTwinChangeEvents

A estrutura de eventos da [origem do evento](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events) do **DigitalTwinChangeEvents** foi alterada para usar o formato **JSON-Patch**. Essa alteração é uma alteração da falha sem suporte a compatibilidade com versões anteriores.

## <a name="message-routing"></a>Roteamento de mensagem

As mensagens de telemetria têm as alterações a seguir na coleção [SystemProperties](../iot-hub/iot-hub-devguide-messages-construct.md).

Agora, ela inclui uma propriedade **dt-dataschema** que é a **ID do Modelo** registrada pelo dispositivo.

A propriedade **dt-subject** representa o componente que envia a mensagem de telemetria.

A propriedade **iothub-interface-name** é preterida.

## <a name="device-and-service-sdks"></a>SDKs de serviço e dispositivo

Não há compatibilidade com versões anteriores em versões prévias anteriores dos SDKs. Você precisará alterar o seu código se mudar para a versão prévia mais recente de um SDK.

Com a abordagem baseada em convenção, não há necessidade de SDKs de cliente de dispositivo separados. Nessa liberação de versão prévia, as bibliotecas existentes do **DigitalTwinClient** são preteridas em todos os idiomas. Em vez disso, os SDKs de cliente do dispositivo do Hub IoT foram atualizados para incluir uma opção para anunciar a **ID do Modelo**.

Os dispositivos que não usam componentes exigem alterações mínimas de código, apenas anunciando a **ID do Modelo**. Dispositivos mais complexos que usam vários componentes podem exigir algumas funções reutilizáveis para implementar as [convenções](concepts-convention.md). Os exemplos de dispositivo incluem um conjunto de funções que você pode reutilizar na sua implementação de dispositivo.

### <a name="service-sdks"></a>SDKs do Serviço

O SDK do serviço está disponível em [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/digitaltwins-preview/digitaltwins/service/readme.md) e [Python](https://github.com/Azure/azure-iot-sdk-python/blob/digitaltwins-preview/azure-iot-hub/README.md).

## <a name="vs-code-extension"></a>Extensão do VS Code

A extensão do [Azure IoT Device Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) dá suporte de criação para o DTDL v1, integração à versão anterior do repositório de modelos e geração de código.

Se você precisar de suporte à criação do DTDL v2 no VS Code, instale a nova [extensão do DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) no VS Code. A extensão não fornece integração ao repositório de modelo nem geração de código. O gerenciamento de modelos no repositório agora é feito usando uma [IU da Web](https://aka.ms/iotmodelrepo) ou a [CLI](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp?view=azure-cli-latest).

## <a name="digital-twin-service-side-rest-apis"></a>APIs REST do lado do serviço de gêmeo digital

As [APIs REST do lado do serviço de gêmeo digital](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin) e os conteúdos foram alterados. As APIs com suporte são:

- Recuperar um gêmeo digital.
- Chamar um comando.
- Atualize um gêmeo digital usando o conteúdo **JSON-Patch**.

As APIs REST existentes continuam com suporte neste lançamento.

## <a name="model-repository"></a>Repositório de modelos

Agora, há um repositório de modelos que contém os modelos publicados públicos e os modelos da empresa protegidos por RBAC privados. Todos os modelos têm um identificador exclusivo e são imutáveis depois de criados.

Os repositórios de modelo da empresa existentes do lançamento anterior não têm suporte neste lançamento. Você pode continuar a usar o site [Azure Certified para IoT](https://preview.catalog.azureiotsolutions.com/products) para gerenciar os modelos antigos do DTDL v1. No entanto, você não pode mais usar este site para registrar, testar e certificar dispositivos.

## <a name="azure-iot-central"></a>Azure IoT Central

O Azure IoT Central está sendo atualizado no momento para dar suporte à liberação de atualização de versão prévia do IoT Plug and Play.
