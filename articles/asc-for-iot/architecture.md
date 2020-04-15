---
title: Arquitetura da solução
description: Conheça o fluxo de informações no Centro de Segurança do Azure para serviço de IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 4cc7d1982555f058a4ea23f7d8a6fdc2d83e484d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311762"
---
# <a name="azure-security-center-for-iot-architecture"></a>Arquitetura da Central de Segurança do Azure para IoT

Este artigo explica a arquitetura do sistema funcional do Azure Security Center para solução IoT.

## <a name="azure-security-center-for-iot-components"></a>Centro de Segurança Azure para componentes de IoT

O Azure Security Center for IoT é composto pelos seguintes componentes:

- Integração do IoT Hub
- Agentes de dispositivo (opcional)
- Envie mensagem de segurança SDK
- Pipeline de análise

### <a name="azure-security-center-for-iot-workflows"></a>Centro de Segurança Do Azure para fluxos de trabalho de IoT

O Azure Security Center para IoT funciona em um dos dois fluxos de trabalho de recursos: Integrado e Aprimorado

### <a name="built-in"></a>Interno

No **modo Integrado, o** Azure Security Center for IoT é habilitado quando você opta por ativar a opção **Segurança** em seu Hub IoT. Oferecendo monitoramento, recomendações e alertas em tempo real, o modo integrado oferece visibilidade de dispositivo em etapa única e segurança incomparável. O modo de entrada não requer a instalação do agente em nenhum dispositivo e usa análises avançadas em atividades registradas para analisar e proteger seu dispositivo de campo.

### <a name="enhanced"></a>Avançado

No modo **Avançado,** depois de ativar a opção **Segurança** em seu Hub IoT e instalar o Azure Security Center para agentes de dispositivos IoT em seus dispositivos, os agentes coletam, agregam e analisam eventos de segurança brutos de seus dispositivos. Os eventos de segurança bruto podem incluir conexões IP, criação de processos, logins de usuário e outras informações relevantes para a segurança. O Azure Security Center para agentes de dispositivos IoT também lida com a agregação de eventos para ajudar a evitar o alto throughput de rede. Os agentes são altamente personalizáveis, permitindo que você os use para tarefas específicas, como enviar apenas informações importantes no SLA mais rápido, ou para agregar informações de segurança e contexto extensivos em segmentos maiores, evitando custos de serviço mais altos.

![Arquitetura da Central de Segurança do Azure para IoT](./media/architecture/azure-iot-security-architecture.png)

Agentes de dispositivos e outros aplicativos usam o **Azure para enviar mensagens de segurança SDK** para enviar informações de segurança para o Azure IoT Hub. O IoT Hub pega essas informações e as encaminha para o Azure Security Center para serviço de IoT.

Uma vez que o Azure Security Center para serviço de IoT esteja habilitado, além dos dados encaminhados, o IoT Hub também envia todos os seus dados internos para análise pelo Azure Security Center for IoT. Esses dados incluem registros de operação em nuvem de dispositivos, identidades de dispositivos e configuração do Hub. Todas essas informações ajudam a criar o Azure Security Center para o pipeline de análise de IoT.

O azure Security Center for IoT analytics pipeline também recebe fluxos adicionais de inteligência de ameaças de várias fontes dentro dos parceiros Microsoft e Microsoft. O azure Security Center para ioT todo o pipeline de análise funciona com todas as configurações de clientes feitas no serviço (como alertas personalizados e uso da mensagem de segurança de envio SDK).

Usando o pipeline de análise, o Azure Security Center for IoT combina todos os fluxos de informações para gerar recomendações e alertas acionáveis. O pipeline contém regras personalizadas criadas por pesquisadores e especialistas em segurança, bem como modelos de aprendizado de máquina que buscam desvio do comportamento do dispositivo padrão e análise de risco.

O Azure Security Center para recomendações e alertas de IoT (saída do pipeline de análise) é gravado no espaço de trabalho do Log Analytics de cada cliente. A inclusão dos eventos brutos no espaço de trabalho, bem como os alertas e recomendações permitem investigações e consultas de mergulho profundo usando os detalhes exatos das atividades suspeitas detectadas.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre a arquitetura básica e o fluxo de trabalho do Azure Security Center para solução IoT. Para saber mais sobre pré-requisitos, como começar e habilitar sua solução de segurança no IoT Hub, consulte os seguintes artigos:

- [Pré-requisitos do serviço](service-prerequisites.md)
- [Começando](getting-started.md)
- [Configurar sua solução](quickstart-configure-your-solution.md)
- [Habilite a segurança no IoT Hub](quickstart-onboard-iot-hub.md)
- [Centro de Segurança Azure para Perguntas Frequentes de IoT](resources-frequently-asked-questions.md)
- [Alertas de segurança da Central de Segurança do Azure para IoT](concept-security-alerts.md)
