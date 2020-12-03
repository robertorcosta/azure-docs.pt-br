---
title: Arquitetura do Azure defender para IoT
description: Saiba mais sobre o Azure defender para arquitetura de IoT e fluxo de informações.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: rkarlin
ms.openlocfilehash: a8697094a3366e3b82ca65f1b962101243b22f84
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548912"
---
# <a name="azure-defender-for-iot-architecture"></a>Arquitetura do Azure defender para IoT

Este artigo descreve a arquitetura do sistema funcional da solução defender para IoT.

## <a name="defender-for-iot-components"></a>Componentes do defender para IoT

O defender para IoT conecta-se à nuvem do Azure, bem como a componentes locais. A solução foi projetada para escalabilidade em ambientes grandes e geograficamente distribuídos com vários locais remotos. Essa solução permite uma arquitetura distribuída de várias camadas por país, região, unidade de negócios ou zona. 

O Azure defender para IoT inclui os seguintes componentes: 

**Implantações conectadas na nuvem**

- VM ou dispositivo de sensor do Azure defender para IoT
- portal do Azure para gerenciamento de nuvem e integração ao Azure Sentinel
- Console de gerenciamento local para gerenciamento de sites locais
- Um agente de segurança inserido (opcional)

**Implantações de ar-gapped (offline)**

- VM ou dispositivo de sensor do Azure defender para IoT
- Console de gerenciamento local para gerenciamento de sites locais


![Defender para arquitetura de IoT](./media/architecture/defender-iot-security-architecture.png)

### <a name="azure-defender-for-iot-sensors"></a>Sensores do Azure defender para IoT

Os sensores do defender for IoT detectam e monitoram continuamente os dispositivos de rede. Os sensores coletam o tráfego de rede do ICS usando o monitoramento passivo (sem agente) nos dispositivos IoT e OT. 
 
Desenvolvido especificamente para IoT e para redes, a tecnologia sem agente oferece visibilidade profunda do IoT e tem risco em minutos de serem conectados à rede. Ele tem impacto zero no desempenho na rede e nos dispositivos de rede devido à sua abordagem não invasiva de NTA (análise de tráfego de rede). 
 
Aproveitando a análise de comportamento patenteado, de IoT e de OT e a inspeção de pacotes (DPI) de camada 7, ela permite que você analise além das soluções tradicionais baseadas em assinatura para detectar imediatamente as ameaças avançadas de IoT e de OT (como malware sem arquivo) com base em atividades anormais ou não autorizadas. 
  
Os sensores do defender for IoT se conectam a uma porta de SPAN ou toque de rede e imediatamente começa a executar DPI no IoT e no tráfego de rede. 
 
A coleta de dados, o processamento, a análise e os alertas ocorrem diretamente no sensor. Isso o torna ideal para locais com baixa largura de banda ou conectividade de alta latência, pois apenas os metadados são transferidos para o console de gerenciamento.

O sensor inclui cinco mecanismos de detecção de análise. Os mecanismos acionam alertas com base na análise de tráfego em tempo real e gravados previamente. Os seguintes mecanismos estão disponíveis: 

#### <a name="protocol-violation-detection-engine"></a>Mecanismo de detecção de violação de protocolo
O mecanismo de detecção de violação de protocolo identifica o uso de estruturas de pacote e valores de campo que violam as especificações do protocolo ICS, por exemplo: exceção Modbus e inicialização de um alerta de código de função obsoleto.

#### <a name="policy-violation-detection-engine"></a>Mecanismo de detecção de violação de política
Usando o Machine Learning, o mecanismo de detecção de violação de política alerta os usuários de qualquer desvio do comportamento de linha de base, como o uso não autorizado de códigos de função específicos, o acesso a objetos específicos ou alterações na configuração do dispositivo. Por exemplo: DeltaV versão de software alterada e alertas de programação PLC não autorizados. Especificamente, o mecanismo de violação de política modela as redes ICS como sequências determinísticas de Estados e transições — usando uma técnica patenteada chamada modelagem de estado finito industrial (IFSM). O mecanismo de detecção de violação de política estabelece uma linha de base das redes ICS, para que a plataforma exija um período de aprendizado mais curto para criar uma linha de base da rede do que abordagens matemáticas genéricas ou análises, que foram originalmente desenvolvidas para ela em vez de se ter redes.

#### <a name="industrial-malware-detection-engine"></a>Mecanismo de detecção de malware industrial
O mecanismo de detecção de malware industrial identifica comportamentos que indicam a presença de malware conhecido, como Conficker, energia preta, Havex, WannaCry, NotPetya e Triton. 

