---
title: Usar propriedades em uma solução de IoT Central do Azure
description: Saiba como usar propriedades somente leitura e graváveis em uma solução de IoT Central do Azure.
author: dominicbetts
ms.author: dobett
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a750a98c27fd62288993b2203acc2032ccf39d71
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999759"
---
# <a name="use-properties-in-an-azure-iot-central-solution"></a>Usar propriedades em uma solução de IoT Central do Azure

Este artigo mostra como usar as propriedades do dispositivo que são definidas em um modelo de dispositivo em seu aplicativo de IoT Central do Azure.

As propriedades representam valores de ponto no tempo. Por exemplo, um dispositivo pode usar uma propriedade para relatar a temperatura de destino que está tentando alcançar. As propriedades também permitem que você sincronize o estado entre o dispositivo e o aplicativo IoT Central do Azure. Você pode definir propriedades graváveis do IoT Central do Azure.

Você também pode definir propriedades de nuvem em um aplicativo de IoT Central do Azure. Os valores de propriedade de nuvem nunca são trocados por um dispositivo e estão fora do escopo deste artigo.

## <a name="define-your-properties"></a>Definir suas propriedades

As propriedades são campos de dados que representam o estado do seu dispositivo. Use Propriedades para representar o estado durável do dispositivo, como o estado ligado/desligado de um dispositivo. As propriedades também podem representar as propriedades básicas do dispositivo, como a versão do software do dispositivo. É possível declarar propriedades como somente leitura ou graváveis.

A captura de tela a seguir mostra uma definição de propriedade em um aplicativo IoT Central do Azure.

![Captura de tela que mostra uma definição de propriedade em um aplicativo IoT Central do Azure.](./media/howto-use-properties/property-definition.png)

A tabela a seguir mostra as definições de configuração para uma funcionalidade de propriedade.

| Campo           | Descrição                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nome de exibição    | O nome de exibição do valor da propriedade usado em dashboards e formulários.                                                                                                                                                              |
| Name            | O nome da propriedade. O Azure IoT Central gera um valor para esse campo a partir do nome de exibição, mas você pode escolher seu próprio valor, se necessário. Este campo deve ser alfanumérico.                                                 |
| Tipo de funcionalidade | Propriedade.                                                                                                                                                                                                                          |
| Tipo semântico   | O tipo semântico da propriedade, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos campos a seguir estão disponíveis.                                                                       |
| Esquema          | O tipo de dados de propriedade, como duplo, cadeia de caracteres ou vetor. As opções disponíveis são determinadas pelo tipo semântico. O esquema não está disponível para os tipos semânticos de evento e estado.                                               |
| Gravável       | Se a propriedade não for gravável, o dispositivo poderá relatar valores de propriedade para o Azure IoT Central. Se a propriedade for gravável, o dispositivo poderá relatar valores de propriedade para o Azure IoT Central. Em seguida, o Azure IoT Central pode enviar atualizações de propriedade para o dispositivo. |
| Severity        | Disponível somente para o tipo semântico de evento. As severidades são **Erro**, **Informação** ou **Aviso**.                                                                                                                         |
| Valores de estado    | Disponível apenas para o tipo de estado semântico. Defina os valores de estado possíveis, cada um dos quais com o nome de exibição, o nome, o tipo de enumeração e o valor.                                                                                   |
| Unit            | Uma unidade para o valor da propriedade, como **mph**, **%** ou ** &deg; C**.                                                                                                                                                              |
| Unidade de exibição    | Uma unidade de exibição para uso em dashboards e formulários.                                                                                                                                                                                    |
| Comentário         | Comentários sobre a funcionalidade de propriedade.                                                                                                                                                                                        |
| Description     | Uma descrição da funcionalidade de propriedade.                                                                                                                                                                                          |

As propriedades também podem ser definidas em uma interface em um modelo de dispositivo, conforme mostrado aqui:

``` json
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
 "@type": "Property",
 "displayName": "Date ",
 "description": "The date on which the device is currently operating",
 "name": "date",
 "writable": true,
 "schema": "date"
},
{ 
 "@type": "Property",
 "displayName": "Location",
 "description": "The current location of the device",
 "name": "location",
 "writable": true,
 "schema": "geopoint"
},
{
 "@type": "Property",
 "displayName": "Vector Level",
 "description": "The Vector level of the device",
 "name": "vector",
 "writable": true,
 "schema": "vector"
}
```

Este exemplo mostra cinco Propriedades. Essas propriedades podem estar relacionadas à definição de propriedade na interface do usuário, conforme mostrado aqui:

* `@type` para especificar o tipo de recurso: `Property`
* `name` para o valor da propriedade.
* `schema` Especifique o tipo de dados para a propriedade. Esse valor pode ser um tipo primitivo, como duplo, inteiro, booliano ou cadeia de caracteres. Também há suporte para tipos de objetos complexos, matrizes e mapas.
* `writable` Por padrão, as propriedades são somente leitura. Você pode marcar uma propriedade como gravável usando esse campo.

Campos opcionais, como nome de exibição e descrição, permitem que você adicione mais detalhes à interface e aos recursos.

Ao criar uma propriedade, você pode especificar os tipos de **esquema** complexos, como Object e enum.

![Captura de tela que mostra como adicionar um recurso.](./media/howto-use-properties/property.png)

Quando você seleciona o **esquema**complexo, como **objeto**, também precisa definir o objeto.

![Captura de tela que mostra como definir um objeto.](./media/howto-use-properties/object.png)

O código a seguir mostra a definição de um tipo de propriedade de objeto. Esse objeto tem dois campos com tipos de cadeia de caracteres e número inteiro.

