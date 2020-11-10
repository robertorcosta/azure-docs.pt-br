---
title: Benchmark de segurança do Azure v2-resposta a incidentes
description: Resposta a incidente do Azure Security benchmark v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 1ba7391b3b524f7532101083a444aab6059e8a10
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408748"
---
# <a name="security-control-v2-incident-response"></a>Controle de segurança v2: resposta a incidentes

A resposta a incidentes cobre controles no ciclo de vida de resposta a incidentes – preparação, detecção e análise, confinamento e atividades pós-incidente. Isso inclui o uso de serviços do Azure, como a central de segurança do Azure e o Sentinel para automatizar o processo de resposta a incidentes.

## <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: preparação – processo de atualização de resposta a incidentes para o Azure

| ID do Azure | Controles do CIS v 7.1 ID (s) | NIST SP800-53 ID (s) R4 |
|--|--|--|--|
| IR-1 | 19 | IR-4, IR-8 |

Verifique se sua organização tem processos para responder a incidentes de segurança, atualizou esses processos para o Azure e está experimentando-os regularmente para garantir a preparação.

- [Implementar a segurança em todo o ambiente corporativo](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [Guia de referência de resposta a incidentes](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Responsabilidade** : Cliente

**Participantes da segurança do cliente** ( [saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação para incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Inteligência contra ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: preparação – notificação de incidente de instalação

| ID do Azure | Controles do CIS v 7.1 ID (s) | NIST SP800-53 ID (s) R4 |
|--|--|--|--|
| IR-2 | 19,5 | IR-4, IR-5, IR-6, IR-8 |

Configure informações de contato de incidente de segurança na central de segurança do Azure. Essas informações de contato são usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobre que seus dados foram acessados por uma parte ilegal ou não autorizada. Você também tem opções para personalizar o alerta de incidente e a notificação em diferentes serviços do Azure com base em suas necessidades de resposta a incidentes. 

- [Como definir o contato da segurança da central de segurança do Azure](../../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade** : Cliente

**Participantes da segurança do cliente** ( [saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação para incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detecção e análise – crie incidentes com base em alertas de alta qualidade

| ID do Azure | Controles do CIS v 7.1 ID (s) | NIST SP800-53 ID (s) R4 |
|--|--|--|--|
| IR-3 | 19,6 | IR-4, IR-5 |

Verifique se você tem um processo para criar alertas de alta qualidade e medir a qualidade dos alertas. Isso permite que você aprenda as lições dos últimos incidentes e Priorize os alertas para analistas, para que eles não percam tempo em falsos positivos. 

Alertas de alta qualidade podem ser criados com base na experiência dos últimos incidentes, fontes de comunidade validadas e ferramentas projetadas para gerar e limpar alertas, combinando e correlacionando diferentes fontes de sinal. 

A central de segurança do Azure fornece alertas de alta qualidade em muitos ativos do Azure. Você pode usar o conector de dados ASC para transmitir os alertas para o Azure Sentinel. O Azure Sentinel permite que você crie regras de alerta avançadas para gerar incidentes automaticamente para uma investigação. 

Exporte seus alertas e recomendações da central de segurança do Azure usando o recurso exportar para ajudar a identificar os riscos para os recursos do Azure. Exporte alertas e recomendações de forma manual ou contínua, de modo contínuo.

- [Como configurar a exportação](../../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Responsabilidade** : Cliente

**Participantes da segurança do cliente** ( [saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação para incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Inteligência contra ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: detecção e análise – investigue um incidente

| ID do Azure | Controles do CIS v 7.1 ID (s) | NIST SP800-53 ID (s) R4 |
|--|--|--|--|
| IR-4 | 19 | IR-4 |

Verifique se os analistas podem consultar e usar diversas fontes de dados à medida que investigam incidentes potenciais, para criar uma visão completa do que aconteceu. Logs diversos devem ser coletados para acompanhar as atividades de um invasor potencial na cadeia de Kill para evitar manchas cegas.  Você também deve garantir que informações e aprendizados sejam capturados para outros analistas e para futura referência histórica.  

As fontes de dados para investigação incluem as fontes de registro em log centralizadas que já estão sendo coletadas dos serviços dentro do escopo e sistemas em execução, mas também podem incluir:

- Dados de rede – Use os logs de fluxo dos grupos de segurança de rede, o observador de rede do Azure e o Azure Monitor para capturar logs de fluxo de rede e outras informações de análise. 

- Instantâneos de sistemas em execução: 

    - Use a capacidade de instantâneo da máquina virtual do Azure para criar um instantâneo do disco do sistema em execução. 

    - Use o recurso de despejo de memória nativo do sistema operacional para criar um instantâneo da memória do sistema em execução.

    - Use o recurso de instantâneo dos serviços do Azure ou a capacidade de seu próprio software para criar instantâneos dos sistemas em execução.

O Azure Sentinel fornece ampla análise de dados em praticamente qualquer origem de log e um portal de gerenciamento de casos para gerenciar o ciclo de vida completo de incidentes. As informações de inteligência durante uma investigação podem ser associadas a um incidente para fins de rastreamento e relatório. 

- [Instantâneo do disco de um computador Windows](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Instantâneo do disco de um computador Linux](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Suporte Microsoft Azure informações de diagnóstico e coleta de despejo de memória](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Investigue incidentes com o Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)

**Responsabilidade** : Cliente

**Participantes da segurança do cliente** ( [saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação para incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Inteligência contra ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: detecção e análise – priorizar incidentes

| ID do Azure | Controles do CIS v 7.1 ID (s) | NIST SP800-53 ID (s) R4 |
|--|--|--|--|
| IR-5 | 19,8 | CA-2, IR-4 |

Forneça contexto aos analistas nos quais os incidentes se concentram primeiro com base na severidade do alerta e na sensibilidade do ativo. 

A central de segurança do Azure atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A gravidade se baseia em quão confiante a central de segurança está na localização ou análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque os recursos usando marcas e crie um sistema de nomeação para identificar e categorizar os recursos do Azure, especialmente aqueles que processam dados confidenciais.  É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

- [Alertas na Central de Segurança do Azure](../../security-center/security-center-alerts-overview.md)

- [Usar marcas para organizar seus recursos do Azure](../../azure-resource-manager/management/tag-resources.md)

**Responsabilidade** : Cliente

**Participantes da segurança do cliente** ( [saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação para incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Inteligência contra ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: contenção, erradicação e recuperação – automatizar o tratamento de incidentes

| ID do Azure | Controles do CIS v 7.1 ID (s) | NIST SP800-53 ID (s) R4 |
|--|--|--|--|
| IR-6 | 19 | IR-4, IR-5, IR-6 |

Automatize tarefas repetitivas manuais para acelerar o tempo de resposta e reduzir a carga de analistas. As tarefas manuais demoram mais para serem executadas, atrasando cada incidente e reduzindo Quantos incidentes um analista pode manipular. As tarefas manuais também aumentam a fadiga do analista, o que aumenta o risco de erros humanos que causam atrasos e degrada a capacidade dos analistas de se concentrarem efetivamente em tarefas complexas. Use os recursos de automação de fluxo de trabalho na central de segurança do Azure e o Azure Sentinel para disparar automaticamente ações ou executar um guia estratégico para responder a alertas de segurança de entrada. O guia estratégico executa ações, como enviar notificações, desabilitar contas e isolar redes problemáticas. 

- [Configurar a automação do fluxo de trabalho na central de segurança](../../security-center/workflow-automation.md)

- [Configurar respostas de ameaças automatizadas na central de segurança do Azure](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurar respostas de ameaças automatizadas no Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Responsabilidade** : Cliente

**Participantes da segurança do cliente** ( [saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação para incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Inteligência contra ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)