---
title: Comece a usar o Azure Security Center para agentes de segurança de IoT| Microsoft Docs
description: Comece a entender, configurar, implantar e usar o Azure Security Center para agentes de serviço de segurança IoT em seus dispositivos IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 1ed890d9d3602de24e6f85f6f0ae7f59849f3df2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74664176"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>Comece com o Azure Security Center para agentes de segurança de dispositivos IoT

O Azure Security Center para agentes de segurança de IoT oferece recursos de segurança aprimorados, como monitoramento de conexões remotas, aplicativos ativos, eventos de login e práticas recomendadas de configuração do sistema operacional. Assuma o controle da proteção de ameaças e a postura de segurança do seu dispositivo com um único serviço. 

A arquitetura de referência para agentes de segurança Linux e Windows, tanto em C# quanto em C são fornecidas.

O Centro de Segurança Do Azure para agentes de segurança ioT lida com a coleta de eventos brutos do sistema operacional do dispositivo, a agregação de eventos para reduzir o custo e a configuração através de um módulo de dispositivo duplo. As mensagens de segurança são enviadas através do seu Hub IoT, para o Azure Security Center para serviços de análise de IoT.

Use o seguinte fluxo de trabalho para implantar e testar o Centro de Segurança Do Azure para agentes de segurança IoT: 

1. [Habilite o Azure Security Center para serviço de IoT no seu Hub IoT](quickstart-onboard-iot-hub.md)
1. Se o seu IoT Hub não tiver dispositivos registrados, [registre um novo dispositivo](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Crie um módulo de segurança azureiotsecurity](quickstart-create-security-twin.md) para seus dispositivos.
1. Para instalar o agente em um dispositivo simulado do Azure em vez de instalar em um dispositivo real, [gire uma nova Máquina Virtual (VM) do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) em uma região disponível. 
1. [Implante um Centro de Segurança Azure para agente de segurança IoT](how-to-deploy-linux-cs.md) em seu dispositivo IoT ou novo VM.
1. Siga as instruções para [trigger_events](https://aka.ms/iot-security-github-trigger-events) executar uma simulação inofensiva de um ataque.
1. Verifique o Azure Security Center para alertas de IoT em resposta ao ataque simulado na etapa anterior. Comece a verificação cinco minutos após a execução do script.
1. Explore [alertas,](concept-security-alerts.md) [recomendações](concept-recommendations.md)e [mergulho profundo usando o Log Analytics](how-to-security-data-access.md) usando o IoT Hub. 


## <a name="next-steps"></a>Próximas etapas

- Configure sua [solução](quickstart-configure-your-solution.md)
- [Criar Módulos de Segurança](quickstart-create-security-twin.md)
- Configure [alertas personalizados](quickstart-create-custom-alerts.md)
- [Implantar um agente de segurança](how-to-deploy-agent.md)
