---
title: Visão geral do Azure Security Benchmark
description: Visão geral do Benchmark de segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: c16d7247b781fea04cfa2d53b8854cff14e039c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616440"
---
# <a name="overview-of-azure-security-baselines"></a>Visão geral das linhas de base de segurança do Azure

As linhas de base do Azure Security ajudam você a reforçar a segurança de nossos produtos através de recursos aprimorados de ferramentas, rastreamento e segurança e fornecem uma experiência consistente ao proteger seu ambiente.

As linhas de base do Azure Service Security se concentram em áreas de controle centradas na nuvem. Esses controles são consistentes com os conhecidos benchmarks de segurança, como os descritos pelo Center for Internet Security (CIS). Nossas linhas de base fornecem orientação para as áreas de controle listadas no [Azure Security Benchmark](overview.md).

Cada recomendação inclui as seguintes informações:
- **ID azure**: O ID de benchmark de segurança do Azure que corresponde à recomendação.
- **Recomendação**: Seguindo diretamente após o ID do Azure, a recomendação fornece uma descrição de alto nível do controle.
- **Orientação**: A lógica da recomendação e vincula-se à orientação sobre como implementá-la. Se a recomendação for apoiada pelo Azure Security Center, essas informações também serão listadas.
- **Responsabilidade**: Quem é responsável pela implementação do controle. Os cenários possíveis são responsabilidade do cliente, responsabilidade da Microsoft ou responsabilidade compartilhada.
- **Monitoramento do Azure Security Center**: Se o controle é monitorado pelo Azure Security Center, com link para referência.

Todas as recomendações, incluindo recomendações que não são aplicáveis a este serviço específico, estão incluídas na linha de base para fornecer uma visão completa de como o Azure Security Benchmark se relaciona com cada serviço. Pode ocasionalmente haver controles que não são aplicáveis por várias razões — por exemplo, controles centrados em IaaS/computação (como controles específicos para o gerenciamento de configuração do Sistema Operacional) podem não ser aplicáveis aos serviços PaaS.
