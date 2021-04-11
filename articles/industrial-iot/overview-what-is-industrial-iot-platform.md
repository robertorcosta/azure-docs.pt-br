---
title: Plataforma de IoT Industrial do Azure
description: Este artigo fornece uma visão geral da Plataforma de IoT Industrial e seus componentes.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 24932b17c630190cb3121d5310a865758fa6a920
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801546"
---
# <a name="what-is-the-industrial-iot-iiot-platform"></a>O que é a Plataforma de IIoT (IoT Industrial)?

A Plataforma de IoT Industrial do Azure é um conjunto de módulos e serviços da Microsoft implantados no Azure. Esses módulos e serviços são totalmente abertos. Especificamente, aplicamos a oferta de PaaS (plataforma como serviço) gerenciada do Azure, software livre licenciado pela licença MIT, padrões internacionais abertos de comunicação (OPC UA, AMQP, MQTT, HTTP) e interfaces (Open API) e modelos de dados industriais abertos (OPC UA) na borda e na nuvem.

## <a name="enabling-shopfloor-connectivity"></a>Habilitando a conectividade com o chão de fábrica 

A Plataforma de IoT Industrial do Azure trata da conectividade industrial de ativos de chão de fábrica (incluindo a descoberta de ativos habilitados para OPC UA), normaliza seus dados no formato OPC UA e transmite dados de telemetria de ativos para o Azure no formato OPC UA PubSub. Lá, ela armazena os dados telemétricos em um banco de dados na nuvem. Além disso, a plataforma habilita o acesso seguro aos ativos do chão de fábrica via OPC UA da nuvem. Os recursos de gerenciamento de dispositivos (incluindo a configuração de segurança) também são internos. A funcionalidade de OPC UA foi criada usando a tecnologia de contêiner do Docker para facilitar a implantação e o gerenciamento. Para ativos não habilitados para OPC UA, estabelecemos parcerias com os principais provedores de conectividade industrial e os ajudamos a portar seu software de adaptador de OPC UA para o Azure IoT Edge. Esses adaptadores estão disponíveis no Azure Marketplace.

## <a name="industrial-iot-components-iot-edge-modules-and-cloud-microservices"></a>Componentes de IoT Industrial: microsserviços de nuvem e módulos do IoT Edge

Os serviços de borda são implementados como módulos do Azure IoT Edge e executados localmente. Os microsserviços de nuvem são implementados como microsserviços ASP.NET com uma interface REST e são executados em Serviços de Kubernetes do Azure gerenciados ou no Serviço de Aplicativo do Azure autônomo. Para serviços de borda e de nuvem, fornecemos contêineres do Docker pré-criados no MCR (Microsoft Container Registry), removendo esta etapa para o cliente. Os serviços de borda e de nuvem utilizam uns aos outros e devem ser usados juntos. Também fornecemos scripts de implantação fáceis de usar que permitem implantar toda a plataforma em um só comando.

## <a name="next-steps"></a>Próximas etapas

Agora que aprendeu o que é a Plataforma de IoT Industrial do Azure, você pode aprender sobre o OPC Publisher:

> [!div class="nextstepaction"]
> [O que é o OPC Publisher?](overview-what-is-opc-publisher.md)