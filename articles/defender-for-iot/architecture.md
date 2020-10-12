---
title: Arquitetura da solução
description: Saiba mais sobre o fluxo de informações no serviço Azure defender para IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: elazark
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: v-ekrieg
ms.openlocfilehash: 3fc695770350e5a60ae3da9ab1796da5cac99370
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843406"
---
# <a name="azure-defender-for-iot-architecture"></a>Arquitetura do Azure defender para IoT

Este artigo explica a arquitetura do sistema funcional da solução defender para IoT.

## <a name="defender-for-iot-components"></a>Componentes do defender para IoT

O defender para IoT é composto pelos seguintes componentes:

- Integração do Hub IoT
- Agentes de dispositivo (opcional)
- Enviar SDK de mensagem de segurança
- Pipeline de análise

### <a name="defender-for-iot-workflows"></a>Fluxos de trabalho do defender para IoT

O defender para IoT funciona em um dos dois fluxos de trabalho de recursos: interno e avançado

### <a name="built-in"></a>Interno

No modo **interno** , o defender para IOT é habilitado quando você opta por ativar a opção de **segurança** em seu hub IOT. Oferecendo monitoramento, recomendações e alertas em tempo real, o modo interno oferece visibilidade de dispositivo de etapa única e segurança inigualável. O modo de compilação não requer a instalação do agente em nenhum dispositivo e usa análises avançadas em atividades registradas para analisar e proteger o dispositivo de campo.

### <a name="enhanced"></a>Avançado

No modo **avançado** , depois de ativar a opção de **segurança** em seu hub IOT e instalar o defender para agentes de dispositivo IOT em seus dispositivos, os agentes coletam, agregam e analisam eventos de segurança brutos de seus dispositivos. Os eventos de segurança brutos podem incluir conexões IP, criação de processo, logons de usuário e outras informações relevantes de segurança. Os defensores de agentes de dispositivo IoT também tratam da agregação de eventos para ajudar a evitar alta taxa de transferência de rede. Os agentes são altamente personalizáveis, permitindo que você os use para tarefas específicas, como enviar apenas informações importantes no SLA mais rápido ou para agregar informações de segurança extensivas e contexto em segmentos maiores, evitando custos de serviço mais altos.

![Defender para arquitetura de IoT](./media/architecture/azure-iot-security-architecture.png)

Os agentes de dispositivo e outros aplicativos usam o **SDK de mensagens de segurança do Azure Send** para enviar informações de segurança para o Hub IOT do Azure. O Hub IoT obtém essas informações e as encaminha para o serviço defender para IoT.

Depois que o serviço do defender para IoT é habilitado, além dos dados encaminhados, o Hub IoT também envia todos os seus dados internos para análise pelo defender para IoT. Esses dados incluem logs de operação do dispositivo-nuvem, identidades do dispositivo e configuração do Hub. Todas essas informações ajudam a criar o pipeline do defender para análise de IoT.

O defender for IoT Analytics pipeline também recebe fluxos adicionais de inteligência contra ameaças de várias fontes dentro da Microsoft e de parceiros da Microsoft. O pipeline de análise inteiro do defender para IoT funciona com cada configuração de cliente feita no serviço (como alertas personalizados e o uso do SDK de envio de mensagem de segurança).

Usando o pipeline de análise, o defender para IoT combina todos os fluxos de informações para gerar alertas e recomendações acionáveis. O pipeline contém as regras personalizadas criadas por pesquisadores e especialistas de segurança, bem como modelos de aprendizado de máquina procurando desvios do comportamento padrão do dispositivo e da análise de riscos.

Os alertas e recomendações do defender for IoT (saída de pipeline de análise) são gravados no espaço de trabalho Log Analytics de cada cliente. Incluir os eventos brutos no espaço de trabalho, bem como os alertas e as recomendações, permite investigações e consultas aprofundadas usando os detalhes exatos das atividades suspeitas detectadas.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre a arquitetura básica e o fluxo de trabalho da solução defender para IoT. Para saber mais sobre os pré-requisitos, como começar e habilitar sua solução de segurança no Hub IoT, consulte os seguintes artigos:

- [Pré-requisitos do serviço](service-prerequisites.md)
- [Guia de Introdução](getting-started.md)
- [Configurar sua solução](quickstart-configure-your-solution.md)
- [Habilitar a segurança no Hub IoT](quickstart-onboard-iot-hub.md)
- [FAQ do defender for IoT](resources-frequently-asked-questions.md)
- [Alertas de segurança do defender para IoT](concept-security-alerts.md)
