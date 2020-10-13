---
title: Implantar módulos e rotas com manifestos de implantação – Azure IoT Edge
description: Saiba como um manifesto de implantação declara quais módulos para implantar, como implantá-las e como criar rotas de mensagens entre eles.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7a9f4f165f457dfb902a4c0ecce3f4a9b13e2ec8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91611530"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Aprenda a implantar módulos e estabelecer rotas no IoT Edge

Cada dispositivo IoT Edge executa ao menos dois módulos: $edgeAgent e $edgeHub, que fazem parte do runtime do IoT Edge. O dispositivo do IoT Edge pode executar vários módulos adicionais para qualquer quantidade de processos. Use um manifesto de implantação para informar ao seu dispositivo quais módulos instalar e como configurá-los para que eles funcionem em conjunto.

O *manifesto de implantação* é um documento JSON que descreve:

* O módulo gêmeo do **agente do IoT Edge**, que inclui três componentes:
  * A imagem de contêiner para cada módulo que é executado no dispositivo.
  * As credenciais para acessar os registros de contêiner privado que contêm as imagens de módulo.
  * As instruções sobre como cada módulo deve ser criado e gerenciado.
* O módulo gêmeo do **hub do IoT Edge**, que inclui como as mensagens fluem entre os módulos e, eventualmente, o Hub IoT.
* As propriedades desejadas de qualquer módulo gêmeo adicional (opcional).

Todos os dispositivos IoT Edge precisam ser configurados com um manifesto de implantação. Um runtime do IoT Edge recém-instalado relatará um código de erro até ser configurado com um manifesto válido.

