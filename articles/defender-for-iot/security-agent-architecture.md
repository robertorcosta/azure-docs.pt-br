---
title: 'Início Rápido: visão geral dos agentes de segurança'
description: Neste início rápido, você aprenderá a entender a arquitetura do agente de segurança para os agentes usados no serviço Azure Defender para IoT.
ms.topic: quickstart
ms.date: 01/24/2021
ms.openlocfilehash: 2e7d7d1e6770667b1ce966724611cc003116409d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778501"
---
# <a name="quickstart-security-agent-reference-architecture"></a>Início Rápido: arquitetura de referência do agente de segurança

O Azure Defender para IoT fornece arquitetura de referência para agentes de segurança que registram, processam, agregam e enviam dados de segurança por meio do Hub IoT.

Os agentes de segurança são projetados para funcionar em um ambiente de IoT restrito e são altamente personalizáveis em termos de valores que fornecem quando comparados aos recursos que consomem.

Os agentes de segurança dão suporte aos seguintes recursos:

- Autenticação com a identidade do dispositivo existente ou com uma identidade do módulo dedicada. Para saber mais, confira os  [Métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md).

- Coleta de eventos de segurança brutos do sistema operacional subjacente (Linux, Windows). Para saber mais sobre os coletores de dados de segurança disponíveis, confira a [Configuração do agente do Defender para IoT](how-to-agent-configuration.md).

- Agregação de eventos de segurança brutos em mensagens enviadas por meio do Hub IoT.

- Configuração remota usando o módulo gêmeo **azureiotsecurity**. Para saber mais, confira [Configurar um agente do Defender para IoT](how-to-agent-configuration.md).

Os agentes de segurança do Defender para IoT são desenvolvidos como projetos de software livre e estão disponíveis no GitHub:

- [Agente baseado em C do Defender para IoT](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Agente baseado em C# do Defender para IoT](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="prerequisites"></a>Pré-requisitos

Nenhum

## <a name="agent-supported-platforms"></a>Plataformas compatíveis com o agente

O Defender para IoT oferece agentes de instalador diferentes para o Windows de 32 bits e de 64 bits e o mesmo para o Linux de 32 bits e de 64 bits. Verifique se você tem o instalador do agente correto para cada um dos seus dispositivos de acordo com a seguinte tabela:

| Arquitetura | Linux | Windows | Detalhes |
|--|--|--|--|
| 32 bits | C | C# |  |
| 64 bits | C# ou C | C# | É recomendável usar o agente de C para dispositivos com recursos de dispositivo mais restritos ou mínimos. |


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você tem uma visão geral de alto nível sobre a arquitetura do micro agente do Defender para IoT e os instaladores disponíveis.

Para continuar com a introdução à implantação do Defender para IoT, use os seguintes artigos:

> [!div class="nextstepaction"]
> [Métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md)
