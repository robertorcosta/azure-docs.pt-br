---
title: Usar alertas na solução de Monitoramento Remoto – Azure | Microsoft Docs
description: Este tutorial mostra como usar alertas para identificar e corrigir problemas com dispositivos conectados ao acelerador da solução de Monitoramento Remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 853fa2b80e04dd8d9225d023db8030fed044ed7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "73890915"
---
# <a name="tutorial-troubleshoot-and-fix-device-issues"></a>Tutorial: Solucionar e corrigir problemas do dispositivo

Neste tutorial, você usará o acelerador da solução de Monitoramento Remoto para identificar e corrigir problemas com seus dispositivos IoT conectados. Use os alertas no painel do acelerador de solução para identificar problemas e, depois, execute trabalhos remotos para corrigir esses problemas.

A Contoso está testando um novo dispositivo **Protótipo** em campo. Como operador da Contoso, você percebeu durante o teste que o dispositivo **Protótipo** está disparando inesperadamente um alerta de temperatura no painel. Agora será necessário investigar o comportamento deste dispositivo **Protótipo** com falha e resolver o problema.

Neste tutorial, você:

>[!div class="checklist"]
> * Investigar um alerta de um dispositivo
> * Resolver o problema com o dispositivo

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="investigate-an-alert"></a>Investigar um alerta

Na página **Painel**, você perceberá que há alertas de temperatura inesperados provenientes da regra associada aos dispositivos **Protótipos**:

[![Alertas mostrados no painel](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-expanded.png#lightbox)

Para investigar o problema, escolha a opção **Explorar alerta** ao lado do alerta:

[![Explorar o alerta no painel](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-expanded.png#lightbox)

A exibição de detalhes do alerta mostra:

* Quando o alerta foi disparado
* Informações de status sobre os dispositivos associados ao alerta
* Telemetria dos dispositivos associados ao alerta

[![Detalhes do alerta](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-expanded.png#lightbox)

Para reconhecer o alerta, selecione todas as **Ocorrências do alerta** e escolha **Reconhecer**. Essa ação permite que outros operadores saibam que você viu o alerta e que está trabalhando nele:

[![Reconhecer os alertas](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-expanded.png#lightbox)

Quando você confirma o alerta, o status da ocorrência altera para **Confirmado**.

Na lista de dispositivos em alerta, é possível ver o dispositivo **Protótipo** responsável por disparar o alerta de temperatura:

[![Lista os dispositivos que estão causando o alerta](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-expanded.png#lightbox)

## <a name="resolve-the-issue"></a>Resolver o problema

Para corrigir o problema do dispositivo **Protótipo**, será necessário chamar o método **DecreaseTemperature** no dispositivo.

Para agir em um dispositivo, selecione-o na lista de dispositivos em alerta e, em seguida, escolha **Trabalhos**. O modelo do dispositivo **Protótipo** oferece suporte a seis métodos:

[![Exibir os métodos com suporte no dispositivo](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-expanded.png#lightbox)

Escolha **DecreaseTemperature** e defina o nome do trabalho para **DecreaseTemperature**. Em seguida, clique em **Aplicar**:

[![Criar o trabalho para diminuir a temperatura](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-expanded.png#lightbox)

Para acompanhar o status do trabalho, clique em **Exibir status do trabalho**. Use a exibição **Trabalhos** para acompanhar todos os trabalhos e chamadas de método na solução:

[![Monitorar o trabalho para diminuir a temperatura](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-expanded.png#lightbox)

Você pode verificar se a temperatura do dispositivo caiu exibindo a telemetria na página **Painel**:

[![Exibir a diminuição da temperatura](./media/iot-accelerators-remote-monitoring-maintain/jobresult-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/jobresult-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Este tutorial mostrou como usar alertas para identificar problemas com seus dispositivos e como agir nesses dispositivos para resolver os problemas. Para saber como conectar um dispositivo real ao seu acelerador de solução, continue lendo os artigos de instrução.

Agora que você aprendeu a gerenciar os problemas com o dispositivo, a próxima etapa sugerida é saber como [Conectar seu dispositivo ao acelerador de solução de Monitoramento Remoto](iot-accelerators-connecting-devices.md).
