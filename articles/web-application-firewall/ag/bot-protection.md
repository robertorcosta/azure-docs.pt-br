---
title: Configurar a proteção de bots para o Azure Web Application Firewall (WAF)
description: Saiba como configurar a proteção de bots para o WAF (Web Application Firewall) no Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73516858"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Configure a proteção do bot para firewall de aplicativos da Web no Azure Application Gateway (Preview)

Este artigo mostra como configurar uma regra de proteção de bots no WaF (Azure Web Application Firewall) para Application Gateway usando o portal Azure. 

Você pode habilitar uma regra de proteção de bot gerenciada definida para o WAF para bloquear ou registrar solicitações de endereços IP maliciosos conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft. O Gráfico de Segurança Inteligente potencializa a inteligência contra ameaças da Microsoft e é usado por vários serviços, incluindo a Central de Segurança do Azure.

> [!NOTE]
> O conjunto de regras de proteção bot está atualmente em visualização pública e é fornecido com um contrato de nível de serviço de visualização. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Consulte os [Termos de Uso Suplementares para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos

Crie uma política WAF básica para gateway de aplicativos seguindo as instruções descritas em [Criar políticas de firewall de aplicativos web para gateway de aplicativos](create-waf-policy-ag.md).

## <a name="enable-bot-protection-rule-set"></a>Habilitar o conjunto de regras de proteção de bots

1. Na **página de** diretiva Básica que você criou anteriormente, em **Configurações,** selecione **Regras**.  

2. Na página de detalhes, na seção **Gerenciar regras,** no menu suspenso, selecione a caixa de seleção para a regra de proteção do bot e, em seguida, **selecione Salvar**.

> [!div class="mx-imgBorder"]
> ![Proteção contra bots](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre regras personalizadas, consulte [Regras personalizadas para o Web Application Firewall v2 no Azure Application Gateway](custom-waf-rules-overview.md).