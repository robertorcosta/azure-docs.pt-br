---
title: incluir arquivo
description: incluir arquivo
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9b9e28f18208674609d0842b0e3a54e3fc661c9f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "67172356"
---
## <a name="view-device-telemetry"></a>Exibir telemetria de dispositivo

Você pode exibir a telemetria enviada do seu dispositivo na página **Device Explorer** na solução.

1. Selecione o dispositivo que você provisionou na lista de dispositivos na página **Device Explorer** . Um painel exibe informações sobre seu dispositivo, incluindo um gráfico de telemetria do dispositivo:

    ![Consultar detalhes do dispositivo](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Escolha **Pressão** para alterar a exibição de telemetria:

    ![Exibir telemetria de pressão](media/iot-suite-visualize-connecting/devicespressure.png)

1. Para exibir informações de diagnóstico sobre o dispositivo, role para baixo até **Diagnóstico**:

    ![Exibir diagnóstico do dispositivo](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Agir no dispositivo

Para invocar métodos em seus dispositivos, use a página **Device Explorer** na solução de monitoramento remoto. Por exemplo, na solução de monitoramento remoto, os dispositivos **resfriadores** implementam um método de **reinicialização** .

1. Escolha **dispositivos** para navegar até a página **Device Explorer** na solução.

1. Selecione o dispositivo que você provisionou na lista de dispositivos na página **Device Explorer** :

    ![Selecione seu dispositivo real](media/iot-suite-visualize-connecting/devicesselect.png)

1. Para exibir uma lista dos métodos que você pode chamar em seu dispositivo, escolha **trabalhos**e **métodos**. Para agendar um método a ser executado em vários dispositivos, você pode selecionar vários dispositivos na lista. O painel **Trabalhos** mostra os tipos de método comuns a todos os dispositivos selecionados.

1. Escolha **reinicialização**, defina o nome do trabalho como **RebootPhysicalChiller** e, em seguida, escolha **aplicar**:

    ![Agendar a atualização do firmware](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Uma sequência de mensagens é exibida no console executando o código de dispositivo, enquanto o dispositivo simulado manipula o método.

> [!NOTE]
> Para acompanhar o status do trabalho na solução, escolha **Exibir status do trabalho**.

## <a name="next-steps"></a>Próximas etapas

O artigo [Personalizar o acelerador de solução de monitoramento remoto](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) descreve algumas maneiras de personalizar o Solution Accelerator.