---
title: Visão geral da proteção de bot de gateway Aplicativo Azure WAF
titleSuffix: Azure Web Application Firewall
description: Este artigo fornece uma visão geral do WAF (firewall do aplicativo Web) na proteção de bot do gateway de aplicativo
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 3bc481cfc35ac94699d2795862f1fe8e4decf875
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77027089"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Visão geral do firewall do aplicativo Web do Azure na Aplicativo Azure proteção de bot do gateway

Aproximadamente 20% de todo o tráfego da Internet provém de bots ruins. Eles fazem coisas como recorte, verificação e procura de vulnerabilidades em seu aplicativo Web. Quando esses bots são interrompidos no firewall do aplicativo Web (WAF), eles não podem atacar você. Eles também não podem usar seus recursos e serviços, como seus back-ends e outras infraestruturas subjacentes.

Você pode habilitar um conjunto de regras de proteção de bot gerenciado para seu WAF para bloquear ou registrar solicitações de endereços IP mal-intencionados conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft. O Gráfico de Segurança Inteligente potencializa a inteligência contra ameaças da Microsoft e é usado por vários serviços, incluindo a Central de Segurança do Azure.

> [!IMPORTANT]
> O conjunto de regras de proteção de bot está atualmente em visualização pública e é fornecido com um contrato de nível de serviço de visualização. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Consulte os [termos de uso complementares para ver](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) os detalhes de Microsoft Azure.

## <a name="use-with-owasp-rulesets"></a>Usar com conjuntos de regras OWASP

Você pode usar o conjunto de regras de proteção de bot juntamente com qualquer um dos conjuntos de regras OWASP (2.2.9, 3,0 e 3,1). Somente um conjunto de regras OWASP pode ser usado em um determinado momento. O conjunto de regras de proteção de bot contém uma regra adicional que aparece em seu próprio conjunto de regras. Ela é intitulada **Microsoft_BotManagerRuleSet_0 0,1**, e você pode habilitá-la ou desabilitá-la como as outras regras de OWASP.

![Conjunto de regras de bot](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Atualização do conjunto de regras

A lista do conjunto de regras de mitigação de bot de endereços IP defeituosos conhecidos atualiza várias vezes por dia do feed do Microsoft Threat Intelligence para permanecer em sincronia com os bots. Os aplicativos Web são protegidos continuamente, mesmo que os vetores de ataque de bot sejam alterados.

## <a name="next-steps"></a>Próximas etapas

- [Configurar a proteção de bot para o Firewall do aplicativo Web no gateway de Aplicativo Azure (versão prévia)](bot-protection.md)
