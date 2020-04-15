---
title: Arquitetura de agente de segurança
description: Entenda a arquitetura do agente de segurança para os agentes usados no Azure Security Center para serviço de IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 9029ece923b7cda09c7a57d07736791e241c9e70
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310680"
---
# <a name="security-agent-reference-architecture"></a>Arquitetura de referência de agente de segurança

O Azure Security Center for IoT fornece arquitetura de referência para agentes de segurança que registram, processam, agregam e enviam dados de segurança através do IoT Hub.

Os agentes de segurança são projetados para trabalhar em um ambiente de IoT restrito, e são altamente personalizáveis em termos de valores que fornecem quando comparados com os recursos que consomem.

Os agentes de segurança suportam os seguintes recursos:

- Coletar eventos de segurança brutos do sistema operacional subjacente (Linux, Windows). Para saber mais sobre coletores de dados de segurança disponíveis, consulte [o Azure Security Center para configuração de agente IoT](how-to-agent-configuration.md).

- Agrege eventos de segurança brutos em mensagens enviadas através do IoT Hub.

- Autenticar com a identidade do dispositivo existente ou uma identidade de módulo dedicada. Consulte [os métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md) para saber mais.

- Configure remotamente através do uso do módulo **de segurança azureiot.** Para saber mais, consulte [Configure um Centro de Segurança Azure para agente IoT](how-to-agent-configuration.md).

O Azure Security Center para agentes de segurança de IoT é desenvolvido como projetos de código aberto e está disponível no GitHub:

- [Centro de Segurança Azure para agente baseado em IoT Baseado em C](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Centro de Segurança Azure para agente baseado em IoT C#](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Plataformas suportadas por agentes

O Azure Security Center for IoT oferece diferentes agentes instaladores para Windows de 32bits e 64bits, e o mesmo para Linux de 32bits e 64bits. Certifique-se de ter o instalador de agente correto para cada um de seus dispositivos de acordo com a tabela a seguir:

| Arquitetura | Linux | Windows |    Detalhes|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32bit  | C  | C#  ||
| 64 bits  | C# ou C           | C#      | Recomendamos o uso do agente C para dispositivos com recursos mais restritos ou mínimos do dispositivo.|
|

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o Azure Security Center para arquitetura de agente de segurança IoT e os instaladores disponíveis.

Para continuar começando com o Azure Security Center para implantação de IoT, use os seguintes artigos:

- Entenda os [métodos de autenticação de agentes de segurança](concept-security-agent-authentication-methods.md)
- Selecione e implante um [agente de segurança](how-to-deploy-agent.md)
- Revise os pré-requisitos de serviço de IoT do Azure para [os pré-requisitos de serviço de IoT](service-prerequisites.md)
- Saiba como [ativar o Azure Security Center para serviço sitopo em seu Hub IoT](quickstart-onboard-iot-hub.md)
- Saiba mais sobre o serviço do [Azure Security Center for IoT FAQ](resources-frequently-asked-questions.md)
