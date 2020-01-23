---
title: Implantar módulos em escala usando o CLI do Azure-Azure IoT Edge
description: Usar a extensão IoT para a CLI do Azure para criar implantações automáticas para grupos dos dispositivos de IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fffe1ebda0103b3ed2cd8f76642ecb2967d23069
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510287"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Implantar e monitorar módulos do IoT Edge em escala usando a CLI do Azure

Crie uma **IOT Edge implantação automática** usando a interface de linha de comando do Azure para gerenciar implantações em andamento para vários dispositivos de uma vez. As implantações automáticas para IoT Edge fazem parte do recurso de [gerenciamento automático de dispositivo](/azure/iot-hub/iot-hub-automatic-device-management) do Hub IOT. As implantações são processos dinâmicos que permitem implantar vários módulos em vários dispositivos, acompanhar o status e a integridade dos módulos e fazer alterações quando necessário.

Para obter mais informações, consulte [entender IOT Edge implantações automáticas para dispositivos únicos ou em escala](module-deployment-monitoring.md).

Neste artigo, você configura a CLI do Azure e a extensão IoT. Em seguida, você aprende como implantar módulos em um conjunto de dispositivos IoT Edge e monitorar o progresso usando os comandos da CLI disponíveis.

## <a name="cli-prerequisites"></a>Pré-requisitos da CLI

