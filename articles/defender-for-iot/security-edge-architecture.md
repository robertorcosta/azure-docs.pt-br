---
title: Módulo de segurança do defender para IoT para IoT Edge
description: Entenda a arquitetura e os recursos do módulo de segurança do Azure defender para IoT para IoT Edge.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 4f6d9f670a1b85e55ccc8f6cb18645b92927221a
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351632"
---
# <a name="azure-defender-for-iot-edge-security-module"></a>Módulo de segurança do Azure defender para IoT Edge

[Azure IOT Edge](../iot-edge/index.yml) fornece recursos poderosos para gerenciar e executar fluxos de trabalho de negócios na borda.
A parte importante que IoT Edge desempenha em ambientes de IoT o torna particularmente atraente para atores mal-intencionados.

O módulo de segurança do defender for IoT fornece uma solução de segurança abrangente para seus dispositivos IoT Edge.
O módulo defender para IoT coleta, agrega e analisa dados de segurança brutos do sistema operacional e do sistema de contêiner em alertas e recomendações de segurança acionáveis.

Semelhante ao defender para agentes de segurança de IoT para dispositivos IoT, o módulo defender for IoT Edge é altamente personalizável por meio de seu módulo.
Consulte [configurar seu agente](how-to-agent-configuration.md) para saber mais.

O módulo de segurança do defender for IoT para o IoT Edge oferece os seguintes recursos:

- Coleta eventos de segurança brutos do sistema operacional subjacente (Linux) e os sistemas de contêineres IoT Edge.

  Confira [configuração do agente do defender para IOT](how-to-agent-configuration.md) para saber mais sobre os coletores de dados de segurança disponíveis.

- Análise de manifestos de implantação do IoT Edge.

- Agrega eventos de segurança brutos em mensagens enviadas por meio do [Hub IOT Edge](../iot-edge/iot-edge-runtime.md#iot-edge-hub).

- Remova a configuração por meio do uso do módulo de segurança.

  Consulte [configurar um agente do defender para IOT](how-to-agent-configuration.md) para saber mais.

O módulo de segurança do defender para IoT para IoT Edge é executado em um modo privilegiado em IoT Edge.
O modo privilegiado é necessário para permitir que o módulo monitore o sistema operacional e outros módulos IoT Edge.

## <a name="module-supported-platforms"></a>Plataformas com suporte do módulo

O módulo de segurança do defender para IoT para IoT Edge está disponível no momento apenas para Linux.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre a arquitetura e os recursos do módulo de segurança do defender for IoT para IoT Edge.

Para continuar a introdução à implantação do defender para IoT, use os seguintes artigos:

- Implantar o [módulo de segurança para IOT Edge](how-to-deploy-edge.md)
- Saiba como [configurar seu módulo de segurança](how-to-agent-configuration.md)
- Examinar os [pré-requisitos do serviço](service-prerequisites.md) defender para IOT
- Saiba como [habilitar o defender para o serviço de IOT em seu hub IOT](quickstart-onboard-iot-hub.md)
- Saiba mais sobre o serviço do [defender for IOT FAQ](resources-frequently-asked-questions.md)