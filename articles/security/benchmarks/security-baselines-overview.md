---
title: Visão geral do benchmark de segurança do Azure
description: Visão geral do benchmark de segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: 2114d9ecff5ee49c63d737cf13278fe45f1f9f73
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589955"
---
# <a name="overview-of-azure-security-baselines"></a>Visão geral das linhas de base de segurança do Azure

As linhas de base de segurança do Azure ajudam você a reforçar a segurança de nossos produtos por meio de ferramentas, controle e recursos de segurança aprimorados e fornecem uma experiência consistente ao proteger seu ambiente.

As linhas de base de segurança de serviço do Azure se concentram em áreas de controle centradas na nuvem Esses controles são consistentes com benchmarks de segurança conhecidos, como os descritos pelo CIS (Center for Internet Security). Nossas linhas de base fornecem orientação para as áreas de controle listadas no [benchmark de segurança do Azure](overview.md).

Cada recomendação inclui as seguintes informações:
- **ID do Azure**: a ID de benchmark de segurança do Azure que corresponde à recomendação.
- **Recomendação**: seguindo diretamente após a ID do Azure, a recomendação fornece uma descrição de alto nível do controle.
- **Diretrizes**: a lógica para a recomendação e links para orientação sobre como implementá-la. Se a recomendação for suportada pela central de segurança do Azure, essas informações também serão listadas.
- **Responsabilidade**: quem é responsável pela implementação do controle. Possíveis cenários são responsabilidade do cliente, responsabilidade da Microsoft ou responsabilidade de compartilhamento.
- **Monitoramento da central de segurança do Azure**: se o controle é monitorado pela central de segurança do Azure, com o link para referência.

Todas as recomendações, incluindo recomendações que não são aplicáveis a esse serviço específico, estão incluídas na linha de base para fornecer a você uma visão completa de como o benchmark de segurança do Azure está relacionado a cada serviço. Ocasionalmente, pode haver controles que não são aplicáveis por vários motivos — por exemplo, controles centrados em IaaS/computação (como controles específicos do gerenciamento de configuração do SO) podem não ser aplicáveis aos serviços de PaaS.