* Um [Hub IoT](../iot-hub/iot-hub-create-using-cli.md) na assinatura do Azure.
* [Dispositivos do IoT Edge](how-to-register-device.md#prerequisites-for-the-azure-cli) com o runtime do IoT Edge instalado.
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) em seu ambiente. No mínimo, a versão da CLI do Azure deve ser 2.0.24 ou superior. Use `az --version` para validar. Esta versão dá suporte aos comandos da extensão az e introduz a estrutura de comandos Knack.
* A [extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos implantar, como os dados fluem entre os módulos e as propriedades desejadas dos módulos gêmeos. Para obter mais informações, consulte [saiba como implantar módulos e estabelecer rotas no IOT Edge](module-composition.md).

Para implantar módulos usando a CLI do Azure, salve o manifesto de implantação localmente como um arquivo .txt. Você usará o caminho do arquivo na próxima seção ao executar o comando para aplicar a configuração ao seu dispositivo.

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

## <a name="layered-deployment"></a>Implantação em camadas

Implantações em camadas são um tipo de implantação automática que pode ser empilhada umas sobre as outras. Para obter mais informações sobre implantações em camadas, consulte [entender IOT Edge implantações automáticas para dispositivos únicos ou em escala](module-deployment-monitoring.md).

Implantações em camadas podem ser criadas e gerenciadas com o CLI do Azure como qualquer implantação automática, com apenas algumas diferenças. Depois que uma implantação em camadas é criada, o mesmo CLI do Azure funciona para implantações em camadas da mesma forma que qualquer implantação. Para criar uma implantação em camadas, adicione o sinalizador `--layered` ao comando criar.

A segunda diferença está na construção do manifesto de implantação. Embora a implantação automática padrão deva conter os módulos de tempo de execução do sistema além de quaisquer módulos de usuário, as implantações em camadas só podem conter módulos de usuário. Em vez disso, as implantações em camadas precisam de uma implantação automática padrão também em um dispositivo, para fornecer os componentes necessários de cada dispositivo IoT Edge, como os módulos de tempo de execução do sistema.

Aqui está um manifesto de implantação em camadas básico com um módulo como um exemplo:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired.modules.SimulatedTemperatureSensor": {
          "settings": {
            "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": ""
          },
          "type": "docker",
          "status": "running",
          "restartPolicy": "always",
          "version": "1.0"
        }
      },
      "$edgeHub": {
        "properties.desired.routes.upstream": "FROM /messages/* INTO $upstream"
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

O exemplo anterior mostrou uma configuração de implantação em camadas `properties.desired` para um módulo. Se essa implantação em camadas tiver como destino um dispositivo em que o mesmo módulo já foi aplicado, ele substituirá as propriedades desejadas existentes. Para atualizar, em vez de substituir as propriedades desejadas, você pode definir uma nova subseção. Por exemplo:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Para obter mais informações sobre como configurar o módulo gêmeos em implantações em camadas, consulte [implantação em camadas](module-deployment-monitoring.md#layered-deployment)

## <a name="identify-devices-using-tags"></a>Identificar dispositivos usando marcações

Antes de criar uma implantação, você precisa conseguir especificar quais dispositivos você deseja afetar. O Azure IoT Edge identifica os dispositivos usando **marcações** no dispositivo gêmeo. Cada dispositivo pode ter várias marcas que você define de qualquer forma que faça sentido para sua solução. Por exemplo, se você gerencia um campus de edifícios inteligentes, poderá adicionar as seguintes marcações a um dispositivo:

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

Para obter mais informações sobre tags e gêmeos de dispositivos, consulte [Entender e usar gêmeos de dispositivos no Hub IoT](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Criar uma implantação

Você implanta módulos nos dispositivos de destino criando uma implantação que consiste no manifesto de implantação, bem como outros parâmetros.

Use o comando [AZ IOT Edge Deployment Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) para criar uma implantação:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Use o mesmo comando com o sinalizador `--layered` para criar um deploymet em camadas.

O comando de criação de implantação usa os seguintes parâmetros:

* **--em camadas** – um sinalizador opcional para identificar a implantação como uma implantação em camadas.
* **--deployment-id** - O nome da implantação que será criada no Hub IoT. Dê à sua implantação um nome exclusivo com até 128 letras minúsculas. Evite usar espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`. Parâmetro necessário.
* **--content** - Caminho do arquivo para o manifesto de implantação JSON. Parâmetro necessário.
* **--hub-name** - Nome do hub IoT no qual a implantação será criada. O hub deve estar na assinatura atual. Altere sua assinatura atual com o comando `az account set -s [subscription name]`.
* **--labels** - Adicione rótulos para ajudar a acompanhar as implantações. Rótulos são pares de Nome e Valor que descrevem a implantação. Os rótulos usam a formatação JSON para os nomes e valores. Por exemplo, `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-condition** - Insira uma condição de destino para determinar quais dispositivos serão segmentados com essa implantação. A condição é baseada nas marcas de dispositivo ou nas propriedades relatadas do dispositivo e deve corresponder ao formato da expressão. Por exemplo, `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--priority** - Um inteiro positivo. No caso de duas ou mais implantações serem direcionadas ao mesmo dispositivo, será aplicada a implantação com o maior valor numérico para Prioridade.
* **--métricas** – crie métricas que consultam as propriedades relatadas edgeHub para acompanhar o status de uma implantação. As métricas usam a entrada JSON ou um FilePath. Por exemplo, `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

## <a name="monitor-a-deployment"></a>Monitorar uma implantação

Use o comando [AZ IOT Edge Deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) para exibir os detalhes de uma única implantação:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

O comando Deployment show usa os seguintes parâmetros:

* **--deployment-id** - O nome da implantação que existe no hub IoT. Parâmetro necessário.
* **--hub-name** - Nome do hub IoT no qual a implantação existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`

Inspecione a implantação na janela de comando. A propriedade de **métricas** lista uma contagem de cada métrica que é avaliada por cada Hub:

* **targetedCount** - Uma métrica do sistema que especifica o número de dispositivos gêmeos no Hub IoT que correspondem à condição de direcionamento.
* **appliedCount** - Uma métrica de sistema especifica o número de dispositivos que tiveram o conteúdo de implantação aplicado aos módulos gêmeos no Hub IoT.
* **reportedSuccessfulCount** -uma métrica de dispositivo que especifica o número de dispositivos IOT Edge na implantação que relataram o sucesso do tempo de execução do cliente IOT Edge.
* **reportedFailedCount** -uma métrica de dispositivo que especifica o número de dispositivos IOT Edge na implantação que relatam a falha do tempo de execução do cliente IOT Edge.

Você pode mostrar uma lista de IDs de dispositivo ou objetos para cada uma das métricas usando o comando [AZ IOT Edge Deployment show-Metric](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) :

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

O comando Deployment show-Metric usa os seguintes parâmetros:

* **--deployment-id** - O nome da implantação que existe no hub IoT.
* **--Metric-ID** -o nome da métrica para a qual você deseja ver a lista de IDs de dispositivo, por exemplo `reportedFailedCount`.
* **--hub-name** - Nome do hub IoT no qual a implantação existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`.

## <a name="modify-a-deployment"></a>Modificar uma implantação

Quando você modifica uma implantação, as alterações são replicadas imediatamente para todos os dispositivos direcionados.

Se você atualizar a condição de destino, ocorrerão as seguintes atualizações:

* Caso um dispositivo não tenha atendido à condição de destino antiga, mas atenda à nova condição de destino e essa implantação tiver a prioridade mais alta para o dispositivo, essa implantação será aplicada ao dispositivo.
* Caso um dispositivo que executa essa implantação no momento não atenda mais à condição de destino, ele desinstalará essa implantação e usará a próxima implantação com a prioridade mais alta.
* Caso um dispositivo que executa essa implantação no momento não atenda mais à condição de destino e não atenda à condição de destino de todas as outras implantações, nenhuma alteração ocorrerá no dispositivo. O dispositivo continua executando seus módulos atuais em seu estado atual, mas não é mais gerenciado como parte dessa implantação. Depois que ele atende à condição de destino de qualquer outra implantação, ele desinstala essa implantação e usa a nova.

Você não pode atualizar o conteúdo de uma implantação, que inclui os módulos e rotas definidos no manifesto de implantação. Se você quiser atualizar o conteúdo de uma implantação, faça isso criando uma nova implantação que se destina aos mesmos dispositivos com uma prioridade mais alta. Você pode modificar determinadas propriedades de um módulo existente, incluindo a condição de destino, os rótulos, as métricas e a prioridade.

Use o comando [AZ IOT Edge Deployment Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) para atualizar uma implantação:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

O comando de atualização de implantação usa os seguintes parâmetros:

* **--deployment-id** - O nome da implantação que existe no hub IoT.
* **--hub-name** - Nome do hub IoT no qual a implantação existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`
* **--set** - Atualize uma propriedade na implantação. Você pode atualizar as seguintes propriedades:
  * targetCondition - por exemplo `targetCondition=tags.location.state='Oregon'`
  * rótulos
  * priority
* **--Add** -adiciona uma nova propriedade à implantação, incluindo condições ou rótulos de destino.
* **--Remove** -remove uma propriedade existente, incluindo condições ou rótulos de destino.

## <a name="delete-a-deployment"></a>Excluir uma implantação

Quando você exclui uma implantação, todos os dispositivos usam sua próxima implantação com a prioridade mais alta. Se os dispositivos não atenderem à condição de destino de qualquer outra implantação, os módulos não serão removidos quando a implantação for excluída.

Use o comando de [exclusão de implantação AZ IOT Edge](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) para excluir uma implantação:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

O comando de exclusão de implantação usa os seguintes parâmetros:

* **--deployment-id** - O nome da implantação que existe no hub IoT.
* **--hub-name** - Nome do hub IoT no qual a implantação existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre [a implantação de módulos em dispositivos IOT Edge](module-deployment-monitoring.md).
