---
title: Execute várias ações de uma regra central de IoT do Azure | Microsoft Docs
description: Execute várias ações de uma única regra central de IoT e crie grupos reutilizáveis de ações que você pode executar a partir de várias regras.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
manager: philmea
ms.openlocfilehash: b447f44d0c95693e560fd5bbfbff8c8daeec964e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157680"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Agrupar várias ações para executar a partir de uma ou mais regras

*Este artigo se aplica a construtores e administradores.*

No Azure IoT Central, você cria regras para executar ações quando uma condição é atendida. As regras são baseadas em telemetria de dispositivos ou eventos. Por exemplo, você pode notificar um operador quando a temperatura de um dispositivo exceder um limite. Este artigo descreve como usar grupos de *ação* [do Azure Monitor](../../azure-monitor/overview.md) para anexar várias ações a uma regra central de IoT. Você pode anexar um grupo de ação a várias regras. Um grupo de [ação](../../azure-monitor/platform/action-groups.md) é uma coleção de preferências de notificação definidas pelo proprietário de uma assinatura do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo criado usando um plano de preços padrão
- Uma conta do Azure e uma assinatura para criar e gerenciar grupos de ação do Azure Monitor

## <a name="create-action-groups"></a>Criar grupos de ação

Você pode [criar e gerenciar grupos de ação no portal Azure](../../azure-monitor/platform/action-groups.md) ou com um modelo do [Azure Resource Manager](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

Um grupo de ação pode:

- Envie notificações como um e-mail, um SMS ou faça uma chamada de voz.
- Execute uma ação como chamar um webhook.

A captura de tela a seguir mostra um grupo de ação que envia notificações de e-mail e SMS e chama um webhook:

![Grupo de ações](media/howto-use-action-groups/actiongroup.png)

Para usar um grupo de ação em uma regra central de IoT, o grupo de ação deve estar na mesma assinatura do Azure que o aplicativo IoT Central.

## <a name="use-an-action-group"></a>Use um grupo de ação

Para usar um grupo de ação em seu aplicativo IoT Central, primeiro crie uma regra. Quando você adicionar uma ação à regra, selecione Grupos de ação do **Monitor do Azure**:

![Escolher ação](media/howto-use-action-groups/chooseaction.png)

Escolha um grupo de ação a partir de sua assinatura do Azure:

![Escolha o grupo de ação](media/howto-use-action-groups/chooseactiongroup.png)

Selecione **Salvar**. O grupo de ação aparece agora na lista de ações a serem executadas quando a regra é acionada:

![Grupo de ação salvo](media/howto-use-action-groups/savedactiongroup.png)

A tabela a seguir resume as informações enviadas aos tipos de ação suportados:

| Tipo de ação | Formato da saída |
| ----------- | -------------- |
| Email       | Modelo de e-mail padrão da Central de IoT |
| sms         | Alerta central do Azure IoT: ${applicationName} - "${ruleName}" acionado em "${deviceName}" em ${triggerDate} ${triggerTime} |
| Voz       | Alerta central do Azure I.O.T: regra "${ruleName}" acionada no dispositivo "${deviceName}" em ${triggerDate} ${triggerTime}, no aplicativo ${applicationName} |
| webhook     | { "schemaId" : "AzureIoTCentralRuleWebhook", "data": {[carga de webhook regular](howto-create-webhooks.md#payload)}} |

O texto a seguir é um exemplo de mensagem SMS de um grupo de ação:

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar grupos de ação com regras, o próximo passo sugerido é aprender a [gerenciar seus dispositivos](howto-manage-devices.md).
