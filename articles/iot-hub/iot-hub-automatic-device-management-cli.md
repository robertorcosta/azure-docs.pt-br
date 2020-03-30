---
title: Gerenciamento automático de dispositivos em escala com o Azure IoT Hub (CLI) | Microsoft Docs
description: Use configurações automáticas do Azure IoT Hub para gerenciar vários dispositivos ou módulos de IoT
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: chrisgre
ms.openlocfilehash: 748f3e09fd03a6f37954c8dfaf4b6ae9144384bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235610"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Gerenciamento automático de dispositivos ioT e módulos usando o Azure CLI

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

O gerenciamento automático de dispositivos no Azure IoT Hub automatiza muitas das tarefas repetitivas e complexas de gerenciar grandes frotas de dispositivos. Com o gerenciamento automático de dispositivos, você pode segmentar um conjunto de dispositivos com base em suas propriedades, definir uma configuração desejada e, em seguida, deixar o IoT Hub atualizar os dispositivos quando eles entrarem em escopo. Esta atualização é feita usando uma _configuração automática do dispositivo_ ou _configuração automática do módulo,_ que permite resumir a conclusão e a conformidade, lidar com a fusão e conflitos e implementar configurações em uma abordagem em fases.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

O gerenciamento automático do dispositivo funciona atualizando um conjunto de gêmeos de dispositivos ou gêmeos de módulo com propriedades desejadas e relatando um resumo baseado em propriedades relatadas por gêmeos.  Ele introduz uma nova classe e um documento JSON chamado *configuração* que tem três partes:

* A **condição de destino** define o escopo de gêmeos de dispositivo ou gêmeos de módulo a ser atualizado. A condição de destino é especificada como uma consulta em marcas de dispositivos gêmeos e/ou propriedades reportadas.

* O **conteúdo de destino** define as propriedades desejadas a serem adicionadas ou atualizadas nos gêmeos do dispositivo-alvo ou gêmeos do módulo. O conteúdo inclui um caminho para a seção de propriedades desejadas a ser alterado.

* As **métricas** definem as contagens de resumos de vários estados de configuração como **êxito**, **em andamento**, e **erro**. As métricas personalizadas são especificadas como consultas em propriedades relatadas por gêmeos.  As métricas do sistema são as métricas padrão que medem o status de atualização dupla, como o número de gêmeos que são alvo e o número de gêmeos que foram atualizados com sucesso.

As configurações automáticas são executadas pela primeira vez logo após a configuração ser criada e, em seguida, em intervalos de cinco minutos. As consultas métricas são executadas cada vez que a configuração automática é executada.

## <a name="cli-prerequisites"></a>Pré-requisitos da CLI

* Um [hub de IoT](../iot-hub/iot-hub-create-using-cli.md) em sua assinatura do Azure. 

* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) em seu ambiente. No mínimo, sua versão Azure CLI deve ser 2.0.70 ou superior. Use `az –-version` para validar. Esta versão dá suporte aos comandos da extensão az e introduz a estrutura de comandos Knack. 

