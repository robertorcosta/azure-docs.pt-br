---
title: O que são modelos de dispositivo no Azure IoT Central | Microsoft Docs
description: Os modelos de dispositivo IoT Central do Azure permitem especificar o comportamento dos dispositivos conectados ao seu aplicativo. Um modelo de dispositivo especifica a telemetria, as propriedades e os comandos que o dispositivo deve implementar. Um modelo de dispositivo também define a interface do usuário para o dispositivo em IoT Central, como os formulários e painéis usados por um operador.
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 75317b5c6af2d0ce89d2db32f4343d9cc73a1a81
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91813161"
---
# <a name="what-are-device-templates"></a>O que são modelos de dispositivo?

_Este artigo se aplica a desenvolvedores de dispositivos e construtores de solução._

Um modelo de dispositivo no Azure IoT Central é um plano gráfico que define as características e os comportamentos de um tipo de dispositivo que se conecta ao seu aplicativo. Por exemplo, o modelo de dispositivo define a telemetria que um dispositivo envia para que IoT Central possa criar visualizações que usam as unidades e os tipos de dados corretos.

Um Solution Builder adiciona modelos de dispositivo a um aplicativo IoT Central. Um desenvolvedor de dispositivos grava o código de dispositivo que implementa os comportamentos definidos no modelo de dispositivo.

Um modelo de dispositivo inclui as seguintes seções:

- _Um DCM (modelo de funcionalidade de dispositivo)_. Esta parte do modelo de dispositivo define como o dispositivo interage com seu aplicativo. Um desenvolvedor de dispositivos implementa os comportamentos definidos no DCM.
    - _Interfaces_. Um DCM contém uma ou mais interfaces que definem a telemetria, as propriedades e os comandos que o dispositivo deve implementar.
- _Propriedades de nuvem_. Essa parte do modelo de dispositivo permite que o desenvolvedor da solução especifique qualquer metadado de dispositivo a ser armazenado. As propriedades de nuvem nunca são sincronizadas com dispositivos e só existem no aplicativo. As propriedades de nuvem não afetam o código que um desenvolvedor de dispositivo grava para implementar o DCM.
- _Personalizações_. Essa parte do modelo de dispositivo permite que o desenvolvedor da solução substitua algumas das definições no DCM. As personalizações serão úteis se o desenvolvedor da solução quiser refinar como o aplicativo lida com um valor, como alterar o nome de exibição de uma propriedade ou a cor usada para exibir um valor de telemetria. As personalizações não afetam o código que um desenvolvedor de dispositivo grava para implementar o DCM.
- _Exibições_. Essa parte do modelo de dispositivo permite que o desenvolvedor da solução defina visualizações para exibir dados do dispositivo e formulários para gerenciar e controlar um dispositivo. As exibições usam o DCM, as propriedades de nuvem e as personalizações. As exibições não afetam o código que um desenvolvedor de dispositivo grava para implementar o DCM.

## <a name="device-capability-models"></a>Modelos de funcionalidade do dispositivo

Um DCM define como um dispositivo interage com seu aplicativo IoT Central. O desenvolvedor do dispositivo deve garantir que o dispositivo implemente os comportamentos definidos no DCM para que IoT Central possa monitorar e gerenciar o dispositivo. Um DCM é composto de uma ou mais _interfaces_, e cada interface pode definir uma coleção de tipos de _telemetria_ , _Propriedades de dispositivo_e _comandos_. Um desenvolvedor de solução pode importar um arquivo JSON que define o DCM em um modelo de dispositivo ou usar a interface do usuário da Web no IoT Central para criar ou editar um DCM. As alterações em um DCM feito usando a interface do usuário da Web exigem [que o modelo de dispositivo tenha controle de versão](./howto-version-device-template.md).

Um desenvolvedor de soluções também pode exportar um arquivo JSON que contém o DCM. Um desenvolvedor de dispositivos pode usar este documento JSON para entender como o dispositivo deve se comunicar com o aplicativo IoT Central.

O arquivo JSON que define o DCM usa o [DTDL (digital Mydefinition Language) v1](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). IoT Central espera que o arquivo JSON contenha o DCM com as interfaces definidas como embutidas, em vez de arquivos separados.

Um dispositivo IoT típico é composto de:

- Partes personalizadas, que são as coisas que tornam seu dispositivo exclusivo.
- As partes padrão, que são coisas comuns a todos os dispositivos.

Essas partes são chamadas de _interfaces_ em um DCM. As interfaces definem os detalhes de cada parte que seu dispositivo implementa. As interfaces são reutilizáveis no DCMs.

O exemplo a seguir mostra a estrutura de um modelo de capacidade de dispositivo para um dispositivo de sensor ambiental com duas interfaces:

