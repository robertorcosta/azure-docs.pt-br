---
title: Interagir com um dispositivo IoT Plug and Play Preview de uma solução de IoT do Azure   Microsoft Docs
description: Como um desenvolvedor de soluções, saiba como usar o SDK do serviço para interagir com dispositivos de IoT Plug and Play.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e349aadfd629202b1c8cdb5c53a88e0a6c2e06de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159210"
---
# <a name="connect-to-and-interact-with-an-iot-plug-and-play-preview-device"></a>Conectar-se a um dispositivo de IoT Plug and Play Preview e interagir com ele

Este guia de instruções mostra como usar os exemplos no SDK do serviço Node que mostram como a solução de IoT pode interagir com dispositivos de IoT Plug and Play Preview.

Se você ainda não tiver concluído o início rápido [conectar um dispositivo IoT Plug and Play à solução](quickstart-connect-pnp-device-solution-node.md), faça isso agora. O início rápido mostra como baixar e instalar o SDK e executar alguns dos exemplos.

Antes de executar os exemplos de serviço, abra um novo terminal, vá para a pasta raiz do repositório clonado, navegue até a pasta **digitaltwins/quickstarts/service** e, em seguida, execute o seguinte comando para instalar as dependências:

```cmd/sh
npm install
```

## <a name="run-the-service-samples"></a>Executar os exemplos de serviço

Use os exemplos a seguir para explorar as funcionalidades do SDK do serviço Node.js. Verifique se a variável de ambiente `IOTHUB_CONNECTION_STRING` está definida no shell que você usa:

### <a name="retrieve-a-digital-twin-and-list-the-interfaces"></a>Recuperar um gêmeo digital e listar as interfaces

**get_digital_twin.js** obtém o gêmeo digital associado ao dispositivo e imprime o respectivo componente na linha de comando. Ele não requer um exemplo de dispositivo em execução para ter sucesso.

**get_digital_twin_interface_instance.js** obtém uma única instância de interface de gêmeo digital associada ao dispositivo e a imprime na linha de comando. Ele não exige que o exemplo de dispositivo seja executado.

### <a name="get-and-set-properties-using-the-node-service-sdk"></a>Obter e definir propriedades usando o SDK do serviço Node

**update_digital_twin.js** atualiza uma propriedade gravável no dispositivo digital usando um patch completo. Se desejar, você poderá atualizar várias propriedades em várias interfaces. Para que esse procedimento seja bem-sucedido, o exemplo de dispositivo precisa estar em execução ao mesmo tempo. Em caso de sucesso, a aparência será de que o exemplo de dispositivo está imprimindo algo sobre a atualização de uma propriedade – o exemplo de serviço que está imprimindo um gêmeo digital atualizado no terminal.

### <a name="send-a-command-and-retrieve-the-response-using-the-node-service-sdk"></a>Enviar um comando e recuperar a resposta usando o SDK do serviço Node

**invoke_command.js** invoca um comando síncrono no dispositivo de gêmeo digital. Para que esse procedimento seja bem-sucedido, o exemplo de dispositivo precisa estar em execução ao mesmo tempo. O sucesso parece que o exemplo de dispositivo está imprimindo algo sobre a confirmação de um comando e o cliente de serviço imprimindo o resultado do comando no terminal.

### <a name="connect-to-the-public-repository-and-retrieve-a-model-definition-using-the-node-service-sdk"></a>Conectar-se ao repositório público e recuperar uma definição de modelo usando o SDK do serviço Node

Com as mesmas instruções usadas para os exemplos de serviço e de dispositivo, você precisa definir a variável de ambiente a seguir:

* `AZURE_IOT_MODEL_REPOSITORY_CONNECTION_STRING`

Você pode encontrar essa cadeia de conexão no [portal do Azure Certified para IoT** na guia **Cadeias de conexão** do seu **Repositório corporativo](https://preview.catalog.azureiotsolutions.com).

A cadeia de conexão tem a aparência do exemplo a seguir:

```text
HostName={repo host name};RepositoryId={repo ID};SharedAccessKeyName={repo key ID};SharedAccessKey={repo key secret}
```

Depois de você definir essas quatro variáveis de ambiente, execute o exemplo da mesma maneira que você executou os outros exemplos:

```cmd/sh
node model_repo.js
```

Este exemplo baixa a interface **ModelDiscovery** e imprime esse modelo no terminal.

### <a name="run-queries-in-iot-hub-based-on-capability-models-and-interfaces"></a>Executar consultas no Hub IoT com base em interfaces e modelos de funcionalidade

A linguagem de consulta do Hub IoT dá suporte a `HAS_INTERFACE` e `HAS_CAPABILITYMODEL` conforme mostrado nos exemplos a seguir:

```sql
select * from devices where HAS_INTERFACE('id without version', version)
```

```sql
select * from devices where HAS_CAPABILITYMODEL('id without version', version)
```

### <a name="creating-digital-twin-routes"></a>Criar rotas de entrelaçamento digital

A solução pode receber notificações de eventos de alteração de gêmeo digital. Para assinar essas notificações, use o [recurso de roteiros do Hub IoT](../iot-hub/iot-hub-devguide-endpoints.md) para enviar as notificações para um ponto de extremidade, tal como armazenamento de Blobs, Hubs de Eventos ou uma fila do Barramento de Serviço.

Para criar uma rota de gêmeos digitais:

1. No portal do Azure, vá para o recurso de Hub IoT.
1. Selecione **Roteamento de mensagens**.
1. Na guia **Rotas**, selecione **Adicionar**.
1. Insira um valor no campo **Nome** e escolha um **Ponto de extremidade**. Se você ainda não configurou um ponto de extremidade, selecione **Adicionar ponto de extremidade**.
1. Na lista suspensa **Fonte de dados**, selecione **Eventos de Alteração de Gêmeo Digital**.
1. Selecione **Salvar**.

O JSON a seguir mostra um exemplo de um evento de alteração de gêmeo digital:

```json
{
  "interfaces": {
    "urn_azureiot_ModelDiscovery_DigitalTwin": {
      "name": "urn_azureiot_ModelDiscovery_DigitalTwin",
      "properties": {
        "modelInformation": {
          "reported": {
            "value": {
              "modelId": "urn:domain:capabilitymodel:TestCapability:1",
              "interfaces": {
                "MyInterfaceFoo": "urn:domain:interfaces:FooInterface:1",
                "urn_azureiot_ModelDiscovery_DigitalTwin": "urn:azureiot:ModelDiscovery:DigitalTwin:1"
              }
            }
          }
        }
      }
    },
    "MyInterfaceFoo": {
      "name": "MyInterfaceFoo",
      "properties": {
        "property_1": { "desired": { "value": "value_1" } },
        "property_2": {
          "desired": { "value": 20 },
          "reported": {
            "value": 10,
            "desiredState": {
              "code": 200,
              "version": 22,
              "subCode": 400,
              "description": ""
            }
          }
        },
        "property_3": { "reported": { "value": "value_3" } }
      }
    }
  },
  "version": 4
}
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre soluções de serviço que interagem com seus dispositivos IoT Plug and Play, uma próxima etapa sugerida é aprender sobre a [Descoberta de modelo](concepts-model-discovery.md).
