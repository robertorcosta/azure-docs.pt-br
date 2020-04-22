---
title: Monitorar e solucionar problemas desconecta-se com o Azure IoT Hub
description: Aprenda a monitorar e solucionar erros comuns com conectividade de dispositivos para o Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: 82139eef9708ff8d76e1087c71aa5445ba898385
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759614"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Monitorar, diagnosticar e solucionar problemas se desconecta com o Azure IoT Hub

Problemas de conectividade para dispositivos IoT podem ser difíceis de solucionar porque há muitos possíveis pontos de falha. Lógica de aplicativos, redes físicas, protocolos, hardware, IoT Hub e outros serviços em nuvem podem causar problemas. A capacidade de detectar e identificar a fonte de um problema é fundamental. No entanto, uma solução de IoT em escala pode ter milhares de dispositivos, por isso não é prático verificar dispositivos individuais manualmente. Para ajudá-lo a detectar, diagnosticar e solucionar esses problemas em escala, use os recursos de monitoramento que o IoT Hub fornece através do Azure Monitor. Esses recursos estão limitados ao que o IoT Hub pode observar, por isso também recomendamos que você siga o monitoramento das práticas recomendadas para seus dispositivos e outros serviços do Azure.

## <a name="get-alerts-and-error-logs"></a>Obtenha alertas e logs de erros

Use o Monitor Azure para obter alertas e gravar registros quando os dispositivos se desconectarem.

### <a name="turn-on-diagnostic-logs"></a>Ativar logs de diagnóstico

Para registrar erros e eventos de conexão do dispositivo, ative o diagnóstico do Hub IoT. Recomendamos ligar esses registros o mais cedo possível, porque se os registros de diagnóstico não forem habilitados, quando ocorrerem desconexões do dispositivo, você não terá nenhuma informação para solucionar o problema.

1. Entre no [portal do Azure](https://portal.azure.com).

2. Navegue até seu hub IoT.

3. Selecione **configurações de diagnóstico**.

4. Selecione **Ativar diagnóstico**.

5. Habilitar log de **conexões** a serem coletados.

6. Para uma análise mais fácil, ative **Enviar para o Log Analytics** ([ver preço](https://azure.microsoft.com/pricing/details/log-analytics/)). Consulte o exemplo em [resolver erros de conectividade](#resolve-connectivity-errors).

   ![Configurações recomendadas](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Para obter mais informações, consulte [monitorar a integridade do IoT Hub do Azure e diagnosticar problemas rapidamente](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>Configure alertas para desconexão do dispositivo em escala

Para obter alertas quando os dispositivos se desconectarem, configure alertas na métrica **de dispositivos conectados (preview).**

1. Entre no [portal do Azure](https://portal.azure.com).

2. Navegue até seu hub IoT.

3. Selecione **Alertas**.

4. Selecione **Nova regra de alerta**.

5. Selecione **Adicionar condição**e selecione "Dispositivos conectados (visualização)".

6. Configure o limiar e alerte seguindo as instruções.

Para saber mais, veja [Quais são os alertas no Microsoft Azure?](../azure-monitor/platform/alerts-overview.md)

#### <a name="detecting-individual-device-disconnects"></a>Detectando desconexões individuais de dispositivos

Para detectar desconexões *por dispositivo,* como quando você precisa saber que uma fábrica acabou de ficar offline, [configure eventos de desconexão de dispositivos com event grid](iot-hub-event-grid.md).

## <a name="resolve-connectivity-errors"></a>Resolver problemas de conectividade

Quando você ativa logs de diagnóstico e alertas para dispositivos conectados, você recebe alertas quando ocorrem erros. Esta seção descreve como procurar problemas comuns quando você recebe um alerta. As etapas abaixo supõem que você configurou registros do Monitor Do Azure para seus registros de diagnóstico.

1. Entre no [portal do Azure](https://portal.azure.com).

1. Navegue até seu hub IoT.

1. Selecione **Logs**.

1. Para isolar logs de erros de conectividade do Hub IoT, insira a consulta a seguir e selecione **Executar**:

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Se houver resultados, procure `OperationName`, `ResultType` (código de erro), e `ResultDescription` (mensagem de erro) para obter mais detalhes sobre o erro.

   ![Exemplo de log de erros](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

1. Siga as guias de resolução de problemas para os erros mais comuns:

    - **[DeviceConnectionClosedRemotely 404104](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)**
    - **[IoTHubUnauthorized 401003](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)**
    - **[LinkCreationConflict 409002](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)**
    - **[ServerError 500001](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**
    - **[GenericTimeout 500008](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Eu tentei os passos, mas eles não funcionaram

Se os passos anteriores não ajudarem, tente:

* Se você tiver acesso aos dispositivos problemáticos, seja física ou remotamente (como o SSH), siga o [guia de solução de problemas do lado do dispositivo](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) para continuar com a solução de problemas.

* Verifique se os seus dispositivos estão **Habilitados** no portal do Azure> seu hub IoT> dispositivos IoT.

* Se o dispositivo usar o protocolo MQTT, verifique se a porta 8883 está aberta. Para obter mais informações, consulte [Conectando-se ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

* Obter ajuda sobre [Fórum do IoT Hub do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub), ou [suporte do Azure](https://azure.microsoft.com/support/options/).

Para ajudar a melhorar a documentação para todos, deixe um comentário na seção de comentários abaixo, caso este guia não tenha ajudado.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como resolver problemas transitórios, consulte [Tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).

* Para saber mais sobre o SDK do IoT do Azure e gerenciar repetições, consulte [Como gerenciar a conectividade e mensagens confiáveis usando SDKs de dispositivo do Hub IoT do Azure](iot-hub-reliability-features-in-sdks.md#connection-and-retry).
