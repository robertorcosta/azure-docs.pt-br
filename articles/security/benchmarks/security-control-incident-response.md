---
title: Controle de Segurança Do Azure - Resposta a Incidentes
description: Resposta a incidente de controle de segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: fb3560aa2d3fbf48ab63c4da4d3a8d69cb677209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934413"
---
# <a name="security-control-incident-response"></a>Controle de Segurança: Resposta a Incidentes

Proteja as informações da organização, bem como sua reputação, desenvolvendo e implementando uma infra-estrutura de resposta a incidentes (por exemplo, planos, funções definidas, treinamento, comunicações, supervisão gerencial) para descobrir rapidamente um ataque e, em seguida, contendo efetivamente o dano, erradicando a presença do invasor e restaurando a integridade da rede e dos sistemas.

## <a name="101-create-an-incident-response-guide"></a>10.1: Crie um guia de resposta a incidentes

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | Cliente |

Crie um guia de resposta a incidentes para sua organização. Certifique-se de que existem planos de resposta a incidentes escritos que definem todas as funções do pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão pós-incidente.

Como configurar automaçãos de fluxo de trabalho no Centro de Segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Orientação sobre a construção do seu próprio processo de resposta a incidentes de segurança:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia de um incidente do Microsoft Security Response Center:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança de Computador da NIST para ajudar na criação de seu próprio plano de resposta a incidentes:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Crie um procedimento de pontuação e priorização de incidentes

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10.2 | 19.8 | Cliente |

O Security Center atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A gravidade é baseada no quão confiante o Security Center está na descoberta ou no analítico usado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marca claramente assinaturas (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos do Azure.

## <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10.3 | 19 | Cliente |

Realize exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

Consulte a publicação da NIST: Guia para programas de teste, treinamento e exercício para planos e recursos de TI:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Forneça detalhes de contato com incidentes de segurança e configure notificações de alerta para incidentes de segurança

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10.4 | 19.5 | Cliente |

As informações de contato sobre incidentes de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center, centro de resposta de segurança) descobrir que os dados do cliente foram acessados por uma parte ilegal ou não autorizada.  Revisar incidentes após o fato para garantir que os problemas sejam resolvidos.

Como definir o contato de segurança do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança ao seu sistema de resposta a incidentes

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10.5 | 19.6 | Cliente |

Exporte os alertas e recomendações do Azure Security Center usando o recurso Exportação Contínua. A Exportação Contínua permite exportar alertas e recomendações manualmente ou de forma contínua e contínua. Você pode usar o conector de dados do Azure Security Center para transmitir os alertas sentinelas.

Como configurar a exportação contínua:

https://docs.microsoft.com/azure/security-center/continuous-export

Como transmitir alertas no Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

## <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatize a resposta aos alertas de segurança

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 10.6 | 19 | Cliente |

Use o recurso Workflow Automation no Azure Security &quot;Center&quot; para disparar automaticamente respostas via Logic Apps em alertas e recomendações de segurança.

Como configurar aplicativos de automação e lógica de fluxo de trabalho:

https://docs.microsoft.com/azure/security-center/workflow-automation

## <a name="next-steps"></a>Próximas etapas

Veja o próximo controle de segurança: [Testes de penetração e Exercícios de Equipe Vermelha](security-control-penetration-tests-red-team-exercises.md)