```json
{
  "@id": "urn:contoso:sensor_device:1",
  "@type": "CapabilityModel",
  "displayName": "Environment Sensor Capability Model",
  "implements": [
    {
      "@type": "InterfaceInstance",
      "name": "deviceinfo",
      "schema": {
        "@id": "urn:azureiot:DeviceManagement:DeviceInformation:1",
        "@type": "Interface",
        "displayName": "Device Information",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
    },
    {
      "@type": "InterfaceInstance",
      "name": "sensor",
      "schema": {
        "@id": "urn:contoso:EnvironmentalSensor:1",
        "@type": "Interface",
        "displayName": "Environmental Sensor",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Um modelo de funcionalidade tem alguns campos obrigatórios:

- `@id`: uma ID exclusiva na forma de um simples nome de recurso uniforme.
- `@type`: declara que esse objeto é um modelo de funcionalidade.
- `@context`: especifica a versão do DTDL usada para o modelo de funcionalidade.
- `implements`: lista as interfaces que seu dispositivo implementa.

Cada entrada na lista de interfaces na seção implementações tem um:

- `name`: o nome de programação da interface.
- `schema`: a interface que o modelo de funcionalidade implementa.

Uma interface tem alguns campos obrigatórios:

- `@id`: uma ID exclusiva na forma de um simples nome de recurso uniforme.
- `@type`: declara que esse objeto é uma interface.
- `@context`: especifica a versão DTDL usada para a interface.
- `contents`: lista as propriedades, a telemetria e os comandos que compõem seu dispositivo.

Há alguns campos opcionais que você pode usar para adicionar mais detalhes ao modelo de funcionalidade, como nome de exibição e descrição.

## <a name="interfaces"></a>Interfaces

O DTDL permite que você descreva os recursos do seu dispositivo. Os recursos relacionados são agrupados em interfaces. As interfaces descrevem as propriedades, a telemetria e os comandos que uma parte do seu dispositivo implementa:

- `Properties`. As propriedades são campos de dados que representam o estado do seu dispositivo. Use Propriedades para representar o estado durável do dispositivo, como o estado ligado de uma bomba refrigeração. As propriedades também podem representar as propriedades básicas do dispositivo, como a versão do firmware do dispositivo. É possível declarar propriedades como somente leitura ou graváveis. Somente os dispositivos podem atualizar o valor de uma propriedade somente leitura. Um operador pode definir o valor de uma propriedade gravável para enviar a um dispositivo.
- `Telemetry`. Campos de telemetria representam medições de sensores. Sempre que o dispositivo toma uma medida de sensor, ele deve enviar um evento de telemetria contendo os dados do sensor.
- `Commands`. Os comandos representam métodos que os usuários do seu dispositivo podem executar no dispositivo. Por exemplo, um comando de redefinição ou um comando para ativar ou desativar um ventilador.

O exemplo a seguir mostra a definição da interface do sensor ambiental:

```json
{
  "@type": "Property",
  "displayName": "Device State",
  "description": "The state of the device. Two states online/offline are available.",
  "name": "state",
  "schema": "boolean"
},
{
  "@type": "Property",
  "displayName": "Customer Name",
  "description": "The name of the customer currently operating the device.",
  "name": "name",
  "schema": "string",
  "writable": true
},
{
  "@type": [
    "Telemetry",
    "SemanticType/Temperature"
  ],
  "description": "Current temperature on the device",
  "displayName": "Temperature",
  "name": "temp",
  "schema": "double",
  "unit": "Units/Temperature/fahrenheit"
},
{
  "@type": "Command",
  "name": "turnon",
  "comment": "This Commands will turn-on the LED light on the device.",
  "commandType": "synchronous"
},
{
  "@type": "Command",
  "name": "turnoff",
  "comment": "This Commands will turn-off the LED light on the device.",
  "commandType": "synchronous"
}
```

Este exemplo mostra duas propriedades (uma somente leitura e uma gravável), um tipo de telemetria e dois comandos. Uma descrição mínima do campo tem um:

- `@type` para especificar o tipo de recurso: `Telemetry` , `Property` ou `Command` .  Em alguns casos, o tipo inclui um tipo semântico para permitir que IoT Central faça algumas suposições sobre como lidar com o valor.
- `name` para o valor de telemetria.
- `schema` para especificar o tipo de dados para a telemetria ou a propriedade. Esse valor pode ser um tipo primitivo, como duplo, inteiro, booliano ou cadeia de caracteres. Também há suporte para tipos de objetos complexos, matrizes e mapas.
- `commandType` para especificar como o comando deve ser manipulado.

Campos opcionais, como nome de exibição e descrição, permitem que você adicione mais detalhes à interface e aos recursos.

## <a name="properties"></a>Propriedades

Por padrão, as propriedades são somente leitura. Propriedades somente leitura significam que o valor da propriedade relatórios de dispositivo é atualizado para seu aplicativo IoT Central. O aplicativo IoT Central não pode definir o valor de uma propriedade somente leitura.

Você também pode marcar uma propriedade como gravável em uma interface. Um dispositivo pode receber uma atualização para uma propriedade gravável de seu aplicativo IoT Central, bem como relatar atualizações de valor de propriedade para seu aplicativo.

Os dispositivos não precisam ser conectados para definir valores de propriedade. Os valores atualizados são transferidos quando o dispositivo se conecta ao aplicativo. Esse comportamento se aplica a propriedades somente leitura e graváveis.

Não use Propriedades para enviar telemetria do seu dispositivo. Por exemplo, uma propriedade ReadOnly, como `temperatureSetting=80` deveria significar que a temperatura do dispositivo foi definida como 80, e o dispositivo está tentando chegar ou permanecer nessa temperatura.

Para propriedades graváveis, o aplicativo do dispositivo retorna um código de status de estado desejado, versão e descrição para indicar se ele recebeu e aplicou o valor da propriedade.

## <a name="telemetry"></a>Telemetria

IoT Central permite exibir a telemetria em painéis e gráficos e usar regras para disparar ações quando os limites forem atingidos. IoT Central usa as informações no DCM, como tipos de dados, unidades e nomes de exibição, para determinar como exibir valores de telemetria.

Você pode usar o recurso de exportação de dados IoT Central para transmitir telemetria para outros destinos, como armazenamento ou hubs de eventos.

## <a name="commands"></a>Comandos

Os comandos são síncronos ou assíncronos. Um comando síncrono deve ser executado dentro de 30 segundos por padrão e o dispositivo deve ser conectado quando o comando chegar. Se o dispositivo responder no tempo ou o dispositivo não estiver conectado, o comando falhará.

Use comandos assíncronos para operações de longa execução. O dispositivo envia informações de progresso usando mensagens de telemetria. Essas mensagens de progresso têm as seguintes propriedades de cabeçalho:

- `iothub-command-name`: o nome do comando, por exemplo `UpdateFirmware` .
- `iothub-command-request-id`: a ID da solicitação gerada no lado do servidor e enviada para o dispositivo na chamada inicial.
- `iothub-interface-id`: A ID da interface em que esse comando é definido, por exemplo `urn:example:AssetTracker:1` .
 `iothub-interface-name`: o nome da instância dessa interface, por exemplo `myAssetTracker` .
- `iothub-command-statuscode`: o código de status retornado do dispositivo, por exemplo `202` .

## <a name="cloud-properties"></a>Propriedades da Nuvem

As propriedades de nuvem fazem parte do modelo de dispositivo, mas não fazem parte do DCM. As propriedades de nuvem permitem que o desenvolvedor da solução especifique qualquer metadado de dispositivo a ser armazenado no aplicativo IoT Central. As propriedades de nuvem não afetam o código que um desenvolvedor de dispositivo grava para implementar o DCM.

Um desenvolvedor de soluções pode adicionar propriedades de nuvem a painéis e exibições junto com as propriedades do dispositivo para permitir que um operador gerencie os dispositivos conectados ao aplicativo. Um desenvolvedor de soluções também pode usar propriedades de nuvem como parte de uma definição de regra para tornar um valor de limite editável por um operador.

## <a name="customizations"></a>Personalizações

As personalizações fazem parte do modelo de dispositivo, mas não fazem parte do DCM. As personalizações permitem que o desenvolvedor da solução aprimore ou substitua algumas das definições no DCM. Por exemplo, um desenvolvedor de soluções pode alterar o nome de exibição de um tipo ou propriedade de telemetria. Um desenvolvedor de soluções também pode usar personalizações para adicionar validação, como um comprimento mínimo ou máximo para uma propriedade de dispositivo de cadeia de caracteres.

As personalizações podem afetar o código que um desenvolvedor de dispositivo grava para implementar o DCM. Por exemplo, uma personalização poderia definir comprimentos mínimos e máximos de cadeia de caracteres ou valores numéricos mínimos e máximos para telemetria.

## <a name="views"></a>Exibições

Um desenvolvedor de soluções cria exibições que permitem que os operadores monitorem e gerenciem dispositivos conectados. As exibições fazem parte do modelo de dispositivo, portanto, uma exibição é associada a um tipo de dispositivo específico. Uma exibição pode incluir:

- Gráficos para plotar a telemetria.
- Blocos para exibir propriedades de dispositivo somente leitura.
- Blocos para permitir que o operador edite as propriedades do dispositivo gravável.
- Blocos para permitir que o operador edite as propriedades de nuvem.
- Blocos para permitir que o operador chame comandos, incluindo comandos que esperam uma carga.
- Blocos para exibir rótulos, imagens ou texto de redução.

A telemetria, as propriedades e os comandos que você pode adicionar a uma exibição são determinados pelo DCM, pelas propriedades de nuvem e pelas personalizações no modelo de dispositivo.

## <a name="next-steps"></a>Próximas etapas

Como desenvolvedor de dispositivos, agora que você aprendeu sobre modelos de dispositivo, as próximas etapas sugeridas são ler as [cargas de telemetria, propriedade e comando](./concepts-telemetry-properties-commands.md) para saber mais sobre os dados que um dispositivo troca com IOT central.

Como desenvolvedor de soluções, uma próxima etapa sugerida é ler como [definir um novo tipo de dispositivo IOT em seu aplicativo de IOT central do Azure](./howto-set-up-template.md) para saber mais sobre como criar um modelo de dispositivo.
