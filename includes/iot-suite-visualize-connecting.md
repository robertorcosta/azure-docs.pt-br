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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "67172356"
---
## <a name="view-device-telemetry"></a>Exibir telemetria de dispositivo

Você pode exibir a telemetria enviada do dispositivo na página **Gerenciador de Dispositivos** na solução.

1. Selecione o dispositivo que você provisionou na lista de dispositivos na página **Gerenciador de Dispositivos**. Um painel exibe informações sobre seu dispositivo, incluindo um gráfico de telemetria do dispositivo:

    ![Consultar detalhes do dispositivo](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Escolha **Pressão** para alterar a exibição de telemetria:

    ![Exibir telemetria de pressão](media/iot-suite-visualize-connecting/devicespressure.png)

1. Para exibir informações de diagnóstico sobre o dispositivo, role para baixo até **Diagnóstico**:

    ![Exibir diagnóstico do dispositivo](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Agir no dispositivo

Para invocar métodos nos seus dispositivos, use a página **Gerenciador de Dispositivos** na solução de Monitoramento Remoto. Por exemplo, na solução de Monitoramento Remoto, os dispositivos **Resfriadores** implementam um método **Reiniciar**.

1. Escolha **Dispositivos** para navegar até a página **Gerenciador de Dispositivos** na solução.

1. Selecione o dispositivo que você provisionou na lista de dispositivos na página **Gerenciador de Dispositivos**:

    ![Selecione seu dispositivo real](media/iot-suite-visualize-connecting/devicesselect.png)

1. Para exibir uma lista dos métodos que você pode chamar no seu dispositivo, escolha **Trabalhos** e, em seguida, **Métodos**. Para agendar um método a ser executado em vários dispositivos, você pode selecionar vários dispositivos na lista. O painel **Trabalhos** mostra os tipos de método comuns a todos os dispositivos selecionados.

1. Escolha **Reinicializar**, defina o nome do trabalho como **RebootPhysicalChiller** e, em seguida, escolha **Aplicar**:

    ![Agendar a atualização do firmware](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Uma sequência de mensagens é exibida no console executando o código de dispositivo, enquanto o dispositivo simulado manipula o método.

> [!NOTE]
> Para acompanhar o status do trabalho na solução, escolha **Exibir Status do Trabalho**.

## <a name="next-steps"></a>Próximas etapas

O artigo [Personalizar o acelerador de solução de Monitoramento Remoto pré-configurado](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) descreve algumas maneiras de personalizar o acelerador da solução.