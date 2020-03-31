---
title: Implantar módulos em escala usando o Visual Studio Code - Azure IoT Edge
description: Use a extensão IoT do Visual Studio Code para criar implantações automáticas para grupos de dispositivos IoT Edge.
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 08299a589dc6e8f768cba7ef976e109ef1fb69d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774127"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>Implantar módulos IoT Edge em escala usando o Visual Studio Code

Você pode criar uma **implantação automática do IoT Edge** usando o Visual Studio Code para gerenciar implantações contínuas para muitos dispositivos ao mesmo tempo. As implantações automáticas para IoT Edge fazem parte do recurso automático de gerenciamento de [dispositivos](/azure/iot-hub/iot-hub-automatic-device-management) do IoT Hub. Implantações são processos dinâmicos que permitem implantar vários módulos em vários dispositivos. Você também pode acompanhar o estado e a saúde dos módulos, e fazer alterações quando necessário.

Para obter mais informações, consulte [Entenda implantações automáticas do IoT Edge para dispositivos únicos ou em escala](module-deployment-monitoring.md).

Neste artigo, você criou o Visual Studio Code e a extensão IoT. Em seguida, você aprende como implantar módulos em um conjunto de dispositivos IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

* Um [hub de IoT](../iot-hub/iot-hub-create-through-portal.md) em sua assinatura do Azure.
* Um [Dispositivo do IoT Edge](how-to-register-device.md#register-with-visual-studio-code) com o runtime do IoT Edge instalado.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Ferramentas IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) para Visual Studio Code.

## <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o Hub IoT

Você pode usar as extensões Azure IoT para Visual Studio Code para fazer operações com o seu Hub. Para que essas operações funcionem, você precisa entrar na sua conta do Azure e selecionar o hub de IoT em que você está trabalhando.

1. No Visual Studio Code, abra a exibição do **Explorer**.

1. Na parte inferior do Explorer, expanda a seção **Azure IoT Hub.**

1. Clique no **cabeçalho da** seção **Azure IoT Hub.** Se você não visualizar as reticências, passe o mouse sobre o cabeçalho.

1. Escolha **Selecionar Hub IoT**.

1. Se você não estiver conectado à sua conta do Azure, siga as instruções para fazê-lo.

1. Selecione sua assinatura do Azure.

1. Selecione seu Hub IoT.

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos serão implantados. Ele também descreve como os dados fluem entre os módulos e as propriedades desejadas dos gêmeos do módulo. Para obter mais informações, consulte [Saiba como implantar módulos e estabelecer rotas no IoT Edge](module-composition.md).

Para implantar módulos usando o Visual Studio Code, salve o manifesto de implantação localmente como um arquivo .JSON. Você precisará fornecer sua localização quando executar o comando para aplicar a configuração ao seu dispositivo.

A seguir, é apresentado um manifesto básico de implantação com um módulo como um exemplo:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
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
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

Se você precisar determinar quais dispositivos IoT Edge você pode configurar atualmente, execute o comando **IoT Edge: Get Device Info.**

## <a name="identify-devices-with-target-conditions"></a>Identificar dispositivos com condições de destino

Para identificar os dispositivos IoT Edge que devem receber a implantação, você deve especificar uma condição de destino. Uma condição de destino é atendida quando os critérios especificados são correspondidos por um dispositivoId, valor de tag ou um valor de propriedade relatado.

Você configura tags no dispositivo twin. Aqui está um exemplo de um dispositivo gêmeo que tem tags:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Este dispositivo receberá uma implantação se a condição de destino para a implantação contiver `tag.location.building = '20'`uma expressão que corresponda a um dos valores da tag, tais como .

Se você quiser segmentar um dispositivo específico, independentemente `deviceId` de suas marcas ou outros valores, basta especificar a condição de destino.

Aqui estão mais alguns exemplos:

* deviceId ='linuxprod1'
* deviceId = 'linuxprod1' OR deviceId = 'linuxprod2' OR deviceId = 'linuxprod3'
* tags.environment ='prod'
* tags.environment = 'prod' E tags.location = 'westus2'
* tags.environment = 'prod' OR tags.location = 'westus2'
* tags.operator = 'John' E tags.environment = 'prod' E NÃO dispositivoId = 'linuxprod1'

Veja [a condição do alvo](module-deployment-monitoring.md#target-condition) para detalhes. Para obter mais informações sobre dispositivos gêmeos e marcações, consulte [Entender e usar dispositivos gêmeos no Hub IoT](../iot-hub/iot-hub-devguide-device-twins.md).

### <a name="edit-the-device-twin"></a>Edite o dispositivo gêmeo

Você pode editar o dispositivo duplo no Visual Studio Code para configurar as tags. No menu **Exibir,** selecione **Paleta de comando** e execute o comando **IoT Edge: Edit Device Twin.** Selecione o dispositivo IoT Edge e o dispositivo é exibido.

Neste exemplo, nenhuma marca foi definida. Substitua a `"tags": {}` seção vazia atual pela definição de suas próprias tags.

```json
{
    "deviceId": "myEdgeDevice",
    "etag": "AAAAAAAAAAE=",
    "deviceEtag": "NTgwMDg5MDAz",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        }
    },
    "capabilities": {
        "iotEdge": true
    },
    "deviceScope": "ms-azure-iot-edge://myEdgeDevice-637131779299315265",
    "tags": {}
}
```

Depois de salvar o arquivo local, execute o comando **IoT Edge: Update Device Twin.**

## <a name="create-deployment-at-scale"></a>Criar implantação em escala

Depois de configurar o manifesto de implantação e as tags configuradas no dispositivo gêmeo, você está pronto para implantar.

1. No menu **Exibir,** selecione **Paleta de comando** e selecione a borda **Azure IoT: Crie a implantação no** comando Scale.

1. Navegue até o arquivo JSON do manifesto de implantação que você quer usar e clique em **Selecionar manifesto de implantação do Edge**.

1. Forneça valores conforme solicitado, começando com o **id de implantação**.

   ![Especifique um id de implantação](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   Especificar valores para esses parâmetros:

  | Parâmetro | Descrição |
  | --- | --- |
  | ID de implantação | O nome da implantação que será criada no hub ioT. Dê à sua implantação um nome exclusivo com até 128 letras minúsculas. Evite usar espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`. |
  | Condição de destino | Digite uma condição de destino para determinar quais dispositivos serão direcionados com essa implantação.A condição se baseia nas marcas do dispositivo gêmeo ou propriedades reportadas do dispositivo gêmeo e deve corresponder ao formato da expressão.Por exemplo, `tags.environment='test' and properties.reported.devicemodel='4000x'`. |
  | Prioridade |  Um número inteiro positivo. Se duas ou mais implantações forem direcionadas ao mesmo dispositivo, a implantação com o maior valor numérico para Prioridade será aplicada. |

  Depois de especificar a prioridade, o terminal deve exibir saída semelhante à seguinte representação:

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>Monitoramento e modificação de implantações

Use o [Azure CLI](how-to-deploy-monitor-cli.md#monitor-a-deployment) ou o [portal Azure](how-to-deploy-monitor.md#monitor-a-deployment) para monitorar, modificar e excluir implantações. Ambos fornecem métricas sobre suas implantações.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [a implantação de módulos para dispositivos IoT Edge](module-deployment-monitoring.md).
