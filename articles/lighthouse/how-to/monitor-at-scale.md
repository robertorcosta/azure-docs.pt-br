---
title: Monitorar recursos delegados em escala
description: Saiba como usar efetivamente os logs de Azure Monitor de maneira escalonável nos locatários do cliente que você está gerenciando.
ms.date: 02/03/2020
ms.topic: how-to
ms.openlocfilehash: bdd91cec650d6810d1d62230219f840a172141d9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84634108"
---
# <a name="monitor-delegated-resources-at-scale"></a>Monitorar recursos delegados em escala

Como provedor de serviços, é provável que você tenha integrado vários locatários de clientes no gerenciamento de recursos delegados do Azure. O [Azure Lighthouse](../overview.md) permite que os provedores de serviços realizem operações em escala em vários locatários ao mesmo tempo, tornando as tarefas de gerenciamento mais eficientes.

Este tópico mostra como usar [os logs de Azure monitor](../../azure-monitor/platform/data-platform-logs.md) de maneira escalonável nos locatários do cliente que você está gerenciando.

## <a name="create-log-analytics-workspaces"></a>Criar Log Analytics espaços de trabalho

Para coletar dados, você precisará criar Log Analytics espaços de trabalho. Esses espaços de trabalho Log Analytics são ambientes exclusivos para dados coletados pelo Azure Monitor. Cada workspace tem seu próprio repositório de dados e configuração. As fontes de dados e as soluções são configuradas para armazenar seus dados em um determinado workspace.

É recomendável criar esses espaços de trabalho diretamente nos locatários do cliente. Dessa forma, seus dados permanecem em seus locatários em vez de serem exportados para o seu. Isso também permite o monitoramento centralizado de quaisquer recursos ou serviços com suporte pelo Log Analytics, proporcionando a você mais flexibilidade sobre os tipos de dados que você monitora.

Você pode criar um espaço de trabalho Log Analytics usando o [portal do Azure](../../azure-monitor/learn/quick-create-workspace.md), usando [CLI do Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)ou usando [Azure PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md).

## <a name="deploy-policies-that-log-data"></a>Implantar políticas que registram dados

Depois de criar seus espaços de trabalho do Log Analytics, você pode implantar [Azure Policy](../../governance/policy/index.yml) em suas hierarquias de clientes para que os dados de diagnóstico sejam enviados para o espaço de trabalho apropriado em cada locatário. As políticas exatas que você implanta podem variar dependendo dos tipos de recursos que você deseja monitorar.

Para saber mais sobre a criação de políticas, consulte [tutorial: criar e gerenciar políticas para impor a conformidade](../../governance/policy/tutorials/create-and-manage.md). Essa [ferramenta de comunidade](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator) fornece um script para ajudá-lo a criar políticas para monitorar os tipos de recursos específicos que você escolher.

Quando você determinar quais políticas implantar, poderá [implantá-las em suas assinaturas delegadas em escala](policy-at-scale.md).

## <a name="analyze-the-gathered-data"></a>Analisar os dados coletados

Depois de implantar suas políticas, os dados serão registrados nos espaços de trabalho do Log Analytics que você criou em cada locatário do cliente. Para obter informações sobre todos os clientes gerenciados, você pode usar ferramentas como [pastas de trabalho do Azure monitor](../../azure-monitor/platform/workbooks-overview.md) para coletar e analisar dados a partir de várias fontes.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [o Azure Monitor](../../azure-monitor/index.yml).
- Saiba mais sobre [os logs de Azure monitor](../../azure-monitor/platform/data-platform-logs.md).
- Saiba mais sobre as [experiências de gerenciamento entre locatários](../concepts/cross-tenant-management-experience.md).
