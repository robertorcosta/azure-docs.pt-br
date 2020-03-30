---
title: Implantar aplicativos seguros no Microsoft Azure
description: Este artigo discute as melhores práticas a serem consideradas durante as fases de lançamento e resposta do seu projeto de aplicação web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: dfe4f09d00a5629249a3041946190f56e83c3480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68934880"
---
# <a name="deploy-secure-applications-on-azure"></a>Implantar aplicativos seguros no Azure
Neste artigo apresentamos atividades de segurança e controles a serem considerados quando você implanta aplicativos para a nuvem. Perguntas e conceitos de segurança a serem considerados durante as fases de lançamento e resposta do [Microsoft Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) são abordados. O objetivo é ajudá-lo a definir atividades e serviços do Azure que você pode usar para implantar um aplicativo mais seguro.

As seguintes fases de SDL são abordadas neste artigo:

- Versão
- Resposta

## <a name="release"></a>Versão
O foco da fase de lançamento é preparar um projeto para lançamento público.
Isso inclui o planejamento de maneiras de executar efetivamente tarefas de manutenção pós-lançamento e resolver vulnerabilidades de segurança que podem ocorrer mais tarde.

### <a name="check-your-applications-performance-before-you-launch"></a>Verifique o desempenho do seu aplicativo antes de iniciar

Verifique o desempenho do seu aplicativo antes de lançá-lo ou implante atualizações para a produção. Execute testes de [carga baseados](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) em nuvem usando o Visual Studio para encontrar problemas de desempenho em seu aplicativo, melhorar a qualidade de implantação, certificar-se de que seu aplicativo está sempre em alta ou disponível e que seu aplicativo pode lidar com o tráfego para o seu lançamento.

### <a name="install-a-web-application-firewall"></a>Instalar um firewall do aplicativo Web

Os aplicativos Web cada vez mais são alvos de ataques mal-intencionados que exploram vulnerabilidades conhecidas comuns. Comum entre essas façanhas são ataques de injeção SQL e ataques de scripts entre sites. Prevenir esses ataques no código do aplicativo pode ser um desafio. Pode exigir manutenção rigorosa, patches e monitoramento em muitas camadas da topologia do aplicativo. Um WAF centralizado ajuda a tornar o gerenciamento de segurança mais simples. Uma solução WAF também pode reagir a uma ameaça de segurança corrigindo uma vulnerabilidade conhecida em um local central versus protegendo cada aplicativo web individual.

O [Azure Application Gateway WAF](../../application-gateway/waf-overview.md) oferece proteção centralizada de seus aplicativos web contra explorações e vulnerabilidades comuns. O WAF é baseado em regras da [regra central OWASP define](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9.

### <a name="create-an-incident-response-plan"></a>Criar um plano de resposta a incidentes

Preparar um plano de resposta a incidentes é crucial para ajudá-lo a lidar com novas ameaças que podem surgir ao longo do tempo. A elaboração de um plano de resposta a incidentes inclui identificar contatos de emergência de segurança apropriados e estabelecer planos de manutenção de segurança para códigos herdados de outros grupos na organização e para códigos de terceiros licenciados.

### <a name="conduct-a-final-security-review"></a>Realize uma revisão final de segurança

Revisar deliberadamente todas as atividades de segurança que foram executadas ajuda a garantir a prontidão para a versão ou aplicativo do software. A revisão final de segurança (FSR) geralmente inclui examinar modelos de ameaças, saídas de ferramentas e desempenho contra os portões de qualidade e barras de bugs que foram definidos na fase de requisitos.

### <a name="certify-release-and-archive"></a>Certificar a liberação e o arquivamento

Certificar o software antes de uma versão ajuda a garantir que os requisitos de segurança e privacidade sejam atendidos. O arquivamento de todos os dados pertinentes é essencial para a execução de tarefas de manutenção pós-lançamento. O arquivamento também ajuda a reduzir os custos de longo prazo associados à engenharia de software sustentada.

## <a name="response"></a>Resposta
A fase de resposta pós-lançamento centra-se na equipe de desenvolvimento sendo capaz e disponível para responder adequadamente a quaisquer relatos de ameaças e vulnerabilidades de software emergentes.

### <a name="execute-the-incident-response-plan"></a>Execute o plano de resposta a incidentes

Ser capaz de implementar o plano de resposta a incidentes instituído na fase de lançamento é essencial para ajudar a proteger os clientes contra vulnerabilidades de segurança de software ou privacidade que emergem.

### <a name="monitor-application-performance"></a>Monitorar desempenho do aplicativo

O monitoramento contínuo do seu aplicativo depois que ele é implantado potencialmente ajuda você a detectar problemas de desempenho, bem como vulnerabilidades de segurança.
Os serviços do Azure que auxiliam no monitoramento de aplicativos são:

  - Azure Application Insights
  - Central de Segurança do Azure

#### <a name="application-insights"></a>Application Insights

[O Application Insights](../../azure-monitor/app/app-insights-overview.md) é um serviço extensível de Gerenciamento de Desempenho de Aplicativos (APM) para desenvolvedores web em várias plataformas. Use-o para monitorar seu aplicativo Web online. O Application Insights detecta automaticamente anomalias de desempenho. Ele inclui ferramentas de análise poderosas para ajudá-lo a diagnosticar problemas e entender o que os usuários realmente fazem com seu aplicativo. Ele foi projetado para ajudar você a aprimorar continuamente o desempenho e a usabilidade do seu aplicativo.

#### <a name="azure-security-center"></a>Central de Segurança do Azure

[O Azure Security Center](../../security-center/security-center-intro.md) ajuda você a prevenir, detectar e responder a ameaças com maior visibilidade (e controle sobre) a segurança de seus recursos do Azure, incluindo aplicativos web. O Azure Security Center ajuda a detectar ameaças que podem passar despercebidas. Funciona com várias soluções de segurança.

A camada gratuita da Central de Segurança oferece segurança limitada somente para seus recursos do Azure. O [nível Padrão do Centro de Segurança](../../security-center/security-center-onboarding.md) estende esses recursos para recursos locais e outras nuvens.
O Security Center Standard ajuda você:

  - Encontre e corrija vulnerabilidades de segurança.
  - Aplique controles de acesso e aplicativos para bloquear atividades maliciosas.
  - Detecte ameaças usando análises e inteligência.
  - Responda rapidamente quando estiver ataque.

## <a name="next-steps"></a>Próximas etapas
Nos artigos a seguir, recomendamos controles de segurança e atividades que possam ajudá-lo a projetar e desenvolver aplicativos seguros.

- [Projetar aplicativos seguros](secure-design.md)
- [Desenvolver aplicativos e segurança](secure-develop.md)
