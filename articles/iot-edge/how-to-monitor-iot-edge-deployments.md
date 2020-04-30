---
title: Monitorar implantações de IoT Edge-Azure IoT Edge
description: Monitoramento de alto nível, incluindo propriedades relatadas de edgeHub e edgeAgent e métricas de implantação automática.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cc7c1fd1dff85908c96e2fd7b2276df3d833e37f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82134306"
---
# <a name="monitor-iot-edge-deployments"></a>Monitorar implantações do IoT Edge

Azure IoT Edge fornece relatórios que permitem monitorar informações em tempo real sobre os módulos implantados em seus dispositivos IoT Edge. O serviço do Hub IoT recupera o status dos dispositivos e os disponibiliza para o operador. O monitoramento também é importante para [implantações feitas em escala](module-deployment-monitoring.md) que incluem implantações automáticas e implantações em camadas.

Os dispositivos e os módulos têm dados semelhantes, como conectividade, para que os valores sejam obtidos de acordo com a ID do dispositivo ou a ID do módulo.

O serviço de Hub IoT coleta dados relatados pelo dispositivo e pelo módulo gêmeos e fornece contagens de vários Estados que os dispositivos podem ter. O serviço do Hub IoT organiza esses dados em quatro grupos de métricas:

| Type | Descrição |
| --- | ---|
| Destino | Mostra os dispositivos IoT Edge que correspondem à condição de destino da implantação. |
| Aplicada | Mostra os dispositivos de IoT Edge de destino que não são direcionados por outra implantação de prioridade mais alta. |
| Êxito no relatório | Mostra os dispositivos IoT Edge que relataram que os módulos foram implantados com êxito. |
| Falha de relatório | Mostra os dispositivos IoT Edge que relataram que um ou mais módulos não foram implantados com êxito. Para investigar o erro mais detalhadamente, conecte-se remotamente a esses dispositivos e exiba os arquivos de log. |

O serviço de Hub IoT disponibiliza esses dados para você monitorar na portal do Azure e no CLI do Azure.

## <a name="monitor-a-deployment-in-the-azure-portal"></a>Monitorar uma implantação no portal do Azure

Para exibir os detalhes de uma implantação e monitorar os dispositivos que a executam, use as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o seu Hub IoT.
1. Selecione **IOT Edge** no menu do painel esquerdo.
1. Selecione a guia **implantações IOT Edge** .
1. Inspecione a lista de implantação.Para cada implantação, você pode exibir os seguintes detalhes:

    | Coluna | Descrição |
    | --- | --- |
    | ID | O nome da implantação. |
    | Type | O tipo de implantação, **implantação** ou **implantação em camadas**. |
    | Condição de destino | A marca usada para definir os dispositivos de destino. |
    | Prioridade | O número de prioridade atribuído à implantação. |
    | Métricas do sistema | O número de dispositivos gêmeos no Hub IoT que correspondem à condição de destino. **Aplicado** especifica o número de dispositivos que tiveram o conteúdo de implantação aplicado ao seu módulo gêmeos no Hub IOT. |
    | Métricas do dispositivo | O número de dispositivos IoT Edge que relataram êxito ou erros do tempo de execução do cliente IoT Edge. |
    | Métricas personalizadas | O número de dispositivos IoT Edge que relata dados para qualquer métrica que você definiu para a implantação. |
    | Hora de criação | O carimbo de data/hora de quando a implantação foi criada. Esse carimbo de data/hora é usado para desempate quando duas implantações têm a mesma prioridade. |

1. Selecione a implantação que deseja monitorar.  
1. Na página **detalhes da implantação** , role para baixo até a seção inferior e selecione a guia **condição de destino** . Selecione **Exibir** para listar os dispositivos que correspondem à condição de destino. Você pode alterar a condição e também a **prioridade**. Selecione **salvar** se você fez alterações.

   ![Exibir dispositivos de destino para uma implantação](./media/how-to-monitor-iot-edge-deployments/target-devices.png)

1. Selecione a guia **métricas** . Se você escolher uma métrica na lista suspensa **selecionar métrica** , um botão **Exibir** será exibido para que você exiba os resultados. Você também pode selecionar **Editar métricas** para ajustar os critérios para as métricas personalizadas que você definiu. Selecione **salvar** se você fez alterações.

   ![Exibir métricas para uma implantação](./media/how-to-monitor-iot-edge-deployments/deployment-metrics-tab.png)


Para fazer alterações em sua implantação, consulte [modificar uma implantação](how-to-deploy-at-scale.md#modify-a-deployment).

## <a name="monitor-a-deployment-with-azure-cli"></a>Monitorar uma implantação com o CLI do Azure

Use o comando [az IOT Edge Deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) para exibir os detalhes de uma única implantação:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

O comando Deployment show usa os seguintes parâmetros:

* **--deployment-id** - O nome da implantação que existe no hub IoT. Parâmetro necessário.
* **--Hub-nome** -nome do Hub IOT no qual a implantação existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`

Inspecione a implantação na janela de comando.A propriedade **Metrics** lista uma contagem para cada métrica avaliada por cada hub:

* **targetedCount** - Uma métrica do sistema que especifica o número de dispositivos gêmeos no Hub IoT que correspondem à condição de direcionamento.
* **appliedCount** - Uma métrica de sistema especifica o número de dispositivos que tiveram o conteúdo de implantação aplicado aos módulos gêmeos no Hub IoT.
* **reportedSuccessfulCount** -uma métrica de dispositivo que especifica o número de dispositivos IOT Edge na implantação que relataram o sucesso do tempo de execução do cliente IOT Edge.
* **reportedFailedCount** -uma métrica de dispositivo que especifica o número de dispositivos IOT Edge na implantação que relatam a falha do tempo de execução do cliente IOT Edge.

Você pode mostrar uma lista de IDs de dispositivo ou objetos para cada uma das métricas com o comando [az IOT Edge Deployment show-Metric](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) :

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

O comando Deployment show-Metric usa os seguintes parâmetros:

* **--deployment-id** - O nome da implantação que existe no hub IoT.
* **--Metric-ID** -o nome da métrica para a qual você deseja ver a lista de IDs de dispositivo, por exemplo `reportedFailedCount`.
* **--Hub-nome** -nome do Hub IOT no qual a implantação existe. O hub deve estar na assinatura atual. Alterne para a assinatura desejada com o comando `az account set -s [subscription name]`.

Para fazer alterações em sua implantação, consulte [modificar uma implantação](how-to-deploy-cli-at-scale.md#modify-a-deployment).

## <a name="next-steps"></a>Próximas etapas

Saiba como [se comunicar com o EdgeAgent usando métodos diretos internos](how-to-edgeagent-direct-method.md).