Nos tutoriais do Azure IoT Edge, você pode criar um manifesto de implantação por meio de um assistente no portal do Azure IoT Edge. Você também pode aplicar um manifesto de implantação por meio de programação usando REST ou o SDK do serviço Hub IoT. Para saber mais, veja [Noções básicas sobre implantações do IoT Edge](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Criar um manifesto de implantação

Em um alto nível, um manifesto de implantação é uma lista de módulos gêmeos que são configurados com suas propriedades desejadas. Um manifesto de implantação informa um dispositivo IoT Edge (ou um grupo de dispositivos), quais módulos instalar e como configurá-los. Manifestos de implantação incluem as *propriedades desejadas* para cada módulo gêmeo. Dispositivos do IoT Edge relatam de volta *as propriedades relatadas* para cada módulo.

Dois módulos são necessários em cada manifesto de implantação: `$edgeAgent`, e `$edgeHub`. Esses módulos são parte do runtime do IoT Edge que gerencia o dispositivo IoT Edge e os módulos em execução nele. Para obter mais informações sobre esses módulos, consulte [Entender o runtime do Azure IoT Edge e sua arquitetura](iot-edge-runtime.md).

Além dos dois módulos de tempo de execução, você pode adicionar até 50 módulos próprios para serem executados em um dispositivo IoT Edge.

Um manifesto de implantação que contém apenas o runtime do IoT Edge (edgeAgent e edgeHub) é válido.

Manifestos de implantação seguem essa estrutura:

```json
{
    "modulesContent": {
        "$edgeAgent": { // required
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": { //required
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "module1": {  // optional
            "properties.desired": {
                // desired properties of module1
            }
        },
        "module2": {  // optional
            "properties.desired": {
                // desired properties of module2
            }
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Configurar módulos

Definir como o runtime do IoT Edge instala os módulos na sua implantação. O agente do IoT Edge é o componente de runtime que gerencia a instalação, atualizações e relatório de status para um dispositivo IoT Edge. Portanto, o módulo gêmeo $edgeAgent contém a configuração e as informações de gerenciamento de todos os módulos. Essas informações incluem os parâmetros de configuração do próprio agente do IoT Edge.

Para obter uma lista completa das propriedades que podem ou precisam ser incluídas, consulte [Propriedades do agente do IoT Edge e do hub do IoT Edge](module-edgeagent-edgehub.md).

As propriedades de $edgeAgent seguem esta estrutura:

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
            "settings":{
                "registryCredentials":{ // give the edge agent access to container images that aren't public
                    }
                }
            }
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "module1": { // optional
                // configuration and management details
            },
            "module2": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Declarar rotas

O hub do IoT Edge gerencia a comunicação entre módulos, o Hub IoT e todos os dispositivos de folha. Portanto, o módulo gêmeo $edgeHub contém propriedade desejada chamada *rotas* que declara como as mensagens são passadas em uma implantação. Você pode ter várias rotas dentro da mesma implantação.

As rotas são declaradas nas propriedades desejadas do **$edgeHub** com a seguinte sintaxe:

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "route1": "FROM <source> WHERE <condition> INTO <sink>",
            "route2": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Cada rota precisa de uma origem e um coletor, mas a condição é uma parte opcional que você pode usar para filtrar mensagens.

### <a name="source"></a>Fonte

A origem especifica de onde as mensagens são provenientes. O IoT Edge pode rotear as mensagens de módulos ou dispositivos folha.

Usando os SDKs de IoT, os módulos podem declarar filas de saída específicas para as mensagens deles usando a classe ModuleClient. As filas de saída não são necessárias, mas são úteis para gerenciar várias rotas. Os dispositivos folha podem usar a classe DeviceClient dos SDKs de IoT para enviar mensagens para dispositivos de gateway do IoT Edge da mesma forma que eles enviariam mensagens ao Hub IoT. Para obter mais informações, consulte [Noções básicas e uso de SDKs do Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md).

A propriedade da fonte pode ser qualquer um dos valores a seguir:

| Fonte | Descrição |
| ------ | ----------- |
| `/*` | Todas as mensagens do dispositivo para a nuvem ou gêmeo alteração as notificações de qualquer módulo ou dispositivo de folha |
| `/twinChangeNotifications` | Qualquer alteração de gêmeo (propriedades relatadas) proveniente de qualquer dispositivo de módulo ou folha |
| `/messages/*` | Qualquer mensagem de dispositivo para nuvem enviada por um módulo por meio de alguma ou nenhuma saída ou por um dispositivo folha |
| `/messages/modules/*` | Qualquer mensagem de dispositivo para nuvem enviada por um módulo por meio de algumas ou nenhuma saída |
| `/messages/modules/<moduleId>/*` | Qualquer mensagem de dispositivo para nuvem enviada por um módulo específico por meio de algumas ou nenhuma saída |
| `/messages/modules/<moduleId>/outputs/*` | Qualquer mensagem de dispositivo para nuvem enviada por um módulo específico por meio de alguma saída |
| `/messages/modules/<moduleId>/outputs/<output>` | Qualquer mensagem de dispositivo para nuvem enviada por um módulo específico por meio de uma saída específica |

### <a name="condition"></a>Condição

A condição é opcional em uma declaração de rota. Se você deseja transmitir todas as mensagens da fonte para o coletor, omita completamente a cláusula **WHERE**. Ou você pode usar a [linguagem de consulta de Hub IoT](../iot-hub/iot-hub-devguide-routing-query-syntax.md) para filtrar determinadas mensagens ou tipos de mensagem que satisfazem a condição. As rotas do IoT Edge não dão suporte a mensagens de filtragem com base em propriedades ou marcas do gêmeo.

As mensagens que passam entre módulos no IoT Edge são formatadas da mesma forma que as mensagens que passam entre seus dispositivos e o Hub IoT do Azure. Todas as mensagens são formatadas como JSON e têm os parâmetros **systemProperties**, **appProperties** e **body**.

Você pode criar consultas baseadas nos três parâmetros com a seguinte sintaxe:

* Propriedades do sistema: `$<propertyName>` ou `{$<propertyName>}`
* Propriedades do aplicativo: `<propertyName>`
* Propriedades do corpo: `$body.<propertyName>`

Para obter exemplos sobre como criar consultas para propriedades de mensagem, consulte [Expressões de consulta de rotas de mensagem do dispositivo para a nuvem](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Um exemplo específico do IoT Edge é quando você deseja filtrar as mensagens que chegaram a um dispositivo de gateway vindo de um dispositivo de folha. Mensagens que chegam de módulos incluem uma propriedade do sistema chamada **connectionModuleId**. Então se você quiser rotear mensagens de dispositivos de folha diretamente ao Hub IoT, use a rota a seguir para excluir mensagens de módulo:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Coletor

O coletor define onde as mensagens são enviadas. Somente os módulos e o Hub IoT podem receber mensagens. As mensagens não podem ser roteadas para outros dispositivos. Não há nenhuma opção de curinga na propriedade do coletor.

A propriedade da fonte pode ser qualquer um dos valores a seguir:

| Coletor | Descrição |
| ---- | ----------- |
| `$upstream` | Enviar a mensagem para o Hub IoT |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Enviar a mensagem para uma entrada específica de um módulo específico |

IoT Edge fornece garantias at-least-once. O hub do IoT Edge armazena mensagens localmente caso uma rota não consiga entregar a mensagem para o coletor. Por exemplo, se o hub do IoT Edge não puder se conectar ao Hub IoT ou se o módulo de destino não estiver conectado.

O hub do IoT Edge armazena as mensagens até a hora especificada na propriedade `storeAndForwardConfiguration.timeToLiveSecs` das [propriedades desejadas do hub do IoT Edge](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Definir ou atualizar as propriedades desejadas

O manifesto de implantação especifica as propriedades desejadas para cada módulo implantado ao dispositivo do IoT Edge. Quando as propriedades no manifesto de implantação substituem qualquer propriedade desejada atualmente no gêmeo do módulo.

Se você não especificar as propriedades desejadas de um gêmeo de módulo no manifesto de implantação, o Hub IoT não modifica o gêmeo de módulo de qualquer forma e você poderá definir as propriedades desejadas programaticamente. Em vez disso, você pode definir as propriedades desejadas programaticamente.

Os mesmos mecanismos que permitem que você modifique dispositivos gêmeos são usados para modificar módulos gêmeos. Para obter mais informações, consulte o [guia do desenvolvedor do módulo gêmeo](../iot-hub/iot-hub-devguide-module-twins.md).

## <a name="deployment-manifest-example"></a>Exemplo de manifesto de implantação

O exemplo a seguir mostra o que pode ser a aparência de um documento de manifesto de implantação válido.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "env": {
              "tempLimit": {"value": "100"}
            },
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "sensorToFilter": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Próximas etapas

* Para obter uma lista completa das propriedades que podem ou precisam ser incluídas em $edgeAgent e $edgeHub, consulte [Propriedades do agente do IoT Edge e do hub do IoT Edge](module-edgeagent-edgehub.md).

* Agora que sabe como os módulos do IoT Edge são usados, [Entender os requisitos e as ferramentas para desenvolvimento de módulos do IoT Edge](module-development.md).
