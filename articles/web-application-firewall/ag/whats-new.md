---
title: Novidades no Firewall do Aplicativo Web do Azure
description: Conheça as novidades do Firewall do aplicativo Web do Azure, como as últimas notas sobre a versão, problemas conhecidos, correções de bug, funcionalidades preteridas e alterações futuras.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 0e0e67bfb893e1bf141182e45ce4a49f5f6880ca
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "73495490"
---
# <a name="whats-new-in-azure-web-application-firewall"></a>Quais são as novidades no Firewall do Aplicativo Web do Azure?

O Firewall do aplicativo Web do Azure é atualizado continuamente. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

- As versões mais recentes
- Problemas conhecidos
- Correções de bug
- Funcionalidades preteridas

## <a name="new-features"></a>Novos recursos

|Recurso  |DESCRIÇÃO  |Data de adição  |
|---------|---------|---------|
|Conjunto de regras de Mitigação de Bot (versão prévia)|Você pode habilitar um conjunto de regras de Mitigação de Bot junto com o conjunto de regras de CRS escolhido. | Novembro de 2019 |
|Integração do GeoDB (versão prévia)|Agora você pode criar regras personalizadas restringindo o tráfego por país de origem. | Novembro de 2019 |
|Política de WAF por site/por URI (versão prévia)|O WAF-v2 agora dá suporte à aplicação de uma política para ouvintes, bem como para regras com base em caminhos. Confira [Criar a política de WAF](create-waf-policy-ag.md). | Novembro de 2019 |
|Regras personalizadas de WAF |O WAF_v2 go Gateway de Aplicativo agora dá suporte à criação de regras personalizadas. Confira [Regras personalizadas do Gateway de Aplicativo](custom-waf-rules-overview.md). |Junho de 2019 |
|Configuração do WAF e lista de exclusões     |Adicionamos mais opções para ajudar você a configurar o WAF e reduzir falsos positivos. Confira [Web application firewall request size limits and exclusion lists](application-gateway-waf-configuration.md) (Limites de tamanho de solicitação e listas de exclusões do firewall do aplicativo Web) para saber mais.|Dezembro de 2018|

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Firewall do Aplicativo Web no Gateway de Aplicativo, confira [Firewall de Aplicativo Web do Azure no Gateway de Aplicativo do Azure](ag-overview.md).