``` json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

## <a name="implement-read-only-properties"></a>Implementar propriedades somente leitura

Por padrão, as propriedades são somente leitura. Propriedades somente leitura significam que o valor da propriedade relatórios de dispositivo é atualizado para o aplicativo IoT Central do Azure. O aplicativo IoT Central do Azure não pode definir o valor de uma propriedade somente leitura.

O IoT Central do Azure usa dispositivos gêmeos para sincronizar valores de propriedade entre o dispositivo e o aplicativo de IoT Central do Azure. Os valores de propriedade do dispositivo usam propriedades relatadas do dispositivo gêmeo. Para obter mais informações, consulte [Device gêmeos](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins).

O trecho a seguir de um modelo de capacidade de dispositivo mostra a definição de um tipo de propriedade somente leitura:

``` json
{
  "@type": "Property",
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

As propriedades somente leitura são enviadas pelo dispositivo para o IoT Central do Azure. As propriedades são enviadas como carga JSON. Para obter mais informações, consulte [payloads](./concepts-telemetry-properties-commands.md).

Você pode usar o SDK do dispositivo IoT do Azure para enviar uma atualização de propriedade para o aplicativo de IoT Central do Azure.

As propriedades de dispositivo de telepropriedade podem ser enviadas para o aplicativo de IoT Central do Azure usando a seguinte função:

``` javascript
hubClient.getTwin((err, twin) => {
    const properties = {
        model: 'environmentalSensor1.0'
    };
    twin.properties.reported.update(properties, (err) => {
        console.log(err ? `error: ${err.toString()}` : `status: success` )
    });
});
```

Este artigo usa Node.js para simplificar a simplicidade. Para obter informações completas sobre exemplos de aplicativos de dispositivo, consulte os seguintes tutoriais:

* [Criar e conectar um aplicativo cliente ao seu aplicativo de IoT Central do Azure (Node.js)](tutorial-connect-device-nodejs.md)
* [Criar e conectar um aplicativo cliente ao seu aplicativo de IoT Central do Azure (Python)](tutorial-connect-device-python.md)

A exibição a seguir no aplicativo IoT Central do Azure mostra as propriedades que você pode ver. A exibição automaticamente torna a propriedade de **modelo do dispositivo** uma _propriedade de dispositivo somente leitura_.

![Captura de tela que mostra a exibição de uma propriedade somente leitura.](./media/howto-use-properties/read-only.png)

## <a name="implement-writable-properties"></a>Implementar propriedades graváveis

As propriedades graváveis são definidas por um operador no aplicativo IoT Central do Azure em um formulário. O Azure IoT Central envia a propriedade para o dispositivo. O Azure IoT Central espera uma confirmação do dispositivo.

O trecho a seguir de um modelo de capacidade de dispositivo mostra a definição de um tipo de propriedade gravável:

``` json
{
  "@type": "Property",
  "displayName": "Brightness Level",
  "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
  "name": "brightness",
  "writable": true,
  "schema": "long"
}
```

Um cliente de dispositivo deve enviar uma carga JSON parecida com o exemplo a seguir como uma propriedade relatada no dispositivo "r".

``` json
{ "Brightness Level": 2 }
```

Para definir e manipular as propriedades graváveis às quais seu dispositivo responde, você pode usar o seguinte código:

``` javascript
hubClient.getTwin((err, twin) => {
    twin.on('properties.desired.brightness', function(desiredBrightness) {
        console.log( `Received setting: ${desiredBrightness.value}` );
        var patch = {
            brightness: {
                value: desiredBrightness.value,
                ad: 'success',
                ac: 200,
                av: desiredBrightness.$version
            }
        }
        twin.properties.reported.update(patch, (err) => {
            console.log(err ? `error: ${err.toString()}` : `status: success` )
        });
    });
});
```

A mensagem de resposta deve incluir `ac` os `av` campos e. O campo `ad` é opcional. Consulte os trechos de código a seguir para obter exemplos:

* `ac` é um campo numérico que usa os valores na tabela a seguir.
* `av` é o número de versão enviado ao dispositivo.
* `ad` é uma descrição de cadeia de caracteres de opção.

| Valor | Rotular | Descrição |
| ----- | ----- | ----------- |
| `'ac': 200` | Concluído | A operação de alteração de propriedade foi concluída com êxito. |
| `'ac': 202` ou `'ac': 201` | Pending (Pendente) | A operação de alteração de propriedade está pendente ou em andamento. |
| `'ac': 4xx` | Erro | A alteração de propriedade solicitada não era válida ou teve um erro. |
| `'ac': 5xx` | Erro | O dispositivo apresentou um erro inesperado ao processar a alteração solicitada. |


Para obter mais informações sobre dispositivos gêmeos, consulte [configurar seus dispositivos de um serviço de back-end](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins).

Quando o operador define uma propriedade gravável no aplicativo IoT Central do Azure, o aplicativo usa uma propriedade FileUp desejada para enviar o valor para o dispositivo. Em seguida, o dispositivo responde usando uma propriedade relatada do dispositivo. Quando o Azure IoT Central recebe o valor da propriedade relatada, ele atualiza a exibição de propriedade com um status de **aceito**.

A exibição a seguir mostra as propriedades graváveis. Quando você inserir o valor e selecionar **salvar**, o status inicial será **pendente**. Quando o dispositivo aceita a alteração, o status é alterado para **aceito**.

![Captura de tela que mostra o status pendente.](./media/howto-use-properties/status-pending.png)

![Captura de tela que mostra a propriedade aceita.](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar as propriedades em seu aplicativo de IoT Central do Azure, consulte:

* [Cargas](concepts-telemetry-properties-commands.md)
* [Criar e conectar um aplicativo cliente ao seu aplicativo de IoT Central do Azure (Node.js)](tutorial-connect-device-nodejs.md)