* A [extensão IoT para Azure CLI](https://github.com/Azure/azure-cli).

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="implement-twins"></a>Implementar gêmeos

As configurações automáticas de dispositivo requerem o uso de dispositivos gêmeos para sincronizar o estado entre a nuvem e os dispositivos.  Para obter mais informações, consulte [Entender e usar gêmeos de dispositivos no IoT Hub](iot-hub-devguide-device-twins.md).

As configurações automáticas do módulo exigem o uso de gêmeos de módulo para sincronizar o estado entre a nuvem e os módulos. Para obter mais informações, consulte [Entender e usar gêmeos de módulo no IoT Hub](iot-hub-devguide-module-twins.md).

## <a name="use-tags-to-target-twins"></a>Use tags para atingir gêmeos

Antes de criar uma configuração, você deve especificar quais dispositivos ou módulos deseja afetar. O Azure IoT Hub identifica dispositivos e usando tags no dispositivo twin, e identifica módulos usando tags no módulo gêmeo. Cada dispositivo ou módulo pode ter várias tags, e você pode defini-los de qualquer maneira que faça sentido para sua solução. Por exemplo, se você gerenciar dispositivos em diferentes locais, adicione as seguintes tags a um dispositivo gêmeo:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>Definir as métricas e o conteúdo de destino

O conteúdo de destino e as consultas métricas são especificados como documentos JSON que descrevem as propriedades desejadas do dispositivo gêmeo ou módulo duplo para definir e relatar propriedades para medir.  Para criar uma configuração automática usando o Azure CLI, salve o conteúdo de destino e as métricas localmente como arquivos .txt. Você usa os caminhos de arquivo em uma seção posterior quando executa o comando para aplicar a configuração ao seu dispositivo.

Aqui está uma amostra básica de conteúdo de destino para uma configuração automática do dispositivo:

```json
{
  "content": {
    "deviceContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

As configurações automáticas do módulo se `moduleContent` comportam de forma muito semelhante, mas você mira em vez de `deviceContent`.

```json
{
  "content": {
    "moduleContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

A seguir estão alguns exemplos de consultas de métricas:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

As consultas métricas para módulos também são semelhantes às `moduleId` `devices.modules`consultas para dispositivos, mas você seleciona para . Por exemplo:  

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>Criar uma configuração

Você configura dispositivos de destino criando uma configuração que consiste de métricas e conteúdo de destino. 

Use o comando a seguir para criar uma configuração:

```azurecli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id** - O nome da configuração que será criada no Hub IoT. Dê um nome exclusivo à sua configuração de até 128 letras minúsculas. Evite usar espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`.

* --**labels** - Adicione rótulos para ajudar a rastrear a configuração. Rótulos são pares de Nome e Valor que descrevem a implantação. Por exemplo, `HostPlatform, Linux` ou `Version, 3.0.1`

* --**content** - JSON em linha ou caminho de arquivo para o conteúdo de destino a ser configurado como propriedades desejadas do gêmeo. 

* --**hub-name** - Nome do Hub IoT no qual a configuração será criada. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`

* --**condição de destino** - Digite uma condição de destino para determinar quais dispositivos ou módulos serão direcionados com esta configuração.Para a configuração automática do dispositivo, a condição é baseada em tags duplas do dispositivo ou propriedades duplas desejadas pelo dispositivo e deve corresponder ao formato de expressão.Por exemplo, `tags.environment='test'` ou `properties.desired.devicemodel='4000x'`.Para a configuração automática do módulo, a condição é baseada em tags gêmeas de módulo ou propriedades desejadas pelo módulo twin.. Por exemplo, `from devices.modules where tags.environment='test'` ou `from devices.modules where properties.reported.chillerProperties.model='4000x'`.

* --**priority** - Um inteiro positivo. No caso de duas ou mais configurações serem direcionadas para o mesmo dispositivo ou módulo, a configuração com o maior valor numérico para Prioridade será aplicada.

* --**metrics** - Caminho para as consultas de métricas. As métricas fornecem contagens sumárias dos vários estados que um dispositivo ou módulo pode relatar após a aplicação do conteúdo da configuração. Por exemplo, você pode criar uma métrica para alterações de configurações pendentes, uma métrica de erros e uma métrica para alterações de configurações bem-sucedidas. 

## <a name="monitor-a-configuration"></a>Monitorar uma configuração

Use o comando a seguir para exibir o conteúdo de uma configuração:

```azurecli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** - O nome da configuração que existe no Hub IoT.

* --**hub-name** - Nome do Hub IoT no qual a configuração existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`

Inspecione a configuração na janela de comando.A propriedade **Metrics** lista uma contagem para cada métrica avaliada por cada hub:

* **targetedCount** - Uma métrica do sistema que especifica o número de gêmeos de dispositivo ou gêmeos de módulo no IoT Hub que correspondem à condição de segmentação.

* **appliedCount** - Uma métrica do sistema especifica o número de dispositivos ou módulos que tiveram o conteúdo de destino aplicado.

* **Sua métrica personalizada** - Todas as métricas definidas são métricas de usuário.

Você pode mostrar uma lista de IDs de dispositivo, IDs de módulo ou objetos para cada uma das métricas usando o seguinte comando:

```azurecli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** - O nome da implantação que existe no hub IoT.

* --**id métrico** - O nome da métrica para a qual você deseja ver a lista `appliedCount`de IDs de dispositivo ou IDs de módulo, por exemplo .

* --**hub-name** - Nome do hub IoT no qual a implantação existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`.

* --**metric-type** - O tipo de métrica pode ser `system` ou `user`.  Métricas de sistema são `targetedCount` e `appliedCount`. Todas as outras métricas são métricas do usuário.

## <a name="modify-a-configuration"></a>Modificar uma configuração

Quando você modifica uma configuração, as alterações são replicadas imediatamente para todos os dispositivos direcionados. 

Se você atualizar a condição de destino, ocorrerão as seguintes atualizações:

* Se um gêmeo não cumpriu a antiga condição de alvo, mas atende à nova condição de destino e esta configuração é a maior prioridade para esse gêmeo, então essa configuração é aplicada. 

* Se um gêmeo atualmente executando essa configuração não atender mais à condição de destino, as configurações da configuração serão removidas e o gêmeo será modificado pela próxima configuração de alta prioridade. 

* Se um gêmeo atualmente executando essa configuração não atender à condição de destino e não atender à condição de destino de qualquer outra configuração, então as configurações da configuração serão removidas e nenhuma outra alteração será feita no gêmeo. 

Use o comando a seguir para atualizar uma configuração:

```azurecli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id** - O nome da configuração que existe no Hub IoT.

* --**hub-name** - Nome do Hub IoT no qual a configuração existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`.

* --**set** - Atualize uma propriedade na configuração. Você pode atualizar as seguintes propriedades:

    * targetCondition - por exemplo `targetCondition=tags.location.state='Oregon'`

    * rótulos 

    * priority

## <a name="delete-a-configuration"></a>Excluir uma configuração

Quando você exclui uma configuração, qualquer dispositivo gêmeoou gêmeos de módulo assumem sua próxima configuração de prioridade máxima. Se os gêmeos não atenderem à condição de destino de qualquer outra configuração, então nenhuma outra configuração será aplicada. 

Use o comando a seguir para excluir uma configuração:

```azurecli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```

* --**config-id** - O nome da configuração que existe no Hub IoT.

* --**hub-name** - Nome do Hub IoT no qual a configuração existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como configurar e monitorar dispositivos IoT em escala. Para saber mais sobre o gerenciamento do Hub IoT do Azure, siga estes links:

* [Gerenciar identidades de dispositivo do Hub IoT em massa](iot-hub-bulk-identity-mgmt.md)
* [Métricas do Hub IoT](iot-hub-metrics.md)
* [Monitoramento de operações](iot-hub-operations-monitoring.md)

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia de desenvolvedores do IoT Hub](iot-hub-devguide.md)
* [Implantando o AI em dispositivos de borda com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Para explorar usando o Serviço de Provisionamento de Dispositivos do Hub IoT para habilitar o provisionamento sem toque e Just-In-Time, consulte: 

* [Serviço de Provisionamento de Dispositivos no Hub IoT do Azure](/azure/iot-dps)