#### <a name="anomaly-detection-engine"></a>Mecanismo de detecção de anomalias
O mecanismo de detecção de anomalias detecta comportamentos e comunicações M2M (máquina a máquina) incomuns. Ao modelar redes ICS como sequências determinísticas de Estados e transições, a plataforma requer um período de aprendizado mais curto do que abordagens matemáticas genéricas ou análises originalmente desenvolvidas para ela em vez de OT. Ele também detecta anomalias mais rapidamente, com um mínimo de falsos positivos. Os alertas do mecanismo de detecção de anomalias incluem tentativas de entrada de SMB excessivas e alertas de verificação de PLC detectados.

#### <a name="operational-incident-detection"></a>Detecção de incidente operacional
A detecção de incidentes operacionais detecta problemas operacionais, como conectividade intermitente que pode indicar sinais de falha de equipamentos antecipados. Por exemplo, a suspeita de dispositivo ser desconectada (sem resposta) e o comando Siemens S7 Stop PLC recebe alertas.


### <a name="management-consoles"></a>Consoles de gerenciamento
O gerenciamento do Azure defender para IoT em ambientes híbridos é realizado por meio de dois portais de gerenciamento: 
- Console do sensor
- O console de gerenciamento local
- O portal do Azure

#### <a name="sensor-console"></a>Console do sensor
As detecções de sensor são exibidas no console do sensor, onde podem ser exibidas, investigadas e analisadas em um mapa de rede, inventário de ativos e em uma ampla gama de relatórios, por exemplo, relatórios de avaliação de risco, Data Mining consultas e vetores de ataque. Você também pode usar o console do para exibir e lidar com ameaças detectadas por mecanismos de sensor, encaminhar informações para sistemas de terceiros, gerenciar usuários e muito mais.

![Console do sensor do defender para IoT](./media/architecture/sensor-console.png)

#### <a name="on-premises-management-console"></a>Console de gerenciamento local
O console de gerenciamento local permite que os operadores da SOC (central de operações de segurança) gerenciem e analisem alertas agregados de vários sensores em um único painel e fornece uma visão geral da integridade das redes de OT.

Essa arquitetura fornece uma exibição unificada abrangente da rede em um nível de SOC, tratamento otimizado de alertas e o controle da segurança de rede operacional, garantindo que a tomada de decisões e o gerenciamento de riscos permaneçam sem falhas.

Além de multilocação, monitoramento, análise de dados e controle remoto de sensor centralizado, o console de gerenciamento fornece ferramentas adicionais de manutenção do sistema (como exclusão de alertas) e recursos de relatório totalmente personalizados para cada um dos dispositivos remotos. Essa arquitetura escalonável dá suporte ao gerenciamento local em nível de site, nível de zona e gerenciamento global no SOC.

O console de gerenciamento pode ser implantado para configuração de alta disponibilidade, que fornece um console de backup que recebe periodicamente backups de todos os arquivos de configuração necessários para a recuperação. Se o console mestre falhar, os dispositivos de gerenciamento de site local realizarão failover automaticamente para sincronizar com o console de backup a fim de manter a disponibilidade sem interrupção.

#### <a name="azure-portal"></a>Portal do Azure

O portal do defender para IoT no Azure é usado para ajudá-lo a: ·   Compre dispositivos de solução ·   Instalar e atualizar software ·   Sensores integrados ao Azure ·   Atualizar pacotes de inteligência contra ameaças

## <a name="embedded-security-agent-built-in-mode"></a>Agente de segurança inserido: modo interno

No modo **interno** , o defender para IOT é habilitado quando você opta por ativar a opção de **segurança** em seu hub IOT. Oferecendo monitoramento, recomendações e alertas em tempo real, o modo interno oferece visibilidade de dispositivo de etapa única e segurança inigualável. O modo de compilação não requer a instalação do agente em nenhum dispositivo e usa análises avançadas em atividades registradas para analisar e proteger o dispositivo de campo e o Hub IoT.

## <a name="embedded-security-agent-enhanced-mode"></a>Agente de segurança inserido: modo avançado

No modo **avançado** , depois de ativar a opção de **segurança** em seu hub IOT e instalar o defender para agentes de dispositivo IOT em seus dispositivos, os agentes coletam, agregam e analisam eventos de segurança brutos de seus dispositivos. Os eventos de segurança brutos podem incluir conexões IP, criação de processo, logons de usuário e outras informações relevantes de segurança. Agentes de dispositivo do defender para IoT também manipulam a agregação de eventos para ajudar a evitar alta taxa de transferência de rede. Os agentes são altamente personalizáveis, permitindo que você os use para tarefas específicas, como enviar apenas informações importantes no SLA mais rápido ou para agregar informações de segurança extensivas e contexto em segmentos maiores, evitando custos de serviço mais altos.

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
