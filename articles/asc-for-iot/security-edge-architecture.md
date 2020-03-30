---
title: Entendendo o Azure Security Center para módulo de segurança IoT para IoT Edge| Microsoft Docs
description: Entenda a arquitetura e os recursos do Azure Security Center para módulo de segurança IoT para IoT Edge.
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
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 148c68234a937efde554ef00a6014cdc1a350f34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315902"
---
# <a name="azure-iot-edge-security-module"></a>Módulo de segurança Azure IoT Edge

[O Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) oferece recursos poderosos para gerenciar e executar fluxos de trabalho de negócios no limite.
A parte chave que o IoT Edge desempenha em ambientes IoT torna-o particularmente atraente para atores mal-intencionados.

O módulo de segurança Azure Security Center for IoT fornece uma solução de segurança abrangente para seus dispositivos IoT Edge.
O Azure Security Center para módulo IoT coleta, agrega e analisa dados brutos de segurança do sistema operacional e do sistema de contêineres em recomendações e alertas de segurança acionáveis.

Semelhante ao Azure Security Center para agentes de segurança IoT para dispositivos IoT, o módulo Azure Security Center for IoT Edge é altamente personalizável através de seu módulo gêmeo.
Consulte [Configurar seu agente](how-to-agent-configuration.md) para saber mais.

O Azure Security Center para módulo de segurança IoT para IoT Edge oferece os seguintes recursos:

- Coleta eventos de segurança brutos do sistema operacional subjacente (Linux) e dos sistemas de contêineres IoT Edge.
  
  Consulte [o Azure Security Center para configuração de agente ioT](how-to-agent-configuration.md) para saber mais sobre coletores de dados de segurança disponíveis.

- A análise da implantação do IoT Edge se manifesta.

- Agrega eventos de segurança brutos em mensagens enviadas através do [IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Remova a configuração através do uso do módulo de segurança twin.

  Consulte [Configurar um Centro de Segurança Azure para agente ioT](how-to-agent-configuration.md) para saber mais.

O Azure Security Center para módulo de segurança IoT para IoT Edge é executado em um modo privilegiado em IoT Edge.
O modo privilegiado é necessário para permitir que o módulo monitore o sistema operacional e outros módulos IoT Edge.

## <a name="module-supported-platforms"></a>Plataformas suportadas por módulos

O Módulo de Segurança Azure Security For IoT para IoT Edge está atualmente disponível apenas para Linux. 

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre a arquitetura e os recursos do Azure Security Center para módulo de segurança IoT para IoT Edge.

Para continuar começando com o Azure Security Center para implantação de IoT, use os seguintes artigos:

- Implantar [módulo de segurança para IoT Edge](how-to-deploy-edge.md)
- Saiba como [configurar seu módulo de segurança](how-to-agent-configuration.md)
- Revise os pré-requisitos do Azure Security Center para [pré-requisitos do Serviço de](service-prerequisites.md) IoT
- Saiba como [ativar o Azure Security Center para serviço sitopo em seu Hub IoT](quickstart-onboard-iot-hub.md)
- Saiba mais sobre o serviço do [Azure Security Center for IoT FAQ](resources-frequently-asked-questions.md)