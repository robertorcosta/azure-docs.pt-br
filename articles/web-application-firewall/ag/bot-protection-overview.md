---
title: WAF na visão geral da proteção do bot do Azure Application Gateway
titleSuffix: Azure Web Application Firewall
description: Este artigo fornece uma visão geral do FIREWALL de aplicativos web (WAF) na proteção de bots do Application Gateway
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 3bc481cfc35ac94699d2795862f1fe8e4decf875
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77027089"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Azure Web Application Firewall no Azure Application Gateway visão geral da proteção do bot

Cerca de 20% de todo o tráfego da Internet vem de bots ruins. Eles fazem coisas como raspar, digitalizar e procurar vulnerabilidades em seu aplicativo web. Quando esses bots são parados no WAF (Web Application Firewall, firewall de aplicativos da Web), eles não podem atacá-lo. Eles também não podem usar seus recursos e serviços, como seus backends e outras infra-estruturas subjacentes.

Você pode habilitar uma regra de proteção de bot gerenciada definida para o WAF para bloquear ou registrar solicitações de endereços IP maliciosos conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft. O Gráfico de Segurança Inteligente potencializa a inteligência contra ameaças da Microsoft e é usado por vários serviços, incluindo a Central de Segurança do Azure.

> [!IMPORTANT]
> O conjunto de regras de proteção bot está atualmente em visualização pública e é fornecido com um contrato de nível de serviço de visualização. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Consulte os [Termos de Uso Suplementares para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

## <a name="use-with-owasp-rulesets"></a>Use com conjuntos de regras OWASP

Você pode usar o conjunto de regras do Bot Protection ao lado de qualquer um dos conjuntos de regras OWASP (2.2.9, 3.0 e 3.1). Apenas um conjunto de regras OWASP pode ser usado a qualquer momento. O conjunto de regras de proteção bot contém uma regra adicional que aparece em seu próprio conjunto de regras. É intitulado **Microsoft_BotManagerRuleSet_0.1**, e você pode habilitá-lo ou desativá-lo como as outras regras do OWASP.

![Conjunto de regras do Bot](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Atualização do ruleset

A lista de regras de mitigação de bots de endereços IP ruins conhecidos atualiza várias vezes por dia a partir do feed microsoft Threat Intelligence para permanecer em sincronia com os bots. Seus aplicativos web são continuamente protegidos mesmo com a alteração dos vetores de ataque de bots.

## <a name="next-steps"></a>Próximas etapas

- [Configure a proteção do bot para firewall de aplicativos da Web no Azure Application Gateway (Preview)](bot-protection.md)
