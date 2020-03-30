---
title: Monitorar recursos delegados em escala
description: Aprenda a usar efetivamente os Logs do Monitor do Azure de forma escalável entre os inquilinos do cliente que você está gerenciando.
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 217df3c55ab54b6569bae8cacb338764ecb7125d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122890"
---
# <a name="monitor-delegated-resources-at-scale"></a>Monitorar recursos delegados em escala

Como provedor de serviços, é provável que você tenha integrado vários locatários de clientes no gerenciamento de recursos delegados do Azure. O [Azure Lighthouse](../overview.md) permite que os provedores de serviços realizem operações em escala em vários locatários ao mesmo tempo, tornando as tarefas de gerenciamento mais eficientes.

Este tópico mostra como usar [o Azure Monitor Logs](../../azure-monitor/platform/data-platform-logs.md) de forma escalável entre os inquilinos do cliente que você está gerenciando.

## <a name="create-log-analytics-workspaces"></a>Criar espaços de trabalho do Log Analytics

Para coletar dados, você precisará criar espaços de trabalho do Log Analytics. Esses espaços de trabalho do Log Analytics são ambientes exclusivos para dados coletados pelo Azure Monitor. Cada workspace tem seu próprio repositório de dados e configuração. As fontes de dados e as soluções são configuradas para armazenar seus dados em um determinado workspace.

Recomendamos a criação desses espaços de trabalho diretamente nos inquilinos do cliente. Dessa forma, seus dados permanecem em seus inquilinos em vez de serem exportados para o seu. Isso também permite o monitoramento centralizado de quaisquer recursos ou serviços suportados pelo Log Analytics, dando-lhe mais flexibilidade sobre quais tipos de dados você monitora.

Você pode criar um espaço de trabalho do Log Analytics usando o [portal Azure,](../../azure-monitor/learn/quick-create-workspace.md)usando [o Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)ou usando o [Azure PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md).

## <a name="deploy-policies-that-log-data"></a>Implantar políticas que registram dados

Depois de criar seus espaços de trabalho do Log Analytics, você pode implantar [a Diretiva Azure](../../governance/policy/index.yml) em suas hierarquias de clientes para que os dados de diagnóstico sejam enviados para o espaço de trabalho apropriado em cada inquilino. As políticas exatas que você implanta podem variar dependendo dos tipos de recursos que você deseja monitorar.

Para saber mais sobre a criação de políticas, consulte [Tutorial: Crie e gerencie políticas para impor a conformidade](../../governance/policy/tutorials/create-and-manage.md). Esta [ferramenta da comunidade](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/tools/azure-diagnostics-policy-generator) fornece um script para ajudá-lo a criar políticas para monitorar os tipos de recursos específicos que você escolher.

Quando você determinar quais políticas implantar, você pode [implantá-las em suas assinaturas delegadas em escala](policy-at-scale.md).

## <a name="analyze-the-gathered-data"></a>Analisar os dados coletados

Depois de implantar suas políticas, os dados serão registrados nos espaços de trabalho do Log Analytics que você criou em cada inquilino do cliente. Para obter insights em todos os clientes gerenciados, você pode usar ferramentas como [o Azure Monitor Workbooks](../../azure-monitor/platform/workbooks-overview.md) para coletar e analisar informações de várias fontes de dados.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [o Azure Monitor](../../azure-monitor/index.yml).
- Saiba mais sobre [os registros do Monitor do Azure](../../azure-monitor/platform/data-platform-logs.md).
- Saiba mais sobre as [experiências de gerenciamento entre locatários](../concepts/cross-tenant-management-experience.md).
