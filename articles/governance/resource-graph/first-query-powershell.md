---
title: Executar sua primeira consulta usando o PowerShell
description: Este artigo orienta você pelas etapas para habilitar o módulo do Resource Graph para o Azure PowerShell e executar a primeira consulta.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: a7d65d975d43a63a38863721273debab46115045
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389717"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Início Rápido: Execute a primeira consulta ao Resource Graph usando o Azure PowerShell

A primeira etapa para usar o Azure Resource Graph é garantir que o módulo do Azure PowerShell esteja instalado. Este início rápido orienta você pelo processo de adicionar o módulo à instalação do Azure PowerShell.

No final desse processo, você terá adicionado o módulo à instalação do Azure PowerShell de preferência e executado sua primeira consulta ao Resource Graph.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="add-the-resource-graph-module"></a>Adicionar o módulo do Resource Graph

Para habilitar o Azure PowerShell para consultar o Azure Resource Graph, o módulo precisa ser adicionado. Esse módulo pode ser usado com o PowerShell instalado localmente, com o [Azure Cloud Shell](https://shell.azure.com) ou com a [imagem do Docker do PowerShell](https://hub.docker.com/_/microsoft-powershell).

### <a name="base-requirements"></a>Requisitos base

O módulo Gráfico de Recursos do Azure requer o seguinte software:

- Azure PowerShell 1.0.0 ou superior. Se ainda não estiver instalado, siga [estas instruções](/powershell/azure/install-az-ps).

- PowerShellGet 2.0.1 ou superior. Se ele não estiver instalado ou atualizado, siga [estas instruções](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>Instalar o módulo

O módulo Azure Resource Graph para o PowerShell é **Az.ResourceGraph**.

1. De um prompt **administrativo** do PowerShell, execute o comando a seguir:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Confirme se o módulo foi importado e é a versão mais recente (0.7.5):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta ao Resource Graph

Com o módulo do Azure PowerShell adicionado ao seu ambiente de preferência, é hora de experimentar uma consulta simples ao Resource Graph. A consulta retornará os cinco primeiros recursos do Azure com o **Nome** e o **Tipo de recurso** de cada recurso.

1. Execute a primeira consulta ao Azure Resource Graph usando o cmdlet `Search-AzGraph`:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > Devido a essa consulta de exemplo não fornecer um modificador de classificação, tal como `order by`, executar essa consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação.

1. Atualizar a consulta para `order by` a propriedade **Name**:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Assim como ocorre com a primeira consulta, executar esta consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação. A ordem dos comandos de consulta é importante. Neste exemplo, o `order by` vem após o `limit`. Isso primeiro limitará os resultados da consulta e, em seguida, os ordenará.

1. Atualize a consulta para, primeiro, `order by` a propriedade **Name** e depois `limit` conforme os primeiros cinco resultados:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'Resources | project name, type | order by name asc | limit 5'
   ```

Quando a consulta final for executada várias vezes, supondo que nada em seu ambiente está sendo alterado, os resultados retornados são consistentes e conforme o esperado – ordenados pela propriedade **Name**, mas ainda limitados aos cinco primeiros resultados.

> [!NOTE]
> Se a consulta não retornar resultados de uma assinatura à qual você já tenha acesso, observe que o cmdlet `Search-AzGraph` é padronizado para assinaturas no contexto padrão. Para ver a lista de IDs de assinatura que fazem parte do contexto padrão, execute isso `(Get-AzContext).Account.ExtendedProperties.Subscriptions` Se você deseja pesquisar em todas as assinaturas às quais você tem acesso, é possível definir PSDefaultParameterValues para o cmdlet `Search-AzGraph` executando `$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`
   
## <a name="clean-up-resources"></a>Limpar recursos

Se quiser remover o módulo do Resource Graph do seu ambiente do Azure PowerShell, você poderá fazer isso usando o comando a seguir:

```azurepowershell-interactive
# Remove the Resource Graph module from the current session
Remove-Module -Name 'Az.ResourceGraph'

# Uninstall the Resource Graph module from the environment
Uninstall-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> Isso não exclui o arquivo de módulo baixado anteriormente. Isso apenas o remove da sessão do PowerShell em execução.

## <a name="next-steps"></a>Próximas etapas

- Obter mais informações sobre a [linguagem da consulta](./concepts/query-language.md)
- Aprender a [explorar recursos](./concepts/explore-resources.md)
- Executar sua primeira consulta com a [CLI do Azure](first-query-azurecli.md)
- Ver exemplos de [consultas iniciais](./samples/starter.md)
- Veja exemplos de [Consultas avançadas](./samples/advanced.md)
- Fornecer comentários sobre o [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)