---
title: Monitorar e solucionar problemas de desconexão com o Hub IoT do Azure
description: Saiba como monitorar e solucionar erros comuns com conectividade de dispositivo para o Hub IoT do Azure
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: 82139eef9708ff8d76e1087c71aa5445ba898385
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759614"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Monitorar, diagnosticar e solucionar problemas de desconexão com o Hub IoT do Azure

Problemas de conectividade para dispositivos IoT podem ser difíceis de solucionar porque há muitos possíveis pontos de falha. Lógica do aplicativo, redes físicas, protocolos, hardware, Hub IoT e outros serviços de nuvem podem causar problemas. A capacidade de detectar e identificar a origem de um problema é essencial. No entanto, uma solução de IoT em escala pode ter milhares de dispositivos, portanto, não é prático verificar dispositivos individuais manualmente. Para ajudá-lo a detectar, diagnosticar e solucionar esses problemas em escala, use os recursos de monitoramento que o Hub IoT fornece por meio de Azure Monitor. Esses recursos são limitados ao que o Hub IoT pode observar, por isso também recomendamos que você siga as práticas recomendadas de monitoramento para seus dispositivos e outros serviços do Azure.

## <a name="get-alerts-and-error-logs"></a>Obtenha alertas e logs de erros

Use Azure Monitor para obter alertas e gravar logs quando dispositivos se desconectarem.

### <a name="turn-on-diagnostic-logs"></a>Ativar logs de diagnóstico

Para registrar erros e eventos de conexão do dispositivo, ative o diagnóstico do Hub IoT. É recomendável ativar esses logs o mais cedo possível, porque se os logs de diagnóstico não estiverem habilitados, quando as desconexões de dispositivo ocorrerem, você não terá nenhuma informação para solucionar o problema com o.

1. Entre no [portal do Azure](https://portal.azure.com).

2. Navegue até seu hub IoT.

3. Selecione **configurações de diagnóstico**.

4. Selecione **Ativar diagnóstico**.

5. Habilitar log de **conexões** a serem coletados.

6. Para uma análise mais fácil, ative **Enviar para o Log Analytics** ([ver preço](https://azure.microsoft.com/pricing/details/log-analytics/)). Consulte o exemplo em [resolver erros de conectividade](#resolve-connectivity-errors).

   ![Configurações recomendadas](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Para obter mais informações, consulte [monitorar a integridade do IoT Hub do Azure e diagnosticar problemas rapidamente](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>Configurar alertas para desconexão do dispositivo em escala

Para obter alertas quando dispositivos se desconectarem, configure alertas na métrica **dispositivos conectados (versão prévia)** .

1. Entre no [portal do Azure](https://portal.azure.com).

2. Navegue até seu hub IoT.

3. Selecione **alertas**.

4. Selecione **Nova regra de alerta**.

5. Selecione **Adicionar condição**e, em seguida, selecione "dispositivos conectados (versão prévia)".

6. Configure o limite e o alerta seguindo os prompts.

Para saber mais, confira [o que são alertas no Microsoft Azure?](../azure-monitor/platform/alerts-overview.md).

#### <a name="detecting-individual-device-disconnects"></a>Detectando desconexões de dispositivo individuais

Para detectar desconexões *por dispositivo* , como quando você precisa saber que uma fábrica acabou de ficar offline, [Configure eventos de desconexão de dispositivo com a grade de eventos](iot-hub-event-grid.md).

## <a name="resolve-connectivity-errors"></a>Resolver problemas de conectividade

Quando você ativa logs de diagnóstico e alertas para dispositivos conectados, você recebe alertas quando ocorrem erros. Esta seção descreve como procurar problemas comuns quando você recebe um alerta. As etapas a seguir pressupõem que você configurou os logs de Azure Monitor para seus logs de diagnóstico.

1. Entre no [portal do Azure](https://portal.azure.com).

1. Navegue até seu hub IoT.

1. Selecione **logs**.

1. Para isolar logs de erros de conectividade do Hub IoT, insira a consulta a seguir e selecione **Executar**:

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Se houver resultados, procure `OperationName`, `ResultType` (código de erro), e `ResultDescription` (mensagem de erro) para obter mais detalhes sobre o erro.

   ![Exemplo de log de erros](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

1. Siga os guias de resolução do problema para obter os erros mais comuns:

    - **[DeviceConnectionClosedRemotely 404104](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)**
    - **[IoTHubUnauthorized 401003](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)**
    - **[LinkCreationConflict 409002](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)**
    - **[ServerError 500001](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**
    - **[GenericTimeout 500008](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Tentei as etapas, mas elas não funcionaram

Se as etapas anteriores não ajudarem, tente:

* Se você tiver acesso aos dispositivos problemáticos, seja física ou remotamente (como o SSH), siga o [guia de solução de problemas do lado do dispositivo](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) para continuar com a solução de problemas.

* Verifique se os seus dispositivos estão **Habilitados** no portal do Azure> seu hub IoT> dispositivos IoT.

* Se o dispositivo usar o protocolo MQTT, verifique se a porta 8883 está aberta. Para obter mais informações, consulte [conectando-se ao Hub IOT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

* Obter ajuda sobre [Fórum do IoT Hub do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub), ou [suporte do Azure](https://azure.microsoft.com/support/options/).

Para ajudar a melhorar a documentação para todos, deixe um comentário na seção de comentários abaixo, caso este guia não tenha ajudado.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como resolver problemas transitórios, consulte [Tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).

* Para saber mais sobre o SDK do IoT do Azure e gerenciar repetições, consulte [Como gerenciar a conectividade e mensagens confiáveis usando SDKs de dispositivo do Hub IoT do Azure](iot-hub-reliability-features-in-sdks.md#connection-and-retry).
